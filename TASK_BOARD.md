# Alpha Mining OS 任务板

## 规则

- 任务 ID 格式：`<AREA>-YYYYMMDD-NNN`；全仓唯一，不得复用或改义。
- 每项任务必须填写涉及模块、负责人窗口、状态、依赖、本地 commit 和测试报告。
- 状态仅用 `BACKLOG`、`READY`、`IN_PROGRESS`、`BLOCKED`、`DONE`、`CANCELLED`。
- 同一模块同一时间最多一个 `IN_PROGRESS` 写任务；只读审计不占写锁。
- 并行窗口必须使用不同的本地 Git branch/worktree；不得共享可写工作树。
- 完成时必须更新本文件、`PROJECT_STATUS.md`、所有相关模块文档和恰好一份本任务测试报告，然后本地提交。
- commit 字段在提交前可写 `PENDING`；提交后由任务记录或后续治理提交写入 SHA。服务器部署记录不能替代本地 commit。

## 当前任务

| 任务 ID | 涉及模块 | 负责人窗口 | 状态 | 依赖 | 本地 commit | 测试报告 |
| --- | --- | --- | --- | --- | --- | --- |
| `GOV-20260718-001` | deployment_and_operations, provider_and_skills, 全部模块文档 | root | DONE | 无 | `HEAD`（本治理提交） | `docs/test_reports/project_governance_and_gpt_codex_bridge_20260718.md` |
| `GOV-20260718-002` | deployment_and_operations, provider_and_skills | root | DONE | `GOV-20260718-001` | `HEAD`（公开审阅配置收口提交） | `docs/test_reports/project_governance_and_gpt_codex_bridge_20260718.md` |
| `GOV-20260718-003` | research_exchange, research_center, provider_and_skills, deployment_and_operations | root | DONE | `GOV-20260718-002` | `HEAD`（资产仓库骨架治理提交） | `docs/test_reports/project_governance_and_gpt_codex_bridge_20260718.md` |
| `KNOW-20260718-002` | research_exchange, research_center, provider_and_skills | root | DONE | `GOV-20260718-003` | `PENDING`（本治理提交） | `docs/test_reports/knowledge_collection_20260718.md` |
| `KNOW-20260718-003` | research_exchange, research_center, provider_and_skills | root | DONE | `KNOW-20260718-002` | `PENDING` | `docs/test_reports/correlation_practice_collection_20260718.md` |
| `GOV-20260718-004` | deployment_and_operations, provider_and_skills | root | BLOCKED | `GOV-20260718-003` | `HEAD` | `docs/test_reports/github_ephemeral_workspace_validation.md` |
| `GOV-20260718-005` | deployment_and_operations, provider_and_skills | root | DONE | `GOV-20260718-004` | `HEAD` | `docs/test_reports/auxiliary_repository_concurrency_and_cleanup_20260718.md` |
| `REG-20260718-001` | platform_registry | root | DONE | `GOV-20260718-003` | `HEAD`（本任务治理与验证提交） | `docs/test_reports/platform_registry_upstream_validation_20260718.md` |
| `REG-20260718-002` | platform_registry, deployment_and_operations | root | BLOCKED | `REG-20260718-001`, wqa `cea82e2119b8a91db818722294ef45d18e3f6a6b` | `a4ae0da` | `docs/test_reports/platform_registry_server_validation_20260718.md` |
| `CORE-20260718-003` | core_models, execution_transport, factor_center | root | DONE | `REG-20260718-002` | `HEAD`（本任务提交） | `docs/test_reports/consultant_core_model_validation_20260718.md` |
| `CORE-20260718-004` | platform_registry, core_models, governance source baseline | RESULT-20260718-001 窗口 | DONE | `REG-20260718-002`, `CORE-20260718-003` | `HEAD`（恢复合并提交） | `docs/test_reports/core_baseline_recovery_20260718.md` |
| `RESULT-20260718-001` | research_exchange, factor_center, research_center, execution_transport | root | DONE | `CORE-20260718-004` | `3c45178` | `docs/test_reports/result_ingestion_offline_20260718.md` |
| `SCHED-20260718-001` | candidate_plan, task_center, scheduler, execution_transport | root | DONE | `CORE-20260718-003`, `REG-20260718-002` | `HEAD`（本任务提交） | `docs/test_reports/single_multi_allocation_validation_20260718.md` |
| `GOV-20260718-006` | deployment_and_operations, user_handoff, wq-review publication | root | DONE | `SCHED-20260718-001`, `GOV-20260718-005` | `HEAD`（本任务提交） | `docs/test_reports/user_handoff_and_review_publication_20260718.md` |
| `LIVE-SCHED-20260718-002` | candidate_plan, allocation, scheduler, execution_transport, deployment_and_operations | root | READY | `SCHED-20260718-001`, `REG-20260718-002`, `RESULT-20260718-001` integration boundary | `PENDING` | `docs/test_reports/live_single_multi_scheduler_validation_20260718.md` |
| `INTEGRATE-LIVE-20260718-001` | candidate_plan, task_center, scheduler, execution_transport, result_ingestion, factor_center, research_center, deployment_and_operations, wq-review publication | root | DONE | `SCHED-20260718-001`, `RESULT-20260718-001`; runtime continuation completed by `RUNTIME-RECOVER-LIVE-20260718-001` | `724ab02f` | `docs/test_reports/integrate_live_result_loop_20260718.md` |
| `RUNTIME-RECOVER-LIVE-20260718-001` | task_center API, worker/queue, ai/campaign runtime dependencies, execution_transport, deployment_and_operations, wq-review publication | root | DONE | `INTEGRATE-LIVE-20260718-001`, `724ab02f` | `PENDING` | `docs/test_reports/runtime_recover_live_20260718.md` |
| `AI-BACKEND-ARCH-AUDIT-20260718-001` | ai_mode, research_exchange, research_center, provider_and_skills, task_center, execution_transport | root | DONE | `GOV-20260718-005`; GPT review `wq-review@73189d5` PASS | `0b6eecf` | `docs/test_reports/autonomous_ai_backend_architecture_audit_20260718.md` |
| `AI-AUTONOMOUS-BACKEND-V1-20260718-001` | ai_mode, provider_and_skills, research_center, research_exchange, factor_center, platform_registry, candidate_plan, task_center, execution_transport | root | CANCELLED | ownership transferred to `AI-AUTONOMOUS-BACKEND-V1-20260719-001` after GPT architecture PASS | `ef06fee9` | `docs/test_reports/autonomous_ai_backend_v1_baseline_20260718.md` |
| `AI-AUTONOMOUS-BACKEND-V1-20260719-001` | ai_mode, provider_and_skills, research_center, research_exchange, factor_center, platform_registry, candidate_plan, task_center, execution_transport | root | DONE | `AI-BACKEND-ARCH-AUDIT-20260718-001` GPT PASS; Phase 0 PASS from clean `ef06fee9`, one Alembic Head | `886d11ad` | `docs/test_reports/autonomous_ai_backend_v1_20260719.md` |
| `GOV-20260719-001` | deployment_and_operations, 审阅白名单/manifest、根治理文档、历史 archive | review-governance | DONE | `AI-BACKEND-ARCH-AUDIT-20260718-001`; cumulative review baseline published | `ae8f544a` | `docs/test_reports/ai_backend_architecture_review_delivery_governance_20260719.md` |

## 累计审阅历史入口

`GOV-20260719-001` 必须在发布前检查 [completed_asset_migration_index](archive/completed_asset_migration_index.md)。该入口明确保留 `REG`、`CORE`、`SCHED`、`RESULT`、`RUNTIME-RECOVER`、`TPL`、`consultant_core`、`single_multi_allocation`、allocation decision、capacity policy 和 scheduler recovery；缺少任一资产、旧文档到当前权威文档的链接，或 archive 被覆盖/删除，均不得发布 `wq-review`。

## 写锁

`KNOW-20260718-003` 已完成并释放 research_exchange、research_center、provider_and_skills 的治理文档写锁；业务代码保持只读。

`REG-20260718-001` 使用独立 worktree 完成 platform_registry 上游资产审计与离线验证；未进入上述研究模块写锁范围，任务完成后不保留写锁。

`REG-20260718-002` 为 `BLOCKED`，当前不持有活动写锁；恢复工作不得据此执行部署、修改回测队列、API Gate、Admission 阈值、候选优先级或调用真实 WorldQuant。

`CORE-20260718-003` 已完成并释放 core_models、execution_transport 与 factor_center 写锁。

`SCHED-20260718-001` 执行期间使用独立 `codex/SCHED-20260718-001-root` 分支/worktree，并持有 candidate_plan、task_center、scheduler、execution_transport 写锁。共享文件清单：`backend/app/models.py`、`backend/alembic/versions/20260718_0030_single_multi_allocation.py`、`docs/TASK_BOARD.md`、`docs/PROJECT_STATUS.md`、`docs/MODULE_INDEX.md`；不修改 Result Ingestion、Factor Center、Research Center、Feedback Delta、真实 WQ worker 或服务器 Gate。

`SCHED-20260718-001` 已完成并释放上述写锁；等待独立 RESULT 集成任务合并共享模型与迁移。

`GOV-20260718-006` 已完成并释放 `wq-review/main` 单写者发布锁。

`LIVE-SCHED-20260718-002` 已由用户协调暂停并释放 CandidatePlan、Allocation、Capacity、Scheduler、Execution Transport 与服务器切换写锁；其固定提交由 `INTEGRATE-LIVE-20260718-001` 语义吸收，原 worktree 保持只读。

`AI-BACKEND-ARCH-AUDIT-20260718-001` 已完成架构审计并通过 GPT 审阅，已释放其治理文档写锁；其审计契约和阶段计划由当前 V1 任务使用。

`GOV-20260719-001` 已完成并释放 review 生成器、累计历史 archive、根治理文档和审计材料写锁；不持有论坛采集模块文档或任何业务代码/迁移写锁。

其他模块在本任务中仅更新治理文档，不授权业务代码写入。

`CORE-20260718-004` 已完成并释放 platform_registry、core_models 与 Git 事实源恢复写锁；恢复过程未授权真实 WQ、部署、API Gate、前端或 RESULT 业务实现。

`RESULT-20260718-001` 已完成并释放 research_exchange、factor_center、research_center 与 execution_transport 写锁。离线事实账本、标准化、幂等投影、Parent/Child 聚合、Checks、五类 Correlation、Research Feedback Delta 与有界导出均由本任务报告验证；未调用真实 WQ、创建真实任务或修改 API Gate。

`INTEGRATE-LIVE-20260718-001` 的离线集成和修正后的服务器 `0031` 迁移已通过，但 clean Git checkout 缺少 `main.py`/worker 的多项运行依赖，API 无法启动。任务转为 `BLOCKED` 并释放业务/部署写锁；API Gate 保持关闭且未调用真实 WQ。`wq-review/main` 发布锁在本报告快照发布后释放。

`RUNTIME-RECOVER-LIVE-20260718-001` 已完成并释放 task_center/API、worker/queue、ai/campaign 运行依赖、execution_transport、deployment_and_operations、服务器切换与 `wq-review/main` 写锁。恢复文件逐个进入 Git，并从 clean checkout 验证；`0031` 未降级或重建。受控 12+30 批次完成后 Gate 已关闭，服务保持 `LIVE_LOOP_RUNNING`，无活动容量预留或重复派发。

`AI-AUTONOMOUS-BACKEND-V1-20260718-001` 已取消并释放 V1 写锁；其固定提交仅作为可验证的已提交实现来源，不得从其 worktree 复制未提交文件。

`AI-AUTONOMOUS-BACKEND-V1-20260719-001` 在独立 `codex/AI-AUTONOMOUS-BACKEND-V1-20260719-001-root` worktree 中持有 ai_mode、provider_and_skills、research_center、research_exchange、factor_center、platform_registry、candidate_plan、task_center 与 execution_transport 的 V1 写锁。Phase 0 仅允许离线 Mock/SQLite 基线验证；禁止真实 WQ、部署、前端改动、API Gate 开启、Prod Corr 和最终 Alpha 提交。

`AI-AUTONOMOUS-BACKEND-V1-20260719-001` 已完成本地验收并释放上述 V1 写锁；状态为 `READY_FOR_GPT_BACKEND_V1_REVIEW`。后续服务器、前端或真实执行工作必须另建任务。
