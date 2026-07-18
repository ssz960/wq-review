# 当前审阅请求

# TPL-20260718-001 GPT 展示验收

请只按本页的脱敏业务逻辑、表格样例和已记录的离线测试证据验收，不要求审查完整源码，不推断真实收益，不触发 WorldQuant、回测或 Alpha 提交。

## 1. 晋升链

`wqc candidate -> PENDING_HUMAN_REVIEW -> APPROVED_FOR_IMPORT -> Template Center DRAFT -> REVIEWED -> TESTING -> ACTIVE/PAUSED/RETIRED -> sanitized Template Asset Export -> Research Package`

验收点：Template Center 是程序和 AI 唯一运行事实源；wqc、wqb、wq-review 都不是运行模板库。ACTIVE 只表示 AI 可选择，展开结果仍须经过当前 Registry、硬准入、去重和相关性预筛。

## 2. 生命周期验收表

| 当前状态 | 允许下一状态 | 人工/程序条件 | 预期 |
| --- | --- | --- | --- |
| PENDING_HUMAN_REVIEW | APPROVED_FOR_IMPORT | 人工明确批准 | 未批准不得导入 |
| APPROVED_FOR_IMPORT | DRAFT | 幂等键和 Registry 条件通过 | 创建 Template Center 草稿 |
| DRAFT | REVIEWED / RETIRED | 完成逻辑和来源审阅 | 不可被 AI 选择 |
| REVIEWED | TESTING / RETIRED | 本地验证计划完整 | 不自动回测 |
| TESTING | ACTIVE / PAUSED / RETIRED | 离线验证状态合格 | ACTIVE 仍非执行准入 |
| ACTIVE | PAUSED / RETIRED | 人工或确定性风险规则 | 暂停后立即不可选择 |
| PAUSED | TESTING / ACTIVE / RETIRED | 重新验证 | 不保留旁路 |
| RETIRED | 无 | 终态 | 历史可审计，不再选择 |

## 3. wqc 候选样例

| 字段 | 脱敏示例 |
| --- | --- |
| template_id / version | public.rank.signal / 1 |
| status | PENDING_HUMAN_REVIEW |
| skeleton | rank({signal}) |
| economic_logic | 横截面相对强弱 |
| field_roles | signal = ranking_input |
| allowed_data_types | MATRIX |
| required_operators | rank |
| applicable_scopes | USA/TOP3000/D1 |
| evidence_level | PUBLIC_DOCUMENTED |
| risk | medium |
| validation | Registry、类型、Operator、Admission、去重、相关性预筛 |

样例不得包含用户 Alpha、已提交表达式、完整私有公式、PnL、凭据、Cookie、Token 或生产接口代码。

## 4. Template Center 导入样例

| 输入/记录 | 示例 | 验收条件 |
| --- | --- | --- |
| 幂等身份 | public.rank.signal + 1 + SHA-256 | 完全重复返回原记录，不新增 |
| 新版本 | public.rank.signal + 2 + 新 Hash | 新建独立 DRAFT 版本 |
| Registry Snapshot | reg-epoch-20260718 | 当前 Snapshot 不一致则失败关闭 |
| required_operators | [rank] | Registry 缺少 rank 则拒绝 |
| allowed_data_types | [MATRIX] | 字段类型不匹配则拒绝 |
| validation_status | REGISTRY_VALIDATED | 不代表执行或收益有效 |

## 5. Template Asset Export 样例

| 文件 | 内容 | 必需元数据 |
| --- | --- | --- |
| template_library_snapshot.jsonl | 版本化脱敏模板记录 | template_id、version、Hash、状态、来源、证据、风险 |
| template_performance_summary.csv | usage/pass/fail/invalid/high_corr 聚合 | 不含完整 PnL |
| template_field_role_map.csv | 字段角色和允许类型 | 不含真实私有字段值 |
| template_failure_patterns.csv | fail/invalid/high_corr 计数 | 仅统计摘要 |
| manifest.json | 文件 Hash 清单 | database epoch、Registry Snapshot、export time、Hash |

## 6. Research Package 根目录验收

根目录必须且只能有：`manifest.json`、`brief.json`、`seed_pack.csv`、`operator_probe.csv`。

`seed_pack.csv` 示例列：

| template_id | version | skeleton | economic_logic | field_roles | evidence_level | validation_status | risk | source_refs |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| public.rank.signal | 1 | rank({signal}) | 横截面相对强弱 | signal:ranking_input | PUBLIC_DOCUMENTED | REGISTRY_VALIDATED | medium | generic-public-operator-pattern |

## 7. 离线测试验收

| 场景 | 预期结果 |
| --- | --- |
| 采集候选 | 强制 PENDING_HUMAN_REVIEW |
| 未经人工批准导入 | 拒绝 |
| 重复导入 | 幂等，不新增 |
| 版本升级 | 新建独立版本 |
| Registry 失效 | 选择失败关闭 |
| 字段类型不匹配 | 拒绝 |
| Operator 不可用 | 拒绝 |
| ACTIVE 暂停/disabled | 不可选择 |
| 历史性能更新 | 导出统计随记录更新 |
| GPT 快照导出 | 五文件资产快照 + 固定四文件 Research Package |

已记录结果：5 个离线测试函数覆盖上述 10 个场景，全部 PASS；Python 编译、JSON Schema、文件大小扫描和 git diff 检查 PASS。请输出逐节 PASS/FAIL、发现的逻辑缺口，以及是否满足“没有第二套运行模板库”和“无真实 WQ 行为”。
