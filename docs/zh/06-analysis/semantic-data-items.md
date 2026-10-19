# 项目分析工具产生的语义数据项清单 — 概览

本文档汇总并分析了现代项目分析工具/软件通常会产生的语义数据项，面向架构师、平台团队、安全与合规、以及需要建立治理或分析数据湖的工程团队。

调研范围

我对常见工具与类别进行了广泛调研：版本控制平台、CI/CD、静态分析、安全扫描、依赖性管理、SBOM 工具、测试与覆盖率、代码质量平台、可观测性与遥测、问题跟踪、API/Schema 抽取、ML/模型管理与语义索引系统。参考工具示例：Git / GitHub / GitLab、Jenkins / GitHub Actions、SonarQube、CodeQL、Semgrep、ESLint、OpenAPI、Dependabot / Snyk / Trivy、CycloneDX / SPDX、JFrog Xray、Jaeger、Prometheus、向量数据库与 embedding 工具等。

如何阅读本文

- 条目按语义层级与来源类型分组（例如：版本控制、静态分析）。
- 每个语义项包含简短名称、说明、数据类型/结构、示例、常见产生工具与典型用途。
- 可将本清单作为设计项目元数据 schema、可观测流水线或治理数据湖的参考。

分类摘要

1. 仓库与 VCS 元数据
2. 提交与变更元数据
3. Pull Request / Merge Request 元数据
4. 贡献者与归属元数据
5. 代码结构与静态语义
6. API 与模式（schema）工件
7. 依赖性与包元数据
8. 构建与 CI/CD 工件
9. 测试与覆盖工件
10. 代码质量与复杂度指标
11. 安全与漏洞工件
12. SBOM / 供应链工件
13. 许可与合规工件
14. 运行时与可观测遥测
15. Issue / 迭代 / 发布工件
16. 发布与工件溯源
17. 开发者生产力与交付指标
18. 代码搜索与语义索引工件
19. ML / 模型 / 数据相关工件
20. 隐私与 PII 检测工件

详尽条目（按类别）

1）仓库与 VCS 元数据
- repo_id: 仓库唯一标识（字符串）。示例：`vchs6/my-ai-guides`。
  - 来源：Git 托管平台（GitHub/GitLab）
  - 用途：数据关联、权限映射
- default_branch：默认分支名称（字符串）。示例：`main`。
- visibility：公开/私有/组织内（枚举）
- topics/tags：仓库话题标签（字符串数组）
- repo_description：简短描述文本

2）提交与变更元数据
- commit_sha / id：提交哈希（字符串）
- author：{name, email, timestamp}
- committer：{name, email, timestamp}
- commit_message：提交信息（文本）
- files_changed：变更文件路径数组
- added/removed/modified counts（整数）
- diff hunks / patch：补丁文本
- code_churn：增加/删除代码行数
- change_type tags：从提交信息或分类器推断的变更类型（修复/新特性/重构）
- semantic_tags：内容分类标签（如 "prompt-template"）

3）Pull Request / Merge Request 元数据
- pr_id / number / uuid
- title, description
- author, assignees, reviewers
- labels（数组）
- state（open/closed/merged）
- merge_commit_sha
- changed_files 列表
- review_comments（包含路径/行）
- approvals 列表
- checks_status（CI 聚合状态）
- associated_issues
- risk_level（组织政策注释）
- required_approvals/policies

4）贡献者与归属元数据
- contributor_id, email, display name
- contributor_activity（提交/PR/评审计数）
- code_ownership（文件/目录到 owner 的映射）
- team/organization mapping
- oncall/排班信息

5）代码结构与静态语义
- file-level metadata：路径、语言、大小、hash
- module/package 定义
- function/class 签名（名称、参数、返回类型）
- exported symbols / public API 列表
- call graph 片段（函数/模块之间的边）
- control-flow / data-flow 摘要
- 类型注解 / 推断类型
- TODO/ FIXME 抽取项
- 注释 / 文档字符串
- code smell 标签（长函数、上帝对象）
- 每文件/函数语义指标：环路复杂度、认知复杂度、嵌套深度

6）API 与模式工件
- OpenAPI / Swagger 规范（完整 JSON/YAML）
- gRPC proto 定义（服务、RPC、消息）
- GraphQL schema
- 消息/事件模式（Avro/JSON Schema/Protobuf）
- DTO / 模型 schema
- API surface summary：端点计数、公开端点列表
- endpoint auth requirements

7）依赖性与包元数据
- direct_dependencies：{name, version, scope, source}
- transitive_dependencies：完整依赖树
- license per dependency
- latest_vulnerable_version（扫描器返回）
- dependency_health（最近发布时间、维护活跃度）
- 包管理器清单（package.json 等）
- outdated / dependabot 建议

8）构建与 CI/CD 工件
- build_id / pipeline_id
- build_status, job_status
- build_logs, build_duration
- artifacts：名称、大小、URL
- runner/environment metadata
- container image tags / digests
- build provenance（commit, builder identity, toolchain）

9）测试与覆盖工件
- test_suites 列表与计数
- tests_run / passed / failed / skipped
- flakiness（历史失败率）
- test_logs / 堆栈轨迹
- code_coverage_percent（全局/文件级）
- mutation testing 输出
- golden outputs

10）代码质量与复杂度指标
- lint_warnings / errors
- code_duplication_percent 与位置
- maintainability_index
- technical_debt 估计
- hotspots（高变动低覆盖）
- smell / rule violations

11）安全与漏洞工件
- vulnerability findings：{id (CVE 或内网 id), package, severity, description, paths}
- secrets detection：{file, line, type, confidence}
- SAST findings：{rule_id, severity, trace}
- DAST findings（运行时扫描的漏洞）
- misconfiguration findings
- remediation status（负责人、修复 PR）
- composite risk_score

12）SBOM / 供应链工件
- SBOM 输出（CycloneDX / SPDX）
- provenance 字段（构建工具、builder id、commit）
- image vulnerabilities 映射到 SBOM 组件
- 签名/attestation（in-toto, sigstore）

13）许可与合规工件
- license_detection（文件/组件的许可证类型）
- license_conflicts
- third_party_components 列表（含 license 与权利信息）
- export_control / 使用限制标记

14）运行时与可观测遥测
- metrics（延迟、错误率、吞吐）
- traces（span, duration, topology）
- structured logs（request_id, user_id, level, message）
- resource metrics（CPU/内存）
- feature flag 状态
- 运行时配置
- anomalies / 告警事件

15）Issue / 迭代 / 发布工件
- issue_id, title, description, assignee, labels, state, timestamps
- release notes / changelog
- milestone / version 映射
- work_item 关系（阻塞、重复）
- effort estimate / story points

16）发布与工件溯源
- release_tag, release_notes, date
- artifact_checksums 与存储位置
- deployment_history（环境、时间、操作人、commit）
- rollback points

17）开发者生产力与交付指标
- lead_time_for_changes
- deployment_frequency
- MTTR
- PR_size 分布
- review_latency
- contributor_network graphs

18）代码搜索与语义索引工件
- code_embeddings（向量）与元数据
- index document ids -> 源路径
- semantic_tags / topic clusters
- similarity scores
- symbol tables 和交叉引用

19）ML / 模型 / 数据相关工件
- dataset_manifest（id、文件、schema、来源、许可）
- model_metadata（id、架构、训练集、超参、评估指标）
- model_card（用途、限制、公平性指标）
- model_weights 校验和与存储位置
- drift metrics（数据/概念漂移）
- experiment runs（run_id、参数、指标）
- prompt catalog 条目与用量遥测
- 红队/战测日志（模型行为相关）

20）隐私与 PII 检测工件
- pii_findings：{path, snippet, pii_type, confidence}
- redaction_status
- consent_records（用户同意记录）
- data_retention_flags

跨切面建议

- 标准化标识符：对 repo_id、commit_sha、pr_id、prompt_id、model_id 等使用统一规则，简化跨数据源关联。
- 使用 JSON Schema：为每类工件发布版本化 JSON Schema。
- 溯源：采集源工具、时间戳与验证证据（PR 链接、构建 ID、artifact URL）。
- 隐私与访问控制：在摄取阶段对 PII/秘密进行脱敏并使用加密存储与严格 RBAC 控制。
- 存储策略：大体量工件（日志、embedding、构建产物）建立分级保留规则与聚合策略。

常见缺失或不一致点

- 不统一的实体 ID（不同工具对同一对象使用不同标识）
- 安全发现字段不标准化（SCA/SAST/DAST 输出格式差异）
- 归属/OWNERS 信息不一致
- SBOM 与构建/运行时溯源关联薄弱
- 针对 prompt 与 AI 资产的语义标签尚不普及

下一步建议（实操）

1. 先定义核心实体 schema：repo, commit, pr, build, sbom, vulnerability, prompt, model。将其设为入湖的规范。
2. 为高价值生产者建立摄取适配器：GitHub events, CI logs, CodeQL/Semgrep JSON, SCA 输出, CycloneDX/SPDX, coverage reports。
3. 在源头强制元数据：例如在 prompt 模板与 model card 中嵌入明确 ID 与版本字段以便关联。
4. 定义敏感/大数据工件的访问与保留策略。

我可以为你继续完成的工作

- 为前 N 个核心实体（repo, commit, pr, build, sbom, vulnerability）生成 JSON Schema 包，并把它们放入仓库 `docs/zh/06-analysis/schemas/` 与 `docs/en/06-analysis/schemas/`；或
- 根据你的当前工具链（列出你使用的具体工具），生成摄取适配器清单与样例转换脚本。

参考来源（代表性）

- GitHub REST API 文档
- SonarQube 指标说明
- CodeQL 输出格式
- CycloneDX / SPDX 规范
- Snyk / Trivy / Dependabot 文档
- OpenAPI / Swagger 规范

