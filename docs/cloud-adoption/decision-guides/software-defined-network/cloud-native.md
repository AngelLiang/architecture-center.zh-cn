---
title: CAF：软件定义的网络 - 云原生
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 云原生虚拟网络服务的介绍
author: rotycenh
ms.openlocfilehash: c6200491bc9ba35a9f00e0003e51716b58628980
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900437"
---
# <a name="software-defined-networks-cloud-native"></a><span data-ttu-id="0b005-103">软件定义网络：云原生</span><span class="sxs-lookup"><span data-stu-id="0b005-103">Software Defined Networks: Cloud native</span></span>

<span data-ttu-id="0b005-104">在将 IaaS 资源（如虚拟机）部署到云平台时，云原生虚拟网络是必需的。</span><span class="sxs-lookup"><span data-stu-id="0b005-104">A cloud native virtual network is a required when deploying IaaS resources such as virtual machines to a cloud platform.</span></span> <span data-ttu-id="0b005-105">需要明确预配从外部源（类似于 Web）访问虚拟网络。</span><span class="sxs-lookup"><span data-stu-id="0b005-105">Access to virtual networks from external sources, similar to the web, need to be explicitly provisioned.</span></span> <span data-ttu-id="0b005-106">这些类型的虚拟网络支持创建子网、传递规则、虚拟防火墙和流量管理设备。</span><span class="sxs-lookup"><span data-stu-id="0b005-106">These types of virtual networks support the creation of subnets, routing rules, and virtual firewall and traffic management devices.</span></span>

<span data-ttu-id="0b005-107">云原生虚拟网络不依赖于组织的本地资源或其他非云资源来支持云托管的工作负荷。</span><span class="sxs-lookup"><span data-stu-id="0b005-107">A cloud native virtual network has no dependencies on your organization's on-premises or other non-cloud resources to support the cloud-hosted workloads.</span></span> <span data-ttu-id="0b005-108">所有必需的资源都可以在虚拟网络本身中进行预配，也可以使用托管 PaaS 产品/服务进行预配。</span><span class="sxs-lookup"><span data-stu-id="0b005-108">All required resources are provisioned either in the virtual network itself or by using managed PaaS offerings.</span></span>

## <a name="cloud-native-assumptions"></a><span data-ttu-id="0b005-109">云原生假设</span><span class="sxs-lookup"><span data-stu-id="0b005-109">Cloud native assumptions</span></span>

<span data-ttu-id="0b005-110">部署云原生虚拟网络的假设条件如下：</span><span class="sxs-lookup"><span data-stu-id="0b005-110">Deploying a cloud native virtual network assumes the following:</span></span>

- <span data-ttu-id="0b005-111">部署到虚拟网络的工作负荷不依赖于只能从本地网络访问的应用程序或服务。</span><span class="sxs-lookup"><span data-stu-id="0b005-111">The workloads you deploy to the virtual network have no dependencies on applications or services that are accessible only from inside your on-premises network.</span></span> <span data-ttu-id="0b005-112">托管在云平台上的资源无法使用本地托管的应用程序和服务，除非这些应用程序和服务提供可通过公共 Internet 访问的终结点。</span><span class="sxs-lookup"><span data-stu-id="0b005-112">Unless they provide endpoints accessible over the public Internet, applications and services hosted internally on-premises are not usable by resources hosted on a cloud platform.</span></span>
- <span data-ttu-id="0b005-113">工作负荷的标识管理和访问控制取决于云平台的标识服务或云环境中托管的 IaaS 服务器。</span><span class="sxs-lookup"><span data-stu-id="0b005-113">Your workload's identity management and access control depends on the cloud platform's identity services or IaaS servers hosted in your cloud environment.</span></span> <span data-ttu-id="0b005-114">无需直接连接到托管在本地或其他外部位置的标识服务。</span><span class="sxs-lookup"><span data-stu-id="0b005-114">You will not need to directly connect to identity services hosted on-premises or other external locations.</span></span>
- <span data-ttu-id="0b005-115">标识服务不需要支持使用本地目录的单一登录 (SSO)。</span><span class="sxs-lookup"><span data-stu-id="0b005-115">Your identity services do not need to support single sign-on (SSO) with on-premises directories.</span></span>

<span data-ttu-id="0b005-116">云原生虚拟网络没有外部依赖项。</span><span class="sxs-lookup"><span data-stu-id="0b005-116">Cloud native virtual networks have no external dependencies.</span></span> <span data-ttu-id="0b005-117">所以，这些网络易于部署和配置。这样的体系结构通常是试验或其他较小的自包含或快速迭代部署的最佳选择。</span><span class="sxs-lookup"><span data-stu-id="0b005-117">This makes them simple to deploy and configure, and as a result this architecture is often the best choice for experiments or other smaller self-contained or rapidly iterating deployments.</span></span>

<span data-ttu-id="0b005-118">在讨论云原生虚拟网络体系结构时，云采用团队应考虑的其他问题包括：</span><span class="sxs-lookup"><span data-stu-id="0b005-118">Additional issues your Cloud Adoption Team should consider when discussing a cloud native virtual networking architecture include:</span></span>

- <span data-ttu-id="0b005-119">设计为在本地数据中心运行的现有工作负荷可能需要进行大量修改才能利用基于云的功能（例如，存储或身份验证服务）。</span><span class="sxs-lookup"><span data-stu-id="0b005-119">Existing workloads designed to run in an on-premises datacenter may need extensive modification to take advantage of cloud-based functionality, such as storage or authentication services.</span></span>
- <span data-ttu-id="0b005-120">云原生网络只能通过云平台管理工具管理，因此随着时间的推移，可能会导致与现有 IT 标准的管理和策略存在差异。</span><span class="sxs-lookup"><span data-stu-id="0b005-120">Cloud native networks are managed solely through the cloud platform management tools, and therefore may lead to management and policy divergence from your existing IT standards as time goes on.</span></span>

## <a name="learn-more"></a><span data-ttu-id="0b005-121">了解详细信息</span><span class="sxs-lookup"><span data-stu-id="0b005-121">Learn more</span></span>

<span data-ttu-id="0b005-122">有关 Azure 平台中的云原生虚拟网络的详细信息，请参阅以下内容。</span><span class="sxs-lookup"><span data-stu-id="0b005-122">See the following for more information about cloud native virtual networking in the Azure platform.</span></span>

- <span data-ttu-id="0b005-123">[Azure 虚拟网络：操作指南](/azure/virtual-network/virtual-network-vnet-plan-design-arm)。</span><span class="sxs-lookup"><span data-stu-id="0b005-123">[Azure Virtual Network: How-to guides](/azure/virtual-network/virtual-network-vnet-plan-design-arm).</span></span> <span data-ttu-id="0b005-124">默认情况下，新创建的 Azure 虚拟网络为云原生虚拟网络。</span><span class="sxs-lookup"><span data-stu-id="0b005-124">Newly created Azure Virtual Networks are cloud-native by default.</span></span> <span data-ttu-id="0b005-125">使用这些指南来帮助规划虚拟网络的设计和部署。</span><span class="sxs-lookup"><span data-stu-id="0b005-125">Use these guides to help plan the design and deployment of your virtual networks.</span></span>
- <span data-ttu-id="0b005-126">[订阅限制：](/azure/azure-subscription-service-limits?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)网络”。</span><span class="sxs-lookup"><span data-stu-id="0b005-126">[Subscription limits: Networking](/azure/azure-subscription-service-limits?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).</span></span> <span data-ttu-id="0b005-127">任何一个虚拟网络和连接的资源都只能位于一个订阅中，并受订阅限制的约束。</span><span class="sxs-lookup"><span data-stu-id="0b005-127">Any single virtual network and connected resources can only exist within a single subscription, and are bound by subscription limits.</span></span>
