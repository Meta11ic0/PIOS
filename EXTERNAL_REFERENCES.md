# 外部参考分析

> 分析日期：2026-08-03
> 分析依据：
> - Austin Xu — [rwh-overlay Lessons (Part 2)](https://austinxyz.github.io/blogs/blog/2026/05/04/rwh-overlay-lessons) / [Investment OS (Part 3)](https://austinxyz.github.io/blogs/blog/2026/05/04/investment-os)
> - [ai-berkshire](https://github.com/xbtlin/ai-berkshire) — AI 时代价值投资研究框架（14,901 stars）
> - [wealthfolio](https://github.com/wealthfolio/) — 本地优先的开源组合追踪桌面应用
> - [ghostfolio](https://github.com/ghostfolio/ghostfolio) — 开源财富管理 Web 应用

---

## 一、ai-berkshire — 主要参考

ai-berkshire 是目前与本项目最接近的参考，两者都是 **Claude Code + 文件驱动 + 多视角审查** 的投资决策辅助系统。其 2024 年实盘收益 +69.29%，2025 年 +66.38%，证明了 AI 辅助投资决策的可行性。

### 1.1 架构总览

```
Skill 层（20 个入口命令）
  ↓ 调度
Agent 层（Team Lead → 4 个并行 Agent，各代表一位投资大师）
  ↓ 调用
Tool 层（Python 精确计算、MCP 实时检索、报告抽检）
```

- **Skill 层**：20 个 `/` 命令，分为深度研究、财报分析、行业筛选、持仓管理、思维工具五类
- **Agent 层**：`/investment-team` 等命令启动 Team Lead，并行调度 4 个 Agent（巴菲特/芒格/段永平/李录视角），各自独立搜索、独立判断，最后合议
- **Tool 层**：`tools/financial_rigor.py` 用 `decimal.Decimal` 做精确计算（禁止 LLM 心算），`skills/financial-data.md` 强制双来源交叉验证

### 1.2 与 PIOS 的架构对照

| 维度 | ai-berkshire | PIOS |
|------|-------------|------|
| 入口方式 | 20 个独立 `/` 命令，按场景选用 | workflow 文件定义场景入口 → 统一进入 8 步 Pipeline |
| 审查方式 | `/investment-team` 并行 4 Agent 对抗 | Committee 四席制（配置/暴露/实施/风险反方），编排第 3–6 步 |
| 输出产物 | 一份完整投研报告（15 节 thesis） | DD 记录 + Decision Log + 可选持仓更新 |
| 数据层 | `data/` + `reports/` 简单分层 | `database/` 结构化数据 + `knowledge/` + `raw_material/` 三级 |
| 决策记录 | 报告即记录，无独立 Decision Log | 独立的 Decision Log（四结论、冻结证据、触发器、复核） |
| 模型选择 | 用户按风险自选（"关键判断用最强模型"） | Agent 跟随会话模型 |
| 授权 | 无授权体系，建议用 `--dangerously-skip-permissions` | 已决定移除授权体系 |

### 1.3 可借鉴的设计

**A. 反偏见机制 → 强化 PIOS 的 Challenge 阶段**

ai-berkshire 有五层防骗机制：

| 机制 | ai-berkshire 实现 | PIOS 对应 |
|------|------------------|----------|
| 信息丰富度评级 | A/B/C 三级，限制低信息输入的结论强度 | 无直接对应，Validation 的 `unknown` 部分覆盖 |
| 逆向检验 | 芒格视角强制思考"什么情况下会死" | Challenge 阶段的反例+失败情景表 |
| 快速否决清单 | 8 条红线一票否决 | Decision 五条硬门禁 |
| 反共识检查 | "聪明人为什么在做空？" | Challenge 的替代方案视角 |
| 留白原则 | 数据不足时标注灰色地带，不用推测伪装确定性 | Research 的"查不到如实记录" |

PIOS 的 Challenge 阶段已经覆盖了逆向检验和反共识，但**信息丰富度评级**和**快速否决清单**两个机制可以增强。

**B. 精确计算层**

ai-berkshire 严禁 LLM 做数学——PE、市值、DCF 全部走 Python `decimal.Decimal`，偏差 >1% 告警。PIOS 目前没有这一层。这是一个重要的设计缺口：LLM 在计算方面不可靠，需要脚本工具兜底。这个缺口应记入 PROJECT.md。

**C. 技能分类体系**

ai-berkshire 将 20 个 skill 按用户意图分类（深度研究/财报分析/行业筛选/持仓管理/思维工具），而不是按 pipeline 阶段分类。PIOS 的 skill 严格按 pipeline 阶段分类（research / validation / modeling / ...）。两种分类互补——PIOS 的阶段分类适合"有纪律的 DD"，ai-berkshire 的意图分类适合"按需使用"。PIOS 可以保留阶段分类，同时在 workflow 层做意图映射。

**D. 无授权运行**

ai-berkshire 官方建议 `claude --dangerously-skip-permissions` 模式运行，没有任何 read-auth / write-auth / checklist-auth 体系。这验证了 PIOS 移除授权体系的决定——同一个生态、同类型项目、有实盘业绩验证。

---

## 二、Austin IOS — 辅助参考

Austin Xu 的两篇文章描述了他从 LLM Wiki 到 Investment Agent 再到 Investment Operating System 的演进。他的 IOS 是一个六阶段框架，与 PIOS 的八阶段 Pipeline 形成互补。

### 2.1 六阶段框架

```
Idea Generation → Fundamental Research → Technical Confirmation
  → Portfolio Allocation → Execution → Position Management
```

| Austin IOS | PIOS | 关系 |
|-----------|------|------|
| Stage 1: Idea Generation | 无 | PIOS 不生成投资想法，起点是用户提出的问题 |
| Stage 2: Fundamental Research | Research + Validation + Modeling | PIOS 将"基础研究"拆成三步：取证→核验→可比化 |
| Stage 3: Technical Confirmation | 无 | PIOS 不覆盖技术分析/趋势确认 |
| Stage 4: Portfolio Allocation | Reasoning（部分覆盖） | Austin 侧重税务与账户选择，PIOS 侧重目标与约束 |
| Stage 5: Execution | Decision（act 后核对清单） | Austin 有具体的入场/止损/目标价分析，PIOS 只提供价格区间绑定 |
| Stage 6: Position Management | Decision Log 触发器 | PIOS 有失效条件和复核触发器，但无系统化监控 |

### 2.2 核心设计原则

Austin 总结了五条教训，PIOS 直接借鉴其中三条：

**A. "脚本取数据，LLM 做解释"**

这是 Austin 最重要的设计原则，与 ai-berkshire 的精确计算层一致。LLM 擅长推理，不擅长回忆事实和计算。PIOS 目前完全依赖 LLM 获取和计算数据。**需要引入工具层来取数据**——先记入 PROJECT.md 作为已知缺口。

**B. "目录边界是承重墙"**

`raw_material/`、`knowledge/`、`reports/`、`decision_log/` 之间的边界一旦模糊，整个系统的可追溯性就崩塌。PIOS 已在 ARCHITECTURE.md §3.5.1 定义了数据生命周期和信任阶梯，需要保持这一纪律。

**C. "从痛点建工作流"**

不要先设计完整的 AI 投资顾问再逆向推导功能。从具体痛点（"QDII 限购了，我能买什么"）出发建 workflow，再逐步泛化。PIOS 当前的 workflow（buy_etf、sell_etf、dca 等）符合这一原则。

### 2.3 PIOS 无需借鉴的部分

- **14 个自定义命令**：Austin 的命令体系针对美股个股，PIOS 的 focus 是中国大陆证券账户可交易的场内跨境 ETF。场景不同，不需要照搬。
- **Obsidian 集成**：PIOS 以 Claude Code 终端为主要交互界面，Obsidian 作为阅读工具是锦上添花，不是必需。
- **Quartz 发布**：PIOS 按公开仓库设计，暂无 Quartz 静态站点发布需求。
- **隐私分层（公开/私有目录）**：个人持仓与生产 reports/decision_log 通过 `.gitignore` 隔离，不入库；规则正文与 demo 可入库。

---

## 三、wealthfolio & ghostfolio — 应用层参考

这两个项目是传统投资组合管理应用，不涉及 AI 决策。它们的参考价值在于**数据模型**和**设计哲学**。

### 3.1 wealthfolio

- **定位**：本地优先的桌面应用（Rust/Tauri），隐私至上
- **核心理念**：数据在本地，不经过任何服务器。这与 PIOS 的"文件即数据库"理念一致
- **功能范围**：持仓追踪、业绩分析、目标规划、Addon 扩展系统
- **对 PIOS 的参考价值**：Addon 扩展系统——允许社区贡献功能模块。PIOS 可以借鉴这个思路，将 workflow 和 skill 设计为可插拔模块
- **关键区别**：wealthfolio 是"记录你做了什么"（事后），PIOS 是"帮你审查该不该做"（事前）。两者解决的问题不同

### 3.2 ghostfolio

- **定位**：开源财富管理 Web 应用（Angular/NestJS/Prisma/PostgreSQL），支持自托管
- **核心理念**：为忙碌的人提供数据驱动的投资决策支持
- **功能范围**：多账户管理、交易记录、组合业绩（ROAI）、风险分析、导入导出
- **对 PIOS 的参考价值**：
  - 数据模型设计——账户、交易、持仓之间的关系建模
  - 组合分析维度——集中度、货币暴露、板块分布等 PIOS 可以纳入 Reasoning 阶段的检查项
- **关键区别**：ghostfolio 是完整应用（需要部署、数据库、前端），PIOS 是零运维的文件驱动系统。PIOS 不需要成为应用

---

## 四、PIOS 的差异化定位

### 4.1 各项目的定位光谱

```
"发现机会"                           "验证决策"                        "追踪结果"
ai-berkshire                          PIOS                      wealthfolio/ghostfolio
多视角投研 + 生成 thesis              八步 DD + 门禁 + 留痕           持仓追踪 + 业绩分析
```

PIOS 填补的是**决策纪律**这个空白——不是帮你发现买什么，也不是帮你记录买了什么，而是在"有想法"和"执行"之间加一层结构化的 DD。

### 4.2 PIOS 的独特优势（相比三个外部参考）

| 优势 | 说明 | 外部参考状况 |
|------|------|------------|
| **8 阶段 DD Pipeline** | 从取证到留痕的完整阶段契约，每步有明确的放行/阻断条件 | ai-berkshire 无阶段化 pipeline，Austin IOS 有 6 阶段但侧重执行 |
| **Committee 四席对抗** | 配置/暴露/实施/风险反方四个视角的正式对抗审查 | ai-berkshire 有四大师分析但侧重研究，不是决策门禁 |
| **Decision Log 体系** | 四结论 + 冻结证据 + 触发器 + 追溯链 | 三个参考项目均无独立的决策记录层 |
| **Data Contracts** | 数据时效分类 + 最大年龄 + 超期阻断 | 三个参考项目均无数据时效管理体系 |
| **结构化数据库** | `database/` 下的产品筛选、组合数据、来源登记 | ai-berkshire 有 `data/` 但非结构化 |
| **纯文件驱动** | 零数据库、零部署、Git 即后端 | ghostfolio 需要 PostgreSQL + Redis，wealthfolio 需要桌面应用安装 |

### 4.3 从外部参考确认的设计缺口

| 缺口 | 来源 | 处理 |
|------|------|------|
| 精确计算工具层 | ai-berkshire + Austin | 记入 PROJECT.md，后续引入 |
| 信息丰富度评级 | ai-berkshire | 考虑增强 Validation 阶段 |
| Execution/Position Management 阶段 | Austin IOS | 记入 PROJECT.md，未来扩展 |
| 运维型 Skill（知识同步/提取/保鲜） | Austin | 记入 PROJECT.md，后续评估 |
| 无授权运行 | ai-berkshire | 已决定采纳 |

---

## 五、参考项目链接

| 项目 | 链接 | 关注点 |
|------|------|--------|
| ai-berkshire | https://github.com/xbtlin/ai-berkshire | 主要参考：Skill/Agent/Tool 架构、反偏见机制、精确计算层 |
| Austin — rwh-overlay Lessons | https://austinxyz.github.io/blogs/blog/2026/05/04/rwh-overlay-lessons | 设计原则：脚本取数据/LLM 做解释、目录边界、从痛点出发 |
| Austin — Investment OS | https://austinxyz.github.io/blogs/blog/2026/05/04/investment-os | 六阶段框架、命令体系、金融技能集成 |
| wealthfolio | https://github.com/wealthfolio/ | 本地优先哲学、Addon 扩展模式 |
| ghostfolio | https://github.com/ghostfolio/ghostfolio | 数据模型参考、组合分析维度 |
