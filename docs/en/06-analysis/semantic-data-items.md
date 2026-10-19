# Semantic Data Items Produced by Project Analysis Tools — Overview

This document aggregates, categorizes, and explains the semantic data items commonly produced by modern project analysis tools and software. It is intended for architects, platform teams, security/compliance, and analytics engineers who need a near-complete inventory of semantic artifacts that can be collected, stored, and used for governance, automation, and observability across multiple subprojects.

Scope of research

I examined broadly used tools and categories (version control platforms, CI/CD, static analysis, security scanners, dependency managers, SBOM tools, testing & coverage tools, code-quality platforms, observability & telemetry, issue trackers, API & schema extractors, ML/Model management tools, and code/semantic indexing systems). Representative tools referenced: Git / GitHub / GitLab, Jenkins / GitHub Actions / CircleCI, SonarQube, CodeQL, Semgrep, ESLint, mypy, OpenAPI / Swagger, DependaBot / Snyk / Trivy / Clair, CycloneDX / SPDX, JFrog Xray, Coverity, Jaeger/Zipkin/Prometheus, and embedding/indexing systems (repo search, code embeddings).

How to read this file

- Items are grouped by semantic layer and source type (e.g., version-control, CI, static analysis).
- For each semantic item I provide: short name, description, data type / structure, example values, common source tools, and typical usage.
- Use this inventory to design a project metadata schema, observability pipeline, or governance data lake.

Summary of categories

1. Repository & VCS metadata
2. Commit & Change metadata
3. Pull Request / Merge Request metadata
4. Contributor & Ownership metadata
5. Code structure & static semantics
6. API & schema artifacts
7. Dependency & package metadata
8. Build & CI/CD artifacts
9. Test & coverage artifacts
10. Code quality & complexity metrics
11. Security & vulnerability artifacts
12. SBOM / supply-chain artifacts
13. Licensing & compliance artifacts
14. Runtime & observability telemetry
15. Issue / backlog / release artifacts
16. Release & artifact provenance
17. Developer productivity and delivery metrics
18. Code search & semantic indexing artifacts
19. ML / Model / Data-specific artifacts
20. Privacy & PII detection artifacts

Detailed items (by category)

1) Repository & VCS metadata
- repo_id: Unique repository identifier (string). Example: `vchs6/my-ai-guides`.
  - Source: Git hosting (GitHub/GitLab)
  - Usage: dataset joins, ownership mapping, entitlement checks
- default_branch: Default branch name (string). Example: `main`.
  - Source: Git host API
- visibility: public/private/internal (enum)
  - Source: Git host
- topics/tags: repository topics (array of strings)
  - Source: Git host
- repo_description: short text
  - Usage: search, onboarding

2) Commit & Change metadata
- commit_sha / id: full commit hash (string)
  - Example: `45d6075bf616...`
- author: {name, email, timestamp}
- committer: {name, email, timestamp}
- commit_message: raw commit message (string)
- files_changed: list of file paths changed in commit (array)
- added/removed/modified counts (integers)
- diff hunks / patch: textual patch (string)
- code_churn: lines added / deleted (ints)
- change_type tags: e.g., refactor/bugfix/feature/docs (inferred or via commit message)
- semantic_tags: labels inferred from content (e.g., "prompt-template", "infrastructure", "policy") produced by code classifiers

3) Pull Request / Merge Request metadata
- pr_id / number, pr_uuid
- title, description (text)
- author, assignees, reviewers (users)
- labels: tags assigned (array)
- state: open/closed/merged
- merge_commit_sha
- changed_files list (paths)
- review_comments: list {author, body, timestamp, path, line}
- approvals: list of approvers and timestamps
- checks_status: aggregated CI status (pass/fail/pending)
- associated_issues (IDs)
- risk_level: annotated risk classification (low/medium/high) — often added by policy scanners
- required_approvals/policies: list of required approvers (org policy)

4) Contributor & Ownership metadata
- contributor_id (user id), email, display name
- contributor_activity: commits, PRs, reviews counts (time window)
- code_ownership: file/dir -> owner(s) mapping (CODEOWNERS file or heuristics)
- team/organization mapping
- oncall/rotations linked to components

5) Code structure & static semantics
- file-level metadata: path, language, size, hash
- module / package definitions (e.g., Java packages, Python modules)
- function / class signatures: name, args, return type (where statically inferable)
- exported symbols / public API list
- call graph snippets (edges between functions/modules)
- control-flow graphs, data-flow summaries (produced by deeper static-analysis)
- type annotations / inferred types (e.g., mypy outputs)
- TODO/TODOs and FIXMEs (line items extracted)
- comments / docstrings (extracted bodies)
- code smell tags (long method, god class) found by linters or code analyzers
- semantic metrics per file/function: cyclomatic_complexity, cognitive_complexity, nesting_depth

6) API & schema artifacts
- OpenAPI / Swagger specs (full JSON/YAML) — endpoints, paths, methods
- gRPC proto definitions (services, RPCs, messages)
- GraphQL schema (type definitions, queries/mutations)
- Message/event schemas used in async systems (Avro/JSON Schema/Protobuf)
- DTO / model schemas derived from source code
- API surface summary: endpoint_count, public_endpoints list
- auth_requirements per endpoint (oauth2, api-key)

7) Dependency & package metadata
- direct_dependencies: list {name, version, scope, source}
- transitive_dependencies: full dependency tree
- license per dependency
- latest_vulnerable_version mappings (from scanners)
- dependency_health indicators: last_release_date, maintainer activity
- package lock / manifest files content (package.json, pom.xml, requirements.txt)
- outdated/dependabot suggestions

8) Build & CI/CD artifacts
- build_id, pipeline_id
- build_status, job_status (success/fail)
- build_logs (text), build_duration
- artifacts produced: names, sizes, artifact URLs
- environment / runner metadata (container image, OS, tool versions)
- container image tags and digests
- build provenance: source commit, builder identity, build toolchain

9) Test & coverage artifacts
- test_suites: names and counts
- tests_run, tests_passed, tests_failed, tests_skipped
- flakiness metrics: historical failure rate per test
- test_logs, failure stack traces
- code_coverage_percent (global) and coverage_per_file
- mutation testing results (if used)
- golden/gold outputs for regression tests

10) Code quality & complexity metrics
- lint_warnings / lint_errors (counts and per-file lists)
- code_duplication_percent and locations
- maintainability_index (SonarQube metric)
- technical_debt estimate (e.g., SQALE remediation time)
- hotspots: files with high churn + low coverage
- smells / rule violations from static analyzers

11) Security & vulnerability artifacts
- vulnerability findings: {id (CVE or internal), package, severity, description, paths_affected, exploitability, references}
- secrets detection alerts: {file, line, type (aws_key, jwt_secret), confidence}
- SAST findings: {rule_id, description, severity, trace}
- DAST findings (for running webapp scans): endpoints, payload, evidence
- misconfiguration findings (e.g., open ports, weak TLS config) from infra scanners
- remediation status: assigned_to, fix_version, PR reference
- risk_score / composite security risk per component

12) SBOM / supply-chain artifacts
- SBOM formats: CycloneDX / SPDX outputs listing components, versions, hashes
- provenance fields: build tool, builder identity, source commit
- image vulnerabilities linked to SBOM components
- attestations / signatures for artifacts (in-toto, sigstore provenance)

13) Licensing & compliance artifacts
- license_detection: detected license type per file/component (MIT, Apache-2.0, GPL)
- license_conflicts: list of problematic license combinations
- third_party_components list with license and copyright
- export_control / usage_restrictions flags

14) Runtime & observability telemetry
- metrics: latency, error_rate, requests_per_second (time-series)
- traces: spans, duration, service topology
- logs: structured logs with fields (request_id, user_id, component, level, message)
- resource metrics: CPU, memory, disk usage for services
- feature flag states per environment
- runtime configuration & secrets exposure indicators
- anomalies / alert events (threshold-based or ML-detected)

15) Issue / backlog / release artifacts
- issue_id, title, description, assignee, labels, state, created_at, closed_at
- release notes / changelog entries (semantic or manual)
- milestone / version mappings
- work_item relationships (blocks, duplicates, related)
- estimated_effort / story_points

16) Release & artifact provenance
- release_tag, release_notes, release_date
- artifact_checksums (sha256) and storage location
- deployment_history: env, time, operator, commit_deployed
- rollback_points and prior_release mapping

17) Developer productivity and delivery metrics
- lead_time_for_changes (time from PR creation to merge)
- deployment_frequency
- mean_time_to_restore (MTTR)
- PR_size distribution (lines changed)
- review_latency: time to first review, time to approve
- contributor_network graphs (who reviews whose PRs)

18) Code search & semantic indexing artifacts
- code_embeddings: vector representations per file/function/snippet (floats + metadata)
- index_shards / document ids linking to source paths
- semantic_tags / topics derived from embedding clusters (e.g., "auth", "billing")
- similarity_scores for fuzzy matching
- symbol tables and cross-references (where symbol defined/used)

19) ML / Model / Data-specific artifacts
- dataset manifests: dataset_id, files, schema, provenance, license
- model_metadata: model_id, architecture, version, training_dataset_ids, hyperparameters, training_time, eval_metrics
- model_card / README describing intended use, limitations, fairness metrics
- model_weights checksum and storage location
- drift metrics: data drift, concept drift, prediction distribution shifts
- experiment runs: run_id, parameters, metrics, artifacts
- prompt catalog entries (prompt id, template, examples, attribution)
- prompt usage telemetry: prompt_id, counts, avg_tokens, error_rate
- red-teaming logs specific to model behavior

20) Privacy & PII detection artifacts
- pii_findings: {file/path, snippet, pii_type (email, ssn), confidence}
- redaction_status: whether and how redaction was applied
- consent_records: user consent metadata for storing memory or data
- data_retention_flags: TTL settings per record

Cross-cutting notes and recommendations

- Normalization: Standardize identifiers (repo_id, commit_sha, prompt_id, model_id) across data producers so different pipelines can join records reliably.
- Schema & types: adopt versioned JSON Schema for each artifact type. Where heavy numeric metrics exist, use timeseries stores (Prometheus/Influx) and for event logs use JSON event stores.
- Provenance: always capture source tool, timestamp, and evidence (e.g., link to GitHub PR, build id, or S3 artifact) to support audits.
- Privacy & access control: PII/secret-related artifacts should be redacted at ingestion and stored in encrypted stores with tight RBAC.
- Retention & size: many artifacts (build logs, traces, embeddings) are large — include retention policies and aggregation policies.

Typical producers (representative tools)

- VCS / Repo: Git, GitHub API, GitLab API
- CI/CD: GitHub Actions, Jenkins, CircleCI, GitLab CI
- Static Analysis: SonarQube, CodeQL, Semgrep, ESLint, Pylint, mypy, clang-tidy
- Security Scanners (SCA/SAST/DAST): Snyk, Dependabot, Trivy, Clair, OWASP ZAP
- SBOM/Provenance: CycloneDX, SPDX, Sigstore, in-toto
- Test/Coverage: pytest, junit, coverage.py, JaCoCo
- Observability: Prometheus, Grafana, Jaeger, Zipkin, ELK/EFK
- Artifact registries: Docker Hub, GitHub Packages, JFrog Artifactory
- Issue / Project Management: GitHub Issues, Jira, Linear
- Semantic Indexing: sourcegraph, repo-search, embeddings + vector DBs (Milvus, Pinecone)
- ML infra: MLFlow, Weights & Biases, TensorBoard, S3-like storage for datasets

Storage & ingestion patterns

- Event-driven ingestion: webhooks (push/PR events), CI job outputs, scanner webhooks
- Batch ingestion: periodic SBOM generation, nightly static-analysis reports
- Streaming telemetry: traces and metrics to monitoring systems
- Artifact storage: binary artifacts in artifact registries with metadata in catalog

What's commonly missing or inconsistent across tools

- Uniform identifiers (many tools use different IDs for same concept)
- Standard schema for security findings (fields differ across SCA/SAST/DAST tools)
- Consistent ownership / CODEOWNERS normalization
- Provenance linking between SBOM, build, and runtime artifacts
- Semantic tags for prompts / AI-related assets — still emerging practice

Next steps (practical)

1. Map these items to your organization's canonical schema. Start with a core set: repo_id, commit_sha, pr_id, prompt_id, model_id, build_id, artifact_id.
2. Build ingest adapters for high-value producers: GitHub events, CI logs, CodeQL/Semgrep JSON outputs, SCA outputs (Snyk/Trivy), SBOM (CycloneDX/SPDX), test coverage reports.
3. Enforce metadata at source: encourage prompt templates and model cards to embed IDs and metadata to ease joinability.
4. Define retention and access policies for heavy or sensitive artifacts.

If you want, I will:
- produce a JSON Schema bundle for the top N items (repo, commit, pr, build, sbom, vulnerability) so you can start ingest pipelines; and
- add these schema files into this repository under `docs/en/06-analysis/schemas/` and `docs/zh/06-analysis/schemas/`.

References (representative)

- GitHub REST API docs
- SonarQube metrics definitions
- CodeQL databases and output formats
- CycloneDX and SPDX SBOM specifications
- Snyk, Trivy, Dependabot public docs
- OpenAPI / Swagger specs


