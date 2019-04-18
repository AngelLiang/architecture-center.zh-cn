---
title: R 机器学习模型的实时评分
description: 在 R 中使用运行于 Azure Kubernetes 服务 (AKS) 中的 Machine Learning Server 实现实时预测服务。
author: njray
ms.date: 12/12/2018
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: azcat-ai
ms.openlocfilehash: 5f3cc62c81c9ef9e5c3c27b1d66badd3e481c228
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59740419"
---
# <a name="real-time-scoring-of-r-machine-learning-models-on-azure"></a>Azure 上 R 机器学习模型的实时评分

本参考体系结构演示如何使用 Azure Kubernetes 服务 (AKS) 中运行的 Microsoft Machine Learning Server 在 R 中实施实时（同步）预测服务。 本体系结构是一个通用的体系结构，适用于在 R 中生成的、要作为实时服务部署的任何预测模型。 **[部署此解决方案][github]**。

## <a name="architecture"></a>体系结构

![Azure 上 R 机器学习模型的实时评分][0]

本参考体系结构采用基于容器的方法。 生成一个 Docker 映像，其中包含 R，以及为新数据评分所需的各种项目。 这些项目包括模型对象本身，以及一个评分脚本。 此映像将推送到 Azure 中托管的 Docker 注册表，然后部署到同样位于 Azure 中的 Kubernetes 群集。

此工作流的体系结构包括以下组件。

- **[Azure 容器注册表][acr]** 用于存储此工作流的映像。 可以通过标准的 [Docker 注册表 V2 API][docker] 和客户端来管理使用容器注册表创建的注册表。

- **[Azure Kubernetes 服务][aks]** 用于托管部署和服务。 可以通过标准的 [Kubernetes API][k-api] 和客户端 (kubectl) 来管理使用 AKS 创建的群集。

- **[Microsoft Machine Learning Server][mmls]** 用于定义服务的 REST API，其中包含[模型操作化][operationalization]。 此面向服务的 Web 服务器进程将侦听请求；然后，这些请求将转发到其他运行实际 R 代码的后台进程，以生成结果。 在此配置中，所有这些进程在包装于某个容器中的单个节点上运行。 有关在开发或测试环境外部使用此服务的详细信息，请与 Microsoft 代表联系。

## <a name="performance-considerations"></a>性能注意事项

在训练和为新数据评分时，机器学习工作负荷往往是计算密集型的。 根据经验法则，请勿尝试在每个核心中运行多个评分进程。 Machine Learning Server 允许定义每个容器中运行的 R 进程数。 默认设置为 5 个进程。 创建相对简单的模型（例如，包含少量变量的线性回归，或小型决策树）时，可以增加进程数。 监视群集节点上的 CPU 负载，以确定容器数的适当限制。

支持 GPU 的群集可以加速某些类型的工作负荷，尤其是深度学习模型。 并非所有工作负荷都可以利用 GPU &mdash; 只有重度使用矩阵代数的工作负荷才可以。 例如，基于树的模型，包括随机林和提升模型，通常不能从 GPU 获得优势。

某些模型类型（例如随机林）可在 CPU 上大规模并行化。 在这种情况下，可以通过将工作负荷分散在多个核心之间来加速单个请求的评分。 但是，假设群集大小固定，则这样做会减少用于处理多个评分请求的容量。

一般而言，开源 R 模型会将其所有数据存储在内存中，因此，请确保节点有足够的内存，可以容纳你要同时运行的进程。 如果使用 Machine Learning Server 来拟合模型，请使用可以处理磁盘中数据的库，而不要将这些数据全部读入内存中。 这可以明显降低内存要求。 不管是使用 Machine Learning Server 还是开源 R，都应该监视节点，以确保评分进程不会耗尽内存。

## <a name="security-considerations"></a>安全注意事项

### <a name="network-encryption"></a>网络加密

在本参考体系结构中，已启用 HTTPS 来与群集通信，并使用了 [Let's Encrypt][encrypt] 提供的临时证书。 对于生产用途，请改用你自己的、由相应签名机构提供的证书。

### <a name="authentication-and-authorization"></a>身份验证和授权

Machine Learning Server [模型操作化][operationalization]要求对评分请求进行身份验证。 此部署中使用了用户名和密码。 在企业设置中，可以使用 [Azure Active Directory][AAD] 启用身份验证，或者使用 [Azure API 管理][API]创建独立的前端。

要使模型操作化与容器中的 Machine Learning Server 正常配合工作，必须安装 JSON Web 令牌 (JWT) 证书。 此部署使用 Microsoft 提供的证书。 在生产设置中，请提供自己的证书。

对于容器注册表与 AKS 之间的流量，请考虑启用[基于角色的访问控制][rbac] (RBAC)，以将访问特权限制给需要这些特权的用户。

### <a name="separate-storage"></a>独立的存储

本参考体系结构将应用程序 (R) 和数据（模型对象和评分脚本）捆绑在单个映像中。 在某些情况下，将它们隔离可能更有利。 可将模型数据和代码放入 Azure Blob 或文件[存储][storage]，并在容器初始化时检索它们。 在这种情况下，请确保将存储帐户设置为仅允许在身份验证后进行访问，并要求使用 HTTPS。

## <a name="monitoring-and-logging-considerations"></a>监视和日志记录注意事项

使用 [Kubernetes 仪表板][dashboard]监视 AKS 群集的总体状态。 在 Azure 门户中查看群集的概述边栏选项卡以了解更多详细信息。 [GitHub][github] 资源也介绍了如何通过 R 打开仪表板。

尽管仪表板可以提供群集总体运行状况的视图，但跟踪单个容器的状态也很重要。 为此，请在 Azure 门户中通过群集概述边栏选项卡启用 [Azure Monitor Insights][monitor]，或参阅[用于容器的 Azure Monitor][monitor-containers]（预览版）。

## <a name="cost-considerations"></a>成本注意事项

Machine Learning Server 按核心授权，这包括群集中要运行 Machine Learning Server 的所有核心。 如果你是 Machine Learning Server 或 Microsoft SQL Server 企业客户，请与 Microsoft 代表联系，以获取价格详细信息。

可以取代 Machine Learning Server 的开源产品是 [Plumber][plumber] - 一个可将代码转换为 REST API 的 R 包。 Plumber 的功能不如 Machine Learning Server 那样全面。 例如，默认情况下，它不包括任何可提供请求身份验证的功能。 如果使用 Plumber，我们建议启用 [Azure API 管理][API]来处理身份验证详细信息。

除了授权以外，主要成本考虑因素还包括 Kubernetes 群集的计算资源。 群集必须足够大才能处理高峰期的预期请求量，但这会在其他时段将资源保持在闲置状态。 若要限制闲置资源的影响，请使用 kubectl 工具为群集启用[水平自动缩放程序][autoscaler]。 或使用 AKS [群集自动缩放程序][cluster-autoscaler]。

## <a name="deploy-the-solution"></a>部署解决方案

[GitHub][github] 中提供了本体系结构的参考实现。 遵循该文章中的步骤可将一个简单的预测模型部署为服务。

<!-- links -->
[AAD]: /azure/active-directory/fundamentals/active-directory-whatis
[API]: /azure/api-management/api-management-key-concepts
[ACR]: /azure/container-registry/container-registry-intro
[AKS]: /azure/aks/intro-kubernetes
[autoscaler]: https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/
[cluster-autoscaler]: /azure/aks/autoscaler
[monitor]: /azure/monitoring/monitoring-container-insights-overview
[dashboard]: /azure/aks/kubernetes-dashboard
[docker]: https://docs.docker.com/registry/spec/api/
[encrypt]: https://letsencrypt.org/
[gitHub]: https://github.com/Azure/RealtimeRDeployment
[K-API]: https://kubernetes.io/docs/reference/
[MMLS]: /machine-learning-server/what-is-machine-learning-server
[monitor-containers]: /azure/azure-monitor/insights/container-insights-overview
[operationalization]: /machine-learning-server/what-is-operationalization
[plumber]: https://www.rplumber.io
[RBAC]: /azure/role-based-access-control/overview
[storage]: /azure/storage/common/storage-introduction
[0]: ./_images/realtime-scoring-r.png
