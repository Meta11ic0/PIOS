# 再平衡场景入口

适用：调仓行动。必须按 [orchestration/diligence/SKILL.md](../orchestration/diligence/SKILL.md) 完整七步执行。

流程：

1. 确认 [investment_policy.md](../database/portfolio/investment_policy.md) 的 IPS 状态 `active` 且仍有效；记录 IPS、目标配置集、偏离快照和有效例外。
2. 统一估值时点、报告币种和可复核汇率计算实际配置及偏离；检查是否达到时间或偏离触发条件。
3. 优先评估用新增现金修复偏离，再评估卖出；计算交易费、税费、价差和机会成本；比较一次调整与分批调整。
4. 重大再平衡、改目标或改变资产暴露时，必须执行 [Committee Skill](../orchestration/committee/SKILL.md)；不适用时记录理由。
5. 完整七步过审；形成具体行动边界和执行顺序；执行前保存 Decision Log；下单由用户在券商完成，用户执行后更新组合数据并在同一 Decision Log 追加实际结果。

修改目标与执行再平衡是两类决策，分别记录，权威在 [decision Skill](../skills/decision/SKILL.md)「决策粒度」；目标权重的调整依据为 IPS 与目标配置的复核周期，权威在同一 Skill「输出」节。本行只做场景化提醒。
