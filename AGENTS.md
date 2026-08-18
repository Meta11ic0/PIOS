# PIOS — Agent 入口

PIOS（Personal Investment Operating System）用文件管理金融知识、产品与组合数据、审查记录和投资决策记录。辅助弄清事实，或在买入、卖出、持有、定投、调仓前把该查的查完。每条判断事后可追溯：当时看见什么、为何这样选、何时该失效。

可执行规则正文只在 `prompts/` 与 `skills/`。读完本文件不等于读完规则正文；动手前须 Read 对应正文。`workflow/` 只补充场景入口，不得放宽停止条件。手册与本文件冲突时以本文件为准。

## 先判断目的

开场先判断本轮目的，标注恰好一种，再只读该路线下一步。阶段、Skill 与投资结论格式由下一步规定。

用户要修改或说明规则、架构、加载协议或建设进度：`[building]`（系统建设），读 `prompts/building.md`。
用户要弄清概念或产品事实、且本轮不形成投资行动结论：`[learning]`（知识调研），读 `prompts/learning.md`。
用户要对具体标的形成买入、卖出、持有、定投、调仓或产品排序的结论：`[diligence]`（投资动作审查），读 `prompts/diligence.md`。

改 README、STATUS、ARCHITECTURE、OPERATIONS、knowledge 等对人说明时加 `prompts/docs_style.md`。新建或写入 `database/` 下 CSV 时加 `prompts/csv_schema.md`。

同时命中 `[building]` 与另外两条时先问本轮做哪件。未确认前不改规则、不推进投资结论。`[learning]` 与 `[diligence]` 以用户是否确认要对具体标的形成买入、卖出、持有、定投、调仓或产品排序结论为准；未确认按 `[learning]`。未读该路线下一步 Prompt 时，不得推进该结论或写入。

Agent 可直接读写仓库文件。推荐用户使用 git 进行文件管理；Agent 在没有收到明确指令之前不会执行 git 操作。每次写入后说明改了什么。不接入券商、不代下单。

## 每次会话须遵守

执行 `date` 确认日期，不得把过期数据表述为当前事实。低置信度、不完整数据、来源缺口、无法核验的推断须显式标注。不构成投资建议。不接入券商、不自动下单、不代下单、不设计或接入券商交易 API。`act` 仅为结论，不是交易授权。用户自行在券商操作。

先回答核心问题，再给依据和下一步。用简体中文；术语优先简单英文。短段落、清单、明确字段。数据对比统一口径、单位和适用时点。区分已验证事实、待验证信息、假设、个人判断。只有适合长期复用的信息才写入仓库，不为一次性回答制造文件。

其余证据规则、停止条件与四结论由该路线下一步 Prompt 规定。投资结论怎么写见 `skills/decision/SKILL.md`。

## 改仓库文件时

只改与任务直接相关的文件。修改 `prompts/` 或 `skills/` 后检查「先判断目的」的下一步。`.cursor/rules/` 只保留指向 `AGENTS.md` 的入口。演示工件只放 `reports/demo/`、`decision_log/demo/`、`screening/runs/demo/`、`raw_material/demo/`，不得标记 `scope: production`。模型和数据规则变更时保留旧版本；历史 Decision 继续引用当时的模型版本和输入快照。数据更正使用追加模式（`supersedes_record_id` + `correction_reason`），不直接修改旧行。
