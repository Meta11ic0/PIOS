---
decision_id:
type: decision_log
title:
work_status: draft
decision_status:
data_status: unknown
created_at:
updated_at:
decided_at:
frozen_at:
content_hash:
review_at:
valid_at:
information_cutoff_at:
pipeline_version:
ips_id:
allocation_set_id:
applicable_exception_ids: []
review_id:
run_id:
committee_id:
source_refs: []
upstream_ids: []
parent_decision_id:
supersedes_decision_id:
user_authorization: pending
authorization_scope:
authorized_by:
authorized_at:
authorization_message_ref:
authorization_expires_at:
execution_status: not_executed
scope:
---

# 当时的目标与约束

# 候选行动

# 当时可见数据

- 数据适用时点：
- 证据快照：
- 来源或上游记录：
- 已知限制：

# 假设、预测与触发器

## 可证伪假设

- 假设 ID：
- 可检验主张：
- 失效条件：

## 可结算预测：可选

- 预测 ID：
- 可观察事件与判定规则：
- 概率：
- 截止日：
- 对照或基准预测：

## 触发器

- 类型：invalidation / action / review
- 条件与数据来源：
- 触发后的固定动作：

# 采用的理由

# 未采用的方案与理由

# 风险审查

# Challenge 结果

# Committee：适用时

- Committee 结论：
- 阻断项或未解决分歧：
- 采纳的席位判断及原因：
- 未采纳的替代方案及原因：

# 最终决策

- `act` 仅表示建议满足条件；Agent 不得下单。实际交易只能由用户在券商系统完成。
- 决策结论：`act` / `wait` / `reject` / `research`
- 行动：
- 金额或比例边界：
- 频率边界；定投时填写：
- 允许产品列表；定投轻量路径时填写：
- `valid_until` / 失效条件：
- 执行条件：
- 用户授权：not_required / pending / approved / declined。`not_required` 仅适用于非行动结论
- 授权范围：产品、方向、数量或金额上限、订单类型、有效期
- 授权证据：本轮用户消息引用与确认时间
- 执行状态：not_executed / user_executed / recorded

# 失效条件

# 复盘

后续内容只能追加，不能回写本日志中的当时信息。

## 过程评估：先不看结果

- 当时目标、约束、证据、替代方案与门禁是否充分：
- 当时推理是否合理：

## 执行记录

- 实际执行时间、对象、数量、价格与费用：
- 执行者与成交/来源凭证：
- 更新后的持仓快照 ID：
- 计划与实际偏差：
- 偏差原因：

## 结果与预测结算

- 后续结果：
- 预测结算证据：
- Brier 分数；仅二元预测且信息完整时填写：

## 学习动作

- 学习动作 ID：
- 应更新的规则、模型或清单：
- 采用/拒绝决定：
- 验证日期与关闭证据：
