# 四级任务分解规范（中文）

目的

提供一套简洁的四级任务分解规范，用于对工作进行分类、将智能体映射到职责，以及指导战测/测试设计。团队应在每个智能体、提示和战测用例中应用此分解，明确范围、权限与影响。

层级定义

- 第0级 — 任务 / 业务目标（Mission）
  - 高层次成果（例如：“降低支付欺诈”、“自动化发票审批”）。
  - 由产品/业务负责。

- 第1级 — 能力 / 史诗（Capability / Epic）
  - 支撑任务的粗粒度能力（例如：“支付授权”、“供应商管理”）。
  - 映射到一组智能体或服务。

- 第2级 — 任务 / 决策单元（Task / Decision Unit）
  - 离散的决策或面向用户的任务（例如：“授权超阈值支付”、“对供应商风险分类”）。
  - 应标注 task_type（information/transformational/decision_support/decision_execution/operational）。
  - 必需包含 authority_level、autonomy_allowed、required_approvals、expected_impact。

- 第3级 — 操作 / 原子动作（Action / Atomic Operation）
  - 执行任务所需的原子步骤（例如：“校验余额”、“调用转账 API”、“记录审批事件”）。
  - 在智能体元数据中表述为工具调用，并需采用确定性适配器。

指导原则

- 每个智能体与战测用例必须在元数据中引用第0..3级的血统（如 mission_id、capability_id、task_id、actions[]）。
- 决策型任务（第2级标注为 decision_support 或 decision_execution）必须包含 simulator_spec 以便战测与回滚策略。
- 使用稳定 ID 以便在遥测、审计与战测报告中进行关联。

示例映射

- Mission：降低支付欺诈
- Capability：支付授权
- Task：授权大额付款（task_type=decision_execution，authority_level=require_human）
- Actions：[validate_balance, fraud_check, request_human_approval, execute_transfer]

效益

- 支持自动化 CI 门控：修改第2级决策任务的 PR 会触发深度战测与模拟运行。
- 简化审计：可追踪事件链路从动作 -> 任务 -> 能力 -> 业务目标。
- 明确归属：在合适粒度分配 ownership 与 SLA。
