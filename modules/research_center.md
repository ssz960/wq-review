# research_center

## 目标职责

把研究知识、证据、记忆、复盘、会话和策略聚合为受控上下文与只读资产视图，支持审计和有限导出。

## 入口文件

- `backend/app/mining/research_center.py`
- `backend/app/mining/research_memory_v2.py`
- `backend/app/mining/research_policy.py`
- `backend/app/mining/context_service.py`
- `backend/app/mining/campaign_workspace_service.py`
- `frontend/src/pages/ResearchCenter.jsx`（只读审计）

## 模型

`ResearchMemoryRecord`、`ResearchKnowledgeRecord`、`ResearchKnowledgeEvidenceRecord`、`ResearchRetrievalLog`、`ResearchCampaign`、Conversation/Context/Instruction/Intent 系列模型。

## API

`/api/research/freshness`、`/assets*`、`/llm-review*`、`/export/*`；campaign 的 workspace/messages/context/dialogue/intent 路由集中在 `backend/app/main.py`。

## 流程

`event/result/material -> normalize knowledge/evidence -> bounded retrieval -> context/checkpoint -> AI/人工复盘 -> audited decision/export`。

## 依赖

Task Center、Factor Center、AI Mode、数据库、Research Exchange 和 package services。

## 安全边界

只提供有界、脱敏上下文；不得把数据库、完整 prompt、原始日志、完整曲线或生产包直接公开。强治理动作仍需确定性权限和人工确认。

## 当前实现

资产列表/详情、evidence CSV、freshness/strong-governance 检查、LLM review、research pack export、memory top-k/token budget 和 retrieval log 代码存在。生产数据 provenance 与脱敏完整性为 `UNVERIFIED`。

## 已确认设计

研究知识与执行分离；检索必须有界并记录；wq-review 只消费摘要，不消费此模块的原始导出包。

## 已知 Bug

- Research Center、Memory、Package、Context 分散在多文件且与 AI campaign 强耦合。
- `export_research_pack` 能生成 ZIP，与当前公开审阅规则不兼容，不能复用为 wq-review。
- 历史 docs/reports 可能含真实研究结果，不能按目录整体复制。

## 验证记录

2026-07-18 静态审计函数、路由、模型与导出边界；未读取研究数据库或生成研究包。

## 相关报告

- `docs/test_reports/project_governance_and_gpt_codex_bridge_20260718.md`
