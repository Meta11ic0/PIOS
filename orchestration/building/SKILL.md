---
name: building
description: 系统建设编排。规定改仓库规则、架构、加载协议、目录结构、数据契约与补缺口时的步骤序列、停止条件与产物。产物是文件变更与 PROJECT.md 的建设记录。
---

# Building

本编排服务 `[building]` 路线：本轮处理仓库规则、架构和加载方式，不是在执行投资流程。开场再读 [PROJECT.md](../../PROJECT.md) 与本轮要改的文件。

本编排以系统建设者身份回答。`read-only` 的产物留在对话；`local` 与 `cross-layer` 的产物是文件变更与 [PROJECT.md](../../PROJECT.md) 的建设记录。陈述设计时标注验证状态：已验证可运行的、未验证的、设计中。Decision 五条门禁与投资侧的停止条件、交易边界不在本编排范围内。

## 适用

本编排服务 [workflow/building.md](../../workflow/building.md) 声明的场景：改规则正文、改架构与加载协议、改目录结构、补缺口。各事务的前置输入在那张卡里。

不适用：给出或修改投资结论，那属于 `[invest]`；构造 IPS、种子核验与来源登记也属于 `[invest]`，分别走 [orchestration/ips_setup/SKILL.md](../ips_setup/SKILL.md) 与 `workflow/` 下的 `ips_setup.md`、`seed_ingest.md`、`register.md`。往 `database/` 录内容是使用系统，不是建设系统。

## 步骤

1. 找权威位置：这条规则现在写在哪份文件，谁是权威。场景卡片与 OPERATIONS 只做场景化表述，权威在 `orchestration/`、`skills/` 或 `database/` 的契约文件。
2. 改权威位置：只改本轮相关文件。写规则正文时按 Agent 执行指令写：每句直述要做什么，一行能写清的写成一行；浏览式小标题、对照表与说明书口吻留给对人说明的文档。
3. 回填引用：按场景卡片的影响面档回填——`local` 只改直接引用该处的文件；`cross-layer` 更新 [AGENTS.md](../../AGENTS.md)「先判断目的」的路线行，并同步 ARCHITECTURE、OPERATIONS、README 的指向。
4. 一致性检查：文内编号与交叉引用、相对链接的目标文件是否存在、跨文件口径是否一致。
5. 记变更：在 [PROJECT.md](../../PROJECT.md) 变更日志追加一行，写清日期、改了什么、涉及哪些文件。

## 停止

出现下列任一条就停下，问用户或补资料，不继续改：

- 权威位置不明，或同一条规则存在两处正文
- 改动会放宽投资停止条件、四结论枚举或交易边界
- 跨路线影响未评估，或会改变其他路线的加载协议
- 未获写入授权

## 写文件约束

改人读文档时按 [skills/docs/SKILL.md](../../skills/docs/SKILL.md)。数据更正使用追加模式（`supersedes_record_id` + `correction_reason`），旧行保持原样。模型与数据规则变更时保留旧版本，历史 Decision 继续引用当时的模型版本和输入快照。演示工件只放 `reports/demo/`、`decision_log/demo/`、`screening/runs/demo/`、`raw_material/demo/`，`scope` 只写 `demo_only` / `example`。`.cursor/rules/` 只保留指向 `AGENTS.md` 的入口。

Agent 可直接读写仓库文件，每次写入后说明改了什么；推荐用户用 git 管理，Agent 在没有收到明确指令之前不执行 git 操作。
