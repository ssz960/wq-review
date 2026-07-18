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
| `AI-BACKEND-ARCH-AUDIT-20260718-001` | ai_mode, research_exchange, research_center, provider_and_skills, task_center, execution_transport | root | DONE | `GOV-20260718-005`; `wq-review` snapshot `5b2f20a2` published; GPT review pending | `0b6eecf0` | `docs/test_reports/autonomous_ai_backend_architecture_audit_20260718.md` |
| `GOV-20260719-001` | deployment_and_operations, 审阅白名单/manifest、根治理文档、历史 archive | review-governance | IN_PROGRESS | `AI-BACKEND-ARCH-AUDIT-20260718-001`; GPT review pending | `PENDING` | `docs/test_reports/ai_backend_architecture_review_delivery_governance_20260719.md` |

## 累计审阅历史入口

`GOV-20260719-001` 必须在发布前检查 [completed_asset_migration_index](archive/completed_asset_migration_index.md)。该入口明确保留 `REG`、`CORE`、`SCHED`、`RESULT`、`RUNTIME-RECOVER`、`TPL`、`consultant_core`、`single_multi_allocation`、allocation decision、capacity policy 和 scheduler recovery；缺少任一资产、旧文档到当前权威文档的链接，或 archive 被覆盖/删除，均不得发布 `wq-review`。

## 写锁

`KNOW-20260718-003` 已完成并释放 research_exchange、research_center、provider_and_skills 的治理文档写锁；业务代码保持只读。
`KNOW-20260718-004` 持有 research_exchange、research_center、provider_and_skills 的治理文档写锁；业务代码保持只读。
`KNOW-20260718-004` 已完成并释放 research_exchange、research_center、provider_and_skills 的治理文档写锁；业务代码保持只读。
`KNOW-20260718-005` 持有 research_exchange、research_center、provider_and_skills 的治理文档写锁；业务代码保持只读。
`AI-BACKEND-ARCH-AUDIT-20260718-001` 已完成架构审计与规划文档写入，释放 ai_mode、research_exchange、research_center、provider_and_skills、task_center、execution_transport 的治理文档写锁；业务代码、迁移、前端和真实 WQ 均未授权写入。
`GOV-20260719-001` 仅持有 review 生成器、累计历史 archive、根治理文档和本审计材料的写锁；不持有论坛采集模块文档或任何业务代码/迁移写锁。

其他模块在本任务中仅更新治理文档，不授权业务代码写入。
