# TPL-20260718-001 Template Candidate Promotion

Result: PASS (offline). No real WorldQuant call, backtest, Alpha submission, credential access, frontend change, or server deployment occurred.

The existing `TemplateLibraryRecord` is consumed by Template Library APIs, candidate generation, optimize/repair, Factor Check performance writeback, Research Memory, test-expand, and research-assistant. `docs/template_distills/` is historical input, not a runtime fact source. This task extends that record instead of creating another runtime library.

The wqc candidate schema has 17 fields. Lifecycle: `PENDING_HUMAN_REVIEW -> APPROVED_FOR_IMPORT -> DRAFT -> REVIEWED -> TESTING -> ACTIVE/PAUSED/RETIRED`. Import is idempotent on `template_id + version + content_hash` and records provenance/evidence, Registry Snapshot, roles/types, Operators, Profiles, risk, and validation.

Template Asset Export writes `template_library_snapshot.jsonl`, three required CSV summaries, and a manifest containing database epoch, Registry Snapshot, time, and hashes. Research Package generation keeps exactly `manifest.json`, `brief.json`, `seed_pack.csv`, and `operator_probe.csv`; seed_pack has the nine requested template columns.

Offline direct runner: 5 test functions covering all 10 requested scenarios PASS. Both wqc JSONL Schemas and example rows validate; size scan PASS. Recommendation: make wqb private before richer GPT exchange. This task did not publish to wqb.
