# 项目：project-a（示例）

负责人：@team-a
目的：示例项目，展示项目级 prompt、测试与 battle 用例的目录布局。
风险等级：中（RAG 检索 + 面向用户的输出）

结构

- docs/02-projects/project-a/README.md（本文件）
- prompt-templates/: project-a 的模板
- tests/unit/: 模板单元测试
- tests/battle/: 红队 battle 用例

如何添加模板

1. 创建 `prompt-templates/<id>.md`，使用 `docs/03-templates/prompt-template.md` 中的模板格式。
2. 将示例添加到 `tests/unit/`，若风险 >= 中则添加 battle 用例到 `tests/battle/`。
3. 提交 PR 并指派 @team-a 与 @platform-team 审查。
