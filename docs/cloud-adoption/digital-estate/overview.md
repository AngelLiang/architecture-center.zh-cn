---
title: CAF：什么是数字资产？
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: 什么是数字资产？
author: BrianBlanchard
ms.date: 12/10/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.openlocfilehash: d23bdbdd98226e8b9cdb9bbb5fefa5a918a498d8
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55898419"
---
<!-- markdownlint-disable MD026 -->

# <a name="caf-what-is-a-digital-estate"></a><span data-ttu-id="56155-103">CAF：什么是数字资产？</span><span class="sxs-lookup"><span data-stu-id="56155-103">CAF: What is a digital estate?</span></span>

<span data-ttu-id="56155-104">每个新型公司都有某种形式的数字资产。</span><span class="sxs-lookup"><span data-stu-id="56155-104">Every modern company has some form of digital estate.</span></span> <span data-ttu-id="56155-105">数字资产与实物资产非常类似，是对许多有形自有资产的抽象引用。</span><span class="sxs-lookup"><span data-stu-id="56155-105">Much like a physical estate, a digital estate is an abstract reference to a number of tangible, owned assets.</span></span> <span data-ttu-id="56155-106">在数字资产中，这些资产包括虚拟机 (VM)、服务器、应用程序、数据等。</span><span class="sxs-lookup"><span data-stu-id="56155-106">In a digital estate, those assets are comprised of virtual machines (VMs), servers, applications, data, and so on.</span></span> <span data-ttu-id="56155-107">本质上，数字资产是为业务流程和支持性运营工作提供动力的 IT 资产集合。</span><span class="sxs-lookup"><span data-stu-id="56155-107">Essentially, a digital estate is the collection of IT assets that power business processes and supporting operations.</span></span>

<span data-ttu-id="56155-108">在规划和执行数字转换工作期间，数字资产的重要性越发明显。</span><span class="sxs-lookup"><span data-stu-id="56155-108">The importance of a digital estate is most obvious during planning and execution of Digital Transformation efforts.</span></span> <span data-ttu-id="56155-109">在转换旅程中，数字资产涉及到云策略团队如何将业务成果映射到发布计划和技术工作。</span><span class="sxs-lookup"><span data-stu-id="56155-109">During transformation journeys, the digital estate is how Cloud Strategy teams map the business outcomes to release plans and technical efforts.</span></span> <span data-ttu-id="56155-110">所有这些工作从盘点和计量组织当前拥有的数字资产开始。</span><span class="sxs-lookup"><span data-stu-id="56155-110">That all starts with an inventory and measurement of the digital assets the organization owns today.</span></span>

## <a name="how-can-a-digital-estate-be-measured"></a><span data-ttu-id="56155-111">如何计量数字资产？</span><span class="sxs-lookup"><span data-stu-id="56155-111">How can a digital estate be measured?</span></span>

<span data-ttu-id="56155-112">数字资产的计量根据所需的业务成果而异。</span><span class="sxs-lookup"><span data-stu-id="56155-112">The measurement of a digital estate changes depending on the desired business outcomes.</span></span>

- <span data-ttu-id="56155-113">**基础结构迁移**。</span><span class="sxs-lookup"><span data-stu-id="56155-113">**Infrastructure migrations**.</span></span> <span data-ttu-id="56155-114">如果组织面向内部并寻求优化成本、运营流程、灵活性或其他运营优化方面，则数字资产侧重于 VM、服务器和支持性工作负荷。</span><span class="sxs-lookup"><span data-stu-id="56155-114">When an organization is inward facing and seeks to optimize cost, operational processes, agility, or other aspects of optimizing operations, the digital estate focuses on VMs, servers, and supporting workloads.</span></span>

- <span data-ttu-id="56155-115">**应用程序创新**。</span><span class="sxs-lookup"><span data-stu-id="56155-115">**Application innovation**.</span></span> <span data-ttu-id="56155-116">对于客户偏爱的转换，视角略有不同。</span><span class="sxs-lookup"><span data-stu-id="56155-116">For customer-obsessed transformations, the lens is a bit different.</span></span> <span data-ttu-id="56155-117">重点应放在应用程序、API 和支持客户的事务数据上。</span><span class="sxs-lookup"><span data-stu-id="56155-117">The focus should be placed in the applications, APIs, and transactional data that support the customers.</span></span> <span data-ttu-id="56155-118">通常不太注重 VM 和网络设备。</span><span class="sxs-lookup"><span data-stu-id="56155-118">VMs and network appliances are often of less focus.</span></span>

- <span data-ttu-id="56155-119">**数据驱动的创新**。</span><span class="sxs-lookup"><span data-stu-id="56155-119">**Data-driven innovation**.</span></span> <span data-ttu-id="56155-120">在当今数字驱动市场中，不建立强大的数据基础，就很难推出新产品或服务。</span><span class="sxs-lookup"><span data-stu-id="56155-120">In today's digitally driven market, it's difficult to launch a new product or service without a strong foundation in data.</span></span> <span data-ttu-id="56155-121">在中断性转换过程中，应更多地将重点放在组织中的数据仓库上。</span><span class="sxs-lookup"><span data-stu-id="56155-121">During disruptive transformation, the focus is more on the silos of data across the organization.</span></span>

<span data-ttu-id="56155-122">组织理解最重要的转换形式后，数字资产规划的管理就会容易得多。</span><span class="sxs-lookup"><span data-stu-id="56155-122">Once an organization understands the most important form of transformation, digital estate planning becomes much easier to manage.</span></span>

> [!TIP]
> <span data-ttu-id="56155-123">可以使用三个视图中的任何一个来计量每种类型的转换。</span><span class="sxs-lookup"><span data-stu-id="56155-123">Each type of transformation could be measured with any of the three views.</span></span> <span data-ttu-id="56155-124">此外，公司经常会同时执行所有三种转换。</span><span class="sxs-lookup"><span data-stu-id="56155-124">Further, it is common for companies to execute all three transformations in parallel.</span></span> <span data-ttu-id="56155-125">强烈建议领导层和云策略团队在对业务成功最重要的转换方面展开紧密的合作。</span><span class="sxs-lookup"><span data-stu-id="56155-125">It is highly suggested that the leadership and Cloud Strategy team be firmly aligned regarding the transformation that is most important for business success.</span></span> <span data-ttu-id="56155-126">这种理解可为多项举措的共同语言和指标建立基础。</span><span class="sxs-lookup"><span data-stu-id="56155-126">That understanding will serve as the basis for common language and metrics across multiple initiatives.</span></span>

## <a name="how-can-a-financial-model-be-updated-to-reflect-the-digital-estate"></a><span data-ttu-id="56155-127">如何更新财务模型以反映数字资产？</span><span class="sxs-lookup"><span data-stu-id="56155-127">How can a financial model be updated to reflect the digital estate?</span></span>

<span data-ttu-id="56155-128">分析数字资产可以推动云采用活动。</span><span class="sxs-lookup"><span data-stu-id="56155-128">An analysis of the digital estate will drive cloud adoption activities.</span></span> <span data-ttu-id="56155-129">它还可以通过提供云成本计算模型来告知财务模型，从而促成投资回报 (ROI)。</span><span class="sxs-lookup"><span data-stu-id="56155-129">It will also inform financial models by providing cloud costing models, which will in turn drive the return on investment (ROI).</span></span>

<span data-ttu-id="56155-130">若要完成数字资产分析，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="56155-130">To complete the digital estate analysis, perform the following steps:</span></span>

1. [<span data-ttu-id="56155-131">确定分析方法</span><span class="sxs-lookup"><span data-stu-id="56155-131">Determine analysis approach</span></span>](approach.md)
1. [<span data-ttu-id="56155-132">收集当前状态库存</span><span class="sxs-lookup"><span data-stu-id="56155-132">Collect current state inventory</span></span>](inventory.md)
1. [<span data-ttu-id="56155-133">将数字资产中的资产合理化</span><span class="sxs-lookup"><span data-stu-id="56155-133">Rationalize the assets in the digital estate</span></span>](rationalize.md)
1. [<span data-ttu-id="56155-134">使资产与云计算产品/服务相符以计算定价</span><span class="sxs-lookup"><span data-stu-id="56155-134">Align assets to cloud offerings to calculate pricing</span></span>](calculate.md)

<span data-ttu-id="56155-135">可以修改财务模型和迁移积压工作，以反映合理化的定价资产。</span><span class="sxs-lookup"><span data-stu-id="56155-135">Financial models and migration backlogs can be modified to reflect the rationalized and priced estate.</span></span>

## <a name="next-steps"></a><span data-ttu-id="56155-136">后续步骤</span><span class="sxs-lookup"><span data-stu-id="56155-136">Next steps</span></span>

<span data-ttu-id="56155-137">在开始数字资产规划之前，必须确定要使用哪种方法。</span><span class="sxs-lookup"><span data-stu-id="56155-137">Before digital estate planning can begin, you must determine what approach to use.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="56155-138">数字资产规划方法</span><span class="sxs-lookup"><span data-stu-id="56155-138">Approaches to digital estate planning</span></span>](approach.md)