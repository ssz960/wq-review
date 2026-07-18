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
| `REVIEW-20260718-001` | deployment_and_operations, wq-review publication | root | IN_PROGRESS | `RESULT-20260718-001` | `PENDING` | `docs/test_reports/wq_review_result_acceptance_20260718.md` |

## 写锁

`KNOW-20260718-003` 已完成并释放 research_exchange、research_center、provider_and_skills 的治理文档写锁；业务代码保持只读。

`REG-20260718-001` 使用独立 worktree 完成 platform_registry 上游资产审计与离线验证；未进入上述研究模块写锁范围，任务完成后不保留写锁。

`REG-20260718-002` 为 `BLOCKED`，当前不持有活动写锁；恢复工作不得据此执行部署、修改回测队列、API Gate、Admission 阈值、候选优先级或调用真实 WorldQuant。

`CORE-20260718-003` 已完成并释放 core_models、execution_transport 与 factor_center 写锁。

其他模块在本任务中仅更新治理文档，不授权业务代码写入。

`CORE-20260718-004` 已完成并释放 platform_registry、core_models 与 Git 事实源恢复写锁；恢复过程未授权真实 WQ、部署、API Gate、前端或 RESULT 业务实现。

`RESULT-20260718-001` 已完成并释放 research_exchange、factor_center、research_center 与 execution_transport 写锁。离线事实账本、标准化、幂等投影、Parent/Child 聚合、Checks、五类 Correlation、Research Feedback Delta 与有界导出均由本任务报告验证；未调用真实 WQ、创建真实任务或修改 API Gate。

`REVIEW-20260718-001` 持有 `wq-review/main` 唯一发布写锁；仅授权从固定 RESULT Git 提交生成、扫描并普通 fast-forward 发布脱敏 GPT 审阅快照。禁止上传完整源码、数据库、PnL、凭据、生产日志，禁止 force push。
