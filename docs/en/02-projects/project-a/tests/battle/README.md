Battle tests (red-team) for project-a.

Purpose: Validate model/harness behavior against prompt-injection, data-exfil and hallucination scenarios.

How to write a case

- Create a YAML/JSON case file containing: id, title, description, seed conversation, attack-type, success-criteria, severity.
- Add automation script or harness adapter so CI can run the case against a mock/real endpoint in sandbox.

Execution

- PRs that change prompt templates must run the relevant battle-subset (fast subset) in CI.
- Full battle-suite runs are scheduled monthly or before production release.
