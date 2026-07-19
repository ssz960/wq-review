# Autonomous AI Backend V1 Review Request

- Task ID: `AI-AUTONOMOUS-BACKEND-V1-20260719-001`
- Architecture prerequisite: `AI-BACKEND-ARCH-AUDIT-20260718-001` passed prior GPT review.
- Implementation commit: `886d11ad`
- Scope: local Mock/SQLite V1 implementation and its sanitized evidence only.

## Requested Review

Verify that V1 preserves the authoritative execution chain:

`Platform Registry -> CandidatePlan -> hard admission -> shared scheduler -> execution adapter -> Result Ingestion -> Factor/feedback`

Confirm that `ResearchOrchestrator` only coordinates round state, leases, policy, bounded budgets, and event projection. It must not introduce a second queue, execution status, result pullback, template store, Factor store, or campaign-event table.

Confirm that the implementation uses five required core tables: Research Round, Research Hypothesis, MemoryProposal, Research Assistance Request, and Provider Profile. Provider credential storage must be exactly one selected alternative, database storage or external Secret Store; this local implementation selected database storage. Campaign events must reuse `MiningEvent` rather than a new campaign-event table.

Review the local campaign evidence for four rounds, twelve unique candidates, two Skills, one admission rejection, one Skill change, Gate block/recovery, duplicate result idempotency, one low-efficiency assistance request, and V2 assistance package context rebuild. Verify that the statistic denominator is unique candidate identity, not callback or event count.

## Safety Checks

- Feature flags are default-off outside the local acceptance process.
- No frontend implementation, server deployment, real provider call, real WorldQuant call, Prod Corr, or final Alpha submission was performed.
- Runtime facts for the V1 endpoints are server-derived; client payloads cannot open Gate or change execution mode.
- Provider credentials, network locations, raw results, submitted pools, PnL, production logs, and full source code are excluded from this snapshot.
- Cumulative history has not been overwritten or deleted. Confirm the manifest and `docs/archive/completed_asset_migration_index.md` retain the REG, CORE, SCHED, RESULT, RUNTIME-RECOVER, TPL, consultant core, single/multi allocation, allocation decision, capacity policy, and scheduler recovery entry points.

Return exactly one of `PASS`, `FAIL`, or `BLOCKED`. For `FAIL` or `BLOCKED`, cite the specific reviewed artifact and the required correction. Do not treat local Mock evidence as server or production authorization.
