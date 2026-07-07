# Research Trove: Software QA/QC Industry Standards

> **⚠ AI-generated, no human review.** This trove was collected and summarized by an AI language model. No human has verified the sources or reviewed the content.

**Collected:** 2026-07-07
**Sources:** Brave Search LLM Context API
**Purpose:** Reference material for the Quality Framework document

## ISO/IEC 25010 (SQuaRE Quality Model)

The ISO 25010 standard defines a software product quality model with 8 characteristics and 31 sub-characteristics. It replaces the older ISO 9126, adding Security and Compatibility as top-level characteristics.

### Eight Quality Characteristics

1. **Functional Suitability** — Completeness, Correctness, Appropriateness
2. **Performance Efficiency** — Time behavior, Resource utilization, Capacity
3. **Compatibility** — Co-existence, Interoperability
4. **Usability** — Appropriateness recognizability, Learnability, Operability, User error protection, UI aesthetics, Accessibility
5. **Reliability** — Maturity, Availability, Fault tolerance, Recoverability
6. **Security** — Confidentiality, Integrity, Non-repudiation, Accountability, Authenticity
7. **Maintainability** — Modularity, Reusability, Analysability, Modifiability, Testability
8. **Portability** — Adaptability, Installability, Replaceability

### Key Insights

- ISO 25010 provides a taxonomy for defining non-functional requirements (NFRs)
- ISTQB Foundation Level explicitly references quality models like ISO 25010
- Academic attention favors security and performance; industry prioritizes maintainability and readability
- The model supports stakeholder-driven selection of quality characteristics based on project context

## IEEE 829-2008 (Test Documentation)

Standard for software test documentation structure:
- Test Plan — scope, objectives, resources, schedule, deliverables
- Test Design Specification — input data, expected results, execution steps
- Test Case Specification — test conditions, expected results, pass/fail criteria
- Test Procedure Specification — step-by-step execution guide
- Test Log — chronological record of test execution
- Test Incident Report — events requiring investigation
- Test Summary Report — overall results and recommendations

## ISTQB (International Software Testing Qualifications Board)

- Test levels: Unit, Integration, System, Acceptance
- Test types: Functional, Non-functional, Structural, Regression
- Test process: Planning → Analysis & Design → Implementation & Execution → Evaluation & Reporting → Closure
- Globally recognized certification for QA professionals

## ISO/IEC 29119 (Software Testing)

Five-part international standard:
- Part 1: Concepts and Definitions
- Part 2: Test Processes
- Part 3: Test Documentation
- Part 4: Test Techniques (specification-based, structure-based, experience-based)
- Part 5: Keyword-Driven Testing

## CI/CD Quality Gates (Industry Practice)

Standard quality gates in modern DevOps pipelines:
1. Static Analysis & Code Quality (linting, complexity, duplication)
2. Unit & Integration Test Success
3. Security Vulnerability & Dependency Scan
4. Artifact Integrity Check (signing, checksums)
5. Configuration & Infrastructure Validation (IaC linting)
6. End-to-End Test Suite
7. Performance & Load Testing
8. Monitoring & Logging Check
9. Rollback Readiness & Health Check
10. DORA Metrics Threshold Check

### Key Insights

- Automated static analysis reduces post-release defect rates by 35-50%
- Quality gates reduce code review time by 30-45% through pre-filtering
- SonarQube's "Clean as You Code" approach focuses enforcement on new code
- Quality gates complement rather than replace human review
- Research shows 59% of developers report improved code quality with AI-powered tools
