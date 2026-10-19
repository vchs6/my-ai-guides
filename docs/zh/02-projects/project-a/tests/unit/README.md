模板的单元测试应放在此目录下。每个测试应当：

1. 提供示例输入（参见模板示例）。
2. 在确定性测试模式下执行 prompt builder/模板（使用模型 mock 或固定种子），并验证输出 schema。
3. 断言安全约束（例如：脱敏行为）。

示例测试运行流程（伪代码）：

- 加载 `infra.rag.retrieve.v1.md` 元数据与提示词
- 使用测试 mock 在 `query` = "test query"、`k` = 2 下运行提示词
- 验证返回 JSON 包含 `retrieved_ids` 与 `snippets` 数组
- 验证不存在未脱敏的 PII

CI 集成：将这些测试接入 `.github/workflows/test.yml`，在 PR 时运行。
