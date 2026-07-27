# 评价运行记录（screening runs）

每次 Modeling 比较的输出落在本目录。关联 `model_version` 与 `input_as_of`，便于复盘；修改模型规则时另起版本，不覆盖历史 run。

**演示 run** 只放在 [`demo/`](demo/)，必须 `scope: demo_only`；生产 Modeling 输入清单排除该目录。

## 文件命名

```text
YYYY-MM-DD-<topic>-<model_version>.yaml
```

示例（仅说明格式，仓库中可不预置实例）：

```text
2026-07-27-nasdaq100-etf-v0.1.yaml
```

## 必填顶层字段

| 字段 | 含义 |
|---|---|
| `run_id` | 本次运行稳定 ID |
| `scope` | `production` 或 `demo_only` |
| `model_version` | 对应 `etf_model_v0.1` 等模型标识 |
| `input_as_of` | 输入动态数据统一时点 |
| `created_at` | 运行记录创建时间 |
| `universe` | 候选 `product_id` 列表 |
| `hard_gate_results` | 各否决项通过/未通过/`unknown` |
| `field_comparison` | 字段对比表（非自动评分亦可） |
| `dimension_scores` | 分项占位；未确认权重时留空或标 `null` |
| `total_score` | 总分占位；draft 阶段应为 `null` |
| `limitations` | 阈值未确认、数据缺失等限制 |
| `verification_status` | `draft / reviewed / unknown` 等 |

复制 [_template.yaml](_template.yaml) 后填写。**不要伪造评分结果**；阈值与权重未确认前，`dimension_scores` / `total_score` 保持空或 `null`，只用字段对比与否决项。`unknown` 硬门槛表示停止/预检，不表示比较通过。
