# PIOS

Personal Investment Operating System。用文件和 DD 流水线（DD 即 Due Diligence，行动前的系统核查）管理投资研究、组合数据、DD 记录和决策日志——每笔判断事后都能对上：当时看见什么、为何这样选、何时该失效。文件自己维护，需要时让 Claude Code 或 Cursor 按项目规则帮忙。本仓库不接入券商、不自动下单；交易由用户自行完成，系统只记录建议与授权。

项目起源于一个具体问题：场外 QDII 联接基金限额后，纳指、标普一类的场外定投没法按预期执行，只能去看大陆证券账户可交易的场内跨境 ETF。同一指数下场内产品很多，得尽量凑齐候选集再横向对比。还要弄清境内联接与场内产品各自追踪的源头，以及上游和底层怎么挂在一起。一张简单的清单不够用，就扩成了决策系统：方法、数据口径、组合约束、DD 顺序、决策留痕，都要能留下来。

业务数据还没补齐，现在不能拿来做真实决策。投资就绪见 [STATUS.md](STATUS.md)；系统建设进度与缺口见 [PROJECT.md](PROJECT.md)。

## 怎么读

1. 本文件 — 项目是什么、当前状态
2. [ARCHITECTURE.md](ARCHITECTURE.md) — 为什么这样设计；「何时调用」见其中「如何串联」
3. [OPERATIONS.md](OPERATIONS.md) 文首「阅读地图」— 日常怎么用；按需要跳，不必通读
4. [STATUS.md](STATUS.md) — 投资就绪与九项门禁；初始化从 [investment_policy.md](database/portfolio/investment_policy.md) 开始
5. [PROJECT.md](PROJECT.md) — 系统建设进度、已知缺口与 Skill 审查记录（非投资就绪）
6. 按事找文件：场景看 `workflow/`；操作细节回 OPERATIONS 相关节；DD 顺序见 [prompts/diligence.md](prompts/diligence.md)，各步做法见 `skills/`；新暴露、首次买入、改目标、重大再平衡或 ETF 排序时加 [Committee Skill](skills/committee/SKILL.md)

补数据见 [OPERATIONS.md](OPERATIONS.md)「当前初始化顺序」。

## 目录

```text
PIOS/
├── STATUS.md                 # 投资就绪
├── PROJECT.md                # 系统建设进度
├── ARCHITECTURE.md           # 为什么
├── OPERATIONS.md             # 怎么用
├── AGENTS.md                 # 助手入口
├── CLAUDE.md                 # Claude Code 入口
├── prompts/                  # 项目规则
├── skills/                   # 各 DD 阶段怎么做
├── workflow/                 # 场景步骤
├── templates/                # 空白模板
├── knowledge/                # 稳定知识
├── raw_material/             # 待整理的原始材料
├── database/                 # 产品与组合等结构化数据
├── reports/                  # 研究报告；演示在 demo/
├── decision_log/             # 决策记录；演示在 demo/
└── EXTERNAL_REFERENCES.md    # 外部参考，非规则
```

## 隐私

公开仓库。不要把持仓、生产报告、生产 Decision Log、密钥、令牌、密码、私钥、完整证券账号、身份证、银行卡、原始券商对账单或其他认证信息写入本仓库。账户用别名。演示只放对应 `demo/` 目录。

Git 不构成保密边界。对留在仓库外的个人数据建立加密、可恢复的离线或异地备份，并定期验证恢复。
