# PIOS — Claude Code 入口

本文件只负责 Claude Code 入口，不维护第二份项目规则正文。

可执行规则与能力正文的唯一来源：

- [`prompts/`](prompts/)
- [`skills/`](skills/)

[AGENTS.md](AGENTS.md) / [OPERATIONS.md](OPERATIONS.md) / [ARCHITECTURE.md](ARCHITECTURE.md) 是入口地图与手册，读完不等于读完全部规则。

## 会话开始

1. **Read** [AGENTS.md](AGENTS.md)，按其中 Prompt 加载时机表加载对应文件。
2. 任务相关时 Read `workflow/*.md` 和对应的 `skills/<name>/SKILL.md`。
3. 开场先列将 Read 清单与 Pipeline 阶段——见 AGENTS.md。

以上是约定加载顺序，不是运行时强制校验。漏读只能靠开场清单与人工抽查补救。

日常操作见 [OPERATIONS.md](OPERATIONS.md)：先看文首阅读地图，勿通读。设计说明见 [ARCHITECTURE.md](ARCHITECTURE.md)。
