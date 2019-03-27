---
title: CAF：软件定义的网络 - 混合网络
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 讨论混合网络如何允许云虚拟网络连接到本地资源
author: rotycenh
ms.openlocfilehash: 02d181db0ae9baef3b453b8623d212b624f6b16a
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58243358"
---
# <a name="software-defined-networks-hybrid-network"></a><span data-ttu-id="4aa6e-103">软件定义的网络：混合网络</span><span class="sxs-lookup"><span data-stu-id="4aa6e-103">Software Defined Networks: Hybrid network</span></span>

<span data-ttu-id="4aa6e-104">混合云网络体系结构允许虚拟网络访问本地资源和服务，反之亦然，使用专用 WAN 连接（如 ExpressRoute 或其他连接方法）可以直接连接网络。</span><span class="sxs-lookup"><span data-stu-id="4aa6e-104">The hybrid cloud network architecture allows virtual networks to access your on-premises resources and services and vice versa, using a Dedicated WAN connection such as ExpressRoute or other connection method to directly connect the networks.</span></span>

![混合网络](../../../reference-architectures/hybrid-networking/images/expressroute.png)

<span data-ttu-id="4aa6e-106">混合虚拟网络在云原生虚拟网络体系结构基础上构建，它在最初创建时是独立的。</span><span class="sxs-lookup"><span data-stu-id="4aa6e-106">Building on the cloud native virtual network architecture, a hybrid virtual network is isolated when initially created.</span></span> <span data-ttu-id="4aa6e-107">虽然需要显式允许虚拟网络中所有其他以资源为目标的入站流量，但向本地环境添加连接性将授予访问本地网络和通过本地网络访问的权限。</span><span class="sxs-lookup"><span data-stu-id="4aa6e-107">Adding connectivity to the on-premises environment grants access to and from the on-premises network, although all other inbound traffic targeting resources in the virtual network need to be explicitly allowed.</span></span> <span data-ttu-id="4aa6e-108">对于连接的安全性，你可以通过虚拟防火墙设备和路由规则限制访问来实现，也可以使用云原生路由功能或部署网络虚拟设备 (NVA) 来管理流量，从而准确地指定在两个网络之间可以访问的具体服务。</span><span class="sxs-lookup"><span data-stu-id="4aa6e-108">You can secure the connection using virtual firewall devices and routing rules to limit access or you can specify exactly what services can be accessed between the two networks using cloud-native routing features or deploying network virtual appliances (NVAs) to manage traffic.</span></span>

<span data-ttu-id="4aa6e-109">虽然混合网络体系结构支持 VPN 连接，但像 ExpressRoute 这样的专用 WAN 连接通常是首选，因为它具有更高的性能和安全性。</span><span class="sxs-lookup"><span data-stu-id="4aa6e-109">Although the hybrid networking architecture supports VPN connections, dedicated WAN connections like ExpressRoute are generally preferred due to higher performance and increased security.</span></span>

## <a name="hybrid-assumptions"></a><span data-ttu-id="4aa6e-110">混合假设</span><span class="sxs-lookup"><span data-stu-id="4aa6e-110">Hybrid assumptions</span></span>

<span data-ttu-id="4aa6e-111">部署混合虚拟网络的假设条件如下：</span><span class="sxs-lookup"><span data-stu-id="4aa6e-111">Deploying a hybrid virtual network assumes the following:</span></span>

- <span data-ttu-id="4aa6e-112">IT 安全团队已经调整了本地和基于云的网络安全策略，以确保可以信任基于云的虚拟网络，进而与本地系统直接通信。</span><span class="sxs-lookup"><span data-stu-id="4aa6e-112">Your IT security teams have aligned on-premises and cloud-based network security policy to ensure cloud-based virtual networks can be trusted to communicated directly with on-premises systems.</span></span>
- <span data-ttu-id="4aa6e-113">基于云的工作负载需要访问托管在本地或第三方网络上的存储、应用程序和服务，或者驻留在本地的用户或应用程序需要访问云托管的资源。</span><span class="sxs-lookup"><span data-stu-id="4aa6e-113">Your cloud-based workloads require access to storage, applications, and services hosted on your on-premises or third-party networks, or your users or applications in your on-premises need access to cloud-hosted resources.</span></span>
- <span data-ttu-id="4aa6e-114">你需要迁移依赖于本地资源的现有应用程序和服务，但不希望在重新开发上消耗资源来删除这些依赖项。</span><span class="sxs-lookup"><span data-stu-id="4aa6e-114">You need to migrate existing applications and services that depend on on-premises resources, but don't want to expend the resources on redevelopment to remove those dependencies.</span></span>
- <span data-ttu-id="4aa6e-115">在本地网络和云提供商之间实现 VPN 或专用 WAN 连接不受公司策略、法规要求或技术兼容性问题的限制。</span><span class="sxs-lookup"><span data-stu-id="4aa6e-115">Implementing a VPN or dedicated WAN connection between your on-premises networks and cloud provider is not prevented by corporate policy, regulatory requirements, or technical compatibility issues.</span></span>
- <span data-ttu-id="4aa6e-116">工作负载要么不需要多个订阅来绕过订阅资源限制，要么涉及多个订阅，但不需要集中管理分布在多个订阅中的资源所使用的连接或共享服务。</span><span class="sxs-lookup"><span data-stu-id="4aa6e-116">Your workloads either do not require multiple subscriptions to bypass subscription resource limits, OR your workloads involve multiple subscriptions but do not require central management of connectivity or shared services used by resources spread across multiple subscriptions.</span></span>

<span data-ttu-id="4aa6e-117">在考虑实现混合虚拟网络体系结构时，云采用团队应考虑以下问题：</span><span class="sxs-lookup"><span data-stu-id="4aa6e-117">Your Cloud Adoption team should consider the following issues when looking at implementing a hybrid virtual networking architecture:</span></span>

- <span data-ttu-id="4aa6e-118">将本地网络与云网络连接会增加安全需求的复杂性。</span><span class="sxs-lookup"><span data-stu-id="4aa6e-118">Connecting on-premises networks with cloud networks increases the complexity of your security requirements.</span></span> <span data-ttu-id="4aa6e-119">这两个网络都需要保护，以防止来自双方混合环境的外部漏洞和未经授权的访问。</span><span class="sxs-lookup"><span data-stu-id="4aa6e-119">Both networks need to be secured against external vulnerabilities and unauthorized access from both sides of the hybrid environment.</span></span>
- <span data-ttu-id="4aa6e-120">在混合云环境中，扩展工作负载的数量和大小会大大增加路由和流量管理的复杂性。</span><span class="sxs-lookup"><span data-stu-id="4aa6e-120">Scaling the number and size of workloads within a hybrid cloud environment can add significant complexity to routing and traffic management.</span></span>
- <span data-ttu-id="4aa6e-121">需要开发兼容的管理和访问控制策略，以在整个组织中维护一致治理。</span><span class="sxs-lookup"><span data-stu-id="4aa6e-121">You will need to develop compatible management and access control policies to maintain consistent governance throughout your organization.</span></span>

## <a name="learn-more"></a><span data-ttu-id="4aa6e-122">了解详细信息</span><span class="sxs-lookup"><span data-stu-id="4aa6e-122">Learn more</span></span>

<span data-ttu-id="4aa6e-123">有关 Azure 平台中的混合网络的详细信息，请参阅以下内容。</span><span class="sxs-lookup"><span data-stu-id="4aa6e-123">See the following for more information about hybrid networking in the Azure platform.</span></span>

- <span data-ttu-id="4aa6e-124">[混合网络参考体系结构](../../../reference-architectures/hybrid-networking/expressroute.md)。</span><span class="sxs-lookup"><span data-stu-id="4aa6e-124">[Hybrid network reference architecture](../../../reference-architectures/hybrid-networking/expressroute.md).</span></span> <span data-ttu-id="4aa6e-125">Azure 混合虚拟网络使用 ExpressRoute 线路或 Azure VPN 将虚拟网络与组织现有的非 Azure 托管的 IT 资产连接起来。</span><span class="sxs-lookup"><span data-stu-id="4aa6e-125">Azure hybrid virtual networks use either an ExpressRoute circuit or Azure VPN to connect your virtual network with your organization's existing non-Azure hosted IT assets.</span></span> <span data-ttu-id="4aa6e-126">本文讨论了在 Azure 中创建混合网络的选项。</span><span class="sxs-lookup"><span data-stu-id="4aa6e-126">This article discusses the options for creating a hybrid network in Azure.</span></span>
