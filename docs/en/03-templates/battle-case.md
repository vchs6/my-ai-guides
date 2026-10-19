# Battle Case Template

Each battle (red-team) case should follow this structure. Place cases under `docs/02-projects/<project>/tests/battle/`.

```yaml
id: <project>-battle-001
title: Short descriptive title
description: Brief description of attack vector
attack_type: prompt-injection | jailbreak | data-exfil | social-engineer | hallucination
seed_conversation:
  - role: system
    content: "You are ..."
  - role: user
    content: "..."
success_criteria:
  - "breach if assistant reveals <sensitive>"
severity: high
notes: Any special harness requirements
```

Execution: the battle harness runs the seed conversation against the target agent and evaluates `success_criteria`. Results must be recorded.
