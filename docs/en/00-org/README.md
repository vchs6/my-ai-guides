# Organization Governance — my-ai-guides

Purpose

This layer defines organization-level governance for all AI projects that use this repo: roles, responsibilities, release gating, risk classification, and the mandatory Battle (red-team) cadence.

Audience: Executive owner, Product Owners, Security/Privacy, Legal, Platform owners.

Owners

- Org Owner: @org-owner (replace)
- Security / Red Team: @security-team
- Platform: @platform-team

Risk matrix (short)

- Low: no PII, no external side-effects (read-only answers). Normal CI.
- Medium: may return sensitive business facts, can suggest actions. Requires PR + automated battle-subset run.
- High: performs external actions (writes DB, sends email, payments). Requires human approval, sandboxed execution and full Battle.

Release gates

- Any change touching a prompt used in a High-risk flow must:
  1. Create PR and reference `risk:high`
  2. Add unit tests and battle cases
  3. Pass automated tests and security scans
  4. Receive explicit approval from Org Owner or Security

Monthly cadence

- Teams must run a Battle subset at least once per month and upload results to `docs/04-examples/`.

Where to start

- Contribute templates to `docs/03-templates/`.
- Create project docs under `docs/02-projects/<project-name>/`.
