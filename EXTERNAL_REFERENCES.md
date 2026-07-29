# 外部参考分析：Austin Xu 的 Wealth LLM Wiki 系列

> 分析日期：2026-07-28
> 分析依据：Building Your Personal Finance Knowledge Base with Claude Code (Part 1)、From LLM Wiki to Investment Agent (Part 2)、The Investment Operating System (Part 3)、wealth-llm-wiki GitHub 仓库、Karpathy LLM Wiki Gist

---

## 一、三层知识模型对比

### 1.1 结构对照

| 层级 | Austin wealth-llm-wiki | PIOS 当前 | 差异 |
|------|----------------------|-----------|------|
| L1 - 原始材料 | `raw_material/` — 按分类放调研文章，统一 frontmatter | `raw_material/` — 存在但基本为空（仅 demo），无 frontmatter schema | PIOS 的 raw_material 尚未启用真实内容 |
| L2 - 结构化知识 | `wiki/` — 按 9 个分类组织，统一 frontmatter，Obsidian MOC | `knowledge/` — 按资产类别分 9+ 子目录 | PIOS 是静态 Markdown，Austin 是 Obsidian wiki（带 frontmatter+DQL） |
| L3a - 输出/报告 | `publish/` — 虚构案例分析（公开）<br>`output/` — 个人建议（gitignored） | `reports/` — 决策输出（公开） | Austin 区分公开案例和私有建议，PIOS 的报告就是公开产出 |
| L3b - 决策记录 | 无独立记录层，靠 `/wealth-log` 写投资日志 | `decision_log/` — 结构化决策记录 | PIOS 的 Decision 四结论体系是该层亮点 |
| 数据库 | 无 | `database/` — ETF screening, portfolio 数据 | PIOS 独有的结构化数据层 |

### 1.2 隐私分层策略

Austin 的隐私模型：
- `output/` 被 `.gitignore` 完全拒绝入库
- `wiki/` + `raw_material/` + `publish/` 完全公开，不含个人数据
- 个人数据（持仓金额、成本、账户余额）只能放在 `output/`
- CLAUDE.md 硬性规则阻止 AI 把个人数据写入公开目录
- `.gitignore` 还额外忽略 `.claude/*`、`.obsidian/workspace.json` 等

PIOS 的隐私策略：
- 整个仓库是私有仓库（git 层面）
- 无细粒度的目录级隐私标记
- `.gitignore` 有 `database/products/history/` 等忽略模式

**评估**：PIOS 当前靠"私有仓库"整体控制，缺乏 Austin 的细粒度隔离。这本身不一定是问题——私有仓库已经提供了足够保护。但如果 PIOS 未来有**公开分享**需求（如发布 screening 模型、知识条目），则需要引入 Austin 的目录级隐私策略。

### 1.3 元数据 Schema 对比

Austin 的 raw_material frontmatter:
```yaml
---
title: 文件主题
collected: YYYY-MM-DD
source_url:
  - https://...
freshness: evergreen | annual | volatile
valid_until: YYYY-MM-DD
tax_year: YYYY
---
```

Austin 的 wiki 条目 frontmatter:
```yaml
---
title: 条目名称
category: 账户类型 | 税务策略 | ...
tags: [tag1, tag2]
source: "[[raw_material/...]]"
updated: YYYY-MM-DD
status: draft | stable | outdated
---
```

PIOS 当前无统一的 frontmatter schema。`knowledge/` 下文件无元数据标记。

### 1.4 策略建议

**PIOS 不需要照搬 Austin 的结构**，但可以借鉴以下点：

1. **raw_material 的 freshness 标记** — PIOS 的 raw_material 可以引入 freshness 分级（evergreen / annual / volatile），帮助团队判断引用时效
2. **知识条目的 source 追溯** — knowledge/ 文件如果来自外部调研，建议标注 source URL
3. **status 标记** — 给 knowledge/ 条目加 draft / stable / outdated 状态，防止使用过期知识

---

## 二、Skill 自动化设计

### 2.1 Austin 的 Skill 体系

wealth-llm-wiki 共有 5 个核心 skill：

| Skill | 类型 | 功能 | 适用场景 |
|-------|------|------|---------|
| `/wealth-advise` | 面向用户 | 读取 wiki + output/ 生成个性化建议 | 日常使用 |
| `/wealth-extract` | 维护型 | raw_material → wiki 提炼 | 作者维护 |
| `/wealth-sync` | 维护型 | raw_material 增量回补 wiki | 作者维护 |
| `/wealth-audit` | 质量型 | 四维质量审计 | 质量保证 |
| `/wealth-freshness` | 维护型 | 时效扫描与更新 | 知识保鲜 |

rwh-overlay 另有 14 个投资操作命令（见下文 2.3）。

### 2.2 PIOS 当前 Skill 体系

PIOS 有 10 个 skill，按功能分：
- **决策类**：committee, decision, reasoning, challenge, risk
- **研究类**：research
- **质量类**：validation
- **支持类**：documentation, modeling, humanizer-zh

对比来看，Austin 有**运维型 skill**（extract, sync, freshness），PIOS 则全是**认知/决策型 skill**。

### 2.3 rwh-overlay 的 14 个命令

Austin 在 Part 2 中列出了 rwh-overlay 的全部命令，分为五个职能组：

| 职能 | 命令 | 说明 |
|------|------|------|
| 知识维护 | `/kb-sync` | 合并上游 RWH wiki + 自己的 overlay 分析 |
| 个股研究 | `/stock-analyze`, `/stock-refresh`, `/stock-entry` | 全管线研究 pipeline |
| 日常监控 | `/morning-check`, `/morning-check ALL` | 开盘前决策检查 |
| ETF 分析 | `/etf-analyze`, `/etf-check` | ETF 深度分析及板块 DCA 决策 |
| 信息整合 | `/chen-integrate`, `/chen-validate` | 第三方分析交叉验证 |
| 周期报告 | `/market-daily`, `/market-weekly`, `/market-monthly`, `/market-quarterly` | 定期审计报告 |

这 14 个命令加上 5 个核心 skill，形成了完整的工作流体系。

### 2.4 PIOS 可借鉴的 Skill 类型

**高优先级——引入运维型 Skill：**

1. **`/knowledge-sync`** — 扫描 raw_material/ 中的新文件，与 knowledge/ 对比，输出未处理材料清单（类似 Austin 的 `/wealth-sync`）
2. **`/knowledge-extract`** — 从 raw_material 文章提炼 knowledge/ 条目（类似 `/wealth-extract`）
3. **`/knowledge-audit`** — 检查 knowledge/ 条目的质量、完整性、时效性（类似 `/wealth-audit`）
4. **`/portfolio-refresh`** — 扫描 portfolio 数据库，检查数据完整性，报告异常值（类似 rwh-overlay 的周期命令）

**中优先级——引入投资操作命令：**

1. **`/asset-analyze <TICKER>`** — 全管线研究 pipeline，调用 research → modeling → reasoning → risk → challenge，输出结构化 thesis（对应 PIOS 的 8 阶段 Pipeline）
2. **`/dca-check`** — DCA 条件检查，比对目标配置偏离度、市场环境、限价可用性（对应 workflow/dca.md 的自动化版本）
3. **`/rebalance-scan`** — 扫描当前持仓 vs 目标配置，生成调仓候选列表（对应 workflow/rebalance.md 的自动化版本）

### 2.5 CLAUDE.md 内容结构对比

| 维度 | Austin wealth-llm-wiki | PIOS |
|------|----------------------|------|
| 核心概念 | 三层知识库说明 + 隐私规则 + schema 定义 | 通过 AGENTS.md 引用 prompts/ 和 skills/ |
| Schema | raw_material 和 wiki 条目的 frontmatter 完全定义 | 无统一 frontmatter schema，依赖 data_contracts.md |
| 隐私规则 | 硬性规则（什么不能做）+ git push 前检查 | 依赖仓库级别管理 |
| Skill 矩阵 | 3+ 个 skill 的触发条件速查 | 通过 AGENTS.md 引用，CLAUDE.md 自身轻量 |

**借鉴点**：PIOS 的 CLAUDE.md 设计理念不同——它专注于入口和引用，把规则正文下放到 prompts/ 和 skills/。这是合理的设计，不需要改为 Austin 的模式。但可以在 CLAUDE.md 中增加一个"可执行 skill 速查表"，简化会话启动时的认知负担。

---

## 三、知识增长路径

### 3.1 "先脚手架再填内容"策略

Austin 的构建顺序：
1. 用一条 prompt 初始化目录结构和 CLAUDE.md
2. 创建知识骨架：先建目录和 index.md（MOC 页），列出待建条目
3. 按需填充：有研究需求时才填充具体条目

这与 PIOS 当前策略一致——PIOS 已经建立了目录结构和框架。差异在于：
- PIOS 的 knowledge/ 条目没有统一的 frontmatter 和 MOC 索引
- PIOS 的 raw_material/ 还没有填充真实内容

### 3.2 Obsidian 集成

Austin 将整个仓库作为 Obsidian vault 直接打开，利用：
- Dataview 插件 — 用 DQL 查询 wiki（"列出所有 tagged retirement 的条目"）
- Templater 插件 — 个人 situation 文件的模板
- Graph View — 可视化知识关联
- WikiLink — `[[wiki/分类/文件名]]` 的内部链接

PIOS 的 knowledge/ 是普通 Markdown 文件，不支持 Obsidian 原生浏览。

**思考**：PIOS 是否需要 Obsidian 集成？取决于使用习惯。如果 PIOS 的主要交互发生在 Claude Code 终端，Obsidian 的价值有限。但如果用户希望_阅读_知识条目时获得更好的浏览体验，Obsidian 集成值得考虑——Cost 很低（只是 Markdown 格式兼容），Benefit 很高（免费图谱浏览）。

### 3.3 Quartz 发布策略

Austin 用 Quartz 将 wiki/ 内容发布为静态网站：
- 仅发布 wiki/，output/ 保持私有
- WikiLink 需要完整路径以兼容 Quartz 渲染
- 可选的发布粒度：仅 wiki，或 wiki+raw_material

PIOS 暂无发布需求。如果未来需要公开 knowledge/ 或 screening 模型，Quartz 路线值得参考。

### 3.4 知识保鲜机制

Austin 的 freshness 体系是一个亮点：
- `freshness` 三级分类 (evergreen / annual / volatile)
- `valid_until` 日期标记
- `/wealth-freshness` skill 定时扫描过期条目
- wiki 条目的 freshness 从 raw_material 源头继承（多来源取最不稳定档）

PIOS 当前没有知识保鲜机制。随着 knowledge/ 增长，这个问题会越来越突出。

**借鉴点**：引入一个轻量的 freshness 标记体系（不需要像 Austin 那样严格），定期扫描 outdated 条目。

---

## 四、可借鉴清单（优先级排序）

| 优先级 | 借鉴内容 | PIOS 目标文件/位置 | 具体做法 | 预期收益 |
|--------|----------|-------------------|----------|----------|
| **P0** | raw_material frontmatter schema | `raw_material/README.md` + 示例文件 | 为 raw_material 文件定义统一 frontmatter（title, collected, source_url, freshness），并修改 README 说明 | 建立可追溯、可时效评估的调研材料基线 |
| **P0** | knowledge/ freshness + status 标记 | `knowledge/` 下各个 SKILL.md 或 README | 在知识条目的 frontmatter 或文件头部加 `status: draft\|stable\|outdated` 和 `source` 追溯；建立定期审计机制 | 防止使用过期知识做决策 |
| **P0** | 运维型 Skill：`/knowledge-sync` | `skills/sync/` (新建) 或合并入 research | 扫描 raw_material/ 与 knowledge/ 比对，输出未处理调研材料的表格 | 消除 raw_material 的"入库断层" |
| **P1** | 运维型 Skill：`/knowledge-extract` | `skills/extract/` (新建) | 从 raw_material 提炼 knowledge/ 条目，调用 research → validation | 标准化知识提取流程，减少人工编写负担 |
| **P1** | 运维型 Skill：`/portfolio-refresh` | 整合入现有 skill | 扫描 database/portfolio/ 和 database/products/，检查数据完整性，报告异常 | 保持数据库质量 |
| **P1** | PIOS CLAUDE.md 增加 skill 速查表 | `CLAUDE.md` | 在 CLAUDE.md 末尾加一段 2-3 行的 skill 触发条件速查（类似 Austin 的"配套 Skills 的使用"表格） | 降低会话启动时的认知负担 |
| **P2** | Obsidian 兼容 | `knowledge/` 文件 | 考虑在 knowledge/ 文件头部加 Obsidian 兼容的 YAML frontmatter，可选的 WikiLink 支持 | 零成本获得图谱浏览能力 |
| **P2** | DCA 自动化命令 | `skills/dca/` 或集成到 workflow | 将 `workflow/dca.md` 的检查逻辑封装为 `/dca-check` skill | 把流程文档变成可执行命令 |
| **P3** | Quartz 发布模板 | 独立的发布仓库 | 如果未来需要公开知识条目，参考 Quartz 的部署模式 | 知识产出的发布通道 |
| **P3** | Freshness 定时扫描 | 新的 cron 或定时 skill | 类似 `/wealth-freshness`，定期扫描过期条目并更新 `valid_until` | 知识体系长期自治 |

---

## 五、关键洞察总结

### 5.1 PIOS 的独特优势

与 Austin 的体系相比，PIOS 有以下差异化优势：

1. **结构化数据库** — `database/` 下的 product screening、portfolio 数据、市场信息是 Austin 体系没有的数据层，也是 PIOS 的差异化竞争力
2. **8 阶段 Review Pipeline** — PIOS 有明确的阶段式审查流程，而 Austin 的流程更多是自由形式
3. **Decision 四结论体系** — PIOS 的决策记录（行动、等待、拒绝、失效）比 Austin 的 `/wealth-log` 更结构化
4. **Committee四席制** — PIOS 有正式的挑战/对抗机制，Austin 没有此类设计

### 5.2 Austin 可借鉴的差异化优势

1. **运维型 Skill** — Austin 把"维护知识库本身"作为一种 skill 类型来支持，PIOS 缺乏这类元维护能力
2. **freshness 元数据** — Austin 为每个知识条目标记时效性，PIOS 没有
3. **工作流即产品** — Austin 的 4 个核心工作流（个股研究、日常监控、ETF DCA、定期审查）比 PIOS 的 workflow 更完整
4. **从 pain 出发的设计** — Austin 的每条命令都源于具体痛点，这比"设计完整投资系统再逆向工作"更实际

### 5.3 关于 Part 2 的核心教训

Austin 在 Part 2 中总结了 5 条教训，PIOS 应重视：

1. **与上游解耦** — PIOS 目前没有上游依赖问题，但如果未来引入外部数据源，需要设计清晰的解耦策略
2. **从痛点建工作流** — 不是设计完整的 AI 投资顾问再倒推功能，而是从具体痛点开始
3. **脚本取数据，LLM 做解释** — LLM 擅长推理，不擅长回忆事实。获取数据用脚本，分析数据交给 LLM。这是 PIOS 设计 Command 时的重要原则
4. **目录边界是承重墙** — PIOS 的 database/knowledge/reports/decision_log 已有明确划分，需保持
5. **知道扩展极限** — 当知识条目突破上下文窗口限制时 RAG 不再是选择而是必需

### 5.4 关于 Part 3 的六阶段框架

Austin 的 IOS 六阶段框架（Idea → Research → Technical → Allocation → Execution → Management）与 PIOS 的 8 阶段 Pipeline（Research → Validation → Modeling → Reasoning → Risk → Challenge → Decision → Documentation）有互补性：

- IOS 的阶段 1-2-3 对应 PIOS 的 Research-Modeling-Reasoning
- IOS 的阶段 4-5-6（分配、执行、持仓管理）是 PIOS 未系统覆盖的
- PIOS 的 Validation-Risk-Challenge-Documentation 是 IOS 未系统覆盖的

PIOS 可考虑在工作流层面补充"执行后管理"阶段（Position Management），完成从研究到退出的全生命周期覆盖。

---

## 六、原始资料清单

以下为分析所依据的原始资料：

1. **Part 1** — [Building Your Personal Finance Knowledge Base with Claude Code](https://austinxyz.github.io/blogs/blog/2026/05/04/wealth-llm-wiki)
2. **Part 2** — [From LLM Wiki to Investment Agent: Lessons from Building rwh-overlay](https://austinxyz.github.io/blogs/blog/2026/05/04/rwh-overlay-lessons)
3. **Part 3** — [The Investment Operating System](https://austinxyz.github.io/blogs/blog/2026/05/04/investment-os)
4. **GitHub 仓库** — [austinxyz/wealth-llm-wiki](https://github.com/austinxyz/wealth-llm-wiki)
5. **Karpathy LLM Wiki Gist** — https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f
6. **RWH 仓库** — [kgajjala/rwh](https://github.com/kgajjala/rwh)
7. **finance-skills** — [kgajjala/finance-skills](https://github.com/kgajjala/finance-skills)
