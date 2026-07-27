# 数据契约与校验

本文件定义 PIOS 本地 CSV/YAML 数据集的最小契约。它补充而不替代 [`products/schema.yaml`](products/schema.yaml)、各工作流和 [Validation Skill](../skills/validation/SKILL.md)。

## 通用约定

- 使用稳定 ID 关联记录；显示名称和 URL 不是主键。
- `as_of` 表示事实适用时点；`published_at` 表示来源发布时间；`retrieved_at` 表示 PIOS 取得时间。时间使用带时区的 RFC 3339。
- 所有 `verified` 事实必须引用 [`sources.csv`](sources.csv) 中的 `source_id`。
- 未知值留空并使用 `missing`、`pending` 或 `unknown`；不得以合理猜测补值。
- 更正不覆盖旧记录：追加新记录，填写 `supersedes_record_id`、`correction_reason` 与新 `source_id`。
- 结构或语义发生不兼容变化时，提升相关 schema 的版本；新增可选字段不视为不兼容变化。
- `scope: production` 才能作为真实研究、模型、组合或 Decision 输入；`demo_only`、`example`、`archive` 和未标注范围的工件不得进入生产查询。演示工件只放在 `reports/demo/`、`decision_log/demo/`、`database/screening/runs/demo/`、`raw_material/demo/`。

## 动态字段最大允许时效

行动前，关键动态字段若超过下表相对决策时点的最大年龄，记为关键 `unknown`，**不得**以 `warning` 放行后进入 `act`。用户可在 IPS 或模型中收紧，不得放宽到超过下表而不重新批准模型版本。

| 字段类别 | 默认最大年龄 | 说明 |
|---|---|---|
| 市价、买卖价差、交易状态 | 1 个交易日（行动当日须可核验） | 盘中值不得冒充收盘；跨市场须注明时区 |
| IOPV / 折溢价率 | 与用于比较的市价同一可比窗口 | 须写明分母是 NAV 还是 IOPV（`premium_discount_basis`） |
| NAV | 最近已公布净值日 + 模型允许滞后 | 跨境产品须评估底层市场休市导致的滞后 |
| 成交额、规模 | 模型或 IPS 约定窗口 | 缺失则不得通过流动性硬门槛 |
| QDII 额度 / 申赎状态 | 行动当日可核验公告或官方状态 | 未知即阻断跨境买入 |
| 汇率（持仓折算） | 与 `valuation_as_of` 同一可比日 | 须记录 `fx_quote_convention` |

## 数据集契约

| 数据集 | 主键或去重键 | 必填血缘 | 说明 |
|---|---|---|---|
| `sources.csv` | `source_id` | URL 或受控文件引用、来源等级、`retrieved_at`、`scope` | 来源登记，不等同于事实结论；可选 `raw_material_path` 指向待蒸馏材料。 |
| `portfolio/holdings.csv` | `record_id` | `source_id`、估值和 FX 来源/时点 | 追加式持仓快照；同一 `holding_id` 可有多个 `as_of`。交易、基金、底层暴露和报告币种不得混用。 |
| `portfolio/target_allocation.csv` | `allocation_id` + `effective_from` | `ips_id`、`allocation_set_id`、`approval_decision_id` | 战略配置；仅 active IPS 的完整配置集可用于比较。 |
| `portfolio/external_cashflows.csv` | `cashflow_id` | `source_id`、发生时间、符号口径、FX 来源/时点 | 组合外部入金/出金；不记录产品内买卖。 |
| `portfolio/performance_periods.csv` | `period_id` | 开/收盘快照、估值与 FX 来源、计算方法、现金流关联 | TWR 子期间与 MWR 终端价值必须可复算；不完整则“不可计算”。 |
| `portfolio/allocation_snapshots.csv` | `snapshot_id` + `allocation_id` | `ips_id`、`allocation_set_id`、`portfolio_snapshot_id`、`source_id` | 目标偏离历史快照；包含现金口径与适用例外。 |
| `portfolio/policy_exceptions.csv` | `exception_id` | `ips_id`、`decision_id`、批准人/时间、期限、关闭证据 | 未批准、过期或未核验例外视为不存在。 |
| `products/history/etf_metrics.csv` | `record_id` | `source_id`、`as_of`、`retrieved_at`、指标口径 | 动态指标快照；市价、NAV/IOPV 时间点、折溢价/价差/跟踪窗口分别记录。 |
| `products/history/asset_profile_exposure.csv` | `record_id` | `source_id`、`as_of`、`retrieved_at`、权重口径与覆盖 | 产品穿透暴露快照；每条记录只表达一个维度值。 |
| `screening/runs/*.yaml` | `run_id` | 输入清单、模型版本、输入时点、`scope` | 派生比较结果；不覆盖已有 run；`demo/` 下文件不得进入生产。 |

## 最小校验清单

每次写入或使用数据前，按以下检查并记录 `pass`、`warning`、`fail` 或 `unknown`：

1. **结构**：列/字段、类型、枚举、ID、主键或去重键符合本契约。
2. **时间**：动态事实有 `as_of`；`retrieved_at` 不早于来源发布时间（有值时）；旧决策不使用决定日之后的事实解释；关键字段未超最大允许时效。
3. **业务口径**：金额有单位和币种；价差、折溢价、跟踪指标说明统计口径或窗口；权重单位与覆盖完整度可定位。
4. **血缘**：`verified` 记录能定位到 `source_id`；每个 screening run 有输入数据、规则/模型版本和限制说明。
5. **绩效**：TWR/MWR 仅使用完整、连续且同一报告币种的估值与外部现金流输入；不完整时标记 `unknown` 或“不可计算”。
6. **范围**：生产运行的输入清单只能包含 `scope: production` 且 `verification_status: verified` 的事实；演示、样例、归档和 pending 材料必须显式排除。

`fail` 或关键 `unknown` 阻断后续 Decision；非关键 `warning` 可以继续，但必须写入限制和复核条件。关键时效过期不得降级为可放行的 `warning`。

## 原始材料蒸馏

`raw_material/` 只保存待蒸馏、允许保留且能以 `source_id` 定位的材料。`sources.csv` 中：

- `raw_material_path`：仓库内合规摘录或快照的相对路径；没有保存材料时留空。
- `material_type`：例如 `excerpt`、`official_document`、`web_snapshot`；不是事实可信度评级。
- `distillation_status`：`pending`、`validated`、`distilled`、`rejected`。

材料必须先经 Research 和 Validation，才可进入 `knowledge/`、`database/` 或支持 Decision。不要把原始材料当成第二套事实库，也不要保存版权受限全文或敏感原件。`gptrecord.md` 属于归档聊天，不是 `raw_material/`。

## 隐私与原始文件

公开来源可记录 URL、定位信息和可用哈希。原始券商文件、税单、账户标识和认证秘密不进入仓库；仅可在仓库外的受控位置保留文件引用或哈希。
