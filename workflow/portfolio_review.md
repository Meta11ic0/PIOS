# 组合复核场景入口

适用：按固定周期或重大事件触发。

权威规则：涉及继续持有、追加、减少、不行动或调仓结论时，必须按 [orchestration/diligence/SKILL.md](../orchestration/diligence/SKILL.md) 完整七步执行；步骤细则见 [OPERATIONS.md](../OPERATIONS.md)「场景四」。

流程：

1. 先复核 [investment_policy.md](../database/portfolio/investment_policy.md) 的 IPS 状态与约束是否仍成立；政策变更必须单独形成 Decision 并触发 [Committee Skill](../orchestration/committee/SKILL.md)。
2. 统一估值时点与报告币种更新持仓；按资产、地区、币种、行业和产品汇总暴露，与目标配置比较并追加偏离快照。
3. 检查集中度、相关性、流动性、最大可承受损失，以及费用、税务、法规和产品状态变化。
4. 复核各持仓原始投资逻辑与失效条件；对继续持有、追加、减少和不行动分别走完整七步。
5. 复核结论写入 Decision Log，并列出需要研究的事项；触发再平衡条件时进入 [rebalance.md](rebalance.md)。

下单由用户在券商完成。
