<!-- TEMPLATE FILE - DO NOT ADD METADATA -->

### <a name="governance-of-resources"></a><span data-ttu-id="6c7a5-101">资源治理</span><span class="sxs-lookup"><span data-stu-id="6c7a5-101">Governance of resources</span></span>

<span data-ttu-id="6c7a5-102">在订阅上实施治理将来自 Azure 蓝图和蓝图内相关的资产。</span><span class="sxs-lookup"><span data-stu-id="6c7a5-102">Enforcing governance across subscriptions will come from Azure Blueprints and the associated assets within the blueprint.</span></span>

1. <span data-ttu-id="6c7a5-103">创建名为“治理 MVP”的 Azure 蓝图。</span><span class="sxs-lookup"><span data-stu-id="6c7a5-103">Create an Azure Blueprint named "Governance MVP".</span></span>
    1. <span data-ttu-id="6c7a5-104">强制使用标准的 Azure 角色。</span><span class="sxs-lookup"><span data-stu-id="6c7a5-104">Enforce the use of standard Azure roles.</span></span>
    2. <span data-ttu-id="6c7a5-105">强制用户只能对现有 RBAC 实现进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="6c7a5-105">Enforce that users can only authenticate against existing an RBAC implementation.</span></span>
    3. <span data-ttu-id="6c7a5-106">将此蓝图应用到管理组中的所有订阅。</span><span class="sxs-lookup"><span data-stu-id="6c7a5-106">Apply this blueprint to all subscriptions within the management group.</span></span>
2. <span data-ttu-id="6c7a5-107">创建 Azure Policy 来应用或实施以下内容：</span><span class="sxs-lookup"><span data-stu-id="6c7a5-107">Create an Azure Policy to apply or enforce the following:</span></span>
    1. <span data-ttu-id="6c7a5-108">资源标记应要求业务功能、数据分类、危急程度、SLA、环境和应用程序值。</span><span class="sxs-lookup"><span data-stu-id="6c7a5-108">Resource tagging should require values for Business Function, Data Classification, Criticality, SLA, Environment, and  Application.</span></span>
    2. <span data-ttu-id="6c7a5-109">应用程序标记的值应与资源组的名称相匹配。</span><span class="sxs-lookup"><span data-stu-id="6c7a5-109">The value of the Application tag should match the name of the resource group.</span></span>
    3. <span data-ttu-id="6c7a5-110">验证每个资源组和资源的角色分配。</span><span class="sxs-lookup"><span data-stu-id="6c7a5-110">Validate role assignments for each resource group and resource.</span></span>
3. <span data-ttu-id="6c7a5-111">将“治理 MVP”Azure 蓝图发布到每个管理组并进行应用。</span><span class="sxs-lookup"><span data-stu-id="6c7a5-111">Publish and apply the "Governance MVP" Azure Blueprint to each management group.</span></span>

<span data-ttu-id="6c7a5-112">通过这些模式可发现资源对其进行跟踪，并强制实施基本角色管理。</span><span class="sxs-lookup"><span data-stu-id="6c7a5-112">These patterns enable resources to be discovered and tracked, and enforce basic role management.</span></span>

### <a name="demilitarized-zone-dmz"></a><span data-ttu-id="6c7a5-113">外围安全区域 (DMZ)</span><span class="sxs-lookup"><span data-stu-id="6c7a5-113">Demilitarized Zone (DMZ)</span></span>

<span data-ttu-id="6c7a5-114">特定订阅通常需要对本地资源进行某种级别的访问。</span><span class="sxs-lookup"><span data-stu-id="6c7a5-114">It’s common for specific subscriptions to require some level of access to on-premises resources.</span></span> <span data-ttu-id="6c7a5-115">当某些依赖资源仍处于本地数据中心时，迁移方案或开发方案可能就是这种情况。</span><span class="sxs-lookup"><span data-stu-id="6c7a5-115">This may be the case for migration scenarios or development scenarios, when some dependent resources are still in the on-premises datacenter.</span></span> <span data-ttu-id="6c7a5-116">在此情况下，治理 MVP 将添加以下最佳做法：</span><span class="sxs-lookup"><span data-stu-id="6c7a5-116">In this case, the governance MVP adds the following best practices:</span></span>

1. <span data-ttu-id="6c7a5-117">建立云 DMZ。</span><span class="sxs-lookup"><span data-stu-id="6c7a5-117">Establish a Cloud DMZ.</span></span>
    1. <span data-ttu-id="6c7a5-118">[云 DMZ 参考体系结构](/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)确立了一种模式和部署模型，用于在 Azure 中创建 VPN 网关。</span><span class="sxs-lookup"><span data-stu-id="6c7a5-118">The [Cloud DMZ reference architecture](/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) establishes a pattern and deployment model for creating a VPN Gateway in Azure.</span></span>
    2. <span data-ttu-id="6c7a5-119">验证本地数据中心中的本地边缘设备是否具有适当的 DMZ 连接和安全要求。</span><span class="sxs-lookup"><span data-stu-id="6c7a5-119">Validate that proper DMZ connectivity and security requirements are in place for a local edge device in the on-premises datacenter.</span></span>
    3. <span data-ttu-id="6c7a5-120">验证本地边缘设备是否符合 Azure VPN 网关要求。</span><span class="sxs-lookup"><span data-stu-id="6c7a5-120">Validate that the local edge device is compatible with Azure VPN Gateway requirements.</span></span>
    4. <span data-ttu-id="6c7a5-121">本地 VPN 连接经验证后，即可捕获由该参考体系结构创建的资源管理器模板。</span><span class="sxs-lookup"><span data-stu-id="6c7a5-121">Once connection to the on-premise VPN has been verified, capture the Resource Manager template created by that reference architecture.</span></span>
2. <span data-ttu-id="6c7a5-122">创建名为“DMZ”的第二个蓝图。</span><span class="sxs-lookup"><span data-stu-id="6c7a5-122">Create a second blueprint named "DMZ".</span></span>
    1. <span data-ttu-id="6c7a5-123">将用于 VPN 网关的资源管理器模板添加到蓝图。</span><span class="sxs-lookup"><span data-stu-id="6c7a5-123">Add the Resource Manager template for the VPN Gateway to the blueprint.</span></span>
3. <span data-ttu-id="6c7a5-124">将 DMZ 蓝图添加到需要本地连接的任何订阅。</span><span class="sxs-lookup"><span data-stu-id="6c7a5-124">Apply the DMZ blueprint to any subscriptions requiring on-premises connectivity.</span></span> <span data-ttu-id="6c7a5-125">除治理 MVP 蓝图外，还应该应用此蓝图。</span><span class="sxs-lookup"><span data-stu-id="6c7a5-125">This blueprint should be applied in addition to the governance MVP blueprint.</span></span>

<span data-ttu-id="6c7a5-126">IT 安全和传统治理团队引起的某个最大问题是采用早期云会危及现有资产的风险。</span><span class="sxs-lookup"><span data-stu-id="6c7a5-126">One of the biggest concerns raised by IT security and traditional governance teams, is the risk of early stage cloud adoption compromising existing assets.</span></span> <span data-ttu-id="6c7a5-127">上述方法允许云采用团队构建和迁移混合解决方案，同时降低对本地资产的风险。</span><span class="sxs-lookup"><span data-stu-id="6c7a5-127">The above approach allows cloud adoption teams to build and migrate hybrid solutions, with reduced risk to on-premises assets.</span></span> <span data-ttu-id="6c7a5-128">在后面的改进中，将删除此临时解决方案。</span><span class="sxs-lookup"><span data-stu-id="6c7a5-128">In later evolution, this temporary solution would be removed.</span></span>

> [!NOTE]
> <span data-ttu-id="6c7a5-129">以上是快速创建基线治理 MVP 的起始点。</span><span class="sxs-lookup"><span data-stu-id="6c7a5-129">The above is a starting point to quickly create a baseline governance MVP.</span></span> <span data-ttu-id="6c7a5-130">这只是治理流程的开始。</span><span class="sxs-lookup"><span data-stu-id="6c7a5-130">This is only the beginning of the governance journey.</span></span> <span data-ttu-id="6c7a5-131">随着公司继续采用云并在以下区域中承担更多风险，将需要进一步改进：</span><span class="sxs-lookup"><span data-stu-id="6c7a5-131">Further evolution will be needed as the company continues to adopt the cloud and takes on more risk in the following areas:</span></span>
>
> - <span data-ttu-id="6c7a5-132">任务关键型工作负荷</span><span class="sxs-lookup"><span data-stu-id="6c7a5-132">Mission-critical workloads</span></span>
> - <span data-ttu-id="6c7a5-133">受保护的数据</span><span class="sxs-lookup"><span data-stu-id="6c7a5-133">Protected data</span></span>
> - <span data-ttu-id="6c7a5-134">成本管理</span><span class="sxs-lookup"><span data-stu-id="6c7a5-134">Cost management</span></span>
> - <span data-ttu-id="6c7a5-135">多云方案</span><span class="sxs-lookup"><span data-stu-id="6c7a5-135">Multi-cloud scenarios</span></span>
>
><span data-ttu-id="6c7a5-136">此外，此 MVP 的特定详细信息基于虚构公司的示例过程，如下文中所述。</span><span class="sxs-lookup"><span data-stu-id="6c7a5-136">Moreover, the specific details of this MVP are based on the example journey of a fictitious company, described in the articles that follow.</span></span> <span data-ttu-id="6c7a5-137">强烈建议在实施此最佳做法前，先熟悉本系列中的其他文章。</span><span class="sxs-lookup"><span data-stu-id="6c7a5-137">We highly recommend becoming familiar with the other articles in this series before implementing this best practice.</span></span>
