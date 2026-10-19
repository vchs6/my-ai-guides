# Platform Overview

This layer describes the shared platform capabilities teams rely on: prompt catalog conventions, MCP integration, shared builders and monitoring conventions.

Audience: Platform engineers, prompt library maintainers, integrators.

Key points

- Prompt-id format: `<team>.<project>.<template>.<version>` (e.g. infra.rag.retrieve.v1)
- Prompt-as-code: store template files under `docs/02-projects/<project>/prompt-templates/` with metadata.
- Expose prompt catalog as an MCP endpoint to allow local agent runtimes to query templates.

Onboarding

- To publish a template: add file + metadata → open PR → unit tests + battle-subset run → merge.
