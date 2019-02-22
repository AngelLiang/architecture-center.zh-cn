---
title: CAF：大型企业 - 成本管理演变
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 大型企业 - 成本管理演变
author: BrianBlanchard
ms.openlocfilehash: 6bf63e36f6fb19dd0e5f9a16a7f66eb6e0ed54ff
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900547"
---
# <a name="large-enterprise-cost-management-evolution"></a><span data-ttu-id="ac5ff-103">大型企业：成本管理演变</span><span class="sxs-lookup"><span data-stu-id="ac5ff-103">Large enterprise: Cost Management evolution</span></span>

<span data-ttu-id="ac5ff-104">本文将成本控制添加到最小可行产品 (MVP) 治理中，对叙述性内容进行了演化。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-104">This article evolves the narrative by adding cost controls to the minimum viable product (MVP) governance.</span></span>

## <a name="evolution-of-the-narrative"></a><span data-ttu-id="ac5ff-105">叙述性内容的演变</span><span class="sxs-lookup"><span data-stu-id="ac5ff-105">Evolution of the narrative</span></span>

<span data-ttu-id="ac5ff-106">采用率已超过治理 MVP 中定义的容差指标。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-106">Adoption has grown beyond the tolerance indicator defined in the governance MVP.</span></span> <span data-ttu-id="ac5ff-107">现在，支出的增加需要云治理团队投入时间来监视和控制支出模式。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-107">The increases in spending now justifies an investment of time from the Cloud Governance team to monitor and control spending patterns.</span></span>

<span data-ttu-id="ac5ff-108">作为创新的驱动力，IT 不再被企业主要作为成本中心来看待。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-108">As a clear driver of innovation, IT is no longer seen primarily as a cost center.</span></span> <span data-ttu-id="ac5ff-109">鉴于 IT 组织实现了更多的价值，CIO 和 CFO 都认为现在是时候提升 IT 在公司中的地位了。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-109">As the IT organization delivers more value, the CIO and CFO agree that the time is right to evolve the role IT plays in the company.</span></span> <span data-ttu-id="ac5ff-110">CFO 希望测试某个业务部门加拿大分支会计云计算的一种直接支付方法，除此之外还作出许多其他变化。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-110">Amongst other changes, the CFO wants to test a direct pay approach to cloud accounting for the Canadian branch of one of the business units.</span></span> <span data-ttu-id="ac5ff-111">两个已停用的数据中心之一曾是该业务部门加拿大分支的独家托管资产。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-111">One of the two retired datacenters was exclusively hosted assets for that business unit’s Canadian operations.</span></span> <span data-ttu-id="ac5ff-112">在此模型中，将向该业务部门的加拿大子公司直接收取与托管资产相关的运营费用。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-112">In this model, the business unit’s Canadian subsidiary will be billed directly for the operational expenses related to the hosted assets.</span></span> <span data-ttu-id="ac5ff-113">此模型使 IT 将工作重心更多地放在创造价值方面，而不是管理其他人的支出方面。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-113">This model allows IT to focus less on managing someone else’s spending and more on creating value.</span></span> <span data-ttu-id="ac5ff-114">但在开始转变前，成本管理工具需首先就位。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-114">However, before this transition can begin Cost Management tooling needs to be in place.</span></span>

### <a name="evolution-of-current-state"></a><span data-ttu-id="ac5ff-115">当前状态的演变</span><span class="sxs-lookup"><span data-stu-id="ac5ff-115">Evolution of current state</span></span>

<span data-ttu-id="ac5ff-116">在本说明的上一阶段，IT 团队积极地将带有受保护数据的生产工作负荷迁移到到 Azure 中。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-116">In the previous phase of this narrative, the IT team was actively moving production workloads with protected data into Azure.</span></span>

<span data-ttu-id="ac5ff-117">自此，一些事态已改变，将会影响到治理：</span><span class="sxs-lookup"><span data-stu-id="ac5ff-117">Since then, some things have changed that will affect governance:</span></span>

- <span data-ttu-id="ac5ff-118">已从标记为停用状态的两个数据中心删除了 5,000 项资产。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-118">5,000 assets have been removed from the two datacenters flagged for retirement.</span></span> <span data-ttu-id="ac5ff-119">采购和 IT 安全部门现在正在取消设置剩余的实物资产。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-119">Procurement and IT security are now deprovisioning the remaining physical assets.</span></span>
- <span data-ttu-id="ac5ff-120">应用程序开发团队已为部署大量云原生应用程序实现了 CI/CD 管道，对客户体验带来的影响显著。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-120">The application development teams have implemented CI/CD pipelines to deploy a number of cloud native applications, significantly affecting customer experiences.</span></span>
- <span data-ttu-id="ac5ff-121">商业智能团队创建了聚合、特选、见解和预测流程，为业务运营带来了切实的利益。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-121">The BI team has created aggregation, curation, insight, and prediction processes driving tangible benefits for business operations.</span></span> <span data-ttu-id="ac5ff-122">现在，这些预测已转化为富有创意的新产品和新服务。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-122">Those predictions are now empowering creative new products and services.</span></span>

### <a name="evolution-of-future-state"></a><span data-ttu-id="ac5ff-123">未来状态的演变</span><span class="sxs-lookup"><span data-stu-id="ac5ff-123">Evolution of future state</span></span>

- <span data-ttu-id="ac5ff-124">成本监视和报告要被添加到云解决方案中。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-124">Cost monitoring and reporting is to be added to the cloud solution.</span></span> <span data-ttu-id="ac5ff-125">报告应将直接运营费用与产生云成本的职能部门关联起来。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-125">Reporting should tie direct operational expenses to the functions that are consuming the cloud costs.</span></span> <span data-ttu-id="ac5ff-126">额外的报告使 IT 能够监视支出并为成本管理提供技术指导。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-126">Additional reporting should allow IT to monitor spending and provide technical guidance on cost management.</span></span> <span data-ttu-id="ac5ff-127">将直接对加拿大分支计费。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-127">For the Canadian branch, the department will be billed directly.</span></span>

## <a name="evolution-of-tangible-risks"></a><span data-ttu-id="ac5ff-128">有形风险的演变</span><span class="sxs-lookup"><span data-stu-id="ac5ff-128">Evolution of tangible risks</span></span>

<span data-ttu-id="ac5ff-129">**预算控制**：自助服务功能存在固有的风险，会导致新平台出现过多的意外成本。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-129">**Budget control**: There is an inherent risk that self-service capabilities will result in excessive and unexpected costs on the new platform.</span></span> <span data-ttu-id="ac5ff-130">必须建立监测成本和降低当前成本风险的治理流程，确保继续与计划的预算保持一致。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-130">Governance processes for monitoring costs and mitigating ongoing cost risks must be in place to ensure continued alignment with the planned budget.</span></span>

<span data-ttu-id="ac5ff-131">此业务风险可以扩展为几种技术风险：</span><span class="sxs-lookup"><span data-stu-id="ac5ff-131">This business risk can be expanded into a few technical risks:</span></span>

- <span data-ttu-id="ac5ff-132">实际成本超出计划之外。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-132">There is a risk of actual costs exceeding the plan.</span></span>
- <span data-ttu-id="ac5ff-133">业务条件发生变化。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-133">Business conditions change.</span></span> <span data-ttu-id="ac5ff-134">如果发生，便会出现业务职能部门需要使用比预期更多的云服务的情况，进而导致支出异常。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-134">When they do, there will be cases when a business function needs to consume more cloud services than expected, leading to spending anomalies.</span></span> <span data-ttu-id="ac5ff-135">这就存在这将这些额外成本视为超额成本（而不是对计划的必要调整）的风险。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-135">There is a risk that these additional costs would be seen as overages as opposed to a required adjustment to the plan.</span></span> <span data-ttu-id="ac5ff-136">如果成功，这项加拿大试验将有助于降低此风险。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-136">If successful, the Canadian experiment should help mitigate this risk.</span></span>
- <span data-ttu-id="ac5ff-137">系统预配过度，造成超支。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-137">There is a risk of systems being overprovisioned, resulting in excess spending.</span></span>

## <a name="evolution-of-the-policy-statements"></a><span data-ttu-id="ac5ff-138">策略语句的演变</span><span class="sxs-lookup"><span data-stu-id="ac5ff-138">Evolution of the policy statements</span></span>

<span data-ttu-id="ac5ff-139">以下策略的更改将有助于缓解新的风险，同时有助于实施指南。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-139">The following changes to policy will help mitigate the new risks and guide implementation.</span></span>

1. <span data-ttu-id="ac5ff-140">云治理团队应每周按计划对所有云成本进行一次监视。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-140">All cloud costs should be monitored against plan on a weekly basis by the Cloud Governance team.</span></span> <span data-ttu-id="ac5ff-141">应每月与 IT 领导层和财务部门共享一次云成本与计划偏差报告。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-141">Reporting on deviations between cloud costs and plan is to be shared with IT leadership and finance monthly.</span></span> <span data-ttu-id="ac5ff-142">IT 领导层和财务部门应每月检查一次所有云成本和计划更新。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-142">All cloud costs and plan updates should be reviewed with IT leadership and finance monthly.</span></span>
2. <span data-ttu-id="ac5ff-143">为了实现问责，必须将所有成本都分配到业务职能部门。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-143">All costs must be allocated to a business function for accountability purposes</span></span>
3. <span data-ttu-id="ac5ff-144">应持续监视云资产，以发现优化机会。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-144">Cloud assets should be continually monitored for optimization opportunities</span></span>
4. <span data-ttu-id="ac5ff-145">云治理工具必须将“资产规模”选项限制为批准的配置列表。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-145">Cloud Governance tooling must limit Asset sizing options to an approved list of configurations.</span></span> <span data-ttu-id="ac5ff-146">此工具必须确保所有资产都是可发现的，并受成本监视解决方案跟踪。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-146">The tooling must ensure that all assets are discoverable and tracked by the cost monitoring solution.</span></span>
5. <span data-ttu-id="ac5ff-147">在部署计划期间，应记录与托管生产工作负载相关的所有必需的云资源。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-147">During deployment planning, any required cloud resources associated with the hosting of production workloads should be documented.</span></span> <span data-ttu-id="ac5ff-148">此项记录有助于优化预算，并做好采用额外自动化的准备，以防使用费用更高的选项。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-148">This documentation will help refine budgets and prepare additional automations to prevent the use of more expensive options.</span></span> <span data-ttu-id="ac5ff-149">在此过程中，应考虑云提供商提供的不同折扣工具，如预留实例或许可证成本降低。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-149">During this process consideration should be given to different discounting tools offered by the cloud provider, such as Reserved Instances or License cost reductions.</span></span>
6. <span data-ttu-id="ac5ff-150">为了更好地控制云成本，所有应用程序所有者都必须参加关于优化工作负载的实践培训。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-150">All application owners are required to attend trained on practices for optimizing workloads to better control cloud costs.</span></span>

## <a name="evolution-of-the-best-practices"></a><span data-ttu-id="ac5ff-151">最佳做法的演变</span><span class="sxs-lookup"><span data-stu-id="ac5ff-151">Evolution of the best practices</span></span>

<span data-ttu-id="ac5ff-152">本文的这一部分将改进治理 MVP 设计，以包括新的 Azure 策略和 Azure 成本管理的实现。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-152">This section of the article will evolve the governance MVP design to include new Azure policies and an implementation of Azure Cost Management.</span></span> <span data-ttu-id="ac5ff-153">这两项设计变更将共同实现新的公司策略语句。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-153">Together, these two design changes will fulfill the new corporate policy statements.</span></span>

1. <span data-ttu-id="ac5ff-154">对 Azure 企业门户做了一些更改，以便对部门管理员收取加拿大部署的相关费用。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-154">Changes in the Azure Enterprise Portal to bill the Department administrator for the Canadian deployment.</span></span>
2. <span data-ttu-id="ac5ff-155">实现 Azure 成本管理。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-155">Implement Azure Cost Management.</span></span>
    1. <span data-ttu-id="ac5ff-156">建立适当级别的访问范围以与订阅模式和资源分组模式保持一致。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-156">Establish the right level of access scope to align with the subscription pattern and resource grouping pattern.</span></span> <span data-ttu-id="ac5ff-157">假设与先前几篇文章中定义的治理 MVP 保持一致，这就要求对执行高层报告的云治理团队建立“注册帐户范围”访问权限。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-157">Assuming alignment with the governance MVP defined in prior articles, this would require **Enrollment Account Scope** access for the Cloud Governance team executing on high level reporting.</span></span> <span data-ttu-id="ac5ff-158">治理团队之外的其他团队（例如加拿大采购团队）则需要“资源组范围”访问权限。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-158">Additional teams outside of governance, like the Canadian procurement team, will require **Resource Group Scope** access.</span></span>
    2. <span data-ttu-id="ac5ff-159">在 Azure 成本管理中设定预算。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-159">Establish a budget in Azure Cost Management.</span></span>
    3. <span data-ttu-id="ac5ff-160">查看并采纳初始建议。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-160">Review and act on initial recommendations.</span></span> <span data-ttu-id="ac5ff-161">建议制定一个定期过程来支持报告过程。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-161">It's recommended to have a recurring process to support the reporting process.</span></span>
    4. <span data-ttu-id="ac5ff-162">配置和执行 Azure 成本管理报告，包括初始报告和重复报告。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-162">Configure and execute Azure Cost Management Reporting, both initial and recurring.</span></span>
3. <span data-ttu-id="ac5ff-163">更新 Azure Policy。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-163">Update Azure Policy.</span></span>
    1. <span data-ttu-id="ac5ff-164">审核标记、管理组、订阅和资源组值，以确定是否有任何偏差。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-164">Audit tagging, management group, subscription, and resource group values to identify any deviation.</span></span>
    2. <span data-ttu-id="ac5ff-165">设定 SKU 大小选项，将部署限制为采用部署计划文档中列出的 SKU。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-165">Establish SKU size options to limit deployments to SKUs listed in deployment planning documentation.</span></span>

## <a name="conclusion"></a><span data-ttu-id="ac5ff-166">结束语</span><span class="sxs-lookup"><span data-stu-id="ac5ff-166">Conclusion</span></span>

<span data-ttu-id="ac5ff-167">将上述流程和变更添加到治理 MVP 中，有助于降低许多与成本治理相关的风险。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-167">Adding the above processes and changes to the governance MVP helps mitigate many of the risks associated with cost governance.</span></span> <span data-ttu-id="ac5ff-168">它们共同实现了控制成本所需的可见性、问责和优化。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-168">Together, they create the visibility, accountability, and optimization needed to control costs.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ac5ff-169">后续步骤</span><span class="sxs-lookup"><span data-stu-id="ac5ff-169">Next steps</span></span>

<span data-ttu-id="ac5ff-170">随着云采用的不断演进和业务附加值的提升，风险和云治理需求也在不断发展。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-170">As cloud adoption continues to evolve and deliver additional business value, risks and cloud governance needs will also evolve.</span></span> <span data-ttu-id="ac5ff-171">对于治理之旅中的这家虚构公司，下一步是使用此治理投资来管理多个云。</span><span class="sxs-lookup"><span data-stu-id="ac5ff-171">For the fictional company in this journey, the next step is using this governance investment to manage multiple clouds.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="ac5ff-172">多云演变</span><span class="sxs-lookup"><span data-stu-id="ac5ff-172">Multi-cloud evolution</span></span>](./multi-cloud-evolution.md)