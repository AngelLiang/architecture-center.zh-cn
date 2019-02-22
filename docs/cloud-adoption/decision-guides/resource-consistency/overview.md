---
title: CAF：资源一致性决策指南
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 了解规划 Azure 迁移时的资源一致性。
author: rotycenh
ms.openlocfilehash: 8170bfd09218a451e086a57e0631b7e567eb2b82
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900516"
---
# <a name="caf-resource-consistency-decision-guide"></a><span data-ttu-id="4b58e-103">CAF：资源一致性决策指南</span><span class="sxs-lookup"><span data-stu-id="4b58e-103">CAF: Resource consistency decision guide</span></span>

<span data-ttu-id="4b58e-104">Azure [订阅设计](../subscriptions/overview.md)定义了如何根据组织的整体结构组织云资产。</span><span class="sxs-lookup"><span data-stu-id="4b58e-104">Azure [subscription design](../subscriptions/overview.md) defines how you organize your cloud assets in relation to your organization's overall structure.</span></span> <span data-ttu-id="4b58e-105">此外，如何整合现有的 IT 管理标准和组织策略，要取决于在订阅中部署和组织云资源的方式。</span><span class="sxs-lookup"><span data-stu-id="4b58e-105">In addition, integrating your existing IT management standards and your organizational policies depends on how you deploy and organize cloud resources within a subscription.</span></span>

<span data-ttu-id="4b58e-106">可用于实现资源部署、分组和管理设计的工具将因云平台而异。</span><span class="sxs-lookup"><span data-stu-id="4b58e-106">The tools available to implement your resource deployment, grouping, and management designs vary by cloud platform.</span></span> <span data-ttu-id="4b58e-107">通常，每个解决方案包括以下功能：</span><span class="sxs-lookup"><span data-stu-id="4b58e-107">In general, each solution includes the following features:</span></span>

- <span data-ttu-id="4b58e-108">订阅或帐户级别下的逻辑分组机制。</span><span class="sxs-lookup"><span data-stu-id="4b58e-108">A logical grouping mechanism below the subscription or account level.</span></span>
- <span data-ttu-id="4b58e-109">使用 API 以编程方式部署资源的功能。</span><span class="sxs-lookup"><span data-stu-id="4b58e-109">The ability to deploy resources programmatically with APIs.</span></span>
- <span data-ttu-id="4b58e-110">用于创建标准化部署的模板。</span><span class="sxs-lookup"><span data-stu-id="4b58e-110">Templates for creating standardized deployments.</span></span>
- <span data-ttu-id="4b58e-111">在订阅、帐户和资源分组级别部署策略规则的功能。</span><span class="sxs-lookup"><span data-stu-id="4b58e-111">The ability to deploy policy rules at the subscription, account, and resource grouping levels.</span></span>

![按复杂性从最低到最高绘制的资源一致性选项，与下面的跳转链接保持一致](../../_images/discovery-guides/discovery-guide-resource-consistency.png)

<span data-ttu-id="4b58e-113">跳转到：[基本分组](#basic-grouping) | [部署一致性](#deployment-consistency) | [策略一致性](#policy-consistency) | [层次结构一致性](#hierarchical-consistency)  | [自动一致性](#automated-consistency)</span><span class="sxs-lookup"><span data-stu-id="4b58e-113">Jump to: [Basic grouping](#basic-grouping) | [Deployment consistency](#deployment-consistency) | [Policy consistency](#policy-consistency) | [Hierarchical consistency](#hierarchical-consistency) | [Automated consistency](#automated-consistency)</span></span>

<span data-ttu-id="4b58e-114">资源部署和分组决策主要受以下因素驱动：迁移后的数字资产规模，不完全适合现有订阅设计方法的业务或环境复杂性，或者在部署资源后逐步强制执行治理的需求。</span><span class="sxs-lookup"><span data-stu-id="4b58e-114">Resource deployment and grouping decisions are primarily driven by these factors: post-migration digital estate size, business or environmental complexity that doesn't fit neatly within your existing subscription design approaches, or the need to enforce governance over time after resources have been deployed.</span></span> <span data-ttu-id="4b58e-115">更高级的资源分组设计需要更多的努力来确保准确的分组，这会增加在更改管理和跟踪上所花费的时间。</span><span class="sxs-lookup"><span data-stu-id="4b58e-115">More advanced resource grouping designs require an increased effort to ensure accurate grouping, and this results in an increase in the time spent on change management and tracking.</span></span>

## <a name="basic-grouping"></a><span data-ttu-id="4b58e-116">基本分组</span><span class="sxs-lookup"><span data-stu-id="4b58e-116">Basic grouping</span></span>

<span data-ttu-id="4b58e-117">在 Azure 中，[资源组](/azure/azure-resource-manager/resource-group-overview#resource-groups)是一种核心资源组织机制，用于在订阅中对资源进行逻辑分组。</span><span class="sxs-lookup"><span data-stu-id="4b58e-117">In Azure, [resource groups](/azure/azure-resource-manager/resource-group-overview#resource-groups) are a core resource organization mechanism to logically group resources within a subscription.</span></span>

<span data-ttu-id="4b58e-118">资源组充当具有共同生命周期或共享管理约束（如策略或基于角色的访问控制 (RBAC) 要求）的资源的容器。</span><span class="sxs-lookup"><span data-stu-id="4b58e-118">Resource groups act as containers for resources with a common lifecycle or shared management constraints such as policy or role-based access control (RBAC) requirements.</span></span> <span data-ttu-id="4b58e-119">资源组无法进行嵌套，资源只能属于一个资源组。</span><span class="sxs-lookup"><span data-stu-id="4b58e-119">Resource groups can't be nested, and resources can only belong to one resource group.</span></span> <span data-ttu-id="4b58e-120">某些操作可对资源组中的所有资源执行操作。</span><span class="sxs-lookup"><span data-stu-id="4b58e-120">Some actions can act on all resources in a resource group.</span></span> <span data-ttu-id="4b58e-121">例如，删除某个资源组会删除该组中的所有资源。</span><span class="sxs-lookup"><span data-stu-id="4b58e-121">For example, deleting a resource group removes all resources within that group.</span></span> <span data-ttu-id="4b58e-122">创建资源组时有常见的模式，通常分为两类：</span><span class="sxs-lookup"><span data-stu-id="4b58e-122">There are common patterns when creating resource groups, commonly divided into two categories:</span></span>

- <span data-ttu-id="4b58e-123">“传统 IT”工作负载：通常按相同生命周期中的项（如某个应用程序）分组。</span><span class="sxs-lookup"><span data-stu-id="4b58e-123">Traditional IT workloads: Most often grouped by items within the same lifecycle, such as an application.</span></span> <span data-ttu-id="4b58e-124">由于可按应用程序分组，因此可以管理每个应用程序。</span><span class="sxs-lookup"><span data-stu-id="4b58e-124">Grouping by application allows for individual application management.</span></span>
- <span data-ttu-id="4b58e-125">“敏捷 IT”工作负载：侧重于面向外部客户的云应用程序。</span><span class="sxs-lookup"><span data-stu-id="4b58e-125">Agile IT workloads: Focus on external customer-facing cloud applications.</span></span> <span data-ttu-id="4b58e-126">资源组通常反映部署的功能层（如 Web 层、应用层）和管理层。</span><span class="sxs-lookup"><span data-stu-id="4b58e-126">These resource groups often reflect the functional layers of deployment (such as web tier or app tier) and management.</span></span>

## <a name="deployment-consistency"></a><span data-ttu-id="4b58e-127">部署一致性</span><span class="sxs-lookup"><span data-stu-id="4b58e-127">Deployment consistency</span></span>

<span data-ttu-id="4b58e-128">基于基础资源分组机制，大多数云平台提供了一个使用模板将资源部署到云环境的系统。</span><span class="sxs-lookup"><span data-stu-id="4b58e-128">Building on top of the base resource grouping mechanism, most cloud platforms provide a system for using templates to deploy your resources to the cloud environment.</span></span> <span data-ttu-id="4b58e-129">在部署工作负载时，可以使用模板创建一致的组织和命名约定，强制执行资源部署和管理设计的这些方面。</span><span class="sxs-lookup"><span data-stu-id="4b58e-129">You can use templates to create consistent organization and naming conventions when deploying workloads, enforcing those aspects of your resource deployment and management design.</span></span>

<span data-ttu-id="4b58e-130">借助 [Azure 资源管理器模板](/azure/azure-resource-manager/resource-group-overview#template-deployment)，可以使用预定的配置和资源组结构以一致的状态重复部署资源。</span><span class="sxs-lookup"><span data-stu-id="4b58e-130">[Azure Resource Manager templates](/azure/azure-resource-manager/resource-group-overview#template-deployment) allow you to repeatedly deploy your resources in a consistent state using a predetermined configuration and resource group structure.</span></span> <span data-ttu-id="4b58e-131">资源管理器模板可帮助用户定义一组标准作为部署的基础。</span><span class="sxs-lookup"><span data-stu-id="4b58e-131">Resource Manager templates help you define a set of standards as a basis for your deployments.</span></span>

<span data-ttu-id="4b58e-132">例如，你可以使用标准模板来部署包含两个虚拟机的 Web 服务器工作负载，将其作为 Web 服务器与负载均衡器结合使用，以管理服务器之间的流量。</span><span class="sxs-lookup"><span data-stu-id="4b58e-132">For example, you can have a standard template for deploying a web server workload that contains two virtual machines as web servers combined with a load balancer to manage traffic between the servers.</span></span> <span data-ttu-id="4b58e-133">然后，只要需要新的 Web 服务器工作负载，就可以重用此模板来创建结构相同的部署，仅更改所涉及的部署名称和 IP 地址即可。</span><span class="sxs-lookup"><span data-stu-id="4b58e-133">You can then reuse this template to create structurally identical deployments whenever a new web server workload is needed, only changing the deployment name and IP addresses involved.</span></span>

<span data-ttu-id="4b58e-134">请注意，还能够以编程方式部署这些模板并将它们与 CI/CD 系统集成。</span><span class="sxs-lookup"><span data-stu-id="4b58e-134">Note that you can also programmatically deploy these templates and integrate them with your CI/CD systems.</span></span>

## <a name="policy-consistency"></a><span data-ttu-id="4b58e-135">策略一致性</span><span class="sxs-lookup"><span data-stu-id="4b58e-135">Policy consistency</span></span>

<span data-ttu-id="4b58e-136">为确保在创建资源时应用治理策略，资源分组设计的一部分涉及在部署资源时使用通用配置。</span><span class="sxs-lookup"><span data-stu-id="4b58e-136">To ensure that governance policies are applied when resources are created, part of resource grouping design involves using a common configuration when deploying resources.</span></span>

<span data-ttu-id="4b58e-137">通过组合资源组和标准化资源管理器模板，你可以强制执行部署中所需设置的标准，以及对每个资源组或资源应用的 [Azure Policy](/azure/governance/policy/overview) 规则的标准。</span><span class="sxs-lookup"><span data-stu-id="4b58e-137">By combining resource groups and standardized Resource Manager templates, you can enforce standards for what settings are required in a deployment and what [Azure Policy](/azure/governance/policy/overview) rules are applied to each resource group or resource.</span></span>

<span data-ttu-id="4b58e-138">例如，用户可能要求订阅中部署的所有虚拟机都连接到由核心 IT 团队管理的公共子网。</span><span class="sxs-lookup"><span data-stu-id="4b58e-138">For example, you may have a requirement that all virtual machines deployed within your subscription connect to a common subnet managed by your central IT team.</span></span> <span data-ttu-id="4b58e-139">你可以创建一个用于部署工作负载 VM 的标准模板，这将为工作负载创建单独的资源组，并在其中部署所需的 VM。</span><span class="sxs-lookup"><span data-stu-id="4b58e-139">You can create a standard template for deploying workload VMs which would create a separate resource group for the workload and deploy the required VMs there.</span></span> <span data-ttu-id="4b58e-140">此资源组将具有仅允许资源组中的网络接口加入共享子网的策略规则。</span><span class="sxs-lookup"><span data-stu-id="4b58e-140">This resource group would have a policy rule to only allow network interfaces within the resource group to be joined to the shared subnet.</span></span>

<span data-ttu-id="4b58e-141">有关在云部署中强制执行策略决策的更深入讨论，请参阅[策略实施](../policy-enforcement/overview.md)。</span><span class="sxs-lookup"><span data-stu-id="4b58e-141">For a more in-depth discussion of enforcing your policy decisions within a cloud deployment, see [Policy enforcement](../policy-enforcement/overview.md).</span></span>

## <a name="hierarchical-consistency"></a><span data-ttu-id="4b58e-142">层次结构一致性</span><span class="sxs-lookup"><span data-stu-id="4b58e-142">Hierarchical consistency</span></span>

<span data-ttu-id="4b58e-143">随着云资产规模的增长，相比使用 Azure 企业协议的企业/部门/帐户/订阅层次结构支持的需求，用户可能需要支持更为复杂的治理需求。</span><span class="sxs-lookup"><span data-stu-id="4b58e-143">As the size of your cloud estate grows, you may need to support more complicated governance requirements than can be supported using the Azure Enterprise Agreement's Enterprise/Department/Account/Subscription hierarchy.</span></span> <span data-ttu-id="4b58e-144">使用资源组，你可以在组织内支持其他级别的层次结构，在资源组级别应用 Azure Policy 规则和访问控制。</span><span class="sxs-lookup"><span data-stu-id="4b58e-144">Resource groups allows you to support additional levels of hierarchy within your organization, applying Azure Policy rules and access controls at a resource group level.</span></span>

<span data-ttu-id="4b58e-145">[Azure 管理组](../subscriptions/overview.md#management-groups)通过在企业协议结构之上覆盖备用层次结构可以支持更复杂的组织结构。</span><span class="sxs-lookup"><span data-stu-id="4b58e-145">[Azure management groups](../subscriptions/overview.md#management-groups) can support more complicated organizational structures by overlaying an alternative hierarchy on top of your enterprise agreement's structure.</span></span> <span data-ttu-id="4b58e-146">这使订阅及其包含的资源能够支持组织的访问控制和策略实施机制，以满足业务组织需求。</span><span class="sxs-lookup"><span data-stu-id="4b58e-146">This allows subscriptions, and the resources they contain, to support access control and policy enforcement mechanisms organized to match your business organizational requirements.</span></span>

## <a name="automated-consistency"></a><span data-ttu-id="4b58e-147">自动一致性</span><span class="sxs-lookup"><span data-stu-id="4b58e-147">Automated consistency</span></span>

<span data-ttu-id="4b58e-148">对于大型云部署，全局治理变得更加重要和复杂。</span><span class="sxs-lookup"><span data-stu-id="4b58e-148">For large cloud deployments, global governance becomes both more important and more complex.</span></span> <span data-ttu-id="4b58e-149">在部署资源时自动应用和强制执行治理要求，以及满足现有部署的更新要求，这一点至关重要。</span><span class="sxs-lookup"><span data-stu-id="4b58e-149">It is crucial to automatically apply and enforce governance requirements when deploying resources, as well as meet updated requirements for existing deployments.</span></span>

<span data-ttu-id="4b58e-150">[Azure 蓝图](/azure/governance/blueprints/overview)使组织能够支持 Azure 中大型云资产的全局治理。</span><span class="sxs-lookup"><span data-stu-id="4b58e-150">[Azure Blueprints](/azure/governance/blueprints/overview) enable organizations to support global governance of large cloud estates in Azure.</span></span> <span data-ttu-id="4b58e-151">蓝图超越了标准 Azure 资源管理器模板提供的功能，可创建能够部署资源和应用策略规则的完整部署业务流程。</span><span class="sxs-lookup"><span data-stu-id="4b58e-151">Blueprints move beyond the capabilities provided by standard Azure Resource Manager templates to create complete deployment orchestrations capable of deploying resources and applying policy rules.</span></span> <span data-ttu-id="4b58e-152">蓝图支持版本控制，能够对使用蓝图的所有订阅应用更新，以及锁定已部署订阅，以避免未经授权创建和修改资源。</span><span class="sxs-lookup"><span data-stu-id="4b58e-152">Blueprints supports versioning, the ability to make apply updates to all subscriptions where the blueprint was used, and the ability to lock down deployed subscriptions to avoid the unauthorized creation and modification of resources.</span></span>

<span data-ttu-id="4b58e-153">使用这些部署包，IT 和开发团队能够快速部署新工作负载和网络资产，顺应不断变化的组织策略要求。</span><span class="sxs-lookup"><span data-stu-id="4b58e-153">These deployment packages allow IT and development teams to rapidly deploy new workloads and networking assets that comply with changing organizational policy requirements.</span></span> <span data-ttu-id="4b58e-154">此外，蓝图还可以整合到 CI/CD 管道中，以便在更新时将修订的治理标准应用于部署。</span><span class="sxs-lookup"><span data-stu-id="4b58e-154">Blueprints can also be integrated into CI/CD pipelines to apply revised governance standards to deployments as they are updated.</span></span>

## <a name="next-steps"></a><span data-ttu-id="4b58e-155">后续步骤</span><span class="sxs-lookup"><span data-stu-id="4b58e-155">Next steps</span></span>

<span data-ttu-id="4b58e-156">了解如何使用资源命名和标记进一步组织和管理云资源。</span><span class="sxs-lookup"><span data-stu-id="4b58e-156">Learn how resource naming and tagging are used to further organize and manage your cloud resources.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="4b58e-157">资源命名和标记</span><span class="sxs-lookup"><span data-stu-id="4b58e-157">Resource naming and tagging</span></span>](../resource-tagging/overview.md)
