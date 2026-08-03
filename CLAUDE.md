# PIOS — Claude Code 入口

本文件只负责 Claude Code 入口。可执行规则正文只在：

- [`prompts/`](prompts/)
- [`skills/`](skills/)

[AGENTS.md](AGENTS.md) / [OPERATIONS.md](OPERATIONS.md) / [ARCHITECTURE.md](ARCHITECTURE.md) 是项目手册入口。

## 会话开始

1. Read [AGENTS.md](AGENTS.md)，按其中 Prompt 加载时机表加载对应文件。
2. 任务相关时 Read `workflow/*.md` 和对应的 `skills/<name>/SKILL.md`。
3. 开场先列 Read 清单与 Pipeline 阶段——见 AGENTS.md。
