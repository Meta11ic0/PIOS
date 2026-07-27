# 外部参考：相关产品与文章

> 调研日期：2026-07-27  
> 用途：梳理与 PIOS 相邻的开源产品与方法论，便于对照定位。不构成投资建议。  
> 元数据：GitHub API（同日拉取）+ 各仓库 README / 官方站点。

## 与 PIOS 的关系（总览）

| 参考 | 一句话 | 与 PIOS |
|------|--------|---------|
| [AI Berkshire](#1-ai-berkshire) | Claude Code / Codex 价值投资研究 Skill 框架 | 最接近：Agent 规则、多视角对抗、可复盘流程；偏个股深度研究 |
| [Austin 文：Wealth LLM Wiki](#2-文章用-claude-code-构建个人理财知识库) | LLM Wiki + Claude Code 搭建个人理财知识库 | 方法论接近：三层知识流与隐私分层；目录可对照，隐私策略并不相同 |
| [Wealthfolio](#3-wealthfolio) | Local-first 个人财务 / 组合追踪（桌面为主，兼 iOS / Docker Web） | 互补：持仓记账与净值可视化；不含决策纪律流水线 |
| [Ghostfolio](#4-ghostfolio) | 自托管财富管理 Web 应用 | 互补：多账户绩效与导入；偏运营型组合仪表盘 |

**PIOS 自身定位（对照基准）**：证据驱动、可复盘的个人投资决策操作系统。规则与 Skill 在 `prompts/`、`skills/`，另有审查流水线、知识库与决策日志。它不是券商同步 App，也不是单纯「问 AI 买不买」。

---

## 1. AI Berkshire

### 定位与一句话摘要

面向 Claude Code / Codex 的价值投资研究 Skill 合集（GitHub 简介称「AI 时代的伯克希尔」）。把巴菲特、芒格、段永平、李录四位大师的方法论结构化，用多 Agent 并行与对抗提高分析质量与决策纪律。

> 一个人 + Claude Code / Codex ≈ 一个投研团队。

### 核心功能 / 架构特点

- **三层架构**：Skill 层（README 列出 20 个场景入口）→ Agent 层（Team Lead 调度四大师视角）→ 工具层（精确计算、交叉验证、报告抽检）。轻量 Skill 可不经 Agent 层。
- **深度研究**：`/investment-research`、`/investment-team`、管理层纵深、未上市公司研究、长文系列等。
- **财报 / 行业 / 持仓**：财报精读与团队解读、产业链扫描、漏斗筛选、组合复核、投资论文追踪与漂移检测、股价异动归因等。
- **反偏见机制**：信息丰富度 A/B/C、芒格式逆向检验、快速否决清单、反共识检查、留白（灰色地带）、强制结论（通过 / 不通过 / 灰色）与价格区间分层建议。
- **金融严谨性**：Python `decimal.Decimal` 手算校验市值 / 估值；关键数据要求多源交叉验证。
- **多客户端入口**：同一套 canonical workflow。`skills/*.md` 为 Claude Code 源文件；`codex-skills/` 由脚本从前者生成；另有可选的 `codex-prompts/` slash prompt 兼容层。

### 技术栈

- 以 Markdown Skill / Prompt 与脚本为主，不是完整 SaaS 应用。
- 辅助工具：Python（如 `tools/financial_rigor.py`）。
- 运行时依赖：Claude Code 或 OpenAI Codex CLI。

### 与「个人投资知识库 / 投资操作系统」的关系

属于 **AI 投研 Skill 框架（研究侧）**：流程强、对抗强、报告产出强；仓库叙事里有 `reports/`、实盘记录等。重点是「如何高质量研究一家公司 / 一个行业」，不是本地记账 UI。作者宣传口径里有公开实盘收益；PIOS 不应把未经验证的外部账户数据当决策输入。

### 与 PIOS 的可比点

| 相似 | 差异 |
|------|------|
| Agent 入口文件（`AGENTS.md` / `CLAUDE.md`）、Skill 分场景、反方挑战、可复现结构 | 方法论绑定「四大师」个股价值投资；PIOS 偏个人组合、ETF / 配置与八阶段 Review Pipeline |
| 强调「不能直接问 AI」、要结论与证据 | README 示例会输出激进 / 稳健 / 保守建仓区间；PIOS 在数据骨架未就绪时明确限制 `act` |
| 多 Agent 并行对抗 | PIOS 的 Committee / Challenge 是配置审查辅助，不是四大师并行搜网 |

**可借鉴**：强制结论格式、信息丰富度评级、财务验算工具、Skill 安装与多客户端同步方式。  
**不宜照搬**：公开实盘收益宣传口径；未经验证的个人账户数据不要当作 PIOS 决策输入。

### 关键信息（截至 2026-07-27）

| 项 | 值 |
|----|-----|
| Stars | 14,318（约 14.3k） |
| Forks | 2,026（约 2.0k） |
| License | MIT |
| 主语言 | Python（GitHub 主语言；主体大量为 Markdown Skill） |
| 活跃度 | 活跃（`pushed_at` 2026-07-26） |
| 创建 | 2026-04-07 |

### 来源链接

- 仓库：https://github.com/xbtlin/ai-berkshire  
- README（中）：仓库根目录 `README.md`  
- 相关公众号（作者侧精选）：「复利炼丹炉」（见项目 README，非 PIOS 背书）

---

## 2. 文章：用 Claude Code 构建个人理财知识库

> **原文标题**：Building Your Personal Finance Knowledge Base with Claude Code  
> **作者**：Austin Xu  
> **日期**：2026-05-04  
> **链接**：https://austinxyz.github.io/blogs/blog/2026/05/04/wealth-llm-wiki  
> **系列**：AI Wealth Management 系列第 1 部分  
> **说明**：以下为简体中文译文，保留原文结构与要点；措辞按中文技术文习惯整理，不增补原文没有的事实。

---

### 译文正文

这是「AI 财富管理」系列的第 1 部分，讲怎么用 Claude Code 和 LLM Wiki 做个人投资相关的知识管理。

多数真正有专长的人（金融、法律、移民或其他领域）把知识放在脑子里：既帮不到别人，也无法复利积累。这篇文章想改变这一点：搭一套结构化知识库，让 AI 能直接在其上推理，以后或许还能服务他人或产生收入。

示例领域是个人理财。北美理财确实复杂（401K、Roth IRA、HSA、Wash Sale Rule、FBAR、跨境合规），值得系统化。但方法本身与领域无关：凡是你已积累专长的领域，同一套做法都适用。

---

#### 两件工具：Claude Code 与 LLM Wiki

##### Claude Code

Claude Code 是 Anthropic 的命令行 AI 助手。和浏览器聊天的关键差别在于：它跑在你的终端里，能直接读写本机文件。

这一点会改变「个性化建议」的含义。聊天型 AI 不了解你的处境，只能给通用答案。Claude Code 可以读到描述你真实 401K 余额、税率档次与当前持仓的文件，从而给出贴合具体情况的建议。

你不需要会写代码，用自然语言下指令即可。

##### LLM Wiki 方法

Karpathy 的 LLM Wiki 概念提出三层知识结构：

```text
raw_material/    ← 原始材料：你收集的文章、笔记、链接
wiki/            ← 蒸馏知识：结构化、可长期复用的参考条目
output/          ← 综合产出：基于你个人处境的分析
```

流程是：你把原材料喂给 AI，它蒸馏成 wiki 条目。需要做决策时，AI 阅读 wiki，再加上 `output/` 里的个人上下文，给出具体、有根据的建议。

##### 为什么要组合使用

没有结构化知识库的 Claude Code，只会给你通用答案，因为 AI 不知道你知道什么。没有 Claude Code 的 LLM Wiki，则一切都要自己手工维护。合在一起时，Claude Code 成为知识的操作系统，知识库则让每个答案都贴合你的真实处境。

---

#### 搭建项目

##### 安装工具

你需要三样东西。

**VS Code**：查看和编辑 Markdown。下载：code.visualstudio.com。

**Claude Code**：用 npm 安装：

```bash
npm install -g @anthropic-ai/claude-code
```

需要 Node.js（nodejs.org）以及 Claude Pro 订阅（约每月 $20）。若不想用 CLI，Claude.ai 的 Projects 也能做类似工作；但在文件操作与 Skill 自动化方面，Claude Code 更强。

**Git + GitHub**：知识库的版本控制。安装 Git（git-scm.com），并在 github.com 注册免费账号。若是 Git 新手，从 GitHub 官方入门指南开始即可。

##### 用一段提示词初始化

创建文件夹 `wealth-llm-wiki`，在其中打开 Claude Code：

```bash
cd ~/Documents/wealth-llm-wiki
claude
```

然后粘贴这段初始化提示：

```text
Initialize a personal finance knowledge base using the LLM Wiki three-layer structure.
Reference Karpathy's LLM Wiki methodology: https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f

Create:
1. Directory structure:
   - raw_material/ (source articles, notes, links)
   - wiki/ (distilled knowledge entries, organized by topic)
   - output/ (personal data and analysis — never committed to Git)
2. README.md in each directory explaining its purpose and conventions
3. CLAUDE.md (instructions for Claude Code itself) including:
   - Project overview: personal finance knowledge base
   - Directory structure
   - Writing conventions: technical terms in English, explanations in plain language
   - Privacy rules:
     * Never put personal account balances, specific holdings, or dollar amounts in raw_material/ or wiki/
     * All personal data goes in output/ (git-ignored)
     * Wiki entries use general examples, not personal data
4. .gitignore that excludes output/ and *.private files
5. git init

Confirm the directory structure and that CLAUDE.md includes the full privacy rules.
```

Claude Code 跑完后，项目大致如下：

```text
wealth-llm-wiki/
├── CLAUDE.md
├── .gitignore
├── raw_material/
│   └── README.md
├── wiki/
│   └── README.md
└── output/
    └── README.md
```

`CLAUDE.md` 是关键：每次在该项目中打开 Claude Code，都会自动读取它。你不必反复解释项目上下文。

---

#### 建设知识库

##### 步骤 1：搭好知识骨架

先脚手架化 wiki 结构：先有分类与条目清单，再填内容。

```text
Create the directory structure and outline for a North American personal finance
knowledge base under wiki/.

Categories:
1. financial-basics/ — account types, credit system, net worth calculation
2. investing/ — ETF vs active funds, US stocks, asset allocation, brokerage accounts
3. tax/ — federal tax brackets, capital gains, Wash Sale Rule, FBAR/FATCA
4. retirement/ — 401K, Roth IRA vs Traditional IRA, Mega Backdoor Roth, 401K Rollover
5. cross-border/ — US-China wire transfers, foreign account reporting, FX risk
6. education/ — 529 Plan basics, 529 vs Roth IRA for education

For each category: create the folder and an index.md listing the entries to build.
```

##### 步骤 2：按需填充条目

不必一次填完。需要时再建条目。以「401K Rollover」为例的完整模式：

先收集原材料：去 IRS.gov、Investopedia 等你信任的来源，把相关内容粘贴到 `raw_material/retirement/401k-rollover-sources.md`，并附上来源链接。

然后让 Claude Code 蒸馏：

```text
Read raw_material/retirement/401k-rollover-sources.md and distill it into a
wiki entry at wiki/retirement/401k-rollover.md.

Requirements:
- Structure: Overview → Options → Comparison → Key rules → Common mistakes
- Use general examples, no personal dollar amounts
- Link related concepts with [[Wiki Links]] (e.g., [[Traditional IRA]], [[Roth IRA]])
- Add YAML frontmatter: tags: [retirement, 401k, rollover]
```

产出会是结构化条目，覆盖四类 rollover 选项（留在旧计划 / 滚入新 401K / 滚入 IRA / 兑现）、60 天规则、Direct vs Indirect 机制，以及最常见错误。

---

#### 用 Obsidian 浏览

Obsidian 是原生 Markdown 知识工具，浏览 wiki 比普通文件管理器舒服得多。

把整个 `wealth-llm-wiki` 文件夹作为 Obsidian vault 打开（根目录，而不只是 `wiki/`），这样可以同时看到 `raw_material/`、`wiki/` 和 `output/`。

安装两个社区插件：

- **Dataview**：像数据库一样查询 wiki（例如「列出所有标记为 `retirement` 的条目」）
- **Templater**：为 `output/` 里的个人处境文件做模板，保持记录结构一致

随着 wiki 增长，Graph View 能可视化知识连接。

---

#### 用 Skills 做自动化

Claude Code Skill 是存成文件、用斜杠命令调用的结构化提示。建好之后，调用 Skill 即可，不必每次从零写提示。Claude Code 也能帮你生成 Skill。

对本知识库特别有用的两个：

**`/wealth-extract`**：把新的源文件蒸馏成 wiki 条目。

```text
Create a skill named wealth-extract.
Function:
- Accept a file path in raw_material/ as an argument
- Identify the relevant finance topic
- Distill into a standard wiki entry (structured, [[wiki links]], YAML frontmatter)
- Save to the appropriate wiki/ subdirectory
```

用法：`/wealth-extract raw_material/tax/roth-conversion-article.md`

**`/wealth-sync`**：审计尚未处理的内容。

```text
Create a skill named wealth-sync.
Function:
- Scan all files in raw_material/
- Compare against wiki/ to find unprocessed source material
- Output a Markdown table: file name, inferred topic, suggested wiki destination
```

每周跑一次 `/wealth-sync`，就能看到原材料堆里还有什么等待蒸馏。

---

#### 私有数据层

`output/` 目录是「通用知识变成个人建议」的地方。

创建 `output/my-situation.md`，写入你的真实细节：

```markdown
# Personal Financial Situation

## Employment
- Software engineer at a tech company, changed jobs this year
- 401K: employer matches 50% up to 6%
- Tax filing: MFJ, 22% federal bracket, ~$X AGI

## Accounts
- Roth IRA at Fidelity: $X balance
- Previous employer 401K to roll over: $X
- Taxable brokerage: mostly index funds

## China assets
- Bank deposits over $10,000 — annual FBAR filing required
- Annual transfers within $10K limit

## Family
- Married, one child, planning 529 contributions
```

该文件被列入 `.gitignore`，按原文说法「永远不离开你的电脑」（指不提交到 Git）。但只要它在上下文范围内，当你问「今年该不该做 Roth conversion？」时，Claude Code 读的是你真实的税率档与账户规模，而不是通用例子。

文件越具体，建议越有用。

---

#### 用 Quartz 发布

若 wiki 内容扎实、想对外分享，Quartz 可以把 Obsidian 风格 Markdown 转成带可用 wiki 链接与搜索的静态网站，部署到 GitHub Pages 或云平台（Vercel、Cloudflare Pages 等）。

你控制发布范围：只发 `wiki/`，或连同 `raw_material/` 一起展示研究过程。`output/` 始终保持私有，仓库级 `.gitignore` 强制这一点。

---

#### 这套东西可以长成什么

框架本意是可生长的，取决于你的目标：

**自用**：更好的决策、更快的研究，以及在真正咨询 CPA / 顾问时问出更清楚的问题。

**分享 raw + wiki**：你的知识劳动成为公共资源，在领域内积累声誉。

**部分分享**：公开 wiki，保留深度分析私有。公开内容证明专长；需要个性化分析的人会找到你。

**客户目录**：建立 `output/clients/` 结构。用同一知识库服务多人，每位客户数据隔离在自己的文件夹。产出保持私有；wiki 是共享地基。

**专家 Agent**：服务足够多客户后，模式会重复。把你的分析框架固化成 Claude Code Skill，让它做初稿；你负责审阅与定稿。

---

#### 延伸阅读（原文链接）

- Part 2: From LLM Wiki to Investment Agent — Lessons from Building rwh-overlay
- Part 3: The Investment Operating System — Full Workflow Walkthrough
- Karpathy's LLM Wiki Gist：https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f
- wealth-llm-wiki on GitHub（见原文站内链接）

---

### 与 PIOS 的对照（文章 → 仓库）

| Austin / LLM Wiki | PIOS |
|-------------------|------|
| `raw_material/` | `raw_material/`（待蒸馏原始材料） |
| `wiki/` | `knowledge/`（稳定知识与术语） |
| `output/` + gitignore（个人数据默认不入库） | 个人持仓 / 决策可落在 `database/portfolio/`、`decision_log/`、`reports/`；当前私人仓库策略下这些路径**可以**入库。更接近「不入库」的是 `private/`、`*.local.md` |
| `CLAUDE.md` | `CLAUDE.md` + `AGENTS.md` + `prompts/` |
| Skills 蒸馏与同步 | `skills/*` + Review Pipeline |

文章的核心贡献是 **隐私分层 + 三层知识流**。PIOS 目录可对照，但默认隐私策略不同：Austin 把个人金额与持仓关进 gitignored 的 `output/`；PIOS 另有强制审查顺序与停止条件（见 `prompts/review_pipeline.md`），并不把 `database/portfolio/` 自动等同于「永不提交」。

另需注意：原文「永远不离开你的电脑」指不提交到 Git。使用 Claude Code 时，文件内容仍可能作为提示发送给模型 API；这与「不入库」不是同一件事。

---

## 3. Wealthfolio

### 定位与一句话摘要

开源、注重隐私的 **local-first 个人财务 / 组合追踪器**：投资、净值、支出与情景模拟。数据默认存在本机，无需账户即可永久免费使用基础能力。

### 核心功能 / 架构特点

- 多账户、多资产组合追踪；活动（成交）导入与管理。
- 绩效分析：时间加权 / 资金加权收益、基准对比、历史分析。
- 目标规划与配置管理；多币种与汇率。
- **Local-first**：SQLite 本地存储，无强制云库。
- 可选 **Wealthfolio Connect**（订阅）：30+ 券商只读同步、加密多端同步；手动录入与 CSV 导入始终免费。
- **Addon 系统**：TypeScript SDK、权限同意、热重载、可扩展页面与数据访问。
- **交付形态**：桌面（Windows / macOS / Linux）、iOS，以及 Docker / 自托管 Web（Axum 服务端 + 可选 OIDC）。

### 技术栈

| 层 | 技术 |
|----|------|
| 前端 | React、React Router、Tailwind、Radix/Shadcn、Recharts、React Query、Zod、Vite、TypeScript、pnpm / Turborepo |
| 桌面 | Tauri |
| 后端 | Rust、SQLite、Diesel；Web 模式为 Axum HTTP server |
| 扩展 | `@wealthfolio/addon-sdk`、addon-dev-tools、共享 UI 包 |

### 与「个人投资知识库 / 投资操作系统」的关系

属于 **持仓与净值应用（记账 / 可视化侧）**：把「现在有什么、表现如何、目标进度」做清楚。不内置价值投资方法论或多 Agent 决策纪律。Addon 可扩展功能，但仍是应用层扩展，不是知识库审查流水线。

### 与 PIOS 的可比点

| 相似 | 差异 |
|------|------|
| 关心组合、目标、多币种、隐私 | Wealthfolio 是可安装产品；PIOS 是 Markdown + Agent 规则仓库 |
| 可自托管 / 本地数据 | PIOS 几乎无 GUI；持仓在 `database/` 文本骨架中维护 |
| 可扩展（Addon） | PIOS 扩展靠 Skill / workflow / templates |

**可借鉴**：活动类型模型、绩效指标口径、本地优先与密钥（OS keyring）做法。  
**分工建议**：若需要日常净值仪表盘，可用 Wealthfolio 做「事实层」；买卖与配置结论仍走 PIOS Pipeline。

### 关键信息（截至 2026-07-27）

| 项 | 值 |
|----|-----|
| Stars | 8,396（约 8.4k） |
| Forks | 602 |
| License | AGPL-3.0（品牌商标另见 `TRADEMARKS.md`） |
| 主语言 | Rust |
| 活跃度 | 活跃（`pushed_at` 2026-07-25）；Issues 较多属正常社区规模 |
| 官网 | https://wealthfolio.app |

### 来源链接

- 仓库：https://github.com/wealthfolio/wealthfolio  
- 官网：https://wealthfolio.app  
- Docker Hub：`wealthfolio/wealthfolio`

---

## 4. Ghostfolio

### 定位与一句话摘要

开源 **财富管理（Wealth Management）** 软件：用 Web 技术持续记录股票、ETF、加密货币等，并据此做数据驱动的组合洞察。面向个人长期自用，可自托管或使用官方 Premium 云服务。

### 核心功能 / 架构特点

- 交易 CRUD、多账户管理。
- 组合绩效：ROAI（Return on Average Investment）覆盖 Today / WTD / MTD / YTD / 1Y / 5Y / Max 等窗口。
- 图表、静态风险分析、导入导出、Dark Mode、Zen Mode。
- **PWA**、移动优先。
- 官方 **Ghostfolio Premium** 云托管（覆盖数据商与运维成本）；自托管走 Docker Compose（PostgreSQL + Redis）。
- 实验性 Public API（Bearer / 匿名 access token）、活动导入、公开组合只读等。
- 家用 NAS / Home Assistant / Umbrel 等社区集成（README 还列 CasaOS、Runtipi、TrueCharts、Unraid 等）。

### 技术栈

| 层 | 技术 |
|----|------|
| 组织 | Nx monorepo、TypeScript |
| 后端 | NestJS、PostgreSQL、Prisma、Redis |
| 前端 | Angular、Angular Material、Bootstrap utility classes |
| 部署 | Docker（linux/amd64、arm/v7、arm64） |

### 与「个人投资知识库 / 投资操作系统」的关系

属于 **自托管组合仪表盘 / 财富管理应用**：强在持续运营、多平台聚合视图与隐私自主；弱在「为什么买、为何现在、反方挑战、决策日志」等知识与纪律层。与 Wealthfolio 同属「事实与绩效」赛道：Ghostfolio 更偏 **Web + Postgres 长期服务**，Wealthfolio 更偏 **桌面 local-first + Tauri**（同时提供 Docker / Web）。

### 与 PIOS 的可比点

| 相似 | 差异 |
|------|------|
| 重视隐私与数据所有权、买 / 持有心态友好 | Ghostfolio 是完整 Web 应用；PIOS 是决策与知识操作系统 |
| 组合构成洞察、风险提示 | 无 PIOS 式 Research→Decision 流水线与 citation / as_of 规则 |
| 可 API 导入活动 | PIOS 侧数据多为 Markdown / 表结构，需人工或脚本桥接 |

**可借鉴**：绩效时间窗口定义、活动导入 schema、自托管运维经验。  
**分工建议**：Ghostfolio / Wealthfolio 二选一（或按桌面 vs 服务器偏好）作持仓真源；PIOS 负责研究、模型、风险、反方与决策落盘。

### 关键信息（截至 2026-07-27）

| 项 | 值 |
|----|-----|
| Stars | 9,014（约 9.0k） |
| Forks | 1,245（约 1.2k） |
| License | AGPL-3.0 |
| 主语言 | TypeScript |
| 活跃度 | 活跃（`pushed_at` 2026-07-26）；项目始于 2021-04 |
| 官网 | https://Ghostfol.io |

### 来源链接

- 仓库：https://github.com/ghostfolio/ghostfolio  
- 官网 / Demo / Premium：https://Ghostfol.io  
- 社区话题：https://github.com/topics/ghostfolio

---

## 对照速查

```text
                    知识/规则层              研究对抗层              持仓事实层
PIOS                ████████               ███████                ██（骨架）
AI Berkshire        ████                   ████████               ██
Austin LLM Wiki     ████████               ██                     ██（output 隐私）
Wealthfolio         █                      █                      ████████
Ghostfolio          █                      █                      ████████
```

对 PIOS 最有直接方法论价值的，是 Austin 文（三层知识 + 隐私）与 AI Berkshire（Skill 纪律与对抗）。  
对 PIOS 最有互补产品价值的，是 Wealthfolio / Ghostfolio（净值与活动真源，不替代 Decision）。

---

## 免责与维护

- 外部项目的实盘收益、星标数与功能以各自仓库为准；本文件仅作对照索引。
- Stars / `pushed_at` 会过时；重要决策前请重新打开源链接核对。
- 更新本文件时：改「调研日期」、必要时重拉 GitHub API，并保留原文链接。
