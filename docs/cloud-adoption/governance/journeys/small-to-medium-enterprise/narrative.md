---
title: CAF：中小型企业 - 治理策略背后的初始说明
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 此说明为小中型企业治理过程建立了一个用例。
author: BrianBlanchard
ms.openlocfilehash: 6173b01f310169017761110d6641acfa51d45df8
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900678"
---
# <a name="small-to-medium-enterprise-the-narrative-behind-the-governance-strategy"></a><span data-ttu-id="7c1c7-103">中小型企业：治理策略背后的说明</span><span class="sxs-lookup"><span data-stu-id="7c1c7-103">Small-to-medium enterprise: The narrative behind the governance strategy</span></span>

<span data-ttu-id="7c1c7-104">以下说明介绍了针对[小中型企业治理过程](./overview.md)的用例。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-104">The following narrative describes the use case for the [small-to-medium enterprise governance journey](./overview.md).</span></span> <span data-ttu-id="7c1c7-105">实施此过程之前，请务必理解本说明中反映的假设和推理。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-105">Before implementing the journey, it’s important to understand the assumptions and reasoning that are reflected in this narrative.</span></span> <span data-ttu-id="7c1c7-106">这有助于你更好地使治理策略与自己组织的发展过程保持一致。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-106">Then you can better align the governance strategy to your own organization’s journey.</span></span>

## <a name="back-story"></a><span data-ttu-id="7c1c7-107">背景情况</span><span class="sxs-lookup"><span data-stu-id="7c1c7-107">Back story</span></span>

<span data-ttu-id="7c1c7-108">今年年初，董事会计划通过多种方式为企业注入活力。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-108">The board of directors started the year with plans to energize the business in several ways.</span></span> <span data-ttu-id="7c1c7-109">他们正在推动领导层，以期改善客户体验，进而获得市场份额。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-109">They are pushing leadership to improve customer experiences to gain market share.</span></span> <span data-ttu-id="7c1c7-110">此外，他们还推动新产品和新服务，使公司成为业内的思想领袖。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-110">They are also pushing for new products and services that will position the company as a thought leader in the industry.</span></span> <span data-ttu-id="7c1c7-111">他们还同时发起了一项减少浪费和削减不必要成本的活动。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-111">They also initiated a parallel effort to reduce waste and cut unnecessary costs.</span></span> <span data-ttu-id="7c1c7-112">尽管充满压力，但董事会和领导层的行动表明，这一工作正在全力整合资本用于未来的发展。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-112">Though intimidating, the actions of the board and leadership show that this effort is focusing as much capital as possible on future growth.</span></span>

<span data-ttu-id="7c1c7-113">过去，公司的 CIO 一直被排除在这些策略对话之外。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-113">In the past, the company’s CIO has been excluded from these strategic conversations.</span></span> <span data-ttu-id="7c1c7-114">然而，由于未来愿景本质上与技术发展相关，因此，IT 人员在会议桌上也有了一席之地，帮助指导这些宏大的计划。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-114">However, because the future vision is intrinsically linked to technical growth, IT has a seat at the table to help guide these big plans.</span></span> <span data-ttu-id="7c1c7-115">IT 现在应能够以新的方式提供支持。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-115">IT is now expected to deliver in new ways.</span></span> <span data-ttu-id="7c1c7-116">该团队并没有真正为这些变化做好准备，并且很可能对学习曲线感到困扰。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-116">The team isn’t really prepared for these changes and is likely to struggle with the learning curve.</span></span>

## <a name="business-characteristics"></a><span data-ttu-id="7c1c7-117">业务特征</span><span class="sxs-lookup"><span data-stu-id="7c1c7-117">Business characteristics</span></span>

<span data-ttu-id="7c1c7-118">该公司具有以下业务配置文件：</span><span class="sxs-lookup"><span data-stu-id="7c1c7-118">The company has the following business profile:</span></span>

- <span data-ttu-id="7c1c7-119">所有销售和运营都集中在一个国家/地区，全球客户的比例很低。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-119">All sales and operations reside in a single country, with a low percentage of global customers.</span></span>
- <span data-ttu-id="7c1c7-120">企业作为单个业务单元运营，预算按照部门职能（包括销售、营销、运营和 IT）来制定。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-120">The business operates as a single business unit, with budget aligned to functions, including Sales, Marketing, Operations, and IT.</span></span>
- <span data-ttu-id="7c1c7-121">公司将大多数 IT 视为资本流失或成本中心。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-121">The business views most of IT as a capital drain or a cost center.</span></span>

## <a name="current-state"></a><span data-ttu-id="7c1c7-122">当前状态</span><span class="sxs-lookup"><span data-stu-id="7c1c7-122">Current state</span></span>

<span data-ttu-id="7c1c7-123">以下是该公司 IT 和云运营的当前状态：</span><span class="sxs-lookup"><span data-stu-id="7c1c7-123">Here is the current state of the company’s IT and cloud operations:</span></span>

- <span data-ttu-id="7c1c7-124">IT 运营两个托管的基础结构环境。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-124">IT operates two hosted infrastructure environments.</span></span> <span data-ttu-id="7c1c7-125">一个环境包含生产资产。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-125">One environment contains production assets.</span></span> <span data-ttu-id="7c1c7-126">另一个环境包含灾难恢复和一些开发/测试资产。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-126">The second environment contains disaster recovery and some dev/test assets.</span></span> <span data-ttu-id="7c1c7-127">这些环境由两个不同的提供商托管。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-127">These environments are hosted by two different providers.</span></span> <span data-ttu-id="7c1c7-128">IT 将这些两个数据中心分别称为“生产”和“灾难恢复”。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-128">IT refers to these two datacenters as Prod and DR respectively.</span></span>
- <span data-ttu-id="7c1c7-129">IT 通过将所有最终用户的电子邮件帐户迁移到 Office 365 进入云。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-129">IT entered the cloud by migrating all end-user email accounts to Office 365.</span></span> <span data-ttu-id="7c1c7-130">此迁移已在六个月前完成。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-130">This migration was completed six months ago.</span></span> <span data-ttu-id="7c1c7-131">仅少量 IT 资产已部署到云。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-131">Few other IT assets have been deployed to the cloud.</span></span>
- <span data-ttu-id="7c1c7-132">应用程序开发团队正致力于以开发/测试的身份了解云原生能力。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-132">The application development teams are working in a dev/test capacity to learn about cloud native capabilities.</span></span>
- <span data-ttu-id="7c1c7-133">商业智能 (BI) 团队正在尝试云中的大数据和新平台上的数据管理。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-133">The business intelligence (BI) team is experimenting with big data in the cloud and curation of data on new platforms.</span></span>
- <span data-ttu-id="7c1c7-134">该公司有一个松散定义的策略，规定客户个人身份信息 (PII) 和财务数据不能托管在云中，这限制了当前部署中的关键任务型应用程序。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-134">The company has a loosely defined policy stating that customer personally identifiable information (PII) and financial data cannot be hosted in the cloud, which limits mission-critical applications in the current deployments.</span></span>
- <span data-ttu-id="7c1c7-135">IT 投资主要由资本支出 (CapEx) 控制。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-135">IT investments are controlled largely by capital expense (CapEx).</span></span> <span data-ttu-id="7c1c7-136">这些投资每年计划一次。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-136">Those investments are planned yearly.</span></span> <span data-ttu-id="7c1c7-137">在过去几年中，投资只包括基本的维护要求。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-137">In the past several years, investments have included little more than basic maintenance requirements.</span></span>

## <a name="future-state"></a><span data-ttu-id="7c1c7-138">未来状态</span><span class="sxs-lookup"><span data-stu-id="7c1c7-138">Future state</span></span>

<span data-ttu-id="7c1c7-139">预计在未来几年会有以下改变：</span><span class="sxs-lookup"><span data-stu-id="7c1c7-139">The following changes are anticipated over the next several years:</span></span>

- <span data-ttu-id="7c1c7-140">CIO 正在审查关于 PII 和财务数据的策略，以实现未来的状态目标。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-140">The CIO is reviewing the policy on PII and financial data to allow for the future state goals.</span></span>
- <span data-ttu-id="7c1c7-141">应用程序开发和 BI 团队希望基于客户参与和新产品的愿景，在未来 24 个月内向生产环境发布基于云的解决方案。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-141">The application development and BI teams want to release cloud-based solutions to production over the next 24 months based on the vision for customer engagement and new products.</span></span>
- <span data-ttu-id="7c1c7-142">今年，IT 团队会通过将 2,000 个 VM 迁移到云中，完成停用 DR 数据中心的灾难恢复工作负载。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-142">This year, the IT team will finish retiring the disaster recovery workloads of the DR datacenter by migrating 2,000 VMs to the cloud.</span></span> <span data-ttu-id="7c1c7-143">预计这将在未来五年节省 2,500 万美元的成本。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-143">This is expected to produce an estimated $25M USD cost savings over the next five years.</span></span>
    <span data-ttu-id="7c1c7-144">![相比于 Azure 成本，本地成本未来五年的回报为 2,500 万美元](../../../_images/governance/calculator-small-to-medium-enterprise.png)</span><span class="sxs-lookup"><span data-stu-id="7c1c7-144">![On-premise costs versus Azure costs demonstrating a return of $25M USD over the next five years](../../../_images/governance/calculator-small-to-medium-enterprise.png)</span></span>
- <span data-ttu-id="7c1c7-145">该公司计划通过在 IT 内将已承诺的 CapEx 重置为运营费用 (OpEx) 来变更 IT 投资的方式。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-145">The company plans to change how it makes IT investments by repositioning the committed CapEx as an operational expense (OpEx) within IT.</span></span> <span data-ttu-id="7c1c7-146">此变更将提供更好的成本控制，并使 IT 加速其他规划工作。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-146">This change will provide greater cost control and enable IT to accelerate other planned efforts.</span></span>

## <a name="next-steps"></a><span data-ttu-id="7c1c7-147">后续步骤</span><span class="sxs-lookup"><span data-stu-id="7c1c7-147">Next steps</span></span>

<span data-ttu-id="7c1c7-148">该公司已制定出公司策略，以形成治理实施。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-148">The company has developed a corporate policy to shape the governance implementation.</span></span> <span data-ttu-id="7c1c7-149">该公司策略将推动很多技术决策。</span><span class="sxs-lookup"><span data-stu-id="7c1c7-149">The corporate policy drives many of the technical decisions.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="7c1c7-150">查看初始公司策略</span><span class="sxs-lookup"><span data-stu-id="7c1c7-150">Review the initial corporate policy</span></span>](./initial-corporate-policy.md)
