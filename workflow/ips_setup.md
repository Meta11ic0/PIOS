# IPS 构造场景入口

适用：首次构造 IPS 与初始目标配置集，对象为 [database/portfolio/investment_policy.md](../database/portfolio/investment_policy.md)。修订已生效的 IPS 属于政策变更，改走 [orchestration/diligence/SKILL.md](../orchestration/diligence/SKILL.md) 完整七步并触发 Committee。

流程：

1. 读 [orchestration/ips_setup/SKILL.md](../orchestration/ips_setup/SKILL.md)，按其收集顺序与质量标准在对话中逐节构造。
2. 内容确认后落盘 frontmatter 与正文；用户明确批准后置 `active` 并写批准字段，见该 Skill「落盘与批准」。
3. 初始目标配置集写入 target_allocation.csv；最低完成标准见 [OPERATIONS.md](../OPERATIONS.md)「当前初始化顺序」。
4. 提示用户 git commit 留痕。政策内容由用户设定；下单由用户在券商完成。
