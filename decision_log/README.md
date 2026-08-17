# 决策日志约定

每个 Decision Log 是某一时点的不可改写决策快照，不是可随结果回填重写的研究报告。

生产 Decision 写在本目录；**演示**只放在 [`demo/`](demo/)，且必须 `scope: demo_only`（`demo_only`）。查询生产 Decision、复盘样本或授权先例时排除 `demo/`。

## 生命周期

1. **决定时**：冻结目标与约束、active `ips_id`、目标配置集、有效例外、上游 `dd_id`/`run_id`、来源与事实快照、候选方案、风险、Challenge、决策边界、失效条件含 `valid_until`，以及复核时间；记录 `frozen_at` 与内容哈希。
2. **授权时**：`act`只是建议满足执行条件，不等于 Agent 获得交易授权。只有本轮用户明确确认产品、方向、数量/限额、订单类型和有效期时，才可记录 `approved`；否则保持 `pending`。
3. **执行后**：追加实际执行、计划偏差和原因；持仓事实更新到 `database/portfolio/`。Agent 不得下单。
4. **复盘时**：追加结果、预测结算、过程评估和学习动作。过程评估先只看决定时信息，再记录结果，避免后见之明。

## 关联与修订

- 使用 `parent_decision_id` 关联同一问题下的后续记录。
- 使用 `supersedes_decision_id` 取代已失效的旧决定；不要修改旧文件的当时理由或冻结内容。
- 只为具有可观察事件、明确截止日和结算规则的判断填写预测；样本不足时不汇总为能力分数。
- 触发器分为 `invalidation`、`action`、`review`。触发器只允许重新进入 DD 流程，绝不自动下单。

Decision Log 可记录 `act`/`wait` / `reject` / `research`（`act`、`wait`、`reject` 或 `research`）；没有成交同样应保留记录，以便复盘过程质量。`unknown`停止演示不是“DD 通过”。
