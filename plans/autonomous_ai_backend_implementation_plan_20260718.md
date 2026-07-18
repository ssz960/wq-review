# Autonomous AI Backend Implementation Plan

- Task ID: `AI-BACKEND-ARCH-AUDIT-20260718-001`
- Status: plan only. Start implementation only after GPT review returns `PASS`.
- Safety: no real WQ, no migration, no server deployment in this task.

## Module Boundary Map

| Target module | Existing mapping | Reuse/new | Inputs | Outputs | Allowed writes | Forbidden writes | Failure state | Idempotency/recovery |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `research_orchestrator` | none; compose runtime/materializer/scheduler | NEW | campaign, policy, round state | next service calls | round/event rows only | expressions, Gate, ExecutionRequest status, memory final writes | paused/blocked/failed closed | round key + event key |
| `research_campaign_service` | `agent_campaign_service.py` | EXTEND | user goal, budget, mode | campaign/session | `ResearchCampaign`, `AgentSession` | execution/results | waiting_provider/assistance | campaign key |
| `research_round_service` | `Experiment` partial | NEW | campaign, hypothesis, skill | round lifecycle | `research_rounds`, events | scheduler internals | blocked/failed | campaign+round no |
| `research_policy_engine` | scattered settings/control | NEW | system/user/campaign/skill/runtime facts | allow/block/confirm/adjust | policy decisions/events | direct execution | fail closed | policy input hash |
| `research_hypothesis_service` | Research Memory/Asset partial | NEW | package, user, result evidence | hypothesis records | hypotheses/proposals | final memory | draft/rejected | hypothesis hash |
| `research_experiment_service` | `Experiment`, `CandidatePlan` | EXTEND | hypothesis, skill output | candidate plans | `Experiment`, `CandidatePlan`, invocation | execution status | rejected | plan content hash |
| `skill_registry` | plugins, `base.py`, `SkillInvocation` | EXTEND | skill specs | validated registry | skill spec rows | WQ/Gate/results | disabled/denied | adapter+version |
| `skill_invocation_service` | `SkillInvocation` | EXTEND | skill call | candidate proposal/read result | invocation logs | direct DB writes outside contract | failed/denied | input hash + attempt |
| `context_builder_v2` | `context_service.py`, `runtime_context_builder.py` | EXTEND | DB facts | L0-L4 snapshot | `ContextSnapshot` | model free-form memory | context_unavailable | content hash |
| `next_move_engine` | `ResearchDecision` generic | EXTEND | round facts/results/policy | NextMoveDecision | decision/event | scheduler/Gate | ask/stop/fail closed | input hash |
| `memory_governance` | `research_memory_v2.py` | EXTEND | proposals/evidence | memory revisions | memory proposal and memory tables | prompt-only policy | conflict/rejected | proposal hash |
| `user_intent_service` | `intent_confirmation_service.py` | EXTEND | dialogue/control | classified intent | intent/confirmation/instruction/proposal | direct high-risk action | confirmation_required | token/hash |
| `research_package_v2` | `research_package_service.py`, `research_exchange_v2.py` | EXTEND | four-file package | validated/imported assets | package/assets/artifacts | template activation/policy activation | quarantined/rejected | package id + hash |
| `research_assistance_service` | none; working-state material requests partial | NEW | policy trigger/user response | assistance lifecycle | assistance rows/events | long-term memory directly | waiting_assistance | request hash |
| `provider_profile_service` | `config.py`, providers | NEW | admin config | provider profile/preflight | provider profile rows | raw key in logs/docs | waiting_secret/invalid | profile key |
| `secret_vault_service` | env only | NEW or external | API key/secret ref | encrypted/ref secret | secret rows/ref metadata | plaintext in DB/log/context | revoked/invalid | secret ref |

## Integration Phases

| Phase | Scope | Files to change later | Migration | API | Tests | Rollback | Write lock | Acceptance |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | Data contracts, Campaign/Round/Event/Policy foundation | `backend/app/models.py`, `backend/app/mining/research_*`, `backend/app/main.py` | Add round/policy decision fields/tables; extend campaign statuses | Versioned campaign/round/policy endpoints | model constraints, idempotency, no WQ smoke | downgrade migration, feature flag off | ai_mode, task_center | Campaign can create paused/automatic policy and one empty round without execution. |
| 2 | Context Builder V2, user intent, memory proposal | context/runtime builder, intent service, memory governance | Extend `ContextSnapshot`, add `memory_proposals` | context snapshot V2, intent classify, proposal review | L0-L4 snapshot replay, trim, unknown counts, proposal conflict tests | fallback to v1 builder | ai_mode, research_center | Snapshot preserves L0/L1 exact facts and omitted counts. |
| 3 | Skill Registry, CandidateProposal, NextMoveDecision | skill registry, runtime, materializer | Skill spec rows, decision schema fields | skill list/invoke, next move | forbidden permission tests, schema compatibility | disable new skill registry | provider_and_skills, ai_mode | Skill cannot emit Gate/WQ/execution fields; plans still use materializer. |
| 4 | Research Package V2 and external asset import | package service, exchange v2 | Package lifecycle/status fields | V2 preflight/import/review | four-root rule, hash/schema/sanitize/idempotency | v1-only mode | research_exchange | V2 imports assets as unverified/pending only. |
| 5 | Provider profile, secret, unattended preflight | config/provider/profile services, settings API | provider profile and secret/ref tables | masked CRUD + preflight | key never returned, missing key waits, auth fail no silent failover | env provider fallback | provider_and_skills | Campaign start can enter `WAITING_PROVIDER_CREDENTIAL`. |
| 6 | Research Assistance loop | assistance service, policy engine, UI-compatible API | assistance table | create/list/respond/import/resolve | low efficiency trigger, blocked scope, response classification | disable assistance triggers | ai_mode, research_exchange | LOW_EFFICIENCY creates one request and stops repeated variants. |
| 7 | Orchestrator connects existing CandidatePlan/Scheduler/Result | orchestrator, round service, materializer adapter | maybe round result refs | run next round, resume, stop | restart recovery, duplicate callback, Gate closed, no Prod Corr | orchestrator feature flag off | ai_mode, task_center, execution_transport | Existing execution chain receives admitted candidates only. |
| 8 | Small integrated experiment | test fixtures and reports only unless approved | none beyond prior phases | run experiment command/API | 1 campaign, 4 rounds, 12 candidates, one assistance, one V2 package | stop campaign, close Gate | all touched modules serial | Mechanism passes without final Alpha submit and Prod Corr calls = 0. |

## Small-Scale Experiment Acceptance

The first real experiment after implementation review must use:

- 1 campaign, max 4 rounds, 3 candidates per round, total budget 12.
- `CAMPAIGN_AUTOMATIC`.
- Self Corr enabled, PP Corr enabled, Prod Corr disabled.
- Automatic final submission disabled.

It must prove:

- User confirms budget once, and any budget/scope/Gate/Provider/Prod Corr expansion asks again.
- At least two skills are invoked.
- At least one admission rejection occurs.
- At least one `CHANGE_SKILL` or `STOP` decision occurs.
- One short-term user instruction and one long-term policy proposal are handled differently.
- One worker restart resumes without duplicate round/candidate.
- Gate closed prevents new dispatch.
- Duplicate callback does not duplicate result facts.
- One `LOW_EFFICIENCY` assistance request is created.
- One Research Package V2 upload passes hash/schema/sanitization/idempotent import and resumes context.
- Prod Corr provider/API calls remain zero.

## Review Package Plan

Publish only sanitized docs and summaries to `wq-review/main` after local commit:

- `docs/design/autonomous_ai_research_architecture.md`
- `docs/audits/autonomous_ai_backend_gap_analysis_20260718.md`
- `docs/plans/autonomous_ai_backend_implementation_plan_20260718.md`
- `docs/contracts/autonomous_ai_contracts_v1.md`
- governance status, module index, test report, and `review_request.md`

`review_request.md` must ask GPT to return exactly one of `PASS`, `FAIL`, or `BLOCKED`, with blockers tied to source evidence. The review snapshot must not contain source code full text, credentials, base URLs, tokens, cookies, submitted pool, full PnL, production DB/logs, private forum content, or real Alpha expressions.
