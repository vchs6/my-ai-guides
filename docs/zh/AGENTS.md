# AGENTS — 智能体治理与工程规范（中文）

目的

本文件定义生产环境中智能体（基于大模型的自动化助手/执行器）的治理、架构、编码原则与运行要求，面向工程团队、平台、安合与产品负责人。

范围

包含智能体设计、运行时 harness、权限模型、prompt-as-code 要求、测试与战测集成、日志与审计字段、部署控制。

重要：OpenSpec 与 Superpowers

本组织采纳 OpenSpec 与 Superpowers 作为智能体与 prompt 工程的主要开发与编排工具。所有智能体工件、提示模板、战测用例和运行时 manifest 应遵循 OpenSpec 规范（适用时）并可通过 Superpowers Skill/CLI 进行验证与执行。

- OpenSpec：作为智能体 manifest、提示模板、输入/输出 schema 与战测元数据的规范格式。模板应包括 OpenSpec 兼容的头部字段，如 `spec_version`、`kind` 与 `id`。
- Superpowers：作为工程/运行 Skill，提供模板校验、目录发布、沙箱执行与战测套件运行。智能体必须包含 Superpowers 所需的元数据块以便 Skill 自动化验证与受控执行。

团队必须遵循集成指南中 "Skill Requirements" 所述要求，详见 `docs/zh/07-integrations/openspec-superpowers.md`。

1. 角色与职责

- 产品负责人：定义智能体能力边界与验收标准。
- 智能体工程师：实现智能体逻辑、模板、harness 接入、单元测试。
- 平台负责人：提供运行时、注册服务与 MCP 接口。
- 安全 / 红队：定义禁止行为、审批高风险智能体并执行战测（通过 Superpowers）。
- SRE / 运维：监控运行指标、限流与故障处置。

2. 智能体分类与风险等级

- 风险等级：LOW / MEDIUM / HIGH。
  - LOW：只读回答、无状态变更、无 PII 访问。
  - MEDIUM：可能建议操作或查询内部数据，但在无人工批准的情况下不能执行外部副作用。
  - HIGH：可执行状态变更或外部调用/访问敏感数据——需人工在环、沙箱与完整战测。

智能体必须在元数据中声明风险等级并给出理由。

3. 智能体元数据与 Prompt-as-Code 要求

每个智能体以 prompt-as-code 工件存在，包含元数据与模板。最小字段：

- agent_id
- name、owner、contact
- version
- risk_level
- description / purpose
- allowed_tools: 可调用工具列表
- forbidden_actions: 明确禁令
- inputs schema 与 outputs schema（JSON Schema）
- examples（正/反例）
- battle_suite_id（关联战测用例）
- spec_version（OpenSpec 版本）

模板放置路径：`docs/02-projects/<project>/agents/`，并通过 prompt catalog（MCP）暴露。使用 Superpowers 时，请包含 Superpowers 元数据块以便 Skill 进行验证与发布。

4. Harness / 运行时要求

运行时必须实现：

- Planner/Executor 分离：计划阶段（决定动作）与执行阶段（确定性执行控制）。
- 工具适配器：对外部集成封装，做输入校验、ACL、速率限制与断路器。
- 人工在环钩子：对 HIGH 风险动作提供交互式审批（Superpowers 提供审批钩子支持）。
- 检查点与回滚：持久化执行状态并支持回滚。
- 超时与重试：按动作与智能体设置超时，重试带退避。
- 密钥与凭据：禁止在提示词中嵌入凭据，使用凭据库与作用域令牌。

5. 函数调用与工具执行流程

- 智能体应输出结构化意图对象（如 {intent: "create_ticket", params:{...}}），而非不确定自然语言，用于任何工具调用。
- 执行器在调用适配器前验证意图是否在 allowed_tools 与策略之内。
- 所有工具调用记录标准化的调用记录：{call_id, agent_id, tool_id, input, output, status, timestamp}。

6. 日志与审计字段

每次智能体调用必须记录下列字段：

- request_id
- agent_id、agent_version、prompt_template_id
- user_id 或 actor
- input_snapshot（PII 已脱敏）
- context_snapshot（检索到的文档 ID、向量检索命中）
- intent_object（模型输出的结构化意图）
- tool_calls：工具调用记录列表
- model_meta：model_name、model_version、temperature、tokens_used
- result_snapshot（完整响应或结构化输出）
- decisions：审批/拒绝记录
- timestamp、duration

审计日志最低保留 90 天，敏感日志按隐私策略管理。

7. 测试与战测集成

- 单元测试：使用确定性 mock 验证 prompt 模板并断言 JSON schema 输出。
- 集成测试：在 mock 适配器下运行智能体 harness 验证端到端流程。
- 战测：为每个 MEDIUM/HIGH 智能体关联 battle_suite；变更 PR 必须在 CI 中运行 battle 子集（优先使用 Superpowers runner）。
- 回归：保存 gold 对话并使用嵌入相似度或关键断言来对抗非确定性。

8. 安全与安全控制

- 黑名单/白名单：平台强制禁止工具/终端的清单；智能体元数据需声明 allowed_tools。
- 速率限制与配额：按智能体与工具实施。
- 策略执行：CI 中自动检测 prompt 注入与越权尝试（Superpowers 可在 CI 中启用校验插件）。
- 最小权限：采用最小权限原则分配工具与凭据。

9. 部署與发布控制

- 智能体版本化，重大变更需提升主版本并执行完整战测。
- 生产发布采用灰度（例如 5%→25%→100%），并使用自动化指标闸门（安全突破率、错误率等）。
- 紧急回滚：保持可快速回滚路径，并对副作用做补偿处理。

10. 可观测性与指标

每个智能体需跟踪：

- invocation_count
- avg_latency_ms
- error_rate
- tool_call_rate
- safety_breach_rate
- tokens_per_invocation
- model_cost_per_invocation

11. 文档与 Model Card

- 每个智能体需有 README：目的、输入/输出、示例、风险、owner、部署状态与 model_card 链接。
- Model Card 描述模型局限、意图用途、训练数据来源（若可）与推荐防护措施。

12. 隐私与数据处理

- 智能体应明确是否保存用户数据为记忆。持久记忆需保存用户同意记录与 TTL。
- 在未获明确同意前不得在提示词中使用 PII，存储前必须脱敏。

13. 示例

- 最小智能体元数据示例（YAML），见英文示例。

14. PR 变更检查表

- [ ] 元数据存在且有效
- [ ] 单元测试已添加
- [ ] 若风险 >= 中，已添加战测子集
- [ ] 安全扫描通过
- [ ] 指定 owner 与平台审阅者
