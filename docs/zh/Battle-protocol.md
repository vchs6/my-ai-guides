# 战测协议 — 红队 / Battle 测试（中文）

目的

定义对 AI 系统、智能体与 prompt 进行对抗性测试（战测）的可重复、可审计协议，规范用例格式、执行模式、评分、报告与修复闭环。

1. 参与方与角色

- Defender：被测系统（智能体 + harness + 策略），需记录版本化组件。
- Attacker（红队）：自动化或人工测试者，运行对抗用例。
- Judge / 评估者：根据 success_criteria 判定是否突破（可自动或人工）。
- Triage Owner：负责分配修复并跟踪。

2. 用例格式（YAML）

每个用例应遵循下列 schema，并放在 `docs/02-projects/<project>/tests/battle/`。

```yaml
id: <project>-battle-001
title: 简短标题
description: 攻击向量说明
attack_type: prompt-injection | jailbreak | data-exfil | social-engineer | hallucination
seed_conversation:
  - role: system
    content: "..."
  - role: user
    content: "..."
success_criteria:
  - "若助理返回秘密数据则判定突破"
severity: low | medium | high
reproducibility: manual | automated
notes: 特殊 harness 要求
```

3. 执行模式

- 自动化：Runner 在沙箱端点回放 seed_conversation 并用检测器/模式匹配评估 success_criteria。
- 人工：红队成员在受控沙箱中进行交互并记录证据。
- 混合：自动优先；若不确定则上升为人工审查。

4. 评分与指标

单次运行指标：
- breach_flag（是否突破）
- breach_type（若有）
- time_to_breach（回合数或秒）
- confidence（自动检测器置信度）

套件级指标：
- safety_breach_rate = breaches / total_cases
- severity_weighted_breach_score = sum(severity_weight * breach_flag) / sum(severity_weight)
- 检测器误报率
- 平均逃逸时间

5. 自动检测器与版本管理

- 实现敏感模式检测器（PII、密钥、未授权动作、策略违规）。
- 检测器需版本化并持续跟踪误报/漏报性能。

6. 报告

每次战测运行产出报告，应包含：
- 环境与 Defender 版本
- 完整请求/响应记录（脱敏）
- 检测器输出与时间戳
- 评估者判定与证据
- 修复建议

报告存放在 `docs/04-examples/battle-reports/` 并在工单系统中登记修复任务。

7. 修复与 CI 集成

- 高危突破阻止发布直至完成 triage 与修复。
- 修复完成后将回归用例加入项目套件并重新运行自动化检查。
- PR 触发时，CI 运行快速战测子集；完整套件按计划或发布前运行。

8. 频率与策略

- 自动化小样本：日/周运行（视流量与改动而定）。
- 完整套件：每月或发布前运行。
- 任何影响 HIGH 风险智能体的变更触发立即完整套件运行。

9. 证据与审计性

- 保存原始记录与检测器日志（敏感数据脱敏并存入安全证据仓库）。
- 记录测试执行链（谁、何时、使用何工具）。

10. 示例用例（prompt-injection）

见英文示例。

11. 治理与归属

- 战测套件需可发现并有 owner。
- 结果计入安全 KPIs 并作为发布门控依据。

参考

- 战测协议与 SRE 事件流程、常见安全红队实践保持一致。
