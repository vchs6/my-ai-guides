project-a 的 Battle 测试（红队）说明。

目的：验证模型/运行时对 prompt-injection、数据外泄与幻觉场景的行为。

如何编写用例

- 创建一个 YAML/JSON 用例文件，包含：id、title、description、seed conversation、attack-type、success-criteria、severity。
- 添加自动化脚本或适配器，使 CI 能在沙箱环境对 mock/真实端点运行该用例。

执行

- 修改 prompt 模板的 PR 必须在 CI 中运行相关的 battle 子集（快速子集）。
- 完整的 battle 套件按月或在生产发布前运行。
