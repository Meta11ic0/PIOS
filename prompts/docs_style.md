# Docs Style

仓库说明文档的写法。会话回答与投资结论结构见 [`answer_style.md`](answer_style.md)。

## 适用

- README、STATUS、OPERATIONS / ARCHITECTURE 的文首导读
- `knowledge/` 知识条目正文
- 其他说明、导读、状态页

## 不适用

下列处可润色句式，不得改门禁含义或字段语义：

- CSV 表头、schema、数据契约字段名
- Decision Log 冻结字段与状态取值
- Review Pipeline 停止条件、放行/阻断条件、阶段契约表
- Skill / workflow 中的硬门槛

## 规则

1. 禁止括号旁白与心理提示。限制与条件写成完整短句或单独清单项。
2. 链接只链到文件。需要指向某小节时用文字备注小节名。不要用 `#锚点`。
3. 术语优先简单英文；必要时用一句中文解释。Decision 用 `act`/`wait`/`reject`/`research`。
4. 可决策条件写成清单，不要堆在一句里用括号夹注。
5. 优先短段与清单。需要区分职责时直接写谁做什么。
6. 改写长段说明文字时，先 Read [`skills/humanizer-zh/SKILL.md`](../skills/humanizer-zh/SKILL.md)，按其文风检查。
