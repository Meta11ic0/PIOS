# PIOS — Claude Code 入口

本文件只负责 Claude Code 入口。可执行规则正文只在：

- [`orchestration/`](orchestration/)
- [`skills/`](skills/)
- [`vendor/`](vendor/)
- [`workflow/`](workflow/)
- [`database/`](database/)

[AGENTS.md](AGENTS.md) / [OPERATIONS.md](OPERATIONS.md) / [ARCHITECTURE.md](ARCHITECTURE.md) 是项目手册入口。

## 会话开始

1. Read [AGENTS.md](AGENTS.md)，按其中「先判断目的」读该路线指定的正文（`building` / `learning` / `invest`）。
2. 阶段与能力单元由该路线的编排正文规定；不要把 `workflow/` 当作开场必读。
