# REG-20260718-002 脱敏审阅摘要

- wqa发布Commit：`cea82e2119b8a91db818722294ef45d18e3f6a6b`
- 本地运行库Active Snapshot：`REG-20260718-001`
- 资产来源Commit：`8548481557fbf51b970a7a22f988bad19f1f7732`
- 本地验证：85,612 fields、6,509 dataset scopes、85 operators、483 profiles；首次同步约2.469秒，数据库约45.1 MiB，峰值RSS约32.2 MiB。
- 校验覆盖：active指针与Manifest Hash、字节数、分批导入、重复同步幂等、失败清理、USA Top-K查询、Operator存在性、合法Profile和非USA Field fail-closed。
- Operator字段类型兼容性保留为`UNVERIFIED`，没有从USA字段推断其他Region；AI仅获得只读结果和离线CandidatePlan元数据。
- 旧`field_registry`仍是兼容入口，但部署契约禁止其继续作为独立字段真相；应转读Active Snapshot。
- 服务器只读预检约990 MiB可用内存、10.3 GiB可用磁盘，但旧WorldQuant worker和接收器均active。未停止、重启或并行启动任何服务，因此服务器激活状态为`BLOCKED`。

本摘要不包含凭据、地址、Cookie、生产日志、完整源码或私有研究结果。
