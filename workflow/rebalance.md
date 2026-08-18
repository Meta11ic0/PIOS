# 再平衡工作流

状态：active

再平衡属于调仓行动，必须按 [Diligence 八步](../prompts/diligence.md) 完整八步执行；深度见 [OPERATIONS.md](../OPERATIONS.md)「DD 深度分级」。细则在各 Skill，本文不重复展开。

## 流程

1. 确认 [`investment_policy.md`](../database/portfolio/investment_policy.md) 中 IPS（Investment Policy Statement，投资政策）状态为 `active` 且仍有效；记录 IPS、目标配置集、偏离快照和有效例外。修改政策、按既定政策再平衡、替换配置桶内产品是三类不同 Decision。
2. 使用统一估值时点、报告币种和可复核汇率计算实际配置及偏离。
3. 检查是否达到预先设定的时间或偏离触发条件。
4. 优先评估用新增现金修复偏离，再评估卖出。
5. 核验跨境 ETF 的 QDII 额度、申赎状态、交易时差、底层市场休市、估值滞后和折溢价口径。
6. 计算交易费、税费、价差、折溢价和机会成本。
7. 比较一次调整与分批调整，列出各自风险。
8. 重大再平衡、改目标或改变资产暴露时必须执行 [Committee Skill](../skills/committee/SKILL.md)；不适用时记录理由。
9. 完成 Diligence 完整八步；禁止因近期涨跌临时修改目标权重。
10. 形成具体行动边界和执行顺序。
11. 执行前保存 Decision Log。Agent 向用户呈现执行清单；Agent 不得下单。用户执行后更新组合数据，并在同一 Decision Log 追加实际执行结果。

目标配置本身发生变化时，应单独记录原因，不能把“修改目标”包装成普通再平衡。
