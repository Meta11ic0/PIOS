# PIOS — Agent 入口

PIOS（Personal Investment Operating System）用文件管理金融知识、产品与组合数据、审查记录和投资决策记录。辅助弄清事实，或在买入、卖出、持有、定投、调仓前把该查的查完。每条判断事后可追溯：当时看见什么、为何这样选、何时该失效。

可执行规则正文只在 `orchestration/`、`skills/`、`vendor/`、`workflow/` 与 `database/`。读完本文件不等于读完规则正文；动手前须 Read 对应正文。`workflow/` 只补充场景入口；停止条件以 `orchestration/` 与 `skills/` 为准。手册与本文件冲突时以本文件为准。

## 先判断目的

开场先判断本轮目的，标注恰好一种路线（`[building]` / `[learning]` / `[invest]`）与该路线的一项场景，紧跟一句人话说明本轮产出什么，再列出本轮将读的文件。场景名取自该路线的场景卡片。阶段、能力单元与结论格式由该路线正文规定。

用户要修改或说明规则、架构、加载协议或建设进度：`[building]`（系统建设）。读 [orchestration/building/SKILL.md](orchestration/building/SKILL.md)；场景见 [workflow/building.md](workflow/building.md)。

用户要弄清概念或产品事实、且本轮不形成投资行动结论：`[learning]`（知识调研）。读 [orchestration/learning/SKILL.md](orchestration/learning/SKILL.md)；场景见 [workflow/learning.md](workflow/learning.md)。

用户要对具体标的形成买入、卖出、持有、定投、调仓或产品排序的结论，或首次构造 IPS 与初始目标配置，或做数据初始化与维护（种子核验、来源登记）：`[invest]`（投资事务）——产物是四结论与 Decision Log；要出结论就必须走完 DD 七步。结论走 [orchestration/diligence/SKILL.md](orchestration/diligence/SKILL.md)；构造 IPS 与初始目标配置走 [workflow/ips_setup.md](workflow/ips_setup.md) 与 [orchestration/ips_setup/SKILL.md](orchestration/ips_setup/SKILL.md)；种子核验走 [workflow/seed_ingest.md](workflow/seed_ingest.md)，来源登记走 [workflow/register.md](workflow/register.md)。这三类的产物是政策文件、产品数据行与来源登记行，各自走入口规定的顺序。四结论只在走完 DD 七步后写入。开场加读 `skills/evidence/SKILL.md`。

新建或写入 `database/` 下 CSV 时读 `database/csv_schema.md`。改 README、STATUS、ARCHITECTURE、OPERATIONS、knowledge 等对人说明时读 `skills/docs/SKILL.md`。

同时命中 `[building]` 与另外两条时先问本轮做哪件，确认前停在提问。`[learning]` 与 `[invest]` 以用户是否确认要对具体标的形成买入、卖出、持有、定投、调仓或产品排序结论为准；未确认按 `[learning]`。推进结论或写入前先读该路线正文。

Agent 可直接读写仓库文件。推荐用户使用 git 进行文件管理；Agent 在没有收到明确指令之前不会执行 git 操作。每次写入后说明改了什么。Agent 的产物止于结论与执行前核对清单，下单由用户在券商完成。

## 目录

| 目录 | 装什么 | 管什么 |
|---|---|---|
| `orchestration/` | diligence、committee、ips_setup、learning、building 五份编排正文 | 顺序：步骤序列、停止条件与产物 |
| `skills/` | research、validation、modeling、reasoning、risk、challenge、decision 七个能力单元；evidence、docs 两份跨步复用 | 做法：一件事怎么做 |
| `vendor/` | humanizer-zh | 第三方文风工具，与投资流程无关 |
| `workflow/` | 场景卡片，三条路线共用 | 声明适用范围、前置输入与深度分档 |
| `database/` | 结构化事实、数据契约、CSV 列名 | 数据与列名权威 |
| `templates/` `knowledge/` `raw_material/` `reports/` `decision_log/` | 模板、知识、原始材料与产物 | 留痕 |

`.cursor/` 下的 rules 与 skills 只做指向根目录的引用层，正文只在根目录维护一份。

## 每次会话须遵守

执行 `date` 确认日期；数据表述以当期事实为准。低置信度、不完整数据、来源缺口、无法核验的推断须显式标注。系统产物是投资分析与审查记录，不构成投资建议。券商交易 API 由用户在券商侧使用；下单由用户在券商完成。`act` 仅为结论，不是交易授权。

先回答核心问题，再给依据和下一步。用简体中文；术语优先简单英文。短段落、清单、明确字段。数据对比统一口径、单位和适用时点。区分已验证事实、待验证信息、假设、个人判断。写入仓库的限于适合长期复用的信息；一次性回答留在对话中。

已读某条路线的正文后改做另一件事时，把那份正文当文件读，不当身份。产品 prompt 当文件读，不当人格。

证据规则见 [skills/evidence/SKILL.md](skills/evidence/SKILL.md)。投资结论怎么写见 [skills/decision/SKILL.md](skills/decision/SKILL.md)。

## 改仓库文件时

按 [orchestration/building/SKILL.md](orchestration/building/SKILL.md) 执行：五步、停止条件，以及演示隔离、版本保留、追加更正、`.cursor/rules/` 只留指向 `AGENTS.md` 的入口这些写文件约束。该文件是这些约束的权威位置。
