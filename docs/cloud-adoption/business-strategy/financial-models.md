---
title: 为云转换创建财务模型
titleSuffix: Enterprise Cloud Adoption
description: 如何为云转换创建财务模型
author: BrianBlanchard
ms.date: 12/10/2018
ms.openlocfilehash: 9a4d6b7c30d3dc0509a1b26fa8d247ae39ab87ca
ms.sourcegitcommit: e7f8676bbffe500fc4d6deb603b7c0b7ba1884a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2018
ms.locfileid: "53179605"
---
# <a name="enterprise-cloud-adoption-how-to-create-a-financial-model-for-cloud-transformation"></a><span data-ttu-id="75199-103">企业云采用：如何为云转换创建财务模型</span><span class="sxs-lookup"><span data-stu-id="75199-103">Enterprise Cloud Adoption: How to create a financial model for cloud transformation</span></span>

<span data-ttu-id="75199-104">创建一个财务模型用于准确全面反映任何云转换的商业价值的过程可能十分复杂。</span><span class="sxs-lookup"><span data-stu-id="75199-104">Creating a financial model that accurately represents the full business value of any cloud transformation can be complicated.</span></span> <span data-ttu-id="75199-105">每家组织的财务模型和业务理由往往各不相同。</span><span class="sxs-lookup"><span data-stu-id="75199-105">Financial models and business justifications tend to be different from one organization to the next.</span></span> <span data-ttu-id="75199-106">本文将会建立一些公式，并指出在创建财务模型时经常遗漏的一些事项。</span><span class="sxs-lookup"><span data-stu-id="75199-106">This article establishes some formulas and points out a few things that are commonly missed when creating a financial model.</span></span>

## <a name="return-on-investment-roi"></a><span data-ttu-id="75199-107">投资回报 (ROI)</span><span class="sxs-lookup"><span data-stu-id="75199-107">Return on investment (ROI)</span></span>

<span data-ttu-id="75199-108">对于企业决策层和董事会而言，投资回报 (ROI) 往往是一个重要标准。</span><span class="sxs-lookup"><span data-stu-id="75199-108">Return on investment (ROI) is often an important criteria with the C-Suite or the board.</span></span> <span data-ttu-id="75199-109">ROI 用于比较投资有限资本性资源的不同方式。</span><span class="sxs-lookup"><span data-stu-id="75199-109">ROI is used to compare different ways to invest limited capital resources.</span></span> <span data-ttu-id="75199-110">ROI 的公式相当简单。</span><span class="sxs-lookup"><span data-stu-id="75199-110">The formula for ROI is fairly simple.</span></span> <span data-ttu-id="75199-111">但是，创建公式中每个输入所需的详细信息可能并不简单。</span><span class="sxs-lookup"><span data-stu-id="75199-111">The details required to create each input to the formula may not be as simple.</span></span> <span data-ttu-id="75199-112">本质上，ROI 是初始投资产生的回报金额。</span><span class="sxs-lookup"><span data-stu-id="75199-112">Essentially, ROI is the amount of return produced from an initial investment.</span></span> <span data-ttu-id="75199-113">它通常以百分比表示：</span><span class="sxs-lookup"><span data-stu-id="75199-113">Usually it is represented as a percentage:</span></span>

![投资回报 (ROI) = (投资收益 – 投资成本) / 投资成本](../_images/formula-roi.png)

<span data-ttu-id="75199-115"><!-- markdownlint-disable MD036 -->
*ROI = (投资收益 &minus; 初始投资) / 初始投资*
<!-- markdownlint-enable MD036 --></span><span class="sxs-lookup"><span data-stu-id="75199-115"><!-- markdownlint-disable MD036 -->
*ROI = (Gain from Investment &minus; Initial Investment) / Initial Investment*
<!-- markdownlint-enable MD036 --></span></span>

<span data-ttu-id="75199-116">后续部分将逐步讲解计算初始投资和投资收益（收入）所需的数据。</span><span class="sxs-lookup"><span data-stu-id="75199-116">In the next sections, we will walk through the data needed to calculate the initial investment and the gain from investment (earnings).</span></span>

## <a name="calculating-initial-investment"></a><span data-ttu-id="75199-117">计算初始投资</span><span class="sxs-lookup"><span data-stu-id="75199-117">Calculating initial investment</span></span>

<span data-ttu-id="75199-118">初始投资是完成转换所需的资本支出 (CapEx) 和运营支出 (OpEx)。</span><span class="sxs-lookup"><span data-stu-id="75199-118">Initial investment is the capital expenditure (CapEx) and operating expenditure (OpEx) required to complete a transformation.</span></span> <span data-ttu-id="75199-119">成本分类根据会计模型和 CFO 的偏好不同而异。</span><span class="sxs-lookup"><span data-stu-id="75199-119">The classification of costs can vary depending on accounting models and CFO preference.</span></span> <span data-ttu-id="75199-120">但是，此类别包含如下所述的项目：完成转换所需的专业服务、专门在转换期间使用的软件许可证、转换期间的云服务成本，以及转换期间带薪员工的潜在成本。</span><span class="sxs-lookup"><span data-stu-id="75199-120">However, this category would include things like: Professional services to transform, software licenses that are used solely during the transformation, cost of cloud services during the transformation, and potentially the cost of the salaried employees during the transformation.</span></span>

<span data-ttu-id="75199-121">将这些成本相加可以估算出初始投资。</span><span class="sxs-lookup"><span data-stu-id="75199-121">Add these costs together to create an estimate of the initial investment.</span></span>

## <a name="calculating-the-gain-from-investment"></a><span data-ttu-id="75199-122">计算投资收益</span><span class="sxs-lookup"><span data-stu-id="75199-122">Calculating the gain from investment</span></span>

<span data-ttu-id="75199-123">计算投资收益通常需要用到另一个公式，该公式与业务成果密切相关，并与技术更改相关联。</span><span class="sxs-lookup"><span data-stu-id="75199-123">Gain from investment often requires a second formula for calculation, which is very specific to the business outcomes and associated technical changes.</span></span> <span data-ttu-id="75199-124">计算收入不如计算成本缩减那样简单。</span><span class="sxs-lookup"><span data-stu-id="75199-124">Earnings are not as simple as calculating reduction in costs.</span></span>

<span data-ttu-id="75199-125">若要计算收入，需要两个变量：</span><span class="sxs-lookup"><span data-stu-id="75199-125">To calculate earnings, two variables are required:</span></span>

![投资收益 = 收入增量 + 成本增量](../_images/formula-gain-from-investment.png)

<span data-ttu-id="75199-127"><!-- markdownlint-disable MD036 -->
*投资收益 = 收入增量 + 成本增量*
<!-- markdownlint-enable MD036 --></span><span class="sxs-lookup"><span data-stu-id="75199-127"><!-- markdownlint-disable MD036 -->
*Gain from Investment = Revenue Deltas + Cost Deltas*
<!-- markdownlint-enable MD036 --></span></span>

<span data-ttu-id="75199-128">下面描述了每个项目。</span><span class="sxs-lookup"><span data-stu-id="75199-128">Each is described below.</span></span>

## <a name="revenue-delta"></a><span data-ttu-id="75199-129">收入增量</span><span class="sxs-lookup"><span data-stu-id="75199-129">Revenue Delta</span></span>

<span data-ttu-id="75199-130">应该与业务部门合作预测收入增量。</span><span class="sxs-lookup"><span data-stu-id="75199-130">Revenue delta should be forecasted in partnership with the business.</span></span> <span data-ttu-id="75199-131">业务利益干系人议定收入影响后，可以使用该信息来改善收入状况。</span><span class="sxs-lookup"><span data-stu-id="75199-131">Once the business stakeholders agree on a revenue impact, that can be used to improve the earning position.</span></span>

## <a name="cost-deltas"></a><span data-ttu-id="75199-132">成本增量</span><span class="sxs-lookup"><span data-stu-id="75199-132">Cost Deltas</span></span>

<span data-ttu-id="75199-133">成本增量是转换造成的成本增加或减少量。</span><span class="sxs-lookup"><span data-stu-id="75199-133">Cost deltas are the amount of increase or decrease that will come as a result of the transformation.</span></span> <span data-ttu-id="75199-134">有许多独立变量可能会影响成本增量。</span><span class="sxs-lookup"><span data-stu-id="75199-134">There are a number of independent variables that can impact cost deltas.</span></span> <span data-ttu-id="75199-135">收入在很大程度上取决于硬性成本，例如资本支出缩减、成本规避、运营成本缩减和折旧。</span><span class="sxs-lookup"><span data-stu-id="75199-135">Earnings are largely based on hard costs like capital expense reductions, cost avoidance, operational cost reductions, and depreciation reductions.</span></span> <span data-ttu-id="75199-136">以下部分提供了要考虑的成本增量示例。</span><span class="sxs-lookup"><span data-stu-id="75199-136">The following sections are examples of cost deltas to consider.</span></span>

### <a name="depreciation-reductions-or-acceleration"></a><span data-ttu-id="75199-137">折旧和折旧加速</span><span class="sxs-lookup"><span data-stu-id="75199-137">Depreciation reductions or acceleration</span></span>

<span data-ttu-id="75199-138">如需有关折旧的指导，请咨询 CFO 或财务团队。</span><span class="sxs-lookup"><span data-stu-id="75199-138">For guidance on depreciation, speak with the CFO or finance team.</span></span> <span data-ttu-id="75199-139">以下内容是有关折旧主题的一般性参考。</span><span class="sxs-lookup"><span data-stu-id="75199-139">The following is meant to serve as a general reference on the topic of depreciation.</span></span>

<span data-ttu-id="75199-140">投资采购资产时，该笔投资可用于财务或税务目的，以便在该资产的预期寿命内持续产生收益。</span><span class="sxs-lookup"><span data-stu-id="75199-140">When capital is invested in the acquisition of an asset, that investment could be used for financial or tax purposes to produce on-going benefits over the expected lifespan of the asset.</span></span> <span data-ttu-id="75199-141">有些公司将折旧视为正面的税务优势。</span><span class="sxs-lookup"><span data-stu-id="75199-141">Some companies see depreciation as a positive tax advantage.</span></span> <span data-ttu-id="75199-142">还有些公司则将它视为承诺的持续支出，类似于构成年度 IT 预算的其他重复性支出。</span><span class="sxs-lookup"><span data-stu-id="75199-142">Others see it as committed, ongoing expense similar to other recurring expenses attributed to the annual IT budget.</span></span>

<span data-ttu-id="75199-143">请咨询财务办公室，以确定是否可以消除折旧，以及折旧是否能够为成本增量提供积极贡献。</span><span class="sxs-lookup"><span data-stu-id="75199-143">Speak with the finance office to see if elimination of depreciation is possible, and if it would make a positive contribution to cost deltas.</span></span>

### <a name="physical-asset-recovery"></a><span data-ttu-id="75199-144">物理资产恢复</span><span class="sxs-lookup"><span data-stu-id="75199-144">Physical asset recovery</span></span>

<span data-ttu-id="75199-145">在某些情况下，可以出售已淘汰的资产，这也是一种收入来源。</span><span class="sxs-lookup"><span data-stu-id="75199-145">In some cases, retired assets can be sold as a source of revenue.</span></span> <span data-ttu-id="75199-146">通常，为简单起见，这种收入将归并到成本缩减。</span><span class="sxs-lookup"><span data-stu-id="75199-146">Often, this revenue is lumped into cost reduction for simplicity.</span></span> <span data-ttu-id="75199-147">但是，它确实提高了收入，因此可能要计税。</span><span class="sxs-lookup"><span data-stu-id="75199-147">However, it's truly an increase in revenue and may be taxed as such.</span></span> <span data-ttu-id="75199-148">请咨询财务办公室来了解此选项的可行性，以及如何计算产生的收入。</span><span class="sxs-lookup"><span data-stu-id="75199-148">Speak with the finance office to understand the viability of this option and how to account for the resulting revenue.</span></span>

### <a name="operational-cost-reductions"></a><span data-ttu-id="75199-149">运营成本缩减</span><span class="sxs-lookup"><span data-stu-id="75199-149">Operational cost reductions</span></span>

<span data-ttu-id="75199-150">运营企业所需的重复性支出通常称为运营支出 (OpEx)。</span><span class="sxs-lookup"><span data-stu-id="75199-150">Recurring expenses required to operate the business are often referred to as operational expenses (OpEx).</span></span> <span data-ttu-id="75199-151">OpEx 的类别非常广泛。</span><span class="sxs-lookup"><span data-stu-id="75199-151">OpEx is a very broad category.</span></span> <span data-ttu-id="75199-152">在大多数会计模型中，它包括软件许可、托管支出、电费帐单、不动产租赁、散热支出、运营所需的临时员工、设备租赁、更换部件、维护合同、维修服务、业务连续性/灾难恢复 (BC/DR) 服务，以及其他许多不需要资本支出审批的支出。</span><span class="sxs-lookup"><span data-stu-id="75199-152">In most accounting models, it would include software licensing, hosting expenses, electric bills, real estate rentals, cooling expenses, temporary staff required for operations, equipment rentals, replacement parts, maintenance contracts, repair services, Business Continuity/Disaster Recovery (BC/DR) services, and a number of other expenses that don't require capital expense approvals.</span></span>

<span data-ttu-id="75199-153">考虑运营转换旅程时，此类别是最大的收入领域之一。</span><span class="sxs-lookup"><span data-stu-id="75199-153">This category is one of the largest earnings areas when considering an Operational Transformation Journey.</span></span> <span data-ttu-id="75199-154">使此列表变得详尽所投入的时间很少会有浪费。</span><span class="sxs-lookup"><span data-stu-id="75199-154">Time invested in making this list exhaustive is seldom wasted.</span></span> <span data-ttu-id="75199-155">请向 CIO 和财务团队提问，以确保考虑到所有运营成本。</span><span class="sxs-lookup"><span data-stu-id="75199-155">Ask questions of the CIO and finance team to ensure all operational costs are accounted for.</span></span>

### <a name="cost-avoidance"></a><span data-ttu-id="75199-156">成本规避</span><span class="sxs-lookup"><span data-stu-id="75199-156">Cost avoidance</span></span>

<span data-ttu-id="75199-157">如果预期需要投入某项运营支出 (OpEx)，但尚未将它列入已批准的预算，则该支出不能划归到成本缩减类别。</span><span class="sxs-lookup"><span data-stu-id="75199-157">When an operational expense (OpEx) is expected, but not yet in an approved budget, it may not fit into a cost reduction category.</span></span> <span data-ttu-id="75199-158">例如，如果 VMWare 和 Microsoft 许可证需要重新议价并在下一年付费，则它们并不完全算作成本。</span><span class="sxs-lookup"><span data-stu-id="75199-158">For instance, if VMWare and Microsoft licenses need to be renegotiated and paid next year, they aren't fully qualified costs yet.</span></span> <span data-ttu-id="75199-159">在计算成本增量时，这些预期成本的缩减将被视为类似于运营成本。</span><span class="sxs-lookup"><span data-stu-id="75199-159">Reductions in those expected costs would be treated like operational costs for the sake of cost delta calculations.</span></span> <span data-ttu-id="75199-160">但是，正式地讲，在议价和预算审批完成之前，这些成本应该称作“成本规避”。</span><span class="sxs-lookup"><span data-stu-id="75199-160">Informally, however, they should be referred to as "cost avoidance," until negotiation and budget approval is complete.</span></span>

### <a name="soft-cost-reductions"></a><span data-ttu-id="75199-161">软性成本缩减</span><span class="sxs-lookup"><span data-stu-id="75199-161">Soft cost reductions</span></span>

<span data-ttu-id="75199-162">某些公司还可能包含软性成本，例如，运营复杂性的下降，或者运营数据中心所需的全职员工数量的减少。</span><span class="sxs-lookup"><span data-stu-id="75199-162">In some companies, soft costs such as reductions in operational complexity or reduction in full-time staff to operate a datacenter could also be included.</span></span> <span data-ttu-id="75199-163">但是，包括软性成本可能是不明智的。</span><span class="sxs-lookup"><span data-stu-id="75199-163">However, including soft costs can be ill-advised.</span></span> <span data-ttu-id="75199-164">包括软性成本会引入一种未经阐述的假设：成本缩减等于实际成本节省。</span><span class="sxs-lookup"><span data-stu-id="75199-164">Including soft costs inserts an undocumented assumption that the reduction in costs will equate to tangible cost savings.</span></span> <span data-ttu-id="75199-165">技术项目很少会产生实际的软性成本恢复。</span><span class="sxs-lookup"><span data-stu-id="75199-165">Technology projects seldom result in actual soft cost recovery.</span></span>

### <a name="headcount-reductions"></a><span data-ttu-id="75199-166">人数缩减</span><span class="sxs-lookup"><span data-stu-id="75199-166">Headcount reductions</span></span>

<span data-ttu-id="75199-167">员工的时间节省通常包括在软性成本缩减中。</span><span class="sxs-lookup"><span data-stu-id="75199-167">Time savings for staff are often included under soft cost reduction.</span></span> <span data-ttu-id="75199-168">将这些时间节省映射到 IT 薪金或人员配备的实际缩减时，可将它单独计算为人数缩减。</span><span class="sxs-lookup"><span data-stu-id="75199-168">When those time savings map to actual reduction of IT salary or staffing, it could be calculated separately as a headcount reduction.</span></span>

<span data-ttu-id="75199-169">即，本地所需的技能通常映射到云中所需的类似技能集（或更高级别）。</span><span class="sxs-lookup"><span data-stu-id="75199-169">That said, the skills needed on-premises generally map to a similar (or higher level) set of skills needed in the cloud.</span></span> <span data-ttu-id="75199-170">这意味着，在云迁移后一般不会裁员。</span><span class="sxs-lookup"><span data-stu-id="75199-170">That means people generally don't get laid off after a cloud migration.</span></span>

<span data-ttu-id="75199-171">一种例外情况是运营容量由第三方或托管服务提供商 (MSP) 提供。</span><span class="sxs-lookup"><span data-stu-id="75199-171">An exception is when operational capacity is provided by a third party or managed services provider (MSP).</span></span> <span data-ttu-id="75199-172">如果 IT 系统由第三方管理，则运营成本可由云原生解决方案或云原生 MSP 取代。</span><span class="sxs-lookup"><span data-stu-id="75199-172">If IT systems are managed by a third party, the costs to operate could be replaced by a cloud-native solution or cloud-native MSP.</span></span> <span data-ttu-id="75199-173">云原生 MSP 的运营效率可能更高，且成本可能更低。</span><span class="sxs-lookup"><span data-stu-id="75199-173">A cloud native MSP is likely to operate more efficiently and potentially at a lower cost.</span></span> <span data-ttu-id="75199-174">如果是这样，则运营成本缩减应包括在硬性成本计算中。</span><span class="sxs-lookup"><span data-stu-id="75199-174">If that's the case, operational cost reductions belong in the hard cost calculations.</span></span>

### <a name="capital-expense-reductions-or-avoidance"></a><span data-ttu-id="75199-175">资本支出缩减或规避</span><span class="sxs-lookup"><span data-stu-id="75199-175">Capital expense reductions or avoidance</span></span>

<span data-ttu-id="75199-176">资本支出 (CapEx) 与运营支出略有不同。</span><span class="sxs-lookup"><span data-stu-id="75199-176">Capital expenses (CapEx) are slightly different that operational expenses.</span></span> <span data-ttu-id="75199-177">一般而言，此类别由更新周期或数据中心扩建驱动。</span><span class="sxs-lookup"><span data-stu-id="75199-177">Generally, this category is driven by refresh cycles or datacenter expansion.</span></span> <span data-ttu-id="75199-178">数据中心扩建的示例是购置新的高性能群集来托管大数据解决方案或数据仓库，通常属于 CapEx 类别。</span><span class="sxs-lookup"><span data-stu-id="75199-178">An example of a datacenter expansion would be a new high-performance cluster to host a Big Data solution or data warehouse, and would generally fit into a CapEx category.</span></span> <span data-ttu-id="75199-179">更常见的资本支出是基本的更新周期。</span><span class="sxs-lookup"><span data-stu-id="75199-179">More common are the basic refresh cycles.</span></span> <span data-ttu-id="75199-180">某些公司实施严格的硬件更新周期，即，按固定的周期（通常是每隔 3、5 或 8 年）淘汰和更换资产。</span><span class="sxs-lookup"><span data-stu-id="75199-180">Some companies have rigid hardware refresh cycles, meaning assets are retired and replaced on a regular cycle (usually every 3, 5, or 8 years).</span></span> <span data-ttu-id="75199-181">这些周期通常与资产租赁周期或者设备的预测寿命相一致。</span><span class="sxs-lookup"><span data-stu-id="75199-181">These cycles often coincide with asset lease cycles or forecasted lifespan of equipment.</span></span> <span data-ttu-id="75199-182">到达更新周期时，IT 部门将申请 CapEx 来采购新设备。</span><span class="sxs-lookup"><span data-stu-id="75199-182">When a refresh cycle hits, IT draws CapEx to acquire new equipment.</span></span>

<span data-ttu-id="75199-183">如果更新周期经过批准且加入预算，则云转换可以帮助消除该成本。</span><span class="sxs-lookup"><span data-stu-id="75199-183">If a refresh cycle is approved and budgeted, the Cloud Transformation could help eliminate that cost.</span></span> <span data-ttu-id="75199-184">如果更新周期已计划但尚未获批准，则云转换可以实现 CapEx 成本规避。</span><span class="sxs-lookup"><span data-stu-id="75199-184">If a refresh cycle is planned but not yet approved, the Cloud Transformation could create a CapEx cost avoidance.</span></span> <span data-ttu-id="75199-185">这两种情况都会添加到成本增量。</span><span class="sxs-lookup"><span data-stu-id="75199-185">Both scenarios would be added to the cost delta.</span></span>
