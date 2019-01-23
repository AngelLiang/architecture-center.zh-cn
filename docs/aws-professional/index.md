---
title: 面向 AWS 专业人员的 Azure
titleSuffix: Azure Architecture Center
description: 了解 Microsoft Azure 帐户、平台和服务的基础知识。 另外，了解 AWS 与 Azure 平台之间的重要相似之处和差别。 在 Azure 中利用 AWS 方面的经验。
keywords: AWS 专家, Azure 比较, AWS 比较, azure 与 aws 之间的差别, azure 与 aws
author: lbrader
ms.date: 09/19/2018
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 56e308b40e24d2febefe995dffc7a14069a5c078
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54482232"
---
# <a name="azure-for-aws-professionals"></a><span data-ttu-id="3913a-106">面向 AWS 专业人员的 Azure</span><span class="sxs-lookup"><span data-stu-id="3913a-106">Azure for AWS Professionals</span></span>

<span data-ttu-id="3913a-107">本文可帮助 Amazon Web Services (AWS) 专家了解 Microsoft Azure 帐户、平台和服务的基础知识。</span><span class="sxs-lookup"><span data-stu-id="3913a-107">This article helps Amazon Web Services (AWS) experts understand the basics of Microsoft Azure accounts, platform, and services.</span></span> <span data-ttu-id="3913a-108">其中介绍了 AWS 与 Azure 平台之间的重要相似之处和差别。</span><span class="sxs-lookup"><span data-stu-id="3913a-108">It also covers key similarities and differences between the AWS and Azure platforms.</span></span>

<span data-ttu-id="3913a-109">学习内容：</span><span class="sxs-lookup"><span data-stu-id="3913a-109">You'll learn:</span></span>

- <span data-ttu-id="3913a-110">帐户和资源在 Azure 中的组织方式。</span><span class="sxs-lookup"><span data-stu-id="3913a-110">How accounts and resources are organized in Azure.</span></span>
- <span data-ttu-id="3913a-111">如何在 Azure 中构建可用的解决方案。</span><span class="sxs-lookup"><span data-stu-id="3913a-111">How available solutions are structured in Azure.</span></span>
- <span data-ttu-id="3913a-112">如何 Azure 服务与 AWS 服务之间有哪些重要差别。</span><span class="sxs-lookup"><span data-stu-id="3913a-112">How the major Azure services differ from AWS services.</span></span>

<span data-ttu-id="3913a-113">长久以来，Azure 和 AWS 各自建立了自身的功能，因此，两者的实施方式和设计具有重要差别。</span><span class="sxs-lookup"><span data-stu-id="3913a-113">Azure and AWS built their capabilities independently over time so that each has important implementation and design differences.</span></span>

## <a name="overview"></a><span data-ttu-id="3913a-114">概述</span><span class="sxs-lookup"><span data-stu-id="3913a-114">Overview</span></span>

<span data-ttu-id="3913a-115">像 AWS 一样，Microsoft Azure 是围绕一组核心计算、存储、数据库和网络服务构建的。</span><span class="sxs-lookup"><span data-stu-id="3913a-115">Like AWS, Microsoft Azure is built around a core set of compute, storage, database, and networking services.</span></span> <span data-ttu-id="3913a-116">在许多情况下，这两个平台在所提供的产品和服务方面具有基本的相似性。</span><span class="sxs-lookup"><span data-stu-id="3913a-116">In many cases, both platforms offer a basic equivalence between the products and services they offer.</span></span> <span data-ttu-id="3913a-117">AWS 和 Azure 都允许基于 Windows 或 Linux 主机构建高可用性解决方案。</span><span class="sxs-lookup"><span data-stu-id="3913a-117">Both AWS and Azure allow you to build highly available solutions based on Windows or Linux hosts.</span></span> <span data-ttu-id="3913a-118">因此，如果你习惯于使用 Linux 和 OSS 技术进行开发，这两个平台都可满足要求。</span><span class="sxs-lookup"><span data-stu-id="3913a-118">So, if you're used to development using Linux and OSS technology, both platforms can do the job.</span></span>

<span data-ttu-id="3913a-119">尽管这两个平台的功能类似，但提供这些功能的资源通常以不同的方式进行组织。</span><span class="sxs-lookup"><span data-stu-id="3913a-119">While the capabilities of both platforms are similar, the resources that provide those capabilities are often organized differently.</span></span> <span data-ttu-id="3913a-120">用于构建解决方案的服务之间的具体一对一关系有时并不明确。</span><span class="sxs-lookup"><span data-stu-id="3913a-120">Exact one-to-one relationships between the services required to build a solution are not always clear.</span></span> <span data-ttu-id="3913a-121">另外，在某些情况下，特定的服务可能已在其中一个平台上提供，但没有在另一个平台上提供。</span><span class="sxs-lookup"><span data-stu-id="3913a-121">There are also cases where a particular service might be offered on one platform, but not the other.</span></span> <span data-ttu-id="3913a-122">请参阅[类似的 Azure 和 AWS 服务图表](./services.md)。</span><span class="sxs-lookup"><span data-stu-id="3913a-122">See [charts of comparable Azure and AWS services](./services.md).</span></span>

## <a name="accounts-and-subscriptions"></a><span data-ttu-id="3913a-123">帐户和订阅</span><span class="sxs-lookup"><span data-stu-id="3913a-123">Accounts and subscriptions</span></span>

<span data-ttu-id="3913a-124">可以根据组织的规模和需求，以多个定价选项购买 Azure 服务。</span><span class="sxs-lookup"><span data-stu-id="3913a-124">Azure services can be purchased using several pricing options, depending on your organization's size and needs.</span></span> <span data-ttu-id="3913a-125">有关详细信息，请参阅[定价概述](https://azure.microsoft.com/pricing/)页。</span><span class="sxs-lookup"><span data-stu-id="3913a-125">See the [pricing overview](https://azure.microsoft.com/pricing/) page for details.</span></span>

<span data-ttu-id="3913a-126">[Azure 订阅](/azure/virtual-machines/linux/infrastructure-example)是一组资源，其中分配的所有者负责进行计费和权限管理。</span><span class="sxs-lookup"><span data-stu-id="3913a-126">[Azure subscriptions](/azure/virtual-machines/linux/infrastructure-example) are a grouping of resources with an assigned owner responsible for billing and permissions management.</span></span> <span data-ttu-id="3913a-127">订阅是独立于其所有者帐户存在的，可根据需要将其重新分配给新的所有者；而 AWS 则与此不同，其中，在 AWS 帐户下创建的所有资源会绑定到该帐户。</span><span class="sxs-lookup"><span data-stu-id="3913a-127">Unlike AWS, where any resources created under the AWS account are tied to that account, subscriptions exist independently of their owner accounts, and can be reassigned to new owners as needed.</span></span>

<!-- markdownlint-disable MD033 -->

<span data-ttu-id="3913a-128">![AWS 帐户与 Azure 订阅的结构和所有权比较](./images/azure-aws-account-compare.png "AWS 帐户与 Azure 订阅的结构和所有权比较")</span><span class="sxs-lookup"><span data-stu-id="3913a-128">![Comparison of structure and ownership of AWS accounts and Azure subscriptions](./images/azure-aws-account-compare.png "Comparison of structure and ownership of AWS accounts and Azure subscriptions")</span></span>
<br/><span data-ttu-id="3913a-129">*AWS 帐户与 Azure 订阅的结构和所有权比较*</span><span class="sxs-lookup"><span data-stu-id="3913a-129">*Comparison of structure and ownership of AWS accounts and Azure subscriptions*</span></span>
<br/><br/>

<!-- markdownlint-enable MD033 -->

<span data-ttu-id="3913a-130">订阅分配给三种类型的管理员帐户：</span><span class="sxs-lookup"><span data-stu-id="3913a-130">Subscriptions are assigned three types of administrator accounts:</span></span>

- <span data-ttu-id="3913a-131">**帐户管理员**。</span><span class="sxs-lookup"><span data-stu-id="3913a-131">**Account Administrator**.</span></span> <span data-ttu-id="3913a-132">针对订阅中所用资源计费的订阅所有者和帐户。</span><span class="sxs-lookup"><span data-stu-id="3913a-132">The subscription owner and the account billed for the resources used in the subscription.</span></span> <span data-ttu-id="3913a-133">只能通过转让订阅所有权来更改帐户管理员。</span><span class="sxs-lookup"><span data-stu-id="3913a-133">The account administrator can only be changed by transferring ownership of the subscription.</span></span>

- <span data-ttu-id="3913a-134">**服务管理员**。</span><span class="sxs-lookup"><span data-stu-id="3913a-134">**Service Administrator**.</span></span> <span data-ttu-id="3913a-135">此帐户有权在订阅中创建和管理资源，但不负责计费。</span><span class="sxs-lookup"><span data-stu-id="3913a-135">This account has rights to create and manage resources in the subscription, but is not responsible for billing.</span></span> <span data-ttu-id="3913a-136">默认情况下，会将帐户管理员和服务管理员分配到同一个帐户。</span><span class="sxs-lookup"><span data-stu-id="3913a-136">By default, the account administrator and service administrator are assigned to the same account.</span></span> <span data-ttu-id="3913a-137">帐户管理员可将一个单独的用户分配到服务管理员帐户，用于管理订阅的技术和操作方面。</span><span class="sxs-lookup"><span data-stu-id="3913a-137">The account administrator can assign a separate user to the service administrator account for managing the technical and operational aspects of a subscription.</span></span> <span data-ttu-id="3913a-138">每个订阅只有一个服务管理员。</span><span class="sxs-lookup"><span data-stu-id="3913a-138">There is only one service administrator per subscription.</span></span>

- <span data-ttu-id="3913a-139">**共同管理员**。</span><span class="sxs-lookup"><span data-stu-id="3913a-139">**Co-administrator**.</span></span> <span data-ttu-id="3913a-140">可将多个共同管理员帐户分配到一个订阅。</span><span class="sxs-lookup"><span data-stu-id="3913a-140">There can be multiple co-administrator accounts assigned to a subscription.</span></span> <span data-ttu-id="3913a-141">协同管理员无法更改服务管理员，但对订阅资源和用户拥有完全控制权。</span><span class="sxs-lookup"><span data-stu-id="3913a-141">Co-administrators cannot change the service administrator, but otherwise have full control over subscription resources and users.</span></span>

<span data-ttu-id="3913a-142">在订阅级别下，还可将用户角色和单个权限分配到特定的资源，类似于在 AWS 中向 IAM 用户和组授予权限。</span><span class="sxs-lookup"><span data-stu-id="3913a-142">Below the subscription level user roles and individual permissions can also be assigned to specific resources, similarly to how permissions are granted to IAM users and groups in AWS.</span></span> <span data-ttu-id="3913a-143">在 Azure 中，所有用户帐户都与 Microsoft 帐户或组织帐户（通过 Azure Active Directory 管理的帐户）相关联。</span><span class="sxs-lookup"><span data-stu-id="3913a-143">In Azure all user accounts are associated with either a Microsoft Account or Organizational Account (an account managed through an Azure Active Directory).</span></span>

<span data-ttu-id="3913a-144">与 AWS 帐户一样，订阅具有默认的服务配额和限制。</span><span class="sxs-lookup"><span data-stu-id="3913a-144">Like AWS accounts, subscriptions have default service quotas and limits.</span></span> <span data-ttu-id="3913a-145">有关这些限制的完整列表，请参阅 [Azure 订阅和服务限制、配额与约束](/azure/azure-subscription-service-limits)。</span><span class="sxs-lookup"><span data-stu-id="3913a-145">For a full list of these limits, see [Azure subscription and service limits, quotas, and constraints](/azure/azure-subscription-service-limits).</span></span>
<span data-ttu-id="3913a-146">可以通过[在管理门户中提出支持请求](https://blogs.msdn.microsoft.com/girishp/2015/09/20/increasing-core-quota-limits-in-azure/)，将这些限制提高到最大值。</span><span class="sxs-lookup"><span data-stu-id="3913a-146">These limits can be increased up to the maximum by [filing a support request in the management portal](https://blogs.msdn.microsoft.com/girishp/2015/09/20/increasing-core-quota-limits-in-azure/).</span></span>

### <a name="see-also"></a><span data-ttu-id="3913a-147">另请参阅</span><span class="sxs-lookup"><span data-stu-id="3913a-147">See also</span></span>

- [<span data-ttu-id="3913a-148">如何添加或更改 Azure 管理员角色</span><span class="sxs-lookup"><span data-stu-id="3913a-148">How to add or change Azure administrator roles</span></span>](/azure/billing/billing-add-change-azure-subscription-administrator)

- [<span data-ttu-id="3913a-149">如何下载 Azure 帐单发票和每日使用数据</span><span class="sxs-lookup"><span data-stu-id="3913a-149">How to download your Azure billing invoice and daily usage data</span></span>](/azure/billing/billing-download-azure-invoice-daily-usage-date)

## <a name="resource-management"></a><span data-ttu-id="3913a-150">资源管理</span><span class="sxs-lookup"><span data-stu-id="3913a-150">Resource management</span></span>

<span data-ttu-id="3913a-151">在 Azure 中，术语“资源”的用法与在 AWS 中一样，表示可在平台中创建或配置的任何计算实例、存储对象、网络设备或其他实体。</span><span class="sxs-lookup"><span data-stu-id="3913a-151">The term "resource" in Azure is used in the same way as in AWS, meaning any compute instance, storage object, networking device, or other entity you can create or configure within the platform.</span></span>

<span data-ttu-id="3913a-152">Azure 资源是通过使用两种模型之一来部署和管理的：[Azure 资源管理器](/azure/azure-resource-manager/resource-group-overview)或早期的 Azure [经典部署模型](/azure/azure-resource-manager/resource-manager-deployment-model)。</span><span class="sxs-lookup"><span data-stu-id="3913a-152">Azure resources are deployed and managed using one of two models: [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview), or the older Azure [classic deployment model](/azure/azure-resource-manager/resource-manager-deployment-model).</span></span> <span data-ttu-id="3913a-153">任何新资源都是使用资源管理器模型创建的。</span><span class="sxs-lookup"><span data-stu-id="3913a-153">Any new resources are created using the Resource Manager model.</span></span>

### <a name="resource-groups"></a><span data-ttu-id="3913a-154">资源组</span><span class="sxs-lookup"><span data-stu-id="3913a-154">Resource groups</span></span>

<span data-ttu-id="3913a-155">Azure 和 AWS 中都包含称作“资源组”的实体，这些实体用于组织 VM、存储和虚拟网络设备等资源。</span><span class="sxs-lookup"><span data-stu-id="3913a-155">Both Azure and AWS have entities called "resource groups" that organize resources such as VMs, storage, and virtual networking devices.</span></span> <span data-ttu-id="3913a-156">但是，不能直接将 [Azure 资源组](/azure/virtual-machines/windows/infrastructure-example)与 AWS 资源组进行比较。</span><span class="sxs-lookup"><span data-stu-id="3913a-156">However, [Azure resource groups](/azure/virtual-machines/windows/infrastructure-example) are not directly comparable to AWS resource groups.</span></span>

<span data-ttu-id="3913a-157">AWS 允许在多个资源组中标记某个资源，而一个 Azure 资源始终与一个资源组相关联。</span><span class="sxs-lookup"><span data-stu-id="3913a-157">While AWS allows a resource to be tagged into multiple resource groups, an Azure resource is always associated with one resource group.</span></span> <span data-ttu-id="3913a-158">在一个资源组中创建的资源可以转移到另一个组，但始终只能在一个资源组中。</span><span class="sxs-lookup"><span data-stu-id="3913a-158">A resource created in one resource group can be moved to another group, but can only be in one resource group at a time.</span></span> <span data-ttu-id="3913a-159">资源组是 Azure 资源管理器使用的基本分组方式。</span><span class="sxs-lookup"><span data-stu-id="3913a-159">Resource groups are the fundamental grouping used by Azure Resource Manager.</span></span>

<span data-ttu-id="3913a-160">也可以使用[标记](/azure/azure-resource-manager/resource-group-using-tags)来组织资源。</span><span class="sxs-lookup"><span data-stu-id="3913a-160">Resources can also be organized using [tags](/azure/azure-resource-manager/resource-group-using-tags).</span></span> <span data-ttu-id="3913a-161">标记是一些键值对，用于将整个订阅中的资源分组，不管资源组的成员身份如何。</span><span class="sxs-lookup"><span data-stu-id="3913a-161">Tags are key-value pairs that allow you to group resources across your subscription irrespective of resource group membership.</span></span>

### <a name="management-interfaces"></a><span data-ttu-id="3913a-162">管理界面</span><span class="sxs-lookup"><span data-stu-id="3913a-162">Management interfaces</span></span>

<span data-ttu-id="3913a-163">Azure 提供多种方式用于管理资源：</span><span class="sxs-lookup"><span data-stu-id="3913a-163">Azure offers several ways to manage your resources:</span></span>

- <span data-ttu-id="3913a-164">[Web 界面](/azure/azure-resource-manager/resource-group-portal)。</span><span class="sxs-lookup"><span data-stu-id="3913a-164">[Web interface](/azure/azure-resource-manager/resource-group-portal).</span></span>
    <span data-ttu-id="3913a-165">Azure 门户针对 Azure 资源提供类似于 AWS 仪表板的基于 Web 的完整管理界面。</span><span class="sxs-lookup"><span data-stu-id="3913a-165">Like the AWS Dashboard, the Azure portal provides a full web-based management interface for Azure resources.</span></span>

- <span data-ttu-id="3913a-166">[REST API](/rest/api/)。</span><span class="sxs-lookup"><span data-stu-id="3913a-166">[REST API](/rest/api/).</span></span>
    <span data-ttu-id="3913a-167">使用 Azure 资源管理器 REST API 能以编程方式访问 Azure 门户中提供的大部分功能。</span><span class="sxs-lookup"><span data-stu-id="3913a-167">The Azure Resource Manager REST API provides programmatic access to most of the features available in the Azure portal.</span></span>

- <span data-ttu-id="3913a-168">[命令行](/azure/azure-resource-manager/cli-azure-resource-manager)。</span><span class="sxs-lookup"><span data-stu-id="3913a-168">[Command Line](/azure/azure-resource-manager/cli-azure-resource-manager).</span></span>
    <span data-ttu-id="3913a-169">Azure CLI 2.0 工具提供一个可以创建和管理 Azure 资源的命令行接口。</span><span class="sxs-lookup"><span data-stu-id="3913a-169">The Azure CLI 2.0 tool provides a command-line interface capable of creating and managing Azure resources.</span></span> <span data-ttu-id="3913a-170">Azure CLI 适用于 [Windows、Linux 和 Mac OS](https://aka.ms/azurecli2)。</span><span class="sxs-lookup"><span data-stu-id="3913a-170">Azure CLI is available for [Windows, Linux, and Mac OS](https://aka.ms/azurecli2).</span></span>

- <span data-ttu-id="3913a-171">[PowerShell](/azure/azure-resource-manager/powershell-azure-resource-manager)。</span><span class="sxs-lookup"><span data-stu-id="3913a-171">[PowerShell](/azure/azure-resource-manager/powershell-azure-resource-manager).</span></span>
    <span data-ttu-id="3913a-172">借助 PowerShell 的 Azure 模块，可以使用脚本执行自动化管理任务。</span><span class="sxs-lookup"><span data-stu-id="3913a-172">The Azure modules for PowerShell allow you to execute automated management tasks using a script.</span></span> <span data-ttu-id="3913a-173">PowerShell 适用于 [Windows、Linux 和 Mac OS](https://github.com/PowerShell/PowerShell)。</span><span class="sxs-lookup"><span data-stu-id="3913a-173">PowerShell is available for [Windows, Linux, and Mac OS](https://github.com/PowerShell/PowerShell).</span></span>

- <span data-ttu-id="3913a-174">[模板](/azure/azure-resource-manager/resource-group-authoring-templates)。</span><span class="sxs-lookup"><span data-stu-id="3913a-174">[Templates](/azure/azure-resource-manager/resource-group-authoring-templates).</span></span>
    <span data-ttu-id="3913a-175">Azure 资源管理器模板提供类似于 AWS CloudFormation 服务的基于 JSON 模板的资源管理功能。</span><span class="sxs-lookup"><span data-stu-id="3913a-175">Azure Resource Manager templates provide similar JSON template-based resource management capabilities to the AWS CloudFormation service.</span></span>

<span data-ttu-id="3913a-176">在其中每个接口中，都是围绕资源组创建、部署或修改 Azure 资源。</span><span class="sxs-lookup"><span data-stu-id="3913a-176">In each of these interfaces, the resource group is central to how Azure resources get created, deployed, or modified.</span></span> <span data-ttu-id="3913a-177">这类似于在执行 CloudFormation 部署过程中，“堆栈”在分组 AWS 资源时所扮演的角色。</span><span class="sxs-lookup"><span data-stu-id="3913a-177">This is similar to the role a "stack" plays in grouping AWS resources during CloudFormation deployments.</span></span>

<span data-ttu-id="3913a-178">这些接口的语法和结构与其 AWS 等效接口不同，但提供类似的功能。</span><span class="sxs-lookup"><span data-stu-id="3913a-178">The syntax and structure of these interfaces are different from their AWS equivalents, but they provide comparable capabilities.</span></span> <span data-ttu-id="3913a-179">此外，在 AWS 中使用的许多第三方管理工具，例如 [Hashicorp 的 Terraform](https://www.terraform.io/docs/providers/azurerm/) 和 [Netflix Spinnaker](https://www.spinnaker.io/)，在 Azure 中也可用。</span><span class="sxs-lookup"><span data-stu-id="3913a-179">In addition, many third party management tools used on AWS, like [Hashicorp's Terraform](https://www.terraform.io/docs/providers/azurerm/) and [Netflix Spinnaker](https://www.spinnaker.io/), are also available on Azure.</span></span>

<!-- markdownlint-disable MD024 -->

### <a name="see-also"></a><span data-ttu-id="3913a-180">另请参阅</span><span class="sxs-lookup"><span data-stu-id="3913a-180">See also</span></span>

- [<span data-ttu-id="3913a-181">Azure 资源组准则</span><span class="sxs-lookup"><span data-stu-id="3913a-181">Azure resource group guidelines</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)

## <a name="regions-and-zones-high-availability"></a><span data-ttu-id="3913a-182">区域 (Region) 和局部区域 (Zone)（高可用性）</span><span class="sxs-lookup"><span data-stu-id="3913a-182">Regions and zones (high availability)</span></span>

<span data-ttu-id="3913a-183">故障的影响范围各不相同。</span><span class="sxs-lookup"><span data-stu-id="3913a-183">Failures can vary in the scope of their impact.</span></span> <span data-ttu-id="3913a-184">某些硬件故障（例如磁盘故障）可能影响单个主机。</span><span class="sxs-lookup"><span data-stu-id="3913a-184">Some hardware failures, such as a failed disk, may affect a single host machine.</span></span> <span data-ttu-id="3913a-185">网络交换机故障可能影响整个服务器机架。</span><span class="sxs-lookup"><span data-stu-id="3913a-185">A failed network switch could affect a whole server rack.</span></span> <span data-ttu-id="3913a-186">中断整个数据中心的故障（例如数据中心断电）不太常见。</span><span class="sxs-lookup"><span data-stu-id="3913a-186">Less common are failures that disrupt a whole data center, such as loss of power in a data center.</span></span> <span data-ttu-id="3913a-187">在极少数情况下，整个区域可能不可用。</span><span class="sxs-lookup"><span data-stu-id="3913a-187">Rarely, an entire region could become unavailable.</span></span>

<span data-ttu-id="3913a-188">冗余是让应用程序保持弹性的方法之一。</span><span class="sxs-lookup"><span data-stu-id="3913a-188">One of the main ways to make an application resilient is through redundancy.</span></span> <span data-ttu-id="3913a-189">但是，需要在计划应用程序时规划这种冗余。</span><span class="sxs-lookup"><span data-stu-id="3913a-189">But you need to plan for this redundancy when you design the application.</span></span> <span data-ttu-id="3913a-190">此外，所需的冗余级别取决于业务要求 &mdash; 并非每个应用程序都需要跨区域的冗余才能防范区域性服务中断。</span><span class="sxs-lookup"><span data-stu-id="3913a-190">Also, the level of redundancy that you need depends on your business requirements &mdash; not every application needs redundancy across regions to guard against a regional outage.</span></span> <span data-ttu-id="3913a-191">一般情况下，提高冗余和可靠性的弊端就是增大成本和复杂性。</span><span class="sxs-lookup"><span data-stu-id="3913a-191">In general, there is a tradeoff between greater redundancy and reliability versus higher cost and complexity.</span></span>

<span data-ttu-id="3913a-192">在 AWS 中，一个区域划分为两个或更多个可用性区域。</span><span class="sxs-lookup"><span data-stu-id="3913a-192">In AWS, a region is divided into two or more Availability Zones.</span></span> <span data-ttu-id="3913a-193">可用性区域对应于某个地理区域中物理隔离的数据中心。</span><span class="sxs-lookup"><span data-stu-id="3913a-193">An Availability Zone corresponds with a physically isolated datacenter in the geographic region.</span></span> <span data-ttu-id="3913a-194">为了使应用程序可在任何故障级别冗余，Azure 提供了许多功能，包括**可用性集**、**可用性区域**和**配对区域**。</span><span class="sxs-lookup"><span data-stu-id="3913a-194">Azure has a number of features to make an application redundant at every level of failure, including **availability sets**, **availability zones**, and **paired regions**.</span></span>

![冗余](../resiliency/images/redundancy.svg)

<span data-ttu-id="3913a-196">下表汇总了各个选项。</span><span class="sxs-lookup"><span data-stu-id="3913a-196">The following table summarizes each option.</span></span>

| &nbsp; | <span data-ttu-id="3913a-197">可用性集</span><span class="sxs-lookup"><span data-stu-id="3913a-197">Availability Set</span></span> | <span data-ttu-id="3913a-198">可用性区域</span><span class="sxs-lookup"><span data-stu-id="3913a-198">Availability Zone</span></span> | <span data-ttu-id="3913a-199">配对区域</span><span class="sxs-lookup"><span data-stu-id="3913a-199">Paired region</span></span> |
|--------|------------------|-------------------|---------------|
| <span data-ttu-id="3913a-200">故障范围</span><span class="sxs-lookup"><span data-stu-id="3913a-200">Scope of failure</span></span> | <span data-ttu-id="3913a-201">机架</span><span class="sxs-lookup"><span data-stu-id="3913a-201">Rack</span></span> | <span data-ttu-id="3913a-202">数据中心</span><span class="sxs-lookup"><span data-stu-id="3913a-202">Datacenter</span></span> | <span data-ttu-id="3913a-203">区域</span><span class="sxs-lookup"><span data-stu-id="3913a-203">Region</span></span> |
| <span data-ttu-id="3913a-204">请求路由</span><span class="sxs-lookup"><span data-stu-id="3913a-204">Request routing</span></span> | <span data-ttu-id="3913a-205">负载均衡器</span><span class="sxs-lookup"><span data-stu-id="3913a-205">Load Balancer</span></span> | <span data-ttu-id="3913a-206">跨区域负载均衡器</span><span class="sxs-lookup"><span data-stu-id="3913a-206">Cross-zone Load Balancer</span></span> | <span data-ttu-id="3913a-207">流量管理器</span><span class="sxs-lookup"><span data-stu-id="3913a-207">Traffic Manager</span></span> |
| <span data-ttu-id="3913a-208">网络延迟</span><span class="sxs-lookup"><span data-stu-id="3913a-208">Network latency</span></span> | <span data-ttu-id="3913a-209">极低</span><span class="sxs-lookup"><span data-stu-id="3913a-209">Very low</span></span> | <span data-ttu-id="3913a-210">低</span><span class="sxs-lookup"><span data-stu-id="3913a-210">Low</span></span> | <span data-ttu-id="3913a-211">中到高</span><span class="sxs-lookup"><span data-stu-id="3913a-211">Mid to high</span></span> |
| <span data-ttu-id="3913a-212">虚拟网络</span><span class="sxs-lookup"><span data-stu-id="3913a-212">Virtual networking</span></span>  | <span data-ttu-id="3913a-213">VNet</span><span class="sxs-lookup"><span data-stu-id="3913a-213">VNet</span></span> | <span data-ttu-id="3913a-214">VNet</span><span class="sxs-lookup"><span data-stu-id="3913a-214">VNet</span></span> | <span data-ttu-id="3913a-215">跨区域 VNet 对等互连</span><span class="sxs-lookup"><span data-stu-id="3913a-215">Cross-region VNet peering</span></span> |

### <a name="availability-sets"></a><span data-ttu-id="3913a-216">可用性集</span><span class="sxs-lookup"><span data-stu-id="3913a-216">Availability sets</span></span>

<span data-ttu-id="3913a-217">若要防范局部性硬件故障（例如磁盘或网络交换机故障），请在可用性集中部署两个或更多个 VM。</span><span class="sxs-lookup"><span data-stu-id="3913a-217">To protect against localized hardware failures, such as a disk or network switch failing, deploy two or more VMs in an availability set.</span></span> <span data-ttu-id="3913a-218">可用性集包括两个或更多个容错域，它们共用一个电源和网络交换机。</span><span class="sxs-lookup"><span data-stu-id="3913a-218">An availability set consists of two or more *fault domains* that share a common power source and network switch.</span></span> <span data-ttu-id="3913a-219">可用性集中的 VM 分布在不同的容错域中，因此，如果硬件故障影响了一个容错域，仍可将网络流量路由到其他容错域中的 VM。</span><span class="sxs-lookup"><span data-stu-id="3913a-219">VMs in an availability set are distributed across the fault domains, so if a hardware failure affects one fault domain, network traffic can still be routed the VMs in the other fault domains.</span></span> <span data-ttu-id="3913a-220">有关可用性集的详细信息，请参阅[在 Azure 中管理 Windows 虚拟机的可用性](/azure/virtual-machines/windows/manage-availability)。</span><span class="sxs-lookup"><span data-stu-id="3913a-220">For more information about Availability Sets, see [Manage the availability of Windows virtual machines in Azure](/azure/virtual-machines/windows/manage-availability).</span></span>

<span data-ttu-id="3913a-221">将 VM 实例添加到可用性集后，还会为这些实例分配一个[更新域](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-manage-availability/)。</span><span class="sxs-lookup"><span data-stu-id="3913a-221">When VM instances are added to availability sets, they are also assigned an [update domain](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-manage-availability/).</span></span> <span data-ttu-id="3913a-222">更新域是针对同时执行的计划内维护事件设置的一组 VM。</span><span class="sxs-lookup"><span data-stu-id="3913a-222">An update domain is a group of VMs that are set for planned maintenance events at the same time.</span></span> <span data-ttu-id="3913a-223">在多个更新域之间分配 VM 可以确保在任意给定时间，计划内更新和修补事件只会影响其中的一部分 VM。</span><span class="sxs-lookup"><span data-stu-id="3913a-223">Distributing VMs across multiple update domains ensures that planned update and patching events affect only a subset of these VMs at any given time.</span></span>

<span data-ttu-id="3913a-224">应该根据实例在应用程序中的角色来组织可用性集，确保每个角色都有一个正常运行的实例。</span><span class="sxs-lookup"><span data-stu-id="3913a-224">Availability sets should be organized by the instance's role in your application to ensure one instance in each role is operational.</span></span> <span data-ttu-id="3913a-225">例如，在三层 Web 应用程序中，为前端、应用程序和数据层创建单独的可用性集。</span><span class="sxs-lookup"><span data-stu-id="3913a-225">For example, in a three-tier web application, create separate availability sets for the front-end, application, and data tiers.</span></span>

<span data-ttu-id="3913a-226">![每个应用程序角色的 Azure 可用性集](./images/three-tier-example.png "每个应用程序角色的可用性集")</span><span class="sxs-lookup"><span data-stu-id="3913a-226">![Azure availability sets for each application role](./images/three-tier-example.png "Availability sets for each application role")</span></span>

### <a name="availability-zones"></a><span data-ttu-id="3913a-227">可用性区域</span><span class="sxs-lookup"><span data-stu-id="3913a-227">Availability zones</span></span>

<span data-ttu-id="3913a-228">[可用性区域](/azure/availability-zones/az-overview)是 Azure 区域中在物理上独立的区域。</span><span class="sxs-lookup"><span data-stu-id="3913a-228">An [Availability Zone](/azure/availability-zones/az-overview) is a physically separate zone within an Azure region.</span></span> <span data-ttu-id="3913a-229">每个可用性区域有独立的电源、网络和散热设备。</span><span class="sxs-lookup"><span data-stu-id="3913a-229">Each Availability Zone has a distinct power source, network, and cooling.</span></span> <span data-ttu-id="3913a-230">跨可用性区域部署 VM 有助于在发生数据中心范围的故障时保护应用程序。</span><span class="sxs-lookup"><span data-stu-id="3913a-230">Deploying VMs across availability zones helps to protect an application against datacenter-wide failures.</span></span>

### <a name="paired-regions"></a><span data-ttu-id="3913a-231">配对区域</span><span class="sxs-lookup"><span data-stu-id="3913a-231">Paired regions</span></span>

<span data-ttu-id="3913a-232">若要使应用程序免受区域性服务中断的影响，可以[使用 Azure 流量管理器][traffic-manager]将 Internet 流量分配到不同的区域，从而跨多个区域部署应用程序。</span><span class="sxs-lookup"><span data-stu-id="3913a-232">To protect an application against a regional outage, you can deploy the application across multiple regions, using [Azure Traffic Manager][traffic-manager] to distribute internet traffic to the different regions.</span></span> <span data-ttu-id="3913a-233">每个 Azure 区域与另一个区域配对。</span><span class="sxs-lookup"><span data-stu-id="3913a-233">Each Azure region is paired with another region.</span></span> <span data-ttu-id="3913a-234">它们共同构成了[区域对][paired-regions]。</span><span class="sxs-lookup"><span data-stu-id="3913a-234">Together, these form a [regional pair][paired-regions].</span></span> <span data-ttu-id="3913a-235">除巴西南部以外，区域对位于同一区域，以符合税务和执法管辖范围方面的数据驻留要求。</span><span class="sxs-lookup"><span data-stu-id="3913a-235">With the exception of Brazil South, regional pairs are located within the same geography in order to meet data residency requirements for tax and law enforcement jurisdiction purposes.</span></span>

<span data-ttu-id="3913a-236">配对区域通常至少相隔 300 英里，这与可用性区域不同，后者虽然是在物理上独立的数据中心，但可能位于相对邻近的地理区域中。</span><span class="sxs-lookup"><span data-stu-id="3913a-236">Unlike Availability Zones, which are physically separate datacenters but may be in relatively nearby geographic areas, paired regions are usually separated by at least 300 miles.</span></span> <span data-ttu-id="3913a-237">保持这种远距离的目的是确保大规模的灾难只会影响配对中的一个区域。</span><span class="sxs-lookup"><span data-stu-id="3913a-237">This is intended to ensure larger scale disasters only impact one of the regions in the pair.</span></span> <span data-ttu-id="3913a-238">可将邻近的配对设置为同步数据库和存储服务数据，并对其进行适当的配置，以便每次只将平台更新部署到配对中的一个区域。</span><span class="sxs-lookup"><span data-stu-id="3913a-238">Neighboring pairs can be set to sync database and storage service data, and are configured so that platform updates are rolled out to only one region in the pair at a time.</span></span>

<span data-ttu-id="3913a-239">Azure [异地冗余存储](/azure/storage/common/storage-redundancy-grs)会自动备份到相应的配对区域。</span><span class="sxs-lookup"><span data-stu-id="3913a-239">Azure [geo-redundant storage](/azure/storage/common/storage-redundancy-grs) is automatically backed up to the appropriate paired region.</span></span> <span data-ttu-id="3913a-240">对于其他所有资源而言，使用配对区域创建完全冗余的解决方案意味着需要在两个区域中创建该解决方案的完整副本。</span><span class="sxs-lookup"><span data-stu-id="3913a-240">For all other resources, creating a fully redundant solution using paired regions means creating a full copy of your solution in both regions.</span></span>

### <a name="see-also"></a><span data-ttu-id="3913a-241">另请参阅</span><span class="sxs-lookup"><span data-stu-id="3913a-241">See also</span></span>

- [<span data-ttu-id="3913a-242">Azure 中虚拟机的区域和可用性</span><span class="sxs-lookup"><span data-stu-id="3913a-242">Regions and availability for virtual machines in Azure</span></span>](/azure/virtual-machines/linux/regions-and-availability)

- [<span data-ttu-id="3913a-243">Azure 应用程序的高可用性</span><span class="sxs-lookup"><span data-stu-id="3913a-243">High availability for Azure applications</span></span>](../resiliency/high-availability-azure-applications.md)

- [<span data-ttu-id="3913a-244">Azure 应用程序的灾难恢复</span><span class="sxs-lookup"><span data-stu-id="3913a-244">Disaster recovery for Azure applications</span></span>](../resiliency/disaster-recovery-azure-applications.md)

- [<span data-ttu-id="3913a-245">Azure 中 Linux 虚拟机的计划内维护</span><span class="sxs-lookup"><span data-stu-id="3913a-245">Planned maintenance for Linux virtual machines in Azure</span></span>](/azure/virtual-machines/linux/maintenance-and-updates)

## <a name="services"></a><span data-ttu-id="3913a-246">服务</span><span class="sxs-lookup"><span data-stu-id="3913a-246">Services</span></span>

<span data-ttu-id="3913a-247">有关平台之间的服务映射方式的列表，请参阅[比较 AWS 与 Azure 服务](./services.md)。</span><span class="sxs-lookup"><span data-stu-id="3913a-247">For a listing of how services map between platforms, see [AWS to Azure services comparison](./services.md).</span></span>

<span data-ttu-id="3913a-248">并非所有 Azure 产品和服务在所有区域中都可用。</span><span class="sxs-lookup"><span data-stu-id="3913a-248">Not all Azure products and services are available in all regions.</span></span> <span data-ttu-id="3913a-249">有关详细信息，请参阅[各区域中推出的产品](https://azure.microsoft.com/global-infrastructure/services/)页。</span><span class="sxs-lookup"><span data-stu-id="3913a-249">Consult the [Products by Region](https://azure.microsoft.com/global-infrastructure/services/) page for details.</span></span> <span data-ttu-id="3913a-250">可在[服务级别协议](https://azure.microsoft.com/support/legal/sla/)页上找到每个 Azure 产品或服务的运行时间保证以及停机时间信用政策。</span><span class="sxs-lookup"><span data-stu-id="3913a-250">You can find the uptime guarantees and downtime credit policies for each Azure product or service on the [Service Level Agreements](https://azure.microsoft.com/support/legal/sla/) page.</span></span>

<span data-ttu-id="3913a-251">以下部分提供 AWS 与 Azure 平台中常用功能和服务的差别的简要说明。</span><span class="sxs-lookup"><span data-stu-id="3913a-251">The following sections provide a brief explanation of how commonly used features and services differ between the AWS and Azure platforms.</span></span>

### <a name="compute-services"></a><span data-ttu-id="3913a-252">计算服务</span><span class="sxs-lookup"><span data-stu-id="3913a-252">Compute services</span></span>

#### <a name="ec2-instances-and-azure-virtual-machines"></a><span data-ttu-id="3913a-253">EC2 实例和 Azure 虚拟机</span><span class="sxs-lookup"><span data-stu-id="3913a-253">EC2 Instances and Azure virtual machines</span></span>

<span data-ttu-id="3913a-254">尽管 AWS 实例类型与 Azure 虚拟机大小的划分方式类似，但 RAM、CPU 和存储功能方面存在一些差别。</span><span class="sxs-lookup"><span data-stu-id="3913a-254">Although AWS instance types and Azure virtual machine sizes breakdown in a similar way, there are differences in the RAM, CPU, and storage capabilities.</span></span>

- [<span data-ttu-id="3913a-255">Amazon EC2 实例类型</span><span class="sxs-lookup"><span data-stu-id="3913a-255">Amazon EC2 Instance Types</span></span>](https://aws.amazon.com/ec2/instance-types/)

- [<span data-ttu-id="3913a-256">Azure 中虚拟机的大小 (Windows)</span><span class="sxs-lookup"><span data-stu-id="3913a-256">Sizes for virtual machines in Azure (Windows)</span></span>](/azure/virtual-machines/windows/sizes)

- [<span data-ttu-id="3913a-257">Azure 中虚拟机的大小 (Linux)</span><span class="sxs-lookup"><span data-stu-id="3913a-257">Sizes for virtual machines in Azure (Linux)</span></span>](/azure/virtual-machines/linux/sizes)

<span data-ttu-id="3913a-258">与 AWS 的按秒计费类似，Azure 按需 VM 是按秒计费的。</span><span class="sxs-lookup"><span data-stu-id="3913a-258">Similar to AWS' per second billing, Azure on-demand VMs are billed per second.</span></span>

#### <a name="ebs-and-azure-storage-for-vm-disks"></a><span data-ttu-id="3913a-259">EBS 与用作 VM 磁盘的 Azure 存储</span><span class="sxs-lookup"><span data-stu-id="3913a-259">EBS and Azure Storage for VM disks</span></span>

<span data-ttu-id="3913a-260">Azure VM 的持久性数据存储是通过驻留在 Blob 存储中的[数据磁盘](/azure/virtual-machines/linux/about-disks-and-vhds)提供的。</span><span class="sxs-lookup"><span data-stu-id="3913a-260">Durable data storage for Azure VMs is provided by [data disks](/azure/virtual-machines/linux/about-disks-and-vhds) residing in blob storage.</span></span> <span data-ttu-id="3913a-261">这类似于 EC2 实例在弹性块存储 (EBS) 中存储磁盘卷。</span><span class="sxs-lookup"><span data-stu-id="3913a-261">This is similar to how EC2 instances store disk volumes on Elastic Block Store (EBS).</span></span> <span data-ttu-id="3913a-262">与 EC2 实例存储（也称为临时性存储）一样，[Azure 临时存储](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)也为 VM 提供低延迟的临时读写存储。</span><span class="sxs-lookup"><span data-stu-id="3913a-262">[Azure temporary storage](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/) also provides VMs the same low-latency temporary read-write storage as EC2 Instance Storage (also called ephemeral storage).</span></span>

<span data-ttu-id="3913a-263">使用 [Azure 高级存储](/azure/virtual-machines/windows/premium-storage)可以支持更高性能的磁盘 IO。</span><span class="sxs-lookup"><span data-stu-id="3913a-263">Higher performance disk IO is supported using [Azure premium storage](/azure/virtual-machines/windows/premium-storage).</span></span> <span data-ttu-id="3913a-264">这类似于 AWS 提供的预配 IOPS 存储选项。</span><span class="sxs-lookup"><span data-stu-id="3913a-264">This is similar to the Provisioned IOPS storage options provided by AWS.</span></span>

#### <a name="lambda-azure-functions-azure-web-jobs-and-azure-logic-apps"></a><span data-ttu-id="3913a-265">Lambda、Azure Functions、Azure Web 作业和 Azure 逻辑应用</span><span class="sxs-lookup"><span data-stu-id="3913a-265">Lambda, Azure Functions, Azure Web-Jobs, and Azure Logic Apps</span></span>

<span data-ttu-id="3913a-266">[Azure Functions](https://azure.microsoft.com/services/functions/) 基本上相当于 AWS Lambda，提供无服务器的按需代码。</span><span class="sxs-lookup"><span data-stu-id="3913a-266">[Azure Functions](https://azure.microsoft.com/services/functions/) is the primary equivalent of AWS Lambda in providing serverless, on-demand code.</span></span> <span data-ttu-id="3913a-267">但是，Lambda 功能还与其他 Azure 服务重叠：</span><span class="sxs-lookup"><span data-stu-id="3913a-267">However, Lambda functionality also overlaps with other Azure services:</span></span>

- <span data-ttu-id="3913a-268">[Web 作业](/azure/app-service/web-sites-create-web-jobs)可用于创建计划的后台任务或连续运行的后台任务。</span><span class="sxs-lookup"><span data-stu-id="3913a-268">[WebJobs](/azure/app-service/web-sites-create-web-jobs) allow you to create scheduled or continuously running background tasks.</span></span>

- <span data-ttu-id="3913a-269">[逻辑应用](https://azure.microsoft.com/services/logic-apps/)提供通信、集成和业务规则管理服务。</span><span class="sxs-lookup"><span data-stu-id="3913a-269">[Logic Apps](https://azure.microsoft.com/services/logic-apps/) provides communications, integration, and business rule management services.</span></span>

#### <a name="autoscaling-azure-vm-scaling-and-azure-app-service-autoscale"></a><span data-ttu-id="3913a-270">自动缩放、Azure VM 缩放和 Azure 应用服务自动缩放</span><span class="sxs-lookup"><span data-stu-id="3913a-270">Autoscaling, Azure VM scaling, and Azure App Service Autoscale</span></span>

<span data-ttu-id="3913a-271">Azure 中的自动缩放由两个服务进行处理：</span><span class="sxs-lookup"><span data-stu-id="3913a-271">Autoscaling in Azure is handled by two services:</span></span>

- <span data-ttu-id="3913a-272">[VM 规模集](/azure/virtual-machine-scale-sets/overview)可用于部署和管理一组相同的 VM。</span><span class="sxs-lookup"><span data-stu-id="3913a-272">[VM scale sets](/azure/virtual-machine-scale-sets/overview) allow you to deploy and manage an identical set of VMs.</span></span> <span data-ttu-id="3913a-273">可以根据性能需求自动缩放实例数。</span><span class="sxs-lookup"><span data-stu-id="3913a-273">The number of instances can autoscale based on performance needs.</span></span>

- <span data-ttu-id="3913a-274">[应用服务自动缩放](/azure/app-service/web-sites-scale)提供自动缩放 Azure 应用服务解决方案的功能。</span><span class="sxs-lookup"><span data-stu-id="3913a-274">[App Service Autoscale](/azure/app-service/web-sites-scale) provides the capability to autoscale Azure App Service solutions.</span></span>

#### <a name="container-service"></a><span data-ttu-id="3913a-275">容器服务</span><span class="sxs-lookup"><span data-stu-id="3913a-275">Container Service</span></span>

<span data-ttu-id="3913a-276">[Azure Kubernetes 服务](/azure/aks/intro-kubernetes)支持通过 Kubernetes 管理的 Docker 容器。</span><span class="sxs-lookup"><span data-stu-id="3913a-276">The [Azure Kubernetes Service](/azure/aks/intro-kubernetes) supports Docker containers managed through Kubernetes.</span></span>

#### <a name="other-compute-services"></a><span data-ttu-id="3913a-277">其他计算服务</span><span class="sxs-lookup"><span data-stu-id="3913a-277">Other compute services</span></span>

<span data-ttu-id="3913a-278">Azure 提供多个计算服务，在 AWS 中没有直接与此类似的服务：</span><span class="sxs-lookup"><span data-stu-id="3913a-278">Azure offers several compute services that do not have direct equivalents in AWS:</span></span>

- <span data-ttu-id="3913a-279">[Azure Batch](/azure/batch/batch-technical-overview) 可用于跨可缩放的一组虚拟机管理计算密集型工作。</span><span class="sxs-lookup"><span data-stu-id="3913a-279">[Azure Batch](/azure/batch/batch-technical-overview) allows you to manage compute-intensive work across a scalable collection of virtual machines.</span></span>

- <span data-ttu-id="3913a-280">[Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-overview) 是用于开发和托管可缩放[微服务](/azure/service-fabric/service-fabric-overview-microservices)解决方案的平台。</span><span class="sxs-lookup"><span data-stu-id="3913a-280">[Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-overview) is a platform for developing and hosting scalable [microservice](/azure/service-fabric/service-fabric-overview-microservices) solutions.</span></span>

#### <a name="see-also"></a><span data-ttu-id="3913a-281">另请参阅</span><span class="sxs-lookup"><span data-stu-id="3913a-281">See also</span></span>

- [<span data-ttu-id="3913a-282">使用门户在 Azure 上创建 Linux VM</span><span class="sxs-lookup"><span data-stu-id="3913a-282">Create a Linux VM on Azure using the portal</span></span>](/azure/virtual-machines/linux/quick-create-portal)

- [<span data-ttu-id="3913a-283">Azure 参考体系结构：在 Azure 上运行 Linux VM</span><span class="sxs-lookup"><span data-stu-id="3913a-283">Azure Reference Architecture: Running a Linux VM on Azure</span></span>](/azure/architecture/reference-architectures/n-tier/linux-vm)

- [<span data-ttu-id="3913a-284">Azure 应用服务中的 Node.js Web 应用入门</span><span class="sxs-lookup"><span data-stu-id="3913a-284">Get started with Node.js web apps in Azure App Service</span></span>](/azure/app-service/app-service-web-get-started-nodejs)

- [<span data-ttu-id="3913a-285">Azure 参考体系结构：基本 Web 应用程序</span><span class="sxs-lookup"><span data-stu-id="3913a-285">Azure Reference Architecture: Basic web application</span></span>](/azure/architecture/reference-architectures/app-service-web-app/basic-web-app)

- [<span data-ttu-id="3913a-286">创建第一个 Azure 函数</span><span class="sxs-lookup"><span data-stu-id="3913a-286">Create your first Azure Function</span></span>](/azure/azure-functions/functions-create-first-azure-function)

### <a name="storage"></a><span data-ttu-id="3913a-287">存储</span><span class="sxs-lookup"><span data-stu-id="3913a-287">Storage</span></span>

#### <a name="s3ebsefs-and-azure-storage"></a><span data-ttu-id="3913a-288">S3/EBS/EFS 与 Azure 存储</span><span class="sxs-lookup"><span data-stu-id="3913a-288">S3/EBS/EFS and Azure Storage</span></span>

<span data-ttu-id="3913a-289">在 AWS 平台中，云存储主要划分为以下三个服务：</span><span class="sxs-lookup"><span data-stu-id="3913a-289">In the AWS platform, cloud storage is primarily broken down into three services:</span></span>

- <span data-ttu-id="3913a-290">**Simple Storage Service (S3)**。</span><span class="sxs-lookup"><span data-stu-id="3913a-290">**Simple Storage Service (S3)**.</span></span> <span data-ttu-id="3913a-291">该基本对象存储使数据能够通过可在 Internet 上访问的 API 使用。</span><span class="sxs-lookup"><span data-stu-id="3913a-291">Basic object storage that akes data available through an Internet accessible API.</span></span>

- <span data-ttu-id="3913a-292">**Elastic Block Storage (EBS)**。</span><span class="sxs-lookup"><span data-stu-id="3913a-292">**Elastic Block Storage (EBS)**.</span></span> <span data-ttu-id="3913a-293">供单个 VM 访问的块级存储。</span><span class="sxs-lookup"><span data-stu-id="3913a-293">Block level storage intended for access by a single VM.</span></span>

- <span data-ttu-id="3913a-294">**Elastic File System (EFS)**。</span><span class="sxs-lookup"><span data-stu-id="3913a-294">**Elastic File System (EFS)**.</span></span> <span data-ttu-id="3913a-295">作为共享存储供多达数千个 EC2 实例使用的文件存储。</span><span class="sxs-lookup"><span data-stu-id="3913a-295">File storage meant for use as shared storage for up to thousands of EC2 instances.</span></span>

<span data-ttu-id="3913a-296">在 Azure 存储中，使用已绑定到订阅的[存储帐户](/azure/storage/common/storage-quickstart-create-account)可以创建和管理以下存储服务：</span><span class="sxs-lookup"><span data-stu-id="3913a-296">In Azure Storage, subscription-bound [storage accounts](/azure/storage/common/storage-quickstart-create-account) allow you to create and manage the following storage services:</span></span>

- <span data-ttu-id="3913a-297">[Blob 存储](/azure/storage/common/storage-quickstart-create-account)可存储任何类型的文本或二进制数据，例如文档、媒体文件或应用程序安装程序。</span><span class="sxs-lookup"><span data-stu-id="3913a-297">[Blob storage](/azure/storage/common/storage-quickstart-create-account) stores any type of text or binary data, such as a document, media file, or application installer.</span></span> <span data-ttu-id="3913a-298">可以设置 Blob 存储进行私人访问，或者在 Internet 上公开共享内容。</span><span class="sxs-lookup"><span data-stu-id="3913a-298">You can set Blob storage for private access or share contents publicly to the Internet.</span></span> <span data-ttu-id="3913a-299">Blob 存储的用途与 AWS S3 和 EBS 相同。</span><span class="sxs-lookup"><span data-stu-id="3913a-299">Blob storage serves the same purpose as both AWS S3 and EBS.</span></span>
- <span data-ttu-id="3913a-300">[表存储](/azure/cosmos-db/table-storage-how-to-use-nodejs)可存储结构化数据集。</span><span class="sxs-lookup"><span data-stu-id="3913a-300">[Table storage](/azure/cosmos-db/table-storage-how-to-use-nodejs) stores structured datasets.</span></span> <span data-ttu-id="3913a-301">表存储是一个 NoSQL“键-属性”数据存储，用于实现快速开发以及快速访问大量数据。</span><span class="sxs-lookup"><span data-stu-id="3913a-301">Table storage is a NoSQL key-attribute data store that allows for rapid development and fast access to large quantities of data.</span></span> <span data-ttu-id="3913a-302">类似于 AWS 的 SimpleDB 和 DynamoDB 服务。</span><span class="sxs-lookup"><span data-stu-id="3913a-302">Similar to AWS' SimpleDB and DynamoDB services.</span></span>

- <span data-ttu-id="3913a-303">[队列存储](/azure/storage/queues/storage-nodejs-how-to-use-queues)为工作流处理和云服务组件之间的通信提供消息传送。</span><span class="sxs-lookup"><span data-stu-id="3913a-303">[Queue storage](/azure/storage/queues/storage-nodejs-how-to-use-queues) provides messaging for workflow processing and for communication between components of cloud services.</span></span>

- <span data-ttu-id="3913a-304">[文件存储](/azure/storage/files/storage-java-how-to-use-file-storage)使用标准服务器消息块 (SMB) 协议为传统应用程序提供共享存储。</span><span class="sxs-lookup"><span data-stu-id="3913a-304">[File storage](/azure/storage/files/storage-java-how-to-use-file-storage) offers shared storage for legacy applications using the standard server message block (SMB) protocol.</span></span> <span data-ttu-id="3913a-305">文件存储的使用方式与 AWS 平台中的 EFS 类似。</span><span class="sxs-lookup"><span data-stu-id="3913a-305">File storage is used in a similar manner to EFS in the AWS platform.</span></span>

#### <a name="glacier-and-azure-storage"></a><span data-ttu-id="3913a-306">Glacier 与 Azure 存储</span><span class="sxs-lookup"><span data-stu-id="3913a-306">Glacier and Azure Storage</span></span>

<span data-ttu-id="3913a-307">[Azure 存档 Blob 存储](/azure/storage/blobs/storage-blob-storage-tiers#archive-access-tier)相当于 AWS Glacier 存储服务。</span><span class="sxs-lookup"><span data-stu-id="3913a-307">[Azure Archive Blob Storage](/azure/storage/blobs/storage-blob-storage-tiers#archive-access-tier) is comparable to AWS Glacier storage service.</span></span> <span data-ttu-id="3913a-308">它适用于已至少存储 180 天且极少访问的数据，并且可以容忍几个小时的检索延迟。</span><span class="sxs-lookup"><span data-stu-id="3913a-308">It is intended for rarely accessed data that is stored for at least 180 days and can tolerate several hours of retrieval latency.</span></span>

<span data-ttu-id="3913a-309">针对不经常访问，但访问时必须立即提供的数据，[Azure 冷 Blob 存储层](/azure/storage/blobs/storage-blob-storage-tiers#cool-access-tier)提供比标准 Blob 存储更经济节省的存储。</span><span class="sxs-lookup"><span data-stu-id="3913a-309">For data that is infrequently accessed but must be available immediately when accessed, [Azure Cool Blob Storage tier](/azure/storage/blobs/storage-blob-storage-tiers#cool-access-tier) provides cheaper storage than standard blob storage.</span></span> <span data-ttu-id="3913a-310">此存储层相当于 AWS S3 - Infrequent Access 存储服务。</span><span class="sxs-lookup"><span data-stu-id="3913a-310">This storage tier is comparable to AWS S3 - Infrequent Access storage service.</span></span>

#### <a name="see-also"></a><span data-ttu-id="3913a-311">另请参阅</span><span class="sxs-lookup"><span data-stu-id="3913a-311">See also</span></span>

- [<span data-ttu-id="3913a-312">Microsoft Azure 存储性能和可伸缩性清单</span><span class="sxs-lookup"><span data-stu-id="3913a-312">Microsoft Azure Storage Performance and Scalability Checklist</span></span>](/azure/storage/common/storage-performance-checklist)

- [<span data-ttu-id="3913a-313">Azure 存储安全指南</span><span class="sxs-lookup"><span data-stu-id="3913a-313">Azure Storage security guide</span></span>](/azure/storage/common/storage-security-guide)

- [<span data-ttu-id="3913a-314">使用内容分发网络 (CDN) 的最佳做法</span><span class="sxs-lookup"><span data-stu-id="3913a-314">Best practices for using content delivery networks (CDNs)</span></span>](/azure/architecture/best-practices/cdn)

### <a name="networking"></a><span data-ttu-id="3913a-315">网络</span><span class="sxs-lookup"><span data-stu-id="3913a-315">Networking</span></span>

#### <a name="elastic-load-balancing-azure-load-balancer-and-azure-application-gateway"></a><span data-ttu-id="3913a-316">弹性负载均衡、Azure 负载均衡器和 Azure 应用程序网关</span><span class="sxs-lookup"><span data-stu-id="3913a-316">Elastic Load Balancing, Azure Load Balancer, and Azure Application Gateway</span></span>

<span data-ttu-id="3913a-317">Azure 提供两个类似于弹性负载均衡的服务：</span><span class="sxs-lookup"><span data-stu-id="3913a-317">The Azure equivalents of the two Elastic Load Balancing services are:</span></span>

- <span data-ttu-id="3913a-318">[负载均衡器](https://azure.microsoft.com/documentation/articles/load-balancer-overview/) - 提供与 AWS 经典负载均衡器相同的功能，可用于在网络级别分配多个 VM 的流量。</span><span class="sxs-lookup"><span data-stu-id="3913a-318">[Load Balancer](https://azure.microsoft.com/documentation/articles/load-balancer-overview/) - provides the same capabilities as the AWS Classic Load Balancer, allowing you to distribute traffic for multiple VMs at the network level.</span></span> <span data-ttu-id="3913a-319">此外，它还提供故障转移功能。</span><span class="sxs-lookup"><span data-stu-id="3913a-319">It also provides failover capability.</span></span>

- <span data-ttu-id="3913a-320">[应用程序网关](https://azure.microsoft.com/documentation/articles/application-gateway-introduction/) - 提供应用程序级别的基于规则的路由，类似于 AWS 应用程序负载均衡器。</span><span class="sxs-lookup"><span data-stu-id="3913a-320">[Application Gateway](https://azure.microsoft.com/documentation/articles/application-gateway-introduction/) - offers application-level rule-based routing comparable to the AWS Application Load Balancer.</span></span>

#### <a name="route-53-azure-dns-and-azure-traffic-manager"></a><span data-ttu-id="3913a-321">Route 53、Azure DNS 和 Azure 流量管理器</span><span class="sxs-lookup"><span data-stu-id="3913a-321">Route 53, Azure DNS, and Azure Traffic Manager</span></span>

<span data-ttu-id="3913a-322">在 AWS 中，Route 53 提供 DNS 名称管理，以及 DNS 级别的流量路由和故障转移服务。</span><span class="sxs-lookup"><span data-stu-id="3913a-322">In AWS, Route 53 provides both DNS name management and DNS-level traffic routing and failover services.</span></span> <span data-ttu-id="3913a-323">在 Azure 中，这些功能通过两个服务进行处理：</span><span class="sxs-lookup"><span data-stu-id="3913a-323">In Azure this is handled through two services:</span></span>

- <span data-ttu-id="3913a-324">[Azure DNS](https://azure.microsoft.com/documentation/services/dns/) 提供域和 DNS 管理。</span><span class="sxs-lookup"><span data-stu-id="3913a-324">[Azure DNS](https://azure.microsoft.com/documentation/services/dns/) provides domain and DNS management.</span></span>

- <span data-ttu-id="3913a-325">[流量管理器][traffic-manager]提供 DNS 级流量路由、负载均衡和故障转移功能。</span><span class="sxs-lookup"><span data-stu-id="3913a-325">[Traffic   Manager][traffic-manager] provides DNS level traffic routing, load balancing, and failover   capabilities.</span></span>

#### <a name="direct-connect-and-azure-expressroute"></a><span data-ttu-id="3913a-326">直接连接和 Azure ExpressRoute</span><span class="sxs-lookup"><span data-stu-id="3913a-326">Direct Connect and Azure ExpressRoute</span></span>

<span data-ttu-id="3913a-327">Azure 通过其 [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) 服务提供类似的站点到站点专用连接。</span><span class="sxs-lookup"><span data-stu-id="3913a-327">Azure provides similar site-to-site dedicated connections through its [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) service.</span></span> <span data-ttu-id="3913a-328">通过 ExpressRoute 可以使用专用网络连接将本地网络直接连接到 Azure 资源。</span><span class="sxs-lookup"><span data-stu-id="3913a-328">ExpressRoute allows you to connect your local network directly to Azure resources using a dedicated private network connection.</span></span> <span data-ttu-id="3913a-329">Azure 还以更低的价格提供更方便的[站点到站点 VPN 连接](https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-site-to-site-resource-manager-portal/)。</span><span class="sxs-lookup"><span data-stu-id="3913a-329">Azure also offers more conventional [site-to-site VPN connections](https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-site-to-site-resource-manager-portal/) at a lower cost.</span></span>

#### <a name="see-also"></a><span data-ttu-id="3913a-330">另请参阅</span><span class="sxs-lookup"><span data-stu-id="3913a-330">See also</span></span>

- [<span data-ttu-id="3913a-331">使用 Azure 门户创建虚拟网络</span><span class="sxs-lookup"><span data-stu-id="3913a-331">Create a virtual network using the Azure   portal</span></span>](https://azure.microsoft.com/documentation/articles/virtual-networks-create-vnet-arm-pportal/)

- [<span data-ttu-id="3913a-332">规划和设计 Azure 虚拟网络</span><span class="sxs-lookup"><span data-stu-id="3913a-332">Plan and design Azure Virtual   Networks</span></span>](https://azure.microsoft.com/documentation/articles/virtual-network-vnet-plan-design-arm/)

- [<span data-ttu-id="3913a-333">Azure 网络安全最佳做法</span><span class="sxs-lookup"><span data-stu-id="3913a-333">Azure Network Security Best   Practices</span></span>](https://azure.microsoft.com/documentation/articles/azure-security-network-security-best-practices/)

### <a name="database-services"></a><span data-ttu-id="3913a-334">数据库服务</span><span class="sxs-lookup"><span data-stu-id="3913a-334">Database services</span></span>

#### <a name="rds-and-azure-relational-database-services"></a><span data-ttu-id="3913a-335">RDS 和 Azure 关系数据库服务</span><span class="sxs-lookup"><span data-stu-id="3913a-335">RDS and Azure relational database services</span></span>

<span data-ttu-id="3913a-336">Azure 提供多个与 AWS 的关系数据库服务 (RDS) 功能相同的不同关系数据库服务。</span><span class="sxs-lookup"><span data-stu-id="3913a-336">Azure provides several different relational database services that are the equivalent of AWS' Relational Database Service (RDS).</span></span>

- [<span data-ttu-id="3913a-337">SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="3913a-337">SQL Database</span></span>](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)
- [<span data-ttu-id="3913a-338">Azure Database for MySQL</span><span class="sxs-lookup"><span data-stu-id="3913a-338">Azure Database for MySQL</span></span>](https://docs.microsoft.com/azure/mysql/overview)
- [<span data-ttu-id="3913a-339">Azure Database for PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="3913a-339">Azure Database for PostgreSQL</span></span>](https://docs.microsoft.com/azure/postgresql/overview)

<span data-ttu-id="3913a-340">可以使用 Azure VM 实例部署其他数据库引擎，例如 [SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)、[Oracle](https://azure.microsoft.com/campaigns/oracle/) 和 [MySQL](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-classic-mysql-2008r2/)。</span><span class="sxs-lookup"><span data-stu-id="3913a-340">Other database engines such as [SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/), [Oracle](https://azure.microsoft.com/campaigns/oracle/), and [MySQL](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-classic-mysql-2008r2/) can be deployed using Azure VM Instances.</span></span>

<span data-ttu-id="3913a-341">AWS RDS 的费用根据实例使用的硬件资源确定，例如 CPU、RAM、存储和网络带宽。</span><span class="sxs-lookup"><span data-stu-id="3913a-341">Costs for AWS RDS are determined by the amount of hardware resources that your instance uses, like CPU, RAM, storage, and network bandwidth.</span></span> <span data-ttu-id="3913a-342">在 Azure 数据库服务中，费用取决于数据库大小、并发连接数和吞吐量级别。</span><span class="sxs-lookup"><span data-stu-id="3913a-342">In the Azure database services, cost depends on your database size, concurrent connections, and throughput levels.</span></span>

#### <a name="see-also"></a><span data-ttu-id="3913a-343">另请参阅</span><span class="sxs-lookup"><span data-stu-id="3913a-343">See also</span></span>

- [<span data-ttu-id="3913a-344">Azure SQL 数据库教程</span><span class="sxs-lookup"><span data-stu-id="3913a-344">Azure SQL Database Tutorials</span></span>](https://azure.microsoft.com/documentation/articles/sql-database-explore-tutorials/)

- [<span data-ttu-id="3913a-345">使用 Azure 门户为 Azure SQL 数据库配置异地复制</span><span class="sxs-lookup"><span data-stu-id="3913a-345">Configure geo-replication for Azure SQL Database with the Azure portal</span></span>](https://azure.microsoft.com/documentation/articles/sql-database-geo-replication-portal/)

- [<span data-ttu-id="3913a-346">Cosmos DB 简介：一种 NoSQL JSON 数据库</span><span class="sxs-lookup"><span data-stu-id="3913a-346">Introduction to Cosmos DB: A NoSQL JSON Database</span></span>](/azure/cosmos-db/sql-api-introduction)

- [<span data-ttu-id="3913a-347">如何通过 Node.js 使用 Azure 表存储</span><span class="sxs-lookup"><span data-stu-id="3913a-347">How to use Azure Table storage from Node.js</span></span>](https://azure.microsoft.com/documentation/articles/storage-nodejs-how-to-use-table-storage/)

### <a name="security-and-identity"></a><span data-ttu-id="3913a-348">安全和标识</span><span class="sxs-lookup"><span data-stu-id="3913a-348">Security and identity</span></span>

#### <a name="directory-service-and-azure-active-directory"></a><span data-ttu-id="3913a-349">目录服务和 Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="3913a-349">Directory service and Azure Active Directory</span></span>

<span data-ttu-id="3913a-350">Azure 将目录服务划分为以下服务：</span><span class="sxs-lookup"><span data-stu-id="3913a-350">Azure splits up directory services into the following offerings:</span></span>

- <span data-ttu-id="3913a-351">[Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-whatis/) - 基于云的目录和标识管理服务。</span><span class="sxs-lookup"><span data-stu-id="3913a-351">[Azure Active   Directory](https://azure.microsoft.com/documentation/articles/active-directory-whatis/) - cloud based directory and identity management service.</span></span>

- <span data-ttu-id="3913a-352">[Azure Active Directory B2B](https://azure.microsoft.com/documentation/articles/active-directory-b2b-collaboration-overview/) - 用于从合作伙伴管理的标识访问你的企业应用程序。</span><span class="sxs-lookup"><span data-stu-id="3913a-352">[Azure Active Directory   B2B](https://azure.microsoft.com/documentation/articles/active-directory-b2b-collaboration-overview/) - enables access to your corporate applications from partner-managed   identities.</span></span>

- <span data-ttu-id="3913a-353">[Azure Active Directory B2C](https://azure.microsoft.com/documentation/articles/active-directory-b2c-overview/) - 该服务针对面向消费者的应用程序提供单一登录和用户管理支持。</span><span class="sxs-lookup"><span data-stu-id="3913a-353">[Azure Active Directory   B2C](https://azure.microsoft.com/documentation/articles/active-directory-b2c-overview/) - service offering support for single sign-on and user management for   consumer facing applications.</span></span>

- <span data-ttu-id="3913a-354">[Azure Active Directory 域服务](https://azure.microsoft.com/documentation/articles/active-directory-ds-overview/) - 托管的域控制器服务，可实现与 Active Directory 兼容的域加入和用户管理功能。</span><span class="sxs-lookup"><span data-stu-id="3913a-354">[Azure Active Directory Domain   Services](https://azure.microsoft.com/documentation/articles/active-directory-ds-overview/) - hosted domain controller service, allowing Active Directory compatible   domain join and user management functionality.</span></span>

#### <a name="web-application-firewall"></a><span data-ttu-id="3913a-355">Web 应用程序防火墙</span><span class="sxs-lookup"><span data-stu-id="3913a-355">Web application firewall</span></span>

<span data-ttu-id="3913a-356">除了[应用程序网关 Web 应用程序防火墙](/azure/application-gateway/waf-overview)以外，还可以使用来自 [Barracuda Networks](https://azure.microsoft.com/marketplace/partners/barracudanetworks/waf/) 等第三方供应商的 Web 应用程序防火墙。</span><span class="sxs-lookup"><span data-stu-id="3913a-356">In addition to the [Application Gateway Web Application Firewall](/azure/application-gateway/waf-overview), you can also use web application firewalls from third-party vendors like [Barracuda Networks](https://azure.microsoft.com/marketplace/partners/barracudanetworks/waf/).</span></span>

#### <a name="see-also"></a><span data-ttu-id="3913a-357">另请参阅</span><span class="sxs-lookup"><span data-stu-id="3913a-357">See also</span></span>

- [<span data-ttu-id="3913a-358">Microsoft Azure 安全入门</span><span class="sxs-lookup"><span data-stu-id="3913a-358">Getting started with Microsoft Azure security</span></span>](/azure/security)

- [<span data-ttu-id="3913a-359">Azure 标识管理和访问控制安全最佳实践</span><span class="sxs-lookup"><span data-stu-id="3913a-359">Azure Identity Management and access control security best practices</span></span>](/azure/security/azure-security-identity-management-best-practices)

### <a name="application-and-messaging-services"></a><span data-ttu-id="3913a-360">应用程序和消息传送服务</span><span class="sxs-lookup"><span data-stu-id="3913a-360">Application and messaging services</span></span>

#### <a name="simple-email-service"></a><span data-ttu-id="3913a-361">简单电子邮件服务</span><span class="sxs-lookup"><span data-stu-id="3913a-361">Simple Email Service</span></span>

<span data-ttu-id="3913a-362">AWS 提供简单电子邮件服务 (SES) 用于发送通知、交易或市场营销电子邮件。</span><span class="sxs-lookup"><span data-stu-id="3913a-362">AWS provides the Simple Email Service (SES) for sending notification, transactional, or marketing emails.</span></span> <span data-ttu-id="3913a-363">在 Azure 中，[SendGrid](https://sendgrid.com/partners/azure/) 等第三方解决方案可提供电子邮件服务。</span><span class="sxs-lookup"><span data-stu-id="3913a-363">In Azure, third-party solutions like [SendGrid](https://sendgrid.com/partners/azure/) provide email services.</span></span>

#### <a name="simple-queueing-service"></a><span data-ttu-id="3913a-364">简单队列服务</span><span class="sxs-lookup"><span data-stu-id="3913a-364">Simple Queueing Service</span></span>

<span data-ttu-id="3913a-365">AWS 简单队列服务 (SQS) 提供一个消息传送系统用于连接 AWS 平台中的应用程序、服务和设备。</span><span class="sxs-lookup"><span data-stu-id="3913a-365">AWS Simple Queueing Service (SQS) provides a messaging system for connecting applications, services, and devices within the AWS platform.</span></span> <span data-ttu-id="3913a-366">在 Azure 中，有两个服务提供类似的功能：</span><span class="sxs-lookup"><span data-stu-id="3913a-366">Azure has two services that provide similar functionality:</span></span>

- <span data-ttu-id="3913a-367">[队列存储](/azure/storage/queues/storage-nodejs-how-to-use-queues)：一个云消息传送服务，可在 Azure 平台中的应用程序组件之间实现通信。</span><span class="sxs-lookup"><span data-stu-id="3913a-367">[Queue storage](/azure/storage/queues/storage-nodejs-how-to-use-queues): a cloud messaging service that allows communication between application components within the Azure platform.</span></span>

- <span data-ttu-id="3913a-368">[服务总线](https://azure.microsoft.com/services/service-bus/) - 一个更可靠的消息传送系统，用于连接应用程序、服务和设备。</span><span class="sxs-lookup"><span data-stu-id="3913a-368">[Service Bus](https://azure.microsoft.com/services/service-bus/): a more robust messaging system for connecting applications, services, and devices.</span></span> <span data-ttu-id="3913a-369">使用相关的[服务总线中继](/azure/service-bus-relay/relay-what-is-it)，服务总线还可以连接到远程托管的应用程序和服务。</span><span class="sxs-lookup"><span data-stu-id="3913a-369">Using the related [Service Bus relay](/azure/service-bus-relay/relay-what-is-it), Service Bus can also connect to remotely hosted applications and services.</span></span>

#### <a name="device-farm"></a><span data-ttu-id="3913a-370">设备场</span><span class="sxs-lookup"><span data-stu-id="3913a-370">Device Farm</span></span>

<span data-ttu-id="3913a-371">AWS 设备场提供跨设备测试服务。</span><span class="sxs-lookup"><span data-stu-id="3913a-371">The AWS Device Farm provides cross-device testing services.</span></span> <span data-ttu-id="3913a-372">在 Azure 中，[Xamarin Test Cloud](https://www.xamarin.com/test-cloud) 针对移动设备提供类似的跨设备前端测试。</span><span class="sxs-lookup"><span data-stu-id="3913a-372">In Azure, [Xamarin Test Cloud](https://www.xamarin.com/test-cloud) provides similar cross-device front-end testing for mobile devices.</span></span>

<span data-ttu-id="3913a-373">除了前端测试以外，[Azure 开发测试实验室](https://azure.microsoft.com/services/devtest-lab/)还为 Linux 和 Windows 环境提供后端测试资源。</span><span class="sxs-lookup"><span data-stu-id="3913a-373">In addition to front-end testing, the [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/) provides back end testing resources for Linux and Windows environments.</span></span>

#### <a name="see-also"></a><span data-ttu-id="3913a-374">另请参阅</span><span class="sxs-lookup"><span data-stu-id="3913a-374">See also</span></span>

- [<span data-ttu-id="3913a-375">如何通过 Node.js 使用队列存储</span><span class="sxs-lookup"><span data-stu-id="3913a-375">How to use Queue storage from Node.js</span></span>](/azure/storage/queues/storage-nodejs-how-to-use-queues)

- [<span data-ttu-id="3913a-376">如何使用 Service Bus 队列</span><span class="sxs-lookup"><span data-stu-id="3913a-376">How to use Service Bus queues</span></span>](/azure/service-bus-messaging/service-bus-nodejs-how-to-use-queues)

### <a name="analytics-and-big-data"></a><span data-ttu-id="3913a-377">分析和大数据</span><span class="sxs-lookup"><span data-stu-id="3913a-377">Analytics and big data</span></span>

<span data-ttu-id="3913a-378">[Cortana Intelligence Suite](https://azure.microsoft.com/suites/cortana-intelligence-suite/) 是 Azure 提供的产品和服务包，旨在捕获、组织、分析和可视化大量数据。</span><span class="sxs-lookup"><span data-stu-id="3913a-378">[The Cortana Intelligence Suite](https://azure.microsoft.com/suites/cortana-intelligence-suite/) is Azure's package of products and services designed to capture, organize, analyze, and visualize large amounts of data.</span></span> <span data-ttu-id="3913a-379">Cortana 套件包括以下服务：</span><span class="sxs-lookup"><span data-stu-id="3913a-379">The Cortana suite consists of the following services:</span></span>

- <span data-ttu-id="3913a-380">[HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/) - 包含 Hadoop、Spark、Storm 或 HBase 的托管 Apache 分发版。</span><span class="sxs-lookup"><span data-stu-id="3913a-380">[HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/) - managed Apache distribution that includes Hadoop, Spark, Storm, or HBase.</span></span>

- <span data-ttu-id="3913a-381">[数据工厂](https://azure.microsoft.com/documentation/services/data-factory/) - 提供数据业务流程和数据管道功能。</span><span class="sxs-lookup"><span data-stu-id="3913a-381">[Data   Factory](https://azure.microsoft.com/documentation/services/data-factory/) - provides data orchestration and data pipeline functionality.</span></span>

- <span data-ttu-id="3913a-382">[SQL 数据仓库](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) - 大规模关系型数据存储。</span><span class="sxs-lookup"><span data-stu-id="3913a-382">[SQL Data   Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) - large-scale relational data storage.</span></span>

- <span data-ttu-id="3913a-383">[Data Lake Store](https://azure.microsoft.com/documentation/services/data-lake-store/) - 针对大数据分析工作负荷优化的大规模存储。</span><span class="sxs-lookup"><span data-stu-id="3913a-383">[Data Lake   Store](https://azure.microsoft.com/documentation/services/data-lake-store/) - large-scale storage optimized for big data analytics workloads.</span></span>

- <span data-ttu-id="3913a-384">[机器学习](https://azure.microsoft.com/documentation/services/machine-learning/) - 用于基于数据生成和应用预测分析。</span><span class="sxs-lookup"><span data-stu-id="3913a-384">[Machine   Learning](https://azure.microsoft.com/documentation/services/machine-learning/) - used to build and apply predictive analytics on data.</span></span>

- <span data-ttu-id="3913a-385">[流分析](https://azure.microsoft.com/documentation/services/stream-analytics/) - 实时数据分析。</span><span class="sxs-lookup"><span data-stu-id="3913a-385">[Stream   Analytics](https://azure.microsoft.com/documentation/services/stream-analytics/) - real-time data analysis.</span></span>

- <span data-ttu-id="3913a-386">[Data Lake Analytics](https://azure.microsoft.com/documentation/articles/data-lake-analytics-overview/) - 经过优化的、可与 Data Lake Store 配合使用的大规模分析服务</span><span class="sxs-lookup"><span data-stu-id="3913a-386">[Data Lake   Analytics](https://azure.microsoft.com/documentation/articles/data-lake-analytics-overview/) - large-scale analytics service optimized to work with Data Lake Store</span></span>

- <span data-ttu-id="3913a-387">[PowerBI](https://powerbi.microsoft.com/) - 用于支持数据可视化。</span><span class="sxs-lookup"><span data-stu-id="3913a-387">[PowerBI](https://powerbi.microsoft.com/) - used to power data   visualization.</span></span>

#### <a name="see-also"></a><span data-ttu-id="3913a-388">另请参阅</span><span class="sxs-lookup"><span data-stu-id="3913a-388">See also</span></span>

- [<span data-ttu-id="3913a-389">Cortana Intelligence 库</span><span class="sxs-lookup"><span data-stu-id="3913a-389">Cortana Intelligence Gallery</span></span>](https://gallery.cortanaintelligence.com/)

- [<span data-ttu-id="3913a-390">了解 Microsoft 大数据解决方案</span><span class="sxs-lookup"><span data-stu-id="3913a-390">Understanding Microsoft big data   solutions</span></span>](https://msdn.microsoft.com/library/dn749804.aspx)

- [<span data-ttu-id="3913a-391">Azure Data Lake 和 Azure HDInsight 博客</span><span class="sxs-lookup"><span data-stu-id="3913a-391">Azure Data Lake & Azure HDInsight   Blog</span></span>](https://blogs.msdn.microsoft.com/azuredatalake/)

### <a name="internet-of-things"></a><span data-ttu-id="3913a-392">物联网</span><span class="sxs-lookup"><span data-stu-id="3913a-392">Internet of Things</span></span>

#### <a name="see-also"></a><span data-ttu-id="3913a-393">另请参阅</span><span class="sxs-lookup"><span data-stu-id="3913a-393">See also</span></span>

- [<span data-ttu-id="3913a-394">Azure IoT 中心入门</span><span class="sxs-lookup"><span data-stu-id="3913a-394">Get started with Azure IoT   Hub</span></span>](https://azure.microsoft.com/documentation/articles/iot-hub-csharp-csharp-getstarted/)

- [<span data-ttu-id="3913a-395">IoT 中心与事件中心的比较</span><span class="sxs-lookup"><span data-stu-id="3913a-395">Comparison of IoT Hub and Event   Hubs</span></span>](https://azure.microsoft.com/documentation/articles/iot-hub-compare-event-hubs/)

### <a name="mobile-services"></a><span data-ttu-id="3913a-396">移动服务</span><span class="sxs-lookup"><span data-stu-id="3913a-396">Mobile services</span></span>

#### <a name="notifications"></a><span data-ttu-id="3913a-397">通知</span><span class="sxs-lookup"><span data-stu-id="3913a-397">Notifications</span></span>

<span data-ttu-id="3913a-398">通知中心不支持发送短信或电子邮件，因此，对于这些传送类型，需要使用第三方服务。</span><span class="sxs-lookup"><span data-stu-id="3913a-398">Notification Hubs do not support sending SMS or email messages, so third-party services are needed for those delivery types.</span></span>

#### <a name="see-also"></a><span data-ttu-id="3913a-399">另请参阅</span><span class="sxs-lookup"><span data-stu-id="3913a-399">See also</span></span>

- [<span data-ttu-id="3913a-400">创建 Android 应用</span><span class="sxs-lookup"><span data-stu-id="3913a-400">Create an Android   app</span></span>](https://azure.microsoft.com/documentation/articles/app-service-mobile-android-get-started/)

- [<span data-ttu-id="3913a-401">Azure 移动应用中的身份验证和授权</span><span class="sxs-lookup"><span data-stu-id="3913a-401">Authentication and Authorization in Azure Mobile   Apps</span></span>](https://azure.microsoft.com/documentation/articles/app-service-mobile-auth/)

- [<span data-ttu-id="3913a-402">使用 Azure 通知中心发送推送通知</span><span class="sxs-lookup"><span data-stu-id="3913a-402">Sending push notifications with Azure Notification   Hubs</span></span>](https://azure.microsoft.com/documentation/articles/notification-hubs-android-push-notification-google-fcm-get-started/)

### <a name="management-and-monitoring"></a><span data-ttu-id="3913a-403">监视和管理</span><span class="sxs-lookup"><span data-stu-id="3913a-403">Management and monitoring</span></span>

#### <a name="see-also"></a><span data-ttu-id="3913a-404">另请参阅</span><span class="sxs-lookup"><span data-stu-id="3913a-404">See also</span></span>

- [<span data-ttu-id="3913a-405">监视和诊断指南</span><span class="sxs-lookup"><span data-stu-id="3913a-405">Monitoring and diagnostics   guidance</span></span>](https://azure.microsoft.com/documentation/articles/best-practices-monitoring/)

- [<span data-ttu-id="3913a-406">有关创建 Azure 资源管理器模板的最佳做法</span><span class="sxs-lookup"><span data-stu-id="3913a-406">Best practices for creating Azure Resource Manager   templates</span></span>](https://azure.microsoft.com/documentation/articles/resource-manager-template-best-practices/)

- [<span data-ttu-id="3913a-407">Azure 资源管理器快速入门模板</span><span class="sxs-lookup"><span data-stu-id="3913a-407">Azure Resource Manager Quickstart   templates</span></span>](https://azure.microsoft.com/documentation/templates/)

## <a name="next-steps"></a><span data-ttu-id="3913a-408">后续步骤</span><span class="sxs-lookup"><span data-stu-id="3913a-408">Next steps</span></span>

- [<span data-ttu-id="3913a-409">Azure 入门</span><span class="sxs-lookup"><span data-stu-id="3913a-409">Get started with Azure</span></span>](https://azure.microsoft.com/get-started/)

- [<span data-ttu-id="3913a-410">Azure 解决方案体系结构</span><span class="sxs-lookup"><span data-stu-id="3913a-410">Azure solution   architectures</span></span>](https://azure.microsoft.com/solutions/architecture/)

- [<span data-ttu-id="3913a-411">Azure 参考体系结构</span><span class="sxs-lookup"><span data-stu-id="3913a-411">Azure Reference   Architectures</span></span>](https://azure.microsoft.com/documentation/articles/guidance-architecture/)

<!-- links -->

[paired-regions]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[traffic-manager]: /azure/traffic-manager/

<!-- markdownlint-enable MD024 -->
