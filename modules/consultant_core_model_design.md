# Consultant Core Model Design

## Status

Historical completed asset restored for cumulative review navigation. The current authoritative implementation is `backend/app/models.py`, with transport behavior documented in `execution_transport.md` and allocation behavior in `single_multi_allocation.md`.

## Scope

The design covers immutable simulation profiles, submission policy records, Multi parent/child transport facts, correlation observations, pool membership, and SuperAlpha selection plans/results. It does not authorize real execution or own a separate queue.

## Migration

The restored source history is represented by the linear compatibility revisions `20260718_0028` and `20260718_0029`; current schema evolution continues through the unique head `20260719_0032`.
