---
name: learning
description: 知识调研编排。规定深度分级、停止条件与产物，供弄清概念或产品事实、且不形成投资行动结论的会话使用。
---

# Learning

本编排服务 `[learning]` 路线：弄清概念或产品事实，本轮不形成投资行动结论。产物是研究结论与 `knowledge/` 条目。发现产品不等于推荐产品。

本轮走哪一档由 [workflow/learning.md](../../workflow/learning.md) 判定。写入 `knowledge/` 条目时按 [skills/docs/SKILL.md](../../skills/docs/SKILL.md) 的写法落盘。

## 深度分级

档位定义与升格条件在 [skills/research/SKILL.md](../../skills/research/SKILL.md) 流程第 1 条；本路线只走前两档。

| 档 | 什么时候走 | 加载 | 产物 |
|---|---|---|---|
| 轻量查询 | 只解释概念，不含数值或可行动事实 | [skills/evidence/SKILL.md](../../skills/evidence/SKILL.md) 底线部分 | 留在对话 |
| 标准取证 | 含数值、可行动事实，或结论要写进 `knowledge/` | research 与 [skills/validation/SKILL.md](../../skills/validation/SKILL.md) 全文 | 研究结论与 `knowledge/` 条目；`verified` 事实按 [workflow/register.md](../../workflow/register.md) 登记 `source_id` |

含数值或可行动事实不能停在轻量查询档：升格标准取证，或把该项标为 `unknown` / 待验证。

## 停止

用户确认要对具体标的形成买入、卖出、持有、定投、调仓或产品排序结论时停下，改标 `[invest]`，改读 [orchestration/diligence/SKILL.md](../diligence/SKILL.md)，停用知识调研身份。

## 输出

问题、来源清单、已验证事实与适用时点、冲突与缺失项、仍待验证项。
