# Autonomous AI Contracts V1

- Task ID: `AI-BACKEND-ARCH-AUDIT-20260718-001`
- Status: contract only; no code or migration implemented in this task.

All contract objects require `schema_version`, `idempotency_key`, `created_at`, `source_refs`, and `content_hash` unless noted.

## ResearchPackage.v2

Root files are exactly `manifest.json`, `brief.json`, `seed_pack.csv`, and `operator_probe.csv`.

```json
{
  "schema_version": "research-package.v2",
  "package_id": "rpkg_20260718_example",
  "purpose": "CAMPAIGN_SEED",
  "created_at": "2026-07-18T00:00:00Z",
  "producer": {"kind": "gpt_review", "tool": "codex"},
  "source": {"repository": "wqb", "commit": "sha-or-UNVERIFIED", "source_type": "public_sanitized"},
  "files": {
    "brief.json": {"sha256": "..."},
    "seed_pack.csv": {"sha256": "..."},
    "operator_probe.csv": {"sha256": "..."}
  },
  "compatibility": {"regions": ["USA"], "delays": [1], "registry_snapshot_id": "snapshot-id"},
  "default_import_policy": "UNVERIFIED_ASSETS_ONLY"
}
```

Allowed purposes: `KNOWLEDGE_IMPORT`, `CAMPAIGN_SEED`, `ASSISTANCE_RESPONSE`, `TEMPLATE_RESEARCH`, `CORRELATION_RESEARCH`, `PPA_RESEARCH`.

Lifecycle: `UPLOADED`, `HASH_VALIDATED`, `SCHEMA_VALIDATED`, `SANITIZED`, `REVIEWED`, `IMPORTED`, `AVAILABLE`, `REJECTED`, `QUARANTINED`, `INCOMPATIBLE`.

## CampaignPolicy.v1

```json
{
  "schema_version": "campaign-policy.v1",
  "campaign_id": 1,
  "mode": "CAMPAIGN_AUTOMATIC",
  "budget": {"max_candidates": 12, "max_rounds": 4, "token_limit": 200000},
  "correlation_policy": {"self_corr": "ENABLED", "pp_corr": "ENABLED", "prod_corr": "DISABLED"},
  "allowed_skills": [{"skill_key": "base_expand", "version": "1"}],
  "requires_confirmation": ["BUDGET_EXPAND", "REGION_DELAY_CHANGE", "PROD_CORR", "FINAL_SUBMIT"],
  "decision": {"outcome": "ALLOW", "matched_rules": ["system.real_wq.default_closed"]}
}
```

## ResearchHypothesis.v1

```json
{
  "schema_version": "research-hypothesis.v1",
  "hypothesis_key": "hyp_fundamental_quality_rank",
  "campaign_id": 1,
  "status": "draft",
  "claim": "Quality fields may improve after group neutralized ranking.",
  "scope": {"dataset": "UNVERIFIED", "region": "USA", "delay": 1},
  "evidence_level": "UNVERIFIED",
  "source_refs": ["research_package:1#brief"],
  "promotion_rules": ["local_result_support_required"],
  "stop_conditions": ["two_rounds_no_near_or_pass"]
}
```

Statuses: `draft`, `active`, `paused`, `validated`, `rejected`, `superseded`, `archived`.

## SkillSpec.v1

```json
{
  "schema_version": "skill-spec.v1",
  "skill_key": "base_expand",
  "skill_version": "1",
  "input_schema": {"required": ["context_snapshot_id", "hypothesis_id"]},
  "output_schema": "CandidateProposal.v1[]",
  "required_context_layers": ["L0", "L1", "L3"],
  "permissions": ["pure_read", "pure_compute", "local_validate"],
  "max_candidates": 5,
  "validators": ["no_gate", "no_wq", "registry_fields_only"]
}
```

Forbidden permissions: direct WQ, Gate mutation, dispatch, `ExecutionRequest` status mutation, long-term memory write, final submission, Prod Corr enable.

## CandidateProposal.v1

```json
{
  "schema_version": "candidate-proposal.v1",
  "proposal_key": "proposal_round1_001",
  "campaign_id": 1,
  "round_id": 1,
  "hypothesis_id": 1,
  "skill_key": "base_expand",
  "template_id": "template-fixture",
  "expression": "REDACTED_OR_TEST_FIXTURE_ONLY",
  "simulation_profile": {"region": "USA", "universe": "TOP3000", "delay": 1},
  "field_refs": ["field_00"],
  "evidence_refs": ["memory:abc"],
  "risk": "low",
  "max_variants": 3
}
```

This object is not executable. Server-side admission creates or rejects `CandidatePlan` and then existing `CandidateRecord`/`ExecutionRequest` facts.

## ContextSnapshot.v2

```json
{
  "schema_version": "context-snapshot.v2",
  "snapshot_id": "ctx_1",
  "campaign_id": 1,
  "round_id": 1,
  "registry_snapshot_id": "snapshot-id",
  "research_package_ids": [1],
  "memory_revision": "memrev_1",
  "layers": {
    "L0": {"safety": [], "budget": {}, "allowed_skills": []},
    "L1": {"current_round": {}, "candidate_facts": [], "result_facts": []},
    "L2": {"recent_aggregates": []},
    "L3": {"top_k_assets": []},
    "L4": {"source_refs": []}
  },
  "compression_method": "deterministic_structured_aggregation",
  "omitted_counts": {"candidate": 0, "memory": 0, "source_text": 0},
  "included_representatives": [],
  "unknowns": ["alembic_head_20260718_0031_unverified"],
  "token_estimate": 1200,
  "source_versions": {"registry": "snapshot-id", "package": "1"}
}
```

## NextMoveDecision.v1

```json
{
  "schema_version": "next-move-decision.v1",
  "decision_key": "next_round1",
  "campaign_id": 1,
  "round_id": 1,
  "action": "ASK_USER",
  "reason": "low efficiency and missing evidence",
  "confidence": 0.64,
  "evidence_refs": ["factor_check:1", "memory:abc"],
  "blocked_scope": {"hypothesis_id": 1},
  "policy_result": {"outcome": "REQUIRE_CONFIRMATION", "matched_rules": []},
  "creates": [{"type": "AssistanceRequest.v1", "key": "assist_1"}]
}
```

Allowed actions: `CONTINUE`, `REPAIR`, `CHANGE_SKILL`, `CHANGE_HYPOTHESIS`, `MIGRATE_TEMPLATE`, `PAUSE`, `STOP`, `ASK_USER`, `COMPLETE`.

## MemoryProposal.v1

```json
{
  "schema_version": "memory-proposal.v1",
  "proposal_key": "memprop_1",
  "proposal_type": "RESEARCH_OBSERVATION",
  "scope": {"campaign_id": 1, "hypothesis_id": 1},
  "claim": "This template failed twice on the same field family.",
  "source_refs": ["round:1", "factor_check:2"],
  "evidence_summary": {"support": 0, "oppose": 2},
  "confidence": 0.55,
  "suggested_ttl": "campaign",
  "conflicts": [],
  "promotion_condition": "one more local validation result",
  "status": "proposed"
}
```

Statuses: `proposed`, `duplicate`, `conflict`, `accepted`, `rejected`, `applied`, `revoked`, `expired`.

## AssistanceRequest.v1

```json
{
  "schema_version": "assistance-request.v1",
  "request_key": "assist_1",
  "campaign_id": 1,
  "round_id": 1,
  "category": "LOW_EFFICIENCY",
  "priority": "high",
  "status": "OPEN",
  "question": "Need new evidence for quality-field repair direction.",
  "why_needed": "Two rounds produced no admitted near/pass candidates.",
  "what_was_tried": ["base_expand", "optimize_repair"],
  "evidence_summary": {"rounds": 2, "admission_rejects": 3},
  "requested_material": ["public forum post", "paper", "template note"],
  "suggested_search_queries": ["quality factor neutralization WQ", "trade_when quality repair"],
  "expected_response_format": "ResearchPackage.v2 or short campaign instruction",
  "blocked_scope": {"hypothesis_id": 1}
}
```

Statuses: `OPEN`, `ACKNOWLEDGED`, `ANSWERED`, `MATERIAL_RECEIVED`, `IMPORTED`, `RESOLVED`, `CANCELLED`.

## ProviderProfile.v1

```json
{
  "schema_version": "provider-profile.v1",
  "profile_key": "default_openai_compatible",
  "provider_type": "openai_compatible",
  "display_name": "Default provider",
  "base_url_ref": "masked_or_secret_ref",
  "model": "model-name",
  "secret_ref": "secret_1",
  "timeout_seconds": 30,
  "context_token_limit": 10000,
  "output_token_limit": 1500,
  "enabled": true,
  "validation_status": "validated",
  "last_validated_at": "2026-07-18T00:00:00Z"
}
```

Frontend-readable fields are limited to `profile_key`, `provider_type`, `display_name`, `model`, `configured`, `masked`, `validation_status`, `last_validated_at`, and `enabled`. Raw key, token, cookie, and unmasked base URL are never returned.
