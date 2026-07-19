# AI-AUTONOMOUS-BACKEND-V1-20260718-001 Baseline Validation

## Scope

This report records the clean implementation baseline before Autonomous AI Backend V1 business work. The isolated task branch starts from `44f3b89865037fcf60eb29649cd21d31305d9f95` (`RUNTIME-RECOVER-LIVE-20260718-001` recovery evidence), not the dirty primary worktree.

- Confirmed Alembic start head: `20260718_0031`.
- Confirmed migration file: `backend/alembic/versions/20260718_0031_result_ingestion_v1.py` with `20260718_0030` as its parent.
- All commands used local SQLite fixtures, Mock execution mode, disabled execution, disabled provider access, and loopback-only configured endpoint values.
- No real WorldQuant request, API Gate action, deployment, or production data access occurred.

## Baseline Regression

| Area | Command or fixture | Result |
| --- | --- | --- |
| API and worker imports | `python -c "import app.main, app.worker"` | PASS |
| Migration topology | `python -m alembic heads` | PASS: only `20260718_0031` |
| Core, allocation, result ingestion, adapter | `python -m unittest -q app.mining.consultant_core_test app.mining.single_multi_allocation_test app.mining.result_ingestion_offline_test app.mining.execution_adapter_test` | PASS: 37 tests |
| Platform Registry | `python -m unittest -q app.mining.platform_registry_sync_test` | PASS: 8 tests, including 85,612-field streaming fixture |
| CandidatePlan to Mock transport recovery | `python backend/app/mining/consultant_multi_transport_smoke_test.py` | PASS: 17 checks |
| Research Package v1 | `python backend/app/mining/agent_research_package_lifecycle_module2_test.py` | PASS |
| Context and checkpoint | `python backend/app/mining/agent_context_conversation_snapshot_module3_test.py` | PASS |
| Existing runtime context decision | `python backend/app/mining/agent_runtime_context_mode_decision_module4_test.py` | PASS |
| Research Exchange V2 | `python backend/app/mining/research_exchange_v2_test.py` | PASS |
| Factor Center | `python backend/app/mining/factor_center_smoke_test.py` | PASS |
| Research Center | `python backend/app/mining/research_center_smoke_test.py` | PASS: 10 checks |
| Research Memory | `python backend/app/mining/llm_research_memory_v2_smoke_test.py` | PASS: 12 deterministic checks |

`platform_registry_runtime_test.py` was not run because it depends on a previously cleaned temporary `wqa_reg_20260718` checkout rather than a committed source fixture. The committed `platform_registry_sync_test.py` supplies the same local manifest/hash, staging, active-switch, admission, and bounded-streaming evidence.

## Baseline Repairs

1. Research Exchange V2 content addressing now excludes volatile local storage telemetry from a restricted delta. Only stable storage-policy state participates, so creating an outbox artifact cannot change the hash of the same research snapshot.
2. Legacy Factor Center CSV pre-filtering now retains `pnl_json` only long enough to place an eligible curve in the local compressed cache. The value remains outside the main read model. The smoke fixture uses a temporary cache path instead of creating runtime data in the source worktree.

## Result

Stage 0 baseline is ready for Autonomous AI Backend V1 implementation. Existing deprecation and SQLite foreign-key ordering warnings did not fail assertions. The worker/API import, unique migration head, registry, admission, allocation, result, package, context, memory, research, and read-model baselines are all locally verified.
