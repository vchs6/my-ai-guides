# 贡献指南 — my-ai-guides

本文件说明团队如何贡献 prompt 模板、battle 用例、测试与文档。

规则

1. 所有 prompt 模板必须包含 YAML 元数据（id、version、author、purpose、inputs、outputs.schema、safety_constraints）。
2. 若模板为中/高风险，模板变更必须包含或更新单元测试与 battle 用例。
3. 模板 `version` 字段请使用语义化版本。

建议的 PR 模板

- 标题：`[project] <简要描述> (template-id vX.Y)`
- 描述：变更内容、风险等级、添加的测试、谁来审查
- 检查项：
  - [ ] 元数据存在
  - [ ] 示例存在
  - [ ] 输出 schema 存在
  - [ ] 单元测试已添加/更新
  - [ ] battle 子集已添加/更新（如需要）

审查流程

- 指定模板 owner 与平台 owner 为审阅者。
- 对于中+风险变更，自动邀请安全团队参与审查。
