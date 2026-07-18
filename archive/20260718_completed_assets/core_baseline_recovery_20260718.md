# CORE-20260718-004 Baseline Recovery Report

Date: 2026-07-18

## Scope

The recovery started from `phase3/research-exchange@0f610cd772a6dc1d7de383dc4ba99c94912042c9` in the isolated branch `codex/core-20260718-003-recovery-root`. It merged the fixed REG/CORE chain ending at `core/CORE-20260718-003@d60cb900e41fac1f12f11aeebc4d682fb36b21fc` and resolved governance conflicts without replacing the later GOV/KNOW history.

Only explicit source files needed by the RESULT audit and executable offline regressions were recovered from the pre-existing untracked working tree. Every copied file was verified byte-for-byte with SHA-256 before staging. No database, environment file, log, archive, credential, raw platform response, PnL or production research result was copied.

## Restored facts

- Alembic revisions `20260606_0001` through `20260718_0029`, including the required `20260718_0028 -> 20260718_0029` dependency.
- `SimulationProfileRecord`, `SubmissionPolicyRecord`, Multi Parent/Child, five append-only Correlation Observation types, Pool Membership history and SuperAlpha Selection Plan/Result.
- CORE deterministic services, schemas and ten offline tests.
- Tracked RESULT audit targets: Data Pullback, both Research Package services, Factor Center/checks, Research Center/Memory/Context and `db.py`, plus their minimum import/test dependencies.
- Current governance history was retained. The incoming Registry decision was renumbered to `DD-20260718-009` because the current fact source already used DD-007 and DD-008.

## Verification

| Check | Result |
| --- | --- |
| CORE consultant model unit tests | PASS, 10/10 |
| Consultant Multi transport smoke | PASS, 15/15 |
| Research Exchange V2 offline regression | PASS |
| Research Memory V2 offline smoke | PASS, 12 deterministic checks |
| Research Center offline smoke | PASS, 10 checks |
| Research Package lifecycle | PASS |
| Context snapshot and runtime mode regressions | PASS |
| Data Pullback service smoke | PASS |
| Data Pullback full route smoke | BLOCKED: `app.main` control-plane dependency is outside the recovered minimum slice |
| Factor Center legacy smoke | FAIL: `legacy_detail["alpha"]["has_curve"]` expected true; treated as a pre-existing RESULT audit defect |
| Alembic revision graph | PASS: 29 revisions, one head `20260718_0029` |
| Governance test | PASS: 11 modules, no orphan, unique task IDs |
| Python compileall | PASS |
| High-risk literal scan | PASS: no credential/key/token/private-key/credential-bearing DSN literal found |
| Real WQ, queue task, deployment, API Gate or frontend mutation | NONE |

The Factor Center failure and missing full API control-plane slice are explicitly not repaired by this recovery task. They are evidence for `RESULT-20260718-001`, whose design audit must complete before business coding.
