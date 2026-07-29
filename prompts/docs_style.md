# Docs Style

仓库说明文档的写法。会话回答与投资结论结构见 [`answer_style.md`](answer_style.md)；本文件只管 README、STATUS、手册文首、知识条目等说明文字。

## 适用

- README、STATUS、OPERATIONS / ARCHITECTURE 的文首导读
- `knowledge/` 知识条目正文
- 其他说明、导读、状态页

## 不适用

下列处可润色句式，不得改门禁含义或字段语义：

- CSV 表头、schema、数据契约字段名
- Decision Log 冻结字段与授权状态取值
- Review Pipeline 停止条件、放行/阻断条件、阶段契约表
- Skill / workflow 中的硬门槛与授权要求

## 硬规则

1. 禁止括号旁白与心理提示。不要写「你在这里」「心里要有数」，也不要把「draft 不可」「非敏感」塞进括号当旁白。限制与条件写成完整短句，或单独清单项。
2. 链接只链到文件。需要指向某小节时，用文字备注小节名，例如：见 OPERATIONS.md「当前初始化顺序」。不要用 `#锚点`。
3. 术语优先简单英文；必要时用一句中文解释。Decision 用 `act`/`wait`/`reject`/`research`，勿用「中文（英文枚举）」包裹。
4. 可决策条件写成「须先完成」清单，不要堆在一句里用括号夹注。
5. 优先短段与清单；少口号、少二元对立腔、少「人类 vs Agent」对立叙述。需要区分职责时，直接写谁做什么。
6. 改写说明文字的长段时，须先 Read [`skills/humanizer-zh/SKILL.md`](../skills/humanizer-zh/SKILL.md)，按其中文风检查；humanizer 是文风参考，不替代本文件约定，也不放宽门禁语义。
