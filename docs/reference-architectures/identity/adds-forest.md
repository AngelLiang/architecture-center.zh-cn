---
title: 在 Azure 中创建 AD DS 资源林
titleSuffix: Azure Reference Architectures
description: >-
  如何在 Azure 中创建受信任的 Active Directory 域。

  指南,vpn 网关,ExpressRoute,负载均衡器,虚拟网络,active-directory
author: telmosampaio
ms.date: 05/02/2018
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seodec18, identity
ms.openlocfilehash: 5fe966f657782b41ec1926d0fd4bb83eb7a3c0fb
ms.sourcegitcommit: 548374a0133f3caed3934fda6a380c76e6eaecea
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2019
ms.locfileid: "58420033"
---
# <a name="create-an-active-directory-domain-services-ad-ds-resource-forest-in-azure"></a><span data-ttu-id="13be9-104">在 Azure 中创建 Active Directory 域服务 (AD DS) 资源林</span><span class="sxs-lookup"><span data-stu-id="13be9-104">Create an Active Directory Domain Services (AD DS) resource forest in Azure</span></span>

<span data-ttu-id="13be9-105">此参考体系结构展示了如何在 Azure 中创建本地 AD 林中的域信任的一个单独 Active Directory 域。</span><span class="sxs-lookup"><span data-stu-id="13be9-105">This reference architecture shows how to create a separate Active Directory domain in Azure that is trusted by domains in your on-premises AD forest.</span></span> <span data-ttu-id="13be9-106">[**部署此解决方案**](#deploy-the-solution)。</span><span class="sxs-lookup"><span data-stu-id="13be9-106">[**Deploy this solution**](#deploy-the-solution).</span></span>

![使用独立的 Active Directory 域保护混合网络体系结构的安全](./images/adds-forest.png)

<span data-ttu-id="13be9-108">下载此体系结构的 [Visio 文件][visio-download]。</span><span class="sxs-lookup"><span data-stu-id="13be9-108">*Download a [Visio file][visio-download] of this architecture.*</span></span>

<span data-ttu-id="13be9-109">Active Directory 域服务 (AD DS) 以分层结构存储标识信息。</span><span class="sxs-lookup"><span data-stu-id="13be9-109">Active Directory Domain Services (AD DS) stores identity information in a hierarchical structure.</span></span> <span data-ttu-id="13be9-110">分层结构中的顶层节点称为林。</span><span class="sxs-lookup"><span data-stu-id="13be9-110">The top node in the hierarchical structure is known as a forest.</span></span> <span data-ttu-id="13be9-111">林包含域，域包含其他类型的对象。</span><span class="sxs-lookup"><span data-stu-id="13be9-111">A forest contains domains, and domains contain other types of objects.</span></span> <span data-ttu-id="13be9-112">此参考体系结构在 Azure 中创建与本地域之间具有单向传出信任关系的 AD DS 林。</span><span class="sxs-lookup"><span data-stu-id="13be9-112">This reference architecture creates an AD DS forest in Azure with a one-way outgoing trust relationship with an on-premises domain.</span></span> <span data-ttu-id="13be9-113">Azure 中的林包含本地不存在的一个域。</span><span class="sxs-lookup"><span data-stu-id="13be9-113">The forest in Azure contains a domain that does not exist on-premises.</span></span> <span data-ttu-id="13be9-114">由于信任关系，将信任针对本地域的登录，允许其访问该单独 Azure 域中的资源。</span><span class="sxs-lookup"><span data-stu-id="13be9-114">Because of the trust relationship, logons made against on-premises domains can be trusted for access to resources in the separate Azure domain.</span></span>

<span data-ttu-id="13be9-115">此体系结构的典型用途包括为云中拥有的对象和标识维护安全隔离，以及将各个域从本地迁移到云。</span><span class="sxs-lookup"><span data-stu-id="13be9-115">Typical uses for this architecture include maintaining security separation for objects and identities held in the cloud, and migrating individual domains from on-premises to the cloud.</span></span>

<span data-ttu-id="13be9-116">有关其他注意事项，请参阅[选择用于将本地 Active Directory 与 Azure 相集成的解决方案][considerations]。</span><span class="sxs-lookup"><span data-stu-id="13be9-116">For additional considerations, see [Choose a solution for integrating on-premises Active Directory with Azure][considerations].</span></span>

## <a name="architecture"></a><span data-ttu-id="13be9-117">体系结构</span><span class="sxs-lookup"><span data-stu-id="13be9-117">Architecture</span></span>

<span data-ttu-id="13be9-118">该体系结构具有以下组件。</span><span class="sxs-lookup"><span data-stu-id="13be9-118">The architecture has the following components.</span></span>

- <span data-ttu-id="13be9-119">**本地网络**。</span><span class="sxs-lookup"><span data-stu-id="13be9-119">**On-premises network**.</span></span> <span data-ttu-id="13be9-120">本地网络包含其自己的 Active Directory 林和域。</span><span class="sxs-lookup"><span data-stu-id="13be9-120">The on-premises network contains its own Active Directory forest and domains.</span></span>
- <span data-ttu-id="13be9-121">**Active Directory 服务器**。</span><span class="sxs-lookup"><span data-stu-id="13be9-121">**Active Directory servers**.</span></span> <span data-ttu-id="13be9-122">它们是域控制器，用于实现在云中作为 VM 运行的域服务。</span><span class="sxs-lookup"><span data-stu-id="13be9-122">These are domain controllers implementing domain services running as VMs in the cloud.</span></span> <span data-ttu-id="13be9-123">这些服务器托管的林中包含独立于本地域的一个或多个域。</span><span class="sxs-lookup"><span data-stu-id="13be9-123">These servers host a forest containing one or more domains, separate from those located on-premises.</span></span>
- <span data-ttu-id="13be9-124">**单向信任关系**。</span><span class="sxs-lookup"><span data-stu-id="13be9-124">**One-way trust relationship**.</span></span> <span data-ttu-id="13be9-125">关系图中的示例显示了从 Azure 中的域到本地域的单向信任。</span><span class="sxs-lookup"><span data-stu-id="13be9-125">The example in the diagram shows a one-way trust from the domain in Azure to the on-premises domain.</span></span> <span data-ttu-id="13be9-126">此关系允许本地用户访问 Azure 中的域的资源，但无法反向访问。</span><span class="sxs-lookup"><span data-stu-id="13be9-126">This relationship enables on-premises users to access resources in the domain in Azure, but not the other way around.</span></span> <span data-ttu-id="13be9-127">如果云用户也需要访问本地资源，则可以创建双向信任。</span><span class="sxs-lookup"><span data-stu-id="13be9-127">It is possible to create a two-way trust if cloud users also require access to on-premises resources.</span></span>
- <span data-ttu-id="13be9-128">**Active Directory 子网**。</span><span class="sxs-lookup"><span data-stu-id="13be9-128">**Active Directory subnet**.</span></span> <span data-ttu-id="13be9-129">AD DS 服务器托管在一个单独的子网中。</span><span class="sxs-lookup"><span data-stu-id="13be9-129">The AD DS servers are hosted in a separate subnet.</span></span> <span data-ttu-id="13be9-130">网络安全组 (NSG) 规则对 AD DS 服务器进行保护，并提供防火墙以阻止来自意外来源的流量。</span><span class="sxs-lookup"><span data-stu-id="13be9-130">Network security group (NSG) rules protect the AD DS servers and provide a firewall against traffic from unexpected sources.</span></span>
- <span data-ttu-id="13be9-131">**Azure 网关**。</span><span class="sxs-lookup"><span data-stu-id="13be9-131">**Azure gateway**.</span></span> <span data-ttu-id="13be9-132">Azure 网关在本地网络与 Azure VNet 之间提供连接。</span><span class="sxs-lookup"><span data-stu-id="13be9-132">The Azure gateway provides a connection between the on-premises network and the Azure VNet.</span></span> <span data-ttu-id="13be9-133">这可以是 [VPN 连接][azure-vpn-gateway]，也可以是 [Azure ExpressRoute][azure-expressroute]。</span><span class="sxs-lookup"><span data-stu-id="13be9-133">This can be a [VPN connection][azure-vpn-gateway] or [Azure ExpressRoute][azure-expressroute].</span></span> <span data-ttu-id="13be9-134">有关详细信息，请参阅[在 Azure 中实现安全的混合网络体系结构][implementing-a-secure-hybrid-network-architecture]。</span><span class="sxs-lookup"><span data-stu-id="13be9-134">For more information, see [Implementing a secure hybrid network architecture in Azure][implementing-a-secure-hybrid-network-architecture].</span></span>

## <a name="recommendations"></a><span data-ttu-id="13be9-135">建议</span><span class="sxs-lookup"><span data-stu-id="13be9-135">Recommendations</span></span>

<span data-ttu-id="13be9-136">在 Azure 中实现 Active Directory 的具体建议，请参阅[扩展 Active Directory 域服务 (AD DS) 到 Azure][adds-extend-domain]。</span><span class="sxs-lookup"><span data-stu-id="13be9-136">For specific recommendations on implementing Active Directory in Azure, see [Extending Active Directory Domain Services (AD DS) to Azure][adds-extend-domain].</span></span>

### <a name="trust"></a><span data-ttu-id="13be9-137">信任</span><span class="sxs-lookup"><span data-stu-id="13be9-137">Trust</span></span>

<span data-ttu-id="13be9-138">本地域包含在与云中的域不同的林中。</span><span class="sxs-lookup"><span data-stu-id="13be9-138">The on-premises domains are contained within a different forest from the domains in the cloud.</span></span> <span data-ttu-id="13be9-139">若要在云中启用对本地用户的身份验证，Azure 中的域必须信任本地林中的登录域。</span><span class="sxs-lookup"><span data-stu-id="13be9-139">To enable authentication of on-premises users in the cloud, the domains in Azure must trust the logon domain in the on-premises forest.</span></span> <span data-ttu-id="13be9-140">同样，如果云为外部用户提供了登录域，则本地林可能需要信任云域。</span><span class="sxs-lookup"><span data-stu-id="13be9-140">Similarly, if the cloud provides a logon domain for external users, it may be necessary for the on-premises forest to trust the cloud domain.</span></span>

<span data-ttu-id="13be9-141">可以通过[创建林信任][creating-forest-trusts]在林级别建立信任，或者通过[创建外部信任][creating-external-trusts]在域级别建立信任。</span><span class="sxs-lookup"><span data-stu-id="13be9-141">You can establish trusts at the forest level by [creating forest trusts][creating-forest-trusts], or at the domain level by [creating external trusts][creating-external-trusts].</span></span> <span data-ttu-id="13be9-142">林级别信任在两个林中的所有域之间创建关系。</span><span class="sxs-lookup"><span data-stu-id="13be9-142">A forest level trust creates a relationship between all domains in two forests.</span></span> <span data-ttu-id="13be9-143">外部域级别信任仅在两个指定的域之间创建关系。</span><span class="sxs-lookup"><span data-stu-id="13be9-143">An external domain level trust only creates a relationship between two specified domains.</span></span> <span data-ttu-id="13be9-144">只应当在不同林中的域之间创建外部域级别信任。</span><span class="sxs-lookup"><span data-stu-id="13be9-144">You should only create external domain level trusts between domains in different forests.</span></span>

<span data-ttu-id="13be9-145">信任可以是单向的，也可以是双向的：</span><span class="sxs-lookup"><span data-stu-id="13be9-145">Trusts can be unidirectional (one-way) or bidirectional (two-way):</span></span>

- <span data-ttu-id="13be9-146">单向信任允许一个域或林（称为*传入*域或林）中的用户访问另一个域或林（*传出*域或林）中拥有的资源。</span><span class="sxs-lookup"><span data-stu-id="13be9-146">A one-way trust enables users in one domain or forest (known as the *incoming* domain or forest) to access the resources held in another (the *outgoing* domain or forest).</span></span>
- <span data-ttu-id="13be9-147">双向信任允许任一域或林中的用户访问另一个域或林中拥有的资源。</span><span class="sxs-lookup"><span data-stu-id="13be9-147">A two-way trust enables users in either domain or forest to access resources held in the other.</span></span>

<span data-ttu-id="13be9-148">下表总结了一些简单方案的信任配置：</span><span class="sxs-lookup"><span data-stu-id="13be9-148">The following table summarizes trust configurations for some simple scenarios:</span></span>

| <span data-ttu-id="13be9-149">场景</span><span class="sxs-lookup"><span data-stu-id="13be9-149">Scenario</span></span> | <span data-ttu-id="13be9-150">本地信任</span><span class="sxs-lookup"><span data-stu-id="13be9-150">On-premises trust</span></span> | <span data-ttu-id="13be9-151">云信任</span><span class="sxs-lookup"><span data-stu-id="13be9-151">Cloud trust</span></span> |
| --- | --- | --- |
| <span data-ttu-id="13be9-152">本地用户需要访问云中的资源，但云中的用户不需要访问本地资源</span><span class="sxs-lookup"><span data-stu-id="13be9-152">On-premises users require access to resources in the cloud, but not vice versa</span></span> |<span data-ttu-id="13be9-153">单向、传入</span><span class="sxs-lookup"><span data-stu-id="13be9-153">One-way, incoming</span></span> |<span data-ttu-id="13be9-154">单向、传出</span><span class="sxs-lookup"><span data-stu-id="13be9-154">One-way, outgoing</span></span> |
| <span data-ttu-id="13be9-155">云中的用户需要访问本地资源，本地用户不需要访问云中的资源</span><span class="sxs-lookup"><span data-stu-id="13be9-155">Users in the cloud require access to resources located on-premises, but not vice versa</span></span> |<span data-ttu-id="13be9-156">单向、传出</span><span class="sxs-lookup"><span data-stu-id="13be9-156">One-way, outgoing</span></span> |<span data-ttu-id="13be9-157">单向、传入</span><span class="sxs-lookup"><span data-stu-id="13be9-157">One-way, incoming</span></span> |
| <span data-ttu-id="13be9-158">云中的和本地用户都需要访问云中和本地拥有的资源。</span><span class="sxs-lookup"><span data-stu-id="13be9-158">Users in the cloud and on-premises both requires access to resources held in the cloud and on-premises</span></span> |<span data-ttu-id="13be9-159">双向、传入和传出</span><span class="sxs-lookup"><span data-stu-id="13be9-159">Two-way, incoming and outgoing</span></span> |<span data-ttu-id="13be9-160">双向、传入和传出</span><span class="sxs-lookup"><span data-stu-id="13be9-160">Two-way, incoming and outgoing</span></span> |

## <a name="scalability-considerations"></a><span data-ttu-id="13be9-161">可伸缩性注意事项</span><span class="sxs-lookup"><span data-stu-id="13be9-161">Scalability considerations</span></span>

<span data-ttu-id="13be9-162">Active Directory 能够针对属于同一域的域控制器自动进行缩放。</span><span class="sxs-lookup"><span data-stu-id="13be9-162">Active Directory is automatically scalable for domain controllers that are part of the same domain.</span></span> <span data-ttu-id="13be9-163">请求被分布到域中的所有控制器中。</span><span class="sxs-lookup"><span data-stu-id="13be9-163">Requests are distributed across all controllers within a domain.</span></span> <span data-ttu-id="13be9-164">可以添加另一个域控制器，并且它将与该域自动同步。</span><span class="sxs-lookup"><span data-stu-id="13be9-164">You can add another domain controller, and it synchronizes automatically with the domain.</span></span> <span data-ttu-id="13be9-165">不要配置单独的负载均衡器来将流量定向到该域中的控制器。</span><span class="sxs-lookup"><span data-stu-id="13be9-165">Do not configure a separate load balancer to direct traffic to controllers within the domain.</span></span> <span data-ttu-id="13be9-166">确保所有域控制器都有足够的内存和存储资源来处理域数据库。</span><span class="sxs-lookup"><span data-stu-id="13be9-166">Ensure that all domain controllers have sufficient memory and storage resources to handle the domain database.</span></span> <span data-ttu-id="13be9-167">使所有域控制器 VM 具有相同的大小。</span><span class="sxs-lookup"><span data-stu-id="13be9-167">Make all domain controller VMs the same size.</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="13be9-168">可用性注意事项</span><span class="sxs-lookup"><span data-stu-id="13be9-168">Availability considerations</span></span>

<span data-ttu-id="13be9-169">请至少为每个域预配两个域控制器。</span><span class="sxs-lookup"><span data-stu-id="13be9-169">Provision at least two domain controllers for each domain.</span></span> <span data-ttu-id="13be9-170">这可以在服务器之间实现自动复制。</span><span class="sxs-lookup"><span data-stu-id="13be9-170">This enables automatic replication between servers.</span></span> <span data-ttu-id="13be9-171">为充当 Active Directory 服务器（用于处理每个域）的 VM 创建一个可用性集。</span><span class="sxs-lookup"><span data-stu-id="13be9-171">Create an availability set for the VMs acting as Active Directory servers handling each domain.</span></span> <span data-ttu-id="13be9-172">至少在此可用性集中放置两个服务器。</span><span class="sxs-lookup"><span data-stu-id="13be9-172">Put at least two servers in this availability set.</span></span>

<span data-ttu-id="13be9-173">另外，请考虑将每个域中的一个或多个服务器指定为[备用操作主机][standby-operations-masters]，以应对作为灵活单一主机操作 (FSMO) 角色连接到服务器时失败的情况。</span><span class="sxs-lookup"><span data-stu-id="13be9-173">Also, consider designating one or more servers in each domain as [standby operations masters][standby-operations-masters] in case connectivity to a server acting as a flexible single master operation (FSMO) role fails.</span></span>

## <a name="manageability-considerations"></a><span data-ttu-id="13be9-174">可管理性注意事项</span><span class="sxs-lookup"><span data-stu-id="13be9-174">Manageability considerations</span></span>

<span data-ttu-id="13be9-175">有关管理和监视注意事项的详细信息，请参阅[将 Active Directory 扩展到 Azure][adds-extend-domain]。</span><span class="sxs-lookup"><span data-stu-id="13be9-175">For information about management and monitoring considerations, see [Extending Active Directory to Azure][adds-extend-domain].</span></span>

<span data-ttu-id="13be9-176">有关详细信息，请参阅[监视 Active Directory][monitoring_ad]。</span><span class="sxs-lookup"><span data-stu-id="13be9-176">For additional information, see [Monitoring Active Directory][monitoring_ad].</span></span> <span data-ttu-id="13be9-177">可以在管理子网中的监视服务器上安装 [Microsoft Systems Center][microsoft_systems_center] 之类的工具来帮助执行这些任务。</span><span class="sxs-lookup"><span data-stu-id="13be9-177">You can install tools such as [Microsoft Systems Center][microsoft_systems_center] on a monitoring server in the management subnet to help perform these tasks.</span></span>

## <a name="security-considerations"></a><span data-ttu-id="13be9-178">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="13be9-178">Security considerations</span></span>

<span data-ttu-id="13be9-179">林级别信任是可传递的。</span><span class="sxs-lookup"><span data-stu-id="13be9-179">Forest level trusts are transitive.</span></span> <span data-ttu-id="13be9-180">如果在一个本地林与云中的一个林之间建立了林级别信任，则此信任将扩展到在任一林中创建的其他新域。</span><span class="sxs-lookup"><span data-stu-id="13be9-180">If you establish a forest level trust between an on-premises forest and a forest in the cloud, this trust is extended to other new domains created in either forest.</span></span> <span data-ttu-id="13be9-181">如果出于安全目的而使用域提供隔离，请考虑仅在域级别创建信任。</span><span class="sxs-lookup"><span data-stu-id="13be9-181">If you use domains to provide separation for security purposes, consider creating trusts at the domain level only.</span></span> <span data-ttu-id="13be9-182">域级别信任是不可传递的。</span><span class="sxs-lookup"><span data-stu-id="13be9-182">Domain level trusts are non-transitive.</span></span>

<span data-ttu-id="13be9-183">有关特定于 Active Directory 的安全注意事项，请参阅[将 Active Directory 扩展到 Azure][adds-extend-domain] 中的安全注意事项部分。</span><span class="sxs-lookup"><span data-stu-id="13be9-183">For Active Directory-specific security considerations, see the security considerations section in [Extending Active Directory to Azure][adds-extend-domain].</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="13be9-184">部署解决方案</span><span class="sxs-lookup"><span data-stu-id="13be9-184">Deploy the solution</span></span>

<span data-ttu-id="13be9-185">[GitHub][github] 上提供了此体系结构的部署。</span><span class="sxs-lookup"><span data-stu-id="13be9-185">A deployment for this architecture is available on [GitHub][github].</span></span> <span data-ttu-id="13be9-186">请注意，整个部署最长可能需要花费两个小时，包括创建 VPN 网关和运行配置 AD DS 的脚本。</span><span class="sxs-lookup"><span data-stu-id="13be9-186">Note that the entire deployment can take up to two hours, which includes creating the VPN gateway and running the scripts that configure AD DS.</span></span>

### <a name="prerequisites"></a><span data-ttu-id="13be9-187">必备组件</span><span class="sxs-lookup"><span data-stu-id="13be9-187">Prerequisites</span></span>

1. <span data-ttu-id="13be9-188">克隆、下载 [GitHub 存储库](https://github.com/mspnp/identity-reference-architectures)的 zip 文件或创建其分库。</span><span class="sxs-lookup"><span data-stu-id="13be9-188">Clone, fork, or download the zip file for the [GitHub repository](https://github.com/mspnp/identity-reference-architectures).</span></span>

2. <span data-ttu-id="13be9-189">安装 [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest)。</span><span class="sxs-lookup"><span data-stu-id="13be9-189">Install [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest).</span></span>

3. <span data-ttu-id="13be9-190">安装 [Azure 构建基块](https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks) npm 包。</span><span class="sxs-lookup"><span data-stu-id="13be9-190">Install the [Azure building blocks](https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks) npm package.</span></span>

   ```bash
   npm install -g @mspnp/azure-building-blocks
   ```

4. <span data-ttu-id="13be9-191">在命令提示符、bash 提示符或 PowerShell 提示符下，按如下所示登录到你的 Azure 帐户：</span><span class="sxs-lookup"><span data-stu-id="13be9-191">From a command prompt, bash prompt, or PowerShell prompt, sign into your Azure account as follows:</span></span>

   ```bash
   az login
   ```

### <a name="deploy-the-simulated-on-premises-datacenter"></a><span data-ttu-id="13be9-192">部署模拟的本地数据中心</span><span class="sxs-lookup"><span data-stu-id="13be9-192">Deploy the simulated on-premises datacenter</span></span>

1. <span data-ttu-id="13be9-193">导航到 GitHub 存储库的 `identity/adds-forest` 文件夹。</span><span class="sxs-lookup"><span data-stu-id="13be9-193">Navigate to the `identity/adds-forest` folder of the GitHub repository.</span></span>

2. <span data-ttu-id="13be9-194">打开 `onprem.json` 文件。</span><span class="sxs-lookup"><span data-stu-id="13be9-194">Open the `onprem.json` file.</span></span> <span data-ttu-id="13be9-195">搜索 `adminPassword` 和 `Password` 的实例并添加密码值。</span><span class="sxs-lookup"><span data-stu-id="13be9-195">Search for instances of `adminPassword` and `Password` and add values for the passwords.</span></span>

3. <span data-ttu-id="13be9-196">运行以下命令，并等待部署完成：</span><span class="sxs-lookup"><span data-stu-id="13be9-196">Run the following command and wait for the deployment to finish:</span></span>

    ```bash
    azbb -s <subscription_id> -g <resource group> -l <location> -p onprem.json --deploy
    ```

### <a name="deploy-the-azure-vnet"></a><span data-ttu-id="13be9-197">部署 Azure VNet</span><span class="sxs-lookup"><span data-stu-id="13be9-197">Deploy the Azure VNet</span></span>

1. <span data-ttu-id="13be9-198">打开 `azure.json` 文件。</span><span class="sxs-lookup"><span data-stu-id="13be9-198">Open the `azure.json` file.</span></span> <span data-ttu-id="13be9-199">搜索 `adminPassword` 和 `Password` 的实例并添加密码值。</span><span class="sxs-lookup"><span data-stu-id="13be9-199">Search for instances of `adminPassword` and `Password` and add values for the passwords.</span></span>

2. <span data-ttu-id="13be9-200">在同一文件中，搜索 `sharedKey` 的实例并输入 VPN 连接的共享密钥。</span><span class="sxs-lookup"><span data-stu-id="13be9-200">In the same file, search for instances of `sharedKey` and enter shared keys for the VPN connection.</span></span>

    ```json
    "sharedKey": "",
    ```

3. <span data-ttu-id="13be9-201">运行以下命令并等待部署完成。</span><span class="sxs-lookup"><span data-stu-id="13be9-201">Run the following command and wait for the deployment to finish.</span></span>

    ```bash
    azbb -s <subscription_id> -g <resource group> -l <location> -p azure.json --deploy
    ```

   <span data-ttu-id="13be9-202">部署到本地 VNet 所在的同一个资源组。</span><span class="sxs-lookup"><span data-stu-id="13be9-202">Deploy to the same resource group as the on-premises VNet.</span></span>

### <a name="test-the-ad-trust-relation"></a><span data-ttu-id="13be9-203">测试 AD 信任关系</span><span class="sxs-lookup"><span data-stu-id="13be9-203">Test the AD trust relation</span></span>

1. <span data-ttu-id="13be9-204">使用 Azure 门户导航到已创建的资源组。</span><span class="sxs-lookup"><span data-stu-id="13be9-204">Use the Azure portal, navigate to the resource group that you created.</span></span>

2. <span data-ttu-id="13be9-205">使用 Azure 门户找到名为 `ra-adt-mgmt-vm1` 的 VM。</span><span class="sxs-lookup"><span data-stu-id="13be9-205">Use the Azure portal to find the VM named `ra-adt-mgmt-vm1`.</span></span>

3. <span data-ttu-id="13be9-206">单击 `Connect` 来与 VM 建立远程桌面会话。</span><span class="sxs-lookup"><span data-stu-id="13be9-206">Click `Connect` to open a remote desktop session to the VM.</span></span> <span data-ttu-id="13be9-207">用户名为 `contoso\testuser`，密码为 `onprem.json` 参数文件中指定的密码。</span><span class="sxs-lookup"><span data-stu-id="13be9-207">The username is `contoso\testuser`, and the password is the one that you specified in the `onprem.json` parameter file.</span></span>

4. <span data-ttu-id="13be9-208">在远程桌面会话中，与 192.168.0.4（名为 `ra-adtrust-onpremise-ad-vm1` 的 VM 的 IP 地址）建立另一个远程桌面会话。</span><span class="sxs-lookup"><span data-stu-id="13be9-208">From inside your remote desktop session, open another remote desktop session to 192.168.0.4, which is the IP address of the VM named `ra-adtrust-onpremise-ad-vm1`.</span></span> <span data-ttu-id="13be9-209">用户名为 `contoso\testuser`，密码为 `azure.json` 参数文件中指定的密码。</span><span class="sxs-lookup"><span data-stu-id="13be9-209">The username is `contoso\testuser`, and the password is the one that you specified in the `azure.json` parameter file.</span></span>

5. <span data-ttu-id="13be9-210">在 `ra-adtrust-onpremise-ad-vm1` 的远程桌面会话中转到“服务器管理器”，然后单击“工具” > “Active Directory 域和信任”。</span><span class="sxs-lookup"><span data-stu-id="13be9-210">From inside the remote desktop session for `ra-adtrust-onpremise-ad-vm1`, go to **Server Manager** and click **Tools** > **Active Directory Domains and Trusts**.</span></span>

6. <span data-ttu-id="13be9-211">在左窗格中右键单击“contoso.com”，然后选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="13be9-211">In the left pane, right-click on the contoso.com and select **Properties**.</span></span>

7. <span data-ttu-id="13be9-212">单击“信任”选项卡。此时会看到 treyresearch.net 作为传入信任列出。</span><span class="sxs-lookup"><span data-stu-id="13be9-212">Click the **Trusts** tab. You should see treyresearch.net listed as an incoming trust.</span></span>

![“Active Directory 林信任”对话框的屏幕截图](./images/ad-forest-trust.png)

## <a name="next-steps"></a><span data-ttu-id="13be9-214">后续步骤</span><span class="sxs-lookup"><span data-stu-id="13be9-214">Next steps</span></span>

- <span data-ttu-id="13be9-215">了解[将本地 AD DS 域扩展到 Azure][adds-extend-domain] 的最佳做法</span><span class="sxs-lookup"><span data-stu-id="13be9-215">Learn the best practices for [extending your on-premises AD DS domain to Azure][adds-extend-domain]</span></span>
- <span data-ttu-id="13be9-216">了解在 Azure 中[创建 AD FS 基础结构][adfs]的最佳做法。</span><span class="sxs-lookup"><span data-stu-id="13be9-216">Learn the best practices for [creating an AD FS infrastructure][adfs] in Azure.</span></span>

<!-- links -->
[adds-extend-domain]: adds-extend-domain.md
[adfs]: adfs.md
[azure-cli-2]: /azure/install-azure-cli
[azbb]: https://github.com/mspnp/template-building-blocks/wiki/Install-Azure-Building-Blocks

[implementing-a-secure-hybrid-network-architecture]: ../dmz/secure-vnet-hybrid.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ../dmz/secure-vnet-dmz.md

[running-VMs-for-an-N-tier-architecture-on-Azure]: ../virtual-machines-windows/n-tier.md

[ad-azure-guidelines]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[azure-expressroute]: /azure/expressroute/expressroute-introduction
[azure-vpn-gateway]: /azure/vpn-gateway/vpn-gateway-about-vpngateways
[considerations]: ./considerations.md
[creating-external-trusts]: https://technet.microsoft.com/library/cc816837(v=ws.10).aspx
[creating-forest-trusts]: https://technet.microsoft.com/library/cc816810(v=ws.10).aspx
[github]: https://github.com/mspnp/identity-reference-architectures/tree/master/adds-forest
[incoming-trust]: https://raw.githubusercontent.com/mspnp/identity-reference-architectures/master/adds-forest/extensions/incoming-trust.ps1
[microsoft_systems_center]: https://microsoft.com/cloud-platform/system-center
[monitoring_ad]: https://msdn.microsoft.com/library/bb727046.aspx
[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview
[solution-script]: https://raw.githubusercontent.com/mspnp/identity-reference-architectures/master/adds-forest/Deploy-ReferenceArchitecture.ps1
[standby-operations-masters]: https://technet.microsoft.com/library/cc794737(v=ws.10).aspx
[outgoing-trust]: https://raw.githubusercontent.com/mspnp/identity-reference-architectures/master/adds-forest/extensions/outgoing-trust.ps1
[verify-a-trust]: https://technet.microsoft.com/library/cc753821.aspx
[visio-download]: https://archcenter.blob.core.windows.net/cdn/identity-architectures.vsdx
