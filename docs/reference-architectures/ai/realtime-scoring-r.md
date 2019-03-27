---
title: R 机器学习模型的实时评分
description: 在 R 中使用运行于 Azure Kubernetes 服务 (AKS) 中的 Machine Learning Server 实现实时预测服务。
author: njray
ms.date: 12/12/2018
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: azcat-ai
ms.openlocfilehash: 00bea3cae0c3d2f0fea2babd7b0157382cf9890a
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58248682"
---
# <a name="real-time-scoring-of-r-machine-learning-models"></a><span data-ttu-id="fbf6d-103">R 机器学习模型的实时评分</span><span class="sxs-lookup"><span data-stu-id="fbf6d-103">Real-time scoring of R machine learning models</span></span>

<span data-ttu-id="fbf6d-104">本参考体系结构演示如何使用 Azure Kubernetes 服务 (AKS) 中运行的 Microsoft Machine Learning Server 在 R 中实施实时（同步）预测服务。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-104">This reference architecture shows how to implement a real-time (synchronous) prediction service in R using Microsoft Machine Learning Server running in Azure Kubernetes Service (AKS).</span></span> <span data-ttu-id="fbf6d-105">本体系结构是一个通用的体系结构，适用于在 R 中生成的、要作为实时服务部署的任何预测模型。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-105">This architecture is intended to be generic and suited for any predictive model built in R that you want to deploy as a real-time service.</span></span> <span data-ttu-id="fbf6d-106">**[部署此解决方案][github]**。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-106">**[Deploy this solution][github]**.</span></span>

## <a name="architecture"></a><span data-ttu-id="fbf6d-107">体系结构</span><span class="sxs-lookup"><span data-stu-id="fbf6d-107">Architecture</span></span>

![Azure 上 R 机器学习模型的实时评分][0]

<span data-ttu-id="fbf6d-109">本参考体系结构采用基于容器的方法。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-109">This reference architecture takes a container-based approach.</span></span> <span data-ttu-id="fbf6d-110">生成一个 Docker 映像，其中包含 R，以及为新数据评分所需的各种项目。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-110">A Docker image is built containing R, as well as the various artifacts needed to score new data.</span></span> <span data-ttu-id="fbf6d-111">这些项目包括模型对象本身，以及一个评分脚本。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-111">These include the model object itself and a scoring script.</span></span> <span data-ttu-id="fbf6d-112">此映像将推送到 Azure 中托管的 Docker 注册表，然后部署到同样位于 Azure 中的 Kubernetes 群集。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-112">This image is pushed to a Docker registry hosted in Azure, and then deployed to a Kubernetes cluster, also in Azure.</span></span>

<span data-ttu-id="fbf6d-113">此工作流的体系结构包括以下组件。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-113">The architecture of this workflow includes the following components.</span></span>

- <span data-ttu-id="fbf6d-114">**[Azure 容器注册表][acr]** 用于存储此工作流的映像。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-114">**[Azure Container Registry][acr]** is used to store the images for this workflow.</span></span> <span data-ttu-id="fbf6d-115">可以通过标准的 [Docker 注册表 V2 API][docker] 和客户端来管理使用容器注册表创建的注册表。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-115">Registries created with Container Registry can be managed via the standard [Docker Registry V2 API][docker] and client.</span></span>

- <span data-ttu-id="fbf6d-116">**[Azure Kubernetes 服务][aks]** 用于托管部署和服务。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-116">**[Azure Kubernetes Service][aks]** is used to host the deployment and service.</span></span> <span data-ttu-id="fbf6d-117">可以通过标准的 [Kubernetes API][k-api] 和客户端 (kubectl) 来管理使用 AKS 创建的群集。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-117">Clusters created with AKS can be managed using the standard [Kubernetes API][k-api] and client (kubectl).</span></span>

- <span data-ttu-id="fbf6d-118">**[Microsoft Machine Learning Server][mmls]** 用于定义服务的 REST API，其中包含[模型操作化][operationalization]。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-118">**[Microsoft Machine Learning Server][mmls]** is used to define the REST API for the service and includes [Model Operationalization][operationalization].</span></span> <span data-ttu-id="fbf6d-119">此面向服务的 Web 服务器进程将侦听请求；然后，这些请求将转发到其他运行实际 R 代码的后台进程，以生成结果。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-119">This service-oriented web server process listens for requests, which are then handed off to other background processes that run the actual R code to generate the results.</span></span> <span data-ttu-id="fbf6d-120">在此配置中，所有这些进程在包装于某个容器中的单个节点上运行。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-120">All these processes run on a single node in this configuration, which is wrapped in a container.</span></span> <span data-ttu-id="fbf6d-121">有关在开发或测试环境外部使用此服务的详细信息，请与 Microsoft 代表联系。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-121">For details about using this service outside a dev or test environment, contact your Microsoft representative.</span></span>

## <a name="performance-considerations"></a><span data-ttu-id="fbf6d-122">性能注意事项</span><span class="sxs-lookup"><span data-stu-id="fbf6d-122">Performance considerations</span></span>

<span data-ttu-id="fbf6d-123">在训练和为新数据评分时，机器学习工作负荷往往是计算密集型的。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-123">Machine learning workloads tend to be compute-intensive, both when training and when scoring new data.</span></span> <span data-ttu-id="fbf6d-124">根据经验法则，请勿尝试在每个核心中运行多个评分进程。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-124">As a rule of thumb, try not to run more than one scoring process per core.</span></span> <span data-ttu-id="fbf6d-125">Machine Learning Server 允许定义每个容器中运行的 R 进程数。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-125">Machine Learning Server lets you define the number of R processes running in each container.</span></span> <span data-ttu-id="fbf6d-126">默认设置为 5 个进程。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-126">The default is five processes.</span></span> <span data-ttu-id="fbf6d-127">创建相对简单的模型（例如，包含少量变量的线性回归，或小型决策树）时，可以增加进程数。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-127">When creating a relatively simple model, such as a linear regression with a small number of variables, or a small decision tree, you can increase the number of processes.</span></span> <span data-ttu-id="fbf6d-128">监视群集节点上的 CPU 负载，以确定容器数的适当限制。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-128">Monitor the CPU load on your cluster nodes to determine the appropriate limit on the number of containers.</span></span>

<span data-ttu-id="fbf6d-129">支持 GPU 的群集可以加速某些类型的工作负荷，尤其是深度学习模型。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-129">A GPU-enabled cluster can speed up some types of workloads, and deep learning models in particular.</span></span> <span data-ttu-id="fbf6d-130">并非所有工作负荷都可以利用 GPU &mdash; 只有重度使用矩阵代数的工作负荷才可以。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-130">Not all workloads can take advantage of GPUs &mdash; only those that make heavy use of matrix algebra.</span></span> <span data-ttu-id="fbf6d-131">例如，基于树的模型，包括随机林和提升模型，通常不能从 GPU 获得优势。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-131">For example, tree-based models, including random forests and boosting models, generally derive no advantage from GPUs.</span></span>

<span data-ttu-id="fbf6d-132">某些模型类型（例如随机林）可在 CPU 上大规模并行化。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-132">Some model types such as random forests are massively parallelizable on CPUs.</span></span> <span data-ttu-id="fbf6d-133">在这种情况下，可以通过将工作负荷分散在多个核心之间来加速单个请求的评分。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-133">In these cases, speed up the scoring of a single request by distributing the workload across multiple cores.</span></span> <span data-ttu-id="fbf6d-134">但是，假设群集大小固定，则这样做会减少用于处理多个评分请求的容量。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-134">However, doing so reduces your capacity to handle multiple scoring requests given a fixed cluster size.</span></span>

<span data-ttu-id="fbf6d-135">一般而言，开源 R 模型会将其所有数据存储在内存中，因此，请确保节点有足够的内存，可以容纳你要同时运行的进程。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-135">In general, open-source R models store all their data in memory, so ensure that your nodes have enough memory to accommodate the processes you plan to run concurrently.</span></span> <span data-ttu-id="fbf6d-136">如果使用 Machine Learning Server 来拟合模型，请使用可以处理磁盘中数据的库，而不要将这些数据全部读入内存中。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-136">If you are using Machine Learning Server to fit your models, use the libraries that can process data on disk, rather than reading it all into memory.</span></span> <span data-ttu-id="fbf6d-137">这可以明显降低内存要求。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-137">This can help reduce memory requirements significantly.</span></span> <span data-ttu-id="fbf6d-138">不管是使用 Machine Learning Server 还是开源 R，都应该监视节点，以确保评分进程不会耗尽内存。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-138">Regardless of whether you use Machine Learning Server or open-source R, monitor your nodes to ensure that your scoring processes are not memory-starved.</span></span>

## <a name="security-considerations"></a><span data-ttu-id="fbf6d-139">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="fbf6d-139">Security considerations</span></span>

### <a name="network-encryption"></a><span data-ttu-id="fbf6d-140">网络加密</span><span class="sxs-lookup"><span data-stu-id="fbf6d-140">Network encryption</span></span>

<span data-ttu-id="fbf6d-141">在本参考体系结构中，已启用 HTTPS 来与群集通信，并使用了 [Let's Encrypt][encrypt] 提供的临时证书。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-141">In this reference architecture, HTTPS is enabled for communication with the cluster, and a staging certificate from [Let’s Encrypt][encrypt] is used.</span></span> <span data-ttu-id="fbf6d-142">对于生产用途，请改用你自己的、由相应签名机构提供的证书。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-142">For production purposes, substitute your own certificate from an appropriate signing authority.</span></span>

### <a name="authentication-and-authorization"></a><span data-ttu-id="fbf6d-143">身份验证和授权</span><span class="sxs-lookup"><span data-stu-id="fbf6d-143">Authentication and authorization</span></span>

<span data-ttu-id="fbf6d-144">Machine Learning Server [模型操作化][operationalization]要求对评分请求进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-144">Machine Learning Server [Model Operationalization][operationalization] requires scoring requests to be authenticated.</span></span> <span data-ttu-id="fbf6d-145">此部署中使用了用户名和密码。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-145">In this deployment, a username and password are used.</span></span> <span data-ttu-id="fbf6d-146">在企业设置中，可以使用 [Azure Active Directory][AAD] 启用身份验证，或者使用 [Azure API 管理][API]创建独立的前端。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-146">In an enterprise setting, you can enable authentication using [Azure Active Directory][AAD] or create a separate front end using [Azure API Management][API].</span></span>

<span data-ttu-id="fbf6d-147">要使模型操作化与容器中的 Machine Learning Server 正常配合工作，必须安装 JSON Web 令牌 (JWT) 证书。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-147">For Model Operationalization to work correctly with Machine Learning Server on containers, you must install a JSON Web Token (JWT) certificate.</span></span> <span data-ttu-id="fbf6d-148">此部署使用 Microsoft 提供的证书。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-148">This deployment uses a certificate supplied by Microsoft.</span></span> <span data-ttu-id="fbf6d-149">在生产设置中，请提供自己的证书。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-149">In a production setting, supply your own.</span></span>

<span data-ttu-id="fbf6d-150">对于容器注册表与 AKS 之间的流量，请考虑启用[基于角色的访问控制][rbac] (RBAC)，以将访问特权限制给需要这些特权的用户。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-150">For traffic between Container Registry and AKS, consider enabling [role-based access control][rbac] (RBAC) to limit access privileges to only those needed.</span></span>

### <a name="separate-storage"></a><span data-ttu-id="fbf6d-151">独立的存储</span><span class="sxs-lookup"><span data-stu-id="fbf6d-151">Separate storage</span></span>

<span data-ttu-id="fbf6d-152">本参考体系结构将应用程序 (R) 和数据（模型对象和评分脚本）捆绑在单个映像中。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-152">This reference architecture bundles the application (R) and the data (model object and scoring script) into a single image.</span></span> <span data-ttu-id="fbf6d-153">在某些情况下，将它们隔离可能更有利。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-153">In some cases, it may be beneficial to separate these.</span></span> <span data-ttu-id="fbf6d-154">可将模型数据和代码放入 Azure Blob 或文件[存储][storage]，并在容器初始化时检索它们。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-154">You can place the model data and code into Azure blob or file [storage][storage], and retrieve them at container initialization.</span></span> <span data-ttu-id="fbf6d-155">在这种情况下，请确保将存储帐户设置为仅允许在身份验证后进行访问，并要求使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-155">In this case, ensure that the storage account is set to allow authenticated access only and require HTTPS.</span></span>

## <a name="monitoring-and-logging-considerations"></a><span data-ttu-id="fbf6d-156">监视和日志记录注意事项</span><span class="sxs-lookup"><span data-stu-id="fbf6d-156">Monitoring and logging considerations</span></span>

<span data-ttu-id="fbf6d-157">使用 [Kubernetes 仪表板][dashboard]监视 AKS 群集的总体状态。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-157">Use the [Kubernetes dashboard][dashboard] to monitor the overall status of your AKS cluster.</span></span> <span data-ttu-id="fbf6d-158">在 Azure 门户中查看群集的概述边栏选项卡以了解更多详细信息。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-158">See the cluster’s overview blade in Azure portal for more details.</span></span> <span data-ttu-id="fbf6d-159">[GitHub][github] 资源也介绍了如何通过 R 打开仪表板。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-159">The [GitHub][github] resources also show how to bring up the dashboard from R.</span></span>

<span data-ttu-id="fbf6d-160">尽管仪表板可以提供群集总体运行状况的视图，但跟踪单个容器的状态也很重要。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-160">Although the dashboard gives you a view of the overall health of your cluster, it’s also important to track the status of individual containers.</span></span> <span data-ttu-id="fbf6d-161">为此，请在 Azure 门户中通过群集概述边栏选项卡启用 [Azure Monitor Insights][monitor]，或参阅[用于容器的 Azure Monitor][monitor-containers]（预览版）。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-161">To do this, enable [Azure Monitor Insights][monitor] from the cluster overview blade in Azure portal, or see [Azure Monitor for containers][monitor-containers] (in preview).</span></span>

## <a name="cost-considerations"></a><span data-ttu-id="fbf6d-162">成本注意事项</span><span class="sxs-lookup"><span data-stu-id="fbf6d-162">Cost considerations</span></span>

<span data-ttu-id="fbf6d-163">Machine Learning Server 按核心授权，这包括群集中要运行 Machine Learning Server 的所有核心。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-163">Machine Learning Server is licensed on a per-core basis, and all the cores in the cluster that will run Machine Learning  Server count towards this.</span></span> <span data-ttu-id="fbf6d-164">如果你是 Machine Learning Server 或 Microsoft SQL Server 企业客户，请与 Microsoft 代表联系，以获取价格详细信息。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-164">If you are an enterprise Machine Learning Server or Microsoft SQL Server customer, contact your Microsoft representative for pricing details.</span></span>

<span data-ttu-id="fbf6d-165">可以取代 Machine Learning Server 的开源产品是 [Plumber][plumber] - 一个可将代码转换为 REST API 的 R 包。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-165">An open-source alternative to Machine Learning Server is [Plumber][plumber], an R package that turns your code into a REST API.</span></span> <span data-ttu-id="fbf6d-166">Plumber 的功能不如 Machine Learning Server 那样全面。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-166">Plumber is less fully featured than Machine Learning Server.</span></span> <span data-ttu-id="fbf6d-167">例如，默认情况下，它不包括任何可提供请求身份验证的功能。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-167">For example, by default it doesn't include any features that provide request authentication.</span></span> <span data-ttu-id="fbf6d-168">如果使用 Plumber，我们建议启用 [Azure API 管理][API]来处理身份验证详细信息。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-168">If you use Plumber, it’s recommended that you enable [Azure API Management][API] to handle authentication details.</span></span>

<span data-ttu-id="fbf6d-169">除了授权以外，主要成本考虑因素还包括 Kubernetes 群集的计算资源。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-169">Besides licensing, the main cost consideration is the Kubernetes cluster's compute resources.</span></span> <span data-ttu-id="fbf6d-170">群集必须足够大才能处理高峰期的预期请求量，但这会在其他时段将资源保持在闲置状态。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-170">The cluster must be large enough to handle the expected request volume at peak times, but this approach leaves resources idle at other times.</span></span> <span data-ttu-id="fbf6d-171">若要限制闲置资源的影响，请使用 kubectl 工具为群集启用[水平自动缩放程序][autoscaler]。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-171">To limit the impact of idle resources, enable the [horizontal autoscaler][autoscaler] for the cluster using the kubectl tool.</span></span> <span data-ttu-id="fbf6d-172">或使用 AKS [群集自动缩放程序][cluster-autoscaler]。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-172">Or use the AKS [cluster autoscaler][cluster-autoscaler].</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="fbf6d-173">部署解决方案</span><span class="sxs-lookup"><span data-stu-id="fbf6d-173">Deploy the solution</span></span>

<span data-ttu-id="fbf6d-174">[GitHub][github] 中提供了本体系结构的参考实现。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-174">The reference implementation of this architecture is available on [GitHub][github].</span></span> <span data-ttu-id="fbf6d-175">遵循该文章中的步骤可将一个简单的预测模型部署为服务。</span><span class="sxs-lookup"><span data-stu-id="fbf6d-175">Follow the steps described there to deploy a simple predictive model as a service.</span></span>

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
