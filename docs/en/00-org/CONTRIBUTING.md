# Contributing to my-ai-guides

This doc explains how teams should contribute prompt templates, battle cases, tests and docs.

Rules

1. All prompt templates must include YAML metadata (id, version, author, purpose, inputs, outputs.schema, safety_constraints).
2. Each template change must include or update unit tests and a battle case if the template is medium/high risk.
3. Use semantic versioning for template `version` field.

PR template (suggested)

- Title: `[project] <short description> (template-id vX.Y)`
- Description: what changed, risk-level, tests added, who to review
- Checklist:
  - [ ] Metadata present
  - [ ] Example(s) present
  - [ ] Output schema present
  - [ ] Unit tests added/updated
  - [ ] Battle subset added/updated (if required)

Review process

- Assign the template owner and platform owners as reviewers.
- Security team will be invited automatically for medium+ risk changes.
