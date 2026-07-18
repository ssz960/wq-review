# template_center

## 目标职责

管理表达式模板的抽取、草稿/启停、来源记录和测试展开，为候选生成提供受控模板，不直接执行。

## 入口文件

- `backend/app/template_library.py`
- `backend/app/mining/fixtures/template_library_v1_expressions.json`
- `backend/app/main.py`
- `frontend/src/pages/TemplateLibrary.jsx`（只读审计）

## 模型

`TemplateLibraryRecord` 定义于 `backend/app/models.py`；迁移包括 `20260611_0012_template_library_v1.py`。

## API

`/api/template-library/extract`、`/import-from-alpha`、`/templates*`、`/research-assistant`、`/{template_id}/activate|deactivate|test-expand`。

## 流程

`source text/alpha -> extraction preview -> explicit create/import -> draft/active lifecycle -> bounded test-expand -> candidate consumer`。

Governed external flow: `wqc PENDING_HUMAN_REVIEW -> APPROVED_FOR_IMPORT -> Template Center DRAFT -> REVIEWED -> TESTING -> ACTIVE/PAUSED/RETIRED -> sanitized asset export -> fixed four-file Research Package`. External repositories are not runtime template libraries.

## 依赖

数据库、schemas、字段/Operator 信息、Task Center 和研究策略；论坛 distill 产物是外部输入而非权威代码。

## 安全边界

抽取与展开不等于准入或执行；模板必须经状态、字段/operator 和候选硬准入校验。原始论坛内容、私有表达式和生产结果不得进入 wq-review。

## 当前实现

抽取 service、CRUD、启停、展开、研究助手路由和 fixture 存在。模板质量、当前 active 数据和与 Platform Registry operator 的一致性为 `UNVERIFIED`。

## 已确认设计

模板生命周期与执行分离；前端通过后端 API 管理；active 只表示可被选择，不表示可执行或有效。

Version identity is `template_id + version + content_hash`. Each version records provenance, evidence, Registry Snapshot, field roles/types, Operators, Profiles, risk, and validation. Registry epoch changes and unavailable types/operators fail selection closed. Asset Export produces one JSONL snapshot, three CSV summaries, and a hashed manifest; Research Package keeps exactly its existing four root files.

## 已知 Bug

- 代码名称仍为 `template_library`，治理模块名为 `template_center`，需避免创建重复实现。
- `docs/template_distills/` 含大量生成想法/JSON，未纳入当前模块事实源。
- 旧 Field Registry 与 Platform Registry 的校验来源选择：`UNVERIFIED`。

## 验证记录

2026-07-18 静态检查入口、模型、路由和 fixture；未执行模板导入/展开。

## 相关报告

- `docs/test_reports/project_governance_and_gpt_codex_bridge_20260718.md`
