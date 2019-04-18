---
title: CAF：什么是云资源调控？
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.date: 02/11/2019
description: 有关 Azure 上的云资源治理的说明
author: petertaylor9999
ms.openlocfilehash: 0989a5aad8a6290cce07fd71771c690bbd615e0d
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59068848"
---
<!-- markdownlint-disable MD026 -->

# <a name="what-is-cloud-resource-governance"></a><span data-ttu-id="61fe9-103">什么是云资源调控？</span><span class="sxs-lookup"><span data-stu-id="61fe9-103">What is cloud resource governance?</span></span>

<span data-ttu-id="61fe9-104">在中[Azure 如何工作？](what-is-azure.md)，你了解 Azure 是一系列服务器和网络硬件代表用户运行虚拟化的硬件和软件。</span><span class="sxs-lookup"><span data-stu-id="61fe9-104">In [How does Azure work?](what-is-azure.md), you learned that Azure is a collection of servers and networking hardware running virtualized hardware and software on behalf of users.</span></span> <span data-ttu-id="61fe9-105">Azure 使你组织的应用程序开发和 IT 部门可通过轻松地创建、 读取、 更新和删除资源，根据需要具备敏捷性。</span><span class="sxs-lookup"><span data-stu-id="61fe9-105">Azure enables your organization's application development and IT departments to be agile by making it easy to create, read, update, and delete resources as needed.</span></span>

<span data-ttu-id="61fe9-106">但是，虽然不受限制的访问资源可以使开发人员非常灵活，则可以还会导致意外的成本。</span><span class="sxs-lookup"><span data-stu-id="61fe9-106">However, while unrestricted access to resources can make developers very agile, it can also lead to unexpected costs.</span></span> <span data-ttu-id="61fe9-107">例如，某个开发团队获批部署一组用于测试的资源，但完成测试后忘记删除这些资源。</span><span class="sxs-lookup"><span data-stu-id="61fe9-107">For example, a development team might be approved to deploy a set of resources for testing but forget to delete them when testing is complete.</span></span> <span data-ttu-id="61fe9-108">这些资源仍将产生成本，即使它们不再是已批准也没有必要。</span><span class="sxs-lookup"><span data-stu-id="61fe9-108">These resources will continue to accrue costs even though they are no longer approved or necessary.</span></span>

<span data-ttu-id="61fe9-109">解决方法是资源访问管理。</span><span class="sxs-lookup"><span data-stu-id="61fe9-109">The solution is resource access governance.</span></span> <span data-ttu-id="61fe9-110">**监管**是持续的过程的管理、 监视和审核使用 Azure 资源以满足你的组织的要求。</span><span class="sxs-lookup"><span data-stu-id="61fe9-110">**Governance** is the ongoing process of managing, monitoring, and auditing the use of Azure resources to meet the requirements of your organization.</span></span>

<!-- markdownlint-disable MD034 -->

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2ii94]

<!-- markdownlint-enable MD034 -->

<span data-ttu-id="61fe9-111">这些要求是唯一的每个组织，因此监管的通用方法不是很有帮助。</span><span class="sxs-lookup"><span data-stu-id="61fe9-111">These requirements are unique to each organization, so a one-size-fits-all approach to governance isn't helpful.</span></span> <span data-ttu-id="61fe9-112">相反，这取决于每个组织设计其使用 Azure 的两个主要管理工具的监管模型：**基于角色的访问控制 (RBAC)** 并**资源策略**。</span><span class="sxs-lookup"><span data-stu-id="61fe9-112">Instead, it's up to each organization to design their governance model using Azure's two primary governance tools: **role-based access control (RBAC)** and **resource policy**.</span></span>

<span data-ttu-id="61fe9-113">RBAC 定义角色，角色和角色定义的每个用户分配该角色的功能。</span><span class="sxs-lookup"><span data-stu-id="61fe9-113">RBAC defines roles, and roles define the capabilities of each user assigned that role.</span></span> <span data-ttu-id="61fe9-114">例如，**所有者**角色允许所有功能 （创建、 读取、 更新和删除） 的资源，而**读取器**角色都允许仅读取的功能。</span><span class="sxs-lookup"><span data-stu-id="61fe9-114">For example, the **owner** role allows all capabilites (create, read, update, and delete) for a resource, while the  **reader** role allows only the read capability.</span></span> <span data-ttu-id="61fe9-115">可以使用适用于很多的资源类型的广泛范围或适用于少量的狭窄作用域定义角色。</span><span class="sxs-lookup"><span data-stu-id="61fe9-115">Roles can be defined with a broad scope that applies to many resource types, or a narrow scope that applies to a few.</span></span>

<span data-ttu-id="61fe9-116">资源策略定义有关创建资源的规则。</span><span class="sxs-lookup"><span data-stu-id="61fe9-116">Resource policies define rules for resource creation.</span></span> <span data-ttu-id="61fe9-117">例如，资源策略可以限制为特定的预先批准大小的虚拟机的 SKU。</span><span class="sxs-lookup"><span data-stu-id="61fe9-117">For example, a resource policy can limit the SKU of a virtual machine to a particular pre-approved size.</span></span> <span data-ttu-id="61fe9-118">发出请求来创建资源时，另一个资源策略可以强制实施分配的成本中心的标记的应用程序。</span><span class="sxs-lookup"><span data-stu-id="61fe9-118">Another resource policy could enforce the application of a tag for an assigned cost center when the request is made to create the resource.</span></span>

<span data-ttu-id="61fe9-119">在配置这些工具时，务必管理和组织敏捷性之间实现平衡。</span><span class="sxs-lookup"><span data-stu-id="61fe9-119">When configuring these tools, it is important to balance governance and organizational agility.</span></span> <span data-ttu-id="61fe9-120">限制性更强您管理的策略，不太敏捷开发人员和 IT 工作人员将为。</span><span class="sxs-lookup"><span data-stu-id="61fe9-120">The more restrictive your governance policy, the less agile your developers and IT workers will be.</span></span> <span data-ttu-id="61fe9-121">严格的管理策略可能要求更多的手动步骤，如要求填写表单或电子邮件发送给管理团队的成员，才能手动创建资源的开发人员。</span><span class="sxs-lookup"><span data-stu-id="61fe9-121">A restrictive governance policy may require more manual steps like requiring a developer to fill out a form or send an email to a member of the governance team to manually create a resource.</span></span> <span data-ttu-id="61fe9-122">管理团队具有有限的容量，可能会成为瓶颈，从而导致无效率地等待其资源会产生成本，在删除之前创建的或不需要的资源的开发团队。</span><span class="sxs-lookup"><span data-stu-id="61fe9-122">The governance team has finite capacity and may become a bottleneck, resulting in development teams waiting unproductively for their resources to be created or unneeded resources accruing costs before they are deleted.</span></span>

## <a name="next-steps"></a><span data-ttu-id="61fe9-123">后续步骤</span><span class="sxs-lookup"><span data-stu-id="61fe9-123">Next steps</span></span>

<span data-ttu-id="61fe9-124">现在，你已了解云资源调控的概念，了解有关如何在 Azure 中管理资源访问权限的详细信息。</span><span class="sxs-lookup"><span data-stu-id="61fe9-124">Now that you understand the concept of cloud resource governance, learn more about how resource access is managed in Azure.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="61fe9-125">了解 Azure 中的资源访问</span><span class="sxs-lookup"><span data-stu-id="61fe9-125">Learn about resource access in Azure</span></span>](azure-resource-access.md)
