---
name: evidence
description: 证据标准。规定来源优先级、引用规则、口径与币种区分、关键动态的双来源与时效要求。Research、Validation 与知识调研标准取证共同遵守。
---

# Evidence

`[learning]` 的轻量查询档只须遵守底线部分；Research、标准取证与 `[invest]` 执行完整规则。本能力单元被 research 与 validation 两步共用，证据规则只在本文件维护。

## 底线

1. 来源优先级：监管机构、交易所、指数公司、产品发行方正式文件 → 产品定期报告、发行文件与公告 → 可靠数据平台与券商行情 → 新闻、社区和搜索结果仅作线索。
2. 引用必须紧邻所支持的事实，且指向实际读取过的页面。
3. 区分净值、参考净值、市场价格、折溢价率、成交额和产品规模，不混用时点；折溢价须写明分母口径。
4. 交易币种、产品计价币种、底层暴露币种与报告币种分别标注。
5. 来源冲突时保留冲突记录，说明采用哪一项及理由。
6. 旧数据用于历史分析时标明其适用时点。

## 完整规则

7. 关键动态指随时间变化、影响买入、卖出、持有、定投、调仓或产品排序结论成立的数据；`database/data_contracts.md` 时效表的字段类别默认属关键动态。
8. 关键动态事实至少两个独立来源，同一机构两个页面不算独立来源。仅官方唯一来源且已说明（如 QDII 额度、申赎状态等仅官方公告的数据）的可通过；其余无双来源记 `unknown`。
9. 每条动态数据记录名称、数值、单位、统计口径、`valid_at`、来源 URL、`fetched_at`。URL 失效时用 `database/sources.csv` 的 `content_hash`、`raw_material_path` 留痕。
10. 关键动态字段超过 `database/data_contracts.md` 最大允许时效时记为 `unknown`，结论上限为 `wait` / `reject` / `research`。
11. 写入 `database/` 下 CSV 时列名以 `database/csv_schema.md` 为准。
