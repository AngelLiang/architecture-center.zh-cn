---
title: CAF：驱动部署加速的动机和业务风险
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 了解作为云治理战略一部分的部署加速的规则。
author: alexbuckgit
ms.openlocfilehash: 854b1fd420de605a665922e9b207e6aecbfab2f0
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900456"
---
# <a name="deployment-acceleration-motivations-and-business-risks"></a><span data-ttu-id="8f7c9-103">部署加速的动机和业务风险</span><span class="sxs-lookup"><span data-stu-id="8f7c9-103">Deployment Acceleration motivations and business risks</span></span>

<span data-ttu-id="8f7c9-104">本文讨论客户通常在云治理策略中采用部署加速规则的原因。</span><span class="sxs-lookup"><span data-stu-id="8f7c9-104">This article discusses the reasons that customers typically adopt a Deployment Acceleration discipline within a cloud governance strategy.</span></span> <span data-ttu-id="8f7c9-105">此外，它还提供了驱动策略声明的业务风险的几个示例。</span><span class="sxs-lookup"><span data-stu-id="8f7c9-105">It also provides a few examples of business risks that drive policy statements.</span></span>

<!-- markdownlint-disable MD026 -->

## <a name="is-deployment-acceleration-relevant"></a><span data-ttu-id="8f7c9-106">部署加速是否相关？</span><span class="sxs-lookup"><span data-stu-id="8f7c9-106">Is Deployment Acceleration relevant?</span></span>

<span data-ttu-id="8f7c9-107">本地系统通常是使用基准映像或安装脚本来部署的。</span><span class="sxs-lookup"><span data-stu-id="8f7c9-107">On-premises systems are often deployed using baseline images or installation scripts.</span></span> <span data-ttu-id="8f7c9-108">通常还需要额外的配置，这可能涉及多个步骤或人为干预。</span><span class="sxs-lookup"><span data-stu-id="8f7c9-108">Additional configuration is usually necessary, which may involve multiple steps or human intervention.</span></span> <span data-ttu-id="8f7c9-109">这些手动过程很容易出错且经常导致“配置偏差”，需要大量时间进行故障排除和修复任务。</span><span class="sxs-lookup"><span data-stu-id="8f7c9-109">These manual processes are error-prone and often result in "configuration drift", requiring time-consuming troubleshooting and remediation tasks.</span></span>

<span data-ttu-id="8f7c9-110">大部分 Azure 资源都可以通过 Azure 门户来手动部署和配置。</span><span class="sxs-lookup"><span data-stu-id="8f7c9-110">Most Azure resources can be deployed and configured manually via the Azure portal.</span></span> <span data-ttu-id="8f7c9-111">当你只有几个要管理的资源时，此方法或许能够满足你的需求。</span><span class="sxs-lookup"><span data-stu-id="8f7c9-111">This approach may be sufficient for your needs when only have a few resources to manage.</span></span> <span data-ttu-id="8f7c9-112">但是，随着云资产的增长，你的组织应自动执行云资源的部署，以利用 Azure 提供的缩放、故障转移和灾难恢复功能。</span><span class="sxs-lookup"><span data-stu-id="8f7c9-112">However, as your cloud estate grows, your organization should automate the deployment of your cloud resources to take advantage of the scaling, failover, and disaster recovery capabilities that Azure provides.</span></span> <span data-ttu-id="8f7c9-113">采用 DevOps 或 DevSecOps 方法通常是管理部署的最佳方法。</span><span class="sxs-lookup"><span data-stu-id="8f7c9-113">Adopting a DevOps or DevSecOps approach is often the best way to manage your deployments.</span></span>

<span data-ttu-id="8f7c9-114">可靠的部署加速计划可确保正确和一致地部署、更新和配置你的云资源，并始终以这样的方式进行。</span><span class="sxs-lookup"><span data-stu-id="8f7c9-114">A robust Deployment Acceleration plan ensures that your cloud resources are deployed, updated, and configured correctly and consistently, and remain that way.</span></span> <span data-ttu-id="8f7c9-115">部署加速策略的成熟度也是你的[成本管理策略](../cost-management/overview.md)中的一个重要因素。</span><span class="sxs-lookup"><span data-stu-id="8f7c9-115">The maturity of your Deployment Acceleration strategy can also be a significant factor in your [Cost Management strategy](../cost-management/overview.md).</span></span> <span data-ttu-id="8f7c9-116">自动执行云资源的预配和配置，使你能够在需求较低或有时间限制时减少或取消分配资源，这样你只在需要资源时才支付费用。</span><span class="sxs-lookup"><span data-stu-id="8f7c9-116">Automated provisioning and configuration of your cloud resources allows you to scale down or deallocate resources when demand is low or time-bound, so you only pay for resources as you need them.</span></span>

## <a name="business-risk"></a><span data-ttu-id="8f7c9-117">业务风险</span><span class="sxs-lookup"><span data-stu-id="8f7c9-117">Business risk</span></span>

<span data-ttu-id="8f7c9-118">部署加速规则尝试解决以下业务风险。</span><span class="sxs-lookup"><span data-stu-id="8f7c9-118">The Deployment Acceleration discipline attempts to address the following business risks.</span></span> <span data-ttu-id="8f7c9-119">在云采用过程中监视以下各项的相关性：</span><span class="sxs-lookup"><span data-stu-id="8f7c9-119">During cloud adoption, monitor each of the following for relevance:</span></span>

- <span data-ttu-id="8f7c9-120">**服务中断**。</span><span class="sxs-lookup"><span data-stu-id="8f7c9-120">**Service disruption**.</span></span> <span data-ttu-id="8f7c9-121">缺乏可预测的可重复部署进程或无法管理对系统配置的更改，可能会中断正常操作并可导致生产或业务的损失。</span><span class="sxs-lookup"><span data-stu-id="8f7c9-121">Lack of predictable repeatable deployment processes or unmanaged changes to system configurations can disrupt normal operations and can result in lost productivity or lost business.</span></span>
- <span data-ttu-id="8f7c9-122">**成本超支**。</span><span class="sxs-lookup"><span data-stu-id="8f7c9-122">**Cost overruns**.</span></span> <span data-ttu-id="8f7c9-123">在配置系统资源过程中的意外更改，可增加标识问题根本原因的难度，从而增加开发、运营和维护的成本。</span><span class="sxs-lookup"><span data-stu-id="8f7c9-123">Unexpected changes in configuration of system resources can make identifying root cause of issues more difficult, raising the costs of development, operations, and maintenance.</span></span>
- <span data-ttu-id="8f7c9-124">**组织效率低下**。</span><span class="sxs-lookup"><span data-stu-id="8f7c9-124">**Organizational inefficiencies**.</span></span> <span data-ttu-id="8f7c9-125">开发、运营和安全团队之间的沟通障碍为有效采用云技术和开发统一的云治理模型带来巨大挑战。</span><span class="sxs-lookup"><span data-stu-id="8f7c9-125">Barriers between development, operations, and security teams can cause numerous challenges to effective adoption of cloud technologies and the development of a unified cloud governance model.</span></span>

## <a name="next-steps"></a><span data-ttu-id="8f7c9-126">后续步骤</span><span class="sxs-lookup"><span data-stu-id="8f7c9-126">Next steps</span></span>

<span data-ttu-id="8f7c9-127">使用[云管理模板](./template.md)，记录可能由当前云采用计划引入的业务风险。</span><span class="sxs-lookup"><span data-stu-id="8f7c9-127">Using the [Cloud Management Template](./template.md), document business risks that are likely to be introduced by the current cloud adoption plan.</span></span>

<span data-ttu-id="8f7c9-128">确立了对实际业务风险的了解后，下一步是记录业务对风险的容忍度，以及监视该容忍度的指示器和关键指标。</span><span class="sxs-lookup"><span data-stu-id="8f7c9-128">Once an understanding of realistic business risks is established, the next step is to document the business's tolerance for risk and the indicators and key metrics to monitor that tolerance.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="8f7c9-129">指标、指示器和风险容忍度</span><span class="sxs-lookup"><span data-stu-id="8f7c9-129">Metrics, indicators, and risk tolerance</span></span>](./metrics-tolerance.md)
