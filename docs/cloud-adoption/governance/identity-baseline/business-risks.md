---
title: CAF：标识基线动机和业务风险
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 标识基线动机和业务风险
author: BrianBlanchard
ms.openlocfilehash: ef831d1b3b01251b27f1f7b18e551c49996a2468
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900457"
---
# <a name="identity-baseline-motivations-and-business-risks"></a><span data-ttu-id="41f22-103">标识基线动机和业务风险</span><span class="sxs-lookup"><span data-stu-id="41f22-103">Identity Baseline motivations and business risks</span></span>

<span data-ttu-id="41f22-104">本文讨论客户通常在云治理策略中采用标识基线规则的原因。</span><span class="sxs-lookup"><span data-stu-id="41f22-104">This article discusses the reasons that customers typically adopt an Identity Baseline discipline within a cloud governance strategy.</span></span> <span data-ttu-id="41f22-105">此外，它还提供了驱动策略声明的业务风险的几个示例。</span><span class="sxs-lookup"><span data-stu-id="41f22-105">It also provides a few examples of business risks that drive policy statements.</span></span>

<!-- markdownlint-disable MD026 -->

## <a name="is-identity-baseline-relevant"></a><span data-ttu-id="41f22-106">与标识基线相关吗？</span><span class="sxs-lookup"><span data-stu-id="41f22-106">Is Identity Baseline relevant?</span></span>

<span data-ttu-id="41f22-107">传统的本地目录旨在允许企业严格控制其内部网络和数据中心内的用户、组和角色的权限和策略。</span><span class="sxs-lookup"><span data-stu-id="41f22-107">Traditional on-premises directories are designed to allow businesses to strictly control permissions and policies for users, groups, and roles within their internal networks and datacenters.</span></span> <span data-ttu-id="41f22-108">它通常用于支持单租户实现，其中的服务仅适用于本地环境。</span><span class="sxs-lookup"><span data-stu-id="41f22-108">This is usually intended to support single tenant implementations, with services applicable only within the on-premises environment.</span></span>

<span data-ttu-id="41f22-109">云标识服务主要用于将组织的身份验证和访问控制功能扩展到 Internet。</span><span class="sxs-lookup"><span data-stu-id="41f22-109">Cloud identity services are intended to expand an organization's authentication and access control capabilities to the internet.</span></span> <span data-ttu-id="41f22-110">它们支持多租户，并且可用于跨云应用程序和部署来管理用户和访问策略。</span><span class="sxs-lookup"><span data-stu-id="41f22-110">They support multi-tenancy and can be used to manage users and access policy across cloud applications and deployments.</span></span> <span data-ttu-id="41f22-111">公共云平台具有某些形式的云本机标识服务，可支持管理和部署任务，并且能够与现有的本地标识解决方案[进行不同级别的集成](../../decision-guides/identity/overview.md)。</span><span class="sxs-lookup"><span data-stu-id="41f22-111">Public cloud platforms have some form of cloud-native identity services supporting management and deployment tasks and are capable of [varying levels of integration](../../decision-guides/identity/overview.md) with your existing on-premises identity solutions.</span></span> <span data-ttu-id="41f22-112">所有这些功能都可能导致云标识策略比传统的本地解决方案的要求更复杂。</span><span class="sxs-lookup"><span data-stu-id="41f22-112">All of these features can result in cloud identity policy being more complicated than your traditional on-premises solutions require.</span></span>

<span data-ttu-id="41f22-113">标识基线规则对于云部署的重要程度取决于团队大小，并且将基于云的标识解决方案与已有本地标识服务集成的需求。</span><span class="sxs-lookup"><span data-stu-id="41f22-113">The importance of the Identity Baseline discipline to your cloud deployment will depend on the size of your team and need to integrate your cloud-based identity solution with an existing on-premises identity service.</span></span> <span data-ttu-id="41f22-114">初始测试部署可能不需要太多的用户组织或管理，但随着云资产的完善，可能会需要支持更为复杂的组织集成和集中管理。</span><span class="sxs-lookup"><span data-stu-id="41f22-114">Initial test deployments may not require much in the way of user organization or management, but as your cloud estate matures, you will likely need to support more complicated organizational integration and centralized management.</span></span>

## <a name="business-risk"></a><span data-ttu-id="41f22-115">业务风险</span><span class="sxs-lookup"><span data-stu-id="41f22-115">Business risk</span></span>

<span data-ttu-id="41f22-116">标识基线规则可尝试解决与标识服务和访问控制相关的核心业务风险。</span><span class="sxs-lookup"><span data-stu-id="41f22-116">The Identity Baseline discipline attempts to address core business risks related to identity services and access control.</span></span> <span data-ttu-id="41f22-117">与你的企业合作来识别这些风险，并在计划和实施云部署时监控每个风险的相关性。</span><span class="sxs-lookup"><span data-stu-id="41f22-117">Work with your business to identify these risks and monitor each of them for relevance as you plan for and implement your cloud deployments.</span></span>

<span data-ttu-id="41f22-118">组织间的风险会有所不同，以下是常见的与标识相关的风险，你可以由此在云治理团队中展开讨论：</span><span class="sxs-lookup"><span data-stu-id="41f22-118">Risks will differ between organization, but the following serve as common identity-related risks that you can use as a starting point for discussions within your Cloud Governance team:</span></span>

- <span data-ttu-id="41f22-119">**未授权访问**。</span><span class="sxs-lookup"><span data-stu-id="41f22-119">**Unauthorized access**.</span></span> <span data-ttu-id="41f22-120">如果敏感数据和资源可被未经授权的用户访问，可能会导致数据泄露或服务中断，同时违反组织的安全外围，并有可能承担业务或法律责任。</span><span class="sxs-lookup"><span data-stu-id="41f22-120">Sensitive data and resources that can be accessed by unauthorized users can lead to data leaks or service disruptions, violating your organization's security perimeter and risking business or legal liabilities.</span></span>
- <span data-ttu-id="41f22-121">**多个标识解决方案导致效率低下**。</span><span class="sxs-lookup"><span data-stu-id="41f22-121">**Inefficiency due to multiple identity solutions**.</span></span> <span data-ttu-id="41f22-122">具有多个标识服务租户的组织可能需要多个用户帐户。</span><span class="sxs-lookup"><span data-stu-id="41f22-122">Organizations with multiple identity services tenants can require multiple accounts for users.</span></span> <span data-ttu-id="41f22-123">对于需要牢记多组凭据的用户，以及跨多个系统管理帐户的 IT 人员，这可能会导致效率低下。</span><span class="sxs-lookup"><span data-stu-id="41f22-123">This can lead to inefficiency for users who need to remember multiple sets of credentials and for IT in managing accounts across multiple systems.</span></span> <span data-ttu-id="41f22-124">若用户访问权限分配未随员工、团队和业务目标的更改在标识解决方案中更新，云资源可能易于遭到未经授权的访问，或者用户可能无法访问所需资源。</span><span class="sxs-lookup"><span data-stu-id="41f22-124">If user access assignments are not updated across identity solutions as staff, teams, and business goals change, your cloud resources may be vulnerable to unauthorized access or users unable to access required resources.</span></span>
- <span data-ttu-id="41f22-125">**无法与外部合作伙伴共享资源**。</span><span class="sxs-lookup"><span data-stu-id="41f22-125">**Inability to share resources with external partners**.</span></span> <span data-ttu-id="41f22-126">难于将外部业务合作伙伴添加到已有标识解决方案，这可能会阻碍资源共享和业务沟通效率。</span><span class="sxs-lookup"><span data-stu-id="41f22-126">Difficulty adding external business partners to your existing identity solutions can prevent efficient resource sharing and business communication.</span></span>
- <span data-ttu-id="41f22-127">**本地标识依赖关系**。</span><span class="sxs-lookup"><span data-stu-id="41f22-127">**On-premises identity dependencies**.</span></span> <span data-ttu-id="41f22-128">传统的身份验证机制或第三方多重身份验证可能不适用于云，因为它们需要迁移工作负载以进行重组，或需要将其他标识服务部署到云。</span><span class="sxs-lookup"><span data-stu-id="41f22-128">Legacy authentication mechanisms or third-party multi-factor authentication might not be available in the cloud, requiring either migrating workloads to be retooled, or additional identity services to be deployed to the cloud.</span></span> <span data-ttu-id="41f22-129">以上任意一项要求均有可能延迟或阻碍迁移，并增加成本。</span><span class="sxs-lookup"><span data-stu-id="41f22-129">Either requirement could delay or prevent migration, and increase costs.</span></span>

## <a name="next-steps"></a><span data-ttu-id="41f22-130">后续步骤</span><span class="sxs-lookup"><span data-stu-id="41f22-130">Next steps</span></span>

<span data-ttu-id="41f22-131">使用[云管理模板](./template.md)，记录可能由当前云采用计划引入的业务风险。</span><span class="sxs-lookup"><span data-stu-id="41f22-131">Using the [Cloud Management Template](./template.md), document business risks that are likely to be introduced by the current cloud adoption plan.</span></span>

<span data-ttu-id="41f22-132">确立了对实际业务风险的了解后，下一步是记录业务对风险的容忍度，以及监视该容忍度的指示器和关键指标。</span><span class="sxs-lookup"><span data-stu-id="41f22-132">Once an understanding of realistic business risks is established, the next step is to document the business's tolerance for risk and the indicators and key metrics to monitor that tolerance.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="41f22-133">了解指示器、指标和风险容忍度</span><span class="sxs-lookup"><span data-stu-id="41f22-133">Understand indicators, metrics, and risk tolerance</span></span>](./metrics-tolerance.md)