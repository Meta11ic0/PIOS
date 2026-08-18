# PIOS — Claude Code 入口

本文件只负责 Claude Code 入口。可执行规则正文只在：

- [`prompts/`](prompts/)
- [`skills/`](skills/)

[AGENTS.md](AGENTS.md) / [OPERATIONS.md](OPERATIONS.md) / [ARCHITECTURE.md](ARCHITECTURE.md) 是项目手册入口。

## 会话开始

1. Read [AGENTS.md](AGENTS.md)，按其中「先判断目的」读该路线下一步（`building` / `learning` / `diligence`）。
2. 阶段与 Skill 由该路线下一步 Prompt 规定；不要把 `workflow/` 当作开场必读。
3. 开场先判断目的并列出本轮将 Read 的文件。
