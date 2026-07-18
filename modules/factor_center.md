# factor_center

## 目标职责

汇总候选/执行后的因子记录、检查结果和轻量指标，支持导入、筛选、同步作业与只读研究建议；不负责提交 Alpha。

## 入口文件

- `backend/app/mining/factor_center.py`
- `backend/app/mining/factor_center_file_import.py`
- `backend/app/mining/factor_checks.py`
- `backend/app/main.py`
- `frontend/src/pages/FactorCenter.jsx`（只读审计）

## 模型

`FactorCenterAlphaRecord`、`FactorCenterImportRun`、`FactorCenterSyncJobRecord`、`FactorCheckRecord`、`FilterPresetRecord`，集中在 `backend/app/models.py`。

## API

`/api/factor-center/alphas*`、`/ai-assistant`、`/import`、`/sync/*`、`/filter-presets*`，以及 `/api/mining/factor-checks`。

## 流程

`execution/factor_check 或文件 -> normalize/import -> alpha record -> filters/detail/assistant`；显式同步作业另走 `create job -> worker -> official read/normalize -> persist`。

## 依赖

Task Center、Execution Transport、数据库、文件导入和可选的官方只读数据接口。

## 安全边界

本地与本任务禁止真实 WQ。任何官方同步必须是人工确认的只读操作，不能提交；数据库、完整曲线/PnL、原始响应不得进入 Git 或 wq-review。

## 当前实现

导入、列表/详情、助手摘要、筛选预设、后台同步与 official source 代码均存在；`factor_center.py` 约 171 KB。实际服务器同步状态和历史数据可信度为 `UNVERIFIED`。

## 已确认设计

Factor Center 是结果/研究视图，不是执行入口；未知指标不能被推断为通过；同步作业需留痕。

## 已知 Bug

- 大文件包含导入、官方读取、筛选、AI 展示与 worker，多职责耦合。
- 旧 legacy file、database、official、official_alpha 多源并存，epoch/provenance 一致性风险。
- 历史报告含生产结果和大附件，不可直接公开。

## 验证记录

2026-07-18 静态审计函数、模型和路由；未读取生产数据库、未启动同步。

2026-07-18 `CORE-20260718-003` 增加五类互不覆盖的 append-only Correlation Observation、Power Pool 历史成员与 SuperAlpha requested/eligible/effective Selection 模型。它们是规范事实模型，不授权 Factor Center 覆盖原始 Observation，也不编码未经验证的阈值、额度或自动准入规则。

## 相关报告

- `docs/test_reports/project_governance_and_gpt_codex_bridge_20260718.md`

## RESULT-20260718-001

Execution-result writes now project from `NormalizedExecutionResult` through `normalized_result_id`. The projection is idempotent and retains normalized provenance; Factor Center does not interpret raw transport payloads and remains a read/query model. Legacy file and official-read imports are unchanged and are not Result Ingestion paths.
