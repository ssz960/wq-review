# ai_mode

## 目标职责

为研究会话构造受限上下文，调用 AI provider，校验结构化输出，生成可审计的建议/候选计划；AI 不能直接执行或扩大权限。

## 入口文件

- `backend/app/mining/agent_runtime_v1.py`
- `backend/app/mining/agent_runtime_orchestrator.py`
- `backend/app/mining/agent_control_service.py`
- `backend/app/mining/agent_campaign_continuous.py`
- `backend/app/mining/supervisor_llm.py`
- `frontend/src/pages/AgentAiMode.jsx`（只读审计）

## 模型

`AgentSession`、`WorkingState`、`ConversationCheckpoint`、`ResearchDecision`、`Experiment`、`CandidatePlan`、`SkillInvocation`，以及 `LlmSupervisor*`、`LlmCallRecord`。

## API

`/api/agent-sessions*`、`/api/research-campaigns*`、dialogue/intent/context/workspace 路由，以及 `/api/mining/llm-supervisor/*`。

## 流程

`runtime context -> provider call -> parse/schema/permission validation -> checkpoint/decision -> candidate plan -> deterministic admission/materialization`。Supervisor 路径另有 `checkpoint -> decision -> reconcile/outcome`。

## 依赖

Provider、Research Center/Memory、Task Center、Platform Registry、数据库 lease/checkpoint 和策略/权限校验。

## 安全边界

AI 仅建议与生成候选；禁止直接写 execution request、开启真实 gate、改变预算或绕过人工确认。provider 输出不可信，必须 schema 与权限校验；失败应回退或关闭。

## 当前实现

Agent runtime 已有输入 hash、lease、checkpoint、结构化校验和 mock provider；Supervisor 有另一套 Pydantic envelope 与 OpenAI-compatible provider。两套体系的最终统一关系为 `UNVERIFIED`。

2026-07-18 架构审计将后续目标固定为：AI 只能提出 CandidateProposal、Skill 调用和 NextMoveDecision；正式执行仍必须通过 CandidatePlan materializer、Platform Registry hard admission、共享 Scheduler、Execution Adapter 和 Result Ingestion。Research Round、Policy Engine、Context Builder V2、MemoryProposal、Assistance Request、Provider Profile/Secret 均为待 GPT 审阅后的改造项，不属于已完成实现。

## 已确认设计

AI 输出与确定性执行分层；候选计划是中间边界；所有 AI 调用应可审计和脱敏。

## 已知 Bug

- Agent runtime 与 LLM supervisor 语义/模型重复。
- 连续 campaign 在 `tasks.py` 中有确定性 fallback，fallback 是否符合所有研究策略：`UNVERIFIED`。
- 历史审计指出完整 prompt/input 可能持久化；当前全路径脱敏状态：`UNVERIFIED`。

## 验证记录

2026-07-18 静态审计类、函数、路由和模型；未调用任何 provider。

2026-07-18 `RUNTIME-RECOVER-LIVE-20260718-001` 将 Agent/campaign API 的实际 import 依赖纳入 Git，并在 clean checkout 验证核心缺失失败关闭、非核心路由可选降级。候选仍须经 CandidatePlan、硬准入和确定性 Allocation 后才可进入执行；本任务没有让 AI 直接开启 Gate、扩张预算或绕过调度。

## 相关报告

- `docs/test_reports/project_governance_and_gpt_codex_bridge_20260718.md`
- `docs/test_reports/autonomous_ai_backend_architecture_audit_20260718.md`

## 相关长期文档

- `docs/design/autonomous_ai_research_architecture.md`
- `docs/audits/autonomous_ai_backend_gap_analysis_20260718.md`
- `docs/plans/autonomous_ai_backend_implementation_plan_20260718.md`
- `docs/contracts/autonomous_ai_contracts_v1.md`

## AI-AUTONOMOUS-BACKEND-V1-20260719-001

V1 adds `autonomous_v1_orchestrator.py` and two `/api/v2/autonomous` round endpoints. The orchestrator uses a per-round lease, Context V2, versioned Skill invocation, CandidatePlan staging/materialization, the existing shared mock dispatcher, and existing Result Ingestion facts before recording a NextMove decision. Client runtime flags are ignored; trusted runtime facts come from server configuration and Task Center control state.

The V1 acceptance path is `CAMPAIGN_AUTOMATIC` with feature flags enabled only in a local test process. It cannot invoke a real provider, real WorldQuant, Prod Corr, final submission, or frontend workflow. Agent runtime and Supervisor legacy unification remains a future compatibility boundary; V1 does not grant either path direct execution authority.
