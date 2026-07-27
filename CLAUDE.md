# PIOS — Claude Code 入口

本文件只负责 Claude Code 入口，**不维护**第二份项目规则正文。

可执行规则与能力正文的唯一来源：

- [`prompts/`](prompts/)
- [`skills/`](skills/)

[AGENTS.md](AGENTS.md) / [OPERATIONS.md](OPERATIONS.md) / [ARCHITECTURE.md](ARCHITECTURE.md) 是入口地图与手册，读完不等于读完全部规则。

## 会话开始

1. **Read** [AGENTS.md](AGENTS.md)，并按该文件全文遵守。
2. 按 `AGENTS.md` 继续 **Read** `prompts/` 中的四份项目规则。
3. 任务相关时，再 **Read** 对应的 `skills/<name>/SKILL.md`。
4. 涉及投资行动时，**Read** [prompts/review_pipeline.md](prompts/review_pipeline.md)，并用 [AGENTS.md 开场话术](AGENTS.md#开场强制列出话术可复制) 先列出将读文件与阶段，再动手。

以上是约定加载顺序，**不是**运行时强制校验。漏读只能靠开场清单与人工抽查补救。

日常操作见 [OPERATIONS.md](OPERATIONS.md)（先看文首阅读地图，勿通读）；设计说明见 [ARCHITECTURE.md](ARCHITECTURE.md)。骨架数据下「可跑流程」与「可作真实决策」的区分见 OPERATIONS 文首就绪条件。
