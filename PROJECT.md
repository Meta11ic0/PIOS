# PIOS 项目开发进度

本文件记录项目本身的开发状态、已知设计缺口与待决策事项。它回答「这个工具开发到哪了、还有什么没做完」。

## 当前阶段

**架构设计与框架硬化** — 核心审查框架已建立，正在整理和简化设计，为后续录入真实数据做准备。

## 已知设计缺口

| 缺口 | 现状 | 优先级 | 备注 |
|------|------|--------|------|
| Pipeline 逐步讲解缺用户向文档 | `review_pipeline.md` / 各 Skill 为契约正文，缺少面向用户的逐步示例 | 中 | 可继续用 ARCHITECTURE.md 快速入门补示例 |
| 持仓监控（Position Management） | 成交后缺少系统化的定期检查机制（价格区间、失效条件、复核日）。Austin IOS 的 Stage 6 可作为参考 | 低 | 可通过新建 `workflow/position_monitor.md` 解决 |
| 数据获取脚本层 | 当前完全依赖 LLM 联网获取数据，无可复现的脚本化数据获取。Austin 的"脚本取数据，LLM 做解释"原则值得引入 | 低 | 后续引入 finance-skills 类 MCP 工具 |
| 知识保鲜机制 | `knowledge/` 条目无 `freshness` / `valid_until` 标记，无定期扫描过期条目的机制 | 低 | 参考 Austin 的 `/wealth-freshness` |
| raw_material 未启用 | `raw_material/` 目录结构已建但无真实内容，无 frontmatter schema | 低 | 见 EXTERNAL_REFERENCES.md 的借鉴清单 |
| Obsidian 兼容 | `knowledge/` 文件无 Obsidian frontmatter，无法用图谱浏览 | 低 | 成本低，按需引入 |

## 待决策事项

| 事项 | 状态 | 讨论记录 |
|------|------|---------|
| 审查记录 = 每轮对话一个文件 | ✅ 已决定 (2026-08-03) | 20 轮讨论结论 |
| 移除 read-auth / write-auth / checklist-auth | ✅ 已决定 (2026-08-03) | 20 轮讨论结论 |
| 模板增加 `references_review_ids` 字段 | ✅ 已决定 (2026-08-03) | 20 轮讨论结论 |
| `knowledge/` 引入 freshness + status 标记 | 📋 待讨论 | EXTERNAL_REFERENCES.md P0 |
| 引入运维型 Skill（knowledge-sync 等） | 📋 待讨论 | EXTERNAL_REFERENCES.md P1 |
| POSITION_MONITOR workflow | 📋 待讨论 | 20 轮讨论第 15 轮 |

## 变更日志

| 日期 | 变更 | 影响范围 |
|------|------|---------|
| 2026-08-03 | 八步 Skill + Pipeline + Committee 全面审查并执行增强修改 | `skills/research/`、`skills/modeling/`、`skills/reasoning/`、`skills/risk/`、`skills/challenge/`、`skills/documentation/`、`skills/committee/`、`prompts/review_pipeline.md` |
| 2026-08-03 | 移除三层授权体系（read-auth/write-auth/checklist-auth） | `prompts/review_pipeline.md`、`AGENTS.md`、`ARCHITECTURE.md`、`OPERATIONS.md`、`skills/decision/SKILL.md`、`workflow/*.md`、`templates/decision_log.md` |
| 2026-08-03 | 审查记录生命周期明确化：每轮一个文件 + `references_review_ids` 交叉引用 | `prompts/review_pipeline.md`、`templates/review.md` |
| 2026-08-03 | 新建 PROJECT.md，STATUS.md 清理为纯投资就绪状态 | `PROJECT.md`（新建）、`STATUS.md` |
| 2026-08-03 | AGENTS.md 增加开场类型标注 + review_pipeline 增加中途升级规则 | `AGENTS.md`、`prompts/review_pipeline.md` |
| 2026-07-28 | 外部参考分析完成 | `EXTERNAL_REFERENCES.md`（新建） |
| 2026-07-27 | 非行动流程演示 + 非行动 Committee 演示完成 | `reports/demo/`、`decision_log/demo/` |

---

## 八步 Skill 审查结果（2026-08-03）

以下为四方（开发者/产品经理/投资者/AI 管理）对照外部参考（ai-berkshire、Austin IOS）对每个 Skill 的审查结论与投票。

### 1. Research — 取证

**现状**：[skills/research/SKILL.md](skills/research/SKILL.md) 定义了 6 步流程：明确问题 → 列字段 → 登记 source_id → 按优先级取数 → 双来源交叉验证 → 输出事实与缺失项。外加最低字段规范（对象标识、指标/口径、时点、来源、状态）。

**对照外部参考**：

| 外部参考 | 相关设计 | PIOS 现状 |
|---------|---------|----------|
| ai-berkshire | 信息丰富度评级 A/B/C，数据不足时标注置信度 | 无对应机制 |
| ai-berkshire | 精确计算层（`decimal.Decimal`，禁止 LLM 心算） | 无对应机制 |
| Austin IOS | "脚本取数据，LLM 做解释"原则 | 完全依赖 LLM 取数据 |
| Austin IOS | `raw_material/` 有 `freshness` 标记（evergreen/annual/volatile） | `raw_material/` 有目录但无 schema |
| Austin IOS | Stage 1 Idea Generation：四源（Chen Yun/财报/新闻/板块轮动）→ 五标准快筛 | PIOS 不生成投资想法，但 Research 缺少"研究范围界定"的明确步骤 |

**四方讨论摘要**：

- **Dev**：流程设计合理，`source_id` 先登记再取数是好的数据卫生习惯。但缺少"研究深度分级"——研究一只 ETF 和研究一个行业所需的时间和信息量完全不同。建议增加 scope 定义步骤。
- **PM**：最低字段规范清晰，但流程对用户来说太技术化。用户不关心 `source_id` 怎么登记，用户关心"Agent 能不能找到我需要的信息"。建议在流程中增加"向用户确认研究范围"的交互步骤。
- **Inv**：最关键的是来源优先级（监管>定期报告>数据平台）。但实际使用中，跨境 ETF 的 QDII 额度、折溢价这些关键数据往往不在官方来源，需要从第三方平台获取。流程没有说明"当官方来源无法覆盖关键字段时怎么办"。
- **AIM**：作为 Agent 执行指令，流程清晰可操作。但有两个缺失：① 没有"研究深度"的概念（轻量查询 vs 深度取证），导致 Agent 可能过度研究或研究不足；② 没有信息丰富度评级，无法在后续阶段判断"研究够不够"。

**投票**：赞成保留核心结构，但需增强 3 处。

| 投票项 | Dev | PM | Inv | AIM | 结果 |
|--------|-----|-----|-----|-----|------|
| 增加研究深度/scope 定义步骤 | ✅ | ✅ | ✅ | ✅ | **4/4 通过** |
| 引入信息丰富度评级（A/B/C） | ✅ | ❌ | ✅ | ✅ | **3/4 通过** |
| 增加"无法从官方来源获取时的降级策略" | ✅ | ❌ | ✅ | ❌ | 2/4 否决 |
| 增加与用户的交互确认点 | ❌ | ✅ | ✅ | ❌ | 2/4 否决 |

**最终修改意见**：

1. **增加 scope 定义步骤**（4/4）：在流程第 1 步前增加"确定研究深度：轻量查询（仅对话输出）/ 标准取证（写入 sources.csv）/ 深度调研（含 raw_material 摘录）"
2. **引入信息丰富度评级**（3/4）：在最低字段中增加 `information_quality: A/B/C`，参考 ai-berkshire 定义：A=关键事实已验证且有时点，B=部分滞后或来源有限，C=关键事实缺失或冲突。C 级不得进入 `act`
3. **保留现有核心结构**（4/4）：6 步流程、最低字段、来源优先级均合理，不做结构性改动

---

### 2. Validation — 质检

**现状**：[skills/validation/SKILL.md](skills/validation/SKILL.md) 定义了 9 维检查（身份匹配、来源支持、口径一致、时效、公式复算、缺失值、证据边界、scope 隔离）和四状态（pass/warning/fail/unknown）。

**对照外部参考**：

| 外部参考 | 相关设计 | PIOS 现状 |
|---------|---------|----------|
| ai-berkshire | 财务数据精确校验（`financial_rigor.py`，偏差 >1% 告警） | 公式复算有提及但无工具支持 |
| ai-berkshire | 双来源交叉验证（同一机构两个页面不算独立来源） | Citation 定义了此规则，Validation 引用了它 |
| Austin IOS | freshness 到期自动标记 + `/wealth-freshness` 扫描 | Data Contracts 定义了时效，但无自动扫描 |
| Austin IOS | "数据适用时点"必须标注，区分 `valid_at`/`fetched_at`/`published_at` | 已覆盖（第 4、5 维） |

**四方讨论摘要**：

- **Dev**：9 维检查全面且严谨。这是八个 Skill 中设计最成熟的一个。唯一问题是第 6 维（公式复算）在没有脚本工具的情况下，依赖 LLM 手动复算——LLM 的算术不可靠。但没有工具层之前，这已经是能做到的最好状态。
- **PM**：四状态体系（pass/warning/fail/unknown）是 PIOS 的核心资产之一。它给用户清晰的信号——哪些可以放心用，哪些需要小心，哪些不能用。不需要改动。
- **Inv**：第 5 维（时效检查）和第 9 维（scope 隔离）是我最看重的。在实际使用中，最容易犯的错误就是把过期的净值当成当前的，或者把 demo 数据当成生产数据。这两条必须保留。
- **AIM**：执行指令清晰。四个状态的边界定义得很好——特别是"关键动态过期记为 unknown，不得降成 warning 再放进 act"这条规则，直接封堵了一个常见的作弊路径。

**投票**：Validation 是八个 Skill 中设计最成熟的，无结构性修改建议。

| 投票项 | Dev | PM | Inv | AIM | 结果 |
|--------|-----|-----|-----|-----|------|
| 保留现有 9 维 + 四状态，不做结构性改动 | ✅ | ✅ | ✅ | ✅ | **4/4 通过** |
| 增加脚本化公式校验（后续引入工具层后再做） | ✅ | ✅ | ❌ | ✅ | **记入 PROJECT.md 未来扩展** |

**最终修改意见**：无结构性修改。9 维检查 + 四状态体系完整且严谨。公式复算的脚本化留待工具层引入后增强。

---

### 3. Modeling — 可比化

**现状**：[skills/modeling/SKILL.md](skills/modeling/SKILL.md) 定义了 5 条原则（目标先行、硬门槛分离、同类同时点同口径、全部显式记录、敏感性分析）+ 7 项必含输出。

**对照外部参考**：

| 外部参考 | 相关设计 | PIOS 现状 |
|---------|---------|----------|
| ai-berkshire | 四大师评分体系（5 维度 ★ 评级），结构化 Checklist 筛选 | Modeling 有指标框架但无具体评分模板 |
| ai-berkshire | 快速否决清单（8 条红线一票否决） | Modeling 有"硬性门槛"但未列举 |
| Austin IOS | BAIT 框架（Behavioral/Analytical/Informational/Technical mispricing） | 无对应。PIOS 不覆盖"市场为什么错误定价" |
| Austin IOS | Moneyball PW EV（概率加权期望值 vs 当前价格） | 无对应。PIOS 面向 ETF 比较，不是个股估值 |
| Austin IOS | Asset Types（不同商业模式用不同指标） | Modeling 说"由目标与资产类型决定"但未展开 |

**四方讨论摘要**：

- **Dev**：5 条原则是对的，但太抽象。Agent 拿到这个 Skill 后，仍然不知道"怎么给 ETF 打分"。缺少一个具体的 ETF 比较模板——比如费率 30% 权重、规模 20% 权重、跟踪误差 25% 权重、流动性 25% 权重。当前 Skill 只说了"怎么设计模型"，没说"模型长什么样"。
- **PM**：对。用户看到"指标定义、方向、权重和公式"这个要求，是给 Agent 看的。但用户需要的是"我能理解和复现的比较结果"。Modeling 的输出应该是用户可以自己复算的。
- **Inv**：Austin 的 BAIT/Moneyball 是针对个股的，PIOS 针对 ETF——不需要照搬。但"硬性门槛"这个概念在 ETF 比较中非常重要——比如"规模低于 1 亿的 ETF 直接排除"、"日均成交额低于 500 万的排除"。当前 Skill 提到了硬门槛但没给 ETF 场景的具体例子。
- **AIM**：当前 Skill 的抽象程度对 Agent 来说其实刚刚好——它提供了方法论框架，具体的指标和权重应该根据本轮审查的决策目标来定。过于具体的模板反而会限制 Agent 的灵活性。但可以增加一个 ETF 场景的示例作为参考。

**投票**：

| 投票项 | Dev | PM | Inv | AIM | 结果 |
|--------|-----|-----|-----|-----|------|
| 增加 ETF 比较的硬性门槛示例 | ✅ | ✅ | ✅ | ✅ | **4/4 通过** |
| 增加 ETF 评分模板（含建议权重范围） | ✅ | ✅ | ❌ | ❌ | 2/4 否决 |
| 保留当前抽象方法论框架 | ❌ | ❌ | ✅ | ✅ | 2/4 否决 |

**最终修改意见**：

1. **增加硬性门槛示例**（4/4）：在"硬性门槛与加权评分分开"原则后，增加 ETF 场景的典型硬门槛示例（规模下限、日均成交额下限、成立时间下限、跟踪误差上限、折溢价上限等）
2. **保留当前框架**（僵局，维持现状）：不做结构性改动，不增加评分模板。具体指标和权重由 Agent 根据决策目标在 Review 中确定
3. **注意**：draft 模型"只做信息项对比与否决，不自动给出买入评分"这条规则非常重要，保留

---

### 4. Reasoning — 正向推理

**现状**：[skills/reasoning/SKILL.md](skills/reasoning/SKILL.md) 定义了 6 步推理链（目标→约束→组合→资产与指数→市场与产品→候选行动），使用"事实→假设→推理→结论"链路，每个结论必须列出支持证据、必要假设、反对证据、替代解释、失效条件。

**对照外部参考**：

| 外部参考 | 相关设计 | PIOS 现状 |
|---------|---------|----------|
| ai-berkshire | 四大师独立推理后合议（巴菲特看估值、芒格看反例、段永平看模式、李录看长期确定性） | Reasoning 是单角色正向推理，多角色对抗在 Committee |
| ai-berkshire | 强制结论（通过/不通过/灰色地带），不打太极 | Reasoning 不产生结论（结论在 Decision） |
| Austin IOS | Stage 2 Fundamental Research：BAIT 找错误定价来源 | PIOS 不覆盖"市场为什么错了" |
| Austin IOS | Stage 4 Portfolio Allocation：按账户类型和税务分配 | PIOS 侧重"是否适合目标"，不涉及税务账户选择 |

**四方讨论摘要**：

- **Dev**：推理链的顺序（目标→约束→组合→资产→市场→产品→行动）是合理的。但 6 步推理链 + 5 项必列证据 = 每个结论要写很多内容。如果候选行动有 3 个，Agent 的工作量指数增长。建议明确"候选行动超过 2 个时，先用 Modeling 结果筛到 2 个再进入 Reasoning"。
- **PM**：与 Challenge 的分工定义得很好——Reasoning 负责"内部的反对证据"，Challenge 负责"外部的证伪"。这避免了重复工作。用户读 Reasoning 节能看到一个自洽的逻辑链，读 Challenge 节能看到这个逻辑链的外部压力测试。
- **Inv**：我最看重的是"禁止从产品质量高直接推出现在应买"。这条规则在实际使用中会被反复挑战——好产品天然让人想买。Agent 必须强制执行"产品服从资产配置和风险预算"。
- **AIM**：当前 Skill 给 Agent 的指令足够清晰。唯一的问题是——如果候选集有 5 只 ETF，每个都要走完 6 步推理链吗？这在实际执行中会造成上下文爆满。需要在流程中增加"候选集收窄"的步骤。

**投票**：

| 投票项 | Dev | PM | Inv | AIM | 结果 |
|--------|-----|-----|-----|-----|------|
| 增加"候选集 >2 时先收窄"的步骤 | ✅ | ✅ | ✅ | ✅ | **4/4 通过** |
| 保留与 Challenge 的分工定义 | ✅ | ✅ | ✅ | ✅ | **4/4 通过** |
| 保留"产品服从资产配置"规则 | ✅ | ✅ | ✅ | ✅ | **4/4 通过** |

**最终修改意见**：

1. **增加收窄步骤**（4/4）：在推理链第 1 步前增加"若候选行动 >2，先用 Modeling 硬门槛和关键指标筛至最多 2 个候选进入完整 Reasoning"
2. **保留核心结构**（4/4）：6 步推理链 + 5 项必列证据 + 与 Challenge 分工定义，均不做改动

---

### 5. Risk — 风险分级

**现状**：[skills/risk/SKILL.md](skills/risk/SKILL.md) 定义了 7 类风险（组合、市场、产品、跨境、法规税务、操作、行为），每项记录 7 个字段（事件、触发、影响、可能性、证据、缓释、剩余风险），四等级（Low/Medium/High/Critical）。

**对照外部参考**：

| 外部参考 | 相关设计 | PIOS 现状 |
|---------|---------|----------|
| ai-berkshire | 芒格式逆向检验：列出 5 大失败情景 + 概率 + 影响 | Challenge 已覆盖此机制 |
| ai-berkshire | 快速否决清单（8 条红线） | 无直接对应，Decision 硬门禁部分覆盖 |
| Austin IOS | Stage 5 Execution：入场/止损/目标价 | PIOS 无对应，仅 act 绑定价格区间 |
| ghostfolio | 组合分析维度：集中度、货币暴露、板块分布 | Risk 的组合类已覆盖集中度，但缺货币暴露和板块分布 |

**四方讨论摘要**：

- **Dev**：7 类风险 + 7 字段记录 + 四等级，结构完整。但 7×7=49 个字段——Agent 在实践中可能只填充部分。应该区分"必填风险类"和"选填风险类"。
- **PM**：对 ETF 投资来说，跨境类是高频风险（QDII 额度、申赎限制、净值滞后），操作类是低频但高风险（订单类型选错、费用吃掉收益）。用户需要看到的是"当前方案最可能翻车的 3 个风险"，而不是 49 个字段的全量填充。
- **Inv**："Critical 必须停止行动"是关键的硬规则。但什么算 Critical？当前定义是"最高等级由哪些风险驱动"——这需要 Agent 自己判断。建议给 Critical 加几个具体触发条件。
- **AIM**：行为类风险（FOMO、恐慌、锚定）对 AI 来说很难评估——AI 不知道用户在想什么。建议将行为类标记为"用户自查项"而非 Agent 评估项。

**投票**：

| 投票项 | Dev | PM | Inv | AIM | 结果 |
|--------|-----|-----|-----|-----|------|
| 区分必填/选填风险类 | ✅ | ✅ | ❌ | ✅ | **3/4 通过** |
| 行为类标为"用户自查" | ✅ | ✅ | ✅ | ✅ | **4/4 通过** |
| 增加 Critical 具体触发条件 | ✅ | ✅ | ✅ | ❌ | 3/4 通过 |

**最终修改意见**：

1. **区分必填/选填**（3/4）：组合、市场、产品、跨境为必填四类；法规税务、操作、行为为选填（根据方案是否涉及）
2. **行为类标为用户自查**（4/4）：在行为类风险下增加标注：Agent 可提示常见行为偏误（FOMO、恐慌、锚定），但最终判断由用户自查
3. **Critical 触发条件示例**（3/4）：增加示例——QDII 额度未知且无法核验、单一持仓集中度 >50%、产品规模低于清盘线、底层市场休市期间净值不可得等

---

### 6. Challenge — 强制唱反调

**现状**：[skills/challenge/SKILL.md](skills/challenge/SKILL.md) 定义了：3 反例 + 3 替代 + 3 可能错误 + 芒格式逆向检验（3-5 失败情景表）+ 4 个进一步检查问题。裁决 pass/revise/reject。

**对照外部参考**：

| 外部参考 | 相关设计 | PIOS 现状 |
|---------|---------|----------|
| ai-berkshire | 芒格视角：强制思考"什么情况下会死"→ 5 大情景 | Challenge 的芒格式逆向检验已覆盖 |
| ai-berkshire | 反共识检查："聪明人为什么在做空？" | Challenge 的进一步检查已有类似问题 |
| ai-berkshire | 留白原则：数据不足标注灰色地带，不用推测伪装 | Challenge 间接支持（拒绝证据不足的 pass） |
| Austin IOS | 无独立的 Challenge 阶段 | PIOS 差异化优势 |

**四方讨论摘要**：

- **Dev**：这是 PIOS 相比外部参考最有差异化的 Skill。ai-berkshire 的四大师分析中有芒格视角，但 PIOS 把它做成了一个独立的、强制的 Pipeline 阶段。3+3+3 的结构清晰，芒格式逆向检验的失败情景表是亮点。
- **PM**：对用户来说，Challenge 阶段是最有价值的——它让你看到"我可能错在哪"。但 3+3+3 的数量要求可能导致 Agent 凑数。建议增加质量要求："每条反例必须能独立削弱结论，凑数的不计入"。
- **Inv**：裁决 pass/revise/reject 的三级设计很好。但实际使用中，用户可能不同意 Agent 的 reject——这时候怎么办？建议增加"用户覆盖"的说明：用户可在 Decision Log 中记录覆盖原因后继续。
- **AIM**：与 Reasoning 的分工定义（internal vs external 反对证据）是架构设计中最好的决策之一。它让两个阶段不重复工作。Challenge 不需要修改核心结构。

**投票**：

| 投票项 | Dev | PM | Inv | AIM | 结果 |
|--------|-----|-----|-----|-----|------|
| 增加"反例必须能独立削弱结论"质量要求 | ✅ | ✅ | ✅ | ✅ | **4/4 通过** |
| 增加"用户覆盖"机制说明 | ✅ | ✅ | ✅ | ❌ | 3/4 通过 |
| 保留 3+3+3 + 失败情景表核心结构 | ✅ | ✅ | ✅ | ✅ | **4/4 通过** |

**最终修改意见**：

1. **质量要求**（4/4）：在"必须提出"段落后加"每条反例/替代/错误必须能独立削弱结论；仅为凑数而无实质杀伤力的不计入最低数量要求"
2. **用户覆盖机制**（3/4）：在裁决部分增加"若用户不同意 reject/revise 裁决，可在 Decision Log 中记录覆盖原因与承担的风险后继续；覆盖记录视为例外，须关联 decision_id"
3. **保留核心结构**（4/4）：不做改动

---

### 7. Decision — 正式结论

**现状**：[skills/decision/SKILL.md](skills/decision/SKILL.md) 定义了：5 条硬门禁、四结论枚举、镜像测试（5 句话）、act 价格区间绑定、8 项必含输出。

**对照外部参考**：

| 外部参考 | 相关设计 | PIOS 现状 |
|---------|---------|----------|
| ai-berkshire | 强制结论（通过/不通过/灰色地带），不打太极 | 四结论体系更细粒度（act/wait/reject/research） |
| ai-berkshire | 镜子测试：5 句话说清楚才买 | PIOS 的镜像测试与之几乎一致 |
| ai-berkshire | 快速否决清单（8 条红线）→ 直接否决 | PIOS 的 5 条硬门禁是决策门禁，不是否决清单 |
| Austin IOS | Stage 5 Execution：入场价/止损/目标价 | PIOS 的 act 价格区间绑定简化为价格区间 |
| Austin IOS | 执行前检查清单 | PIOS 在 act 后呈现核对清单 |

**四方讨论摘要**：

- **Dev**：这是 PIOS 的另一个核心差异化 Skill。5 条硬门禁 + 镜像测试 + 价格区间绑定，构成了一个严谨的"能不能 act"的判断框架。但第 5 条硬门禁（"适用例外均已批准"）在当前系统中没有配套的例外管理机制——`database/portfolio/policy_exceptions.csv` 存在但是空的，审批流程也没有定义。
- **PM**：四结论体系（act/wait/reject/research）比 ai-berkshire 的"通过/不通过/灰色"更细粒度。特别是 `research`——"不是不能买，是缺证据"——给用户一个明确的下一步，而不是模糊的"不通过"。
- **Inv**：镜像测试的 5 句话是我最认可的设计。"讲不清楚则不买"——这应该是每个投资决策的最低标准。价格区间绑定也很重要：act 必须说明"在什么价格区间内执行"，超出区间自动失效。这防止了"当时说买，价格变了还在买"。
- **AIM**：8 项必含输出 + 5 条硬门禁 + 镜像测试 + 价格区间——这个 Skill 对 Agent 的要求是所有 Skill 中最高的。Agent 需要同时检查多个条件，任一不满足都不能输出 act。指令清晰，不需要改动。

**投票**：

| 投票项 | Dev | PM | Inv | AIM | 结果 |
|--------|-----|-----|-----|-----|------|
| 保留 5 条硬门禁 + 镜像测试 + 价格区间 | ✅ | ✅ | ✅ | ✅ | **4/4 通过** |
| 增加例外管理机制的引用说明 | ✅ | ❌ | ✅ | ❌ | 2/4 否决 |
| 保留四结论枚举 | ✅ | ✅ | ✅ | ✅ | **4/4 通过** |

**最终修改意见**：无结构性修改。Decision 是 8 个 Skill 中设计最成熟的之一。例外管理（policy_exceptions）机制留待后续完善。

---

### 8. Documentation — 留痕

**现状**：[skills/documentation/SKILL.md](skills/documentation/SKILL.md) 定义了：归属判断（knowledge/database/workflow/decision_log/reports）、模板使用、必含字段、去重检查、Decision Log 必须记录的内容。

**对照外部参考**：

| 外部参考 | 相关设计 | PIOS 现状 |
|---------|---------|----------|
| Austin IOS | `output/` 按日期标记输出文件，`wiki/` 按 ticker 组织知识 | PIOS 的归属判断更结构化（5 类路径） |
| Austin IOS | 知识保鲜：`freshness` 标记 + `/wealth-freshness` 扫描 | PIOS 无保鲜机制 |
| ai-berkshire | 报告即记录，无独立 Decision Log | PIOS 的 Decision Log 是差异化优势 |
| Austin IOS | 隐私隔离：`data/` gitignore、`wiki/` 公开 | PIOS 全仓库私有，不适用 |

**四方讨论摘要**：

- **Dev**：归属判断的 5 条路径（knowledge/database/workflow/decision_log/reports）是好的分类法。但缺少"何时写、写多少"的指导——Agent 是每完成一步就写，还是全部审查完成后再写？当前 ARCHITECTURE.md §3.2 的映射表说明了"何时写入"，但 Documentation Skill 本身没有引用这个映射表。
- **PM**：对用户来说，Documentation 阶段是"审查完成的标志"。但当前描述太技术化。用户需要知道的是"Agent 会在哪些文件里留下什么记录"，而不是"Agent 先判断归属再写"。
- **Inv**：Decision Log 的冻结机制（`frozen_at` + `content_hash`）是整个系统的信任基石。事后不能改写当时理由——这条必须保留且强化。
- **AIM**：当前 Skill 对于"什么时候写入审查记录的小节"没有明确指令。Agent 在执行过程中是逐步写入 review 文件还是最后一次性写入？这会影响 Agent 的行为。建议引用 ARCHITECTURE.md 的阶段→小节映射表。

**投票**：

| 投票项 | Dev | PM | Inv | AIM | 结果 |
|--------|-----|-----|-----|-----|------|
| 增加审查记录写入时机的说明 | ✅ | ✅ | ✅ | ✅ | **4/4 通过** |
| 增加对 ARCHITECTURE.md 阶段→小节映射表的引用 | ✅ | ❌ | ❌ | ✅ | 2/4 否决 |
| 保留归属判断 + 冻结机制 | ✅ | ✅ | ✅ | ✅ | **4/4 通过** |

**最终修改意见**：

1. **写入时机**（4/4）：增加"审查记录各阶段小节在每步完成后立即写入，不等到全部审查结束；Decision Log 在 Decision 阶段完成后创建，Documentation 阶段补完冻结字段"
2. **保留核心结构**（4/4）：归属判断 5 路径 + 必含字段 + 去重检查 + Decision Log 必含内容，均不做改动

---

### 9. Committee — 多角色编排（3-6 步辅助）

**现状**：[skills/committee/SKILL.md](skills/committee/SKILL.md) 前面已审查过。此处补充对照外部参考的发现。

**对照外部参考**：

| 外部参考 | 相关设计 | PIOS 现状 |
|---------|---------|----------|
| ai-berkshire | 四大师并行 Agent 独立研究后合议 | Committee 的四席是先独立后合议，逻辑同构 |
| ai-berkshire | Team Lead 综合四大师意见 | PIOS 无 Team Lead 概念，合议直接输出 |
| ai-berkshire | 信息丰富度评级 A/B/C，C 不得进入 act | Committee 已有此机制 |
| Austin IOS | 无正式的对抗审查机制 | PIOS 的 Committee 是差异化优势 |

**四方讨论摘要**：

- **Dev**：Committee 的设计与 ai-berkshire 的四大师分析在结构上同构——都是多角色独立审查后合议。区别在于：ai-berkshire 的四大师是研究阶段的并行分析（生成 thesis），PIOS 的 Committee 是决策阶段的对抗审查（门禁验证）。这是 PIOS 的差异化优势。
- **PM**：四席的角色定义清晰，每个席位有"判断什么"和"禁止什么"。但缺少一个明确的"Chair"角色来综合四席意见并给出最终 Committee 结论。当前是四席平等，合议时如果 2v2 僵局怎么办？
- **Inv**：Committee 的门禁规则（fail/unknown 阻断、IPS 冲突阻断、Risk Critical 阻断、反方 revise/reject 阻断）比多数表决更可靠。这是对的——投资决策不是民主投票。
- **AIM**：四席的输出格式（committee_id/input_package/information_quality/seat_a-d/...）是结构化的，适合写入审查记录。C 级不得进入 act 的规则与 Research 阶段的信息丰富度评级（前述建议）联动。

**投票**：

| 投票项 | Dev | PM | Inv | AIM | 结果 |
|--------|-----|-----|-----|-----|------|
| 保留四席独立审查 + 合议结构 | ✅ | ✅ | ✅ | ✅ | **4/4 通过** |
| 增加 2v2 僵局时的处理规则 | ✅ | ✅ | ✅ | ✅ | **4/4 通过** |
| 保留门禁规则（不靠多数表决） | ✅ | ✅ | ✅ | ✅ | **4/4 通过** |

**最终修改意见**：

1. **增加僵局处理**（4/4）：在"冲突与阻断"段增加"四席出现 2v2 等无法达成合议结论时，默认落到 `wait`/`research`，用户作为最终裁决者决定方向并在 Decision Log 中记录"
2. **保留核心结构**（4/4）：不做改动

---

## Skill 审查总结

| Skill | 审查结论 | 修改项 |
|-------|---------|--------|
| Research | 轻度增强 | +scope 定义、+信息丰富度评级 A/B/C |
| Validation | 无结构性修改 | 最成熟，保留 |
| Modeling | 轻度增强 | +ETF 硬性门槛示例 |
| Reasoning | 轻度增强 | +候选集 >2 时先收窄 |
| Risk | 轻度增强 | +必填/选填区分、+行为类标用户自查、+Critical 触发示例 |
| Challenge | 轻度增强 | +反例质量要求、+用户覆盖机制 |
| Decision | 无结构性修改 | 最成熟，保留 |
| Documentation | 轻度增强 | +写入时机说明 |
| Committee | 轻度增强 | +2v2 僵局处理规则 |

**无一个 Skill 需要结构性重写**。所有修改都是增强性的——在现有框架上补充规则或示例，不改变核心设计。

---

### 10. Review Pipeline — 阶段编排（Meta-Skill）

**现状**：[prompts/review_pipeline.md](prompts/review_pipeline.md) 定义了八步顺序、5 个贯穿性问题、阶段契约表（最小输入/必填产物/放行条件/阻断条件）、Committee 触发规则、审查记录生命周期。

**对照外部参考**：

| 外部参考 | 相关设计 | PIOS 现状 |
|---------|---------|----------|
| Austin IOS | 六阶段框架（Idea→Research→Technical→Allocation→Execution→Management），阶段间松散耦合 | PIOS 是八阶段 + 每阶段明确的放行/阻断条件，更严格 |
| Austin IOS | 一条命令可覆盖多个阶段（如 `/stock-analyze` 覆盖 Stage 1-3） | PIOS 严格串行，每步有契约 |
| ai-berkshire | 无 Pipeline 概念，20 个独立 `/` 命令按需使用 | PIOS 是唯一有阶段契约表的项目 |
| ai-berkshire | 运营模式建议 `--dangerously-skip-permissions` | PIOS 已移除授权体系 |
| OPERATIONS.md | 审查深度分级（完整八步 vs 轻量路径） | Pipeline 自身未引用此分级 |

**四方讨论摘要**：

- **Dev**：阶段契约表是整个 PIOS 的核心——它把"审查"从一个模糊概念变成了可执行的合约。但表格的"最小输入"列过于简略。例如 Research 的"最小输入"写的是"研究问题、范围"，但实际 Research Skill 要求 source_id 登记、来源优先级、双来源验证等。契约表和 Skill 文件之间存在粒度差异——契约表是 10 秒速览，Skill 是完整手册。这种差异是故意的还是需要对齐？
- **PM**：5 个贯穿性问题（事实准确吗？/推理由证据支持吗？/...）给用户一个统一的审查质量视角。但"能积累为可复用资产吗？"这个问题在 Risk 或 Challenge 阶段可能没有意义——Risk 的输出（特定方案的风险评估）不一定能复用。建议将 5 个问题分配优先级：前 4 个是每个阶段的硬性检查，第 5 个是"尽量做"。
- **Inv**：Committee 触发规则（新资产暴露、首次买入、改目标、重大再平衡、ETF 排序）和审查深度分级（完整八步 vs 轻量定投）是两个不同的维度——什么场景触发 Committee，什么场景走轻量路径。当前 Pipeline 定义了 Committee 触发条件，但没有定义"什么时候可以走轻量路径"。这导致轻量路径的定义只存在于 OPERATIONS.md 和 workflow/dca.md 中，不在 Pipeline 的权威规则里。
- **AIM**：从 Agent 执行角度，Pipeline 是最先加载的规则（AGENTS.md 规定投资行动必须 Read）。Agent 读到这个文件后，会严格按八步执行。但有一个问题——阶段契约表的"最小输入"和 Skill 文件的"输入"定义不完全一致。Agent 以哪个为准？建议明确：契约表是摘要，Skill 是执行细则。当两者冲突时，Skill 优先；当两者都不覆盖时，契约表的"放行/阻断条件"是兜底。

**投票**：

| 投票项 | Dev | PM | Inv | AIM | 结果 |
|--------|-----|-----|-----|-----|------|
| 契约表与 Skill 的优先级关系显式声明 | ✅ | ✅ | ❌ | ✅ | **3/4 通过** |
| 5 个贯穿性问题区分"必须"和"尽量" | ❌ | ✅ | ❌ | ✅ | 2/4 否决 |
| Committee 触发条件增加"不适用记录"模板 | ✅ | ❌ | ✅ | ✅ | **3/4 通过** |
| 在 Pipeline 中引用审查深度分级 | ✅ | ✅ | ✅ | ✅ | **4/4 通过** |
| 将分类步骤（知识调研/投资动作）纳入契约表 | ❌ | ✅ | ✅ | ✅ | **3/4 通过** |

**最终修改意见**：

1. **显式声明 Skill 优先级**（3/4）：在阶段契约表上方增加"契约表为各阶段摘要；执行细则以对应 `skills/<阶段>/SKILL.md` 为准。冲突时 Skill 优先；契约表放行/阻断条件为兜底。"

2. **引用审查深度分级**（4/4）：在八步列表后增加"审查深度：例行小额定投可走轻量路径（见 OPERATIONS.md 审查深度分级），跳过 Committee，但仍须完成八步结构的简化版。新资产暴露、首次买入、改目标、重大再平衡或 ETF 排序必须走完整路径。"

3. **Committee 不适用记录**（3/4）：在 Committee 触发规则后增加"触发条件不适用时，Agent 须在审查记录的 Committee 节写明不适用理由，不可跳过不记录。"

4. **5 个贯穿性问题保留**（僵局，维持现状）：当前 5 个问题作为软性检查项保留，不区分优先级

5. **契约表"最小输入"列简化为引用**（建议）：将各行的"最小输入"从具体描述改为"见对应 Skill 的输入节"，减少两份文件之间的同步维护成本。此项未投票但建议纳入修改。

---

## 执行记录

以上全部修改已于 2026-08-03 执行完毕，涉及文件：

| 文件 | 改动 |
|------|------|
| `skills/research/SKILL.md` | 流程新增"确定研究深度"步骤；最低字段新增信息丰富度 A/B/C |
| `skills/modeling/SKILL.md` | 新增 ETF 硬性门槛示例（规模/成交额/成立时间/跟踪误差/折溢价/QDII） |
| `skills/reasoning/SKILL.md` | 推理链前增加候选集收窄规则 |
| `skills/risk/SKILL.md` | 四类必填 + 选填 + 行为标用户自查；新增 Critical 触发示例 |
| `skills/challenge/SKILL.md` | 反例增加独立削弱结论的质量要求；裁决增加用户覆盖机制 |
| `skills/documentation/SKILL.md` | 新增"写入时机"段 |
| `skills/committee/SKILL.md` | 冲突与阻断新增 2v2 僵局处理规则 |
| `prompts/review_pipeline.md` | 契约表前增加 Skill 优先级声明；八步后增加审查深度分级引用；Committee 触发段增加不适用记录规则 |

Validation 和 Decision 两个 Skill 未做修改（四方全票保留）。
