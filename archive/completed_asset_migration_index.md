# Completed Asset Migration Index

This index preserves the historical entry point for completed work that was
previously represented by phase-specific module documents and reports. The
archived files are immutable historical evidence. Any correction or new work
must go to the linked current authoritative module document, not to an archive.

| Completed asset | Archived historical document(s) | Current authoritative document(s) |
| --- | --- | --- |
| `REG-20260718-001` | [platform registry upstream validation](20260718_completed_assets/platform_registry_upstream_validation_20260718.md) | [platform_registry](../modules/platform_registry.md) |
| `CORE-20260718-003` / `CORE-20260718-004` | [consultant core design](20260718_completed_assets/consultant_core_model_design.md), [core validation](20260718_completed_assets/consultant_core_model_validation_20260718.md), [core baseline recovery](20260718_completed_assets/core_baseline_recovery_20260718.md) | [platform_registry](../modules/platform_registry.md), [execution_transport](../modules/execution_transport.md), [factor_center](../modules/factor_center.md) |
| `SCHED-20260718-001` | [single/multi validation](20260718_completed_assets/single_multi_allocation_validation_20260718.md) | [task_center](../modules/task_center.md), [execution_transport](../modules/execution_transport.md) |
| `RESULT-20260718-001` | [offline result ingestion validation](20260718_completed_assets/result_ingestion_offline_20260718.md) | [execution_transport](../modules/execution_transport.md), [factor_center](../modules/factor_center.md), [research_center](../modules/research_center.md), [research_exchange](../modules/research_exchange.md) |
| `RUNTIME-RECOVER-LIVE-20260718-001` | [runtime recovery validation](20260718_completed_assets/runtime_recover_live_20260718.md) | [task_center](../modules/task_center.md), [ai_mode](../modules/ai_mode.md), [execution_transport](../modules/execution_transport.md), [deployment_and_operations](../modules/deployment_and_operations.md) |
| `TPL-20260718-001` | [template candidate promotion](20260718_completed_assets/template_candidate_promotion_20260718.md) | [template_center](../modules/template_center.md), [research_exchange](../modules/research_exchange.md) |
| `consultant_core` | [consultant core model design](20260718_completed_assets/consultant_core_model_design.md) | [execution_transport](../modules/execution_transport.md), [factor_center](../modules/factor_center.md) |
| `single_multi_allocation` | [single/multi allocation design](20260718_completed_assets/single_multi_allocation_design.md) | [task_center](../modules/task_center.md), [execution_transport](../modules/execution_transport.md) |
| `allocation decision` | [allocation decision matrix](20260718_completed_assets/allocation_decision_matrix.md) | [task_center](../modules/task_center.md), [execution_transport](../modules/execution_transport.md) |
| `capacity policy` | [capacity policy and slot model](20260718_completed_assets/capacity_policy_and_slot_model.md) | [task_center](../modules/task_center.md), [execution_transport](../modules/execution_transport.md) |
| `scheduler recovery` | [scheduler recovery state machine](20260718_completed_assets/scheduler_recovery_state_machine.md) | [task_center](../modules/task_center.md), [execution_transport](../modules/execution_transport.md) |

The archived source documents were restored from fixed local Git commits rather
than copied from a dirty worktree. Their source commits are `013c7b3`,
`c640d7e`, `9329852`, `fd5831f`, `4c73a8f`, `44f3b89`, and `af30139`. The three
test-command references that looked like DNS names are rendered as repository
paths in this review-safe archive; the fixed source commits retain the original
historical text.

The review manifest must list this index and every archived historical document.
Reviewers must treat a missing asset, missing migration link, or an overwritten
archive as a delivery failure.
