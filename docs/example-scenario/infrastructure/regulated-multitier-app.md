---
title: 使用 Windows VM 构建安全的 Web 应用
titleSuffix: Azure Example Scenarios
description: 使用规模集、应用程序网关和负载均衡器在 Azure 上通过 Windows Server 构建安全的多层 Web 应用程序。
author: iainfoulds
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 2c5f77f265c10388f42138e7d3f6da9e3ead1cd8
ms.sourcegitcommit: bb7fcffbb41e2c26a26f8781df32825eb60df70c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/20/2018
ms.locfileid: "53643527"
---
# <a name="building-secure-web-applications-with-windows-virtual-machines-on-azure"></a>使用 Windows 虚拟机在 Azure 上构建安全的 Web 应用程序

本方案提供的体系结构和设计指南适用于在 Microsoft Azure 上运行安全的多层 Web 应用程序。 在此示例中，ASP.NET 应用程序通过虚拟机以安全方式连接到受保护的后端 Microsoft SQL Server 群集。

传统上，组织需保留旧版本地应用程序和服务来提供安全的基础结构。 如果可以在 Azure 中安全地部署这些 Windows Server 应用程序，组织就可以实现其部署方式的现代化，减少其本地操作成本和管理开销。

## <a name="relevant-use-cases"></a>相关用例

下面是此方案的一些应用示例：

- 在安全的云环境中实现应用程序部署方式的现代化。
- 减少旧版本地应用程序和服务的管理开销。
- 改善患者的医疗保健以及对新应用程序平台的体验。

## <a name="architecture"></a>体系结构

![从体系结构的角度概要说明针对管控行业的多层 Windows Server 应用程序中涉及的 Azure 组件][architecture]

此方案演示一个连接到后端数据库的前端 Web 应用程序，二者都在 Windows Server 2016 上运行。 数据流经方案的情形如下所示：

1. 用户通过 Azure 应用程序网关访问前端 ASP.NET 应用程序。
2. 应用程序网关将流量分发到 Azure 虚拟机规模集中的 VM 实例。
3. 应用程序通过 Azure 负载均衡器连接到后端层中的 Microsoft SQL Server 群集。 这些后端 SQL Server 实例位于单独的 Azure 虚拟网络中，受可以限制流量的网络安全组规则的保护。
4. 该负载均衡器会将 SQL Server 流量分发到另一虚拟机规模集中的 VM 实例。
5. Azure Blob 存储充当后端层中 SQL Server 群集的[云见证][cloud-witness]。 已为 Azure 存储的 VNet 服务终结点启用从 VNet 中进行连接的功能。

### <a name="components"></a>组件

- [Azure 应用程序网关][appgateway-docs]是一个 7 层 Web 流量负载均衡器，可以感知应用程序并根据特定的路由规则分发流量。 应用网关还可以处理 SSL 卸载，以便改进 Web 服务器性能。
- [Azure 虚拟网络][vnet-docs]允许 VM 之类的资源以安全方式彼此通信、与 Internet 通信，以及与本地网络通信。 虚拟网络提供隔离和细分，可以筛选和路由流量，并且允许在不同位置之间进行连接。 本方案中使用了两个虚拟网络和相应的 NSG，这样是为了提供一个[外围安全区域][dmz] (DMZ)，对应用程序组件进行隔离。 虚拟网络对等互连可将两个网络连接到一起。
- 使用 [Azure 虚拟机规模集][scaleset-docs]可以创建并管理一组完全相同的、负载均衡的 VM。 可以根据需求或定义的计划自动增减 VM 实例的数目。 本方案中使用了两个单独的虚拟机规模集 - 一个用于前端 ASP.NET 应用程序实例，一个用于后端 SQL Server 群集 VM 实例。 可以使用 PowerShell Desired State Configuration (DSC) 或 Azure 自定义脚本扩展，通过所需的软件和配置设置来预配 VM 实例。
- [Azure 网络安全组][nsg-docs]包含一个安全规则列表，这些规则可根据源或目标 IP 地址、端口和协议允许或拒绝入站或出站网络流量。 本方案中的虚拟网络受网络安全组规则的保护，这些规则可以限制应用程序组件之间的流量。
- [Azure 负载均衡器][loadbalancer-docs]根据规则和运行状况探测来分发入站流量。 负载均衡器提供低延迟和高吞吐量，以及为所有 TCP 和 UDP 应用程序纵向扩展到数以百万计的流。 本方案使用内部负载均衡器将流量从前端应用程序层分发到后端 SQL Server 群集。
- [Azure Blob 存储][cloudwitness-docs]充当 SQL Server 群集的云见证位置。 此见证用于需要更多投票来决定仲裁的群集操作和决策。 使用云见证，就不再需要让其他 VM 充当传统的文件共享见证。

### <a name="alternatives"></a>备选项

- Linux 和 Windows 可以互换使用，因为基础结构不依赖于操作系统。

- [适用于 Linux 的 SQL Server][sql-linux] 可以替代后端数据存储。

- [Cosmos DB](/azure/cosmos-db/introduction) 是数据存储的另一种替代方案。

## <a name="considerations"></a>注意事项

### <a name="availability"></a>可用性

本方案中的 VM 实例跨[可用性区域](/azure/availability-zones/az-overview)进行部署。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。 每个已启用的地区至少有三个可用性区域。 这样跨区域分发 VM 实例可以为应用程序层提供高可用性。

可以将数据库层配置为使用 Always On 可用性组。 使用此 SQL Server 配置时，最多可以为群集中的一个主数据库配置八个辅助数据库。 如果主数据库发生问题，则群集可以故障转移到其中一个辅助数据库，这样应用程序就会继续可用。 有关详细信息，请参阅[适用于 SQL Server 的 Always On 可用性组概述][sqlalwayson-docs]。

如需更多可用性指南，请参阅 Azure 体系结构中心的[可用性核对清单][availability]。

### <a name="scalability"></a>可伸缩性

本方案将虚拟机规模集用于前端和后端组件。 使用规模集时，运行前端应用程序层的 VM 实例数可以根据客户需求或定义的计划自动进行缩放。 有关详细信息，请参阅[虚拟机规模集的自动缩放概述][vmssautoscale-docs]。

若要了解其他可伸缩性主题，请参阅 Azure 体系结构中心的[可伸缩性核对清单][scalability]。

### <a name="security"></a>安全

所有虚拟网络流量都进入前端应用程序层，受网络安全组的保护。 规则会限制流量的流动，只有前端应用程序层 VM 实例可以访问后端数据库层。 不允许数据库层发出的出站 Internet 流量。 为了减少受攻击面，请勿打开直接远程管理端口。 有关详细信息，请参阅 [Azure 网络安全组][nsg-docs]。

若要查看支付卡行业数据安全标准 (PCI DSS 3.2) 部署指南，请参阅[相容的基础结构][pci-dss]。 若需安全方案的通用设计指南，请参阅 [Azure 安全性文档][security]。

### <a name="resiliency"></a>复原

除了使用可用性区域和虚拟机规模集，本方案还使用 Azure 应用程序网关和负载均衡器。 这两个网络组件可将流量分发到连接的 VM 实例，并且包括运行状况探测，可以确保流量只分发到正常运行的 VM。 两个应用程序网关实例采用主-被配置进行配置，并使用一个区域冗余负载均衡器。 此配置使得网络资源和应用程序可以容忍那些本来会导致流量中断并影响最终用户访问的问题。

若需可复原方案的通用设计指南，请参阅[设计适用于 Azure 的可复原应用程序][resiliency]。

## <a name="deploy-the-scenario"></a>部署方案

### <a name="prerequisites"></a>先决条件

- 必须已经有 Azure 帐户。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 若要将 SQL Server 群集部署到后端规模集中，需要在 Azure Active Directory (AD) 域服务中有一个域。

### <a name="deploy-the-components"></a>部署组件

若要通过 Azure 资源管理器模板部署此方案的核心基础结构，请执行以下步骤。

<!-- markdownlint-disable MD033 -->

1. 选择“部署到 Azure”按钮：<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Fsolution-architectures%2Fmaster%2Finfrastructure%2Fregulated-multitier-app%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>
2. 等待模板部署在 Azure 门户中打开，然后完成以下步骤：
   - 选择“新建”资源组，然后在文本框中提供一个名称，例如 *myWindowsscenario*。
   - 从“位置”下拉框中选择区域。
   - 提供用于虚拟机规模集实例的用户名和安全密码。
   - 查看条款和条件，然后勾选“我同意上述条款和条件”。
   - 选择“购买”按钮。

<!-- markdownlint-enable MD033 -->

部署可能需要 15-20 分钟才能完成。

## <a name="pricing"></a>定价

为了方便用户了解运行本方案的成本，我们已在成本计算器中预配置了所有服务。 若要了解自己的特定用例的定价变化情况，请按预期的流量更改相应的变量。

我们已根据运行应用程序的规模集 VM 实例数提供了三个示例性的成本配置文件。

- [小型][small-pricing]：此定价示例对应于两个前端和两个后端 VM 实例。
- [中型][medium-pricing]：此定价示例对应于 20 个前端和 5 个后端 VM 实例。
- [大型][large-pricing]：此定价示例对应于 100 个前端和 10 个后端 VM 实例。

## <a name="related-resources"></a>相关资源

本方案使用的后端虚拟机规模集运行 Microsoft SQL Server 群集。 也可将 Cosmos DB 用作一个可缩放的、安全的、适用于应用程序数据的数据库层。 使用 [Azure 虚拟网络服务终结点][vnetendpoint-docs]可以保护关键的 Azure 服务资源，只允许你在自己的虚拟网络中对其进行访问。 在本方案中，可以通过 VNet 终结点来确保前端应用程序层和 Cosmos DB 之间流量的安全。 有关详细信息，请参阅 [Azure Cosmos DB 概述](/azure/cosmos-db/introduction)。

如需更详细的实施指南，请参阅[使用 SQL Server 的 N 层应用程序的参考体系结构][ntiersql-ra]。

<!-- links -->
[appgateway-docs]: /azure/application-gateway/overview
[architecture]: ./media/architecture-regulated-multitier-app.png
[autoscaling]: /azure/architecture/best-practices/auto-scaling
[availability]: ../../checklist/availability.md
[cloudwitness-docs]: /windows-server/failover-clustering/deploy-cloud-witness
[loadbalancer-docs]: /azure/load-balancer/load-balancer-overview
[nsg-docs]: /azure/virtual-network/security-overview
[ntiersql-ra]: /azure/architecture/reference-architectures/n-tier/n-tier-sql-server
[resiliency]: /azure/architecture/resiliency/
[security]: /azure/security/
[scalability]: /azure/architecture/checklist/scalability
[scaleset-docs]: /azure/virtual-machine-scale-sets/overview
[sqlalwayson-docs]: /sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server
[vmssautoscale-docs]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview
[vnet-docs]: /azure/virtual-network/virtual-networks-overview
[vnetendpoint-docs]: /azure/virtual-network/virtual-network-service-endpoints-overview
[pci-dss]: /azure/security/blueprints/pcidss-iaaswa-overview
[dmz]: /azure/virtual-network/virtual-networks-dmz-nsg
[sql-linux]: /sql/linux/sql-server-linux-overview?view=sql-server-linux-2017
[cloud-witness]: /windows-server/failover-clustering/deploy-cloud-witness
[small-pricing]: https://azure.com/e/711bbfcbbc884ef8aa91cdf0f2caff72
[medium-pricing]: https://azure.com/e/b622d82d79b34b8398c4bce35477856f
[large-pricing]: https://azure.com/e/1d99d8b92f90496787abecffa1473a93
