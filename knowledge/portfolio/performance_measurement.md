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
- `market_value_reporting` 是按 `valuation_as_of`、`fx_as_of`、`fx_quote_convention` 与可定位来源折算后的报告币种市值。
- 每次组合比较应使用统一估值时点；不同日期的持仓市值不能直接汇总为当前权重。

## 外部现金流

`external_cashflows.csv` 只记录入金、出金等组合外部现金流。`amount_signed` 与 `amount_reporting_signed` 统一采用“流入组合为正、流出组合为负”的符号口径。买卖、分红或产品间转换不是外部现金流，除非其确实改变组合外部资金。

## 绩效指标

- **资金加权收益率（MWR / XIRR）**：衡量实际投入资金获得的回报，依赖每笔外部现金流日期、金额、符号口径、期末（终端）估值与 `fx_quote_convention`；`performance_periods.csv` 须填写 `mwr_terminal_value_reporting` 与 `linked_cashflow_ids`。
- **时间加权收益率（TWR）**：衡量组合在排除外部现金流影响后的表现；每次外部现金流都要求一个可核验的估值子期间。`external_cashflow_timing`、`twr_subperiod_ids`、起止快照、报告币种、估值与汇率来源必须齐全。

输入缺少现金流日期、统一估值、汇率或来源时，结论必须是“不可计算”，不能从单一持仓快照倒推收益率。绩效不等于决策质量，仍须以当时目标、证据、风险和执行纪律复盘。