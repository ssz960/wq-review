# CORE-20260718-003 脱敏审阅摘要

- 在Platform Registry快照边界之后新增版本化Simulation Profile与Submission Policy，Policy按Region、Delay、Universe、Alpha Type和版本解析。
- Multi采用独立Parent/Child核心模型，Parent最多10个Child；Instrument Type、Region、Delay、Language为批内共享不变量，Parent状态由Child确定性聚合。
- 新增五类互不覆盖的Correlation Observation：本地PnL、本地结构、官方Self、Power Pool和官方Prod。
- Power Pool保留历史成员关系，当前标签与历史相关性池资格分离；SuperAlpha分别记录请求、合格、有效及截断数量和原因。
- 统一Scheduler仅实现Mock审计边界，Pending、429、Timeout和Unknown为显式状态；没有创建真实队列或调用WorldQuant。
- 10项离线测试通过。现有Single模型、Admission、API Gate、队列优先级与真实Adapter未修改；旧运输脚本因Git事实源缺少既有`app.db`文件而未能执行。
- Single/Multi额度关系、每日限额、Invalid/Cancelled计数、Osmosis、Pyramid、奖励、具体限流数值及相关性阈值均保持UNKNOWN。

本摘要不包含凭据、地址、生产日志、完整源码、真实回测或私有研究结果。
