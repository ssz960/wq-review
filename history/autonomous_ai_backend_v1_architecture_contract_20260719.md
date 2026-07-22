# Autonomous AI Backend V1 Architecture Contract

This cumulative contract records the completed V1 boundary preceding server Mock acceptance.

- Registry facts come only from an immutable Active Platform Registry snapshot.
- AI and fixture Skills may propose bounded candidates but cannot open Gate, select real mode, expand budget, or submit Alpha.
- Execution identity remains `execution_requests + simulation_batches + simulation_batch_children`.
- Result Ingestion authority remains `execution_result_facts`, projected through `normalized_execution_results` and feedback deltas.
- Campaign events reuse `MiningEvent`; no second event table, queue, Factor store, or Mock state machine is allowed.
- Unknown Registry, Provider, Transport, budget, or storage state fails closed.
- Real Provider and real execution require a separate human-approved task.
