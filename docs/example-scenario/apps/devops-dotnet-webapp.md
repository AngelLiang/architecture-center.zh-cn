---
title: 将 CI/CD 管道与 VSTS 配合使用
description: 举例说明如何生成 .NET 应用并将其发布到 Azure Web 应用
author: christianreddington
ms.date: 07/11/18
ms.openlocfilehash: ae4ac5fc02cc841fc39b3cbef46124fe9da75e9b
ms.sourcegitcommit: 71cbef121c40ef36e2d6e3a088cb85c4260599b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2018
ms.locfileid: "39061008"
---
# <a name="cicd-pipeline-with-vsts"></a><span data-ttu-id="c4050-103">将 CI/CD 管道与 VSTS 配合使用</span><span class="sxs-lookup"><span data-stu-id="c4050-103">CI/CD pipeline with VSTS</span></span>

<span data-ttu-id="c4050-104">DevOps 集成了开发、质量保证和 IT 运营。</span><span class="sxs-lookup"><span data-stu-id="c4050-104">DevOps is the integration of development, quality assurance and IT operations.</span></span> <span data-ttu-id="c4050-105">DevOps 要求通过统一的区域性和一系列严格的流程来交付软件。</span><span class="sxs-lookup"><span data-stu-id="c4050-105">DevOps requires both unified culture and a strong set of processes for delivering software.</span></span>

<span data-ttu-id="c4050-106">本示例方案演示了开发团队如何使用 Visual Studio Team Services 将 .NET 双层 Web 应用程序部署到 Azure 应用服务。</span><span class="sxs-lookup"><span data-stu-id="c4050-106">This example scenario demonstrates how development teams can use Visual Studio Team Services to deploy a .NET two-tier web application to Azure App Service.</span></span> <span data-ttu-id="c4050-107">此 Web 应用程序依赖于下游的 Azure 平台即服务 (PaaS) 服务。</span><span class="sxs-lookup"><span data-stu-id="c4050-107">The Web Application dependends on downstream Azure Platform as a Service (PaaS) services.</span></span> <span data-ttu-id="c4050-108">本文档还指出了在使用 Azure 平台即服务 (PaaS) 设计此类方案时应考虑的一些注意事项。</span><span class="sxs-lookup"><span data-stu-id="c4050-108">This document also points out some considerations that you should make when designing such a scenario using Azure Platform as a Service (PaaS).</span></span>

<span data-ttu-id="c4050-109">采用现代的方法通过持续集成 (CI) 和持续部署 (CD) 进行应用程序开发，你可以通过可靠的生成、测试、部署和监视服务更快地为用户带来价值。</span><span class="sxs-lookup"><span data-stu-id="c4050-109">Adopting a modern approach to application development using Continuous Integration (CI) and Continuous Deployment (CD), helps you to accelerate the delivery of value to your users through a robust build, test, deployment and monitoring service.</span></span> <span data-ttu-id="c4050-110">使用 Visual Studio Team Services 之类的平台和应用服务之类的 Azure 服务，组织就可以确保只关注方案的开发，不必管理启用这些方案所需的基础结构。</span><span class="sxs-lookup"><span data-stu-id="c4050-110">By using a platform such as Visual Studio Team Services in addition to Azure services such as App Service, organizations can ensure they remain focused on the development of their scenario, rather than the management of the infrastructure to enable it.</span></span>

## <a name="related-use-cases"></a><span data-ttu-id="c4050-111">相关的用例</span><span class="sxs-lookup"><span data-stu-id="c4050-111">Related use cases</span></span>

<span data-ttu-id="c4050-112">以下用例可以考虑 DevOps：</span><span class="sxs-lookup"><span data-stu-id="c4050-112">Consider DevOps for the following use cases:</span></span>

* <span data-ttu-id="c4050-113">缩短应用程序开发和部署生命周期</span><span class="sxs-lookup"><span data-stu-id="c4050-113">Speeding up application development and development life cycles</span></span>
* <span data-ttu-id="c4050-114">将质量和一致性管理内置到自动化的生成和发布过程中</span><span class="sxs-lookup"><span data-stu-id="c4050-114">Building quality and consistency into an automated build and release process</span></span>

## <a name="architecture"></a><span data-ttu-id="c4050-115">体系结构</span><span class="sxs-lookup"><span data-stu-id="c4050-115">Architecture</span></span>

![从体系结构的角度概要说明某个使用 Visual Studio Team Services 和 Azure 应用服务的 DevOps 方案中涉及的 Azure 组件][architecture]

<span data-ttu-id="c4050-117">本方案介绍的 DevOps 管道针对一个使用 Visual Studio Team Services (VSTS) 的 .NET Web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="c4050-117">This scenario covers a DevOps pipeline for a .NET web application using Visual Studio Team Services (VSTS).</span></span> <span data-ttu-id="c4050-118">数据流经方案的情形如下所示：</span><span class="sxs-lookup"><span data-stu-id="c4050-118">The data flows through the scenario as follows:</span></span>

1. <span data-ttu-id="c4050-119">更改应用程序源代码。</span><span class="sxs-lookup"><span data-stu-id="c4050-119">Change application source code.</span></span>
2. <span data-ttu-id="c4050-120">提交应用程序代码和 Web 应用 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="c4050-120">Commit application code and Web Apps web.config file.</span></span>
3. <span data-ttu-id="c4050-121">持续集成触发应用程序生成和单元测试。</span><span class="sxs-lookup"><span data-stu-id="c4050-121">Continuous integration triggers application build and unit tests.</span></span>
4. <span data-ttu-id="c4050-122">持续部署触发器使用特定于环境的参数化配置值来协调应用程序项目的部署。</span><span class="sxs-lookup"><span data-stu-id="c4050-122">Continuous deployment trigger orchestrates deployment of application artifacts *with environment-specific parameterized configuration values*.</span></span>
5. <span data-ttu-id="c4050-123">部署到 Azure 应用服务。</span><span class="sxs-lookup"><span data-stu-id="c4050-123">Deployment to Azure App Service.</span></span>
6. <span data-ttu-id="c4050-124">Azure Application Insights 收集并分析运行状况、性能和使用情况数据。</span><span class="sxs-lookup"><span data-stu-id="c4050-124">Azure Application Insights collects and analyzes health, performance, and usage data.</span></span>
7. <span data-ttu-id="c4050-125">查看运行状况、性能和使用情况信息。</span><span class="sxs-lookup"><span data-stu-id="c4050-125">Review health, performance, and usage information.</span></span>

### <a name="components"></a><span data-ttu-id="c4050-126">组件</span><span class="sxs-lookup"><span data-stu-id="c4050-126">Components</span></span>

* <span data-ttu-id="c4050-127">[资源组][resource-groups]是 Azure 资源的逻辑容器，此外还为管理平面提供一个访问控制边界 - 可以将资源组视为“部署单元”的体现。</span><span class="sxs-lookup"><span data-stu-id="c4050-127">[Resource Groups][resource-groups] are a logical container for Azure resources and also provide an access control boundary for the management plane - think of a Resource Group as representing a "unit of deployment".</span></span>
* <span data-ttu-id="c4050-128">[Visual Studio Team Services (VSTS)][vsts] 是一项服务，用于对开发生命周期进行端到端管理，包括规划和项目管理、代码管理以及生成和发布管理。</span><span class="sxs-lookup"><span data-stu-id="c4050-128">[Visual Studio Team Services (VSTS)][vsts] is a service that enables you to manage your development life cycle end-to-end; from planning and project management, to code management, through to build and release.</span></span>
* <span data-ttu-id="c4050-129">[Azure Web 应用][web-apps]是一种平台即服务 (PaaS) 服务，用于托管 Web 应用程序、REST API 和移动后端。</span><span class="sxs-lookup"><span data-stu-id="c4050-129">[Azure Web Apps][web-apps] is a Platform as a Service (PaaS) service for hosting web applications, REST APIs, and mobile back ends.</span></span> <span data-ttu-id="c4050-130">虽然本文着重讨论 .NET，但系统也支持多个其他的开发平台选项。</span><span class="sxs-lookup"><span data-stu-id="c4050-130">While this article focuses on .NET, there are several additional development platform options supported.</span></span>
* <span data-ttu-id="c4050-131">[Application Insights][application-insights] 是第一方的可扩展应用程序性能管理 (APM) 服务，适用于多个平台上的 Web 开发人员。</span><span class="sxs-lookup"><span data-stu-id="c4050-131">[Application Insights][application-insights] is a first-party, extensible Application Performance Management (APM) service for web developers on multiple platforms.</span></span>

### <a name="alternative-devops-tooling-options"></a><span data-ttu-id="c4050-132">替代 DevOps 工具选项</span><span class="sxs-lookup"><span data-stu-id="c4050-132">Alternative DevOps tooling options</span></span>

<span data-ttu-id="c4050-133">虽然本文重点介绍 Visual Studio Team Services，但也可在本地使用 [Team Foundation Server][team-foundation-server] 作为替代。</span><span class="sxs-lookup"><span data-stu-id="c4050-133">Whilst this article focuses on Visual Studio Team Services, [Team Foundation Server][team-foundation-server] could be used as on premises substitute.</span></span> <span data-ttu-id="c4050-134">另外，也可将一系列技术一起用于某个利用 [Jenkins][jenkins-on-azure] 的开源开发管道。</span><span class="sxs-lookup"><span data-stu-id="c4050-134">Alternatively, you may also find a collection of technologies being used together for an Open Source development pipeline leveraging [Jenkins][jenkins-on-azure].</span></span>

<span data-ttu-id="c4050-135">从基础结构即代码角度来看，可将 [Azure 资源管理器 (ARM) 模板][arm-templates]包括在 Azure DevOps 项目中，但也可考虑使用 [Terraform][terraform] 或 [Chef][chef]（如果在这里有投资）。</span><span class="sxs-lookup"><span data-stu-id="c4050-135">From an Infrastructure as Code perspective, [Azure Resource Manager (ARM) Templates][arm-templates] are included as part of the Azure DevOps project, but you could consider [Terraform][terraform] or [Chef][chef] if you have investments here.</span></span> <span data-ttu-id="c4050-136">如果首选以基础结构即服务 (IaaS) 为基础的部署并且需要配置管理，则可考虑 [Azure Desired State Configuration][desired-state-configuration]、[Ansible][ansible] 或 [Chef][chef]。</span><span class="sxs-lookup"><span data-stu-id="c4050-136">If you prefer an Infrastructure as a Service (IaaS) based deployment and require configuration management, then you could consider either [Azure Desired State Configuration][desired-state-configuration], [Ansible][ansible] or [Chef][chef].</span></span>

### <a name="alternatives-to-web-app-hosting"></a><span data-ttu-id="c4050-137">Web 应用托管的替代方式</span><span class="sxs-lookup"><span data-stu-id="c4050-137">Alternatives to Web App Hosting</span></span>

<span data-ttu-id="c4050-138">以下方式可以替代在 Azure Web 应用中进行的托管：</span><span class="sxs-lookup"><span data-stu-id="c4050-138">Alternatives to hosting in Azure Web Apps:</span></span>

* <span data-ttu-id="c4050-139">[VM][compare-vm-hosting] - 适用于需要进行严格控制的工作负荷，或者所适用的工作负荷依赖于 OS 组件/服务，而后者 Web 应用无法提供（例如 Windows GAC 或 COM）</span><span class="sxs-lookup"><span data-stu-id="c4050-139">[VM][compare-vm-hosting] - For workloads that require a high degree of control, or depend on OS components / services that are not possible with Web Apps (e.g. the Windows GAC, or COM)</span></span>
* <span data-ttu-id="c4050-140">[容器托管][azure-containers] - 适用于依赖 OS 以及对托管可移植性或托管密度也有要求的情况。</span><span class="sxs-lookup"><span data-stu-id="c4050-140">[Container Hosting][azure-containers] - Where there are OS dependencies and hosting portability, or hosting density, are also requirements.</span></span>
* <span data-ttu-id="c4050-141">[Service Fabric][service-fabric] - 如果工作负荷体系结构侧重于分布式组件，而此类组件适合在严格控制的群集上部署和运行，则可使用此选项。</span><span class="sxs-lookup"><span data-stu-id="c4050-141">[Service Fabric][service-fabric] - A good option if the workload architecture is focused around distributed components that benefit from being deployed and run across a cluster with a high degree of control.</span></span> <span data-ttu-id="c4050-142">Service Fabric 也可用于托管容器。</span><span class="sxs-lookup"><span data-stu-id="c4050-142">Service Fabric can also be used to host containers.</span></span>
* <span data-ttu-id="c4050-143">[无服务器 Azure Functions][azure-functions] - 如果工作负荷体系结构侧重于精细的分布式组件，几乎不需要依赖，只在需要的情况下运行单个组件（不需持续运行），且不需对组件的运行进行协调，则可使用此选项。</span><span class="sxs-lookup"><span data-stu-id="c4050-143">[Serverless Azure functions][azure-functions] - A good option if the workload architecture is centered around fine grained distributed components, requiring minimal dependencies, where individual components are only required to run on demand (not continuously) and orchestration of components is not required.</span></span>

### <a name="devops"></a><span data-ttu-id="c4050-144">DevOps</span><span class="sxs-lookup"><span data-stu-id="c4050-144">DevOps</span></span>

<span data-ttu-id="c4050-145">**[持续集成 (CI)][continuous-integration]** 的目标应该是体现稳定生成的优势，即允许多个单独的开发人员或团队持续向共享的代码库提交小的频繁进行的更改。</span><span class="sxs-lookup"><span data-stu-id="c4050-145">**[Continuous Integration (CI)][continuous-integration]** should aim to demonstrate a stable build, with more than one individual developer or team continuously committing small, frequent changes to the shared codebase.</span></span>
<span data-ttu-id="c4050-146">在进行持续集成管道操作时，应遵循以下要求：</span><span class="sxs-lookup"><span data-stu-id="c4050-146">As part of your Continuous Integration pipeline you should;</span></span>

* <span data-ttu-id="c4050-147">频繁签入少量代码（避免批量签入较大的或较复杂的更改，因为这样会更难合并成功）</span><span class="sxs-lookup"><span data-stu-id="c4050-147">Check in small amounts of code frequently (avoid batching up larger or more complex changes as these can be harder to merge successfully)</span></span>
* <span data-ttu-id="c4050-148">对应用程序组件进行单元测试时，需涵盖足够多的代码（包括异常路径）</span><span class="sxs-lookup"><span data-stu-id="c4050-148">Unit Test the components of your application with sufficient code coverage (including the unhappy paths)</span></span>
* <span data-ttu-id="c4050-149">确保针对共享的主分库运行生成。</span><span class="sxs-lookup"><span data-stu-id="c4050-149">Ensuring the build is run against the shared, master (or trunk) branch.</span></span> <span data-ttu-id="c4050-150">此分库应该稳定且始终处于“准备部署”状态。</span><span class="sxs-lookup"><span data-stu-id="c4050-150">This branch should be stable and maintained as "deployment ready".</span></span> <span data-ttu-id="c4050-151">不完整的或者正在进行的更改应该置于单独的可以频繁进行“前向集成”式合并的分库中，避免以后发生冲突。</span><span class="sxs-lookup"><span data-stu-id="c4050-151">Incomplete or work-in-progress changes should be isolated in a separate branch with frequent 'forward integration' merges to avoid conflicts later.</span></span>

<span data-ttu-id="c4050-152">**[持续交付 (CD)][continuous-delivery]** 的目标应该是体现稳定生成和稳定部署的优势。</span><span class="sxs-lookup"><span data-stu-id="c4050-152">**[Continuous Delivery (CD)][continuous-delivery]** should aim to demonstrate not only a stable build but a stable deployment.</span></span> <span data-ttu-id="c4050-153">这样会使得 CD 的实现更为困难一些，既需进行特定于环境的配置，又需通过某个机制来确保这些值设置正确。</span><span class="sxs-lookup"><span data-stu-id="c4050-153">This makes realising CD a little more difficult, environment specific configuration is required and a mechanism for setting those values correctly.</span></span>

<span data-ttu-id="c4050-154">另外，集成测试还需要有足够的涵盖面，确保以端到端的方式正确配置和运行各种组件。</span><span class="sxs-lookup"><span data-stu-id="c4050-154">In addition, sufficient coverage of Integration Testing is required to ensure that the various components are configured and working correctly end-to-end.</span></span>

<span data-ttu-id="c4050-155">这可能还需要设置和重置特定于环境的数据，以及管理数据库架构版本。</span><span class="sxs-lookup"><span data-stu-id="c4050-155">This may also require setting up and resetting environment specific data and managing database schema versions.</span></span>

<span data-ttu-id="c4050-156">持续交付可能还会扩展到负载测试和用户验收测试环境。</span><span class="sxs-lookup"><span data-stu-id="c4050-156">Continuous Delivery may also extend to Load Testing and User Acceptance Testing Environments.</span></span>

<span data-ttu-id="c4050-157">持续交付得益于理想情况下对所有环境进行的持续监视。</span><span class="sxs-lookup"><span data-stu-id="c4050-157">Continuous Delivery benefits from continuous Monitoring, ideally across all environments.</span></span>
<span data-ttu-id="c4050-158">可以针对创建和配置基础结构或托管基础结构来编写脚本，这样就可以更容易地确保跨环境进行的部署和集成测试的一致性和可靠性（对于基于云的工作负荷来说，这要更容易得多，详见“Azure 基础结构即代码”）- 这也称为[“基础结构即代码”][infra-as-code]。</span><span class="sxs-lookup"><span data-stu-id="c4050-158">The consistency and reliability of deployments and integration testing across environments is made easier by scripting the creation and configuration or the hosting infrastructure (something which is considerably easier for Cloud based workloads, see Azure infrastructure as code) - this is also known as ["infrastructure-as-code"][infra-as-code].</span></span>

* <span data-ttu-id="c4050-159">在项目生命周期中尽早启动持续交付。</span><span class="sxs-lookup"><span data-stu-id="c4050-159">Start Continuous Delivery as early as possible in the project life-cycle.</span></span> <span data-ttu-id="c4050-160">越晚越困难。</span><span class="sxs-lookup"><span data-stu-id="c4050-160">The later you leave it, the more difficult it will be.</span></span>
* <span data-ttu-id="c4050-161">作为项目功能，集成和单元测试的优先级应该是一样的。</span><span class="sxs-lookup"><span data-stu-id="c4050-161">Integration & unit tests should be given the same priority as the project features</span></span>
* <span data-ttu-id="c4050-162">使用与环境无关的部署包，在发布过程中管理特定于环境的配置。</span><span class="sxs-lookup"><span data-stu-id="c4050-162">Use environment agnostic deployment packages and manage environment specific configuration through the release process.</span></span>
* <span data-ttu-id="c4050-163">在发布管理工具中保护敏感配置，或者通过在发布过程中调用硬件安全模块 (HSM) 或 [Key Vault][azure-key-vault] 来这样做。</span><span class="sxs-lookup"><span data-stu-id="c4050-163">Protect sensitive configuration within the release management tooling or by calling out to a Hardware-security-module (HSM), or [Key Vault][azure-key-vault], during the release process.</span></span> <span data-ttu-id="c4050-164">请勿在源代码管理中存储敏感配置。</span><span class="sxs-lookup"><span data-stu-id="c4050-164">Do not store sensitive configuration within source control.</span></span>

<span data-ttu-id="c4050-165">**持续学习** - 对 CD 环境进行的最有效监视由应用程序性能监视（简称 APM）工具（例如 Microsoft 的 [Application Insights][application-insights]）提供。</span><span class="sxs-lookup"><span data-stu-id="c4050-165">**Continuous Learning** - The most effective monitoring of a CD environment is provided by Application-Performance-Monitoring tools (APM for short), for example Microsoft's [Application Insights][application-insights].</span></span> <span data-ttu-id="c4050-166">若要了解 Bug 和负载下的性能，必须针对应用程序工作负荷进行够深入的监视。</span><span class="sxs-lookup"><span data-stu-id="c4050-166">Sufficient depth of monitoring for an application workload is critical to understand bugs, performance under load.</span></span> <span data-ttu-id="c4050-167">[可以将 App Insights 集成到 VSTS 中以持续监视 CD 管道][app-insights-cd-monitoring]。</span><span class="sxs-lookup"><span data-stu-id="c4050-167">[App Insights can be integrated into VSTS to enable continuous monitoring of the CD pipeline][app-insights-cd-monitoring].</span></span> <span data-ttu-id="c4050-168">可以通过这种方式启用自动进入下一阶段的功能（不需人工干预），或者启用在检测到警报的情况下自动回退的功能。</span><span class="sxs-lookup"><span data-stu-id="c4050-168">This could be used to enable automatic progression to the next stage, without human intervention, or rollback if an alert is detected.</span></span>

## <a name="considerations"></a><span data-ttu-id="c4050-169">注意事项</span><span class="sxs-lookup"><span data-stu-id="c4050-169">Considerations</span></span>

### <a name="availability"></a><span data-ttu-id="c4050-170">可用性</span><span class="sxs-lookup"><span data-stu-id="c4050-170">Availability</span></span>

<span data-ttu-id="c4050-171">考虑在生成云应用程序时，利用[针对可用性的典型设计模式][design-patterns-availability]。</span><span class="sxs-lookup"><span data-stu-id="c4050-171">Consider leveraging the [typical design patterns for availability][design-patterns-availability] when building your cloud application.</span></span>

<span data-ttu-id="c4050-172">在适当的[应用服务 Web 应用程序参考体系结构][app-service-reference-architecture]中审核可用性注意事项</span><span class="sxs-lookup"><span data-stu-id="c4050-172">Review the availability considerations in the appropriate [App Service web application reference architecture][app-service-reference-architecture]</span></span>

<span data-ttu-id="c4050-173">若要了解其他可用性主题，请参阅 Azure 体系结构中心的[可用性核对清单][availability]。</span><span class="sxs-lookup"><span data-stu-id="c4050-173">For other availability topics, see the [availability checklist][availability] in the Azure Architecure Center.</span></span>

### <a name="scalability"></a><span data-ttu-id="c4050-174">可伸缩性</span><span class="sxs-lookup"><span data-stu-id="c4050-174">Scalability</span></span>

<span data-ttu-id="c4050-175">生成云应用程序时，请弄清楚[针对可伸缩性的典型设计模式][design-patterns-scalability]。</span><span class="sxs-lookup"><span data-stu-id="c4050-175">When building a cloud application be aware of the [typical design patterns for scalability][design-patterns-scalability].</span></span>

<span data-ttu-id="c4050-176">在适当的[应用服务 Web 应用程序参考体系结构][app-service-reference-architecture]中审核可伸缩性注意事项</span><span class="sxs-lookup"><span data-stu-id="c4050-176">Review the scalability considerations in the appropriate [App Service web application reference architecture][app-service-reference-architecture]</span></span>

<span data-ttu-id="c4050-177">若要了解其他可伸缩性主题，请参阅 Azure 体系结构中心的[可伸缩性核对清单][scalability]。</span><span class="sxs-lookup"><span data-stu-id="c4050-177">For other scalability topics, see the [scalability checklist][scalability] in the Azure Architecure Center.</span></span>

### <a name="security"></a><span data-ttu-id="c4050-178">“安全”</span><span class="sxs-lookup"><span data-stu-id="c4050-178">Security</span></span>

<span data-ttu-id="c4050-179">考虑在适当情况下利用[针对安全性的典型设计模式][design-patterns-security]。</span><span class="sxs-lookup"><span data-stu-id="c4050-179">Consider leveraging the [typical design patterns for security][design-patterns-security] where appropriate.</span></span>

<span data-ttu-id="c4050-180">在适当的[应用服务 Web 应用程序参考体系结构][app-service-reference-architecture]中审核安全性注意事项。</span><span class="sxs-lookup"><span data-stu-id="c4050-180">Review the security considerations in the appropriate [App Service web application reference architecture][app-service-reference-architecture].</span></span>

<span data-ttu-id="c4050-181">若需安全解决方案的通用设计指南，请参阅 [Azure 安全性文档][security]。</span><span class="sxs-lookup"><span data-stu-id="c4050-181">For general guidance on designing secure solutions, see the [Azure Security Documentation][security].</span></span>

### <a name="resiliency"></a><span data-ttu-id="c4050-182">复原</span><span class="sxs-lookup"><span data-stu-id="c4050-182">Resiliency</span></span>

<span data-ttu-id="c4050-183">审核[针对复原的典型设计模式][design-patterns-resiliency]，考虑在适当情况下实施这些模式。</span><span class="sxs-lookup"><span data-stu-id="c4050-183">Review the [typical design patterns for resiliency][design-patterns-resiliency] and consider implementing these where appropriate.</span></span>

<span data-ttu-id="c4050-184">可以在体系结构中心找到许多[建议用于应用服务的复原做法][resiliency-app-service]。</span><span class="sxs-lookup"><span data-stu-id="c4050-184">You can find a number of [resiliency recommended practices for App Service][resiliency-app-service] on the architecture center.</span></span>

<span data-ttu-id="c4050-185">若需可复原解决方案的通用设计指南，请参阅[设计适用于 Azure 的可复原应用程序][resiliency]。</span><span class="sxs-lookup"><span data-stu-id="c4050-185">For general guidance on designing resilient solutions, see [Designing resilient applications for Azure][resiliency].</span></span>

## <a name="deploy-the-scenario"></a><span data-ttu-id="c4050-186">部署方案</span><span class="sxs-lookup"><span data-stu-id="c4050-186">Deploy the scenario</span></span>

### <a name="prerequisites"></a><span data-ttu-id="c4050-187">先决条件</span><span class="sxs-lookup"><span data-stu-id="c4050-187">Prerequisites</span></span>

* <span data-ttu-id="c4050-188">必须已经有 Azure 帐户。</span><span class="sxs-lookup"><span data-stu-id="c4050-188">You must have an existing Azure account.</span></span> <span data-ttu-id="c4050-189">如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户][azure-free-account]。</span><span class="sxs-lookup"><span data-stu-id="c4050-189">If you don't have an Azure subscription, create a [free account][azure-free-account] before you begin.</span></span>
* <span data-ttu-id="c4050-190">必须有一个现成的 Visual Studio Team Services (VSTS) 帐户。</span><span class="sxs-lookup"><span data-stu-id="c4050-190">You must have an existing Visual Studio Team Services (VSTS) account.</span></span> <span data-ttu-id="c4050-191">请了解有关如何[创建 Visual Studio Team Services (VSTS) 帐户][vsts-account-create]的更多详细信息。</span><span class="sxs-lookup"><span data-stu-id="c4050-191">Find out more details about [creating a Visual Studio Team Services (VSTS) account][vsts-account-create].</span></span>

### <a name="walk-through"></a><span data-ttu-id="c4050-192">演练</span><span class="sxs-lookup"><span data-stu-id="c4050-192">Walk through</span></span>

<span data-ttu-id="c4050-193">在本方案中，需使用 Azure DevOps 项目创建 CI/CD 管道。</span><span class="sxs-lookup"><span data-stu-id="c4050-193">In this scenario you'll use the Azure DevOps Project to create your CI/CD pipeline.</span></span>

<span data-ttu-id="c4050-194">DevOps 项目会部署应用服务计划、应用服务以及 App Insights 资源，并配置 Visual Studio Team Services 项目。</span><span class="sxs-lookup"><span data-stu-id="c4050-194">The DevOps project will deploy an App Service Plan, App Service and an App Insights resource for you, as well as configure the Visual Studio Team Services Project for you.</span></span>

<span data-ttu-id="c4050-195">有了 DevOps 项目并完成生成以后，请查看相关联的代码更改、工作项和测试结果。</span><span class="sxs-lookup"><span data-stu-id="c4050-195">Once you've  the DevOps project and the build is completed, review the associated code changes, work items and test results.</span></span> <span data-ttu-id="c4050-196">你会注意到系统未显示测试结果，因为代码不包含任何需要运行的测试。</span><span class="sxs-lookup"><span data-stu-id="c4050-196">You will notice no test results are displayed, as the code does not contain any tests to run.</span></span>

<span data-ttu-id="c4050-197">查看发布定义。</span><span class="sxs-lookup"><span data-stu-id="c4050-197">Review the Release definitions.</span></span> <span data-ttu-id="c4050-198">请注意，发布管道已设置好，可以将应用程序发布到开发环境中。</span><span class="sxs-lookup"><span data-stu-id="c4050-198">Notice that a release pipeline has been setup, releasing our application into Dev.</span></span> <span data-ttu-id="c4050-199">请注意，有一个从 **Drop** 生成项目设置的**持续部署触发器**，可以将内容自动发布到开发环境中。</span><span class="sxs-lookup"><span data-stu-id="c4050-199">Observe that there is a **continuous deployment trigger** set from the **Drop** build artifact, with automatic releases into the Dev environments.</span></span> <span data-ttu-id="c4050-200">在持续部署过程中，可能会看到跨多个环境的发布。</span><span class="sxs-lookup"><span data-stu-id="c4050-200">As part of a Continuous Deployment process, you may see releases span across multiple environments.</span></span> <span data-ttu-id="c4050-201">发布可以跨基础结构（使用基础结构即代码之类的技术），还可以部署所需的应用程序包以及任何配置后任务。</span><span class="sxs-lookup"><span data-stu-id="c4050-201">A release can span both infrastructure (using techniques such as Infrastructure as Code), and also deploy the application packages required as well as any post-configuration tasks.</span></span>

<span data-ttu-id="c4050-202">**其他注意事项。**</span><span class="sxs-lookup"><span data-stu-id="c4050-202">**Additional Considerations.**</span></span>

* <span data-ttu-id="c4050-203">考虑利用在 VSTS 市场中提供的一个[词汇切分任务][vsts-tokenization]。</span><span class="sxs-lookup"><span data-stu-id="c4050-203">Consider leveraging one of the [tokenization tasks][vsts-tokenization] that are available in the VSTS marketplace.</span></span>
* <span data-ttu-id="c4050-204">考虑通过[部署：Azure Key Vault][download-keyvault-secrets] VSTS 任务将机密从 Azure KeyVault 下载到发布中。</span><span class="sxs-lookup"><span data-stu-id="c4050-204">Consider using the [Deploy: Azure Key Vault][download-keyvault-secrets] VSTS task to download secrets from an Azure KeyVault into your release.</span></span> <span data-ttu-id="c4050-205">然后即可将这些机密用作发布定义中的变量，但不应将其存储在源代码管理中。</span><span class="sxs-lookup"><span data-stu-id="c4050-205">You can then use those secrets as variables as part of your release definition, and should not be storing them in source control.</span></span>
* <span data-ttu-id="c4050-206">考虑在发布定义中使用[发布变量][vsts-release-variables]来驱动对环境的配置更改。</span><span class="sxs-lookup"><span data-stu-id="c4050-206">Consider using [release variables][vsts-release-variables] in your release definitions to drive configuration changes of your environments.</span></span> <span data-ttu-id="c4050-207">发布变量的作用域可以是整个发布，也可以是给定的环境。</span><span class="sxs-lookup"><span data-stu-id="c4050-207">Release variables can be scoped to an entire release or a given environment.</span></span> <span data-ttu-id="c4050-208">如果将变量用于机密信息，请确保选择挂锁图标。</span><span class="sxs-lookup"><span data-stu-id="c4050-208">If using variables for secret information, ensure that you select the padlock icon.</span></span>
* <span data-ttu-id="c4050-209">考虑在发布管道中使用[部署入口][vsts-deployment-gates]。</span><span class="sxs-lookup"><span data-stu-id="c4050-209">Consider using [deployment gates][vsts-deployment-gates] in your release pipeline.</span></span> <span data-ttu-id="c4050-210">这样就可以利用与外部系统（例如，事件管理系统或其他定制系统）关联的监视数据，以便确定是否应提升某个发布。</span><span class="sxs-lookup"><span data-stu-id="c4050-210">This allows you to leverage monitoring data in association with external systems (e.g. incident management or additional bespoke systems) to determine whether a release should be promoted.</span></span>
* <span data-ttu-id="c4050-211">如果需要在发布管道中进行手动干预，请考虑使用[审批][vsts-approvals]功能。</span><span class="sxs-lookup"><span data-stu-id="c4050-211">Where manual intervention in a release pipeline is required, consider using the [approvals][vsts-approvals] functionality.</span></span>
* <span data-ttu-id="c4050-212">考虑在发布管道中尽早使用 [Application Insights][application-insights] 和其他监视工具。</span><span class="sxs-lookup"><span data-stu-id="c4050-212">Consider using [Application Insights][application-insights] and additional monitoring tooling as early as possible in your release pipeline.</span></span> <span data-ttu-id="c4050-213">大多数组织只在生产环境中开启监视功能，不过你可以早一点在这个过程中确定可能的 Bug，避免对生产环境中的用户造成影响。</span><span class="sxs-lookup"><span data-stu-id="c4050-213">Most organizations only begin monitoring in their production environment, though you could identify potential bugs earlier in the process and prevent impact to your users in production.</span></span>

## <a name="pricing"></a><span data-ttu-id="c4050-214">定价</span><span class="sxs-lookup"><span data-stu-id="c4050-214">Pricing</span></span>

<span data-ttu-id="c4050-215">Visual Studio Team Services 成本计算取决于组织中需要进行访问的用户数，以及所需的并发生成/发布数、测试用户数等因素。</span><span class="sxs-lookup"><span data-stu-id="c4050-215">Your Visual Studio Team Services costing will depend upon the number of users in your organization that require access, in addition to factors such as the number of concurrent build/releases required, and number of test users.</span></span> <span data-ttu-id="c4050-216">这些在 [VSTS 定价页][vsts-pricing-page]中有更详细的说明。</span><span class="sxs-lookup"><span data-stu-id="c4050-216">These are detailed further on the [VSTS pricing page][vsts-pricing-page].</span></span>

* <span data-ttu-id="c4050-217">[Visual Studio Team Services (VSTS)][vsts-pricing-calculator] 是一项用于管理开发生命周期的服务，按月按用户付款。</span><span class="sxs-lookup"><span data-stu-id="c4050-217">[Visual Studio Team Services (VSTS)][vsts-pricing-calculator] is a service that enables you to manage your development life cycle and is paid for on a per user, per month basis.</span></span> <span data-ttu-id="c4050-218">可能会有其他的费用，具体取决于所需的并发管道数，以及是否有其他测试用户，或者是否使用了基本的用户许可证。</span><span class="sxs-lookup"><span data-stu-id="c4050-218">There may be additional charges dependent upon concurrent pipelines needed, in addition to any additional test users, or user basic licenses.</span></span>

## <a name="related-resources"></a><span data-ttu-id="c4050-219">相关资源</span><span class="sxs-lookup"><span data-stu-id="c4050-219">Related Resources</span></span>

* <span data-ttu-id="c4050-220">[什么是 DevOps？][devops-whatis]</span><span class="sxs-lookup"><span data-stu-id="c4050-220">[What is DevOps?][devops-whatis]</span></span>
* <span data-ttu-id="c4050-221">[Microsoft 的 DevOps - 如何使用 Visual Studio Team Services][devops-microsoft]</span><span class="sxs-lookup"><span data-stu-id="c4050-221">[DevOps at Microsoft - How we work with Visual Studio Team Services][devops-microsoft]</span></span>
* <span data-ttu-id="c4050-222">[分步教程：Visual Studio Team Services 的 DevOps][devops-with-vsts]</span><span class="sxs-lookup"><span data-stu-id="c4050-222">[Step-by-step Tutorials: DevOps with Visual Studio Team Services][devops-with-vsts]</span></span>
* <span data-ttu-id="c4050-223">[使用 Azure DevOps 项目创建用于 .NET 的 CI/CD 管道][devops-project-create]</span><span class="sxs-lookup"><span data-stu-id="c4050-223">[Create a CI/CD pipeline for .NET with the Azure DevOps project][devops-project-create]</span></span>

<!-- links -->
[ansible]: /azure/ansible/
[application-insights]: /azure/application-insights/app-insights-overview
[app-service-reference-architecture]: /azure/architecture/reference-architectures/app-service-web-app/
[azure-free-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[arm-templates]: /azure/azure-resource-manager/resource-group-overview#template-deployment
[architecture]: ./media/devops-dotnet-webapp/architecture-devops-dotnet-webapp.png
[availability]: /azure/architecture/checklist/availability
[chef]: /azure/chef/
[design-patterns-availability]: /azure/architecture/patterns/category/availability
[design-patterns-resiliency]: /azure/architecture/patterns/category/resiliency
[design-patterns-scalability]: /azure/architecture/patterns/category/performance-scalability
[design-patterns-security]: /azure/architecture/patterns/category/security
[desired-state-configuration]: /azure/automation/automation-dsc-overview
[devops-microsoft]: /azure/devops/devops-at-microsoft/
[devops-with-vsts]: https://almvm.azurewebsites.net/labs/vsts/
[application-insights]: https://azure.microsoft.com/en-gb/services/application-insights/
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
[vsts-account-create]: /vsts/organizations/accounts/create-account-msa-or-work-student?view=vsts
[vsts-approvals]: /vsts/pipelines/release/approvals/approvals?view=vsts
[devops-project]: https://portal.azure.com/?feature.customportal=false#create/Microsoft.AzureProject
[vsts-deployment-gates]: /vsts/pipelines/release/approvals/gates?view=vsts
[vsts-pricing-calculator]: https://azure.com/e/498aa024454445a8a352e75724f900b1
[vsts-pricing-page]: https://azure.microsoft.com/en-us/pricing/details/visual-studio-team-services/
[vsts-release-variables]: /vsts/pipelines/release/variables?view=vsts&tabs=batch
[vsts-tokenization]: https://marketplace.visualstudio.com/search?term=token&target=VSTS&category=All%20categories&sortBy=Relevance
[azure-key-vault]: /azure/key-vault/key-vault-overview
[infra-as-code]: https://blogs.msdn.microsoft.com/mvpawardprogram/2018/02/13/infrastructure-as-code/
[team-foundation-server]: https://visualstudio.microsoft.com/tfs/
[infra-as-code]: https://blogs.msdn.microsoft.com/mvpawardprogram/2018/02/13/infrastructure-as-code/
[service-fabric]:/azure/service-fabric/
[azure-functions]:/azure/azure-functions/
[azure-containers]:https://azure.microsoft.com/en-us/overview/containers/
[compare-vm-hosting]:/azure/app-service/choose-web-site-cloud-service-vm
[app-insights-cd-monitoring]:/azure/application-insights/app-insights-vsts-continuous-monitoring
[azure-region-pair-bcdr]:/azure/best-practices-availability-paired-regions
[devops-project-create]: /vsts/pipelines/apps/cd/azure/azure-devops-project-aspnetcore?view=vsts
[security]: /azure/security/