---
review_id:
type: review
title:
work_status: draft
decision_status:
data_status: unknown
review_target:
created_at:
updated_at:
reviewed_at:
as_of:
information_cutoff_at:
pipeline_version:
ips_id:
source_refs: []
upstream_ids: []
run_id:
committee_id:
review_at:
next_action:
blocker:
scope:
---

# Research

- 状态：
- 输入来源或上游记录：
- 发现：

# Validation

- 状态：pass / warning / fail / unknown
- 验证项：字段、值、单位/口径、适用时点、`source_id`、裁决：
- 发现：

# Modeling

- 硬门槛 / 否决项：
- 字段对比。draft 阶段不自动评分：
- 敏感性：

# Reasoning

- 关键链路：
- 必要假设：

# Risk

- 等级：Low / Medium / High / Critical
- 风险—触发器—影响—缓释—剩余风险：

# Challenge

- 反例：
- 替代方案：
- 可能错误：
- 裁决：pass / revise / reject

# 决策委员会：适用时

适用触发：新资产暴露、首次买入、改目标、重大再平衡、ETF 排序。不适用时写明理由。

```text
committee_id:
input_package:
information_quality: A / B / C
seat_a:
seat_b:
seat_c:
seat_d:
fact_conflicts:
blocking_gates:
unresolved_disagreements: # material / non_material
committee_outcome: pass / revise / reject / research
next_action:
review_trigger:
```

- 输入包：
- 信息丰富度：A / B / C。`C` 不得进入 `act`
- 目标与战略配置席：
- 资产暴露与组合结构席：
- ETF 实施与数据验证席：
- 风险与反方席：
- 事实冲突：
- 阻断项：
- 未解决分歧：material / non_material：
- 委员会结论：pass / revise / reject / research
- 下一步：
- 复核触发器：

# Final Review

- 是否允许进入 Decision：
- IPS 是否 active、目标配置集是否有效：
- 关键动态数据是否均在最大允许时效内：
- 未解决事项：
- 所有阻断项的关闭证据：
- 最终放行人或用户裁决：

# Decision Handoff

- 建议结论：act / wait / reject / research
- Decision Log 路径：
- 最终行动边界仅以 Decision Log 为准：
- 提醒：`act` ≠ 交易授权；Agent 不得下单

# Documentation

- 报告路径：
- 决策日志路径：
- 数据快照：
- 复核日期或触发条件：
