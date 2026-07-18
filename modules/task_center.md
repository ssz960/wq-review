# task_center

## 目标职责

管理任务、候选准入、共享队列、优先级、执行请求、状态/日志与人工控制，是候选到执行适配层之间的控制面。

## 入口文件

- `backend/app/main.py`
- `backend/app/mining/service.py`
- `backend/app/tasks.py`
- `backend/app/task_queue.py`、`backend/app/worker.py`
- `frontend/src/pages/task-center/TaskCenterPage.jsx`（只读消费者）

## 模型

`TaskRun`、`TaskLog`、`CandidateRecord`、`ExecutionRequest`、`SimulationBatch*`、`DailySimulationBudget`、`PriorityEvent`、`BanRule`、`GovernanceActionRecord`、`MiningEvent` 等集中在 `backend/app/models.py`。

## API

`/api/task-runs*`、`/api/task-center/top-stats`、`/api/task-center/tasks/compact`、`/api/task-center/timeline*`、`/api/task-center/control`、`/api/task-center/llm-scheduler-*`，以及 `/api/mining/execution-*`、`/api/mining/events`、priority/ban 路由。

## 流程

`创建任务 -> 候选计划/候选池 -> 硬准入 -> execution_requests -> 有界调度 -> adapter -> poll/writeback -> factor_check/event/timeline`。

## 依赖

数据库/RQ、Platform Registry、Research Scheduler、Execution Transport、Factor Center、AI supervisor 和配置。

## 安全边界

不得绕过硬准入、队列、API Gate、并发/预算限制或 execution adapter。控制状态未知时失败关闭。本任务不执行任何队列任务。

## 当前实现

FastAPI 路由与服务函数广泛存在；`service.py` 约 527 KB，是任务、准入、调度、supervisor 与观测的高耦合实现。运行中队列、迁移状态和服务器数据为 `UNVERIFIED`。

## 已确认设计

任务中心是唯一调度控制面；前端只通过 API 展示/控制；执行结果写回统一审计链。

## 已知 Bug

- 超大 `service.py` 增加重复逻辑和权限边界回归风险。
- `tasks.py` 同时包含执行循环和连续 AI 研究循环，职责较重。
- 历史测试/runner 数量多且部分可触发 real provider/WQ，不能批量运行。

## 验证记录

2026-07-18 静态枚举路由、入口、模型和测试文件；无业务代码改变、无任务执行。

## 相关报告

- `docs/test_reports/project_governance_and_gpt_codex_bridge_20260718.md`

## SCHED-20260718-001

CandidatePlan allocation now has a read-only deterministic preview and a
confirmed immutable plan. Single and Multi share Admission evidence, Capacity
reservation, existing Batch tables, scheduler entry and audit. Capacity absence
returns DEFER; preview never creates an ExecutionRequest.

## INTEGRATE-LIVE-20260718-001

Confirmed Allocation decisions and legacy admitted requests converge on the same Batch scheduler without a second execution-state table. Server dispatch stayed disabled because the clean Git runtime could not start.
