# Four-level Task Decomposition Specification (English)

Purpose

Provide a concise 4-level task decomposition standard to classify work, map agents to responsibilities, and inform Battle/test design. Teams should apply this decomposition to every agent, prompt, and battle case to make scope, authority, and expected impacts explicit.

Levels

- Level 0 — Mission / Business Objective
  - High-level outcome the system supports (e.g., "Reduce payment fraud", "Automate invoice approvals").
  - Owned by Product/Business.

- Level 1 — Capability / Epic
  - Coarse capability that contributes to the mission (e.g., "Payment Authorization", "Vendor Management").
  - Mapped to a set of agents / services.

- Level 2 — Task / Decision Unit
  - Discrete decision or user-facing task (e.g., "Authorize payment over threshold", "Classify vendor risk").
  - Should be labeled with task_type (informational, transformational, decision_support, decision_execution, operational).
  - Must include authority_level, autonomy_allowed, required_approvals, expected_impact.

- Level 3 — Action / Atomic Operation
  - Atomic steps required to execute a Task (e.g., "validate balance", "initiate transfer API call", "record approval event").
  - Represented as tool calls in agent metadata and must have deterministic adapters.

Guidelines

- Every agent and battle-case MUST reference the Level 0..3 lineage in metadata (e.g., mission_id, capability_id, task_id, actions[]).
- Decision tasks (Level 2 labeled decision_support or decision_execution) must include simulator_spec for battle testing and a rollback strategy.
- Use stable IDs for lineage to enable joins across telemetry, audits and battle reports.

Example (mapping)

- Mission: Reduce payment fraud
- Capability: Payment Authorization
- Task: Authorize high-value payment (task_type=decision_execution, authority_level=require_human)
- Actions: [validate_balance, fraud_check, request_human_approval, execute_transfer]

Benefits

- Enables automatic CI gating: PRs that modify Level 2 decision tasks trigger deep battle-subsets and simulation runs.
- Simplifies audits: you can trace an incident from action -> task -> capability -> mission.
- Helps assign ownership and SLA obligations at the right granularity.
