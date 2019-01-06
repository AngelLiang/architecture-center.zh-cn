---
title: SAP 工作负荷的开发/测试环境
titleSuffix: Azure Example Scenarios
description: 为 SAP 工作负荷构建开发/测试环境。
author: AndrewDibbins
ms.date: 7/11/18
ms.custom: fasttrack
ms.openlocfilehash: 3f6c828e8757a3f82ad6972a8f21cd2fed629162
ms.sourcegitcommit: bb7fcffbb41e2c26a26f8781df32825eb60df70c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/20/2018
ms.locfileid: "53643962"
---
# <a name="devtest-environments-for-sap-workloads-on-azure"></a><span data-ttu-id="0124d-103">Azure 上的 SAP 工作负荷的开发/测试环境</span><span class="sxs-lookup"><span data-stu-id="0124d-103">Dev/test environments for SAP workloads on Azure</span></span>

<span data-ttu-id="0124d-104">本示例演示如何针对 Azure 上的 Windows 或 Linux 环境中的 SAP NetWeaver 建立开发/测试环境。</span><span class="sxs-lookup"><span data-stu-id="0124d-104">This example shows how to establish a dev/test environment for SAP NetWeaver in a Windows or Linux environment on Azure.</span></span> <span data-ttu-id="0124d-105">使用的数据库为 AnyDB，这是一个 SAP 术语，指任何受支持的 DBMS（不是 SAP HANA）。</span><span class="sxs-lookup"><span data-stu-id="0124d-105">The database used is AnyDB, the SAP term for any supported DBMS (that isn't SAP HANA).</span></span> <span data-ttu-id="0124d-106">由于此体系结构设计用于非生产环境，因此在部署时只有一个虚拟机 (VM)，其大小可以根据你组织的需要进行更改。</span><span class="sxs-lookup"><span data-stu-id="0124d-106">Because this architecture is designed for non-production environments, it's deployed with just a single virtual machine (VM) and it's size can be changed to accommodate your organization's needs.</span></span>

<span data-ttu-id="0124d-107">对于生产用例，请查看下面提供的 SAP 参考体系结构：</span><span class="sxs-lookup"><span data-stu-id="0124d-107">For production use cases review the SAP reference architectures available below:</span></span>

- <span data-ttu-id="0124d-108">[适用于 AnyDB 的 SAP NetWeaver][sap-netweaver]</span><span class="sxs-lookup"><span data-stu-id="0124d-108">[SAP NetWeaver for AnyDB][sap-netweaver]</span></span>
- <span data-ttu-id="0124d-109">[SAP S/4HANA][sap-hana]</span><span class="sxs-lookup"><span data-stu-id="0124d-109">[SAP S/4HANA][sap-hana]</span></span>
- <span data-ttu-id="0124d-110">[Azure SAP 大型实例][sap-large]</span><span class="sxs-lookup"><span data-stu-id="0124d-110">[SAP on Azure large instances][sap-large]</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="0124d-111">相关用例</span><span class="sxs-lookup"><span data-stu-id="0124d-111">Relevant use cases</span></span>

<span data-ttu-id="0124d-112">其他相关用例包括：</span><span class="sxs-lookup"><span data-stu-id="0124d-112">Other relevant use cases include:</span></span>

- <span data-ttu-id="0124d-113">非关键性 SAP 非生产工作负荷（沙盒、开发、测试、质量保证）</span><span class="sxs-lookup"><span data-stu-id="0124d-113">Non-critical SAP non-productive workloads (sandbox, development, test, quality assurance)</span></span>
- <span data-ttu-id="0124d-114">非关键性 SAP 业务型工作负荷</span><span class="sxs-lookup"><span data-stu-id="0124d-114">Non-critical SAP business workloads</span></span>

## <a name="architecture"></a><span data-ttu-id="0124d-115">体系结构</span><span class="sxs-lookup"><span data-stu-id="0124d-115">Architecture</span></span>

![SAP 工作负荷的开发/测试环境的体系结构图](media/architecture-sap-dev-test.png)

<span data-ttu-id="0124d-117">本方案演示如何在单个虚拟机上预配单个 SAP 系统数据库和 SAP 应用程序服务器。</span><span class="sxs-lookup"><span data-stu-id="0124d-117">This scenario demonstrates provisioning a single SAP system database and SAP application server on a single virtual machine.</span></span> <span data-ttu-id="0124d-118">数据流经方案的情形如下所示：</span><span class="sxs-lookup"><span data-stu-id="0124d-118">The data flows through the scenario as follows:</span></span>

1. <span data-ttu-id="0124d-119">客户使用 SAP 用户界面或其他客户端工具（Excel、Web 浏览器或其他 Web 应用程序）来访问基于 Azure 的 SAP 系统。</span><span class="sxs-lookup"><span data-stu-id="0124d-119">Customers use the SAP user interface or other client tools (Excel, a web browser, or other web application) to access the Azure-based SAP system.</span></span>
2. <span data-ttu-id="0124d-120">使用既定的 ExpressRoute 来提供连接。</span><span class="sxs-lookup"><span data-stu-id="0124d-120">Connectivity is provided through the use of an established ExpressRoute.</span></span> <span data-ttu-id="0124d-121">ExpressRoute 连接在 Azure 中的 ExpressRoute 网关处终止。</span><span class="sxs-lookup"><span data-stu-id="0124d-121">The ExpressRoute connection is terminated in Azure at the ExpressRoute gateway.</span></span> <span data-ttu-id="0124d-122">网络流量的路径是：通过 ExpressRoute 网关到达网关子网，再从网关子网到达应用程序层辐射子网（参见[中心辐射][hub-spoke]模式），最后通过网络安全网关到达 SAP 应用程序虚拟机。</span><span class="sxs-lookup"><span data-stu-id="0124d-122">Network traffic routes through the ExpressRoute gateway to the gateway subnet, and from the gateway subnet to the application-tier spoke subnet (see the [hub-spoke pattern][hub-spoke]) and via a Network Security Gateway to the SAP application virtual machine.</span></span>
3. <span data-ttu-id="0124d-123">标识管理服务器提供身份验证服务。</span><span class="sxs-lookup"><span data-stu-id="0124d-123">The identity management servers provide authentication services.</span></span>
4. <span data-ttu-id="0124d-124">跳转盒提供本地管理功能。</span><span class="sxs-lookup"><span data-stu-id="0124d-124">The jump box provides local management capabilities.</span></span>

### <a name="components"></a><span data-ttu-id="0124d-125">组件</span><span class="sxs-lookup"><span data-stu-id="0124d-125">Components</span></span>

- <span data-ttu-id="0124d-126">[虚拟网络](/azure/virtual-network/virtual-networks-overview)是在 Azure 中进行网络通信的基础。</span><span class="sxs-lookup"><span data-stu-id="0124d-126">[Virtual Networks](/azure/virtual-network/virtual-networks-overview) are the basis of network communication within Azure.</span></span>
- <span data-ttu-id="0124d-127">[虚拟机](/azure/virtual-machines/windows/overview)：Azure 虚拟机使用 Windows 或 Linux Server 按需提供具有高可伸缩性并且十分安全的虚拟化基础结构。</span><span class="sxs-lookup"><span data-stu-id="0124d-127">[Virtual Machine](/azure/virtual-machines/windows/overview) Azure Virtual Machines provides on-demand, high-scale, secure, virtualized infrastructure using Windows or Linux Server.</span></span>
- <span data-ttu-id="0124d-128">使用 [ExpressRoute](/azure/expressroute/expressroute-introduction) 可通过连接服务提供商所提供的专用连接，将本地网络扩展到 Microsoft 云。</span><span class="sxs-lookup"><span data-stu-id="0124d-128">[ExpressRoute](/azure/expressroute/expressroute-introduction) lets you extend your on-premises networks into the Microsoft cloud over a private connection facilitated by a connectivity provider.</span></span>
- <span data-ttu-id="0124d-129">[网络安全组](/azure/virtual-network/security-overview)用于限制发往虚拟网络中的资源的网络流量。</span><span class="sxs-lookup"><span data-stu-id="0124d-129">[Network Security Group](/azure/virtual-network/security-overview) lets you limit network traffic to resources in a virtual network.</span></span> <span data-ttu-id="0124d-130">网络安全组包含一个安全规则列表，这些规则可根据源或目标 IP 地址、端口和协议允许或拒绝入站或出站网络流量。</span><span class="sxs-lookup"><span data-stu-id="0124d-130">A network security group contains a list of security rules that allow or deny inbound or outbound network traffic based on source or destination IP address, port, and protocol.</span></span>
- <span data-ttu-id="0124d-131">[资源组](/azure/azure-resource-manager/resource-group-overview#resource-groups)充当 Azure 资源的逻辑容器。</span><span class="sxs-lookup"><span data-stu-id="0124d-131">[Resource Groups](/azure/azure-resource-manager/resource-group-overview#resource-groups) act as logical containers for Azure resources.</span></span>

## <a name="considerations"></a><span data-ttu-id="0124d-132">注意事项</span><span class="sxs-lookup"><span data-stu-id="0124d-132">Considerations</span></span>

### <a name="availability"></a><span data-ttu-id="0124d-133">可用性</span><span class="sxs-lookup"><span data-stu-id="0124d-133">Availability</span></span>

 <span data-ttu-id="0124d-134">Microsoft 提供了用于单个 VM 实例的服务级别协议 (SLA)。</span><span class="sxs-lookup"><span data-stu-id="0124d-134">Microsoft offers a service level agreement (SLA) for single VM instances.</span></span> <span data-ttu-id="0124d-135">若要详细了解适用于虚拟机的 Microsoft Azure 服务级别协议，请参阅[虚拟机的 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines)</span><span class="sxs-lookup"><span data-stu-id="0124d-135">For more information on Microsoft Azure Service Level Agreement for Virtual Machines [SLA For Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines)</span></span>

### <a name="scalability"></a><span data-ttu-id="0124d-136">可伸缩性</span><span class="sxs-lookup"><span data-stu-id="0124d-136">Scalability</span></span>

<span data-ttu-id="0124d-137">有关如何设计可缩放解决方案的通用指南，请参阅 Azure 体系结构中心的[可伸缩性核对清单][scalability]。</span><span class="sxs-lookup"><span data-stu-id="0124d-137">For general guidance on designing scalable solutions, see the [scalability checklist][scalability] in the Azure Architecture Center.</span></span>

### <a name="security"></a><span data-ttu-id="0124d-138">安全</span><span class="sxs-lookup"><span data-stu-id="0124d-138">Security</span></span>

<span data-ttu-id="0124d-139">若需安全解决方案的通用设计指南，请参阅 [Azure 安全性文档][security]。</span><span class="sxs-lookup"><span data-stu-id="0124d-139">For general guidance on designing secure solutions, see the [Azure Security Documentation][security].</span></span>

### <a name="resiliency"></a><span data-ttu-id="0124d-140">复原</span><span class="sxs-lookup"><span data-stu-id="0124d-140">Resiliency</span></span>

<span data-ttu-id="0124d-141">若需可复原解决方案的通用设计指南，请参阅[设计适用于 Azure 的可复原应用程序][resiliency]。</span><span class="sxs-lookup"><span data-stu-id="0124d-141">For general guidance on designing resilient solutions, see [Designing resilient applications for Azure][resiliency].</span></span>

## <a name="pricing"></a><span data-ttu-id="0124d-142">定价</span><span class="sxs-lookup"><span data-stu-id="0124d-142">Pricing</span></span>

<span data-ttu-id="0124d-143">为帮助你了解运行本方案的成本，我们在以下成本计算器示例中预配置了所有服务。</span><span class="sxs-lookup"><span data-stu-id="0124d-143">To help you explore the cost of running this scenario, all of the services are pre-configured in the cost calculator examples below.</span></span> <span data-ttu-id="0124d-144">若要了解自己的特定用例的定价变化情况，请按预期的流量更改相应的变量。</span><span class="sxs-lookup"><span data-stu-id="0124d-144">To see how the pricing would change for your particular use case, change the appropriate variables to match your expected traffic.</span></span>

<span data-ttu-id="0124d-145">我们已根据你预期接收的流量提供了四个示例成本配置文件：</span><span class="sxs-lookup"><span data-stu-id="0124d-145">We have provided four sample cost profiles based on amount of traffic you expect to receive:</span></span>

|<span data-ttu-id="0124d-146">大小</span><span class="sxs-lookup"><span data-stu-id="0124d-146">Size</span></span>|<span data-ttu-id="0124d-147">SAP</span><span class="sxs-lookup"><span data-stu-id="0124d-147">SAPs</span></span>|<span data-ttu-id="0124d-148">VM 类型</span><span class="sxs-lookup"><span data-stu-id="0124d-148">VM Type</span></span>|<span data-ttu-id="0124d-149">存储</span><span class="sxs-lookup"><span data-stu-id="0124d-149">Storage</span></span>|<span data-ttu-id="0124d-150">Azure 定价计算器</span><span class="sxs-lookup"><span data-stu-id="0124d-150">Azure Pricing Calculator</span></span>|
|----|----|-------|-------|---------------|
|<span data-ttu-id="0124d-151">小型</span><span class="sxs-lookup"><span data-stu-id="0124d-151">Small</span></span>|<span data-ttu-id="0124d-152">8000</span><span class="sxs-lookup"><span data-stu-id="0124d-152">8000</span></span>|<span data-ttu-id="0124d-153">D8s_v3</span><span class="sxs-lookup"><span data-stu-id="0124d-153">D8s_v3</span></span>|<span data-ttu-id="0124d-154">2xP20、1xP10</span><span class="sxs-lookup"><span data-stu-id="0124d-154">2xP20, 1xP10</span></span>|[<span data-ttu-id="0124d-155">小型</span><span class="sxs-lookup"><span data-stu-id="0124d-155">Small</span></span>](https://azure.com/e/9d26b9612da9466bb7a800eab56e71d1)|
|<span data-ttu-id="0124d-156">中型</span><span class="sxs-lookup"><span data-stu-id="0124d-156">Medium</span></span>|<span data-ttu-id="0124d-157">16000</span><span class="sxs-lookup"><span data-stu-id="0124d-157">16000</span></span>|<span data-ttu-id="0124d-158">D16s_v3</span><span class="sxs-lookup"><span data-stu-id="0124d-158">D16s_v3</span></span>|<span data-ttu-id="0124d-159">3xP20、1xP10</span><span class="sxs-lookup"><span data-stu-id="0124d-159">3xP20, 1xP10</span></span>|[<span data-ttu-id="0124d-160">中型</span><span class="sxs-lookup"><span data-stu-id="0124d-160">Medium</span></span>](https://azure.com/e/465bd07047d148baab032b2f461550cd)|
<span data-ttu-id="0124d-161">大型</span><span class="sxs-lookup"><span data-stu-id="0124d-161">Large</span></span>|<span data-ttu-id="0124d-162">32000</span><span class="sxs-lookup"><span data-stu-id="0124d-162">32000</span></span>|<span data-ttu-id="0124d-163">E32s_v3</span><span class="sxs-lookup"><span data-stu-id="0124d-163">E32s_v3</span></span>|<span data-ttu-id="0124d-164">3xP20、1xP10</span><span class="sxs-lookup"><span data-stu-id="0124d-164">3xP20, 1xP10</span></span>|[<span data-ttu-id="0124d-165">大型</span><span class="sxs-lookup"><span data-stu-id="0124d-165">Large</span></span>](https://azure.com/e/ada2e849d68b41c3839cc976000c6931)|
<span data-ttu-id="0124d-166">特大型</span><span class="sxs-lookup"><span data-stu-id="0124d-166">Extra Large</span></span>|<span data-ttu-id="0124d-167">64000</span><span class="sxs-lookup"><span data-stu-id="0124d-167">64000</span></span>|<span data-ttu-id="0124d-168">M64s</span><span class="sxs-lookup"><span data-stu-id="0124d-168">M64s</span></span>|<span data-ttu-id="0124d-169">4xP20、1xP10</span><span class="sxs-lookup"><span data-stu-id="0124d-169">4xP20, 1xP10</span></span>|[<span data-ttu-id="0124d-170">特大型</span><span class="sxs-lookup"><span data-stu-id="0124d-170">Extra Large</span></span>](https://azure.com/e/975fb58a965c4fbbb54c5c9179c61cef)|

> [!NOTE]
> <span data-ttu-id="0124d-171">此定价为指导价，仅指出了 VM 和存储费用。</span><span class="sxs-lookup"><span data-stu-id="0124d-171">This pricing is a guide that only indicates the VMs and storage costs.</span></span> <span data-ttu-id="0124d-172">此费用不包括网络、备份存储和数据传入/传出费用。</span><span class="sxs-lookup"><span data-stu-id="0124d-172">It excludes networking, backup storage, and data ingress/egress charges.</span></span>

- <span data-ttu-id="0124d-173">[小型](https://azure.com/e/9d26b9612da9466bb7a800eab56e71d1)：小型系统，其 VM 类型为 D8s_v3，使用 8x vCPU，32 GB RAM 和 200 GB 临时存储，另外还有两个 512 GB 的和一个 128 GB 的高级存储磁盘。</span><span class="sxs-lookup"><span data-stu-id="0124d-173">[Small](https://azure.com/e/9d26b9612da9466bb7a800eab56e71d1): A small system consists of VM type D8s_v3 with 8x vCPUs, 32 GB RAM and 200 GB temp storage, additionally two 512 GB and one 128 GB premium storage disks.</span></span>
- <span data-ttu-id="0124d-174">[中型](https://azure.com/e/465bd07047d148baab032b2f461550cd)：中型系统，其 VM 类型为 D16s_v3，使用 16x vCPU，64 GB RAM 和 400 GB 临时存储，另外还有三个 512 GB 的和一个 128 GB 的高级存储磁盘。</span><span class="sxs-lookup"><span data-stu-id="0124d-174">[Medium](https://azure.com/e/465bd07047d148baab032b2f461550cd): A medium system consists of VM type D16s_v3 with 16x vCPUs, 64 GB RAM and 400 GB temp storage, additionally three 512 GB and one 128 GB premium storage disks.</span></span>
- <span data-ttu-id="0124d-175">[大型](https://azure.com/e/ada2e849d68b41c3839cc976000c6931)：大型系统，其 VM 类型为 E32s_v3，使用 32x vCPU，256 GB RAM 和 512 GB 临时存储，另外还有三个 512 GB 的和一个 128 GB 的高级存储磁盘。</span><span class="sxs-lookup"><span data-stu-id="0124d-175">[Large](https://azure.com/e/ada2e849d68b41c3839cc976000c6931): A large system consists of VM type E32s_v3 with 32x vCPUs, 256 GB RAM and 512 GB temp storage, additionally three 512GB and one 128GB premium storage disks.</span></span>
- <span data-ttu-id="0124d-176">[特大型](https://azure.com/e/975fb58a965c4fbbb54c5c9179c61cef)：特大型系统，其 VM 类型为 M64s，使用 64x vCPU，1024 GB RAM 和 2000 GB 临时存储，另外还有四个 512 GB 的和一个 128 GB 的高级存储磁盘。</span><span class="sxs-lookup"><span data-stu-id="0124d-176">[Extra Large](https://azure.com/e/975fb58a965c4fbbb54c5c9179c61cef): An extra large system consists of a VM type M64s with 64x vCPUs, 1024 GB RAM and 2000 GB temp storage, additionally four 512 GB and one 128 GB premium storage disks.</span></span>

## <a name="deployment"></a><span data-ttu-id="0124d-177">部署</span><span class="sxs-lookup"><span data-stu-id="0124d-177">Deployment</span></span>

<span data-ttu-id="0124d-178">单击此处即可部署本方案的底层基础结构。</span><span class="sxs-lookup"><span data-stu-id="0124d-178">Click here to deploy the underlying infrastructure for this scenario.</span></span>

<!-- markdownlint-disable MD033 -->

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Fsolution-architectures%2Fmaster%2Fapps%2Fsap-2tier%2Fazuredeploy.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

<!-- markdownlint-enable MD033 -->

> [!NOTE]
> <span data-ttu-id="0124d-179">部署过程中不会安装 SAP 和 Oracle。</span><span class="sxs-lookup"><span data-stu-id="0124d-179">SAP and Oracle are not installed during this deployment.</span></span> <span data-ttu-id="0124d-180">需要单独部署这些组件。</span><span class="sxs-lookup"><span data-stu-id="0124d-180">You will need to deploy these components separately.</span></span>

<!-- links -->
[resiliency]: /azure/architecture/resiliency/
[security]: /azure/security/
[scalability]: /azure/architecture/checklist/scalability
[sap-netweaver]: /azure/architecture/reference-architectures/sap/sap-netweaver
[sap-hana]: /azure/architecture/reference-architectures/sap/sap-s4hana
[sap-large]: /azure/architecture/reference-architectures/sap/hana-large-instances
[hub-spoke]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke
