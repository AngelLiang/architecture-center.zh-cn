---
title: 银行间的分散式信任
titleSuffix: Azure Example Scenarios
description: 建立受信任的环境以进行通信和信息共享，而不必求助于集中式数据库。
author: vitoc
ms.date: 09/09/2018
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.custom: csa-team
social_image_url: /azure/architecture/example-scenario/apps/media/architecture-decentralized-trust.png
ms.openlocfilehash: a3c497f91b3861bf02f05981ee92e578a22a14ca
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58246278"
---
# <a name="decentralized-trust-between-banks-on-azure"></a>Azure 上银行间的分散式信任

本示例方案适用于银行，或者希望在不借助集中式数据库的情况下，为信息共享建立可信环境的其他任何机构。 本示例将在维护不同银行信用评分信息的上下文中描述方案，但该体系结构可应用于如下所述的任何方案：组织联盟希望能够在不借助于任何一方运行的中心系统的情况下，相互共享验证的信息。

金融系统中的银行一贯依赖于征信局等集中源来了解有关个人信用评分和历史信息。 集中式方法会将操作风险凝聚到一起，有时还会涉及到某个不必要的第三方。

使用 DLT（分布式账本技术），银行联盟可以建立更高效、不易受到攻击并可充当新平台的分散式系统，在其中可以实施创新结构来解决隐私、速度和成本方面的传统难题。

本示例将演示如何快速预配虚拟机规模集、虚拟网络、Key Vault、存储、负载均衡器和 Monitor 等 Azure 服务，以部署高效的专用 Ethereum PoA 区块链，让成员银行在其中建立自身的节点。

## <a name="relevant-use-cases"></a>相关用例

其他相关用例包括：

- 分配的预算在跨国公司的不同业务部门之间转移
- 跨界付款
- 贸易融资方案
- 涉及不同公司的会员系统
- 供应链生态系统

## <a name="architecture"></a>体系结构

![分散式银行信任体系结构示意图](./media/architecture-decentralized-trust.png)

此方案包括在由两个或更多个成员组成的联盟中创建可缩放、安全、受监视的专用企业区块链网络所需的后端组件。 你需要根据组织的政策要求，详细考虑如何预配这些组件（即，在不同的订阅和资源组中预配）和连接要求（即，VPN 或 ExpressRoute）。 数据流如下：

1. 银行 A 通过 JSON-RPC 将事务发送到区块链网络，以创建/更新个人的信用记录。
2. 数据从银行 A 的专用应用程序服务器发送到 Azure 负载均衡器，然后发送到虚拟机规模集上的验证节点 VM。
3. Ethereum PoA 网络在预设的时间（在本方案中为 2 秒）创建一个区块。
4. 事务捆绑到创建的区块，并通过区块链网络进行验证。
5. 银行 B 可以通过与其自身的节点通信（同样通过 JSON-RPC），来读取银行 A 创建的信用记录。

### <a name="components"></a>组件

- 虚拟机规模集中的虚拟机提供按需计算设施用于托管区块链的验证程序进程
- Key Vault 用作安全存储设施，可存储每个验证程序的私钥
- 负载均衡器分散 RPC、对等互连和监管 DApp 请求
- 用于托管持久性网络信息并协调租用的存储
- Operations Management Suite（绑定多个 Azure 服务）提供可用节点数、每分钟事务数和联盟成员的见解

### <a name="alternatives"></a>备选项

之所以在本示例中选择 Ethereum PoA 方法，是因为此方法是一个良好的入口点，可让组织联盟创建一个可以通过可信、分散且可轻松理解的方式交换和共享信息的环境。 此外，可用的 Azure 解决方案模板不仅为联盟领袖提供快速方便的方法用于启动 Ethereum PoA 区块链，而且还可让联盟中的成员组织在其自己的 Azure 资源组和订阅中运转其自己的 Azure 资源以加入现有网络。

对于其他扩展方案或不同方案，可能会出现交易隐私等方面的忧虑。 例如，在证券转让方案中，联盟中的成员可能不希望其他人（甚至是其他成员）看到其交易。 Ethereum PoA 中的其他替代方案可按自身的方式解决这些忧虑：

- Corda
- Quorum
- Hyperledger

## <a name="considerations"></a>注意事项

### <a name="availability"></a>可用性

[Azure Monitor][monitor] 用于持续监视区块链网络中的问题，以确保可用性。 成功部署此方案中使用的区块链解决方案模板后，会将基于 Azure Monitor 的自定义监视仪表板的链接发送给您。 该仪表板显示过去 30 分钟报告了检测信号的节点，以及其他有用的统计信息。

若要了解其他可用性主题，请参阅 Azure 体系结构中心的[可用性核对清单][availability]。

### <a name="scalability"></a>可伸缩性

在区块链方面，一个常见的考虑因素是区块链在预设时间内可以包含的事务数。 此方案使用权威证明，与使用工作证明相比，可以更好地管理可伸缩性。 在基于权威证明的网络中，达成共识的参与者是已知的并且是受管理的，因此权威证明更适合相互了解的组织联盟的专用区块链。 通过自定义仪表板可以轻松监视平均区块时间、每分钟事务数和计算资源消耗量等参数。 然后，可以根据规模要求相应地调整资源。

有关如何设计可缩放解决方案的通用指南，请参阅 Azure 体系结构中心的[可伸缩性核对清单][scalability]。

### <a name="security"></a>安全

[Azure Key Vault][vault] 用于轻松存储和管理验证程序的私钥。 本示例中的默认部署将创建一个可通过 Internet 访问的区块链网络。 对于需要专用网络的生产方案，成员可以通过 VNet 间的 VPN 网关相互连接。 以下“相关资源”部分包含了配置 VPN 的步骤。

若需安全解决方案的通用设计指南，请参阅 [Azure 安全性文档][security]。

### <a name="resiliency"></a>复原

由于验证程序节点可以部署在不同的区域，Ethereum PoA 区块链本身能够提供某种程度的复原能力。 Azure 提供在超过 54 个全球区域中进行部署的选项。 如本方案中所述的区块链提供独特、全新的协作功能用于提高复原能力。 网络复原能力不能仅仅由单个集中式方案参与方提供，而应该由联盟的所有成员提供。 基于权威证明的区块链允许对网络复原能力进行更多的精心规划。

若需可复原解决方案的通用设计指南，请参阅[设计适用于 Azure 的可复原应用程序][resiliency]。

## <a name="pricing"></a>定价

为了方便用户了解运行本方案的成本，我们已在成本计算器中预配置了所有服务。 若要了解自己的特定用例的定价变化情况，请根据预期的性能和可用性要求更改相应的变量。

我们已根据运行应用程序的规模集 VM 实例数（实例可能驻留在不同的区域中）提供了三个示例性的成本配置文件。

- [小型][small-pricing]：此定价示例适用于每个月使用 2 个 VM 并禁用监视的情况
- [中型][medium-pricing]：此定价示例适用于每个月使用 7 个 VM 并启用监视的情况
- [大型][large-pricing]：此定价示例适用于每个月使用 15 个 VM 并启用监视的情况

以上定价适用于启动或加入区块链网络的一个联盟成员。 通常，在涉及到多个公司或组织的联盟中，每个成员会获取自身的 Azure 订阅。

## <a name="next-steps"></a>后续步骤

若要查看此方案的示例，请在 Azure 上部署 [Ethereum PoA 区块链演示应用程序][deploy]。 然后，查看[方案源代码的自述文件][source]。

## <a name="related-resources"></a>相关资源

有关使用适用于 Azure 的 Ethereum 权威证明解决方案模板的详细信息，请查看此[用法指南][guide]。

<!-- links -->
[small-pricing]: https://azure.com/e/4e429d721eb54adc9a1558fae3e67990
[medium-pricing]: https://azure.com/e/bb42cd77437744be8ed7064403bfe2ef
[large-pricing]: https://azure.com/e/e205b443de3e4adfadf4e09ffee30c56
[guide]: /azure/blockchain-workbench/ethereum-poa-deployment
[deploy]: https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium
[source]: https://github.com/vitoc/creditscoreblockchain
[monitor]: /azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor
[availability]: /azure/architecture/checklist/availability
[scalability]: /azure/architecture/checklist/scalability
[resiliency]: ../../resiliency/index.md
[security]: /azure/security/
[vault]: https://azure.microsoft.com/services/key-vault/
