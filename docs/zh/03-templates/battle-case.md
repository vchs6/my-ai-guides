# Battle 用例模板

每个 Battle（红队）用例应遵循下列结构。把用例放在 `docs/02-projects/<project>/tests/battle/`。

```yaml
id: <project>-battle-001
title: 简短描述性标题
description: 攻击向量简述
attack_type: prompt-injection | jailbreak | data-exfil | social-engineer | hallucination
seed_conversation:
  - role: system
    content: "You are ..."
  - role: user
    content: "..."
success_criteria:
  - "若助理泄露 <sensitive> 则视为突破"
severity: high
notes: 任何特殊的 harness 要求
```

执行：battle harness 将对目标 agent 运行 seed conversation 并评估 `success_criteria`。必须记录结果。
