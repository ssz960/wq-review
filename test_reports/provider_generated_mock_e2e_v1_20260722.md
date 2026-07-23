# AI Provider Mock E2E V1 Server Acceptance

STATUS: BLOCKED

Task: `AI-PROVIDER-MOCK-E2E-V1-20260722-002`
Generated: `2026-07-23T04:42:35Z`

## Decision

The server deployment and the bounded real Provider preflight were exercised. The task is BLOCKED before Provider-generated Mock execution because the ten-call Provider budget was exhausted by repeated empty or non-JSON Provider outputs. No Proposal, Candidate, Mock ExecutionRequest, Mock Result, WQ call, or manual substitute was created.

The single blocking condition is external Provider output instability within the authorized call budget. The implementation now fails closed when this occurs and the server Readiness response reports `provider_preflight_call_budget_exhausted`.

## Source And Deployment

- `implementation_commit`: `3273770cd3b808f9e7be1942d78bb6735eaf6507`
- `source_head`: `3273770cd3b808f9e7be1942d78bb6735eaf6507` (deployed code head; the later evidence commit is documentation only)
- `server_deployed_commit`: `3273770cd3b808f9e7be1942d78bb6735eaf6507`
- Previous server code: `e8f3ad09a9232ad1fd68394514e92ba07f2ade2f`
- PostgreSQL migration: `20260719_0032 -> 20260722_0033`
- Final server Alembic Head: `20260722_0033`, unique
- Active Registry: `REG-20260718-001`, non-fixture; source commit `8548481557fbf51b970a7a22f988bad19f1f7732`
- Provider type: `openai_compatible`; model identifier configured and intentionally omitted; profile key is represented by hash `c7ba941dd7cccfce`

The prior V1 deployment began at `20260719_0032`; it was not rebuilt over in place. A custom PostgreSQL dump and the previous source tree were retained at each deployment rollback point. The final rollback point is the `pre-3273770` backup set. The dump has a valid PostgreSQL custom-format signature. No production database was rebuilt.

## SSH And Cleanup

The earlier TCP-reachable/no-banner incident has no provable root cause in the available console evidence. The console recovery left the system SSH unit active and port 22 listening; repeated non-interactive commands through the dedicated SSH alias were stable. SSH authentication and the shared administrator account were not changed because another project uses that access path. The deployment directory was replaced from a verified local archive, legacy source was moved to a rollback archive, and unrelated build cache was pruned conservatively. The retained frontend image was used because frontend source is outside this backend task.

## Application Evidence

- `/api/health`: HTTP 200
- FastAPI `app.openapi()` on the deployed application contains exactly four Autonomous routes: readiness, providers, campaigns/mock, and provider-preflight/run.
- Provider listing returned only masked/configured/validated/status fields.
- Readiness before calls was configured, armed for Provider preflight, not yet validated, and warned that the execution gate was closed.
- Readiness after the budget was exhausted: `ready_for_real_provider=false`, blocker `provider_preflight_call_budget_exhausted`; `ready_for_real_single=false` and `ready_for_consultant_multi=false`.
- Required Autonomous schema tables: 13 of 13 present.
- Active Registry and its source provenance were checked from the server database, not a fixture or legacy field endpoint.
- Existing Research Center, Task Center, Factor Center, feedback, and execution-plane read endpoints returned HTTP 200.

## Real Provider Preflight

The only real Provider entry point was `Provider Profile -> environment reference resolution -> bounded request -> response parser -> strict Pydantic schema -> authority-key rejection -> sanitized audit -> research facts`. It uses one trace ID internally across the request path. No secret value, authentication header, prompt, full response, or server address was printed or persisted in the audit facts.

There were 8 sanitized audit records and 10 physical Provider calls, serialized with concurrency 1. Total recorded usage was 5,040 tokens and total measured latency was 26,303 ms. Provider retry count for network/rate-limit retries was 0; the final proposal record used the single permitted JSON repair call. All HTTP responses below were Provider status 200; the application status and error category are the authoritative outcome.

| Seq | Purpose | Outcome | Error category | Physical calls | Tokens | Latency ms | Response length |
| --- | --- | --- | --- | ---: | ---: | ---: | ---: |
| 1 | connectivity | failed | empty_response | 1 | 215 | 1541 | 1027 |
| 2 | connectivity diagnostic through the same service | succeeded | none | 1 | 189 | 1318 | 866 |
| 3 | structured_json diagnostic | failed | empty_response | 2 | 448 | 3127 | 986 |
| 4 | structured_json | failed | empty_response | 1 | 231 | 1439 | 1000 |
| 5 | structured_json retry | succeeded | none | 1 | 339 | 2583 | 1551 |
| 6 | proposal_dry_run | failed | empty_response | 1 | 713 | 3186 | 1658 |
| 7 | proposal_dry_run diagnostic | failed | empty_response | 1 | 713 | 3335 | 1720 |
| 8 | proposal_dry_run retry plus JSON repair | failed | non_json_response | 2 | 2192 | 9774 | 2743 |

The successful structured request was replayed after the API/worker restart. The replay returned the stored success with `replayed=true` and added zero Provider calls. 401/403, 429/5xx, timeout, network, empty, non-JSON, missing-field, oversized, and malicious-authority-field paths are covered by the local Stub test; no invalid real credential was used to manufacture an authentication failure.

## Mock Execution Boundary

Provider-generated Mock execution did not start because no valid Proposal was available and the Provider call budget was zero. The task-specific campaign and strategy were absent, so the measured deltas are all zero:

- Proposals: 0
- Research rounds for the Mock campaign: 0
- Candidates: 0
- `ExecutionRequest`: 0
- `SimulationBatch`: 0
- `SimulationBatchChild`: 0
- Result facts: 0
- Feedback deltas: 0
- Duplicate execution requests: 0
- Duplicate result facts: 0
- WQ, Single, Multi, correlation, and submission calls: 0

Consequently, the required two rounds, six candidates, Gate-close dispatch check, result ingestion, Context/NextMove/Memory update, and worker restart during the two-round run remain unverified. No fake result was inserted to make those checks pass.

## Bugs And Fixes

1. The preflight token was present in the server environment but not passed to the backend container. `docker-compose.yml` was fixed in `e58fe5d`, and the protected route then authenticated correctly.
2. The committed source lacked the referenced nginx configuration. A minimal proxy/static configuration was added in `ea3b46f`; `nginx -t` passed and health returned 200.
3. Reasoning-capable Provider output exhausted the original connectivity/schema limit of 96 tokens and the Proposal limit of 256, producing empty content with `finish_reason=length`. The bounded limits were changed to 256 and 512 respectively in `112d0a9` and `137aff2`; local regression tests assert both limits.
4. Readiness did not account for a missing or exhausted Provider budget. It now fails closed in `3273770`; the deployed response exposes the exhausted-budget blocker.
5. The first deployment helper checked four bytes of the five-byte `PGDMP` signature and stopped before source replacement. The procedure was corrected to validate five bytes and use separate staging/extraction steps. No incomplete backup was used as a rollback point.

## Resource Use

The server has 2 CPUs, about 843 MB available memory, and about 15 GB free on a 40 GB disk. At the final observation: backend used 119.9 MiB of its 384 MiB limit, worker 51.13 MiB of 256 MiB, PostgreSQL 131.9 MiB of 192 MiB, and Redis 8.301 MiB of 64 MiB. No parallel build or high-load test was run.

## Remaining Boundary

This evidence authorizes neither real WQ execution nor Provider-generated Mock execution. The next attempt must use a fresh, explicitly approved Provider call budget after the external Provider returns stable structured content. WQ Gate, real execution, Single, consultant Multi, correlation, and submission remain closed.
