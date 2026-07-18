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
| `KNOW-20260718-008` | research_exchange, research_center, provider_and_skills | root | IN_PROGRESS | `KNOW-20260718-005` | `PENDING` | `docs/test_reports/consultant_forum_full_collection_20260718.md` |

## 写锁

`KNOW-20260718-003` 已完成并释放 research_exchange、research_center、provider_and_skills 的治理文档写锁；业务代码保持只读。
`KNOW-20260718-004` 持有 research_exchange、research_center、provider_and_skills 的治理文档写锁；业务代码保持只读。
`KNOW-20260718-004` 已完成并释放 research_exchange、research_center、provider_and_skills 的治理文档写锁；业务代码保持只读。
`KNOW-20260718-005` 持有 research_exchange、research_center、provider_and_skills 的治理文档写锁；业务代码保持只读。

`KNOW-20260718-008` 持有 research_exchange、research_center、provider_and_skills 的治理文档写锁；范围为四个顾问社区板块的可恢复只读全量采集、脱敏研究资产发布和审阅摘要；业务代码、真实 WQ 执行和账户状态保持只读。

其他模块在本任务中仅更新治理文档，不授权业务代码写入。
