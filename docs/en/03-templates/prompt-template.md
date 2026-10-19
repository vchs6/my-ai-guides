# Prompt Template File Template

Use this template when creating new prompt template files under `docs/02-projects/<project>/prompt-templates/`.

Top-level YAML metadata (required fields):

```yaml
id: <team>.<project>.<template>.<version>
version: YYYY-MM-DD or v1
title: Short title
author: name/email
purpose: Short description
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

Below the metadata include the prompt body, clear instructions for the harness, and notes about failure modes.
