---
title: 用于开发/测试工作负荷的 SAP
description: 用于开发/测试环境的 SAP 方案
author: AndrewDibbins
ms.date: 7/11/18
ms.openlocfilehash: d0f266e40969cf4782e69041889a686387499722
ms.sourcegitcommit: c49aeef818d7dfe271bc4128b230cfc676f05230
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2018
ms.locfileid: "44389173"
---
# <a name="sap-for-devtest-workloads"></a><span data-ttu-id="51519-103">用于开发/测试工作负荷的 SAP</span><span class="sxs-lookup"><span data-stu-id="51519-103">SAP for dev/test workloads</span></span>

<span data-ttu-id="51519-104">本示例介绍了如何运行一个开发/测试实现，以便在 Azure 上的 Windows 或 Linux 环境中实现 SAP NetWeaver。</span><span class="sxs-lookup"><span data-stu-id="51519-104">This example provides guidance for how to run a dev/test implementation of SAP NetWeaver in a Windows or Linux environment on Azure.</span></span> <span data-ttu-id="51519-105">使用的数据库为 AnyDB，这是一个 SAP 术语，指任何受支持的 DBMS（不是 SAP HANA）。</span><span class="sxs-lookup"><span data-stu-id="51519-105">The database used is AnyDB, the SAP term for any supported DBMS (that isn't SAP HANA).</span></span> <span data-ttu-id="51519-106">由于此体系结构设计用于非生产环境，因此在部署时只有一个虚拟机 (VM)，其大小可以根据你组织的需要进行更改。</span><span class="sxs-lookup"><span data-stu-id="51519-106">Because this architecture is designed for non-production environments, it's deployed with just a single virtual machine (VM) and it's size can be changed to accommodate your organization's needs.</span></span>

<span data-ttu-id="51519-107">对于生产用例，请查看下面提供的 SAP 参考体系结构：</span><span class="sxs-lookup"><span data-stu-id="51519-107">For production use cases review the SAP reference architectures available below:</span></span>

* <span data-ttu-id="51519-108">[适用于 AnyDB 的 SAP NetWeaver][sap-netweaver]</span><span class="sxs-lookup"><span data-stu-id="51519-108">[SAP netweaver for AnyDB][sap-netweaver]</span></span>
* <span data-ttu-id="51519-109">[SAP S/4Hana][sap-hana]</span><span class="sxs-lookup"><span data-stu-id="51519-109">[SAP S/4Hana][sap-hana]</span></span>
* <span data-ttu-id="51519-110">[Azure SAP 大型实例][sap-large]</span><span class="sxs-lookup"><span data-stu-id="51519-110">[SAP on Azure large instances][sap-large]</span></span>

## <a name="related-use-cases"></a><span data-ttu-id="51519-111">相关的用例</span><span class="sxs-lookup"><span data-stu-id="51519-111">Related use cases</span></span>

<span data-ttu-id="51519-112">以下用例可以考虑本方案：</span><span class="sxs-lookup"><span data-stu-id="51519-112">Consider this scenario for the following use cases:</span></span>

* <span data-ttu-id="51519-113">非关键性 SAP 非生产工作负荷（沙盒、开发、测试、质量保证）</span><span class="sxs-lookup"><span data-stu-id="51519-113">Non-critical SAP non-productive workloads (sandbox, development, test, quality assurance)</span></span>
* <span data-ttu-id="51519-114">非关键性 SAP 业务型工作负荷</span><span class="sxs-lookup"><span data-stu-id="51519-114">Non-critical SAP business one workloads</span></span>

## <a name="architecture"></a><span data-ttu-id="51519-115">体系结构</span><span class="sxs-lookup"><span data-stu-id="51519-115">Architecture</span></span>

![图表](media/sap-2tier/SAP-Infra-2Tier_finalversion.png)

<span data-ttu-id="51519-117">本方案介绍如何在单个虚拟机上预配单个 SAP 系统数据库和 SAP 应用程序服务器。数据流经方案的情形如下所示：</span><span class="sxs-lookup"><span data-stu-id="51519-117">This scenario covers the provision of a single SAP system database and SAP application Server on a single virtual machine, the data flows through the scenario as follows:</span></span>

1. <span data-ttu-id="51519-118">展示层的客户使用其 SAP GUI 或本地的其他用户界面（Internet Explorer、Excel 或其他 Web 应用程序）来访问基于 Azure 的 SAP 系统。</span><span class="sxs-lookup"><span data-stu-id="51519-118">Customers from the Presentation Tier use their SAP GUI, or other user interfaces (Internet Explorer, Excel, or other web application) on premise to access the Azure-based SAP system.</span></span>
2. <span data-ttu-id="51519-119">使用既定的 Express Route 来提供连接。</span><span class="sxs-lookup"><span data-stu-id="51519-119">Connectivity is provided through the use of an established Express Route.</span></span> <span data-ttu-id="51519-120">Express Route 连接在 Azure 中的 Express Route 网关处终止。</span><span class="sxs-lookup"><span data-stu-id="51519-120">The Express Route connection is terminated in Azure at the Express Route Gateway.</span></span> <span data-ttu-id="51519-121">网络流量的路径是：通过 Express Route 网关到达网关子网，再从网关子网到达应用程序层辐射子网（参见[中心辐射][hub-spoke]模式），最后通过网络安全网关到达 SAP 应用程序虚拟机。</span><span class="sxs-lookup"><span data-stu-id="51519-121">Network traffic routes through the Express Route gateway to the Gateway Subnet and from the gateway subnet to the Application Tier Spoke subnet (see the [hub-spoke][hub-spoke] pattern) and via a Network Security Gateway to the SAP application virtual machine.</span></span>
3. <span data-ttu-id="51519-122">标识管理服务器提供身份验证服务。</span><span class="sxs-lookup"><span data-stu-id="51519-122">The identity management servers provide authentication services.</span></span>
4. <span data-ttu-id="51519-123">跳转盒提供本地管理功能。</span><span class="sxs-lookup"><span data-stu-id="51519-123">The jump box provides local management capabilities.</span></span>

### <a name="components"></a><span data-ttu-id="51519-124">组件</span><span class="sxs-lookup"><span data-stu-id="51519-124">Components</span></span>

* <span data-ttu-id="51519-125">[资源组](/azure/azure-resource-manager/resource-group-overview#resource-groups)是 Azure 资源的逻辑容器</span><span class="sxs-lookup"><span data-stu-id="51519-125">[Resource Groups](/azure/azure-resource-manager/resource-group-overview#resource-groups) is a logical container for Azure resources.</span></span>
* <span data-ttu-id="51519-126">[虚拟网络](/azure/virtual-network/virtual-networks-overview)是在 Azure 中进行通信的基础</span><span class="sxs-lookup"><span data-stu-id="51519-126">[Virtual Networks](/azure/virtual-network/virtual-networks-overview) is the basis of network communications within Azure</span></span>
* <span data-ttu-id="51519-127">[虚拟机](/azure/virtual-machines/windows/overview)：Azure 虚拟机使用 Windows 或 Linux Server 按需提供具有高可伸缩性并且十分安全的虚拟化基础结构</span><span class="sxs-lookup"><span data-stu-id="51519-127">[Virtual Machine](/azure/virtual-machines/windows/overview) Azure Virtual Machines provides on-demand, high-scale, secure, virtualized infrastructure using Windows or Linux Server</span></span>
* <span data-ttu-id="51519-128">使用 [Express Route](/azure/expressroute/expressroute-introduction) 可通过连接服务提供商所提供的专用连接，将本地网络扩展到 Microsoft 云。</span><span class="sxs-lookup"><span data-stu-id="51519-128">[Express Route](/azure/expressroute/expressroute-introduction) lets you extend your on-premises networks into the Microsoft cloud over a private connection facilitated by a connectivity provider.</span></span>
* <span data-ttu-id="51519-129">[网络安全组](/azure/virtual-network/security-overview)用于限制发往虚拟网络中的资源的网络流量。</span><span class="sxs-lookup"><span data-stu-id="51519-129">[Network Security Group](/azure/virtual-network/security-overview) lets you limit network traffic to resources in a virtual network.</span></span> <span data-ttu-id="51519-130">网络安全组包含一个安全规则列表，这些规则可根据源或目标 IP 地址、端口和协议允许或拒绝入站或出站网络流量。</span><span class="sxs-lookup"><span data-stu-id="51519-130">A network security group contains a list of security rules that allow or deny inbound or outbound network traffic based on source or destination IP address, port, and protocol.</span></span> 

## <a name="considerations"></a><span data-ttu-id="51519-131">注意事项</span><span class="sxs-lookup"><span data-stu-id="51519-131">Considerations</span></span>

### <a name="availability"></a><span data-ttu-id="51519-132">可用性</span><span class="sxs-lookup"><span data-stu-id="51519-132">Availability</span></span>

 <span data-ttu-id="51519-133">Microsoft 提供了用于单个 VM 实例的服务级别协议 (SLA)。</span><span class="sxs-lookup"><span data-stu-id="51519-133">Microsoft offers a service level agreement (SLA) for single VM instances.</span></span> <span data-ttu-id="51519-134">若要详细了解适用于虚拟机的 Microsoft Azure 服务级别协议，请参阅[虚拟机的 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines)</span><span class="sxs-lookup"><span data-stu-id="51519-134">For more information on Microsoft Azure Service Level Agreement for Virtual Machines [SLA For Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines)</span></span>

### <a name="scalability"></a><span data-ttu-id="51519-135">可伸缩性</span><span class="sxs-lookup"><span data-stu-id="51519-135">Scalability</span></span>

<span data-ttu-id="51519-136">有关如何设计可缩放解决方案的通用指南，请参阅 Azure 体系结构中心的[可伸缩性核对清单][scalability]。</span><span class="sxs-lookup"><span data-stu-id="51519-136">For general guidance on designing scalable solutions, see the [scalability checklist][scalability] in the Azure Architecture Center.</span></span>

### <a name="security"></a><span data-ttu-id="51519-137">安全</span><span class="sxs-lookup"><span data-stu-id="51519-137">Security</span></span>

<span data-ttu-id="51519-138">若需安全解决方案的通用设计指南，请参阅 [Azure 安全性文档][security]。</span><span class="sxs-lookup"><span data-stu-id="51519-138">For general guidance on designing secure solutions, see the [Azure Security Documentation][security].</span></span>

### <a name="resiliency"></a><span data-ttu-id="51519-139">复原</span><span class="sxs-lookup"><span data-stu-id="51519-139">Resiliency</span></span>

<span data-ttu-id="51519-140">若需可复原解决方案的通用设计指南，请参阅[设计适用于 Azure 的可复原应用程序][resiliency]。</span><span class="sxs-lookup"><span data-stu-id="51519-140">For general guidance on designing resilient solutions, see [Designing resilient applications for Azure][resiliency].</span></span>

## <a name="pricing"></a><span data-ttu-id="51519-141">定价</span><span class="sxs-lookup"><span data-stu-id="51519-141">Pricing</span></span>

<span data-ttu-id="51519-142">为了方便用户了解运行本方案的成本，我们已在成本计算器中预配置了所有服务。</span><span class="sxs-lookup"><span data-stu-id="51519-142">Explore the cost of running this scenario, all of the services are pre-configured in the cost calculator.</span></span>  <span data-ttu-id="51519-143">若要了解自己的特定用例的定价变化情况，请按预期的流量更改相应的变量。</span><span class="sxs-lookup"><span data-stu-id="51519-143">To see how the pricing would change for your particular use case change the appropriate variables to match your expected traffic.</span></span>

<span data-ttu-id="51519-144">我们已根据你预期接收的流量提供了四个示例性的成本配置文件：</span><span class="sxs-lookup"><span data-stu-id="51519-144">We have provided four sample cost profiles based on amount of traffic you expect to get:</span></span>

|<span data-ttu-id="51519-145">大小</span><span class="sxs-lookup"><span data-stu-id="51519-145">Size</span></span>|<span data-ttu-id="51519-146">SAP</span><span class="sxs-lookup"><span data-stu-id="51519-146">SAPs</span></span>|<span data-ttu-id="51519-147">VM 类型</span><span class="sxs-lookup"><span data-stu-id="51519-147">VM Type</span></span>|<span data-ttu-id="51519-148">存储</span><span class="sxs-lookup"><span data-stu-id="51519-148">Storage</span></span>|<span data-ttu-id="51519-149">Azure 定价计算器</span><span class="sxs-lookup"><span data-stu-id="51519-149">Azure Pricing Calculator</span></span>|
|----|----|-------|-------|---------------|
|<span data-ttu-id="51519-150">小型</span><span class="sxs-lookup"><span data-stu-id="51519-150">Small</span></span>|<span data-ttu-id="51519-151">8000</span><span class="sxs-lookup"><span data-stu-id="51519-151">8000</span></span>|<span data-ttu-id="51519-152">D8s_v3</span><span class="sxs-lookup"><span data-stu-id="51519-152">D8s_v3</span></span>|<span data-ttu-id="51519-153">2xP20、1xP10</span><span class="sxs-lookup"><span data-stu-id="51519-153">2xP20, 1xP10</span></span>|[<span data-ttu-id="51519-154">小型</span><span class="sxs-lookup"><span data-stu-id="51519-154">Small</span></span>](https://azure.com/e/9d26b9612da9466bb7a800eab56e71d1)|
|<span data-ttu-id="51519-155">中型</span><span class="sxs-lookup"><span data-stu-id="51519-155">Medium</span></span>|<span data-ttu-id="51519-156">16000</span><span class="sxs-lookup"><span data-stu-id="51519-156">16000</span></span>|<span data-ttu-id="51519-157">D16s_v3</span><span class="sxs-lookup"><span data-stu-id="51519-157">D16s_v3</span></span>|<span data-ttu-id="51519-158">3xP20、1xP10</span><span class="sxs-lookup"><span data-stu-id="51519-158">3xP20, 1xP10</span></span>|[<span data-ttu-id="51519-159">中型</span><span class="sxs-lookup"><span data-stu-id="51519-159">Medium</span></span>](https://azure.com/e/465bd07047d148baab032b2f461550cd)|
<span data-ttu-id="51519-160">大型</span><span class="sxs-lookup"><span data-stu-id="51519-160">Large</span></span>|<span data-ttu-id="51519-161">32000</span><span class="sxs-lookup"><span data-stu-id="51519-161">32000</span></span>|<span data-ttu-id="51519-162">E32s_v3</span><span class="sxs-lookup"><span data-stu-id="51519-162">E32s_v3</span></span>|<span data-ttu-id="51519-163">3xP20、1xP10</span><span class="sxs-lookup"><span data-stu-id="51519-163">3xP20, 1xP10</span></span>|[<span data-ttu-id="51519-164">大型</span><span class="sxs-lookup"><span data-stu-id="51519-164">Large</span></span>](https://azure.com/e/ada2e849d68b41c3839cc976000c6931)|
<span data-ttu-id="51519-165">特大型</span><span class="sxs-lookup"><span data-stu-id="51519-165">Extra Large</span></span>|<span data-ttu-id="51519-166">64000</span><span class="sxs-lookup"><span data-stu-id="51519-166">64000</span></span>|<span data-ttu-id="51519-167">M64s</span><span class="sxs-lookup"><span data-stu-id="51519-167">M64s</span></span>|<span data-ttu-id="51519-168">4xP20、1xP10</span><span class="sxs-lookup"><span data-stu-id="51519-168">4xP20, 1xP10</span></span>|[<span data-ttu-id="51519-169">特大型</span><span class="sxs-lookup"><span data-stu-id="51519-169">Extra Large</span></span>](https://azure.com/e/975fb58a965c4fbbb54c5c9179c61cef)|

<span data-ttu-id="51519-170">注意：定价为指导价，仅表明 VM 和存储成本（不包括网络、备份存储和数据入口/出口费用）。</span><span class="sxs-lookup"><span data-stu-id="51519-170">Note: pricing is a guide and only indicates the VMs and storage costs (excludes, networking, backup storage, and data ingress/egress charges).</span></span>

* <span data-ttu-id="51519-171">[小](https://azure.com/e/9d26b9612da9466bb7a800eab56e71d1)：小型系统，其 VM 类型为 D8s_v3，使用 8x vCPU，32 GB RAM 和 200 GB 临时存储，另外还有两个 512 GB 的和一个 128 GB 的高级存储磁盘。</span><span class="sxs-lookup"><span data-stu-id="51519-171">[Small](https://azure.com/e/9d26b9612da9466bb7a800eab56e71d1): A small system consists of VM type D8s_v3 with 8x vCPUs, 32 GB RAM and 200 GB temp storage, additionally two 512 GB and one 128 GB premium storage disks.</span></span>
* <span data-ttu-id="51519-172">[中](https://azure.com/e/465bd07047d148baab032b2f461550cd)：中型系统，其 VM 类型为 D16s_v3，使用 16x vCPU，64 GB RAM 和 400 GB 临时存储，另外还有三个 512 GB 的和一个 128 GB 的高级存储磁盘。</span><span class="sxs-lookup"><span data-stu-id="51519-172">[Medium](https://azure.com/e/465bd07047d148baab032b2f461550cd): A medium system consists of VM type D16s_v3 with 16x vCPUs, 64 GB RAM and 400 GB temp storage, additionally three 512 GB and one 128 GB premium storage disks.</span></span>
* <span data-ttu-id="51519-173">[大](https://azure.com/e/ada2e849d68b41c3839cc976000c6931)：大型系统，其 VM 类型为 E32s_v3，使用 32x vCPU，256 GB RAM 和 512 GB 临时存储，另外还有三个 512 GB 的和一个 128 GB 的高级存储磁盘。</span><span class="sxs-lookup"><span data-stu-id="51519-173">[Large](https://azure.com/e/ada2e849d68b41c3839cc976000c6931): A large system consists of VM type E32s_v3 with 32x vCPUs, 256 GB RAM and 512 GB temp storage, additionally three 512GB and one 128GB premium storage disks.</span></span>
* <span data-ttu-id="51519-174">[特大](https://azure.com/e/975fb58a965c4fbbb54c5c9179c61cef)：特大型系统，其 VM 类型为 M64s，使用 64x vCPU，1024 GB RAM 和 2000 GB 临时存储，另外还有四个 512 GB 的和一个 128 GB 的高级存储磁盘。</span><span class="sxs-lookup"><span data-stu-id="51519-174">[Extra Large](https://azure.com/e/975fb58a965c4fbbb54c5c9179c61cef): An extra large system consists of a VM type M64s with 64x vCPUs, 1024 GB RAM and 2000 GB temp storage, additionally four 512 GB and one 128 GB premium storage disks.</span></span>

## <a name="deployment"></a><span data-ttu-id="51519-175">部署</span><span class="sxs-lookup"><span data-stu-id="51519-175">Deployment</span></span>

<span data-ttu-id="51519-176">若要部署类似于上述方案的底层基础结构，请使用部署按钮</span><span class="sxs-lookup"><span data-stu-id="51519-176">To deploy the underlying infrastructure similar to the scenario above, use the deploy button</span></span>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Fsolution-architectures%2Fmaster%2Fapps%2Fsap-2tier%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

<span data-ttu-id="51519-177">\* SAP 不会自动安装，请在生成基础结构后手动进行安装。</span><span class="sxs-lookup"><span data-stu-id="51519-177">\* SAP won't be automatically installed, manually install it after the infrastructure has been built.</span></span>

<!-- links -->
[reference architecture]:  /azure/architecture/reference-architectures/sap
[resiliency]: /azure/architecture/resiliency/
[security]: /azure/security/
[scalability]: /azure/architecture/checklist/scalability
[sap-netweaver]: /azure/architecture/reference-architectures/sap/sap-netweaver
[sap-hana]: /azure/architecture/reference-architectures/sap/sap-s4hana
[sap-large]: /azure/architecture/reference-architectures/sap/hana-large-instances
[hub-spoke]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke