---
title: Python 模型的实时评分
titleSuffix: Azure Reference Architectures
description: 本参考体系结构演示如何在 Azure 上将 Python 模型部署为 Web 服务，用于进行实时预测。
author: msalvaris
ms.date: 01/28/2019
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: azcat-ai
ms.openlocfilehash: ba2d9a295e5a231f0ffca9e3cf2d53ace4deddfe
ms.sourcegitcommit: 1ee873aaf40010eb2a38314ac56974bc9e227736
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2019
ms.locfileid: "55141036"
---
# <a name="real-time-scoring-of-python-scikit-learn-and-deep-learning-models-on-azure"></a>Azure 上 Python Scikit-Learn 和深度学习模型的实时评分

本参考体系结构演示如何使用 Azure 机器学习服务将 Python 模型部署为 Web 服务用于进行实时预测。 将介绍两种方案：部署常规 Python 模型，以及部署深度学习模型所要满足的具体要求。 这两种方案都使用下面所示的体系结构。

GitHub 上提供了本体系结构的两种参考实现，一个适用于[常规 Python 模型][github-python]，另一个适用于[深度学习模型][github-dl]。

![Azure 上 Python 模型的实时评分的体系结构图](./_images/python-model-architecture.png)

## <a name="scenarios"></a>方案

参考实现演示使用本体系结构的两种方案。

**方案 1：常见问题解答匹配**。 此方案演示如何将常见问题 (FAQ) 匹配模型部署为 Web 服务，用于对用户的问题提供预测。 对于此方案，体系结构示意图中的“输入数据”是指包含要与 FAQ 列表匹配的用户问题的文本字符串。 此方案是针对适用于 Python 的 [scikit-learn][scikit] 机器学习库设计的，但可通用化为使用 Python 模型进行实时预测的任何方案。

此方案使用堆栈溢出问题数据的子集，其中包括标记为 JavaScript 的原始问题、其重复问题及其答案。 此方案训练某个 scikit-learn 管道来预测某个重复问题与每个原始问题匹配的概率。 这些预测是使用 REST API 终结点实时做出的。

本体系结构的应用程序流如下：

1. 将训练的模型注册到机器学习模型注册表。
2. 机器学习服务创建包含模型和评分脚本的 Docker 映像。
3. 机器学习将 Azure Kubernetes 服务 (AKS) 中的评分映像部署为 Web 服务。
4. 客户端发送包含已编码问题数据的 HTTP POST 请求。
5. 机器学习创建的 Web 服务从请求中提取问题。
6. 将问题发送到 Scikit-learn 管道模型进行特征化和评分。 
7. 将匹配的 FAQ 问题及其评分返回给客户端。

下面是使用结果的示例应用的屏幕截图：

![示例应用的屏幕截图](./_images/python-faq-matches.png)

**方案 2：图像分类**。 此方案演示如何将卷积神经网络 (CNN) 模型部署为 Web 服务，以基于图像提供预测。 对于此方案，体系结构示意图中的“输入数据”指图像文件。 在计算机视觉领域，CNN 对图像分类和对象检测等任务非常有效。 此方案是针对 TensorFlow、Keras（具有 TensorFlow 后端）和 PyTorch 等框架设计的。 但是，它可以通用化为使用深度学习模型进行实时预测的任何方案。

此方案使用基于 ImageNet-1K（1,000 个类）数据集预先训练的 ResNet-152 模型来预测某个图像属于哪些类别（参阅下图）。 这些预测是使用 REST API 终结点实时做出的。

![预测示例](./_images/python-example-predictions.png)

深度学习模型的应用程序流如下：

1. 将深度学习模型注册到机器学习模型注册表。
2. 机器学习服务创建包含模型和评分脚本的 Docker 映像。
3. 机器学习将 Azure Kubernetes 服务 (AKS) 中的评分映像部署为 Web 服务。
4. 客户端发送包含已编码图像数据的 HTTP POST 请求。
5. 机器学习创建的 Web 服务对图像数据进行预处理，然后将其发送到模型进行评分。 
6. 将预测的类别及其评分返回给客户端。

## <a name="architecture"></a>体系结构

该体系结构包括以下组件。

**[Azure 机器学习服务][aml]** 是一项云服务，可以使用它来训练、部署、自动执行以及管理机器学习模型，所有这些都是在云提供的广泛范围内进行的。 本体系结构使用该服务来管理模型的部署，以及 Web 服务的身份验证、路由和负载均衡。

**[虚拟机][vm]** (VM)。 VM 显示为本地或云中的设备示例，它可以发送 HTTP 请求。

**[Azure Kubernetes 服务][aks]** (AKS) 用于在 Kubernetes 群集上部署应用程序。 AKS 简化了 Kubernetes 的部署和操作。 可以使用仅有 CPU 的 VM 为常规 Python 模型配置群集，或使用支持 GPU 的 VM 为深度学习模型配置群集。

**[Azure 容器注册表][acr]** 用于存储所有类型的 Docker 容器部署（包括 DC/OS、Docker Swarm 和 Kubernetes）的映像。 评分映像部署为 Azure Kubernetes 服务上的容器，用于运行评分脚本。 此处使用的映像是机器学习基于训练的模型和评分脚本创建的，随后将推送到 Azure 容器注册表。

## <a name="performance-considerations"></a>性能注意事项

对于实时评分体系结构，吞吐量性能是首要考虑因素。 对于常规 Python 模型，仅使用 CPU 通常就足以处理工作负荷。

但是，对于深度学习工作负荷，如果速度是一个瓶颈，则可以使用 GPU，因为它的[性能][gpus-vs-cpus]通常优于 CPU。 若要在使用 CPU 的情况下获得相当于 GPU 的性能，通常一个具有需要大量 CPU 的群集。

可在任一方案中对本体系结构使用 CPU，但对于深度学习模型，GPU 提供的吞吐量值明显高于 CPU 群集，而且成本类似。 AKS 支持使用 GPU，这是对本体系结构使用 AKS 的一个优势。 另外，深度学习部署通常使用具有大量参数的模型。 使用 GPU 可以防止模型与 Web 服务之间发生资源争用，这在仅用 CPU 的部署中是一个问题。

## <a name="scalability-considerations"></a>可伸缩性注意事项

对于 AKS 群集是使用仅有 CPU 的 VM 预配的常规 Python 模型，在[扩展 pod 数][manually-scale-pods]时请小心。 目标是充分利用群集。 缩放取决于为 pod 定义的 CPU 请求和限制。 通过 Kubernetes 运行的机器学习还支持根据 CPU 利用率或其他指标进行 [pod 自动缩放][autoscale-pods]。 [群集自动缩放程序][autoscaler]（预览版）可以根据等待中的 pod 缩放代理节点。

对于使用支持 GPU 的 VM 的深入学习方案，pod 的资源限制方式是将一个 GPU 分配给一个 pod。 根据所用的 VM 类型，必须[缩放群集的节点][scale-cluster]来满足服务的需求。 可以使用 Azure CLI 和 kubectl 轻松实现此目的。

## <a name="monitoring-and-logging-considerations"></a>监视和日志记录注意事项

### <a name="aks-monitoring"></a>AKS 监视

若要洞察 AKS 性能，请使用[用于容器的 Azure Monitor][monitor-containers] 功能。 此服务通过指标 API 从 Kubernetes 中提供的控制器、节点和容器收集内存与处理器指标。

部署应用程序时，请监视 AKS 群集，以确保它按预期方式工作、所有节点都正常运行，并且所有 pod 都在运行。 尽管你可以使用 [kubectl][kubectl] 命令行工具来检索 pod 状态，但 Kubernetes 还包含一个 Web 仪表板，用于进行基本的群集状态监视和管理。

![Kubernetes 仪表板的屏幕截图](./_images/python-kubernetes-dashboard.png)

若要查看群集和节点的总体状态，请转到 Kubernetes 仪表板的“节点”部分。 如果某个节点处于非活动状态或发生故障，可以在该页中显示错误日志。 同样，转到“Pod”和“部署”部分可获取有关 pod 数目和部署状态的信息。

### <a name="aks-logs"></a>AKS 日志

AKS 自动将所有 stdout/stderr 记录到群集中 pod 的日志。 使用 kubectl 可查看这些日志，以及节点级别的事件和日志。 有关详细信息，请参阅部署步骤。

使用[用于容器的 Azure Monitor][monitor-containers] 通过适用于 Linux 的 Log Analytics 代理的容器化版本，来收集存储在 Log Analytics 工作区中的指标和日志。

## <a name="security-considerations"></a>安全注意事项

使用 [Azure 安全中心][security-center]可在一个中心视图中获得 Azure 资源的安全状态。 安全中心会监视潜在的安全问题并全面描述部署的安全运行状况，不过，它不会监视 AKS 代理节点。 安全中心针对每个 Azure 订阅进行配置。 根据[将 Azure 订阅载入安全中心标准版][get-started]中所述启用安全数据收集。 启用数据收集后，安全中心会自动扫描该订阅下创建的所有 VM。

**操作**。 若要使用 Azure Active Directory (Azure AD) 身份验证令牌登录到 AKS 群集，请将 AKS 配置为使用 Azure AD 进行[用户身份验证][aad-auth]。 群集管理员还可以根据用户标识或目录组成员身份来配置 Kubernetes 基于角色的访问控制 (RBAC)。

使用 [RBAC][rbac] 控制对已部署的 Azure 资源的访问。 RBAC 允许将授权角色分配给开发运营团队的成员。 可将一个用户分配到多个角色，并且可以创建自定义角色以实现更细化的[权限]。

**HTTPS**。 作为安全最佳做法，应用程序应强制实施 HTTPS 并重定向 HTTP 请求。 使用[入口控制器][ingress-controller]部署一个反向代理，用于终止 SSL 并重定向 HTTP 请求。 有关详细信息，请参阅[在 Azure Kubernetes 服务 (AKS) 中创建 HTTPS 入口控制器][https-ingress]。

“身份验证”。 此解决方案不限制对终结点的访问。 若要在企业设置中部署本体系结构，请通过 API 密钥保护终结点，并将某种形式的用户身份验证添加到客户端应用程序。

**容器注册表**。 此解决方案使用 Azure 容器注册表存储 Docker 映像。 应用程序依赖的代码以及模型将包含在此映像中。 企业应用程序应使用专用注册表来帮助防范运行恶意代码以及容器中的信息泄密。

**DDOS 防护**。 考虑启用 [DDoS 防护标准版][ddos]。 尽管基本 DDoS 防护已作为 Azure 平台的一部分启用，但 DDoS 防护标准版提供专门针对 Azure 虚拟网络资源优化的缓解功能。

**日志记录**。 在存储日志数据之前采用最佳做法，例如，清理可能被用来实施安全欺诈行为的用户密码和其他信息。

## <a name="deployment"></a>部署

若要部署本参考体系结构，请遵循 GitHub 存储库中所述的步骤：

- [常规 Python 模型][github-python]
- [深度学习模型][github-dl]

<!-- links -->

[aad-auth]: /azure/aks/aad-integration
[acr]: /azure/container-registry/
[something]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/
[aks]: /azure/aks/intro-kubernetes
[autoscaler]: /azure/aks/autoscaler
[autoscale-pods]: /azure/aks/tutorial-kubernetes-scale#autoscale-pods
[azcopy]: /azure/storage/common/storage-use-azcopy-linux
[ddos]: /azure/virtual-network/ddos-protection-overview
[get-started]: /azure/security-center/security-center-get-started
[github-python]: https://github.com/Microsoft/MLAKSDeployAML
[github-dl]: https://github.com/Microsoft/AKSDeploymentTutorial_AML
[gpus-vs-cpus]: https://azure.microsoft.com/en-us/blog/gpus-vs-cpus-for-deployment-of-deep-learning-models/
[https-ingress]: /azure/aks/ingress-tls
[ingress-controller]: https://kubernetes.io/docs/concepts/services-networking/ingress/
[kubectl]: https://kubernetes.io/docs/tasks/tools/install-kubectl/
[aml]: /azure/machine-learning/service/overview-what-is-azure-ml
[manually-scale-pods]: /azure/aks/tutorial-kubernetes-scale#manually-scale-pods
[monitor-containers]: /azure/monitoring/monitoring-container-insights-overview
[权限]: /azure/aks/concepts-identity
[rbac]: /azure/active-directory/role-based-access-control-what-is
[scale-cluster]: /azure/aks/scale-cluster
[scikit]: https://pypi.org/project/scikit-learn/
[security-center]: /azure/security-center/security-center-intro
[vm]: /azure/virtual-machines/
