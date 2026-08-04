---
name: committee
description: 对新资产暴露、首次买入、目标配置变更、重大再平衡或产品排序进行四席对抗审查。使用目标配置、组合结构、产品实施验证和风险反方四个独立视角识别分歧与阻断项；不用于例行小额定投。
---

# Committee

Committee 是 Review Pipeline 第 3–6 阶段的编排辅助，不是第九阶段，不替代 Modeling、Reasoning、Risk 或 Challenge。不新增 Pipeline 阶段，不以投票或平均分代替事实核验。

## 触发条件

- 新增资产类别、地域、币种或指数暴露
- 首次买入某资产
- 修改 IPS 或目标配置
- 重大再平衡
- 对候选产品进行排序

例行小额定投不默认使用；出现配置偏离、产品状态变化或 Decision 失效条件时重新启用。

## 前置门禁

先读取 `skills/modeling/SKILL.md`、`skills/reasoning/SKILL.md`、`skills/risk/SKILL.md`、`skills/challenge/SKILL.md`，并确认：

1. IPS 状态为 `active` 且具有批准记录；持仓、有效目标配置、候选动作和适用例外可定位。
2. 产品身份、来源、口径和动态数据已经过 Validation；关键 `fail`/`unknown` 直接停止。
3. 冻结输入包：`ips_id`、持仓/目标/偏离快照、候选动作、产品与指数资料、`source_id`、适用时点、硬约束和模型版本。

## 四席独立审查

四席共享冻结输入包，先独立形成意见，再阅读其他席意见。汇总须列出共同假设和未解决分歧。

### A. 目标与战略配置席

- 判断：方案是否服务期限、用途、现金流、风险预算和目标配置；当前是否真的需要行动
- 输出：配置必要性、目标区间、偏离、再平衡规则和不行动理由
- 禁止：用短期市场观点改写 IPS

### B. 资产暴露与组合结构席

- 判断：候选资产新增的资产类别、地区、行业、风格、币种和重叠暴露是否符合组合结构
- 输出：暴露变化、集中/重叠风险、替代资产或指数路径
- 禁止：因近期表现强势绕过风险预算

### C. 产品实施与数据验证席

- 判断：候选产品能否以可接受成本、流动性、跟踪、申赎状态、账户可达性和法规税务约束实现既定暴露；涉及跨境产品还须核对准入限制、申赎状态、交易/计价/底层暴露币种、底层市场时差与估值滞后
- 输出：身份与数据逐项 `pass`/`warning`/`fail`/`unknown`，以及来源、适用时点、口径、替代产品
- 禁止：从"产品质量较高"推出"当前应配置该资产"

### D. 风险与反方席

- 判断：不行动、现金、替代配置或更简单方案是否更优；哪些假设、数据或执行错误会推翻结论
- 输出：至少三项反例、三种替代方案、三类可能错误；风险—触发器—影响—缓释—剩余风险；`pass/revise/reject` 裁决
- 禁止：以无关联的泛化风险否决全部行动

## 冲突与阻断

按顺序处理，不以多数表决覆盖：

1. 事实冲突交回 Validation；关键 `fail`/`unknown` → `research` 或 `wait`
2. 未满足 IPS 硬约束、Risk 为 `Critical` → 阻断
3. D 席裁决 `revise` / `reject` → 暂停，补证或改方案后再审
4. 剩余判断分歧按 `material` 或 `non_material` 分类；`material` 分歧默认 `wait`/`revise`，用户作为最终裁决者在 Decision Log 中明确接受剩余风险、裁决依据与复核条件后方可继续
5. 四席 2v2 无法达成合议 → 默认 `wait` 或 `research`，用户决定方向并在 Decision Log 中记录

## 统一输出

在审查记录 Committee 节记录：

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
unresolved_disagreements:
committee_outcome: pass / revise / reject / research
next_action:
review_trigger:
```

信息丰富度：
- `A`：关键事实有适用时点、清楚口径且已核验
- `B`：部分事实滞后、来源有限或存在已披露限制
- `C`：关键事实缺失、冲突或无法验证

`C` 不允许进入 `act`。最终 Decision 仍按 `skills/decision/SKILL.md` 形成。
