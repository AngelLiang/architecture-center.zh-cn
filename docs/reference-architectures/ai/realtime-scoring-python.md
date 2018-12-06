---
title: Azure 上 Python Scikit-Learn 和深度学习模型的实时评分
description: 本参考体系结构演示如何在 Azure 上将 Python 模型部署为 Web 服务，用于进行实时预测。
author: njray
ms.date: 11/09/2018
ms.openlocfilehash: ff385e232c69e46b0afc6b15e73983bd856b9b2b
ms.sourcegitcommit: e7e0e0282fa93f0063da3b57128ade395a9c1ef9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2018
ms.locfileid: "52902572"
---
# <a name="real-time-scoring-of-python-scikit-learn-and-deep-learning-models-on-azure"></a><span data-ttu-id="52704-103">Azure 上 Python Scikit-Learn 和深度学习模型的实时评分</span><span class="sxs-lookup"><span data-stu-id="52704-103">Real-time scoring of Python Scikit-Learn and Deep Learning Models on Azure</span></span>

<span data-ttu-id="52704-104">本参考体系结构演示如何将 Python 模型部署为 Web 服务用于进行实时预测。</span><span class="sxs-lookup"><span data-stu-id="52704-104">This reference architecture shows how to deploy Python models as web services to make real-time predictions.</span></span> <span data-ttu-id="52704-105">将介绍两种方案：部署常规 Python 模型，以及部署深度学习模型所要满足的具体要求。</span><span class="sxs-lookup"><span data-stu-id="52704-105">Two scenarios are covered: deploying regular Python models, and the specific requirements of deploying deep learning models.</span></span> <span data-ttu-id="52704-106">这两种方案都使用下面所示的体系结构。</span><span class="sxs-lookup"><span data-stu-id="52704-106">Both scenarios use the architecture shown.</span></span>

<span data-ttu-id="52704-107">GitHub 上提供了本体系结构的两种参考实现，一个适用于[常规 Python 模型][github-python]，另一个适用于[深度学习模型][github-dl]。</span><span class="sxs-lookup"><span data-stu-id="52704-107">Two reference implementations for this architecture are available on GitHub, one for [regular Python models][github-python] and one for [deep learning models][github-dl].</span></span>

![](./_images/python-model-architecture.png)

## <a name="scenarios"></a><span data-ttu-id="52704-108">方案</span><span class="sxs-lookup"><span data-stu-id="52704-108">Scenarios</span></span>

<span data-ttu-id="52704-109">参考实现演示使用本体系结构的两种方案。</span><span class="sxs-lookup"><span data-stu-id="52704-109">The reference implementations demonstrate two scenarios using this architecture.</span></span>

<span data-ttu-id="52704-110">**方案 1：常见问题匹配**。</span><span class="sxs-lookup"><span data-stu-id="52704-110">**Scenario 1: FAQ matching**.</span></span> <span data-ttu-id="52704-111">此方案演示如何将常见问题 (FAQ) 匹配模型部署为 Web 服务，用于对用户的问题提供预测。</span><span class="sxs-lookup"><span data-stu-id="52704-111">This scenario shows how to deploy a frequently asked questions (FAQ) matching model as a web service to provide predictions for user questions.</span></span> <span data-ttu-id="52704-112">对于此方案，体系结构示意图中的“输入数据”是指包含要与 FAQ 列表匹配的用户问题的文本字符串。</span><span class="sxs-lookup"><span data-stu-id="52704-112">For this scenario, "Input Data" in the architecture diagram refers to text strings containing user questions to match with a list of FAQs.</span></span> <span data-ttu-id="52704-113">此方案是针对适用于 Python 的 [scikit-learn][scikit] 机器学习库设计的，但可通用化为使用 Python 模型进行实时预测的任何方案。</span><span class="sxs-lookup"><span data-stu-id="52704-113">This scenario is designed for the [scikit-learn][scikit] machine learning library for Python, but can be generalized to any scenario that uses Python models to make real-time predictions.</span></span>

<span data-ttu-id="52704-114">此方案使用堆栈溢出问题数据的子集，其中包括标记为 JavaScript 的原始问题、其重复问题及其答案。</span><span class="sxs-lookup"><span data-stu-id="52704-114">This scenario uses a subset of Stack Overflow question data that includes original questions tagged as JavaScript, their duplicate questions, and their answers.</span></span> <span data-ttu-id="52704-115">此方案训练某个 scikit-learn 管道来预测某个重复问题与每个原始问题匹配的概率。</span><span class="sxs-lookup"><span data-stu-id="52704-115">It trains a scikit-learn pipeline to predict the match probability of a duplicate question with each of the original questions.</span></span> <span data-ttu-id="52704-116">这些预测是使用 REST API 终结点实时做出的。</span><span class="sxs-lookup"><span data-stu-id="52704-116">These predictions are made in real time using a REST API  endpoint.</span></span>

<span data-ttu-id="52704-117">本体系结构的应用程序流如下：</span><span class="sxs-lookup"><span data-stu-id="52704-117">The application flow for this architecture is as follows:</span></span>

1.  <span data-ttu-id="52704-118">客户端发送包含已编码问题数据的 HTTP POST 请求。</span><span class="sxs-lookup"><span data-stu-id="52704-118">The client sends an HTTP POST request with the encoded question data.</span></span>

2.  <span data-ttu-id="52704-119">Flask 应用从请求中提取问题。</span><span class="sxs-lookup"><span data-stu-id="52704-119">The Flask app extracts the question from the request.</span></span>

3.  <span data-ttu-id="52704-120">将问题发送到 scikit-learn 管道模型进行特征化和评分。</span><span class="sxs-lookup"><span data-stu-id="52704-120">The question is sent to the scikit-learn pipeline model for featurization and scoring.</span></span>

4.  <span data-ttu-id="52704-121">通过管道将匹配的 FAQ 问题及其评分传递给 JSON 对象，并将其返回给客户端。</span><span class="sxs-lookup"><span data-stu-id="52704-121">The matching FAQ questions with their scores are piped into a JSON object and returned to the client.</span></span>

<span data-ttu-id="52704-122">下面是使用结果的示例应用的屏幕截图：</span><span class="sxs-lookup"><span data-stu-id="52704-122">Here is a screenshot of the example app that consumes the results:</span></span>

![](./_images/python-faq-matches.png)

<span data-ttu-id="52704-123">**方案 2：图像分类**。</span><span class="sxs-lookup"><span data-stu-id="52704-123">**Scenario 2: Image classification.**</span></span> <span data-ttu-id="52704-124">此方案演示如何将卷积神经网络 (CNN) 模型部署为 Web 服务，以基于图像提供预测。</span><span class="sxs-lookup"><span data-stu-id="52704-124">This scenario shows how to deploy a Convolutional Neural Network (CNN) model as a web service to provide predictions on images.</span></span> <span data-ttu-id="52704-125">对于此方案，体系结构示意图中的“输入数据”指图像文件。</span><span class="sxs-lookup"><span data-stu-id="52704-125">For this scenario, "Input Data" in the architecture diagram refers to image files.</span></span> <span data-ttu-id="52704-126">在计算机视觉领域，CNN 对图像分类和对象检测等任务非常有效。</span><span class="sxs-lookup"><span data-stu-id="52704-126">CNNs are very effective in computer vision for tasks such as image classification and object detection.</span></span> <span data-ttu-id="52704-127">此方案是针对 TensorFlow、Keras（具有 TensorFlow 后端）和 PyTorch 等框架设计的。</span><span class="sxs-lookup"><span data-stu-id="52704-127">This scenario is designed for the frameworks TensorFlow, Keras (with the TensorFlow back end), and PyTorch.</span></span> <span data-ttu-id="52704-128">但是，它可以通用化为使用深度学习模型进行实时预测的任何方案。</span><span class="sxs-lookup"><span data-stu-id="52704-128">However, it can be generalized to any scenario that uses deep learning models to make real-time predictions.</span></span>

<span data-ttu-id="52704-129">此方案使用基于 ImageNet-1K（1,000 个类）数据集预先训练的 ResNet-152 模型来预测某个图像属于哪些类别（参阅下图）。</span><span class="sxs-lookup"><span data-stu-id="52704-129">This scenario uses a pre-trained ResNet-152 model trained on ImageNet-1K (1,000 classes) dataset to predict which category (see figure below) an image belongs to.</span></span> <span data-ttu-id="52704-130">这些预测是使用 REST API 终结点实时做出的。</span><span class="sxs-lookup"><span data-stu-id="52704-130">These predictions are made in real time using a REST API endpoint.</span></span>

![](./_images/python-example-predictions.png)

<span data-ttu-id="52704-131">深度学习模型的应用程序流如下：</span><span class="sxs-lookup"><span data-stu-id="52704-131">The application flow for the deep learning model is as follows:</span></span>

1.  <span data-ttu-id="52704-132">客户端发送包含已编码图像数据的 HTTP POST 请求。</span><span class="sxs-lookup"><span data-stu-id="52704-132">The client sends an HTTP POST request with the encoded image data.</span></span>

2.  <span data-ttu-id="52704-133">Flask 应用从请求中提取图像。</span><span class="sxs-lookup"><span data-stu-id="52704-133">The Flask app extracts the image from the request.</span></span>

3.  <span data-ttu-id="52704-134">预处理图像，并将其发送到模型进行评分。</span><span class="sxs-lookup"><span data-stu-id="52704-134">The image is preprocessed and sent to the model for scoring.</span></span>

4.  <span data-ttu-id="52704-135">通过管道将评分结果传递给 JSON 对象，并将其返回给客户端。</span><span class="sxs-lookup"><span data-stu-id="52704-135">The scoring result is piped into a JSON object and returned to the client.</span></span>

## <a name="architecture"></a><span data-ttu-id="52704-136">体系结构</span><span class="sxs-lookup"><span data-stu-id="52704-136">Architecture</span></span>

<span data-ttu-id="52704-137">该体系结构包括以下组件。</span><span class="sxs-lookup"><span data-stu-id="52704-137">This architecture consists of the following components.</span></span>

<span data-ttu-id="52704-138">**[虚拟机][vm]** (VM)。</span><span class="sxs-lookup"><span data-stu-id="52704-138">**[Virtual machine][vm]** (VM).</span></span> <span data-ttu-id="52704-139">VM 显示为本地或云中的设备示例，它可以发送 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="52704-139">The VM is shown as an example of a device &mdash; local or in the cloud &mdash; that can send an HTTP request.</span></span>

<span data-ttu-id="52704-140">**[Azure Kubernetes 服务][aks]** (AKS) 用于在 Kubernetes 群集上部署应用程序。</span><span class="sxs-lookup"><span data-stu-id="52704-140">**[Azure Kubernetes Service][aks]** (AKS) is used to deploy the application on a Kubernetes cluster.</span></span> <span data-ttu-id="52704-141">AKS 简化了 Kubernetes 的部署和操作。</span><span class="sxs-lookup"><span data-stu-id="52704-141">AKS simplifies the deployment and operations of Kubernetes.</span></span> <span data-ttu-id="52704-142">可以使用仅有 CPU 的 VM 为常规 Python 模型配置群集，或使用支持 GPU 的 VM 为深度学习模型配置群集。</span><span class="sxs-lookup"><span data-stu-id="52704-142">The cluster can be configured using CPU-only VMs for regular Python models or GPU-enabled VMs for deep learning models.</span></span>

<span data-ttu-id="52704-143">**[负载均衡器][lb]**。</span><span class="sxs-lookup"><span data-stu-id="52704-143">**[Load balancer][lb]**.</span></span> <span data-ttu-id="52704-144">AKS 预配的负载均衡器用于在外部公开服务。</span><span class="sxs-lookup"><span data-stu-id="52704-144">A load balancer, provisioned by AKS, is used to expose the service externally.</span></span> <span data-ttu-id="52704-145">来自负载均衡器的流量将定向到后端 pod。</span><span class="sxs-lookup"><span data-stu-id="52704-145">Traffic from the load balancer is directed to the back-end pods.</span></span>

<span data-ttu-id="52704-146">**[Docker 中心][docker]** 用于存储 Kubernetes 群集中部署的 Docker 映像。</span><span class="sxs-lookup"><span data-stu-id="52704-146">**[Docker Hub][docker]** is used to store the Docker image that is deployed on Kubernetes cluster.</span></span> <span data-ttu-id="52704-147">之所以为本体系结构选择 Docker 中心，是因为它易于使用，并且是 Docker 用户的默认映像存储库。</span><span class="sxs-lookup"><span data-stu-id="52704-147">Docker Hub was chosen for this architecture because it's easy to use and is the default image repository for Docker users.</span></span> <span data-ttu-id="52704-148">也可以对本体系结构使用 [Azure 容器注册表][acr]。</span><span class="sxs-lookup"><span data-stu-id="52704-148">[Azure Container Registry][acr] can also be used for this architecture.</span></span>

## <a name="performance-considerations"></a><span data-ttu-id="52704-149">性能注意事项</span><span class="sxs-lookup"><span data-stu-id="52704-149">Performance considerations</span></span>

<span data-ttu-id="52704-150">对于实时评分体系结构，吞吐量性能是首要考虑因素。</span><span class="sxs-lookup"><span data-stu-id="52704-150">For real-time scoring architectures, throughput performance becomes a dominant consideration.</span></span> <span data-ttu-id="52704-151">对于常规 Python 模型，仅使用 CPU 通常就足以处理工作负荷。</span><span class="sxs-lookup"><span data-stu-id="52704-151">For regular Python models, it's generally accepted that CPUs are sufficient to handle the workload.</span></span> 

<span data-ttu-id="52704-152">但是，对于深度学习工作负荷，如果速度是一个瓶颈，则可以使用 GPU，因为它的[性能][gpus-vs-cpus]通常优于 CPU。</span><span class="sxs-lookup"><span data-stu-id="52704-152">However for deep learning workloads, when speed is a bottleneck, GPUs generally provide better [performance][gpus-vs-cpus] compared to CPUs.</span></span> <span data-ttu-id="52704-153">若要在使用 CPU 的情况下获得相当于 GPU 的性能，通常一个具有需要大量 CPU 的群集。</span><span class="sxs-lookup"><span data-stu-id="52704-153">To match GPU performance using CPUs, a cluster with large number of CPUs is usually needed.</span></span>

<span data-ttu-id="52704-154">可在任一方案中对本体系结构使用 CPU，但对于深度学习模型，GPU 提供的吞吐量值明显高于 CPU 群集，而且成本类似。</span><span class="sxs-lookup"><span data-stu-id="52704-154">You can use CPUs for this architecture in either scenario, but for deep learning models, GPUs provide significantly higher throughput values compared to a CPU cluster of similar cost.</span></span> <span data-ttu-id="52704-155">AKS 支持使用 GPU，这是对本体系结构使用 AKS 的一个优势。</span><span class="sxs-lookup"><span data-stu-id="52704-155">AKS supports the use of GPUs, which is one advantage of using AKS for this architecture.</span></span> <span data-ttu-id="52704-156">另外，深度学习部署通常使用具有大量参数的模型。</span><span class="sxs-lookup"><span data-stu-id="52704-156">Also, deep learning deployments typically use models with a high number of parameters.</span></span> <span data-ttu-id="52704-157">使用 GPU 可以防止模型与 Web 服务之间发生资源争用，这在仅用 CPU 的部署中是一个问题。</span><span class="sxs-lookup"><span data-stu-id="52704-157">Using GPUs prevents contention for resources between the model and the web service, which is an issue in CPU-only deployments.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="52704-158">可伸缩性注意事项</span><span class="sxs-lookup"><span data-stu-id="52704-158">Scalability considerations</span></span>

<span data-ttu-id="52704-159">对于 AKS 群集是使用仅有 CPU 的 VM 预配的常规 Python 模型，在[扩展 pod 数][manually-scale-pods]时请小心。</span><span class="sxs-lookup"><span data-stu-id="52704-159">For regular Python models, where AKS cluster is provisioned with CPU-only VMs, take care when [scaling out the number of pods][manually-scale-pods].</span></span> <span data-ttu-id="52704-160">目标是充分利用群集。</span><span class="sxs-lookup"><span data-stu-id="52704-160">The goal is to fully utilize the cluster.</span></span> <span data-ttu-id="52704-161">缩放取决于为 pod 定义的 CPU 请求和限制。</span><span class="sxs-lookup"><span data-stu-id="52704-161">Scaling depends on the CPU requests and limits defined for the pods.</span></span> <span data-ttu-id="52704-162">Kubernetes 还支持 pod 的[自动缩放][autoscale-pods]，以根据 CPU 利用率或其他选择指标调整部署中的 pod 数。</span><span class="sxs-lookup"><span data-stu-id="52704-162">Kubernetes also supports [autoscaling][autoscale-pods] of the pods to adjust the number of pods in a deployment depending on CPU utilization or other select metrics.</span></span> <span data-ttu-id="52704-163">[群集自动缩放程序][autoscaler]（预览版）可以根据等待中的 pod 缩放代理节点。</span><span class="sxs-lookup"><span data-stu-id="52704-163">The [cluster autoscaler][autoscaler] (in preview) can scale agent nodes based on pending pods.</span></span>

<span data-ttu-id="52704-164">对于使用支持 GPU 的 VM 的深入学习方案，pod 的资源限制方式是将一个 GPU 分配给一个 pod。</span><span class="sxs-lookup"><span data-stu-id="52704-164">For deep learning scenarios, using GPU-enabled VMs, resource limits on pods are such that one GPU is assigned to one pod.</span></span> <span data-ttu-id="52704-165">根据所用的 VM 类型，必须[缩放群集的节点][scale-cluster]来满足服务的需求。</span><span class="sxs-lookup"><span data-stu-id="52704-165">Depending on the type of VM used, you must [scale the nodes of the cluster][scale-cluster] to meet the demand for the service.</span></span> <span data-ttu-id="52704-166">可以使用 Azure CLI 和 kubectl 轻松实现此目的。</span><span class="sxs-lookup"><span data-stu-id="52704-166">You can do this easily using the Azure CLI and kubectl.</span></span>

## <a name="monitoring-and-logging-considerations"></a><span data-ttu-id="52704-167">监视和日志记录注意事项</span><span class="sxs-lookup"><span data-stu-id="52704-167">Monitoring and logging considerations</span></span>

### <a name="aks-monitoring"></a><span data-ttu-id="52704-168">AKS 监视</span><span class="sxs-lookup"><span data-stu-id="52704-168">AKS monitoring</span></span>

<span data-ttu-id="52704-169">若要洞察 AKS 性能，请使用[用于容器的 Azure Monitor][monitor-containers] 功能。</span><span class="sxs-lookup"><span data-stu-id="52704-169">For visibility into AKS performance, use the [Azure Monitor for containers][monitor-containers] feature.</span></span> <span data-ttu-id="52704-170">此服务通过指标 API 从 Kubernetes 中提供的控制器、节点和容器收集内存与处理器指标。</span><span class="sxs-lookup"><span data-stu-id="52704-170">It collects memory and processor metrics from controllers, nodes, and containers that are available in Kubernetes through the Metrics API.</span></span>

<span data-ttu-id="52704-171">部署应用程序时，请监视 AKS 群集，以确保它按预期方式工作、所有节点都正常运行，并且所有 pod 都在运行。</span><span class="sxs-lookup"><span data-stu-id="52704-171">While deploying your application, monitor the AKS cluster to make sure it's working as expected, all the nodes are operational, and all pods are running.</span></span> <span data-ttu-id="52704-172">尽管你可以使用 [kubectl][kubectl] 命令行工具来检索 pod 状态，但 Kubernetes 还包含一个 Web 仪表板，用于进行基本的群集状态监视和管理。</span><span class="sxs-lookup"><span data-stu-id="52704-172">Although you can use the [kubectl][kubectl] command-line tool to retrieve pod status, Kubernetes also includes a web dashboard for basic monitoring of the cluster status and management.</span></span>

![](./_images/python-kubernetes-dashboard.png)

<span data-ttu-id="52704-173">若要查看群集和节点的总体状态，请转到 Kubernetes 仪表板的“节点”部分。</span><span class="sxs-lookup"><span data-stu-id="52704-173">To see the overall state of the cluster and nodes, go to the **Nodes** section of the Kubernetes dashboard.</span></span> <span data-ttu-id="52704-174">如果某个节点处于非活动状态或发生故障，可以在该页中显示错误日志。</span><span class="sxs-lookup"><span data-stu-id="52704-174">If a node is inactive or has failed, you can display the error logs from that page.</span></span> <span data-ttu-id="52704-175">同样，转到“Pod”和“部署”部分可获取有关 pod 数目和部署状态的信息。</span><span class="sxs-lookup"><span data-stu-id="52704-175">Similarly, go to the **Pods** and **Deployments** sections for information about the number of pods and status of your deployment.</span></span>

### <a name="aks-logs"></a><span data-ttu-id="52704-176">AKS 日志</span><span class="sxs-lookup"><span data-stu-id="52704-176">AKS logs</span></span> 

<span data-ttu-id="52704-177">AKS 自动将所有 stdout/stderr 记录到群集中 pod 的日志。</span><span class="sxs-lookup"><span data-stu-id="52704-177">AKS automatically logs all stdout/stderr to the logs of the pods in the cluster.</span></span> <span data-ttu-id="52704-178">使用 kubectl 可查看这些日志，以及节点级别的事件和日志。</span><span class="sxs-lookup"><span data-stu-id="52704-178">Use kubectl to see these and also node-level events and logs.</span></span> <span data-ttu-id="52704-179">有关详细信息，请参阅部署步骤。</span><span class="sxs-lookup"><span data-stu-id="52704-179">For details, see the deployment steps.</span></span>

<span data-ttu-id="52704-180">使用[用于容器的 Azure Monitor][monitor-containers] 通过适用于 Linux 的 Log Analytics 代理的容器化版本，来收集存储在 Log Analytics 工作区中的指标和日志。</span><span class="sxs-lookup"><span data-stu-id="52704-180">Use [Azure Monitor for containers][monitor-containers] to collect metrics and logs through a containerized version of the Log Analytics agent for Linux, which is stored in your Log Analytics workspace.</span></span>

## <a name="security-considerations"></a><span data-ttu-id="52704-181">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="52704-181">Security considerations</span></span>

<span data-ttu-id="52704-182">使用 [Azure 安全中心][security-center]可在一个中心视图中获得 Azure 资源的安全状态。</span><span class="sxs-lookup"><span data-stu-id="52704-182">Use [Azure Security Center][security-center] to get a central view of the security state of your Azure resources.</span></span> <span data-ttu-id="52704-183">安全中心会监视潜在的安全问题并全面描述部署的安全运行状况，不过，它不会监视 AKS 代理节点。</span><span class="sxs-lookup"><span data-stu-id="52704-183">Security Center monitors potential security issues and provides a comprehensive picture of the security health of your deployment, although it doesn't monitor AKS agent nodes.</span></span> <span data-ttu-id="52704-184">安全中心针对每个 Azure 订阅进行配置。</span><span class="sxs-lookup"><span data-stu-id="52704-184">Security Center is configured per Azure subscription.</span></span> <span data-ttu-id="52704-185">根据[将 Azure 订阅载入安全中心标准版][get-started]中所述启用安全数据收集。</span><span class="sxs-lookup"><span data-stu-id="52704-185">Enable security data collection as described in [Onboard your Azure subscription to Security Center Standard][get-started].</span></span> <span data-ttu-id="52704-186">启用数据收集后，安全中心会自动扫描该订阅下创建的所有 VM。</span><span class="sxs-lookup"><span data-stu-id="52704-186">When data collection is enabled, Security Center automatically scans any VMs created under that subscription.</span></span>

<span data-ttu-id="52704-187">**操作**。</span><span class="sxs-lookup"><span data-stu-id="52704-187">**Operations**.</span></span> <span data-ttu-id="52704-188">若要使用 Azure Active Directory (Azure AD) 身份验证令牌登录到 AKS 群集，请将 AKS 配置为使用 Azure AD 进行[用户身份验证][aad-auth]。</span><span class="sxs-lookup"><span data-stu-id="52704-188">To sign in to an AKS cluster using your Azure Active Directory (Azure AD) authentication token, configure AKS to use Azure AD for [user authentication][aad-auth].</span></span> <span data-ttu-id="52704-189">群集管理员还可以根据用户标识或目录组成员身份来配置 Kubernetes 基于角色的访问控制 (RBAC)。</span><span class="sxs-lookup"><span data-stu-id="52704-189">Cluster administrators can also configure Kubernetes role-based access control (RBAC) based on a user's identity or directory group membership.</span></span>

<span data-ttu-id="52704-190">使用 [RBAC][rbac] 控制对已部署的 Azure 资源的访问。</span><span class="sxs-lookup"><span data-stu-id="52704-190">Use [RBAC][rbac] to control access to the Azure resources that you deploy.</span></span> <span data-ttu-id="52704-191">RBAC 允许将授权角色分配给开发运营团队的成员。</span><span class="sxs-lookup"><span data-stu-id="52704-191">RBAC lets you assign authorization roles to members of your DevOps team.</span></span> <span data-ttu-id="52704-192">可将一个用户分配到多个角色，并且可以创建自定义角色以实现更细化的[权限]。</span><span class="sxs-lookup"><span data-stu-id="52704-192">A user can be assigned to multiple roles, and you can create custom roles for even more fine-grained [permissions].</span></span>

<span data-ttu-id="52704-193">**HTTPS**。</span><span class="sxs-lookup"><span data-stu-id="52704-193">**HTTPS**.</span></span> <span data-ttu-id="52704-194">作为安全最佳做法，应用程序应强制实施 HTTPS 并重定向 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="52704-194">As a security best practice, the application should enforce HTTPS and redirect HTTP requests.</span></span> <span data-ttu-id="52704-195">使用[入口控制器][ingress-controller]部署一个反向代理，用于终止 SSL 并重定向 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="52704-195">Use an [ingress controller][ingress-controller] to deploy a reverse proxy that terminates SSL and redirects HTTP requests.</span></span> <span data-ttu-id="52704-196">有关详细信息，请参阅[在 Azure Kubernetes 服务 (AKS) 中创建 HTTPS 入口控制器][https-ingress]。</span><span class="sxs-lookup"><span data-stu-id="52704-196">For more information, see [Create an HTTPS ingress controller on Azure Kubernetes Service (AKS)][https-ingress].</span></span>

<span data-ttu-id="52704-197">“身份验证”。</span><span class="sxs-lookup"><span data-stu-id="52704-197">**Authentication**.</span></span> <span data-ttu-id="52704-198">此解决方案不限制对终结点的访问。</span><span class="sxs-lookup"><span data-stu-id="52704-198">This solution doesn't restrict access to the endpoints.</span></span> <span data-ttu-id="52704-199">若要在企业设置中部署本体系结构，请通过 API 密钥保护终结点，并将某种形式的用户身份验证添加到客户端应用程序。</span><span class="sxs-lookup"><span data-stu-id="52704-199">To deploy the architecture in an enterprise setting, secure the endpoints through API keys and add some form of user authentication to the client application.</span></span>

<span data-ttu-id="52704-200">**容器注册表**。</span><span class="sxs-lookup"><span data-stu-id="52704-200">**Container registry**.</span></span> <span data-ttu-id="52704-201">此解决方案使用公共注册表来存储 Docker 映像。</span><span class="sxs-lookup"><span data-stu-id="52704-201">This solution uses a public registry to store the Docker image.</span></span> <span data-ttu-id="52704-202">应用程序依赖的代码以及模型将包含在此映像中。</span><span class="sxs-lookup"><span data-stu-id="52704-202">The code that the application depends on, and the model, are contained within this image.</span></span> <span data-ttu-id="52704-203">企业应用程序应使用专用注册表来帮助防范运行恶意代码以及容器中的信息泄密。</span><span class="sxs-lookup"><span data-stu-id="52704-203">Enterprise applications should use a private registry to help guard against running malicious code and to help keep the information inside the container from being compromised.</span></span>

<span data-ttu-id="52704-204">**DDOS 防护**。</span><span class="sxs-lookup"><span data-stu-id="52704-204">**DDoS protection**.</span></span> <span data-ttu-id="52704-205">考虑启用 [DDoS 防护标准版][ddos]。</span><span class="sxs-lookup"><span data-stu-id="52704-205">Consider enabling [DDoS Protection Standard][ddos].</span></span> <span data-ttu-id="52704-206">尽管基本 DDoS 防护已作为 Azure 平台的一部分启用，但 DDoS 防护标准版提供专门针对 Azure 虚拟网络资源优化的缓解功能。</span><span class="sxs-lookup"><span data-stu-id="52704-206">Although basic DDoS protection is enabled as part of the Azure platform, DDoS Protection Standard provides mitigation capabilities that are tuned specifically to Azure virtual network resources.</span></span>

<span data-ttu-id="52704-207">**日志记录**。</span><span class="sxs-lookup"><span data-stu-id="52704-207">**Logging**.</span></span> <span data-ttu-id="52704-208">在存储日志数据之前采用最佳做法，例如，清理可能被用来实施安全欺诈行为的用户密码和其他信息。</span><span class="sxs-lookup"><span data-stu-id="52704-208">Use best practices before storing log data, such as scrubbing user passwords and other information that could be used to commit security fraud.</span></span>

## <a name="deployment"></a><span data-ttu-id="52704-209">部署</span><span class="sxs-lookup"><span data-stu-id="52704-209">Deployment</span></span>

<span data-ttu-id="52704-210">若要部署本参考体系结构，请遵循 GitHub 存储库中所述的步骤：</span><span class="sxs-lookup"><span data-stu-id="52704-210">To deploy this reference architecture, follow the steps described in the GitHub repos:</span></span> 

  - <span data-ttu-id="52704-211">[常规 Python 模型][github-python]</span><span class="sxs-lookup"><span data-stu-id="52704-211">[Regular Python models][github-python]</span></span>
  - <span data-ttu-id="52704-212">[深度学习模型][github-dl]</span><span class="sxs-lookup"><span data-stu-id="52704-212">[Deep learning models][github-dl]</span></span>

[aad-auth]: /azure/aks/aad-integration
[acr]: /azure/container-registry/
[something]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/
[aks]: /azure/aks/intro-kubernetes
[autoscaler]: /azure/aks/autoscaler
[autoscale-pods]: /azure/aks/tutorial-kubernetes-scale#autoscale-pods
[azcopy]: /azure/storage/common/storage-use-azcopy-linux
[ddos]: /azure/virtual-network/ddos-protection-overview
[docker]: https://hub.docker.com/
[get-started]: /azure/security-center/security-center-get-started
[github-python]: https://github.com/Azure/MLAKSDeployment
[github-dl]: https://github.com/Microsoft/AKSDeploymentTutorial
[gpus-vs-cpus]: https://azure.microsoft.com/en-us/blog/gpus-vs-cpus-for-deployment-of-deep-learning-models/
[https-ingress]: /azure/aks/ingress-tls
[ingress-controller]: https://kubernetes.io/docs/concepts/services-networking/ingress/
[kubectl]: https://kubernetes.io/docs/tasks/tools/install-kubectl/
[lb]: /azure/load-balancer/load-balancer-overview
[manually-scale-pods]: /azure/aks/tutorial-kubernetes-scale#manually-scale-pods
[monitor-containers]: /azure/monitoring/monitoring-container-insights-overview
[权限]: /azure/aks/concepts-identity
[permissions]: /azure/aks/concepts-identity
[rbac]: /azure/active-directory/role-based-access-control-what-is
[scale-cluster]: /azure/aks/scale-cluster
[scikit]: https://pypi.org/project/scikit-learn/
[security-center]: /azure/security-center/security-center-intro
[vm]: /azure/virtual-machines/

