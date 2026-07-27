# Database

数据库按市场、指数、产品、组合、观察池和筛选结果组织。产品再按资产类型分目录，避免为单一 ETF 建立不可扩展的结构。

**就绪提示：** 多数路径仍是骨架（空表、pending 种子、draft 模型）。有工作流可以练审查写法；IPS 非 active、没有真实持仓/目标、已核验产品和适用 `as_of` 的动态数据时，不能据此下真实买入 Decision。补齐顺序见 [STATUS.md](../STATUS.md) 与 [OPERATIONS.md §14](../OPERATIONS.md#14-当前初始化顺序)（IPS → 目标 → 持仓 → 产品）。演示数据仅在 `*/demo/`，生产查询须排除。

## 目录职责

| 路径 | 职责 |
|---|---|
| `market/` | 市场/交易场所级事实（日历、时区、开市规则摘要）；见 [market/README.md](market/README.md) |
| `index/` | 指数标识、规则摘要与来源；见 [index/README.md](index/README.md) |
| `products/` | 产品字段基线 [products/schema.yaml](products/schema.yaml)；动态历史追加见 [products/history/](products/history/) |
| `portfolio/` | 个人持仓与目标配置（操作表） |
| `watchlist/` | 候选种子；`pending` / `coverage_status` 表示未完备，使用前须人工核验 |
| `screening/` | 评价模型定义；运行结果在 [screening/runs/](screening/runs/) |

## 四类数据

1. **目录数据**：唯一标识、名称、市场和资产类型，低频变化。
2. **基础数据**：费率、成立日期、跟踪对象等，按公告更新；对应 schema 中非 `category: dynamic` 字段。
3. **动态数据**：规模、成交额、价差、净值/IOPV、折溢价、跟踪偏离、申赎/交易状态，必须带 `as_of`；按 [products/history/](products/history/) **追加**，不在基础表覆盖历史。
4. **评价数据**：模型版本、输入快照、分项/总分占位与解释；写入 [screening/runs/](screening/runs/)，禁止伪造评分。

## 写入要求

- 使用稳定 ID 关联数据，不用显示名称作为唯一键。
- 数值与单位分开，日期使用 ISO 8601。
- 市场、指数、产品和评价数据保留 `source_id`、来源 URL、来源等级和 `retrieved_at`；来源登记见 [`sources.csv`](sources.csv)，字段契约与校验见 [`data_contracts.md`](data_contracts.md)。
- 个人组合表 `portfolio/holdings.csv` 与 `portfolio/target_allocation.csv` 是操作表：持仓用 `source` 记录对账单或人工核对即可；目标配置用 `rationale` 记录依据。更细证据放在 Decision Log。
- 未知值留空，并将验证状态设为 `missing` 或 `pending`。
- 不在静态基础表中覆盖历史动态值；动态记录按时间追加。
- 任何评分都必须关联模型版本和输入数据日期；draft 模型阶段只做字段对比与否决项。

`products/schema.yaml` 是统一产品字段基线（当前 `schema_version: 0.3.0`），各资产类型可增加特有字段，但不要修改通用字段含义。发现错误时追加更正记录，不覆盖原始动态快照。

## 观察池人工核验清单

入口：[watchlist/us_index_etf_candidates.csv](watchlist/us_index_etf_candidates.csv)。当前条目为候选种子，**不是**已核验产品目录。

对每一行在使用前：

1. 用交易所与基金公司资料确认 `ticker`、正式名称、跟踪指数与 `product_id`。
2. 将 `verification_status` 从 `pending` 改为 `verified`（或 `conflicting` / `missing`）。
3. 将 `coverage_status` 从 `seed_pending` 改为 `identity_verified` 或 `incomplete`（候选集仍不完备时）。
4. 核验通过后，再写入 `products/` 基础记录；需要动态值时追加到 `products/history/`。
5. **不要**根据对话记忆或第三方摘要填造 `name` 等官方字段。
