---
title: 使用 Apache Cassandra 的 N 层应用程序
titleSuffix: Azure Reference Architectures
description: 在 Microsoft Azure 中使用 Apache Cassandra 运行用于 N 层体系结构的 Linux 虚拟机。
author: MikeWasson
ms.date: 11/12/2018
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seodec18
ms.openlocfilehash: 6c9a2b4fe513c959e537f705beb1c024b54c7b50
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54485442"
---
# <a name="linux-n-tier-application-in-azure-with-apache-cassandra"></a>Azure 中包含 Apache Cassandra 的 Linux N 层应用程序

此参考体系结构演示如何使用 Linux 上适用于数据层的 Apache Cassandra 部署为 [N 层](../../guide/architecture-styles/n-tier.md)应用程序配置的虚拟机 (VM) 和虚拟网络。 [**部署此解决方案**](#deploy-the-solution)。

![使用 Microsoft Azure 的 N 层体系结构](./images/n-tier-cassandra.png)

下载此体系结构的 [Visio 文件][visio-download]。

## <a name="architecture"></a>体系结构

此体系结构具有以下组件：

- 资源组。 [资源组][resource-manager-overview]用于对资源进行分组，以便可以按生存期、所有者或其他条件对其进行管理。

- **虚拟网络 (VNet) 和子网**。 每个 Azure VM 都会部署到可细分为子网的 VNet 中。 为每个层创建一个单独的子网。

- **NSG**。 使用[网络安全组][nsg] (NSG) 来限制 VNet 中的网络流量。 例如，在此处显示的三层体系结构中，数据库层接受来自业务层和管理子网的流量，但不接受来自 Web 前端的流量。

- **DDoS 防护**。 尽管 Azure 平台提供基本的保护来防范分布式拒绝服务 (DDoS) 攻击，但我们建议使用 [DDoS 保护标准版][ddos]，它提供增强的 DDoS 缓解功能。 请参阅[安全注意事项](#security-considerations)。

- **虚拟机**。 有关如何配置 VM 的建议，请参阅[在 Azure 上运行 Windows VM](./windows-vm.md) 和[在 Azure 上运行 Linux VM](./linux-vm.md)。

- **可用性集**。 为每个层创建[可用性集][azure-availability-sets]，并在每个层中至少预配两个 VM，使 VM 符合更高的[服务级别协议 (SLA)][vm-sla]。

- **Azure 负载均衡器**。 [负载均衡器][load-balancer]将传入的 Internet 请求分配到 VM 实例。 使用[公共负载均衡器][load-balancer-external]将传入的 Internet 流量分配到 Web 层，使用[内部负载均衡器][load-balancer-internal]将来自 Web 层的网络流量分配到业务层。

- **公共 IP 地址**。 公共负载均衡器需要使用一个公共 IP 地址来接收 Internet 流量。

- **Jumpbox**。 也称为[守护主机]。 网络上的一个安全 VM，管理员使用它来连接到其他 VM。 Jumpbox 中的某个 NSG 只允许来自安全列表中的公共 IP 地址的远程流量。 NSG 应允许 SSH 流量。

- **Apache Cassandra 数据库**。 通过启用复制和故障转移，在数据层提供高可用性。

- **Azure DNS**。 [Azure DNS][azure-dns] 是 DNS 域的托管服务。 它使用 Microsoft Azure 基础结构提供名称解析。 通过在 Azure 中托管域，可以使用与其他 Azure 服务相同的凭据、API、工具和计费来管理 DNS 记录。

## <a name="recommendations"></a>建议

你的要求可能不同于此处描述的体系结构。 请使用以下建议作为入手点。

### <a name="vnet--subnets"></a>VNet/子网

在创建 VNet 时，确定每个子网中的资源需要多少 IP 地址。 使用 [CIDR] 表示法为所需的 IP 地址指定子网掩码和足够大的 VNet 地址范围。 使用标准[专用 IP 地址块][private-ip-space]内的一个地址空间，这些地址块为 10.0.0.0/8、172.16.0.0/12 和 192.168.0.0/16。

如果以后需要在 VNet 与本地网络之间设置一个网关，请选择一个不与本地网络重叠的地址范围。 在创建 VNet 后，将无法更改地址范围。

在设计子网时一定要牢记功能和安全要求。 同一层或同一角色中的所有 VM 应当置于同一子网，这可能是一个安全边界。 有关设计 VNet 和子网的详细信息，请参阅[规划和设计 Azure 虚拟网络][plan-network]。

### <a name="load-balancers"></a>负载均衡器

不要直接向 Internet 公开 VM。 应该为每个 VM 提供专用 IP 地址。 客户端使用公共负载均衡器的 IP 地址进行连接。

定义用于将网络流量定向到 VM 的负载均衡器规则。 例如，若要启用 HTTP 流量，请创建将前端配置中的端口 80 映射到后端地址池上的端口 80 的规则。 当客户端将 HTTP 请求发送到端口 80 时，负载均衡器会通过使用包括源 IP 地址的[哈希算法][load-balancer-hashing]选择后端 IP 地址。 客户端请求将在所有 VM 之间分配。

### <a name="network-security-groups"></a>网络安全组

使用 NSG 规则限制各个层之间的流量。 例如，在上面显示的三层体系结构中，Web 层不直接与数据库层进行通信。 为强制实现此目的，数据库层应当阻止来自 Web 层子网的传入流量。

1. 拒绝来自 VNet 的所有入站流量。 （在规则中使用 `VIRTUAL_NETWORK` 标记。）
2. 允许来自业务层子网的入站流量。
3. 允许来自数据库层子网本身的入站流量。 此规则允许在数据库 VM 之间通信，这是进行数据库复制和故障转移所必需的。
4. 允许来自 Jumpbox 子网的 SSH 流量（端口 22）。 此规则允许管理员从 jumpbox 连接到数据库层。

创建优先级比第一项规则更高的规则 2 &ndash; 4，以便替代第一项规则。

### <a name="cassandra"></a>Cassandra

我们建议将 [DataStax Enterprise][datastax] 用于生产用途，但这些建议适用于任何 Cassandra 版本。 有关在 Azure 中运行 DataStax 的详细信息，请参阅[适用于 Azure 的 DataStax Enterprise 部署指南][cassandra-in-azure]。

将 Cassandra 群集的 VM 放置在一个可用性集中，以确保 Cassandra 副本分布在多个容错域和升级域中。 有关容错域和升级域的详细信息，请参阅[管理虚拟机的可用性][azure-availability-sets]。

为每个可用性集配置三个容错域（最大数目），并且为每个可用性集配置 18 个升级域。 这提供了仍可均匀分布在容错域中的升级域的最大数量。

以机架感知模式配置节点。 将故障域映射到 `cassandra-rackdc.properties` 文件中的机架。

群集前面不需要负载均衡器。 客户端会直接连接到群集中的节点。

若要确保高可用性，请将 Cassandra 部署在多个 Azure 区域。 每个区域中的节点采用机架感知模式并配置有容错域和升级域，以实现区域内的复原能力。

### <a name="jumpbox"></a>Jumpbox

不要允许从公共 Internet 对运行应用程序工作负荷的 VM 进行 SSH 访问。 相反，对这些 VM 的所有 SSH 访问都必须通过 Jumpbox 进行。 管理员登录到 jumpbox，然后从 jumpbox 登录到其他 VM。 Jumpbox 允许来自 Internet 的 SSH 流量，但仅允许来自已知的安全 IP 地址的流量。

Jumpbox 的性能要求非常低，因此请选择一个较小的 VM 大小。 为 jumpbox 创建一个[公共 IP 地址]。 将 jumpbox 放置在与其他 VM 相同的 VNet 中，但将其置于一个单独的管理子网中。

若要确保 Jumpbox 的安全，请添加一项 NSG 规则，仅允许来自一组安全的公共 IP 地址的 SSH 连接。 为其他子网配置 NSG，允许来自管理子网的 SSH 流量。

## <a name="scalability-considerations"></a>可伸缩性注意事项

对于 Web 和业务层，请考虑使用[虚拟机规模集][vmss]，而不要在可用性集中部署独立的 VM。 使用规模集可以轻松部署和管理一组相同的 VM 并根据性能指标自动缩放 VM。 VM 上的负载增加时，会自动向负载均衡器添加更多 VM。 如果需要快速横向扩展 VM，或者需要进行自动缩放，请考虑规模集。

有两种基本方法可用来配置规模集中部署的 VM：

- 在部署 VM 后使用扩展对其进行配置。 使用此方法时，启动新 VM 实例的所需时间可能会长于启动不带扩展的 VM 的所需时间。

- 使用自定义磁盘映像部署[托管磁盘](/azure/storage/storage-managed-disks-overview)。 此选项的部署速度可能更快。 但是，它要求将映像保持最新。

有关详细信息，请参阅[规模集的设计注意事项][vmss-design]。

> [!TIP]
> 在使用任何自动缩放解决方案时，请早早提前使用生产级工作负荷测试它。

每个 Azure 订阅都有适用的默认限制，包括每个区域的最大 VM 数量。 可以通过提出支持请求来提高上限。 有关详细信息，请参阅 [Azure 订阅和服务限制、配额与约束][subscription-limits]。

## <a name="availability-considerations"></a>可用性注意事项

如果不使用虚拟机规模集，请将相同层的 VM 放入可用性集。 请在可用性集中至少创建两个 VM 以支持 [Azure VM 的可用性 SLA][vm-sla]。 有关详细信息，请参阅[管理虚拟机的可用性][availability-set]。 规模集自动使用充当隐式可用性集的位置组。

负载均衡器使用[运行状况探测][health-probes]监视 VM 实例的可用性。 如果探测在超时期限内无法到达实例，负载均衡器会停止向该 VM 发送流量。 负载均衡器将继续探测，并且如果 VM 再次变得可用，负载均衡器会继续向该 VM 发送流量。

下面是有关负载均衡器运行状况探测的一些建议：

- 探测可以测试 HTTP 或 TCP。 如果 VM 运行 HTTP 服务器，请创建 HTTP 探测。 否则，请创建 TCP 探测。
- 对于 HTTP 探测，请指定指向 HTTP 终结点的路径。 探测将检查是否有来自此路径的 HTTP 200 响应。 这可以是根路径 ("/")，也可以是一个运行状况监视终结点，该终结点实现某些自定义逻辑来检查应用程序运行状况。 终结点必须允许匿名 HTTP 请求。
- 探测发送自[已知 IP 地址][health-probe-ip] 168.63.129.16。 务必不要在任何防火墙策略或 NSG 规则中阻止与此 IP 地址相互传送的流量。
- 使用[运行状况探测日志][health-probe-log]查看运行状况探测的状态。 请在 Azure 门户中为每个负载均衡器启用日志记录。 日志将写入到 Azure Blob 存储。 这些日志显示有多少个 VM 由于探测响应失败而未收到网络流量。

对于 Cassandra 群集，故障转移方案取决于应用程序使用的一致性级别以及副本数目。 有关 Cassandra 中的一致性级别和用法，请参阅[配置数据一致性][cassandra-consistency]和 [Cassandra：使用仲裁时要与多少个节点通信？][cassandra-consistency-usage] Cassandra 中的数据可用性取决于应用程序使用的一致性级别和复制机制。 有关 Cassandra 中的复制，请参阅 [NoSQL 数据库的数据复制说明][cassandra-replication]。

## <a name="security-considerations"></a>安全注意事项

虚拟网络是 Azure 中的流量隔离边界。 一个 VNet 中的 VM 无法直接与其他 VNet 中的 VM 通信。 同一个 VNet 中的 VM 之间可以通信，除非你创建[网络安全组][nsg] (NSG) 来限制流量。 有关详细信息，请参阅 [Microsoft 云服务和网络安全性][network-security]。

对于传入 Internet 流量，负载均衡器规则定义哪些流量可以到达后端。 但是，负载均衡器规则不支持 IP 安全列表，因此如果要将某些公共 IP 地址添加到安全列表，请将 NSG 添加到子网。

**外围网络**。 请考虑添加一个网络虚拟设备 (NVA) 以在 Internet 与 Azure 虚拟网络之间创建一个外围网络。 NVA 是虚拟设备的一个通用术语，可以执行与网络相关的任务，例如防火墙、包检查、审核和自定义路由。 有关详细信息，请参阅[在 Azure 与 Internet 之间实现外围网络][dmz]。

**加密**。 加密静态的敏感数据并使用 [Azure Key Vault][azure-key-vault] 管理数据库加密密钥。 Key Vault 可以将加密密钥存储在硬件安全模块 (HSM) 中。 另外，建议将应用程序机密（例如数据库连接字符串）也存储在 Key Vault 中。

**DDOS 防护**。 Azure 平台默认提供基本 DDoS 防护。 这种基本保护旨在将 Azure 基础结构作为一个整体进行保护。 尽管已自动启用基本 DDoS 防护，但我们建议使用 [DDoS 防护标准版][ddos]。 标准防护基于应用程序的网络流量模式使用自适应优化来检测威胁。 这样，它便可以针对基础结构范围的 DDoS 策略忽略的 DDoS 攻击应用缓解措施。 标准防护还通过 Azure Monitor 提供警报、遥测和分析。 有关详细信息，请参阅 [Azure DDoS 防护：最佳做法和参考体系结构][ddos-best-practices]。

## <a name="deploy-the-solution"></a>部署解决方案

[GitHub][github-folder] 中提供了此参考体系结构的部署。

### <a name="prerequisites"></a>先决条件

[!INCLUDE [ref-arch-prerequisites.md](../../../includes/ref-arch-prerequisites.md)]

### <a name="deploy-the-solution-using-azbb"></a>使用 azbb 部署解决方案

若要为 N 层应用程序参考体系结构部署 Linux VM，请执行以下步骤：

1. 导航到在以上前决条件的第 1 步中克隆的存储库的 `virtual-machines\n-tier-linux` 文件夹。

2. 参数文件为部署中的每个 VM 指定默认管理员用户名称和密码。 请在部署参考体系结构之前更改这些凭据。 打开 `n-tier-linux.json` 文件，然后将每个“adminUsername”和“adminPassword”字段替换为新设置。   保存文件。

3. 如下所示，使用 **azbb** 工具部署参考体系结构。

   ```azurecli
   azbb -s <your subscription_id> -g <your resource_group_name> -l <azure region> -p n-tier-linux.json --deploy
   ```

若要详细了解如何使用 Azure 构建基块部署此示例参考体系结构，请访问 [GitHub 存储库][git]。

## <a name="next-steps"></a>后续步骤

- [Microsoft Learn 模块：探索 N 层体系结构样式](/learn/modules/n-tier-architecture/)

<!-- links -->

[dmz]: ../dmz/secure-vnet-dmz.md
[multi-vm]: ./multi-vm.md
[naming conventions]: /azure/guidance/guidance-naming-conventions
[azure-availability-sets]: /azure/virtual-machines/virtual-machines-linux-manage-availability
[azure-dns]: /azure/dns/dns-overview
[azure-key-vault]: https://azure.microsoft.com/services/key-vault

[守护主机]: https://en.wikipedia.org/wiki/Bastion_host
[cassandra-in-azure]: https://academy.datastax.com/resources/deployment-guide-azure
[cassandra-consistency]: https://docs.datastax.com/en/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html
[cassandra-replication]: https://academy.datastax.com/planet-cassandra/data-replication-in-nosql-databases-explained
[cassandra-consistency-usage]: https://medium.com/@foundev/cassandra-how-many-nodes-are-talked-to-with-quorum-also-should-i-use-it-98074e75d7d5#.b4pb4alb2

[cidr]: https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing
[datastax]: https://www.datastax.com/products/datastax-enterprise
[ddos]: /azure/virtual-network/ddos-protection-overview
[ddos-best-practices]: /azure/security/azure-ddos-best-practices
[git]: https://github.com/mspnp/template-building-blocks
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/virtual-machines/n-tier-linux
[load-balancer-external]: /azure/load-balancer/load-balancer-internet-overview
[load-balancer-internal]: /azure/load-balancer/load-balancer-internal-overview
[nsg]: /azure/virtual-network/virtual-networks-nsg
[nsg-rules]: /azure/azure-resource-manager/best-practices-resource-manager-security#network-security-groups
[plan-network]: /azure/virtual-network/virtual-network-vnet-plan-design-arm
[private-ip-space]: https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces
[公共 IP 地址]: /azure/virtual-network/virtual-network-ip-addresses-overview-arm
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines
[visio-download]: https://archcenter.blob.core.windows.net/cdn/vm-reference-architectures.vsdx

[resource-manager-overview]: /azure/azure-resource-manager/resource-group-overview
[vmss]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview
[load-balancer]: /azure/load-balancer/load-balancer-get-started-internet-arm-cli
[load-balancer-hashing]: /azure/load-balancer/load-balancer-overview#load-balancer-features
[vmss-design]: /azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview
[subscription-limits]: /azure/azure-subscription-service-limits
[availability-set]: /azure/virtual-machines/virtual-machines-windows-manage-availability
[health-probes]: /azure/load-balancer/load-balancer-overview#load-balancer-features
[health-probe-log]: /azure/load-balancer/load-balancer-monitor-log
[health-probe-ip]: /azure/virtual-network/virtual-networks-nsg#special-rules
[network-security]: /azure/best-practices-network-security
