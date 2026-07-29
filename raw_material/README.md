# 原始材料摄入层

`raw_material/` 保存待蒸馏、可定位且允许保留的外部研究材料。它是 Research 的输入，不是事实库、知识库或投资结论。

## 写入顺序

1. 先在 [`database/sources.csv`](../database/sources.csv) 创建 `source_id`。
2. 用 `raw_material/<主题>/<source_id>.md` 保存合规摘录或可再现快照。
3. 经 Research 与 Validation 后，再将稳定结论写入 `knowledge/`，将结构化事实写入 `database/`，将本次分析写入 `reports/`。

未经 Validation 的材料不得作为 `verified` 事实或 Decision 依据。

## 每份材料的最小 frontmatter

```yaml
---
source_id:
material_type: excerpt
distillation_status: pending
published_at:
fetched_at:
source_url_or_file_ref:
content_hash:
hash_status: unavailable
locator:
access_note:
---
```

字段 `published_at` 为来源发布时间；字段 `fetched_at` 为取得时间。

## 隐私与版权

- 仅保存公开、允许保留且确有蒸馏价值的摘录或快照；不要把它变成网页全文缓存。
- 版权受限全文、券商原件、税单、完整账号、证件、密钥和认证信息留在仓库外的受控位置。
- 对仓库外材料，在 `sources.csv` 记录文件引用、哈希状态、定位信息和核验结论；不得复制敏感内容。
