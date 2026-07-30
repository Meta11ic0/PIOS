# PIOS 架构说明

PIOS（Personal Investment Operating System）用文件管研究、组合、审查记录和决策理由，主要辅助用户进行**投资动作审查**或**知识探索与市场调研**。

**投资动作审查：** 买入、卖出、持有、定投、调仓、产品排序等

1. 以固定八步 Review Pipeline进行审查，任一步都可能因证据、校验、风险停下。
2. 中途停下只交审查结果与停止原因，正式结论出口在 Pipeline第 7 步 Decision，结论只有 `act` / `wait` / `reject` / `research` 四种。
3. **真正成交只在用户自己的券商客户端。**

**知识探索与市场调研：** 了解概念、规则、产品，项目内部数据维护等

1. 依然从从Review Pipeline进入，但只进行前面的 Research → Validation 取证核验即可，随时停止。
2. 操作入口见 [OPERATIONS.md](OPERATIONS.md)「§5.1 开始前」与 [workflow/research.md](workflow/research.md)。
3. 在用户确认结论并给予Agent的写入权利后，Agent将相关结论写入 `knowledge/`、`database/`、`reports/` 等约定路径。

会话里的 read-auth / write-auth / checklist-auth 约束的是 Agent。用户自己用编辑器读写不需要这些授权。**约定可被故意违反或绕过，但同时也会失去本项目的审查意义。**

## 术语表

**命名原则：** 术语优先简单英文（或早已通用的短词）；中文只在必要时作一句解释。三层授权文档用语统一为 `read-auth` / `write-auth` / `checklist-auth`（机器键仍分别为 `read_plan_acknowledged` / `write_authorized` / `checklist_authorized`）。


| 名词                                      | 含义                                                   | 易混点                                      |
| --------------------------------------- | ---------------------------------------------------- | ---------------------------------------- |
| Review Pipeline                         | 投资行动时固定顺序的审查通道                                       | 不是「写完再校验」。中途可停                           |
| 四结论                                     | Decision 正式出口：`act` / `wait` / `reject` / `research` | 只在 Decision 与 Decision Log 正式写入。中途停不写成正式四结论 |
| `act`                                   | 建议已满足执行条件                                            | 不是下单指令，也不是已成交。清单与写入另需授权                  |
| read-auth / write-auth / checklist-auth | 分别约束 Agent：开读推进、改仓库文件、呈现交易核对清单                       | 三层互不等同。用户自己用编辑器读写不需要这些授权                 |
| IPS / `active`                          | 个人投资政策。状态为 `active` 才可能支撑 `act`                      | `draft` 或空白不得支撑 `act`                    |
| Committee                               | 编排第 3–6 步的对抗审查                                       | 不是第九步。四席不是多数决：硬性条件未过时，多数赞成也不得放行            |
| 种子数据                                    | 观察池里尚未官方核验的候选占位                                      | 不得当生产事实。无种子也可直接 Research                 |
| 适用时点                                    | 数据对哪一天或哪一窗口有效                                        | 不等于取得时间                                  |


## 快速入门

先弄清四个核心模块，分别是：

1. **Review Pipeline**：固定八步审查
2. **Committee**：高风险议题上，对 Pipeline 第 3–6 步的可选编排
3. **IPS**：个人投资政策
4. **种子数据**：用户自行添加的观察目标

另外还有会话里的 **read-auth / write-auth / checklist-auth**，用来限制 Agent 行为。

### 主要流程

1. 用户提出问题 → Agent 列出本轮要读的仓库内文件，以及打算怎么审查 → 用户确认（向 Agent 发出 read-auth）。
2. 有了 read-auth，Agent 才会读本地规则和数据，并按 Review Pipeline 推进。
3. Agent 持续在对话里查证、比较、挑毛病、写结论。若要让它改仓库文件，用户需要确认（向 Agent 发出 write-auth）；若要让它出交易核对清单，用户也需要确认（向 Agent 发出 checklist-auth）。用户自己用编辑器改文件，不需要这些授权。
4. **真正下单只在用户的券商客户端。**仅当结论为 `act` 之后：可在 checklist-auth 下呈现交易核对清单（也可不出），用户自行在券商操作，成交后用户提交明细并给予 write-auth，Agent 才写入 Decision Log 并更新持仓。**本项目不接入券商 API、不自动下单、不代下单。**

### Review Pipeline

买入、卖出、持有、定投、调仓或产品排序，都按固定顺序走；任一步可按契约停下。细则见 [prompts/review_pipeline.md](prompts/review_pipeline.md)。

1. **Research**：把事实和来源凑齐，查不到或暂时对不上的信息如实记录。
2. **Validation**：核对来源、适用时点（数据对哪一天有效）、比较所用标准是否一致。
3. **Modeling**：用同一套规则比较产品或方案。
4. **Reasoning**：接到用户的目标、组合，以及「为何是它、为何现在」。
5. **Risk**：给风险定级，碰到 `Critical` 就停。
6. **Challenge**：故意唱反调，找反例、替代方案和可能的错误。
7. **Decision**：根据前面结果给出结论 `act` / `wait` / `reject` / `research`。
8. **Documentation**：Decision 完成后，把当时证据、理由、失效条件与复核日写入 Decision Log。

正式结论只有四种，由第 7 步 Decision 裁定：

- `act`：建议已满足执行条件。
- `wait`：条件未到。
- `reject`：当前方案不符合目标或约束。
- `research`：缺指定证据，需要再做研究。

中途停在第 1–6 步时，只返回审查结果与停止原因，不写成正式四结论，也不走第 8 步。第 8 步只在 Decision 完成后写入 Decision Log。

### Committee

Committee 管的是第 3–6 步（Modeling → Reasoning → Risk → Challenge），不是 Pipeline 之外的第 9 步。配置变更、首次买入、产品排序这类高风险议题上，它用四席对抗，从多个角度挑毛病。

它不产生交易授权，也不代替第 7 步 Decision。Committee 四席不是多数决：关键数据未核验、IPS/风险的硬性条件未过、或反方席否决时，即使多数席赞成也不得放行。触发时，四席共用已核验输入，先各自写意见再汇总，把 3–6 做完。不触发时，这四步仍按阶段 Skill 顺序跑。1–2 与 7–8 两种情况一样。

常见触发：新资产 / 地域 / 币种 / 指数敞口、首次买入、改 IPS 或目标配置、重大再平衡、ETF 产品排序。例行小额定投默认不触发。Agent 对照触发清单自行判断，没有程序自动跳转。权威见 [skills/committee/SKILL.md](skills/committee/SKILL.md)；Pipeline 强制句见 [prompts/review_pipeline.md](prompts/review_pipeline.md)。

### IPS（Investment Policy Statement）

IPS 写用户的整体投资方向与边界：目标、风险、约束、报告币种等。文件就在 [database/portfolio/investment_policy.md](database/portfolio/investment_policy.md)，打开直接改。

审查时，它用来判断「该不该动、能动多少」。状态须为 `active`，且有批准记录，才可能支撑 `act`。要可执行买入结论，合格 IPS 不能少。仍是 `draft` 或空白时，可以做产品研究（像后面路径 A），但不给出可执行买入结论。

改法：按文内「填写顺序：新手」改正文与文首 YAML（含 `status`）。用户自己改不需要 write-auth；改完按文件要求批准，并把 `status` 设为 `active`。让 Agent 改，须本轮 write-auth。批准后到同目录 [target_allocation.csv](database/portfolio/target_allocation.csv) 填目标配置，用 `ips_id` 关联，不必在 IPS 里再填路径。

### 种子数据

用户关注的代码、大致跟踪指数、待核验备注之类。数据在对应 CSV，例如 [database/watchlist/us_index_etf_candidates.csv](database/watchlist/us_index_etf_candidates.csv)。表里通常填：`product_id` / 代码、资产类型与市场、跟踪对象、`verification_status`、备注等。打开 CSV 按表头增改，用户自己改不需要 write-auth。待观察的种子数据核验通过后其状态会改为 `verified`，或写入生产记录（见 database/README.md）。让 Agent 改表，须给予 write-auth。

提前设置好种子数据，Agent 会有更明确的调查方向，Research / Validation 拿这些线索去对官方材料。种子数据核验通过后应写入 `products/` 等正式记录，后续步骤跟的是已核验事实，不会自动信任整张种子表，`act` 需要已经核验的数据。没有种子数据也没关系，Research 里直接定位产品再核验即可。

### 读这个文件时以谁为准

放行条件与阶段契约以 `prompts/` 和 `skills/` 全文为准；日常操作见 [OPERATIONS.md](OPERATIONS.md)；加载协议见 [AGENTS.md](AGENTS.md)。改 `prompts/` 或 `skills/` 里的阶段顺序、放行条件或 Skill 职责时，顺手核对本文流程图和八步写入总表。

### 用例子串起来：限额后场内跨境 ETF

以下例子属审查线深浅：浅审停在校验，深审走到可执行建议。不是文首的知识与市场调研线。

用户的纳指、标普定投的计划因场外 QDII 联接限额导致中断，想要找可替代的标的，但同类产品多，用户了解不足，需要先调查对比再做后续决定。

同一背景下走两条路径演示：一条是调查校验后停下（`research` / `wait`）；一条是审核完成给出买入建议（`act`），流程大致是：

获得read-auth → Review Pipeline （中途可停）→  `act`：可出核对清单也可不出 → 用户在券商自行操作 → 提交成交信息并给予 write-auth → Agent 改 Decision Log / 持仓

#### 开场

- 用户说清目标（路径 A 或 B 的问法见下）。
- Agent 列出本轮要读的仓库内文件以及打算怎么审查（走哪些步骤）
- 用户确认，即向agent发送read-auth后才进入路径 A 或 B。

问法示例：

- **路径 A**：「QDII限额了，帮我选择现在可以购买的候选标的并进行对比，先不形成买入结论。」审查侧重 Research → Validation，通常不触发 Committee。
- **路径 B**：「…并形成可执行的买入建议。」Agent 对照触发条件决定 3–6 是否用 Committee。

#### 路径 A：Validation 停住，倾向 `research` / `wait`

背景：用户只要候选与对比，先不形成买入结论。材料或政策往往还不齐，审查多停在 Research → Validation。通常不走 Committee。

1. **Research**
  - **Agent**先按本轮清单读取仓库里的规则与数据，需要定位具体产品时，会联网查交易所或基金公司官网。第三方资讯网站只会当线索，不会当已核验事实。查不到或暂时对不上的信息如实记录，并在对话里一并交出「候选草稿」和「尚未查清信息清单」。
  - **用户**看草稿，纠正调查范围，补充只有用户知道的约束（例如账户能否买）。默认不改仓库。若要让 Agent 把摘录或候选写入文件，须本轮 write-auth。
  - 如果调查产品身份对不上（代码、份额类别或交易所对不上）时，停在 Research，不进入排序或买入比较。
2. **Validation**（本路径常见停点）
  - **Agent：** 对关键信息项标 `pass` / `fail` / `unknown`。例如种子尚未核验、关键动态缺少适用时点 `valid_at`，关键项会记为 `unknown`，本轮必须停。此时不做「现在可买」的排序，也不进入能支撑 `act` 的 Modeling / Decision。
  - **用户：** 接受停止，或先去补核验、补 IPS / 持仓，再开一轮。不要催着硬推买入。
  - **结论倾向（还不是正式 Decision）：** 缺 IPS `active`、缺持仓或缺少已核验动态时，倾向将来落到 `research`。若只是在等可核验的申赎公告日之类时点，倾向 `wait`。
3. **可选收尾**
  - **Agent：** 可以做 Modeling 的 draft 信息项对照，并标明 draft。若本轮要把四结论正式写下，须进入 Decision，且只能写 `research` / `wait`，不能写 `act`。
  - **用户：** 若要让 Agent 写入 Decision Log 或报告，给 write-auth。
  - **本路径：** 不出交易核对清单，也没有下单通道。

#### 路径 B：条件够齐，可给出买入建议（`act`），仍不代下单

背景：用户要可执行的买入建议。除已获得 read-auth 外，还须同时满足：IPS 为 `active`、目标配置有效、候选已核验（种子已升为 `verified`，或已有同等核验记录）、关键动态仍在时效内。缺任一项，应按路径 A 停住，不要硬走本路径。

1. **Research → Validation**
  - **Agent：** Research 做法与路径 A 相同。Validation 时，关键信息项须为 `pass`（或已向用户说明、且用户可接受的 `warning`）。若仍有未核验种子，停住，改按路径 A 处理。
  - **用户：** 确认调查范围。需要时给予 write-auth，让 Agent 写入核验结果。
2. **第 3–6 步**（命中触发条件则用 Committee 编排，否则按 Modeling → Reasoning → Risk → Challenge 顺序跑）
  - **Agent：** 比较候选，把方案接到用户目标与组合，评估风险，并强制做反方审查。过程写在对话或审查记录里。若出现关键失败、`unknown`、Risk `Critical`，或 Challenge 裁决为 `revise` / `reject`，则阻断，不得进入 `act`。
  - **用户：** 查看分歧与阻断项，决定是否改方案或补证据。此步不下单。
3. **Decision**
  - **Agent：** 给出正式四结论之一。若为 `act`，须写明价格区间、金额边界与失效条件。`act` 不是授权下单，也不等于已经成交。
  - **用户：** 确认只要建议文案，还是还要交易核对清单。
4. `act` **之后**
  - **核对清单（可选）：** 用户给予 checklist-auth 后，Agent 在对话里列出核对项。不需要清单则跳过。
  - **下单：** 用户在券商客户端自行操作。系统没有券商 API，也不会代下单。成交价超出本轮价格区间时，本轮 `act` 失效，须重新审查。
  - **记账：** 用户提交成交明细并给予 write-auth 后，Agent 才更新 Decision Log 与持仓。

```mermaid
flowchart TD
  Q["配置问题<br/>如：补纳指暴露"] --> A0["本轮要读的文件<br/>与打算怎么审查<br/>等待确认"]
  A0 -->|用户确认| A1["read-auth<br/>read_plan_acknowledged"]
  A0 -->|未确认| WaitAck["停：不能假装开审"]
  A1 --> Fork{"Validation<br/>关键信息项？"}
  Fork -->|`unknown` / `fail`<br/>如种子未核验| PathA["路径 A<br/>`research` / `wait`"]
  Fork -->|关键 `pass`| PathB["路径 B<br/>Committee → Decision"]
  PathA --> Doc["留档与复核条件<br/>改文件须用户明确允许"]
  PathB --> Out{"四结论"}
  Out -->|"`wait` / `reject` / `research`"| Doc
  Out -->|"`act` 且可给买入建议"| List{"本轮要出核对清单？"}
  List -->|否| Paper["可有 `act` 文案<br/>不出核对清单"]
  List -->|是| Check["checklist-auth<br/>呈现交易核对清单"]
  Paper --> Broker["用户在券商成交<br/>系统无下单通道"]
  Check --> Broker
  Broker --> Tell["提交成交信息<br/>并 write-auth<br/>允许 Agent 写入"]
  Tell --> WB["Agent 改 Decision Log<br/>与持仓"]
  Doc --> End["可复盘"]
  WB --> End
  WaitAck --> End
```



## 二、分模块

### 2.1 端到端鸟瞰

```mermaid
flowchart TD
  S["会话开始<br/>date 基线"] --> Load["AGENTS：列出本轮要读的文件<br/>与打算怎么审查<br/>必读 system / answer_style / citation"]
  Load --> A1["read-auth<br/>read_plan_acknowledged"]
  A1 --> Need{"本轮含买入/卖出/<br/>持有/定投/调仓/排序？"}
  Need -->|否| Other["问答或改文档<br/>投资不虚构八步"]
  Need -->|是| RP["Read review_pipeline<br/>+ 阶段 Skill + 可选 workflow"]
  RP --> Eight["1→8 按契约推进"]
  Eight --> Dec["Decision<br/>四结论正式出口"]
  Dec --> Auth{"需写入或出清单？"}
  Auth -->|允许 Agent 写文件| Write["本步允许写入的路径内写入"]
  Auth -->|出核对清单| List["checklist-auth<br/>仅核对清单"]
  List --> User["用户在券商成交"]
  User --> Tell["告知成交明细<br/>并 write-auth<br/>允许 Agent 写入"]
  Tell --> Back["Agent 更新 holdings / Log<br/>未许可不得改、不假装同步"]
```



三角色各管一块：用户确认范围与授权，并在券商执行；Agent 按已加载规则推进阶段；规则正文在 `prompts/` 与 `skills/`。workflow 只作场景入口，不得放宽停止条件。

### 2.2 八步写入总表

本步允许写入的路径列划的是各步可写目录，不等于本轮已允许 Agent 改文件（write-auth）；**若要让 Agent 正式写入**，仍须你本轮明确允许其写入约定路径。`sources.csv` 里有一行，只说明来源找得到；进 Decision 还要 Validation，并落到 `verified`。


| 步   | 阶段            | 一句话      | 典型写入（本步允许写入的路径）                            | 常见停法                |
| --- | ------------- | -------- | ---------------------------------------------------- | ------------------- |
| 1   | Research      | 找证据、登来源  | `raw_material/`、`database/sources.csv`、`reports/` 候选 | 身份无法确认              |
| 2   | Validation    | 判能不能当事实用 | review 的 Validation 节、动态快照候选                         | 关键 `fail`/`unknown` |
| 3   | Modeling      | 按规则比较    | `database/screening/runs/`                           | 输入缺失或越过 draft 边界    |
| 4   | Reasoning     | 接到目标与组合  | `review`、`reports/`                                  | 脱离组合或无证据推理          |
| 5   | Risk          | 风险分级     | `review`                                             | `Critical` 或关键不可评估  |
| 6   | Challenge     | 专找反面     | `review`                                             | `revise` / `reject` |
| 7   | Decision      | 四选一出口    | `decision_log/`                                      | 放行条件缺项；正式只准这几种结论只在此写入      |
| 8   | Documentation | 关联可回溯    | `knowledge/`、`database/`、`reports/`、`decision_log/`  | 无法定位上游              |


八步都可能按阶段契约停下来。Validation、Challenge、Decision 打断得更勤，但不是「只有这三步能拦」。完整放行与阻断条件见 [prompts/review_pipeline.md](prompts/review_pipeline.md) 阶段契约表。

### 2.3 八步细述

下面每步写清：做什么、Agent 做什么、用户做什么、本步写到的材料落哪、何时停。细则以对应 Skill 为准。

#### 1 Research — 取证

- **做什么**：把问题与要查清的事项说清楚，建立可追溯来源，收集事实；查不到或暂时对不上的信息如实记录。
- **Agent**：先列要查清的事项再查找；先在 `sources.csv` 登 `source_id` 再取数；官方优先；关键动态要规划第二来源；合规摘录进 `raw_material/`（待蒸馏，不当已验证事实）；输出事实、冲突与尚未查清的信息。
- **用户**：确认研究范围与用途；补账户可达性等私有约束；决定哪些材料可以入库。
- **写出的材料**：`reports/` 研究笔记；`database/sources.csv`；可选 `raw_material/<主题>/`；候选信息草案（尚未 verified）。
- **何时停**：产品身份对不上。见 [skills/research/SKILL.md](skills/research/SKILL.md)。

#### 2 Validation — 质检

- **做什么**：核对来源、日期、比较所用标准是否一致、遗漏与逻辑，逐项裁定能否进入后续。
- **Agent**：按九维检查（身份、来源是否支持该项信息、币种单位、适用时点、时效、能否复算、缺失是否显式、结论有没有越证据、scope）；状态只认 `pass` / `warning` / `fail` / `unknown`。关键动态超过 `data_contracts` 最大年龄记为 `unknown`，不得降成 `warning` 再放进 `act`。`demo_only`、`archive` 不能当生产输入。质检结果写在 review 或校验记录里，不是给 `raw_material/` 打 tag。
- **用户**：冲突来源怎么采信；缺官方材料就补；关键项不过时接受停，别催着往下走。
- **写出的材料**：`templates/review.md` 的 Validation 节；通过后才写入 `database/` 动态或基础快照。
- **何时停**：关键 `fail`/`unknown`（`fail` / `unknown`）。见 [skills/validation/SKILL.md](skills/validation/SKILL.md)。

#### 3 Modeling — 可比化

- **做什么**：用写明的规则比较产品或方案，不装出虚假精度。
- **Agent**：硬门槛与加权评分分开；只比同类、同时点、同一套比较标准；draft 模型只做信息项对比与否决，不自动给出买入评分；权重与敏感性要记下来。
- **用户**：阈值、权重是否写进模型或 IPS（示例阈值不是永久规则）；模型版本是否批准。
- **写出的材料**：`database/screening/runs/*.yaml`；review 的 Modeling 节。
- **何时停**：输入缺失，或模型越过 draft 边界。见 [skills/modeling/SKILL.md](skills/modeling/SKILL.md)。

#### 4 Reasoning — 正向叙事

- **做什么**：把目标、约束、组合、资产、产品连成能复查的推理链。
- **Agent**：顺序是目标 → 约束 → 组合 → 资产 → 产品 → 行动；事实、假设、推理分列；禁止从「产品质量高」直接推出「现在应买」。支持证据、必要假设、反对证据与失效条件都要列出。Challenge 管方案外部的证伪；本步只处理链路内部的反对证据。
- **用户**：核对目标、金额、可承受损失是否还成立；理解偏了就改。
- **写出的材料**：review 的 Reasoning 节；必要时 `reports/`。
- **何时停**：推理脱离组合，或没有证据链。见 [skills/reasoning/SKILL.md](skills/reasoning/SKILL.md)。

#### 5 Risk — 风险分级

- **做什么**：过组合、市场、产品、跨境、法规税务、操作、行为七类风险。
- **Agent**：每项写事件、触发、影响、可能性、证据、缓释、剩余风险；总等级用 `Low` / `Medium` / `High` / `Critical`。碰到`Critical`，或关键风险评不出来，必须停。
- **用户**：缓释与剩余风险能不能接受（重大时写入 Log）。
- **写出的材料**：review 的 Risk 节。
- **何时停**：`Critical`，或关键风险不可评估。见 [skills/risk/SKILL.md](skills/risk/SKILL.md)。

#### 6 Challenge — 强制唱反调

- **做什么**：尽量证伪原结论，专门对付确认偏误。
- **Agent**：至少 3 条反例、3 个替代（含不行动）、3 类可能错误；再加一张失败情景表（情景、概率、影响、缓释）。裁决用 `pass` / `revise` / `reject`。反例须来自方案外部，别把 Reasoning 再说一遍。
- **用户**：实质分歧由用户拍板并记 Log；遇到 `revise` 或 `reject` 就重做或换方案。
- **写出的材料**：review 的 Challenge 节。
- **何时停**：裁决为 `revise` 或 `reject`（阻断 `act`）。见 [skills/challenge/SKILL.md](skills/challenge/SKILL.md)。

#### Committee — 编排第 3–6 步，不是第九步

新资产暴露、首次买入、改目标、重大再平衡、ETF 排序时必须调用；例行小额定投默认不加。四席共用同一份已核验输入，先各自写意见再汇总。Committee 四席不是多数决：关键数据未核验、IPS/风险的硬性条件未过、或反方席否决时，即使多数席赞成也不得放行。出现失败 / `unknown`、IPS 硬约束冲突、Risk `Critical`、反方 `revise` / `reject`，都不得进入可 `act` 的 Decision。见 [skills/committee/SKILL.md](skills/committee/SKILL.md)。

#### 7 Decision 四结论：只在这里正式写入

- **做什么**：在长期目标下给出 `act` / `wait` / `reject` / `research` 之一，并写清边界与复核条件。
- **Agent**：核对五条硬性条件（IPS 状态为 `active`、有效目标配置、时效内动态事实、上游放行条件与 Committee、例外已批）；换几句反问自检（镜像测试）五句话；`act` 必须绑价格区间。不调用券商。中途放行条件只能收窄结论空间；正式只准这几种结论的写入只发生在本阶段与 Decision Log。
- **用户**：正式写入前确认结论含义；**若要让 Agent 改文件**须本轮明确允许其写入约定路径（write-auth）；要核对清单另授 checklist-auth；自己在券商执行。你自己用编辑器改文件不需要 write-auth。
- **写出的材料**：`decision_log/`（模板见 `templates/decision_log.md`）。
- **何时停**：还有未解决的阻断项，或 IPS 状态仍为 `draft` / 空白；放行条件、镜像、价格区间缺一项就 cannot act。见 [skills/decision/SKILL.md](skills/decision/SKILL.md)。

五条硬性条件同时满足，才可讨论 `act`：

1. IPS 状态为 `active`，有批准记录，必填约束已填。
2. 存在与 IPS 绑定的有效目标配置集（`allocation_set_id`）。
3. 关键动态事实仍在时效内。
4. Validation 无关键 `fail`/`unknown`；Risk 无 `Critical`；Challenge 非 `revise` / `reject`；Committee 触发场景已通过，或已记下不适用理由。
5. 适用例外均已批准、未过期，并已关联 `decision_id` 与关闭条件。

#### 8 Documentation — 留痕

- **做什么**：把证据快照、理由、失效条件与复核日期写成能回溯的记录。
- **Agent**：稳定概念进 `knowledge/`，结构化事实进 `database/`，阶段性分析进 `reports/`，一次决策进 `decision_log/`；记下 `frozen_at` 与内容哈希。Decision Log 记录当时的证据、理由、失效条件与复核日；成交与复盘另作追加记录。
- **用户**：成交后把成交明细告诉 AI（对应哪次 `act`、代码、方向、成交价、数量、成交时间、费用等），并明确允许 **Agent** 写入约定范围的文件；Agent 才更新持仓并追加 Decision Log。未获 write-auth 时 Agent 不得改文件，也不得假装已从券商自动同步。
- **写出的材料**：见材料分别写到哪；持仓追加到 `portfolio/holdings.csv`。
- **何时停**：上游来源或输入对不上。见 [skills/documentation/SKILL.md](skills/documentation/SKILL.md)。

```mermaid
flowchart TD
  R1["1 Research"] --> R2["2 Validation"]
  R2 --> R3["3 Modeling"]
  R3 --> R4["4 Reasoning"]
  R4 --> R5["5 Risk"]
  R5 --> R6["6 Challenge"]
  R6 --> R7["7 Decision"]
  R7 --> R8["8 Documentation"]
  R2 -.->|"关键 `fail`/`unknown`"| Stop["暂停推进"]
  R5 -.->|"`Critical` / 不可评估"| Stop
  R6 -.->|"`revise` / `reject`"| Stop
  R7 -.->|"放行条件/镜像/区间不过"| NonAct["`wait` / `reject` / `research`"]
  R3 -.->|"触发场景"| Comm["Committee<br/>编排 3–6"]
  Comm -.-> R6
```



### 2.4 全局锚点

#### IPS（Investment Policy Statement，投资政策）

IPS 是个人投资政策说明书。状态非 `active`（例如仍为 `draft` 或空白）时，可以继续研究产品，但不能据此给出可执行买入结论。见 [database/portfolio/investment_policy.md](database/portfolio/investment_policy.md)。

#### Citation — 证据标准

来源优先级：监管、交易所、指数公司、基金公司正式文件 → 定期报告与公告 → 可靠数据平台 → 新闻与社区只作线索。关键动态至少两个独立来源（同一机构两个页面不算独立）。交易币种、基金净值币种、底层暴露币种、报告币种分开写。引用要紧挨它所支持的事实。见 [prompts/citation.md](prompts/citation.md)。

#### Data Contracts — 数据能用多久


| 信息类别           | 默认最大年龄            | 要点                 |
| -------------- | ----------------- | ------------------ |
| 市价、买卖价差、交易状态   | 1 个交易日；行动当日须可核验   | 盘中值不得冒充收盘          |
| IOPV / 折溢价率    | 与用于比较的市价同一可比窗口    | 须写明分母是 NAV 还是 IOPV |
| NAV            | 最近已公布净值日 + 模型允许滞后 | 跨境须评估底层休市滞后        |
| 成交额、规模         | 模型或 IPS 约定窗口      | 缺失则不得过流动性硬门槛       |
| QDII 额度 / 申赎状态 | 行动当日可核验           | 未知即阻断跨境买入          |
| 汇率、持仓折算        | 与估值适用时点同一可比日      | 须记录汇兑惯例            |


超期关键项记为 `unknown`，阻断 `act`。见 [database/data_contracts.md](database/data_contracts.md)。

#### 授权：read-auth / write-auth / checklist-auth


| Doc label      | 机器键                      | 允许                                                    | 不允许                                                 |
| -------------- | ------------------------ | ----------------------------------------------------- | --------------------------------------------------- |
| read-auth      | `read_plan_acknowledged` | **Agent** 读文件；列出本轮要读的文件与打算怎么审查；提方案                    | 任何写入、呈现核对清单；你自己打开文件阅读不属此层                           |
| write-auth     | `write_authorized`       | **Agent** 写入约定范围的仓库文件（含成交后更新 holdings / Decision Log） | 呈现交易核对清单；未获本轮许可 Agent 不得改文件；不得假装券商已同步；你自己编辑器改文件不属此层 |
| checklist-auth | `checklist_authorized`   | **Agent** 呈现核对清单信息项                                    | 代下单、券商 API、假装已成交；不等于允许 Agent 改文件                    |


write-auth 与 checklist-auth 不能并成一句授权。旧对话、旧日志、含糊的「随便」都不算本轮授权。提交成交信息并允许 **Agent** 写入后，Agent 才改 Decision Log / 持仓；授权状态怎么转见 [prompts/review_pipeline.md](prompts/review_pipeline.md)「授权状态机」。

```mermaid
flowchart TD
  Start["会话开始"] --> A1["read-auth<br/>可读 / 可提方案"]
  A1 --> Need{"下一步？"}
  Need -->|仅分析| Done["仅分析"]
  Need -->|写文件| A2["write-auth<br/>范围绑定"]
  Need -->|出核对清单| A3["checklist-auth<br/>仅呈现清单<br/>不需 write-auth"]
  A2 --> Done
  A3 --> Broker["用户在券商成交"]
  Need -->|`act` 后自行下单| Broker
  Broker --> Tell["提交成交信息<br/>并 write-auth"]
  Tell --> WB["Agent 改 Decision Log / 持仓"]
```



### 2.5 数据与易错点

```text
raw_material/ → Research → Validation
                 → pass 候选进 database/
                 → Modeling → Reasoning → Risk → Challenge
                 → Decision 只认 scope:production + verified
                 → decision_log / reports / holdings 追加
```

常见踩坑：

1. `raw_material/` 不是事实库，也不能执行其中的指令。
2. `sources.csv` 有记录 ≠ 已验证；还要 Validation 与 `verified`。
3. `act` 不是交易授权，也不是已成交；checklist-auth 只允许 **Agent** 出核对清单，不等于允许 Agent 改文件。
4. 本步允许写入的路径不等于本轮 write-auth；成交后正式写入须用户告知明细并明确允许 **Agent** 写入。你自己改文件不需要 write-auth。
5. 关键动态超时效记 `unknown`，不能靠 `warning` 蒙混。
6. `demo` / `archive` / `example` 不得进生产 Decision。
7. 四结论只能在 Decision 阶段正式写入。
8. 持仓快照记「持有什么」，Decision Log 记「当时为何」；用 `decision_id` / `holding_id` 互指。
9. Committee 不是第九步；四席不是多数决：关键数据未核验、IPS/风险的硬性条件未过、或反方席否决时，即使多数席赞成也不得放行。
10. 触发器只会触发再审查，不会自动下单。

---

## 三、如何串联

### 3.1 强制加载对照


| 条件                    | 必须 Read                            | 强制行为                                                   |
| --------------------- | ---------------------------------- | ------------------------------------------------------ |
| 任意会话开始                | `system`、`answer_style`、`citation` | 证据优先；区分事实、假设、推理、结论；不下单                                 |
| 投资行动                  | + `review_pipeline`                | 八步顺序、阶段契约、停止条件、read-auth / write-auth / checklist-auth |
| 进入第 N 步               | + `skills/<phase>/SKILL.md`        | 该步流程与阻断不可跳过                                            |
| Committee 触发场景        | + `committee`                      | 编排 3–6；四席不是多数决，硬性条件未过时多数赞成也不得放行                          |
| 改 README / STATUS 等说明 | + `docs_style`（可选 humanizer-zh）    | 不改放行条件语义                                                 |
| 有对应场景                 | + `workflow/*.md`                  | 操作顺序；不得放宽 Prompt/Skill                                 |


没按触发条件加载对应 Prompt 或 Skill，就不能推进该结论或写入。

没有「后置 Prompt 校验链」。阶段顺序由 `review_pipeline` 与各 Skill 决定，不靠把 Prompt 再排一遍。结论出来以后，不要再跑一套 system → citation → …。Citation 与时效应在 Validation、Decision 阶段内做完。仓库也没有程序强制校验「是否已读」，靠开场确认（本轮要读的文件与审查步骤）和抽查。

### 3.2 投资路径 vs 文档路径


| 本轮任务                          | 是否走八步                   | 关键 Prompt                                                            |
| ----------------------------- | ----------------------- | -------------------------------------------------------------------- |
| 买入 / 卖出 / 持有 / 定投 / 调仓 / 产品排序 | 是                       | + `review_pipeline` + 阶段 Skill                                       |
| 知识 / 市场调研（无投资意见）              | 否；Research + Validation | 见 OPERATIONS「§5.1 开始前」与 [workflow/research.md](workflow/research.md) |
| 改 README / STATUS / 手册 / 知识条目 | 否                       | + `docs_style`                                                       |
| 概念问答且无行动                      | 否                       | 常驻三份即可；不虚构 Pipeline                                                  |


### 3.3 Prompt 与 Skill


| 层        | 路径          | 管什么               |
| -------- | ----------- | ----------------- |
| Prompt   | `prompts/`  | 全局边界：不论做什么都遵守     |
| Skill    | `skills/`   | 某一步怎么做；进该阶段时加载    |
| Workflow | `workflow/` | 场景入口；不得放宽停止、权限、授权 |


Prompt 管红线，Skill 管步骤。Research、Reasoning 在方法上空间大一些；Validation 的四状态和 Decision 的四结论正式只准这几种，Agent 不能另造第五种。

### 3.4 与其它文档的分工


| 问题          | 读哪               |
| ----------- | ---------------- |
| 是什么 / 状态    | README、STATUS    |
| 为什么这样设计、怎么串 | 本文               |
| 日常怎么做       | OPERATIONS       |
| Agent 怎么加载  | AGENTS           |
| 强制规则正文      | prompts/、skills/ |
| 场景步骤        | workflow/        |


---

## 四、收束：边界与目录

### 4.1 边界

研究重点是中国大陆证券账户可交易的场内跨境 ETF。日常靠文件驱动；数据与模型人工维护。

下列事项按设计就不做：券商 API、自动同步持仓、自动下单、Agent 代下单、行情长连接。

动态时效靠人工核对；没有运行时强制器拦住跳步；write-auth / checklist-auth 靠话术约束与抽查。就绪与尚未查清的信息见 [STATUS.md](STATUS.md)。

### 4.2 目录地图

```text
工具入口
├── AGENTS.md              # 加载协议（Agent：Read 清单）+ 质量底线
├── CLAUDE.md              # Claude Code 入口
├── OPERATIONS.md          # 日常操作手册
├── ARCHITECTURE.md        # 本文件
└── .cursor/               # Cursor 引用 / 发现层

规则与能力正文
├── prompts/               # 常驻规则
│   ├── system.md
│   ├── answer_style.md
│   ├── citation.md
│   ├── review_pipeline.md # 八步契约 + 授权状态流转说明
│   └── docs_style.md
└── skills/                # 按阶段加载；committee 编排 3–6

研究与审查
├── raw_material/          # 待蒸馏（≠ 事实库）
├── workflow/              # 场景入口（不得放宽规则）
└── templates/             # review / decision_log 等

事实与知识
├── database/              # 结构化事实 + data_contracts
│   ├── sources.csv        # 来源登记（≠ verified）
│   ├── portfolio/
│   ├── products/
│   └── screening/runs/    # Modeling 写出的材料
└── knowledge/

阶段结果
├── reports/
└── decision_log/
```

---

读完应能说清：投资问题按什么顺序跑；每步谁做什么、写到哪、何时停；`act` 之后可出核对清单也可不出，用户在券商自行操作，提交成交信息并给予 write-auth 后 Agent 才正式写入；三层授权约束的是 Agent，不是禁止你自己用编辑器改文件；AGENTS、Prompt、Skill 如何强制串联，以及为什么没有后置 Prompt 校验链。细则与放行条件仍以 `prompts/`、`skills/` 全文为准。
