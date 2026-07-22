# Completed Asset Migration Index

This archive is the cumulative review entry point. It preserves completed assets
that were previously spread across task notes and temporary review snapshots. It
does not replace current module authority, and it is retained rather than
deleted whenever a newer document supersedes an older one.

| Historical asset or entry | Current authoritative document | Migration relationship |
| --- | --- | --- |
| Autonomous architecture contract | `docs/audits/autonomous_ai_backend_v1_architecture_contract_20260719.md` | Prior architecture entry -> retained V1 contract |
| Autonomous V1 design | `docs/design/autonomous_ai_backend_v1_design_20260719.md` | Prior design entry -> retained V1 design |
| Autonomous V1 local validation | `docs/test_reports/autonomous_ai_backend_v1_20260719.md` | Prior local validation -> retained pre-server evidence |
| Autonomous server Mock V1 | `docs/test_reports/autonomous_ai_server_mock_v1_20260719.md` | Current server acceptance report |
| Real Provider preflight V1 | `docs/test_reports/real_provider_preflight_v1_20260722.md` | New preflight evidence -> retains Server Mock and architecture history; current run is blocked before server deployment |
| REG / Platform Registry | `docs/modules/platform_registry.md` | Completed Registry asset -> current Registry authority |
| CORE / consultant core | `docs/modules/consultant_core_model_design.md` | Completed core asset -> current core-model authority |
| SCHED / single-multi allocation | `docs/modules/single_multi_allocation_design.md` | Completed allocation asset -> deterministic allocator authority |
| allocation decision | `docs/modules/allocation_decision_matrix.md` | Historical decision record -> current decision matrix |
| capacity policy | `docs/modules/capacity_policy_and_slot_model.md` | Historical capacity record -> current reservation policy |
| scheduler recovery / RUNTIME-RECOVER | `docs/modules/scheduler_recovery_state_machine.md` | Historical recovery entry -> current durable lease contract |
| RESULT / Result Ingestion | `docs/test_reports/autonomous_ai_server_mock_v1_20260719.md` | Prior result entry -> canonical `execution_result_facts` evidence |
| TPL / Template Center | `docs/modules/template_center.md` | Historical template entry -> current template authority |

The documents in this archive and the linked module/design/report records are
intentional historical evidence. A newer authority is recorded as a migration
target above; no historical entry is silently removed from the review boundary.
