# ETF 定投工作流

状态：active  
适用范围：已纳入目标配置的场内 ETF 定期定额或定期不定额买入

定投属于投资行动。审查深度见 [OPERATIONS.md](../OPERATIONS.md) §1.1：

- **例行小额定投，轻量路径**：存在未过期的**有效轻量定投 Decision**，且本次未改标的、未超原计划、无触发器命中。
- **完整八步**：新标的、首次纳入定投、加仓超原计划、或原 Decision 已失效。

## 有效轻量定投 Decision：缺一不可

原 Decision Log 必须同时写明：

1. `valid_until` 或等价到期日，且尚未到期；
2. 允许的 `product_id` 列表；不得临时替换；
3. 单笔金额上限与频率上限；
4. 失效条件与复核触发器；
5. 绑定的 IPS（Investment Policy Statement，投资政策；状态须为 `active`）与有效 `allocation_set_id`。

到期、改标的、超金额/频率、IPS/目标变更、关键动态数据过期或触发器命中 → **立即回退完整路径**。

## 轻量路径

仍须经过八个检查点，可写简短，且：

1. Research：核对原 Decision、来源与产品身份仍可定位；不能定位则回退完整路径。
2. Validation：关键动态数据在字段最大允许时效内，包括折溢价、价差、流动性、申赎/交易状态、QDII 等。任一关键项过期、冲突或`unknown`即回退完整路径；不得用 `warning` 放行后执行。
3. Modeling：确认原模型版本、硬门槛与输入口径仍适用；否则回退完整路径。
4. Reasoning：核对 IPS、目标配置、当前偏离和可用资金没有变化；否则回退完整路径。
5. Risk：核对跨境、流动性、集中度、监管税务和操作风险没有新增触发器；否则回退完整路径。
6. Challenge：仍遵循 [skills/challenge/SKILL.md](../skills/challenge/SKILL.md) 全文：3 反例 + 3 替代 + 3 可能错误，裁决为 `pass` / `revise` / `reject`。
7. Decision：确认原结论仍在有效期内且本次金额未超授权边界；否则回退完整路径。
8. Documentation：优先追加到原 Decision Log；条件变化则新建。
9. 用户执行：Agent 呈现检查清单；Agent 不得下单。用户成交后更新持仓。

## 完整路径

新标的或超计划加仓时，改走 [buy_etf.md](buy_etf.md) 的完整流程，含 Committee 触发；或等价完成八步全量审查后再定投。
