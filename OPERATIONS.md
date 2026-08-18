# PIOS 日常运行手册

这份手册回答日常「什么时候读什么、按什么顺序做、结果写哪」。建议先读完 [README.md](README.md) 和 [ARCHITECTURE.md](ARCHITECTURE.md)，再读本文。

**它不是可执行规则的权威源。** 强制规则以 `prompts/` 与 `skills/` 正文为准。设计原因已在 [ARCHITECTURE.md](ARCHITECTURE.md)，本文只管怎么用。

DD 顺序的权威源：[prompts/diligence.md](prompts/diligence.md)，八步为 Research → … → Documentation。本文只写操作场景与数据落盘，不再展开逐步细则。

---

## 阅读地图

### 本手册怎么读

建议先读完 [README.md](README.md)（是什么）与 [ARCHITECTURE.md](ARCHITECTURE.md)（为什么），再按下面跳读本文。

1. 本页「就绪条件」+ 文内「§14 当前初始化顺序」
2. 文内「§1 当前运行方式」与「§1.1 DD 深度分级」
3. 按你要做的事跳到场景节，见下表
4. 需要字段细节时再读文内「§8 个人持仓」「§9 目标配置」

**不要先通读全文。** 持仓字段、目标配置等长节按需查阅。

### 与助手协作时

1. [AGENTS.md](AGENTS.md) 先判断目的后读该路线下一步。`[building]` 读 `building.md`；`[learning]` 读 `learning.md`；`[diligence]` 读 `diligence.md`。后两路线文内再读 `evidence_standards.md`。
2. 改 README、STATUS、ARCHITECTURE、OPERATIONS、knowledge 等对人说明时读 `prompts/docs_style.md`
3. 涉及投资行动时：本次用到的 `skills/*/SKILL.md`
4. 场景入口：对应 `workflow/*.md` + 本文相关场景节
5. 开场先判断目的并列出本轮将 Read 的文件，见 AGENTS.md「先判断目的」，再动手

工具不会强制校验是否已 Read；跳过只能靠话术约束与事后抽查。见文内「§4 Agent 使用方式」与「§16 当前限制和已知维护成本」。

### 场景跳转

| 你要做的事 | 先读 | 手册节 |
|---|---|---|
| 研究新问题 / 更新事实 | [Research Skill](skills/research/SKILL.md)、[Validation Skill](skills/validation/SKILL.md)、[研究笔记模板](templates/research_note.md) | 文内「§5 场景一：研究一个新问题」 |
| ETF 买入前复核 | [workflow/buy_etf.md](workflow/buy_etf.md) | 文内「§6 场景二：ETF 买入前复核」 |
| 卖出 / 减仓 | [workflow/sell_etf.md](workflow/sell_etf.md) | 深度见「§1.1」；步骤在 workflow |
| 例行定投 | [workflow/dca.md](workflow/dca.md) | 「§1.1」轻量路径 |
| 成交后记账 | — | 文内「§7 场景三：交易后记录」 |
| 组合复核 | [workflow/portfolio_review.md](workflow/portfolio_review.md) | 文内「§10 场景四：组合复核」 |
| 再平衡 | [workflow/rebalance.md](workflow/rebalance.md) | 文内「§11 场景五：再平衡」 |
| 补齐骨架数据 | [database/README.md](database/README.md) | 文内「§14 当前初始化顺序」 |
| 首次搭建系统 | — | 文内「§14」；README → 本文就绪条件 → 初始化顺序 |
| 数据更正 / 修复 | — | 文内「§17 常见错误与恢复」 |
| 修改 Prompt / Skill / 规则 | [prompts/building.md](prompts/building.md)、[PROJECT.md](PROJECT.md) | 文内「§15 维护项目本身」+「§17」验证流程 |

---

## 就绪条件：可跑流程与可作真实决策

| | 可跑流程 | 可作真实决策 |
|---|---|---|
| 含义 | 工作流、模板、八步审查、Skill 齐；可练习写法与门禁 | 持仓与目标已填；IPS（Investment Policy Statement，投资政策）状态为 `active`；关键产品已核验；动态数据有适用时点且未超时效；比较用真实生产输入 |
| 典型产物 | 研究笔记、`research` / `wait` Decision、结构演示 | 可讨论 `act`，但仍须过 Diligence 八步 |
| 当前仓库 | **多数处于此态** | 须按文内「§14 当前初始化顺序」补齐后才进入 |

持仓或目标为空、或 IPS 状态仍为 `draft`：可以继续产品研究，**不能**根据组合得出买入结论。观察池种子在核验前不是产品目录。`reports/demo/`、`decision_log/demo/`、`screening/runs/demo/`不得作为生产输入。

---

## 1. 当前运行方式

PIOS 目前是文件驱动：

1. 你提出研究或投资问题。
2. Agent 读取项目规则和相关 Skill；须实际 Read，见上文协作清单。
3. 一起收集和核验数据。
4. 结果写入 Markdown、CSV 或 YAML。
5. 涉及投资行动时按 [Diligence 八步](prompts/diligence.md) 过审。
6. 成交后更新持仓和决策日志。
7. 定期复核组合，并把新认识积累进知识、数据、模型或工作流。

尚未实现：自动抓取行情、读券商账户、定时提醒、自动评分、自动下单、从成交流水生成持仓。

因此行动前都要核对数据日期。Agent 可以整理和审查，不能假设仓库里的动态数据仍然有效。

### 1.1 DD 深度分级

权威顺序与停止条件见 [diligence.md](prompts/diligence.md)。深度只在这里约定：

| 路径 | 适用 | 要求 |
|---|---|---|
| **完整八步** | 新标的、首次买入、加仓超原计划、卖出、调仓、改目标、产品排序 | 各 Skill 全文；新资产暴露、首次买入、改目标、重大再平衡或 ETF 排序同时加载Committee Skill；Modeling 在 draft 阶段只做字段对比与否决项，不自动评分 |
| **轻量路径** | 已有**有效轻量定投 Decision** 的例行买入，且标的与金额边界未变 | 仍过八个检查点，可写简短；关键动态数据须在最大允许时效内；Challenge 仍按 Skill 全文；Documentation 可追加原 Decision Log |

**有效轻量定投 Decision** 必须同时具备：`valid_until` 或等价到期日、允许的 `product_id` 列表、单笔金额上限、频率上限、失效/复核触发器，且 IPS 与目标配置仍有效。任一缺失、过期、改标的、超原计划金额/频率，或关键动态数据过期 → 回退完整八步。

场景入口：[buy_etf.md](workflow/buy_etf.md)、[sell_etf.md](workflow/sell_etf.md)、[dca.md](workflow/dca.md)、[rebalance.md](workflow/rebalance.md)、[portfolio_review.md](workflow/portfolio_review.md)。

## 2. 文件应该放在哪里

| 内容 | 路径 | 说明 |
|---|---|---|
| 稳定概念、机制和术语 | `knowledge/` | 低频更新，不放当前行情 |
| 待蒸馏的合规原始材料 | `raw_material/` | 关联 `source_id`；未经 Validation 不作为事实或结论 |
| 产品、指数、市场和组合事实 | `database/` | 结构化字段，动态值带适用时点 |
| 可重复执行的步骤 | `workflow/` | 买入、卖出、定投、组合复核和再平衡 |
| 空白格式 | `templates/` | 复制后再填写，模板本身不放实例 |
| 一次研究或阶段复核 | `reports/` | 可以随着新数据生成新版本；演示仅放 `reports/demo/` |
| 一次实际决策 | `decision_log/` | 保存当时理由、失效条件和复盘；演示仅放 `decision_log/demo/` |

三个容易混淆的类型：

- `holdings.csv`：某个时点实际持有什么。
- `reports/`：研究或复核得出了什么。
- `decision_log/`：当时为什么行动、等待、拒绝或继续研究。

## 3. 文件命名建议

仓库没有程序强制命名，建议：

```text
reports/YYYY-MM-DD-topic-type.md
decision_log/YYYY-MM-DD-decision-id.md
```

示例：

```text
reports/2026-07-27-us-index-etf-research.md
reports/2026-09-30-portfolio-review.md
decision_log/2026-07-27-dca.md
```

名称只用于定位。文件中的创建时间、更新时间、适用时点、决定时间等正式时间字段，才是时间依据。

## 4. Agent 使用方式

### 4.1 Claude Code

从项目根目录开始。[CLAUDE.md](CLAUDE.md) 要求先读 [AGENTS.md](AGENTS.md)，再按「先判断目的」读该路线下一步；改文档时另加 `docs_style`。任务相关时再读 Skill。这是约定，不是运行时强制。

可以直接提出：

```text
请按 skills/research/SKILL.md 与 skills/validation/SKILL.md 研究这个问题。
使用 templates/research_note.md 组织结果。
只做研究，不形成投资建议。
```

涉及行动时：

```text
请先读取当前持仓、目标配置和 workflow/buy_etf.md，
再按 prompts/diligence.md 执行 DD；完整或轻量见 OPERATIONS「DD 深度分级」。
关键动态数据无法验证时停止，不要补造结论。
```

开场清单要求见 [AGENTS.md](AGENTS.md)「先判断目的」。

### 4.2 Cursor

Cursor 通过 `.cursor/rules/agents.mdc` 注入，只指向 `AGENTS.md`，不复制正文；通过 `.cursor/skills/` 发现 Skill。可执行正文只在根目录 `prompts/` 和 `skills/`。Agent 须实际 Read 正文；单靠引用链接不会自动展开全文。

若回答没有体现规则，明确要求：

```text
先读取 AGENTS.md、prompts/diligence.md 和本任务涉及的所有 SKILL.md，
列出本次将执行的阶段，再开始研究。持仓/目标不足时标明「cannot act」。
```

### 4.3 Codex 或其他 IDE / Agent

识别 `AGENTS.md` 的工具可以直接读它。其他工具须在请求里手动指定：

```text
先读取 AGENTS.md，并按其中引用的 prompts 和 skills 执行。
开场先列出将读文件与阶段。
```

## 5. 场景一：研究一个新问题

适用于概念研究、指数研究、产品发现、数据更新和规则核查。

### 5.1 开始前

先判断研究是否会直接影响投资行动：

- 只更新知识或事实：Research + Validation 即可。
- 只做字段对照、不形成产品排序或投资建议：可停在 Research、Validation，必要时做 Modeling 字段对比。
- 形成产品排序，或可能影响买入、卖出、持有、定投、调仓：走完 [Diligence 八步](prompts/diligence.md)。

### 5.2 创建研究记录

1. 复制 [研究笔记模板](templates/research_note.md)。
2. 保存到 `reports/`，状态先写 `draft`。
3. 填写：研究问题、决策用途、对象和时间范围、必需字段、来源计划、停止条件。

先列字段再搜索，减少「找到什么就研究什么」。

### 5.3 收集资料

按 [Research Skill](skills/research/SKILL.md) 执行取证，再按 [Validation Skill](skills/validation/SKILL.md) 核验。优先监管机构、交易所、指数公司、基金公司正式资料；需要保留且许可允许的材料先登记 `source_id`，再存入 `raw_material/`。第三方冲突时回到适用时点的一手资料。

ETF 查找顺序是人读提示，执行以 Research Skill 为准：

1. 交易所与基金公司确认名称、代码、跟踪对象和基础字段；
2. 集思录观察成交额、IOPV、折溢价和交易状态；
3. 东方财富核对规模、成立时间、费率和历史资料；
4. 基金公司官网复核申赎、QDII 变化和风险公告。

### 5.4 记录每条动态数据

至少保留：对象与唯一标识、指标名、数值与单位、口径、适用时点、来源名称与 URL、取得时间、状态。状态取 `verified` / `conflicting` / `missing`；未核验可用 `pending`。

缺失值留空并说明原因。旧报告不能推断当前规模、成交额、折溢价或申赎状态。

### 5.5 执行 Validation

按 [Validation Skill](skills/validation/SKILL.md) 检查代码匹配、来源是否支持字段、关键动态双来源、时点、币种单位、盘中/收盘混用、公式复算、是否猜测填空、结论是否越界、关键时效是否过期、`scope` 隔离。失败、关键 `fail` / `unknown`、或未关闭的关键 `warning` 停止，含时效过期与双来源缺失；非关键 `warning` 可继续但须写出限制。

### 5.6 归档

- 可长期复用的解释 → `knowledge/`
- 结构化事实 → `database/`
- 本次过程与结论 → `reports/`
- 流程缺陷才改 → `workflow/`

笔记状态改为 `complete`，或项目约定终态，并写下次复核条件。

## 6. 场景二：ETF 买入前复核

适用范围见 [ETF 买入工作流](workflow/buy_etf.md)。

### 6.1 准备输入

先读：

- [投资政策 IPS](database/portfolio/investment_policy.md)（Investment Policy Statement）。状态须为 `active` 才可讨论买入。
- [当前持仓](database/portfolio/holdings.csv)
- [目标配置](database/portfolio/target_allocation.csv)
- 候选指数和候选产品
- 最新产品数据与公告：交易、基金与底层币种，以及 QDII/申赎状态
- 本次可用资金、用途和应急现金要求

持仓、目标为空或 IPS 状态为 `draft` 时，无法判断组合缺口。可以继续做产品研究，不能根据组合得出买入结论。见文首「就绪条件」。

### 6.2 先看目标和组合

回答：这笔资金解决什么配置问题？目标资产是否仍低于区间？买入后集中度如何？是否影响应急现金或近期支出？目标配置本身是否仍有效？

目标配置若要改，应单独做一次 Decision，不能为给当前买入找理由临时改目标。

### 6.3 确定指数与候选集

先查指数规则、币种、集中度和长期风险，再发现产品。候选集应来自交易所、基金公司或可靠工具，不能只比已经知道的两三只。

[ETF 候选种子](database/watchlist/us_index_etf_candidates.csv) 当前全部是 `pending` / `seed_pending`，观察池非完备。使用前按 [database/README.md](database/README.md) 人工核验；不要根据种子行填造官方名称。

### 6.4 执行 DD

本小节不展开逐步细则。

1. 复制 [DD 模板](templates/dd_record.md)。
2. 按 [Diligence 八步](prompts/diligence.md) 顺序执行；深度见文内「§1.1 DD 深度分级」。
3. 每步操作以对应 [Skill](skills/) 为准；索引见 [AGENTS.md](AGENTS.md)。
4. 操作落盘要点：
   - **Modeling**：当前模型草案，只做字段对比与否决项，不自动输出买入评分；见 [etf_model_v0.1](database/screening/etf_model_v0.1.md)。
   - **Decision**：仅用`act` / `wait` / `reject` / `research`；`act` 须写清金额或比例、频率、执行条件、失效条件、下次复核触发器。
   - **Documentation**：在真正执行前用 [决策日志模板](templates/decision_log.md) 建档；成交后再追加实际结果，不要等成交后才首次创建。

### 6.5 执行前检查

执行前由 Agent 向用户呈现核对清单。Agent 不得下单。清单至少包括：

- 证券代码与市场、买卖方向、委托价格、订单类型、数量
- 佣金与其他费用、当前折溢价与价差及分母口径
- 交易/基金/底层暴露币种、QDII 额度与申赎状态
- 相关市场是否开市与估值时差
- Decision 执行条件是否仍成立

PIOS 不负责下单。实际交易只能由用户在券商系统完成。成交后，用户把本次成交明细告诉 AI（对应哪次 `act`、代码、方向、成交价、数量、成交时间、费用等），Agent 更新持仓与 Decision Log。Agent 不得假装已从券商自动同步。

## 7. 场景三：交易后记录

用户告知成交明细后，Agent 更新两处。

### 7.1 更新持仓快照

在 [holdings.csv](database/portfolio/holdings.csv) **追加**成交后的新状态，不要覆盖旧行。同一持仓继续用相同 `holding_id`，更新适用时点、数量、成本、市值与来源登记。清仓也追加一行，数量/成本/市值按实际口径写 `0`；发现旧记录错误时填写 `supersedes_record_id` 与 `correction_reason`。

### 7.2 追加 Decision Log

打开 Documentation 阶段已创建的 Decision Log，只追加：实际成交时间、产品和数量、实际价格与费用、是否符合原 Decision、偏差原因若有、下一次复核条件。

若最终没有成交，也在同一份日志中记录`wait` / `reject` / `research`。没有行动本身也可能是需要复盘的决策。

## 8. 个人持仓

### 8.1 `holdings.csv`

路径：本地操作文件 [database/portfolio/holdings.csv](database/portfolio/holdings.csv)（不入库）。没有该文件时按 [`prompts/csv_schema.md`](prompts/csv_schema.md) 生成表头。列名以该文件为准。

当前采用追加快照：一次成交或组合更新 → 为受影响的 `holding_id` 追加新行 → 查询时取该 `holding_id` 最新适用时点。

| 字段 | 含义 | 填写规则 |
|---|---|---|
| `record_id` | 本行快照主键 | 每追加一行新 ID；更正时新行填 `supersedes_record_id` |
| `holding_id` | 一项持仓的稳定 ID | 建议由账户别名和产品 ID 组成；首次建立后不变 |
| `valid_at` | 适用时点 | ISO 8601；需要时含时区 |
| `fetched_at` | 取得时间 | 拉取或录入时刻；可留空；有值时用不早于来源 `published_at` |
| `account_alias` | 账户别名 | 例如 `broker_a`；不要填完整账号 |
| `product_id` | 产品稳定 ID | 与产品库一致，例如 `CN:<ticker>` |
| `asset_type` | 资产类型 | 使用产品 Schema 中的枚举 |
| `trade_currency` | 交易币种 | 成交与市价所用币种 |
| `fund_currency` | 基金净值币种 | 与交易币种不同时必填 |
| `underlying_exposure_currency` | 底层暴露币种 | 跨境产品必填；多币种在 notes 说明 |
| `quantity` | 持有数量 | 使用产品的交易数量单位 |
| `cost_basis` | 总成本 | 与 `trade_currency` 一致，保持同一计算口径 |
| `cost_basis_scope` | 成本口径 | 例如是否含费用、分红和转入处理；所有持仓保持一致 |
| `market_value` | 快照市值 | 与适用时点、交易币种对应 |
| `report_currency` / `market_value_reporting` | 报告币种与折算市值 | 组合汇总使用；没有统一估值与汇率时不得计算权重 |
| `fx_rate_to_reporting` / `fx_source_id` / `fx_valid_at` / `fx_quote_convention` | 折算汇率、来源、汇率适用时点与报价口径 | 交易币种与报告币种不同时必填 |
| `valuation_method` / `valuation_price` / `valuation_price_currency` / `valuation_source` / `valuation_valid_at` | 估值细节与估值适用时点 | 与报告币种市值对应 |
| `portfolio_snapshot_id` | 组合快照 ID | 同一估值时点的持仓批次共用 |
| `source_id` | 来源登记 ID | 对应 `database/sources.csv`；无法登记时说明人工核对依据 |
| `source` | 数据来源 | 券商对账单、成交记录或人工核对 |
| `verification_status` | 验证状态 | `verified / pending / conflicting / missing` |
| `supersedes_record_id` / `correction_reason` | 更正关联 | 仅更正旧记录时填写；不覆盖旧行 |
| `notes` | 补充说明 | 不含账号、证件或认证信息 |

个人组合 CSV 是操作表，不强制复制产品库的 `source_url` 等字段。需要更细证据时写入 `notes`，或在 Decision Log 中保存快照。

CSV 示例只表达格式，不代表真实产品：

```csv
record_id,holding_id,valid_at,fetched_at,account_alias,product_id,asset_type,trade_currency,fund_currency,underlying_exposure_currency,quantity,cost_basis,cost_basis_scope,market_value,report_currency,market_value_reporting,fx_rate_to_reporting,fx_source_id,fx_valid_at,fx_quote_convention,valuation_method,valuation_price,valuation_price_currency,valuation_source,valuation_valid_at,portfolio_snapshot_id,source_id,source,verification_status,supersedes_record_id,correction_reason,notes
rec-1,broker_a-CN-<ticker>,2026-07-27T15:00:00+08:00,2026-07-27T15:00:00+08:00,broker_a,CN:<ticker>,ETF,CNY,CNY,USD,<quantity>,<total_cost>,includes_fees,<market_value>,CNY,<market_value>,1,,,,market_price,<price>,CNY,broker_statement,2026-07-27T15:00:00+08:00,snap-1,source-broker-statement,broker_statement,verified,,,
```

### 8.2 如何得到「当前持仓」

对每个 `holding_id` 取最新适用时点的一行，再排除 `quantity = 0` 的已清仓持仓。

组合复核时，各项 `market_value` 应使用统一估值时点。若最新记录来自不同日期，应先更新所有市值，再算配置比例。

只有 [`external_cashflows.csv`](database/portfolio/external_cashflows.csv) 与 [`performance_periods.csv`](database/portfolio/performance_periods.csv) 的现金流、估值时段、报告币种与来源完整时，才可计算 TWR 或 MWR/XIRR；否则写“不可计算”，不得由单次快照倒推收益率。口径见 [组合绩效与估值](knowledge/portfolio/performance_measurement.md)。

### 8.3 成本口径

当前只有一个 `cost_basis`，表示总成本；不处理逐笔批次、FIFO、税务成本或分红再投资拆分。

首次录入前选定并长期保持同一口径：是否含费用、分红是否冲减成本、转入如何定初始成本。写在 `notes` 或组合级说明中。不要在不同持仓之间混用。

### 8.4 何时需要交易流水

当前不新增 `transactions.csv`。出现逐笔对账、自动算持仓、税务批次、逐笔归因、公司行动等需求后再设计，并明确一个事实源——流水生成持仓，或仍以快照为准——避免两套手工并行。

## 9. 目标配置

路径：[database/portfolio/target_allocation.csv](database/portfolio/target_allocation.csv)

| 字段 | 含义 | 填写规则 |
|---|---|---|
| `allocation_id` | 配置桶的稳定 ID | 同一配置范围保持稳定 |
| `allocation_set_id` | 同一生效配置集 ID | 一次批准的完整桶集合共用 |
| `ips_id` | 投资政策版本 | 对应 `investment_policy.md` 的 IPS ID；IPS 状态须为 `active` |
| `approval_decision_id` | 批准本配置集的 Decision | 无批准记录则不得用于 `act` |
| `effective_from` | 生效日期 | ISO 8601 日期 |
| `asset_class` | 资产类别 | 按自己的分类体系统一 |
| `market` | 市场范围 | 使用一致的市场编码 |
| `report_currency` | 组合报告币种 | 用于统一换算后比较权重 |
| `target_weight` | 目标权重 | `0` 到 `1`，20% 写作 `0.20` |
| `min_weight` / `max_weight` | 容忍带 | 须满足 `min ≤ target ≤ max` |
| `rationale` | 制定依据 | 期限、现金流、风险承受能力等 |
| `review_trigger` | 复核触发器 | 日期、偏离幅度或生活事件 |
| `review_at` | 最迟复核时间 | 使用 ISO 8601 日期；无固定日期时留空并说明触发器 |
| `status` | 当前状态 | `active / superseded` |

当前有效配置规则：

1. 同一时点只保留一组 `active` 配置集，共用同一 `allocation_set_id`。
2. 该集内所有 `active` 的 `target_weight` 之和应为 `1.0`。
3. 资产分类不要重叠；同一暴露不要计入两个桶。
4. 新旧并存时用 `effective_from` 选生效日，旧方案标为 `superseded`。
5. `report_currency` 是组合报告币种，不是单个产品交易币种。
6. IPS 状态仍为 `draft` 时，目标配置只能作为草稿，不能支撑 `act`。

格式示例：

```csv
allocation_id,allocation_set_id,ips_id,approval_decision_id,effective_from,asset_class,market,report_currency,target_weight,min_weight,max_weight,rationale,review_trigger,review_at,status
<allocation_id>,alloc-set-1,ips-personal-v1,<decision_id>,2026-07-27,<asset_class>,<market>,CNY,<target>,<min>,<max>,<rationale>,<trigger>,<review_at>,active
```

### 9.1 修改目标配置

目标变化属于新的投资决策：走完 Diligence 八步 → 在 `decision_log/` 说明原因 → 旧记录 `superseded` → 新增带 `effective_from` 的 `active` 记录。

普通再平衡只把实际配置拉回现有目标区间，不改目标本身。

## 10. 场景四：组合复核

使用 [组合复核工作流](workflow/portfolio_review.md)。

### 10.1 触发条件

固定周期；收入/负债/支出或应急现金明显变化；目标配置到期；某项资产明显出容忍带；产品、监管、税务或市场结构重大变化；原 Decision 失效条件被触发。

### 10.2 复核步骤

1. 更新资产、负债、应急现金和未来现金需求。
2. 统一时点更新持仓市值。
3. 按资产、地区、币种、行业和产品汇总暴露。
4. 与目标配置比较，计算偏离。
5. 检查集中度、相关性、流动性和最大可承受损失。
6. 读原 Decision Log，检查原始逻辑和失效条件。
7. 更新费用、税务、法规和产品状态。
8. 对继续持有、追加、减少和不行动分别按 [Diligence 八步](prompts/diligence.md) 过审；走完整路径，见「§1.1」。
9. 结果写入 `reports/`，复核结论写入 Decision Log。
10. 触发调整时进入再平衡流程。

## 11. 场景五：再平衡

使用 [再平衡工作流](workflow/rebalance.md)。再平衡属于调仓，须按 [diligence.md](prompts/diligence.md) 走完整八步。

1. 确认目标配置仍然有效。
2. 统一估值时点计算实际配置。
3. 检查是否达到时间或偏离触发条件。
4. 先评估用新增现金修复偏离。
5. 必要时再评估卖出，计算费用、税务、价差和机会成本。
6. 比较一次调整与分批调整。
7. 按 Diligence 八步过审。
8. 写清金额、顺序、执行条件和停止条件。
9. 执行前保存 Decision Log；执行后更新持仓，并在同一日志追加实际结果。

修改目标与执行再平衡是两类决策，应分别记录。

## 12. 数据更新节奏

| 触发场景 | 要更新的内容 |
|---|---|
| 每次成交后 | 受影响持仓快照、Decision Log 实际执行 |
| 分红、拆分、转入转出后 | 数量、成本、市值、来源和说明 |
| 买入或调仓前 | 产品规模、成交额、价差、净值/IOPV、折溢价、申赎状态、公告 |
| 组合复核前 | 所有持仓统一时点市值、现金需求、目标偏离 |
| 资金或生活目标变化后 | 现金需求；必要时重审目标配置 |
| 产品公告发布后 | 产品基础数据或状态；记录来源和生效时间 |
| 模型规则变化时 | 新模型版本、输入要求和变更原因 |
| 知识或法规变化时 | 更新知识条目并记录复核日期 |

频率只提供维护节奏，不能替代行动前的数据核验。当前决策使用的动态数据必须有适用时点。

### 12.1 隐私、来源与备份

- `knowledge/`、`workflow/`、`templates/` 和公开产品资料可按普通项目版本控制。
- 本仓库按公开仓库设计。个人持仓、生产报告、生产 Decision Log 只留本机，Git 不入库。`.gitignore` 已排除 `database/portfolio/holdings.csv` 与非 demo 的 `reports/`、`decision_log/`。不要写入完整账号、证件、银行卡、认证秘密或原始券商文件。工作区里若仍有这些文件，当作本机数据，不要当成已入库的协作副本。
- 生产 DD 与 Decision Log 须在本机创建，才构成 `act` 留痕；演示只放对应 `demo/` 目录。
- 原始券商文件存于仓库外的受控位置；在 [`sources.csv`](database/sources.csv) 仅记录文件引用、哈希状态与核验结论。
- Git 用于版本与复盘，不是秘密管理工具。对个人组合、报告和日志保持加密、可恢复的备份，并定期测试恢复。
- 行情 API 或其他认证信息出现后，放入已忽略的本地 `.env` 或 `private/`；不得放入报告、来源登记或 Agent 上下文。

## 13. 停止条件

出现以下情况时暂停行动。与 [diligence.md](prompts/diligence.md) 一致，此处作操作备忘：

- 关键数据缺失、产品身份冲突、来源冲突未解决、动态数据过期、公式无法复算、关键动态无合格双来源、未关闭的关键 `warning`
- 模型阈值未确认却试图输出精确排名
- Risk 为`Critical`，或关键风险无法评估
- Challenge 为 `revise` / `reject`
- 追加买入时目标资产已达或超过上限
- Decision 执行条件已失效，或下单信息与 DD 对象不一致

超过目标配置可能是卖出或再平衡的触发条件，不应当作全局「永远停止」。暂停后把 Decision 记为 `research` / `wait` / `reject`，并列出重新进入流程所需条件。

## 14. 当前初始化顺序

进度总览先看 [STATUS.md](STATUS.md)。要从骨架进入「可作真实决策」，按此顺序补（可并行组已标注）：

**阶段 1（独立完成）**

1. 填写并批准 [investment_policy.md](database/portfolio/investment_policy.md)，使 IPS（Investment Policy Statement，投资政策）状态为 `active`。
   - **最低完成标准**：目的、风险承受能力、应急现金要求、约束条件、报告币种 5 节非空；IPS ID 已分配；有批准记录。
   - IPS 状态仍为 `draft` 时 cannot act。

**阶段 2（可并行）**

2. 在 `target_allocation.csv` 录入与该 IPS 绑定的有效配置集。
   - **最低完成标准**：至少一个 `allocation_set_id`；所有 active 桶的 `target_weight` 之和 = 1.0；有 `approval_decision_id`。
3. 在本地 `holdings.csv` 录入不含敏感账号的当前持仓快照。该文件仅本机有效，勿 commit。公开仓库设计见 README 隐私节。
   - **最低完成标准**：至少一条 `verified` 记录；统一估值时点；填写交易/基金/底层暴露三种币种。

**阶段 3（可并行推进）**

4. 按 [database/README.md](database/README.md) 核验 [ETF 候选种子](database/watchlist/us_index_etf_candidates.csv)。
   - **最低完成标准**：至少一个观察标的完成官方名称、代码、跟踪指数核验；核验结果写入 `verification_status`。
5. 为核验后的产品建立基础数据，区分交易、基金与底层暴露币种。
   - **最低完成标准**：每个产品基础字段（代码/名称/交易所/币种/费率/跟踪指数）无 `missing`。
6. 按 [products/history/](database/products/history/) 追加真实动态观测。
   - **最低完成标准**：至少一个产品有 2 期以上真实动态记录（含适用时点）；遵守最大允许时效。
7. 用真实数据补全 [ETF 模型 v0.1](database/screening/etf_model_v0.1.md)；比较结果写入 [screening/runs/](database/screening/runs/)。
   - **最低完成标准**：至少一个 run 写入非 demo 目录；输入清单完整。

**阶段 4（完成以上后）**

8. 完成第一份研究报告。
9. 跑一次完整 DD，即使最终 Decision 是`research`或`wait`。
10. 建立第一份生产 Decision Log。不要写在 `decision_log/demo/`。
11. 在约定周期后进行复盘：先过程评估，再看结果。

## 15. 维护项目本身

### 修改规则

- 加载协议与建设协作 → `prompts/building.md`
- 知识调研身份与分档 → `prompts/learning.md`
- 会话怎么回答 → `AGENTS.md`
- 人读文档文风 → `prompts/docs_style.md`
- 证据规范 → `prompts/evidence_standards.md`
- Diligence 八步与停止条件 → `prompts/diligence.md`
- 某项能力怎么做 → 对应 `skills/<name>/SKILL.md`

改完后检查 `AGENTS.md`、`CLAUDE.md`；Cursor 入口仍为 `.cursor/rules/agents.mdc` 指向 `AGENTS.md`。

### 修改工作流

只有真实执行暴露出重复问题时才改 `workflow/`。在 Decision Log 或报告里记下问题和修改原因，避免凭单次结果随意改规则。

### 修改模型

保留旧版本，建新版本文件。历史决策继续引用当时的模型和输入快照。

### 扩展资产

增加新资产类别前，至少补齐：稳定知识、产品字段、数据来源、评价方法、特有风险、操作工作流、对应模板是否够用。

## 16. 当前限制和已知维护成本

- 例行定投已有轻量路径，见「§1.1」；但 Challenge 仍要求 3+3+3，可能与 Reasoning 内容重叠。
- Reasoning 与 Challenge 都含反方检查，边界尚需真实案例验证。
- 动态历史与评价 run 目录已有骨架，尚无真实观测与评分结果。
- 没有交易流水；持仓依赖人工追加快照。
- **没有自动测试能阻止跳过某项 Skill**；执行质量依赖 Agent 遵守开场清单与人工复核。
- 无自动行情抓取、无券商导入、无定时任务、无自动评分、无自动下单；全部动态数据依赖人工收集与核对。
- 无紧急/加速 DD 路径；极端市场事件时用户可能被迫在"遵守纪律但不操作"与"绕过系统直接下单"之间选择。

这些问题先记着，等实际案例积累后再决定是否继续简化规则。

## 17. 常见错误与恢复

### 文件误改或误删

1. **持仓数据写错**：不要直接修改旧行。追加新行，填写 `supersedes_record_id` 指向被更正行，`correction_reason` 记录原因。
2. **Decision Log 写错**：追加 `amendment` 段，标注修正时间和原因；不覆盖原记录。
3. **规则文件（Prompt/Skill）误改**：`git diff` 查看变更 → `git checkout -- <file>` 恢复。若变更已提交，`git revert`。
4. **整个文件误删**：`git checkout -- <file>` 恢复。若已 `git rm` 并提交，`git revert <commit>`。

### Decision Log 写入中断

1. 检查已写入内容（`decision_log/` 下对应文件）。
2. 从 [templates/decision_log.md](templates/decision_log.md) 复制空白模板，补填已完成部分。
3. 在 `notes` 中注明中断原因和恢复时间。
4. 已写入但未完成的旧文件保留，标注 `incomplete`；新文件引用旧文件路径。

### 发现过期数据被用于分析

1. 立即停止当前流程，Decision 暂记为 `research`。
2. 重新核验所有关键动态字段的时效（对照 [data_contracts.md](database/data_contracts.md) 最大允许时效表）。
3. 将过期数据在来源登记中标记为 `outdated`。
4. 用新数据重新走 Validation → 继续被阻断的八步阶段。

### 误操作需要回退

1. 用户在对话中说明需要回退的操作。
2. 在 Decision Log 中追加 `correction` 记录：时间、范围、原因。
3. 已写入的文件通过 git revert 回退。

### 漏读 Skill 或 Prompt

1. 发现后立即暂停当前阶段。
2. 补读遗漏文件。
3. 检查已产出内容是否违背漏读文件的规则；如有违背，回退该阶段产出并重做。
4. 在 DD 记录中标注补读时间与影响范围。

## 18. 外部工具对接

### Obsidian 浏览知识库

推荐用 [Obsidian](https://obsidian.md/) 打开 PIOS 仓库根目录作为 vault，利用以下功能：

- **Graph View**：可视化 knowledge/ 条目间的 `[[wiki link]]` 关联。
- **Dataview 插件**：查询标记（如 `from "knowledge" where status = "draft"`）。
- **Templater 插件**：为 knowledge/ 条目和 reports/ 创建标准模板。

### 与 Wealthfolio / Ghostfolio 的分工

PIOS 专注决策纪律层；Wealthfolio 或 Ghostfolio 可选作持仓事实层：

| 层 | 工具 | 职责 |
|---|------|------|
| 事实层 | Wealthfolio / Ghostfolio | 持仓记账、净值追踪、绩效计算、可视化仪表盘 |
| 决策层 | PIOS | 研究、核验、建模、推理、风险、反方、决策记录 |

**数据交换**：从 Wealthfolio/Ghostfolio 导出 CSV → 按 [holdings.csv](database/portfolio/holdings.csv) 字段映射导入 PIOS。映射表：

| 外部字段（示例） | PIOS 字段 |
|------|------|
| Ticker / Symbol | `product_id`（加前缀 `CN:` / `US:`） |
| Quantity / Shares | `quantity` |
| Cost Basis / Avg Cost | `cost_basis` |
| Market Value | `market_value` |
| Currency | `trade_currency` |
| Account | `account_alias` |

导入后须走 Validation 核验字段完整性，然后才能进入生产 Decision 输入。
