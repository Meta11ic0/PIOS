---
ips_id: ips-personal-v1
title: 个人投资政策声明（IPS）
version: 0.1.0
status: draft
effective_from:
review_at:
created_at:
updated_at:
owner: self
approval_decision_id:
approved_at:
approval_evidence:
portfolio_scope:
---

# 填写顺序（新手）

1. 先写清资金用途、期限、报告币种与成功定义（本节「目的」）。
2. 再写风险与流动性约束（应急现金、可承受回撤、账户限制）。
3. 批准本 IPS 为 `active` 后，再到 [`target_allocation.csv`](target_allocation.csv) 填写配置桶；**不要**在 IPS 仍为 `draft` 时把目标配置当作可执行政策。
4. 产品与定投规则是配置桶内的实现细节，写在 Decision / 工作流中，不替代本文件。

只有 `status: active`、存在批准记录且所有必填约束已填的 IPS 才能支持 `act`。

# 目的与适用范围

- 投资目标：（例如：退休储备 / 子女教育 / 长期增值；写清优先顺序）
- 资金用途与期限：（例如：10 年以上、不可挪用金额）
- 报告币种：（例如：CNY；用于组合权重与绩效汇总）
- 成功定义：（可观察、可复核；避免“感觉不错”）
- 组合范围（账户、现金、负债是否纳入）：

# 风险与流动性约束

- 风险承受能力：
- 风险意愿：
- 最大可承受损失：（相对组合或相对年收入的可观察口径）
- 应急现金与未来现金需求：
- 可投资范围与账户约束：（例如：仅大陆证券账户场内 ETF）
- 税务、监管与操作约束：

# 战略配置

目标配置以 [`target_allocation.csv`](target_allocation.csv) 为准。每个配置桶必须说明其目标、允许区间、风险理由及复核触发器；产品只是在已批准配置桶内的实现工具。

# 再平衡政策

- 时间触发：
- 偏离触发：
- 统一估值时点：
- 新增现金优先规则：
- 卖出、分批与成本评估规则：

# 例外政策

例外记录在 [`policy_exceptions.csv`](policy_exceptions.csv)。例外不得静默覆盖配置目标或风险上限，必须关联独立 Decision、IPS 版本、批准人/时间、影响上限、期限、失效条件与关闭证据；未批准、过期或未核验的例外视为不存在。

# 复核与版本

- 定期复核：
- 事件触发复核：
- 修订原因：
- 上游决策或审查记录：
