# Ingest Adapters Mapping & Implementation Notes

This document lists practical ingest adapters for commonly used tools, mapping each tool's primary outputs to canonical schema targets. It includes recommended ingest patterns, event types, and transformation notes.

Design principles

- Prefer event-driven ingestion where available (webhooks).
- Use scheduled batch pulls for tools that emit reports (SBOM, nightly scans).
- Normalize IDs and attach schema_version on ingest.
- Redact sensitive fields early and route secrets/PII to protected stores.

Adapter list

1. GitHub / GitLab (VCS events)
- Sources: push events, commit webhooks, pull_request webhooks
- Targets: commit.jsonschema, pr.jsonschema
- Ingest pattern: webhook -> validation -> enrichment (resolve user ids) -> store
- Notes: include full patch if needed; be mindful of size

2. CI Systems (GitHub Actions, Jenkins, GitLab CI, CircleCI)
- Sources: job/webhook events, build logs, artifacts
- Targets: build.jsonschema, test_report.jsonschema
- Ingest pattern: webhook / artifact upload -> parse logs, extract artifacts list -> store artifact metadata
- Notes: store logs in blob storage and link via URL in schema

3. Static analysis / SAST (CodeQL, Semgrep, SonarQube)
- Sources: analysis reports (JSON/SARIF), API
- Targets: vulnerability.jsonschema (SAST findings), code_quality metrics
- Ingest pattern: scheduled scan or CI-run report upload -> transform SARIF/CodeQL JSON -> map to canonical vulnerability schema
- Notes: preserve rule_id and trace information for triage

4. SCA / Dependency Scanners (Snyk, Dependabot, Trivy)
- Sources: vulnerability reports, dependency lists
- Targets: vulnerability.jsonschema, sbom.jsonschema
- Ingest pattern: scan output -> map package coordinates -> correlate with SBOM and build
- Notes: deduplicate by package+version+CVE

5. SBOM Generators (CycloneDX, SPDX)
- Sources: CycloneDX / SPDX artifacts (JSON/XML)
- Targets: sbom.jsonschema
- Ingest pattern: file upload or CI artifact -> validate schema -> store components and hashes
- Notes: extract purl and license fields for compliance pipelines

6. Coverage & Test Tools (pytest/junit, JaCoCo, coverage.py)
- Sources: junit XML, coverage reports
- Targets: test_report.jsonschema
- Ingest pattern: CI artifact upload -> parse -> store counts, failures, coverage

7. Artifact Registries / Container Scanners (Docker Registry, JFrog, Harbor)
- Sources: image metadata, vulnerability scans
- Targets: sbom.jsonschema, vulnerability.jsonschema, release provenance
- Ingest pattern: registry webhook or scheduled scan -> map image digest to SBOM components

8. Observability (Prometheus, Jaeger, ELK)
- Sources: metrics, traces, logs
- Targets: runtime telemetry (time-series DB, traces store). Not JSON Schema-ified here but should be linked via artifact IDs and request_ids
- Ingest pattern: metrics scraping, log ingestion pipelines

9. Issue Trackers (GitHub Issues, Jira, Linear)
- Sources: issue webhooks, API
- Targets: issue artifacts schema (can be simple JSON documents)
- Ingest pattern: webhook -> normalize fields -> store

10. ML infra (MLflow, W&B, TensorBoard)
- Sources: experiment runs, model metadata, dataset manifests
- Targets: model.jsonschema, dataset manifests
- Ingest pattern: export metadata or use API to push artifacts -> store with model_id and provenance

11. Semantic Indexing / Embeddings (Sourcegraph, vector DBs)
- Sources: embedding generation pipelines
- Targets: semantic index entries: {doc_id, path, vector, metadata}
- Ingest pattern: batch or streaming vector insert -> link to source path

Transformation & deduplication notes

- Timestamp normalization to UTC
- Deduplicate vulnerabilities across scanners by CVE+package+version
- Enrich commits/PRs with user directory (LDAP) data for teams
- Keep raw original report as evidence alongside normalized schema

Security & privacy considerations

- Redact secrets and PII at edge; maintain separate secure store for evidence when necessary.
- Authenticate webhooks and validate payloads.
- Rate-limit heavy ingestion sources (embeddings, logs).

Examples & starter hints

- GitHub webhook -> Lambda / small collector -> validate -> transform to commit/pr schema -> push to event store (e.g., Kafka)
- CI -> upload junit/coverage artifacts -> workflow job calls ingestion API to validate and store report
- CodeQL -> run in CI, upload SARIF -> ingestion pipeline converts SARIF -> canonical vulnerability documents

Files added

- docs/en/06-analysis/adapters.md
- docs/en/06-analysis/schemas/commit.jsonschema
- docs/en/06-analysis/schemas/pr.jsonschema
- docs/en/06-analysis/schemas/build.jsonschema
- docs/en/06-analysis/schemas/sbom.jsonschema
- docs/en/06-analysis/schemas/vulnerability.jsonschema
- docs/en/06-analysis/schemas/model.jsonschema
- docs/en/06-analysis/schemas/prompt.jsonschema
- docs/en/06-analysis/schemas/test_report.jsonschema
