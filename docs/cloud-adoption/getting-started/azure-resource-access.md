---
title: CAF：Azure 中的资源访问管理
description: 有关 Azure 中资源访问管理构造的说明 - Azure 资源管理器、订阅、资源组和资源。
author: petertaylor9999
ms.date: 2/11/2019
ms.openlocfilehash: f23540a03c82fbc46872645ac0fd82d574db353a
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55898113"
---
# <a name="resource-access-management-in-azure"></a><span data-ttu-id="38eaf-103">Azure 中的资源访问管理</span><span class="sxs-lookup"><span data-stu-id="38eaf-103">Resource access management in Azure</span></span>

<span data-ttu-id="38eaf-104">在[什么是资源调控？](what-is-governance.md)中我们已了解到，调控是指根据组织的目标和要求，对 Azure 资源的使用持续进行管理、监视和审核的过程。</span><span class="sxs-lookup"><span data-stu-id="38eaf-104">In [what is resource governance?](what-is-governance.md), you learned that governance refers to the ongoing process of managing, monitoring, and auditing the use of Azure resources to meet the goals and requirements of your organization.</span></span> <span data-ttu-id="38eaf-105">在继续学习如何设计调控模型之前，必须了解 Azure 中的资源访问管理控制措施。</span><span class="sxs-lookup"><span data-stu-id="38eaf-105">Before you move on to learn how to design a governance model, it's important to understand the resource access management controls in Azure.</span></span> <span data-ttu-id="38eaf-106">这些资源访问管理控制措施的配置构成了调控模型的基础。</span><span class="sxs-lookup"><span data-stu-id="38eaf-106">The configuration of these resource access management controls forms the basis of your governance model.</span></span>

<span data-ttu-id="38eaf-107">首先，让我们进一步探讨资源在 Azure 中的部署方式。</span><span class="sxs-lookup"><span data-stu-id="38eaf-107">Let's begin by taking a closer look at how resources are deployed in Azure.</span></span>

<!-- markdownlint-disable MD026 -->

## <a name="what-is-an-azure-resource"></a><span data-ttu-id="38eaf-108">什么是 Azure 资源？</span><span class="sxs-lookup"><span data-stu-id="38eaf-108">What is an Azure resource?</span></span>

<span data-ttu-id="38eaf-109">在 Azure 中，术语“资源”是指 Azure 管理的实体。</span><span class="sxs-lookup"><span data-stu-id="38eaf-109">In Azure, the term **resource** refers to an entity managed by Azure.</span></span> <span data-ttu-id="38eaf-110">例如，虚拟机、虚拟网络和存储帐户都称为 Azure 资源。</span><span class="sxs-lookup"><span data-stu-id="38eaf-110">For example, virtual machines, virtual networks, and storage accounts are all referred to as Azure resources.</span></span>

<span data-ttu-id="38eaf-111">![](../_images/governance-1-9.png)
*图 1.资源。*</span><span class="sxs-lookup"><span data-stu-id="38eaf-111">![](../_images/governance-1-9.png)
*Figure 1. A resource.*</span></span>

## <a name="what-is-an-azure-resource-group"></a><span data-ttu-id="38eaf-112">什么是 Azure 资源组？</span><span class="sxs-lookup"><span data-stu-id="38eaf-112">What is an Azure resource group?</span></span>

<span data-ttu-id="38eaf-113">Azure 中的每个资源必须属于某个[资源组](/azure/azure-resource-manager/resource-group-overview#resource-groups)。</span><span class="sxs-lookup"><span data-stu-id="38eaf-113">Each resource in Azure must belong to a [resource group](/azure/azure-resource-manager/resource-group-overview#resource-groups).</span></span> <span data-ttu-id="38eaf-114">资源组只是一个逻辑构造，它将多个资源分组到一起，以便可将它们作为单个实体进行管理。</span><span class="sxs-lookup"><span data-stu-id="38eaf-114">A resource group is simply a logical construct that groups multiple resources together so they can be managed as a single entity.</span></span> <span data-ttu-id="38eaf-115">举例来说，具有类似生命周期的资源（例如，[n 层应用程序](/azure/architecture/guide/architecture-styles/n-tier)的资源）可以作为一个组进行创建或删除。</span><span class="sxs-lookup"><span data-stu-id="38eaf-115">For example, resources that share a similar lifecycle, such as the resources for an [n-tier application](/azure/architecture/guide/architecture-styles/n-tier) may be created or deleted as a group.</span></span>

<span data-ttu-id="38eaf-116">![](../_images/governance-1-10.png)
*图 2.资源组包含资源。*</span><span class="sxs-lookup"><span data-stu-id="38eaf-116">![](../_images/governance-1-10.png)
*Figure 2. A resource group contains a resource.*</span></span>

<span data-ttu-id="38eaf-117">资源组及其包含的资源与 Azure **订阅**相关联。</span><span class="sxs-lookup"><span data-stu-id="38eaf-117">Resource groups and the resources they contain are associated with an Azure **subscription**.</span></span>

## <a name="what-is-an-azure-subscription"></a><span data-ttu-id="38eaf-118">什么是 Azure 订阅？</span><span class="sxs-lookup"><span data-stu-id="38eaf-118">What is an Azure subscription?</span></span>

<span data-ttu-id="38eaf-119">Azure 订阅与资源组的类似之处在于，它也是一个逻辑构造，可将资源组及其资源分组到一起。</span><span class="sxs-lookup"><span data-stu-id="38eaf-119">An Azure subscription is similar to a resource group in that it's a logical construct that groups together resource groups and their resources.</span></span> <span data-ttu-id="38eaf-120">但是，Azure 订阅还与 Azure 资源管理器使用的控制措施相关联。</span><span class="sxs-lookup"><span data-stu-id="38eaf-120">However, an Azure subscription is also associated with the controls used by Azure Resource Manager.</span></span> <span data-ttu-id="38eaf-121">这是什么意思呢？</span><span class="sxs-lookup"><span data-stu-id="38eaf-121">What does this mean?</span></span> <span data-ttu-id="38eaf-122">让我们进一步探讨资源管理器，以了解它与 Azure 订阅之间的关系。</span><span class="sxs-lookup"><span data-stu-id="38eaf-122">Let's take a closer look at Resource Manager to learn about the relationship between it and an Azure subscription.</span></span>

<span data-ttu-id="38eaf-123">![](../_images/governance-1-11.png)
*图 3.Azure 订阅。*</span><span class="sxs-lookup"><span data-stu-id="38eaf-123">![](../_images/governance-1-11.png)
*Figure 3. An Azure subscription.*</span></span>

## <a name="what-is-azure-resource-manager"></a><span data-ttu-id="38eaf-124">什么是 Azure 资源管理器？</span><span class="sxs-lookup"><span data-stu-id="38eaf-124">What is Azure Resource Manager?</span></span>

<span data-ttu-id="38eaf-125">在 [Azure 的工作原理](what-is-azure.md)中我们已了解到，Azure 包含一个“前端”，该前端中的许多服务协调 Azure 的所有功能。</span><span class="sxs-lookup"><span data-stu-id="38eaf-125">In [how does Azure work?](what-is-azure.md) you learned that Azure includes a "front end" with many services that orchestrate all the functions of Azure.</span></span> <span data-ttu-id="38eaf-126">其中一项服务是[资源管理器](/azure/azure-resource-manager/)，此服务托管客户端用于管理资源的 RESTful API。</span><span class="sxs-lookup"><span data-stu-id="38eaf-126">One of these services is [Resource Manager](/azure/azure-resource-manager/), and this service hosts the RESTful API used by clients to manage resources.</span></span>

<span data-ttu-id="38eaf-127">![](../_images/governance-1-12.png)
*图 4.Azure 资源管理器。*</span><span class="sxs-lookup"><span data-stu-id="38eaf-127">![](../_images/governance-1-12.png)
*Figure 4. Azure resource manager.*</span></span>

<span data-ttu-id="38eaf-128">下图显示了三个客户端：[PowerShell](/powershell/azure/overview)、[Azure 门户](https://portal.azure.com)和 [Azure 命令行接口 (CLI)](/cli/azure)：</span><span class="sxs-lookup"><span data-stu-id="38eaf-128">The following figure shows three clients: [PowerShell](/powershell/azure/overview), the [Azure portal](https://portal.azure.com), and the [Azure command line interface (CLI)](/cli/azure):</span></span>

<span data-ttu-id="38eaf-129">![](../_images/governance-1-13.png)
*图 5.Azure 客户端连接到资源管理器 RESTful API。*</span><span class="sxs-lookup"><span data-stu-id="38eaf-129">![](../_images/governance-1-13.png)
*Figure 5. Azure clients connect to the Resource Manager RESTful API.*</span></span>

<span data-ttu-id="38eaf-130">尽管这些客户端使用 RESTful API 连接到资源管理器，但资源管理器不包含直接管理资源的功能。</span><span class="sxs-lookup"><span data-stu-id="38eaf-130">While these clients connect to Resource Manager using the RESTful API, Resource Manager does not include functionality to manage resources directly.</span></span> <span data-ttu-id="38eaf-131">Azure 中的大多数资源类型具有自身的[**资源提供程序**](/azure/azure-resource-manager/resource-group-overview#terminology)。</span><span class="sxs-lookup"><span data-stu-id="38eaf-131">Rather, most resource types in Azure have their own [**resource provider**](/azure/azure-resource-manager/resource-group-overview#terminology).</span></span>

<span data-ttu-id="38eaf-132">\*
*图 6.Azure 资源提供程序。*</span><span class="sxs-lookup"><span data-stu-id="38eaf-132">![](../_images/governance-1-14.png)
*Figure 6. Azure resource providers.*</span></span>

<span data-ttu-id="38eaf-133">当客户端发出管理特定资源的请求时，资源管理器会连接到该资源类型的资源提供程序以完成该请求。</span><span class="sxs-lookup"><span data-stu-id="38eaf-133">When a client makes a request to manage a specific resource, Resource Manager connects to the resource provider for that resource type to complete the request.</span></span> <span data-ttu-id="38eaf-134">例如，如果客户端发出管理虚拟机资源的请求，则资源管理器会连接到 **Microsoft.compute** 资源提供程序。</span><span class="sxs-lookup"><span data-stu-id="38eaf-134">For example, if a client makes a request to manage a virtual machine resource, Resource Manager connects to the **Microsoft.Compute** resource provider.</span></span>

<span data-ttu-id="38eaf-135">![](../_images/governance-1-15.png)
*图 7.资源管理器连接到 **Microsoft.compute** 资源提供程序，以管理客户端请求中指定的资源。*</span><span class="sxs-lookup"><span data-stu-id="38eaf-135">![](../_images/governance-1-15.png)
*Figure 7. Resource Manager connects to the **Microsoft.Compute** resource provider to manage the resource specified in the client request.*</span></span>

<span data-ttu-id="38eaf-136">资源管理器要求客户端指定订阅和资源组的标识符，以管理虚拟机资源。</span><span class="sxs-lookup"><span data-stu-id="38eaf-136">Resource Manager requires the client to specify an identifier for both the subscription and the resource group in order to manage the virtual machine resource.</span></span>

<span data-ttu-id="38eaf-137">了解资源管理器工作原理后，让我们返回到有关 Azure 订阅如何与 Azure 资源管理器所用控制措施相关联的内容。</span><span class="sxs-lookup"><span data-stu-id="38eaf-137">Now that you have an understanding of how Resource Manager works, let's return to our discussion of how an Azure subscription is associated with the controls used by Azure resource manager.</span></span> <span data-ttu-id="38eaf-138">在资源管理器执行任何资源管理请求之前，需要先检查一组控制措施。</span><span class="sxs-lookup"><span data-stu-id="38eaf-138">Before any resource management request can be executed by Resource Manager, a set of controls are checked.</span></span>

<span data-ttu-id="38eaf-139">第一个控制措施是请求必须由已验证的用户发出，并且资源管理器与 [Azure Active Directory](/azure/active-directory/) (Azure AD) 之间建立了可信的关系来提供用户标识功能。</span><span class="sxs-lookup"><span data-stu-id="38eaf-139">The first control is that a request must be made by a validated user, and Resource Manager has a trusted relationship with [Azure Active Directory](/azure/active-directory/) (Azure AD) to provide user identity functionality.</span></span>

<span data-ttu-id="38eaf-140">![](../_images/governance-1-16.png)
*图 8.Azure Active Directory。*</span><span class="sxs-lookup"><span data-stu-id="38eaf-140">![](../_images/governance-1-16.png)
*Figure 8. Azure Active Directory.*</span></span>

<span data-ttu-id="38eaf-141">在 Azure AD 中，用户划分到**租户**。</span><span class="sxs-lookup"><span data-stu-id="38eaf-141">In Azure AD, users are segmented into **tenants**.</span></span> <span data-ttu-id="38eaf-142">租户是一个逻辑构造，它通常代表一个与组织关联的安全专用的 Azure AD 实例。</span><span class="sxs-lookup"><span data-stu-id="38eaf-142">A tenant is a logical construct that represents a secure, dedicated instance of Azure AD typically associated with an organization.</span></span> <span data-ttu-id="38eaf-143">每个订阅与一个 Azure AD 租户相关联。</span><span class="sxs-lookup"><span data-stu-id="38eaf-143">Each subscription is associated with an Azure AD tenant.</span></span>

<span data-ttu-id="38eaf-144">![](../_images/governance-1-17.png)
*图 9.与订阅关联的 Azure AD 租户。*</span><span class="sxs-lookup"><span data-stu-id="38eaf-144">![](../_images/governance-1-17.png)
*Figure 9. An Azure AD tenant associated with a subscription.*</span></span>

<span data-ttu-id="38eaf-145">管理特定订阅中的资源的每个客户端请求要求用户在关联的 Azure AD 租户中有一个帐户。</span><span class="sxs-lookup"><span data-stu-id="38eaf-145">Each client request to manage a resource in a particular subscription requires that the user has an account in the associated Azure AD tenant.</span></span>

<span data-ttu-id="38eaf-146">下一个控制措施是检查用户是否拥有发出请求的足够权限。</span><span class="sxs-lookup"><span data-stu-id="38eaf-146">The next control is a check that the user has sufficient permission to make the request.</span></span> <span data-ttu-id="38eaf-147">权限是使用[基于角色的访问控制 (RBAC)](/azure/role-based-access-control/) 分配给用户的。</span><span class="sxs-lookup"><span data-stu-id="38eaf-147">Permissions are assigned to users using [role-based access control (RBAC)](/azure/role-based-access-control/).</span></span>

<span data-ttu-id="38eaf-148">![](../_images/governance-1-18.png)
*图 10.为租户中的每个用户分配一个或多个 RBAC 角色。*</span><span class="sxs-lookup"><span data-stu-id="38eaf-148">![](../_images/governance-1-18.png)
*Figure 10. Each user in the tenant is assigned one or more RBAC roles.*</span></span>

<span data-ttu-id="38eaf-149">RBAC 角色指定用户对特定的资源拥有的一组权限。</span><span class="sxs-lookup"><span data-stu-id="38eaf-149">An RBAC role specifies a set of permissions a user may take on a specific resource.</span></span> <span data-ttu-id="38eaf-150">将角色分配给用户时，会应用这些权限。</span><span class="sxs-lookup"><span data-stu-id="38eaf-150">When the role is assigned to the user, those permissions are applied.</span></span> <span data-ttu-id="38eaf-151">例如，[内置的**所有者**角色](/azure/role-based-access-control/built-in-roles#owner)允许用户对资源执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="38eaf-151">For example, the [built-in **owner** role](/azure/role-based-access-control/built-in-roles#owner) allows a user to perform any action on a resource.</span></span>

<span data-ttu-id="38eaf-152">下一个控制措施是检查为 [Azure 资源策略](/azure/governance/policy/)指定的设置是否允许该请求。</span><span class="sxs-lookup"><span data-stu-id="38eaf-152">The next control is a check that the request is allowed under the settings specified for [Azure resource policy](/azure/governance/policy/).</span></span> <span data-ttu-id="38eaf-153">Azure 资源策略指定允许对特定资源执行的操作。</span><span class="sxs-lookup"><span data-stu-id="38eaf-153">Azure resource policies specify the operations allowed for a specific resource.</span></span> <span data-ttu-id="38eaf-154">例如，Azure 资源策略可以指定只允许用户部署特定类型的虚拟机。</span><span class="sxs-lookup"><span data-stu-id="38eaf-154">For example, an Azure resource policy can specify that users are only allowed to deploy a specific type of virtual machine.</span></span>

<span data-ttu-id="38eaf-155">![](../_images/governance-1-19.png)
*图 11.Azure 资源策略。*</span><span class="sxs-lookup"><span data-stu-id="38eaf-155">![](../_images/governance-1-19.png)
*Figure 11. Azure resource policy.*</span></span>

<span data-ttu-id="38eaf-156">下一个控制措施是检查请求是否未超过 [Azure 订阅限制](/azure/azure-subscription-service-limits)。</span><span class="sxs-lookup"><span data-stu-id="38eaf-156">The next control is a check that the request does not exceed an [Azure subscription limit](/azure/azure-subscription-service-limits).</span></span> <span data-ttu-id="38eaf-157">例如，每个订阅限制为包含 980 个资源组。</span><span class="sxs-lookup"><span data-stu-id="38eaf-157">For example, each subscription has a limit of 980 resource groups per subscription.</span></span> <span data-ttu-id="38eaf-158">达到该限制后，如果收到部署更多资源组的请求，则会拒绝该请求。</span><span class="sxs-lookup"><span data-stu-id="38eaf-158">If a request is received to deploy an additional resource group once the limit has been reached, it is denied.</span></span>

<span data-ttu-id="38eaf-159">![](../_images/governance-1-20.png)
*图 12.Azure 资源限制。*</span><span class="sxs-lookup"><span data-stu-id="38eaf-159">![](../_images/governance-1-20.png)
*Figure 12. Azure resource limits.*</span></span>

<span data-ttu-id="38eaf-160">最后一个控制措施是检查请求是否在与订阅关联的财务承诺范围内。</span><span class="sxs-lookup"><span data-stu-id="38eaf-160">The final control is a check that the request is within the financial commitment associated with the subscription.</span></span> <span data-ttu-id="38eaf-161">例如，如果请求内容是部署虚拟机，则资源管理器会验证订阅是否包含足够的付款信息。</span><span class="sxs-lookup"><span data-stu-id="38eaf-161">For example, if the request is to deploy a virtual machine, Resource Manager verifies that the subscription has sufficient payment information.</span></span>

<span data-ttu-id="38eaf-162">![](../_images/governance-1-21.png)
*图 13.财务承诺与订阅相关联。*</span><span class="sxs-lookup"><span data-stu-id="38eaf-162">![](../_images/governance-1-21.png)
*Figure 13. A financial commitment is associated with a subscription.*</span></span>

## <a name="summary"></a><span data-ttu-id="38eaf-163">摘要</span><span class="sxs-lookup"><span data-stu-id="38eaf-163">Summary</span></span>

<span data-ttu-id="38eaf-164">本文已介绍如何使用 Azure 资源管理器在 Azure 中管理资源访问权限。</span><span class="sxs-lookup"><span data-stu-id="38eaf-164">In this article, you learned about how resource access is managed in Azure using Azure Resource Manager.</span></span>

## <a name="next-steps"></a><span data-ttu-id="38eaf-165">后续步骤</span><span class="sxs-lookup"><span data-stu-id="38eaf-165">Next steps</span></span>

<span data-ttu-id="38eaf-166">了解如何在 Azure 中管理资源访问权限后，接下来请学习如何设计治理模型。</span><span class="sxs-lookup"><span data-stu-id="38eaf-166">Now that you understand how resource access is managed in Azure, move on to learn how to design a governance model.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="38eaf-167">云治理</span><span class="sxs-lookup"><span data-stu-id="38eaf-167">Cloud governance</span></span>](../governance/overview.md)
