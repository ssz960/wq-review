# CORE-20260718-003 Test Report

Command: `python -m unittest backend/app/mining/consultant_core_test -q`.
Result: 10 tests passed, 0 failed. No WorldQuant request, real Simulation or
queue task was created.

Coverage includes canonical Profile idempotency; two Multi Parents with 5 and 7
Child rows; all-success; mixed Invalid and Timeout; Parent error after Child
success; duplicate terminal writeback; restart recovery; scope/count invariants;
three Policy scopes; five correlation types; Power Pool history; SuperAlpha
effective selection; and explicit Single/Multi Mock Scheduler external states.

Model and migration files pass `compileall`. The existing
`consultant_multi_transport_smoke_test.py` could not start because this tracked
branch does not contain `backend/app/db.py`; that is a pre-existing untracked
workspace gap. No existing Single model, state transition, Admission, API Gate,
priority or adapter file changed, so static Single compatibility is PASS and
executable legacy regression remains UNVERIFIED.
