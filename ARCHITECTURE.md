# PIOS 架构说明

这份文档解释**当前结构为什么长成这样**、各模块职责，以及和真实个人投资工作的差距。

| 本文是 | 本文不是 |
|---|---|
| 设计地图与因果说明 | 日常逐步操作手册（那是 [OPERATIONS.md](OPERATIONS.md)） |
| 帮助人理解取舍 | **可执行规则的权威源** |

**权威源：** 强制规则与能力正文只在 [`prompts/`](prompts/) 与 [`skills/`](skills/)；场景步骤在 [`workflow/`](workflow/)。读完 README / AGENTS / OPERATIONS / 本文，**不等于**读完全部规则。涉及投资行动时，执行顺序以 [prompts/review_pipeline.md](prompts/review_pipeline.md) 为准（本文只解释「为何八步、为何此顺序」）。

---

## 1. 项目为何存在

最初触发点：场外 QDII 联接基金限额后，原定投无法按预期执行，需要研究 A 股账户可交易的跨境 ETF。

继续做下去后，真正难的是：

- 产品代码很多，单靠记忆凑不出完整候选集。
- 规模、成交额、费率、跟踪能力、折溢价的更新时点不同。
- 同一产品在不同时点可能得出不同结论。
- 「工具好不好」回答不了「当前组合要不要它」。
- 临时推荐很快过期，也无法解释一年后为何曾买入。

于是项目从 ETF 清单扩成个人投资决策系统：除了产品资料，还要留下研究方法、数据口径、组合约束、风险审查、决策理由和复盘结果。

## 2. 设计目标与当前边界

当前设计追求五件事：

1. **证据可追溯**：动态事实带来源、口径和 `as_of`。
2. **过程可重复**：相似问题沿用相同工作流。
3. **推理可检查**：事实、假设、推理和结论分开。
4. **决策可复盘**：保存当时可见信息和失效条件。
5. **结构可扩展**：以后加其他资产时，不必重写整套方法。

现阶段边界：

- 研究重点是中国大陆投资者可交易的纳斯达克 100、标普 500 场内 ETF。
- 数据、模型和组合由人工维护；Agent 辅助整理和审查。
- 没有行情 API、券商导入、定时任务、自动提醒或自动交易。
- 股票工作流仍是占位；债券、REIT、黄金等只预留扩展位置。
- 税务与监管由 Risk 阶段检查，尚无独立完整的税务规则库。

**就绪：** 多数数据仍是骨架。「可跑流程」与「可作真实决策」的区分见 [OPERATIONS.md 文首](OPERATIONS.md#就绪条件可跑流程--可作真实决策) 与 [§14](OPERATIONS.md#14-当前初始化顺序)。

## 3. 架构总览

```text
工具入口
├── AGENTS.md              # 跨工具入口地图（非规则正文）
├── CLAUDE.md              # Claude Code 入口
└── .cursor/               # Cursor 引用 / 发现层
       ↓
规则与能力正文（唯一权威源）
├── prompts/
└── skills/
       ↓
研究与审查
├── raw_material/
├── workflow/
└── templates/
       ↓
事实与知识
├── database/
└── knowledge/
       ↓
阶段结果与历史记录
├── reports/
└── decision_log/
```

### 3.1 加载与治理层

这一层决定 Agent **应该**读到哪些规则（靠约定与开场清单，不是运行时强制；见 [AGENTS.md 开场话术](AGENTS.md#开场强制列出话术可复制)）。

- [AGENTS.md](AGENTS.md)：跨工具入口地图与索引。
- [CLAUDE.md](CLAUDE.md)：Claude Code 入口，只指向 `AGENTS.md`。
- `.cursor/rules/`、`.cursor/skills/`：Cursor 的发现与引用层。
- [prompts/](prompts/)、[skills/](skills/)：可执行规则与能力正文。

入口和引用层都不复制完整正文，避免同一条规则改两处后漂移。

### 3.2 研究与数据层

- `raw_material/`：待蒸馏、可定位且允许留存的原始研究材料；先关联 `source_id`，未经 Validation 不得作为事实或结论。
- `knowledge/`：稳定概念、机制和术语。
- `database/`：产品、指数、市场、观察池、模型、持仓和目标配置。
- `workflow/`：研究、买入、卖出、定投、组合复核和再平衡步骤。
- `templates/`：研究笔记、审查记录、产品报告、投资报告和决策日志格式。

### 3.3 结果与历史层

- `reports/`：阶段性研究和组合复核结果（可随新数据重生）。
- `decision_log/`：一次决策当时的证据、理由、边界、失效条件和复盘（事后结果追加，不回头改写原始理由）。

## 4. 四个 Prompt 为什么分开

拆开后每份可独立改、独立审。

### 4.1 `system.md`：目标和边界

路径：[prompts/system.md](prompts/system.md)

回答：服务什么目标；知识 → 工作流 → 数据 → 决策 → 建议的优先顺序；哪些行为必须避免；何时必须进 Review Pipeline。

`Knowledge → Workflow → Database → Decision → Recommendation` 用来约束回答重心：先确认已有知识和流程，再看数据能否支撑决策，最后才形成建议。

这份保持短：产品字段、评分方法和具体操作会变；塞进系统级规则会让每次会话背无关内容，也更容易维护冲突。

### 4.2 `output_style.md`：输出如何组织

路径：[prompts/output_style.md](prompts/output_style.md)

管语言、信息标注和投资结论的基本结构：结论先出现；已验证 / 待验证 / 假设 / 判断分开；比较统一日期单位口径；结论带原因、替代方案和失效条件。

它只管表达，不决定来源可信度，也不替代审查。

### 4.3 `citation.md`：证据如何选择和记录

路径：[prompts/citation.md](prompts/citation.md)

投资数据常有口径和时点问题。同名指标可能来自盘中、收盘、季报或第三方估算，直接并排会出错。

它规定：官方与一手优先；关键动态尽量交叉验证；数值记单位、口径、`as_of`、来源和采集时间；冲突要保留并解释；历史不能冒充当前。

证据标准在这里；具体怎么查一批数据，由 Validation Skill 执行。

### 4.4 `review_pipeline.md`：投资行动如何过审

路径：[prompts/review_pipeline.md](prompts/review_pipeline.md)

**八步顺序与全局停止条件的权威源。** 只在买入、卖出、持有、定投、调仓或产品排序等行动场景强制。Pipeline 只定义先后；每步怎么做在对应 Skill。深度分级（完整 / 轻量）见 [OPERATIONS.md §1.1](OPERATIONS.md#11-审查深度分级)。

## 5. 为何是八步、为何此顺序

顺序一句话：`Research → Validation → Modeling → Reasoning → Risk → Challenge → Decision → Documentation`（细则只维护在 [review_pipeline.md](prompts/review_pipeline.md)）。

对应信息链：事实 → 质检 → 比较 → 推理 → 风险 → 证伪 → 决策 → 留档。

### 5.1 每步主要挡什么错

| 阶段 | 主要阻断的问题 |
|---|---|
| Research | 没有事实、来源不明、关键字段遗漏 |
| Validation | 代码错配、时点混用、单位不一、猜测填空 |
| Modeling | 跨类比较、主观打分、总分掩盖否决项、虚假精确 |
| Reasoning | 先挑产品后看目标、忽略当前组合、用宏观叙事代替证据 |
| Risk | 只看收益、忽略最坏情景、操作与行为风险无人检查 |
| Challenge | 确认偏误、替代方案缺失、错误假设没有暴露 |
| Decision | 输入不完整仍行动、金额和条件含糊、没有失效条件 |
| Documentation | 无法复盘、事后改写理由、规则改动没有历史 |

八步不等于每次写同样长的报告。例行小额定投可走轻量路径；Challenge 在两种路径下仍按 Skill 全文（见 OPERATIONS §1.1）。

### 5.2 关键顺序决策（设计理由）

- **Validation 在 Modeling 前**：模型只能吃已确认口径的数据；算得越细，错口径时越误导。Validation `fail` 应停止。
- **Modeling 与 Reasoning 分开**：前者比同类差异；后者把结果放回个人目标、现金流与组合。「第一名」仍可能不适合当前仓位。
- **Risk 在 Challenge 前**：先摊开坏情景暴露，再站到结论对面证伪框架本身。
- **Documentation 在流程内**：没有当时快照与失效条件，未来只能凭盈亏评价，无法判断过程是否合理。

## 6. 八个 Skill 的职责（设计视角）

Skill 负责「怎么做」，Prompt 负责「何时必须做、遵守什么原则」。**执行细则以各 `skills/*/SKILL.md` 为准**；下面只记职责边界与为何独立。

| Skill | 一句话 | 为何独立 |
|---|---|---|
| [Research](skills/research/SKILL.md) | 证据采集：拆问题、按来源优先级收事实 | 采集与质检分开，缺失值更容易保留，少「顺手补一个合理值」 |
| [Validation](skills/validation/SKILL.md) | 证据质检：代码、时点、单位、公式；`pass/warning/fail/unknown` | Citation 定标准，Validation 查这批数据 |
| [Modeling](skills/modeling/SKILL.md) | 透明比较：硬门槛与加权分开；当前 ETF 模型为 draft | 复算不承担个人目标的最终解释 |
| [Reasoning](skills/reasoning/SKILL.md) | 目标→约束→组合→资产/指数→产品→候选行动 | 防止产品分析脱离组合；与 Challenge 有意部分重叠以减确认偏误 |
| [Risk](skills/risk/SKILL.md) | 损失暴露：组合/市场/产品/跨境/法规税务/操作/行为 | `Critical` 或关键不可评估则停 |
| [Challenge](skills/challenge/SKILL.md) | 对抗性证伪：3 反例 + 3 替代 + 3 可能错误 | 刻意反向，不继续完善原结论 |
| [Decision](skills/decision/SKILL.md) | 承诺点：`act/wait/reject/research` 与边界 | 综合前置阶段，形成可执行、可失效的结论 |
| [Documentation](skills/documentation/SKILL.md) | 分流到 knowledge/database/workflow/reports/decision_log | 更新频率不同，避免动态值污染稳定知识、事后结果覆盖当时理由 |

`Committee` 是可选编排辅助，而非第九个 Pipeline Skill：在新资产暴露、首次买入、改目标、重大再平衡或 ETF 排序时，它将 Modeling、Reasoning、Risk 与 Challenge 组织为“目标配置、组合暴露、ETF 实施、风险反方”四席对抗。它不替代任何原阶段，也不以投票覆盖 Validation、IPS 硬约束或 Risk 停止条件。

## 7. 工作流、Skill 和模板的关系

| 类型 | 回答的问题 | 示例 |
|---|---|---|
| Prompt | 全局必须遵守什么 | 证据优先、必须走 Pipeline |
| Skill | 某项能力怎么执行 | 如何验证数据、如何做 Challenge |
| Workflow | 某个场景按什么顺序操作 | ETF 买入、组合复核 |
| Template | 本次结果按什么格式保存 | review、decision_log |

以 ETF 买入为例：[buy_etf.md](workflow/buy_etf.md) 先查目标与候选 → 工作流内按 Pipeline 调 Skill → [review.md](templates/review.md) 记过程 → 需要时用投资报告模板整理 → [decision_log.md](templates/decision_log.md) 留决策与复盘。卖出 / 定投见 [sell_etf.md](workflow/sell_etf.md)、[dca.md](workflow/dca.md)；深度见 OPERATIONS §1.1。

## 8. 数据为什么这样分层

[database/README.md](database/README.md) 将数据分为：目录（少变 ID）、基础（按公告变）、动态（必须 `as_of`，追加历史）、评价（模型版本 + 输入快照）。

避免两类问题：用旧动态值覆盖稳定基础信息；改模型后无法还原过去的评分。

[产品 Schema](database/products/schema.yaml) 提供通用字段；动态历史在 [products/history/](database/products/history/)，评价运行在 [screening/runs/](database/screening/runs/)。

## 9. 持仓与决策为何分开

[holdings.csv](database/portfolio/holdings.csv) 保存事实快照（账户别名、产品、数量成本市值、时点与核验状态）。`decision_log/` 保存一次决策的目标、证据、取舍、风险、Challenge、失效条件与复盘。

分开后：没成交也可留 `wait` / `reject` / `research`；成交时持仓表只反映结果，不承担解释。

## 10. Claude Code 与 Cursor 如何共用

### Claude Code

1. 从 [CLAUDE.md](CLAUDE.md) 进入。
2. 读 [AGENTS.md](AGENTS.md)。
3. 按要求读四份 `prompts/*.md`。
4. 任务相关时读对应 `skills/<name>/SKILL.md`。

### Cursor

1. `.cursor/rules/*.mdc`（`alwaysApply: true`）注入引用层，指向 `AGENTS.md` 与 `prompts/`。
2. **Skill 发现走 `.cursor/skills/<name>/SKILL.md`**（frontmatter + 指向根正文的链接）。
3. **可执行正文只维护在根目录 `skills/`**；Rules / AGENTS 要求 Agent **Read 根正文**，引用层不复制全文。

根目录 `skills/` 是跨工具共用正文，**不是**给 Cursor 再扫一遍的第二套发现入口。若 Agent 只「看见」了 `.cursor/skills/` 的短引用而未 Read 根 `SKILL.md`，属于未按规则加载，应用开场清单补救。

### Codex 与其他工具

识别 `AGENTS.md` 的直接读它，再加载 `prompts/` 与相关 `skills/`。不识别的，须在请求里显式要求。

### 已知代价

- 引用层要求 Agent 真的执行 Read；链接本身不会加载正文。
- **没有运行时强制**，也没有自动测试拦住跳过 Skill（见 OPERATIONS §16）。
- 未识别 `AGENTS.md` 的工具，需要在请求中点名读取。

## 11. 当前设计的优势

1. **证据与结论之间有门禁**：Validation、Risk、Challenge 都能拦住往前冲。
2. **数据与决策可追溯**：动态字段带日期来源，模型带版本，决策带当时证据。
3. **个人组合在核心**：Reasoning / Decision 要求读持仓与目标；产品排名只是输入。
4. **能力可复用**：八个 Skill 未绑死单一 ETF；补领域知识与字段后可扩资产。
5. **正文只维护一份**：Claude Code 与 Cursor 共用 `prompts/` 和 `skills/`。

## 12. 不符合实际或仍未完成的地方

1. **数据时效靠人工**：无自动过期判断与定时更新。
2. **产品库和模型仍是骨架**：观察池多为 `pending` 种子；模型无确认阈值/权重，不能直接评分。
3. **组合数据可能未填**：无真实持仓/目标时，Reasoning、Risk、复核、再平衡只能做结构演示（见 OPERATIONS 就绪条件）。
4. **交易未接系统**：下单、成交确认、分红、公司行动、对账靠人工；无逐笔流水与税务成本分层。
5. **税务与监管较薄**：Risk 只提供检查入口。
6. **八步对例行操作偏重**：轻量路径见 OPERATIONS §1.1；若仍过重，再据真实案例决定是否收缩 Challenge 数量。

## 13. 是否存在冗余

### 13.1 Pipeline 在多处出现

入口文档、README、模板都会**提到**八步。处理原则（已收敛写法）：

- **执行规范源**：[prompts/review_pipeline.md](prompts/review_pipeline.md)
- **入口索引**：[AGENTS.md](AGENTS.md)（阶段 ↔ Skill 表，不展开细则）
- **操作手册**：[OPERATIONS.md](OPERATIONS.md)（场景 + 深度分级，一行摘要链到权威源）
- **本文**：只解释原因与顺序取舍
- **模板**：[templates/review.md](templates/review.md) 保存实例

不要在入口三份里再贴完整八步操作细则。

### 13.2 输出结构相似

`output_style.md`、`review.md`、`investment_report.md` 都含数据/风险/Challenge/Decision，但分工不同（约束回复 / 记过程 / 写可读报告）。可保留；若实际只用一种文档，再合并模板。

### 13.3 Reasoning 与 Challenge 有交叉

有意保留的防偏误重叠，也是最可能显得冗长的地方。用真实案例再决定是否收缩。

### 13.4 预留目录较多

多个资产目录目前只有 `.gitkeep`，表达扩展方向。按需填充即可。

## 14. 已停止采用的早期方案

以下不再作为执行规则（演进过程见已隔离归档 `gptrecord.md`；不得引用其中推荐或阈值）：

- HaoETF 作为主要数据源；固定产品代码与永久排序
- Excel / Notion 自动给买入分数；未经验证的硬门槛当永久规则
- 九位专家投资委员会；按 Product/Macro/Strategy/Behavioral 建角色型 Skill
- CGEDS、PIDS 等早期名称；把换工作、买房等通用决策写成「已实现范围」

## 15. 后续演进原则

1. 先用真实案例验证当前流程，再加自动化。
2. 新规则须解决已经出现的问题，不为结构完整硬加文件。
3. 动态数据和模型改动保留版本，不覆盖历史。
4. 扩展资产前先补领域知识、字段、风险规则和工作流。
5. 只有出现逐笔对账、税务批次或自动聚合需求时，再加交易流水。
6. 任何简化都要保留证据、风险、Challenge 和决策记录的核心门禁。
