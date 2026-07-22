# Autonomous AI Server Mock V1

Task: `AI-SERVER-MOCK-V1-20260719-001`

## Result

`AI autonomous server mock V1: PASS`

`NEXT_STATE: READY_FOR_REAL_PROVIDER_PREFLIGHT`

No real Provider, WorldQuant, Self/PP/Prod Corr, or Alpha submission call was made. The WQ execution Gate and both Autonomous real flags remained closed throughout acceptance.

## Deployment And Rollback

- Local deployment source: commit `e8f3ad0` and its linear ancestors; backend image includes runtime fix commit `910d6d8`.
- Server migration: `20260718_0031 -> 20260719_0032`; one Alembic Head confirmed.
- Database backup: compressed PostgreSQL dump, gzip integrity checked, about 8.9 MB; checksum recorded on server.
- Deployment backup: pre-deployment archive about 45 MB plus intact rollback deployment directory.
- Clean synchronization: a new release directory was extracted from a local Git archive. Existing frontend and nginx assets were copied unchanged as deployment dependencies; no frontend source was modified.
- Cleanup: unused Docker images and build cache were pruned. Running containers, volumes, database, Registry, and legacy WorldQuant files were retained.
- Rollback point: pre-0032 database dump and the pre-deployment directory. Production database was not rebuilt.

## Route And Service Evidence

- `/api/health`: HTTP 200.
- `/api/v2/autonomous/readiness`: mounted and HTTP 200.
- backend, worker, PostgreSQL, Redis, nginx, frontend, and tunnel containers: running; PostgreSQL and Redis healthy.
- Task Center, Factor Center, Factor/Result reads, and Research Storage: HTTP 200.
- Legacy WorldQuant service: remained active and was not restarted or modified.
- New tables verified through schema/Readiness: Research Round, Hypothesis, Memory Proposal, Assistance Request, Provider Profile, Provider Secret, existing execution/batch/child, MiningEvent, and canonical 0031 result tables.

## Readiness

- `ready_for_local_research`: configured, validated, armed, and ready; no blockers.
- `ready_for_real_provider`: not configured, not armed; Provider profile and secret are absent.
- `ready_for_real_single`: not configured, not armed; execution Gate closed.
- `ready_for_consultant_multi`: not configured, not armed; consultant multi disabled.
- Provider API returned an empty list and exposed no secret material.
- Active Registry: `REG-20260718-001`, with a non-fixture source commit; no fallback to `/api/fields` occurred.

## Mock Campaign

Campaign ID 4 used fixture Skill ranking while binding all candidates to the server Active Registry.

| Round | Unique candidates | Budget used | Registry | Result |
| --- | ---: | ---: | --- | --- |
| 1 | 3 | 3 | `REG-20260718-001` | completed |
| 2 | 3 | 3 | `REG-20260718-001` | completed after worker restart |

Final facts:

- Two completed rounds, six unique candidates, total execution budget six.
- Six existing `ExecutionRequest` records and six canonical execution result facts.
- Duplicate ExecutionRequest: 0; duplicate Result Fact: 0.
- Two Context V2 snapshots with NextMove, two MemoryProposal records, and six Feedback Delta records.
- Gate-closed observation before and after wait remained 3 requests/3 facts; no dispatch or budget change occurred.
- Worker was restarted between rounds and returned to the shared queue.
- Round 2 terminal callback was repeated; counts remained six requests and six facts.
- Campaign events reused `MiningEvent`; no Campaign Event table, queue, Factor store, or Mock state machine was added.

## Resources And Boundaries

- Server: 2 CPU, 1613 MB RAM, 1 GB swap.
- Final memory available: about 803 MB; swap used: 0.
- Final disk: about 19 GB free of 40 GB after safe cache cleanup.
- Backend/worker memory during final snapshot: about 110 MB / 51 MB; total Alpha containers remained within configured limits.
- Real Provider credential validation, real transport, correlation services, final Alpha submission, and production budget arming remain intentionally unverified and require a separate approved preflight.

## Restored Schema Reconciliation

The recovered local baseline initially represented result feedback with simplified `result_facts` and `research_feedback_deltas` ORM shapes. The already deployed 0031 V1 schema retained the richer authoritative chain `execution_result_facts -> normalized_execution_results -> research_feedback_deltas`, where feedback references `normalized_result_id` and carries scope, schema version, and payload hash.

No destructive rename or table replacement was performed. Migration 0032 was additive and preserved all 0031 rows. During server acceptance, the Mock driver was corrected to write the existing 0031 chain idempotently; the simplified `result_facts` name remains compatibility metadata and is not the Result Ingestion authority. Existing execution, normalized result, feedback, Factor, Registry, and MiningEvent data remained in place. The unique Result Ingestion fact source is `execution_result_facts`, with `normalized_execution_results` as its normalized projection.
