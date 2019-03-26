---
title: Azure 应用程序的设计原则
titleSuffix: Azure Application Architecture Guide
description: Azure 应用程序的设计原则。
author: MikeWasson
ms.date: 08/30/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19
---

# <a name="ten-design-principles-for-azure-applications"></a><span data-ttu-id="d032e-103">Azure 应用程序的十个设计原则</span><span class="sxs-lookup"><span data-stu-id="d032e-103">Ten design principles for Azure applications</span></span>

<span data-ttu-id="d032e-104">遵循这些设计原则可以提高应用程序的可伸缩性、复原能力和易管理性。</span><span class="sxs-lookup"><span data-stu-id="d032e-104">Follow these design principles to make your application more scalable, resilient, and manageable.</span></span>

<span data-ttu-id="d032e-105">**[自我修复设计](self-healing.md)**。</span><span class="sxs-lookup"><span data-stu-id="d032e-105">**[Design for self healing](self-healing.md)**.</span></span> <span data-ttu-id="d032e-106">在分布式系统中，故障时有发生。</span><span class="sxs-lookup"><span data-stu-id="d032e-106">In a distributed system, failures happen.</span></span> <span data-ttu-id="d032e-107">设计应用程序以在故障发生时进行自我修复。</span><span class="sxs-lookup"><span data-stu-id="d032e-107">Design your application to be self healing when failures occur.</span></span>

<span data-ttu-id="d032e-108">**[实现全面冗余](redundancy.md)**。</span><span class="sxs-lookup"><span data-stu-id="d032e-108">**[Make all things redundant](redundancy.md)**.</span></span> <span data-ttu-id="d032e-109">在应用程序中构建冗余，以避免出现单一故障点。</span><span class="sxs-lookup"><span data-stu-id="d032e-109">Build redundancy into your application, to avoid having single points of failure.</span></span>

<span data-ttu-id="d032e-110">**[尽量减少协调](minimize-coordination.md)**。</span><span class="sxs-lookup"><span data-stu-id="d032e-110">**[Minimize coordination](minimize-coordination.md)**.</span></span> <span data-ttu-id="d032e-111">最大程度地减少应用程序服务之间的协调以实现可伸缩性。</span><span class="sxs-lookup"><span data-stu-id="d032e-111">Minimize coordination between application services to achieve scalability.</span></span>

<span data-ttu-id="d032e-112">**[横向扩展设计](scale-out.md)**。合理设计应用程序，以便能够通过按需添加或删除新实例对应用程序进行横向缩放。</span><span class="sxs-lookup"><span data-stu-id="d032e-112">**[Design to scale out](scale-out.md)**. Design your application so that it can scale horizontally, adding or removing new instances as demand requires.</span></span>

<span data-ttu-id="d032e-113">**[通过分区解决限制](partition.md)**。</span><span class="sxs-lookup"><span data-stu-id="d032e-113">**[Partition around limits](partition.md)**.</span></span> <span data-ttu-id="d032e-114">使用分区来解决数据库、网络和计算限制。</span><span class="sxs-lookup"><span data-stu-id="d032e-114">Use partitioning to work around database, network, and compute limits.</span></span>

<span data-ttu-id="d032e-115">**[运营设计](design-for-operations.md)**。</span><span class="sxs-lookup"><span data-stu-id="d032e-115">**[Design for operations](design-for-operations.md)**.</span></span> <span data-ttu-id="d032e-116">合理设计应用程序，使运营团队获得所需的工具。</span><span class="sxs-lookup"><span data-stu-id="d032e-116">Design your application so that the operations team has the tools they need.</span></span>

<span data-ttu-id="d032e-117">**[使用托管服务](managed-services.md)**。</span><span class="sxs-lookup"><span data-stu-id="d032e-117">**[Use managed services](managed-services.md)**.</span></span> <span data-ttu-id="d032e-118">尽量使用平台即服务 (PaaS) 而不是基础结构即服务 (IaaS)。</span><span class="sxs-lookup"><span data-stu-id="d032e-118">When possible, use platform as a service (PaaS) rather than infrastructure as a service (IaaS).</span></span>

<span data-ttu-id="d032e-119">**[使用最佳的数据存储完成作业](use-the-best-data-store.md)**。</span><span class="sxs-lookup"><span data-stu-id="d032e-119">**[Use the best data store for the job](use-the-best-data-store.md)**.</span></span> <span data-ttu-id="d032e-120">选择最适合数据的存储技术，并了解如何使用该技术。</span><span class="sxs-lookup"><span data-stu-id="d032e-120">Pick the storage technology that is the best fit for your data and how it will be used.</span></span>

<span data-ttu-id="d032e-121">**[演变设计](design-for-evolution.md)**。</span><span class="sxs-lookup"><span data-stu-id="d032e-121">**[Design for evolution](design-for-evolution.md)**.</span></span> <span data-ttu-id="d032e-122">所有成功的应用程序会不断变化。</span><span class="sxs-lookup"><span data-stu-id="d032e-122">All successful applications change over time.</span></span> <span data-ttu-id="d032e-123">演变设计是持续创新的关键。</span><span class="sxs-lookup"><span data-stu-id="d032e-123">An evolutionary design is key for continuous innovation.</span></span>

<span data-ttu-id="d032e-124">**[根据业务需求构建](build-for-business.md)**。</span><span class="sxs-lookup"><span data-stu-id="d032e-124">**[Build for the needs of business](build-for-business.md)**.</span></span> <span data-ttu-id="d032e-125">每个设计决策必须与业务要求相称。</span><span class="sxs-lookup"><span data-stu-id="d032e-125">Every design decision must be justified by a business requirement.</span></span>
