# Superpowers CI 与校验示例（中文）

本文件包含在 CI 中用于校验 OpenSpec 头部和战测用例 schema 的示例脚本，以及如何通过 Superpowers 运行战测子集的示例。

GitHub Actions 片段（扩展）：

```yaml
name: Validate & Run Battle-Subset
on: [pull_request]
jobs:
  validate-and-battle:
    runs-on: ubuntu-latest
    env:
      PROJECT: project-a
    steps:
      - uses: actions/checkout@v3
      - name: Setup Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.10'
      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install jsonschema pyyaml requests
      - name: Validate OpenSpec & battle-case schema
        run: |
          python scripts/validate_superpowers.py docs/02-projects/${{ env.PROJECT }}/agents docs/02-projects/${{ env.PROJECT }}/tests/battle
      - name: Install Superpowers CLI
        run: |
          curl -sSfL https://example.com/superpowers/install.sh | bash
      - name: Run battle-subset via Superpowers
        env:
          SUPERPOWERS_TOKEN: ${{ secrets.SUPERPOWERS_TOKEN }}
        run: |
          superpowers battle run --subset --project ${{ env.PROJECT }} --simulator-start-on-demand
```

样例 Python 校验脚本（scripts/validate_superpowers.py）与英文版相同。

注意事项

- 该脚本为起点；生产 CI 还应校验 OpenSpec 头部以及其他 JSON Schema（prompt、agent）。
- `--simulator-start-on-demand` 为示例参数，Superpowers 运行器应支持基于 `simulator_spec` 启动模拟器。
