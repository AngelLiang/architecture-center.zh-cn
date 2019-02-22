---
title: 将 Citrix 用于 Linux 虚拟桌面
titleSuffix: Azure Example Scenarios
description: 在 Azure 上使用 Citrix 为 Linux 桌面构建 VDI 环境。
author: miguelangelopereira
ms.date: 09/12/2018
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.custom: fasttrack, Linux
social_image_url: /azure/architecture/example-scenario/infrastructure/media/azure-citrix-sample-diagram.png
ms.openlocfilehash: 77093cba0e5646146ec9de68655754d9ac1e5461
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55897705"
---
# <a name="linux-virtual-desktops-with-citrix"></a>将 Citrix 用于 Linux 虚拟桌面

本示例方案适用于任何需要用于 Linux 桌面的虚拟桌面基础结构 (VDI) 的行业。 VDI 是指在数据中心服务器上的虚拟机中运行用户桌面的过程。 本方案中的客户选择使用基于 Citrix 的解决方案来满足其 VDI 需求。

组织通常采用异构环境，其中包含员工使用的多个设备和操作系统。 要在维护安全环境的同时提供一致的应用程序访问方式，可能存在一定的难度。 借助用于 Linux 桌面的 VDI 解决方案，不管最终用户使用哪种设备或 OS，组织都能提供访问。

本方案的部分优势包括：

- 让更多的用户访问同一个基础结构，通过共享的 Linux 虚拟桌面来提高投资回报。 在集中式 VDI 环境中整合资源，不需要配置强大的最终用户设备。
- 不管最终用户设备的配置如何，都能保持一致的性能。
- 用户可从任何设备（包括非 Linux 设备）访问 Linux 应用程序。
- 可在 Azure 数据中心保护各地员工的敏感数据。

## <a name="relevant-use-cases"></a>相关用例

以下用例可以考虑本方案：

- 从 Linux 或非 Linux 设备安全访问任务关键型的专用 Linux VDI 桌面

## <a name="architecture"></a>体系结构

[![](./media/azure-citrix-sample-diagram.png "体系结构示意图")](./media/azure-citrix-sample-diagram.png#lightbox)

本示例方案演示如何从企业网络访问 Linux 虚拟桌面：

- 在本地环境与 Azure 之间建立 ExpressRoute，以便快速可靠地连接到云。
- 为 VDI 部署 Citrix XenDeskop 解决方案。
- CitrixVDA 在 Ubuntu（或其他支持的分发版）上运行。
- Azure 网络安全组应用正确的网络 ACL。
- Citrix ADC (NetScaler) 发布所有 Citrix 服务并对其进行负载均衡。
- Active Directory 域服务用于将 Citrix 服务器加入域。 VDA 服务器不会加入域。
- Azure 混合文件同步在整个解决方案中启用共享存储。 例如，可以在远程/家庭解决方案中使用它。

本方案使用以下 SKU：

- Citrix ADC (NetScaler)：2 个包含 [NetScaler 12.0 VPX Standard Edition 200 MBPS PAYG 映像](https://azuremarketplace.microsoft.com/pt-br/marketplace/apps/citrix.netscalervpx-120?tab=PlansAndPrice)的 D4sv3
- Citrix 许可证服务器：1 个 D2s v3
- Citrix VDA：4 个 D8s v3
- Citrix Storefront：2 个 D2s v3
- Citrix 传送控制器：2 个 D2s v3
- 域控制器：2 个 D2sv3
- Azure 文件服务器：2 个 D2sv3

> [!NOTE]
> 所有许可证（NetScaler 除外）都是自带许可证 (BYOL)

### <a name="components"></a>组件

- [Azure 虚拟网络](/azure/virtual-network/virtual-networks-overview)允许 VM 之类的资源以安全方式彼此通信、与 Internet 通信，以及与本地网络通信。 虚拟网络提供隔离和细分，可以筛选和路由流量，并且允许在不同位置之间进行连接。 在本方案中，所有资源将使用一个虚拟网络。
- [Azure 网络安全组](/azure/virtual-network/security-overview)包含一个安全规则列表，这些规则可根据源或目标 IP 地址、端口和协议允许或拒绝入站或出站网络流量。 本方案中的虚拟网络受网络安全组规则的保护，这些规则可以限制应用程序组件之间的流量。
- [Azure 负载均衡器](/azure/application-gateway/overview)根据规则和运行状况探测来分配入站流量。 负载均衡器提供低延迟和高吞吐量，以及为所有 TCP 和 UDP 应用程序纵向扩展到数以百万计的流。 本方案使用内部负载均衡器在 Citrix NetScaler 上分配流量。
- [Azure 混合文件同步](https://github.com/MicrosoftDocs/azure-docs/edit/master/articles/storage/files/storage-sync-files-planning.md)用于所有共享存储。 存储将使用混合文件同步复制到两个文件服务器。
- [Azure SQL 数据库](/azure/sql-database/sql-database-technical-overview)是基于最新稳定版 Microsoft SQL Server 数据库引擎的关系数据库即服务 (DBaaS)。 它用于托管 Citrix 数据库。
- 使用 [ExpressRoute](/azure/expressroute/expressroute-introduction) 可通过连接服务提供商所提供的专用连接，将本地网络扩展到 Microsoft 云。
- [Active Directory 域服务用于目录服务和用户身份验证
- [Azure 可用性集](/azure/virtual-machines/windows/tutorial-availability-sets)确保在 Azure 上部署的 VM 能够跨群集中多个隔离的硬件节点分布。 这样，就可以确保当 Azure 中发生硬件或软件故障时，只有一部分 VM 会受到影响，整体解决方案仍可使用和操作。
- [Citrix ADC (NetScaler)](https://www.citrix.com/products/citrix-adc) 是应用程序传送控制器，执行特定于应用程序的流量分析，以智能分配、优化和保护 Web 应用程序的第 4 层到第 7 层 (L4-L7) 网络流量。
- [Citrix Storefront](https://www.citrix.com/products/citrix-virtual-apps-and-desktops/citrix-storefront.html) 是企业应用存储，可以改善安全性并简化部署，针对任何平台上的 Citrix 接收器提供无可比拟的、接近本机速度的新式用户体验。 使用 StoreFront 能够轻松管理多站点和多版本 Citrix 虚拟应用与桌面环境。
- [Citrix 许可证服务器](https://www.citrix.com/buy/licensing/overview.html)管理 Citrix 产品的许可证。
- [Citrix XenDesktops VDA](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service) 用于与应用程序和桌面建立连接。 VDA 安装在运行用户应用程序或虚拟桌面的计算机上。 VDA 使计算机能够注册到传送控制器，并可以管理用户设备的高清用户体验 (HDX) 连接。
- [Citrix 传送控制器](https://docs.citrix.com/en-us/xenapp-and-xendesktop/7-15-ltsr/manage-deployment/delivery-controllers)是负责管理用户访问以及中转和优化连接的服务器端组件。 控制器还提供用于创建桌面和服务器映像的计算机创建服务。

### <a name="alternatives"></a>备选项

- Azure 支持 VDI 解决方案的多个合作伙伴，例如 VMware、Workspot，等等。 本特定示例体系结构基于使用 Citrix 的已部署项目。
- Citrix 提供的某个云服务可以抽象化此体系结构的一部分。 此服务可能是本解决方案的替代方案。 有关详细信息，请参阅 [Citrix Cloud](https://www.citrix.com/products/citrix-cloud)。

## <a name="considerations"></a>注意事项

- 检查 [Citrix Linux 要求](https://docs.citrix.com/en-us/linux-virtual-delivery-agent/current-release/system-requirements)。
- 延迟可能会对整体解决方案产生影响。 对于生产环境，请相应地进行测试。
- 根据具体的方案，本解决方案可能需要配备用于 VDA 的 GPU 的 VM。 本解决方案假设不要求使用 GPU。

### <a name="availability-scalability-and-security"></a>可用性、可伸缩性和安全性

- 本示例是为了实现所有角色（许可服务器除外）的高可用性而设计的。 由于在许可证服务器脱机的情况下，环境将在 30 天宽限期内继续运行，因此，不需要在该服务器上配置额外的冗余。
- 提供类似角色的所有服务器应部署在[可用性集](/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)中。
- 本示例方案不包括灾难恢复功能。 [Azure Site Recovery](/azure/site-recovery/site-recovery-overview) 可能是这种设计的适当加载项。
- 考虑跨[可用性区域](/azure/availability-zones/az-overview)在此方案中部署 VM 实例。 每个可用性区域都由一个或多个数据中心组成，这些数据中心都配置了独立电源、冷却和网络。 每个已启用的地区至少有三个可用性区域。 这样跨区域分发 VM 实例可以为应用程序层提供高可用性。 有关详细信息，请参阅 [Azure 中的可用性区域是什么？](/azure/availability-zones/az-overview)。 也可以[在 Azure 可用性区域中部署 VPN 网关和 ExpressRoute 网关](/azure/vpn-gateway/about-zone-redundant-vnet-gateways)。
- 对于生产部署，应实施[备份](/azure/backup/backup-introduction-to-azure-backup)、[监视](/azure/monitoring-and-diagnostics/monitoring-overview)和[更新管理](/azure/automation/automation-update-management)等管理解决方案。
- 本示例应适用于采用混合技术的大约 250 个并发用户（每台 VDA 服务器大约有 50-60 个用户）。 但是，具体的用户数在很大程度上取决于所用应用程序的类型。 对于生产用途，应执行严格的负载测试。

## <a name="deployment"></a>部署

有关部署信息，请参阅官方 [Citrix 文档](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/install-configure.html)。

## <a name="pricing"></a>定价

- Azure 服务费用不包括 Citrix XenDesktop 许可证。
- Citrix NetScaler 许可费用包含在即用即付模型中。
- 使用预留实例可大大降低解决方案的计算费用。
- 不包括 ExpressRoute 费用。

## <a name="next-steps"></a>后续步骤

- 在[此处](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops/install-configure)查看有关规划和部署的 Citrix 文档。
- 若要在 Azure 中部署 Citrix ADC (NetScaler)，请在[此处](https://github.com/citrix/netscaler-azure-templates)查看 Citrix 提供的资源管理器模板。
