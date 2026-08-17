---
id: report-depth-analysis-wealthfolio-v1
type: report
status: complete
created_at: 2026-07-28
topic: Wealthfolio 深度分析 — 对 PIOS 的借鉴价值
source_refs: [github.com/wealthfolio/wealthfolio, wealthfolio.app]
---

# Wealthfolio 深度分析

> 分析日期：2026-07-28
> 用途：提取 Wealthfolio 的设计要素，评估对 PIOS 持仓层、绩效口径、隐私策略和自动化设计的借鉴价值。
> 分析范围：数据模型、活动类型系统、绩效指标、隐私安全、自动化机制。

---

## 1. 数据模型借鉴

### 1.1 Wealthfolio 的核心数据模型

Wealthfolio 的数据模型围绕四个核心维度设计：

| 维度 | 含义 | 在活动中的变化 |
|------|------|----------------|
| **Cash Balance** | 按币种管理的现金头寸 | 每个活动影响现金余额 |
| **Asset Quantity** | 持有的股份/单位数量 | BUY/SELL/SPLIT 改变数量 |
| **Cost Basis** | 原始成本（用于损益计算） | LOT 级别 FIFO 管理 |
| **Net Contribution** | 净投入资金（影响 TWR 计算） | DEPOSIT/WITHDRAWAL 改变 |

**账户模型：**

```
Account
├── Name (display label)
├── Group (e.g., RRSP, Taxable)
├── Type (Securities | Cash | Credit Card | Crypto)
├── Currency (base currency)
├── Tracking Mode (Transactions | Holdings)
└── Status (Active | Hidden | Archived)
```

**Activity 模型（14 种规范类型）：**

| 分类 | 类型 | 现金流影响 | 持仓影响 |
|------|------|-----------|----------|
| 交易类 | BUY | - | + |
| 交易类 | SELL | + | - |
| 交易类 | SPLIT | 无 | 调整 |
| 收入类 | DIVIDEND | + | 无 |
| 收入类 | INTEREST | + | 无 |
| 收入类 | CREDIT | + | 无 |
| 现金流 | DEPOSIT | + | 无 |
| 现金流 | WITHDRAWAL | - | 无 |
| 转账 | TRANSFER_IN | +/- | +/- |
| 转账 | TRANSFER_OUT | +/- | +/- |
| 费用 | FEE | - | 无 |
| 费用 | TAX | - | 无 |
| 调整 | ADJUSTMENT | 取决于子类型 | 取决于子类型 |
| 未知 | UNKNOWN | 待识别 | 待识别 |

**关键设计模式：**
- Subtype 扩展：DRIP（DIVIDEND + BUY 展开）、STAKING_REWARD（INTEREST + BUY 展开）
- 元数据 JSON 字段：`flow.is_external`、`split_ratio`、`source.broker`、`source.original_type`
- 活动状态：POSTED（已结算，参与计算）、PENDING（待结算，排除）
- 两个跟踪模式：Transactions（完整流水，全绩效计算）vs Holdings（快照，仅价格绩效）

**持仓管理：**
- LOT 级别 FIFO：每个 BUY 创建一个 LOT，SELL 按 FIFO 消耗
- SPLIT 仅调整数量与单位成本，总成本不变
- TRANSFER_IN 保留成本基础，TRANSFER_OUT FIFO 匹配移除

### 1.2 PIOS holdings.csv 对照

PIOS `holdings.csv` 当前字段（30+ 列，仅表头无数据）：

| PIOS 字段 | Wealthfolio 等效 | 差距分析 |
|-----------|-----------------|----------|
| quantity, cost_basis, market_value | Holdings snapshot | PIOS 已有快照字段，但无 LOT 级管理 |
| cost_basis_scope | -- | PIOS 有口径说明，Wealthfolio 用 FIFO LOT 系统自动管理 |
| valuation_method/price/source/valid_at | Holdings snapshot | PIOS 估值字段非常精细（6 列），Wealthfolio 简化为 `avgCost` + market price |
| account_alias, product_id, asset_type | Account + Symbol | PIOS 使用自由文本 alias，Wealthfolio 用结构化账户 |
| trade_currency, fund_currency, underlying_exposure_currency | Currency per account | PIOS 的三大币种拆分对跨境 ETF 非常必要，Wealthfolio 仅单币种/账户 |
| portfolio_snapshot_id | -- | PIOS 特有的批次快照聚合机制 |
| verification_status | -- | PIOS 特有的验证状态追踪 |
| superseeds_record_id / correction_reason | -- | PIOS 特有的可追溯更正机制 |

**PIOS 缺失但 Wealthfolio 有而值得借鉴的：**
1. **活动/事件模型**：PIOS §8.4 说「当前不新增 transactions.csv」，但 Wealthfolio 的经验表明，有了活动流水才能自动计算成本基础、实现 TWR 精确计算、支持逐笔对账。建议 PIOS **保持快照为主，但增加一个可选的 event log 层**，记录 DEPOSIT/WITHDRAWAL、DIVIDEND、SPLIT 等「不改变持仓数量但影响现金流/成本」的事件。
2. **账户结构**：PIOS 目前只有 `account_alias` 自由文本，缺少账户类型、分组、跟踪模式等元数据。建议至少在 `notes` 或约定中显式记录账户类型。
3. **LOT/FIFO 系统**：PIOS 当前只有单 `cost_basis`，无法处理分批买入不同成本的问题。建议设计一个轻量的 LOT 层：在 holdings.csv 外增加 `lots.csv`，只记录每次买入的批次，卖出时手动标记消耗了哪些 LOT。

### 1.3 结论：是否引入「活动/交易」概念？

**短期（维持现状）**：PIOS 既然不做逐笔对账和税务成本，保持快照足够。Wealthfolio 的活动模型对 PIOS 的借鉴价值主要在概念层——理解现金流、成本基础和持仓数量的三向变化关系。

**中期（建议引入 event log）**：如果 PIOS 需要自动计算 TWR、分红再投资跟踪、或更正历史错误，建议引入轻量 event log，只记录三类事件：
1. 外部现金流（DEPOSIT/WITHDRAWAL） — 与现有 `external_cashflows.csv` 对应
2. 分红/利息（DIVIDEND/INTEREST） — 影响收入跟踪
3. 拆分/公司行动（SPLIT） — 影响数量与成本调整

这三类事件**不涉及交易流水**，维持了 OPERATIONS.md 的「不新增 transactions.csv」约束。

---

## 2. 绩效指标口径

### 2.1 Wealthfolio 的绩效系统

Wealthfolio 的重建性能引擎是**范围感知的**：

| 账户模式 | 使用的绩效指标 |
|----------|---------------|
| 交易模式（完整流水） | TWR + IRR(MWR) |
| 持仓模式（快照） | Value Return（价格变化） |
| 基准/指数 | Price Return |
| 个股/Symbol | 基于行情价格的回报率 |

**TWR 计算细节：**
- 计算每日回报率：`(End Value + Outflows − Start Value − Inflows) / (Start Value + Inflows)`
- 日回报几何链式复合：`TWR = [(1 + r₁) × (1 + r₂) × ... × (1 + rₙ) − 1] × 100`
- 以外部现金流为子期间边界
- 期初市值低于 1 单位货币的天数被排除

**MWR/IRR (XIRR) 计算细节：**
- 构建带日期现金流序列
- 用二分法求解器（XIRR solver）找到使 NPV=0 的单一折现率
- 时间计量用 365.25 天/年
- 报告年化收益率和期间收益率
- 现金流符号不变的场景下 MWR 不可算
- Per-account 场景下使用 Modified Dietz 作为快速替代

**额外指标：**
- 年化收益率
- 最大回撤
- 组合波动率
- Sharpe Ratio（基于 TWR）
- Portfolio Beta
- Gain/Loss 分解：未实现 P&L、已实现 P&L、收入、费用、税费、外汇影响、现金流

### 2.2 PIOS 绩效文档对照

PIOS `performance_measurement.md` 当前为 draft 状态，内容：

- 定义了 MWR/XIRR 和 TWR 的基本概念
- 强调了必须完整输入才能计算
- 缺少具体的计算口径、公式细节和执行规范

**差距分析：**

| 方面 | Wealthfolio | PIOS | 建议 |
|------|------------|------|------|
| TWR 口径 | 日回报几何链式，每天独立子期间 | 概念性定义，缺少子期间划分规则 | PIOS 应借鉴 Wealthfolio 的「外部现金流为子期间边界」规则 |
| MWR 口径 | XIRR 二分法求解 + Modified Dietz 近似 | 概念性定义 | PIOS 应引入具体的求解器选择和精度规范 |
| 现金流处理 | 指定外部现金流（DEPOSIT/WITHDRAWAL）参与计算 | `external_cashflows.csv` 已有类似概念但缺乏符号一致性校验 | PIOS 的口径已合理，但需补充"净值类"活动的处理 |
| 基准对比 | S&P 500 等指数 Price Return | 未定义 | 可选：定义基准对比的方法论 |
| 范围意识 | 不同账户模式用不同指标 | 未区分 | PIOS 应明确：快照模式下只能算价格回报，不能算 TWR |
| 年化口径 | 365.25 天/年 | 未定义 | PIOS 应定义 |
| 分解报告 | 完整 Gain/Loss 分解 | 未定义 | PIOS 不需要，这不是决策系统的重点 |
| 不可算条件 | 现金流不变号时 MWR 不可算 | "缺少时不可计算"已定义 | PIOS 已有更强的限制 |

### 2.3 更新建议

PIOS 的绩效文档应该更新以下内容：
1. **增加 TWR 子期间划分规则**：以外部现金流事件为边界，相邻两次现金流之间作为一个独立子期间
2. **增加 MWR 求解规范**：指定二分法/XIRR 求解、365.25 天/年、年化输出
3. **增加范围条件表**：明确什么数据场景下可算什么指标，什么场景下必须写"不可计算"
4. **当前 draft 状态足够**：PIOS 的核心是决策纪律而非绩效可视化，绩效文档保持概念级即可，但应增加上述三条以便未来使用

---

## 3. 隐私与数据安全

### 3.1 Wealthfolio 的隐私架构

| 层次 | 做法 |
|------|------|
| 数据存储 | 本地 SQLite，无强制云端 |
| 传输加密 | TLS/HTTPS |
| 凭据加密 | AES-256-GCM + Envelope Encryption（Cloudflare Secrets Store） |
| 端到端加密 | X25519 密钥交换，设备间同步数据加密传输，密钥永不离设备 |
| API 密钥 | OS keyring（`keyring` crate），永不落盘 |
| 密码哈希 | Argon2id（自托管 Web 模式） |
| 代理凭证 | SnapTrade（SOC 2 Type II）处理券商授权，Wealthfolio 自身不接触登录凭据 |
| 可恢复性 | 全量 SQL 备份为推荐恢复方案 |

**Wealthfolio 从未存储的内容**（即使使用 Connect 订阅）：登录凭证、交易历史、持仓/头寸、账户余额、绩效分析数据。

### 3.2 PIOS 当前隐私策略

OPERATIONS.md §12.1 定义了 PIOS 的隐私规则：
- 个人数据应只进入私有仓库
- 不要写入完整账号、证件、认证秘密
- 原始券商文件存于仓库外的受控位置
- Git 不是秘密管理工具
- 行情 API 密钥放入 `.env` 或 `private/`
- 保持加密备份

### 3.3 差距与建议

| Wealthfolio 做法 | PIOS 当前状态 | 建议 |
|-----------------|--------------|------|
| SQLite 本地存储 | Git 仓库 + 手动备份 | PIOS 可以借鉴 SQLite 作为本地数据汇总层，Git 仍用于规则/知识版本控制 |
| OS keyring 管理密钥 | `.env` / `private/` 更接近 config file | PIOS 起步阶段 `.env` 够用，但 ARCHITECTURE.md 应记录 OS keyring 为推荐升级路径 |
| SNAPTRADE 代理 | 不适用（PIOS 不做券商同步） | PIOS 如未来需要券商数据对接，应借鉴代理模式，避免直接存储券商凭据 |
| E2EE 多设备同步 | 不适用 | PIOS 当前无多设备需求，但如果将来跨设备用 Git，现有 Git 加密可满足 |
| 定期 SQL 全量备份 | 已有加密备份概念 | 需增加周期性恢复测试的要求 |

**结论**：PIOS 当前的隐私策略在决策操作系统层面已经足够。需要补充的是：
- 在 `ARCHITECTURE.md` 或 `OPERATIONS.md` 中增加**引入行情 API 后**的密钥管理规范（参考 OS keyring 模式）
- 在备份策略中增加「定期测试恢复」的具体频率（当前只写了「定期测试」）

---

## 4. 自动化潜力

### 4.1 Wealthfolio 的自动化机制

| 机制 | 说明 | 付费/免费 |
|------|------|-----------|
| CSV 导入 | 五步 mapping wizard，保存 per-account 模板，支持交易/持仓两种格式 | 免费 |
| Broker Sync (Connect) | SnapTrade OAuth，15+ 券商，每日自动同步持仓、余额、交易历史 | 付费（$7.99+/月） |
| 汇率更新 | 每 6 小时从 Yahoo Finance 拉取 | 免费 |
| 行情定价 | 自动拉取证券市价 | 免费 |
| 多设备同步 | E2EE 加密同步 | 付费 |
| Addon 系统 | TypeScript SDK，可扩展页面和数据访问 | 免费 |

### 4.2 Wealthfolio CSV 格式对 PIOS 的参考价值

Wealthfolio 的 CSV 格式非常清晰，可以作为 PIOS 未来数据导入的参考标准：

**交易 CSV**（适合券商导出 → PIOS event log）：
```
date,symbol,instrumentType,quantity,activityType,unitPrice,currency,fee,amount,fxRate,subtype,comment,account
```

**持仓快照 CSV**（与 PIOS holdings.csv 最相似）：
```
date,symbol,quantity,avgCost,currency
```

关键对比：
- Wealthfolio `symbol` 接受 `$CASH-<CCY>` 作为现金表示 — PIOS 可以借鉴类似的现金行处理
- Wealthfolio `activityType` + `subtype` 的二级分类 — PIOS 可以在 event log 中采用
- Wealthfolio `instrumentType` 用于自动分类 — PIOS 已有 `asset_type` 字段，一致

### 4.3 券商同步机制

Wealthfolio 使用 SnapTrade 作为中介层：
- 用户通过 OAuth 授权 SnapTrade 访问券商
- SnapTrade 只读拉取持仓、余额、交易历史
- Wealthfolio 仅接收加密后的连接令牌，不接触登录凭据
- 每日自动同步 + 手动「Sync Now」
- 同步数据为只读（可用 ADJUSTMENT 覆盖）

### 4.4 对 PIOS 的数据流设计建议

PIOS 未来如需对接真实数据，推荐分层设计：

```
券商/银行 │ 手动导出 CSV / 对账单 PDF
         ↓
导入层    │ BXP 类工具（或 Wealthfolio CSV 格式为中介）
         ↓
验证层    │ AI/人工核验 → 标注 verified / conflicting / missing
         ↓
存储层    │ holdings.csv（快照）+ optional event_log.csv
         ↓
使用层    │ PIOS Decision Pipeline
```

**关键原则**：
1. **不直接连接券商**：PIOS 不是数据同步工具，应通过 Wealthfolio/CSV 等已验证的中介获取数据
2. **数据验证不可跳过**：即使在 Wealthfolio 中已验证，进入 PIOS 后仍需通过 Validation Skill
3. **适用时点不可丢失**：所有导入数据必须保留时间戳和来源

---

## 5. 可借鉴清单（优先级排序）

| 优先级 | 借鉴内容 | 目标文件 | 具体做法 | 预期收益 |
|--------|----------|----------|----------|----------|
| **P0** | 活动类型系统概念映射 | OPERATIONS.md §8 | 在 §8 中补充说明：现金流事件、分红事件、拆分事件对持仓的三维影响（数量/成本/市值），即使不新增 transactions.csv | 避免未来扩充持仓字段时遗漏现金流维度 |
| **P0** | 绩效口径补充 | knowledge/portfolio/performance_measurement.md | 增加 TWR 子期间划分规则、MWR 求解规范、范围条件表 | 确保绩效文档可用，避免未来口径不一致 |
| **P1** | 账户元数据扩展 | holdings.csv / OPERATIONS.md §8.1 | 在 `account_alias` 用法约定中增加账户类型（Securities/Cash/Crypto）和跟踪模式（Transactions/Holdings）的显式标记 | 支持多账户场景的正确聚合 |
| **P1** | 轻量 Event Log | OPERATIONS.md §8.4 | 不推翻"不新增 transactions.csv"，但增加一个仅记录 DEPOSIT/WITHDRAWAL/DIVIDEND/SPLIT 的 event_log.csv（不包含 BUY/SELL 交易流水） | 支持精确 TWR 计算和分红跟踪，维持无交易流水的约束 |
| **P2** | LOT 级别成本管理 | holdings.csv / 新增 lots.csv | 定义每个 BUY 批次的 LOT 记录，SELL 时手动引用 LOT ID | 支持分批成本跟踪，无需全量 FIFO 自动系统 |
| **P2** | 现金头寸表示 | holdings.csv | 引入 `$CASH-<CCY>` 作为现金行的 symbol 约定，记录各账户现金余额 | 组合总市值的完整性 |
| **P3** | CSV 导入格式参考 | database/README.md 或 data_contracts.md | 当 PIOS 需要批量导入时，参考 Wealthfolio CSV schema 设计 | 降低后续对接工具的磨合成本 |
| **P3** | 隐私策略补充 | OPERATIONS.md §12.1 | 增加 OS keyring 路径描述、定期恢复测试频率、行情 API 密钥加载规范 | 为引入 API 密钥做准备 |
| **P3** | 分层权限参考 | ARCHITECTURE.md | Wealthfolio 的 Addon SDK/权限同意机制，可作为 PIOS 未来"技能市场"的设计参考 | 长期架构储备 |

### 5.1 PIOS 应该做什么

1. **概念对齐**：把 Wealthfolio 的活动类型系统映射为 PIOS 的领域词汇，统一对现金流、成本基础、净投入的理解
2. **绩效方法对齐**：更新 PIOS 绩效文档，明确 TWR/MWR 的计算口径和限制条件
3. **引入轻量 Event Log**：在不破坏"无交易流水"约束的前提下，增加现金流/分红/拆分事件的记录能力
4. **现金行标准化**：在 holdings.csv 中规范化现金余额的记录方式

### 5.2 PIOS 不应该做什么

1. **不做完整活动流水系统**：PIOS 的核心是决策纪律，不是账户对账。交易流水属于记账工具层
2. **不做自动券商同步**：这是 Wealthfolio Connect 的价值所在。PIOS 应保持"数据来自已验证来源"的原则
3. **不做绩效可视化 UI**：使用 Wealthfolio 作为可视化前端更合理
4. **不做 LOT 级自动 FIFO**：除非有税务或精确业绩归因需求，当前单 cost_basis 已满足决策需要

### 5.3 「事实层」对接方案

PIOS 与 Wealthfolio 的最佳分工关系：

```
Wealthfolio（事实层）                  PIOS（决策层）
┌─────────────────────┐             ┌─────────────────────┐
│  CSV 导入/券商同步    │  定期      │  Research Pipeline   │
│  → 活动流水          │  export    │  → Validation        │
│  → 持仓计算           │ ────────→  │  → Modeling/Risk     │
│  → 绩效计算(TWR/MWR)  │  holdings  │  → Challenge          │
│  → 净值跟踪           │  + perf   │  → Decision(四结论)   │
│  → 仪表盘/可视化      │             │  → Decision Log       │
└─────────────────────┘             └─────────────────────┘
        ↑                                    ↑
        │ CSV 导入 / 手动录入               │ 适用时点 + 来源核验
        │                                    │
  券商/银行对账单                    PIOS 数据库（holdings/eval）
```

**具体流程**：
1. 用户在 Wealthfolio 中管理所有账户，同步或导入交易数据
2. PIOS 从 Wealthfolio 周期性导出持仓快照和绩效数据
3. 导出数据经过 PIOS 的 Validation Skill 检查（时效、来源、口径）
4. 验证后的数据写入 PIOS 的 holdings.csv 和绩效记录
5. PIOS 的 Decision Pipeline 在验证后的数据基础上运行

这种分工的优势：
- Wealthfolio 处理「记账」复杂性（活动展开、LOT、FIFO、绩效计算）
- PIOS 保持「决策」专注（纪律、证据、DD、留痕）
- 数据通过 CSV 快照交换，耦合度低
- 任一方出问题不影响另一方

### 5.4 对核心文件的修改建议

| 文件 | 建议修改 |
|------|---------|
| **OPERATIONS.md §8** | 补充活动类型的现金流/成本/数量三维影响概念；增加 Event Log 可选层的说明；增加现金行`$CASH-<CCY>`约定 |
| **OPERATIONS.md §12.1** | 补充 OS keyring 路径参考和密钥加载规范；补充备份恢复测试频率 |
| **knowledge/portfolio/performance_measurement.md** | 从 draft→complete；补充 TWR 子期间规则、MWR 求解规范、范围条件表、365.25 天/年约定 |
| **EXTERNAL_REFERENCES.md** | 更新 Wealthfolio 章节，补充本次分析的详细借鉴点 |
| **ARCHITECTURE.md** | 可选：在 "当前边界" 或 "6. 与 OPERATIONS 的分工" 中增加 Wealthfolio 作为可选事实层的描述 |

---

## 附录：关键来源

- Wealthfolio Activity Types: https://wealthfolio.app/docs/concepts/activity-types/
- Wealthfolio Core Concepts: https://wealthfolio.app/docs/concepts/
- Wealthfolio CSV Import: https://wealthfolio.app/docs/guide/csv-import/
- Wealthfolio User Guide: https://wealthfolio.app/docs/guide/
- Wealthfolio Features: https://wealthfolio.app/features/
- Wealthfolio Connect: https://wealthfolio.app/connect/
- Wealthfolio FAQ: https://wealthfolio.app/docs/faq/
- Performance Metrics (via Web Search): https://wealthfolio.app/docs/concepts/performance-metrics/
- Tracking Modes: https://wealthfolio.app/docs/concepts/tracking-modes/
- Cost Basis & Lots: https://wealthfolio.app/docs/concepts/cost-basis-and-lots/
