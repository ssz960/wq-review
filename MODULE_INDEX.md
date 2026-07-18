# Alpha Mining OS 模块索引

本索引是当前模块文档的唯一入口；子设计文档由对应主模块维护。

| 模块 | 文档 | 主要代码入口 | 状态摘要 |
| --- | --- | --- | --- |
| consultant_core | [consultant_core_model_design](modules/consultant_core_model_design.md) | `backend/app/mining/consultant_core.py` | Additive Multi/Policy/Correlation/Pool mock core; real WQ unchanged. |
| single_multi_allocation | [single_multi_allocation_design](modules/single_multi_allocation_design.md) | `backend/app/mining/single_multi_allocation.py` | Deterministic preview/confirm, shared Capacity reservation and Mock recovery. |
| platform_registry | [platform_registry](modules/platform_registry.md) | `backend/app/mining/platform_registry.py` | 快照同步实现存在；生产源状态 UNVERIFIED。 |
| task_center | [task_center](modules/task_center.md) | `backend/app/main.py`、`backend/app/mining/service.py` | 路由与调度实现广泛，耦合较高。 |
| ai_mode | [ai_mode](modules/ai_mode.md) | `backend/app/mining/agent_runtime_v1.py` | Agent 与 supervisor 两套语义并存。 |
| execution_transport | [execution_transport](modules/execution_transport.md) | `backend/app/mining/execution_adapter.py` | real/mock adapter 均存在；本任务禁用真实调用。 |
| factor_center | [factor_center](modules/factor_center.md) | `backend/app/mining/factor_center.py` | 导入、筛选、同步 API 存在。 |
| template_center | [template_center](modules/template_center.md) | `backend/app/template_library.py` | 模板抽取、CRUD 与展开存在。 |
| research_center | [research_center](modules/research_center.md) | `backend/app/mining/research_center.py` | 研究资产、复盘与导出存在。 |
| research_exchange | [research_exchange](modules/research_exchange.md) | `backend/app/mining/research_exchange_v2.py` | 快照/delta/包适配存在。 |
| provider_and_skills | [provider_and_skills](modules/provider_and_skills.md) | `backend/app/config.py`、`backend/app/mining/*provider*.py` | OpenAI-compatible provider 存在；技能契约分散。 |
| frontend_shell | [frontend_shell](modules/frontend_shell.md) | `frontend/src/App.jsx`、`frontend/src/api.js` | React/Vite 页面壳；本任务未改。 |
| deployment_and_operations | [deployment_and_operations](modules/deployment_and_operations.md) | `docker-compose.yml`、`scripts/` | 2C2G40G guard 与本地脚本并存。 |

## 跨模块主链

`platform_registry -> research/provider context -> candidate plan -> task_center admission/scheduler -> execution_transport -> factor/research result -> research_exchange`

静态代码只能证明接口和实现存在，不能证明服务器正在运行、数据最新或真实 WQ 可用。
