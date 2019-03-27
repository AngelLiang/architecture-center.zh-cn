---
title: CAF：Azure 中的资源访问管理
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 介绍 Azure 中的资源访问管理构造：Azure 资源管理器、订阅、资源组和资源
author: petertaylor9999
ms.openlocfilehash: b98cdc94d6d3a37c1e65da1d4de35d5d9520d6eb
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58243198"
---
# <a name="resource-access-management-in-azure"></a><span data-ttu-id="1c3c6-103">Azure 中的资源访问管理</span><span class="sxs-lookup"><span data-stu-id="1c3c6-103">Resource access management in Azure</span></span>

<span data-ttu-id="1c3c6-104">[云治理](../overview.md)概述了云治理的五大规范，其中包括资源管理。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-104">[Cloud Governance](../overview.md) outlines the five disciplines of Cloud Governance, which includes Resource Management.</span></span>  <span data-ttu-id="1c3c6-105">[什么是资源访问治理](overview.md)进一步说明了如何在资源管理规范中嵌入资源访问管理。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-105">[What is resource access governance](overview.md) furthers explains how resource access management fits into the resource management discipline.</span></span> <span data-ttu-id="1c3c6-106">在继续学习如何设计调控模型之前，必须了解 Azure 中的资源访问管理控制措施。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-106">Before you move on to learn how to design a governance model, it's important to understand the resource access management controls in Azure.</span></span> <span data-ttu-id="1c3c6-107">这些资源访问管理控制措施的配置构成了调控模型的基础。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-107">The configuration of these resource access management controls forms the basis of your governance model.</span></span>

<span data-ttu-id="1c3c6-108">首先，细看一下资源是如何在 Azure 中部署的。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-108">Begin by taking a closer look at how resources are deployed in Azure.</span></span>

<!-- markdownlint-disable MD026 -->

## <a name="what-is-an-azure-resource"></a><span data-ttu-id="1c3c6-109">什么是 Azure 资源？</span><span class="sxs-lookup"><span data-stu-id="1c3c6-109">What is an Azure resource?</span></span>

<span data-ttu-id="1c3c6-110">在 Azure 中，术语“资源”是指 Azure 管理的实体。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-110">In Azure, the term **resource** refers to an entity managed by Azure.</span></span> <span data-ttu-id="1c3c6-111">例如，虚拟机、虚拟网络和存储帐户都称为 Azure 资源。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-111">For example, virtual machines, virtual networks, and storage accounts are all referred to as Azure resources.</span></span>

<span data-ttu-id="1c3c6-112">![资源图](../../_images/governance-1-9.png)
图 1：*资源。*</span><span class="sxs-lookup"><span data-stu-id="1c3c6-112">![Diagram of a resource](../../_images/governance-1-9.png)
*Figure 1. A resource.*</span></span>

## <a name="what-is-an-azure-resource-group"></a><span data-ttu-id="1c3c6-113">什么是 Azure 资源组？</span><span class="sxs-lookup"><span data-stu-id="1c3c6-113">What is an Azure resource group?</span></span>

<span data-ttu-id="1c3c6-114">Azure 中的每个资源必须属于某个[资源组](/azure/azure-resource-manager/resource-group-overview#resource-groups)。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-114">Each resource in Azure must belong to a [resource group](/azure/azure-resource-manager/resource-group-overview#resource-groups).</span></span> <span data-ttu-id="1c3c6-115">资源组只是一个逻辑构造，它将多个资源分组到一起，以便可将它们作为单个实体进行管理。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-115">A resource group is simply a logical construct that groups multiple resources together so they can be managed as a single entity.</span></span> <span data-ttu-id="1c3c6-116">举例来说，具有类似生命周期的资源（例如，[n 层应用程序](/azure/architecture/guide/architecture-styles/n-tier)的资源）可以作为一个组进行创建或删除。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-116">For example, resources that share a similar lifecycle, such as the resources for an [n-tier application](/azure/architecture/guide/architecture-styles/n-tier) may be created or deleted as a group.</span></span>

<span data-ttu-id="1c3c6-117">![包含资源的资源组图](../../_images/governance-1-10.png)
图 2：*资源组包含资源。*</span><span class="sxs-lookup"><span data-stu-id="1c3c6-117">![Diagram of a resource group containing a resource](../../_images/governance-1-10.png)
*Figure 2. A resource group contains a resource.*</span></span>

<span data-ttu-id="1c3c6-118">资源组及其包含的资源与 Azure **订阅**相关联。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-118">Resource groups and the resources they contain are associated with an Azure **subscription**.</span></span>

## <a name="what-is-an-azure-subscription"></a><span data-ttu-id="1c3c6-119">什么是 Azure 订阅？</span><span class="sxs-lookup"><span data-stu-id="1c3c6-119">What is an Azure subscription?</span></span>

<span data-ttu-id="1c3c6-120">Azure 订阅与资源组的类似之处在于，它也是一个逻辑构造，可将资源组及其资源分组到一起。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-120">An Azure subscription is similar to a resource group in that it's a logical construct that groups together resource groups and their resources.</span></span> <span data-ttu-id="1c3c6-121">不过，Azure 订阅还与 Azure 资源管理器使用的控制措施相关联。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-121">However, an Azure subscription is also associated with the controls used by Azure Resource Manager.</span></span> <span data-ttu-id="1c3c6-122">这是什么意思呢？</span><span class="sxs-lookup"><span data-stu-id="1c3c6-122">What does this mean?</span></span> <span data-ttu-id="1c3c6-123">请仔细查看 Azure 资源管理器，了解它与 Azure 订阅之间的关系。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-123">Take a closer look at Azure Resource Manager to learn about the relationship between it and an Azure subscription.</span></span>

<span data-ttu-id="1c3c6-124">![Azure 订阅图](../../_images/governance-1-11.png)
图 3：\*Azure 订阅。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-124">![Diagram of an Azure subscription](../../_images/governance-1-11.png)
*Figure 3. An Azure subscription.*</span></span>

## <a name="what-is-azure-resource-manager"></a><span data-ttu-id="1c3c6-125">什么是 Azure 资源管理器？</span><span class="sxs-lookup"><span data-stu-id="1c3c6-125">What is Azure Resource Manager?</span></span>

<span data-ttu-id="1c3c6-126">在 [Azure 的工作原理](../../getting-started/what-is-azure.md)中我们已了解到，Azure 包含一个“前端”，该前端中的许多服务协调 Azure 的所有功能。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-126">In [how does Azure work?](../../getting-started/what-is-azure.md) you learned that Azure includes a "front end" with many services that orchestrate all the functions of Azure.</span></span> <span data-ttu-id="1c3c6-127">其中一项服务是 [Azure 资源管理器](/azure/azure-resource-manager/)，此服务托管客户端用来管理资源的 RESTful API。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-127">One of these services is [Azure Resource Manager](/azure/azure-resource-manager/), and this service hosts the RESTful API used by clients to manage resources.</span></span>

<span data-ttu-id="1c3c6-128">![Azure 资源管理器图](../../_images/governance-1-12.png)
图 4：\*Azure 资源管理器。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-128">![Diagram of Azure Resource Manager](../../_images/governance-1-12.png)
*Figure 4. Azure Resource Manager.*</span></span>

<span data-ttu-id="1c3c6-129">下图显示了三个客户端：[PowerShell](/powershell/azure/overview)、[Azure 门户](https://portal.azure.com)和 [Azure 命令行接口 (CLI)](/cli/azure)：</span><span class="sxs-lookup"><span data-stu-id="1c3c6-129">The following figure shows three clients: [PowerShell](/powershell/azure/overview), the [Azure portal](https://portal.azure.com), and the [Azure command-line interface (CLI)](/cli/azure):</span></span>

<span data-ttu-id="1c3c6-130">![连接到 Azure 资源管理器 API 的 Azure 客户端图](../../_images/governance-1-13.png)
图 5：\*Azure 客户端连接到 Azure 资源管理器 RESTful API。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-130">![Diagram of Azure clients connecting to the Azure Resource Manager API](../../_images/governance-1-13.png)
*Figure 5. Azure clients connect to the Azure Resource Manager RESTful API.*</span></span>

<span data-ttu-id="1c3c6-131">尽管这些客户端使用 RESTful API 连接到 Azure 资源管理器，但 Azure 资源管理器没有直接管理资源的功能。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-131">While these clients connect to Azure Resource Manager using the RESTful API, Azure Resource Manager does not include functionality to manage resources directly.</span></span> <span data-ttu-id="1c3c6-132">Azure 中的大多数资源类型具有自身的[**资源提供程序**](/azure/azure-resource-manager/resource-group-overview#terminology)。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-132">Rather, most resource types in Azure have their own [**resource provider**](/azure/azure-resource-manager/resource-group-overview#terminology).</span></span>

<span data-ttu-id="1c3c6-133">![Azure 资源提供程序](../../_images/governance-1-14.png)
图 6：*Azure 资源提供程序。*</span><span class="sxs-lookup"><span data-stu-id="1c3c6-133">![Azure resource providers](../../_images/governance-1-14.png)
*Figure 6. Azure resource providers.*</span></span>

<span data-ttu-id="1c3c6-134">当客户端发出管理特定资源的请求时，Azure 资源管理器连接到相应资源类型的资源提供程序，以完成请求。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-134">When a client makes a request to manage a specific resource, Azure Resource Manager connects to the resource provider for that resource type to complete the request.</span></span> <span data-ttu-id="1c3c6-135">例如，如果客户端发出管理虚拟机资源的请求，Azure 资源管理器会连接到 Microsoft.Compute 资源提供程序。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-135">For example, if a client makes a request to manage a virtual machine resource, Azure Resource Manager connects to the **Microsoft.Compute** resource provider.</span></span>

<span data-ttu-id="1c3c6-136">![连接到 Microsoft.Compute 资源提供程序的 Azure 资源管理器](../../_images/governance-1-15.png)
图 7：\*Azure 资源管理器连接到 Microsoft.Compute\*\*\*\* 资源提供程序，以管理客户端请求中指定的资源。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-136">![Azure Resource Manager connecting to the Microsoft.Compute resource provider](../../_images/governance-1-15.png)
*Figure 7. Azure Resource Manager connects to the **Microsoft.Compute** resource provider to manage the resource specified in the client request.*</span></span>

<span data-ttu-id="1c3c6-137">Azure 资源管理器要求客户端，必须指定订阅和资源组的标识符，才能管理虚拟机资源。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-137">Azure Resource Manager requires the client to specify an identifier for both the subscription and the resource group in order to manage the virtual machine resource.</span></span>

<span data-ttu-id="1c3c6-138">至此，已了解 Azure 资源管理器的工作原理，再回到有关 Azure 订阅如何与 Azure 资源管理器使用的控制措施相关联的讨论。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-138">Now that you have an understanding of how Azure Resource Manager works, return to the discussion of how an Azure subscription is associated with the controls used by Azure Resource Manager.</span></span> <span data-ttu-id="1c3c6-139">需要先检查一组控制措施，然后 Azure 资源管理器才能执行任何资源管理请求。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-139">Before any resource management request can be executed by Azure Resource Manager, a set of controls are checked.</span></span>

<span data-ttu-id="1c3c6-140">第一个控制措施是请求必须由已验证的用户发出，且 Azure 资源管理器已与 [Azure Active Directory (Azure AD)](/azure/active-directory/) 建立可信关系，以提供用户标识功能。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-140">The first control is that a request must be made by a validated user, and Azure Resource Manager has a trusted relationship with [Azure Active Directory (Azure AD)](/azure/active-directory/) to provide user identity functionality.</span></span>

<span data-ttu-id="1c3c6-141">![Azure Active Directory](../../_images/governance-1-16.png)
图 8：\*Azure Active Directory。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-141">![Azure Active Directory](../../_images/governance-1-16.png)
*Figure 8. Azure Active Directory.*</span></span>

<span data-ttu-id="1c3c6-142">在 Azure AD 中，用户划分到**租户**。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-142">In Azure AD, users are segmented into **tenants**.</span></span> <span data-ttu-id="1c3c6-143">租户是一个逻辑构造，它通常代表一个与组织关联的安全专用的 Azure AD 实例。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-143">A tenant is a logical construct that represents a secure, dedicated instance of Azure AD typically associated with an organization.</span></span> <span data-ttu-id="1c3c6-144">每个订阅与一个 Azure AD 租户相关联。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-144">Each subscription is associated with an Azure AD tenant.</span></span>

<span data-ttu-id="1c3c6-145">![与订阅关联的 Azure AD 租户](../../_images/governance-1-17.png)
图 9：*与订阅关联的 Azure AD 租户。*</span><span class="sxs-lookup"><span data-stu-id="1c3c6-145">![An Azure AD tenant associated with a subscription](../../_images/governance-1-17.png)
*Figure 9. An Azure AD tenant associated with a subscription.*</span></span>

<span data-ttu-id="1c3c6-146">管理特定订阅中的资源的每个客户端请求要求用户在关联的 Azure AD 租户中有一个帐户。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-146">Each client request to manage a resource in a particular subscription requires that the user has an account in the associated Azure AD tenant.</span></span>

<span data-ttu-id="1c3c6-147">下一个控制措施是检查用户是否拥有发出请求的足够权限。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-147">The next control is a check that the user has sufficient permission to make the request.</span></span> <span data-ttu-id="1c3c6-148">权限是使用[基于角色的访问控制 (RBAC)](/azure/role-based-access-control/) 分配给用户的。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-148">Permissions are assigned to users using [role-based access control (RBAC)](/azure/role-based-access-control/).</span></span>

<span data-ttu-id="1c3c6-149">![分配到 RBAC 角色的用户](../../_images/governance-1-18.png)
图 10：*为租户中的每个用户分配一个或多个 RBAC 角色。*</span><span class="sxs-lookup"><span data-stu-id="1c3c6-149">![Users assigned to RBAC roles](../../_images/governance-1-18.png)
*Figure 10. Each user in the tenant is assigned one or more RBAC roles.*</span></span>

<span data-ttu-id="1c3c6-150">RBAC 角色指定用户对特定的资源拥有的一组权限。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-150">An RBAC role specifies a set of permissions a user may take on a specific resource.</span></span> <span data-ttu-id="1c3c6-151">将角色分配给用户时，会应用这些权限。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-151">When the role is assigned to the user, those permissions are applied.</span></span> <span data-ttu-id="1c3c6-152">例如，[内置的**所有者**角色](/azure/role-based-access-control/built-in-roles#owner)允许用户对资源执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-152">For example, the [built-in **owner** role](/azure/role-based-access-control/built-in-roles#owner) allows a user to perform any action on a resource.</span></span>

<span data-ttu-id="1c3c6-153">下一个控制措施是检查为 [Azure 资源策略](/azure/governance/policy/)指定的设置是否允许该请求。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-153">The next control is a check that the request is allowed under the settings specified for [Azure resource policy](/azure/governance/policy/).</span></span> <span data-ttu-id="1c3c6-154">Azure 资源策略指定允许对特定资源执行的操作。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-154">Azure resource policies specify the operations allowed for a specific resource.</span></span> <span data-ttu-id="1c3c6-155">例如，Azure 资源策略可以指定只允许用户部署特定类型的虚拟机。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-155">For example, an Azure resource policy can specify that users are only allowed to deploy a specific type of virtual machine.</span></span>

<span data-ttu-id="1c3c6-156">![Azure 资源策略](../../_images/governance-1-19.png)
图 11：*Azure 资源策略。*</span><span class="sxs-lookup"><span data-stu-id="1c3c6-156">![Azure resource policy](../../_images/governance-1-19.png)
*Figure 11. Azure resource policy.*</span></span>

<span data-ttu-id="1c3c6-157">下一个控制措施是检查请求是否未超过 [Azure 订阅限制](/azure/azure-subscription-service-limits)。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-157">The next control is a check that the request does not exceed an [Azure subscription limit](/azure/azure-subscription-service-limits).</span></span> <span data-ttu-id="1c3c6-158">例如，每个订阅限制为包含 980 个资源组。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-158">For example, each subscription has a limit of 980 resource groups per subscription.</span></span> <span data-ttu-id="1c3c6-159">达到该限制后，如果收到部署更多资源组的请求，则会拒绝该请求。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-159">If a request is received to deploy an additional resource group once the limit has been reached, it is denied.</span></span>

<span data-ttu-id="1c3c6-160">![Azure 资源限制](../../_images/governance-1-20.png)
图 12：*Azure 资源限制。*</span><span class="sxs-lookup"><span data-stu-id="1c3c6-160">![Azure resource limits](../../_images/governance-1-20.png)
*Figure 12. Azure resource limits.*</span></span>

<span data-ttu-id="1c3c6-161">最后一个控制措施是检查请求是否在与订阅关联的财务承诺范围内。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-161">The final control is a check that the request is within the financial commitment associated with the subscription.</span></span> <span data-ttu-id="1c3c6-162">例如，如果请求是部署虚拟机，Azure 资源管理器会验证订阅是否包含足够的付款信息。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-162">For example, if the request is to deploy a virtual machine, Azure Resource Manager verifies that the subscription has sufficient payment information.</span></span>

<span data-ttu-id="1c3c6-163">![与订阅关联的财务承诺](../../_images/governance-1-21.png)
图 13：*财务承诺与订阅相关联。*</span><span class="sxs-lookup"><span data-stu-id="1c3c6-163">![A financial commitment associated with a subscription](../../_images/governance-1-21.png)
*Figure 13. A financial commitment is associated with a subscription.*</span></span>

## <a name="summary"></a><span data-ttu-id="1c3c6-164">摘要</span><span class="sxs-lookup"><span data-stu-id="1c3c6-164">Summary</span></span>

<span data-ttu-id="1c3c6-165">本文已介绍如何使用 Azure 资源管理器在 Azure 中管理资源访问。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-165">In this article, you learned about how resource access is managed in Azure using Azure Resource Manager.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1c3c6-166">后续步骤</span><span class="sxs-lookup"><span data-stu-id="1c3c6-166">Next steps</span></span>

<span data-ttu-id="1c3c6-167">至此，已了解如何在 Azure 中管理资源访问，接下来请学习如何使用这些服务为[简单工作负载](governance-simple-workload.md)或[多个团队](governance-multiple-teams.md)设计治理模型。</span><span class="sxs-lookup"><span data-stu-id="1c3c6-167">Now that you understand how to manage resource access in Azure, move on to learn how to design a governance model [for a simple workload](governance-simple-workload.md) or for [multiple teams](governance-multiple-teams.md) using these services.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="1c3c6-168">治理概述</span><span class="sxs-lookup"><span data-stu-id="1c3c6-168">An overview of governance</span></span>](../overview.md)
