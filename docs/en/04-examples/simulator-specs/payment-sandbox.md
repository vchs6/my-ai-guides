# Payment Sandbox Simulator — Spec (English)

Purpose

Describe minimal simulator specification needed by battle cases that test payment-related decision_execution agents.

Simulator capabilities

- Accept synthetic payment dataset and apply business logic similar to production (balance checks, fraud rules in simplified form).
- Expose API for agent's tool adapter to call: POST /simulate/transfer with payload {vendor, amount, requester, context}
- Return structured response {status: approved/pending/rejected, reason, txn_id}
- Support an "approval" simulation endpoint to record human approvals: POST /simulate/approve {txn_id, approver}
- Emit events/logs that the battle runner can observe (e.g., approval events, executed transactions)

Data & Privacy

- Only synthetic or anonymized data to be used.
- Simulator must not contact external payment networks.

Deployment

- Lightweight docker image with REST API, preloaded synthetic datasets. Provide CLI to seed data and reset state.

Integration with Superpowers

- Superpowers runner can boot the simulator container during battle-run when `simulator_spec.type` == "payment-sandbox".
- Simulator service URL is provided to the agent adapter via environment variables.
