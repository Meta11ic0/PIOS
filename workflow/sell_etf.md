# ETF 卖出场景入口

适用：中国大陆证券账户可交易的场内 ETF 卖出或减仓。卖出始终走完整 Diligence 七步。

权威规则：[prompts/diligence.md](../prompts/diligence.md) 与各 `skills/*/SKILL.md`；深度见 [OPERATIONS.md](../OPERATIONS.md)「DD 深度分级」。

流程：

1. 确认卖出原因（达到目标上限、再平衡、风险事件、原 Decision 失效等），不因短期涨跌临时改规则。
2. 读取持仓、目标配置与原 Decision Log。
3. 重大再平衡或改变资产暴露时，先按 [Committee Skill](../skills/committee/SKILL.md) 编排；不适用时在 DD 记录写明理由。
4. 完整七步过审；执行前保存 Decision Log，Agent 呈现执行清单但不得下单。
5. 用户成交后更新持仓，并在同一 Decision Log 追加实际结果。

组合级再平衡中的卖出步骤可与 [rebalance.md](rebalance.md) 合并执行，仍须满足七步与落盘要求。
