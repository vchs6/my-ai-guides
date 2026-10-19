# OpenSpec 与 Superpowers 集成指南（中文）

概述

本指南说明如何以 OpenSpec 作为规范格式并使用 Superpowers 作为 Skill/引擎来进行校验、发布、沙箱执行与战测编排，列出集成所需的 Skill 能力与最小运行要求。

为何采用 OpenSpec + Superpowers

- OpenSpec 为智能体 manifest、提示模板、输入/输出 schema 与战测元数据提供一致、可版本化的规范，简化校验、代码生成与跨团队发现。
- Superpowers 提供 Skill 集成能力：校验 OpenSpec 工件、发布到 prompt 目录、以受控沙箱运行并执行战测。Superpowers 可与 CI 与 agent harness 集成。

仓库约定

- 所有智能体工件必须包含 OpenSpec 头部字段，至少包括：`spec_version`、`kind`（agent|prompt|battle）、`id`、`version`。
- 工件路径约定：
  - Agents: `docs/02-projects/<project>/agents/*.yaml`
  - Prompts: `docs/02-projects/<project>/prompt-templates/*.md`（内含 OpenSpec 头部）
  - Battle 用例: `docs/02-projects/<project>/tests/battle/*.yaml`

Superpowers Skill 最低能力要求

集成 Superpowers 的团队必须确保 Skill 或运行器具备下列能力：

1. 认证与授权
- Skill 使用组织托管的服务账号或 OIDC token 进行认证。
- 支持基于角色的权限（read:catalog, write:catalog, run:tests, manage:agents 类似的粒度）。

2. 校验与 schema 强制
- Skill 能对工件执行 OpenSpec schema 校验与本地 JSON Schema 校验。
- 对于 risk_level=high 的工件必须实施严格校验。

3. 目录发布
- 能将经校验的工件发布到集中式 prompt catalog（兼容 MCP）。
- 发布记录溯源信息：用户、commit_sha、时间戳、tool_version。

4. 沙箱执行
- 支持在隔离沙箱中执行智能体与战测用例，具有限制资源、网络出口控制和模拟工具适配器。
- 沙箱应产生完整的对话记录、检测器事件和标准化工具调用日志。

5. 战测执行器与检测器
- 能自动运行 battle-case YAML，挂载检测器（PII、密钥、未授权动作），输出结构化结果。
- 提供 API 供 CI 查询运行结果。

6. 审计与证据存储
- 将证据（对话、检测输出）持久化到访问受控的证据仓库；支持脱敏与保留策略。

7. CI / GitOps 集成
- Superpowers CLI 或 Skill API 能被 CI（GitHub Actions, Jenkins）调用。常见流程：
  - PR -> Superpowers validate -> publish to staging catalog -> run battle-subset -> return status

安全与运维规则

- 最小权限：仅授予 Skill 必要的最小权限。
- 网络出口：沙箱默认禁止外网出口，需显式允许并审计。
- 密钥：Skill 不应直接持有明文密钥；适配器在执行时获取短时凭证。

团队入门检查项

- [ ] 在本地环境安装 OpenSpec lint 与 Superpowers CLI
- [ ] 为所有智能体/提示/战测工件添加 OpenSpec 头部
- [ ] 配置 Superpowers 服务账号与 RBAC
- [ ] 在 CI 中添加 Superpowers validate 与 battle-subset 步骤
- [ ] 在工件元数据中登记团队联系人与战测负责人

示例 CI 片段（GitHub Actions）

见英文示例（相同逻辑）。

文档与支持

- 团队需在项目 README 中记录 `spec_version` 与 Superpowers 运行器版本。
- 内部支持请在工件元数据中登记负责人的联系方式。
