# 演示 screening runs：`demo_only`

本目录只存放演示运行包。

- 顶层必须有 `scope: demo_only`（`demo_only`），或等价说明。
- 生产 Modeling 输入与比较 **不得**引用本目录文件。
- `verification_status: unknown` 表示停止/预检，不表示评分或比较通过。

命名仍建议：`YYYY-MM-DD-<topic>-<model_version>.yaml`。
