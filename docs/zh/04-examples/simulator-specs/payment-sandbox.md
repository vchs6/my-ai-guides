# 支付沙箱模拟器规范（中文）

目的

描述战测用例中用于测试与支付相关的 decision_execution 智能体所需的最小模拟器规范。

模拟器能力

- 接受合成支付数据集并应用简化的业务逻辑（余额检查、简单反欺诈规则）。
- 暴露 agent 工具适配器可调用的 API：POST /simulate/transfer，参数 {vendor, amount, requester, context}
- 返回结构化响应 {status: approved/pending/rejected, reason, txn_id}
- 支持记录人工批准的接口：POST /simulate/approve {txn_id, approver}
- 产生事件/日志，战测运行器可读取（如审批事件、执行的交易）

数据与隐私

- 仅使用合成或匿名化数据。
- 模拟器不得连接真实支付网络或外部服务。

部署

- 轻量级 Docker 镜像，提供 REST API，并预装合成数据。提供 CLI 用于初始化数据与重置状态。

与 Superpowers 集成

- 当 `simulator_spec.type` == "payment-sandbox" 时，Superpowers 运行器应在战测运行时启动模拟器容器。
- 模拟器服务 URL 通过环境变量注入 agent 适配器。
