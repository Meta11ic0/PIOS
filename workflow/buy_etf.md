# ETF 买入工作流

状态：active  
适用范围：中国大陆证券账户可交易的场内 ETF

## 前置输入

- 投资目标、期限和本次可用资金
- 已批准且状态为 `active` 的 IPS（Investment Policy Statement，投资政策）、当前组合、有效目标配置和未过期例外
- 候选指数和候选产品
- 同一时点的产品数据与官方公告

## 流程

1. **确定目标**：说明本次资金要解决的配置问题。
2. **检查组合**：确认该资产仍低于目标配置，且不影响应急现金。
3. **确定指数**：比较指数规则、集中度、币种和长期风险。
4. **发现产品**：从交易所、基金公司或可靠行情工具获取完整候选集。
5. **过审**：按 [Diligence 八步](../prompts/diligence.md) 执行八步；各步读对应 `skills/*/SKILL.md`。新资产暴露、首次买入、改目标、重大再平衡或 ETF 排序时，进入 Modeling 前按 [Committee Skill](../skills/committee/SKILL.md) 编排第 3–6 步，不是八步之外的前置步骤；不适用时在 DD 记录的 Committee 节写明理由。Modeling 在 draft 阶段只做字段对比与否决项；见 [etf_model_v0.1](../database/screening/etf_model_v0.1.md)。Challenge 须 Skill 全文。Documentation 在真正执行前完成；见 [OPERATIONS.md](../OPERATIONS.md)「§6.4」「§6.5」。
6. **执行前检查**：核对代码、市场、价格、订单类型、数量、费用、交易/计价/底层暴露币种、QDII 额度、申赎状态、底层市场时差和估值滞后。
7. **用户执行**：仅当 Decision 为有效 `act`、`user_authorization: approved` 绑定本轮对象/方向/金额/有效期且执行条件仍成立时，向用户呈现清单。Agent 不得下单；用户在券商完成交易后再记录结果。

## 停止条件

- 关键数据过期或相互冲突
- 折溢价、价差或流动性超出既定限制
- 官方风险公告未完成核对
- 该资产已达到或超过目标配置
- Challenge 为 `revise` / `reject`

具体阈值必须在决策前由用户确认并写入评价模型，不能把示例阈值当作永久规则。
