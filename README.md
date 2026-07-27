# PIOS

这个仓库保存投资研究、组合数据、审查记录和决策日志。当前靠人工维护文件，并由 Claude Code 或 Cursor 按项目规则协助研究与复核。

## 三份入口文档是地图，不是规则全文

| 文档 | 用途 |
|---|---|
| [AGENTS.md](AGENTS.md) | 跨工具入口地图 + Prompt/Skill **索引** |
| [OPERATIONS.md](OPERATIONS.md) | 日常怎么用、场景跳转、初始化 |
| [ARCHITECTURE.md](ARCHITECTURE.md) | 设计原因与取舍 |

**可执行规则权威源**只在 [`prompts/`](prompts/) 与 [`skills/`](skills/)；场景步骤在 [`workflow/`](workflow/)。读完上面三份，不等于读完全部规则。审查顺序以 [prompts/review_pipeline.md](prompts/review_pipeline.md) 为准（Research → … → Documentation）。

## 现在能不能做真实投资决策？

**不能。** 当前多数是骨架数据（持仓、目标多为空表头，产品与动态事实未核验，IPS 仍为 draft）。工作流和模板可以练研究与审查写法，但结论应停在研究笔记或 `research` / `wait`，不要据此下真实 `act`。

要进入可决策状态：看 [STATUS.md](STATUS.md) 卡在哪，再按 [OPERATIONS.md](OPERATIONS.md)（§14 当前初始化顺序）补数据。

## 从哪里开始（按顺序）

1. **现在卡在哪** → [STATUS.md](STATUS.md)（门禁、阻塞、下一步）
2. **怎么用** → [OPERATIONS.md](OPERATIONS.md)（文首「阅读地图」；按场景跳转，勿通读全文）
3. **为什么这样设计** → [ARCHITECTURE.md](ARCHITECTURE.md)（取舍与因果，不是操作步骤）
4. **然后按你要做的事跳转**
   - 研究 / 持仓 / 复核 / 再平衡 → [OPERATIONS.md](OPERATIONS.md)（文首「阅读地图」；按场景跳转）
   - 补齐骨架数据 → [OPERATIONS.md](OPERATIONS.md)（§14 当前初始化顺序）
   - 用 Agent（Claude Code / Cursor）→ [AGENTS.md](AGENTS.md)；Claude Code 先看 [CLAUDE.md](CLAUDE.md)
   - 写库字段与校验 → [database/README.md](database/README.md) + [database/data_contracts.md](database/data_contracts.md)（数据契约）
   - 原始研究材料怎么放 → [raw_material/README.md](raw_material/README.md)

## 常用操作

| 要做的事 | 入口 | 主要产物 |
|---|---|---|
| 研究一个新问题 | [研究工作流](workflow/research.md) + [研究笔记模板](templates/research_note.md) | `knowledge/`、`database/`、`reports/` |
| 复核一次 ETF 买入 | [ETF 买入工作流](workflow/buy_etf.md) + [审查模板](templates/review.md) | 审查记录、投资报告、Decision |
| ETF 卖出 / 减仓 | [ETF 卖出工作流](workflow/sell_etf.md) | 审查记录、Decision、持仓快照 |
| 例行定投 | [定投工作流](workflow/dca.md)（深度见 [OPERATIONS.md](OPERATIONS.md) §1.1 审查深度分级） | Decision Log 追加或新建 |
| 记录成交后的持仓 | [holdings.csv](database/portfolio/holdings.csv) | 新的持仓快照 |
| 记录当时为何行动 | [决策日志模板](templates/decision_log.md) | `decision_log/` 下的新记录 |
| 检查整个组合 | [组合复核工作流](workflow/portfolio_review.md) | `reports/` 下的复核报告 |
| 处理配置偏离 | [再平衡工作流](workflow/rebalance.md) | 行动方案、持仓快照、决策日志 |

涉及买入、卖出、持有、定投、调仓或产品排序时，按 [Review Pipeline](prompts/review_pipeline.md) 过审；原因见 [ARCHITECTURE.md](ARCHITECTURE.md)，逐步操作见 [OPERATIONS.md](OPERATIONS.md)。

## 目录

```text
PIOS/
├── README.md              # 本入口
├── ARCHITECTURE.md        # 设计说明（地图）
├── OPERATIONS.md          # 日常运行手册（地图）
├── AGENTS.md              # 跨工具入口地图
├── CLAUDE.md              # Claude Code 入口
├── prompts/               # 项目规则正文（权威源）
├── skills/                # 八项阶段能力与委员会辅助 Skill（权威源）
├── .cursor/               # Cursor 引用 / 发现层
├── raw_material/           # 待蒸馏、可定位的原始研究材料
├── knowledge/             # 稳定知识
├── database/              # 产品、观察池、模型与个人组合
├── workflow/              # 可重复执行的流程
├── templates/             # 研究、审查、报告与日志模板
├── reports/               # 阶段研究和组合复核结果
└── decision_log/          # 单次决策及后续复盘
```

- `knowledge/`：低频变化的概念和方法
- `raw_material/`：未经 Validation 的合规摘录或快照，不直接形成事实或结论
- `database/`：结构化事实，含个人持仓和目标配置
- `workflow/`：可重复执行的步骤
- `reports/`：阶段性结果
- `decision_log/`：某次决策当时的证据、理由、边界和复盘

新资产暴露、首次买入、改目标、重大再平衡或 ETF 排序时，可使用 [委员会 Skill](skills/committee/SKILL.md) 对目标配置、组合暴露、ETF 实施与反方风险进行四席审查；它不会替代八步 Pipeline 或产生自动交易。

## 当前优先事项

以 [STATUS.md](STATUS.md) 为准；它汇总就绪门禁、阻塞、数据时效和待复核项，避免在多个入口维护容易过期的任务副本。

## 隐私

私人仓库：持仓、目标、报告和决策日志可以进版本控制。不要写入密钥、令牌、密码、私钥、完整证券账号、身份证、银行卡、原始券商对账单或其他认证信息；账户用无敏感信息的别名。原始文件留在仓库外的受控位置，仓库最多保留其哈希或核验结论。

Git 负责版本与复盘，不构成保密边界。对 `database/portfolio/`、`reports/` 与 `decision_log/` 建立加密、可恢复的离线/异地备份，并定期验证恢复；当前不引入 Vault、sops 或全仓加密。

`gptrecord.md` 是已隔离的历史聊天归档（`usage: historical_do_not_use`），不是研究材料、事实、模型输入或 Decision 来源；提交前单独检查是否含不宜长期保留的个人信息。
