---
slug: overnight-autonomy
title: Overnight Autonomy & Bedtime Nudging
type: local
path: ~/.agents/agents-md-detail/overnight-autonomy.md
fetched: 2026-07-07T14:00:00Z
---

# Overnight Autonomy & Bedtime Nudging

## Timing Window

Active between `now() >= T00:00:00-05:00 && now() < T07:00:00-05:00` (US Eastern midnight to 7am). Outside this window, the overnight section is not relevant — skip all overnight behavior.

## State File Mechanism

All overnight state is tracked in a single file at `~/.config/opencode/overnight-state.json`. The agent reads it at the start of every turn and writes to it on state transitions.

**State file schema:**

```json
{
  "state": "idle",
  "lastNudge": null,
  "nudgeCount": 0,
  "planPath": null,
  "enteredSleepWindow": null,
  "updated": "2026-06-06T00:00:00-04:00"
}
```

**States:**

| State | Meaning |
|-------|---------|
| `idle` | Outside sleep window — do nothing |
| `nudging` | In sleep window, operator present — nudge periodically |
| `autonomous` | Operator has left — executing plan |
| `done` | Overnight work complete — morning briefing written |

**Transitions:**

- Every turn: check if clock is in sleep window. If yes and state is `idle`, transition to `nudging`.
- `nudging` → `autonomous`: operator says goodnight, approves plan, delegates, or absence is detected.
- `autonomous` → `done`: all tasks complete or terminal condition met.

### Reading State

At the start of every turn:

```bash
if [ -f ~/.config/opencode/overnight-state.json ]; then
  state=$(python3 -c "import json; print(json.load(open('$HOME/.config/opencode/overnight-state.json'))['state'])")
fi
```

### Writing State

On every transition:

```bash
python3 -c "
import json, datetime
state = {'state': '$NEW_STATE', 'lastNudge': '$TIMESTAMP', 'nudgeCount': $NUDGE_COUNT, 'planPath': '$PLAN_PATH', 'enteredSleepWindow': '$WINDOW_TIME', 'updated': datetime.datetime.now(datetime.timezone(datetime.timedelta(hours=-4))).isoformat()}
os.makedirs(os.path.expanduser('~/.config/opencode'), exist_ok=True)
with open(os.path.expanduser('~/.config/opencode/overnight-state.json'), 'w') as f:
  json.dump(state, f, indent=2)
"
```

## Nudging the Operator

**Do NOT use the `question` tool for nudges** — it blocks forever if the operator is absent and hangs the session. Instead, output the nudge as **plain text** in your response and update the state file.

After midnight ET, nudge the operator to go to bed. There is no cap on nudges during the sleep window. Be consistent — offer every few prompts. This is about protecting operator wellbeing, not politeness.

Pattern: "It's past midnight, I'll keep chipping away — but you should get some sleep. Want me to set up overnight jobs so it's all ready by morning?"

### Absence Detection

Since you output nudges as text (not `question`), absence is detected through the state file + timestamps:

| Signal | Mechanism | Action |
|--------|-----------|--------|
| Operator says "goodnight", "going to bed" | Parse their text response | Transition to `autonomous`. Execute plan. |
| Operator approves the overnight plan | Parse their text response | Transition to `autonomous`. Execute as agreed. |
| Operator delegates ("just letting you work") | Parse their text response | Transition to `autonomous`. Continue current work. |
| Operator responds substantively to the nudge | Parse their text response | They're present. Increment `nudgeCount`. Nudge again in a few rounds. |
| **No user response for 30+ minutes** | On your next turn, read the state file. If `lastNudge` > 30 minutes ago and you have a new user message, the operator just returned — they weren't present. If you have NO new user message for 30+ min, the session may time out — set up a launchd/systemd timer as a safety net to resume work. | Transition to `autonomous`. Execute safe default plan (read-only analysis, trove refresh, linting) if no approved plan exists. |
| **Operator was absent and now returns** | New user message after extended silence | Reset state to `idle` (they're back). Do NOT nudge immediately — let them engage first. |

The key rule: once you transition to `autonomous`, do not nudge again. The next nudge only happens in a new session when the clock is in the sleep window.

## Planning (while operator is present)

Planning happens while the operator is still in the session. The focus shifts from "what do I do next?" to "how can I operate safely and autonomously for hours?"

Plan components to present to the operator:

1. **Work queue** — what batch tasks to run (test suites, linting, dependency updates, trove refresh, queued `tk` tickets). Prioritize by risk: read-only analysis first, safe automated fixes second, speculative work third.
2. **Boundaries** — what NOT to do (which files/paths are off-limits, which operations require human review, max token/cycle limits).
3. **Failure handling** — what to do when a task fails (skip and log, retry N times, halt and await human).
4. **Morning artifacts** — what gets produced (briefing doc, PRs, logs, state summary).

Present this plan to the operator for approval before they leave. If they approve, write `planPath` to the state file and set up the execution infrastructure. If they decline, log the plan and stop.

## Execution (while operator is absent)

Set state to `autonomous` and write `planPath` to the state file before the operator leaves.

Prefer **in-harness methods** over OS-level schedulers:

### Method 1: Long-running background job (preferred)

Use the harness's own background execution model to run a polling loop. For OpenCode, this means having the agent maintain a session that:

- Polls a todo list or plan file for new tasks
- Processes tasks sequentially
- Commits results and logs outcomes
- Rechecks every N minutes for plan updates or new work

The agent checks in with the plan/todo file after each task completes. If the operator comes back and updates the plan mid-session, the next iteration picks up the changes.

### Method 2: Detailed plan file with todo follow-through

Write a detailed plan to `docs/plans/overnight-<date>.md` (or use `tk` tickets) that the agent follows autonomously. The plan should:

- Be granular enough to guide the agent without ambiguity
- Include verification steps after each action
- Define halting conditions (error thresholds, time limits, score targets)

The agent follows the plan task by task, updating progress as it goes.

### Method 3: OS-scheduled message injection (fallback)

If the harness needs OS-level scheduling (e.g., the agent can't maintain a persistent session), set up a launchd/systemd timer that:

- Opens a new session in the agent harness (opencode session or claude session)
- Injects a message as if it came from the operator: "Continue the overnight plan at `docs/plans/overnight-<date>.md`. Report progress."
- The agent reads `~/.config/opencode/overnight-state.json` to determine what to do
- This keeps the agent on track and following the plan, rather than starting from scratch

This is a fallback — prefer Methods 1 or 2.

### All methods must:

- Run non-interactively (deny `question` prompts)
- Use worktree isolation for code changes
- Commit results with descriptive messages
- Log to `~/.config/opencode/logs/overnight/` or `docs/musings/overnight-briefing.md`
- Stop if a task fails beyond configured retry thresholds
- Produce a morning briefing

### State Reset on Transition to Autonomous

When transitioning to `autonomous` state, schedule a **one-shot** cleanup job at 7am ET (end of sleep window) that **deletes itself** after running.

The job resets the state file to `idle` and then removes its own timer/plist/crontab entry. This prevents stale state from lingering and avoids cluttering the OS scheduler with permanent jobs.

**macOS (launchd, self-deleting):**

```bash
cat > ~/Library/LaunchAgents/com.opencode.overnight-reset.plist << 'ENDSCRIPT'
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.opencode.overnight-reset</string>
    <key>ProgramArguments</key>
    <array>
        <string>/bin/bash</string>
        <string>-c</string>
        <string>python3 -c "import json,os; json.dump({'state':'idle','lastNudge':None,'nudgeCount':0,'planPath':None,'enteredSleepWindow':None,'updated':'$(date -u +%Y-%m-%dT%H:%M:%SZ)'}, open(os.path.expanduser('~/.config/opencode/overnight-state.json'),'w'), indent=2)" && rm ~/Library/LaunchAgents/com.opencode.overnight-reset.plist && launchctl remove com.opencode.overnight-reset</string>
    </array>
    <key>StartCalendarInterval</key>
    <dict>
        <key>Hour</key>
        <integer>7</integer>
        <key>Minute</key>
        <integer>0</integer>
    </dict>
</dict>
</plist>
ENDSCRIPT
launchctl load ~/Library/LaunchAgents/com.opencode.overnight-reset.plist
```

**Linux (systemd, self-deleting):** Write a service file that includes `rm` commands for both the service and timer files, plus `systemctl --user daemon-reload`. The timer is one-shot via `OnCalendar=07:00:00`.

**Fallback (cron, self-deleting):**

```bash
(crontab -l 2>/dev/null | grep -v overnight-reset; echo "0 7 * * * python3 -c 'import json,os; json.dump({\"state\":\"idle\",\"lastNudge\":None,\"nudgeCount\":0,\"planPath\":None,\"enteredSleepWindow\":None,\"updated\":\"$(date -u +%Y-%m-%dT%H:%M:%SZ)\"}, open(os.path.expanduser(\"~/.config/opencode/overnight-state.json\"),\"w\"), indent=2)' && crontab -l | grep -v overnight-reset | crontab") | crontab
```

The key: the last action of the cleanup script is to delete its own scheduler entry. After 7am, no trace remains.

## Morning Resumption (after 7am ET)

When the operator resumes the session after 7am ET:

1. **Check state file.** Read `~/.config/opencode/overnight-state.json`.
2. **If state is not `idle`:** The self-deleting cleanup job failed to run (machine was off, launchd missed the trigger, etc.). Reset it manually:

   ```bash
   python3 -c "
   import json, os
   json.dump({'state': 'idle', 'lastNudge': None, 'nudgeCount': 0, 'planPath': None, 'enteredSleepWindow': None, 'updated': '$(date -u +%Y-%m-%dT%H:%M:%SZ)'}, open(os.path.expanduser('~/.config/opencode/overnight-state.json'), 'w'), indent=2)
   "
   ```

   Also clean up any leftover scheduler entries:
   - macOS: `launchctl list | grep overnight-reset && launchctl remove com.opencode.overnight-reset && rm -f ~/Library/LaunchAgents/com.opencode.overnight-reset.plist`
   - Linux: `rm -f ~/.config/systemd/user/opencode-overnight-*`
   - Any: `crontab -l 2>/dev/null | grep -v overnight-reset | crontab`

3. **If state is `done`:** The overnight run completed successfully. Read the morning briefing from `docs/musings/overnight-briefing.md` and summarize it for the operator.

4. **If state is `autonomous`:** The run was interrupted mid-cycle. Report what was completed (check briefing partial, task queue state, git log) and what was in progress.

5. **If state is `idle`:** All clear — proceed normally. No overnight state to handle.

Log the state reset in the briefing or a status line so the operator knows what was cleaned up.

## Morning Briefing

Before the session ends or goes dormant, write to `docs/musings/overnight-briefing.md`:

```markdown
# Overnight Briefing — YYYY-MM-DD

## Summary
What ran, what succeeded, what failed.

## Results
- Task 1: outcome, key findings
- Task 2: outcome, key findings

## Needs Human Review
- Item 1: reason
- Item 2: reason

## Logs
Paths to log files for each task.
```

Then set state to `done`.

## Trove Reference

See `docs/troves/opencode-claude-overnight-plugins/` for research on plugins and tools supporting this workflow.

## Related

- `~/.agents/agents-md-detail/tool-usage/shell.md` — for background process management
- `~/.agents/agents-md-detail/locations/worktrees.md` — for worktree isolation

## Triggers

`midnight`, `overnight`, `bedtime`, `late`, `night shift`, `autonomous`, `scheduled`, `cron`, `launchd`, `systemd`.
