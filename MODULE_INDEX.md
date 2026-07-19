# Alpha Mining OS 模块索引

本索引是当前模块文档的唯一入口；子设计文档由对应主模块维护。

| 模块 | 文档 | 主要代码入口 | 状态摘要 |
| --- | --- | --- | --- |
| consultant_core | [execution_transport](modules/execution_transport.md) | `backend/app/mining/consultant_core.py` | Consultant领域兼容模型；不作为真实执行状态源。 |
| single_multi_allocation | [single_multi_allocation_design](modules/single_multi_allocation_design.md) | `backend/app/mining/single_multi_allocation.py` | Deterministic preview/confirm, shared Capacity reservation and recovery. |
| platform_registry | [platform_registry](modules/platform_registry.md) | `backend/app/mining/platform_registry.py` | V1 materializer 先绑定 Active Snapshot/Profile 再 hard admission；服务器 Registry 仍 UNVERIFIED。 |
| task_center | [task_center](modules/task_center.md) | `backend/app/main.py`、`backend/app/mining/service.py` | V1 只读取受信控制状态并复用 scoped mock dispatcher；Gate 默认关闭。 |
| ai_mode | [ai_mode](modules/ai_mode.md) | `backend/app/mining/autonomous_v1_orchestrator.py` | V1 Round 编排与恢复已在本地 Mock 验证；遗留 Agent/Supervisor 统一仍待后续兼容工作。 |
| execution_transport | [execution_transport](modules/execution_transport.md) | `backend/app/mining/execution_adapter.py`, `backend/app/mining/result_ingestion.py` | V1 不新建 transport，只复用 shared mock scheduler 和 canonical Result Ingestion。 |
| factor_center | [factor_center](modules/factor_center.md) | `backend/app/mining/factor_center.py` | 导入、筛选、同步 API 与 Result Ingestion 读模型投影存在。 |
| template_center | [template_center](modules/template_center.md) | `backend/app/template_library.py` | 模板抽取、CRUD 与展开存在。 |
| research_center | [research_center](modules/research_center.md) | `backend/app/mining/research_center.py` | V1 Round/MemoryProposal/Assistance 与有界反馈已本地验证；生产数据边界未验证。 |
| research_exchange | [research_exchange](modules/research_exchange.md) | `backend/app/mining/research_exchange_v2.py` | V2 assistance package attach/context rebuild 已本地验证；不交换原始执行结果。 |
| provider_and_skills | [provider_and_skills](modules/provider_and_skills.md) | `backend/app/mining/autonomous_v1_provider.py`、`backend/app/mining/autonomous_v1_skills.py` | V1 Profile/Secret、versioned Skill contract 和 fixture acceptance 已实现；真实 Provider 未验证。 |
| frontend_shell | [frontend_shell](modules/frontend_shell.md) | `frontend/src/App.jsx`、`frontend/src/api.js` | React/Vite 页面壳；本任务未改。 |
| deployment_and_operations | [deployment_and_operations](modules/deployment_and_operations.md) | `docker-compose.yml`、`scripts/` | 恢复提交已部署并健康；Gate 在受控批次后关闭。 |

## Autonomous AI Backend V1

`AI-AUTONOMOUS-BACKEND-V1-20260719-001` starts from the committed `20260718_0031` recovery baseline and spans the existing registry, campaign, candidate-plan, scheduler/result, context, research, provider, and exchange boundaries. The implementation is local Mock/SQLite-only and verified by [autonomous_ai_backend_v1_20260719.md](test_reports/autonomous_ai_backend_v1_20260719.md); server work remains unauthorized until GPT review.

## 跨模块主链

`platform_registry -> research/provider context -> candidate plan -> task_center admission/scheduler -> execution_transport -> factor/research result -> research_exchange`

静态代码只能证明接口和实现存在，不能证明服务器正在运行、数据最新或真实 WQ 可用。

## 架构审计入口

`AI-BACKEND-ARCH-AUDIT-20260718-001` 新增长期架构审计材料：

- `docs/design/autonomous_ai_research_architecture.md`
- `docs/audits/autonomous_ai_backend_gap_analysis_20260718.md`
- `docs/plans/autonomous_ai_backend_implementation_plan_20260718.md`
- `docs/contracts/autonomous_ai_contracts_v1.md`

这些文档是后续自主 AI 后端改造的审阅输入，不表示后端改造已经完成。

## 累计完成资产入口

历史完成资产不会因新审计文档而删除或覆盖。`REG`、`CORE`、`SCHED`、`RESULT`、`RUNTIME-RECOVER`、`TPL`、`consultant_core`、`single_multi_allocation`、allocation decision、capacity policy 和 scheduler recovery 的历史文档与“旧文档 -> 当前权威文档”迁移关系见 [completed_asset_migration_index](archive/completed_asset_migration_index.md)。

该索引及其归档文档必须随 `wq-review` manifest 发布；当前模块文档是后续修订的唯一入口，archive 只保留历史事实。
