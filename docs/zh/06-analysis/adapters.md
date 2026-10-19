# 采集适配器映射与实现说明

文件说明：列出常见工具的采集适配器建议，将每个工具产出的主要数据映射到规范 schema，并给出摄取建议与注意事项。

设计原则

- 优先使用事件驱动摄取（webhook）。
- 对于产出报告的工具使用定时批量抓取（SBOM、夜间报告）。
- 摄取时统一 ID 并在记录中带上 schema_version。
- 早期脱敏敏感字段并将 secrets/PII 存入受保护的存储。

适配器清单（要点）

1. GitHub / GitLab（VCS 事件）
- 来源：push events、commit webhook、pull_request webhook
- 目标 schema：commit.jsonschema, pr.jsonschema
- 摄取模式：webhook -> 验证 -> 丰富用户信息 -> 存储
- 注意：补丁文本可能较大，谨慎存储

2. CI 系统（GitHub Actions, Jenkins, GitLab CI, CircleCI）
- 来源：job/webhook 事件、构建日志、工件
- 目标 schema：build.jsonschema, test_report.jsonschema
- 摄取模式：webhook / 工件上传 -> 解析日志 -> 存储工件元数据
- 注意：日志放在 blob 存储并用 URL 关联

3. 静态分析 / SAST（CodeQL, Semgrep, SonarQube）
- 来源：JSON/SARIF 报告或 API
- 目标 schema：vulnerability.jsonschema、code_quality metrics
- 摄取模式：CI 扫描后上报 -> 报告转换成通用漏洞结构 -> 存储
- 注意：保留 rule_id 与 trace 信息以便排查

4. SCA / 依赖扫描（Snyk, Dependabot, Trivy）
- 来源：依赖/漏洞报告
- 目标 schema：vulnerability.jsonschema, sbom.jsonschema
- 摄取模式：报告输出 -> 包坐标映射 -> 与 SBOM/构建关联
- 注意：按 package+version+CVE 去重

5. SBOM 工具（CycloneDX, SPDX）
- 来源：CycloneDX / SPDX 文件
- 目标 schema：sbom.jsonschema
- 摄取模式：工件上传或 CI 产出 -> 验证 -> 存储组件和哈希
- 注意：提取 purl 和 license 字段用于合规流程

6. 覆盖率与测试工具（pytest/junit, JaCoCo, coverage.py）
- 来源：junit XML、coverage 报告
- 目标 schema：test_report.jsonschema
- 摄取模式：CI 工件上传 -> 解析 -> 存储计数与失败详情

7. 镜像注册/容器扫描（Docker Registry, JFrog, Harbor）
- 来源：镜像元数据、漏洞扫描
- 目标 schema：sbom.jsonschema, vulnerability.jsonschema, release provenance
- 摄取模式：注册表 webhook 或 定时扫描 -> 将镜像 digest 映射到 SBOM 组件

8. 可观测平台（Prometheus, Jaeger, ELK）
- 来源：metrics、traces、logs
- 目标：运行时遥测（时间序列 DB / traces 存储）。这些数据与 JSON Schema 不同，但要通过 request_id / artifact_id 建立关联
- 摄取模式：抓取 / 日志管道

9. Issue 跟踪（GitHub Issues, Jira, Linear）
- 来源：issue webhooks / API
- 目标：issue 文档（JSON）
- 摄取模式：webhook -> 归一化字段 -> 存储

10. ML 平台（MLflow, W&B, TensorBoard）
- 来源：实验运行、模型元数据、数据集清单
- 目标 schema：model.jsonschema, dataset manifests
- 摄取模式：API 导出或平台上报 -> 存储并关联 model_id

11. 语义索引 / 向量库（Sourcegraph, 向量 DB）
- 来源：embedding 生成流程
- 目标：索引条目 {doc_id, path, vector, metadata}
- 摄取模式：批量或流式向量插入 -> 关联源路径

转换与去重建议

- 时间戳统一为 UTC
- 不同扫描器间以 CVE + package + version 去重漏洞
- 丰富 commit/PR 的用户信息（LDAP/IDP）以便归属分析
- 保留原始报告以便审计

安全与隐私说明

- 在边缘脱敏 PII/Secrets；将证据副本放入受保护存储。
- 验证 webhook 签名与鉴权
- 对重型数据源（日志、向量）应用速率限制与分层存储

示例

- GitHub webhook -> collector -> validate -> transform into commit/pr schema -> publish to Kafka
- CI 上传 junit/coverage -> ingestion API validates and stores report
- CodeQL SARIF -> ingestion pipeline converts SARIF -> produce canonical vulnerability docs

文件已添加

- docs/zh/06-analysis/adapters.md
- docs/zh/06-analysis/schemas/*.jsonschema
