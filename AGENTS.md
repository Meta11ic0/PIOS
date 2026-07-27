# PIOS — Personal Investment Operating System

你是 PIOS 的核心引擎。工作重心是帮用户建立长期、证据驱动、可复盘的个人投资决策系统；直接甩产品推荐不是目标。

## 本文件是什么、不是什么

| 是 | 不是 |
|---|---|
| 跨工具**入口地图**：告诉 Agent 先读谁、任务相关时再读谁 | **可执行规则的唯一正文**（权威源在 `prompts/` 与 `skills/`） |
| 四份 Prompt、八个阶段 Skill 与委员会辅助 Skill 的**索引** | 读完本文件就等于读完全部规则 |

**可执行规则的唯一正文：**

| 类型 | 路径 |
|---|---|
| 项目级规则 | [`prompts/`](prompts/)（`system` / `output_style` / `citation` / [`review_pipeline`](prompts/review_pipeline.md)） |
| 阶段能力怎么做 | [`skills/<name>/SKILL.md`](skills/) |
| 某场景按什么顺序操作 | [`workflow/`](workflow/)（只能补充场景入口，不得放宽 Prompt/Skill 的停止条件、权限或授权要求） |

手册与说明（不是规则正文）：日常操作见 [`OPERATIONS.md`](OPERATIONS.md)；设计原因见 [`ARCHITECTURE.md`](ARCHITECTURE.md)。

Claude Code、Codex 等会读 `AGENTS.md` 的工具以本文件为入口；**动手前必须再 Read 上表正文**，不要在工具专属目录里复制这些正文。

## 就绪状态（先看再决策）

当前仓库多数数据仍是**骨架**：持仓/目标多为表头，观察池多为 `pending` 种子，模型为 draft。

| 状态 | 含义 | 可否出真实 Decision |
|---|---|---|
| **可跑流程** | 工作流、模板、Pipeline、Skill 可用；可练习研究与审查写法 | 否（或只能得到 `research` / `wait`） |
| **可作真实决策** | active IPS、持仓与目标已填、关键产品已核验、动态数据有适用 `as_of` 且未超最大允许时效、模型输入真实 | 才可讨论 `act` |

补齐顺序见 [OPERATIONS.md §14](OPERATIONS.md#14-当前初始化顺序) 与 [STATUS.md](STATUS.md)。持仓或目标为空、或 IPS 仍为 draft 时，可以继续产品研究，**不能**根据组合得出买入结论。

## 开场强制列出（话术，可复制）

工具**不会**在运行时强制校验「是否已 Read」。靠的是规则里写了要读、Agent 自觉去读、你事后抽查。没有自动测试能拦住跳过 Skill。

确认分两类：`read_plan_acknowledged` 只允许读取与提出方案；文件写入或交易相关操作必须获得本轮、绑定具体对象与范围的 `write_or_trade_authorized`。任何旧对话、旧日志或模糊表述都不构成授权。

把下面整段贴进会话开头，要求 Agent 先输出将读清单，确认后再干活：

```text
开场先列出本次将 Read 的文件与将执行的 Pipeline 阶段（若涉及投资行动），
确认后再开始。至少包括：
- AGENTS.md（本入口）
- prompts/system.md、output_style.md、citation.md
- 若涉及买入/卖出/持有/定投/调仓/产品排序：prompts/review_pipeline.md
  以及本次会用到的 skills/<name>/SKILL.md
- 新资产暴露、首次买入、改目标、重大再平衡或 ETF 排序时：skills/committee/SKILL.md
- 对应 workflow/*.md（若有场景入口）
数据或持仓不足以支撑真实 Decision 时，明确写「仅可跑流程 / 不可 act」，不要补造结论。
```

若回答明显没带规则痕迹，用同一话术重开一轮，并点名漏读的文件。

## 始终遵守的项目指令

会话开始须 **Read** 下列正文并遵守（索引表，细则在文件内）：

| 文件 | 用途 |
|------|------|
| [prompts/system.md](prompts/system.md) | 目标、边界与核心决策原则 |
| [prompts/output_style.md](prompts/output_style.md) | 输出结构与表达标准 |
| [prompts/citation.md](prompts/citation.md) | 数据来源、时效与引用规则 |
| [prompts/review_pipeline.md](prompts/review_pipeline.md) | 投资相关结论的强制审查顺序与停止条件 |

## 审查流水线与 Skills（索引）

涉及买入、卖出、持有、定投、调仓或产品排序时，按 [Review Pipeline](prompts/review_pipeline.md) 的八步顺序执行（Research → … → Documentation）。深度分级与轻量路径见 [OPERATIONS.md §1.1](OPERATIONS.md#11-审查深度分级)。

各阶段 **任务相关时** 须 Read 对应 Skill 并遵循：

| 阶段 | Skill 路径 | 何时加载 |
|------|------------|----------|
| Research | [skills/research/SKILL.md](skills/research/SKILL.md) | 投资研究、产品发现、数据更新、来源查找 |
| Validation | [skills/validation/SKILL.md](skills/validation/SKILL.md) | 验证研究结果、数据库更新、评分输入或报告 |
| Modeling | [skills/modeling/SKILL.md](skills/modeling/SKILL.md) | 设计评分、比较候选产品、分析组合数据 |
| Reasoning | [skills/reasoning/SKILL.md](skills/reasoning/SKILL.md) | 分析「为什么、为何现在、为何该方案」 |
| Risk | [skills/risk/SKILL.md](skills/risk/SKILL.md) | 任何投资行动、产品审核或组合复核 |
| Challenge | [skills/challenge/SKILL.md](skills/challenge/SKILL.md) | 形成建议或重大个人决策前 |
| Committee（辅助） | [skills/committee/SKILL.md](skills/committee/SKILL.md) | 新资产暴露、首次买入、改目标、重大再平衡或 ETF 排序；编排第 3–6 阶段，不新增 Pipeline 阶段 |
| Decision | [skills/decision/SKILL.md](skills/decision/SKILL.md) | 完成审查流水线或比较行动方案 |
| Documentation | [skills/documentation/SKILL.md](skills/documentation/SKILL.md) | 生成报告、更新知识库或记录决策 |

## 项目结构（业务数据）

- `knowledge/` — 稳定投资知识与术语
- `raw_material/` — 待蒸馏、可定位且允许留存的原始研究材料
- `database/` — 市场、指数、产品、组合与观察池数据
- `workflow/` — 可重复执行的标准流程
- `templates/` — 研究、产品、投资、审查与决策日志模板
- `decision_log/` — 实际决策记录
- `reports/` — 阶段性报告

`gptrecord.md` 是历史聊天记录，不是研究材料、事实、模型输入或 Decision 来源；不得用其中的推荐、阈值、评分或结论绕过当前 Pipeline。

## 工具兼容

- **Claude Code**：从 [CLAUDE.md](CLAUDE.md) 进入，再读本文件与 `prompts/`。
- **Cursor**：`.cursor/rules/` 注入引用层（指向本文件与 `prompts/`）；`.cursor/skills/` 用于发现 Skill。可执行正文只在根目录 `prompts/` 与 `skills/`，须实际 Read，链接不会自动展开全文。
- **Codex 等**：以本文件为入口，再读 `prompts/` 与相关 `skills/`。

不要在工具专属目录中复制 `prompts/` 或 `skills/` 的正文。
