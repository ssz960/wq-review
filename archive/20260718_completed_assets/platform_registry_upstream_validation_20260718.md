# REG-20260718-001 Platform Registry Upstream Validation

## Result

`PASS / READY_WITH_LIMITS`。本任务没有修改候选研究逻辑、Admission 规则、Materializer、前端或执行适配器，没有启动回测、提交 Alpha 或调用真实 WQ。

## Scope

- 独立 `wqa` main worktree 全文件盘点、前置资产合并、目录/命名/重复/来源审计。
- 已登录 Chrome 下只读 Dataset、Simulation Settings、Operator 页面核验。
- 不可变 Manifest、Schema、Hash、Scope 覆盖矩阵和容量报告生成。
- 指定 source commit 的断网 SQLite Active Snapshot 端到端验证。
- 2 CPU / 2 GB RAM / 40 GB disk 承载评估与 Active/Previous 清理策略。

## Evidence

- `wqa` snapshot: `REG-20260718-001`；final commit `cea82e2119b8a91db818722294ef45d18e3f6a6b`；Manifest source commit `8548481557fbf51b970a7a22f988bad19f1f7732`。
- Manifest coverage: `PARTIAL`；Dataset Scope 6,509，Field 85,612，Operator 85，合法 Profile 483。
- Field coverage: USA/TOP3000/D1 complete；其余 42 个 Scope missing and fail-closed。
- Hash/Manifest verification approximately 0.02 s；SQLite import approximately 5.5 s。
- SQLite 41,320,448 bytes；estimated indexes 25,910,288 bytes；peak process RSS 33,624,064 bytes。
- Local query 25 runs: median approximately 1.99 ms, P95 approximately 2.85 ms。
- Top-K query filters: Region USA, Dataset analyst15, Field type MATRIX, keyword earnings, limit 5。
- Operator `ts_rank` exists, but compatibility status is `UNVERIFIED`。
- Simulation Profile hash: `9bacfc9691578e7e94a82aace249f738fc4c6477b33ff6e62ee90ef62c2a26d5`。
- Embedding disabled；vector database disabled；network disabled；real WQ calls 0。

## Capacity and rollback

Active Snapshot compressed payload plus SQLite is under 50 MiB. Active + Previous steady-state and one staging generation remain far below 40 GB. Import is streaming in 1,000-row batches and does not hold the complete CSV in memory. The server must retain Active and Previous, delete staging on failure, atomically switch only after validation, and delete snapshots older than Previous only after a new Active succeeds.

## Limits / blockers

- Non-USA Field catalogs are unavailable; no deployment may claim multi-Region Field completeness.
- Operator-to-field-type compatibility lacks an authoritative platform record and must fail closed.
- Full multi-Region Field disk/import cost cannot be measured until authoritative records are collected.
- Server deployment and production activation were not authorized or performed.
