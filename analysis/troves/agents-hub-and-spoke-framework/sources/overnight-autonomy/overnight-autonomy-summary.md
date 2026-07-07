---
slug: overnight-autonomy
relates-to: agents-hub-and-spoke-framework
relevance: Defines the autonomous execution protocol for background subagent work, including state management, absence detection, and plan-driven execution — directly applicable to the hub's background dispatch mode.
selected-because: The hub-and-spoke framework needs a protocol for long-running autonomous subagent tasks; this spoke provides the state machine, scheduling, and cleanup patterns.
aspects-covered:
  - State machine (idle/nudging/autonomous/done)
  - Absence detection and operator nudge protocol
  - Plan-driven autonomous execution
  - OS-level scheduling (launchd/systemd/cron)
  - Self-deleting cleanup jobs
  - Morning briefing and resumption protocol
gaps:
  - Does not address subagent dispatch mechanics (lock-watch, turn sequencing)
  - Does not cover concurrent autonomous tasks
---
