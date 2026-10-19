# Prompt 模板文件模版

在 `docs/02-projects/<project>/prompt-templates/` 下创建新的 prompt 模板文件时请使用此模版。

顶部 YAML 元数据（必填字段）：

```yaml
id: <team>.<project>.<template>.<version>
version: YYYY-MM-DD 或 v1
title: 简短标题
author: 姓名/邮箱
purpose: 简短描述
inputs:
  - name: x
    required: true
    type: string
outputs:
  schema: <JSON Schema>
safety_constraints:
  - "..."
examples:
  - input: {...}
    expected_output: {...}
```

在元数据下方包含提示词正文、对 harness 的清晰指示及失败模式说明。
