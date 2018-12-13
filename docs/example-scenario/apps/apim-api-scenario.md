---
title: 将旧式 Web 应用程序迁移到 Azure 上基于 API 的体系结构
description: 使用 Azure API 管理来实现旧式 Web 应用程序的现代化。
author: begim
ms.date: 09/13/2018
ms.custom: fasttrack
ms.openlocfilehash: ea063653b4962e42cbec7f9d98c16e22e987efd1
ms.sourcegitcommit: a0e8d11543751d681953717f6e78173e597ae207
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2018
ms.locfileid: "53004700"
---
# <a name="migrating-a-legacy-web-application-to-an-api-based-architecture-on-azure"></a>将旧式 Web 应用程序迁移到 Azure 上基于 API 的体系结构

某家旅游行业的电子商务公司正在现代化其旧式基于浏览器的软件堆栈。 尽管其现有堆栈基本上是整体化的，但最近某个项目中采用了一些[基于 SOAP 的 HTTP 服务][soap]。 他们正在考虑建立更多的收入流，以便将一些已开发的内部知识产权转化为收益。

项目目标包括解决技术债务、改进日常维护，以及加速功能开发并减少回归缺陷。 该项目将使用迭代过程来避免风险，同时并行执行某些步骤：

* 开发团队将现代化应用程序后端，该后端由 VM 上托管的关系数据库组成。
* 内部开发团队将编写要通过新 HTTP API 公开的新业务功能。
* 合同开发团队将生成一个要托管在 Azure 中的基于浏览器的新 UI。

新应用程序功能分阶段交付。 这些功能将逐渐替代现有的、为目前电子商务业务提供动力的基于浏览器的客户端-服务器 UI 功能（托管在本地）。

管理团队不希望进行不必要的现代化。 此外，他们希望能够保持控制项目范围和成本。 为此，他们决定保留现有的 SOAP HTTP 服务。 他们还希望能够尽量减少对现有 UI 做出的更改。 利用 [Azure API 管理 (APIM)][apim] 可以解决该项目的许多要求和约束。

## <a name="architecture"></a>体系结构

![体系结构关系图][architecture]

新 UI 将作为平台即服务 (PaaS) 应用程序托管在 Azure 上，并依赖于现有和新的 HTTP API。 这些 API 附带了一组设计更合理的接口，可以提高性能、简化集成和实现将来的扩展。

### <a name="components-and-security"></a>组件和安全性

1. 现有的本地 Web 应用程序继续直接使用现有的本地 Web 服务。
2. 仍然是从现有的 Web 应用调用现有的 HTTP 服务。 这些调用在企业网络内部执行。
3. 入站调用是从 Azure 向现有内部服务发出的：
    * 安全团队允许来自 APIM 实例的流量[使用安全传输 (HTTPS/SSL)][apim-ssl] 通过企业防火墙传递到现有的本地服务。
    * 运营团队只允许从 APIM 实例向服务发出入站调用。 在企业网络边界内将 [APIM 实例的 IP 地址加入允许列表][apim-whitelist-ip]可满足此要求。
    * 在本地 HTTP 服务请求管道（**只**处理来自外部的连接）中配置一个新模块，用于验证[APIM 提供的证书][apim-mutualcert-auth]。
1. 新 API：
    * 只通过提供 API 结构的 APIM 实例公开。 不会直接访问新 API。
    * 开发并发布为 [Azure PaaS Web API 应用][azure-api-apps]。
    * 已加入允许列表（通过 [Web 应用设置][azure-appservice-ip-restrict]），仅接受 [APIM VIP][apim-faq-vip]。
    * 托管在已启用安全传输/SSL 的 Azure Web 应用中。
    * 已启用授权，该授权由 [Azure 应用服务][azure-appservice-auth]使用 Azure Active Directory 和 OAuth2 提供。
2. 基于浏览器的新 Web 应用程序将依赖于 Azure API 管理实例来使用现有的 HTTP API **和**新的 API。

APIM 实例配置为将旧式 HTTP 服务映射到新的 API 合同。 这样，新 Web UI 将不知道与一组旧式服务/API 和新 API 进行了集成。 将来，项目团队会逐渐将功能移植到新 API，并淘汰原始服务。 这些更改将在 APIM 配置中处理，使前端 UI 不受影响，并避免重复开发工作。

### <a name="alternatives"></a>备选项

* 如果组织打算将整个基础结构（包括托管旧式应用程序的 VM）转移到 Azure，则 APIM 仍是一个极佳的选项，因为它可以充当任何可寻址 HTTP 终结点的结构。
* 如果客户决定保持现有终结点的私密性，而不想将其公开，可将其 API 管理实例链接到 [Azure 虚拟网络 (VNet)][azure-vnet]：
  * 在链接到已部署的 Azure 虚拟网络的 [Azure 直接迁移方案][azure-vm-lift-shift]中，客户可以通过专用 IP 地址直接寻址后端服务。
  * 在本地方案中，API 管理实例可以通过 [Azure VPN 网关和站点到站点 IPSec VPN 连接][azure-vpn]或 [ExpressRoute][azure-er] 以私密方式反向连接到内部服务，从而形式了 [Azure 和本地混合方案][azure-hybrid]。
* 以内部模式部署 API 管理实例可以保持 API 管理实例的私密性。 然后，可以结合 [Azure 应用程序网关][azure-appgw]使用该部署，让某些 API 进行公开访问，同时让其他一些 API 进行内部访问。 有关详细信息，请参阅[将处于内部模式的 APIM 连接到 VNET][apim-vnet-internal]。

> [!NOTE]
> 有关将 API 管理连接到 VNET 的一般信息，请[参阅此文][apim-vnet]。

### <a name="availability-and-scalability"></a>可用性和可伸缩性

* 可以通过选择定价层，然后添加单元，来[横向扩展][apim-scaleout] Azure API 管理。
* 也可以[使用自动缩放][apim-autoscale]来实现自动缩放。
* [跨多个区域部署][apim-multi-regions]可以实现故障转移，在[高级层][apim-pricing]中可以执行此操作。
* 考虑[与 Azure Application Insights 集成][azure-apim-ai]，这样还可以通过 [Azure Monitor][azure-mon] 公开指标用于监视。

## <a name="deployment"></a>部署

若要开始，请[在门户中创建一个 Azure API 管理实例][apim-create]。

或者，可以从符合具体用例的现有 Azure 资源管理器[快速入门模板][azure-quickstart-templates-apim]中进行选择。

## <a name="pricing"></a>定价

以下四个层中提供了 API 管理：开发人员、基本、标准和高级。 在 [Azure API 管理定价指导][apim-pricing]中，可以找到有关这些层的差异的详细指导。

客户可通过添加和删除单元来缩放 API 管理。 每个单元的容量取决于其所在的层。

> [!NOTE]
> “开发人员”层可用于评估 API 管理功能。 不应在生产环境中使用“开发人员”层。

若要查看预计成本并根据部署需求进行自定义，可在 [Azure 定价计算器][pricing-calculator]中修改缩放单元和应用服务实例的数目。

## <a name="related-resources"></a>相关资源

查看有关 Azure API 管理的丰富[文档和参考文章][apim]。


<!-- links -->
[architecture]: ./media/architecture-apim-api-scenario.png
[apim-create]: /azure/api-management/get-started-create-service-instance
[apim-git]: /azure/api-management/api-management-configuration-repository-git
[apim-multi-regions]: /azure/api-management/api-management-howto-deploy-multi-region
[apim-autoscale]: /azure/api-management/api-management-howto-autoscale
[apim-scaleout]: /azure/api-management/upgrade-and-scale
[azure-apim-ai]: /azure/api-management/api-management-howto-app-insights
[azure-ai]: /azure/application-insights/
[azure-mon]: /azure/monitoring-and-diagnostics/monitoring-overview
[azure-appgw]: /azure/application-gateway/application-gateway-introduction
[apim-vnet-internal]: /azure/api-management/api-management-howto-integrate-internal-vnet-appgateway
[apim-vnet]: /azure/api-management/api-management-using-with-vnet
[azure-hybrid]: /azure/architecture/reference-architectures/hybrid-networking/
[azure-er]: /azure/expressroute/expressroute-introduction
[azure-vpn]: /azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal
[azure-vnet]: /azure/virtual-network/virtual-networks-overview
[azure-appservice-auth]: /azure/app-service/app-service-authentication-overview#identity-providers
[apim-faq-vip]: /azure/api-management/api-management-faq#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules
[azure-appservice-ip-restrict]: /azure/app-service/app-service-ip-restrictions
[azure-api-apps]: /azure/app-service/
[apim-ssl]: /azure/api-management/api-management-howto-manage-protocols-ciphers
[apim-mutualcert-auth]: /azure/api-management/api-management-howto-mutual-certificates
[apim-whitelist-ip]: /azure/api-management/api-management-faq#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules
[anti-corruption-layer-pattern]: /azure/architecture/patterns/anti-corruption-layer
[apim]: /azure/api-management/api-management-key-concepts
[apim-api-design-guidance]: /azure/architecture/best-practices/api-design
[visualstudio-youtube-solid-design]: https://youtu.be/agkWYPUcLpg
[azure-vm-lift-shift]: https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/
[standard-pricing-calc]: https://azure.com/e/
[premium-pricing-calc]: https://azure.com/e/
[apim-pricing]: https://azure.microsoft.com/pricing/details/api-management/
[azure-quickstart-templates-apim]: https://azure.microsoft.com/resources/templates/?term=API+Management&pageNumber=1
[soap]: https://en.wikipedia.org/wiki/SOAP
[pricing-calculator]: https://azure.com/e/0e916a861fac464db61342d378cc0bd6
