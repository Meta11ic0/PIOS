# PIOS — Agent 入口

仓库是个人投资决策操作系统：文件 + DD 流水线 + AI 辅助。目标是让每条判断事后可追溯——当时看见什么、为何这样选、何时该失效。

可执行规则正文只在 [`prompts/`](prompts/) 与 [`skills/`](skills/)。读完本文件不等于读完规则正文；动手前须 Read 对应正文。

[`workflow/`](workflow/) 只补充场景入口，不得放宽 Prompt/Skill 的停止条件。

## Read 清单

动手前列出本轮将 Read 的文件，并**显式标注本轮类型**：`[知识调研]` 或 `[投资动作 DD]`。涉及买入、卖出、持有、定投、调仓、产品排序任一即为投资动作 DD。涉及投资行动时同时列出将执行的 Pipeline 阶段。

Agent 可直接读写仓库文件（推荐用户使用 git 进行文件管理；Agent 在没有收到明确指令之前不会执行 git 操作），每次写入后在对话中明确告知改了什么；用户通过 `git diff` 审核变更。本系统不接入券商、不代下单。

清单至少覆盖：

- 本文件
- 下节规定的 Prompt
- 投资行动时：将用到的 `skills/<name>/SKILL.md`；新资产暴露、首次买入、改目标、重大再平衡或 产品排序时含 `skills/committee/SKILL.md`
- 有场景入口时：对应 `workflow/*.md`

未按触发条件加载对应 Prompt/Skill 时，不得推进该结论或写入。

## 质量底线

以下在每次会话中有效，违反即停止：

1. **日期基线**：每轮会话开始时执行 `date` 确认日期。所有动态事实须标注适用时点；不得把过期数据表述为当前事实。
2. **双来源核验**：关键动态事实至少两个独立来源；同一机构两个页面不算独立来源。
3. **显式标注不确定性**：低置信度结论、不完整数据、来源缺口、无法核验的推断须显式标注，不得用模糊措辞掩盖。
4. **时效阻断**：关键动态字段超过 [`data_contracts.md`](database/data_contracts.md) 最大允许时效时记为 `unknown`，不得用 `warning` 放行后进入 `act`。
5. **范围隔离**：`demo_only`、`archive`、`*/demo/` 目录数据不得进入生产 Decision 输入。只有 `scope: production` 且 `verification_status: verified` 的事实才能支撑 `act`。
6. **免责与交易边界**：不构成投资建议。不接入券商、不自动下单、不代下单、不设计或接入券商交易 API。`act` 仅为结论，不是交易授权。用户自行在券商操作。
7. **停止条件**：关键数据缺失、来源冲突未解决、或风险超出约束时，暂停并输出 `research` / `wait` / `reject`，不得跳过问题继续推荐。

细则见 [`citation.md`](prompts/citation.md)、[`dd_pipeline.md`](prompts/dd_pipeline.md)、[`data_contracts.md`](database/data_contracts.md)。

## Prompt 加载

Prompt 是常驻规则。阶段顺序在 [`dd_pipeline.md`](prompts/dd_pipeline.md) 与各 Skill。得出结论后不要再跑后置 Prompt 校验。

### 加载时机

| 时机 | Read | 说明 |
|------|------|------|
| 会话开始 | [`system.md`](prompts/system.md)、[`answer_style.md`](prompts/answer_style.md)、[`citation.md`](prompts/citation.md) | 必读 |
| 本轮涉及买入、卖出、持有、定投、调仓或产品排序 | 同时 Read [`dd_pipeline.md`](prompts/dd_pipeline.md) | 严格按全文执行八步与停止条件 |
| 编写或改写仓库说明 | 再 Read [`docs_style.md`](prompts/docs_style.md) | 纯投资行动且不改文档时不必读 |
| 投资行动各阶段 | 按阶段 Read 对应 `skills/*/SKILL.md` | Committee 触发场景再读 committee |

### Decision 四结论

中途门禁可收窄结论空间；正式枚举写入仍在 Decision 阶段与 Decision Log。不得在跳过 Decision 的情况下落盘四结论。

权威定义见：

- [`skills/decision/SKILL.md`](skills/decision/SKILL.md) — 四结论、硬门禁 5 条、镜像测试、价格区间绑定
- [`prompts/dd_pipeline.md`](prompts/dd_pipeline.md) — 阶段契约表、停止条件、Committee 触发

索引提示：`act` 仅 Decision 阶段可讨论，须同时满足 IPS 状态为 `active`、有效目标配置、时效内动态事实、Validation/Risk/Challenge/Committee 门禁通过、适用例外已批准；缺一项则落到 `wait` / `reject` / `research`。`act` 仅为结论，不是交易授权。

## Skills

涉及买入、卖出、持有、定投、调仓或产品排序时，按 DD Pipeline 顺序：Research → … → Documentation。

| 阶段 | Skill 路径 | Pipeline | 何时加载 |
|------|------------|:--------:|----------|
| Research | [skills/research/SKILL.md](skills/research/SKILL.md) | 1 | 投资研究、产品发现、数据更新、来源查找 |
| Validation | [skills/validation/SKILL.md](skills/validation/SKILL.md) | 2 | 验证研究结果、数据库更新、评分输入或报告 |
| Modeling | [skills/modeling/SKILL.md](skills/modeling/SKILL.md) | 3 | 设计评分、比较候选产品、分析组合数据 |
| Reasoning | [skills/reasoning/SKILL.md](skills/reasoning/SKILL.md) | 4 | 分析为什么、为何现在、为何该方案 |
| Risk | [skills/risk/SKILL.md](skills/risk/SKILL.md) | 5 | 任何投资行动、产品审核或组合复核 |
| Challenge | [skills/challenge/SKILL.md](skills/challenge/SKILL.md) | 6 | 形成建议或重大个人决策前 |
| Committee（辅助） | [skills/committee/SKILL.md](skills/committee/SKILL.md) | 3–6 编排 | 新资产暴露、首次买入、改目标、重大再平衡或 产品排序 |
| Decision | [skills/decision/SKILL.md](skills/decision/SKILL.md) | 7 | 完成 DD 流水线或比较行动方案 |
| Documentation | [skills/documentation/SKILL.md](skills/documentation/SKILL.md) | 8 | 生成报告、更新知识库或记录决策 |

## 编辑规则

修改仓库文件时：

1. **范围约束**：只改与任务直接相关的文件。
2. **引用同步**：修改 `prompts/` 或 `skills/` 后，检查本文件索引表和 `.cursor/rules/` 引用路径。
3. **演示隔离**：演示工件只放 `reports/demo/`、`decision_log/demo/`、`screening/runs/demo/`、`raw_material/demo/`。演示数据不得标记 `scope: production`。
4. **版本留痕**：模型和数据规则变更时保留旧版本；历史 Decision 继续引用当时的模型版本和输入快照。
5. **不覆盖旧记录**：数据更正使用追加模式（`supersedes_record_id` + `correction_reason`），不直接修改旧行。
