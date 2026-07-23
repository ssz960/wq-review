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
| `KNOW-20260718-004` | research_exchange, research_center, provider_and_skills | root | DONE | `KNOW-20260718-003` | `PENDING` | `docs/test_reports/consultant_mainline_evidence_20260718.md` |
| `KNOW-20260718-005` | research_exchange, research_center, provider_and_skills | root | DONE | `KNOW-20260718-004` | `PENDING` | `docs/test_reports/alpha_practice_collection_20260718.md` |
| `GOV-20260718-004` | deployment_and_operations, provider_and_skills | root | BLOCKED | `GOV-20260718-003` | `HEAD` | `docs/test_reports/github_ephemeral_workspace_validation.md` |
| `GOV-20260718-005` | deployment_and_operations, provider_and_skills | root | DONE | `GOV-20260718-004` | `HEAD` | `docs/test_reports/auxiliary_repository_concurrency_and_cleanup_20260718.md` |
| `REG-20260718-001` | platform_registry | root | DONE | `GOV-20260718-003` | `HEAD`（本任务治理与验证提交） | `docs/test_reports/platform_registry_upstream_validation_20260718.md` |
| `REG-20260718-002` | platform_registry, deployment_and_operations | root | BLOCKED | `REG-20260718-001`, wqa `cea82e2119b8a91db818722294ef45d18e3f6a6b` | `a4ae0da` | `docs/test_reports/platform_registry_server_validation_20260718.md` |
| `CORE-20260718-003` | core_models, execution_transport, factor_center | root | DONE | `REG-20260718-002` | `HEAD`（本任务提交） | `docs/test_reports/consultant_core_model_validation_20260718.md` |
| `SCHED-20260718-001` | candidate_plan, task_center, scheduler, execution_transport | root | DONE | `CORE-20260718-003`, `REG-20260718-002` | `HEAD`（本任务提交） | `docs/test_reports/single_multi_allocation_validation_20260718.md` |
| `AI-SERVER-MOCK-V1-20260719-001` | ai_mode, platform_registry, task_center, execution_transport, research_center, provider_and_skills, deployment_and_operations | server-mock-v1 | DONE/PASS | local recovery `e95a23f`; server Active Registry and 0031 | `e90a7172fd57d6b1d3fcda2259935842cda2dec4` | `docs/test_reports/autonomous_ai_server_mock_v1_20260719.md`; cumulative history `docs/archive/completed_asset_migration_index.md` |
| `AI-REAL-PROVIDER-PREFLIGHT-V1-20260722-001` | ai_mode, provider_and_skills, deployment_and_operations, research_center, platform_registry, task_center | real-provider-preflight-v1 | BLOCKED | `AI-SERVER-MOCK-V1-20260719-001`; responsive server SSH, Active Registry and Provider credential | `95674fd4ab6ef6e6fb1eb6383ae43b65e9f50ceb` (implementation), `777e157608ab64fbbaff15b0a0583cd372935fdf` (evidence) | `docs/test_reports/real_provider_preflight_v1_20260722.md` |
| `AI-PROVIDER-MOCK-E2E-V1-20260722-002` | ai_mode, provider_and_skills, platform_registry, task_center, execution_transport, research_center, deployment_and_operations | provider-mock-e2e-v1 | BLOCKED | `AI-REAL-PROVIDER-PREFLIGHT-V1-20260722-001`; server SSH, Active Registry, Provider output stability | `3273770cd3b808f9e7be1942d78bb6735eaf6507` (implementation and server deployment) | `docs/test_reports/provider_generated_mock_e2e_v1_20260722.md` |

## 写锁

`KNOW-20260718-003` 已完成并释放 research_exchange、research_center、provider_and_skills 的治理文档写锁；业务代码保持只读。
`KNOW-20260718-004` 已完成并释放 research_exchange、research_center、provider_and_skills 的治理文档写锁；业务代码保持只读。
`KNOW-20260718-005` 已完成并释放 research_exchange、research_center、provider_and_skills 的治理文档写锁；业务代码保持只读。

`SCHED-20260718-001` 执行期间使用独立 `codex/SCHED-20260718-001-root` 分支/worktree，并持有 candidate_plan、task_center、scheduler、execution_transport 写锁。共享文件清单：`backend/app/models.py`、`backend/alembic/versions/20260718_0030_single_multi_allocation.py`、`docs/TASK_BOARD.md`、`docs/PROJECT_STATUS.md`、`docs/MODULE_INDEX.md`；不修改 Result Ingestion、Factor Center、Research Center、Feedback Delta、真实 WQ worker 或服务器 Gate。

`SCHED-20260718-001` 已完成并释放上述写锁；RESULT 集成历史入口保留在累计审阅索引。

其他模块在本任务中仅更新治理文档，不授权业务代码写入。

`AI-SERVER-MOCK-V1-20260719-001` 已完成并释放后端、迁移、部署与治理文档写锁；未获得前端、真实 Provider、真实 WQ、相关性服务或最终提交权限。

`AI-SERVER-MOCK-V1-20260719-001` 的累计审阅历史保留于 [完成资产迁移索引](history/completed_asset_migration_index.md)，不会因新快照生成而覆盖或删除。

`AI-REAL-PROVIDER-PREFLIGHT-V1-20260722-001` 已释放 ai_mode、provider_and_skills、deployment_and_operations、research_center、platform_registry 与 task_center 的后端及治理文档写锁。本地 Provider 预检入口、审计/Schema/Proposal Dry Run 模型与迁移已提交为 `95674fd4ab6ef6e6fb1eb6383ae43b65e9f50ceb`；服务器 SSH banner 阻断部署和真实 Provider 验收。前端、旧 `/worldquant`、真实执行 adapter、WQ Gate、相关性服务和最终提交路径未修改。

`AI-PROVIDER-MOCK-E2E-V1-20260722-002` 使用独立 `codex/AI-PROVIDER-MOCK-E2E-V1-20260722-002-implementation` 分支/worktree；实现/服务器部署 Commit 为 `3273770cd3b808f9e7be1942d78bb6735eaf6507`，任务状态为 `BLOCKED`。Provider 预算耗尽后未启动 Mock 执行，唯一报告为 `docs/test_reports/provider_generated_mock_e2e_v1_20260722.md`。本任务已释放 ai_mode、provider_and_skills、platform_registry、task_center、execution_transport、research_center 与 deployment_and_operations 的全部写锁；不修改前端、旧 `/worldquant`、真实执行 adapter、WQ Gate、Single/Multi 容量、相关性或最终提交路径。
