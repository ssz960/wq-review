# provider_and_skills

## 目标职责

统一 AI provider 配置、故障切换、调用边界和技能调用审计，为 AI Mode/Supervisor 提供受控能力；不直接拥有候选执行权限。

## 入口文件

- `backend/app/config.py`
- `backend/app/mining/agent_runtime_provider.py`
- `backend/app/mining/supervisor_llm.py`
- `backend/app/mining/runtime_context_builder.py`
- `backend/app/mining/agent_materializer.py`
- `backend/app/mining/provider_preflight.py`
- `backend/app/mining/autonomous_v1_api.py`
- `backend/app/mining/plugins/`、`backend/app/mining/registry.py`

## 模型

`LlmCallRecord`、`SkillInvocation`、`SystemSetting`；provider 的请求/响应 envelope 分别在 Agent Runtime 与 Supervisor 文件中定义。预检专用 `ProviderPreflightBudget` 和 `ProviderPreflightAudit` 只保存 hash、长度、状态、Provider request ID、耗时、Token、重试和安全结果元数据。

## API

Provider 旧入口通过 `/api/mining/llm-supervisor/*`、agent dialogue/campaign 和 task worker 间接调用。Autonomous V1 只读 `/api/v2/autonomous/providers` 只返回 masked/configured/validated/status；`/api/v2/autonomous/provider-preflight/run` 仅接受受限 purpose 和幂等键，且要求服务器令牌。`/api/mining/plugins` 是策略插件展示，不等于 AI 技能注册表。

## 流程

`config/provider pool -> sanitized bounded input -> provider failover -> parse/schema validation -> permission validation -> audit -> downstream deterministic service`。

本任务唯一权威预检链为 `Provider Profile -> Secret env reference -> bounded context -> HTTP -> parser -> JSON Schema -> permission rejection -> sanitized audit -> Research Round/Proposal dry run`。它不调用 `LlmCallRecord`、Agent Runtime、Supervisor pool、执行适配器或 WQ。

## 依赖

配置/环境变量、OpenAI-compatible HTTP provider、AI Mode、Research Center、Task Center、plugin registry 与数据库。

## 安全边界

凭据只来自进程/服务器环境，禁止进入配置样例以外的仓库内容、日志、prompt、错误或 wq-review。provider/skill 输出均不可信；调用不能开启 WQ gate、部署、修改预算或绕过人工。

## 当前实现

OpenAI-compatible Agent provider、Supervisor provider、provider pool/failover 配置、mock provider、策略 plugin registry 和 `SkillInvocation` 模型存在。统一技能 manifest、capability contract、全路径 secret redaction 与预算实现为 `UNVERIFIED`。

预检只接受单个 enabled/validated OpenAI-compatible Profile、HTTPS endpoint 和 `env:NAME` Secret reference；不读取密文列。401/403 不重试，429/5xx 最多重试两次，JSON 修复最多一次，总物理 Provider 调用上限十次、并发一。服务器尚无法连接，因此真实 Profile、模型和凭据没有读取或声称已验证。

## 已确认设计

Provider 是可替换依赖；技能调用需要显式权限与审计；GPT 公开审阅通过 wq-review 摘要而非直接连接生产 provider。

## 已知 Bug

- Agent/Supervisor provider 两套 envelope 与重试逻辑可能重复。
- plugin registry 与 SkillInvocation 的关系未形成单一注册事实源。
- 历史 probe/real-provider smoke 文件位于业务目录，误运行和凭据泄露风险。

## 验证记录

2026-07-18 静态检查配置入口、provider 类、插件目录和模型；wq-review 与 wqc 骨架扫描确认密钥格式、Cookie/Token、地址/域名和连接串会被阻断，且无 provider/WQ 网络调用路径；未读取环境密钥、未调用 provider。

2026-07-18 `KNOW-20260718-002`：论坛 RAG 与研究记忆只作为带来源定位的只读审阅输入。社区 MCP、凭据处理、直接 API、自动提交和轮询示例均未采用；任何未来真实调用仍受人工批准、API Gate、共享调度、适配器和审计约束。
2026-07-18 `KNOW-20260718-003`：外部相关性或回测系统代码只提取为队列、缓存、限流、状态机、错误分类和结果物化模式。任何直接会话、接口、自动执行、重试循环或凭据逻辑均为 non-adoptable，未来连接器仍需独立批准与 Gate 审计。
2026-07-18 `KNOW-20260718-004`：进程中断后不得从本地退出推断平台槽位已释放。未来恢复只允许经批准的只读对账适配器将状态从 `UNKNOWN` 物化；直接取消、删除或轮询代码仍不具备采用资格。

## 相关报告

2026-07-22：Provider Profile/Secret 采用数据库表方案；`/api/v2/autonomous/providers` 只返回 masked/configured/validated/status 等非秘密字段。预检本地 Stub 覆盖成功、401、403、429、500、超时、断网、空/非 JSON、缺字段、超长、恶意权限字段、重复回调和重启重放；服务器 SSH banner 阻断使真实 Provider 请求仍为零。

- `docs/test_reports/project_governance_and_gpt_codex_bridge_20260718.md`
- `docs/test_reports/real_provider_preflight_v1_20260722.md`

## AI-PROVIDER-MOCK-E2E-V1-20260722-002

The deployed Provider path used one bounded entry point with environment
secret-reference resolution, sanitized audit fields, trace continuity, and
strict output validation. Ten serialized physical Provider calls were the
maximum allowed; the Provider returned repeated empty/non-JSON content before
a valid Proposal, so the task is BLOCKED and no Mock execution was attempted.
No Secret, prompt, full response, address, or authentication header entered
the evidence package. The current report is
`docs/test_reports/provider_generated_mock_e2e_v1_20260722.md`.
