---
title: CAF：架构决策指南
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 了解云采用框架中的架构决策指南。
author: rotycenh
ms.openlocfilehash: b43047b5fc5b636bc84b9f28ec24846730e63672
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900615"
---
# <a name="architectural-decision-guides"></a><span data-ttu-id="485c3-103">架构决策指南</span><span class="sxs-lookup"><span data-stu-id="485c3-103">Architectural decision guides</span></span>

<span data-ttu-id="485c3-104">云采用框架中的架构决策指南介绍了在创建云治理设计指南时可以提供帮助的模式和模型。</span><span class="sxs-lookup"><span data-stu-id="485c3-104">The architectural decision guides in the Cloud Adoption Framework describe patterns and models that help when creating cloud governance design guidance.</span></span> <span data-ttu-id="485c3-105">每个决策指南侧重于云部署的一个核心基础结构组件，并列出用于支持特定云部署场景的潜在模式或模型。</span><span class="sxs-lookup"><span data-stu-id="485c3-105">Each decision guide focuses on one core infrastructure component of cloud deployments and lists potential patterns or models intended to support specific cloud deployment scenarios.</span></span>

<span data-ttu-id="485c3-106">当开始为组织建立云治理时，可操作的治理过程将提供一个基线路线图。</span><span class="sxs-lookup"><span data-stu-id="485c3-106">When you begin to establish cloud governance for your organization,  actionable governance journeys provide a baseline roadmap.</span></span> <span data-ttu-id="485c3-107">然而，这些过程做出有关需求和优先级的假设，但这些假设可能并不能反映组织的需求和优先级。</span><span class="sxs-lookup"><span data-stu-id="485c3-107">However, these journeys make assumptions about requirements and priorities that may not reflect those of your organization.</span></span>
<span data-ttu-id="485c3-108">这些决策指南通过提供可选的模式和模型来补充示例治理过程，这些模式和模型可有助于使示例设计指南中所做的架构设计选择符合自己的需求。</span><span class="sxs-lookup"><span data-stu-id="485c3-108">These decision guides supplement the sample governance journeys by providing alternative patterns and models that help you align the architectural design choices made in the example design guidance with your own requirements.</span></span>

## <a name="design-guidance-categories"></a><span data-ttu-id="485c3-109">设计指南类别</span><span class="sxs-lookup"><span data-stu-id="485c3-109">Design guidance categories</span></span>

<span data-ttu-id="485c3-110">以下每个类别分别代表所有云部署的一项基础技术。</span><span class="sxs-lookup"><span data-stu-id="485c3-110">Each of the following categories represents a foundational technology of all cloud deployments.</span></span> <span data-ttu-id="485c3-111">对于示例设计过程中与需求不匹配的每个选择，请检查下面相关部分中的选项，以选择更适合你需求的模式或模型。</span><span class="sxs-lookup"><span data-stu-id="485c3-111">For each choice in the example design journeys that doesn’t match your requirements, examine the options in the relevant section below to choose a pattern or model better suited to your needs.</span></span>

<span data-ttu-id="485c3-112">[订阅](./subscriptions/overview.md)：规划云部署的订阅设计和帐户结构，以匹配组织的所有权、计费和管理功能。</span><span class="sxs-lookup"><span data-stu-id="485c3-112">[Subscriptions](./subscriptions/overview.md): Plan your cloud deployment's subscription design and account structure to match your organization's ownership, billing, and management capabilities.</span></span>

<span data-ttu-id="485c3-113">[标识](./identity/overview.md)：将基于云的标识服务与现有标识资源集成，以支持 IT 环境中的访问控制。</span><span class="sxs-lookup"><span data-stu-id="485c3-113">[Identity](./identity/overview.md): Integrate cloud-based identity services with your existing identity resources to support access control within your IT environment.</span></span>

<span data-ttu-id="485c3-114">[策略强制实施](./policy-enforcement/overview.md)：为部署到云的资源和工作负载定义和强制实施组织策略规则。</span><span class="sxs-lookup"><span data-stu-id="485c3-114">[Policy Enforcement](./policy-enforcement/overview.md): Define and enforce organizational policy rules for resources and workloads that you deploy to the cloud.</span></span>

<span data-ttu-id="485c3-115">[资源一致性](./resource-consistency/overview.md)：确保基于云的资源的部署和组织一致，以实现资源管理和策略需求。</span><span class="sxs-lookup"><span data-stu-id="485c3-115">[Resource Consistency](./resource-consistency/overview.md): Ensure that deployment and organization of your cloud-based resources align to enforce resource management and policy requirements.</span></span>

<span data-ttu-id="485c3-116">[资源标记](./resource-tagging/overview.md)：组织基于云的资源以支持计费模型、云核算方法、管理、访问控制和运营效率。</span><span class="sxs-lookup"><span data-stu-id="485c3-116">[Resource Tagging](./resource-tagging/overview.md): Organize your cloud-based resources to support billing models, cloud accounting approaches, management, access control, and operational efficiency.</span></span> <span data-ttu-id="485c3-117">资源标记需要一致的、有条理的命名和元数据方案。</span><span class="sxs-lookup"><span data-stu-id="485c3-117">Resource tagging requires a consistent and well-organized naming and metadata scheme.</span></span>

<span data-ttu-id="485c3-118">[软件定义的网络](./software-defined-network/overview.md)：使用虚拟网络功能的快速部署和修改，将安全工作负载部署到云中。</span><span class="sxs-lookup"><span data-stu-id="485c3-118">[Software Defined Networks](./software-defined-network/overview.md): Deploy secure workloads to the cloud using rapid deployment and modification of virtualized networking capabilities.</span></span> <span data-ttu-id="485c3-119">软件定义的网络 (SDN) 可以支持敏捷工作流、隔离资源，并将基于云的系统与现有 IT 基础结构集成。</span><span class="sxs-lookup"><span data-stu-id="485c3-119">Software-defined networks (SDNs) can support agile workflows, isolate resources, and integrate cloud-based systems with your existing IT infrastructure.</span></span>

<span data-ttu-id="485c3-120">[加密](./encryption/overview.md)：使用加密保护敏感数据，这是云部署中的一个重要安全方面。</span><span class="sxs-lookup"><span data-stu-id="485c3-120">[Encryption](./encryption/overview.md): Secure your sensitive data using encryption, an important aspect of security within a cloud deployment.</span></span>

<span data-ttu-id="485c3-121">[日志和报告](./log-and-report/overview.md)：监视由基于云的资源生成的日志数据。</span><span class="sxs-lookup"><span data-stu-id="485c3-121">[Logs and Reporting](./log-and-report/overview.md): Monitor log data generated by cloud-based resources.</span></span> <span data-ttu-id="485c3-122">分析数据为工作负载的操作、维护和策略强制实施状态提供了与运行状况相关的见解。</span><span class="sxs-lookup"><span data-stu-id="485c3-122">Analyzing data provides health-related insights into the operations, maintenance, and policy enforcement status of workloads.</span></span>

## <a name="next-steps"></a><span data-ttu-id="485c3-123">后续步骤</span><span class="sxs-lookup"><span data-stu-id="485c3-123">Next steps</span></span>

<span data-ttu-id="485c3-124">了解订阅和帐户如何作为云部署的基础。</span><span class="sxs-lookup"><span data-stu-id="485c3-124">Learn how subscriptions and accounts serve as the base of a cloud deployment.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="485c3-125">订阅设计</span><span class="sxs-lookup"><span data-stu-id="485c3-125">Subscriptions design</span></span>](subscriptions/overview.md)
