# Autonomous AI Research Architecture

- Task ID: `AI-BACKEND-ARCH-AUDIT-20260718-001`
- Status: target architecture recorded; backend refactor not started.
- Safety: no real WQ call, no migration, no frontend change, no production decision.

## Purpose

Alpha Mining OS should support a long-running autonomous Alpha researcher that can operate inside approved budgets and boundaries, ask for help when evidence is insufficient, and always reuse the existing platform registry, admission, scheduler, execution adapter, result ingestion, Factor Center, Research Center, Research Exchange, and Research Memory facts.

The required execution chain remains:

```text
platform registry snapshot -> bounded retrieval -> candidate plan -> hard admission -> scheduler -> execution adapter -> result/audit
```

No module may create a second execution queue, second result pullback path, second template library, second Factor store, scheduler bypass, or AI-owned database write path.

## Three Loops

| Loop | Goal | Authoritative path | Stop/help behavior |
| --- | --- | --- | --- |
| External knowledge loop | Bring public material into the local system as sanitized, reviewable research assets. | Codex or GPT produces a Research Package; server validates hashes/schema/sanitization; importer creates External Knowledge, Hypothesis, Template Candidate, Operator Practice, Dataset Practice, or Policy Proposal assets. | Imported material starts unverified and cannot activate templates, policies, or long-term truth without local validation. |
| Internal research loop | Convert user goals and current evidence into small rounds of validated candidates. | Research Campaign -> Research Round -> Hypothesis + Skill -> CandidateProposal -> CandidatePlan -> hard admission -> Scheduler -> Execution Adapter -> Result Ingestion -> NextMoveDecision. | The AI may continue, repair, change skill, change hypothesis, pause, stop, ask user, or complete. It may not open Prod Corr, change Gate, submit final Alpha, or bypass admission. |
| Human assistance loop | Ask accurately when the AI lacks evidence or direction. | Policy Engine creates a Research Assistance Request with blocking scope, evidence summary, tried paths, and requested material. User response is classified before becoming campaign instruction, policy proposal, hypothesis, memory correction, package, or ordinary dialogue. | Only affected hypothesis/direction is blocked when alternatives exist; otherwise campaign enters `WAITING_ASSISTANCE`. |

## Single Sources Of Truth

| Domain | Existing authority to preserve | Design rule |
| --- | --- | --- |
| Platform capabilities | `PlatformRegistrySnapshot`, capability/dataset/field/operator/setting rows. | Every plan binds a registry snapshot and simulation profile before admission. |
| Templates | `TemplateLibraryRecord` and template APIs. | Skills reference template IDs/versions; no autonomous second template store. |
| Candidate execution plan | `CandidatePlan` plus deterministic materializer. | AI output is a proposal only; server expands and rejects/accepts deterministically. |
| Real execution | `ExecutionRequest`, `SimulationBatch`, `SimulationBatchChild`, scheduler and adapter. | Only shared scheduler/adapter can submit or poll. |
| Results | `FeedbackRecord`, `FactorCheckRecord`, `FactorCenterAlphaRecord`, Research Feedback Delta. | Result facts are ingested once and projected, not recomputed by AI. |
| Research package exchange | `ResearchPackage`, `ResearchPackageAsset`, `ResearchArtifact`, `ResearchExchangeV2Service`. | V2 must extend current package and exchange services. |
| Context/session | `ResearchCampaign`, `AgentSession`, `WorkingState`, `ConversationCheckpoint`, `ContextSnapshot`. | Context Builder V2 builds immutable snapshots from DB facts, not free-form summaries alone. |
| Memory | `ResearchMemoryRecord`, `ResearchKnowledgeRecord`, evidence/retrieval logs. | AI can propose memory, but program governs promotion, conflict, versioning, and revocation. |
| Audit events | Existing `MiningEvent` plus future campaign event projection. | Use append-only events and avoid a parallel audit log unless a migration proves the generic event model cannot support campaign queries. |

## Core Mechanisms

### Research Campaign

`ResearchCampaign` owns the user goal, run mode, hard budget, allowed skills, correlation policy, active registry snapshot, and package versions. It should be extended rather than replaced. Campaign state must distinguish active, paused, waiting for provider credential, waiting for assistance, completed, failed, cancelled, and archived.

### Research Round

`ResearchRound` should represent one small deterministic cycle: context build, skill choice, candidate proposal, admission, execution wait, result analysis, and next move. Existing `Experiment` can remain a hypothesis/skill execution container, but it does not fully model round lifecycle, blocking scope, budget counters, or restart boundaries.

### Research Hypothesis

Hypotheses are durable, testable research ideas such as correlation repair, crowding probe, A x B, `trade_when`, PPA, or new dataset strategy. They start as draft/unverified and are promoted only by evidence. Existing knowledge/memory records can provide evidence, but a hypothesis lifecycle should be explicit.

### Skill Registry

Skills expose versioned specs: input schema, output schema, required context layers, permissions, candidate cap, validators, and idempotency key. A skill may only output structured `CandidateProposal` or read-only information; it cannot call WQ, change Gate, mutate execution state, write long-term memory, or enable Prod Corr.

### Policy Engine

Policy must evaluate five layers: System Safety, User Global, Campaign, Skill, and Runtime. Output is `ALLOW`, `BLOCK`, `REQUIRE_CONFIRMATION`, or `ADJUST`, with matched rule IDs and reasons. Prompt text is not a policy authority.

### Context Builder V2

Context Builder V2 produces bounded, immutable, replayable snapshots from database facts:

| Layer | Content | Compression rule |
| --- | --- | --- |
| L0 | Safety rules, user long-term rules, campaign goal, budget, Gate, Self/PP/Prod settings, allowed skills, registry snapshot, run mode. | Never compress away. |
| L1 | Current round, hypothesis, skill, parent candidates, current candidate expressions, admission rejects, six metrics, Self Corr, PP Corr, unfinished results. | Exact facts for current decision; no fuzzy summary. |
| L2 | Recent research aggregates by family, skill, template, field cluster, and failure type. | Deterministic aggregation with representative and anomaly samples. |
| L3 | Top-K long-term memory and external knowledge relevant to the current direction. | Include asset IDs, evidence level, validation status, source refs, and updated time. |
| L4 | Raw evidence pointers. | Default to source ref, summary, and relevance reason; full text requires a read-only skill. |

Each snapshot records snapshot ID, campaign ID, round ID, registry snapshot ID, package IDs, memory revision, per-layer content, compression method, omitted counts, representatives, unknowns, token estimate, source versions, and content hash.

### NextMoveDecision

Each round ends with exactly one decision: `CONTINUE`, `REPAIR`, `CHANGE_SKILL`, `CHANGE_HYPOTHESIS`, `MIGRATE_TEMPLATE`, `PAUSE`, `STOP`, `ASK_USER`, or `COMPLETE`. The decision stores evidence refs, confidence, blocked scope, generated follow-up work, and policy outcomes.

### Memory Governance

Target memory types are `USER_POLICY`, `CAMPAIGN_MEMORY`, `RESEARCH_OBSERVATION`, `RESEARCH_HYPOTHESIS`, `VALIDATED_PRACTICE`, `EXTERNAL_KNOWLEDGE`, and `STOP_RULE`. Priority is system safety > user long-term policy > current campaign instruction > locally validated memory > reviewed external knowledge > AI hypothesis.

AI can only create `MemoryProposal`. Program code performs schema validation, dedupe, conflict detection, versioning, promotion, application, revocation, and archival. Evidence progression is Observation -> Hypothesis -> Locally Validated -> Long-Term Memory -> Policy/Stop Rule.

### Research Assistance

Assistance requests are first-class records, not memory. Required fields include category, priority, status, question, why needed, what was tried, evidence summary, requested material, suggested search queries, expected response format, blocked scope, and timestamps. Categories include `KNOWLEDGE_GAP`, `DIRECTION_GAP`, `EVIDENCE_CONFLICT`, `LOW_EFFICIENCY`, `MISSING_SKILL`, `DATA_GAP`, `PLATFORM_RULE_GAP`, `USER_DECISION`, and `SYSTEM_SETUP`.

### Provider And Secret

Provider Profile stores provider type, display name, base URL, model, secret ref, timeout, context/output token limits, enabled flag, validation status, and last validation time. API keys are submitted only over HTTPS and stored encrypted or by secret reference. Keys, full base URLs, credentials, tokens, cookies, submitted pools, full PnL, production DBs, and raw logs must not enter Git, docs, Context Snapshot, Research Package, or wq-review.

Unattended campaign preflight requires configured provider, decryptable key, model validation, successful test call, and sufficient context limit. Missing key creates a `SYSTEM_SETUP` assistance request and campaign waits instead of retrying forever.

### Research Package V2

The package root remains exactly four files:

```text
manifest.json
brief.json
seed_pack.csv
operator_probe.csv
```

`research-package.v2` must preserve v1 compatibility and add lifecycle states `UPLOADED`, `HASH_VALIDATED`, `SCHEMA_VALIDATED`, `SANITIZED`, `REVIEWED`, `IMPORTED`, `AVAILABLE`, plus `REJECTED`, `QUARANTINED`, and `INCOMPATIBLE`. Duplicate package ID plus content hash is idempotent. Imported external claims start as unverified assets or pending proposals.

## Run Modes

| Mode | Meaning | First refactor target |
| --- | --- | --- |
| `ADVISORY` | AI can analyze and propose, no autonomous materialization. | Supported as safe baseline. |
| `ROUND_AUTOMATIC` | One approved round can run automatically. | Optional after core contracts. |
| `CAMPAIGN_AUTOMATIC` | User confirms one direction, budget, allowed skills, and boundary; AI can run multiple rounds. | First official target. |
| `UNATTENDED` | Long-running operation with provider and assistance preflight. | Later target after GPT review and small experiment. |

Reconfirmation is mandatory for budget expansion, Region/Delay change, new high-risk skill, new provider credential, Prod Corr, final submission, irreversible operation, or scope expansion.

## Non-Breakable Boundaries

- Prod Corr remains disabled and cannot be enabled by AI, skill, background job, or policy fallback.
- Final Alpha submission is out of scope for this automation phase.
- Real WQ calls require a separate explicitly authorized task, API Gate, hard admission, budget/concurrency caps, unified adapter, and audit.
- The orchestrator only coordinates; it cannot generate expressions, submit simulations, compute results, mutate Gate, release slots, or write formal long-term memory.
- Any missing evidence is marked `UNVERIFIED`; the system must not infer permission or platform facts.

## V1 Implementation Evidence

`AI-AUTONOMOUS-BACKEND-V1-20260719-001` implemented the bounded V1 path in an isolated worktree after the Phase 0 fact-source check. The migration chain is `20260718_0031 -> 20260719_0032`, with one Alembic Head.

- `ResearchOrchestrator` owns only idempotent campaign/round progress, a round lease, bounded budget reservation, and append-only `MiningEvent` records. It does not create a second queue, result pullback, template store, factor store, Gate path, or formal memory write path.
- Candidate generation remains `Skill -> CandidateProposal -> CandidatePlan -> materializer -> Platform Registry hard admission`. The materializer binds an otherwise unbound plan to the active immutable Registry profile before final admission.
- Dispatch remains the existing shared `run_mock_dispatcher(..., agent_only=True, force_mode="mock")` path. Existing `ExecutionRequest`, Single/Multi batch transport, Result Ingestion, Factor Check, Factor Center, and feedback projections remain the facts of record.
- Versioned endpoints advance a bounded round and resume its results. Runtime facts are derived from server settings and Task Center control state, never client-provided Gate or execution flags.
- The local acceptance campaign covered four rounds and twelve unique candidates with a Gate block, recovery, duplicate result callback, Skill change, assistance request, and V2 package context rebuild.

This evidence is Mock/SQLite-only. Feature flags remain default-off outside test configuration; no frontend implementation, server deployment, real provider call, real WorldQuant call, Prod Corr, or final Alpha submission is authorized by this work.
