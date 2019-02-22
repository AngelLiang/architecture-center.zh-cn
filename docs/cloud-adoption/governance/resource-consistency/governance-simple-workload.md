---
title: CAF：适用于简单工作负荷的治理设计
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
description: 有关配置 Azure 调控措施，使用户能够部署简单工作负荷的指南
author: petertaylor9999
ms.date: 2/11/2019
ms.openlocfilehash: ce090562bf256a34078cfe1a9e9f678dbdf10ffe
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900483"
---
# <a name="governance-design-for-a-simple-workload"></a><span data-ttu-id="786ce-103">适用于简单工作负荷的治理设计</span><span class="sxs-lookup"><span data-stu-id="786ce-103">Governance design for a simple workload</span></span>

<span data-ttu-id="786ce-104">本指南旨在介绍在 Azure 中设计资源治理模型，以便为单个团队和简单工作负荷提供支持这一过程。</span><span class="sxs-lookup"><span data-stu-id="786ce-104">The goal of this guidance is to help you learn the process for designing a resource governance model in Azure to support a single team and a simple workload.</span></span> <span data-ttu-id="786ce-105">其中探讨了一系列假设性的调控要求，然后详细讲解满足这些要求的几个示例实现。</span><span class="sxs-lookup"><span data-stu-id="786ce-105">You'll look at a set of hypothetical governance requirements, then go through several example implementations that satisfy those requirements.</span></span>

<span data-ttu-id="786ce-106">在基础采用阶段，我们的目标是将简单的工作负荷部署到 Azure。</span><span class="sxs-lookup"><span data-stu-id="786ce-106">In the foundational adoption stage, our goal is to deploy a simple workload to Azure.</span></span> <span data-ttu-id="786ce-107">这可以满足以下要求：</span><span class="sxs-lookup"><span data-stu-id="786ce-107">This results in the following requirements:</span></span>

* <span data-ttu-id="786ce-108">由负责部署和维护简单工作负荷的**工作负荷所有者**进行标识管理。</span><span class="sxs-lookup"><span data-stu-id="786ce-108">Identity management for a single **workload owner** who is responsible for deploying and maintaining the simple workload.</span></span> <span data-ttu-id="786ce-109">工作负荷所有者需要有权创建、读取、更新和删除资源，以及有权将这些权限委托给标识管理系统中的其他用户。</span><span class="sxs-lookup"><span data-stu-id="786ce-109">The workload owner requires permission to create, read, update, and delete resources as well as permission to delegate these rights to other users in the identity management system.</span></span>
* <span data-ttu-id="786ce-110">将简单工作负荷的所有资源作为单个管理单元进行管理。</span><span class="sxs-lookup"><span data-stu-id="786ce-110">Manage all resources for the simple workload as a single management unit.</span></span>

## <a name="licensing-azure"></a><span data-ttu-id="786ce-111">许可 Azure</span><span class="sxs-lookup"><span data-stu-id="786ce-111">Licensing Azure</span></span>

<span data-ttu-id="786ce-112">在开始设计调控模型之前，必须了解 Azure 的许可方式。</span><span class="sxs-lookup"><span data-stu-id="786ce-112">Before you begin designing our governance model, it's important to understand how Azure is licensed.</span></span> <span data-ttu-id="786ce-113">这是因为，与 Azure 许可证关联的管理帐户对所有 Azure 资源拥有最高级别的访问权限。</span><span class="sxs-lookup"><span data-stu-id="786ce-113">This is because the administrative accounts associated with your Azure license have the highest level of access to all of your Azure resources.</span></span> <span data-ttu-id="786ce-114">这些管理帐户构成了调控模型的基础。</span><span class="sxs-lookup"><span data-stu-id="786ce-114">These administrative accounts form the basis of your governance model.</span></span>  

> [!NOTE]
> <span data-ttu-id="786ce-115">如果组织的某个现有 [Microsoft 企业协议](https://www.microsoft.com/licensing/licensing-programs/enterprise.aspx)不包括 Azure，可以通过做出前期货币承诺来添加 Azure。</span><span class="sxs-lookup"><span data-stu-id="786ce-115">If your organization has an existing [Microsoft Enterprise Agreement](https://www.microsoft.com/licensing/licensing-programs/enterprise.aspx) that does not include Azure, Azure can be added by making an upfront monetary commitment.</span></span> <span data-ttu-id="786ce-116">有关详细信息，请参阅[为企业获得 Azure 许可](https://azure.microsoft.com/pricing/enterprise-agreement/)。</span><span class="sxs-lookup"><span data-stu-id="786ce-116">See [licensing Azure for the enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/) for more information.</span></span>

<span data-ttu-id="786ce-117">将 Azure 添加到组织的企业协议时，系统已提示组织创建 **Azure 帐户**。</span><span class="sxs-lookup"><span data-stu-id="786ce-117">When Azure was added to your organization's Enterprise Agreement, your organization was prompted to create an **Azure account**.</span></span> <span data-ttu-id="786ce-118">在帐户创建过程中，已创建 **Azure 帐户所有者**，以及具有**全局管理员**帐户的 Azure Active Directory (Azure AD) 租户。</span><span class="sxs-lookup"><span data-stu-id="786ce-118">During the account creation process, an **Azure account owner** was created, as well as an Azure Active Directory (Azure AD) tenant with a **global administrator** account.</span></span> <span data-ttu-id="786ce-119">Azure AD 租户是一个逻辑构造，它代表 Azure AD 的安全专用实例。</span><span class="sxs-lookup"><span data-stu-id="786ce-119">An Azure AD tenant is a logical construct that represents a secure, dedicated instance of Azure AD.</span></span>

<span data-ttu-id="786ce-120">![充当 Azure 帐户管理者和 Azure AD 全局管理员的 Azure 帐户](../../_images/governance-3-0.png)
*图 1.充当 Azure 帐户管理者和 Azure AD 全局管理员的 Azure 帐户。*</span><span class="sxs-lookup"><span data-stu-id="786ce-120">![Azure account with Azure Account Manager and Azure AD global administrator](../../_images/governance-3-0.png)
*Figure 1. An Azure account with an Account Manager and Azure AD Global Administrator.*</span></span>

## <a name="identity-management"></a><span data-ttu-id="786ce-121">身份管理</span><span class="sxs-lookup"><span data-stu-id="786ce-121">Identity management</span></span>

<span data-ttu-id="786ce-122">Azure 只信任 [Azure AD](/azure/active-directory) 进行用户身份验证以及授权用户访问资源，因此，Azure AD 是我们的标识管理系统。</span><span class="sxs-lookup"><span data-stu-id="786ce-122">Azure only trusts [Azure AD](/azure/active-directory) to authenticate users and authorize user access to resources, so Azure AD is our identity management system.</span></span> <span data-ttu-id="786ce-123">Azure AD 全局管理员拥有最高级别的权限，可以执行标识相关的所有操作，包括创建用户和分配权限。</span><span class="sxs-lookup"><span data-stu-id="786ce-123">The Azure AD global administrator has the highest level of permissions and can perform all actions related to identity, including creating users and assigning permissions.</span></span>

<span data-ttu-id="786ce-124">我们的要求是由负责部署和维护简单工作负荷的**工作负荷所有者**进行标识管理。</span><span class="sxs-lookup"><span data-stu-id="786ce-124">Our requirement is identity management for a single **workload owner** who is responsible for deploying and maintaining the simple workload.</span></span> <span data-ttu-id="786ce-125">工作负荷所有者需要有权创建、读取、更新和删除资源，以及有权将这些权限委托给标识管理系统中的其他用户。</span><span class="sxs-lookup"><span data-stu-id="786ce-125">The workload owner requires permission to create, read, update, and delete resources as well as permission to delegate these rights to other users in the identity management system.</span></span>

<span data-ttu-id="786ce-126">Azure AD 全局管理员将为**工作负荷所有者**创建**工作负荷所有者**帐户：</span><span class="sxs-lookup"><span data-stu-id="786ce-126">Our Azure AD global administrator will create the **workload owner** account for the **workload owner**:</span></span>

<span data-ttu-id="786ce-127">![Azure AD 全局管理员创建工作负荷所有者帐户](../../_images/governance-1-2.png)
*图 2.Azure AD 全局管理员创建工作负荷所有者用户帐户。*</span><span class="sxs-lookup"><span data-stu-id="786ce-127">![The Azure AD global administrator creates the workload owner account](../../_images/governance-1-2.png)
*Figure 2. The Azure AD global administrator creates the workload owner user account.*</span></span>

<span data-ttu-id="786ce-128">在将此用户添加到订阅之前，无法分配资源访问权限，因此将在后续两个部分执行该操作。</span><span class="sxs-lookup"><span data-stu-id="786ce-128">You aren't able to assign resource access permission until this user is added to a **subscription**, so you'll do that in the next two sections.</span></span>

## <a name="resource-management-scope"></a><span data-ttu-id="786ce-129">资源管理范围</span><span class="sxs-lookup"><span data-stu-id="786ce-129">Resource management scope</span></span>

<span data-ttu-id="786ce-130">随着组织部署的资源不断增多，调控这些资源的复杂性也会不断提高。</span><span class="sxs-lookup"><span data-stu-id="786ce-130">As the number of resources deployed by your organization grows, the complexity of governing those resources grows as well.</span></span> <span data-ttu-id="786ce-131">Azure 实施逻辑容器层次结构，使组织能够以不同的粒度级（称为**范围**）管理管理组中的资源。</span><span class="sxs-lookup"><span data-stu-id="786ce-131">Azure implements a logical container hierarchy to enable your organization to manage your resources in groups at various levels of granularity, also known as **scope**.</span></span>

<span data-ttu-id="786ce-132">资源管理范围的最高层级是**订阅**级别。</span><span class="sxs-lookup"><span data-stu-id="786ce-132">The top level of resource management scope is the **subscription** level.</span></span> <span data-ttu-id="786ce-133">订阅由建立资金承诺，并负责支付所有订阅相关 Azure 资源费用的 Azure **帐户所有者**创建：</span><span class="sxs-lookup"><span data-stu-id="786ce-133">A subscription is created by the Azure **account owner**, who establishes the financial commitment and is responsible for paying for all Azure resources associated with the subscription:</span></span>

<span data-ttu-id="786ce-134">![Azure 帐户所有者创建订阅](../../_images/governance-1-3.png)
*图 3.Azure 帐户所有者创建订阅。*</span><span class="sxs-lookup"><span data-stu-id="786ce-134">![The Azure account owner creates a subscription](../../_images/governance-1-3.png)
*Figure 3. The Azure account owner creates a subscription.*</span></span>

<span data-ttu-id="786ce-135">创建订阅时，Azure **帐户所有者**会将 Azure AD 租户与订阅相关联，此 Azure AD 租户用于对用户进行身份验证和授权：</span><span class="sxs-lookup"><span data-stu-id="786ce-135">When the subscription is created, the Azure **account owner** associates an Azure AD tenant with the subscription, and this Azure AD tenant is used for authenticating and authorizing users:</span></span>

<span data-ttu-id="786ce-136">![Azure 帐户所有者将 Azure AD 租户与订阅相关联](../../_images/governance-1-4.png)
*图 4.Azure 帐户所有者将 Azure AD 租户与订阅相关联。*</span><span class="sxs-lookup"><span data-stu-id="786ce-136">![The Azure account owner associates the Azure AD tenant with the subscription](../../_images/governance-1-4.png)
*Figure 4. The Azure account owner associates the Azure AD tenant with the subscription.*</span></span>

<span data-ttu-id="786ce-137">你可能已注意到，目前没有任何用户与订阅相关联，这意味着，无人有权管理资源。</span><span class="sxs-lookup"><span data-stu-id="786ce-137">You may have noticed that there is currently no user associated with the subscription, which means that no one has permission to manage resources.</span></span> <span data-ttu-id="786ce-138">在现实中，**帐户所有者**是订阅的所有者，有权对订阅中的资源执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="786ce-138">In reality, the **account owner** is the owner of the subscription and has permission to take any action on a resource in the subscription.</span></span> <span data-ttu-id="786ce-139">但是，从实际的术语讲，**帐户所有者**更像是组织中的财务人员，他们不负责创建、读取、更新和删除资源 - 这些任务由**工作负荷所有者**执行。</span><span class="sxs-lookup"><span data-stu-id="786ce-139">However, in practical terms the **account owner** is more than likely a finance person in your organization and is not responsible for creating, reading, updating, and deleting resources - those tasks will be performed by the **workload owner**.</span></span> <span data-ttu-id="786ce-140">因此，需要将工作负荷所有者添加到订阅并分配权限。</span><span class="sxs-lookup"><span data-stu-id="786ce-140">Therefore, you need to add the **workload owner** to the subscription and assign permissions.</span></span>

<span data-ttu-id="786ce-141">由于**帐户所有者**目前是唯一有权将**工作负荷所有者**添加到订阅的用户，因此他们需要将**工作负荷所有者**添加到订阅：</span><span class="sxs-lookup"><span data-stu-id="786ce-141">Since the **account owner** is currently the only user with permission to add the **workload owner** to the subscription, they add the **workload owner** to the subscription:</span></span>

<span data-ttu-id="786ce-142">![Azure 帐户所有者将**工作负荷所有者**添加到订阅](../../_images/governance-1-5.png)
*图 5.Azure 帐户所有者将工作负荷所有者添加到订阅。*</span><span class="sxs-lookup"><span data-stu-id="786ce-142">![The Azure account owner adds the **workload owner** to the subscription](../../_images/governance-1-5.png)
*Figure 5. The Azure account owner adds the workload owner to the subscription.*</span></span>

<span data-ttu-id="786ce-143">Azure **帐户所有者**通过分配[基于角色的访问控制 (RBAC)](/azure/role-based-access-control/) 角色将权限授予**工作负荷所有者**。</span><span class="sxs-lookup"><span data-stu-id="786ce-143">The Azure **account owner** grants permissions to the **workload owner** by assigning a [role-based access control (RBAC)](/azure/role-based-access-control/) role.</span></span> <span data-ttu-id="786ce-144">RBAC 角色指定**工作负荷所有者**对单个资源类型或一组资源类型拥有的一组权限。</span><span class="sxs-lookup"><span data-stu-id="786ce-144">The RBAC role specifies a set of permissions that the **workload owner** has for an individual resource type or a set of resource types.</span></span>

<span data-ttu-id="786ce-145">请注意，在此示例中，为**帐户所有者**分配了[内置的**所有者**角色](/azure/role-based-access-control/built-in-roles#owner)：</span><span class="sxs-lookup"><span data-stu-id="786ce-145">Notice that in this example, the **account owner** has assigned the [built-in **owner** role](/azure/role-based-access-control/built-in-roles#owner):</span></span>

<span data-ttu-id="786ce-146">![为**工作负荷所有者**分配了内置的所有者角色](../../_images/governance-1-6.png)
*图 6.为工作负荷所有者分配了内置的所有者角色。*</span><span class="sxs-lookup"><span data-stu-id="786ce-146">![The **workload owner** was assigned the built-in owner role](../../_images/governance-1-6.png)
*Figure 6. The workload owner was assigned the built-in owner role.*</span></span>

<span data-ttu-id="786ce-147">内置的**所有者**角色在订阅范围向**工作负荷所有者**授予所有权限。</span><span class="sxs-lookup"><span data-stu-id="786ce-147">The built-in **owner** role grants all permissions to the **workload owner** at the subscription scope.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="786ce-148">Azure **帐户所有者**负责与订阅关联的财务承诺，但**工作负荷所有者**拥有相同的权限。</span><span class="sxs-lookup"><span data-stu-id="786ce-148">The Azure **acount owner** is responsible for the financial committment associated with the subscription, but the **workload owner** has the same permissions.</span></span> <span data-ttu-id="786ce-149">**帐户所有者**必须信任**工作负荷所有者**部署订阅预算范围内的资源。</span><span class="sxs-lookup"><span data-stu-id="786ce-149">The **account owner** must trust the **workload owner** to deploy resources that are within the subscription budget.</span></span>

<span data-ttu-id="786ce-150">管理范围的下一个级别是**资源组**级别。</span><span class="sxs-lookup"><span data-stu-id="786ce-150">The next level of management scope is the **resource group** level.</span></span> <span data-ttu-id="786ce-151">资源组是资源的逻辑容器。</span><span class="sxs-lookup"><span data-stu-id="786ce-151">A resource group is a logical container for resources.</span></span> <span data-ttu-id="786ce-152">在资源组级别应用的操作将应用到组中的所有资源。</span><span class="sxs-lookup"><span data-stu-id="786ce-152">Operations applied at the resource group level apply to all resources in a group.</span></span> <span data-ttu-id="786ce-153">此外，必须注意，每个用户的权限继承自下一个更高级别，除非在该范围显式更改了这些权限。</span><span class="sxs-lookup"><span data-stu-id="786ce-153">Also, it's important to note that permissions for each user are inherited from the next level up unless they are explicitly changed at that scope.</span></span>

<span data-ttu-id="786ce-154">为了演示这种继承方式，让我们看一下当**工作负荷所有者**创建资源组时会发生什么情况：</span><span class="sxs-lookup"><span data-stu-id="786ce-154">To illustrate this, let's look at what happens when the **workload owner** creates a resource group:</span></span>

<span data-ttu-id="786ce-155">![**工作负荷所有者**创建资源组](../../_images/governance-1-7.png)
*图 7.工作负荷所有者创建资源组，并在资源组范围继承内置的所有者角色。*</span><span class="sxs-lookup"><span data-stu-id="786ce-155">![The **workload owner** creates a resource group](../../_images/governance-1-7.png)
*Figure 7. The workload owner creates a resource group and inherits the built-in owner role at the resource group scope.*</span></span>

<span data-ttu-id="786ce-156">同样，内置的**所有者**角色在资源组范围向**工作负荷所有者**授予所有权限。</span><span class="sxs-lookup"><span data-stu-id="786ce-156">Again, the built-in **owner** role grants all permissions to the **workload owner** at the resource group scope.</span></span> <span data-ttu-id="786ce-157">如前所述，此角色继承自订阅级别。</span><span class="sxs-lookup"><span data-stu-id="786ce-157">As discussed earlier, this role is inherited from the subscription level.</span></span> <span data-ttu-id="786ce-158">如果在此范围将不同的角色分配到此用户，则只会将此分配应用到此范围。</span><span class="sxs-lookup"><span data-stu-id="786ce-158">If a different role is assigned to this user at this scope, it applies to this scope only.</span></span>

<span data-ttu-id="786ce-159">管理范围的最低级别是**资源**级别。</span><span class="sxs-lookup"><span data-stu-id="786ce-159">The lowest level of management scope is at the **resource** level.</span></span> <span data-ttu-id="786ce-160">在资源级别应用的操作只应用到资源本身。</span><span class="sxs-lookup"><span data-stu-id="786ce-160">Operations applied at the resource level apply only to the resource itself.</span></span> <span data-ttu-id="786ce-161">再次强调，资源级别的权限继承自资源组范围。</span><span class="sxs-lookup"><span data-stu-id="786ce-161">And once again, permissions at the resource level are inherited from resource group scope.</span></span> <span data-ttu-id="786ce-162">例如，让我们看一下当**工作负荷所有者**在资源组中部署[虚拟网络](/azure/virtual-network/virtual-networks-overview)时会发生什么情况：</span><span class="sxs-lookup"><span data-stu-id="786ce-162">For example, let's look at what happens if the **workload owner** deploys a [virtual network](/azure/virtual-network/virtual-networks-overview) into the resource group:</span></span>

<span data-ttu-id="786ce-163">![**工作负荷所有者**创建资源](../../_images/governance-1-8.png)
*图 8.工作负荷所有者创建资源，并在资源范围继承内置的所有者角色。*</span><span class="sxs-lookup"><span data-stu-id="786ce-163">![The **workload owner** creates a resource](../../_images/governance-1-8.png)
*Figure 8. The workload owner creates a resource and inherits the built-in owner role at the resource scope.*</span></span>

<span data-ttu-id="786ce-164">**工作负荷所有者**在资源范围继承所有者角色，这意味着，工作负荷所有者对该虚拟网络拥有所有权限。</span><span class="sxs-lookup"><span data-stu-id="786ce-164">The **workload owner** inherits the owner role at the resource scope, which means the workload owner has all permissions for the virtual network.</span></span>

## <a name="implementing-the-basic-resource-access-management-model"></a><span data-ttu-id="786ce-165">实现基本的资源访问管理模型</span><span class="sxs-lookup"><span data-stu-id="786ce-165">Implementing the basic resource access management model</span></span>

<span data-ttu-id="786ce-166">让我们继续学习，了解如何实现前面设计的治理模型。</span><span class="sxs-lookup"><span data-stu-id="786ce-166">Let's move on to learn how to implement the governance model designed earlier.</span></span>

<span data-ttu-id="786ce-167">若要开始，组织需要一个 Azure 帐户。</span><span class="sxs-lookup"><span data-stu-id="786ce-167">To begin, your organization requires an Azure account.</span></span> <span data-ttu-id="786ce-168">如果组织的某个现有 [Microsoft 企业协议](https://www.microsoft.com/licensing/licensing-programs/enterprise.aspx)不包括 Azure，可以通过做出前期货币承诺来添加 Azure。</span><span class="sxs-lookup"><span data-stu-id="786ce-168">If your organization has an existing [Microsoft Enterprise Agreement](https://www.microsoft.com/licensing/licensing-programs/enterprise.aspx) that does not include Azure, Azure can be added by making an upfront monetary commitment.</span></span> <span data-ttu-id="786ce-169">有关详细信息，请参阅[为企业获得 Azure 许可](https://azure.microsoft.com/pricing/enterprise-agreement/)。</span><span class="sxs-lookup"><span data-stu-id="786ce-169">See [licensing Azure for the enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/) for more information.</span></span>

<span data-ttu-id="786ce-170">创建 Azure 帐户时，需将组织中的某人指定为 Azure **帐户所有者**。</span><span class="sxs-lookup"><span data-stu-id="786ce-170">When your Azure account is created, you specify a person in your organization to be the Azure **account owner**.</span></span> <span data-ttu-id="786ce-171">然后，会默认创建一个 Azure Active Directory (Azure AD) 租户。</span><span class="sxs-lookup"><span data-stu-id="786ce-171">An Azure Active Directory (Azure AD) tenant is then created by default.</span></span> <span data-ttu-id="786ce-172">Azure **帐户所有者**必须为组织中的**工作负荷所有者**[创建用户帐户](/azure/active-directory/add-users-azure-active-directory)。</span><span class="sxs-lookup"><span data-stu-id="786ce-172">Your Azure **account owner** must [create the user account](/azure/active-directory/add-users-azure-active-directory) for the person in your organization who is the **workload owner**.</span></span>

<span data-ttu-id="786ce-173">接下来，Azure **帐户所有者**必须[创建一个订阅](/partner-center/create-a-new-subscription)，并将其[关联到 Azure AD 租户](/azure/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory)。</span><span class="sxs-lookup"><span data-stu-id="786ce-173">Next, your Azure **account owner** must [create a subscription](/partner-center/create-a-new-subscription) and [associate the Azure AD tenant](/azure/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory) with it.</span></span>

<span data-ttu-id="786ce-174">最后，在创建订阅并将其关联到 Azure AD 租户后，可[将**工作负荷所有者**添加到具有内置**所有者**角色的订阅](/azure/billing/billing-add-change-azure-subscription-administrator#add-an-rbac-owner-for-a-subscription-in-azure-portal)。</span><span class="sxs-lookup"><span data-stu-id="786ce-174">Finally, now that the subscription is created and your Azure AD tenant is associated with it, you can [add the **workload owner** to the subscription with the built-in **owner** role](/azure/billing/billing-add-change-azure-subscription-administrator#add-an-rbac-owner-for-a-subscription-in-azure-portal).</span></span>

## <a name="next-steps"></a><span data-ttu-id="786ce-175">后续步骤</span><span class="sxs-lookup"><span data-stu-id="786ce-175">Next steps</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="786ce-176">将基本工作负荷部署到 Azure</span><span class="sxs-lookup"><span data-stu-id="786ce-176">Deploy a basic workload to Azure</span></span>](../../infrastructure/basic-workload.md)
> [!div class="nextstepaction"]
> [<span data-ttu-id="786ce-177">了解多个团队的资源访问权限</span><span class="sxs-lookup"><span data-stu-id="786ce-177">Learn about resource access for multiple teams</span></span>](governance-multiple-teams.md)
