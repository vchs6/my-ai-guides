# 项目分析语义清单（不含仓库级信息）

本报告列出了项目分析工具产生的语义数据项（不含任何仓库级元数据），用于设计数据摄取、治理与可观测流水线。

范围与目的

- 范围：提交、PR、贡献者、代码结构、API/schema、依赖、构建/CI、测试/覆盖、安全发现、SBOM、许可、运行时遥测、Issue/发布、工件溯源、开发者指标、语义索引、模型/数据工件与隐私/PII 检测。
- 目的：作为可实现的清单，帮助团队在不依赖仓库级字段的情况下设计数据平台与治理模型。

核心类别与数据项

1. 提交与变更元数据
- commit_sha、author/committer（name/email/timestamp）、commit_message、files_changed、增加/删除/修改 行数、diff、code_churn、change_type、semantic_tags。

2. Pull Request / Merge Request 元数据
- pr_id、title、description、author、assignees、reviewers、labels、state、merge_commit_sha、changed_files、review_comments、approvals、checks_status、associated_issues、risk_level、required_policies。

3. 贡献者与归属元数据
- contributor_id、email、活动指标、code_ownership、团队归属。

4. 代码结构与静态语义
- 文件元数据、模块/包、函数/类签名、导出符号、调用图、控制/数据流摘要、TODO/FIXME、复杂度指标。

5. API 与 schema 工件
- OpenAPI、gRPC proto、GraphQL、消息模式、API surface summary 与认证要求。

6. 依赖性与包元数据
- 直接/传递依赖、许可证、过时建议、依赖健康指标。

7. 构建与 CI/CD 工件
- build_id、pipeline_id、状态、时长、日志 URL、产物列表、运行环境、镜像 tag、溯源信息等。

8. 测试与覆盖工件
- 测试套件计数、通过/失败/跳过、波动率、失败详情、覆盖率等。

9. 代码质量与复杂度指标
- lint 报告、重复率、可维护性指标、技术债务估算、热点文件等。

10. 安全与漏洞工件
- 漏洞发现（CVE 或内部 id）、敏感密钥检测、SAST/DAST 报告、修复状态、复合风险评分等。

11. SBOM 与供应链工件
- CycloneDX/SPDX 输出、组件列表、签名/attestation、镜像漏洞映射等。

12. 许可与合规工件
- 检测到的许可证、冲突、第三方组件清单、出口管制标记等。

13. 运行时与可观测遥测
- 指标（延迟/错误/吞吐）、链路跟踪、结构化日志、资源指标、特性开关、告警事件等。

14. Issue / 迭代 / 发布工件
- Issue 和里程碑数据、发布说明、工作项关系、估算等。

15. 发布与工件溯源
- 发布标签、校验和、部署历史、回滚点等。

16. 开发者生产力与交付指标
- 变更交付周期、部署频率、MTTR、PR 大小、审查延迟、贡献者网络等。

17. 代码搜索与语义索引工件
- 代码向量嵌入、索引文档 id、语义标签、符号表与交叉引用等。

18. ML / 模型 / 数据相关工件
- 数据集清单、模型元数据、model_card、权重校验、漂移与实验运行、prompt 目录与使用遥测等。

19. 隐私与 PII 检测工件
- PII 发现、脱敏状态、同意记录、数据保留标记等。

跨切片建议

- 采用统一标识符（commit_sha, pr_id, build_id, prompt_id, model_id）。
- 为每类工件发布版本化 JSON Schema 并记录 schema_version。
- 记录溯源信息（来源工具、时间戳、证据链接）。
- 在摄取阶段脱敏 PII，并对敏感工件启用加密与严格权限控制。
- 对大体量或高频数据建立分级存储与聚合策略。

后续步骤建议

1. 生成核心实体 JSON Schema（commit, pr, build, sbom, vulnerability, model, prompt, test_report）。
2. 为关键数据生产者实现采集适配器（见 adapters 文档）。
3. 在模板与 model_card 中强制嵌入 ID 与元数据以便关联。

文件已添加

- docs/zh/06-analysis/semantic-data-items-no-repo.md
- docs/zh/06-analysis/adapters.md
- docs/zh/06-analysis/schemas/*.jsonschema
