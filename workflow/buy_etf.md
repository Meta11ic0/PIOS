# ETF 买入场景入口

适用：中国大陆证券账户可交易的场内 ETF 买入。

权威规则：[orchestration/diligence/SKILL.md](../orchestration/diligence/SKILL.md) 七步与各 `skills/*/SKILL.md`。本文只指路，规则的权威位置如上。

前置输入：IPS 状态 `active` 且目标配置有效；候选指数与产品；同一时点的产品数据与官方公告。输入不足时按 [OPERATIONS.md](../OPERATIONS.md)「场景二」与 [STATUS.md](../STATUS.md) 门禁先补齐。

流程：

1. 按 Diligence 七步执行；进入第 N 步读对应 `skills/<阶段>/SKILL.md`。
2. 新资产暴露、首次买入、修改 IPS 或目标配置、重大再平衡、产品排序时，进入 Modeling 前按 [Committee Skill](../orchestration/committee/SKILL.md) 编排第 3–6 步；不适用时在 DD 记录 Committee 节写明理由。
3. Decision 为 `act` 后，Agent 在对话呈现执行前核对清单（清单内容见 [OPERATIONS.md](../OPERATIONS.md) 场景二「执行前检查」）；用户确认产品、方向、数量或金额限额、订单类型和有效期后，Decision Log 记录 `user_authorization: approved`。
4. Agent 的产物止于执行清单；下单由用户在券商完成，用户成交后告知明细，Agent 更新持仓并追加 Decision Log。

停止与回退条件以 `orchestration/diligence/SKILL.md` 与各 Skill 为准；Modeling draft 规则见 [etf_model_v0.1](../database/screening/etf_model_v0.1.md)。
