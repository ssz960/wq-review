# Autonomous AI Backend Gap Analysis

- Task ID: `AI-BACKEND-ARCH-AUDIT-20260718-001`
- Audit date: 2026-07-18
- Evidence source: local source-of-truth tree, read only.
- Write target: isolated task worktree.
- Business code changes: none.
- Real WQ calls: none.

## Source Caveats

The main source tree is dirty and contains unrelated conflicts in governance/model files. It was used only as read evidence. The task text states Alembic head `20260718_0031`, but the local `backend/alembic/versions` evidence only shows migrations through `20260718_0030`; this is marked `UNVERIFIED/source gap`.

## Current Call Chain Findings

| # | Current-system question | Evidence and caller | Actual responsibility | Sole source? | Problem | Recommended action |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | Frontend -> backend -> Agent Session -> provider chain | `frontend/src/pages/AgentAiMode.jsx:297-304`, `:438-476`; `backend/app/main.py:439-458`; `agent_runtime_v1.py:370-393`; `agent_runtime_provider.py:18-27` | UI calls campaign/workspace/dialogue/package APIs; dialogue calls runtime with OpenAI-compatible provider; runtime checkpoints results. | Partial. Session/checkpoint are DB facts; provider profile is env/config. | Agent runtime and legacy supervisor both exist. | MERGE runtime/supervisor semantics behind one policy/skill/decision contract. |
| 2 | Agent loads package, templates, fields, results, memory, checkpoint | `runtime_context_builder.py:17-42`, `:55-61`; `agent_runtime_v1.py:150-164`; `research_memory_v2.py:867-975` | Builds bounded material context from current package assets, template rows, local docs field/operator files, working state, latest experiment, checkpoint, and memory retrieval. | Partial. Package/session facts are sole sources; docs files are supplemental. | Not full L0-L4 Context Builder V2; source versions/omitted counts incomplete. | EXTEND context builder into deterministic five-layer snapshot. |
| 3 | Context compression/truncation/persistence/recovery | `context_service.py:38-56`; `agent_runtime_v1.py:188-194`; `research_memory_v2.py:1027-1058`; `agent_research_store.py:52-54` | Stores snapshots, compacts every 10 committed checkpoints or by token estimate, trims memory/check rows by budget, CAS-updates working state. | Partial. ContextSnapshot and WorkingState are facts. | Compression is v1/bootstrap level, not replayable L0-L4 with unknowns. | EXTEND `ContextSnapshot` fields and builder output; keep CAS. |
| 4 | Skill definition/discovery/invocation/permissions/logging | `agent_runtime_v1.py:19-23`, `:111-124`, `:290-294`; `models.py:1618-1621`; `base.py` strategy plugin classes | Runtime accepts bounded skill invocation stubs and candidate plans; DB model logs invocation idempotently. | Partial. `SkillInvocation` exists but no central Skill Registry table/spec. | Skill specs are scattered across plugins/methods/prompts. | EXTEND into versioned Skill Registry and Invocation service. |
| 5 | User natural language to task/command/rule/memory | `main.py:439-460`; `context_service.py:14-32`; `intent_confirmation_service.py:12-33` | Query triggers runtime; guidance becomes instruction; control creates confirmable intent. | Partial. Confirmation path is sole command authority for listed actions. | No classification into long-term policy, hypothesis, memory correction, package, or ordinary chat. | EXTEND user intent service and memory proposal workflow. |
| 6 | Can AI directly mutate DB, queue, Gate, or real execution? | `agent_runtime_v1.py:23`, `:91-124`; `agent_materializer.py:81-118`, `:132-147`; `service.py:2537-2643`; `execution_adapter.py:350-486` | Structured authority keys are rejected; materializer re-runs registry admission; execution goes through scheduler/adapter. | Yes for current audited path. | Dialogue guidance materializes draft plans automatically after runtime; policy clarity needs stronger round boundary. | Preserve materializer-only execution bridge; add policy check before each materialization. |
| 7 | CandidatePlan, Scheduler, Result Ingestion reusable interfaces | `models.py:1594-1621`; `agent_materializer.py:59-149`; `service.py:2527-2730`; `consultant_multi_transport.py:1353-1486`; `service.py:11297-11330` | CandidatePlan persists plan; shared scheduler dispatches; factor check records result facts. | Yes. | High coupling in `service.py`, but it is the current execution authority. | REUSE; wrap with orchestrator APIs, do not replace. |
| 8 | Memory, AI Memory, ContextSnapshot, Decision, PendingConfirmation overlap | `models.py:1211-1297`, `:1416-1466`, `:1522-1566`; `research_memory_v2.py:410-471`, `:867-952` | Multiple records hold observations, knowledge, context, decisions, and confirmations. | Partial. Each table has a domain but promotion semantics overlap. | No formal `MemoryProposal`; assistance and prompt observations may blur into memory. | MERGE memory governance semantics; add proposal lifecycle. |
| 9 | Research Package v1 import/assets | `research_package_service.py:9-12`, `:29-64`, `:70-90`; `main.py:363-382`; `research_exchange_v2.py:872-1013` | Strict four-root v1 ZIP import; assets persisted and current/previous status managed. | Yes for v1 package store. | Only accepts `research-package.v1`; lifecycle lacks V2 review/sanitization states. | EXTEND importer to V2 while keeping v1 compatibility. |
| 10 | Provider/API key/settings/env/log redaction | `config.py:50-59`, `:211-220`; `supervisor_llm.py:557-606`, `:763-823`; `main.py:842-849`; `Settings.jsx:24-32` | Provider is OpenAI-compatible config from env/pool; frontend only shows SET/EMPTY-like status; settings endpoint says server-env-only. | Partial. Env is source, not DB profile. | No provider profile table, encrypted secret vault, preflight lifecycle, or full raw-response redaction guarantee. | NEW ProviderProfile/Secret mechanism; keep frontend masked. |
| 11 | Errors, low efficiency, no direction handling | `agent_runtime_v1.py:325-335`, `:382-389`; `runtime_context_builder.py:43-54`; `research_memory_v2.py:1178-1195`; `campaign_workspace_service.py:29` | Runtime records protocol failures; context policy may suggest wait; memory has auto-spawn rejection reasons; workspace exposes material requests. | Partial. | No standalone assistance request; possible repeated similar variants before policy stops. | NEW Research Assistance and policy triggers. |
| 12 | Task state, worker restart, callback idempotency/recovery | `agent_research_store.py:33-43`, `:52-54`, `:62-98`; `agent_runtime_v1.py:168-180`, `:370-393`; `consultant_multi_transport.py:1195-1237`; `service.py:2733-2973` | Leases, input hashes, checkpoint identity, CAS, batch recovery, writeback retry, execution recovery. | Yes for audited paths. | Round-level recovery state is missing. | REUSE primitives; add round/event idempotency boundaries. |
| 13 | Self/PP/Prod Corr entry and bypass check | `main.py:341-355`; `execution_adapter.py:647-690`; Factor Center official refresh route `main.py:1207-1215` | Self Corr endpoint exists; performance comparison fetch exists in adapter; official refresh requires explicit request payload. | Partial. | Static audit found no autonomous Prod Corr enable path, but adapter has performance comparison read capability. | Keep Prod Corr disabled by policy; audit all future skills for no PP/Prod bypass. |
| 14 | Existing DB tables reuse vs new fields/tables | `models.py:383-511`, `:1211-1621`; candidate table analysis below. | Many reusable campaign/session/checkpoint/plan/execution/result/memory/package tables exist. | Partial. | Missing formal round, hypothesis, memory proposal, assistance, provider profile/secret. | Add only justified tables after GPT review and migration task. |
| 15 | Frontend API contracts to protect | `AgentAiMode.jsx:289-304`, `:371-476`, `:699-703`; `Settings.jsx:24-32`; `main.py:253-518`, `:842-849` | Frontend depends on campaign list/workspace/activity/continuous/status/self-corr/dialogue/package/settings contracts. | Yes for current page. | Backend refactor could break existing workspace shape. | Preserve API shape or add versioned V2 endpoints/adapters. |

## Reusable Assets

| Area | Reuse conclusion |
| --- | --- |
| Platform Registry | REUSE. Already gives snapshot, field/operator/setting validation, bounded field selection, and hard admission. |
| CandidatePlan and materializer | REUSE/EXTEND. Keep as AI-to-execution boundary; add CandidateProposal contract and policy check. |
| Scheduler/execution/result ingestion | REUSE. `ExecutionRequest`, `SimulationBatch*`, dispatcher, adapter, FactorCheck, and FactorCenter are the only execution facts. |
| Agent persistence primitives | REUSE/EXTEND. Lease, checkpoint, decision, experiment, plan, and working-state CAS are strong foundations. |
| Research Package v1/exchange | EXTEND. Four-root file rule exists; V2 lifecycle and schema are missing. |
| Research Memory v2 | EXTEND/MERGE. Strong retrieval/evidence aggregation exists; formal proposal/promotion governance missing. |
| Provider | EXTEND/NEW. Runtime/provider code exists, but profile/secret storage does not. |
| Frontend contracts | REUSE. Preserve current endpoints and add versioned fields without breaking page shape. |

## Target Gap Matrix

| Target capability | Current implementation | Reuse class | Gap | Risk | Suggested change | Modules | Migration? | Priority |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Platform snapshot and hard admission | Platform Registry + materializer admission | REUSE | Need round/orchestrator binding | Low | Call existing service only | platform_registry, ai_mode | No | P0 |
| Shared execution chain | ExecutionRequest, SimulationBatch, adapter | REUSE | Service is highly coupled | Medium | Wrap, do not replace | task_center, execution_transport | No | P0 |
| Result/audit facts | FactorCheck, FactorCenter, MiningEvent | REUSE | Campaign event projection incomplete | Medium | Add campaign-level event mapping | factor_center, ai_mode | Maybe | P1 |
| Research Campaign | Existing `research_campaigns` | EXTEND | Missing run mode, policy, status variants | Medium | Add fields/statuses | ai_mode | Yes | P0 |
| Research Round | Only `Experiment`/CandidatePlan partial | NEW | No one-round lifecycle | High | Add `research_rounds` | ai_mode | Yes | P0 |
| Research Hypothesis | ResearchKnowledge/Asset partial | NEW | No hypothesis lifecycle | Medium | Add `research_hypotheses` | research_center, ai_mode | Yes | P1 |
| Skill Registry | Plugins + SkillInvocation partial | EXTEND | No versioned skill spec | High | Add registry/spec service | provider_and_skills | Yes | P1 |
| Policy Engine | Settings/control scattered | NEW | Prompt/settings are not enough | High | Add policy evaluator and decisions | ai_mode, task_center | Yes | P0 |
| Context Builder V2 | Context service/runtime builder v1 | EXTEND | No L0-L4 replay metadata | High | Extend snapshots and builder | ai_mode | Yes | P0 |
| NextMoveDecision | ResearchDecision generic | EXTEND | Need explicit enum and evidence schema | Medium | Version decision contract | ai_mode | Maybe | P1 |
| Memory Governance | Research Memory v2 | EXTEND | No proposal lifecycle | High | Add memory proposals | research_center | Yes | P1 |
| Research Assistance | material_requests/open_questions only | NEW | No formal help request lifecycle | High | Add assistance service/table | ai_mode, research_exchange | Yes | P1 |
| Campaign Event | MiningEvent generic | EXTEND | Need campaign projection/idempotency | Medium | Extend or project MiningEvent | task_center, ai_mode | Maybe | P1 |
| Provider Profile/Secret | Env/pool config only | NEW | No admin profile/secret/preflight | High | Add profile + encrypted/ref secret | provider_and_skills | Yes | P1 |
| Research Package V2 | v1 strict four files | EXTEND | V2 schema/lifecycle missing | Medium | Extend importer | research_exchange | Yes | P1 |
| Agent/Supervisor unification | Runtime v1 and supervisor both exist | MERGE | Two AI semantics | High | Common policy/decision envelope | ai_mode | Maybe | P0 |
| Field Registry vs Platform Registry | Old field registry plus Platform Registry | MERGE | Duplicate field authority risk | Medium | Make Platform Registry authoritative for AI | platform_registry | Maybe | P1 |
| Legacy/bypass scripts | Root and mining real-WQ scripts exist | DEPRECATE | Can confuse operators | High | Mark non-authoritative or guard | deployment | No | P2 |
| Env-only provider for unattended | Env config works for local | DEPRECATE | Not auditable enough | Medium | Keep fallback, prefer profile/secret | provider_and_skills | Yes | P1 |

Summary count: REUSE 3, EXTEND 7, MERGE 2, DEPRECATE 2, NEW 5.

## Candidate Table Reuse Analysis

| Candidate table | Decision | Why existing tables are/are not enough | Sole responsibility | Idempotency key | Lifecycle | Deletion/archive |
| --- | --- | --- | --- | --- | --- | --- |
| `research_campaigns` | EXTEND existing | Existing table owns campaign identity/config/status; adding fields is safer than a second campaign table. | Campaign goal, run mode, policy refs, active snapshot/package, budget. | `campaign_key` plus versioned policy hash. | active, paused, waiting_provider, waiting_assistance, completed, failed, cancelled, archived. | Archive; never hard-delete while results exist. |
| `research_rounds` | NEW | `Experiment` models an experiment/hypothesis container, not exact per-round context/admission/execution/decision lifecycle. | One autonomous cycle and restart boundary. | `campaign_id + round_no` or `round_key`. | planned, context_built, proposing, admitted, executing, waiting_results, deciding, blocked, completed, failed, cancelled. | Archive with campaign. |
| `research_hypotheses` | NEW | `ResearchKnowledgeRecord` is evidence/memory; `ResearchAsset` is immutable content. Neither owns active hypothesis status and policy scope. | Testable idea lifecycle. | `campaign_id + hypothesis_hash` plus optional source ref. | draft, active, paused, validated, rejected, superseded, archived. | Archive/supersede, preserve evidence refs. |
| `memory_proposals` | NEW | `ResearchDecision` records AI output, not promotion workflow; `ResearchMemoryRecord` is already formal memory. | AI/user proposal awaiting governance. | `source_decision_id + proposal_hash`. | proposed, duplicate, conflict, accepted, rejected, applied, revoked, expired. | Archive; applied proposals link to memory revision. |
| `research_campaign_events` | EXTEND existing `MiningEvent` first | `MiningEvent` is already append-only and used broadly. A separate table risks a second audit system unless query/load proves it necessary. | Campaign event projection with stable event types. | `event_type + entity_type + entity_id + content_hash`. | append-only. | No deletion; retention policy can compact projections only. |
| `research_assistance_requests` | NEW | `material_requests`/open questions are JSON fragments in working state, not user-facing lifecycle records. | AI asks for help with blocked scope and evidence. | `campaign_id + round_id + category + blocked_scope_hash`. | open, acknowledged, answered, material_received, imported, resolved, cancelled. | Archive with campaign; keep audit trail. |
| `llm_provider_profiles` | NEW | Env/settings expose only runtime config and SET/EMPTY status. | Admin-managed provider configuration and validation state. | `profile_key` or provider+model+base hash. | draft, enabled, disabled, invalid, waiting_secret, validated, archived. | Soft delete/archive. |
| Provider Secret storage | One database table or external Secret Store | No encrypted DB secret/ref model exists. Env-only is not enough for unattended profiles. Choose one implementation boundary, not both as required new tables. | Encrypted secret material or secret reference metadata. | `secret_ref`. | active, rotated, revoked, invalid. | Rotate/revoke; hard deletion only after no profile references. |

Required new table count after reuse analysis: 5 (`research_rounds`, `research_hypotheses`, `memory_proposals`, `research_assistance_requests`, `llm_provider_profiles`). Provider Secret is a mutually exclusive implementation choice: add one `llm_provider_secrets` database table or use an external Secret Store. `research_campaign_events` should start as `MiningEvent` extension/projection, not a new table.

## Highest Risks

1. The two AI paths (`agent_runtime_v1` and LLM supervisor) can drift into separate authority semantics unless merged before autonomous campaign work.
2. Context Builder V2 and Memory Governance are not yet strong enough for unattended operation; missing omitted counts and proposal lifecycle can turn missing evidence into false certainty.
3. Provider/secret is env-driven and not yet suitable for unattended admin-managed campaigns.
4. Local evidence has a source gap (`20260718_0031` not found locally) and a dirty main tree; GPT review must treat that as `UNVERIFIED`, not as production truth.

## V1 Implementation Update

The Phase 0 source gap was resolved before coding by using a clean committed fact source containing `20260718_0031`, verifying the single `20260719_0032` Head, and developing in a separate worktree. The V1 implementation now supplies the planned Round, Hypothesis, MemoryProposal, Assistance, Provider Profile/Secret, Policy, Context V2, Skill Registry, Package V2, and bounded Orchestrator capabilities behind feature flags.

The reuse decisions remain intact: CandidatePlan/materializer, shared scheduler/adapter, Result Ingestion, Factor Center, Research Feedback Delta, Registry, and `MiningEvent` remain authoritative. No second execution queue, result pullback, template store, Factor store, or campaign-event table was introduced. Local coverage proves mock behavior only; server integration, real provider connectivity, real WorldQuant, Prod Corr, and final submission remain `UNVERIFIED` and unauthorized.
