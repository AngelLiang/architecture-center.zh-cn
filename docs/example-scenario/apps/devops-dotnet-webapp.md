---
title: 使用 Azure DevOps 设计 CI/CD 管道
description: 使用 Azure DevOps 生成 .NET 应用并将其发布到 Azure Web 应用。
author: christianreddington
ms.date: 12/06/2018
ms.custom:
- fasttrack
- seodec18
ms.openlocfilehash: 23945493115522d099b6b26922f567653da0367e
ms.sourcegitcommit: 4ba3304eebaa8c493c3e5307bdd9d723cd90b655
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2018
ms.locfileid: "53307276"
---
# <a name="design-a-cicd-pipeline-using-azure-devops"></a>使用 Azure DevOps 设计 CI/CD 管道

本方案提供有关生成持续集成 (CI) 和持续部署 (CD) 管道的体系结构和设计指导。  在此示例中，CI/CD 管道会将一个双层 .NET Web 应用程序部署到 Azure 应用服务。

迁移到新式 CI/CD 过程可为应用程序生成、部署、测试和监视提供诸多好处。 将 Azure DevOps 与其他服务（例如应用服务）配合使用，组织可将工作重心放在开发应用而不是管理支持性基础结构上。

## <a name="relevant-use-cases"></a>相关用例

对于以下目的，请考虑使用 Azure DevOps 和 CI/CD 过程：

- 加速应用程序开发和开发生命周期
- 将质量和一致性管理内置到自动化的生成和发布过程中
- 提高应用程序稳定性和正常运行时间

## <a name="architecture"></a>体系结构

![使用 Azure DevOps 和 Azure 应用服务的 DevOps 方案中涉及的 Azure 组件体系结构示意图][architecture]

数据流经方案的情形如下所示：

1. 开发人员更改应用程序源代码。
2. 将包含 web.config 文件的应用程序代码提交到 Azure Repos 中的源代码存储库。
3. 持续集成使用 Azure Test Plans 触发应用程序生成和单元测试。
4. Azure Pipelines 中的持续部署使用特定于环境的配置值来触发应用程序项目的自动化部署。
5. 将项目部署到 Azure 应用服务。
6. Azure Application Insights 收集并分析运行状况、性能和使用情况数据。
7. 开发人员监视和管理运行状况、性能与使用情况信息。
8. 使用积压工作信息通过 Azure Boards 确定新功能和 bug 修复的优先级。

### <a name="components"></a>组件

- [Azure DevOps][vsts] 服务用于管理端到端的开发生命周期 &mdash; 从规划和项目管理，到代码管理，再到生成和发布。

- [Azure Web 应用][web-apps]是用于托管 Web 应用程序、REST API 和移动后端的 PaaS 服务。 虽然本文着重讨论 .NET，但系统也支持多个其他的开发平台选项。

- [Application Insights][application-insights] 是第一方的可扩展应用程序性能管理 (APM) 服务，适用于多个平台上的 Web 开发人员。

### <a name="alternatives"></a>备选项

虽然本文将重点放在 Azure DevOps 上，但也可以在本地改用 [Azure DevOps Server][azure-devops-server]（前称为 Team Foundation Server）。 或者，也可以使用一组技术来取代使用 [Jenkins][jenkins-on-azure] 的开源开发管道。

从基础结构即代码的角度看，可将[资源管理器模板][arm-templates]用作 Azure DevOps 项目的一部分，但也可考虑使用 [Terraform][terraform] 或 [Chef][chef] 等其他管理技术。 如果首选以基础结构即服务 (IaaS) 为基础的部署并且需要配置管理，可以考虑 [Azure Automation State Configuration][desired-state-configuration]、[Ansible][ansible] 或 [Chef][chef]。

可以考虑使用以下替代方案在 Azure Web 应用中进行托管：

- [Azure 虚拟机][compare-vm-hosting]可以处理需要较高控制度，或者依赖于 Web 应用所不支持的 OS 组件和服务（例如 Windows GAC 或 COM）的工作负荷。

- 如果工作负荷体系结构侧重于分布式组件，而此类组件适合在具有较高控制度的群集上部署和运行，则 [Service Fabric][service-fabric] 是个不错的选项。 Service Fabric 也可用于托管容器。

- 如果工作负荷体系结构侧重于精细的分布式组件，几乎不需要依赖项，只在需要的情况下运行单个组件（不需要持续运行），且不需要对组件的运行进行协调，则 [Azure Functions][azure-functions] 可提供有效的无服务器方法。

选择适当的迁移路径时，这个[适用于 Azure 计算服务的决策树](/azure/architecture/guide/technology-choices/compute-decision-tree)可能有所帮助。

## <a name="management-and-security-considerations"></a>管理和安全注意事项

- 考虑利用在 VSTS 市场中提供的一个[词汇切分任务][vsts-tokenization]。

- [Azure Key Vault][download-keyvault-secrets] 任务可将 Azure Key Vault 中的机密下载到发布中。 然后可将这些机密用作发布定义中的变量，这样可以避免将其存储在源代码管理中。

- 在发布定义中使用[发布变量][vsts-release-variables]来驱动对环境的配置更改。 发布变量的作用域可以是整个发布，也可以是给定的环境。 将变量用于机密信息时，请确保选择挂锁图标。

- 应在发布管道中使用[部署门限][vsts-deployment-gates]。 这样就可以利用与外部系统（例如，事件管理系统或其他定制系统）关联的监视数据，以便确定是否应提升某个发布。

- 如果需要在发布管道中进行手动干预，请使用[审批][vsts-approvals]功能。

- 考虑在发布管道中尽早使用 [Application Insights][application-insights] 和其他监视工具。 许多组织只在其生产环境中开始监视。 如果监视其他环境，则可以提前在开发过程中识别 Bug，避免生产环境中出现问题。

## <a name="deploy-the-scenario"></a>部署方案

### <a name="prerequisites"></a>先决条件

- 必须已经有 Azure 帐户。 如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户][azure-free-account]。

- 必须注册一个 Azure DevOps 组织。 有关详细信息，请参阅[快速入门：创建组织][vsts-account-create]。

### <a name="walk-through"></a>演练

该 [Azure DevOps 项目](/azure/devops-project/azure-devops-project-github)将部署应用服务计划、应用服务以及 App Insights 资源，并配置 Azure DevOps 项目。

部署 Azure DevOps 项目并完成生成后，请查看关联的代码更改、工作项和测试结果。 你会发现，测试结果并未显示，因为代码不包含任何需要运行的测试。

该项目将创建发布管道和持续部署触发器，并将应用程序部署到开发环境中。 在持续部署过程中，可能会看到跨多个环境的发布。 发布可以跨基础结构（使用基础结构即代码之类的技术），还可以部署所需的应用程序包以及任何配置后任务。

## <a name="pricing"></a>定价

Azure DevOps 成本取决于组织中需要访问权限的用户数和其他因素，例如，所需的并发生成/发布数，以及测试用户数。 有关详细信息，请参阅 [Azure DevOps 定价][vsts-pricing-page]。

此[定价计算器][vsts-pricing-calculator]提供 20 个用户运行 Azure DevOps 的估计费用。

Azure DevOps 按每月每个用户计费。 可能存在其他费用，具体取决于所需的并发管道数，以及是否有其他测试用户，或者是否使用了基本的用户许可证。

## <a name="related-resources"></a>相关资源

查看以下资源详细了解 CI/CD 和 Azure DevOps：

- [什么是 DevOps？][devops-whatis]
- [Microsoft 中的 DevOps - 如何使用 Azure DevOps][devops-microsoft]
- [分步教程：DevOps 与 Azure DevOps][devops-with-vsts]
- [DevOps 查检表][devops-checklist]
- [使用 Azure DevOps 项目创建用于 .NET 的 CI/CD 管道][devops-project-create]

<!-- links -->

[ansible]: /azure/ansible/
[application-insights]: /azure/application-insights/app-insights-overview
[app-service-reference-architecture]: ../../reference-architectures/app-service-web-app/basic-web-app.md
[azure-free-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
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
