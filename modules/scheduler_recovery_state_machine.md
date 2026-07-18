# Scheduler Recovery State Machine

Single and Multi both use `SimulationBatch`: Single has one Child; Multi has two
to ten. Both pass through the same Allocation, Capacity reservation, dispatch
and audit functions.

`planned -> reserved -> submitted -> running/poll_wait` retains its reservation.
429 becomes Child `poll_wait` with a retry timestamp. Parent Pending remains
explicit `poll_wait`. Process restart calls recovery, re-aggregates durable
Child facts and renews the lease without resubmitting.

Terminal Child writeback is idempotent. Parent error records an error but cannot
overwrite completed Child metrics. All-success becomes `completed`; mixed
terminal results become `partial` or `partial_timeout`; all timeout becomes
`timed_out`. Only these deterministic terminal aggregates release capacity.
Partial running work keeps the reservation. Unknown results require manual
review. No automatic Multi-to-Single fallback exists.
