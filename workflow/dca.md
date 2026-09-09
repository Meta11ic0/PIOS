# ETF 定投场景入口

适用：已纳入目标配置的场内 ETF 定期定额或定期不定额买入。

权威规则：深度分级与「有效轻量定投 Decision」五要素见 [OPERATIONS.md](../OPERATIONS.md)「DD 深度分级」；停止条件以 [prompts/diligence.md](../prompts/diligence.md) 与各 Skill 为准。

路径判定：

- 轻量路径：存在未过期的有效轻量定投 Decision，且本次未改标的、未超原计划、无触发器命中。仍过七个检查点，可写简短；Challenge 按 Skill 全文；跳过 Committee。
- 完整路径：新标的、首次纳入定投、加仓超原计划、原 Decision 失效或五要素任一缺失时，改走 [buy_etf.md](buy_etf.md) 或等价完成七步全量 DD。

轻量路径检查点：原 Decision 与产品身份可定位；关键动态数据在最大允许时效内（任一关键项过期、冲突或 `unknown` 即回退，不得用 `warning` 放行）；模型版本与输入口径仍适用；IPS、目标配置、偏离与可用资金无变化；风险无新增触发器；原结论在有效期内且本次金额未超边界。

Documentation 优先追加原 Decision Log，条件变化则新建。Agent 不得下单；用户成交后更新持仓。
