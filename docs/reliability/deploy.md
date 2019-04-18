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
# <a name="deploying-azure-applications-for-resiliency-and-availability"></a><span data-ttu-id="51699-103">部署 Azure 应用程序的复原能力和可用性</span><span class="sxs-lookup"><span data-stu-id="51699-103">Deploying Azure applications for resiliency and availability</span></span>

<span data-ttu-id="51699-104">在预配和更新 Azure 资源、 应用程序代码和配置设置时，可重复且可预测的过程将帮助您避免错误和停机时间。</span><span class="sxs-lookup"><span data-stu-id="51699-104">As you provision and update Azure resources, application code, and configuration settings, a repeatable and predictable process will help you avoid errors and downtime.</span></span> <span data-ttu-id="51699-105">我们建议自动化的过程来部署，您可以按需运行，然后重新运行如果出现故障。</span><span class="sxs-lookup"><span data-stu-id="51699-105">We recommend automated processes for deployment that you can run on demand and rerun if something fails.</span></span> <span data-ttu-id="51699-106">部署过程会顺利运行后，过程文档可以让它们通过这种方式。</span><span class="sxs-lookup"><span data-stu-id="51699-106">After your deployment processes are running smoothly, process documentation can keep them that way.</span></span>

## <a name="automate-processes"></a><span data-ttu-id="51699-107">自动执行流程</span><span class="sxs-lookup"><span data-stu-id="51699-107">Automate processes</span></span>

<span data-ttu-id="51699-108">若要按需激活资源、 快速部署解决方案、 最大程度减少人为错误，并生成一致且可重复的结果，请确保自动部署和更新。</span><span class="sxs-lookup"><span data-stu-id="51699-108">To activate resources on demand, deploy solutions rapidly, minimize human error, and produce consistent and repeatable results, be sure to automate deployments and updates.</span></span>

### <a name="automate-as-many-processes-as-possible"></a><span data-ttu-id="51699-109">尽可能自动执行尽可能多的进程</span><span class="sxs-lookup"><span data-stu-id="51699-109">Automate as many processes as possible</span></span>

<span data-ttu-id="51699-110">自动执行的最可靠的部署过程并*幂等* &mdash; ，它是可重复生成相同的结果。</span><span class="sxs-lookup"><span data-stu-id="51699-110">The most reliable deployment processes are automated and *idempotent* &mdash; that is, repeatable to produce the same results.</span></span>

- <span data-ttu-id="51699-111">若要自动执行 Azure 资源的预配，可以使用[Terraform](/azure/virtual-machines/windows/infrastructure-automation#terraform)， [Ansible](/azure/virtual-machines/windows/infrastructure-automation#ansible)， [Chef](/azure/virtual-machines/windows/infrastructure-automation#chef)， [Puppet](/azure/virtual-machines/windows/infrastructure-automation#puppet)， [AzurePowerShell](/powershell/azure/overview)， [Azure 命令行接口 (CLI)](/cli/azure)，或 [Azure 资源管理器模板](/azure/azure-resource-manager/resource-group-overview#template-deployment)。</span><span class="sxs-lookup"><span data-stu-id="51699-111">To automate provisioning of Azure resources, you can use [Terraform](/azure/virtual-machines/windows/infrastructure-automation#terraform),   [Ansible](/azure/virtual-machines/windows/infrastructure-automation#ansible), [Chef](/azure/virtual-machines/windows/infrastructure-automation#chef), [Puppet](/azure/virtual-machines/windows/infrastructure-automation#puppet),   [Azure PowerShell](/powershell/azure/overview), [Azure command-line interface (CLI)](/cli/azure), or [Azure Resource Manager templates](/azure/azure-resource-manager/resource-group-overview#template-deployment).</span></span>
- <span data-ttu-id="51699-112">若要配置的 Vm，可以使用 [的 cloud-init](/azure/virtual-machines/windows/infrastructure-automation#cloud-init) （适用于 Linux Vm) 或 [Azure 自动化状态配置](/azure/automation/automation-dsc-overview)(DSC)。</span><span class="sxs-lookup"><span data-stu-id="51699-112">To configure VMs, you can use [cloud-init](/azure/virtual-machines/windows/infrastructure-automation#cloud-init) (for Linux VMs) or [Azure Automation State Configuration](/azure/automation/automation-dsc-overview) (DSC).</span></span>
- <span data-ttu-id="51699-113">若要自动执行应用程序部署，可以使用 [Azure DevOps 服务](/azure/virtual-machines/windows/infrastructure-automation#azure-devops-services)， [Jenkins](/azure/virtual-machines/windows/infrastructure-automation#jenkins)，或其他 CI/CD 解决方案。</span><span class="sxs-lookup"><span data-stu-id="51699-113">To automate application deployment, you can use [Azure DevOps Services](/azure/virtual-machines/windows/infrastructure-automation#azure-devops-services), [Jenkins](/azure/virtual-machines/windows/infrastructure-automation#jenkins), or other CI/CD solutions.</span></span>

<span data-ttu-id="51699-114">最佳做法是创建分类的自动化脚本以便快速访问，记录的参数和脚本使用的示例说明了存储的库。</span><span class="sxs-lookup"><span data-stu-id="51699-114">As a best practice, create a repository of categorized automation scripts for quick access, documented with explanations of parameters and examples of script use.</span></span> <span data-ttu-id="51699-115">将此文档与 Azure 部署中，同步并指派专人来管理存储库。</span><span class="sxs-lookup"><span data-stu-id="51699-115">Keep this documentation in sync with your Azure deployments, and designate a primary person to manage the repository.</span></span>

<span data-ttu-id="51699-116">自动化脚本也可以激活的灾难恢复需求上的资源。</span><span class="sxs-lookup"><span data-stu-id="51699-116">Automation scripts can also activate resources on demand for disaster recovery.</span></span>

### <a name="use-code-to-provision-and-configure-infrastructure"></a><span data-ttu-id="51699-117">使用预配代码和配置基础结构</span><span class="sxs-lookup"><span data-stu-id="51699-117">Use code to provision and configure infrastructure</span></span>

<span data-ttu-id="51699-118">本练习中，调用*基础结构即代码，* 可能使用声明性方法或命令性方法 （或这两者的组合）。</span><span class="sxs-lookup"><span data-stu-id="51699-118">This practice, called *infrastructure as code,* may use a declarative approach or an imperative approach (or a combination of both).</span></span>

- <span data-ttu-id="51699-119">[资源管理器模板](/azure/azure-resource-manager/resource-group-overview#template-deployment)是声明性方法的一个示例。</span><span class="sxs-lookup"><span data-stu-id="51699-119">[Resource Manager templates](/azure/azure-resource-manager/resource-group-overview#template-deployment) are an example of a declarative approach.</span></span>
- <span data-ttu-id="51699-120">[PowerShell](/powershell/azure/overview)脚本是命令性方法的一个示例。</span><span class="sxs-lookup"><span data-stu-id="51699-120">[PowerShell](/powershell/azure/overview) scripts are an example of an imperative approach.</span></span>

### <a name="practice-immutable-infrastructure"></a><span data-ttu-id="51699-121">做法是不可变基础结构</span><span class="sxs-lookup"><span data-stu-id="51699-121">Practice immutable infrastructure</span></span>

<span data-ttu-id="51699-122">换而言之，不会修改基础结构部署到生产环境之后。</span><span class="sxs-lookup"><span data-stu-id="51699-122">In other words, don't modify infrastructure after it's deployed to production.</span></span> <span data-ttu-id="51699-123">已应用即席更改后，可能不知道实际已更改的内容，因此它可能很难对系统进行故障排除。</span><span class="sxs-lookup"><span data-stu-id="51699-123">After ad hoc changes have been applied, you might not know exactly what has changed, so it can be difficult to troubleshoot the system.</span></span>

### <a name="automate-and-test-deployment-and-maintenance-tasks"></a><span data-ttu-id="51699-124">自动化和测试部署和维护任务</span><span class="sxs-lookup"><span data-stu-id="51699-124">Automate and test deployment and maintenance tasks</span></span>

<span data-ttu-id="51699-125">分布式应用程序由多个必须共同工作的部件组成。</span><span class="sxs-lookup"><span data-stu-id="51699-125">Distributed applications consist of multiple parts that must work together.</span></span> <span data-ttu-id="51699-126">部署应利用经过验证的机制，例如，可以更新和验证配置并自动执行部署过程的脚本。</span><span class="sxs-lookup"><span data-stu-id="51699-126">Deployment should take advantage of proven mechanisms, such as scripts, that can update and validate configuration and automate the deployment process.</span></span> <span data-ttu-id="51699-127">测试所有过程完全来确保错误不会导致额外的停机时间。</span><span class="sxs-lookup"><span data-stu-id="51699-127">Test all processes fully to ensure that errors don't cause additional downtime.</span></span>

### <a name="implement-deployment-security-measures"></a><span data-ttu-id="51699-128">执行部署安全措施</span><span class="sxs-lookup"><span data-stu-id="51699-128">Implement deployment security measures</span></span>

<span data-ttu-id="51699-129">所有部署工具必须都包含要保护部署的应用程序的安全限制。</span><span class="sxs-lookup"><span data-stu-id="51699-129">All deployment tools must incorporate security restrictions to protect the deployed application.</span></span> <span data-ttu-id="51699-130">定义和仔细，强制实施部署策略和最大程度减少需人工干预。</span><span class="sxs-lookup"><span data-stu-id="51699-130">Define and enforce deployment policies carefully, and minimize the need for human intervention.</span></span>

## <a name="deploy-to-multiple-regions-and-instances"></a><span data-ttu-id="51699-131">将部署到多个区域和实例</span><span class="sxs-lookup"><span data-stu-id="51699-131">Deploy to multiple regions and instances</span></span>

<span data-ttu-id="51699-132">取决于服务的单个实例的应用程序创建单一故障点。</span><span class="sxs-lookup"><span data-stu-id="51699-132">An application that depends on a single instance of a service creates a single point of failure.</span></span> <span data-ttu-id="51699-133">若要提高复原能力和可伸缩性，预配多个实例。</span><span class="sxs-lookup"><span data-stu-id="51699-133">To improve resiliency and scalability, provision multiple instances.</span></span>

- <span data-ttu-id="51699-134">对于 [Azure 应用服务](/azure/app-service/app-service-value-prop-what-is/)，请选择提供多个实例的[应用服务计划](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview/)。</span><span class="sxs-lookup"><span data-stu-id="51699-134">For [Azure App Service](/azure/app-service/app-service-value-prop-what-is/), select an [App Service plan](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview/) that offers multiple instances.</span></span>
- <span data-ttu-id="51699-135">有关[Azure 云服务](/azure/cloud-services/cloud-services-choose-me)，配置每个角色以使用[多个实例](/azure/cloud-services/cloud-services-choose-me/#scaling-and-management)。</span><span class="sxs-lookup"><span data-stu-id="51699-135">For [Azure Cloud Services](/azure/cloud-services/cloud-services-choose-me), configure each of your roles to use [multiple instances](/azure/cloud-services/cloud-services-choose-me/#scaling-and-management).</span></span>
- <span data-ttu-id="51699-136">有关[Azure 虚拟机](/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，确保您的体系结构包含多个 VM，并且，每个 VM 包含在[可用性集](/azure/virtual-machines/virtual-machines-windows-manage-availability/)。</span><span class="sxs-lookup"><span data-stu-id="51699-136">For [Azure Virtual Machines](/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), ensure that your architecture includes more than one VM and that each VM is included in an [availability set](/azure/virtual-machines/virtual-machines-windows-manage-availability/).</span></span>

### <a name="consider-deploying-across-multiple-regions"></a><span data-ttu-id="51699-137">考虑跨多个区域部署</span><span class="sxs-lookup"><span data-stu-id="51699-137">Consider deploying across multiple regions</span></span>

<span data-ttu-id="51699-138">我们建议部署所有但的严重程度最低的应用程序和多个区域的应用程序服务。</span><span class="sxs-lookup"><span data-stu-id="51699-138">We recommend deploying all but the least critical applications and application services across multiple regions.</span></span> <span data-ttu-id="51699-139">如果你的应用程序部署到单个区域中，在极少数情况下整个区域变得不可用，应用程序也将不可用。</span><span class="sxs-lookup"><span data-stu-id="51699-139">If your application is deployed to a single region, in the rare event that the entire region becomes unavailable, the application will also be unavailable.</span></span>

<span data-ttu-id="51699-140">如果你选择部署到单个区域，请考虑正在准备作为对意外失败的响应，重新部署到次要区域。</span><span class="sxs-lookup"><span data-stu-id="51699-140">If you choose to deploy to a single region, consider preparing to redeploy to a secondary region as a response to an unexpected failure.</span></span>

### <a name="redeploy-to-a-secondary-region"></a><span data-ttu-id="51699-141">重新部署到次要区域</span><span class="sxs-lookup"><span data-stu-id="51699-141">Redeploy to a secondary region</span></span>

<span data-ttu-id="51699-142">如果没有复制与单一的主区域中运行应用程序和数据库，可能是您的恢复策略重新部署到另一个区域。</span><span class="sxs-lookup"><span data-stu-id="51699-142">If you run applications and databases in a single, primary region with no replication, your recovery strategy might be to redeploy to another region.</span></span> <span data-ttu-id="51699-143">此解决方案非常经济实惠，但最适用于可以容忍长的恢复时间的非关键应用程序。</span><span class="sxs-lookup"><span data-stu-id="51699-143">This solution is affordable but most appropriate for non-critical applications that can tolerate longer recovery times.</span></span>

<span data-ttu-id="51699-144">如果选择此策略，自动执行尽可能多地重新部署过程并重新部署方案包括在你的灾难响应测试。</span><span class="sxs-lookup"><span data-stu-id="51699-144">If you choose this strategy, automate the redeployment process as much as possible and include redeployment scenarios in your disaster response testing.</span></span>

<span data-ttu-id="51699-145">若要自动执行重新部署过程，请考虑使用[Azure Site Recovery](/azure/site-recovery/)。</span><span class="sxs-lookup"><span data-stu-id="51699-145">To automate your redeployment process, consider using [Azure Site Recovery](/azure/site-recovery/).</span></span>

## <a name="use-staging-and-production-environments"></a><span data-ttu-id="51699-146">使用过渡和生产环境</span><span class="sxs-lookup"><span data-stu-id="51699-146">Use staging and production environments</span></span>

<span data-ttu-id="51699-147">通过充分利用过渡和生产环境，可以将更新推送到生产环境中以高度受控的方式，并最大程度减少意外的部署问题造成中断。</span><span class="sxs-lookup"><span data-stu-id="51699-147">With good use of staging and production environments, you can push updates to the production environment in a highly controlled way and minimize disruption from unanticipated deployment issues.</span></span>

- <span data-ttu-id="51699-148">[*蓝绿部署*](https://martinfowler.com/bliki/BlueGreenDeployment.html) 涉及部署到生产环境分开实时应用程序的更新。</span><span class="sxs-lookup"><span data-stu-id="51699-148">[*Blue-green deployment*](https://martinfowler.com/bliki/BlueGreenDeployment.html) involves deploying an update into a production environment that's separate from the live application.</span></span> <span data-ttu-id="51699-149">验证部署后，可将流量改为路由到更新的版本。</span><span class="sxs-lookup"><span data-stu-id="51699-149">After you validate the deployment, switch the traffic routing to the updated version.</span></span> <span data-ttu-id="51699-150">一种方法来执行这是使用[过渡槽](/azure/app-service/web-sites-staged-publishing)在 Azure 应用服务以分阶段部署之前将其移到生产环境中可用。</span><span class="sxs-lookup"><span data-stu-id="51699-150">One way to do this is to use the [staging slots](/azure/app-service/web-sites-staged-publishing) available in Azure App Service to stage a deployment before moving it to production.</span></span>
- <span data-ttu-id="51699-151">[*Canary 发布*](https://martinfowler.com/bliki/CanaryRelease.html) 类似于蓝绿部署。</span><span class="sxs-lookup"><span data-stu-id="51699-151">[*Canary releases*](https://martinfowler.com/bliki/CanaryRelease.html) are similar to blue-green deployments.</span></span> <span data-ttu-id="51699-152">而不是所有流量都切换到更新的应用程序，将只有一小部分流量路由到新的部署。</span><span class="sxs-lookup"><span data-stu-id="51699-152">Instead of switching all traffic to the updated application, you route only a small portion of the traffic to the new deployment.</span></span> <span data-ttu-id="51699-153">如果没有问题，恢复到旧的部署。</span><span class="sxs-lookup"><span data-stu-id="51699-153">If there's a problem, revert to the old deployment.</span></span> <span data-ttu-id="51699-154">如果没有，逐渐将更多的流量路由到新版本。</span><span class="sxs-lookup"><span data-stu-id="51699-154">If not, gradually route more traffic to the new version.</span></span> <span data-ttu-id="51699-155">如果使用 Azure 应用服务，可以使用生产功能中的测试来管理 canary 发布。</span><span class="sxs-lookup"><span data-stu-id="51699-155">If you're using Azure App Service, you can use the Testing in production feature to manage a canary release.</span></span>

## <a name="create-a-rollback-plan-for-deployment-and-updates"></a><span data-ttu-id="51699-156">创建回滚计划用于部署和更新</span><span class="sxs-lookup"><span data-stu-id="51699-156">Create a rollback plan for deployment and updates</span></span>

<span data-ttu-id="51699-157">如果部署失败，你的应用程序可能会变得不可用。</span><span class="sxs-lookup"><span data-stu-id="51699-157">If a deployment fails, your application could become unavailable.</span></span> <span data-ttu-id="51699-158">若要尽量减少停机时间，请设计回滚过程，以返回到上次已知良好版本。</span><span class="sxs-lookup"><span data-stu-id="51699-158">To minimize downtime, design a rollback process to go back to a last-known good version.</span></span> <span data-ttu-id="51699-159">包括一个更改回滚到的数据库和应用依赖于任何其他服务的策略。</span><span class="sxs-lookup"><span data-stu-id="51699-159">Include a strategy to roll back changes to databases and any other services your app depends on.</span></span>

<span data-ttu-id="51699-160">如果你使用 Azure 应用服务，可以设置的最后一个已知的良好站点槽，并使用它来从 web 或 API 应用部署回滚。</span><span class="sxs-lookup"><span data-stu-id="51699-160">If you're using Azure App Service, you can set up a last-known good site slot and use it to roll back from a web or API app deployment.</span></span>

## <a name="log-and-audit-deployments"></a><span data-ttu-id="51699-161">日志和审核部署</span><span class="sxs-lookup"><span data-stu-id="51699-161">Log and audit deployments</span></span>

<span data-ttu-id="51699-162">若要捕获为更特定于版本的信息，请实现可靠的日志记录策略。</span><span class="sxs-lookup"><span data-stu-id="51699-162">To capture as much version-specific information as possible, implement a robust logging strategy.</span></span> <span data-ttu-id="51699-163">如果使用分阶段的部署技术，将在生产环境中运行多个版本的应用程序。</span><span class="sxs-lookup"><span data-stu-id="51699-163">If you use staged deployment techniques, more than one version of your application will be running in production.</span></span> <span data-ttu-id="51699-164">如果出现问题，确定哪个版本导致它。</span><span class="sxs-lookup"><span data-stu-id="51699-164">If a problem occurs, determine which version is causing it.</span></span>

## <a name="document-release-processes"></a><span data-ttu-id="51699-165">文档发布进程</span><span class="sxs-lookup"><span data-stu-id="51699-165">Document release processes</span></span>

<span data-ttu-id="51699-166">而不使用详细的发布过程文档，操作员可能部署错误的更新，或可能会不当地配置应用程序的设置。</span><span class="sxs-lookup"><span data-stu-id="51699-166">Without detailed release process documentation, an operator might deploy a bad update or might improperly configure settings for your application.</span></span> <span data-ttu-id="51699-167">明确定义和阐述发布过程，并确保将其提供给整个运营团队。</span><span class="sxs-lookup"><span data-stu-id="51699-167">Clearly define and document your release process, and ensure that it's available to the entire operations team.</span></span>

## <a name="next-steps"></a><span data-ttu-id="51699-168">后续步骤</span><span class="sxs-lookup"><span data-stu-id="51699-168">Next steps</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="51699-169">可靠性监视器应用程序运行状况</span><span class="sxs-lookup"><span data-stu-id="51699-169">Monitor application health for reliability</span></span>](./monitoring.md)
