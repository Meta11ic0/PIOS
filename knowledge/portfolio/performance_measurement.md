---
id: knowledge-portfolio-performance-measurement-v1
type: knowledge
status: draft
created_at:
updated_at:
review_at:
source_refs: []
scope: PIOS portfolio measurement conventions
---

# 组合绩效与估值口径

本条目定义 PIOS 在输入完整时如何记录绩效；它不产生收益结论，也不替代投资决策的过程审查。

## 报告币种与估值

- `report_currency` 是组合汇总时使用的币种；单项产品交易币种仍保留在持仓快照。
- `market_value_reporting` 是按估值适用时点、汇率适用时点、`fx_quote_convention` 与可定位来源折算后的报告币种市值。估值与汇率适用时点对应字段 `valuation_as_of`、`fx_as_of`。
- 每次组合比较应使用统一估值时点；不同日期的持仓市值不能直接汇总为当前权重。

## 外部现金流

`external_cashflows.csv` 只记录入金、出金等组合外部现金流。`amount_signed` 与 `amount_reporting_signed` 统一采用“流入组合为正、流出组合为负”的符号口径。买卖、分红或产品间转换不是外部现金流，除非其确实改变组合外部资金。

## 绩效指标

### 时间窗口（参考 Ghostfolio ROAI 体系）

绩效应至少覆盖以下标准化时间窗口，使用统一估值时点：

| 窗口 | 缩写 | 说明 |
|------|------|------|
| 当日 | Today | 最近一个交易日 |
| 本周至今 | WTD | Week-to-Date |
| 本月至今 | MTD | Month-to-Date |
| 年初至今 | YTD | Year-to-Date |
| 近 1 年 | 1Y | 滚动 365 天 |
| 近 5 年 | 5Y | 滚动 5×365 天 |
| 全区间 | Max | 自有记录以来 |

估值时点不统一时，各窗口不得直接比较；缺失估值时记为”不可计算”。

### 计算口径

- **资金加权收益率 MWR / XIRR**：衡量实际投入资金获得的回报，依赖每笔外部现金流日期、金额、符号口径、期末或终端估值与 `fx_quote_convention`；`performance_periods.csv` 须填写 `mwr_terminal_value_reporting` 与 `linked_cashflow_ids`。XIRR 使用 365.25 天/年的日计数惯例，二分法求解，迭代精度 ≤ 1e-6。
- **时间加权收益率 TWR**：衡量组合在排除外部现金流影响后的表现；每次外部现金流都要求一个可核验的估值子期间。采用日回报几何链式法：子期间以外部现金流发生日为边界，每个子期间计算简单收益率，全周期 TWR = ∏(1+rᵢ)−1。`external_cashflow_timing`、`twr_subperiod_ids`、起止快照、报告币种、估值与汇率来源必须齐全。
- **Modified Dietz（近似）**：仅当外部现金流相对组合规模较小且均匀分布时作为 TWR 近似。不适用于大额进出场景。

### 可计算性条件

| 条件 | 缺失时的处理 |
|------|-------------|
| 外部现金流日期、金额、符号口径 | TWR/MWR 均不可计算 |
| 每次现金流发生日的估值快照 | TWR 不可计算，降级为 MWR（若有终端估值） |
| 统一报告币种 + 汇率来源 | 不可汇总，标记”不可计算” |
| 连续完整的估值期间 | 缺失期间两端标记，仅计算有完整数据的子区间 |

输入缺少现金流日期、统一估值、汇率或来源时，结论必须是”不可计算”，不能从单一持仓快照倒推收益率。绩效不等于决策质量，仍须以当时目标、证据、风险和执行纪律复盘。