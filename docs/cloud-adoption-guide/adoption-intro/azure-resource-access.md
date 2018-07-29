---
title: 了解 Azure 中的资源访问管理
description: 解释 Azure 中的资源访问管理构造：Azure 资源管理器、订阅、资源组和资源
author: petertay
ms.openlocfilehash: 02e25e943822ba065d360d687d34283d86a805bd
ms.sourcegitcommit: c704d5d51c8f9bbab26465941ddcf267040a8459
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2018
ms.locfileid: "39229457"
---
# <a name="understanding-resource-access-management-in-azure"></a><span data-ttu-id="273b1-103">了解 Azure 中的资源访问管理</span><span class="sxs-lookup"><span data-stu-id="273b1-103">Understanding resource access management in Azure</span></span>

<span data-ttu-id="273b1-104">在[什么是资源调控？](governance-explainer.md)中我们已了解到，调控是指根据组织的目标和要求，对 Azure 资源的使用持续进行管理、监视和审核的过程。</span><span class="sxs-lookup"><span data-stu-id="273b1-104">In [what is resource governance?](governance-explainer.md), you learned that governance refers to the ongoing process of managing, monitoring, and auditing the use of Azure resources to meet the goals and requirements of your organization.</span></span> <span data-ttu-id="273b1-105">在继续学习如何设计调控模型之前，必须了解 Azure 中的资源访问管理控制措施。</span><span class="sxs-lookup"><span data-stu-id="273b1-105">Before you move on to learn how to design a governance model, it's important to understand the resource access management controls in Azure.</span></span> <span data-ttu-id="273b1-106">这些资源访问管理控制措施的配置构成了调控模型的基础。</span><span class="sxs-lookup"><span data-stu-id="273b1-106">The configuration of these resource access management controls forms the basis of your governance model.</span></span>

<span data-ttu-id="273b1-107">首先，让我们进一步探讨资源在 Azure 中的部署方式。</span><span class="sxs-lookup"><span data-stu-id="273b1-107">Let's begin by taking a closer look at how are resources are deployed in Azure.</span></span> 

## <a name="what-is-an-azure-resource"></a><span data-ttu-id="273b1-108">什么是 Azure 资源？</span><span class="sxs-lookup"><span data-stu-id="273b1-108">What is an Azure resource?</span></span>

<span data-ttu-id="273b1-109">在 Azure 中，术语“资源”是指 Azure 管理的实体。</span><span class="sxs-lookup"><span data-stu-id="273b1-109">In Azure, the term **resource** refers to an entity managed by Azure.</span></span> <span data-ttu-id="273b1-110">例如，虚拟机、虚拟网络和存储帐户都称为 Azure 资源。</span><span class="sxs-lookup"><span data-stu-id="273b1-110">For example, virtual machines, virtual networks, and storage accounts are all referred to as Azure resources.</span></span>

![](../_images/governance-1-9.png)   
<span data-ttu-id="273b1-111">*图 1.资源。*</span><span class="sxs-lookup"><span data-stu-id="273b1-111">*Figure 1. A resource.*</span></span>

## <a name="what-is-an-azure-resource-group"></a><span data-ttu-id="273b1-112">什么是 Azure 资源组？</span><span class="sxs-lookup"><span data-stu-id="273b1-112">What is an Azure resource group?</span></span>

<span data-ttu-id="273b1-113">Azure 中的每个资源必须属于某个[资源组](/azure/azure-resource-manager/resource-group-overview#resource-groups)。</span><span class="sxs-lookup"><span data-stu-id="273b1-113">Each resource in Azure must belong to a [resource group](/azure/azure-resource-manager/resource-group-overview#resource-groups).</span></span> <span data-ttu-id="273b1-114">资源组只是一个逻辑构造，它将多个资源分组到一起，以便可将它们作为单个实体进行管理。</span><span class="sxs-lookup"><span data-stu-id="273b1-114">A resource group is simply a logical construct that groups multiple resources together so they can be managed as a single entity.</span></span> <span data-ttu-id="273b1-115">举例来说，具有类似生命周期的资源（例如，[n 层应用程序](/azure/architecture/guide/architecture-styles/n-tier)的资源）可以作为一个组进行创建或删除。</span><span class="sxs-lookup"><span data-stu-id="273b1-115">For example, resources that share a similar lifecycle, such as the resources for an [n-tier application](/azure/architecture/guide/architecture-styles/n-tier) may be created or deleted as a group.</span></span> 

![](../_images/governance-1-10.png)   
<span data-ttu-id="273b1-116">*图 2.资源组包含资源。*</span><span class="sxs-lookup"><span data-stu-id="273b1-116">*Figure 2. A resource group contains a resource.*</span></span> 

<span data-ttu-id="273b1-117">资源组及其包含的资源与 Azure **订阅**相关联。</span><span class="sxs-lookup"><span data-stu-id="273b1-117">Resource groups and the resources they contain are associated with an Azure **subscription**.</span></span> 

## <a name="what-is-an-azure-subscription"></a><span data-ttu-id="273b1-118">什么是 Azure 订阅？</span><span class="sxs-lookup"><span data-stu-id="273b1-118">What is an Azure subscription?</span></span>

<span data-ttu-id="273b1-119">Azure 订阅与资源组的类似之处在于，它也是一个逻辑构造，可将资源组及其资源分组到一起。</span><span class="sxs-lookup"><span data-stu-id="273b1-119">An Azure subscription is similar to a resource group in that it's a logical construct that groups together resource groups and their resources.</span></span> <span data-ttu-id="273b1-120">但是，Azure 订阅还与 Azure 资源管理器使用的控制措施相关联。</span><span class="sxs-lookup"><span data-stu-id="273b1-120">However, an Azure subscription is also associated with the controls used by Azure resource manager.</span></span> <span data-ttu-id="273b1-121">这是什么意思呢？</span><span class="sxs-lookup"><span data-stu-id="273b1-121">What does this mean?</span></span> <span data-ttu-id="273b1-122">让我们进一步探讨 Azure 资源管理器，以了解它与 Azure 订阅之间的关系。</span><span class="sxs-lookup"><span data-stu-id="273b1-122">Let's take a closer look at Azure resource manager to learn about the relationship between it and an Azure subscription.</span></span>

![](../_images/governance-1-11.png)   
<span data-ttu-id="273b1-123">*图 3.Azure 订阅。*</span><span class="sxs-lookup"><span data-stu-id="273b1-123">*Figure 3. An Azure subscription.*</span></span>

## <a name="what-is-azure-resource-manager"></a><span data-ttu-id="273b1-124">什么是 Azure 资源管理器？</span><span class="sxs-lookup"><span data-stu-id="273b1-124">What is Azure resource manager?</span></span>

<span data-ttu-id="273b1-125">在 [Azure 的工作原理](azure-explainer.md)中我们已了解到，Azure 包含一个“前端”，该前端中的许多服务协调 Azure 的所有功能。</span><span class="sxs-lookup"><span data-stu-id="273b1-125">In [how does Azure work?](azure-explainer.md) you learned that Azure includes a "front end" with many services that orchestrate all the functions of Azure.</span></span> <span data-ttu-id="273b1-126">其中的一个服务是 [Azure 资源管理器](/azure/azure-resource-manager/)，此服务托管客户端用来管理资源的 RESTful API。</span><span class="sxs-lookup"><span data-stu-id="273b1-126">One of these services is [Azure resource manager](/azure/azure-resource-manager/), and this service hosts the RESTful API used by clients to manage resources.</span></span> 

![](../_images/governance-1-12.png)   
<span data-ttu-id="273b1-127">*图 4.Azure 资源管理器。*</span><span class="sxs-lookup"><span data-stu-id="273b1-127">*Figure 4. Azure resource manager.*</span></span>

<span data-ttu-id="273b1-128">下图显示了三个客户端：[Powershell](/powershell/azure/overview)、[Azure 门户](https://portal.azure.com)和 [Azure 命令行接口 (CLI)](/cli/azure)：</span><span class="sxs-lookup"><span data-stu-id="273b1-128">The following figure shows three clients: [Powershell](/powershell/azure/overview),[the Azure portal](https://portal.azure.com), and the [Azure command line interface (CLI)](/cli/azure):</span></span>

![](../_images/governance-1-13.png)   
<span data-ttu-id="273b1-129">*图 5.Azure 客户端连接到 Azure 资源管理器 RESTful API。*</span><span class="sxs-lookup"><span data-stu-id="273b1-129">*Figure 5. Azure clients connect to the Azure resource manager RESTful API.*</span></span>

<span data-ttu-id="273b1-130">尽管这些客户端使用 RESTful API 连接到 Azure 资源管理器，但 Azure 资源管理器不包含直接管理资源的功能。</span><span class="sxs-lookup"><span data-stu-id="273b1-130">While these clients connect to Azure resource manager using the RESTful API, Azure resource manager does not include functionality to manage resources directly.</span></span> <span data-ttu-id="273b1-131">Azure 中的大多数资源类型具有自身的[**资源提供程序**](/azure/azure-resource-manager/resource-group-overview#terminology)。</span><span class="sxs-lookup"><span data-stu-id="273b1-131">Rather, most resource types in Azure have their own [**resource provider**](/azure/azure-resource-manager/resource-group-overview#terminology).</span></span> 

![](../_images/governance-1-14.png)   
<span data-ttu-id="273b1-132">*图 6.Azure 资源提供程序。*</span><span class="sxs-lookup"><span data-stu-id="273b1-132">*Figure 6. Azure resource providers.*</span></span>

<span data-ttu-id="273b1-133">当客户端发出管理特定资源的请求时，Azure 资源管理器会连接到该资源类型的资源提供程序以完成该请求。</span><span class="sxs-lookup"><span data-stu-id="273b1-133">When a client makes a request to manage a specific resource, Azure resource manager connects to the resource provider for that resource type to complete the request.</span></span> <span data-ttu-id="273b1-134">例如，如果客户端发出管理虚拟机资源的请求，则 Azure 资源管理器会连接到 **Microsoft.compute** 资源提供程序。</span><span class="sxs-lookup"><span data-stu-id="273b1-134">For example, if a client makes a request to manage a virtual machine resource, Azure resource manager connects to the **Microsoft.compute** resource provider.</span></span> 

![](../_images/governance-1-15.png)   
<span data-ttu-id="273b1-135">*图 7.Azure 资源管理器连接到 **Microsoft.compute** 资源提供程序，以管理客户端请求中指定的资源。*</span><span class="sxs-lookup"><span data-stu-id="273b1-135">*Figure 7. Azure resource manager connects to the **Microsoft.compute** resource provider to manage the resource specified in the client request.*</span></span>

<span data-ttu-id="273b1-136">请注意，Azure 资源管理器要求客户端指定订阅和资源组的标识符，以管理虚拟机资源。</span><span class="sxs-lookup"><span data-stu-id="273b1-136">Notice that Azure resource manager required the client to specify an identifier for both the subscription and the resource group in order to manage the virtual machine resource.</span></span> 

<span data-ttu-id="273b1-137">了解 Azure 资源管理器工作原理后，让我们返回到有关 Azure 订阅如何与 Azure 资源管理器所用控制措施相关联的内容。</span><span class="sxs-lookup"><span data-stu-id="273b1-137">Now that you have an understanding of how Azure resource manager works, let's return to our discussion of how an Azure subscription is associated with the controls used by Azure resource manager.</span></span> <span data-ttu-id="273b1-138">在 Azure 资源管理器执行任何资源管理请求之前，需要先检查一组控制措施。</span><span class="sxs-lookup"><span data-stu-id="273b1-138">Before any resource management request can be executed by Azure resource manager, a set of controls are checked.</span></span> 

<span data-ttu-id="273b1-139">第一个控制措施是请求必须由已验证的用户发出，并且 Azure 资源管理器与 [Azure Active Directory (Azure AD)](/azure/active-directory/) 之间建立了可信的关系，这样才能提供用户标识功能。</span><span class="sxs-lookup"><span data-stu-id="273b1-139">The first control is that a request must be made by a validated user, and Azure resource manager has a trusted relationship with [Azure Active Directory (Azure AD)](/azure/active-directory/) to provide user identity functionality.</span></span>

![](../_images/governance-1-16.png)   
<span data-ttu-id="273b1-140">*图 8.Azure Active Directory。*</span><span class="sxs-lookup"><span data-stu-id="273b1-140">*Figure 8. Azure Active Directory.*</span></span>

<span data-ttu-id="273b1-141">在 Azure AD 中，用户划分到**租户**。</span><span class="sxs-lookup"><span data-stu-id="273b1-141">In Azure AD, users are segmented into **tenants**.</span></span> <span data-ttu-id="273b1-142">租户是一个逻辑构造，它通常代表一个与组织关联的安全专用的 Azure AD 实例。</span><span class="sxs-lookup"><span data-stu-id="273b1-142">A tenant is a logical construct that represents a secure, dedicated instance of Azure AD typically associated with an organization.</span></span> <span data-ttu-id="273b1-143">每个订阅与一个 Azure AD 租户相关联。</span><span class="sxs-lookup"><span data-stu-id="273b1-143">Each subscription is associated with an Azure AD tenant.</span></span>

![](../_images/governance-1-17.png)   
<span data-ttu-id="273b1-144">*图 9.与订阅关联的 Azure AD 租户。*</span><span class="sxs-lookup"><span data-stu-id="273b1-144">*Figure 9. An Azure AD tenant associated with a subscription.*</span></span>

<span data-ttu-id="273b1-145">管理特定订阅中的资源的每个客户端请求要求用户在关联的 Azure AD 租户中有一个帐户。</span><span class="sxs-lookup"><span data-stu-id="273b1-145">Each client request to manage a resource in a particular subscription requires that the user has an account in the associated Azure AD tenant.</span></span> 

<span data-ttu-id="273b1-146">下一个控制措施是检查用户是否拥有发出请求的足够权限。</span><span class="sxs-lookup"><span data-stu-id="273b1-146">The next control is a check that the user has sufficient permission to make the request.</span></span> <span data-ttu-id="273b1-147">权限是使用[基于角色的访问控制 (RBAC)](/azure/role-based-access-control/) 分配给用户的。</span><span class="sxs-lookup"><span data-stu-id="273b1-147">Permissions are assigned to users using [role-based access control (RBAC)](/azure/role-based-access-control/).</span></span>

![](../_images/governance-1-18.png)   
<span data-ttu-id="273b1-148">*图 10.为租户中的每个用户分配一个或多个 RBAC 角色。*</span><span class="sxs-lookup"><span data-stu-id="273b1-148">*Figure 10. Each user in the tenant is assigned one or more RBAC roles.*</span></span>

<span data-ttu-id="273b1-149">RBAC 角色指定用户对特定的资源拥有的一组权限。</span><span class="sxs-lookup"><span data-stu-id="273b1-149">An RBAC role specifies a set of permissions a user may take on a specific resource.</span></span> <span data-ttu-id="273b1-150">将角色分配给用户时，会应用这些权限。</span><span class="sxs-lookup"><span data-stu-id="273b1-150">When the role is assigned to user, those permissions are applied.</span></span> <span data-ttu-id="273b1-151">例如，[内置的**所有者**角色](/azure/role-based-access-control/built-in-roles#owner)允许用户对资源执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="273b1-151">For example, the [built-in **owner** role](/azure/role-based-access-control/built-in-roles#owner) allows a user to perform any action on a resource.</span></span>

<span data-ttu-id="273b1-152">下一个控制措施是检查为 [Azure 资源策略](/azure/azure-policy/)指定的设置是否允许该请求。</span><span class="sxs-lookup"><span data-stu-id="273b1-152">The next control is a check that the request is allowed under the settings specified for [Azure resource policy](/azure/azure-policy/).</span></span> <span data-ttu-id="273b1-153">Azure 资源策略指定允许对特定资源执行的操作。</span><span class="sxs-lookup"><span data-stu-id="273b1-153">Azure resource policies specify the operations allowed for a specific resource.</span></span> <span data-ttu-id="273b1-154">例如，Azure 资源策略可以指定只允许用户部署特定类型的虚拟机。</span><span class="sxs-lookup"><span data-stu-id="273b1-154">For example, an Azure resource policy can specify that users are only allowed to deploy a specific type of virtual machine.</span></span>

![](../_images/governance-1-19.png)   
<span data-ttu-id="273b1-155">*图 11.Azure 资源策略。*</span><span class="sxs-lookup"><span data-stu-id="273b1-155">*Figure 11. Azure resource policy.*</span></span>

<span data-ttu-id="273b1-156">下一个控制措施是检查请求是否未超过 [Azure 订阅限制](/azure/azure-subscription-service-limits)。</span><span class="sxs-lookup"><span data-stu-id="273b1-156">The next control is a check that the request does not exceed an [Azure subscription limit](/azure/azure-subscription-service-limits).</span></span> <span data-ttu-id="273b1-157">例如，每个订阅限制为包含 980 个资源组。</span><span class="sxs-lookup"><span data-stu-id="273b1-157">For example, each subscription has a limit of 980 resource groups per subscription.</span></span> <span data-ttu-id="273b1-158">达到该限制后，如果收到部署更多资源组的请求，则会拒绝该请求。</span><span class="sxs-lookup"><span data-stu-id="273b1-158">If a request is received to deploy an additional resource groups once the limit has been reached, it is denied.</span></span>

![](../_images/governance-1-20.png)   
<span data-ttu-id="273b1-159">*图 12.Azure 资源限制。*</span><span class="sxs-lookup"><span data-stu-id="273b1-159">*Figure 12. Azure resource limits.*</span></span> 

<span data-ttu-id="273b1-160">最后一个控制措施是检查请求是否在与订阅关联的财务承诺范围内。</span><span class="sxs-lookup"><span data-stu-id="273b1-160">The final control is a check that the request is within the financial commitment associated with the subscription.</span></span> <span data-ttu-id="273b1-161">例如，如果请求是部署虚拟机，则 Azure 资源管理器会验证订阅是否包含足够的付款信息。</span><span class="sxs-lookup"><span data-stu-id="273b1-161">For example, if the request is to deploy a virtual machine, Azure resource manager verifies that the subscription has sufficient payment information.</span></span>

![](../_images/governance-1-21.png)   
<span data-ttu-id="273b1-162">*图 13.财务承诺与订阅相关联。*</span><span class="sxs-lookup"><span data-stu-id="273b1-162">*Figure 13. A financial commitment is associated with a subscription.*</span></span>

# <a name="summary"></a><span data-ttu-id="273b1-163">摘要</span><span class="sxs-lookup"><span data-stu-id="273b1-163">Summary</span></span>

<span data-ttu-id="273b1-164">本文已介绍如何使用 Azure 资源管理器在 Azure 中管理资源访问权限。</span><span class="sxs-lookup"><span data-stu-id="273b1-164">In this article, you learned about how resource access is managed in Azure using Azure resource manager.</span></span>

# <a name="next-steps"></a><span data-ttu-id="273b1-165">后续步骤</span><span class="sxs-lookup"><span data-stu-id="273b1-165">Next steps</span></span>

<span data-ttu-id="273b1-166">了解如何在 Azure 中管理资源访问权限后，接下来请学习如何使用这些服务[设计调控模型](governance-how-to.md)。</span><span class="sxs-lookup"><span data-stu-id="273b1-166">Now that you understand how resource access is managed in Azure, move on to learn how to [design a governance model](governance-how-to.md) using these services.</span></span>