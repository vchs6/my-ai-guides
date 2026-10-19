# Project: project-a (sample)

Owner: @team-a
Purpose: Example project to show per-project layout for prompts, tests and battle cases.
Risk-level: medium (RAG retrieval + user-visible outputs)

Structure

- docs/02-projects/project-a/README.md (this file)
- prompt-templates/: templates for project-a
- tests/unit/: unit tests for templates
- tests/battle/: battle cases for red-team

How to add a template

1. Create `prompt-templates/<id>.md` using the prompt-template pattern from `docs/03-templates/prompt-template.md`.
2. Add an example to `tests/unit/` and a battle case to `tests/battle/` if risk >= medium.
3. Open PR and assign @team-a and @platform-team for review.
