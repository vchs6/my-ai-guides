Unit tests for prompt templates should be placed under this directory. Each test should:

1. Provide example inputs (see template examples).
2. Execute the prompt builder / template in a deterministic test mode (mock model or seed) and validate output schema.
3. Assert safety constraints (e.g., redaction behavior).

Example test runner flow (pseudo):

- Load `infra.rag.retrieve.v1.md` metadata and prompt
- Run prompt with `query` = "test query", `k` = 2 using a deterministic mock
- Verify returned JSON has `retrieved_ids` and `snippets` arrays
- Verify no un-redacted PII present

CI integration: wire these tests into `.github/workflows/test.yml` to run on PRs.
