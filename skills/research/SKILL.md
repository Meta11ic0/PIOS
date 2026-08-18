---
name: research
description: 收集并组织投资、市场、指数和产品事实，建立可追溯证据。处理投资研究、产品发现、数据更新或来源查找时使用。
---

# Research

## 流程

1. 确定研究深度：
   - 轻量查询：仅对话输出，不落盘。不降低 `prompts/evidence_standards.md`。含数值或可行动事实须升格为标准取证，或将该项标为 `unknown` / 待验证，不得当作已验证事实。
   - 标准取证：写入 sources.csv + DD 记录
   - 深度调研：含 raw_material 摘录与蒸馏
2. 明确研究问题、对象、时间点和输出用途。
3. 先列出决策所需字段，再开始查找。
4. 在 `database/sources.csv` 登记 `source_id`；列名见 `prompts/csv_schema.md`。摘录或快照写入 `raw_material/`，不当作已验证事实。
5. 按 `prompts/evidence_standards.md` 的来源优先级取数。
6. 关键动态字段按 `prompts/evidence_standards.md` 做第二来源交叉验证。
7. 输出事实、来源、`source_id`、`valid_at`、`fetched_at` 和缺失项。

## 必填字段

- 对象与唯一标识
- 指标、数值、单位和统计口径
- `valid_at` 与 `fetched_at`
- 来源名称、文档标题和 URL
- `source_id`；若保存原始材料，记录相对路径和蒸馏状态
- 来源等级：primary / secondary
- 状态：verified / conflicting / missing
- 信息丰富度：`A` / `B` / `C`

禁止根据旧报告推断当前规模、成交额或交易状态。无法获取时保留空值并解释。

输出 Research Report：问题、来源清单、原始材料状态、已验证事实、冲突、缺失项、建议写入的数据库位置。

场内跨境 ETF 取数顺序：交易所与基金公司确认完整清单、正式名称和基础字段 → 集思录观察成交额、IOPV、折溢价和交易状态 → 东方财富核对规模、成立时间、费率和历史资料 → 基金公司官网核对申赎、QDII 与风险公告。第三方冲突时以适用时点的官方文件为准。行情平台不能替代交易所与基金公司正式资料。研究结论不得超出证据日期。发现产品不等于推荐产品；形成排序或买入、卖出、持有、定投、调仓结论时改标 `[diligence]` 并读 `prompts/diligence.md`。
