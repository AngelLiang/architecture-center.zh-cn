---
title: 将 CI/CD 管道与 Azure DevOps 配合使用
description: 使用 Azure DevOps 生成 .NET 应用并将其发布到 Azure Web 应用。
author: christianreddington
ms.date: 07/11/18
ms.openlocfilehash: 97f16b2d3d9c15bc6f5db6fad4c9d8097243ad3d
ms.sourcegitcommit: 0a31fad9b68d54e2858314ca5fe6cba6c6b95ae4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2018
ms.locfileid: "51610781"
---
# <a name="cicd-pipeline-with-azure-devops"></a><span data-ttu-id="ac8c9-103">将 CI/CD 管道与 Azure DevOps 配合使用</span><span class="sxs-lookup"><span data-stu-id="ac8c9-103">CI/CD pipeline with Azure DevOps</span></span>

<span data-ttu-id="ac8c9-104">DevOps 集成了开发、质量保证和 IT 运营。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-104">DevOps is the integration of development, quality assurance, and IT operations.</span></span> <span data-ttu-id="ac8c9-105">DevOps 要求通过统一的区域性和一系列严格的流程来交付软件。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-105">DevOps requires both unified culture and a strong set of processes for delivering software.</span></span>

<span data-ttu-id="ac8c9-106">本示例方案演示了开发团队如何使用 Azure DevOps 将 .NET 双层 Web 应用程序部署到 Azure 应用服务。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-106">This example scenario demonstrates how development teams can use Azure DevOps to deploy a .NET two-tier web application to Azure App Service.</span></span> <span data-ttu-id="ac8c9-107">此 Web 应用程序依赖于下游的 Azure 平台即服务 (PaaS) 服务。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-107">The Web Application depends on downstream Azure platform as a service (PaaS) services.</span></span> <span data-ttu-id="ac8c9-108">本文档还指出了在使用 Azure PaaS 设计此类方案时应考虑的一些注意事项。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-108">This document also points out some considerations that you should make when designing such a scenario using Azure PaaS.</span></span>

<span data-ttu-id="ac8c9-109">采用现代的方法通过持续集成和持续部署 (CI/CD) 进行应用程序开发，你可以通过可靠的生成、测试、部署和监视服务更快地为用户带来价值。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-109">Adopting a modern approach to application development using Continuous Integration and Continuous Deployment (CI/CD), helps you to accelerate the delivery of value to your users through a robust build, test, deployment, and monitoring service.</span></span> <span data-ttu-id="ac8c9-110">将 Azure DevOps 等平台与应用服务等 Azure 服务配合使用，组织可将工作重心放在开发方案而不是管理支持性基础结构上。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-110">By using a platform such as Azure DevOps along with Azure services such as App Service, organizations can focus on the development of their scenario rather than the management of the supporting infrastructure.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="ac8c9-111">相关用例</span><span class="sxs-lookup"><span data-stu-id="ac8c9-111">Relevant use cases</span></span>

<span data-ttu-id="ac8c9-112">以下用例可以考虑 DevOps：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-112">Consider DevOps for the following use cases:</span></span>

* <span data-ttu-id="ac8c9-113">加速应用程序开发和开发生命周期</span><span class="sxs-lookup"><span data-stu-id="ac8c9-113">Accelerating application development and development life cycles</span></span>
* <span data-ttu-id="ac8c9-114">将质量和一致性管理内置到自动化的生成和发布过程中</span><span class="sxs-lookup"><span data-stu-id="ac8c9-114">Building quality and consistency into an automated build and release process</span></span>

## <a name="architecture"></a><span data-ttu-id="ac8c9-115">体系结构</span><span class="sxs-lookup"><span data-stu-id="ac8c9-115">Architecture</span></span>

![从体系结构的角度概要说明某个使用 Azure DevOps 和 Azure 应用服务的 DevOps 方案中涉及的 Azure 组件][architecture]

<span data-ttu-id="ac8c9-117">此方案包括一个使用 Azure DevOps 的 .NET Web 应用程序 CI/CD 管道。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-117">This scenario covers a CI/CD pipeline for a .NET web application using Azure DevOps.</span></span> <span data-ttu-id="ac8c9-118">数据流经方案的情形如下所示：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-118">The data flows through the scenario as follows:</span></span>

1. <span data-ttu-id="ac8c9-119">更改应用程序源代码。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-119">Change application source code.</span></span>
2. <span data-ttu-id="ac8c9-120">提交应用程序代码和 Web 应用 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-120">Commit application code and Web Apps web.config file.</span></span>
3. <span data-ttu-id="ac8c9-121">持续集成触发应用程序生成和单元测试。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-121">Continuous integration triggers application build and unit tests.</span></span>
4. <span data-ttu-id="ac8c9-122">持续部署触发器使用特定于环境的参数化配置值来协调应用程序项目的部署。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-122">Continuous deployment trigger orchestrates deployment of application artifacts *with environment-specific parameterized configuration values*.</span></span>
5. <span data-ttu-id="ac8c9-123">部署到 Azure 应用服务。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-123">Deployment to Azure App Service.</span></span>
6. <span data-ttu-id="ac8c9-124">Azure Application Insights 收集并分析运行状况、性能和使用情况数据。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-124">Azure Application Insights collects and analyzes health, performance, and usage data.</span></span>
7. <span data-ttu-id="ac8c9-125">查看运行状况、性能和使用情况信息。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-125">Review health, performance, and usage information.</span></span>

### <a name="components"></a><span data-ttu-id="ac8c9-126">组件</span><span class="sxs-lookup"><span data-stu-id="ac8c9-126">Components</span></span>

* <span data-ttu-id="ac8c9-127">[Azure DevOps][vsts] 服务用于管理端到端的开发生命周期 &mdash; 从规划和项目管理，到代码管理，再到生成和发布。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-127">[Azure DevOps][vsts] is a service for managing your development life cycle end-to-end &mdash; from planning and project management, to code management, and continuing to build and release.</span></span>
* <span data-ttu-id="ac8c9-128">[Azure Web 应用][web-apps]是用于托管 Web 应用程序、REST API 和移动后端的 PaaS 服务。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-128">[Azure Web Apps][web-apps] is a PaaS service for hosting web applications, REST APIs, and mobile back ends.</span></span> <span data-ttu-id="ac8c9-129">虽然本文着重讨论 .NET，但系统也支持多个其他的开发平台选项。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-129">While this article focuses on .NET, there are several additional development platform options supported.</span></span>
* <span data-ttu-id="ac8c9-130">[Application Insights][application-insights] 是第一方的可扩展应用程序性能管理 (APM) 服务，适用于多个平台上的 Web 开发人员。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-130">[Application Insights][application-insights] is a first-party, extensible Application Performance Management (APM) service for web developers on multiple platforms.</span></span>

### <a name="alternative-devops-tooling-options"></a><span data-ttu-id="ac8c9-131">替代 DevOps 工具选项</span><span class="sxs-lookup"><span data-stu-id="ac8c9-131">Alternative DevOps tooling options</span></span>

<span data-ttu-id="ac8c9-132">虽然本文将重点放在 Azure DevOps 上，但也可以在本地改用 [Team Foundation Server][team-foundation-server]。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-132">While this article focuses on Azure DevOps, [Team Foundation Server][team-foundation-server] could be used as on-premises substitute.</span></span> <span data-ttu-id="ac8c9-133">或者，也可以使用一组技术来取代使用 [Jenkins][jenkins-on-azure] 的开源开发管道。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-133">Alternatively, you could also use a set of technologies for an open source development pipeline using [Jenkins][jenkins-on-azure].</span></span>

<span data-ttu-id="ac8c9-134">从基础结构即代码的角度看，可将 [Azure 资源管理器模板][arm-templates]包含在 Azure DevOps 项目中，但也可考虑使用 [Terraform][terraform] 或 [Chef][chef]。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-134">From an infrastructure-as-code perspective, [Azure Resource Manager Templates][arm-templates] are included as part of the Azure DevOps project, but you could consider [Terraform][terraform] or [Chef][chef].</span></span> <span data-ttu-id="ac8c9-135">如果首选以基础结构即服务 (IaaS) 为基础的部署并且需要配置管理，可以考虑 [Azure Automation State Configuration][desired-state-configuration]、[Ansible][ansible] 或 [Chef][chef]。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-135">If you prefer an infrastructure-as-a-service (IaaS)-based deployment and require configuration management, you could consider either [Azure Automation State Configuration][desired-state-configuration], [Ansible][ansible], or [Chef][chef].</span></span>

### <a name="alternatives-to-azure-web-apps"></a><span data-ttu-id="ac8c9-136">Azure Web 应用的替代方案</span><span class="sxs-lookup"><span data-stu-id="ac8c9-136">Alternatives to Azure Web Apps</span></span>

<span data-ttu-id="ac8c9-137">可以考虑使用以下替代方案在 Azure Web 应用中进行托管：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-137">You could consider these alternatives to hosting in Azure Web Apps:</span></span>

* <span data-ttu-id="ac8c9-138">[Azure 虚拟机][compare-vm-hosting] &mdash; 所适用的工作负荷需要进行严格的控制或者依赖于 OS 组件和服务，而后者 Web 应用无法提供（例如 Windows GAC 或 COM）。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-138">[Azure Virtual Machines][compare-vm-hosting] &mdash; For workloads that require a high degree of control, or depend on OS components and services that are not possible with Web Apps (for example, the Windows GAC, or COM).</span></span>
* <span data-ttu-id="ac8c9-139">[Service Fabric][service-fabric] &mdash; 如果工作负荷体系结构侧重于分布式组件，而此类组件适合在严格控制的群集上部署和运行，则可使用此选项。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-139">[Service Fabric][service-fabric] &mdash; a good option if the workload architecture is focused around distributed components that benefit from being deployed and run across a cluster with a high degree of control.</span></span> <span data-ttu-id="ac8c9-140">Service Fabric 也可用于托管容器。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-140">Service Fabric can also be used to host containers.</span></span>
* <span data-ttu-id="ac8c9-141">[Azure Functions][azure-functions] - 如果工作负荷体系结构侧重于精细的分布式组件，几乎不需要依赖，只在需要的情况下运行单个组件（不需持续运行），且不需对组件的运行进行协调，则 Azure Functions 是一种有效的无服务器方法。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-141">[Azure Functions][azure-functions] - an effective serverless approach if the workload architecture is centered around fine grained distributed components, requiring minimal dependencies, where individual components are only required to run on demand (not continuously) and orchestration of components is not required.</span></span>

<span data-ttu-id="ac8c9-142">选择适当的迁移路径时，此[决策树](/azure/architecture/guide/technology-choices/compute-decision-tree)可能有所帮助。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-142">This [decision tree](/azure/architecture/guide/technology-choices/compute-decision-tree) may help when choosing the right path to take for a migration.</span></span>

### <a name="devops"></a><span data-ttu-id="ac8c9-143">DevOps</span><span class="sxs-lookup"><span data-stu-id="ac8c9-143">DevOps</span></span>

<span data-ttu-id="ac8c9-144">**[持续集成 (CI)][continuous-integration]** 维护稳定的版本，可让多个开发人员定期提交对共享代码库进行的小规模、经常性的更改。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-144">**[Continuous Integration (CI)][continuous-integration]** maintains a stable build, with multiple developers regularly committing small, frequent changes to the shared codebase.</span></span> <span data-ttu-id="ac8c9-145">在进行持续集成管道操作时，应遵循以下要求：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-145">As part of your continuous integration pipeline, you should:</span></span>
* <span data-ttu-id="ac8c9-146">经常提交小规模的代码更改。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-146">Frequently commit smaller code changes.</span></span> <span data-ttu-id="ac8c9-147">避免分批提交大规模或较复杂的更改，否则可能会使成功合并变得更困难。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-147">Avoid batching up larger or more complex changes that may be more difficult to merge successfully.</span></span>
* <span data-ttu-id="ac8c9-148">使用足够大的代码覆盖率对应用程序组件展开单元测试，包括测试异常路径。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-148">Conduct unit testing of your application components with sufficient code coverage, including testing the unhappy paths.</span></span>
* <span data-ttu-id="ac8c9-149">确保针对共享的主要（或主干）分支运行生成。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-149">Ensure the build is run against the shared master (or trunk) branch.</span></span> <span data-ttu-id="ac8c9-150">此分库应该稳定且始终处于“准备部署”状态。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-150">This branch should be stable and maintained as "deployment ready".</span></span> <span data-ttu-id="ac8c9-151">不完整的或者正在进行的更改应该置于单独的可以频繁进行“前向集成”式合并的分库中，避免以后发生冲突。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-151">Incomplete or work-in-progress changes should be isolated in a separate branch with frequent "forward integration" merges to avoid conflicts later.</span></span>

<span data-ttu-id="ac8c9-152">**[持续交付 (CD)][continuous-delivery]** 不仅要展示稳定的版本，而且要展示稳定的部署。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-152">**[Continuous Delivery (CD)][continuous-delivery]** demonstrates not just a stable build but a stable deployment.</span></span> <span data-ttu-id="ac8c9-153">这样会使得 CD 的实现更为困难一些，既需进行特定于环境的配置，又需通过某个机制来确保这些值设置正确。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-153">This makes realizing CD a little more difficult, requiring environment-specific configuration and a mechanism for setting those values correctly.</span></span> <span data-ttu-id="ac8c9-154">其他 CD 注意事项包括：</span><span class="sxs-lookup"><span data-stu-id="ac8c9-154">Other CD considerations include the following:</span></span>
* <span data-ttu-id="ac8c9-155">需要足够的集成测试覆盖率，以验证是否以端到端的方式正确配置和运行了各个组件。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-155">Sufficient integration testing coverage is required to validate that the various components are configured and working correctly end-to-end.</span></span>
* <span data-ttu-id="ac8c9-156">CD 可能还需要设置和重置特定于环境的数据，以及管理数据库架构版本。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-156">CD may also require setting up and resetting environment-specific data and managing database schema versions.</span></span>
* <span data-ttu-id="ac8c9-157">持续交付还应该扩展到负载测试和用户验收测试环境。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-157">Continuous delivery should also extend to load testing and user acceptance testing environments.</span></span>
* <span data-ttu-id="ac8c9-158">持续交付得益于理想情况下对所有环境进行的持续监视。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-158">Continuous delivery benefits from continuous monitoring, ideally across all environments.</span></span>
* <span data-ttu-id="ac8c9-159">可以编写脚本来创建和配置托管基础结构，以便更轻松地确保跨环境的部署和集成测试的一致性与可靠性。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-159">The consistency and reliability of deployments and integration testing across environments is made easier by scripting the creation and configuration of the hosting infrastructure.</span></span> <span data-ttu-id="ac8c9-160">对于基于云的工作负荷而言，这可以大大简化操作。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-160">This is considerably easier for cloud-based workloads.</span></span> <span data-ttu-id="ac8c9-161">有关详细信息，请参阅[基础结构即代码][infra-as-code]。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-161">For more information, see [Infrastructure as Code][infra-as-code].</span></span>
* <span data-ttu-id="ac8c9-162">在项目生命周期中尽早开始持续交付。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-162">Begin continuous delivery as early as possible in the project lifecycle.</span></span> <span data-ttu-id="ac8c9-163">开始得越晚，整合就越困难。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-163">The later you begin, the more difficult it will be to incorporate.</span></span>
* <span data-ttu-id="ac8c9-164">集成和单元测试的优先级应与应用程序功能相同。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-164">Integration and unit tests should be given the same priority as application features.</span></span>
* <span data-ttu-id="ac8c9-165">使用与环境无关的部署包，在发布过程中管理特定于环境的配置。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-165">Use environment-agnostic deployment packages and manage environment-specific configuration via the release process.</span></span>
* <span data-ttu-id="ac8c9-166">在发布管理工具中保护敏感配置，或者通过在发布过程中调用硬件安全模块 (HSM) 或 [Azure Key Vault][azure-key-vault] 来这样做。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-166">Protect sensitive configuration using the release management tooling, or by calling out to a Hardware-security-module (HSM) or [Azure Key Vault][azure-key-vault] during the release process.</span></span> <span data-ttu-id="ac8c9-167">请勿在源代码管理中存储敏感配置。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-167">Do not store sensitive configuration within source control.</span></span>

<span data-ttu-id="ac8c9-168">**持续学习**。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-168">**Continuous Learning**.</span></span> <span data-ttu-id="ac8c9-169">使用 [Application Insights][application-insights] 等应用程序性能监视 (APM) 工具能够最有效地监视 CD 环境。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-169">The most effective monitoring of a CD environment is provided by application performance monitoring (APM) tools such as [Application Insights][application-insights].</span></span> <span data-ttu-id="ac8c9-170">若要了解 Bug 或负载下的性能，必须针对应用程序工作负荷进行够深入的监视。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-170">Sufficient depth of monitoring for an application workload is critical to understand bugs or performance under load.</span></span> <span data-ttu-id="ac8c9-171">可将 Application Insights 集成到 VSTS 中，以[持续监视 CD 管道][app-insights-cd-monitoring]。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-171">Application Insights can be integrated into VSTS to enable [continuous monitoring of the CD pipeline][app-insights-cd-monitoring].</span></span> <span data-ttu-id="ac8c9-172">可以通过这种方式启用自动进入下一阶段的功能（不需人工干预），或者启用在检测到警报的情况下自动回退的功能。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-172">This could be used to enable automatic progression to the next stage, without human intervention, or rollback if an alert is detected.</span></span>

## <a name="considerations"></a><span data-ttu-id="ac8c9-173">注意事项</span><span class="sxs-lookup"><span data-stu-id="ac8c9-173">Considerations</span></span>

### <a name="availability"></a><span data-ttu-id="ac8c9-174">可用性</span><span class="sxs-lookup"><span data-stu-id="ac8c9-174">Availability</span></span>

<span data-ttu-id="ac8c9-175">考虑在生成云应用程序时，利用[针对可用性的典型设计模式][design-patterns-availability]。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-175">Consider leveraging the [typical design patterns for availability][design-patterns-availability] when building your cloud application.</span></span>

<span data-ttu-id="ac8c9-176">在适当的[应用服务 Web 应用程序参考体系结构][app-service-reference-architecture]中审核可用性注意事项</span><span class="sxs-lookup"><span data-stu-id="ac8c9-176">Review the availability considerations in the appropriate [App Service web application reference architecture][app-service-reference-architecture]</span></span>

<span data-ttu-id="ac8c9-177">若要了解其他可用性主题，请参阅 Azure 体系结构中心的[可用性核对清单][availability]。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-177">For other availability topics, see the [availability checklist][availability] in the Azure Architecture Center.</span></span>

### <a name="scalability"></a><span data-ttu-id="ac8c9-178">可伸缩性</span><span class="sxs-lookup"><span data-stu-id="ac8c9-178">Scalability</span></span>

<span data-ttu-id="ac8c9-179">生成云应用程序时，请弄清楚[针对可伸缩性的典型设计模式][design-patterns-scalability]。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-179">When building a cloud application be aware of the [typical design patterns for scalability][design-patterns-scalability].</span></span>

<span data-ttu-id="ac8c9-180">在适当的[应用服务 Web 应用程序参考体系结构][app-service-reference-architecture]中审核可伸缩性注意事项</span><span class="sxs-lookup"><span data-stu-id="ac8c9-180">Review the scalability considerations in the appropriate [App Service web application reference architecture][app-service-reference-architecture]</span></span>

<span data-ttu-id="ac8c9-181">若要了解其他可伸缩性主题，请参阅 Azure 体系结构中心的[可伸缩性核对清单][scalability]。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-181">For other scalability topics, see the [scalability checklist][scalability] in the Azure Architecture Center.</span></span>

### <a name="security"></a><span data-ttu-id="ac8c9-182">安全</span><span class="sxs-lookup"><span data-stu-id="ac8c9-182">Security</span></span>

<span data-ttu-id="ac8c9-183">考虑在适当情况下利用[针对安全性的典型设计模式][design-patterns-security]。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-183">Consider leveraging the [typical design patterns for security][design-patterns-security] where appropriate.</span></span>

<span data-ttu-id="ac8c9-184">在适当的[应用服务 Web 应用程序参考体系结构][app-service-reference-architecture]中审核安全性注意事项。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-184">Review the security considerations in the appropriate [App Service web application reference architecture][app-service-reference-architecture].</span></span>

<span data-ttu-id="ac8c9-185">若需安全解决方案的通用设计指南，请参阅 [Azure 安全性文档][security]。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-185">For general guidance on designing secure solutions, see the [Azure Security Documentation][security].</span></span>

### <a name="resiliency"></a><span data-ttu-id="ac8c9-186">复原</span><span class="sxs-lookup"><span data-stu-id="ac8c9-186">Resiliency</span></span>

<span data-ttu-id="ac8c9-187">根据情况考虑实施[针对复原的典型设计模式][design-patterns-resiliency]。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-187">Consider implementing the [typical design patterns for resiliency][design-patterns-resiliency] where appropriate.</span></span>

<span data-ttu-id="ac8c9-188">可以在 Azure 体系结构中心找到许多[建议用于应用服务的做法][resiliency-app-service]。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-188">You can find a number of [recommended practices for App Service][resiliency-app-service] in the Azure Architecture Center.</span></span>

<span data-ttu-id="ac8c9-189">若需可复原解决方案的通用设计指南，请参阅[设计适用于 Azure 的可复原应用程序][resiliency]。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-189">For general guidance on designing resilient solutions, see [Designing resilient applications for Azure][resiliency].</span></span>

## <a name="deploy-the-scenario"></a><span data-ttu-id="ac8c9-190">部署方案</span><span class="sxs-lookup"><span data-stu-id="ac8c9-190">Deploy the scenario</span></span>

### <a name="prerequisites"></a><span data-ttu-id="ac8c9-191">先决条件</span><span class="sxs-lookup"><span data-stu-id="ac8c9-191">Prerequisites</span></span>

* <span data-ttu-id="ac8c9-192">必须已经有 Azure 帐户。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-192">You must have an existing Azure account.</span></span> <span data-ttu-id="ac8c9-193">如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户][azure-free-account]。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-193">If you don't have an Azure subscription, create a [free account][azure-free-account] before you begin.</span></span>
* <span data-ttu-id="ac8c9-194">必须注册一个 Azure DevOps 组织。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-194">You must sign up for an Azure DevOps organization.</span></span> <span data-ttu-id="ac8c9-195">有关详细信息，请参阅[快速入门：创建组织][vsts-account-create]。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-195">For more information, see [Quickstart: Create your organization][vsts-account-create].</span></span>

### <a name="walk-through"></a><span data-ttu-id="ac8c9-196">演练</span><span class="sxs-lookup"><span data-stu-id="ac8c9-196">Walk-through</span></span>

<span data-ttu-id="ac8c9-197">在本方案中，我们将使用 Azure DevOps 项目创建 CI/CD 管道。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-197">In this scenario, you'll use the Azure DevOps project to create your CI/CD pipeline.</span></span>

<span data-ttu-id="ac8c9-198">该 Azure DevOps 项目将部署应用服务计划、应用服务以及 App Insights 资源，并配置 Azure DevOps 项目。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-198">The Azure DevOps project will deploy an App Service Plan, App Service, and an App Insights resource for you, as well as configure the Azure DevOps project for you.</span></span>

<span data-ttu-id="ac8c9-199">部署 Azure DevOps 项目并完成生成后，请查看关联的代码更改、工作项和测试结果。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-199">Once you've deployed the Azure DevOps project and the build is completed, review the associated code changes, work items, and test results.</span></span> <span data-ttu-id="ac8c9-200">你会发现，测试结果并未显示，因为代码不包含任何需要运行的测试。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-200">You will notice that no test results are displayed, because the code does not contain any tests to run.</span></span>

<span data-ttu-id="ac8c9-201">查看发布定义。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-201">Review the release definitions.</span></span> <span data-ttu-id="ac8c9-202">可以看到，发布管道已设置好，可将应用程序发布到开发环境中。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-202">Notice that a release pipeline has been set up, releasing our application into the Dev environment.</span></span> <span data-ttu-id="ac8c9-203">请注意，有一个从 **Drop** 生成项目设置的**持续部署触发器**，它可将内容自动发布到开发环境中。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-203">Observe that there is a **continuous deployment trigger** set from the **Drop** build artifact, with automatic releases into the Dev environment.</span></span> <span data-ttu-id="ac8c9-204">在持续部署过程中，可能会看到跨多个环境的发布。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-204">As part of a continuous deployment process, you may see releases that span multiple environments.</span></span> <span data-ttu-id="ac8c9-205">发布可以跨基础结构（使用基础结构即代码之类的技术），还可以部署所需的应用程序包以及任何配置后任务。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-205">A release can span both infrastructure (using techniques such as infrastructure-as-code), and can also deploy the application packages required along with any post-configuration tasks.</span></span>

## <a name="additional-considerations"></a><span data-ttu-id="ac8c9-206">其他注意事项</span><span class="sxs-lookup"><span data-stu-id="ac8c9-206">Additional considerations</span></span>

* <span data-ttu-id="ac8c9-207">考虑利用在 VSTS 市场中提供的一个[词汇切分任务][vsts-tokenization]。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-207">Consider leveraging one of the [tokenization tasks][vsts-tokenization] available in the VSTS marketplace.</span></span>
* <span data-ttu-id="ac8c9-208">考虑通过[部署：Azure Key Vault][download-keyvault-secrets] VSTS 任务将机密从 Azure Key Vault 下载到发布中。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-208">Consider using the [Deploy: Azure Key Vault][download-keyvault-secrets] VSTS task to download secrets from an Azure Key Vault into your release.</span></span> <span data-ttu-id="ac8c9-209">然后可将这些机密用作发布定义中的变量，这样可以避免将其存储在源代码管理中。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-209">You can then use those secrets as variables in your release definition, so you can avoid storing them in source control.</span></span>
* <span data-ttu-id="ac8c9-210">考虑在发布定义中使用[发布变量][vsts-release-variables]来驱动对环境的配置更改。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-210">Consider using [release variables][vsts-release-variables] in your release definitions to drive configuration changes of your environments.</span></span> <span data-ttu-id="ac8c9-211">发布变量的作用域可以是整个发布，也可以是给定的环境。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-211">Release variables can be scoped to an entire release or a given environment.</span></span> <span data-ttu-id="ac8c9-212">将变量用于机密信息时，请确保选择挂锁图标。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-212">When using variables for secret information, ensure that you select the padlock icon.</span></span>
* <span data-ttu-id="ac8c9-213">考虑在发布管道中使用[部署入口][vsts-deployment-gates]。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-213">Consider using [deployment gates][vsts-deployment-gates] in your release pipeline.</span></span> <span data-ttu-id="ac8c9-214">这样就可以利用与外部系统（例如，事件管理系统或其他定制系统）关联的监视数据，以便确定是否应提升某个发布。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-214">This lets you leverage monitoring data in association with external systems (for example, incident management or additional bespoke systems) to determine whether a release should be promoted.</span></span>
* <span data-ttu-id="ac8c9-215">如果需要在发布管道中进行手动干预，请考虑使用[审批][vsts-approvals]功能。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-215">Where manual intervention in a release pipeline is required, consider using the [approvals][vsts-approvals] functionality.</span></span>
* <span data-ttu-id="ac8c9-216">考虑在发布管道中尽早使用 [Application Insights][application-insights] 和其他监视工具。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-216">Consider using [Application Insights][application-insights] and additional monitoring tools as early as possible in your release pipeline.</span></span> <span data-ttu-id="ac8c9-217">许多组织只在生产环境中开始监视；如果监视其他环境，则可以提前在开发过程中识别 Bug，避免生产环境中出现问题。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-217">Many organizations only begin monitoring in their production environment; by monitoring your other environments, you can identify bugs earlier in the development process and avoid issues in your production environment.</span></span>

## <a name="pricing"></a><span data-ttu-id="ac8c9-218">定价</span><span class="sxs-lookup"><span data-stu-id="ac8c9-218">Pricing</span></span>

<span data-ttu-id="ac8c9-219">Azure DevOps 成本取决于组织中需要访问权限的用户数和其他因素，例如，所需的并发生成/发布数，以及测试用户数。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-219">Azure DevOps costs depend on the number of users in your organization that require access, along with other factors like the number of concurrent build/releases required and number of test users.</span></span> <span data-ttu-id="ac8c9-220">有关详细信息，请参阅 [Azure DevOps 定价][vsts-pricing-page]。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-220">For more information, see [Azure DevOps pricing][vsts-pricing-page].</span></span>

* <span data-ttu-id="ac8c9-221">[Azure DevOps][vsts-pricing-calculator] 是可用于管理开发生命周期的服务。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-221">[Azure DevOps][vsts-pricing-calculator] is a service that enables you to manage your development life cycle.</span></span> <span data-ttu-id="ac8c9-222">它按月按用户收费。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-222">It is paid for on a per-user per-month basis.</span></span> <span data-ttu-id="ac8c9-223">可能存在其他费用，具体取决于所需的并发管道数，以及是否有其他测试用户，或者是否使用了基本的用户许可证。</span><span class="sxs-lookup"><span data-stu-id="ac8c9-223">There may be additional charges dependent upon concurrent pipelines needed, in addition to any additional test users or user basic licenses.</span></span>

## <a name="related-resources"></a><span data-ttu-id="ac8c9-224">相关资源</span><span class="sxs-lookup"><span data-stu-id="ac8c9-224">Related resources</span></span>

* <span data-ttu-id="ac8c9-225">[什么是 DevOps？][devops-whatis]</span><span class="sxs-lookup"><span data-stu-id="ac8c9-225">[What is DevOps?][devops-whatis]</span></span>
* <span data-ttu-id="ac8c9-226">[Microsoft 中的 DevOps - 如何使用 Azure DevOps][devops-microsoft]</span><span class="sxs-lookup"><span data-stu-id="ac8c9-226">[DevOps at Microsoft - How we work with Azure DevOps][devops-microsoft]</span></span>
* <span data-ttu-id="ac8c9-227">[分步教程：将 DevOps 与 Azure DevOps 配合使用][devops-with-vsts]</span><span class="sxs-lookup"><span data-stu-id="ac8c9-227">[Step-by-step Tutorials: DevOps with Azure DevOps][devops-with-vsts]</span></span>
* <span data-ttu-id="ac8c9-228">[DevOps 查检表][devops-checklist]</span><span class="sxs-lookup"><span data-stu-id="ac8c9-228">[Devops Checklist][devops-checklist]</span></span>
* <span data-ttu-id="ac8c9-229">[使用 Azure DevOps 项目创建用于 .NET 的 CI/CD 管道][devops-project-create]</span><span class="sxs-lookup"><span data-stu-id="ac8c9-229">[Create a CI/CD pipeline for .NET with the Azure DevOps project][devops-project-create]</span></span>

<!-- links -->
[ansible]: /azure/ansible/
[application-insights]: /azure/application-insights/app-insights-overview
[app-service-reference-architecture]: ../../reference-architectures/app-service-web-app/basic-web-app.md
[azure-free-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[arm-templates]: /azure/azure-resource-manager/resource-group-overview#template-deployment
[architecture]: ./media/architecture-devops-dotnet-webapp.png
[availability]: /azure/architecture/checklist/availability
[chef]: /azure/chef/
[design-patterns-availability]: /azure/architecture/patterns/category/availability
[design-patterns-resiliency]: /azure/architecture/patterns/category/resiliency
[design-patterns-scalability]: /azure/architecture/patterns/category/performance-scalability
[design-patterns-security]: /azure/architecture/patterns/category/security
[desired-state-configuration]: /azure/automation/automation-dsc-overview
[devops-microsoft]: /azure/devops/devops-at-microsoft/
[devops-with-vsts]: https://almvm.azurewebsites.net/labs/vsts/
[devops-checklist]: /azure/architecture/checklist/dev-ops
[application-insights]: https://azure.microsoft.com/services/application-insights/
[cloud-based-load-testing]: https://visualstudio.microsoft.com/team-services/cloud-load-testing/
[cloud-based-load-testing-on-premises]: /vsts/test/load-test/clt-with-private-machines?view=vsts
[jenkins-on-azure]: /azure/jenkins/
[devops-whatis]: /azure/devops/what-is-devops
[download-keyvault-secrets]: /vsts/pipelines/tasks/deploy/azure-key-vault?view=vsts
[resource-groups]: /azure/azure-resource-manager/resource-group-overview
[resiliency-app-service]: /azure/architecture/checklist/resiliency-per-service#app-service
[resiliency]: /azure/architecture/checklist/resiliency
[scalability]: /azure/architecture/checklist/scalability
[vsts]: /vsts/?view=vsts#pivot=services
[continuous-integration]: /azure/devops/what-is-continuous-integration
[continuous-delivery]: /azure/devops/what-is-continuous-delivery
[web-apps]: /azure/app-service/app-service-web-overview
[terraform]: /azure/terraform/
[vsts-account-create]: /azure/devops/organizations/accounts/create-organization-msa-or-work-student?view=vsts
[vsts-approvals]: /vsts/pipelines/release/approvals/approvals?view=vsts
[devops-project]: https://portal.azure.com/?feature.customportal=false#create/Microsoft.AzureProject
[vsts-deployment-gates]: /vsts/pipelines/release/approvals/gates?view=vsts
[vsts-pricing-calculator]: https://azure.com/e/498aa024454445a8a352e75724f900b1
[vsts-pricing-page]: https://azure.microsoft.com/pricing/details/visual-studio-team-services/
[vsts-release-variables]: /vsts/pipelines/release/variables?view=vsts&tabs=batch
[vsts-tokenization]: https://marketplace.visualstudio.com/search?term=token&target=VSTS&category=All%20categories&sortBy=Relevance
[azure-key-vault]: /azure/key-vault/key-vault-overview
[infra-as-code]: https://blogs.msdn.microsoft.com/mvpawardprogram/2018/02/13/infrastructure-as-code/
[team-foundation-server]: https://visualstudio.microsoft.com/tfs/
[infra-as-code]: https://blogs.msdn.microsoft.com/mvpawardprogram/2018/02/13/infrastructure-as-code/
[service-fabric]: /azure/service-fabric/
[azure-functions]: /azure/azure-functions/
[azure-containers]: https://azure.microsoft.com/overview/containers/
[compare-vm-hosting]: /azure/app-service/choose-web-site-cloud-service-vm
[app-insights-cd-monitoring]: /azure/application-insights/app-insights-vsts-continuous-monitoring
[azure-region-pair-bcdr]: /azure/best-practices-availability-paired-regions
[devops-project-create]: /azure/devops-project/azure-devops-project-aspnet-core
[security]: /azure/security/