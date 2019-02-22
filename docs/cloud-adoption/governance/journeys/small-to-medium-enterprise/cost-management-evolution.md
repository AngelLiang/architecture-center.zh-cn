---
title: 'CAF：中小型企业 - 成本管理演化  '
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 2/11/2019
description: 阐释：中小型企业 - 成本管理演化
author: BrianBlanchard
ms.openlocfilehash: ca070bfca3efbf9548faa8cf28a1adffefd4a33a
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900460"
---
# <a name="small-to-medium-enterprise-cost-management-evolution"></a><span data-ttu-id="dc7c6-103">中小型企业：成本管理演变</span><span class="sxs-lookup"><span data-stu-id="dc7c6-103">Small-to-medium enterprise: Cost Management evolution</span></span>

<span data-ttu-id="dc7c6-104">本文通过将成本控制添加到治理 MVP 中，对叙述性内容进行了演化。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-104">This article evolves the narrative by adding cost controls to the governance MVP.</span></span>

## <a name="evolution-of-the-narrative"></a><span data-ttu-id="dc7c6-105">叙述性内容的演化</span><span class="sxs-lookup"><span data-stu-id="dc7c6-105">Evolution of the narrative</span></span>

<span data-ttu-id="dc7c6-106">采用率已超过治理 MVP 中定义的成本容差指标。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-106">Adoption has grown beyond the cost tolerance indicator defined in the governance MVP.</span></span> <span data-ttu-id="dc7c6-107">这是一件好事，因为这符合从“DR”数据中心迁移后出现的结果。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-107">This is a good thing, as it corresponds with migrations from the "DR" datacenter.</span></span> <span data-ttu-id="dc7c6-108">现在，支出增加证明了云治理团队的时间投入是合理的。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-108">The increase in spending now justifies an investment of time from the Cloud Governance team.</span></span>

### <a name="evolution-of-the-current-state"></a><span data-ttu-id="dc7c6-109">当前状态的演化</span><span class="sxs-lookup"><span data-stu-id="dc7c6-109">Evolution of the current state</span></span>

<span data-ttu-id="dc7c6-110">在此叙述性内容的前一阶段，IT 已经完全停用了 DR 数据中心。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-110">In the previous phase of this narrative, IT had retired 100% of the DR datacenter.</span></span> <span data-ttu-id="dc7c6-111">应用程序开发团队和 BI 团队已对生产流量做好准备。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-111">The application development and BI teams were ready for production traffic.</span></span>

<span data-ttu-id="dc7c6-112">此后，一些变化将会影响治理：</span><span class="sxs-lookup"><span data-stu-id="dc7c6-112">Since then, some things have changed that will affect governance:</span></span>

- <span data-ttu-id="dc7c6-113">迁移团队已开始将 VM 迁移到生产数据中心外。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-113">The migration team has begun migrating VMs out of the production datacenter.</span></span>
- <span data-ttu-id="dc7c6-114">应用程序开发团队正在积极通过 CI/CD 管道将生产应用程序推送到云中。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-114">The application development teams is actively pushing production applications to the cloud through CI/CD pipelines.</span></span> <span data-ttu-id="dc7c6-115">这些应用程序可以根据用户需求相应缩放。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-115">Those applications can reactively scale with user demands.</span></span>
- <span data-ttu-id="dc7c6-116">IT 中的商业智能团队已在云中交付了许多预测分析工具。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-116">The business intelligence team within IT has delivered a number of predictive analytics tools in the cloud.</span></span> <span data-ttu-id="dc7c6-117">云中聚合的数据量持续增长。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-117">the volumes of data aggregated in the cloud continues to grow.</span></span>
- <span data-ttu-id="dc7c6-118">所有此类增长都为实现承诺的业务成效提供支持。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-118">All of this growth supports committed business outcomes.</span></span> <span data-ttu-id="dc7c6-119">不过，成本已开始迅速增加。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-119">However, costs have begun to mushroom.</span></span> <span data-ttu-id="dc7c6-120">预估预算的增长速度快于预期。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-120">Projected budgets are growing faster than expected.</span></span> <span data-ttu-id="dc7c6-121">CFO 需要使用改进后的成本管理方法。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-121">The CFO needs improved approaches to managing costs.</span></span>

### <a name="evolution-of-the-future-state"></a><span data-ttu-id="dc7c6-122">未来状态的演化</span><span class="sxs-lookup"><span data-stu-id="dc7c6-122">Evolution of the future state</span></span>

<span data-ttu-id="dc7c6-123">成本监视和报告要被添加到云解决方案中。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-123">Cost monitoring and reporting is to be added to the cloud solution.</span></span> <span data-ttu-id="dc7c6-124">IT 仍负责成本结算。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-124">IT is still serving as a cost clearing house.</span></span> <span data-ttu-id="dc7c6-125">也就是说，云服务付款继续来自 IT 采购。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-125">This means that payment for cloud services continues to come from IT procurement.</span></span> <span data-ttu-id="dc7c6-126">不过，报告应将直接运营费用与产生云成本的职能部门关联起来。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-126">However, reporting should tie direct operational expenses to the functions that are consuming the cloud costs.</span></span> <span data-ttu-id="dc7c6-127">此模型称为云核算的“重现”模型。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-127">This model is referred to as "Show Back" model to cloud accounting.</span></span>

<span data-ttu-id="dc7c6-128">当前状态和未来状态的变化带来需要新策略声明的新风险。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-128">The changes to current and future state expose new risks that will require new policy statements.</span></span>

## <a name="evolution-of-tangible-risks"></a><span data-ttu-id="dc7c6-129">有形风险的演化</span><span class="sxs-lookup"><span data-stu-id="dc7c6-129">Evolution of tangible risks</span></span>

<span data-ttu-id="dc7c6-130">**预算控制**：自助服务功能本身所具有的风险是，导致新平台的成本意外过多。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-130">**Budget control**: There is an inherent risk that self-service capabilities will result in excessive and unexpected costs on the new platform.</span></span> <span data-ttu-id="dc7c6-131">为了确保继续与计划预算保持一致，必须建立监视成本和缓解持续成本风险的治理流程。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-131">Governance processes for monitoring costs and mitigating ongoing cost risks must be in place to ensure continued alignment with the planned budget.</span></span>

<span data-ttu-id="dc7c6-132">此业务风险可以扩大成为几种技术风险：</span><span class="sxs-lookup"><span data-stu-id="dc7c6-132">This business risk can be expanded into a few technical risks:</span></span>

- <span data-ttu-id="dc7c6-133">实际成本可能超出计划。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-133">Actual costs might exceed the plan.</span></span>
- <span data-ttu-id="dc7c6-134">业务条件发生变化。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-134">Business conditions change.</span></span> <span data-ttu-id="dc7c6-135">如果发生，便会出现业务职能部门需要使用比预期更多的云服务的情况，进而导致支出异常。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-135">When they do, there will be cases when a business function needs to consume more cloud services than expected, leading to spending anomalies.</span></span> <span data-ttu-id="dc7c6-136">存在将这种额外支出视为超额，而不对计划进行必要调整的风险。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-136">There is a risk that this extra spending will be considered overages, as opposed to a necessary adjustment to the plan.</span></span>
- <span data-ttu-id="dc7c6-137">系统可能预配过度，导致支出过高。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-137">Systems could be overprovisioned, resulting in excess spending.</span></span>

## <a name="evolution-of-the-policy-statements"></a><span data-ttu-id="dc7c6-138">策略声明的演化</span><span class="sxs-lookup"><span data-stu-id="dc7c6-138">Evolution of the policy statements</span></span>

<span data-ttu-id="dc7c6-139">以下策略变化有助于缓解新风险和指导如何实现。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-139">The following changes to policy will help mitigate the new risks and guide implementation.</span></span>

1. <span data-ttu-id="dc7c6-140">治理团队应每周对照计划监视一次所有云成本。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-140">All cloud costs should be monitored against plan on a weekly basis by the governance team.</span></span> <span data-ttu-id="dc7c6-141">应每月与 IT 领导层和财务部门共享一次云成本与计划偏差报告。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-141">Reporting on deviations between cloud costs and plan is to be shared with IT leadership and finance monthly.</span></span> <span data-ttu-id="dc7c6-142">IT 领导层和财务部门应每月检查一次所有云成本和计划更新。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-142">All cloud costs and plan updates should be reviewed with IT leadership and finance monthly.</span></span>
2. <span data-ttu-id="dc7c6-143">为了实现问责，必须将所有成本都分配到业务职能部门。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-143">All costs must be allocated to a business function for accountability purposes.</span></span>
3. <span data-ttu-id="dc7c6-144">应持续监视云资产，以发现优化机会。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-144">Cloud assets should be continually monitored for optimization opportunities.</span></span>
4. <span data-ttu-id="dc7c6-145">云治理工具必须将“资产规模”选项限制为批准的配置列表。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-145">Cloud Governance tooling must limit Asset sizing options to an approved list of configurations.</span></span> <span data-ttu-id="dc7c6-146">此工具必须确保所有资产都是可发现的，并受成本监视解决方案跟踪。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-146">The tooling must ensure that all assets are discoverable and tracked by the cost monitoring solution.</span></span>
5. <span data-ttu-id="dc7c6-147">在部署计划期间，应记录与托管生产工作负载相关的任何必需云资源。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-147">During deployment planning, any required cloud resources associated with the hosting of production workloads should be documented.</span></span> <span data-ttu-id="dc7c6-148">此项记录有助于优化预算，并做好采用额外自动化的准备，以防使用费用更高的选项。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-148">This documentation will help refine budgets and prepare additional automation to prevent the use of more expensive options.</span></span> <span data-ttu-id="dc7c6-149">在此过程中，应考虑云提供商提供的不同折扣工具，如预留实例或许可证成本降低。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-149">During this process consideration should be given to different discounting tools offered by the cloud provider, such as reserved instances or license cost reductions.</span></span>
6. <span data-ttu-id="dc7c6-150">为了更好地控制云成本，所有应用程序所有者都必须参加关于优化工作负载的实践培训。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-150">All application owners are required to attend trained on practices for optimizing workloads to better control cloud costs.</span></span>

## <a name="evolution-of-the-best-practices"></a><span data-ttu-id="dc7c6-151">最佳做法的演化</span><span class="sxs-lookup"><span data-stu-id="dc7c6-151">Evolution of the best practices</span></span>

<span data-ttu-id="dc7c6-152">本文的这一部分将把治理 MVP 设计演化为，包括新 Azure 策略和实现 Azure 成本管理。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-152">This section of the article will evolve the governance MVP design to include new Azure policies and an implementation of Azure Cost Management.</span></span> <span data-ttu-id="dc7c6-153">这两项设计变更将共同实现新的企业策略声明。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-153">Together, these two design changes will fulfill the new corporate policy statements.</span></span>

1. <span data-ttu-id="dc7c6-154">实现 Azure 成本管理</span><span class="sxs-lookup"><span data-stu-id="dc7c6-154">Implement Azure Cost Management</span></span>
    1. <span data-ttu-id="dc7c6-155">建立适当的访问权限范围，与订阅模式和资源一致性规范保持一致。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-155">Establish the right scope of access to align with the subscription pattern and the Resource Consistency discipline.</span></span> <span data-ttu-id="dc7c6-156">假设与先前几篇文章中定义的治理 MVP 保持一致，这就要求对执行简要报告的云治理团队建立“注册帐户范围”访问权限。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-156">Assuming alignment with the governance MVP defined in prior articles, this requires **Enrollment Account Scope** access for the Cloud Governance team executing on high-level reporting.</span></span> <span data-ttu-id="dc7c6-157">其他非治理团队可能需要“资源组范围”访问权限。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-157">Additional teams outside of governance may require **Resource Group Scope** access.</span></span>
    2. <span data-ttu-id="dc7c6-158">在 Azure 成本管理中设定预算。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-158">Establish a budget in Azure Cost Management.</span></span>
    3. <span data-ttu-id="dc7c6-159">查看并采纳初始建议。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-159">Review and act on initial recommendations.</span></span> <span data-ttu-id="dc7c6-160">建立支持报告的重复流程。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-160">Have a recurring process to support reporting.</span></span>
    4. <span data-ttu-id="dc7c6-161">配置和执行 Azure 成本管理报告，包括初始报告和重复报告。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-161">Configure and execute Azure Cost Management Reporting, both initial and recurring.</span></span>
2. <span data-ttu-id="dc7c6-162">更新 Azure Policy</span><span class="sxs-lookup"><span data-stu-id="dc7c6-162">Update Azure Policy</span></span>
    1. <span data-ttu-id="dc7c6-163">审核标记、管理组、订阅和资源组值，以确定是否有任何偏差。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-163">Audit the tagging, management group, subscription, and resource group values to identify any deviation.</span></span>
    2. <span data-ttu-id="dc7c6-164">设定 SKU 大小选项，将部署限制为采用部署计划文档中列出的 SKU。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-164">Establish SKU size options to limit deployments to SKUs listed in deployment planning documentation.</span></span>

## <a name="conclusion"></a><span data-ttu-id="dc7c6-165">结束语</span><span class="sxs-lookup"><span data-stu-id="dc7c6-165">Conclusion</span></span>

<span data-ttu-id="dc7c6-166">将这些流程和变更添加到治理 MVP 中，有助于缓解许多与成本治理相关的风险。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-166">Adding these processes and changes to the governance MVP helps mitigate many of the risks associated with cost governance.</span></span> <span data-ttu-id="dc7c6-167">它们共同实现了控制成本所需的可见性、问责和优化。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-167">Together, they create the visibility, accountability, and optimization needed to control costs.</span></span>

## <a name="next-steps"></a><span data-ttu-id="dc7c6-168">后续步骤</span><span class="sxs-lookup"><span data-stu-id="dc7c6-168">Next steps</span></span>

<span data-ttu-id="dc7c6-169">随着云采用不断演化和实现更多业务增值，风险和云治理需求也在发生演化。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-169">As cloud adoption continues to evolve and deliver additional business value, risks and cloud governance needs will also evolve.</span></span> <span data-ttu-id="dc7c6-170">对于治理之旅中的这家虚构公司，下一步是使用此治理投资来管理多个云。</span><span class="sxs-lookup"><span data-stu-id="dc7c6-170">For the fictional company in this journey, the next step is using this governance investment to manage multiple clouds.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="dc7c6-171">多云演化</span><span class="sxs-lookup"><span data-stu-id="dc7c6-171">Multi-cloud evolution</span></span>](./multi-cloud-evolution.md)
