---
title: 在 Kubernetes 上构建微服务的 CI/CD 管道
description: 介绍了用于部署到 Azure Kubernetes 服务 (AKS) 的微服务的示例中的 CI/CD 管道。
author: MikeWasson
ms.date: 04/11/2019
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: microservices
ms.openlocfilehash: e7da8a1b4111fb7856b919b40d033833a4e475e0
ms.sourcegitcommit: d58e6b2b891c9c99e951c59f15fce71addcb96b1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/12/2019
ms.locfileid: "59533157"
---
<!-- markdownlint-disable MD040 -->

# <a name="building-a-cicd-pipeline-for-microservices-on-kubernetes"></a><span data-ttu-id="b735f-103">在 Kubernetes 上构建微服务的 CI/CD 管道</span><span class="sxs-lookup"><span data-stu-id="b735f-103">Building a CI/CD pipeline for microservices on Kubernetes</span></span>

<span data-ttu-id="b735f-104">它极具挑战性创建微服务体系结构的可靠的 CI/CD 进程。</span><span class="sxs-lookup"><span data-stu-id="b735f-104">It can be challenging to create a reliable CI/CD process for a microservices architecture.</span></span> <span data-ttu-id="b735f-105">各个团队必须能够快速可靠地发布服务，而无需中断其他团队或破坏作为一个整体应用程序。</span><span class="sxs-lookup"><span data-stu-id="b735f-105">Individual teams must be able to release services quickly and reliably, without disrupting other teams or destabilizing the application as a whole.</span></span>

<span data-ttu-id="b735f-106">本指南介绍了用于部署到 Azure Kubernetes 服务 (AKS) 的微服务的示例中的 CI/CD 管道。</span><span class="sxs-lookup"><span data-stu-id="b735f-106">This article describes an example CI/CD pipeline for deploying microservices to Azure Kubernetes Service (AKS).</span></span> <span data-ttu-id="b735f-107">每个团队和项目都不同，因此不需要为一组严格的规则的这篇文章。</span><span class="sxs-lookup"><span data-stu-id="b735f-107">Every team and project is different, so don't take this article as a set of hard-and-fast rules.</span></span> <span data-ttu-id="b735f-108">相反，其目的是要设计您自己的 CI/CD 过程的起点。</span><span class="sxs-lookup"><span data-stu-id="b735f-108">Instead, it's meant to be a starting point for designing your own CI/CD process.</span></span>

<span data-ttu-id="b735f-109">此处所述的示例中管道已创建名为无人机交付应用程序，可在上找到的微服务引用实现[GitHub][ri]。</span><span class="sxs-lookup"><span data-stu-id="b735f-109">The example pipeline described here was created for a microservices reference implementation called the Drone Delivery application, which you can find on [GitHub][ri].</span></span> <span data-ttu-id="b735f-110">描述了应用程序方案[此处](./design/index.md#reference-implementation)。</span><span class="sxs-lookup"><span data-stu-id="b735f-110">The application scenario is described [here](./design/index.md#reference-implementation).</span></span>

<span data-ttu-id="b735f-111">管道的目标可以总结如下：</span><span class="sxs-lookup"><span data-stu-id="b735f-111">The goals of the pipeline can be summarized as follows:</span></span>

- <span data-ttu-id="b735f-112">团队可以生成和独立部署其服务。</span><span class="sxs-lookup"><span data-stu-id="b735f-112">Teams can build and deploy their services independently.</span></span>
- <span data-ttu-id="b735f-113">将 CI 过程的代码更改会自动部署到类似生产的环境。</span><span class="sxs-lookup"><span data-stu-id="b735f-113">Code changes that pass the CI process are automatically deployed to a production-like environment.</span></span>
- <span data-ttu-id="b735f-114">每个阶段的管道强制实施质量要求。</span><span class="sxs-lookup"><span data-stu-id="b735f-114">Quality gates are enforced at each stage of the pipeline.</span></span>
- <span data-ttu-id="b735f-115">可以与以前的版本并行部署服务的新版本。</span><span class="sxs-lookup"><span data-stu-id="b735f-115">A new version of a service can be deployed side by side with the previous version.</span></span>

<span data-ttu-id="b735f-116">有关详细背景信息，请参阅[微服务体系结构的 CI/CD](./ci-cd.md)。</span><span class="sxs-lookup"><span data-stu-id="b735f-116">For more background, see [CI/CD for microservices architectures](./ci-cd.md).</span></span>

## <a name="assumptions"></a><span data-ttu-id="b735f-117">假设</span><span class="sxs-lookup"><span data-stu-id="b735f-117">Assumptions</span></span>

<span data-ttu-id="b735f-118">对于此示例的目的，这里有一些假设开发团队和代码基：</span><span class="sxs-lookup"><span data-stu-id="b735f-118">For purposes of this example, here are some assumptions about the development team and the code base:</span></span>

- <span data-ttu-id="b735f-119">代码存储库是 monorepo，按微服务的文件夹。</span><span class="sxs-lookup"><span data-stu-id="b735f-119">The code repository is a monorepo, with folders organized by microservice.</span></span>
- <span data-ttu-id="b735f-120">团队的分库策略以[基于主库的开发](https://trunkbaseddevelopment.com/)为基础。</span><span class="sxs-lookup"><span data-stu-id="b735f-120">The team's branching strategy is based on [trunk-based development](https://trunkbaseddevelopment.com/).</span></span>
- <span data-ttu-id="b735f-121">该团队使用[发布分支](/azure/devops/repos/git/git-branching-guidance?view=azure-devops#manage-releases)管理发布。</span><span class="sxs-lookup"><span data-stu-id="b735f-121">The team uses [release branches](/azure/devops/repos/git/git-branching-guidance?view=azure-devops#manage-releases) to manage releases.</span></span> <span data-ttu-id="b735f-122">为每个微服务创建单独的版本。</span><span class="sxs-lookup"><span data-stu-id="b735f-122">Separate releases are created for each microservice.</span></span>
- <span data-ttu-id="b735f-123">使用 CI/CD 过程[Azure 管道](/azure/devops/pipelines/?view=azure-devops)若要生成，测试，并将微服务部署到 AKS。</span><span class="sxs-lookup"><span data-stu-id="b735f-123">The CI/CD process uses [Azure Pipelines](/azure/devops/pipelines/?view=azure-devops) to build, test, and deploy the microservices to AKS.</span></span>
- <span data-ttu-id="b735f-124">为每个微服务的容器映像存储在[Azure 容器注册表](/azure/container-registry/)。</span><span class="sxs-lookup"><span data-stu-id="b735f-124">The container images for each microservice are stored in [Azure Container Registry](/azure/container-registry/).</span></span>
- <span data-ttu-id="b735f-125">团队使用 Helm 图表来打包每个微服务。</span><span class="sxs-lookup"><span data-stu-id="b735f-125">The team uses Helm charts to package each microservice.</span></span>

<span data-ttu-id="b735f-126">这些假设驱动许多 CI/CD 管道的特定详细信息。</span><span class="sxs-lookup"><span data-stu-id="b735f-126">These assumptions drive many of the specific details of the CI/CD pipeline.</span></span> <span data-ttu-id="b735f-127">但是，此处所述的基本方法进行调整其他流程、 工具和服务，例如 Jenkins 或 Docker Hub。</span><span class="sxs-lookup"><span data-stu-id="b735f-127">However, the basic approach described here be adapted for other processes, tools, and services, such as Jenkins or Docker Hub.</span></span>

## <a name="validation-builds"></a><span data-ttu-id="b735f-128">验证生成</span><span class="sxs-lookup"><span data-stu-id="b735f-128">Validation builds</span></span>

<span data-ttu-id="b735f-129">假设在名为交付服务的微服务的开发人员正常工作。</span><span class="sxs-lookup"><span data-stu-id="b735f-129">Suppose that a developer is working on a microservice called the Delivery Service.</span></span> <span data-ttu-id="b735f-130">在开发新功能时，开发人员会将代码签入到某个功能分库中。</span><span class="sxs-lookup"><span data-stu-id="b735f-130">While developing a new feature, the developer checks code into a feature branch.</span></span> <span data-ttu-id="b735f-131">根据约定，功能分库名为 `feature/*`。</span><span class="sxs-lookup"><span data-stu-id="b735f-131">By convention, feature branches are named `feature/*`.</span></span>

![CI/CD 工作流](./images/aks-cicd-1.png)

<span data-ttu-id="b735f-133">生成定义文件包括按分支名称和源路径筛选器的触发器：</span><span class="sxs-lookup"><span data-stu-id="b735f-133">The build definition file includes a trigger that filters by the branch name and the source path:</span></span>

```yaml
trigger:
  batch: true
  branches:
    include:
    - master
    - feature/*
    - topic/*

    exclude:
    - feature/experimental/*
    - topic/experimental/*

  paths:
     include:
     - /src/shipping/delivery/
```

<span data-ttu-id="b735f-134">&#11162;请参阅[源文件](https://github.com/mspnp/microservices-reference-implementation/blob/master/src/shipping/delivery/azure-pipelines-validation.yml)。</span><span class="sxs-lookup"><span data-stu-id="b735f-134">&#11162; See the [source file](https://github.com/mspnp/microservices-reference-implementation/blob/master/src/shipping/delivery/azure-pipelines-validation.yml).</span></span>

<span data-ttu-id="b735f-135">使用此方法，每个团队都可以有自己的生成管道。</span><span class="sxs-lookup"><span data-stu-id="b735f-135">Using this approach, each team can have its own build pipeline.</span></span> <span data-ttu-id="b735f-136">已签入的代码`/src/shipping/delivery`文件夹触发传送服务的生成。</span><span class="sxs-lookup"><span data-stu-id="b735f-136">Only code that is checked into the `/src/shipping/delivery` folder triggers a build of the Delivery Service.</span></span> <span data-ttu-id="b735f-137">将提交内容推送到与筛选器匹配的分支会触发一个 CI 生成。</span><span class="sxs-lookup"><span data-stu-id="b735f-137">Pushing commits to a branch that matches the filter triggers a CI build.</span></span> <span data-ttu-id="b735f-138">在工作流中，CI 生成此时会运行某种最低程度的代码验证：</span><span class="sxs-lookup"><span data-stu-id="b735f-138">At this point in the workflow, the CI build runs some minimal code verification:</span></span>

1. <span data-ttu-id="b735f-139">生成代码。</span><span class="sxs-lookup"><span data-stu-id="b735f-139">Build the code.</span></span>
1. <span data-ttu-id="b735f-140">运行单元测试。</span><span class="sxs-lookup"><span data-stu-id="b735f-140">Run unit tests.</span></span>

<span data-ttu-id="b735f-141">目标是要保留生成时间短，因此开发人员可以获得快速反馈。</span><span class="sxs-lookup"><span data-stu-id="b735f-141">The goal is to keep build times short, so the developer can get quick feedback.</span></span> <span data-ttu-id="b735f-142">开发人员功能合并到主分支合并准备就绪后，打开 pr。</span><span class="sxs-lookup"><span data-stu-id="b735f-142">Once the feature is ready to merge into master, the developer opens a PR.</span></span> <span data-ttu-id="b735f-143">此时会触发另一个 CI 生成来执行一些其他的检查：</span><span class="sxs-lookup"><span data-stu-id="b735f-143">This triggers another CI build that performs some additional checks:</span></span>

1. <span data-ttu-id="b735f-144">生成代码。</span><span class="sxs-lookup"><span data-stu-id="b735f-144">Build the code.</span></span>
1. <span data-ttu-id="b735f-145">运行单元测试。</span><span class="sxs-lookup"><span data-stu-id="b735f-145">Run unit tests.</span></span>
1. <span data-ttu-id="b735f-146">生成运行时容器映像。</span><span class="sxs-lookup"><span data-stu-id="b735f-146">Build the runtime container image.</span></span>
1. <span data-ttu-id="b735f-147">在映像上运行漏洞扫描。</span><span class="sxs-lookup"><span data-stu-id="b735f-147">Run vulnerability scans on the image.</span></span>

![CI/CD 工作流](./images/aks-cicd-2.png)

> [!NOTE]
> <span data-ttu-id="b735f-149">在 Azure DevOps 存储库，你可以定义[策略](/azure/devops/repos/git/branch-policies)以保护分支。</span><span class="sxs-lookup"><span data-stu-id="b735f-149">In Azure DevOps Repos, you can define [policies](/azure/devops/repos/git/branch-policies) to protect branches.</span></span> <span data-ttu-id="b735f-150">例如，策略可以要求在合并到主库之前，必须成功完成 CI 生成并由审批人员签署同意书。</span><span class="sxs-lookup"><span data-stu-id="b735f-150">For example, the policy could require a successful CI build plus a sign-off from an approver in order to merge into master.</span></span>

## <a name="full-cicd-build"></a><span data-ttu-id="b735f-151">完整的 CI/CD 生成</span><span class="sxs-lookup"><span data-stu-id="b735f-151">Full CI/CD build</span></span>

<span data-ttu-id="b735f-152">有时候，团队可以部署新版传送服务。</span><span class="sxs-lookup"><span data-stu-id="b735f-152">At some point, the team is ready to deploy a new version of the Delivery service.</span></span> <span data-ttu-id="b735f-153">从与此命名模式的主版本管理器创建的分支： `release/<microservice name>/<semver>`。</span><span class="sxs-lookup"><span data-stu-id="b735f-153">The release manager creates a branch from master with this naming pattern: `release/<microservice name>/<semver>`.</span></span> <span data-ttu-id="b735f-154">例如，`release/delivery/v1.0.2`。</span><span class="sxs-lookup"><span data-stu-id="b735f-154">For example, `release/delivery/v1.0.2`.</span></span>

![CI/CD 工作流](./images/aks-cicd-3.png)

<span data-ttu-id="b735f-156">创建此分支将触发完整的 CI 生成运行的所有加上以前的步骤：</span><span class="sxs-lookup"><span data-stu-id="b735f-156">Creation of this branch triggers a full CI build that runs all of the previous steps plus:</span></span>

1. <span data-ttu-id="b735f-157">容器映像推送到 Azure 容器注册表。</span><span class="sxs-lookup"><span data-stu-id="b735f-157">Push the container image to Azure Container Registry.</span></span> <span data-ttu-id="b735f-158">该映像标记有版本号（取自分库名称）。</span><span class="sxs-lookup"><span data-stu-id="b735f-158">The image is tagged with the version number taken from the branch name.</span></span>
2. <span data-ttu-id="b735f-159">运行`helm package`打包服务 Helm 图表。</span><span class="sxs-lookup"><span data-stu-id="b735f-159">Run `helm package` to package the Helm chart for the service.</span></span> <span data-ttu-id="b735f-160">图表还使用版本号标记。</span><span class="sxs-lookup"><span data-stu-id="b735f-160">The chart is also tagged with a version number.</span></span>
3. <span data-ttu-id="b735f-161">Helm 包推送到容器注册表。</span><span class="sxs-lookup"><span data-stu-id="b735f-161">Push the Helm package to Container Registry.</span></span>

<span data-ttu-id="b735f-162">假设此生成成功，它将触发一个部署 (CD) 过程使用 Azure 管道[发布管道](/azure/devops/pipelines/release/what-is-release-management)。</span><span class="sxs-lookup"><span data-stu-id="b735f-162">Assuming this build succeeds, it triggers a deployment (CD) process using an Azure Pipelines [release pipeline](/azure/devops/pipelines/release/what-is-release-management).</span></span> <span data-ttu-id="b735f-163">此管道具有以下步骤：</span><span class="sxs-lookup"><span data-stu-id="b735f-163">This pipeline has the following steps:</span></span>

1. <span data-ttu-id="b735f-164">将 Helm 图表部署到 QA 环境。</span><span class="sxs-lookup"><span data-stu-id="b735f-164">Deploy the Helm chart to a QA environment.</span></span>
1. <span data-ttu-id="b735f-165">审批者签署同意书，然后包就会转到生产环境。</span><span class="sxs-lookup"><span data-stu-id="b735f-165">An approver signs off before the package moves to production.</span></span> <span data-ttu-id="b735f-166">请参阅[通过审批进行发布部署控制](/azure/devops/pipelines/release/approvals/approvals)。</span><span class="sxs-lookup"><span data-stu-id="b735f-166">See [Release deployment control using approvals](/azure/devops/pipelines/release/approvals/approvals).</span></span>
1. <span data-ttu-id="b735f-167">重新标记 Docker 图像可查看 Azure 容器注册表中的生产命名空间。</span><span class="sxs-lookup"><span data-stu-id="b735f-167">Retag the Docker image for the production namespace in Azure Container Registry.</span></span> <span data-ttu-id="b735f-168">例如，如果当前标记为 `myrepo.azurecr.io/delivery:v1.0.2`，则生产标记为 `myrepo.azurecr.io/prod/delivery:v1.0.2`。</span><span class="sxs-lookup"><span data-stu-id="b735f-168">For example, if the current tag is `myrepo.azurecr.io/delivery:v1.0.2`, the production tag is `myrepo.azurecr.io/prod/delivery:v1.0.2`.</span></span>
1. <span data-ttu-id="b735f-169">将 Helm 图表部署到生产环境。</span><span class="sxs-lookup"><span data-stu-id="b735f-169">Deploy the Helm chart to the production environment.</span></span>

<span data-ttu-id="b735f-170">即使在 monorepo，这些任务的范围可以为各个微服务，以便团队可以使用快速部署。</span><span class="sxs-lookup"><span data-stu-id="b735f-170">Even in a monorepo, these tasks can be scoped to individual microservices, so that teams can deploy with high velocity.</span></span> <span data-ttu-id="b735f-171">该过程有一些手动步骤：审批 PR、创建发布分库，以及审批部署到生产群集中的内容。</span><span class="sxs-lookup"><span data-stu-id="b735f-171">The process has some manual steps: Approving PRs, creating release branches, and approving deployments into the production cluster.</span></span> <span data-ttu-id="b735f-172">这些步骤都是手动过程由策略&mdash;它们如果组织倾向于可自动完成。</span><span class="sxs-lookup"><span data-stu-id="b735f-172">These steps are manual by policy &mdash; they could be automated if the organization prefers.</span></span>

## <a name="isolation-of-environments"></a><span data-ttu-id="b735f-173">环境隔离</span><span class="sxs-lookup"><span data-stu-id="b735f-173">Isolation of environments</span></span>

<span data-ttu-id="b735f-174">将在多个环境中部署服务，包括用于开发、版本验收测试、集成测试、负载测试和最终生产的环境。</span><span class="sxs-lookup"><span data-stu-id="b735f-174">You will have multiple environments where you deploy services, including environments for development, smoke testing, integration testing, load testing, and finally production.</span></span> <span data-ttu-id="b735f-175">这些环境需要某种程度的隔离。</span><span class="sxs-lookup"><span data-stu-id="b735f-175">These environments need some level of isolation.</span></span> <span data-ttu-id="b735f-176">在 Kubernetes 中，可以选择物理隔离或逻辑隔离。</span><span class="sxs-lookup"><span data-stu-id="b735f-176">In Kubernetes, you have a choice between physical isolation and logical isolation.</span></span> <span data-ttu-id="b735f-177">物理隔离表示部署到独立的群集。</span><span class="sxs-lookup"><span data-stu-id="b735f-177">Physical isolation means deploying to separate clusters.</span></span> <span data-ttu-id="b735f-178">逻辑隔离利用前面所述的命名空间和策略。</span><span class="sxs-lookup"><span data-stu-id="b735f-178">Logical isolation makes use of namespaces and policies, as described earlier.</span></span>

<span data-ttu-id="b735f-179">我们建议创建专用的生产群集，并为开发/测试环境创建独立的群集。</span><span class="sxs-lookup"><span data-stu-id="b735f-179">Our recommendation is to create a dedicated production cluster along with a separate cluster for your dev/test environments.</span></span> <span data-ttu-id="b735f-180">使用逻辑隔离来隔离开发/测试群集中的环境。</span><span class="sxs-lookup"><span data-stu-id="b735f-180">Use logical isolation to separate environments within the dev/test cluster.</span></span> <span data-ttu-id="b735f-181">部署到开发/测试群集的服务不得有权访问保存业务数据的数据存储。</span><span class="sxs-lookup"><span data-stu-id="b735f-181">Services deployed to the dev/test cluster should never have access to data stores that hold business data.</span></span>

## <a name="build-process"></a><span data-ttu-id="b735f-182">生成过程</span><span class="sxs-lookup"><span data-stu-id="b735f-182">Build process</span></span>

<span data-ttu-id="b735f-183">如果可能，请打包到 Docker 容器中生成过程。</span><span class="sxs-lookup"><span data-stu-id="b735f-183">When possible, package your build process into a Docker container.</span></span> <span data-ttu-id="b735f-184">这允许你生成而无需在每个生成计算机上配置的生成环境中使用 Docker，你的代码项目。</span><span class="sxs-lookup"><span data-stu-id="b735f-184">That allows you to build your code artifacts using Docker, without needing to configure the build environment on each build machine.</span></span> <span data-ttu-id="b735f-185">容器化的生成过程中添加新的生成代理可以轻松进行横向扩展 CI 管道的。</span><span class="sxs-lookup"><span data-stu-id="b735f-185">A containerized build process makes it easy to scale out the CI pipeline by adding new build agents.</span></span> <span data-ttu-id="b735f-186">此外，任何团队的开发人员可以生成的代码只需运行生成容器。</span><span class="sxs-lookup"><span data-stu-id="b735f-186">Also, any developer on the team can build the code simply by running the build container.</span></span>

<span data-ttu-id="b735f-187">通过在 Docker 中使用多阶段生成，可以在单个 Dockerfile 中定义的生成环境和运行时映像。</span><span class="sxs-lookup"><span data-stu-id="b735f-187">By using multi-stage builds in Docker, you can define the build environment and the runtime image in a single Dockerfile.</span></span> <span data-ttu-id="b735f-188">例如，下面是 Dockerfile，生成的 ASP.NET Core 应用程序：</span><span class="sxs-lookup"><span data-stu-id="b735f-188">For example, here's a Dockerfile that builds an ASP.NET Core application:</span></span>

```
FROM microsoft/dotnet:2.2-runtime AS base
WORKDIR /app

FROM microsoft/dotnet:2.2-sdk AS build
WORKDIR /src/Fabrikam.Workflow.Service

COPY Fabrikam.Workflow.Service/Fabrikam.Workflow.Service.csproj .
RUN dotnet restore Fabrikam.Workflow.Service.csproj

COPY Fabrikam.Workflow.Service/. .
RUN dotnet build Fabrikam.Workflow.Service.csproj -c Release -o /app

FROM build AS publish
RUN dotnet publish Fabrikam.Workflow.Service.csproj -c Release -o /app

FROM base AS final
WORKDIR /app
COPY --from=publish /app .
ENTRYPOINT ["dotnet", "Fabrikam.Workflow.Service.dll"]
```

<span data-ttu-id="b735f-189">&#11162;请参阅[源文件](https://github.com/mspnp/microservices-reference-implementation/blob/master/src/shipping/workflow/Dockerfile)。</span><span class="sxs-lookup"><span data-stu-id="b735f-189">&#11162; See the [source file](https://github.com/mspnp/microservices-reference-implementation/blob/master/src/shipping/workflow/Dockerfile).</span></span>

<span data-ttu-id="b735f-190">此 Dockerfile 定义多个生成阶段。</span><span class="sxs-lookup"><span data-stu-id="b735f-190">This Dockerfile defines several build stages.</span></span> <span data-ttu-id="b735f-191">请注意，名为阶段`base`ASP.NET Core 运行时，使用名为的阶段时`build`使用完整的 ASP.NET Core SDK。</span><span class="sxs-lookup"><span data-stu-id="b735f-191">Notice that the stage named `base` uses the ASP.NET Core runtime, while the stage named `build` uses the full ASP.NET Core SDK.</span></span> <span data-ttu-id="b735f-192">`build`阶段用于生成 ASP.NET Core 项目。</span><span class="sxs-lookup"><span data-stu-id="b735f-192">The `build` stage is used to build the ASP.NET Core project.</span></span> <span data-ttu-id="b735f-193">从生成最终运行时容器，但`base`，其中包含只运行时和显著小于完整的 SDK 映像。</span><span class="sxs-lookup"><span data-stu-id="b735f-193">But the final runtime container is built from `base`, which contains just the runtime and is significantly smaller than the full SDK image.</span></span>

### <a name="building-a-test-runner"></a><span data-ttu-id="b735f-194">生成的测试运行程序</span><span class="sxs-lookup"><span data-stu-id="b735f-194">Building a test runner</span></span>

<span data-ttu-id="b735f-195">另一种很好的做法是在容器中运行单元测试。</span><span class="sxs-lookup"><span data-stu-id="b735f-195">Another good practice is to run unit tests in the container.</span></span> <span data-ttu-id="b735f-196">例如，下面是生成的测试运行程序的 Docker 文件的一部分：</span><span class="sxs-lookup"><span data-stu-id="b735f-196">For example, here is part of a Docker file that builds a test runner:</span></span>

```
FROM build AS testrunner
WORKDIR /src/tests

COPY Fabrikam.Workflow.Service.Tests/*.csproj .
RUN dotnet restore Fabrikam.Workflow.Service.Tests.csproj

COPY Fabrikam.Workflow.Service.Tests/. .
ENTRYPOINT ["dotnet", "test", "--logger:trx"]
```

<span data-ttu-id="b735f-197">开发人员可以使用此 Docker 文件以在本地运行测试：</span><span class="sxs-lookup"><span data-stu-id="b735f-197">A developer can use this Docker file to run the tests locally:</span></span>

```bash
docker build . -t delivery-test:1 --target=testrunner
docker run -p 8080:8080 delivery-test:1
```

<span data-ttu-id="b735f-198">CI 管道还应对作为生成验证步骤的一部分运行这些测试。</span><span class="sxs-lookup"><span data-stu-id="b735f-198">The CI pipeline should also run the tests as part of the build verification step.</span></span>

<span data-ttu-id="b735f-199">请注意，此文件使用 Docker`ENTRYPOINT`命令来运行测试，不 Docker`RUN`命令。</span><span class="sxs-lookup"><span data-stu-id="b735f-199">Note that this file uses the Docker `ENTRYPOINT` command to run the tests, not the Docker `RUN` command.</span></span>

- <span data-ttu-id="b735f-200">如果使用`RUN`命令时，每次生成映像时运行测试。</span><span class="sxs-lookup"><span data-stu-id="b735f-200">If you use the `RUN` command, the tests run every time you build the image.</span></span> <span data-ttu-id="b735f-201">通过使用`ENTRYPOINT`，测试是可以选择的。</span><span class="sxs-lookup"><span data-stu-id="b735f-201">By using `ENTRYPOINT`, the tests are opt-in.</span></span> <span data-ttu-id="b735f-202">仅当显式目标时它们运行`testrunner`阶段。</span><span class="sxs-lookup"><span data-stu-id="b735f-202">They run only when you explicitly target the `testrunner` stage.</span></span>
- <span data-ttu-id="b735f-203">失败的测试不会导致 Docker`build`命令失败。</span><span class="sxs-lookup"><span data-stu-id="b735f-203">A failing test doesn't cause the Docker `build` command to fail.</span></span> <span data-ttu-id="b735f-204">你可以区分容器通过这种方式构建中测试失败的失败。</span><span class="sxs-lookup"><span data-stu-id="b735f-204">That way you can distinguish container build failures from test failures.</span></span>
- <span data-ttu-id="b735f-205">测试结果可以保存到已装入的卷。</span><span class="sxs-lookup"><span data-stu-id="b735f-205">Test results can be saved to a mounted volume.</span></span>

### <a name="container-best-practices"></a><span data-ttu-id="b735f-206">容器的最佳做法</span><span class="sxs-lookup"><span data-stu-id="b735f-206">Container best practices</span></span>

<span data-ttu-id="b735f-207">下面是一些其他最佳做法要考虑的容器：</span><span class="sxs-lookup"><span data-stu-id="b735f-207">Here are some other best practices to consider for containers:</span></span>

- <span data-ttu-id="b735f-208">针对要部署到群集中的资源（pod、服务等），定义组织范围的容器标记约定、版本控制和命名约定。</span><span class="sxs-lookup"><span data-stu-id="b735f-208">Define organization-wide conventions for container tags, versioning, and naming conventions for resources deployed to the cluster (pods, services, and so on).</span></span> <span data-ttu-id="b735f-209">这样，便可以更轻松地诊断部署问题。</span><span class="sxs-lookup"><span data-stu-id="b735f-209">That can make it easier to diagnose deployment issues.</span></span>

- <span data-ttu-id="b735f-210">在开发和测试周期，CI/CD 过程将生成许多容器映像。</span><span class="sxs-lookup"><span data-stu-id="b735f-210">During the development and test cycle, the CI/CD process will build many container images.</span></span> <span data-ttu-id="b735f-211">这些映像的一些仅适合添加到版本中，然后仅这一部分发布候选项会得到提升到生产环境。</span><span class="sxs-lookup"><span data-stu-id="b735f-211">Only some of those images are candidates for release, and then only some of those release candidates will get promoted to production.</span></span> <span data-ttu-id="b735f-212">具有一种明确的版本控制策略，以便您知道哪些映像当前部署到生产环境，并且可以回滚到以前的版本如有必要。</span><span class="sxs-lookup"><span data-stu-id="b735f-212">Have a clear versioning strategy, so that you know which images are currently deployed to production, and can roll back to a previous version if necessary.</span></span>

- <span data-ttu-id="b735f-213">在部署时始终特定容器版本标记不`latest`。</span><span class="sxs-lookup"><span data-stu-id="b735f-213">Always deploy specific container version tags, not `latest`.</span></span>

- <span data-ttu-id="b735f-214">使用[命名空间](/azure/container-registry/container-registry-best-practices#repository-namespaces)Azure 容器注册表来隔离批准用于生产仍正在测试的映像从映像中。</span><span class="sxs-lookup"><span data-stu-id="b735f-214">Use [namespaces](/azure/container-registry/container-registry-best-practices#repository-namespaces) in Azure Container Registry to isolate images that are approved for production from images that are still being tested.</span></span> <span data-ttu-id="b735f-215">不要移动到生产命名空间的图像，直到你准备好将其部署到生产环境。</span><span class="sxs-lookup"><span data-stu-id="b735f-215">Don't move an image into the production namespace until you're ready to deploy it into production.</span></span> <span data-ttu-id="b735f-216">如果将这种做法与容器映像的语义版本控制结合使用，则可以减少意外部署尚未批准发布的版本的可能性。</span><span class="sxs-lookup"><span data-stu-id="b735f-216">If you combine this practice with semantic versioning of container images, it can reduce the chance of accidentally deploying a version that wasn't approved for release.</span></span>

- <span data-ttu-id="b735f-217">遵循最小特权原则的非特权用户身份运行的容器。</span><span class="sxs-lookup"><span data-stu-id="b735f-217">Follow the principle of least privilege by running containers as a nonprivileged user.</span></span> <span data-ttu-id="b735f-218">在 Kubernetes 中，您可以创建以运行可防止容器的 pod 安全策略*根*。</span><span class="sxs-lookup"><span data-stu-id="b735f-218">In Kubernetes, you can create a pod security policy that prevents containers from running as *root*.</span></span> <span data-ttu-id="b735f-219">请参阅[阻止使用 Root 权限运行 Pod](https://docs.bitnami.com/kubernetes/how-to/secure-kubernetes-cluster-psp/)。</span><span class="sxs-lookup"><span data-stu-id="b735f-219">See [Prevent Pods From Running With Root Privileges](https://docs.bitnami.com/kubernetes/how-to/secure-kubernetes-cluster-psp/).</span></span>

## <a name="helm-charts"></a><span data-ttu-id="b735f-220">Helm 图表</span><span class="sxs-lookup"><span data-stu-id="b735f-220">Helm charts</span></span>

<span data-ttu-id="b735f-221">考虑使用 Helm 来管理服务的生成和部署。</span><span class="sxs-lookup"><span data-stu-id="b735f-221">Consider using Helm to manage building and deploying services.</span></span> <span data-ttu-id="b735f-222">下面是 helm 的一些帮助使用 CI/CD 的功能：</span><span class="sxs-lookup"><span data-stu-id="b735f-222">Here are some of the features of Helm that help with CI/CD:</span></span>

- <span data-ttu-id="b735f-223">通常由多个 Kubernetes 对象定义单个微服务。</span><span class="sxs-lookup"><span data-stu-id="b735f-223">Often a single microservice is defined by multiple Kubernetes objects.</span></span> <span data-ttu-id="b735f-224">Helm 允许这些对象打包到单个的 Helm 图表。</span><span class="sxs-lookup"><span data-stu-id="b735f-224">Helm allows these objects to be packaged into a single Helm chart.</span></span>
- <span data-ttu-id="b735f-225">图表可以部署与单个 Helm 命令，而不是一系列 kubectl 命令。</span><span class="sxs-lookup"><span data-stu-id="b735f-225">A chart can be deployed with a single Helm command, rather than a series of kubectl commands.</span></span>
- <span data-ttu-id="b735f-226">图表的显式版本控制。</span><span class="sxs-lookup"><span data-stu-id="b735f-226">Charts are explicitly versioned.</span></span> <span data-ttu-id="b735f-227">使用 Helm 发布的版本、 查看版本中，并回滚到以前的版本。</span><span class="sxs-lookup"><span data-stu-id="b735f-227">Use Helm to release a version, view releases, and roll back to a previous version.</span></span> <span data-ttu-id="b735f-228">使用语义版本控制以及用于回滚到以前版本的功能来跟踪更新和修订。</span><span class="sxs-lookup"><span data-stu-id="b735f-228">Tracking updates and revisions, using semantic versioning, along with the ability to roll back to a previous version.</span></span>
- <span data-ttu-id="b735f-229">Helm 图表使用模板来避免重复多个文件的信息，如标签和选择器。</span><span class="sxs-lookup"><span data-stu-id="b735f-229">Helm charts use templates to avoid duplicating information, such as labels and selectors, across many files.</span></span>
- <span data-ttu-id="b735f-230">Helm 可以管理图表之间的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="b735f-230">Helm can manage dependencies between charts.</span></span>
- <span data-ttu-id="b735f-231">图表可以存储在一个 Helm 存储库，如 Azure 容器注册表，并集成到生成管道。</span><span class="sxs-lookup"><span data-stu-id="b735f-231">Charts can be stored in a Helm repository, such as Azure Container Registry, and integrated into the build pipeline.</span></span>

<span data-ttu-id="b735f-232">有关将容器注册表用作 Helm 存储库的详细信息，请参阅[将 Azure 容器注册表用作应用程序图表的 Helm 存储库](/azure/container-registry/container-registry-helm-repos)。</span><span class="sxs-lookup"><span data-stu-id="b735f-232">For more information about using Container Registry as a Helm repository, see [Use Azure Container Registry as a Helm repository for your application charts](/azure/container-registry/container-registry-helm-repos).</span></span>

<span data-ttu-id="b735f-233">单个微服务可能涉及多个 Kubernetes 配置文件。</span><span class="sxs-lookup"><span data-stu-id="b735f-233">A single microservice may involve multiple Kubernetes configuration files.</span></span> <span data-ttu-id="b735f-234">更新服务可能意味着涉及所有这些文件，以更新选择器、 标签和图像标记。</span><span class="sxs-lookup"><span data-stu-id="b735f-234">Updating a service can mean touching all of these files to update selectors, labels, and image tags.</span></span> <span data-ttu-id="b735f-235">Helm 将它们作为名为图表的单个包，并允许您轻松地使用变量更新 YAML 文件。</span><span class="sxs-lookup"><span data-stu-id="b735f-235">Helm treats these as a single package called a chart and allows you to easily update the YAML files by using variables.</span></span> <span data-ttu-id="b735f-236">Helm 使用模板语言 （基于转到模板） 以便你可以编写参数化的 YAML 配置文件。</span><span class="sxs-lookup"><span data-stu-id="b735f-236">Helm uses a template language (based on Go templates) to let you write parameterized YAML configuration files.</span></span>

<span data-ttu-id="b735f-237">例如，下面是定义部署的 YAML 文件的一部分：</span><span class="sxs-lookup"><span data-stu-id="b735f-237">For example, here's part of a YAML file that defines a deployment:</span></span>

```yaml
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: {{ include "package.fullname" . | replace "." "" }}
  labels:
    app.kubernetes.io/name: {{ include "package.name" . }}
    app.kubernetes.io/instance: {{ .Release.Name }}
  annotations:
    kubernetes.io/change-cause: {{ .Values.reason }}

...

  spec:
      containers:
      - name: &package-container_name fabrikam-package
        image: {{ .Values.dockerregistry }}/{{ .Values.image.repository }}:{{ .Values.image.tag }}
        imagePullPolicy: {{ .Values.image.pullPolicy }}
        env:
        - name: LOG_LEVEL
          value: {{ .Values.log.level }}
```

<span data-ttu-id="b735f-238">&#11162;请参阅[源文件](https://github.com/mspnp/microservices-reference-implementation/blob/master/charts/package/templates/package-deploy.yaml)。</span><span class="sxs-lookup"><span data-stu-id="b735f-238">&#11162; See the [source file](https://github.com/mspnp/microservices-reference-implementation/blob/master/charts/package/templates/package-deploy.yaml).</span></span>

<span data-ttu-id="b735f-239">您可以看到部署名称、 标签和容器规范所有使用模板参数，在部署时提供。</span><span class="sxs-lookup"><span data-stu-id="b735f-239">You can see that the deployment name, labels, and container spec all use template parameters, which are provided at deployment time.</span></span> <span data-ttu-id="b735f-240">例如，从命令行：</span><span class="sxs-lookup"><span data-stu-id="b735f-240">For example, from the command line:</span></span>

```bash
helm install $HELM_CHARTS/package/ \
     --set image.tag=0.1.0 \
     --set image.repository=package \
     --set dockerregistry=$ACR_SERVER \
     --namespace backend \
     --name package-v0.1.0
```

<span data-ttu-id="b735f-241">尽管 CI/CD 管道无法安装直接到 Kubernetes 图表，但我们建议创建一个图表的存档 （.tgz 文件） 并将图表推送到 Helm 存储库，例如 Azure 容器注册表。</span><span class="sxs-lookup"><span data-stu-id="b735f-241">Although your CI/CD pipeline could install a chart directly to Kubernetes, we recommend creating a chart archive (.tgz file) and pushing the chart to a Helm repository such as Azure Container Registry.</span></span> <span data-ttu-id="b735f-242">有关详细信息，请参阅[基于包 Docker 的应用程序中 Azure 管道中的 Helm 图表](/azure/devops/pipelines/languages/helm?view=azure-devops)。</span><span class="sxs-lookup"><span data-stu-id="b735f-242">For more information, see [Package Docker-based apps in Helm charts in Azure Pipelines](/azure/devops/pipelines/languages/helm?view=azure-devops).</span></span>

<span data-ttu-id="b735f-243">请考虑将 Helm 部署到自己的命名空间和使用基于角色的访问控制 (RBAC) 来限制可以部署到的命名空间。</span><span class="sxs-lookup"><span data-stu-id="b735f-243">Consider deploying Helm to its own namespace and using role-based access control (RBAC) to restrict which namespaces it can deploy to.</span></span> <span data-ttu-id="b735f-244">有关详细信息，请参阅[基于角色的访问控制](https://helm.sh/docs/using_helm/#helm-and-role-based-access-control)Helm 文档中。</span><span class="sxs-lookup"><span data-stu-id="b735f-244">For more information, see [Role-based Access Control](https://helm.sh/docs/using_helm/#helm-and-role-based-access-control) in the Helm documentation.</span></span>

### <a name="revisions"></a><span data-ttu-id="b735f-245">修订</span><span class="sxs-lookup"><span data-stu-id="b735f-245">Revisions</span></span>

<span data-ttu-id="b735f-246">Helm 图表始终具有版本号，它必须使用[语义化版本控制](https://semver.org/)。</span><span class="sxs-lookup"><span data-stu-id="b735f-246">Helm charts always have a version number, which must use [semantic versioning](https://semver.org/).</span></span> <span data-ttu-id="b735f-247">可以在图表`appVersion`。</span><span class="sxs-lookup"><span data-stu-id="b735f-247">A chart can also have an `appVersion`.</span></span> <span data-ttu-id="b735f-248">此字段是可选的且无需与图表版本相关。</span><span class="sxs-lookup"><span data-stu-id="b735f-248">This field is optional, and doesn't have to be related to the chart version.</span></span> <span data-ttu-id="b735f-249">一些团队可能需要应用程序版本单独的图表内容的最新更新。</span><span class="sxs-lookup"><span data-stu-id="b735f-249">Some teams might want to application versions separately from updates to the charts.</span></span> <span data-ttu-id="b735f-250">但更简单的方法是使用一个版本号，因此图表版本和应用程序版本之间的 1 对 1 关系。</span><span class="sxs-lookup"><span data-stu-id="b735f-250">But a simpler approach is to use one version number, so there's a 1:1 relation between chart version and application version.</span></span> <span data-ttu-id="b735f-251">这样一来，可以存储每个发行一个图表并轻松地部署所需的版本：</span><span class="sxs-lookup"><span data-stu-id="b735f-251">That way, you can store one chart per release and easily deploy the desired release:</span></span>

```bash
helm install <package-chart-name> --version <desiredVersion>
```

<span data-ttu-id="b735f-252">另一种很好的做法是提供在部署模板中的更改原因批注：</span><span class="sxs-lookup"><span data-stu-id="b735f-252">Another good practice is to provide a change-cause annotation in the deployment template:</span></span>

```yaml
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: {{ include "delivery.fullname" . | replace "." "" }}
  labels:
     ...
  annotations:
    kubernetes.io/change-cause: {{ .Values.reason }}
```

<span data-ttu-id="b735f-253">这样可以查看每个修订版本的更改原因字段使用`kubectl rollout history`命令。</span><span class="sxs-lookup"><span data-stu-id="b735f-253">This lets you view the change-cause field for each revision, using the `kubectl rollout history` command.</span></span> <span data-ttu-id="b735f-254">在上一示例中，更改原因提供作为 Helm 图表参数。</span><span class="sxs-lookup"><span data-stu-id="b735f-254">In the previous example, the change-cause is provided as a Helm chart parameter.</span></span>

```bash
$ kubectl rollout history deployments/delivery-v010 -n backend
deployment.extensions/delivery-v010
REVISION  CHANGE-CAUSE
1         Initial deployment
```

<span data-ttu-id="b735f-255">此外可以使用`helm list`命令以查看修订版本历史记录：</span><span class="sxs-lookup"><span data-stu-id="b735f-255">You can also use the `helm list` command to view the revision history:</span></span>

```bash
~$ helm list
NAME            REVISION    UPDATED                     STATUS        CHART            APP VERSION     NAMESPACE
delivery-v0.1.0 1           Sun Apr  7 00:25:30 2019    DEPLOYED      delivery-v0.1.0  v0.1.0          backend
```

> [!TIP]
> <span data-ttu-id="b735f-256">使用`--history-max`标志初始化 Helm 时。</span><span class="sxs-lookup"><span data-stu-id="b735f-256">Use the `--history-max` flag when initializing Helm.</span></span> <span data-ttu-id="b735f-257">此设置将限制 Tiller 将保存其历史记录中的修订的号。</span><span class="sxs-lookup"><span data-stu-id="b735f-257">This setting limits the number of revisions that Tiller saves in its history.</span></span> <span data-ttu-id="b735f-258">Tiller configmaps 中存储修订版本历史记录。</span><span class="sxs-lookup"><span data-stu-id="b735f-258">Tiller stores revision history in configmaps.</span></span> <span data-ttu-id="b735f-259">如果要频繁发布更新，则 configmaps 可以变得很大，除非您限制历史记录大小。</span><span class="sxs-lookup"><span data-stu-id="b735f-259">If you're releasing updates frequently, the configmaps can grow very large unless you limit the history size.</span></span>

## <a name="azure-devops-pipeline"></a><span data-ttu-id="b735f-260">Azure DevOps Pipeline</span><span class="sxs-lookup"><span data-stu-id="b735f-260">Azure DevOps Pipeline</span></span>

<span data-ttu-id="b735f-261">在 Azure 的管道，管道被分成*生成管道*并*发布管道*。</span><span class="sxs-lookup"><span data-stu-id="b735f-261">In Azure Pipelines, pipelines are divided into *build pipelines* and *release pipelines*.</span></span> <span data-ttu-id="b735f-262">生成管道运行 CI 过程，并创建生成项目。</span><span class="sxs-lookup"><span data-stu-id="b735f-262">The build pipeline runs the CI process and creates build artifacts.</span></span> <span data-ttu-id="b735f-263">对于在 Kubernetes 上微服务体系结构，这些项目是容器映像并定义每个微服务的 Helm 图表。</span><span class="sxs-lookup"><span data-stu-id="b735f-263">For a microservices architecture on Kubernetes, these artifacts are the container images and Helm charts that define each microservice.</span></span> <span data-ttu-id="b735f-264">发布管道在运行该 CD 过程中的微服务部署到群集。</span><span class="sxs-lookup"><span data-stu-id="b735f-264">The release pipeline runs that CD process that deploys a microservice into a cluster.</span></span>

<span data-ttu-id="b735f-265">根据本文前面部分中所述的 CI 流程，生成管道可能包含以下任务：</span><span class="sxs-lookup"><span data-stu-id="b735f-265">Based on the CI flow described earlier in this article, a build pipeline might consist of the following tasks:</span></span>

1. <span data-ttu-id="b735f-266">生成测试运行程序容器。</span><span class="sxs-lookup"><span data-stu-id="b735f-266">Build the test runner container.</span></span>

    ```yaml
    - task: Docker@1
      inputs:
        azureSubscriptionEndpoint: $(AzureSubscription)
        azureContainerRegistry: $(AzureContainerRegistry)
        arguments: '--pull --target testrunner'
        dockerFile: $(System.DefaultWorkingDirectory)/$(dockerFileName)
        imageName: '$(imageName)-test'
    ```

1. <span data-ttu-id="b735f-267">通过调用对测试运行程序容器运行的 docker 中运行测试。</span><span class="sxs-lookup"><span data-stu-id="b735f-267">Run the tests, by invoking docker run against the test runner container.</span></span>

    ```yaml
    - task: Docker@1
      inputs:
        azureSubscriptionEndpoint: $(AzureSubscription)
        azureContainerRegistry: $(AzureContainerRegistry)
        command: 'run'
        containerName: testrunner
        volumes: '$(System.DefaultWorkingDirectory)/TestResults:/app/tests/TestResults'
        imageName: '$(imageName)-test'
        runInBackground: false
    ```

1. <span data-ttu-id="b735f-268">发布测试结果。</span><span class="sxs-lookup"><span data-stu-id="b735f-268">Publish the test results.</span></span> <span data-ttu-id="b735f-269">请参阅[集成生成和测试任务](/azure/devops/pipelines/languages/docker?view=azure-devops&tabs=yaml#integrate-build-and-test-tasks)。</span><span class="sxs-lookup"><span data-stu-id="b735f-269">See [Integrate build and test tasks](/azure/devops/pipelines/languages/docker?view=azure-devops&tabs=yaml#integrate-build-and-test-tasks).</span></span>

    ```yaml
    - task: PublishTestResults@2
      inputs:
        testResultsFormat: 'VSTest'
        testResultsFiles: 'TestResults/*.trx'
        searchFolder: '$(System.DefaultWorkingDirectory)'
        publishRunAttachments: true
    ```

1. <span data-ttu-id="b735f-270">生成运行时容器。</span><span class="sxs-lookup"><span data-stu-id="b735f-270">Build the runtime container.</span></span>

    ```yaml
    - task: Docker@1
      inputs:
        azureSubscriptionEndpoint: $(AzureSubscription)
        azureContainerRegistry: $(AzureContainerRegistry)
        dockerFile: $(System.DefaultWorkingDirectory)/$(dockerFileName)
        includeLatestTag: false
        imageName: '$(imageName)'
    ```

1. <span data-ttu-id="b735f-271">推送到 Azure 容器注册表 （或其他容器注册表） 的容器。</span><span class="sxs-lookup"><span data-stu-id="b735f-271">Push to the container to Azure Container Registry (or other container registry).</span></span>

    ```yaml
    - task: Docker@1
      inputs:
        azureSubscriptionEndpoint: $(AzureSubscription)
        azureContainerRegistry: $(AzureContainerRegistry)
        command: 'Push an image'
        imageName: '$(imageName)'
        includeSourceTags: false
    ```

1. <span data-ttu-id="b735f-272">包 Helm 图表。</span><span class="sxs-lookup"><span data-stu-id="b735f-272">Package the Helm chart.</span></span>

    ```yaml
    - task: HelmDeploy@0
      inputs:
        command: package
        chartPath: $(chartPath)
        chartVersion: $(Build.SourceBranchName)
        arguments: '--app-version $(Build.SourceBranchName)'
    ```

1. <span data-ttu-id="b735f-273">将 Helm 包推送到 Azure 容器注册表 （或其他 Helm 存储库）。</span><span class="sxs-lookup"><span data-stu-id="b735f-273">Push the Helm package to Azure Container Registry (or other Helm repository).</span></span>

    ```yaml
    task: AzureCLI@1
      inputs:
        azureSubscription: $(AzureSubscription)
        scriptLocation: inlineScript
        inlineScript: |
        az acr helm push $(System.ArtifactsDirectory)/$(repositoryName)-$(Build.SourceBranchName).tgz --name $(AzureContainerRegistry);
    ```

<span data-ttu-id="b735f-274">&#11162;请参阅[源文件](https://github.com/mspnp/microservices-reference-implementation/blob/master/src/shipping/delivery/azure-pipelines-ci.yml)。</span><span class="sxs-lookup"><span data-stu-id="b735f-274">&#11162; See the [source file](https://github.com/mspnp/microservices-reference-implementation/blob/master/src/shipping/delivery/azure-pipelines-ci.yml).</span></span>

<span data-ttu-id="b735f-275">CI 管道的输出是生产容器映像和微服务更新的 Helm 图表。</span><span class="sxs-lookup"><span data-stu-id="b735f-275">The output from the CI pipeline is a production-ready container image and an updated Helm chart for the microservice.</span></span> <span data-ttu-id="b735f-276">此时，发布管道可继续使用。</span><span class="sxs-lookup"><span data-stu-id="b735f-276">At this point, the release pipeline can take over.</span></span> <span data-ttu-id="b735f-277">它执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="b735f-277">It performs the following steps:</span></span>

- <span data-ttu-id="b735f-278">部署到开发人员/QA/过渡环境。</span><span class="sxs-lookup"><span data-stu-id="b735f-278">Deploy to dev/QA/staging environments.</span></span>
- <span data-ttu-id="b735f-279">等待审批者批准或拒绝部署。</span><span class="sxs-lookup"><span data-stu-id="b735f-279">Wait for an approver to approve or reject the deployment.</span></span>
- <span data-ttu-id="b735f-280">重新标记容器映像版本</span><span class="sxs-lookup"><span data-stu-id="b735f-280">Retag the container image for release</span></span>
- <span data-ttu-id="b735f-281">将发布标记推送到容器注册表。</span><span class="sxs-lookup"><span data-stu-id="b735f-281">Push the release tag to the container registry.</span></span>
- <span data-ttu-id="b735f-282">升级生产群集中的 Helm 图表。</span><span class="sxs-lookup"><span data-stu-id="b735f-282">Upgrade the Helm chart in the production cluster.</span></span>

<span data-ttu-id="b735f-283">有关创建发布管道的详细信息，请参阅[发布管道草稿版本中，并释放选项](/azure/devops/pipelines/release/?view=azure-devops)。</span><span class="sxs-lookup"><span data-stu-id="b735f-283">For more information about creating a release pipeline, see [Release pipelines, draft releases, and release options](/azure/devops/pipelines/release/?view=azure-devops).</span></span>

<span data-ttu-id="b735f-284">下图显示了在本文中所述的端到端 CI/CD 过程：</span><span class="sxs-lookup"><span data-stu-id="b735f-284">The following diagram shows the end-to-end CI/CD process described in this article:</span></span>

![CD/CD 管道](./images/aks-cicd-flow.png)

## <a name="next-steps"></a><span data-ttu-id="b735f-286">后续步骤</span><span class="sxs-lookup"><span data-stu-id="b735f-286">Next steps</span></span>

<span data-ttu-id="b735f-287">这篇文章的基础，可找到的引用实现[GitHub][ri]。</span><span class="sxs-lookup"><span data-stu-id="b735f-287">This article was based on a reference implementation that you can find on [GitHub][ri].</span></span>

[ri]: https://github.com/mspnp/microservices-reference-implementation