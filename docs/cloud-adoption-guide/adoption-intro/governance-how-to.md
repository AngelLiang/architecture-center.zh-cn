---
title: Azure 调控设计指南
description: 有关配置 Azure 调控措施，使用户能够部署简单工作负荷的指南
author: petertay
ms.openlocfilehash: 78545400fc0b09262dce3d0e577442443468b2ed
ms.sourcegitcommit: c704d5d51c8f9bbab26465941ddcf267040a8459
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2018
ms.locfileid: "39229456"
---
# <a name="azure-governance-design-guide"></a><span data-ttu-id="dfed0-103">Azure 调控设计指南</span><span class="sxs-lookup"><span data-stu-id="dfed0-103">Azure governance design guide</span></span>

<span data-ttu-id="dfed0-104">本设计指南的受众是组织中的中心 IT 角色。</span><span class="sxs-lookup"><span data-stu-id="dfed0-104">The audience for this design guide is the *central IT* persona in your organization.</span></span> <span data-ttu-id="dfed0-105">中心 IT 负责设计和实施组织的云调控体系结构。</span><span class="sxs-lookup"><span data-stu-id="dfed0-105">*Central IT* is responsible for designing and implementing your organization's cloud governance architecture.</span></span> <span data-ttu-id="dfed0-106">根据[什么是云资源调控？](governance-explainer.md)说明文章中所述，调控是指根据组织的目标和要求，对 Azure 资源的使用持续进行管理、监视和审核的过程。</span><span class="sxs-lookup"><span data-stu-id="dfed0-106">As you learned in the [what is cloud resource governance?](governance-explainer.md) explainer, governance refers to the ongoing process of managing, monitoring, and auditing the use of Azure resources to meet the goals and requirements of your organization.</span></span>

<span data-ttu-id="dfed0-107">本指南旨在描述一组假设性的调控目标和要求，并帮助你了解设计组织调控体系结构的过程。</span><span class="sxs-lookup"><span data-stu-id="dfed0-107">The goal of this guidance is to help you learn the process of designing your organization's governance architecture by looking at a set of hypothetical governance goals and requirements.</span></span> <span data-ttu-id="dfed0-108">然后，本指南介绍如何配置 Azure 调控工具以满足这些目标和要求。</span><span class="sxs-lookup"><span data-stu-id="dfed0-108">Then, we'll discuss how to configure Azure's governance tools to meet them.</span></span> 

<span data-ttu-id="dfed0-109">在基础采用阶段，我们的目标是将简单的工作负荷部署到 Azure。</span><span class="sxs-lookup"><span data-stu-id="dfed0-109">In the foundational adoption stage, our goal is to deploy a simple workload to Azure.</span></span> <span data-ttu-id="dfed0-110">这可以满足以下要求：</span><span class="sxs-lookup"><span data-stu-id="dfed0-110">This results in the following requirements:</span></span>
* <span data-ttu-id="dfed0-111">由负责部署和维护简单工作负荷的**工作负荷所有者**进行标识管理。</span><span class="sxs-lookup"><span data-stu-id="dfed0-111">Identity management for a single **workload owner** who is responsible for deploying and maintaining the simple workload.</span></span> <span data-ttu-id="dfed0-112">工作负荷所有者需要有权创建、读取、更新和删除资源，以及有权将这些权限委托给标识管理系统中的其他用户。</span><span class="sxs-lookup"><span data-stu-id="dfed0-112">The workload owner requires permission to create, read, update, and delete resources as well as permission to delegate these rights to other users in the identity management system.</span></span>
* <span data-ttu-id="dfed0-113">将简单工作负荷的所有资源作为单个管理单元进行管理。</span><span class="sxs-lookup"><span data-stu-id="dfed0-113">Manage all resources for the simple workload as a single management unit.</span></span>

## <a name="licensing-azure"></a><span data-ttu-id="dfed0-114">许可 Azure</span><span class="sxs-lookup"><span data-stu-id="dfed0-114">Licensing Azure</span></span>

<span data-ttu-id="dfed0-115">在开始设计调控模型之前，必须了解 Azure 的许可方式。</span><span class="sxs-lookup"><span data-stu-id="dfed0-115">Before we begin designing our governance model, it's important to understand how Azure is licensed.</span></span> <span data-ttu-id="dfed0-116">这是因为，与 Azure 许可证关联的管理帐户对所有 Azure 资源拥有最高级别的访问权限。</span><span class="sxs-lookup"><span data-stu-id="dfed0-116">This is because the administrative accounts associated with your Azure license have the highest level of access to all of your Azure resources.</span></span> <span data-ttu-id="dfed0-117">这些管理帐户构成了调控模型的基础。</span><span class="sxs-lookup"><span data-stu-id="dfed0-117">These administrative accounts form the basis of your governance model.</span></span>  

> [!NOTE]
> <span data-ttu-id="dfed0-118">如果组织的某个现有 [Microsoft 企业协议](https://www.microsoft.com/licensing/licensing-programs/enterprise.aspx)不包括 Azure，可以通过做出前期货币承诺来添加 Azure。</span><span class="sxs-lookup"><span data-stu-id="dfed0-118">If your organization has an existing [Microsoft Enterprise Agreement](https://www.microsoft.com/licensing/licensing-programs/enterprise.aspx) that does not include Azure, Azure can be added by making an upfront monetary commitment.</span></span> <span data-ttu-id="dfed0-119">有关详细信息，请参阅[为企业获得 Azure 许可](https://azure.microsoft.com/pricing/enterprise-agreement/)。</span><span class="sxs-lookup"><span data-stu-id="dfed0-119">See [licensing Azure for the enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/) for more information.</span></span> 

<span data-ttu-id="dfed0-120">将 Azure 添加到组织的企业协议时，系统已提示组织创建 **Azure 帐户**。</span><span class="sxs-lookup"><span data-stu-id="dfed0-120">When Azure added to your organization's Enterprise Agreement, your organization was prompted to create an **Azure account**.</span></span> <span data-ttu-id="dfed0-121">在帐户创建过程中，已创建 **Azure 帐户所有者**，以及具有**全局管理员**帐户的 Azure Active Directory (Azure AD) 租户。</span><span class="sxs-lookup"><span data-stu-id="dfed0-121">During the account creation process, an **Azure account owner** was created, as well as an Azure Active Directory (Azure AD) tenant with a **global administrator** account.</span></span> <span data-ttu-id="dfed0-122">Azure AD 租户是一个逻辑构造，它代表 Azure AD 的安全专用实例。</span><span class="sxs-lookup"><span data-stu-id="dfed0-122">An Azure AD tenant is a logical construct that represents a secure, dedicated instance of Azure AD.</span></span>

<span data-ttu-id="dfed0-123">![充当 Azure 帐户管理者和 Azure AD 全局管理员的 Azure 帐户](../_images/governance-3-0.png)
*图 1.充当 Azure 帐户管理者和 Azure AD 全局管理员的 Azure 帐户。*</span><span class="sxs-lookup"><span data-stu-id="dfed0-123">![Azure account with Azure Account Manager and Azure AD global administrator](../_images/governance-3-0.png)
*Figure 1. An Azure account with an Account Manager and Azure AD Global Administrator.*</span></span>

## <a name="identity-management"></a><span data-ttu-id="dfed0-124">身份管理</span><span class="sxs-lookup"><span data-stu-id="dfed0-124">Identity management</span></span>

<span data-ttu-id="dfed0-125">Azure 只信任 [Azure AD](/azure/active-directory) 进行用户身份验证以及授权用户访问资源，因此，Azure AD 是我们的标识管理系统。</span><span class="sxs-lookup"><span data-stu-id="dfed0-125">Azure only trusts [Azure AD](/azure/active-directory) to authenticate users and authorize user access to resources, so Azure AD is our identity management system.</span></span> <span data-ttu-id="dfed0-126">Azure AD 全局管理员拥有最高级别的权限，可以执行标识相关的所有操作，包括创建用户和分配权限。</span><span class="sxs-lookup"><span data-stu-id="dfed0-126">The Azure AD global administrator has the highest level of permissions and can perform all actions related to identity, including creating users and assigning permissions.</span></span> 

<span data-ttu-id="dfed0-127">我们的要求是由负责部署和维护简单工作负荷的**工作负荷所有者**进行标识管理。</span><span class="sxs-lookup"><span data-stu-id="dfed0-127">Our requirement is identity management for a single **workload owner** who is responsible for deploying and maintaining the simple workload.</span></span> <span data-ttu-id="dfed0-128">工作负荷所有者需要有权创建、读取、更新和删除资源，以及有权将这些权限委托给标识管理系统中的其他用户。</span><span class="sxs-lookup"><span data-stu-id="dfed0-128">The workload owner requires permission to create, read, update, and delete resources as well as permission to delegate these rights to other users in the identity management system.</span></span>

<span data-ttu-id="dfed0-129">Azure AD 全局管理员将为**工作负荷所有者**创建**工作负荷所有者**帐户：</span><span class="sxs-lookup"><span data-stu-id="dfed0-129">Our Azure AD global administrator will create the **workload owner** account for the **workload owner**:</span></span>

<span data-ttu-id="dfed0-130">![Azure AD 全局管理员创建工作负荷所有者帐户](../_images/governance-1-2.png)
*图 2.Azure AD 全局管理员创建工作负荷所有者用户帐户。*</span><span class="sxs-lookup"><span data-stu-id="dfed0-130">![The Azure AD global administrator creates the workload owner account](../_images/governance-1-2.png)
*Figure 2. The Azure AD global administrator creates the workload owner user account.*</span></span>

<span data-ttu-id="dfed0-131">在将此用户添加到**订阅**之前，我们无法分配资源访问权限，因此，我们将在后续两个部分执行该操作。</span><span class="sxs-lookup"><span data-stu-id="dfed0-131">We aren't able to assign resource access permission until this user is added to a **subscription**, so we'll do that in the next two sections.</span></span> 

## <a name="resource-management-scope"></a><span data-ttu-id="dfed0-132">资源管理范围</span><span class="sxs-lookup"><span data-stu-id="dfed0-132">Resource management scope</span></span>

<span data-ttu-id="dfed0-133">随着组织部署的资源不断增多，调控这些资源的复杂性也会不断提高。</span><span class="sxs-lookup"><span data-stu-id="dfed0-133">As the number of resources deployed by your organization grows, the complexity of governing those resources grows as well.</span></span> <span data-ttu-id="dfed0-134">Azure 实施逻辑容器层次结构，使组织能够以不同的粒度级（称为**范围**）管理管理组中的资源。</span><span class="sxs-lookup"><span data-stu-id="dfed0-134">Azure implements a logical container hierarchy to enable your organization to manage your resources in groups at various levels of granularity, also known as **scope**.</span></span> 

<span data-ttu-id="dfed0-135">资源管理范围的最高层级是**订阅**级别。</span><span class="sxs-lookup"><span data-stu-id="dfed0-135">The top level of resource management scope is the **subscription** level.</span></span> <span data-ttu-id="dfed0-136">订阅由建立资金承诺，并负责支付所有订阅相关 Azure 资源费用的 Azure **帐户所有者**创建：</span><span class="sxs-lookup"><span data-stu-id="dfed0-136">A subscription is created by the Azure **account owner**, who establishes the financial commitment and is responsible for paying for all Azure resources associated with the subscription:</span></span>

<span data-ttu-id="dfed0-137">![Azure 帐户所有者创建订阅](../_images/governance-1-3.png)
*图 3.Azure 帐户所有者创建订阅。*</span><span class="sxs-lookup"><span data-stu-id="dfed0-137">![The Azure account owner creates a subscription](../_images/governance-1-3.png)
*Figure 3. The Azure account owner creates a subscription.*</span></span>

<span data-ttu-id="dfed0-138">创建订阅时，Azure **帐户所有者**会将 Azure AD 租户与订阅相关联，此 Azure AD 租户用于对用户进行身份验证和授权：</span><span class="sxs-lookup"><span data-stu-id="dfed0-138">When the subscription is created, the Azure **account owner** associates an Azure AD tenant with the subscription, and this Azure AD tenant is used for authenticating and authorizing users:</span></span>

<span data-ttu-id="dfed0-139">![Azure 帐户所有者将 Azure AD 租户与订阅相关联](../_images/governance-1-4.png)
*图 4.Azure 帐户所有者将 Azure AD 租户与订阅相关联。*</span><span class="sxs-lookup"><span data-stu-id="dfed0-139">![The Azure account owner associates the Azure AD tenant with the subscription](../_images/governance-1-4.png)
*Figure 4. The Azure account owner associates the Azure AD tenant with the subscription.*</span></span>

<span data-ttu-id="dfed0-140">你可能已注意到，目前没有任何用户与订阅相关联，这意味着，无人有权管理资源。</span><span class="sxs-lookup"><span data-stu-id="dfed0-140">You may have noticed that there is currently no user associated with the subscription, which means that no one has permission to manage resources.</span></span> <span data-ttu-id="dfed0-141">在现实中，**帐户所有者**是订阅的所有者，有权对订阅中的资源执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="dfed0-141">In reality, the **account owner** is the owner of the subscription and has permission to take any action on a resource in the subscription.</span></span> <span data-ttu-id="dfed0-142">但是，从实际的术语讲，**帐户所有者**更像是组织中的财务人员，他们不负责创建、读取、更新和删除资源 - 这些任务由**工作负荷所有者**执行。</span><span class="sxs-lookup"><span data-stu-id="dfed0-142">However, in practical terms the **account owner** is more than likely a finance person in your organization and is not responsible for creating, reading, updating, and deleting resources - those tasks will be performed by the **workload owner**.</span></span> <span data-ttu-id="dfed0-143">因此，我们需要将**工作负荷所有者**添加到订阅并分配权限。</span><span class="sxs-lookup"><span data-stu-id="dfed0-143">Therefore, we need to add the **workload owner** to the subscription and assign permissions.</span></span>

<span data-ttu-id="dfed0-144">由于**帐户所有者**目前是唯一有权将**工作负荷所有者**添加到订阅的用户，因此他们需要将**工作负荷所有者**添加到订阅：</span><span class="sxs-lookup"><span data-stu-id="dfed0-144">Since the **account owner** is currently the only user with permission to add the **workload owner** to the subscription, they add the **workload owner** to the subscription:</span></span>

<span data-ttu-id="dfed0-145">![Azure 帐户所有者将**工作负荷所有者**添加到订阅](../_images/governance-1-5.png)
*图 5.Azure 帐户所有者将工作负荷所有者添加到订阅。*</span><span class="sxs-lookup"><span data-stu-id="dfed0-145">![The Azure account owner adds the **workload owner** to the subscription](../_images/governance-1-5.png)
*Figure 5. The Azure account owner adds the workload owner to the subscription.*</span></span>

<span data-ttu-id="dfed0-146">Azure **帐户所有者**通过分配[基于角色的访问控制 (RBAC)](/azure/role-based-access-control/) 角色将权限授予**工作负荷所有者**。</span><span class="sxs-lookup"><span data-stu-id="dfed0-146">The Azure **account owner** grants permissions to the **workload owner** by assigning a [role-based access control (RBAC)](/azure/role-based-access-control/) role.</span></span> <span data-ttu-id="dfed0-147">RBAC 角色指定**工作负荷所有者**对单个资源类型或一组资源类型拥有的一组权限。</span><span class="sxs-lookup"><span data-stu-id="dfed0-147">The RBAC role specifies a set of permissions that the **workload owner** has for an individual resource type or a set of resource types.</span></span>

<span data-ttu-id="dfed0-148">请注意，在此示例中，为**帐户所有者**分配了[内置的**所有者**角色](/azure/role-based-access-control/built-in-roles#owner)：</span><span class="sxs-lookup"><span data-stu-id="dfed0-148">Notice that in this example, the **account owner** has assigned the [built-in **owner** role](/azure/role-based-access-control/built-in-roles#owner):</span></span> 

<span data-ttu-id="dfed0-149">![为**工作负荷所有者**分配了内置的所有者角色](../_images/governance-1-6.png)
*图 6.为工作负荷所有者分配了内置的所有者角色。*</span><span class="sxs-lookup"><span data-stu-id="dfed0-149">![The **workload owner** was assigned the built-in owner role](../_images/governance-1-6.png)
*Figure 6. The workload owner was assigned the built-in owner role.*</span></span>

<span data-ttu-id="dfed0-150">内置的**所有者**角色在订阅范围向**工作负荷所有者**授予所有权限。</span><span class="sxs-lookup"><span data-stu-id="dfed0-150">The built-in **owner** role grants all permissions to the **workload owner** at the subscription scope.</span></span> 

> [!IMPORTANT]
> <span data-ttu-id="dfed0-151">Azure **帐户所有者**负责与订阅关联的财务承诺，但**工作负荷所有者**拥有相同的权限。</span><span class="sxs-lookup"><span data-stu-id="dfed0-151">The Azure **acount owner** is responsible for the financial committment associated with the subscription, but the **workload owner** has the same permissions.</span></span> <span data-ttu-id="dfed0-152">**帐户所有者**必须信任**工作负荷所有者**部署订阅预算范围内的资源。</span><span class="sxs-lookup"><span data-stu-id="dfed0-152">The **account owner** must trust the **workload owner** to deploy resources that are within the subscription budget.</span></span>

<span data-ttu-id="dfed0-153">管理范围的下一个级别是**资源组**级别。</span><span class="sxs-lookup"><span data-stu-id="dfed0-153">The next level of management scope is the **resource group** level.</span></span> <span data-ttu-id="dfed0-154">资源组是资源的逻辑容器。</span><span class="sxs-lookup"><span data-stu-id="dfed0-154">A resource group is a logical container for resources.</span></span> <span data-ttu-id="dfed0-155">在资源组级别应用的操作将应用到组中的所有资源。</span><span class="sxs-lookup"><span data-stu-id="dfed0-155">Operations applied at the resource group level apply to all resources in a group.</span></span> <span data-ttu-id="dfed0-156">此外，必须注意，每个用户的权限继承自下一个更高级别，除非在该范围显式更改了这些权限。</span><span class="sxs-lookup"><span data-stu-id="dfed0-156">Also, it's important to note that permissions for each user are inherited from the next level up unless they are explicitly changed at that scope.</span></span> 

<span data-ttu-id="dfed0-157">为了演示这种继承方式，让我们看一下当**工作负荷所有者**创建资源组时会发生什么情况：</span><span class="sxs-lookup"><span data-stu-id="dfed0-157">To illustrate this, let's look at what happens when the **workload owner** creates a resource group:</span></span>

<span data-ttu-id="dfed0-158">![**工作负荷所有者**创建资源组](../_images/governance-1-7.png)
*图 7.工作负荷所有者创建资源组，并在资源组范围继承内置的所有者角色。*</span><span class="sxs-lookup"><span data-stu-id="dfed0-158">![The **workload owner** creates a resource group](../_images/governance-1-7.png)
*Figure 7. The workload owner creates a resource group and inherits the built-in owner role at the resource group scope.*</span></span>

<span data-ttu-id="dfed0-159">同样，内置的**所有者**角色在资源组范围向**工作负荷所有者**授予所有权限。</span><span class="sxs-lookup"><span data-stu-id="dfed0-159">Again, the built-in **owner** role grants all permissions to the **workload owner** at the resource group scope.</span></span> <span data-ttu-id="dfed0-160">如前所述，此角色继承自订阅级别。</span><span class="sxs-lookup"><span data-stu-id="dfed0-160">As we discussed earlier, this role is inherited from the subscription level.</span></span> <span data-ttu-id="dfed0-161">如果在此范围将不同的角色分配到此用户，则只会将此分配应用到此范围。</span><span class="sxs-lookup"><span data-stu-id="dfed0-161">If a different role is assigned to this user at this scope, it applies to this scope only.</span></span>

<span data-ttu-id="dfed0-162">管理范围的最低级别是**资源**级别。</span><span class="sxs-lookup"><span data-stu-id="dfed0-162">The lowest level of management scope is at the **resource** level.</span></span> <span data-ttu-id="dfed0-163">在资源级别应用的操作只应用到资源本身。</span><span class="sxs-lookup"><span data-stu-id="dfed0-163">Operations applied at the resource level apply only to the resource itself.</span></span> <span data-ttu-id="dfed0-164">再次强调，资源级别的权限继承自资源组范围。</span><span class="sxs-lookup"><span data-stu-id="dfed0-164">And once again, permissions at the resource level are inherited from resource group scope.</span></span> <span data-ttu-id="dfed0-165">例如，让我们看一下当**工作负荷所有者**在资源组中部署[虚拟网络](/azure/virtual-network/virtual-networks-overview)时会发生什么情况：</span><span class="sxs-lookup"><span data-stu-id="dfed0-165">For example, let's look at what happens if the **workload owner** deploys a [virtual network](/azure/virtual-network/virtual-networks-overview) into the resource group:</span></span>

<span data-ttu-id="dfed0-166">![**工作负荷所有者**创建资源](../_images/governance-1-8.png)
*图 8.工作负荷所有者创建资源，并在资源范围继承内置的所有者角色。*</span><span class="sxs-lookup"><span data-stu-id="dfed0-166">![The **workload owner** creates a resource](../_images/governance-1-8.png)
*Figure 8. The workload owner creates a resource and inherits the built-in owner role at the resource scope.*</span></span>

<span data-ttu-id="dfed0-167">**工作负荷所有者**在资源范围继承所有者角色，这意味着，工作负荷所有者对该虚拟网络拥有所有权限。</span><span class="sxs-lookup"><span data-stu-id="dfed0-167">The **workload owner** inherits the owner role at the resource scope, which means the workload owner has all permissions for the virtual network.</span></span> 

## <a name="summary"></a><span data-ttu-id="dfed0-168">摘要</span><span class="sxs-lookup"><span data-stu-id="dfed0-168">Summary</span></span>

<span data-ttu-id="dfed0-169">在本文中，我们已了解到：</span><span class="sxs-lookup"><span data-stu-id="dfed0-169">In this article, you learned:</span></span>

* <span data-ttu-id="dfed0-170">Azure 只信任使用 Azure AD 进行标识管理。</span><span class="sxs-lookup"><span data-stu-id="dfed0-170">Azure only trusts Azure AD for identity management.</span></span>
* <span data-ttu-id="dfed0-171">订阅具有最高的资源管理范围，每个订阅与一个 Azure AD 租户相关联。</span><span class="sxs-lookup"><span data-stu-id="dfed0-171">A subscription has the highest scope of resource management, and each subscription is associated with an Azure AD tenant.</span></span> <span data-ttu-id="dfed0-172">只有关联的 Azure AD 租户中的用户有权访问订阅中的资源。</span><span class="sxs-lookup"><span data-stu-id="dfed0-172">Only users in the associated Azure AD tenant can access resources in the subscription.</span></span>
* <span data-ttu-id="dfed0-173">资源管理范围有三个级别：订阅、资源组和资源。</span><span class="sxs-lookup"><span data-stu-id="dfed0-173">There are three levels of resource management scope: subscription, resource group, and resource.</span></span> <span data-ttu-id="dfed0-174">使用 RBAC 角色在每个范围分配权限。</span><span class="sxs-lookup"><span data-stu-id="dfed0-174">Permissions are assigned at each scope using RBAC roles.</span></span> <span data-ttu-id="dfed0-175">RBAC 角色从更高的范围继承到较低的范围。</span><span class="sxs-lookup"><span data-stu-id="dfed0-175">RBAC roles are inherited from higher scope to lower scope.</span></span>

## <a name="next-steps"></a><span data-ttu-id="dfed0-176">后续步骤</span><span class="sxs-lookup"><span data-stu-id="dfed0-176">Next steps</span></span>

<span data-ttu-id="dfed0-177">返回[基础采用阶段概述](overview.md)，了解如何实施此调控模型。</span><span class="sxs-lookup"><span data-stu-id="dfed0-177">Return to the [foundational adoption stage overview](overview.md) to learn how to implement this goverance model.</span></span> <span data-ttu-id="dfed0-178">然后，选择某种类型的工作负荷并了解如何部署它。</span><span class="sxs-lookup"><span data-stu-id="dfed0-178">Then, select a type of workload and learn how to deploy it.</span></span>