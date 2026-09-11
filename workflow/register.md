# 来源登记场景入口

适用：把外部材料登记成 `source_id`，需要留存时保存合规摘录，供 Research 与 Validation 使用。

权威规则：[database/data_contracts.md](../database/data_contracts.md)「数据集契约」与「原始材料蒸馏」；写入字段与隐私边界见 [raw_material/README.md](../raw_material/README.md)。本文只指路，规则的权威位置如上。

流程：

1. 在 [database/sources.csv](../database/sources.csv) 建 `source_id`：记录来源等级、URL 或受控文件引用、取得时间与 `scope`。来源登记不等同于事实结论。
2. 需要留存摘录或可再现快照的，按 raw_material/README「写入顺序」与「最小 frontmatter」存到 `raw_material/<主题>/<source_id>.md`，并在 `sources.csv` 回填 `raw_material_path`。
3. 经 Research 与 Validation 后，稳定结论写 `knowledge/`，结构化事实写 `database/`，本次分析写 `reports/`。

本场景的产物是来源登记行与 `raw_material/` 摘录；Committee 的作用域只有 DD 第 3–6 步。材料经 Validation 通过后才记 `verified`、才作为 Decision 依据；账号、证件、银行卡、认证秘密与券商原件留在本机，入库内容限于合规摘录，见 [OPERATIONS.md](../OPERATIONS.md)「§12.1 隐私、来源与备份」。
