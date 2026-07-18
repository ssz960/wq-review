# frontend_shell

## 目标职责

提供 React/Vite 操作界面、路由壳、统一 API client 与各中心页面；前端只展示/发起受控后端操作，不保存秘密或执行业务核心规则。

## 入口文件

- `frontend/src/main.jsx`
- `frontend/src/App.jsx`
- `frontend/src/api.js`
- `frontend/src/pages/`
- `frontend/src/styles.css`

## 模型

没有持久化数据库模型。页面使用后端 JSON shape 和组件本地状态；权威 schema 位于后端 `backend/app/schemas.py`。前端类型契约为 `UNVERIFIED`（项目为 JSX，未发现独立生成类型层）。

## API

通过 `frontend/src/api.js` 消费 `/api`；页面覆盖 Login、Dashboard、TaskCenter、AlphaBoard、FieldResearch、Settings、Run/Observation、Factor、Template、Research、Agent AI 等。

## 流程

`route -> page/component -> api.js -> backend API -> render state/error`；所有真实状态应来自后端，不由定时器或 UI 推断。

## 依赖

React、Vite、Ant Design（由 package 文件确认）、后端 API 和 Nginx/Vite runtime。

## 安全边界

前端不得接收/持久化 WQ 或 provider 凭据，不得直接连接 WQ，不得自行判定准入或打开执行 gate。错误展示不得泄漏 traceback、原始响应或配置。

## 当前实现

页面和 task-center 子组件均存在；同时存在 `dist/`、`node_modules/` 和多份日志。运行时可用性、页面与所有后端 shape 的一致性为 `UNVERIFIED`。本治理任务未修改 `frontend/`。

## 已确认设计

API client 集中、后端为状态事实源、前端是操作壳。本任务的所有前端内容仅来自文件树和静态引用。

## 已知 Bug

- 根目录有旧 `frontend_agent_api_map.md`，未纳入权威治理。
- 历史 reports 含大量 UI 截图与 DOM/log，不能进入 wq-review。
- 旧模块文档存在乱码，不能作为当前 UI 验收证据。

## 验证记录

2026-07-18 只读枚举入口、页面、组件和 package 文件；未构建、未启动、未改前端。

## 相关报告

- `docs/test_reports/project_governance_and_gpt_codex_bridge_20260718.md`
