---
title: CAF：资源一致性动机和业务风险
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 资源一致性动机和业务风险
author: alexbuckgit
ms.openlocfilehash: 19e0d761e4afa3473099bde2edc960c8b9eadb79
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900542"
---
# <a name="resource-consistency-motivations-and-business-risks"></a><span data-ttu-id="7cd75-103">资源一致性动机和业务风险</span><span class="sxs-lookup"><span data-stu-id="7cd75-103">Resource Consistency motivations and business risks</span></span>

<span data-ttu-id="7cd75-104">本文讨论客户通常在云治理策略中采用资源一致性规则的原因。</span><span class="sxs-lookup"><span data-stu-id="7cd75-104">This article discusses the reasons that customers typically adopt a Resource Consistency discipline within a cloud governance strategy.</span></span> <span data-ttu-id="7cd75-105">此外，还提供了可能驱动策略语句的几个潜在业务风险示例。</span><span class="sxs-lookup"><span data-stu-id="7cd75-105">It also provides a few examples of potential business risks that can drive policy statements.</span></span>

<!-- markdownlint-disable MD026 -->

## <a name="is-resource-consistency-relevant"></a><span data-ttu-id="7cd75-106">资源一致性是否相关？</span><span class="sxs-lookup"><span data-stu-id="7cd75-106">Is Resource Consistency relevant?</span></span>

<span data-ttu-id="7cd75-107">涉及到部署资源和工作负荷时，与大多数传统本地数据中心相比，云可提供更高的敏捷性和灵活性。</span><span class="sxs-lookup"><span data-stu-id="7cd75-107">When it comes to deploying resources and workloads, the cloud offers increased agility and flexibility over most traditional on-premises datacenters.</span></span> <span data-ttu-id="7cd75-108">但是，这些基于云的潜在优势也带有潜在的管理缺陷，这些缺陷可能会严重损害云采用的成功。</span><span class="sxs-lookup"><span data-stu-id="7cd75-108">However, these potential cloud-based advantages also come paired with potential management drawbacks that can seriously jeopardize the success of your cloud adoption.</span></span> <span data-ttu-id="7cd75-109">部署了哪些资产？</span><span class="sxs-lookup"><span data-stu-id="7cd75-109">What assets have you deployed?</span></span> <span data-ttu-id="7cd75-110">哪些团队拥有哪些资产？</span><span class="sxs-lookup"><span data-stu-id="7cd75-110">What teams own what assets?</span></span> <span data-ttu-id="7cd75-111">是否有足够的资源来支持工作负荷？</span><span class="sxs-lookup"><span data-stu-id="7cd75-111">Do you have enough resources supporting a workload?</span></span> <span data-ttu-id="7cd75-112">如何知道工作负荷是否状态良好？</span><span class="sxs-lookup"><span data-stu-id="7cd75-112">How do you know if workloads are healthy?</span></span>

<span data-ttu-id="7cd75-113">对于确保始终如一地重复部署、更新和配置资源，以及确保尽量减少服务中断并在尽可能少的时间内修复，资源一致性至关重要。</span><span class="sxs-lookup"><span data-stu-id="7cd75-113">Resource Consistency is crucial to ensure that resources are deployed, updated, and configured consistently and repeatably, and that service disruptions are minimized and remedied in as little time as possible.</span></span>

<span data-ttu-id="7cd75-114">资源一致性规则涉及到确定并缓解与云部署运营方面相关的业务风险。</span><span class="sxs-lookup"><span data-stu-id="7cd75-114">The Resource Consistency discipline is concerned with identifying and mitigating business risks related to the operational aspects of your cloud deployment.</span></span> <span data-ttu-id="7cd75-115">资源一致性包括监视应用程序、工作负荷和资产性能。</span><span class="sxs-lookup"><span data-stu-id="7cd75-115">Resource Consistency includes monitoring of applications, workloads, and asset performance.</span></span> <span data-ttu-id="7cd75-116">资源一致性还包括满足规模需求、纠正性能服务级别协议 (SLA) 违规以及通过自动补救主动避免性能 SLA 违规所需的任务。</span><span class="sxs-lookup"><span data-stu-id="7cd75-116">It also includes the tasks required to meet scale demands, remediate performance Service Level Agreement (SLA) violations, and proactively avoid performance SLA violations through automated remediation.</span></span>

<span data-ttu-id="7cd75-117">初始测试部署可能不需要比采用一些粗略的命名和标记标准超出太多，即可支持资源一致性需求。</span><span class="sxs-lookup"><span data-stu-id="7cd75-117">Initial test deployments may not require much beyond adopting some cursory naming and tagging standards to support your Resource Consistency needs.</span></span> <span data-ttu-id="7cd75-118">随着云采用逐渐成熟并且部署更复杂的任务关键型资产，对投资资源一致性规则的需求会快速增加。</span><span class="sxs-lookup"><span data-stu-id="7cd75-118">As your cloud adoption matures and you deploy more complicated and mission-critical assets, the need to invest in the Resource Consistency discipline increases rapidly.</span></span>

## <a name="business-risk"></a><span data-ttu-id="7cd75-119">业务风险</span><span class="sxs-lookup"><span data-stu-id="7cd75-119">Business risk</span></span>

<span data-ttu-id="7cd75-120">资源一致性规则尝试解决核心运营业务风险。</span><span class="sxs-lookup"><span data-stu-id="7cd75-120">The Resource Consistency discipline attempts to address core operational business risks.</span></span> <span data-ttu-id="7cd75-121">与企业和 IT 团队合作来确定这些风险，并在规划和实现云部署时监视每个风险的相关性。</span><span class="sxs-lookup"><span data-stu-id="7cd75-121">Work with your business and IT teams to identify these risks and monitor each of them for relevance as you plan for and implement your cloud deployments.</span></span>

<span data-ttu-id="7cd75-122">组织间的风险会有所不同，不过以下这些常见风险可以用作在云治理团队中展开讨论的起点：</span><span class="sxs-lookup"><span data-stu-id="7cd75-122">Risks will differ between organization, but the following serve as common risks that you can use as a starting point for discussions within your Cloud Governance team:</span></span>

- <span data-ttu-id="7cd75-123">**不必要的运营成本**。</span><span class="sxs-lookup"><span data-stu-id="7cd75-123">**Unnecessary operational cost**.</span></span> <span data-ttu-id="7cd75-124">已过时或未使用的资源或是在需求较低时间内预配过度的资源会增加不必要的运营成本。</span><span class="sxs-lookup"><span data-stu-id="7cd75-124">Obsolete or unused resources, or resources that are overprovisioned during times of low demand, add unnecessary operational costs.</span></span>
- <span data-ttu-id="7cd75-125">**预配不足的资源**。</span><span class="sxs-lookup"><span data-stu-id="7cd75-125">**Underprovisioned resources**.</span></span> <span data-ttu-id="7cd75-126">对于这类资源，高于预期需求的体验可能会在云资源不足以应对需求时，导致业务中断。</span><span class="sxs-lookup"><span data-stu-id="7cd75-126">Resources that experience higher than anticipated demand can result in business disruption as cloud resources are overwhelmed by demand.</span></span>
- <span data-ttu-id="7cd75-127">**管理效率低下**。</span><span class="sxs-lookup"><span data-stu-id="7cd75-127">**Management inefficiencies**.</span></span> <span data-ttu-id="7cd75-128">缺少与资源相关联的一致的命名和标记元数据可能会导致 IT 人员难以查找用于管理任务的资源或标识与资产相关的所有权和计帐信息。</span><span class="sxs-lookup"><span data-stu-id="7cd75-128">Lack of consistent naming and tagging metadata associated with resources can lead to IT staff having difficulty finding resources for management tasks or identifying ownership and accounting information related to assets.</span></span> <span data-ttu-id="7cd75-129">这样会导致管理效率低下，从而可能会增加成本并减慢针对服务中断或其他运营问题的 IT 响应速度。</span><span class="sxs-lookup"><span data-stu-id="7cd75-129">This results in management inefficiencies that can increase cost and slow IT responsiveness to service disruption or other operational issues.</span></span>
- <span data-ttu-id="7cd75-130">**业务中断**。</span><span class="sxs-lookup"><span data-stu-id="7cd75-130">**Business Interruption**.</span></span> <span data-ttu-id="7cd75-131">会导致违反组织建立的服务级别协议 (SLA) 的服务中断可能会导致失去业务或对公司造成其他财务影响。</span><span class="sxs-lookup"><span data-stu-id="7cd75-131">Service disruptions that result in violations of your organization's established Service Level Agreements (SLAs) can result in loss of business or other financial impacts to your company.</span></span>

## <a name="next-steps"></a><span data-ttu-id="7cd75-132">后续步骤</span><span class="sxs-lookup"><span data-stu-id="7cd75-132">Next steps</span></span>

<span data-ttu-id="7cd75-133">使用[云管理模板](./template.md)，记录可能由当前云采用计划引入的业务风险。</span><span class="sxs-lookup"><span data-stu-id="7cd75-133">Using the [Cloud Management Template](./template.md), document business risks that are likely to be introduced by the current cloud adoption plan.</span></span>

<span data-ttu-id="7cd75-134">确立了对实际业务风险的了解后，下一步是记录业务对风险的容忍度，以及用于监视该容忍度的指示器和关键指标。</span><span class="sxs-lookup"><span data-stu-id="7cd75-134">Once an understanding of realistic business risks is established, the next step is to document the business's tolerance for risk and the indicators and key metrics to monitor that tolerance.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="7cd75-135">了解指示器、指标和风险容忍度</span><span class="sxs-lookup"><span data-stu-id="7cd75-135">Understand indicators, metrics, and risk tolerance</span></span>](./metrics-tolerance.md)
