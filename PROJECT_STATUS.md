# Alpha Mining OS 项目状态

- 更新日期：2026-07-18
- 当前治理任务：`KNOW-20260718-002`
- 当前阶段：正在只读采集 Consultant 机制与系统工程证据，准备供人工审阅的脱敏 wqc 候选资产及 wq-review 摘要；Registry、Multi、AI 循环等业务功能仍冻结。
- 安全状态：本任务未调用真实 WQ，未修改前端或业务代码。

## 实际架构清单

| 区域 | 主要入口 | 当前事实 |
| --- | --- | --- |
| API | `backend/app/main.py` | 单一 FastAPI 应用，路由直接集中注册；API 面较大。 |
| 后台任务 | `backend/app/tasks.py`、`backend/app/worker.py`、`backend/app/task_queue.py` | RQ/同步回退入口与研究循环任务并存。 |
| 挖掘域 | `backend/app/mining/` | Registry、执行适配、调度、AI、研究、交换等实现均在此。 |
| 数据 | `backend/app/models.py`、`backend/app/db.py`、`backend/alembic/versions/` | 模型集中于单文件；当前发现 27 个迁移文件。 |
| 前端 | `frontend/src/App.jsx`、`frontend/src/api.js`、`frontend/src/pages/` | React/Vite 壳与各中心页面；本任务只读审计。 |
| 部署 | `docker-compose.yml`、`backend/Dockerfile`、`frontend/Dockerfile`、`nginx/`、`scripts/` | 本地脚本、服务器 guard 与历史传输脚本混杂。 |
| 文档 | `docs/` | 历史 archive/design/audits、研究资产、测试报告很多；治理入口此前缺失。 |

## 审计发现

### 主要模块入口

模块权威清单见 [MODULE_INDEX.md](MODULE_INDEX.md)。当前代码入口以 `backend/app/main.py` 为 API 汇聚点，以 `backend/app/models.py` 为模型汇聚点，以 `backend/app/mining/service.py` 为任务中心、调度和执行服务的高耦合汇聚点。

### 重复实现与重复目录

- `backend/app/mining/registry.py` 是策略插件 registry，`backend/app/mining/platform_registry.py` 是平台数据快照；名称相近但职责不同，容易误用。
- `backend/app/field_registry.py` 与 `backend/app/mining/platform_registry.py` 都覆盖字段/数据集概念，存在旧 Field Registry 与新 Platform Registry 并行的接口风险。
- `backend/app/mining/agent_runtime_v1.py`、`agent_runtime_orchestrator.py`、`agent_runtime_provider.py` 与 `supervisor_llm.py`/`service.py` 内 LLM supervisor 形成两套 AI 调度语义，整合状态未验证。
- `backend/app/mining/research_package_service.py`、`research_package_export_service.py`、`research_exchange_v2.py` 与 `backend/app/data_pullback.py` 都涉及打包/导出，边界部分重叠。
- `backend/backend/` 只含重复嵌套的数据库与 runtime 数据，不是第二后端；应视为目录风险，未在本任务移动或删除。
- `agent_domain_module1_review/` 复制迁移和代码审阅材料，其中两份迁移与正式目录字节相同；属于孤立审阅副本。
- 根目录 `ai memory/AGENTS.md` 是第二份非权威 AGENTS；同目录还含旧状态/任务文档，是提示词与治理冲突风险。

### 孤立文件与临时资产

- 根目录存在多个 `.tar.gz` 部署包、`.patch`、临时请求 JSON、SQL、SFTP 文本、`sandbox_test.txt` 和独立 JS 工作流；未发现被当前主入口统一引用。
- `.playwright-*`、`.pressure-venv/`、`__pycache__/`、前端/后端日志、`runtime_reports/` 和多个数据库属于运行或测试残留。
- `frontend_agent_api_map.md`、`implementation_report.md` 位于根目录，绕过统一 docs 治理。
- `autonomous_research_workflow.js` 与 `server_workflow.js` 位于根目录，和 `scripts/` 放置规则冲突。

### 过期文档

- `docs/archive/` 明确为 2026-06 历史状态，不代表当前实现。
- 原 `docs/modules/*.md` 含 Phase 1、重复部署步骤、编码损坏文本及具体服务器地址示例，已合并替换为统一模块文档。
- `docs/design/` 与 `docs/audits/` 的 2026-07-17 服务器常驻方案仍可作历史设计输入，但未经当前代码逐项验证，不是状态事实源。
- 大量 `docs/test_reports/` 记录真实运行、截图、CSV、JSON、ZIP 和日志；只能作为历史证据，不能整体进入 wq-review。

### 目录与版本风险

- Git 当前有 15 个提交但仅 32 个已跟踪文件，审计时约 475 个未跟踪条目；绝大多数现有代码不在当前提交边界内。
- 当前分支为 `phase3/research-exchange`，没有 remote；符合“不上传 GitHub”，但不能把当前 HEAD 误认为完整仓库快照。
- `backend/` 审计体积约 2 GB，主要来自数据库/运行数据；`docs/test_reports/` 同时承载报告与大附件，存在磁盘、泄露和审阅污染风险。
- Alembic 文件编号连续到 0027，但部分迁移过去由不同阶段/分支加入；升级路径应在独立业务任务中验证。

## 当前结论

- 治理事实源已统一到四份根文档和 11 份模块文档。
- 本地仓库是唯一源码事实源；服务器与 wq-review 均不得反向写回。
- 当前业务实现不因本任务获得生产可用或真实 WQ 授权；模块中无法由静态代码确认的内容标为 `UNVERIFIED`。
- 离线治理验收已通过：16 个治理/模块文档链接无缺失，11 个模块无孤儿，任务 ID 唯一，重复目录风险被检测并记录，敏感内容被阻断，review 重复生成且 manifest 稳定，业务/前端 diff 与真实 WQ 调用均为零。
- 公开 `wq-review` 已匿名验证可读；仅含 24 个白名单 Markdown/JSON 文件，Pages/Wiki/Issues/Discussions 均关闭且无 Release。公开仓库不是源码、服务器同步或生产事实源。
- 独立 `wqb` 与 `wqc` 已建立 Public `main` 骨架：各含 6 个治理/schema 文件、空 manifest、0 个业务条目和 0 个 Release；匿名读取、schema、白名单、链接与敏感信息扫描均通过。

## 下一步边界

`GOV-20260718-001` 完成后不自动启动后续工作。任何业务修复、目录清理、服务器部署或真实 WQ 验证必须另建任务。

## REG-20260718-001 Platform Registry 上游状态

- `wqa` 已在独立 `main` worktree 合并 USA Field 上下文与 43 个多 Region Dataset/Settings Scope，并在 commit `cea82e2119b8a91db818722294ef45d18e3f6a6b` 生成不可变 `REG-20260718-001` Manifest、Hash、Schema、Scope 覆盖矩阵、容量评估和离线同步验证。
- Dataset/Settings 覆盖 USA、GLB、EUR、ASI、CHN、JPN、IND、MEA 共 43 个合法 Scope；Field 权威记录仅 USA/TOP3000/D1 完整，其余 Scope 均明确 `MISSING`，禁止跨 Region 推断。
- 已登录平台只读 UI 验证 8 个 Region、GLB Settings 选项和 85 条 Operator；Operator 字段类型兼容矩阵仍 `UNVERIFIED`，最终组合必须失败关闭。
- 无网络端到端验证通过：Manifest/Hash -> 1,000 行批量 SQLite staging import -> Active Snapshot -> Region/Dataset/Field type/关键词 Top-K -> Operator/Profile 校验；真实 WQ 调用为零。
- 2C/2G/40G 下热 Scope 实测数据库约 39.4 MiB、导入约 5.5 秒、峰值 RSS 约 32.1 MiB、查询中位数约 2 ms；部署结论 `READY_WITH_LIMITS`，未执行部署。
