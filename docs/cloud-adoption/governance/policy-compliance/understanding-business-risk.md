---
title: CAF：云中的业务风险如何变化？
description: 了解迁移期间的业务风险
author: BrianBlanchard
ms.date: 10/10/2018
ms.openlocfilehash: 458474f3c94c5df4f7ffef439095adf138f33d78
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58246488"
---
<!-- markdownlint-disable MD026 -->

# <a name="how-does-business-risk-change-in-the-cloud"></a><span data-ttu-id="bbc5c-103">云中的业务风险如何变化？</span><span class="sxs-lookup"><span data-stu-id="bbc5c-103">How does business risk change in the cloud?</span></span>

<span data-ttu-id="bbc5c-104">了解业务风险是任何云转换中最重要的要素之一。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-104">An understanding of business risk is one of the most important elements of any cloud transformation.</span></span> <span data-ttu-id="bbc5c-105">风险驱动策略，并影响监视和强制执行要求。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-105">Risk drives policy, it influences monitoring and enforcement requirements.</span></span> <span data-ttu-id="bbc5c-106">风险严重影响如何在本地或云中管理数字资产。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-106">Risk heavily influences how we manage the digital estate, on-premises or in the cloud.</span></span>

<!-- markdownlint-enable MD026 -->

## <a name="relativity-of-risk"></a><span data-ttu-id="bbc5c-107">风险的相对性</span><span class="sxs-lookup"><span data-stu-id="bbc5c-107">Relativity of risk</span></span>

<span data-ttu-id="bbc5c-108">风险是相对的。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-108">Risk is relative.</span></span> <span data-ttu-id="bbc5c-109">对于在封闭的建筑物中拥有少量 IT 资产的小型公司而言，风险极小。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-109">A small company with a few IT assets, in a closed building has little risk.</span></span> <span data-ttu-id="bbc5c-110">只要添加用户和 Internet 连接来访问这些资产，风险就会增大。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-110">Add users and an internet connection with access to those assets, the risk is intensified.</span></span> <span data-ttu-id="bbc5c-111">当这家小公司发展到财富 500 强公司的规模时，风险将呈指数级增长。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-111">When that small company grows to Fortune 500 status, the risks are exponentially greater.</span></span> <span data-ttu-id="bbc5c-112">随着收入、业务流程、员工数量和 IT 资产累积，风险会增加并联合。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-112">As revenue, business process, employee counts, and IT assets accumulate, risks increase and coalesce.</span></span> <span data-ttu-id="bbc5c-113">有助于产生收入的 IT 资产存在的有形风险是，在发生故障时收入流会停止。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-113">IT assets that aid in generating revenue are at tangible risk of stopping that revenue stream in the event of an outage.</span></span> <span data-ttu-id="bbc5c-114">每一刻故障时间都相当于损失。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-114">Every moment of downtime equates to losses.</span></span> <span data-ttu-id="bbc5c-115">同样，随着数据累积，损害客户的风险也在增加。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-115">Likewise, as data accumulates, the risk of harming customers grows.</span></span>

<span data-ttu-id="bbc5c-116">在传统的本地环境中，IT 治理团队专注于评估这些风险。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-116">In the traditional on-premises world, IT governance teams focus on assessing those risks.</span></span> <span data-ttu-id="bbc5c-117">他们制定风险缓解流程，</span><span class="sxs-lookup"><span data-stu-id="bbc5c-117">Creating processes to mitigate the risk.</span></span> <span data-ttu-id="bbc5c-118">并通过部署系统来确保风险缓解措施已成功实现。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-118">Deploying systems to ensure mitigation measures are successful and implemented.</span></span> <span data-ttu-id="bbc5c-119">这样可以平衡在互连的新式业务环境中运营必须承担的风险。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-119">This balances the risks required to operate in a connected, modern business environment.</span></span>

## <a name="understanding-business-risks-in-the-cloud"></a><span data-ttu-id="bbc5c-120">了解云中的业务风险</span><span class="sxs-lookup"><span data-stu-id="bbc5c-120">Understanding business risks in the cloud</span></span>

<span data-ttu-id="bbc5c-121">在转换期间，同样存在相对风险。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-121">During a transformation, the same relative risks can be seen.</span></span>

* <span data-ttu-id="bbc5c-122">在早期试验期间，一些资产得到了部署，但其中几乎没有相关数据。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-122">During early experimentation, a few assets are deployed with little to no relevant data.</span></span> <span data-ttu-id="bbc5c-123">所以风险很小。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-123">The risk is small.</span></span>
* <span data-ttu-id="bbc5c-124">部署首个工作负载后，风险会略有增加。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-124">When the first workload is deployed, risk goes up a little.</span></span> <span data-ttu-id="bbc5c-125">通过选择用户群小且固有风险低的应用程序，可以轻松缓解此风险。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-125">This risk is easily mitigated by choosing an inherently low risk application with a small user base.</span></span>
* <span data-ttu-id="bbc5c-126">随着越来越多的工作负载进入联机状态，每个版本的风险都会发生变化。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-126">As more workloads come online, risks change at each release.</span></span> <span data-ttu-id="bbc5c-127">只要有新应用程序发布，风险就会发生变化。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-127">New apps go live, risks change.</span></span>
* <span data-ttu-id="bbc5c-128">公司发布前 10-20 个应用程序时的风险状况，与第 1000 个应用程序在云中投入生产时的风险状况有很大不同。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-128">When a company brings the first 10-20 applications online, the risk profile is much different that it is when the 1000th applications go into production in the cloud.</span></span>

<span data-ttu-id="bbc5c-129">在传统的本地资产中累积的资产可能会随着时间推移而不断累积。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-129">The assets that accumulated in the traditional, on-premises estate likely accumulated overtime.</span></span> <span data-ttu-id="bbc5c-130">业务和 IT 团队的成熟度可能以类似的方式增长。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-130">The maturity of the business and IT teams was likely growing in a similar fashion.</span></span> <span data-ttu-id="bbc5c-131">这种并行增长往往可能会造成一些不必要的策略包袱。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-131">That parallel growth can tend to create some unnecessary policy baggage.</span></span>

<span data-ttu-id="bbc5c-132">在云转换期间，业务和 IT 团队都有机会重置这些策略，并以成熟的思维模式制定新策略。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-132">During a cloud transformation, both the business and IT teams have an opportunity to reset those policies and build new with a matured mindset.</span></span>

<!-- markdownlint-disable MD026 -->

## <a name="what-is-a-business-risk-mvp"></a><span data-ttu-id="bbc5c-133">什么是业务风险 MVP？</span><span class="sxs-lookup"><span data-stu-id="bbc5c-133">What is a business risk MVP?</span></span>

<span data-ttu-id="bbc5c-134">最简可行产品是一个行业标准术语，用于定义可以产生有形价值的最小单位内容。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-134">**Minimum viable product** is an industry-standard term for defining the smallest unit of something that can produce tangible value.</span></span> <span data-ttu-id="bbc5c-135">在业务风险 MVP 中，团队首先假定一些资产会部署到云环境中。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-135">In a business risk MVP, the team starts with an assumption that some assets will be deployed to a cloud environment.</span></span> <span data-ttu-id="bbc5c-136">此时，这些资产还是未知的。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-136">It's unknown at the time what those assets are.</span></span> <span data-ttu-id="bbc5c-137">这些资产将处理什么类型的数据也是未知的。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-137">It's also unknown what types of data will be processed by those assets.</span></span>

<span data-ttu-id="bbc5c-138">云治理团队可以做出最坏的假设，并将每个可能的策略都映射到云。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-138">The Cloud Governance team could build for the worst-case scenario and map every possible policy to the cloud.</span></span> <span data-ttu-id="bbc5c-139">虽然不建议这样做，但不失为一种选择。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-139">This is not advised, but is an option.</span></span>

<span data-ttu-id="bbc5c-140">反过来说，团队可以采用 MVP 方法，并定义对大多数/所有资产都适用的起点和一组假设。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-140">Conversely, the team could take an MVP approach and define a starting point and set of assumptions that would be true for most/all assets.</span></span>
<span data-ttu-id="bbc5c-141">下面是一些极为基本的示例：</span><span class="sxs-lookup"><span data-stu-id="bbc5c-141">The following are a few extremely basic examples:</span></span>

* <span data-ttu-id="bbc5c-142">所有资产都面临被终止的风险（由于错误、过失或维护所致）</span><span class="sxs-lookup"><span data-stu-id="bbc5c-142">All assets are at risk of being terminated (through error, mistake or maintenance)</span></span>
* <span data-ttu-id="bbc5c-143">所有资产都面临导致支出过多的风险</span><span class="sxs-lookup"><span data-stu-id="bbc5c-143">All assets are at risk of generating too much spending</span></span>
* <span data-ttu-id="bbc5c-144">所有资产都可能因弱密码而遭入侵</span><span class="sxs-lookup"><span data-stu-id="bbc5c-144">All assets could be compromised by weak passwords</span></span>
* <span data-ttu-id="bbc5c-145">任何开放端口向 Internet 公开的所有资产都面临遭入侵的风险</span><span class="sxs-lookup"><span data-stu-id="bbc5c-145">Any asset with all open ports exposed to the internet are at risk of compromise</span></span>

<span data-ttu-id="bbc5c-146">上述示例是为了将 MVP 业务风险上升到理论水平。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-146">The above examples are meant to establish MVP business risks as a theory.</span></span> <span data-ttu-id="bbc5c-147">实际清单对每个环境都是唯一的。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-147">The actual list will be unique to every environment.</span></span>
<span data-ttu-id="bbc5c-148">建立业务风险 MVP 后，就可以将它们转换为[策略](overview.md)来缓解每个风险。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-148">Once the Business Risk MVP is established, they can be converted to [Policies](overview.md) to mitigate each risk.</span></span>

<!-- markdownlint-enable MD026 -->

## <a name="incremental-risk-mitigation"></a><span data-ttu-id="bbc5c-149">增量风险缓解</span><span class="sxs-lookup"><span data-stu-id="bbc5c-149">Incremental risk mitigation</span></span>

<span data-ttu-id="bbc5c-150">假定业务风险 MVP 是起点，治理可以与计划部署（而不是业务发展）并行完善。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-150">Assuming a business risk MVP is the starting point, governance can mature in parallel to planned deployment (as opposed to growing in parallel to business growth).</span></span> <span data-ttu-id="bbc5c-151">这是更稳定的治理完善模型。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-151">This is a much more stable model for governance maturity.</span></span> <span data-ttu-id="bbc5c-152">在每次迭代中，新资产都会进行复制和暂存。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-152">At each iteration, new assets are replicated and staged.</span></span> <span data-ttu-id="bbc5c-153">在每个版本中，可以将工作负载提升到生产。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-153">At each release, workloads are readied for production promotion.</span></span> <span data-ttu-id="bbc5c-154">当然，相对风险可能会随每个周期而增长。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-154">Of course, the relative risk could grow with each cycle.</span></span>

<span data-ttu-id="bbc5c-155">当云治理团队与云采用团队并行运营时，也可以解决业务风险增长问题。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-155">When the Cloud Governance team operates in parallel to the cloud adoption teams, the growth of business risks can likewise be addressed.</span></span> <span data-ttu-id="bbc5c-156">可以根据风险对所有暂存的资产轻松进行分类。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-156">Each asset staged can easily be classified according to risk.</span></span> <span data-ttu-id="bbc5c-157">数据分类文档可以与暂存并行生成或创建。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-157">Data classification documents can be built or created in parallel to staging.</span></span> <span data-ttu-id="bbc5c-158">也能记录风险状况和暴露点。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-158">Risk profile and exposure points can likewise be documented.</span></span> <span data-ttu-id="bbc5c-159">随着时间推移，就可以为整个组织展现极为明确的业务风险视图。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-159">Overtime an extremely clear view of business risk wil come into focus across the organization.</span></span>

<span data-ttu-id="bbc5c-160">通过每次迭代，云治理团队可以与云策略团队合作，以快速沟通新风险、缓解措施、权衡和可能成本。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-160">With each iteration, the Cloud Governance team can work with Cloud Strategy team to quickly communicate new risks, mitigation strategies, tradeoffs, and potential costs.</span></span> <span data-ttu-id="bbc5c-161">这样一来，业务参与者和 IT 领导能够共同制定成熟、明智的决策。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-161">This empowers business participants and IT leaders to partner in mature, well-informed decisions.</span></span> <span data-ttu-id="bbc5c-162">然后，这些决策会指示完善策略。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-162">Those decisions then inform policy maturity.</span></span> <span data-ttu-id="bbc5c-163">必要时，策略变化会产生新的工作项，以完善核心基础结构系统。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-163">When required, the policy changes produce new work items for the maturity of core infrastructure systems.</span></span> <span data-ttu-id="bbc5c-164">如果必须更改暂存系统，云采用团队有足够的时间进行更改，而企业则可以测试暂存系统，并制定用户采用计划。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-164">When changes to staged systems are required, the cloud adoption teams have ample time to make changes, while the business tests the staged systems and develops a user adoption plan.</span></span>

<span data-ttu-id="bbc5c-165">这种方法最大限度地缓解了风险，同时确保团队能够快速行动。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-165">This approach minimizes risks, while empowering the team to move quickly.</span></span> <span data-ttu-id="bbc5c-166">它还确保在部署前及时发现和解决风险。</span><span class="sxs-lookup"><span data-stu-id="bbc5c-166">It also ensures that risks are promptly identified and resolved before deployment.</span></span>

## <a name="next-steps"></a><span data-ttu-id="bbc5c-167">后续步骤</span><span class="sxs-lookup"><span data-stu-id="bbc5c-167">Next steps</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="bbc5c-168">评估风险容忍度</span><span class="sxs-lookup"><span data-stu-id="bbc5c-168">Evaluate risk tolerance</span></span>](./risk-tolerance.md)
