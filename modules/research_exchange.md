# research_exchange

## 目标职责

在本地系统与独立 `wqb` 资产仓库之间交换经过 schema、大小、路径和 hash 校验的研究快照/增量包，不承担运行时调度。

## 入口文件

- `backend/app/mining/research_exchange_v2.py`
- `backend/app/mining/research_package_service.py`
- `backend/app/mining/research_package_export_service.py`
- `backend/app/data_pullback.py`
- `backend/alembic/versions/20260621_0023_research_package_lifecycle_v1.py`

## 模型

`ResearchPackage`、`ResearchPackageAsset`、`ResearchArtifact`、`DataPullbackJob`、`DataPullbackItem`。

## API

`/api/research-campaigns/{id}/packages*`、`/api/research-packages/cleanup`、`/api/research-storage/health`、`/exports/incremental`、artifact download；Data Pullback 为 `/api/data-pullback/*`。

## 流程

`local reviewed state -> PlatformResearchSnapshotBuilder/ResearchDeltaBuilder -> manifest/hash/schema -> bounded package/Git adapter -> wqb`；反向只接受 inbox 白名单包并先校验。

## 依赖

Research Center、campaign/session 模型、package 存储、独立 wqb manifest/schema 和可选 Git adapter。

## 安全边界

拒绝路径穿越、symlink、超大文件/数量、hash/schema 不符和未批准内容。不得交换数据库、凭据、完整 PnL、原始日志或长寿命大 ZIP。wq-review 不是 wqb 包。

## 当前实现

快照 builder、delta builder、service、Git adapter、包生命周期 API 与测试存在。独立 `wqb` Public 骨架已建立，当前 manifest 为空且不含运行 inbox/outbox；实际交换 cursor、服务器包状态为 `UNVERIFIED`。

## 已确认设计

`wqb` 独立；交换按 manifest 发生；服务器运行不依赖 wq-review；Data Pullback 是冷数据归档，不是公开审阅桥。

## 已知 Bug

- Research package、exchange、pullback 三套打包能力容易被混用。
- Git adapter 的实际部署凭据/仓库策略：`UNVERIFIED`。
- 根目录遗留多个 ZIP/tar 包说明历史打包缺少统一出口。

## 验证记录

2026-07-18 静态审计类、路由、模型和历史测试；独立 wqb 骨架通过 schema、白名单、链接、敏感信息和匿名读取验证。未生成/导入任何研究包。

2026-07-18 `KNOW-20260718-002`：独立 `wqc` 发布了源索引的脱敏候选知识资产，均标记 `PENDING_HUMAN_REVIEW` 且未列入其 Manifest；`wq-review` 仅含摘要。原始论坛 JSON、SQLite、完整帖子和账号资料保持本地，不进入交换包。
2026-07-18 `KNOW-20260718-003`：相关性工程候选资产通过现有单向审阅链发布。仅交换 Practice Card、来源定位、证据矩阵与冲突摘要；不交换原始帖子、外部代码、接口信息、账号信息、Alpha 表达式、结果或执行包。
2026-07-18 `KNOW-20260718-004`：使用固定 Commit 的临时浅层工作区发布 `wqc` 与 `wq-review`，完成后删除临时目录。公开资产仅含脱敏主链结论；原始论坛内容、代码和接口信息未进入单向发布链。
2026-07-18 `KNOW-20260718-008`：对昆奇、非洲顾问、全球顾问和顾问专属中文四个板块执行可恢复的只读全量采集。原始分页快照、完整帖子和索引数据库只保留在任务本地缓存；`wqc` 只接收结构化脱敏结论、来源定位和主题覆盖，`wq-review` 只接收摘要、冲突与待审项。

## 相关报告

- `docs/test_reports/phase3_research_exchange_and_pullback_v2_20260718.md`（历史阶段报告）
- `docs/test_reports/project_governance_and_gpt_codex_bridge_20260718.md`
