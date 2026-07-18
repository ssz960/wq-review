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

2026-07-18 `CORE-20260718-003` 离线基线提供版本化 Simulation Profile、三类 Submission Policy、最多十个 Child 的 Multi Parent/Child 状态模型与不创建队列的 Mock Scheduler。该加法模型不替换 `ExecutionRequest`、Admission、API Gate、优先级、现有 Single transport 或真实 adapter；旧运输执行级回归在完整 `db.py` 进入事实源前仍为 `UNVERIFIED`。

## 相关报告

- `docs/test_reports/phase2_consultant_multi_transport_20260718.md`（历史阶段报告）
- `docs/test_reports/project_governance_and_gpt_codex_bridge_20260718.md`

## RESULT-20260718-001

Execution Transport now hands terminal raw envelopes to `result_ingestion.py`. It no longer owns result normalization, checks, correlation classification, Factor Center projection, or Research Center feedback semantics. The existing adapter, scheduler, admission and API Gate boundaries were not changed. Transport batch rows receive only the canonical terminal projection and aggregate produced by Result Ingestion.

## SCHED-20260718-001

The unified Mock path represents Single as a one-Child `SimulationBatch` and
Multi as a 2-10 Child Batch. It persists slot reservation before marking work
reserved, preserves Child facts across Parent errors, and recovers Pending, 429
and restart without calling the real adapter.

## INTEGRATE-LIVE-20260718-001

Confirmed Allocation decisions and legacy admitted requests now converge on the same `SimulationBatch`/Child scheduler. Terminal Child envelopes enter Result Ingestion before projection; duplicate poll/pullback is keyed by request, batch, Child, remote reference and payload hash. Allocation reservations are released idempotently even when Result Ingestion releases them before the transport aggregate. Offline and migration verification passed; real transport remains stopped because the runnable API/worker dependency set is absent from Git.

## RUNTIME-RECOVER-LIVE-20260718-001

The runnable Git source completed the explicitly authorized bounded validation: one 10-Child Multi plus two Singles, then three 10-Child Multi Parents. All 42 Child requests reached terminal completed state, each active Parent released its allocation reservation, and the daily ledger reached 42 submitted with zero remaining. No duplicate request, 429 event, lost Child result or permanent slot lease was observed. Gate is now closed; this is evidence for the deployed commit, not a standing authorization for future real execution.
