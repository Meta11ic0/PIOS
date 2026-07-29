# PIOS 状态总览

本文件是人工维护的运营视图，不是持仓、目标、产品数据、报告或决策的事实源。每条事项必须链接回原始文件；更新事实后同步本页。

**第一步：** 按下方「初始化顺序」与 [OPERATIONS.md](OPERATIONS.md)「当前初始化顺序」补齐。当前等级为 **可跑流程 / cannot act**。

## 当前就绪等级

**可跑流程 / cannot act**

| 决策门禁 | 状态 | 原始事实源 | 下一步 |
|---|---|---|---|
| 1. 投资政策声明 IPS（Investment Policy Statement） | draft | [investment_policy.md](database/portfolio/investment_policy.md) | 填完约束并批准，使 IPS 状态为 `active`。状态为 `draft` 时不可支撑 `act`。 |
| 2. 目标配置集 | 缺失 | [target_allocation.csv](database/portfolio/target_allocation.csv) | IPS 状态为 `active` 后填写完整 `allocation_set_id`。 |
| 3. 当前持仓 | 缺失 | [holdings.csv](database/portfolio/holdings.csv) | 录入不含敏感账号的当前快照；币种拆分并带 `record_id`。 |
| 4. 产品身份核验 | 缺失 | [ETF 候选种子](database/watchlist/us_index_etf_candidates.csv) | 用基金公司与交易所资料核验 1–2 个候选。 |
| 5. 动态事实快照 | 缺失 | [etf_metrics.csv](database/products/history/etf_metrics.csv) | 追加带 `source_id`、适用时点和取得时间的记录；遵守最大允许时效。 |
| 6. 原始材料蒸馏 | 缺失 | [raw_material/README.md](raw_material/README.md)、[sources.csv](database/sources.csv) | 先登记 `source_id`，再保存合规摘录；经 Validation 后分流。 |
| 7. 组合估值与偏离 | 缺失 | [组合数据集](database/portfolio/) | 补齐统一估值时点、报告币种、现金流与配置偏离快照。 |
| 8. 筛选运行输入 | 缺失 | [runs 模板](database/screening/runs/_template.yaml) | 使用已核验生产数据生成字段比较 run；排除 `runs/demo/`。 |
| 9. Committee 输入包 | 缺失 | [Committee Skill](skills/committee/SKILL.md) | 补齐 IPS、组合、候选动作和已核验资料后再使用。 |

## 已知文档缺口

面向人的说明仍缺两块（不影响门禁语义；补齐前靠 `AGENTS.md` / `skills/` 与会话讲解兜底）：

| 缺口 | 现状 | 建议落点 | 原始记录 |
|---|---|---|---|
| Read 清单无固定格式 | `AGENTS.md` 只规定「须列出」与最少覆盖项；无模板/版式；`templates/` 无开场清单样例 | 在 `AGENTS.md` 或 `templates/` 增加开场清单样例（Agent 侧短格式即可） | [AGENTS.md](AGENTS.md)「Read 清单」、[review_pipeline 授权表](prompts/review_pipeline.md) |
| Pipeline 逐步「做什么」缺用户向讲解 | `review_pipeline` / 各 Skill 仍是契约正文；[ARCHITECTURE.md](ARCHITECTURE.md)「快速入门」已补框架 + 路径 A/B 人机分工，逐步细例仍可再补 | 继续在快速入门或模块节用例子补 Modeling/Reasoning 等 | [ARCHITECTURE.md](ARCHITECTURE.md)「快速入门」、[review_pipeline.md](prompts/review_pipeline.md) |

## 进行中

| 对象 | 工作状态 | 下一步 | 阻塞 | 原始记录 |
|---|---|---|---|---|
| 架构与门禁硬化 | active | 按初始化顺序录入真实非敏感数据 | 无真实组合数据 | [架构说明](ARCHITECTURE.md) |

## 待复核

当前没有已登记的生产 Decision 或知识条目复核日期。新增工件时在其 `review_at` 字段登记，并在本表添加链接。`decision_log/demo/` 与 `reports/demo/` 不计入生产复核。

| 对象 | 复核原因 | 到期日 | 原始记录 |
|---|---|---|---|

## 数据时效

当前没有已登记的动态产品事实。行动或组合复核前，不得将空白状态视为“当前”。关键动态字段超过 [data_contracts.md](database/data_contracts.md) 最大允许时效时记为 `unknown`，不得用 `warning` 放行后进入 `act`。

| 数据对象 | 数据状态 | 适用时点 | 下次核验条件 | 原始记录 |
|---|---|---|---|---|

## 近期关闭

| 对象 | 关闭日期 | 结果 | 原始记录 |
|---|---|---|---|
| 非行动流程演示 | 2026-07-27 | 停止/预检证明：Validation `unknown` 阻断行动，Decision 为 `research`。这不代表 Modeling 已通过。 | [研究](reports/demo/demo-non-action-pipeline.md)、[Decision](decision_log/demo/demo-non-action-pipeline.md) |
| 非行动 Committee 演示 | 2026-07-27 | 停止/预检证明：输入质量 C，ETF 实施席 `unknown`，Committee 阻断并输出 `research`。 | [审查](reports/demo/demo-committee-non-action-review.md)、[Decision](decision_log/demo/demo-committee-non-action.md) |
