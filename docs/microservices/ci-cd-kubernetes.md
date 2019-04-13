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

# <a name="building-a-cicd-pipeline-for-microservices-on-kubernetes"></a>在 Kubernetes 上构建微服务的 CI/CD 管道

它极具挑战性创建微服务体系结构的可靠的 CI/CD 进程。 各个团队必须能够快速可靠地发布服务，而无需中断其他团队或破坏作为一个整体应用程序。

本指南介绍了用于部署到 Azure Kubernetes 服务 (AKS) 的微服务的示例中的 CI/CD 管道。 每个团队和项目都不同，因此不需要为一组严格的规则的这篇文章。 相反，其目的是要设计您自己的 CI/CD 过程的起点。

此处所述的示例中管道已创建名为无人机交付应用程序，可在上找到的微服务引用实现[GitHub][ri]。 描述了应用程序方案[此处](./design/index.md#reference-implementation)。

管道的目标可以总结如下：

- 团队可以生成和独立部署其服务。
- 将 CI 过程的代码更改会自动部署到类似生产的环境。
- 每个阶段的管道强制实施质量要求。
- 可以与以前的版本并行部署服务的新版本。

有关详细背景信息，请参阅[微服务体系结构的 CI/CD](./ci-cd.md)。

## <a name="assumptions"></a>假设

对于此示例的目的，这里有一些假设开发团队和代码基：

- 代码存储库是 monorepo，按微服务的文件夹。
- 团队的分库策略以[基于主库的开发](https://trunkbaseddevelopment.com/)为基础。
- 该团队使用[发布分支](/azure/devops/repos/git/git-branching-guidance?view=azure-devops#manage-releases)管理发布。 为每个微服务创建单独的版本。
- 使用 CI/CD 过程[Azure 管道](/azure/devops/pipelines/?view=azure-devops)若要生成，测试，并将微服务部署到 AKS。
- 为每个微服务的容器映像存储在[Azure 容器注册表](/azure/container-registry/)。
- 团队使用 Helm 图表来打包每个微服务。

这些假设驱动许多 CI/CD 管道的特定详细信息。 但是，此处所述的基本方法进行调整其他流程、 工具和服务，例如 Jenkins 或 Docker Hub。

## <a name="validation-builds"></a>验证生成

假设在名为交付服务的微服务的开发人员正常工作。 在开发新功能时，开发人员会将代码签入到某个功能分库中。 根据约定，功能分库名为 `feature/*`。

![CI/CD 工作流](./images/aks-cicd-1.png)

生成定义文件包括按分支名称和源路径筛选器的触发器：

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

&#11162;请参阅[源文件](https://github.com/mspnp/microservices-reference-implementation/blob/master/src/shipping/delivery/azure-pipelines-validation.yml)。

使用此方法，每个团队都可以有自己的生成管道。 已签入的代码`/src/shipping/delivery`文件夹触发传送服务的生成。 将提交内容推送到与筛选器匹配的分支会触发一个 CI 生成。 在工作流中，CI 生成此时会运行某种最低程度的代码验证：

1. 生成代码。
1. 运行单元测试。

目标是要保留生成时间短，因此开发人员可以获得快速反馈。 开发人员功能合并到主分支合并准备就绪后，打开 pr。 此时会触发另一个 CI 生成来执行一些其他的检查：

1. 生成代码。
1. 运行单元测试。
1. 生成运行时容器映像。
1. 在映像上运行漏洞扫描。

![CI/CD 工作流](./images/aks-cicd-2.png)

> [!NOTE]
> 在 Azure DevOps 存储库，你可以定义[策略](/azure/devops/repos/git/branch-policies)以保护分支。 例如，策略可以要求在合并到主库之前，必须成功完成 CI 生成并由审批人员签署同意书。

## <a name="full-cicd-build"></a>完整的 CI/CD 生成

有时候，团队可以部署新版传送服务。 从与此命名模式的主版本管理器创建的分支： `release/<microservice name>/<semver>`。 例如，`release/delivery/v1.0.2`。

![CI/CD 工作流](./images/aks-cicd-3.png)

创建此分支将触发完整的 CI 生成运行的所有加上以前的步骤：

1. 容器映像推送到 Azure 容器注册表。 该映像标记有版本号（取自分库名称）。
2. 运行`helm package`打包服务 Helm 图表。 图表还使用版本号标记。
3. Helm 包推送到容器注册表。

假设此生成成功，它将触发一个部署 (CD) 过程使用 Azure 管道[发布管道](/azure/devops/pipelines/release/what-is-release-management)。 此管道具有以下步骤：

1. 将 Helm 图表部署到 QA 环境。
1. 审批者签署同意书，然后包就会转到生产环境。 请参阅[通过审批进行发布部署控制](/azure/devops/pipelines/release/approvals/approvals)。
1. 重新标记 Docker 图像可查看 Azure 容器注册表中的生产命名空间。 例如，如果当前标记为 `myrepo.azurecr.io/delivery:v1.0.2`，则生产标记为 `myrepo.azurecr.io/prod/delivery:v1.0.2`。
1. 将 Helm 图表部署到生产环境。

即使在 monorepo，这些任务的范围可以为各个微服务，以便团队可以使用快速部署。 该过程有一些手动步骤：审批 PR、创建发布分库，以及审批部署到生产群集中的内容。 这些步骤都是手动过程由策略&mdash;它们如果组织倾向于可自动完成。

## <a name="isolation-of-environments"></a>环境隔离

将在多个环境中部署服务，包括用于开发、版本验收测试、集成测试、负载测试和最终生产的环境。 这些环境需要某种程度的隔离。 在 Kubernetes 中，可以选择物理隔离或逻辑隔离。 物理隔离表示部署到独立的群集。 逻辑隔离利用前面所述的命名空间和策略。

我们建议创建专用的生产群集，并为开发/测试环境创建独立的群集。 使用逻辑隔离来隔离开发/测试群集中的环境。 部署到开发/测试群集的服务不得有权访问保存业务数据的数据存储。

## <a name="build-process"></a>生成过程

如果可能，请打包到 Docker 容器中生成过程。 这允许你生成而无需在每个生成计算机上配置的生成环境中使用 Docker，你的代码项目。 容器化的生成过程中添加新的生成代理可以轻松进行横向扩展 CI 管道的。 此外，任何团队的开发人员可以生成的代码只需运行生成容器。

通过在 Docker 中使用多阶段生成，可以在单个 Dockerfile 中定义的生成环境和运行时映像。 例如，下面是 Dockerfile，生成的 ASP.NET Core 应用程序：

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

&#11162;请参阅[源文件](https://github.com/mspnp/microservices-reference-implementation/blob/master/src/shipping/workflow/Dockerfile)。

此 Dockerfile 定义多个生成阶段。 请注意，名为阶段`base`ASP.NET Core 运行时，使用名为的阶段时`build`使用完整的 ASP.NET Core SDK。 `build`阶段用于生成 ASP.NET Core 项目。 从生成最终运行时容器，但`base`，其中包含只运行时和显著小于完整的 SDK 映像。

### <a name="building-a-test-runner"></a>生成的测试运行程序

另一种很好的做法是在容器中运行单元测试。 例如，下面是生成的测试运行程序的 Docker 文件的一部分：

```
FROM build AS testrunner
WORKDIR /src/tests

COPY Fabrikam.Workflow.Service.Tests/*.csproj .
RUN dotnet restore Fabrikam.Workflow.Service.Tests.csproj

COPY Fabrikam.Workflow.Service.Tests/. .
ENTRYPOINT ["dotnet", "test", "--logger:trx"]
```

开发人员可以使用此 Docker 文件以在本地运行测试：

```bash
docker build . -t delivery-test:1 --target=testrunner
docker run -p 8080:8080 delivery-test:1
```

CI 管道还应对作为生成验证步骤的一部分运行这些测试。

请注意，此文件使用 Docker`ENTRYPOINT`命令来运行测试，不 Docker`RUN`命令。

- 如果使用`RUN`命令时，每次生成映像时运行测试。 通过使用`ENTRYPOINT`，测试是可以选择的。 仅当显式目标时它们运行`testrunner`阶段。
- 失败的测试不会导致 Docker`build`命令失败。 你可以区分容器通过这种方式构建中测试失败的失败。
- 测试结果可以保存到已装入的卷。

### <a name="container-best-practices"></a>容器的最佳做法

下面是一些其他最佳做法要考虑的容器：

- 针对要部署到群集中的资源（pod、服务等），定义组织范围的容器标记约定、版本控制和命名约定。 这样，便可以更轻松地诊断部署问题。

- 在开发和测试周期，CI/CD 过程将生成许多容器映像。 这些映像的一些仅适合添加到版本中，然后仅这一部分发布候选项会得到提升到生产环境。 具有一种明确的版本控制策略，以便您知道哪些映像当前部署到生产环境，并且可以回滚到以前的版本如有必要。

- 在部署时始终特定容器版本标记不`latest`。

- 使用[命名空间](/azure/container-registry/container-registry-best-practices#repository-namespaces)Azure 容器注册表来隔离批准用于生产仍正在测试的映像从映像中。 不要移动到生产命名空间的图像，直到你准备好将其部署到生产环境。 如果将这种做法与容器映像的语义版本控制结合使用，则可以减少意外部署尚未批准发布的版本的可能性。

- 遵循最小特权原则的非特权用户身份运行的容器。 在 Kubernetes 中，您可以创建以运行可防止容器的 pod 安全策略*根*。 请参阅[阻止使用 Root 权限运行 Pod](https://docs.bitnami.com/kubernetes/how-to/secure-kubernetes-cluster-psp/)。

## <a name="helm-charts"></a>Helm 图表

考虑使用 Helm 来管理服务的生成和部署。 下面是 helm 的一些帮助使用 CI/CD 的功能：

- 通常由多个 Kubernetes 对象定义单个微服务。 Helm 允许这些对象打包到单个的 Helm 图表。
- 图表可以部署与单个 Helm 命令，而不是一系列 kubectl 命令。
- 图表的显式版本控制。 使用 Helm 发布的版本、 查看版本中，并回滚到以前的版本。 使用语义版本控制以及用于回滚到以前版本的功能来跟踪更新和修订。
- Helm 图表使用模板来避免重复多个文件的信息，如标签和选择器。
- Helm 可以管理图表之间的依赖关系。
- 图表可以存储在一个 Helm 存储库，如 Azure 容器注册表，并集成到生成管道。

有关将容器注册表用作 Helm 存储库的详细信息，请参阅[将 Azure 容器注册表用作应用程序图表的 Helm 存储库](/azure/container-registry/container-registry-helm-repos)。

单个微服务可能涉及多个 Kubernetes 配置文件。 更新服务可能意味着涉及所有这些文件，以更新选择器、 标签和图像标记。 Helm 将它们作为名为图表的单个包，并允许您轻松地使用变量更新 YAML 文件。 Helm 使用模板语言 （基于转到模板） 以便你可以编写参数化的 YAML 配置文件。

例如，下面是定义部署的 YAML 文件的一部分：

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

&#11162;请参阅[源文件](https://github.com/mspnp/microservices-reference-implementation/blob/master/charts/package/templates/package-deploy.yaml)。

您可以看到部署名称、 标签和容器规范所有使用模板参数，在部署时提供。 例如，从命令行：

```bash
helm install $HELM_CHARTS/package/ \
     --set image.tag=0.1.0 \
     --set image.repository=package \
     --set dockerregistry=$ACR_SERVER \
     --namespace backend \
     --name package-v0.1.0
```

尽管 CI/CD 管道无法安装直接到 Kubernetes 图表，但我们建议创建一个图表的存档 （.tgz 文件） 并将图表推送到 Helm 存储库，例如 Azure 容器注册表。 有关详细信息，请参阅[基于包 Docker 的应用程序中 Azure 管道中的 Helm 图表](/azure/devops/pipelines/languages/helm?view=azure-devops)。

请考虑将 Helm 部署到自己的命名空间和使用基于角色的访问控制 (RBAC) 来限制可以部署到的命名空间。 有关详细信息，请参阅[基于角色的访问控制](https://helm.sh/docs/using_helm/#helm-and-role-based-access-control)Helm 文档中。

### <a name="revisions"></a>修订

Helm 图表始终具有版本号，它必须使用[语义化版本控制](https://semver.org/)。 可以在图表`appVersion`。 此字段是可选的且无需与图表版本相关。 一些团队可能需要应用程序版本单独的图表内容的最新更新。 但更简单的方法是使用一个版本号，因此图表版本和应用程序版本之间的 1 对 1 关系。 这样一来，可以存储每个发行一个图表并轻松地部署所需的版本：

```bash
helm install <package-chart-name> --version <desiredVersion>
```

另一种很好的做法是提供在部署模板中的更改原因批注：

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

这样可以查看每个修订版本的更改原因字段使用`kubectl rollout history`命令。 在上一示例中，更改原因提供作为 Helm 图表参数。

```bash
$ kubectl rollout history deployments/delivery-v010 -n backend
deployment.extensions/delivery-v010
REVISION  CHANGE-CAUSE
1         Initial deployment
```

此外可以使用`helm list`命令以查看修订版本历史记录：

```bash
~$ helm list
NAME            REVISION    UPDATED                     STATUS        CHART            APP VERSION     NAMESPACE
delivery-v0.1.0 1           Sun Apr  7 00:25:30 2019    DEPLOYED      delivery-v0.1.0  v0.1.0          backend
```

> [!TIP]
> 使用`--history-max`标志初始化 Helm 时。 此设置将限制 Tiller 将保存其历史记录中的修订的号。 Tiller configmaps 中存储修订版本历史记录。 如果要频繁发布更新，则 configmaps 可以变得很大，除非您限制历史记录大小。

## <a name="azure-devops-pipeline"></a>Azure DevOps Pipeline

在 Azure 的管道，管道被分成*生成管道*并*发布管道*。 生成管道运行 CI 过程，并创建生成项目。 对于在 Kubernetes 上微服务体系结构，这些项目是容器映像并定义每个微服务的 Helm 图表。 发布管道在运行该 CD 过程中的微服务部署到群集。

根据本文前面部分中所述的 CI 流程，生成管道可能包含以下任务：

1. 生成测试运行程序容器。

    ```yaml
    - task: Docker@1
      inputs:
        azureSubscriptionEndpoint: $(AzureSubscription)
        azureContainerRegistry: $(AzureContainerRegistry)
        arguments: '--pull --target testrunner'
        dockerFile: $(System.DefaultWorkingDirectory)/$(dockerFileName)
        imageName: '$(imageName)-test'
    ```

1. 通过调用对测试运行程序容器运行的 docker 中运行测试。

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

1. 发布测试结果。 请参阅[集成生成和测试任务](/azure/devops/pipelines/languages/docker?view=azure-devops&tabs=yaml#integrate-build-and-test-tasks)。

    ```yaml
    - task: PublishTestResults@2
      inputs:
        testResultsFormat: 'VSTest'
        testResultsFiles: 'TestResults/*.trx'
        searchFolder: '$(System.DefaultWorkingDirectory)'
        publishRunAttachments: true
    ```

1. 生成运行时容器。

    ```yaml
    - task: Docker@1
      inputs:
        azureSubscriptionEndpoint: $(AzureSubscription)
        azureContainerRegistry: $(AzureContainerRegistry)
        dockerFile: $(System.DefaultWorkingDirectory)/$(dockerFileName)
        includeLatestTag: false
        imageName: '$(imageName)'
    ```

1. 推送到 Azure 容器注册表 （或其他容器注册表） 的容器。

    ```yaml
    - task: Docker@1
      inputs:
        azureSubscriptionEndpoint: $(AzureSubscription)
        azureContainerRegistry: $(AzureContainerRegistry)
        command: 'Push an image'
        imageName: '$(imageName)'
        includeSourceTags: false
    ```

1. 包 Helm 图表。

    ```yaml
    - task: HelmDeploy@0
      inputs:
        command: package
        chartPath: $(chartPath)
        chartVersion: $(Build.SourceBranchName)
        arguments: '--app-version $(Build.SourceBranchName)'
    ```

1. 将 Helm 包推送到 Azure 容器注册表 （或其他 Helm 存储库）。

    ```yaml
    task: AzureCLI@1
      inputs:
        azureSubscription: $(AzureSubscription)
        scriptLocation: inlineScript
        inlineScript: |
        az acr helm push $(System.ArtifactsDirectory)/$(repositoryName)-$(Build.SourceBranchName).tgz --name $(AzureContainerRegistry);
    ```

&#11162;请参阅[源文件](https://github.com/mspnp/microservices-reference-implementation/blob/master/src/shipping/delivery/azure-pipelines-ci.yml)。

CI 管道的输出是生产容器映像和微服务更新的 Helm 图表。 此时，发布管道可继续使用。 它执行以下步骤：

- 部署到开发人员/QA/过渡环境。
- 等待审批者批准或拒绝部署。
- 重新标记容器映像版本
- 将发布标记推送到容器注册表。
- 升级生产群集中的 Helm 图表。

有关创建发布管道的详细信息，请参阅[发布管道草稿版本中，并释放选项](/azure/devops/pipelines/release/?view=azure-devops)。

下图显示了在本文中所述的端到端 CI/CD 过程：

![CD/CD 管道](./images/aks-cicd-flow.png)

## <a name="next-steps"></a>后续步骤

这篇文章的基础，可找到的引用实现[GitHub][ri]。

[ri]: https://github.com/mspnp/microservices-reference-implementation