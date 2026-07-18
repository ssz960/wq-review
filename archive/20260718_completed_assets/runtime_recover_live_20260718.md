# RUNTIME-RECOVER-LIVE-20260718-001 Validation Report

## Outcome

`LIVE_LOOP_RUNNING`. The source-of-truth recovery, clean-checkout validation, deployed service recovery and explicitly authorized 12+30 bounded validation completed. The API Gate is now closed and the dispatch session is invalidated; live services remain healthy without a standing submission authorization.

## Git Recovery

- Baseline: integration commit `724ab02f` and its SCHED semantics, retaining the database head `20260718_0031`.
- Recovery commits before this report: `89552e00`, `af4e3800`, `143d42c3`, `ebe00f39`, `8ed57f6e`, `9debf66b`, `8efd8563`.
- 25 runtime/build files relative to the baseline were restored deliberately. Core files include `backend/app/schemas.py`, `security.py`, `task_queue.py`, `worker.py`, `mining/agent_control_service.py`, campaign/runtime helpers, `main.py`, deployment inputs and pinned dependencies.
- No database, runtime data, log, cache, backup, credential or deployment archive was committed. Scheduler, Result Ingestion semantics and the `0031` business schema were not rebuilt or downgraded.

## Clean Checkout Evidence

Fresh detached worktree at `8efd8563`, with execution forced to mock/disabled:

- `import app.main, app.worker`: PASS.
- Runtime recovery failure-closed/optional-route suite: PASS, 5/5.
- Live Result Loop integration suite: PASS, 12/12.
- Result Ingestion offline suite: PASS, 10/10.
- Single/Multi Allocation plus Consultant CORE: PASS, 26/26.
- Multi Transport replay, including sequential confirmed allocation regression: PASS, 17/17.
- Plain child-ID polling adapter regression: PASS, 1/1.

The local workstation did not have a Docker CLI available for a repeat local image build. The server image had already been built from the archived committed recovery source and was verified running after deployment; clean-checkout imports and all offline suites above ran on the final commit.

## Server Evidence

- Alembic reports the single head `20260718_0031`.
- `/api/health`: HTTP 200.
- backend, worker, postgres, redis, nginx, frontend and cloudflared: one running service each.
- Default RQ queue length: 0.
- The running backend contains the plain child-ID polling and sequential allocation reconciliation markers, and its authorized run limit is the expected bounded value.
- Gate close recorded through `_update_task_center_control_state(action="close_api_gate")`; dispatch is paused, safe shutdown is true and the session is closed.

## Authorized Runtime Batch Evidence

| Batch | Allocation | Terminal result | 429 | Facts | Normalized | Factor projections | Feedback deltas | Duplicate dispatch |
| --- | --- | --- | --- | ---: | ---: | ---: | ---: | --- |
| First 12 | 1 Multi Parent / 10 Children, 2 Single | 12 completed; 0 invalid/error/pending | 0 | 22 | 22 | 12 | 22 | 0 |
| Next 30 | 3 Multi Parents / 30 Children | 30 completed; 0 invalid/error/pending | 0 | 30 | 30 | 30 | 30 | 0 |

- Daily budget: 42 submitted, 0 reserved, 0 remaining, 0 counted failures.
- Allocation plans completed; all six recorded capacity reservations are released.
- Parent/Child state, Fact, Normalized, Factor projection and Feedback Delta were checked by aggregate queries only. No remote references, candidate expressions, raw payloads, credentials, production rows or logs appear in this report.

## Final State

`LIVE_LOOP_RUNNING`: the deployed API and worker are healthy, the requested bounded validation completed without duplicate dispatch or slot leak, and Gate is closed pending a separately authorized execution task.
