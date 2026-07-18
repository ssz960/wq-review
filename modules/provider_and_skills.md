# provider_and_skills

## 目标职责

统一 AI provider 配置、故障切换、调用边界和技能调用审计，为 AI Mode/Supervisor 提供受控能力；不直接拥有候选执行权限。

## 入口文件

- `backend/app/config.py`
- `backend/app/mining/agent_runtime_provider.py`
- `backend/app/mining/supervisor_llm.py`
- `backend/app/mining/runtime_context_builder.py`
- `backend/app/mining/agent_materializer.py`
- `backend/app/mining/plugins/`、`backend/app/mining/registry.py`

## 模型

`LlmCallRecord`、`SkillInvocation`、`SystemSetting`；provider 的请求/响应 envelope 分别在 Agent Runtime 与 Supervisor 文件中定义。

## API

Provider 没有独立公开 API；通过 `/api/mining/llm-supervisor/*`、agent dialogue/campaign 和 task worker 间接调用。`/api/mining/plugins` 是策略插件展示，不等于 AI 技能注册表。

## 流程

`config/provider pool -> sanitized bounded input -> provider failover -> parse/schema validation -> permission validation -> audit -> downstream deterministic service`。

## 依赖

配置/环境变量、OpenAI-compatible HTTP provider、AI Mode、Research Center、Task Center、plugin registry 与数据库。

## 安全边界

凭据只来自进程/服务器环境，禁止进入配置样例以外的仓库内容、日志、prompt、错误或 wq-review。provider/skill 输出均不可信；调用不能开启 WQ gate、部署、修改预算或绕过人工。

## 当前实现

OpenAI-compatible Agent provider、Supervisor provider、provider pool/failover 配置、mock provider、策略 plugin registry 和 `SkillInvocation` 模型存在。统一技能 manifest、capability contract、全路径 secret redaction 与预算实现为 `UNVERIFIED`。

## 已确认设计

Provider 是可替换依赖；技能调用需要显式权限与审计；GPT 公开审阅通过 wq-review 摘要而非直接连接生产 provider。

## 已知 Bug

- Agent/Supervisor provider 两套 envelope 与重试逻辑可能重复。
- plugin registry 与 SkillInvocation 的关系未形成单一注册事实源。
- 历史 probe/real-provider smoke 文件位于业务目录，误运行和凭据泄露风险。

## 验证记录

2026-07-18 静态检查配置入口、provider 类、插件目录和模型；wq-review 自测确认无 provider/WQ 网络调用路径；未读取环境密钥、未调用 provider。

## 相关报告

- `docs/test_reports/project_governance_and_gpt_codex_bridge_20260718.md`
