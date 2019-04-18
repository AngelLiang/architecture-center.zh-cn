---
title: 部署 Azure 应用程序的复原能力和可用性
description: 在 Azure 中创建可靠的部署技术的概述
author: MikeWasson
ms.date: 04/10/2019
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-design-principles
ms.openlocfilehash: d8df3fe1c3353c60462959a625ba13ae47b96cf8
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59646493"
---
# <a name="deploying-azure-applications-for-resiliency-and-availability"></a>部署 Azure 应用程序的复原能力和可用性

在预配和更新 Azure 资源、 应用程序代码和配置设置时，可重复且可预测的过程将帮助您避免错误和停机时间。 我们建议自动化的过程来部署，您可以按需运行，然后重新运行如果出现故障。 部署过程会顺利运行后，过程文档可以让它们通过这种方式。

## <a name="automate-processes"></a>自动执行流程

若要按需激活资源、 快速部署解决方案、 最大程度减少人为错误，并生成一致且可重复的结果，请确保自动部署和更新。

### <a name="automate-as-many-processes-as-possible"></a>尽可能自动执行尽可能多的进程

自动执行的最可靠的部署过程并*幂等* &mdash; ，它是可重复生成相同的结果。

- 若要自动执行 Azure 资源的预配，可以使用[Terraform](/azure/virtual-machines/windows/infrastructure-automation#terraform)， [Ansible](/azure/virtual-machines/windows/infrastructure-automation#ansible)， [Chef](/azure/virtual-machines/windows/infrastructure-automation#chef)， [Puppet](/azure/virtual-machines/windows/infrastructure-automation#puppet)， [AzurePowerShell](/powershell/azure/overview)， [Azure 命令行接口 (CLI)](/cli/azure)，或 [Azure 资源管理器模板](/azure/azure-resource-manager/resource-group-overview#template-deployment)。
- 若要配置的 Vm，可以使用 [的 cloud-init](/azure/virtual-machines/windows/infrastructure-automation#cloud-init) （适用于 Linux Vm) 或 [Azure 自动化状态配置](/azure/automation/automation-dsc-overview)(DSC)。
- 若要自动执行应用程序部署，可以使用 [Azure DevOps 服务](/azure/virtual-machines/windows/infrastructure-automation#azure-devops-services)， [Jenkins](/azure/virtual-machines/windows/infrastructure-automation#jenkins)，或其他 CI/CD 解决方案。

最佳做法是创建分类的自动化脚本以便快速访问，记录的参数和脚本使用的示例说明了存储的库。 将此文档与 Azure 部署中，同步并指派专人来管理存储库。

自动化脚本也可以激活的灾难恢复需求上的资源。

### <a name="use-code-to-provision-and-configure-infrastructure"></a>使用预配代码和配置基础结构

本练习中，调用*基础结构即代码，* 可能使用声明性方法或命令性方法 （或这两者的组合）。

- [资源管理器模板](/azure/azure-resource-manager/resource-group-overview#template-deployment)是声明性方法的一个示例。
- [PowerShell](/powershell/azure/overview)脚本是命令性方法的一个示例。

### <a name="practice-immutable-infrastructure"></a>做法是不可变基础结构

换而言之，不会修改基础结构部署到生产环境之后。 已应用即席更改后，可能不知道实际已更改的内容，因此它可能很难对系统进行故障排除。

### <a name="automate-and-test-deployment-and-maintenance-tasks"></a>自动化和测试部署和维护任务

分布式应用程序由多个必须共同工作的部件组成。 部署应利用经过验证的机制，例如，可以更新和验证配置并自动执行部署过程的脚本。 测试所有过程完全来确保错误不会导致额外的停机时间。

### <a name="implement-deployment-security-measures"></a>执行部署安全措施

所有部署工具必须都包含要保护部署的应用程序的安全限制。 定义和仔细，强制实施部署策略和最大程度减少需人工干预。

## <a name="deploy-to-multiple-regions-and-instances"></a>将部署到多个区域和实例

取决于服务的单个实例的应用程序创建单一故障点。 若要提高复原能力和可伸缩性，预配多个实例。

- 对于 [Azure 应用服务](/azure/app-service/app-service-value-prop-what-is/)，请选择提供多个实例的[应用服务计划](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview/)。
- 有关[Azure 云服务](/azure/cloud-services/cloud-services-choose-me)，配置每个角色以使用[多个实例](/azure/cloud-services/cloud-services-choose-me/#scaling-and-management)。
- 有关[Azure 虚拟机](/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，确保您的体系结构包含多个 VM，并且，每个 VM 包含在[可用性集](/azure/virtual-machines/virtual-machines-windows-manage-availability/)。

### <a name="consider-deploying-across-multiple-regions"></a>考虑跨多个区域部署

我们建议部署所有但的严重程度最低的应用程序和多个区域的应用程序服务。 如果你的应用程序部署到单个区域中，在极少数情况下整个区域变得不可用，应用程序也将不可用。

如果你选择部署到单个区域，请考虑正在准备作为对意外失败的响应，重新部署到次要区域。

### <a name="redeploy-to-a-secondary-region"></a>重新部署到次要区域

如果没有复制与单一的主区域中运行应用程序和数据库，可能是您的恢复策略重新部署到另一个区域。 此解决方案非常经济实惠，但最适用于可以容忍长的恢复时间的非关键应用程序。

如果选择此策略，自动执行尽可能多地重新部署过程并重新部署方案包括在你的灾难响应测试。

若要自动执行重新部署过程，请考虑使用[Azure Site Recovery](/azure/site-recovery/)。

## <a name="use-staging-and-production-environments"></a>使用过渡和生产环境

通过充分利用过渡和生产环境，可以将更新推送到生产环境中以高度受控的方式，并最大程度减少意外的部署问题造成中断。

- [*蓝绿部署*](https://martinfowler.com/bliki/BlueGreenDeployment.html) 涉及部署到生产环境分开实时应用程序的更新。 验证部署后，可将流量改为路由到更新的版本。 一种方法来执行这是使用[过渡槽](/azure/app-service/web-sites-staged-publishing)在 Azure 应用服务以分阶段部署之前将其移到生产环境中可用。
- [*Canary 发布*](https://martinfowler.com/bliki/CanaryRelease.html) 类似于蓝绿部署。 而不是所有流量都切换到更新的应用程序，将只有一小部分流量路由到新的部署。 如果没有问题，恢复到旧的部署。 如果没有，逐渐将更多的流量路由到新版本。 如果使用 Azure 应用服务，可以使用生产功能中的测试来管理 canary 发布。

## <a name="create-a-rollback-plan-for-deployment-and-updates"></a>创建回滚计划用于部署和更新

如果部署失败，你的应用程序可能会变得不可用。 若要尽量减少停机时间，请设计回滚过程，以返回到上次已知良好版本。 包括一个更改回滚到的数据库和应用依赖于任何其他服务的策略。

如果你使用 Azure 应用服务，可以设置的最后一个已知的良好站点槽，并使用它来从 web 或 API 应用部署回滚。

## <a name="log-and-audit-deployments"></a>日志和审核部署

若要捕获为更特定于版本的信息，请实现可靠的日志记录策略。 如果使用分阶段的部署技术，将在生产环境中运行多个版本的应用程序。 如果出现问题，确定哪个版本导致它。

## <a name="document-release-processes"></a>文档发布进程

而不使用详细的发布过程文档，操作员可能部署错误的更新，或可能会不当地配置应用程序的设置。 明确定义和阐述发布过程，并确保将其提供给整个运营团队。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [可靠性监视器应用程序运行状况](./monitoring.md)
