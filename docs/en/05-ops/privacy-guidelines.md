# Privacy Guidelines for Prompt Templates and Memory

Principles

- Minimize storage of PII; only store persistent memory after explicit user consent.
- When storing user data for personalization, apply encryption-at-rest and role-based access.
- Examples in prompt templates must not contain real PII. Use synthetic or anonymized examples only.

Redaction rules

- If any retrieved snippet contains PII, redact before presenting: replace names/emails/SSNs with `<REDACTED>` and add `redacted: true` field.

Data retention

- Default retention for conversational logs: 90 days for audit; 7 days for raw detailed logs unless flagged for longer retention.
- Long-term memories: TTL and explicit user controls to export or delete.
