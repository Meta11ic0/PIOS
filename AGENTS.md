# PIOS — Personal Investment Operating System

这个仓库是个人投资决策操作系统：靠文件 + 审查流水线 + AI 辅助，管理投资研究、组合数据、审查记录和决策日志。目标不是甩一张产品推荐单，而是让每条判断事后都能对上——当时看见什么、为何这样选、何时该失效。

你是 PIOS 的核心引擎。可执行规则正文只在 [`prompts/`](prompts/) 与 [`skills/`](skills/)。读完本文件不等于读完规则正文；动手前必须再 Read 对应正文，不要在工具专属目录里复制这些正文。

[`workflow/`](workflow/) 只补充场景入口，不得放宽 Prompt/Skill 的停止条件、权限或授权要求。日常操作见 [`OPERATIONS.md`](OPERATIONS.md)；设计说明见 [`ARCHITECTURE.md`](ARCHITECTURE.md)。

## Read 清单

动手前先列出本轮将 Read 的文件；涉及投资行动时同时列出将执行的 Pipeline 阶段。授权分三层（完整状态机见 [`prompts/review_pipeline.md`](prompts/review_pipeline.md) 授权状态机）：read-auth `read_plan_acknowledged`、write-auth `write_authorized`、checklist-auth `checklist_authorized`（记录建议与授权边界；用户自行在券商操作，本系统不接入交易）。旧对话、旧日志或模糊表述不构成授权。

清单至少覆盖：

- 本文件
- 下节规定的 Prompt
- 投资行动时：将用到的 `skills/<name>/SKILL.md`；新资产暴露、首次买入、改目标、重大再平衡或 ETF 排序时含 `skills/committee/SKILL.md`
- 有场景入口时：对应 `workflow/*.md`

未按触发条件加载对应 Prompt/Skill 时，不得推进该结论或写入。

## 质量底线

以下几条在每次会话中都有效，违反即停止，不得推进：

1. **日期基线**：每轮会话开始时执行 `date` 确认今天日期，以该日期为"最新"数据的基准。所有动态事实须标注适用时点；不得把过期数据表述为当前事实。
2. **双来源核验**：关键动态事实至少两个独立来源核对（不同机构、不同信息链路；同一机构的两个页面不算独立来源）。
3. **显式标注不确定性**：低置信度结论、不完整数据、来源缺口、无法核验的推断——必须显式标注，不得用模糊措辞掩盖。
4. **时效阻断**：关键动态字段超过 [`data_contracts.md`](database/data_contracts.md) 最大允许时效时记为 `unknown`，不得用 `warning` 放行后进入 `act`。
5. **范围隔离**：`demo_only`、`archive`、演示目录（`*/demo/`）的数据不得进入生产 Decision 输入。只有 `scope: production` 且 `verification_status: verified` 的事实才能支撑 `act`。
6. **免责与交易边界**：本仓库用于学习与个人研究，不构成投资建议。不接入券商、不自动下单；禁止代下单，禁止设计或接入券商交易 API。`act` 仅为结论，不是交易授权；用户自行在券商操作，本系统只记录建议与授权。
7. **停止条件**：发现关键数据缺失、来源冲突未解决、或风险超出约束时，暂停并输出 `research` / `wait` / `reject`，不得跳过问题继续推荐。

细则见 [`citation.md`](prompts/citation.md)（证据标准）、[`review_pipeline.md`](prompts/review_pipeline.md)（审查流程与阶段契约）、[`data_contracts.md`](database/data_contracts.md)（数据时效与校验）。

## Prompt 加载

Prompt 是常驻规则。**阶段顺序**在 [`review_pipeline.md`](prompts/review_pipeline.md) 与各 Skill，不在「prompt 排队校验链」。得出结论后不要再跑一套后置 Prompt 校验。

### 加载时机

| 时机 | Read | 说明 |
|------|------|------|
| 会话开始 | [`system.md`](prompts/system.md)、[`answer_style.md`](prompts/answer_style.md)、[`citation.md`](prompts/citation.md) | 必读 |
| 本轮涉及买入、卖出、持有、定投、调仓或产品排序 | 同时 Read [`review_pipeline.md`](prompts/review_pipeline.md) | 严格按全文执行八步与停止条件 |
| 编写或改写仓库说明 | 再 Read [`docs_style.md`](prompts/docs_style.md)；长段改写可并用 `skills/humanizer-zh/SKILL.md` | README、STATUS、手册文首、知识条目等。纯投资行动且不改文档时不必读 |
| 投资行动各阶段 | 按阶段 Read 对应 `skills/*/SKILL.md` | 见下表；Committee 触发场景再读 committee |

### Decision 四结论

中途门禁可收窄结论空间（倾向 / 只能落到 `research` | `wait` | `reject`）；**正式枚举写入仍在 Decision 阶段与 Decision Log**（见 ARCHITECTURE.md「Decision 四结论」）。不得在跳过 Decision 的情况下落盘四结论。

四结论的正式枚举、门禁细则与硬门槛的权威定义见：

- [`skills/decision/SKILL.md`](skills/decision/SKILL.md) — 四结论（`act` / `wait` / `reject` / `research`）、`act` 硬门禁 5 条、镜像测试、价格区间绑定
- [`prompts/review_pipeline.md`](prompts/review_pipeline.md) — 阶段契约表、停止条件、Committee 触发

此处只保留索引提示：`act` 仅 Decision 阶段可讨论，须同时满足 IPS（Investment Policy Statement，投资政策）状态为 `active`、有效目标配置、时效内动态事实、Validation/Risk/Challenge/Committee 门禁通过、适用例外已批准；缺一项则落到 `wait` / `reject` / `research`。`act` 仅为结论，不是交易授权。用户授权须本轮明确、范围绑定；用户自行在券商操作，本系统不接入交易、不代下单。

## Skills

涉及买入、卖出、持有、定投、调仓或产品排序时，按 Review Pipeline 顺序：Research → … → Documentation。深度分级见 OPERATIONS.md「审查深度分级」。

| 阶段 | Skill 路径 | Pipeline | 何时加载 |
|------|------------|:--------:|----------|
| Research | [skills/research/SKILL.md](skills/research/SKILL.md) | 1 | 投资研究、产品发现、数据更新、来源查找 |
| Validation | [skills/validation/SKILL.md](skills/validation/SKILL.md) | 2 | 验证研究结果、数据库更新、评分输入或报告 |
| Modeling | [skills/modeling/SKILL.md](skills/modeling/SKILL.md) | 3 | 设计评分、比较候选产品、分析组合数据 |
| Reasoning | [skills/reasoning/SKILL.md](skills/reasoning/SKILL.md) | 4 | 分析「为什么、为何现在、为何该方案」 |
| Risk | [skills/risk/SKILL.md](skills/risk/SKILL.md) | 5 | 任何投资行动、产品审核或组合复核 |
| Challenge | [skills/challenge/SKILL.md](skills/challenge/SKILL.md) | 6 | 形成建议或重大个人决策前 |
| Committee（辅助） | [skills/committee/SKILL.md](skills/committee/SKILL.md) | 3–6 编排 | 新资产暴露、首次买入、改目标、重大再平衡或 ETF 排序；编排第 3–6 阶段，不新增 Pipeline 阶段 |
| Decision | [skills/decision/SKILL.md](skills/decision/SKILL.md) | 7 | 完成审查流水线或比较行动方案 |
| Documentation | [skills/documentation/SKILL.md](skills/documentation/SKILL.md) | 8 | 生成报告、更新知识库或记录决策 |

## 编辑规则

修改仓库文件时遵守：

1. **范围约束**：只改与任务直接相关的文件。不改无关的 Skill、Prompt、模板或历史记录。
2. **引用同步**：修改 `prompts/` 或 `skills/` 中的阶段顺序、门禁逻辑、Skill 职责后，检查本文件索引表和 [`.cursor/rules/`](.cursor/rules/) 引用路径是否受影响。
3. **演示隔离**：演示工件只放在 `reports/demo/`、`decision_log/demo/`、`screening/runs/demo/`、`raw_material/demo/`。演示数据不得标记为 `scope: production`；`demo_only` 不得进生产 Decision。
4. **版本留痕**：模型和数据规则变更时保留旧版本；历史 Decision 继续引用当时的模型版本和输入快照，不追溯修改。
5. **不覆盖旧记录**：数据更正使用追加模式（`supersedes_record_id` + `correction_reason`），不直接修改旧行。
