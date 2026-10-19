# Semantic Data Inventory (excluding repository-level items)

This report lists and explains semantic data items produced by project analysis tools, excluding any repository-level metadata (repo id, visibility, topics). Use this as a canonical reference for designing ingestion schemas and analytics pipelines.

Scope & purpose

- Scope: commit, PR, contributor, code structure, API/schema, dependencies, build/CI, test/coverage, code quality, security findings, SBOM, licensing, runtime telemetry, issues/releases, artifacts provenance, developer metrics, semantic indexing, ML/model/data artifacts, and privacy/PII findings.
- Purpose: provide a compact, implementable inventory (no repo-level fields) for teams building data lakes, governance, and observability.

Core categories and items

1. Commit & Change metadata
- commit_sha: full commit hash
- author / committer: {name, email, timestamp}
- commit_message
- files_changed: list of paths
- added/removed/modified counts
- diff / patch (optional)
- code_churn: {lines_added, lines_deleted}
- change_type: inferred tag (fix/feature/refactor)
- semantic_tags: classifier tags (e.g., "prompt-template")

2. Pull Request / Merge Request metadata
- pr_id / number
- title, description
- author, assignees, reviewers
- labels, state (open/closed/merged)
- merge_commit_sha
- changed_files
- review_comments (author, body, timestamp, path, line)
- approvals and approval timestamps
- checks_status: aggregated CI status
- associated_issues
- risk_level: org-assigned (low/medium/high)
- required_policies: required approvers/branch-protection rules

3. Contributor & Ownership metadata
- contributor_id, email, display name
- activity metrics: commits, PRs, reviews in window
- code_ownership mapping (file/dir -> owners)
- team/organization mapping

4. Code structure & static semantics
- file metadata: path, language, size, file_hash
- modules / packages, exported symbols
- function/class signatures and docstrings
- inferred types and annotations
- call-graph edges (source, target)
- control/data-flow summaries
- TODO/FIXME items
- complexity metrics: cyclomatic, cognitive

5. API & schema artifacts
- OpenAPI/Swagger specs (full payload)
- gRPC proto definitions
- GraphQL schema
- message schemas (Avro/Protobuf/JSON Schema)
- API surface summary and auth requirements

6. Dependency & package metadata
- direct_dependencies: {name,version,scope}
- transitive_dependencies: full tree
- license per dependency
- outdated suggestions
- dependency_health indicators

7. Build & CI/CD artifacts
- build_id / pipeline_id
- status, start_time, end_time, duration
- build_logs URL, summary
- produced_artifacts: [{name,url,size,checksum}]
- runner/environment metadata
- container image tags & digests
- build_provenance (source commit, toolchain)

8. Test & coverage artifacts
- test_suites, counts (run/pass/fail/skip)
- flakiness metrics
- failure details (test name, message, stack)
- coverage: overall percent and per-file
- golden outputs used for regression

9. Code quality & complexity metrics
- lint_warnings/errors list
- duplication_percent and locations
- maintainability_index / technical_debt estimate
- hotspots (high churn + low coverage)

10. Security & vulnerability artifacts
- vulnerability findings: {id,source,package/component,version,severity,description,paths,evidence,references}
- secrets detection alerts: {file,line,type,confidence}
- SAST findings: rule_id, trace, severity
- DAST findings for running systems
- remediation status and assigned PRs
- composite risk_score per component

11. SBOM & supply-chain artifacts
- SBOM record (CycloneDX/SPDX): components, versions, hashes, licenses
- attestation/signature metadata (sigstore / in-toto)
- image vulnerability mappings to SBOM components

12. Licensing & compliance artifacts
- detected_license per component/file
- license_conflicts
- third_party_components list with license and copyright
- usage_restrictions / export_control flags

13. Runtime & observability telemetry
- metrics: latency, error_rate, throughput (time-series)
- traces: spans, durations, service topology
- structured logs: {request_id,component,level,message,context}
- resource metrics: CPU, memory
- feature flags per environment
- anomaly/alert events

14. Issue / backlog / release artifacts
- issue_id, title, description, assignee, labels, state, timestamps
- release_notes / changelog entries
- milestone/version mapping
- workitem relationships (blocks, duplicates)
- estimated effort / story points

15. Release & artifact provenance
- release_tag, release_notes, release_date
- artifact_checksums and storage locations
- deployment_history: env, time, operator, commit_deployed
- rollback_points

16. Developer productivity & delivery metrics
- lead_time_for_changes
- deployment_frequency
- MTTR
- PR_size distribution and review_latency
- contributor_network graphs

17. Code search & semantic indexing artifacts
- code_embeddings per file/function/snippet (vectors + metadata)
- index document ids linking to source paths
- semantic_tags / topics from clustering
- symbol tables and cross-references

18. ML / Model / Data-specific artifacts
- dataset manifests: id, files, schema, provenance, license
- model_metadata: id, architecture, version, training_data_ids, hyperparameters, eval_metrics
- model_card: intended_use, limitations, fairness notes
- model_weights checksum & storage
- drift metrics and experiment runs
- prompt catalog entries and prompt usage telemetry

19. Privacy & PII detection artifacts
- pii_findings: {path, snippet, pii_type, confidence}
- redaction_status
- consent_records for stored memory/data
- data_retention flags

Cross-cutting recommendations

- Use canonical identifiers (commit_sha, pr_id, build_id, prompt_id, model_id) for joins.
- Adopt versioned JSON Schema for each artifact type and store schema versions with ingested records.
- Capture provenance: source tool, timestamp, evidence link.
- Redact PII at ingestion and enforce RBAC for sensitive artifacts.
- Apply retention policies and aggregation for heavy artifacts (logs, embeddings).

Next steps

1. Generate JSON Schema bundle for core entities (commit, pr, build, sbom, vulnerability, model, prompt, test_report).
2. Create ingestion adapters for key tools (GitHub/GitLab, CI systems, CodeQL/Semgrep, Snyk/Trivy, CycloneDX/SPDX, coverage tools, Prometheus). See adapters list.

Files added

- docs/en/06-analysis/semantic-data-items-no-repo.md
- docs/en/06-analysis/adapters.md
- docs/en/06-analysis/schemas/*.jsonschema

