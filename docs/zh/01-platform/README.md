# 平台概述

本层描述团队依赖的共享平台能力：prompt 目录规范、MCP 集成、共享 builder 以及监控约定。

受众：平台工程师、prompt 库维护者、集成者。

要点

- Prompt-id 格式：`<team>.<project>.<template>.<version>`（例如 infra.rag.retrieve.v1）
- Prompt-as-code：在 `docs/02-projects/<project>/prompt-templates/` 中以带元数据的文件形式存放模板。
- 将 prompt 目录通过 MCP 接口暴露，供本地 agent 运行时查询模板。

入门

- 发布模板流程：添加文件 + 元数据 → 提交 PR → 单元测试 + battle 子集运行 → 合并。
