# Prompt Catalog and Prompt-ID Conventions

Purpose

Define the canonical prompt-id format and catalog practices so multiple projects interoperate and CI can find templates.

Prompt ID format

- `<team>.<project>.<template>.<version>`
  - team: short org team id (infra, ml, product)
  - project: project slug
  - template: short template name
  - version: semantic style `v1`, `v1.0`, or date-based `2026-10-19`

Example

- infra.rag.retrieve.v1

Catalog layout

- Project templates live at `docs/02-projects/<project>/prompt-templates/`
- Shared templates live at `docs/03-templates/`

MCP integration

- Platform exposes a simple JSON endpoint `/mcp/templates?q=prompt_id` returning metadata and template body.
- Clients should cache the catalog with TTL and validate template versions before use.
