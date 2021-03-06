<!-- TEMPLATE FILE - DO NOT ADD METADATA -->

## <a name="policy-statements"></a>策略语句

以下策略语句可建立缓解定义的风险所需的要求。 这些策略为治理 MVP 定义功能要求。 每个语句通过治理 MVP 的实现进行表示。

部署加速：

- 必须根据定义的分组和标记策略对所有资产进行分组和标记。
- 所有资产都必须使用已批准的部署模型。
- 为云提供程序建立了治理基础之后，任何部署工具都必须与治理团队定义的工具兼容。

标识基线：

- 应使用由当前治理策略批准的标识和角色来控制部署到云的所有资产。
- 本地 Active Directory 基础结构中具有提升的特权的所有组都应映射到已批准的 RBAC 角色。

安全基线：

- 部署到云的任何资产都必须具有已批准的数据分类。
- 在可以批准并实现足够的安全和治理要求之前，无法将任何使用受保护数据级别标识的资产部署到云。
- 在可以验证和控制最小网络安全要求之前，云环境都被视为非管制区域，应符合与其他数据中心或内部网络类似的连接要求。

成本管理：

- 为进行跟踪，所有资产都必须分配给一个核心业务功能中的应用程序所有者。
- 当成本问题出现时，将与财务团队一起建立其他治理要求。

资源一致性：

- 由于在此阶段不部署任何任务关键型工作，因此无需控制任何 SLA、性能或 BCDR 要求。
- 部署任何关键型工作负荷时，将随 IT 操作一起建立其他治理要求。

## <a name="processes"></a>进程

没有为这些治理策略的持续监视和执行分配任何预算。 因此，云治理团队有一些临时方法来监视是否遵从策略语句。

- **教育**：云治理团队花费时间在支持这些策略的治理过程方面对云采用团队进行培训。
- **部署评审**：在部署任何资产之前，云治理团队会与云采用团队一起评审治理过程。
