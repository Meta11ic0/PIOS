# 种子摄入与核验场景入口

适用：把 [database/watchlist/](../database/watchlist/) 下的候选种子核验成可用的产品记录，并把基础数据与动态观测写入 `database/products/`。

权威规则：[database/README.md](../database/README.md)「观察池人工核验清单」与「写入要求」；列名以 [database/csv_schema.md](../database/csv_schema.md) 为准，字段以 [products/schema.yaml](../database/products/schema.yaml) 为准。本文只指路，规则的权威位置如上。

前置输入：来源已在 [sources.csv](../database/sources.csv) 登记并取得 `source_id`；交易所与基金公司的正式资料可取得。缺来源时先走 [register.md](register.md)。

流程：

1. 按 database/README 核验清单逐行核验 `ticker`、正式名称、跟踪指数与 `product_id`，更新 `verification_status` 与 `coverage_status`。
2. 核验通过后写 `products/` 基础记录；动态值只追加到 [products/history/](../database/products/history/)，基础表保留首次记录值。
3. 每条 `verified` 记录引用 `source_id`；未知值留空并置 `missing` 或 `pending`。
4. 补缺口的最低完成标准见 [OPERATIONS.md](../OPERATIONS.md)「§14 当前初始化顺序」阶段 3。

本场景的产物是产品数据行；Committee 的作用域只有 DD 第 3–6 步。停止与回退以 database/README 与 [data_contracts.md](../database/data_contracts.md) 为准；官方字段只依据交易所与基金公司的正式资料填写。
