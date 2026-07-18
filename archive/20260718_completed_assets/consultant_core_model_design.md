# Consultant Core Model Design

`CORE-20260718-003` adds an evidence-bounded core beside the stable Single
execution chain. It does not replace `ExecutionRequest`, `SimulationBatch`,
Admission, API Gate, queue priorities, or the real adapter.

The versioned entities are `SimulationProfileRecord` and
`SubmissionPolicyRecord`. Operational entities are `MultiSimulationParent`,
`MultiSimulationChild`, `CorrelationObservation`, `PoolMembership`,
`SuperAlphaSelectionPlan`, and `SuperAlphaSelectionResult`. Migration
`20260718_0029` is additive.

`consultant_core.py` contains deterministic database services only.
`consultant_core_schema.py` defines frozen read-only request schemas. The Mock
Scheduler returns audit envelopes and never creates a queue task or invokes WQ.

Strong evidence encoded as invariants: at most ten Child records per Multi
Parent; instrument type, region, delay and language are shared; Regular,
SuperAlpha and Power Pool policy scopes are distinct. Quotas and thresholds are
not modeled as facts.
