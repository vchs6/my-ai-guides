# AGENTS — Agent Governance & Engineering (English)

Purpose

This document defines governance, architecture, coding principles, and operational requirements for agents (automated LLM-driven assistants/executors) used in production. It is intended for engineering teams, platform owners, security, and product managers.

Scope

Covers agent design, runtime harness, permissions model, prompt templates and prompt-as-code requirements, testing and Battle integration, logging and audit fields, and deployment controls.

Important: OpenSpec & Superpowers

This organization adopts OpenSpec and Superpowers as the primary development and orchestration tools for agent and prompt engineering. All agent artifacts, prompt templates, battle cases and runtime manifests SHOULD conform to the OpenSpec specification (where applicable) and be operable via the Superpowers Skill/CLI integration.

- OpenSpec: used as the canonical specification format for agent manifests, prompt templates, input/output schemas, and battle-case metadata. Templates should include an OpenSpec-compliant header with `spec_version`, `kind` and `id` fields.
- Superpowers: used as the engineering/runtime Skill that performs template validation, catalog publishing, sandboxed execution, and running battle suites. Agents must include meta fields required by the Superpowers Skill to enable automated validation and controlled execution.

Teams MUST follow the "Skill Requirements" described in the separate integration guide `docs/en/07-integrations/openspec-superpowers.md` (and `docs/zh/07-integrations/openspec-superpowers.md`). The integration guide defines the minimum capabilities, authentication, and policy gates required to use Superpowers in your environment.

1. Roles & Responsibilities

- Product Owner: defines allowed agent capabilities and high-level acceptance criteria.
- Agent Engineer: implements agent logic, templates, harness wiring, and unit tests.
- Platform Owner: provides agent runtime, registry, and MCP endpoints.
- Security / Red Team: defines forbidden actions, approves high-risk agents, runs Battle tests via Superpowers.
- SRE / Ops: observes runtime metrics, enforces quotas, handles incidents.

2. Agent Classification & Risk Levels

- Risk levels: LOW / MEDIUM / HIGH.
  - LOW: read-only answers, no state mutation, no PII access.
  - MEDIUM: may suggest actions, query internal data, but cannot perform external side-effects without approval.
  - HIGH: can perform state changes, external calls, or access sensitive data — requires human-in-loop, sandboxing, and full Battle.

Agents must declare their risk level in metadata and include a justification.

3. Agent Metadata & Prompt-as-Code Requirements

Each agent must be represented as a prompt-as-code artifact containing metadata and template body. Minimum fields:

- agent_id (string)
- name, owner, contact
- version
- risk_level
- description / purpose
- allowed_tools: list of tool IDs the agent may call
- forbidden_actions: explicit prohibitions
- inputs schema & outputs schema (JSON Schema)
- examples (positive/negative)
- battle_suite_id (reference to battle cases)
- spec_version (OpenSpec version)

Templates should be stored under: `docs/02-projects/<project>/agents/` and exposed via the prompt catalog (MCP). When using Superpowers, include the Superpowers metadata block (see integration guide) so the Skill can validate and publish the artifact.

4. Harness / Runtime Requirements

The runtime must implement:

- Planner/Executor separation: planning phase (decide actions) and execution phase (deterministic execution control).
- Tool adapters: wrap external integrations with input validation, ACL checks, rate limits, and circuit breakers.
- Human-in-loop hooks: for HIGH-risk actions provide interactive approval workflows (Superpowers provides hooks to wire approval flows).
- Checkpointing & rollback: persist execution state and support rollback where feasible.
- Timeouts & retries: per-action and per-agent timeouts, bounded retries with backoff.
- Secrets & credential handling: do not allow agents to embed credentials in prompts; use credential vault and scoped tokens.

5. Function-calling & Tool Execution Flow

- Agents should output structured intent objects (e.g., {intent: "create_ticket", params: {...}}) rather than opaque natural language commands for any tool invocation.
- Executor verifies intent against allowed_tools and policies before calling adapters.
- All tool calls produce a normalized call record: {call_id, agent_id, tool_id, input, output, status, timestamp}.

6. Logging & Auditing

Every agent call must record the following fields in logs/audit store:

- request_id
- agent_id, agent_version, prompt_template_id
- user_id or actor
- input_snapshot (redact PII)
- context_snapshot (retrieved docs IDs, vector-search hits)
- intent_object (structured output from model)
- tool_calls: list of normalized call records
- model_meta: model_name, model_version, temperature, tokens_used
- result_snapshot (full response or structured output)
- decisions: approvals, denials (human-in-loop)
- timestamp, duration

Retention for audit logs: at least 90 days for non-sensitive artifacts; sensitive logs must follow privacy policy.

7. Testing & Battle Integration

- Unit tests: validate prompt template behavior using deterministic mocks and assert JSON schema outputs.
- Integration tests: run agent harness with mock adapters to verify end-to-end flows.
- Battle tests: associate each MEDIUM/HIGH agent with a battle_suite; PRs changing agent templates must run a battle-subset CI job using the Superpowers runner where possible.
- Regression: store gold conversations and compare embeddings or key assertions rather than exact text where nondeterminism exists.

8. Security & Safety Controls

- Deny-list and allow-list: platform enforces forbidden tool/endpoint lists; agent metadata must list allowed_tools.
- Rate limiting & quotas per agent and per tool.
- Policy enforcement: automatic checks for prompt-injection patterns and escaping attempts during CI (Superpowers includes validation plugins that can be enabled in CI pipelines).
- Minimal privileges: principle of least privilege for tools and credentials used by agents.

9. Deployment & Release Controls

- Agents are versioned. Breaking changes must bump major version and require full-suite Battle.
- Production rollouts use canary stages (e.g., 5% → 25% → 100%) with automated metric gating (safety breach rate, error rate, latency, task success).
- Emergency rollback: maintain fast rollback path and ensure database/side-effect compensation where applicable.

10. Observability & Metrics

Track these KPIs per agent:

- invocation_count
- avg_latency_ms
- error_rate
- tool_call_rate
- safety_breach_rate (from Battle and runtime detectors)
- tokens_per_invocation
- model_cost_per_invocation

11. Documentation & Model Cards

- Each agent must have a brief README: purpose, input/outputs, examples, risk, owned-by, deployment status, and model_card link.
- Model cards describe model limitations, intended use, training data lineage (if available), and recommended guardrails.

12. Privacy & Data Handling

- Agents must explicitly mark whether they store user data as memory. Persistent memory requires consent records and TTL.
- PII must be redacted before being stored or used in prompts without explicit consent.

13. Examples

- Minimal agent metadata example (YAML)

```yaml
spec_version: "openspec/v1"
agent_id: infra.rag.assistant.v1
name: Infra RAG Assistant
owner: team-a@example.com
version: v1
risk_level: medium
allowed_tools: ["search_docs","open_ticket"]
forbidden_actions: ["execute_shell","transfer_funds"]
inputs:
  - name: query
    type: string
outputs:
  schema: { type: object }
examples: []
superpowers:
  validate: true
  publish_to_catalog: true
```

14. Checklist for PRs that change agents

- [ ] Metadata present and valid
- [ ] Unit tests added
- [ ] Battle-subset test added (if risk >= medium)
- [ ] Security scan / injection lint passed
- [ ] Owner and platform reviewers assigned

References

- OpenSpec and Superpowers integration guide (see docs/en/07-integrations/openspec-superpowers.md)
- Prompt-as-code patterns, MCP, prompt catalog best practices.
