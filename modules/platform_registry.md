# platform_registry

## 目标职责

把外部平台资产解析为可追溯的只读快照，为候选计划提供 `snapshot_id` 与规范化 SimulationProfile；不负责候选生成或执行。

## 入口文件

- `backend/app/mining/platform_registry.py`
- `backend/app/mining/platform_registry_sync_test.py`
- `backend/alembic/versions/20260718_0026_platform_registry_snapshots.py`
- `backend/app/mining/fixtures/platform_registry_v1/`

## 模型

`PlatformRegistrySnapshot`、`PlatformRegistryCapability`、`PlatformRegistryDataset`、`PlatformRegistryField`、`PlatformRegistryOperator`、`PlatformRegistrySetting`、`CandidateFieldUsageRecord`，定义于 `backend/app/models.py`。

## API

当前未在 `backend/app/main.py` 发现独立的 Platform Registry HTTP 路由。旧字段接口 `/api/fields/*` 来自 `backend/app/field_registry.py`，不能等同于本模块。生产触发接口：`UNVERIFIED`。

## 流程

`RegistrySource -> manifest/schema/hash 校验 -> PlatformRegistrySyncService -> 快照与明细表 -> profile 解析/受限查询`。

## 依赖

SQLAlchemy、配置、Git/本地目录源、manifest/schema fixture；候选物化与执行服务消费快照。

## 安全边界

Registry 数据只读；manifest 列出的内容必须按 hash 校验。同步不得触发 WQ、候选提交或执行。外部路径和 Git revision 必须显式，不从可变目录猜测。

## 当前实现

本地代码树中存在本地目录源、GitHub Contents 源、同步服务、SimulationProfile 解析、数据库模型、迁移和静态测试，但这些业务文件未纳入本任务提交。`wqa` 已提供不可变 `REG-20260718-001` Manifest 与离线 SQLite 参考验证器；服务器实际同步和部署状态仍为 `UNVERIFIED`。

## 已确认设计

平台注册快照与策略插件 registry 分离；候选链应引用不可变快照，`wqa` 保持独立资产仓库。

## 已知 Bug

- 与旧 `field_registry.py` 概念重叠，API 归属容易混淆。
- GitHub source 与“不配置本仓库 remote”并不冲突，但生产使用是否允许：`UNVERIFIED`。
- 当前 HEAD 只跟踪部分仓库文件，完整集成状态：`UNVERIFIED`。

## 验证记录

2026-07-18 `REG-20260718-001` 在禁网条件下从指定 `wqa` source commit 完成 Hash 校验、85,612 Field 流式导入、Active 切换、Top-K 查询、Operator 存在性与合法 Simulation Profile 校验；真实 WQ 调用为零。非 USA/TOP3000/D1 Field 和 Operator 类型兼容性未验证。

## 相关报告

- `docs/test_reports/phase1_platform_registry_and_git_assets_20260718.md`（历史阶段报告，非本次复验）
- `docs/test_reports/project_governance_and_gpt_codex_bridge_20260718.md`
- `docs/test_reports/platform_registry_upstream_validation_20260718.md`
