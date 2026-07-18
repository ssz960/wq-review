# Allocation Decision Matrix

| Condition | Decision | reason_code |
|---|---|---|
| Validation, Registry, Profile or Admission fails | REJECT | `REJECT_*` |
| Duplicate normalized expression | REJECT | `REJECT_DUPLICATE_EXPRESSION` |
| Valid one-row pack with capacity | SINGLE | `SINGLE_REMAINDER` |
| Valid 2-10 row pack with capacity | MULTI_CHILD | `MULTI_GROUPED` |
| Gate closed | DEFER | `DEFER_GATE_CLOSED` |
| Capacity semantics/observation unknown | DEFER | `DEFER_CAPACITY_UNKNOWN` |
| Shared capacity exhausted | DEFER | `DEFER_NO_SHARED_CAPACITY` |
| Separate Single capacity exhausted | DEFER | `DEFER_NO_SINGLE_CAPACITY` |
| Separate Multi capacity exhausted | DEFER | `DEFER_NO_MULTI_CAPACITY` |

Slot allocation never changes research priority or economic ranking. A failed
Multi is not automatically repacked as Single; repacking requires a new input,
AllocationPlan, Admission decision and authorization.
