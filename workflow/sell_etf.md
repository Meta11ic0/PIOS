# ETF 卖出工作流

状态：active  
适用范围：中国大陆证券账户可交易的场内 ETF 卖出或减仓

卖出属于投资行动，必须执行完整 Review Pipeline；见 [prompts/review_pipeline.md](../prompts/review_pipeline.md)。审查深度见 [OPERATIONS.md](../OPERATIONS.md)「审查深度分级」：**卖出始终走完整八步**。

## 流程

1. 确认卖出原因：达到目标上限、再平衡、风险事件、原 Decision 失效等。不是因短期涨跌临时改规则。
2. 读取持仓、目标配置与原 Decision Log。
3. 若属于重大再平衡或改变资产暴露，先执行 [Committee Skill](../skills/committee/SKILL.md)；不适用时记录理由。
4. 按 [Review Pipeline](../prompts/review_pipeline.md) 完整八步执行；各步细则见对应 `skills/*/SKILL.md`。
5. Challenge 遵循 [skills/challenge/SKILL.md](../skills/challenge/SKILL.md) 全文。
6. 执行前保存 Decision Log。Agent 向用户呈现执行清单；Agent 不得下单。用户成交后更新持仓，并在同一 Decision Log 追加实际结果。

组合级再平衡中的卖出步骤，可与 [rebalance.md](rebalance.md) 合并执行，但仍须满足八步与 Documentation 在执行前完成。
