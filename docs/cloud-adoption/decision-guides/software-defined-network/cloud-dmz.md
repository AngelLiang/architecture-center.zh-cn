---
title: CAF：软件定义网络 - 云 DMZ
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 此网络体系结构允许在本地网络和基于云的网络之间进行有限访问
author: rotycenh
ms.openlocfilehash: a192541dcfb0f3d713f4139a2ab0541d0c7202db
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900543"
---
# <a name="software-defined-networks-cloud-dmz"></a><span data-ttu-id="0a3c2-103">软件定义网络：云外围网络</span><span class="sxs-lookup"><span data-stu-id="0a3c2-103">Software Defined Networks: Cloud DMZ</span></span>

<span data-ttu-id="0a3c2-104">云 DMZ 网络体系结构使用虚拟专用网络 (VPN) 来连接网络，允许在本地网络和基于云的网络之间进行有限访问。</span><span class="sxs-lookup"><span data-stu-id="0a3c2-104">The Cloud DMZ network architecture allows limited access between your on-premises and cloud-based networks, using a virtual private network (VPN) to connect the networks.</span></span> <span data-ttu-id="0a3c2-105">DMZ 在云中部署，可为从基于云的资源访问本地网络提供保护。</span><span class="sxs-lookup"><span data-stu-id="0a3c2-105">A DMZ is deployed in the cloud to secure access to the on-premises network from cloud-based resources.</span></span>

![安全混合网络体系结构](../../../reference-architectures/dmz/images/dmz-private.png)

<span data-ttu-id="0a3c2-107">此体系结构旨在为以下方案提供支持：组织想要开始将基于云的工作负荷与本地工作负荷集成，但可能还没有完全成熟的云安全策略或获得了两个环境之间的安全专用 WAN 连接。</span><span class="sxs-lookup"><span data-stu-id="0a3c2-107">This architecture is designed to support scenarios where your organization wants to start integrating cloud-based workloads with on-premises workloads but may not have fully matured cloud security policies or acquired a secure dedicated WAN connection between the two environments.</span></span> <span data-ttu-id="0a3c2-108">因此，云网络应被视为隔离区，从而确保本地服务的安全。</span><span class="sxs-lookup"><span data-stu-id="0a3c2-108">As a result, cloud networks should be treated like a demilitarized zone to ensure on-premises services are secure.</span></span>

<span data-ttu-id="0a3c2-109">DMZ 部署网络虚拟设备 (NVA) 来实现防火墙和数据包检查等安全功能。</span><span class="sxs-lookup"><span data-stu-id="0a3c2-109">The DMZ deploys network virtual appliances (NVAs) to implement security functionality such as firewalls and packet inspection.</span></span> <span data-ttu-id="0a3c2-110">在本地和基于云的应用程序或服务之间传递的流量必须通过可在其中对这些流量进行审核的 DMZ。</span><span class="sxs-lookup"><span data-stu-id="0a3c2-110">Traffic passing between on-premises and cloud-based applications or services must pass through the DMZ where it can be audited.</span></span> <span data-ttu-id="0a3c2-111">可用于确定允许哪些流量通过 DMZ 网络的 VPN 连接和规则受到 IT 安全团队的严格控制。</span><span class="sxs-lookup"><span data-stu-id="0a3c2-111">VPN connections and the rules determining what traffic is allowed through the DMZ network are strictly controlled by IT security teams.</span></span>

## <a name="cloud-dmz-assumptions"></a><span data-ttu-id="0a3c2-112">云 DMZ 假设</span><span class="sxs-lookup"><span data-stu-id="0a3c2-112">Cloud DMZ assumptions</span></span>

<span data-ttu-id="0a3c2-113">部署云 DMZ 的假设条件如下：</span><span class="sxs-lookup"><span data-stu-id="0a3c2-113">Deploying a Cloud DMZ assumes the following:</span></span>

- <span data-ttu-id="0a3c2-114">安全团队尚未使本地和基于云的安全要求和策略保持完全一致。</span><span class="sxs-lookup"><span data-stu-id="0a3c2-114">Your security teams have not fully aligned on-premises and cloud-based security requirements and policies.</span></span>
- <span data-ttu-id="0a3c2-115">基于云的工作负荷需要对托管在本地或第三方网络上的服务进行有限访问，或本地环境中的用户或应用程序需要对云托管的资源进行有限访问。</span><span class="sxs-lookup"><span data-stu-id="0a3c2-115">Your cloud-based workloads require limited access to services hosted on your on-premises or third-party networks, or your users or applications in your on-premises environment need limited access to cloud-hosted resources.</span></span>
- <span data-ttu-id="0a3c2-116">在本地网络和云服务提供商之间实现 VPN 连接不受公司策略、法规要求或技术兼容性问题的限制。</span><span class="sxs-lookup"><span data-stu-id="0a3c2-116">Implementing a VPN connection between your on-premises networks and cloud provider is not prevented by corporate policy, regulatory requirements, or technical compatibility issues.</span></span>
- <span data-ttu-id="0a3c2-117">工作负荷要么不需要多个订阅来绕过订阅资源限制，要么涉及多个订阅，但不需要集中管理分布在多个订阅中的资源所使用的连接或共享服务。</span><span class="sxs-lookup"><span data-stu-id="0a3c2-117">Your workloads either do not require multiple subscriptions to bypass subscription resource limits, or they involve multiple subscriptions but don't require central management of connectivity or shared services used by resources spread across multiple subscriptions.</span></span>

<span data-ttu-id="0a3c2-118">在考虑实现云 DMZ 虚拟网络体系结构时，云采用团队应考虑以下问题：</span><span class="sxs-lookup"><span data-stu-id="0a3c2-118">Your Cloud Adoption team should consider the following issues when looking at implementing a Cloud DMZ virtual networking architecture:</span></span>

- <span data-ttu-id="0a3c2-119">将本地网络与云网络连接会增加安全需求的复杂性。</span><span class="sxs-lookup"><span data-stu-id="0a3c2-119">Connecting on-premises networks with cloud networks increases the complexity of your security requirements.</span></span> <span data-ttu-id="0a3c2-120">即使云网络与本地环境之间的连接是安全的，仍然需要确保云资源的安全。</span><span class="sxs-lookup"><span data-stu-id="0a3c2-120">Even though the connection between cloud networks and the on-premises environment are secured, you still need to ensure cloud resources are secured.</span></span>
- <span data-ttu-id="0a3c2-121">云 DMZ 体系结构通常用作敲门砖，同时进一步保护连接并在内部和云网络之间实现安全策略的一致性，从而允许更广泛地全面采用混合网络体系结构。</span><span class="sxs-lookup"><span data-stu-id="0a3c2-121">The Cloud DMZ architecture is commonly used as a stepping stone while connectivity is further secured and security policy aligned between on-premises and cloud networks, allowing a broader adoption of a full-scale hybrid networking architecture.</span></span>

## <a name="learn-more"></a><span data-ttu-id="0a3c2-122">了解详细信息</span><span class="sxs-lookup"><span data-stu-id="0a3c2-122">Learn more</span></span>

<span data-ttu-id="0a3c2-123">有关在 Azure 平台中实现云 DMZ 的详细信息，请参阅以下内容。</span><span class="sxs-lookup"><span data-stu-id="0a3c2-123">See the following for more information about the implementing a Cloud DMZ in the Azure platform.</span></span>

- <span data-ttu-id="0a3c2-124">[在 Azure 和本地数据中心之间实现 DMZ](../../../reference-architectures/dmz/secure-vnet-hybrid.md)。</span><span class="sxs-lookup"><span data-stu-id="0a3c2-124">[Implement a DMZ between Azure and your on-premises datacenter](../../../reference-architectures/dmz/secure-vnet-hybrid.md).</span></span> <span data-ttu-id="0a3c2-125">本文讨论如何在 Azure 中实现安全的混合网络体系结构。</span><span class="sxs-lookup"><span data-stu-id="0a3c2-125">This article discusses how to implement a secure hybrid network architecture in Azure.</span></span>
