---
title: 针对 HA/DR 生成的多层 Web 应用程序
titleSuffix: Azure Example Scenarios
description: 使用 Azure 虚拟机、可用性集、可用性区域、Azure Site Recovery 和 Azure 流量管理器在 Azure 上创建可实现高可用性和灾难恢复的多层 Web 应用程序。
author: sujayt
ms.date: 11/16/2018
ms.custom: product-team
ms.openlocfilehash: d2b466cc691c02ea3c5fb6ff72d91865d33c3378
ms.sourcegitcommit: 1f4cdb08fe73b1956e164ad692f792f9f635b409
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2019
ms.locfileid: "54114090"
---
# <a name="multitier-web-application-built-for-high-availability-and-disaster-recovery-on-azure"></a>Azure 上可实现高可用性和灾难恢复的多层 Web 应用程序

本示例方案适用于需要部署可实现高可用性和灾难恢复的可复原多层应用程序的任何行业。 在本方案中，应用程序由三个层组成。

- Web 层：包含用户界面的最上层。 此层分析用户交互，并将操作传递到下一层进行处理。
- 业务层：处理用户交互，并针对后续步骤做出逻辑决策。 此层连接 Web 层和数据层。
- 数据层：存储应用程序数据。 通常使用数据库、对象存储或文件存储。

常见应用程序方案包括 Windows 或 Linux 上运行的任何任务关键型应用程序。 这可能是现成的应用程序（例如 SAP 和 SharePoint），也可能是自定义的业务线应用程序。

## <a name="relevant-use-cases"></a>相关用例

其他相关用例包括：

- 部署 SAP 和 SharePoint 等高度可复原的应用程序
- 为业务线应用程序设计业务连续性和灾难恢复计划
- 配置灾难恢复并出于合规目的执行相关的演练

## <a name="architecture"></a>体系结构

本方案演示一个使用 ASP.NET 和 Microsoft SQL Server 的多层应用程序。 在[支持可用性区域的 Azure 区域](/azure/availability-zones/az-overview#regions-that-support-availability-zones)中，可以在跨可用性区域的源区域中部署虚拟机 (VM)，并将 VM 复制到用于灾难恢复的目标区域。 在不支持可用性区域的 Azure 区域中，可以在可用性集内部署 VM，并将 VM 复制到目标区域。

![高度可复原的多层 Web 应用程序的体系结构概览][architecture]

- 在支持局部区域的区域中，将每个层中的 VM 分布在两个可用性区域之间。 在其他区域，将每个层中的 VM 部署到一个可用性集内。
- 可以将数据库层配置为使用 Always On 可用性组。 使用此 SQL Server 配置时，最多可以为群集中的一个主数据库配置八个辅助数据库。 如果主数据库发生问题，群集可以故障转移到某个辅助数据库，使应用程序保持可用。 有关详细信息，请参阅[适用于 SQL Server 的 Always On 可用性组概述][docs-sql-always-on]。
- 对于灾难恢复方案，可以配置 SQL Always On 异步本机复制，以复制到用于灾难恢复的目标区域。 还可以配置 Azure Site Recovery 复制，以便在数据更改率处于 Azure Site recovery 支持的限制范围内时复制到目标区域。
- 用户通过流量管理器终结点访问前端 ASP.NET Web 层。
- 流量管理器将流量重定向到主要源区域中的主要公共 IP 终结点。
- 公共 IP 通过公共负载均衡器将调用重定向到某个 Web 层 VM 实例。 所有 Web 层 VM 实例位于一个子网中。
- 在 Web 层 VM 中，每个调用将通过内部负载均衡器路由到业务层中的某个 VM 实例进行处理。 所有业务层 VM 位于独立的子网中。
- 操作将在业务层中进行处理，ASP.NET 应用程序通过 Azure 内部负载均衡器连接到后端层中的 Microsoft SQL Server 群集。 这些后端 SQL Server 实例位于独立的子网中。
- 流量管理器的辅助终结点配置为用于灾难恢复的目标区域中的公共 IP。
- 如果主要区域发生中断，则你可以调用 Azure Site Recovery 故障转移，应用程序将在目标区域中变为活动状态。
- 流量管理器终结点自动将客户端流量重定向到目标区域中的公共 IP。

### <a name="components"></a>组件

- [可用性集][docs-availability-sets]确保在 Azure 上部署的 VM 能够跨群集中多个隔离的硬件节点分布。 如果 Azure 中发生硬件或软件故障，只有一部分 VM 会受到影响，而整个解决方案将保持可用且正常运行。
- 数据中心发生故障时，[可用性区域][docs-availability-zones]可以保护应用程序和数据。 可用性区域是 Azure 区域中独立的物理位置。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源以及散热和网络设备。
- 使用 [Azure Site Recovery (ASR)][docs-azure-site-recovery] 可将 VM 复制到另一个 Azure 区域以满足业务连续性和灾难恢复需求。 可以定期执行灾难恢复演练来确保满足合规需求。 源区域中发生故障时，VM 将按照指定的设置复制到所选区域，以便恢复应用程序。
- [Azure 流量管理器][docs-traffic-manager]是基于 DNS 的流量负载均衡器，可在全球 Azure 区域之间以最佳方式向服务分配流量，同时提供高可用性和响应能力。
- [Azure 负载均衡器][docs-load-balancer]根据定义的规则和运行状况探测分配入站流量。 负载均衡器提供低延迟和高吞吐量，可纵向扩展到数百万个流，以满足所有 TCP 和 UDP 应用程序的需求。 本方案使用公共负载均衡器将传入的客户端流量分配到 Web 层。 本方案使用内部负载均衡器将来自业务层的流量分配到后端 SQL Server 群集。

### <a name="alternatives"></a>备选项

- 可以使用其他操作系统来替换 Windows，因为基础结构中没有任何组件依赖于操作系统。
- [适用于 Linux 的 SQL Server][docs-sql-server-linux] 可以替代后端数据存储。
- 可以使用任何可用的标准数据库应用程序替换数据库。

## <a name="other-considerations"></a>其他注意事项

### <a name="scalability"></a>可伸缩性

可以根据缩放要求，在每个层中添加或删除 VM。 由于本方案使用负载均衡器，因此你可以在某个层中添加更多的 VM，而不会影响应用程序的运行时间。

若要了解其他可伸缩性主题，请参阅 Azure 体系结构中心的[可伸缩性核对清单][scalability]。

### <a name="security"></a>安全

进入前端应用层的所有虚拟网络流量受网络安全组的保护。 规则会限制流量的流动，只有前端应用程序层 VM 实例可以访问后端数据库层。 不允许业务层或数据库层发出的出站 Internet 流量。 为了减少受攻击面，请勿打开直接远程管理端口。 有关详细信息，请参阅 [Azure 网络安全组][docs-nsg]。

若需安全方案的通用设计指南，请参阅 [Azure 安全性文档][security]。

## <a name="pricing"></a>定价

使用 Azure Site Recovery 为 Azure VM 配置灾难恢复会持续产生以下费用。

- 每个 VM 的 Azure Site Recovery 许可费用。
- 将数据更改从源 VM 磁盘复制到另一个 Azure 区域时产生的网络出口费用。 Azure Site Recovery 使用内置压缩，可将数据传输要求减少约 50%。
- 恢复站点上的存储费用。 此存储通常相当于源区域存储加上将恢复点保留为恢复快照所需的任何附加存储。

我们提供了一个[示例成本计算器][calculator]，用于计算使用六个虚拟机为某个三层应用程序配置灾难恢复所需的费用。 成本计算器中已预配置所有服务。 若要了解特定用例的定价变化情况，请更改相应的变量来估算费用。

<!-- links -->
[architecture]: ./media/arhitecture-disaster-recovery-multi-tier-app.png
[autoscaling]: /azure/architecture/best-practices/auto-scaling
[availability]: ../../checklist/availability.md
[resiliency]: /azure/architecture/resiliency/
[security]: /azure/security/
[scalability]: /azure/architecture/checklist/scalability
[docs-availability-zones]: /azure/availability-zones/az-overview
[docs-load-balancer]: /azure/load-balancer/load-balancer-overview
[docs-nsg]: /azure/virtual-network/security-overview
[docs-vmss]: /azure/virtual-machine-scale-sets/overview
[docs-sql-always-on]: /sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server
[docs-vmss-autoscale]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview
[docs-vnet]: /azure/virtual-network/virtual-networks-overview
[docs-sql-server-linux]: /sql/linux/sql-server-linux-overview?view=sql-server-linux-2017
[docs-traffic-manager]: /azure/traffic-manager/
[docs-azure-site-recovery]: /azure/site-recovery/azure-to-azure-quickstart/
[docs-availability-sets]: /azure/virtual-machines/windows/manage-availability/
[calculator]: https://azure.com/e/6835332265044d6d931d68c917979e6d/
