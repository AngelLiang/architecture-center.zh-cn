---
title: 使用 VPN 将本地网络连接到 Azure
titleSuffix: Azure Reference Architectures
description: 实现这样一个安全的站点到站点网络体系结构：跨 Azure 虚拟网络，以及使用 VPN 建立连接的本地网络。
author: RohitSharma-pnp
ms.date: 10/22/2018
ms.openlocfilehash: 63e919041e4a8c7c9f90a49de05effc48cb743d8
ms.sourcegitcommit: 1f4cdb08fe73b1956e164ad692f792f9f635b409
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2019
ms.locfileid: "54110904"
---
# <a name="connect-an-on-premises-network-to-azure-using-a-vpn-gateway"></a>使用 VPN 网关将本地网络连接到 Azure

此参考体系结构演示如何使用站点到站点虚拟专用网络 (VPN) 将本地网络扩展到 Azure。 本地网络与 Azure 虚拟专用网络 (VNet) 之间的流量通过 IPSec VPN 隧道传送。 [**部署此解决方案**](#deploy-the-solution)。

![跨本地和 Azure 基础结构的混合网络](./images/vpn.png)

下载此体系结构的 [Visio 文件][visio-download]。

## <a name="architecture"></a>体系结构

该体系结构包括以下组件。

- **本地网络**。 组织中运行的专用局域网。

- **VPN 设备**。 用于与本地网络建立外部连接的设备或服务。 该 VPN 设备可以是硬件设备，也可以是软件解决方案，例如 Windows Server 2012 中的路由和远程访问服务 (RRAS)。 有关支持的 VPN 设备的列表以及有关如何配置它们以连接到 Azure VPN 网关的信息，请参阅[关于站点到站点 VPN 网关连接的 VPN 设备][vpn-appliance]一文中针对所选设备的说明。

- **虚拟网络 (VNet)**。 云应用程序和 Azure VPN 网关的组件驻留在相同 [VNet][azure-virtual-network] 中。

- **Azure VPN 网关**。 [VPN 网关][azure-vpn-gateway]服务使你可以通过 VPN 设备将 VNet 连接到本地网络。 有关详细信息，请参阅[将本地网络连接到 Microsoft Azure 虚拟网络][connect-to-an-Azure-vnet]。 VPN 网关包括以下元素：

  - **虚拟网络网关**。 为 VNet 提供虚拟 VPN 设备的资源。 它负责将流量从本地网络路由到 VNet。
  - **本地网络网关**。 本地 VPN 设备的抽象。 从云应用程序到本地网络的网络流量通过此网关进行路由。
  - **连接**。 该连接包含一些属性，这些属性指定连接类型 (IPSec)，以及与本地 VPN 设备共享的、用于加密流量的密钥。
  - **网关子网**。 虚拟网络网关保留在自己的子网中，该子网需满足下面“建议”一节中所述的要求。

- **云应用程序**。 Azure 中托管的应用程序。 它可以包含多个层，以及通过 Azure 负载均衡器连接的多个子网。 有关应用程序基础结构的详细信息，请参阅[运行 Windows VM 工作负荷][windows-vm-ra]和[运行 Linux VM 工作负荷][linux-vm-ra]。

- **内部负载均衡器**。 来自 VPN 网关的网络流量通过内部负载均衡器路由到云应用程序。 该负载均衡器位于应用程序的前端子网中。

## <a name="recommendations"></a>建议

以下建议适用于大多数方案。 除非有优先于这些建议的特定要求，否则请遵循这些建议。

### <a name="vnet-and-gateway-subnet"></a>VNet 和网关子网

创建地址空间的大小足以容纳所有所需资源的 Azure VNet。 确保 VNet 地址空间具有足够空间，以便在将来可能需要更多 VM 时增长。 VNet 的地址空间不得与本地网络重叠。 例如，上图将地址空间 10.20.0.0/16 用于 VNet。

创建一个名为 *GatewaySubnet* 的子网，使其地址范围为 /27。 此子网是虚拟网络网关所必需的。 向此子网分配 32 个地址将有助于防止将来达到网关大小限制。 此外，避免将此子网置于地址空间中间。 一个好的做法是将网关子网的地址空间设置在 VNet 地址空间上端。 图中所示的示例使用 10.20.255.224/27。  下面是用于计算 [CIDR] 的快速过程：

1. 将 VNet 地址空间中的变量位设置为 1（直到网关子网所使用的位），然后将剩余位设置为 0。
2. 将得到的位转换为十进制，然后将它表示为前缀长度设置为网关子网大小的地址空间。

例如，对于 IP 地址范围为 10.20.0.0/16 的 VNet，应用上面的步骤 1 会成为 10.20.0b11111111.0b11100000。  将该数字转换为十进制并将它表示为地址空间会生成 10.20.255.224/27。

> [!WARNING]
> 请勿向网关子网部署任何 VM。 此外，请勿向此子网分配 NSG，因为它会导致网关停止工作。
>

### <a name="virtual-network-gateway"></a>虚拟网络网关

为虚拟网络网关分配公共 IP 地址。

在网关子网中创建虚拟网络网关，并将新分配的公共 IP 地址分配给它。 使用最符合你要求并且通过 VPN 设备启用的网关类型：

- 如果需要基于策略条件（如地址前缀）密切控制如何对请求进行路由，请创建[基于策略的网关][policy-based-routing]。 基于策略的网关使用静态路由，仅适用于站点到站点连接。

- 如果使用 RRAS 连接到本地网络、支持多站点或跨区域连接或是实现 VNet 到 VNet 连接（包含遍历多个 VNet 的路由），请创建[基于路由的网关][route-based-routing]。 基于路由的网关使用动态路由定向网络之间的流量。 它们在网络路径中的容错能力好于静态路由，因为它们可以尝试备用路由。 基于路由的网关还可以减少管理开销，因为路由在网络地址更改时可以无需手动更新。

有关支持的 VPN 设备的列表，请参阅[关于站点到站点 VPN 网关连接的 VPN 设备][vpn-appliances]。

> [!NOTE]
> 创建网关后，必须先删除并重新创建网关才能更改网关类型。
>

选择最符合你吞吐量要求的 Azure VPN 网关 SKU。 有关详细信息，请参阅[网关 SKU][azure-gateway-skus]

> [!NOTE]
> 基本 SKU 不与 Azure ExpressRoute 兼容。 可以在创建网关之后[更改 SKU][changing-SKUs]。
>

会基于预配和提供网关的时间量进行收费。 请参阅 [VPN 网关定价][azure-gateway-charges]。

为网关子网创建将传入应用程序流量从网关定向到内部负载均衡器，而不是允许请求直接传递到应用程序 VM 的路由规则。

### <a name="on-premises-network-connection"></a>本地网络连接

创建本地网络网关。 指定本地 VPN 设备的公共 IP 地址以及本地网络的地址空间。 请注意，本地 VPN 设备必须具有可由 Azure VPN 网关中的本地网络网关访问的公共 IP 地址。 VPN 设备不能位于网络地址转换 (NAT) 设备后面。

为虚拟网络网关和本地网络网关创建站点到站点连接。 选择站点到站点 (IPSec) 连接类型，并指定共享密钥。 具有 Azure VPN 网关的站点到站点加密基于 IPSec 协议，使用预共享密钥进行身份验证。 创建 Azure VPN 网关时需指定密钥。 必须使用相同密钥配置在本地运行的 VPN 设备。 当前不支持其他身份验证机制。

确保本地路由基础结构配置为将目标为 Azure VNet 中的地址的请求转发到 VPN 设备。

在本地网络中打开云应用程序所需的任何端口。

测试连接以验证以下事项：

- 本地 VPN 设备通过 Azure VPN 网关将流量正确路由到云应用程序。
- VNet 将流量正确路由回本地网络。
- 正确阻止两个方向上的禁止流量。

## <a name="scalability-considerations"></a>可伸缩性注意事项

可以通过从基本或标准 VPN 网关 SKU 升级到高性能 VPN SKU，来实现有限纵向可缩放性。

对于预期存在大量 VPN 流量的 VNet，请考虑将不同工作负载分发到单独的较小 VNet 以及为每个 VNet 都配置 VPN 网关。

可以按水平或垂直方式对 VNet 进行分区。 若要进行水平分区，请将某些 VM 实例从每个层移动到新 VNet 的子网中。 结果是每个 VNet 都具有相同结构和功能。 若要进行垂直分区，请重新设计每个层，以将功能划分为不同逻辑区域（如处理订单、开发票、客户帐户管理等）。 每个功能区域随后可以放置在自己的 VNet 中。

在 VNet 中复制本地 Active Directory 域控制器以及在 VNet 中实现 DNS 可以帮助减少从本地到云的某些安全相关和管理流量。 有关详细信息，请参阅[将 Active Directory 域服务 (AD DS) 扩展到 Azure][adds-extend-domain]。

## <a name="availability-considerations"></a>可用性注意事项

如果需要确保本地网络对 Azure VPN 网关保持可用，请为本地 VPN 网关实现故障转移群集。

如果组织具有多个本地站点，请创建与一个或多个 Azure VNet 之间的[多站点连接][vpn-gateway-multi-site]。 此方法需要动态（基于路由的）路由，因此请确保本地 VPN 网关支持此功能。

有关服务级别协议的详细信息，请参阅 [VPN 网关的 SLA][sla-for-vpn-gateway]。

## <a name="manageability-considerations"></a>可管理性注意事项

监视来自本地 VPN 设备的诊断信息。 此过程取决于 VPN 设备提供的功能。 例如，如果在 Windows Server 2012 上使用路由和远程访问服务，则可使用 [RRAS 日志记录][rras-logging]。

使用 [Azure VPN 网关诊断][gateway-diagnostic-logs]可捕获有关连接问题的信息。 这些日志可以用于跟踪信息，如连接请求的源和目标、使用的协议以及连接的建立方式（或尝试失败的原因）。

使用 Azure 门户中提供的审核日志监视 Azure VPN 网关的运行日志。 为本地网络网关、Azure 网络网关和连接分别提供了单独的日志。 此信息可以用于跟踪对网关进行的任何更改，并且在以前正常运行的网关由于某种原因而停止工作时可能会十分有用。

![Azure 门户中的审核日志](../_images/guidance-hybrid-network-vpn/audit-logs.png)

监视连接，并跟踪连接失败事件。 可以使用监视包（如 [Nagios][nagios]）捕获并报告此信息。

## <a name="security-considerations"></a>安全注意事项

为每个 VPN 网关生成不同的共享密钥。 使用强共享密钥可帮助抵御暴力攻击。

> [!NOTE]
> 当前无法使用 Azure Key Vault 为 Azure VPN 网关预共享密钥。
>

确保本地 VPN 设备使用的加密方法[与 Azure VPN 网关兼容][vpn-appliance-ipsec]。 对于基于策略的路由，Azure VPN 网关支持 AES256、AES128 和 3DES 加密算法。 基于路由的网关支持 AES256 和 3DES。

如果本地 VPN 设备位于在外围网络 (DMZ) 与 Internet 之间具有防火墙的外围网络中，则可能必须配置[其他防火墙规则][additional-firewall-rules]以允许实现站点到站点 VPN 连接。

如果 VNet 中的应用程序将数据发送到 Internet，请考虑[实现强制隧道][forced-tunneling]以通过本地网络路由所有 Internet 绑定流量。 此方法使你可以审核应用程序从本地基础结构进行的传出请求。

> [!NOTE]
> 强制隧道可能会影响与 Azure 服务（例如存储服务）和 Windows 许可证管理器之间的连接。
>

## <a name="deploy-the-solution"></a>部署解决方案

**先决条件**。 必须提供一个已配置适当网络设备的现有本地基础结构。

若要部署该解决方案，请执行以下步骤。

<!-- markdownlint-disable MD033 -->

1. 单击下面的按钮：<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fhybrid-networking%2Fvpn%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>
2. 等待该链接在 Azure 门户中打开，然后执行以下步骤：
   - 参数文件中已定义**资源组**名称，因此请选择“新建”，并在文本框中输入 `ra-hybrid-vpn-rg`。
   - 从“位置”下拉框中选择区域。
   - 不要编辑“模板根 URI”或“参数根 URI”文本框。
   - 查看条款和条件，并单击“我同意上述条款和条件”复选框。
   - 单击“购买”按钮。
3. 等待部署完成。

<!-- markdownlint-enable MD033 -->

若要排查连接问题，请参阅[排查混合 VPN 连接问题](./troubleshoot-vpn.md)。

<!-- links -->

[adds-extend-domain]: ../identity/adds-extend-domain.md
[windows-vm-ra]: ../virtual-machines-windows/index.md
[linux-vm-ra]: ../virtual-machines-linux/index.md

[azure-cli]: /azure/virtual-machines-command-line-tools
[azure-virtual-network]: /azure/virtual-network/virtual-networks-overview
[vpn-appliance]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices
[azure-vpn-gateway]: https://azure.microsoft.com/services/vpn-gateway/
[azure-gateway-charges]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[azure-gateway-skus]: /azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[vpn-gateway-multi-site]: /azure/vpn-gateway/vpn-gateway-multi-site
[policy-based-routing]: https://en.wikipedia.org/wiki/Policy-based_routing
[route-based-routing]: https://en.wikipedia.org/wiki/Static_routing
[sla-for-vpn-gateway]: https://azure.microsoft.com/support/legal/sla/vpn-gateway/
[additional-firewall-rules]: https://technet.microsoft.com/library/dn786406.aspx#firewall
[nagios]: https://www.nagios.org/
[changing-SKUs]: https://azure.microsoft.com/blog/azure-virtual-network-gateway-improvements/
[gateway-diagnostic-logs]: https://blogs.technet.microsoft.com/keithmayer/2016/10/12/step-by-step-capturing-azure-resource-manager-arm-vnet-gateway-diagnostic-logs/
[rras-logging]: https://www.petri.com/enable-diagnostic-logging-in-windows-server-2012-r2-routing-and-remote-access
[forced-tunneling]: /azure/vpn-gateway/vpn-gateway-about-forced-tunneling
[vpn-appliances]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices
[visio-download]: https://archcenter.blob.core.windows.net/cdn/hybrid-network-architectures.vsdx
[vpn-appliance-ipsec]: /azure/vpn-gateway/vpn-gateway-about-vpn-devices#ipsec-parameters
[azure-cli]: /cli/azure/install-azure-cli
[CIDR]: https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing
