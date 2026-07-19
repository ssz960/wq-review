# Autonomous AI Backend V1 Local Acceptance

- Task ID: `AI-AUTONOMOUS-BACKEND-V1-20260719-001`
- Branch: `codex/AI-AUTONOMOUS-BACKEND-V1-20260719-001-root`
- Worktree start commit: `47d4d0fe`
- Orchestrator implementation commit: `886d11ad`
- Alembic transition: `20260718_0031 -> 20260719_0032`
- Result: local acceptance PASS; status `READY_FOR_GPT_BACKEND_V1_REVIEW` after review publication.

## Scope

The implementation uses a clean committed fact source in an isolated worktree. It adds bounded V1 orchestration and documentation only; no root-worktree conflict was resolved or copied. The acceptance environment uses local SQLite, a fake provider probe, a fixture Registry snapshot, and the existing mock execution adapter.

No frontend, deployment, real provider call, real WorldQuant call, Prod Corr, or final Alpha submission is part of this result.

## Implementation Delivered

- `ResearchOrchestrator` creates or resumes one leased round, builds Context V2, invokes a bounded Skill, stages and materializes CandidatePlans, dispatches through the shared mock scheduler, waits for canonical result facts, and records one NextMove decision.
- API endpoints advance a round and recover results using server-derived runtime facts.
- CandidatePlan materialization binds the active Registry profile before hard admission.
- Fixture-only rank and mean Skills provide deterministic local coverage; they have no direct execution capability.
- Provider Profile preflight, V2 package import, MemoryProposal, Assistance, Context V2, and append-only `MiningEvent` integration are exercised through the V1 path.

The required core-table conclusion is unchanged: Research Round, Research Hypothesis, MemoryProposal, Research Assistance Request, and Provider Profile are the five required new core tables. The selected Provider credential option is a database table. `MiningEvent` remains the campaign event source; there is no second campaign-event table.

## Verification

| Check | Result |
| --- | --- |
| Python compile for changed V1 modules | PASS |
| V1 Stage 1 through Stage 6 | 6 PASS scripts |
| V1 Stage 7 integrated campaign | PASS |
| Migration upgrade/downgrade/re-upgrade rehearsal | PASS |
| Alembic Heads | one Head: `20260719_0032` |
| Consultant Multi transport smoke | 17 checks PASS |
| Result Ingestion offline regression | 10 tests PASS |

## Campaign Evidence

| Round | Skill | Unique admitted candidates | Terminal results | Next move |
| --- | --- | ---: | ---: | --- |
| 1 | fixture rank | 3 | 3 | `CHANGE_SKILL` after one admission rejection |
| 2 | fixture mean | 3 | 3 | `CONTINUE` |
| 3 | fixture rank | 3 | 3 | `CONTINUE` |
| 4 | fixture mean | 3 | 3 | `COMPLETE` |

The campaign generated 13 proposals, admitted and executed 12 unique candidates, and consumed the approved execution budget of 12. Each round records `unique_candidate_id` as its statistic denominator. A forced Gate closure blocked dispatch without creating duplicate requests; reopening recovered the same work. A repeated terminal callback was idempotent. The low-efficiency trigger created one assistance request, and an `ASSISTANCE_RESPONSE` V2 package rebuilt the current context.

## Call and Safety Counts

| Boundary | Count or state |
| --- | --- |
| Real WorldQuant calls | 0 |
| External provider calls | 0 |
| Self Corr external calls | 0; policy enabled |
| PP Corr external calls | 0; policy enabled |
| Prod Corr calls | 0; policy disabled |
| Final Alpha submission calls | 0; disabled |
| Duplicate execution requests | 0 |
| Duplicate result facts from repeated callback | 0 |

## Unverified Boundaries

- Server deployment and server database upgrade.
- Real provider connectivity and credential rotation in a server secret environment.
- Real WorldQuant adapter behavior, production scheduler capacity, and live rate limits.
- External Self/PP correlation services, Prod Corr, and final Alpha submission.
- Frontend controls for V1 Provider Profile and autonomous campaign workflows.

## Evidence Files

- `backend/app/mining/autonomous_v1_stage1_test.py` through `autonomous_v1_stage7_test.py`
- `backend/app/mining/autonomous_v1_migration_test.py`
- `backend/app/mining/consultant_multi_transport_smoke_test.py`
- `backend/app/mining/result_ingestion_offline_test.py`
- `docs/audits/autonomous_ai_backend_v1_review_request_20260719.md`
