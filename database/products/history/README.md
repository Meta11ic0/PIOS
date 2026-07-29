# 产品动态历史

本目录保存产品**动态指标**的追加记录。不得用新观测覆盖旧行。

## 约定

- 主表：`etf_metrics.csv`。当前仅表头为模板；有真实观测后再追加行。
- 同一 `product_id` 可有多行；查询某时点取适用时点不晚于该时点的最新一行。
- 未知值留空；`verification_status` 使用 `verified / conflicting / missing / pending`。
- 字段含义与 [../schema.yaml](../schema.yaml) 中 `category: dynamic` 字段一致。
- **禁止编造行情**；无核验数据时不要填充数值。

## 追加步骤

1. 确认 `product_id` 已在产品目录或观察池中存在。
2. 采集数值、单位、口径、适用时点（列名 `valid_at`）、来源 URL、取得时间（列名 `fetched_at`）。
3. 在 CSV **末尾追加一行**，不修改历史行。
4. 需要对照基础费率等时，读产品基础记录，不把基础字段写入本历史表。
