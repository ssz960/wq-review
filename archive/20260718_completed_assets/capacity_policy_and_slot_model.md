# Capacity Policy And Slot Model

`CapacityPolicyRecord` supports `SHARED`, `SEPARATE` and `UNKNOWN`. Limits are
optional evidence in JSON; no slot count, Parent concurrency, daily quota or
failure-counting rule is embedded in code. `CapacitySnapshotRecord` records one
runtime observation of available shared, Single and Multi-parent capacity.

Under SHARED, each Single or Multi Parent consumes one observed shared unit.
Under SEPARATE, Single and Multi Parent consume their respective observed
capacity. UNKNOWN or missing observations fail closed to `DEFER`, never `FAIL`.

`CapacitySlotReservation` is persisted before a Batch or ExecutionRequest is
marked reserved. Single and Multi use the same reservation table and scheduler
entry. Reservations remain held through Pending, 429 and recovery-required
states; release occurs only after deterministic terminal aggregation or an
explicit recovery path.
