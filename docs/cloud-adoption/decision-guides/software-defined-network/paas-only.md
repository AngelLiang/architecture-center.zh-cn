---
title: CAF：软件定义网络 - 仅限 PaaS
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 讨论基于云的网络功能的仅限 PaaS 模型
author: rotycenh
ms.openlocfilehash: 2f3f82d781ddb6544721e82e7b7d795222a2f8ff
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241198"
---
# <a name="software-defined-networks-paas-only"></a><span data-ttu-id="291f2-103">软件定义网络：仅限 PaaS</span><span class="sxs-lookup"><span data-stu-id="291f2-103">Software Defined Networks: PaaS-only</span></span>

<span data-ttu-id="291f2-104">实现平台即服务 (PaaS) 资源时，部署过程会自动创建一个假设的底层网络，该网络上的控制数量有限，包括负载均衡、端口阻止以及与其他 PaaS 服务的连接。</span><span class="sxs-lookup"><span data-stu-id="291f2-104">When you implement a platform as a service (PaaS) resource, the deployment process automatically creates an assumed underlying network with a limited number of controls over that network, including load balancing, port blocking, and connections to other PaaS services.</span></span>

<span data-ttu-id="291f2-105">在 Azure 中，可以将多个 PaaS 资源类型[部署到](/azure/virtual-network/virtual-network-for-azure-services)或[连接到](/azure/virtual-network/virtual-network-service-endpoints-overview)虚拟网络中，从而允许这些资源与现有的虚拟网络基础结构集成。</span><span class="sxs-lookup"><span data-stu-id="291f2-105">In Azure, several PaaS resource types can be [deployed into](/azure/virtual-network/virtual-network-for-azure-services) or [connected to](/azure/virtual-network/virtual-network-service-endpoints-overview) a virtual network, allowing these resources to integrate with your existing virtual networking infrastructure.</span></span> <span data-ttu-id="291f2-106">但是，在许多情况下，仅依赖于 PaaS 资源本身提供的这些默认网络功能的仅限 PaaS 的网络体系结构足以满足工作负载要求。</span><span class="sxs-lookup"><span data-stu-id="291f2-106">However, in many cases a PaaS only networking architecture, relying only on these default networking capabilities natively provided by PaaS resources, is sufficient to meet workload requirements.</span></span>

<span data-ttu-id="291f2-107">如果你正在考虑仅限 PaaS 的网络体系结构，请确保验证所需的假设符合要求。</span><span class="sxs-lookup"><span data-stu-id="291f2-107">If you are considering a PaaS only networking architecture, be sure you validate that the required assumptions align with your requirements.</span></span>

## <a name="paas-only-assumptions"></a><span data-ttu-id="291f2-108">仅限 PaaS 的假设</span><span class="sxs-lookup"><span data-stu-id="291f2-108">PaaS-only assumptions</span></span>

<span data-ttu-id="291f2-109">部署仅限 PaaS 的网络体系结构的前提是：</span><span class="sxs-lookup"><span data-stu-id="291f2-109">Deploying a PaaS-only networking architecture assumes the following:</span></span>

- <span data-ttu-id="291f2-110">部署的应用程序是独立应用程序，或仅依赖于其他 PaaS 资源。</span><span class="sxs-lookup"><span data-stu-id="291f2-110">The application being deployed is a standalone application OR is dependent on only other PaaS resources.</span></span>
- <span data-ttu-id="291f2-111">IT 运营团队可以更新其工具、培训和流程，以支持独立 PaaS 应用程序的管理、配置和部署。</span><span class="sxs-lookup"><span data-stu-id="291f2-111">Your IT operations teams can update their tools, training, and processes to support management, configuration, and deployment of standalone PaaS applications.</span></span>
- <span data-ttu-id="291f2-112">PaaS 应用程序不是包含 IaaS 资源的更广泛的云迁移工作的一部分。</span><span class="sxs-lookup"><span data-stu-id="291f2-112">The PaaS application is not part of a broader cloud migration effort that will include IaaS resources.</span></span>

<span data-ttu-id="291f2-113">这些假设是与部署仅限 PaaS 的网络一致的最低限定符。</span><span class="sxs-lookup"><span data-stu-id="291f2-113">These assumptions are minimum qualifiers aligned to deploying a PaaS-only network.</span></span> <span data-ttu-id="291f2-114">虽然此方法可能符合单个应用程序部署的要求，但云采用团队应研究这些长期问题：</span><span class="sxs-lookup"><span data-stu-id="291f2-114">While this approach may align with the requirements of a single application deployment, your Cloud Adoption Team should examine these long-term questions:</span></span>

- <span data-ttu-id="291f2-115">这种部署是否会扩大范围或规模，以要求访问其他非 PaaS 资源？</span><span class="sxs-lookup"><span data-stu-id="291f2-115">Will this deployment expand in scope or scale to require access to other non-PaaS resources?</span></span>
- <span data-ttu-id="291f2-116">是否计划在当前解决方案之外部署其他 PaaS？</span><span class="sxs-lookup"><span data-stu-id="291f2-116">Are other PaaS deployments planned beyond the current solution?</span></span>
- <span data-ttu-id="291f2-117">组织是否有计划进行其他未来的云迁移？</span><span class="sxs-lookup"><span data-stu-id="291f2-117">Does the organization have plans for other future cloud migrations?</span></span>

<span data-ttu-id="291f2-118">这些问题的答案不会妨碍团队选择仅限 PaaS 选项，但在做出最终决定之前应予以考虑。</span><span class="sxs-lookup"><span data-stu-id="291f2-118">The answers to these questions would not preclude a team from choosing a PaaS only option but should be considered before making a final decision.</span></span>
