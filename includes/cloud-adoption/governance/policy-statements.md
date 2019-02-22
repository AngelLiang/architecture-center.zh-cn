<!-- TEMPLATE FILE - DO NOT ADD METADATA -->

## <a name="policy-statements"></a><span data-ttu-id="0546b-101">策略语句</span><span class="sxs-lookup"><span data-stu-id="0546b-101">Policy statements</span></span>

<span data-ttu-id="0546b-102">以下策略语句可建立缓解定义的风险所需的要求。</span><span class="sxs-lookup"><span data-stu-id="0546b-102">The following policy statements establish the requirements needed to mitigate the defined risks.</span></span> <span data-ttu-id="0546b-103">这些策略为治理 MVP 定义功能要求。</span><span class="sxs-lookup"><span data-stu-id="0546b-103">These policies define the functional requirements for the governance MVP.</span></span> <span data-ttu-id="0546b-104">每个语句通过治理 MVP 的实现进行表示。</span><span class="sxs-lookup"><span data-stu-id="0546b-104">Each will be represented in the implementation of the governance MVP.</span></span>

<span data-ttu-id="0546b-105">部署加速：</span><span class="sxs-lookup"><span data-stu-id="0546b-105">Deployment Acceleration:</span></span>

- <span data-ttu-id="0546b-106">必须根据定义的分组和标记策略对所有资产进行分组和标记。</span><span class="sxs-lookup"><span data-stu-id="0546b-106">All assets must be grouped and tagged according to defined grouping and tagging strategies.</span></span>
- <span data-ttu-id="0546b-107">所有资产都必须使用已批准的部署模型。</span><span class="sxs-lookup"><span data-stu-id="0546b-107">All assets must use an approved deployment model.</span></span>
- <span data-ttu-id="0546b-108">为云提供程序建立了治理基础之后，任何部署工具都必须与治理团队定义的工具兼容。</span><span class="sxs-lookup"><span data-stu-id="0546b-108">Once a governance foundation has been established for a cloud provider, any deployment tooling must be compatible with the tools defined by the governance team.</span></span>

<span data-ttu-id="0546b-109">标识基线：</span><span class="sxs-lookup"><span data-stu-id="0546b-109">Identity Baseline:</span></span>

- <span data-ttu-id="0546b-110">应使用由当前治理策略批准的标识和角色来控制部署到云的所有资产。</span><span class="sxs-lookup"><span data-stu-id="0546b-110">All assets deployed to the cloud should be controlled using identities and roles approved by current governance policies.</span></span>
- <span data-ttu-id="0546b-111">本地 Active Directory 基础结构中具有提升的特权的所有组都应映射到已批准的 RBAC 角色。</span><span class="sxs-lookup"><span data-stu-id="0546b-111">All groups in the on-premises Active Directory infrastructure that have elevated privileges should be mapped to an approved RBAC role.</span></span>

<span data-ttu-id="0546b-112">安全基线：</span><span class="sxs-lookup"><span data-stu-id="0546b-112">Security Baseline:</span></span>

- <span data-ttu-id="0546b-113">部署到云的任何资产都必须具有已批准的数据分类。</span><span class="sxs-lookup"><span data-stu-id="0546b-113">Any asset deployed to the cloud must have an approved data classification.</span></span>
- <span data-ttu-id="0546b-114">在可以批准并实现足够的安全和治理要求之前，无法将任何使用受保护数据级别标识的资产部署到云。</span><span class="sxs-lookup"><span data-stu-id="0546b-114">No assets identified with a protected level of data may be deployed to the cloud, until sufficient requirements for security and governance can be approved and implemented.</span></span>
- <span data-ttu-id="0546b-115">在可以验证和控制最小网络安全要求之前，云环境都被视为非管制区域，应符合与其他数据中心或内部网络类似的连接要求。</span><span class="sxs-lookup"><span data-stu-id="0546b-115">Until minimum network security requirements can be validated and governed, cloud environments are seen as a demilitarized zone and should meet similar connection requirements to other data centers or internal networks.</span></span>

<span data-ttu-id="0546b-116">成本管理：</span><span class="sxs-lookup"><span data-stu-id="0546b-116">Cost Management:</span></span>

- <span data-ttu-id="0546b-117">为进行跟踪，所有资产都必须分配给一个核心业务功能中的应用程序所有者。</span><span class="sxs-lookup"><span data-stu-id="0546b-117">For tracking purposes, all assets must be assigned to an application owner within one of the core business functions.</span></span>
- <span data-ttu-id="0546b-118">当成本问题出现时，将与财务团队一起建立其他治理要求。</span><span class="sxs-lookup"><span data-stu-id="0546b-118">When cost concerns arise, additional governance requirements will be established with the Finance team.</span></span>

<span data-ttu-id="0546b-119">资源一致性：</span><span class="sxs-lookup"><span data-stu-id="0546b-119">Resource Consistency:</span></span>

- <span data-ttu-id="0546b-120">由于在此阶段不部署任何任务关键型工作，因此无需控制任何 SLA、性能或 BCDR 要求。</span><span class="sxs-lookup"><span data-stu-id="0546b-120">Because no mission-critical workloads are deployed at this stage, there are no SLA, performance, or BCDR requirements to be governed.</span></span>
- <span data-ttu-id="0546b-121">部署任何关键型工作负荷时，将随 IT 操作一起建立其他治理要求。</span><span class="sxs-lookup"><span data-stu-id="0546b-121">When mission-critical workloads are deployed, additional governance requirements will be established with IT operations.</span></span>

## <a name="processes"></a><span data-ttu-id="0546b-122">进程</span><span class="sxs-lookup"><span data-stu-id="0546b-122">Processes</span></span>

<span data-ttu-id="0546b-123">没有为这些治理策略的持续监视和执行分配任何预算。</span><span class="sxs-lookup"><span data-stu-id="0546b-123">No budget has been allocated for ongoing monitoring and enforcement of these governance policies.</span></span> <span data-ttu-id="0546b-124">因此，云治理团队有一些临时方法来监视是否遵从策略语句。</span><span class="sxs-lookup"><span data-stu-id="0546b-124">Because of that, the Cloud Governance team has some ad hoc ways to monitor adherence to policy statements.</span></span>

- <span data-ttu-id="0546b-125">**教育**：云治理团队花费时间在支持这些策略的治理过程方面对云采用团队进行培训。</span><span class="sxs-lookup"><span data-stu-id="0546b-125">**Education**: The Cloud Governance team is investing time to educate the cloud adoption teams on the governance journeys that support these policies.</span></span>
- <span data-ttu-id="0546b-126">**部署评审**：在部署任何资产之前，云治理团队会与云采用团队一起评审治理过程。</span><span class="sxs-lookup"><span data-stu-id="0546b-126">**Deployment** reviews: Before deploying any asset, the Cloud Governance team will review the governance journey with the cloud adoption teams.</span></span>
