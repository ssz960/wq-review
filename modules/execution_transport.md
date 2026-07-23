# execution_transport

## 目标职责

为调度器提供统一的 submit/poll/batch transport 协议，把真实与 mock 执行隔离在适配器后，并规范错误、限流和恢复。

## 入口文件

- `backend/app/mining/execution_adapter.py`
- `backend/app/mining/consultant_multi_transport.py`
- `backend/app/mining/service.py`
- `backend/app/tasks.py`

## 模型

`ExecutionRequest`、`SimulationBatch`、`SimulationBatchChild`、`DailySimulationBudget`、`ExecutionRateLimitBucket`；适配器内有 `SubmitResult`、`PollResult` 和 batch result 数据类。

## API

`/api/mining/execution-requests`、`/dispatch`、`/recover`、`/execution-plane/summary`、`/simulation-transport/stats`。真实远端 API 不是公开前端接口。

## 流程

`admitted request -> scheduler capacity/budget -> build adapter -> submit -> remote_ref -> bounded poll -> normalized result/error -> database/event writeback`。

## 依赖

Task Center、配置、安全 gate、requests、数据库；Multi transport 消费 Platform Registry 的 profile/snapshot。

## 安全边界

默认 mock/disabled。真实 adapter 只能在明确人工授权、gate 开启、资源/预算/并发检查通过后由统一调度器调用；严禁脚本或 AI 直接调用。

## 当前实现

`WorldQuantExecutionAdapter`、`MockExecutionAdapter`、adapter factory、错误分类与 batch transport 实现存在。服务器凭据、当前 gate、远端兼容性和生产稳定性为 `UNVERIFIED`。

## 已确认设计

传输层不决定研究方向；调度状态持久化后才能调用 adapter；服务器不是源码事实源。

## 已知 Bug

- 仓库含多个 `real_wq_*` runner、probe 和历史脚本，误运行风险高。
- 单体 service 与 tasks 对 transport 进行编排，边界复杂。
- Multi 迁移/测试存在但本治理任务不复验业务语义。

## 验证记录

2026-07-18 仅静态检查协议、实现类、路由和模型；未实例化 real adapter、未网络调用。

## 相关报告

- `docs/test_reports/phase2_consultant_multi_transport_20260718.md`（历史阶段报告）
- `docs/test_reports/project_governance_and_gpt_codex_bridge_20260718.md`

## SCHED-20260718-001

The unified Mock path represents Single as a one-Child `SimulationBatch` and
Multi as a 2-10 Child Batch. It persists slot reservation before marking work
reserved, preserves Child facts across Parent errors, and recovers Pending, 429
and restart without calling the real adapter.

## AI-PROVIDER-MOCK-E2E-V1-20260722-002

The shared execution facts remain the only permitted Mock path:
`execution_requests + simulation_batches + simulation_batch_children`, followed
by Result Ingestion and Factor Center. This server acceptance was blocked
before Proposal materialization because the authorized Provider budget was
exhausted. No second queue, result table, Mock state machine, execution
request, result fact, or WQ call was created.
