# SCHED-20260718-001 Test Report

Commands:

- `python -m unittest backend/app/mining/single_multi_allocation_test -q`
- `python -m unittest backend/app/mining/consultant_core_test -q`
- `python -m compileall -q` for the allocation service, tests and migration.

Result: 25 tests passed, 0 failed. The allocation suite contributes 15 tests
and CORE regression contributes 10. No WorldQuant client was imported or
called, no production worker was started and no server Gate was modified.

Coverage includes 1, 2, 9, 10, 11 and 20 same-Profile candidates; mixed Region,
Delay, Language and Instrument Type; allowed/blocked Universe variance;
duplicates; invalid Field and Operator; Gate closed; insufficient capacity;
SHARED, SEPARATE and UNKNOWN modes; deterministic plan hash; read-only preview;
idempotent confirmation/dispatch/poll; Single+Multi shared Admission, Capacity,
Scheduler and Audit; Child Invalid/Timeout; Parent Pending/error; successful
Child preservation; 429; restart recovery; capacity snapshot change; and
rejection of AI-asserted/untrusted hard-Admission evidence.

Shared files for future RESULT integration: `backend/app/models.py`, migration
`20260718_0030_single_multi_allocation.py`, `docs/TASK_BOARD.md`,
`docs/PROJECT_STATUS.md`, `docs/MODULE_INDEX.md` and
`docs/DESIGN_DECISIONS.md`. This task did not modify `main.py`, `service.py`,
Result Ingestion, Factor Center, Research Center or Feedback Delta.
