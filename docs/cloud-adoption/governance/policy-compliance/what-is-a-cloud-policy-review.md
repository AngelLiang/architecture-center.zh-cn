---
title: CAF：什么是云策略评审？
description: 什么是云策略评审？
author: BrianBlanchard
ms.date: 2/8/2019
ms.openlocfilehash: b879f261e16ffc72180417e2e0f533e2eaa435ba
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900580"
---
<!-- markdownlint-disable MD026 -->

# <a name="what-is-a-cloud-policy-review"></a><span data-ttu-id="5fe90-103">什么是云策略评审？</span><span class="sxs-lookup"><span data-stu-id="5fe90-103">What is a cloud policy review?</span></span>

<span data-ttu-id="5fe90-104">云策略审核是云中[治理成熟度](../overview.md)迈出的第一步。</span><span class="sxs-lookup"><span data-stu-id="5fe90-104">A **cloud policy review** is the first step toward [governance maturity](../overview.md) in the cloud.</span></span> <span data-ttu-id="5fe90-105">此过程的目标是使现有的企业 IT 策略现代化。</span><span class="sxs-lookup"><span data-stu-id="5fe90-105">The objective of this process is to modernize existing corporate IT policies.</span></span> <span data-ttu-id="5fe90-106">完成后，更新的策略可为基于云的资源提供同等级别的风险缓解。</span><span class="sxs-lookup"><span data-stu-id="5fe90-106">When completed, the updated policies provide an equivalent level of risk mitigation for cloud-based resources.</span></span> <span data-ttu-id="5fe90-107">本文介绍了云策略评审流程及其重要性。</span><span class="sxs-lookup"><span data-stu-id="5fe90-107">This article explains the cloud policy review process and its importance.</span></span>

## <a name="why-perform-a-cloud-policy-review"></a><span data-ttu-id="5fe90-108">为什么要执行云策略评审？</span><span class="sxs-lookup"><span data-stu-id="5fe90-108">Why perform a cloud policy review?</span></span>

<span data-ttu-id="5fe90-109">大多数企业通过执行与管理策略一致的流程来管理 IT。</span><span class="sxs-lookup"><span data-stu-id="5fe90-109">Most businesses manage IT through the execution of processes which alignment with governing policies.</span></span> <span data-ttu-id="5fe90-110">在小型企业中，这些策略可能是轶事式的，流程可能定义不严格。</span><span class="sxs-lookup"><span data-stu-id="5fe90-110">In small businesses, these policies may anecdotal and processes loosely defined.</span></span> <span data-ttu-id="5fe90-111">随着企业发展成为大型企业，往往会更清晰地记录策略和流程并始终如一地执行。</span><span class="sxs-lookup"><span data-stu-id="5fe90-111">As businesses grow into large enterprises, policies and processes tend to be more clearly documented and consistently executed.</span></span>

<span data-ttu-id="5fe90-112">随着公司企业 IT 策略的成熟，对过去技术策略的依赖倾向于渗透到管理策略中。</span><span class="sxs-lookup"><span data-stu-id="5fe90-112">As companies mature corporate IT policies, dependencies on past technical decisions have a tendency to seep into governing policies.</span></span> <span data-ttu-id="5fe90-113">例如，常见的灾难恢复过程包括强制进行异地磁带备份的策略。</span><span class="sxs-lookup"><span data-stu-id="5fe90-113">For instance, its common to see disaster recovery processes include policy that mandates offsite tape backups.</span></span> <span data-ttu-id="5fe90-114">此包含假定依赖于一种类型的技术（磁带备份），该技术可能不再是最相关的解决方案。</span><span class="sxs-lookup"><span data-stu-id="5fe90-114">This inclusion assumes a dependency on one type of technology (tape backups), that may no longer be the most relevant solution.</span></span>

<span data-ttu-id="5fe90-115">云转换创造了一个自然的拐点，重新考虑过去的遗留策略决策。</span><span class="sxs-lookup"><span data-stu-id="5fe90-115">Cloud Transformations create a natural inflection point to reconsider the legacy policy decisions of the past.</span></span> <span data-ttu-id="5fe90-116">云中的技术功能和默认流程发生了很大变化，继承风险也是如此。</span><span class="sxs-lookup"><span data-stu-id="5fe90-116">Technical capabilities and default processes change considerably in the cloud, as do the inherit risks.</span></span> <span data-ttu-id="5fe90-117">利用前面的示例，磁带备份策略源于单点故障的风险，即将数据保存在一个位置，业务需要通过降低此风险来最小化风险。</span><span class="sxs-lookup"><span data-stu-id="5fe90-117">Leveraging the prior example, the tape backup policy stemmed from the risk of a single point of failure by keeping data in one location and the business need to minimize the risk profile by mitigating this risk.</span></span> <span data-ttu-id="5fe90-118">在云部署中，有几个选项可以提供相同的风险缓解，并且恢复时间目标 (RTO) 要低得多。</span><span class="sxs-lookup"><span data-stu-id="5fe90-118">In a cloud deployment, there are several options that deliver the same risk mitigation, with much lower recovery time objectives (RTO).</span></span> <span data-ttu-id="5fe90-119">例如：</span><span class="sxs-lookup"><span data-stu-id="5fe90-119">For instance:</span></span>

- <span data-ttu-id="5fe90-120">云原生解决方案可以实现 SQL Azure 数据库的异地复制</span><span class="sxs-lookup"><span data-stu-id="5fe90-120">A cloud-native solution could enable geo-replication of the SQL Azure database</span></span>
- <span data-ttu-id="5fe90-121">混合解决方案可以利用 Azure Site Recovery (ASR) 将 IaaS 工作负载复制到多个数据中心</span><span class="sxs-lookup"><span data-stu-id="5fe90-121">A hybrid solution could leverage Azure Site Recovery (ASR) to replicate an IaaS workload to multiple datacenters</span></span>

<span data-ttu-id="5fe90-122">在执行云转换时，策略通常会管理对云采用团队可用的许多工具、服务和流程。</span><span class="sxs-lookup"><span data-stu-id="5fe90-122">When executing a cloud transformation, policies often govern many of the tools, services, and processes available to the cloud adoption teams.</span></span> <span data-ttu-id="5fe90-123">如果这些策略基于传统技术，则可能会阻碍团队推动更改的工作。</span><span class="sxs-lookup"><span data-stu-id="5fe90-123">If those policies are based on legacy technologies, they may hinder the team's efforts to drive change.</span></span> <span data-ttu-id="5fe90-124">在最糟糕的情况下，迁移团队会完全忽略重要的策略来启用变通方案。</span><span class="sxs-lookup"><span data-stu-id="5fe90-124">In the worst case, important policies are entirely ignored by the migration team to enable workarounds.</span></span> <span data-ttu-id="5fe90-125">两者都不是可接受的结果。</span><span class="sxs-lookup"><span data-stu-id="5fe90-125">Neither is an acceptable outcome.</span></span>

## <a name="the-cloud-policy-review-process"></a><span data-ttu-id="5fe90-126">云策略评审流程</span><span class="sxs-lookup"><span data-stu-id="5fe90-126">The cloud policy review process</span></span>

<span data-ttu-id="5fe90-127">云策略评审将现有 IT 治理和 IT 安全策略与[云治理的五个规则](../overview.md)对齐：[成本管理](../cost-management/overview.md)、[安全基线](../security-baseline/overview.md)、[标识基线](../identity-baseline/overview.md)、[资源一致性](../resource-consistency/overview.md)和[部署加速](../deployment-acceleration/overview.md)。</span><span class="sxs-lookup"><span data-stu-id="5fe90-127">Cloud policy reviews align existing IT governance and IT security policies with the [five disciplines of Cloud Governance](../overview.md): [Cost Management](../cost-management/overview.md), [Security Baseline](../security-baseline/overview.md), [Identity Baseline](../identity-baseline/overview.md), [Resource Consistency](../resource-consistency/overview.md), and [Deployment Acceleration](../deployment-acceleration/overview.md).</span></span>

<span data-ttu-id="5fe90-128">对于这些规则中的每一个，评审流程都遵循以下步骤：</span><span class="sxs-lookup"><span data-stu-id="5fe90-128">For each of these disciplines, the review process follows these steps:</span></span>

1. <span data-ttu-id="5fe90-129">查看与特定规则相关的现有本地策略，查找两个关键数据点：遗留依赖项和已识别的业务风险。</span><span class="sxs-lookup"><span data-stu-id="5fe90-129">Review existing on-premises policies related to the specific discipline, looking for two key data points: legacy dependencies and identified business risks.</span></span>
2. <span data-ttu-id="5fe90-130">通过提出一个简单的问题来评估每个业务风险：“云模型中是否仍然存在业务风险？”</span><span class="sxs-lookup"><span data-stu-id="5fe90-130">Evaluate each business risk by asking a simple question: "Does the business risk still exist in a cloud model?"</span></span>
3. <span data-ttu-id="5fe90-131">如果风险仍然存在，请通过记录必要的缓解措施（而不是技术解决方案）重新编写策略。</span><span class="sxs-lookup"><span data-stu-id="5fe90-131">If the risk still exists, re-write the policy by documenting the necessary mitigation, not the technical solution.</span></span>
4. <span data-ttu-id="5fe90-132">与云采用团队一起查看更新的策略，以了解所需缓解的潜在解决方案。</span><span class="sxs-lookup"><span data-stu-id="5fe90-132">Review the updated policy with the cloud adoption teams to understand potential solutions to the required mitigation.</span></span>

## <a name="example-of-a-policy-review-for-a-legacy-policy"></a><span data-ttu-id="5fe90-133">遗留策略的策略评审示例</span><span class="sxs-lookup"><span data-stu-id="5fe90-133">Example of a policy review for a legacy policy</span></span>

<span data-ttu-id="5fe90-134">要提供该过程的示例，让我们再次使用前一节中的磁带备份策略：</span><span class="sxs-lookup"><span data-stu-id="5fe90-134">To provide an example of the process, let's again use the tape backup policy in the prior section:</span></span>

- <span data-ttu-id="5fe90-135">公司策略要求对所有生产系统强制进行异地磁带备份。</span><span class="sxs-lookup"><span data-stu-id="5fe90-135">A corporate policy mandates offsite tape backups for all production systems.</span></span> <span data-ttu-id="5fe90-136">在此策略中，可以看到两个感兴趣的数据点：</span><span class="sxs-lookup"><span data-stu-id="5fe90-136">In this policy, you can see two data points of interest:</span></span>
  - <span data-ttu-id="5fe90-137">对磁带备份解决方案的遗留依赖</span><span class="sxs-lookup"><span data-stu-id="5fe90-137">Legacy dependency on a tape backup solution</span></span>
  - <span data-ttu-id="5fe90-138">与在生产设备相同的物理位置存储备份相关的假定业务风险。</span><span class="sxs-lookup"><span data-stu-id="5fe90-138">An assumed business risk associated with the storage of backups in the same physical location as the production equipment.</span></span>
- <span data-ttu-id="5fe90-139">风险是否仍然存在？</span><span class="sxs-lookup"><span data-stu-id="5fe90-139">Does the risk still exist?</span></span> <span data-ttu-id="5fe90-140">是的。</span><span class="sxs-lookup"><span data-stu-id="5fe90-140">Yes.</span></span> <span data-ttu-id="5fe90-141">即使在云中，依赖单一设施确实会产生一些风险。</span><span class="sxs-lookup"><span data-stu-id="5fe90-141">Even in the cloud, a dependence on a single facility does create some risk.</span></span> <span data-ttu-id="5fe90-142">此风险影响业务的可能性低于本地解决方案中存在的可能性，但风险仍然存在。</span><span class="sxs-lookup"><span data-stu-id="5fe90-142">There is a lower probability of this risk affecting the business than was present in the on-premises solution, but the risk still exists.</span></span>
- <span data-ttu-id="5fe90-143">重写策略。</span><span class="sxs-lookup"><span data-stu-id="5fe90-143">Rewrite of the policy.</span></span> <span data-ttu-id="5fe90-144">对于数据中心范围内的灾难，必须存在一种在停机 24 小时内在不同的数据中心和不同的地理位置恢复生产系统的方法。</span><span class="sxs-lookup"><span data-stu-id="5fe90-144">In the case of a datacenter-wide disaster, there must exist a means of restoring production systems within 24 hours of the outage in a different datacenter and different geographic location.</span></span>
- <span data-ttu-id="5fe90-145">与云采用团队一起评审。</span><span class="sxs-lookup"><span data-stu-id="5fe90-145">Review with the cloud adoption teams.</span></span> <span data-ttu-id="5fe90-146">根据所实施的解决方案，有多种方法可以遵守此资源一致性策略。</span><span class="sxs-lookup"><span data-stu-id="5fe90-146">Depending on the solution being implemented, there are multiple means of adhering to this Resource Consistency policy.</span></span>

## <a name="tools-to-help-create-modern-policies"></a><span data-ttu-id="5fe90-147">帮助制定现代策略的工具</span><span class="sxs-lookup"><span data-stu-id="5fe90-147">Tools to help create modern policies</span></span>

<span data-ttu-id="5fe90-148">为了帮助加速制定现代策略，云治理的五个规则中的每一个都提供了一系列示例策略。</span><span class="sxs-lookup"><span data-stu-id="5fe90-148">To help accelerate the creation of modern policies, a set of sample policies is available in each of the five disciplines of Cloud Governance.</span></span> <span data-ttu-id="5fe90-149">这些示例策略将从三个设计假设之一开始：</span><span class="sxs-lookup"><span data-stu-id="5fe90-149">Those sample policies will each start with one of three design assumptions:</span></span>

- <span data-ttu-id="5fe90-150">**云原生**：部署的解决方案是云原生的，可以利用 Azure 中的默认解决方案，只需最少的配置。</span><span class="sxs-lookup"><span data-stu-id="5fe90-150">**Cloud Native**: The solution being deployed is cloud native and can capitalize on default solutions found in Azure, with minimal configuration.</span></span>
- <span data-ttu-id="5fe90-151">**企业版**：部署的解决方案很复杂，需要混合云部署模型。</span><span class="sxs-lookup"><span data-stu-id="5fe90-151">**Enterprise**: The solution being deployed is complex and requires a hybrid cloud deployment model.</span></span> <span data-ttu-id="5fe90-152">这需要对某些治理规则进行更复杂的实施。</span><span class="sxs-lookup"><span data-stu-id="5fe90-152">This necessitates more complex implementations of certain governance disciplines.</span></span>
- <span data-ttu-id="5fe90-153">**符合云设计原则 (CDP)**：部署的解决方案必须遵循 CDP 中定义的体系结构轴，这需要更高级别的治理。</span><span class="sxs-lookup"><span data-stu-id="5fe90-153">**Cloud design principle (CDP) compliant**: The solution being deployed must adhere to the architecture axes defined in CDP, requiring a much higher degree of governance.</span></span>  

<span data-ttu-id="5fe90-154">对于每个规则，需要在每个级别创建示例策略。</span><span class="sxs-lookup"><span data-stu-id="5fe90-154">For each discipline, a sample policy needs to be created at each of these levels.</span></span> <span data-ttu-id="5fe90-155">每个示例都旨在触发企业环境中的想法和对话。</span><span class="sxs-lookup"><span data-stu-id="5fe90-155">Each sample is meant to trigger thoughts and conversations inside the corporate environment.</span></span> <span data-ttu-id="5fe90-156">请注意，这些示例不能用作正确构建的公司 IT 策略的替代方案。</span><span class="sxs-lookup"><span data-stu-id="5fe90-156">Note that these samples are not intended to be used as an alternative to a properly constructed corporate IT policy.</span></span>