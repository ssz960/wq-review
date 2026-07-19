# Alpha Mining OS 项目状态

- 更新日期：2026-07-19
- 当前任务：`AI-AUTONOMOUS-BACKEND-V1-20260719-001` 已在独立本地 worktree 完成本地 V1 验收，等待 GPT 后端审阅。
- 当前阶段：Phase 0 至 Stage 7 已通过；唯一 Alembic Head 为 `20260719_0032`，其下游为 `20260718_0031`。
- 安全状态：本任务仅使用本地 Mock/SQLite；不部署、不调用真实 WQ、不调用真实 Provider、不修改前端。测试 Gate 状态只存在于本地 fixture，不改变服务器 Gate。
- 架构前置：`AI-BACKEND-ARCH-AUDIT-20260718-001` 已通过 GPT 审阅（`wq-review@edf17d79632d02e6bc706520c1375783df6d5996`），审计契约与规划作为 V1 实施依据。

## AI-AUTONOMOUS-BACKEND-V1-20260718-001 状态

- 状态：`CANCELLED`；所有权已转移到 `AI-AUTONOMOUS-BACKEND-V1-20260719-001`。
- 基线：从干净恢复提交 `44f3b898` 创建任务分支；API/worker import、唯一 migration head、Registry、CandidatePlan 到 Mock transport、Single/Multi、Result Ingestion、Factor Center、Research Package、Context/Checkpoint、Research Center、Research Memory 与 Exchange V2 离线回归均已通过。
- 基线修复：Exchange V2 的内容哈希不再吸收自身会改变的存储遥测；legacy Factor Center CSV 的曲线只进入本地缓存，不进入主读模型。
- 未验证边界：服务器联调、真实 WQ、真实 Provider、Prod Corr、最终提交和无人值守模式均未验证且未授权。
- 证据：[autonomous_ai_backend_v1_baseline_20260718.md](test_reports/autonomous_ai_backend_v1_baseline_20260718.md)。

## INTEGRATE-LIVE-20260718-001 状态

- 状态：`DONE`，由 `RUNTIME-RECOVER-LIVE-20260718-001` 在原检查点续跑完成。
- 共同基线：`20260718_0029_consultant_core_models`；保留 SCHED `0030`，RESULT 迁移顺延为 `0031`。
- 唯一真实执行状态：`execution_requests + simulation_batches + simulation_batch_children`；Consultant `multi_simulation_*` 仅为领域/兼容投影。
- 结果：48 项组合测试、17 项 Multi Transport、0029→0031 临时数据库迁移与治理测试通过；恢复后的 clean checkout 可导入 API/worker，服务器以 `0031` 运行。首批为 1 个 10-Child Multi 加 2 个 Single，后续为 3 个 10-Child Multi；42 条均完成，未见重复派发、429、活动槽位或结果链缺失。
- 写锁：续跑完成并已释放；Gate 在受控批次结束后关闭。

## RUNTIME-RECOVER-LIVE-20260718-001 状态

- 状态：`DONE`；运行状态为 `LIVE_LOOP_RUNNING`。
- Git 恢复：相对 `724ab02f`，25 个运行时/构建文件进入事实源，核心包括 `schemas.py`、`security.py`、`task_queue.py`、`worker.py`、`agent_control_service.py`、AI/campaign 支持模块、`main.py` 与镜像输入。未复制数据库、日志、缓存、部署包、备份、运行数据或凭据。
- clean checkout：API/worker import、恢复失败关闭/可选路由 5 项、结果闭环 12 项、Result Ingestion 10 项、Allocation+CORE 26 项、Multi replay 17 项和适配器回归通过。当前工作站未安装 Docker CLI；已运行服务器镜像来自已提交恢复源，backend/worker 均健康。
- 真实闭环：Alembic 唯一 Head 为 `20260718_0031`；42 条受控候选全部完成。首批产生 22 Fact/Normalized/Feedback 和 12 个 Factor 投影；30 条批次产生 30 Fact/Normalized/Factor/Feedback。预算 `42/42`、剩余 `0`、429 为 `0`、重复派发为 `0`。
- 收尾：`/api/health` 返回 200，backend、worker、postgres、redis、nginx、frontend、cloudflared 均运行，RQ 默认队列为 0；Gate 已关闭并使派发会话失效。

## RESULT-20260718-001 状态

- 状态：`DONE`，离线实现、回归和治理收口完成。
- 目标：Execution Transport 只产生原始执行结果；Result Ingestion 负责原始事实账本、标准化、幂等写入、Parent/Child 聚合、Checks 与 Correlation 分类；Factor Center 只作读模型；Research Center 生成 Feedback Delta；Research Exchange 只作有界导出；Data Pullback 保持冷数据归档。
- 结果：迁移链单一 head `20260718_0030`；RESULT 10/10、CORE 10/10、Multi Transport 15/15、Research Exchange/Memory/Center 与 Data Pullback 服务层通过。完整证据见 `docs/test_reports/result_ingestion_offline_20260718.md`。
- 写锁：已释放 research_exchange、factor_center、research_center、execution_transport。

## CORE-20260718-004 基线恢复

- 状态：`DONE`。
- 原因：`CORE-20260718-003` 的迁移 `20260718_0029`、核心模型、服务、Schema 与 10 项测试仅存在于隔离分支，尚未进入当前事实源；RESULT 目标文件中另有多项仅存在于未跟踪工作树。
- 范围：在独立临时 worktree 中整合固定 REG/CORE 提交，语义化解决治理冲突，并逐文件恢复可追溯业务源码。禁止批量纳入未跟踪文件。
- 安全：不调用真实 WQ，不创建真实任务，不部署，不修改 API Gate 或前端；恢复验证通过前不启动 `RESULT-20260718-001` 业务开发。
- 结果：固定提交链已语义合并；Alembic 从 `20260606_0001` 到 `20260718_0029` 共 29 个 revision 且单一 head 为 `20260718_0029`。CORE 10 项、Multi 15 项、Research Exchange、Research Memory、Research Center、Research Package/Context 离线回归通过。
- 已知基线缺陷：Data Pullback 服务层通过但完整路由仍依赖未恢复的 `app.main` 控制面；Factor Center 既有 smoke 在 legacy curve 外置缓存断言失败，留给 RESULT 审计，不在恢复任务中改业务语义。

## GOV-20260718-004 状态

- 范围：只审计和删除已经验证的常驻本地辅助 GitHub 检出，并替换为固定 Commit GitHub API 读取或任务范围临时稀疏检出。
- 安全：不调用真实 WQ，不修改前端或业务代码，不修改服务器快照，不执行服务器部署动作。
- 当前结论：`wqa_sync`、`wqa_reg_20260718`、`wqc` 与本地 `wq-review` 因含未提交或仅本地历史而保留为 `UNKNOWN`，自动化不得删除；仅已验证的 `wqb` 与 `wq-review-repeat` 已删除。
- 服务器 Registry 仅保留只读证据；完整的无变更启动/查询/回滚演练必须由明确授权的部署运行任务执行。

## GOV-20260718-005 状态

- 目标：重新发布统一 `wq-review` 快照，建立多窗口 Git 并发规则，并清理除 `wqmining` 外的常驻本地仓库/worktree 目录。
- 策略：辅助资产仓库使用任务分支；`wq-review/main` 使用单发布者、固定远端 Commit、普通 fast-forward 的完整生成快照，不替换 Git 历史。
- 安全：不调用真实 WQ，不修改前端、业务代码、服务器 Snapshot 或数据库。
- 结果：`wq-review` 已由固定本地 Commit 重新生成、扫描、普通 fast-forward 发布并匿名验证；旧窗口文件仅从当前树移除，历史保留。
- 本地：已删除 `wqa` 及其 worktree、`wqc`、`wq-review`、三个已完成 `wqmining_*` worktree 和空的 `wqmining-sync` 目录；`D:\Codex` 下长期仅保留主项目 `wqmining`。

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

## 累计完成资产入口

- [completed_asset_migration_index](archive/completed_asset_migration_index.md) 是 `REG`、`CORE`、`SCHED`、`RESULT`、`RUNTIME-RECOVER`、`TPL`、`consultant_core`、`single_multi_allocation`、allocation decision、capacity policy 和 scheduler recovery 的累计审阅入口。
- 被新模块文档替代的独立设计/验证文档已从固定 Git 提交归档；archive 不得覆盖、删除或作为新实现的写入目标。
- `GOV-20260719-001` 只修复审阅交付治理：重新生成 manifest、白名单、敏感扫描与临时 GitHub workspace 验证；不修改后端业务代码或迁移。

## AI-BACKEND-ARCH-AUDIT-20260718-001 记录

- 范围：只读审计自主 AI 研究员后端现状，固化长期目标架构、差异矩阵、阶段改造计划和 V1 契约。
- 产出：新增 `docs/design/autonomous_ai_research_architecture.md`、`docs/audits/autonomous_ai_backend_gap_analysis_20260718.md`、`docs/plans/autonomous_ai_backend_implementation_plan_20260718.md`、`docs/contracts/autonomous_ai_contracts_v1.md`。
- 结论：现有 Platform Registry、CandidatePlan、Scheduler、Execution Adapter、Result Ingestion、Factor Center、Research Package v1、Context/Checkpoint、Research Memory v2 等可复用；必需新增表只有 5 张：Research Round、Hypothesis、MemoryProposal、Assistance Request 和 Provider Profile。Provider Secret 采用 1 张数据库表或外部 Secret Store 二选一；Campaign Event 优先复用/投影 `MiningEvent`，不新建第二套审计表。
- 风险与前置：正式编码前必须找到包含 `20260718_0031` 的干净事实源 Commit，确认唯一 Alembic Head，并在独立 worktree 开发；找不到时停止编码并报告 source gap。该前置已由 `AI-AUTONOMOUS-BACKEND-V1-20260719-001` Phase 0 以 `ef06fee9` 和单 Head `20260719_0032 -> 20260718_0031` 满足。
- 审阅发布：`GOV-20260719-001` 已建立累计式 `wq-review` 历史入口和“累计历史未被覆盖或删除”核对项；本次 V1 发布继续要求 `PASS`、`FAIL` 或 `BLOCKED`。
- 状态：架构审阅已获 `PASS`；正式 V1 实施已转由 `AI-AUTONOMOUS-BACKEND-V1-20260719-001` 在独立 worktree 执行。

## 下一步边界

`GOV-20260718-001` 完成后不自动启动后续工作。任何业务修复、目录清理、服务器部署或真实 WQ 验证必须另建任务。

## KNOW-20260718-002 记录

- `BeastOrange/wq-forum` 已在本地 clone；74 篇官方 Documents 已入库。
- 因未发现用户原始论坛导出，使用已登录 Chrome 只读建立了 6 条高信号论坛页面的本地脱敏索引；SQLite、原始页面内容和本地 JSON 均未进入公开仓。
- `wqc` 发布 20 条 `PENDING_HUMAN_REVIEW` 结构化候选结论，Manifest 依宪法保持空列表；`wq-review` 仅发布摘要、目录、冲突和待审项。
- 未调用真实 Simulation、未读写凭据/Cookie/Token、未调用提交或账户修改操作；论坛自动化代码只作为禁止直用的反例记录。

## REG-20260718-001 Platform Registry 上游状态

- `wqa` 已在独立 `main` worktree 合并 USA Field 上下文与 43 个多 Region Dataset/Settings Scope，并在 commit `cea82e2119b8a91db818722294ef45d18e3f6a6b` 生成不可变 `REG-20260718-001` Manifest、Hash、Schema、Scope 覆盖矩阵、容量评估和离线同步验证。
- Dataset/Settings 覆盖 USA、GLB、EUR、ASI、CHN、JPN、IND、MEA 共 43 个合法 Scope；Field 权威记录仅 USA/TOP3000/D1 完整，其余 Scope 均明确 `MISSING`，禁止跨 Region 推断。
- 已登录平台只读 UI 验证 8 个 Region、GLB Settings 选项和 85 条 Operator；Operator 字段类型兼容矩阵仍 `UNVERIFIED`，最终组合必须失败关闭。
- 无网络端到端验证通过：Manifest/Hash -> 1,000 行批量 SQLite staging import -> Active Snapshot -> Region/Dataset/Field type/关键词 Top-K -> Operator/Profile 校验；真实 WQ 调用为零。
- 2C/2G/40G 下热 Scope 实测数据库约 39.4 MiB、导入约 5.5 秒、峰值 RSS 约 32.1 MiB、查询中位数约 2 ms；部署结论 `READY_WITH_LIMITS`，未执行部署。

## CORE-20260718-003 Consultant Core

- 新增版本化 Simulation Profile、Submission Policy、Multi Parent/Child、五类 Correlation Observation、Power Pool 历史成员和 SuperAlpha Selection 模型，迁移版本为 `20260718_0029`。
- 统一 Scheduler 本轮仅提供不创建队列的 Mock 边界；现有 Single、Admission、API Gate、优先级和真实 WQ worker 未修改。
- 10 项离线单元测试通过；现有旧运输回归因 Git 事实源缺少 `backend/app/db.py` 无法启动，执行级 Single 回归仍 `UNVERIFIED`。

## AI-AUTONOMOUS-BACKEND-V1-20260719-001 状态

- `AI-BACKEND-ARCH-AUDIT-20260718-001` 已获 GPT PASS，审阅基线为 `wq-review@edf17d79632d02e6bc706520c1375783df6d5996`。
- Phase 0 已通过：固定本地提交 `ef06fee970ec45ccca49ce4278bb94159bd8c5bf` 的祖先 `44f3b89865037fcf60eb29649cd21d31305d9f95` 是运行恢复成果，树中包含 `20260718_0031_result_ingestion_v1.py`、Single/Multi、Result Ingestion 和恢复测试。Alembic 只有一个 Head `20260719_0032`，其 `down_revision` 为 `20260718_0031`；SQLite 升级、降级、再升级演练通过。
- 前一 V1 任务的所有权已转移，旧 worktree 的未提交内容不构成来源。当前任务只在新的独立 worktree 修改已验证的 Git 事实源。
- 后续实现只允许 Feature Flag 默认关闭的本地 Mock/SQLite 路径；仍禁止前端修改、服务器部署、真实 WQ、Prod Corr 和最终 Alpha 提交。
- 额度关系、每日限额、Invalid/Cancelled 计数、Osmosis、Pyramid、奖励、限流数值及相关性阈值继续保持 `UNKNOWN`。
- 实现结果：`886d11ad` 已交付 Round Orchestrator、Registry binding、受信运行时 API、版本化 fixture Skill 与 4 轮/12 条本地 campaign 验收。Stages 1-7、迁移演练、17 项 Multi transport 与 10 项 Result Ingestion 回归均通过。
- 调用边界：真实 WQ、外部 Provider、Prod Corr 和最终 Alpha 提交调用均为 `0`；Self/PP 仅以本地 policy 启用，不调用外部相关性服务。
- 状态：`READY_FOR_GPT_BACKEND_V1_REVIEW`。服务器、真实 Provider、真实 WQ、Prod Corr、最终提交和前端工作仍未验证且未授权。

## SCHED-20260718-001 Single/Multi Allocation

- 已恢复Platform Registry迁移0028与CORE迁移0029，并新增连续迁移`20260718_0030`。
- 实现只读预览、不可变AllocationPlan、SHARED/SEPARATE/UNKNOWN Capacity、持久化预留、Single/Multi统一Mock调度和恢复；复用现有Batch与ExecutionRequest。
- 25项离线测试通过；真实WQ、生产Worker和服务器Gate调用为零。
- Single/Multi额度关系、最大Multi Parent并发、每日额度及Invalid/Cancelled计数仍为UNKNOWN。
