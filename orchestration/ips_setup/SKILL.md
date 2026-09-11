---
name: ips_setup
description: 通过对话构造个人投资政策声明 IPS 与初始目标配置。初始化系统、首次填写 investment_policy.md 或用户要求整理投资边界时使用；修订已生效的 IPS 走 diligence 政策变更。
---

# IPS Setup

本 Skill 管 IPS 的首次构造、批准落盘与初始目标配置集。政策内容只能由用户决定：Agent 提问、结构化、检验可复核性；投资目标、风险承受与配置比例由用户给出。

修订已生效（`status: active`）的 IPS 属于政策变更，改走 [orchestration/diligence/SKILL.md](../diligence/SKILL.md) 完整七步，触发 [orchestration/committee/SKILL.md](../committee/SKILL.md)，单独形成 Decision。

## 对话收集顺序

按 [investment_policy.md](../../database/portfolio/investment_policy.md) 模板小节依次收集，一次问一组，逐节确认后再进下一节：

1. 目的与适用范围：资金用途、期限、优先顺序、成功定义。
2. 报告币种与组合范围：哪些账户、现金、负债纳入。
3. 风险与流动性约束：风险承受能力与意愿、最大可承受损失、应急现金与未来现金需求。
4. 可投资范围与账户约束：市场、产品类型、账户限制。
5. 税务、监管与操作约束。
6. 再平衡政策：时间触发、偏离触发、统一估值时点、新增现金优先规则。
7. 例外政策：通常留空，保持默认「例外须独立 Decision 批准」。

## 质量标准

每条目标与约束须可观察、可复核；不可复核的表述退回用户改写：

- 成功定义给出可检验的口径，例如具体收益率、金额或期限。
- 风险承受须绑定量化口径，例如最大回撤百分比、最大可承受损失相对年收入的比例。
- 期限、金额、币种写明确数值，或写「未定」并说明原因。
- 约束之间保持一致；发现冲突当场向用户指出并请其裁决。

## 一致性检查

写入前核对：

- 可投资范围覆盖目标配置将出现的全部市场与资产类型。
- 报告币种唯一，且与未来 [target_allocation.csv](../../database/portfolio/target_allocation.csv) 的 `report_currency` 一致。
- 约束不与既有 workflow 或 Decision 冲突。

## 落盘与批准

1. 将确认后的内容填入 [investment_policy.md](../../database/portfolio/investment_policy.md)，分配 `ips_id` 与 `version`，填 `created_at`。
2. 呈现 IPS 全文，请用户逐节确认。
3. 用户在对话中明确批准（说清批准的是哪个版本）后：`status` 改 `active`，写 `approved_at`、`approval_evidence`（引用用户消息与时间），`approval_decision_id` 填 `ips-approval:<ips_id>`。
4. 提示用户 git commit 留痕；Agent 的 git 操作以用户明确指令为准。
5. 初始目标配置集写入 [target_allocation.csv](../../database/portfolio/target_allocation.csv)，`approval_decision_id` 填同一引用；权重规则见 [OPERATIONS.md](../../OPERATIONS.md)「目标配置」。写入 CSV 时列名以 [database/csv_schema.md](../../database/csv_schema.md) 为准。
