# OpenSpec & Superpowers Integration Guide (English)

Overview

This guide describes how to use OpenSpec as the canonical specification format and Superpowers as the Skill/engine for validation, publishing, sandboxed execution, and running battle suites. It lists required Skill capabilities and minimal operational requirements teams must satisfy to use these tools in our environment.

Why OpenSpec + Superpowers

- OpenSpec provides a consistent, versioned schema for agent manifests, prompt templates, I/O schemas and battle metadata. Using a single spec simplifies validation, codegen and cross-team discovery.
- Superpowers provides the Skill integration to validate OpenSpec artifacts, publish them to the prompt catalog, run controlled sandboxed executions, and orchestrate battle test runs. Superpowers can be integrated into CI and the agent harness.

Repository conventions

- All agent artifacts MUST include an OpenSpec header containing at least: `spec_version`, `kind` (agent|prompt|battle), `id`, `version`.
- Artifacts locations:
  - Agents: `docs/02-projects/<project>/agents/*.yaml`
  - Prompts: `docs/02-projects/<project>/prompt-templates/*.md` (include OpenSpec header)
  - Battle cases: `docs/02-projects/<project>/tests/battle/*.yaml`

Superpowers Skill requirements (minimum capabilities)

Teams integrating with Superpowers MUST ensure the following capabilities are provisioned for the Skill or its runner:

1. Authentication & Authorization
- Skill must authenticate via an organization-managed service account or OIDC token.
- RBAC: support role-based scopes (read:catalog, write:catalog, run:tests, manage:agents).

2. Validation & Schema Enforcement
- Skill validates incoming artifacts against OpenSpec schemas and the local prompt/payload JSON Schemas.
- Validation must be strict for risk_level=high artifacts.

3. Catalog Publishing
- Ability to publish validated artifacts to a centralized prompt catalog (MCP-compatible).
- Publish must record provenance: user, commit_sha, timestamp, tool_version.

4. Sandboxed Execution
- Support executing agents and battle cases in an isolated sandbox with resource limits, network egress controls, and simulated tool adapters.
- Sandboxes should produce full transcripts, detector events, and normalized tool call logs.

5. Battle Runner & Detectors
- Provide an automated runner for battle-case YAML, attach detectors (PII, secrets, unauthorized action), and emit structured results.
- Expose runner logs and results via API for CI integration.

6. Audit & Evidence Storage
- Persist evidence (transcripts, detector outputs) in an access-controlled evidence store; support redaction and retention policy.

7. CI / GitOps Integration
- Superpowers CLI or Skill endpoints must be callable from CI (GitHub Actions, Jenkins). Typical flows:
  - PR -> Superpowers validate -> publish to staging catalog -> run battle-subset -> return status

Security & Operational rules

- Minimum-permission principle: grant the Skill only the scopes necessary for its job.
- Network egress: sandboxed executions must default to no outbound network unless explicitly allowed and audited.
- Secrets: Skill must not have access to raw secrets; adapters should retrieve credentials at runtime with short-lived tokens.

Onboarding checklist for a team

- [ ] Install OpenSpec linting & Superpowers CLI in local dev environment
- [ ] Add OpenSpec header to all agent/prompt/battle artifacts
- [ ] Configure Superpowers service account and RBAC scopes
- [ ] Add CI step to call Superpowers validate and run battle-subset on PRs
- [ ] Register team contact & battle-suite owner in artifact metadata

Example CI snippet (GitHub Actions)

```yaml
name: Validate Agent Artifact
on: [pull_request]
jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Install Superpowers CLI
        run: |
          curl -sSfL https://example.com/superpowers/install.sh | bash
      - name: Validate artifacts
        run: |
          superpowers validate --path docs/02-projects/${{ env.PROJECT }}/agents
      - name: Run battle-subset
        run: |
          superpowers battle run --subset --project ${{ env.PROJECT }}
        env:
          SUPERPOWERS_TOKEN: ${{ secrets.SUPERPOWERS_TOKEN }}
```

Documentation & Support

- Teams must document artifact `spec_version` and Superpowers runner version in the README of each project.
- For internal support, list the people responsible for the Superpowers integration under project metadata.

