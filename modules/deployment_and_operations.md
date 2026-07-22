# deployment_and_operations

## 目标职责

管理本地开发、本地 Git、部署包生成、服务器资源 guard、发布/回滚和脱敏审阅桥；服务器只运行，不成为源码事实源。

## 入口文件

- `docker-compose.yml`
- `backend/Dockerfile`、`frontend/Dockerfile`、`nginx/`
- `scripts/server_resource_guard.sh`、`scripts/safe_server_deploy.sh`、`scripts/real_api_gate.sh`
- `scripts/start_local_dev.bat`、`scripts/check_local_dev.bat`、`scripts/stop_local_dev.bat`
- `scripts/wq_review.py`、`scripts/test_project_governance.py`

## 模型

无独立业务模型。部署事实由本地 commit、部署包 manifest、服务器脱敏状态和测试报告表达；当前尚无统一部署记录表。

## API

健康检查 `/api/health`。部署/审阅生成器为 CLI，不提供网络 API。服务器连接地址、用户和凭据不得写入本模块。

## 流程

`task branch/worktree -> tests -> local commit -> whitelist deployment package -> hash/resource preflight -> server deploy -> sanitized status`；审阅为 `local docs/static summaries + sanitized server status -> wq-review`。

## 依赖

本地 Git、GitHub CLI、Python 标准库、Docker Compose、服务器 shell guard；wqa/wqb/wqc 仅作为独立上游/交换仓库。

## 安全边界

2C2G40G，构建和高负载操作串行。无 remote。`.env`、密钥、数据库、日志、ZIP/备份、生产数据不提交也不进入 review。服务器不得反向覆盖本地。wq-review 不参与运行。

## 当前实现

现有 guard/deploy/local 脚本存在；安全的 wq-review 生成器已落地。独立 wqb/wqc 已在 `wqmining` 外建立本地 checkout 和 Public 骨架，不作为 nested repo。审计发现的大量根目录历史部署包、数据库、日志和临时脚本仍保留原位并纳入 ignore/risk 清单。服务器当前运行状态为 `UNVERIFIED`。

## 已确认设计

本地单向发布；独立资产仓库按 manifest 交换；生成器白名单、敏感值/密钥/地址/连接串/日志/大文件扫描、失败不发布、稳定 manifest；公开 wq-review 只允许普通 fast-forward push；并行窗口使用 branch/worktree。

## 已知 Bug

- 当前 Git 仅跟踪 32 个文件而工作树约 475 个未跟踪条目，完整源码提交边界尚未治理。
- 根目录遗留传输包/脚本和 `backend/backend/` 重复数据目录。
- 现有部署脚本的端到端回滚与服务器状态格式：`UNVERIFIED`。

## 验证记录

2026-07-18 完成静态仓库/Git 审计；离线治理测试已验证链接、索引、任务 ID、重复目录检测、敏感拦截、manifest 契约、Git checkout 保留和 review 重现性，全部 PASS。wq-review、wqb、wqc 均已匿名读取验证；两个资产骨架各 6 个治理/schema 文件且 manifest 为空。未部署服务器、未调用 WQ。

## 多窗口仓库发布

- 辅助仓库默认仅在线上存在，任务通过 GitHub API 或系统临时目录固定 Commit 稀疏检出。
- `wqa/wqb/wqc` 使用任务分支；`wq-review/main` 由任务板登记的单一发布者生成完整快照并普通 fast-forward 推送。
- 发布结束或失败均清理临时目录；本地长期仅保留 `wqmining`。

## 相关报告

2026-07-22：目标服务器 TCP 22 可连接，但 SSH 协议握手无响应；未能开始部署前检查、清理、备份或迁移，任务按失败关闭原则标记 `BLOCKED`。

- `docs/test_reports/project_governance_and_gpt_codex_bridge_20260718.md`
- `docs/test_reports/auxiliary_repository_concurrency_and_cleanup_20260718.md`
