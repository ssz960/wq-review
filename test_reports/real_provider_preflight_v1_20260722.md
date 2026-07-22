# Real Provider Preflight V1

Task: `AI-REAL-PROVIDER-PREFLIGHT-V1-20260722-001`

AI real Provider preflight V1: BLOCKED

`NEXT_STATE: BLOCKED_SERVER_SSH_BANNER`

## Outcome

The local implementation is complete and its isolated tests passed. Server
deployment and the required real Provider acceptance did not begin because the
server accepted TCP on port 22 without returning an SSH banner before
authentication. The task therefore remains fail-closed. No local Stub result,
old log, historical Provider activity, fixture Registry, or prior Server Mock
result is used as a substitute for server acceptance.

## Source, Deployment, And Registry Evidence

| Fact | Recorded value |
| --- | --- |
| Local implementation commit | `95674fd4ab6ef6e6fb1eb6383ae43b65e9f50ceb` |
| Local Alembic head | `20260722_0033` |
| Last recorded server deployment commit | `e8f3ad09a9232ad1fd68394514e92ba07f2ade2f` |
| Last recorded server Alembic head | `20260719_0032` |
| Server migration in this task | Not attempted; `0032 -> 0033` requires an authenticated server session |
| Last recorded Active Registry | `REG-20260718-001`, non-fixture provenance |
| Registry source commit | `8548481557fbf51b970a7a22f988bad19f1f7732` |
| Backup and rollback in this task | Not created because the remote deployment step never began; the prior Server Mock rollback point remains untouched |

The committed implementation adds migration `20260722_0033` and one isolated
preflight chain: `Provider Profile -> Secret env reference -> bounded context
-> HTTP -> parser -> strict JSON Schema -> authority-key rejection -> sanitized
LLM audit -> Research Round/Hypothesis proposal dry run`. It is deliberately
separate from the legacy Agent Runtime and Supervisor Provider pools, does not
write `LlmCallRecord`, and never calls the execution adapter or a WQ path.

Secret resolution accepts only `LlmProviderSecret.key_reference` in `env:NAME`
form; ciphertext is never read. The server-only API requires its own header
token, accepts only `connectivity`, `structured_json`, or `proposal_dry_run`,
and cannot select a Profile, Gate, real mode, budget, execution, Single, Multi,
correlation, or submission control. Provider and model identifiers remain
masked/not-read in this blocked run.

## Local Verification And Debug Record

| Trigger and root cause | Minimal correction | Commit | Re-verification |
| --- | --- | --- | --- |
| The recovered source had no isolated, authoritative real-Provider preflight path, so Runtime/Supervisor reuse could create duplicate calling and audit semantics. | Added one bounded service, migration, protected API, strict schemas, sanitized audits, retry policy, transactional idempotency, and local Stub coverage. | `ce0f2444334124ee9a88d957046e3ef160139be6` | Isolated preflight test passed; no execution or WQ dependency is imported by the entry point. |
| An active Registry lookup used a singleton query that did not express the required ambiguity failure mode. | Treat zero or more than one active Registry as `active_registry_missing_or_ambiguous`; reject fixture/mock provenance. | `95674fd4ab6ef6e6fb1eb6383ae43b65e9f50ceb` | Local readiness and proposal tests fail closed for missing or ambiguous Registry state. |
| Governance validation generated a generic review request, so it did not verify that the published snapshot requested the current task. | Read the committed preflight review request and assert its task binding, primary report, BLOCKED outcome, counters, and four actual OpenAPI routes. | `777e157608ab64fbbaff15b0a0583cd372935fdf` | Governance test passed with the actual preflight request. |
| The generated review README linked the cumulative migration index at the wrong published path. | Point the README link at `history/completed_asset_migration_index.md` and retain link validation. | `777e157608ab64fbbaff15b0a0583cd372935fdf` | Fixed-tree link scan reports zero missing links. |

- `provider_preflight_v1_test.py` upgrades an isolated database from
  `20260719_0032` to `20260722_0033`, verifies the sole Alembic head, and uses
  the real FastAPI application's `app.openapi()`.
- OpenAPI contains four Autonomous routes: readiness, providers, mock campaign,
  and provider preflight.
- Stub coverage passed for success, 401, 403, 429, 500, timeout, network
  failure, empty response, non-JSON response, missing fields, oversized output,
  malicious authority fields, retry limits, duplicate callback/idempotency, and
  restart replay.
- 401/403 have no retry; 429/5xx have at most two retries with bounded
  `Retry-After`; JSON repair is bounded to one extra request. Running
  idempotency keys return a 409-style fail-closed result; completed keys replay
  their sanitized result without another Provider request, Round, or Proposal.

## Server Connection Blocker

Two batch SSH attempts and a separate five-second banner read reached neither
authentication nor a remote command. The connection state is
`server_ssh_banner_unavailable`. This is the only current blocker. The server
was not inspected, cleaned, backed up, synchronized, migrated, restarted, or
queried after the failure; resource usage and current health therefore cannot
be revalidated by this task.

A post-local-verification TCP retry again connected to port 22 and timed out
waiting for the protocol banner. It did not invoke an SSH authentication flow or
remote command, confirming the same blocker without changing server state.

## Real Provider Request Ledger

| Intended purpose | Status | Duration | Token count | Retries | Result |
| --- | --- | --- | --- | --- | --- |
| Minimal connectivity | Not started | 0 | 0 | 0 | Blocked before server authentication |
| Strict structured JSON | Not started | 0 | 0 | 0 | Blocked before server authentication |
| Active-Registry Proposal Dry Run | Not started | 0 | 0 | 0 | Blocked before server authentication |

- Real Provider requests: `0` of maximum `10`; concurrency: `0` of maximum `1`.
- Strict JSON requests: `0`; Dry Run proposals: `0`; Provider audit records: `0`.
- `ExecutionRequest` delta: `0`; duplicate `ExecutionRequest`: `0`; duplicate
  Result Fact: `0`.
- WQ, Single, Multi, Self Corr, PP Corr, Prod Corr, and final submission calls:
  `0`.

## Safety, Service, And Restart Boundary

- The WQ Gate and all real execution flags were not changed. The prior server
  record has them closed; it was not used as a current-health assertion.
- `/api/health`, backend, worker, Postgres, Redis, legacy reads, masked Provider
  response, readiness response, current Registry freshness, and current
  resource usage were not rechecked because no remote session was established.
- No worker or API restart was initiated in this task, so no server restart
  idempotency claim is made. Local restart replay coverage passed without a
  second Provider call.
- No Secret, authentication header, prompt, full Provider response, server
  address, connection string, or production log was read into this report,
  database public field, diagnostic output, or `wq-review`.

## Remaining Boundary

After an SSH service that returns a banner is restored, the next attempt must
start over with server inventory/cleanup, database and release backups, resource
guard, clean deployment of the committed local source, `0032 -> 0033` migration,
health/readiness/Registry checks, and no more than ten serialized real Provider
requests. Until those facts exist, the task cannot enter real Provider generated
mock execution or any WQ state.
