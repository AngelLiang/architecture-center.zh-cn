---
title: 将旧式 Web 应用程序迁移到 Azure 上基于 API 的体系结构
description: 使用 Azure API 管理来实现旧式 Web 应用程序的现代化。
author: begim
ms.date: 09/13/2018
ms.openlocfilehash: 1aa7ea6dc895146e13677dd9867fb2530f0a8f04
ms.sourcegitcommit: 62945777e519d650159f0f963a2489b6bb6ce094
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2018
ms.locfileid: "48876777"
---
# <a name="migrating-a-legacy-web-application-to-an-api-based-architecture-on-azure"></a><span data-ttu-id="a184a-103">将旧式 Web 应用程序迁移到 Azure 上基于 API 的体系结构</span><span class="sxs-lookup"><span data-stu-id="a184a-103">Migrating a legacy web application to an API-based architecture on Azure</span></span>

<span data-ttu-id="a184a-104">某家旅游行业的电子商务公司正在现代化其旧式基于浏览器的软件堆栈。</span><span class="sxs-lookup"><span data-stu-id="a184a-104">An e-commerce company in the travel industry is modernizing their legacy browser-based software stack.</span></span> <span data-ttu-id="a184a-105">尽管其现有堆栈基本上是整体化的，但最近某个项目中采用了一些[基于 SOAP 的 HTTP 服务][soap]。</span><span class="sxs-lookup"><span data-stu-id="a184a-105">While their existing stack is mostly monolithic, some [SOAP-based HTTP services][soap] exist from a recent project.</span></span> <span data-ttu-id="a184a-106">他们正在考虑建立更多的收入流，以便将一些已开发的内部知识产权转化为收益。</span><span class="sxs-lookup"><span data-stu-id="a184a-106">They are considering the creation of additional revenue streams to monetize some of the internal intellectual property that's been developed.</span></span>

<span data-ttu-id="a184a-107">项目目标包括解决技术债务、改进日常维护，以及加速功能开发并减少回归缺陷。</span><span class="sxs-lookup"><span data-stu-id="a184a-107">Goals for the project include addressing technical debt, improving ongoing maintenance, and accelerating feature development with fewer regression bugs.</span></span> <span data-ttu-id="a184a-108">该项目将使用迭代过程来避免风险，同时并行执行某些步骤：</span><span class="sxs-lookup"><span data-stu-id="a184a-108">The project will use an iterative process to avoid risk, with some steps performed in parallel:</span></span>

* <span data-ttu-id="a184a-109">开发团队将现代化应用程序后端，该后端由 VM 上托管的关系数据库组成。</span><span class="sxs-lookup"><span data-stu-id="a184a-109">The development team will modernize the application back end, which is composed of relational databases hosted on VMs.</span></span>
* <span data-ttu-id="a184a-110">内部开发团队将编写要通过新 HTTP API 公开的新业务功能。</span><span class="sxs-lookup"><span data-stu-id="a184a-110">The in-house development team will write new business functionality, which will be exposed over new HTTP APIs.</span></span>
* <span data-ttu-id="a184a-111">合同开发团队将生成一个要托管在 Azure 中的基于浏览器的新 UI。</span><span class="sxs-lookup"><span data-stu-id="a184a-111">A contract development team will build a new browser-based UI, which will be hosted in Azure.</span></span>

<span data-ttu-id="a184a-112">新应用程序功能分阶段交付。</span><span class="sxs-lookup"><span data-stu-id="a184a-112">New application features will be delivered in stages.</span></span> <span data-ttu-id="a184a-113">他们将逐渐替代现有的、为目前电子商务业务提供动力的基于浏览器的客户端-服务器 UI 功能（托管在本地）。</span><span class="sxs-lookup"><span data-stu-id="a184a-113">They will *gradually replace* the existing browser-based client-server UI functionality (hosted on-premises) that powers their e-commerce business today.</span></span>

<span data-ttu-id="a184a-114">管理团队不希望进行不必要的现代化。</span><span class="sxs-lookup"><span data-stu-id="a184a-114">The management team does not want to modernize unnecessarily.</span></span> <span data-ttu-id="a184a-115">此外，他们希望能够保持控制项目范围和成本。</span><span class="sxs-lookup"><span data-stu-id="a184a-115">They also want to maintain control of scope and costs.</span></span> <span data-ttu-id="a184a-116">为此，他们决定保留现有的 SOAP HTTP 服务。</span><span class="sxs-lookup"><span data-stu-id="a184a-116">To do this, they have decided to preserve their existing SOAP HTTP services.</span></span> <span data-ttu-id="a184a-117">他们还希望能够尽量减少对现有 UI 做出的更改。</span><span class="sxs-lookup"><span data-stu-id="a184a-117">They also intend to minimize changes to the existing UI.</span></span> <span data-ttu-id="a184a-118">利用 [Azure API 管理 (APIM)][apim] 可以解决该项目的许多要求和约束。</span><span class="sxs-lookup"><span data-stu-id="a184a-118">[Azure API Management (APIM)][apim] can be utilized to address many of the project's requirements and constraints.</span></span>

## <a name="architecture"></a><span data-ttu-id="a184a-119">体系结构</span><span class="sxs-lookup"><span data-stu-id="a184a-119">Architecture</span></span>

![体系结构关系图][architecture]

<span data-ttu-id="a184a-121">新 UI 将作为平台即服务 (PaaS) 应用程序托管在 Azure 上，并依赖于现有和新的 HTTP API。</span><span class="sxs-lookup"><span data-stu-id="a184a-121">The new UI will be hosted as a platform as a service (PaaS) application on Azure, and will depend on both existing and new HTTP APIs.</span></span> <span data-ttu-id="a184a-122">这些 API 附带了一组设计更合理的接口，可以提高性能、简化集成和实现将来的扩展。</span><span class="sxs-lookup"><span data-stu-id="a184a-122">These APIs will ship with a better-designed set of interfaces enabling better performance, easier integration, and future extensibility.</span></span>

### <a name="components-and-security"></a><span data-ttu-id="a184a-123">组件和安全性</span><span class="sxs-lookup"><span data-stu-id="a184a-123">Components and Security</span></span>

1. <span data-ttu-id="a184a-124">现有的本地 Web 应用程序继续直接使用现有的本地 Web 服务。</span><span class="sxs-lookup"><span data-stu-id="a184a-124">The existing on-premises web application will continue to directly consume the existing on-premises web services.</span></span>
2. <span data-ttu-id="a184a-125">仍然是从现有的 Web 应用调用现有的 HTTP 服务。</span><span class="sxs-lookup"><span data-stu-id="a184a-125">Calls from the existing web app to the existing HTTP services will remain unchanged.</span></span> <span data-ttu-id="a184a-126">这些调用在企业网络内部执行。</span><span class="sxs-lookup"><span data-stu-id="a184a-126">These calls are internal to the corporate network.</span></span>
3. <span data-ttu-id="a184a-127">入站调用是从 Azure 向现有内部服务发出的：</span><span class="sxs-lookup"><span data-stu-id="a184a-127">Inbound calls are made from Azure to the existing internal services:</span></span>
    * <span data-ttu-id="a184a-128">安全团队允许来自 APIM 实例的流量[使用安全传输 (HTTPS/SSL)][apim-ssl] 通过企业防火墙传递到现有的本地服务。</span><span class="sxs-lookup"><span data-stu-id="a184a-128">The security team allows traffic from the APIM instance to pass through the corporate firewall to the existing on-premises services [using secure transport (HTTPS/SSL)][apim-ssl].</span></span>
    * <span data-ttu-id="a184a-129">运营团队只允许从 APIM 实例向服务发出入站调用。</span><span class="sxs-lookup"><span data-stu-id="a184a-129">The operations team will allow inbound calls to the services only from the APIM instance.</span></span> <span data-ttu-id="a184a-130">在企业网络边界内将 [APIM 实例的 IP 地址加入白名单][apim-whitelist-ip]可满足此要求。</span><span class="sxs-lookup"><span data-stu-id="a184a-130">This requirement is met by [white-listing the IP address of the APIM instance][apim-whitelist-ip] within the corporate network perimeter.</span></span>
    * <span data-ttu-id="a184a-131">在本地 HTTP 服务请求管道（**只**处理来自外部的连接）中配置一个新模块，用于验证[APIM 提供的证书][apim-mutualcert-auth]。</span><span class="sxs-lookup"><span data-stu-id="a184a-131">A new module is configured into the on-premises HTTP services request pipeline (to act upon **only** those connections originating externally), which will validate [a certificate which APIM will provide][apim-mutualcert-auth].</span></span>
1. <span data-ttu-id="a184a-132">新 API：</span><span class="sxs-lookup"><span data-stu-id="a184a-132">The new API:</span></span>
    * <span data-ttu-id="a184a-133">只通过提供 API 结构的 APIM 实例公开。</span><span class="sxs-lookup"><span data-stu-id="a184a-133">Is surfaced only through the APIM instance, which will provide the API facade.</span></span> <span data-ttu-id="a184a-134">不会直接访问新 API。</span><span class="sxs-lookup"><span data-stu-id="a184a-134">The new API won't be accessed directly.</span></span>
    * <span data-ttu-id="a184a-135">开发并发布为 [Azure PaaS Web API 应用][azure-api-apps]。</span><span class="sxs-lookup"><span data-stu-id="a184a-135">Is developed and published as an [Azure PaaS Web API App][azure-api-apps].</span></span>
    * <span data-ttu-id="a184a-136">已加入白名单（通过 [Web 应用设置][azure-appservice-ip-restrict]），仅接受 [APIM VIP][apim-faq-vip]。</span><span class="sxs-lookup"><span data-stu-id="a184a-136">Is white-listed (via [Web App settings][azure-appservice-ip-restrict]) to accept only the [APIM VIP][apim-faq-vip].</span></span>
    * <span data-ttu-id="a184a-137">托管在已启用安全传输/SSL 的 Azure Web 应用中。</span><span class="sxs-lookup"><span data-stu-id="a184a-137">Is hosted in Azure Web Apps with Secure Transport/SSL turned on.</span></span>
    * <span data-ttu-id="a184a-138">已启用授权，该授权由 [Azure 应用服务][azure-appservice-auth]使用 Azure Active Directory 和 OAuth2 提供。</span><span class="sxs-lookup"><span data-stu-id="a184a-138">Has authorization enabled, [provided by the Azure App Service][azure-appservice-auth] using Azure Active Directory and OAuth2.</span></span>
2. <span data-ttu-id="a184a-139">基于浏览器的新 Web 应用程序将依赖于 Azure API 管理实例来使用现有的 HTTP API **和**新的 API。</span><span class="sxs-lookup"><span data-stu-id="a184a-139">The new browser-based web application will depend on the Azure API Management instance for **both** the existing HTTP API and the new API.</span></span>

<span data-ttu-id="a184a-140">APIM 实例配置为将旧式 HTTP 服务映射到新的 API 合同。</span><span class="sxs-lookup"><span data-stu-id="a184a-140">The APIM instance will be configured to map the legacy HTTP services to a new API contract.</span></span> <span data-ttu-id="a184a-141">这样，新 Web UI 将不知道与一组旧式服务/API 和新 API 进行了集成。</span><span class="sxs-lookup"><span data-stu-id="a184a-141">By doing this, the new Web UI is unaware of the integration with a set of legacy services/APIs and new APIs.</span></span> <span data-ttu-id="a184a-142">将来，项目团队会逐渐将功能移植到新 API，并淘汰原始服务。</span><span class="sxs-lookup"><span data-stu-id="a184a-142">In the future, the project team will gradually port functionality to the new APIs and retire the original services.</span></span> <span data-ttu-id="a184a-143">这些更改将在 APIM 配置中处理，使前端 UI 不受影响，并避免重复开发工作。</span><span class="sxs-lookup"><span data-stu-id="a184a-143">These changes will be handled within APIM configuration, leaving the front-end UI unaffected and avoiding redevelopment work.</span></span>

### <a name="alternatives"></a><span data-ttu-id="a184a-144">备选项</span><span class="sxs-lookup"><span data-stu-id="a184a-144">Alternatives</span></span>

* <span data-ttu-id="a184a-145">如果组织打算将整个基础结构（包括托管旧式应用程序的 VM）转移到 Azure，则 APIM 仍是一个极佳的选项，因为它可以充当任何可寻址 HTTP 终结点的结构。</span><span class="sxs-lookup"><span data-stu-id="a184a-145">If the organization was planning to move their infrastructure entirely to Azure, including the VMs hosting the legacy applications, then APIM would still be a great option since it can act as a facade for any addressable HTTP endpoint.</span></span>
* <span data-ttu-id="a184a-146">如果客户决定保持现有终结点的私密性，而不想将其公开，可将其 API 管理实例链接到 [Azure 虚拟网络 (VNet)][azure-vnet]：</span><span class="sxs-lookup"><span data-stu-id="a184a-146">If the customer had decided to keep the existing endpoints private and not expose them publicly, their API Management instance could be linked to an [Azure Virtual Network (VNet)][azure-vnet]:</span></span>
  * <span data-ttu-id="a184a-147">在链接到已部署的 Azure 虚拟网络的 [Azure 直接迁移方案][azure-vm-lift-shift]中，客户可以通过专用 IP 地址直接寻址后端服务。</span><span class="sxs-lookup"><span data-stu-id="a184a-147">In an [Azure lift and shift scenario][azure-vm-lift-shift] linked to their deployed Azure Virtual Network, the customer could directly address the back-end service through private IP addresses.</span></span>
  * <span data-ttu-id="a184a-148">在本地方案中，API 管理实例可以通过 [Azure VPN 网关和站点到站点 IPSec VPN 连接][azure-vpn]或 [ExpressRoute][azure-er] 以私密方式反向连接到内部服务，从而形式了 [Azure 和本地混合方案][azure-hybrid]。</span><span class="sxs-lookup"><span data-stu-id="a184a-148">In the on-premises scenario, the API Management instance could reach back to the internal service privately via an [Azure VPN gateway and site-to-site IPSec VPN connection][azure-vpn] or [ExpressRoute][azure-er] making this a [hybrid Azure and on-premises scenario][azure-hybrid].</span></span>
* <span data-ttu-id="a184a-149">以内部模式部署 API 管理实例可以保持 API 管理实例的私密性。</span><span class="sxs-lookup"><span data-stu-id="a184a-149">The API Management instance can be kept private by deploying the API Management instance in Internal mode.</span></span> <span data-ttu-id="a184a-150">然后，可以结合 [Azure 应用程序网关][azure-appgw]使用该部署，让某些 API 进行公开访问，同时让其他一些 API 进行内部访问。</span><span class="sxs-lookup"><span data-stu-id="a184a-150">The deployment could then be used with an [Azure Application Gateway][azure-appgw] to enable public access for some APIs while others remain internal.</span></span> <span data-ttu-id="a184a-151">有关详细信息，请参阅[将处于内部模式的 APIM 连接到 VNET][apim-vnet-internal]。</span><span class="sxs-lookup"><span data-stu-id="a184a-151">For more information, see [Connecting APIM in internal mode to a VNET][apim-vnet-internal].</span></span>

> [!NOTE]
> <span data-ttu-id="a184a-152">有关将 API 管理连接到 VNET 的一般信息，请[参阅此文][apim-vnet]。</span><span class="sxs-lookup"><span data-stu-id="a184a-152">For general information on connecting API Management to a VNET, [see here][apim-vnet].</span></span>

### <a name="availability-and-scalability"></a><span data-ttu-id="a184a-153">可用性和可伸缩性</span><span class="sxs-lookup"><span data-stu-id="a184a-153">Availability and scalability</span></span>

* <span data-ttu-id="a184a-154">可以通过选择定价层，然后添加单元，来[横向扩展][apim-scaleout] Azure API 管理。</span><span class="sxs-lookup"><span data-stu-id="a184a-154">Azure API Management can be [scaled out][apim-scaleout] by choosing a pricing tier and then adding units.</span></span>
* <span data-ttu-id="a184a-155">也可以[使用自动缩放][apim-autoscale]来实现自动缩放。</span><span class="sxs-lookup"><span data-stu-id="a184a-155">Scaling also happen [automatically with auto scaling][apim-autoscale].</span></span>
* <span data-ttu-id="a184a-156">[跨多个区域部署][apim-multi-regions]可以实现故障转移，在[高级层][apim-pricing]中可以执行此操作。</span><span class="sxs-lookup"><span data-stu-id="a184a-156">[Deploying across multiple regions][apim-multi-regions] will enable fail over options and can be done in the [Premium tier][apim-pricing].</span></span>
* <span data-ttu-id="a184a-157">考虑[与 Azure Application Insights 集成][azure-apim-ai]，这样还可以通过 [Azure Monitor][azure-mon] 公开指标用于监视。</span><span class="sxs-lookup"><span data-stu-id="a184a-157">Consider [Integrating with Azure Application Insights][azure-apim-ai], which also surfaces metrics through [Azure Monitor][azure-mon] for monitoring.</span></span>

## <a name="deployment"></a><span data-ttu-id="a184a-158">部署</span><span class="sxs-lookup"><span data-stu-id="a184a-158">Deployment</span></span>

<span data-ttu-id="a184a-159">若要开始，请[在门户中创建一个 Azure API 管理实例][apim-create]。</span><span class="sxs-lookup"><span data-stu-id="a184a-159">To get started, [create an Azure API Management instance in the portal.][apim-create]</span></span>

<span data-ttu-id="a184a-160">或者，可以从符合具体用例的现有 Azure 资源管理器[快速入门模板][azure-quickstart-templates-apim]中进行选择。</span><span class="sxs-lookup"><span data-stu-id="a184a-160">Alternatively, you can choose from an existing Azure Resource Manager [quickstart template][azure-quickstart-templates-apim] that aligns to your specific use case.</span></span>

## <a name="pricing"></a><span data-ttu-id="a184a-161">定价</span><span class="sxs-lookup"><span data-stu-id="a184a-161">Pricing</span></span>

<span data-ttu-id="a184a-162">以下四个层中提供了 API 管理：开发人员、基本、标准和高级。</span><span class="sxs-lookup"><span data-stu-id="a184a-162">API Management is offered in four tiers: developer, basic, standard, and premium.</span></span> <span data-ttu-id="a184a-163">在 [Azure API 管理定价指导][apim-pricing]中，可以找到有关这些层的差异的详细指导。</span><span class="sxs-lookup"><span data-stu-id="a184a-163">You can find detailed guidance on the difference in these tiers at the [Azure API Management pricing guidance here.][apim-pricing]</span></span>

<span data-ttu-id="a184a-164">客户可通过添加和删除单元来缩放 API 管理。</span><span class="sxs-lookup"><span data-stu-id="a184a-164">Customers can scale API Management by adding and removing units.</span></span> <span data-ttu-id="a184a-165">每个单元的容量取决于其所在的层。</span><span class="sxs-lookup"><span data-stu-id="a184a-165">Each unit has capacity that depends on its tier.</span></span>

> [!NOTE]
> <span data-ttu-id="a184a-166">“开发人员”层可用于评估 API 管理功能。</span><span class="sxs-lookup"><span data-stu-id="a184a-166">The Developer tier can be used for evaluation of the API Management features.</span></span> <span data-ttu-id="a184a-167">不应在生产环境中使用“开发人员”层。</span><span class="sxs-lookup"><span data-stu-id="a184a-167">The Developer tier should not be used for production.</span></span>

<span data-ttu-id="a184a-168">若要查看预计成本并根据部署需求进行自定义，可在 [Azue 定价计算器][pricing-calculator]中修改缩放单元和应用服务实例的数目。</span><span class="sxs-lookup"><span data-stu-id="a184a-168">To view projected costs and customize to your deployment needs, you can modify the number of scale units and App Service instances in the [Azue Pricing Calculator][pricing-calculator].</span></span>

## <a name="related-resources"></a><span data-ttu-id="a184a-169">相关资源</span><span class="sxs-lookup"><span data-stu-id="a184a-169">Related resources</span></span>

<span data-ttu-id="a184a-170">查看有关 Azure API 管理的丰富[文档和参考文章][apim]。</span><span class="sxs-lookup"><span data-stu-id="a184a-170">Check out the extensive Azure API Management [documentation and reference articles.][apim]</span></span>

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
