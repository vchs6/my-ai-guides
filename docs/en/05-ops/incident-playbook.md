# Incident Playbook — AI System

Scope

This playbook covers incidents where an AI agent produced harmful output, leaked sensitive data, or caused unintended external side effects.

Immediate steps (first 15 minutes)

1. Stop new requests: flip the feature flag or redirect traffic to safe fallback.
2. Capture logs and conversation snapshot (request id, prompt id, model name, raw output).
3. Notify on-call SRE + Security via PagerDuty/Slack with incident tag `ai-incident`.

Triage (15-60 minutes)

1. Classify incident: hallucination / PII leak / external action / other.
2. If PII leak: contact Privacy Officer to assess breach and notify according to policy.
3. Begin rollback if release change triggered the incident.

Recovery & Postmortem

- Restore service after patch and regression tests pass.
- Draft postmortem within 72 hours including root cause, fix, and follow-ups (e.g., additional tests, monitoring).
