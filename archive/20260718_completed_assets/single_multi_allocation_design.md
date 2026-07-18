# Single/Multi Allocation Design

`AllocationService` is the deterministic boundary between AI CandidatePlan
intent and the existing execution transport. AI priority is accepted as an
ordering input; AI transport hints are ignored.

The fixed pipeline is expression/field/operator validation, Registry and
Profile validation, hard Admission evidence, expression deduplication, shared
invariant grouping, packing, Capacity reservation planning, immutable
Allocation persistence, then unified Mock scheduling. Preview returns the full
decision set and creates no database rows. Confirm persists one
`AllocationPlanRecord`, its decisions, existing `SimulationBatch` parents and
`SimulationBatchChild` rows, slot reservations and audit events in one database
transaction.

Packing is stable by descending supplied priority and candidate key tie-break.
A group is defined by Instrument Type, Region, Delay and Language. Universe or
Settings can vary only when every affected candidate carries an explicit
Registry capability. Packs contain at most ten Child rows; a one-row remainder
is Single. Thus 11 candidates produce 10 Multi Child plus one Single, while 20
produce two Multi parents.

`plan_hash` covers normalized inputs, Registry snapshot, Submission Policy
version, Capacity Policy/Snapshot, Gate state and decisions. Reversed input
order produces the same hash and confirmation is idempotent.
