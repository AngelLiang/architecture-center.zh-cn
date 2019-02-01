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
# <a name="real-time-scoring-of-python-scikit-learn-and-deep-learning-models-on-azure"></a><span data-ttu-id="a814d-103">Azure 上 Python Scikit-Learn 和深度学习模型的实时评分</span><span class="sxs-lookup"><span data-stu-id="a814d-103">Real-time scoring of Python Scikit-Learn and deep learning models on Azure</span></span>

<span data-ttu-id="a814d-104">本参考体系结构演示如何使用 Azure 机器学习服务将 Python 模型部署为 Web 服务用于进行实时预测。</span><span class="sxs-lookup"><span data-stu-id="a814d-104">This reference architecture shows how to deploy Python models as web services to make real-time predictions using the Azure Machine Learning service.</span></span> <span data-ttu-id="a814d-105">将介绍两种方案：部署常规 Python 模型，以及部署深度学习模型所要满足的具体要求。</span><span class="sxs-lookup"><span data-stu-id="a814d-105">Two scenarios are covered: deploying regular Python models, and the specific requirements of deploying deep learning models.</span></span> <span data-ttu-id="a814d-106">这两种方案都使用下面所示的体系结构。</span><span class="sxs-lookup"><span data-stu-id="a814d-106">Both scenarios use the architecture shown.</span></span>

<span data-ttu-id="a814d-107">GitHub 上提供了本体系结构的两种参考实现，一个适用于[常规 Python 模型][github-python]，另一个适用于[深度学习模型][github-dl]。</span><span class="sxs-lookup"><span data-stu-id="a814d-107">Two reference implementations for this architecture are available on GitHub, one for [regular Python models][github-python] and one for [deep learning models][github-dl].</span></span>

![Azure 上 Python 模型的实时评分的体系结构图](./_images/python-model-architecture.png)

## <a name="scenarios"></a><span data-ttu-id="a814d-109">方案</span><span class="sxs-lookup"><span data-stu-id="a814d-109">Scenarios</span></span>

<span data-ttu-id="a814d-110">参考实现演示使用本体系结构的两种方案。</span><span class="sxs-lookup"><span data-stu-id="a814d-110">The reference implementations demonstrate two scenarios using this architecture.</span></span>

<span data-ttu-id="a814d-111">**方案 1：常见问题解答匹配**。</span><span class="sxs-lookup"><span data-stu-id="a814d-111">**Scenario 1: FAQ matching**.</span></span> <span data-ttu-id="a814d-112">此方案演示如何将常见问题 (FAQ) 匹配模型部署为 Web 服务，用于对用户的问题提供预测。</span><span class="sxs-lookup"><span data-stu-id="a814d-112">This scenario shows how to deploy a frequently asked questions (FAQ) matching model as a web service to provide predictions for user questions.</span></span> <span data-ttu-id="a814d-113">对于此方案，体系结构示意图中的“输入数据”是指包含要与 FAQ 列表匹配的用户问题的文本字符串。</span><span class="sxs-lookup"><span data-stu-id="a814d-113">For this scenario, "Input Data" in the architecture diagram refers to text strings containing user questions to match with a list of FAQs.</span></span> <span data-ttu-id="a814d-114">此方案是针对适用于 Python 的 [scikit-learn][scikit] 机器学习库设计的，但可通用化为使用 Python 模型进行实时预测的任何方案。</span><span class="sxs-lookup"><span data-stu-id="a814d-114">This scenario is designed for the [scikit-learn][scikit] machine learning library for Python, but can be generalized to any scenario that uses Python models to make real-time predictions.</span></span>

<span data-ttu-id="a814d-115">此方案使用堆栈溢出问题数据的子集，其中包括标记为 JavaScript 的原始问题、其重复问题及其答案。</span><span class="sxs-lookup"><span data-stu-id="a814d-115">This scenario uses a subset of Stack Overflow question data that includes original questions tagged as JavaScript, their duplicate questions, and their answers.</span></span> <span data-ttu-id="a814d-116">此方案训练某个 scikit-learn 管道来预测某个重复问题与每个原始问题匹配的概率。</span><span class="sxs-lookup"><span data-stu-id="a814d-116">It trains a scikit-learn pipeline to predict the match probability of a duplicate question with each of the original questions.</span></span> <span data-ttu-id="a814d-117">这些预测是使用 REST API 终结点实时做出的。</span><span class="sxs-lookup"><span data-stu-id="a814d-117">These predictions are made in real time using a REST API endpoint.</span></span>

<span data-ttu-id="a814d-118">本体系结构的应用程序流如下：</span><span class="sxs-lookup"><span data-stu-id="a814d-118">The application flow for this architecture is as follows:</span></span>

1. <span data-ttu-id="a814d-119">将训练的模型注册到机器学习模型注册表。</span><span class="sxs-lookup"><span data-stu-id="a814d-119">The trained model is registered to the Machine Learning model registry.</span></span>
2. <span data-ttu-id="a814d-120">机器学习服务创建包含模型和评分脚本的 Docker 映像。</span><span class="sxs-lookup"><span data-stu-id="a814d-120">The Machine Learning service creates a Docker image that includes the model and scoring script.</span></span>
3. <span data-ttu-id="a814d-121">机器学习将 Azure Kubernetes 服务 (AKS) 中的评分映像部署为 Web 服务。</span><span class="sxs-lookup"><span data-stu-id="a814d-121">Machine Learning deploys the scoring image on Azure Kubernetes Service (AKS) as a web service.</span></span>
4. <span data-ttu-id="a814d-122">客户端发送包含已编码问题数据的 HTTP POST 请求。</span><span class="sxs-lookup"><span data-stu-id="a814d-122">The client sends an HTTP POST request with the encoded question data.</span></span>
5. <span data-ttu-id="a814d-123">机器学习创建的 Web 服务从请求中提取问题。</span><span class="sxs-lookup"><span data-stu-id="a814d-123">The web service created by Machine Learning extracts the question from the request.</span></span>
6. <span data-ttu-id="a814d-124">将问题发送到 Scikit-learn 管道模型进行特征化和评分。</span><span class="sxs-lookup"><span data-stu-id="a814d-124">The question is sent to the Scikit-learn pipeline model for featurization and scoring.</span></span> 
7. <span data-ttu-id="a814d-125">将匹配的 FAQ 问题及其评分返回给客户端。</span><span class="sxs-lookup"><span data-stu-id="a814d-125">The matching FAQ questions with their scores are returned to the client.</span></span>

<span data-ttu-id="a814d-126">下面是使用结果的示例应用的屏幕截图：</span><span class="sxs-lookup"><span data-stu-id="a814d-126">Here is a screenshot of the example app that consumes the results:</span></span>

![示例应用的屏幕截图](./_images/python-faq-matches.png)

<span data-ttu-id="a814d-128">**方案 2：图像分类**。</span><span class="sxs-lookup"><span data-stu-id="a814d-128">**Scenario 2: Image classification**.</span></span> <span data-ttu-id="a814d-129">此方案演示如何将卷积神经网络 (CNN) 模型部署为 Web 服务，以基于图像提供预测。</span><span class="sxs-lookup"><span data-stu-id="a814d-129">This scenario shows how to deploy a Convolutional Neural Network (CNN) model as a web service to provide predictions on images.</span></span> <span data-ttu-id="a814d-130">对于此方案，体系结构示意图中的“输入数据”指图像文件。</span><span class="sxs-lookup"><span data-stu-id="a814d-130">For this scenario, "Input Data" in the architecture diagram refers to image files.</span></span> <span data-ttu-id="a814d-131">在计算机视觉领域，CNN 对图像分类和对象检测等任务非常有效。</span><span class="sxs-lookup"><span data-stu-id="a814d-131">CNNs are very effective in computer vision for tasks such as image classification and object detection.</span></span> <span data-ttu-id="a814d-132">此方案是针对 TensorFlow、Keras（具有 TensorFlow 后端）和 PyTorch 等框架设计的。</span><span class="sxs-lookup"><span data-stu-id="a814d-132">This scenario is designed for the frameworks TensorFlow, Keras (with the TensorFlow back end), and PyTorch.</span></span> <span data-ttu-id="a814d-133">但是，它可以通用化为使用深度学习模型进行实时预测的任何方案。</span><span class="sxs-lookup"><span data-stu-id="a814d-133">However, it can be generalized to any scenario that uses deep learning models to make real-time predictions.</span></span>

<span data-ttu-id="a814d-134">此方案使用基于 ImageNet-1K（1,000 个类）数据集预先训练的 ResNet-152 模型来预测某个图像属于哪些类别（参阅下图）。</span><span class="sxs-lookup"><span data-stu-id="a814d-134">This scenario uses a pre-trained ResNet-152 model trained on ImageNet-1K (1,000 classes) dataset to predict which category (see figure below) an image belongs to.</span></span> <span data-ttu-id="a814d-135">这些预测是使用 REST API 终结点实时做出的。</span><span class="sxs-lookup"><span data-stu-id="a814d-135">These predictions are made in real time using a REST API endpoint.</span></span>

![预测示例](./_images/python-example-predictions.png)

<span data-ttu-id="a814d-137">深度学习模型的应用程序流如下：</span><span class="sxs-lookup"><span data-stu-id="a814d-137">The application flow for the deep learning model is as follows:</span></span>

1. <span data-ttu-id="a814d-138">将深度学习模型注册到机器学习模型注册表。</span><span class="sxs-lookup"><span data-stu-id="a814d-138">The deep learning model is registered to the Machine Learning model registry.</span></span>
2. <span data-ttu-id="a814d-139">机器学习服务创建包含模型和评分脚本的 Docker 映像。</span><span class="sxs-lookup"><span data-stu-id="a814d-139">The Machine Learning service creates a docker image including the model and scoring script.</span></span>
3. <span data-ttu-id="a814d-140">机器学习将 Azure Kubernetes 服务 (AKS) 中的评分映像部署为 Web 服务。</span><span class="sxs-lookup"><span data-stu-id="a814d-140">Machine Learning deploys the scoring image on Azure Kubernetes Service (AKS) as a web service.</span></span>
4. <span data-ttu-id="a814d-141">客户端发送包含已编码图像数据的 HTTP POST 请求。</span><span class="sxs-lookup"><span data-stu-id="a814d-141">The client sends an HTTP POST request with the encoded image data.</span></span>
5. <span data-ttu-id="a814d-142">机器学习创建的 Web 服务对图像数据进行预处理，然后将其发送到模型进行评分。</span><span class="sxs-lookup"><span data-stu-id="a814d-142">The web service created by Machine Learning preprocesses the image data and sends it to the model for scoring.</span></span> 
6. <span data-ttu-id="a814d-143">将预测的类别及其评分返回给客户端。</span><span class="sxs-lookup"><span data-stu-id="a814d-143">The predicted categories with their scores are returned to the client.</span></span>

## <a name="architecture"></a><span data-ttu-id="a814d-144">体系结构</span><span class="sxs-lookup"><span data-stu-id="a814d-144">Architecture</span></span>

<span data-ttu-id="a814d-145">该体系结构包括以下组件。</span><span class="sxs-lookup"><span data-stu-id="a814d-145">This architecture consists of the following components.</span></span>

<span data-ttu-id="a814d-146">**[Azure 机器学习服务][aml]** 是一项云服务，可以使用它来训练、部署、自动执行以及管理机器学习模型，所有这些都是在云提供的广泛范围内进行的。</span><span class="sxs-lookup"><span data-stu-id="a814d-146">**[Azure Machine Learning service][aml]** is a cloud service that is used to train, deploy, automate and manage machine learning models, all at the broad scale that the cloud provides.</span></span> <span data-ttu-id="a814d-147">本体系结构使用该服务来管理模型的部署，以及 Web 服务的身份验证、路由和负载均衡。</span><span class="sxs-lookup"><span data-stu-id="a814d-147">It is used in this architecture to manage the deployment of models as well as authentication, routing, and load balancing of the web service.</span></span>

<span data-ttu-id="a814d-148">**[虚拟机][vm]** (VM)。</span><span class="sxs-lookup"><span data-stu-id="a814d-148">**[Virtual machine][vm]** (VM).</span></span> <span data-ttu-id="a814d-149">VM 显示为本地或云中的设备示例，它可以发送 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="a814d-149">The VM is shown as an example of a device &mdash; local or in the cloud &mdash; that can send an HTTP request.</span></span>

<span data-ttu-id="a814d-150">**[Azure Kubernetes 服务][aks]** (AKS) 用于在 Kubernetes 群集上部署应用程序。</span><span class="sxs-lookup"><span data-stu-id="a814d-150">**[Azure Kubernetes Service][aks]** (AKS) is used to deploy the application on a Kubernetes cluster.</span></span> <span data-ttu-id="a814d-151">AKS 简化了 Kubernetes 的部署和操作。</span><span class="sxs-lookup"><span data-stu-id="a814d-151">AKS simplifies the deployment and operations of Kubernetes.</span></span> <span data-ttu-id="a814d-152">可以使用仅有 CPU 的 VM 为常规 Python 模型配置群集，或使用支持 GPU 的 VM 为深度学习模型配置群集。</span><span class="sxs-lookup"><span data-stu-id="a814d-152">The cluster can be configured using CPU-only VMs for regular Python models or GPU-enabled VMs for deep learning models.</span></span>

<span data-ttu-id="a814d-153">**[Azure 容器注册表][acr]** 用于存储所有类型的 Docker 容器部署（包括 DC/OS、Docker Swarm 和 Kubernetes）的映像。</span><span class="sxs-lookup"><span data-stu-id="a814d-153">**[Azure Container Registry][acr]** enables storage of images for all types of Docker container deployments including DC/OS, Docker Swarm and Kubernetes.</span></span> <span data-ttu-id="a814d-154">评分映像部署为 Azure Kubernetes 服务上的容器，用于运行评分脚本。</span><span class="sxs-lookup"><span data-stu-id="a814d-154">The scoring images are deployed as containers on Azure Kubernetes Service and used to run the scoring script.</span></span> <span data-ttu-id="a814d-155">此处使用的映像是机器学习基于训练的模型和评分脚本创建的，随后将推送到 Azure 容器注册表。</span><span class="sxs-lookup"><span data-stu-id="a814d-155">The image used here is created by Machine Learning from the trained model and scoring script, and then is pushed to the Azure Container Registry.</span></span>

## <a name="performance-considerations"></a><span data-ttu-id="a814d-156">性能注意事项</span><span class="sxs-lookup"><span data-stu-id="a814d-156">Performance considerations</span></span>

<span data-ttu-id="a814d-157">对于实时评分体系结构，吞吐量性能是首要考虑因素。</span><span class="sxs-lookup"><span data-stu-id="a814d-157">For real-time scoring architectures, throughput performance becomes a dominant consideration.</span></span> <span data-ttu-id="a814d-158">对于常规 Python 模型，仅使用 CPU 通常就足以处理工作负荷。</span><span class="sxs-lookup"><span data-stu-id="a814d-158">For regular Python models, it's generally accepted that CPUs are sufficient to handle the workload.</span></span>

<span data-ttu-id="a814d-159">但是，对于深度学习工作负荷，如果速度是一个瓶颈，则可以使用 GPU，因为它的[性能][gpus-vs-cpus]通常优于 CPU。</span><span class="sxs-lookup"><span data-stu-id="a814d-159">However for deep learning workloads, when speed is a bottleneck, GPUs generally provide better [performance][gpus-vs-cpus] compared to CPUs.</span></span> <span data-ttu-id="a814d-160">若要在使用 CPU 的情况下获得相当于 GPU 的性能，通常一个具有需要大量 CPU 的群集。</span><span class="sxs-lookup"><span data-stu-id="a814d-160">To match GPU performance using CPUs, a cluster with large number of CPUs is usually needed.</span></span>

<span data-ttu-id="a814d-161">可在任一方案中对本体系结构使用 CPU，但对于深度学习模型，GPU 提供的吞吐量值明显高于 CPU 群集，而且成本类似。</span><span class="sxs-lookup"><span data-stu-id="a814d-161">You can use CPUs for this architecture in either scenario, but for deep learning models, GPUs provide significantly higher throughput values compared to a CPU cluster of similar cost.</span></span> <span data-ttu-id="a814d-162">AKS 支持使用 GPU，这是对本体系结构使用 AKS 的一个优势。</span><span class="sxs-lookup"><span data-stu-id="a814d-162">AKS supports the use of GPUs, which is one advantage of using AKS for this architecture.</span></span> <span data-ttu-id="a814d-163">另外，深度学习部署通常使用具有大量参数的模型。</span><span class="sxs-lookup"><span data-stu-id="a814d-163">Also, deep learning deployments typically use models with a high number of parameters.</span></span> <span data-ttu-id="a814d-164">使用 GPU 可以防止模型与 Web 服务之间发生资源争用，这在仅用 CPU 的部署中是一个问题。</span><span class="sxs-lookup"><span data-stu-id="a814d-164">Using GPUs prevents contention for resources between the model and the web service, which is an issue in CPU-only deployments.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="a814d-165">可伸缩性注意事项</span><span class="sxs-lookup"><span data-stu-id="a814d-165">Scalability considerations</span></span>

<span data-ttu-id="a814d-166">对于 AKS 群集是使用仅有 CPU 的 VM 预配的常规 Python 模型，在[扩展 pod 数][manually-scale-pods]时请小心。</span><span class="sxs-lookup"><span data-stu-id="a814d-166">For regular Python models, where the AKS cluster is provisioned with CPU-only VMs, take care when [scaling out the number of pods][manually-scale-pods].</span></span> <span data-ttu-id="a814d-167">目标是充分利用群集。</span><span class="sxs-lookup"><span data-stu-id="a814d-167">The goal is to fully utilize the cluster.</span></span> <span data-ttu-id="a814d-168">缩放取决于为 pod 定义的 CPU 请求和限制。</span><span class="sxs-lookup"><span data-stu-id="a814d-168">Scaling depends on the CPU requests and limits defined for the pods.</span></span> <span data-ttu-id="a814d-169">通过 Kubernetes 运行的机器学习还支持根据 CPU 利用率或其他指标进行 [pod 自动缩放][autoscale-pods]。</span><span class="sxs-lookup"><span data-stu-id="a814d-169">Machine Learning through Kubernetes also supports [pod autoscaling][autoscale-pods] based on CPU utilization or other metrics.</span></span> <span data-ttu-id="a814d-170">[群集自动缩放程序][autoscaler]（预览版）可以根据等待中的 pod 缩放代理节点。</span><span class="sxs-lookup"><span data-stu-id="a814d-170">The [cluster autoscaler][autoscaler] (in preview) can scale agent nodes based on the pending pods.</span></span>

<span data-ttu-id="a814d-171">对于使用支持 GPU 的 VM 的深入学习方案，pod 的资源限制方式是将一个 GPU 分配给一个 pod。</span><span class="sxs-lookup"><span data-stu-id="a814d-171">For deep learning scenarios, using GPU-enabled VMs, resource limits on pods are such that one GPU is assigned to one pod.</span></span> <span data-ttu-id="a814d-172">根据所用的 VM 类型，必须[缩放群集的节点][scale-cluster]来满足服务的需求。</span><span class="sxs-lookup"><span data-stu-id="a814d-172">Depending on the type of VM used, you must [scale the nodes of the cluster][scale-cluster] to meet the demand for the service.</span></span> <span data-ttu-id="a814d-173">可以使用 Azure CLI 和 kubectl 轻松实现此目的。</span><span class="sxs-lookup"><span data-stu-id="a814d-173">You can do this easily using the Azure CLI and kubectl.</span></span>

## <a name="monitoring-and-logging-considerations"></a><span data-ttu-id="a814d-174">监视和日志记录注意事项</span><span class="sxs-lookup"><span data-stu-id="a814d-174">Monitoring and logging considerations</span></span>

### <a name="aks-monitoring"></a><span data-ttu-id="a814d-175">AKS 监视</span><span class="sxs-lookup"><span data-stu-id="a814d-175">AKS monitoring</span></span>

<span data-ttu-id="a814d-176">若要洞察 AKS 性能，请使用[用于容器的 Azure Monitor][monitor-containers] 功能。</span><span class="sxs-lookup"><span data-stu-id="a814d-176">For visibility into AKS performance, use the [Azure Monitor for containers][monitor-containers] feature.</span></span> <span data-ttu-id="a814d-177">此服务通过指标 API 从 Kubernetes 中提供的控制器、节点和容器收集内存与处理器指标。</span><span class="sxs-lookup"><span data-stu-id="a814d-177">It collects memory and processor metrics from controllers, nodes, and containers that are available in Kubernetes through the Metrics API.</span></span>

<span data-ttu-id="a814d-178">部署应用程序时，请监视 AKS 群集，以确保它按预期方式工作、所有节点都正常运行，并且所有 pod 都在运行。</span><span class="sxs-lookup"><span data-stu-id="a814d-178">While deploying your application, monitor the AKS cluster to make sure it's working as expected, all the nodes are operational, and all pods are running.</span></span> <span data-ttu-id="a814d-179">尽管你可以使用 [kubectl][kubectl] 命令行工具来检索 pod 状态，但 Kubernetes 还包含一个 Web 仪表板，用于进行基本的群集状态监视和管理。</span><span class="sxs-lookup"><span data-stu-id="a814d-179">Although you can use the [kubectl][kubectl] command-line tool to retrieve pod status, Kubernetes also includes a web dashboard for basic monitoring of the cluster status and management.</span></span>

![Kubernetes 仪表板的屏幕截图](./_images/python-kubernetes-dashboard.png)

<span data-ttu-id="a814d-181">若要查看群集和节点的总体状态，请转到 Kubernetes 仪表板的“节点”部分。</span><span class="sxs-lookup"><span data-stu-id="a814d-181">To see the overall state of the cluster and nodes, go to the **Nodes** section of the Kubernetes dashboard.</span></span> <span data-ttu-id="a814d-182">如果某个节点处于非活动状态或发生故障，可以在该页中显示错误日志。</span><span class="sxs-lookup"><span data-stu-id="a814d-182">If a node is inactive or has failed, you can display the error logs from that page.</span></span> <span data-ttu-id="a814d-183">同样，转到“Pod”和“部署”部分可获取有关 pod 数目和部署状态的信息。</span><span class="sxs-lookup"><span data-stu-id="a814d-183">Similarly, go to the **Pods** and **Deployments** sections for information about the number of pods and status of your deployment.</span></span>

### <a name="aks-logs"></a><span data-ttu-id="a814d-184">AKS 日志</span><span class="sxs-lookup"><span data-stu-id="a814d-184">AKS logs</span></span>

<span data-ttu-id="a814d-185">AKS 自动将所有 stdout/stderr 记录到群集中 pod 的日志。</span><span class="sxs-lookup"><span data-stu-id="a814d-185">AKS automatically logs all stdout/stderr to the logs of the pods in the cluster.</span></span> <span data-ttu-id="a814d-186">使用 kubectl 可查看这些日志，以及节点级别的事件和日志。</span><span class="sxs-lookup"><span data-stu-id="a814d-186">Use kubectl to see these and also node-level events and logs.</span></span> <span data-ttu-id="a814d-187">有关详细信息，请参阅部署步骤。</span><span class="sxs-lookup"><span data-stu-id="a814d-187">For details, see the deployment steps.</span></span>

<span data-ttu-id="a814d-188">使用[用于容器的 Azure Monitor][monitor-containers] 通过适用于 Linux 的 Log Analytics 代理的容器化版本，来收集存储在 Log Analytics 工作区中的指标和日志。</span><span class="sxs-lookup"><span data-stu-id="a814d-188">Use [Azure Monitor for containers][monitor-containers] to collect metrics and logs through a containerized version of the Log Analytics agent for Linux, which is stored in your Log Analytics workspace.</span></span>

## <a name="security-considerations"></a><span data-ttu-id="a814d-189">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="a814d-189">Security considerations</span></span>

<span data-ttu-id="a814d-190">使用 [Azure 安全中心][security-center]可在一个中心视图中获得 Azure 资源的安全状态。</span><span class="sxs-lookup"><span data-stu-id="a814d-190">Use [Azure Security Center][security-center] to get a central view of the security state of your Azure resources.</span></span> <span data-ttu-id="a814d-191">安全中心会监视潜在的安全问题并全面描述部署的安全运行状况，不过，它不会监视 AKS 代理节点。</span><span class="sxs-lookup"><span data-stu-id="a814d-191">Security Center monitors potential security issues and provides a comprehensive picture of the security health of your deployment, although it doesn't monitor AKS agent nodes.</span></span> <span data-ttu-id="a814d-192">安全中心针对每个 Azure 订阅进行配置。</span><span class="sxs-lookup"><span data-stu-id="a814d-192">Security Center is configured per Azure subscription.</span></span> <span data-ttu-id="a814d-193">根据[将 Azure 订阅载入安全中心标准版][get-started]中所述启用安全数据收集。</span><span class="sxs-lookup"><span data-stu-id="a814d-193">Enable security data collection as described in [Onboard your Azure subscription to Security Center Standard][get-started].</span></span> <span data-ttu-id="a814d-194">启用数据收集后，安全中心会自动扫描该订阅下创建的所有 VM。</span><span class="sxs-lookup"><span data-stu-id="a814d-194">When data collection is enabled, Security Center automatically scans any VMs created under that subscription.</span></span>

<span data-ttu-id="a814d-195">**操作**。</span><span class="sxs-lookup"><span data-stu-id="a814d-195">**Operations**.</span></span> <span data-ttu-id="a814d-196">若要使用 Azure Active Directory (Azure AD) 身份验证令牌登录到 AKS 群集，请将 AKS 配置为使用 Azure AD 进行[用户身份验证][aad-auth]。</span><span class="sxs-lookup"><span data-stu-id="a814d-196">To sign in to an AKS cluster using your Azure Active Directory (Azure AD) authentication token, configure AKS to use Azure AD for [user authentication][aad-auth].</span></span> <span data-ttu-id="a814d-197">群集管理员还可以根据用户标识或目录组成员身份来配置 Kubernetes 基于角色的访问控制 (RBAC)。</span><span class="sxs-lookup"><span data-stu-id="a814d-197">Cluster administrators can also configure Kubernetes role-based access control (RBAC) based on a user's identity or directory group membership.</span></span>

<span data-ttu-id="a814d-198">使用 [RBAC][rbac] 控制对已部署的 Azure 资源的访问。</span><span class="sxs-lookup"><span data-stu-id="a814d-198">Use [RBAC][rbac] to control access to the Azure resources that you deploy.</span></span> <span data-ttu-id="a814d-199">RBAC 允许将授权角色分配给开发运营团队的成员。</span><span class="sxs-lookup"><span data-stu-id="a814d-199">RBAC lets you assign authorization roles to members of your DevOps team.</span></span> <span data-ttu-id="a814d-200">可将一个用户分配到多个角色，并且可以创建自定义角色以实现更细化的[权限]。</span><span class="sxs-lookup"><span data-stu-id="a814d-200">A user can be assigned to multiple roles, and you can create custom roles for even more fine-grained [permissions].</span></span>

<span data-ttu-id="a814d-201">**HTTPS**。</span><span class="sxs-lookup"><span data-stu-id="a814d-201">**HTTPS**.</span></span> <span data-ttu-id="a814d-202">作为安全最佳做法，应用程序应强制实施 HTTPS 并重定向 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="a814d-202">As a security best practice, the application should enforce HTTPS and redirect HTTP requests.</span></span> <span data-ttu-id="a814d-203">使用[入口控制器][ingress-controller]部署一个反向代理，用于终止 SSL 并重定向 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="a814d-203">Use an [ingress controller][ingress-controller] to deploy a reverse proxy that terminates SSL and redirects HTTP requests.</span></span> <span data-ttu-id="a814d-204">有关详细信息，请参阅[在 Azure Kubernetes 服务 (AKS) 中创建 HTTPS 入口控制器][https-ingress]。</span><span class="sxs-lookup"><span data-stu-id="a814d-204">For more information, see [Create an HTTPS ingress controller on Azure Kubernetes Service (AKS)][https-ingress].</span></span>

<span data-ttu-id="a814d-205">“身份验证”。</span><span class="sxs-lookup"><span data-stu-id="a814d-205">**Authentication**.</span></span> <span data-ttu-id="a814d-206">此解决方案不限制对终结点的访问。</span><span class="sxs-lookup"><span data-stu-id="a814d-206">This solution doesn't restrict access to the endpoints.</span></span> <span data-ttu-id="a814d-207">若要在企业设置中部署本体系结构，请通过 API 密钥保护终结点，并将某种形式的用户身份验证添加到客户端应用程序。</span><span class="sxs-lookup"><span data-stu-id="a814d-207">To deploy the architecture in an enterprise setting, secure the endpoints through API keys and add some form of user authentication to the client application.</span></span>

<span data-ttu-id="a814d-208">**容器注册表**。</span><span class="sxs-lookup"><span data-stu-id="a814d-208">**Container registry**.</span></span> <span data-ttu-id="a814d-209">此解决方案使用 Azure 容器注册表存储 Docker 映像。</span><span class="sxs-lookup"><span data-stu-id="a814d-209">This solution uses Azure Container Registry to store the Docker image.</span></span> <span data-ttu-id="a814d-210">应用程序依赖的代码以及模型将包含在此映像中。</span><span class="sxs-lookup"><span data-stu-id="a814d-210">The code that the application depends on, and the model, are contained within this image.</span></span> <span data-ttu-id="a814d-211">企业应用程序应使用专用注册表来帮助防范运行恶意代码以及容器中的信息泄密。</span><span class="sxs-lookup"><span data-stu-id="a814d-211">Enterprise applications should use a private registry to help guard against running malicious code and to help keep the information inside the container from being compromised.</span></span>

<span data-ttu-id="a814d-212">**DDOS 防护**。</span><span class="sxs-lookup"><span data-stu-id="a814d-212">**DDoS protection**.</span></span> <span data-ttu-id="a814d-213">考虑启用 [DDoS 防护标准版][ddos]。</span><span class="sxs-lookup"><span data-stu-id="a814d-213">Consider enabling [DDoS Protection Standard][ddos].</span></span> <span data-ttu-id="a814d-214">尽管基本 DDoS 防护已作为 Azure 平台的一部分启用，但 DDoS 防护标准版提供专门针对 Azure 虚拟网络资源优化的缓解功能。</span><span class="sxs-lookup"><span data-stu-id="a814d-214">Although basic DDoS protection is enabled as part of the Azure platform, DDoS Protection Standard provides mitigation capabilities that are tuned specifically to Azure virtual network resources.</span></span>

<span data-ttu-id="a814d-215">**日志记录**。</span><span class="sxs-lookup"><span data-stu-id="a814d-215">**Logging**.</span></span> <span data-ttu-id="a814d-216">在存储日志数据之前采用最佳做法，例如，清理可能被用来实施安全欺诈行为的用户密码和其他信息。</span><span class="sxs-lookup"><span data-stu-id="a814d-216">Use best practices before storing log data, such as scrubbing user passwords and other information that could be used to commit security fraud.</span></span>

## <a name="deployment"></a><span data-ttu-id="a814d-217">部署</span><span class="sxs-lookup"><span data-stu-id="a814d-217">Deployment</span></span>

<span data-ttu-id="a814d-218">若要部署本参考体系结构，请遵循 GitHub 存储库中所述的步骤：</span><span class="sxs-lookup"><span data-stu-id="a814d-218">To deploy this reference architecture, follow the steps described in the GitHub repos:</span></span>

- <span data-ttu-id="a814d-219">[常规 Python 模型][github-python]</span><span class="sxs-lookup"><span data-stu-id="a814d-219">[Regular Python models][github-python]</span></span>
- <span data-ttu-id="a814d-220">[深度学习模型][github-dl]</span><span class="sxs-lookup"><span data-stu-id="a814d-220">[Deep learning models][github-dl]</span></span>

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
[permissions]: /azure/aks/concepts-identity
[rbac]: /azure/active-directory/role-based-access-control-what-is
[scale-cluster]: /azure/aks/scale-cluster
[scikit]: https://pypi.org/project/scikit-learn/
[security-center]: /azure/security-center/security-center-intro
[vm]: /azure/virtual-machines/
