---
title: 使用 Azure DevOps 设计 CI/CD 管道
titleSuffix: Azure Example Scenarios
description: 使用 Azure DevOps 生成 .NET 应用并将其发布到 Azure Web 应用。
author: christianreddington
ms.date: 12/06/2018
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.custom: fasttrack, seodec18
social_image_url: /azure/architecture/example-scenario/apps/media/architecture-devops-dotnet-webapp.svg
ms.openlocfilehash: f999b2ffdf234161f668887d5b2327ecf50f0e55
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59740402"
---
# <a name="design-a-cicd-pipeline-using-azure-devops"></a><span data-ttu-id="3395d-103">使用 Azure DevOps 设计 CI/CD 管道</span><span class="sxs-lookup"><span data-stu-id="3395d-103">Design a CI/CD pipeline using Azure DevOps</span></span>

<span data-ttu-id="3395d-104">本方案提供有关生成持续集成 (CI) 和持续部署 (CD) 管道的体系结构和设计指导。</span><span class="sxs-lookup"><span data-stu-id="3395d-104">This scenario provides architecture and design guidance for building a continuous integration (CI) and continuous deployment (CD) pipeline.</span></span> <span data-ttu-id="3395d-105">在此示例中，CI/CD 管道会将一个双层 .NET Web 应用程序部署到 Azure 应用服务。</span><span class="sxs-lookup"><span data-stu-id="3395d-105">In this example, the CI/CD pipeline deploys a two-tier .NET web application to the Azure App Service.</span></span>

<span data-ttu-id="3395d-106">迁移到新式 CI/CD 过程可为应用程序生成、部署、测试和监视提供诸多好处。</span><span class="sxs-lookup"><span data-stu-id="3395d-106">Migrating to modern CI/CD processes provides many benefits for application builds, deployments, testing, and monitoring.</span></span> <span data-ttu-id="3395d-107">将 Azure DevOps 与其他服务（例如应用服务）配合使用，组织可将工作重心放在开发应用而不是管理支持性基础结构上。</span><span class="sxs-lookup"><span data-stu-id="3395d-107">By utilizing Azure DevOps along with other services such as App Service, organizations can focus on the development of their apps rather than the management of the supporting infrastructure.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="3395d-108">相关用例</span><span class="sxs-lookup"><span data-stu-id="3395d-108">Relevant use cases</span></span>

<span data-ttu-id="3395d-109">对于以下目的，请考虑使用 Azure DevOps 和 CI/CD 过程：</span><span class="sxs-lookup"><span data-stu-id="3395d-109">Consider Azure DevOps and CI/CD processes for:</span></span>

- <span data-ttu-id="3395d-110">加速应用程序开发和开发生命周期</span><span class="sxs-lookup"><span data-stu-id="3395d-110">Accelerating application development and development life cycles</span></span>
- <span data-ttu-id="3395d-111">将质量和一致性管理内置到自动化的生成和发布过程中</span><span class="sxs-lookup"><span data-stu-id="3395d-111">Building quality and consistency into an automated build and release process</span></span>
- <span data-ttu-id="3395d-112">提高应用程序稳定性和正常运行时间</span><span class="sxs-lookup"><span data-stu-id="3395d-112">Increasing application stability and uptime</span></span>

## <a name="architecture"></a><span data-ttu-id="3395d-113">体系结构</span><span class="sxs-lookup"><span data-stu-id="3395d-113">Architecture</span></span>

![使用 Azure DevOps 和 Azure 应用服务的 DevOps 方案中涉及的 Azure 组件体系结构示意图][architecture]

<span data-ttu-id="3395d-115">数据流经方案的情形如下所示：</span><span class="sxs-lookup"><span data-stu-id="3395d-115">The data flows through the scenario as follows:</span></span>

1. <span data-ttu-id="3395d-116">开发人员更改应用程序源代码。</span><span class="sxs-lookup"><span data-stu-id="3395d-116">A developer changes application source code.</span></span>
2. <span data-ttu-id="3395d-117">将包含 web.config 文件的应用程序代码提交到 Azure Repos 中的源代码存储库。</span><span class="sxs-lookup"><span data-stu-id="3395d-117">Application code including the web.config file is committed to the source code repository in Azure Repos.</span></span>
3. <span data-ttu-id="3395d-118">持续集成使用 Azure Test Plans 触发应用程序生成和单元测试。</span><span class="sxs-lookup"><span data-stu-id="3395d-118">Continuous integration triggers application build and unit tests using Azure Test Plans.</span></span>
4. <span data-ttu-id="3395d-119">Azure Pipelines 中的持续部署使用特定于环境的配置值来触发应用程序项目的自动化部署。</span><span class="sxs-lookup"><span data-stu-id="3395d-119">Continuous deployment within Azure Pipelines triggers an automated deployment of application artifacts *with environment-specific configuration values*.</span></span>
5. <span data-ttu-id="3395d-120">将项目部署到 Azure 应用服务。</span><span class="sxs-lookup"><span data-stu-id="3395d-120">The artifacts are deployed to Azure App Service.</span></span>
6. <span data-ttu-id="3395d-121">Azure Application Insights 收集并分析运行状况、性能和使用情况数据。</span><span class="sxs-lookup"><span data-stu-id="3395d-121">Azure Application Insights collects and analyzes health, performance, and usage data.</span></span>
7. <span data-ttu-id="3395d-122">开发人员监视和管理运行状况、性能与使用情况信息。</span><span class="sxs-lookup"><span data-stu-id="3395d-122">Developers monitor and mange health, performance, and usage information.</span></span>
8. <span data-ttu-id="3395d-123">使用积压工作信息通过 Azure Boards 确定新功能和 bug 修复的优先级。</span><span class="sxs-lookup"><span data-stu-id="3395d-123">Backlog information is used to prioritize new features and bug fixes using Azure Boards.</span></span>

### <a name="components"></a><span data-ttu-id="3395d-124">组件</span><span class="sxs-lookup"><span data-stu-id="3395d-124">Components</span></span>

- <span data-ttu-id="3395d-125">[Azure DevOps][vsts] 服务用于管理端到端的开发生命周期 &mdash; 从规划和项目管理，到代码管理，再到生成和发布。</span><span class="sxs-lookup"><span data-stu-id="3395d-125">[Azure DevOps][vsts] is a service for managing your development life cycle end-to-end &mdash; from planning and project management, to code management, and continuing to build and release.</span></span>

- <span data-ttu-id="3395d-126">[Azure Web 应用][web-apps]是用于托管 Web 应用程序、REST API 和移动后端的 PaaS 服务。</span><span class="sxs-lookup"><span data-stu-id="3395d-126">[Azure Web Apps][web-apps] is a PaaS service for hosting web applications, REST APIs, and mobile back ends.</span></span> <span data-ttu-id="3395d-127">虽然本文着重讨论 .NET，但系统也支持多个其他的开发平台选项。</span><span class="sxs-lookup"><span data-stu-id="3395d-127">While this article focuses on .NET, there are several additional development platform options supported.</span></span>

- <span data-ttu-id="3395d-128">[Application Insights][application-insights] 是第一方的可扩展应用程序性能管理 (APM) 服务，适用于多个平台上的 Web 开发人员。</span><span class="sxs-lookup"><span data-stu-id="3395d-128">[Application Insights][application-insights] is a first-party, extensible Application Performance Management (APM) service for web developers on multiple platforms.</span></span>

### <a name="alternatives"></a><span data-ttu-id="3395d-129">备选项</span><span class="sxs-lookup"><span data-stu-id="3395d-129">Alternatives</span></span>

<span data-ttu-id="3395d-130">虽然本文将重点放在 Azure DevOps 上，但也可以在本地改用 [Azure DevOps Server][azure-devops-server]（前称为 Team Foundation Server）。</span><span class="sxs-lookup"><span data-stu-id="3395d-130">While this article focuses on Azure DevOps, [Azure DevOps Server][azure-devops-server] (previously known as Team Foundation Server) could be used as an on-premises substitute.</span></span> <span data-ttu-id="3395d-131">或者，也可以使用一组技术来取代使用 [Jenkins][jenkins-on-azure] 的开源开发管道。</span><span class="sxs-lookup"><span data-stu-id="3395d-131">Alternatively, you could also use a set of technologies for an open-source development pipeline using [Jenkins][jenkins-on-azure].</span></span>

<span data-ttu-id="3395d-132">从基础结构即代码的角度看，可将[资源管理器模板][arm-templates]用作 Azure DevOps 项目的一部分，但也可考虑使用 [Terraform][terraform] 或 [Chef][chef] 等其他管理技术。</span><span class="sxs-lookup"><span data-stu-id="3395d-132">From an infrastructure-as-code perspective, [Resource Manager templates][arm-templates] were used as part of the Azure DevOps project, but you could consider other management technologies such as [Terraform][terraform] or [Chef][chef].</span></span> <span data-ttu-id="3395d-133">如果首选以基础结构即服务 (IaaS) 为基础的部署并且需要配置管理，可以考虑 [Azure Automation State Configuration][desired-state-configuration]、[Ansible][ansible] 或 [Chef][chef]。</span><span class="sxs-lookup"><span data-stu-id="3395d-133">If you prefer an infrastructure-as-a-service (IaaS)-based deployment and require configuration management, you could consider either [Azure Automation State Configuration][desired-state-configuration], [Ansible][ansible], or [Chef][chef].</span></span>

<span data-ttu-id="3395d-134">可以考虑使用以下替代方案在 Azure Web 应用中进行托管：</span><span class="sxs-lookup"><span data-stu-id="3395d-134">You could consider these alternatives to hosting in Azure Web Apps:</span></span>

- <span data-ttu-id="3395d-135">[Azure 虚拟机][compare-vm-hosting]可以处理需要较高控制度，或者依赖于 Web 应用所不支持的 OS 组件和服务（例如 Windows GAC 或 COM）的工作负荷。</span><span class="sxs-lookup"><span data-stu-id="3395d-135">[Azure Virtual Machines][compare-vm-hosting] handles workloads that require a high degree of control, or depend on OS components and services that are not possible with Web Apps (for example, the Windows GAC, or COM).</span></span>

- <span data-ttu-id="3395d-136">如果工作负荷体系结构侧重于分布式组件，而此类组件适合在具有较高控制度的群集上部署和运行，则 [Service Fabric][service-fabric] 是个不错的选项。</span><span class="sxs-lookup"><span data-stu-id="3395d-136">[Service Fabric][service-fabric] is a good option if the workload architecture is focused around distributed components that benefit from being deployed and run across a cluster with a high degree of control.</span></span> <span data-ttu-id="3395d-137">Service Fabric 也可用于托管容器。</span><span class="sxs-lookup"><span data-stu-id="3395d-137">Service Fabric can also be used to host containers.</span></span>

- <span data-ttu-id="3395d-138">如果工作负荷体系结构侧重于精细的分布式组件，几乎不需要依赖项，只在需要的情况下运行单个组件（不需要持续运行），且不需要对组件的运行进行协调，则 [Azure Functions][azure-functions] 可提供有效的无服务器方法。</span><span class="sxs-lookup"><span data-stu-id="3395d-138">[Azure Functions][azure-functions] provides an effective serverless approach if the workload architecture is centered around fine grained distributed components, requiring minimal dependencies, where individual components are only required to run on demand (not continuously) and orchestration of components is not required.</span></span>

<span data-ttu-id="3395d-139">选择适当的迁移路径时，这个[适用于 Azure 计算服务的决策树](/azure/architecture/guide/technology-choices/compute-decision-tree)可能有所帮助。</span><span class="sxs-lookup"><span data-stu-id="3395d-139">This [decision tree for Azure compute services](/azure/architecture/guide/technology-choices/compute-decision-tree) may help when choosing the right path to take for a migration.</span></span>

## <a name="management-and-security-considerations"></a><span data-ttu-id="3395d-140">管理和安全注意事项</span><span class="sxs-lookup"><span data-stu-id="3395d-140">Management and Security Considerations</span></span>

- <span data-ttu-id="3395d-141">考虑利用在 VSTS 市场中提供的一个[词汇切分任务][vsts-tokenization]。</span><span class="sxs-lookup"><span data-stu-id="3395d-141">Consider leveraging one of the [tokenization tasks][vsts-tokenization] available in the VSTS marketplace.</span></span>

- <span data-ttu-id="3395d-142">[Azure Key Vault][download-keyvault-secrets] 任务可将 Azure Key Vault 中的机密下载到发布中。</span><span class="sxs-lookup"><span data-stu-id="3395d-142">[Azure Key Vault][download-keyvault-secrets] tasks can download secrets from an Azure Key Vault into your release.</span></span> <span data-ttu-id="3395d-143">然后可将这些机密用作发布定义中的变量，这样可以避免将其存储在源代码管理中。</span><span class="sxs-lookup"><span data-stu-id="3395d-143">You can then use those secrets as variables in your release definition, which avoids storing them in source control.</span></span>

- <span data-ttu-id="3395d-144">在发布定义中使用[发布变量][vsts-release-variables]来驱动对环境的配置更改。</span><span class="sxs-lookup"><span data-stu-id="3395d-144">Use [release variables][vsts-release-variables] in your release definitions to drive configuration changes of your environments.</span></span> <span data-ttu-id="3395d-145">发布变量的作用域可以是整个发布，也可以是给定的环境。</span><span class="sxs-lookup"><span data-stu-id="3395d-145">Release variables can be scoped to an entire release or a given environment.</span></span> <span data-ttu-id="3395d-146">将变量用于机密信息时，请确保选择挂锁图标。</span><span class="sxs-lookup"><span data-stu-id="3395d-146">When using variables for secret information, ensure that you select the padlock icon.</span></span>

- <span data-ttu-id="3395d-147">应在发布管道中使用[部署门限][vsts-deployment-gates]。</span><span class="sxs-lookup"><span data-stu-id="3395d-147">[Deployment gates][vsts-deployment-gates] should be used in your release pipeline.</span></span> <span data-ttu-id="3395d-148">这样就可以利用与外部系统（例如，事件管理系统或其他定制系统）关联的监视数据，以便确定是否应提升某个发布。</span><span class="sxs-lookup"><span data-stu-id="3395d-148">This lets you leverage monitoring data in association with external systems (for example, incident management or additional bespoke systems) to determine whether a release should be promoted.</span></span>

- <span data-ttu-id="3395d-149">如果需要在发布管道中进行手动干预，请使用[审批][vsts-approvals]功能。</span><span class="sxs-lookup"><span data-stu-id="3395d-149">Where manual intervention in a release pipeline is required, use the [approvals][vsts-approvals] functionality.</span></span>

- <span data-ttu-id="3395d-150">考虑在发布管道中尽早使用 [Application Insights][application-insights] 和其他监视工具。</span><span class="sxs-lookup"><span data-stu-id="3395d-150">Consider using [Application Insights][application-insights] and additional monitoring tools as early as possible in your release pipeline.</span></span> <span data-ttu-id="3395d-151">许多组织只在其生产环境中开始监视。</span><span class="sxs-lookup"><span data-stu-id="3395d-151">Many organizations only begin monitoring in their production environment.</span></span> <span data-ttu-id="3395d-152">如果监视其他环境，则可以提前在开发过程中识别 Bug，避免生产环境中出现问题。</span><span class="sxs-lookup"><span data-stu-id="3395d-152">By monitoring your other environments, you can identify bugs earlier in the development process and avoid issues in your production environment.</span></span>

## <a name="deploy-the-scenario"></a><span data-ttu-id="3395d-153">部署方案</span><span class="sxs-lookup"><span data-stu-id="3395d-153">Deploy the scenario</span></span>

### <a name="prerequisites"></a><span data-ttu-id="3395d-154">必备组件</span><span class="sxs-lookup"><span data-stu-id="3395d-154">Prerequisites</span></span>

- <span data-ttu-id="3395d-155">必须已经有 Azure 帐户。</span><span class="sxs-lookup"><span data-stu-id="3395d-155">You must have an existing Azure account.</span></span> <span data-ttu-id="3395d-156">如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。</span><span class="sxs-lookup"><span data-stu-id="3395d-156">If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.</span></span>

- <span data-ttu-id="3395d-157">必须注册一个 Azure DevOps 组织。</span><span class="sxs-lookup"><span data-stu-id="3395d-157">You must sign up for an Azure DevOps organization.</span></span> <span data-ttu-id="3395d-158">有关详细信息，请参阅[快速入门：创建组织][vsts-account-create]。</span><span class="sxs-lookup"><span data-stu-id="3395d-158">For more information, see [Quickstart: Create your organization][vsts-account-create].</span></span>

### <a name="walk-through"></a><span data-ttu-id="3395d-159">演练</span><span class="sxs-lookup"><span data-stu-id="3395d-159">Walk-through</span></span>

<span data-ttu-id="3395d-160">[Azure DevOps 项目](/azure/devops-project/azure-devops-project-github)将为你部署应用服务计划、 应用服务和 App Insights 资源，以及为你配置一个 Azure 管道的管道。</span><span class="sxs-lookup"><span data-stu-id="3395d-160">[Azure DevOps Projects](/azure/devops-project/azure-devops-project-github) will deploy an App Service Plan, App Service, and an App Insights resource for you, as well as configure an Azure Pipelines pipeline for you.</span></span>

<span data-ttu-id="3395d-161">已通过 Azure DevOps 项目配置的管道并在生成完成后，查看关联的代码更改工作项和测试结果。</span><span class="sxs-lookup"><span data-stu-id="3395d-161">Once you've configure a pipeline with Azure DevOps Projects and the build is completed, review the associated code changes, work items, and test results.</span></span> <span data-ttu-id="3395d-162">你会发现，测试结果并未显示，因为代码不包含任何需要运行的测试。</span><span class="sxs-lookup"><span data-stu-id="3395d-162">You will notice that no test results are displayed, because the code does not contain any tests to run.</span></span>

<span data-ttu-id="3395d-163">管道创建发布定义和持续部署触发器，我们应用程序部署到开发环境。</span><span class="sxs-lookup"><span data-stu-id="3395d-163">The pipeline creates a release definition and a continuous deployment trigger, deploying our application into the Dev environment.</span></span> <span data-ttu-id="3395d-164">在持续部署过程中，可能会看到跨多个环境的发布。</span><span class="sxs-lookup"><span data-stu-id="3395d-164">As part of a continuous deployment process, you may see releases that span multiple environments.</span></span> <span data-ttu-id="3395d-165">发布可以跨基础结构（使用基础结构即代码之类的技术），还可以部署所需的应用程序包以及任何配置后任务。</span><span class="sxs-lookup"><span data-stu-id="3395d-165">A release can span both infrastructure (using techniques such as infrastructure-as-code), and can also deploy the application packages required along with any post-configuration tasks.</span></span>

## <a name="pricing"></a><span data-ttu-id="3395d-166">定价</span><span class="sxs-lookup"><span data-stu-id="3395d-166">Pricing</span></span>

<span data-ttu-id="3395d-167">Azure DevOps 成本取决于组织中需要访问权限的用户数和其他因素，例如，所需的并发生成/发布数，以及测试用户数。</span><span class="sxs-lookup"><span data-stu-id="3395d-167">Azure DevOps costs depend on the number of users in your organization that require access, along with other factors like the number of concurrent build/releases required and number of test users.</span></span> <span data-ttu-id="3395d-168">有关详细信息，请参阅 [Azure DevOps 定价][vsts-pricing-page]。</span><span class="sxs-lookup"><span data-stu-id="3395d-168">For more information, see [Azure DevOps pricing][vsts-pricing-page].</span></span>

<span data-ttu-id="3395d-169">此[定价计算器][vsts-pricing-calculator]提供 20 个用户运行 Azure DevOps 的估计费用。</span><span class="sxs-lookup"><span data-stu-id="3395d-169">This [pricing calculator][vsts-pricing-calculator] provides an estimate for running Azure DevOps with 20 users.</span></span>

<span data-ttu-id="3395d-170">Azure DevOps 按每月每个用户计费。</span><span class="sxs-lookup"><span data-stu-id="3395d-170">Azure DevOps is billed on a per-user per-month basis.</span></span> <span data-ttu-id="3395d-171">可能存在其他费用，具体取决于所需的并发管道数，以及是否有其他测试用户，或者是否使用了基本的用户许可证。</span><span class="sxs-lookup"><span data-stu-id="3395d-171">There may be additional charges dependent upon concurrent pipelines needed, in addition to any additional test users or user basic licenses.</span></span>

## <a name="related-resources"></a><span data-ttu-id="3395d-172">相关资源</span><span class="sxs-lookup"><span data-stu-id="3395d-172">Related resources</span></span>

<span data-ttu-id="3395d-173">查看以下资源详细了解 CI/CD 和 Azure DevOps：</span><span class="sxs-lookup"><span data-stu-id="3395d-173">Review the following resources to learn more about CI/CD and Azure DevOps:</span></span>

- <span data-ttu-id="3395d-174">[什么是 DevOps？][devops-whatis]</span><span class="sxs-lookup"><span data-stu-id="3395d-174">[What is DevOps?][devops-whatis]</span></span>
- <span data-ttu-id="3395d-175">[Microsoft 中的 DevOps - 如何使用 Azure DevOps][devops-microsoft]</span><span class="sxs-lookup"><span data-stu-id="3395d-175">[DevOps at Microsoft - How we work with Azure DevOps][devops-microsoft]</span></span>
- <span data-ttu-id="3395d-176">[分步教程：DevOps 与 Azure DevOps][devops-with-vsts]</span><span class="sxs-lookup"><span data-stu-id="3395d-176">[Step-by-step Tutorials: DevOps with Azure DevOps][devops-with-vsts]</span></span>
- <span data-ttu-id="3395d-177">[DevOps 清单][devops-checklist]</span><span class="sxs-lookup"><span data-stu-id="3395d-177">[DevOps Checklist][devops-checklist]</span></span>
- <span data-ttu-id="3395d-178">[适用于.NET 的 Azure DevOps 项目创建 CI/CD 管道][devops-project-create]</span><span class="sxs-lookup"><span data-stu-id="3395d-178">[Create a CI/CD pipeline for .NET with Azure DevOps Projects][devops-project-create]</span></span>

<!-- links -->

[ansible]: /azure/ansible/
[application-insights]: /azure/application-insights/app-insights-overview
[app-service-reference-architecture]: ../../reference-architectures/app-service-web-app/basic-web-app.md
[arm-templates]: /azure/azure-resource-manager/resource-group-overview#template-deployment
[architecture]: ./media/architecture-devops-dotnet-webapp.svg
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
[vsts]: /vsts/?view=vsts#pivot=services
[continuous-integration]: /azure/devops/what-is-continuous-integration
[continuous-delivery]: /azure/devops/what-is-continuous-delivery
[web-apps]: /azure/app-service/app-service-web-overview
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
[azure-devops-server]: https://visualstudio.microsoft.com/tfs/
[infra-as-code]: https://blogs.msdn.microsoft.com/mvpawardprogram/2018/02/13/infrastructure-as-code/
[service-fabric]: /azure/service-fabric/
[azure-functions]: /azure/azure-functions/
[azure-containers]: https://azure.microsoft.com/overview/containers/
[compare-vm-hosting]: /azure/app-service/choose-web-site-cloud-service-vm
[app-insights-cd-monitoring]: /azure/application-insights/app-insights-vsts-continuous-monitoring
[azure-region-pair-bcdr]: /azure/best-practices-availability-paired-regions
[devops-project-create]: /azure/devops-project/azure-devops-project-aspnet-core
[terraform]: /azure/terraform/
