---
name: docs
description: 仓库说明文档的写法。规定句式为完整短句、链接只链到文件、术语用简单英文、条件写成清单。改 README、STATUS、OPERATIONS、ARCHITECTURE 与 knowledge 条目时使用。
---

# Docs

仓库说明文档的写法。投资结论结构见 `../decision/SKILL.md`。

## 适用

- README、STATUS、OPERATIONS / ARCHITECTURE 的文首导读
- `knowledge/` 知识条目正文
- 其他说明、导读、状态页

## 不适用

下列处只润色句式，门禁含义与字段语义保持不变：

- CSV 表头、schema、数据契约字段名（列名权威为 `database/csv_schema.md`）
- Decision Log 冻结字段与状态取值
- diligence 七步停止条件、放行/阻断条件、阶段契约表
- Skill / workflow 中的硬门槛

## 规则

1. 限制与条件写成完整短句或单独清单项。
2. 链接只链到文件。需要指向某小节时用文字备注小节名。
3. 术语优先简单英文；必要时用一句中文解释。Decision 用 `act`/`wait`/`reject`/`research`。
4. 可决策条件写成清单，每项一条。
5. 优先短段与清单。需要区分职责时直接写谁做什么。
6. 改写长段说明文字时，先 Read [`vendor/humanizer-zh/SKILL.md`](../../vendor/humanizer-zh/SKILL.md)，按其文风检查。
