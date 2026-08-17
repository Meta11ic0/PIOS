---
name: documentation
description: 将投资研究、数据更新和决策沉淀为统一、可追溯、可维护的项目记录。生成报告、更新知识库或记录决策时使用。
---

# Documentation

## 写入时机

- DD 记录各阶段小节：每步完成后立即写入
- Decision Log：Decision 阶段完成后创建，Documentation 阶段补完 `frozen_at` 与内容哈希
- 成交记录：用户告知明细后追加

## 归属判断

- 稳定概念 → `knowledge/`
- 结构化事实 → `database/`
- 可重复步骤 → `workflow/`
- 一次决策 → `decision_log/`
- 阶段性分析 → `reports/`

使用 `templates/` 中最接近的模板。记录原始证据快照，不用事后结果改写当时理由。

## 必含字段

- 标题、状态、创建时间、最后更新时间
- 适用范围和数据适用时点
- 来源或上游记录
- 已知限制和待办
- 复核条件或更新频率

写入前检查重复：已有条目应更新并保留变更原因，不创建同义副本。动态数据与长期知识分离。

## Decision Log 必含内容

当时目标、候选方案、采用证据、风险、Challenge、最终行动、失效条件和未来复盘结果。
