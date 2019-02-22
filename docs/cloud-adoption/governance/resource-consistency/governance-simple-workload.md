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
# <a name="governance-design-for-a-simple-workload"></a>适用于简单工作负荷的治理设计

本指南旨在介绍在 Azure 中设计资源治理模型，以便为单个团队和简单工作负荷提供支持这一过程。 其中探讨了一系列假设性的调控要求，然后详细讲解满足这些要求的几个示例实现。

在基础采用阶段，我们的目标是将简单的工作负荷部署到 Azure。 这可以满足以下要求：

* 由负责部署和维护简单工作负荷的**工作负荷所有者**进行标识管理。 工作负荷所有者需要有权创建、读取、更新和删除资源，以及有权将这些权限委托给标识管理系统中的其他用户。
* 将简单工作负荷的所有资源作为单个管理单元进行管理。

## <a name="licensing-azure"></a>许可 Azure

在开始设计调控模型之前，必须了解 Azure 的许可方式。 这是因为，与 Azure 许可证关联的管理帐户对所有 Azure 资源拥有最高级别的访问权限。 这些管理帐户构成了调控模型的基础。  

> [!NOTE]
> 如果组织的某个现有 [Microsoft 企业协议](https://www.microsoft.com/licensing/licensing-programs/enterprise.aspx)不包括 Azure，可以通过做出前期货币承诺来添加 Azure。 有关详细信息，请参阅[为企业获得 Azure 许可](https://azure.microsoft.com/pricing/enterprise-agreement/)。

将 Azure 添加到组织的企业协议时，系统已提示组织创建 **Azure 帐户**。 在帐户创建过程中，已创建 **Azure 帐户所有者**，以及具有**全局管理员**帐户的 Azure Active Directory (Azure AD) 租户。 Azure AD 租户是一个逻辑构造，它代表 Azure AD 的安全专用实例。

![充当 Azure 帐户管理者和 Azure AD 全局管理员的 Azure 帐户](../../_images/governance-3-0.png)
*图 1.充当 Azure 帐户管理者和 Azure AD 全局管理员的 Azure 帐户。*

## <a name="identity-management"></a>身份管理

Azure 只信任 [Azure AD](/azure/active-directory) 进行用户身份验证以及授权用户访问资源，因此，Azure AD 是我们的标识管理系统。 Azure AD 全局管理员拥有最高级别的权限，可以执行标识相关的所有操作，包括创建用户和分配权限。

我们的要求是由负责部署和维护简单工作负荷的**工作负荷所有者**进行标识管理。 工作负荷所有者需要有权创建、读取、更新和删除资源，以及有权将这些权限委托给标识管理系统中的其他用户。

Azure AD 全局管理员将为**工作负荷所有者**创建**工作负荷所有者**帐户：

![Azure AD 全局管理员创建工作负荷所有者帐户](../../_images/governance-1-2.png)
*图 2.Azure AD 全局管理员创建工作负荷所有者用户帐户。*

在将此用户添加到订阅之前，无法分配资源访问权限，因此将在后续两个部分执行该操作。

## <a name="resource-management-scope"></a>资源管理范围

随着组织部署的资源不断增多，调控这些资源的复杂性也会不断提高。 Azure 实施逻辑容器层次结构，使组织能够以不同的粒度级（称为**范围**）管理管理组中的资源。

资源管理范围的最高层级是**订阅**级别。 订阅由建立资金承诺，并负责支付所有订阅相关 Azure 资源费用的 Azure **帐户所有者**创建：

![Azure 帐户所有者创建订阅](../../_images/governance-1-3.png)
*图 3.Azure 帐户所有者创建订阅。*

创建订阅时，Azure **帐户所有者**会将 Azure AD 租户与订阅相关联，此 Azure AD 租户用于对用户进行身份验证和授权：

![Azure 帐户所有者将 Azure AD 租户与订阅相关联](../../_images/governance-1-4.png)
*图 4.Azure 帐户所有者将 Azure AD 租户与订阅相关联。*

你可能已注意到，目前没有任何用户与订阅相关联，这意味着，无人有权管理资源。 在现实中，**帐户所有者**是订阅的所有者，有权对订阅中的资源执行任何操作。 但是，从实际的术语讲，**帐户所有者**更像是组织中的财务人员，他们不负责创建、读取、更新和删除资源 - 这些任务由**工作负荷所有者**执行。 因此，需要将工作负荷所有者添加到订阅并分配权限。

由于**帐户所有者**目前是唯一有权将**工作负荷所有者**添加到订阅的用户，因此他们需要将**工作负荷所有者**添加到订阅：

![Azure 帐户所有者将**工作负荷所有者**添加到订阅](../../_images/governance-1-5.png)
*图 5.Azure 帐户所有者将工作负荷所有者添加到订阅。*

Azure **帐户所有者**通过分配[基于角色的访问控制 (RBAC)](/azure/role-based-access-control/) 角色将权限授予**工作负荷所有者**。 RBAC 角色指定**工作负荷所有者**对单个资源类型或一组资源类型拥有的一组权限。

请注意，在此示例中，为**帐户所有者**分配了[内置的**所有者**角色](/azure/role-based-access-control/built-in-roles#owner)：

![为**工作负荷所有者**分配了内置的所有者角色](../../_images/governance-1-6.png)
*图 6.为工作负荷所有者分配了内置的所有者角色。*

内置的**所有者**角色在订阅范围向**工作负荷所有者**授予所有权限。

> [!IMPORTANT]
> Azure **帐户所有者**负责与订阅关联的财务承诺，但**工作负荷所有者**拥有相同的权限。 **帐户所有者**必须信任**工作负荷所有者**部署订阅预算范围内的资源。

管理范围的下一个级别是**资源组**级别。 资源组是资源的逻辑容器。 在资源组级别应用的操作将应用到组中的所有资源。 此外，必须注意，每个用户的权限继承自下一个更高级别，除非在该范围显式更改了这些权限。

为了演示这种继承方式，让我们看一下当**工作负荷所有者**创建资源组时会发生什么情况：

![**工作负荷所有者**创建资源组](../../_images/governance-1-7.png)
*图 7.工作负荷所有者创建资源组，并在资源组范围继承内置的所有者角色。*

同样，内置的**所有者**角色在资源组范围向**工作负荷所有者**授予所有权限。 如前所述，此角色继承自订阅级别。 如果在此范围将不同的角色分配到此用户，则只会将此分配应用到此范围。

管理范围的最低级别是**资源**级别。 在资源级别应用的操作只应用到资源本身。 再次强调，资源级别的权限继承自资源组范围。 例如，让我们看一下当**工作负荷所有者**在资源组中部署[虚拟网络](/azure/virtual-network/virtual-networks-overview)时会发生什么情况：

![**工作负荷所有者**创建资源](../../_images/governance-1-8.png)
*图 8.工作负荷所有者创建资源，并在资源范围继承内置的所有者角色。*

**工作负荷所有者**在资源范围继承所有者角色，这意味着，工作负荷所有者对该虚拟网络拥有所有权限。

## <a name="implementing-the-basic-resource-access-management-model"></a>实现基本的资源访问管理模型

让我们继续学习，了解如何实现前面设计的治理模型。

若要开始，组织需要一个 Azure 帐户。 如果组织的某个现有 [Microsoft 企业协议](https://www.microsoft.com/licensing/licensing-programs/enterprise.aspx)不包括 Azure，可以通过做出前期货币承诺来添加 Azure。 有关详细信息，请参阅[为企业获得 Azure 许可](https://azure.microsoft.com/pricing/enterprise-agreement/)。

创建 Azure 帐户时，需将组织中的某人指定为 Azure **帐户所有者**。 然后，会默认创建一个 Azure Active Directory (Azure AD) 租户。 Azure **帐户所有者**必须为组织中的**工作负荷所有者**[创建用户帐户](/azure/active-directory/add-users-azure-active-directory)。

接下来，Azure **帐户所有者**必须[创建一个订阅](/partner-center/create-a-new-subscription)，并将其[关联到 Azure AD 租户](/azure/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory)。

最后，在创建订阅并将其关联到 Azure AD 租户后，可[将**工作负荷所有者**添加到具有内置**所有者**角色的订阅](/azure/billing/billing-add-change-azure-subscription-administrator#add-an-rbac-owner-for-a-subscription-in-azure-portal)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [将基本工作负荷部署到 Azure](../../infrastructure/basic-workload.md)
> [!div class="nextstepaction"]
> [了解多个团队的资源访问权限](governance-multiple-teams.md)
