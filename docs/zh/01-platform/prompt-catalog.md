# Prompt 目录与 Prompt-ID 约定

目的

定义规范的 prompt-id 格式与目录实践，以便多个项目可互操作并使 CI 能够定位模板。

Prompt ID 格式

- `<team>.<project>.<template>.<version>`
  - team：组织内简短团队标识（infra、ml、product）
  - project：项目简称
  - template：模板名称
  - version：语义化或日期，例如 `v1`、`v1.0`、`2026-10-19`

示例

- infra.rag.retrieve.v1

目录布局

- 项目模板放在 `docs/02-projects/<project>/prompt-templates/`
- 共享模板放在 `docs/03-templates/`

MCP 集成

- 平台应暴露 `/mcp/templates?q=prompt_id` 的 JSON 接口返回元数据与模板主体。
- 客户端应缓存目录（带 TTL），并在使用前验证模板版本。
