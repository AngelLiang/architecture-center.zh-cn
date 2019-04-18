---
title: 在 Azure 上生成实时建议 API
description: 使用 Azure Databricks 和 Azure Data Science Virtual Machines (DSVM) 在 Azure 上训练一个模型，以便通过机器学习自动生成建议。
author: njray
ms.date: 12/12/2018
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: azcat-ai
ms.openlocfilehash: c7e7423da11667c90d53247c2c5303a8fbd1a76a
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59640152"
---
# <a name="build-a-real-time-recommendation-api-on-azure"></a><span data-ttu-id="e787a-103">在 Azure 上生成实时建议 API</span><span class="sxs-lookup"><span data-stu-id="e787a-103">Build a real-time recommendation API on Azure</span></span>

<span data-ttu-id="e787a-104">本参考体系结构演示如何使用 Azure Databricks 训练一个建议模型，并使用 Azure Cosmos DB、Azure 机器学习和 Azure Kubernetes 服务 (AKS) 将其作为 API 部署。</span><span class="sxs-lookup"><span data-stu-id="e787a-104">This reference architecture shows how to train a recommendation model using Azure Databricks and deploy it as an API by using Azure Cosmos DB, Azure Machine Learning, and Azure Kubernetes Service (AKS).</span></span> <span data-ttu-id="e787a-105">本体系结构可以通用于大多数建议引擎方案，包括针对产品、影片和新闻的建议。</span><span class="sxs-lookup"><span data-stu-id="e787a-105">This architecture can be generalized for most recommendation engine scenarios, including recommendations for products, movies, and news.</span></span>

<span data-ttu-id="e787a-106">[GitHub][als-example] 中提供了本体系结构的参考实现。</span><span class="sxs-lookup"><span data-stu-id="e787a-106">A reference implementation for this architecture is available on [GitHub][als-example].</span></span>

![用于训练影片建议的机器学习模型的体系结构](./_images/recommenders-architecture.png)

<span data-ttu-id="e787a-108">**场景**：一家媒体组织希望向其用户提供影片或视频建议。</span><span class="sxs-lookup"><span data-stu-id="e787a-108">**Scenario**: A media organization wants to provide movie or video recommendations to its users.</span></span> <span data-ttu-id="e787a-109">通过提供个性化建议，组织可以满足多个业务目标，包括提高点击率、提高站点参与度以及提高用户满意度。</span><span class="sxs-lookup"><span data-stu-id="e787a-109">By providing personalized recommendations, the organization meets several business goals, including increased click-through rates, increased engagement on site, and higher user satisfaction.</span></span>

<span data-ttu-id="e787a-110">本参考体系结构用于训练和部署实时推荐器服务 API，以便为给定用户提供排名前 10 的影片的建议。</span><span class="sxs-lookup"><span data-stu-id="e787a-110">This reference architecture is for training and deploying a real-time recommender service API that can provide the top 10 movie recommendations for a given user.</span></span>

<span data-ttu-id="e787a-111">此建议模型的数据流如下：</span><span class="sxs-lookup"><span data-stu-id="e787a-111">The data flow for this recommendation model is as follows:</span></span>

1. <span data-ttu-id="e787a-112">跟踪用户行为。</span><span class="sxs-lookup"><span data-stu-id="e787a-112">Track user behaviors.</span></span> <span data-ttu-id="e787a-113">例如，后端服务可能会记录用户何时对影片进行了评分或单击了某个产品或某篇新闻文章。</span><span class="sxs-lookup"><span data-stu-id="e787a-113">For example, a backend service might log when a user rates a movie or clicks a product or news article.</span></span>

2. <span data-ttu-id="e787a-114">从可用的[数据源][data-source]将数据加载到 Azure Databricks 中。</span><span class="sxs-lookup"><span data-stu-id="e787a-114">Load the data into Azure Databricks from an available [data source][data-source].</span></span>

3. <span data-ttu-id="e787a-115">准备数据并将其拆分为训练集和测试集，以便训练模型。</span><span class="sxs-lookup"><span data-stu-id="e787a-115">Prepare the data and split it into training and testing sets to train the model.</span></span> <span data-ttu-id="e787a-116">（[此指南][guide]介绍拆分数据的选项。）</span><span class="sxs-lookup"><span data-stu-id="e787a-116">([This guide][guide] describes options for splitting data.)</span></span>

4. <span data-ttu-id="e787a-117">根据数据对 [Spark 协作筛选][als]模型进行拟合。</span><span class="sxs-lookup"><span data-stu-id="e787a-117">Fit the [Spark Collaborative Filtering][als] model to the data.</span></span>

5. <span data-ttu-id="e787a-118">使用评分和排名指标对模型质量进行评估。</span><span class="sxs-lookup"><span data-stu-id="e787a-118">Evaluate the quality of the model using rating and ranking metrics.</span></span> <span data-ttu-id="e787a-119">（[此指南][eval-guide]详细介绍了可以用来评估推荐器的指标。）</span><span class="sxs-lookup"><span data-stu-id="e787a-119">([This guide][eval-guide] provides details about the metrics you can evaluate your recommender on.)</span></span>

6. <span data-ttu-id="e787a-120">按用户预先计算排名前 10 的建议，并在 Azure Cosmos DB 中将其作为缓存存储。</span><span class="sxs-lookup"><span data-stu-id="e787a-120">Precompute the top 10 recommendations per user and store as a cache in Azure Cosmos DB.</span></span>

7. <span data-ttu-id="e787a-121">使用 Azure 机器学习 API 将 API 服务部署到 AKS，以便容器化和部署 API。</span><span class="sxs-lookup"><span data-stu-id="e787a-121">Deploy an API service to AKS using the Azure Machine Learning APIs to containerize and deploy the API.</span></span>

8. <span data-ttu-id="e787a-122">当后端服务从用户获取请求时，请调用托管在 AKS 中的建议 API，以便获取排名前 10 的建议并将其显示给用户。</span><span class="sxs-lookup"><span data-stu-id="e787a-122">When the backend service gets a request from a user, call the recommendations API hosted in AKS to get the top 10 recommendations and display them to the user.</span></span>

## <a name="architecture"></a><span data-ttu-id="e787a-123">体系结构</span><span class="sxs-lookup"><span data-stu-id="e787a-123">Architecture</span></span>

<span data-ttu-id="e787a-124">该体系结构包括以下组件：</span><span class="sxs-lookup"><span data-stu-id="e787a-124">This architecture consists of the following components:</span></span>

<span data-ttu-id="e787a-125">[Azure Databricks][databricks]。</span><span class="sxs-lookup"><span data-stu-id="e787a-125">[Azure Databricks][databricks].</span></span> <span data-ttu-id="e787a-126">Databricks 是一个开发环境，用于在 Spark 群集上准备输入数据并训练推荐器模型。</span><span class="sxs-lookup"><span data-stu-id="e787a-126">Databricks is a development environment used to prepare input data and train the recommender model on a Spark cluster.</span></span> <span data-ttu-id="e787a-127">Azure Databricks 还提供一个交互式工作区，用于运行笔记本并在其上进行协作，以便执行数据处理或机器学习任务。</span><span class="sxs-lookup"><span data-stu-id="e787a-127">Azure Databricks also provides an interactive workspace to run and collaborate on notebooks for any data processing or machine learning tasks.</span></span>

<span data-ttu-id="e787a-128">[Azure Kubernetes 服务][aks] (AKS)。</span><span class="sxs-lookup"><span data-stu-id="e787a-128">[Azure Kubernetes Service][aks] (AKS).</span></span> <span data-ttu-id="e787a-129">AKS 用于在 Kubernetes 群集上部署机器学习模型服务 API 并使之可操作。</span><span class="sxs-lookup"><span data-stu-id="e787a-129">AKS is used to deploy and operationalize a machine learning model service API on a Kubernetes cluster.</span></span> <span data-ttu-id="e787a-130">AKS 托管容器化模型，提供符合吞吐量要求的可伸缩性，同时提供标识和访问管理以及日志记录和运行状况监视功能。</span><span class="sxs-lookup"><span data-stu-id="e787a-130">AKS hosts the containerized model, providing scalability that meets your throughput requirements, identity and access management, and logging and health monitoring.</span></span>

<span data-ttu-id="e787a-131">[Azure Cosmos DB][cosmosdb]。</span><span class="sxs-lookup"><span data-stu-id="e787a-131">[Azure Cosmos DB][cosmosdb].</span></span> <span data-ttu-id="e787a-132">Cosmos DB 是一种全球分布式数据库服务，用于为每位用户存储排名前 10 的推荐影片。</span><span class="sxs-lookup"><span data-stu-id="e787a-132">Cosmos DB is a globally distributed database service used to store the top 10 recommended movies for each user.</span></span> <span data-ttu-id="e787a-133">Azure Cosmos DB 特别适合本方案，因为它的延迟低（99% 的情况下为 10 毫秒），可以为给定用户读取排名靠前的建议项。</span><span class="sxs-lookup"><span data-stu-id="e787a-133">Azure Cosmos DB is well-suited for this scenario, because it provides low latency (10 ms at 99th percentile) to read the top recommended items for a given user.</span></span>

<span data-ttu-id="e787a-134">[Azure 机器学习服务][mls]。</span><span class="sxs-lookup"><span data-stu-id="e787a-134">[Azure Machine Learning Service][mls].</span></span> <span data-ttu-id="e787a-135">此服务用于跟踪和管理机器学习模型，然后将这些模型打包并部署到可缩放的 AKS 环境。</span><span class="sxs-lookup"><span data-stu-id="e787a-135">This service is used to track and manage machine learning models, and then package and deploy these models to a scalable AKS environment.</span></span>

<span data-ttu-id="e787a-136">[Microsoft 推荐器][github]。</span><span class="sxs-lookup"><span data-stu-id="e787a-136">[Microsoft Recommenders][github].</span></span> <span data-ttu-id="e787a-137">此开源存储库包含实用程序代码和示例，有助于用户完成推荐器系统的生成、评估和可操作方面的入门。</span><span class="sxs-lookup"><span data-stu-id="e787a-137">This open-source repository contains utility code and samples to help users get started in building, evaluating, and operationalizing a recommender system.</span></span>

## <a name="performance-considerations"></a><span data-ttu-id="e787a-138">性能注意事项</span><span class="sxs-lookup"><span data-stu-id="e787a-138">Performance considerations</span></span>

<span data-ttu-id="e787a-139">性能是实时建议的主要考量，因为建议通常落在用户在站点上发出的请求的关键路径中。</span><span class="sxs-lookup"><span data-stu-id="e787a-139">Performance is a primary consideration for real-time recommendations, because recommendations usually fall in the critical path of the request a user makes on your site.</span></span>

<span data-ttu-id="e787a-140">将 AKS 和 Azure Cosmos DB 组合在一起以后，此体系结构就可以为中型工作负荷提供建议，其开销极小。</span><span class="sxs-lookup"><span data-stu-id="e787a-140">The combination of AKS and Azure Cosmos DB enables this architecture to provide a good starting point to provide recommendations for a medium-sized workload with minimal overhead.</span></span> <span data-ttu-id="e787a-141">在进行 200 个并发用户的负载测试时，此体系结构可以在中等延迟（大约 60 毫秒）情况下提供建议，表现出的吞吐量为每秒 180 个请求。</span><span class="sxs-lookup"><span data-stu-id="e787a-141">Under a load test with 200 concurrent users, this architecture provides recommendations at a median latency of about 60 ms and performs at a throughput of 180 requests per second.</span></span> <span data-ttu-id="e787a-142">负载测试是针对默认部署配置（一个 3x D3 v2 AKS 群集，包含 12 个 vCPU、42 GB 内存、11,000 [请求单元 (RU)/秒][ru]，针对 Azure Cosmos DB 进行预配）运行的。</span><span class="sxs-lookup"><span data-stu-id="e787a-142">The load test was run against the default deployment configuration (a 3x D3 v2 AKS cluster with 12 vCPUs, 42 GB of memory, and 11,000 [Request Units (RUs) per second][ru] provisioned for Azure Cosmos DB).</span></span>

![性能图](./_images/recommenders-performance.png)

![吞吐量图](./_images/recommenders-throughput.png)

<span data-ttu-id="e787a-145">建议使用 Azure Cosmos DB 是因为它的统包式全球分发功能，以及它可以用来满足应用的任何数据库要求。</span><span class="sxs-lookup"><span data-stu-id="e787a-145">Azure Cosmos DB is recommended for its turnkey global distribution and usefulness in meeting any database requirements your app has.</span></span> <span data-ttu-id="e787a-146">可以考虑使用 [Azure Redis 缓存][redis]而不是 Azure Cosmos DB 来提供查找服务，这样可以稍微[降低延迟][latency]。</span><span class="sxs-lookup"><span data-stu-id="e787a-146">For slightly [faster latency][latency], consider using [Azure Redis Cache][redis] instead of Azure Cosmos DB to serve lookups.</span></span> <span data-ttu-id="e787a-147">对于高度依赖后端存储中数据的系统，Redis 缓存可以改进其性能。</span><span class="sxs-lookup"><span data-stu-id="e787a-147">Redis Cache can improve performance of systems that rely highly on data in back-end stores.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="e787a-148">可伸缩性注意事项</span><span class="sxs-lookup"><span data-stu-id="e787a-148">Scalability considerations</span></span>

<span data-ttu-id="e787a-149">如果不打算使用 Spark，或者因工作负荷较小而不需要分发，则可考虑使用 [Data Science Virtual Machine][dsvm] (DSVM) 而不是 Azure Databricks。</span><span class="sxs-lookup"><span data-stu-id="e787a-149">If you don't plan to use Spark, or you have a smaller workload where you don't need distribution, consider using [Data Science Virtual Machine][dsvm] (DSVM) instead of Azure Databricks.</span></span> <span data-ttu-id="e787a-150">DSVM 是搭载有机器学习和数据科学深度学习框架和工具的 Azure 虚拟机。</span><span class="sxs-lookup"><span data-stu-id="e787a-150">DSVM is an Azure virtual machine with deep learning frameworks and tools for machine learning and data science.</span></span> <span data-ttu-id="e787a-151">就像使用 Azure Databricks 一样，在 DSVM 中创建的任何模型都可以通过 Azure 机器学习作为 AKS 上的服务来操作。</span><span class="sxs-lookup"><span data-stu-id="e787a-151">As with Azure Databricks, any model you create in a DSVM can be operationalized as a service on AKS via Azure Machine Learning.</span></span>

<span data-ttu-id="e787a-152">在训练期间，请在 Azure Databricks 中预配一个较大的固定大小的 Spark 群集，或者配置[自动缩放][autoscaling]。</span><span class="sxs-lookup"><span data-stu-id="e787a-152">During training, provision a larger fixed-size Spark cluster in Azure Databricks or configure [autoscaling][autoscaling].</span></span> <span data-ttu-id="e787a-153">在启用自动缩放的情况下，Databricks 会监视群集上的负载，并根据需要进行纵向缩放。</span><span class="sxs-lookup"><span data-stu-id="e787a-153">When autoscaling is enabled, Databricks monitors the load on your cluster and scales up and downs when required.</span></span> <span data-ttu-id="e787a-154">如果数据大，而你需要缩短数据准备或建模任务的完成时间，则可预配或横向扩展较大型的群集。</span><span class="sxs-lookup"><span data-stu-id="e787a-154">Provision or scale out a larger cluster if you have a large data size and you want to reduce the amount of time it takes for data preparation or modeling tasks.</span></span>

<span data-ttu-id="e787a-155">根据性能和吞吐量要求缩放 AKS 群集。</span><span class="sxs-lookup"><span data-stu-id="e787a-155">Scale the AKS cluster to meet your performance and throughput requirements.</span></span> <span data-ttu-id="e787a-156">请谨慎地增加 [Pod][scale] 数以充分利用群集，并按服务需求缩放群集的[节点][nodes]。</span><span class="sxs-lookup"><span data-stu-id="e787a-156">Take care to scale up the number of [pods][scale] to fully utilize the cluster, and to scale the [nodes][nodes] of the cluster to meet the demand of your service.</span></span> <span data-ttu-id="e787a-157">若要详细了解如何根据推荐器服务的性能和吞吐量要求来缩放群集，请参阅 [Scaling Azure Container Service Clusters][blog]（缩放 Azure 容器服务群集）。</span><span class="sxs-lookup"><span data-stu-id="e787a-157">For more information on how to scale your cluster to meet the performance and throughput requirements of your recommender service, see [Scaling Azure Container Service Clusters][blog].</span></span>

<span data-ttu-id="e787a-158">若要管理 Azure Cosmos DB 性能，请估算每秒所需的读取数，然后预配所需的 [RU/秒][ru]值（吞吐量）。</span><span class="sxs-lookup"><span data-stu-id="e787a-158">To manage Azure Cosmos DB performance, estimate the number of reads required per second, and provision the number of [RUs per second][ru] (throughput) needed.</span></span> <span data-ttu-id="e787a-159">使用最佳做法进行[分区和水平缩放][partition-data]。</span><span class="sxs-lookup"><span data-stu-id="e787a-159">Use best practices for [partitioning and horizontal scaling][partition-data].</span></span>

## <a name="cost-considerations"></a><span data-ttu-id="e787a-160">成本注意事项</span><span class="sxs-lookup"><span data-stu-id="e787a-160">Cost considerations</span></span>

<span data-ttu-id="e787a-161">此方案中的主要成本动因包括：</span><span class="sxs-lookup"><span data-stu-id="e787a-161">The main drivers of cost in this scenario are:</span></span>

- <span data-ttu-id="e787a-162">训练所需的 Azure Databricks 群集大小。</span><span class="sxs-lookup"><span data-stu-id="e787a-162">The Azure Databricks cluster size required for training.</span></span>
- <span data-ttu-id="e787a-163">满足性能要求所需的 AKS 群集大小。</span><span class="sxs-lookup"><span data-stu-id="e787a-163">The AKS cluster size required to meet your performance requirements.</span></span>
- <span data-ttu-id="e787a-164">根据性能要求预配的 Azure Cosmos DB RU。</span><span class="sxs-lookup"><span data-stu-id="e787a-164">Azure Cosmos DB RUs provisioned to meet your performance requirements.</span></span>

<span data-ttu-id="e787a-165">通过降低重新训练的频率以及在 Spark 群集不使用的情况下将其关闭，对 Azure Databricks 成本进行管理。</span><span class="sxs-lookup"><span data-stu-id="e787a-165">Manage the Azure Databricks costs by retraining less frequently and turning off the Spark cluster when not in use.</span></span> <span data-ttu-id="e787a-166">AKS 和 Azure Cosmos DB 的成本与站点所需的吞吐量和性能相关，并且会根据入站流量上下浮动。</span><span class="sxs-lookup"><span data-stu-id="e787a-166">The AKS and Azure Cosmos DB costs are tied to the throughput and performance required by your site and will scale up and down depending on the volume of traffic to your site.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="e787a-167">部署解决方案</span><span class="sxs-lookup"><span data-stu-id="e787a-167">Deploy the solution</span></span>

<span data-ttu-id="e787a-168">若要部署此体系结构，请按照**Azure Databricks**中的说明[安装程序文档][setup]。</span><span class="sxs-lookup"><span data-stu-id="e787a-168">To deploy this architecture, follow the **Azure Databricks** instructions in the [setup document][setup].</span></span> <span data-ttu-id="e787a-169">简单地说，说明要求你：</span><span class="sxs-lookup"><span data-stu-id="e787a-169">Briefly, the instructions require you to:</span></span>

1. <span data-ttu-id="e787a-170">创建 [Azure Databricks 工作区][workspace]。</span><span class="sxs-lookup"><span data-stu-id="e787a-170">Create an [Azure Databricks workspace][workspace].</span></span>

1. <span data-ttu-id="e787a-171">使用 Azure Databricks 中的以下配置创建新的群集：</span><span class="sxs-lookup"><span data-stu-id="e787a-171">Create a new cluster with the following configuration in Azure Databricks:</span></span>

    - <span data-ttu-id="e787a-172">群集模式：标准</span><span class="sxs-lookup"><span data-stu-id="e787a-172">Cluster mode: Standard</span></span>
    - <span data-ttu-id="e787a-173">Databricks 运行时版本：4.3 （包括 Apache Spark 2.3.1 版 Scala 2.11）</span><span class="sxs-lookup"><span data-stu-id="e787a-173">Databricks Runtime Version: 4.3 (includes Apache Spark 2.3.1, Scala 2.11)</span></span>
    - <span data-ttu-id="e787a-174">Python 版本：3</span><span class="sxs-lookup"><span data-stu-id="e787a-174">Python Version: 3</span></span>
    - <span data-ttu-id="e787a-175">驱动程序类型：Standard\_DS3\_v2</span><span class="sxs-lookup"><span data-stu-id="e787a-175">Driver Type: Standard\_DS3\_v2</span></span>
    - <span data-ttu-id="e787a-176">辅助进程类型：Standard\_DS3\_v2（视需要选择最小值和最大值）</span><span class="sxs-lookup"><span data-stu-id="e787a-176">Worker Type: Standard\_DS3\_v2 (min and max as required)</span></span>
    - <span data-ttu-id="e787a-177">自动终止：（视需要进行选择）</span><span class="sxs-lookup"><span data-stu-id="e787a-177">Auto Termination: (as required)</span></span>
    - <span data-ttu-id="e787a-178">Spark 配置：（视需要进行选择）</span><span class="sxs-lookup"><span data-stu-id="e787a-178">Spark Config: (as required)</span></span>
    - <span data-ttu-id="e787a-179">环境变量：（视需要进行选择）</span><span class="sxs-lookup"><span data-stu-id="e787a-179">Environment Variables: (as required)</span></span>

1. <span data-ttu-id="e787a-180">创建个人访问令牌内的[Azure Databricks 工作区][workspace]。</span><span class="sxs-lookup"><span data-stu-id="e787a-180">Create a personal access token within the [Azure Databricks workspace][workspace].</span></span> <span data-ttu-id="e787a-181">请参阅 Azure Databricks 身份验证[文档][ adbauthentication]有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="e787a-181">See the Azure Databricks authentication [documentation][adbauthentication] for details.</span></span>

1. <span data-ttu-id="e787a-182">克隆[Microsoft 建议者][ github]存储库到环境下都无法执行脚本 （例如在本地计算机）。</span><span class="sxs-lookup"><span data-stu-id="e787a-182">Clone the [Microsoft Recommenders][github] repository into an environment where you can execute scripts (e.g. your local computer).</span></span>

1. <span data-ttu-id="e787a-183">请按照**快速安装**设置为说明[安装相关的库][ setup] Azure Databricks 上。</span><span class="sxs-lookup"><span data-stu-id="e787a-183">Follow the **Quick install** setup instructions to [install the relevant libraries][setup] on Azure Databricks.</span></span>

1. <span data-ttu-id="e787a-184">请按照**快速安装**设置为说明[准备操作化的 Azure Databricks][setupo16n]。</span><span class="sxs-lookup"><span data-stu-id="e787a-184">Follow the **Quick install** setup instructions to [prepare Azure Databricks for operationalization][setupo16n].</span></span>

1. <span data-ttu-id="e787a-185">导入[ALS 电影操作化 notebook] [ als-example]到你的工作区。</span><span class="sxs-lookup"><span data-stu-id="e787a-185">Import the [ALS Movie Operationalization notebook][als-example] into your workspace.</span></span> <span data-ttu-id="e787a-186">登录到 Azure Databricks 工作区后，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="e787a-186">After logging into your Azure Databricks Workspace, do the following:</span></span>

    <span data-ttu-id="e787a-187">a.</span><span class="sxs-lookup"><span data-stu-id="e787a-187">a.</span></span> <span data-ttu-id="e787a-188">单击**主页**在工作区的左侧。</span><span class="sxs-lookup"><span data-stu-id="e787a-188">Click **Home** on the left side of the workspace.</span></span>

    <span data-ttu-id="e787a-189">b.</span><span class="sxs-lookup"><span data-stu-id="e787a-189">b.</span></span> <span data-ttu-id="e787a-190">右键单击主目录中的空白区域。</span><span class="sxs-lookup"><span data-stu-id="e787a-190">Right-click on white space in your home directory.</span></span> <span data-ttu-id="e787a-191">选择“导入”。</span><span class="sxs-lookup"><span data-stu-id="e787a-191">Select **Import**.</span></span>

    <span data-ttu-id="e787a-192">c.</span><span class="sxs-lookup"><span data-stu-id="e787a-192">c.</span></span> <span data-ttu-id="e787a-193">选择**URL**，并粘贴到文本字段的以下： `https://github.com/Microsoft/Recommenders/blob/master/notebooks/05_operationalize/als_movie_o16n.ipynb`</span><span class="sxs-lookup"><span data-stu-id="e787a-193">Select **URL**, and paste the following into the text field: `https://github.com/Microsoft/Recommenders/blob/master/notebooks/05_operationalize/als_movie_o16n.ipynb`</span></span>

    <span data-ttu-id="e787a-194">d.</span><span class="sxs-lookup"><span data-stu-id="e787a-194">d.</span></span> <span data-ttu-id="e787a-195">单击“导入”。</span><span class="sxs-lookup"><span data-stu-id="e787a-195">Click **Import**.</span></span>

1. <span data-ttu-id="e787a-196">打开在 Azure Databricks notebook，并附加配置的群集。</span><span class="sxs-lookup"><span data-stu-id="e787a-196">Open the notebook within Azure Databricks and attach the configured cluster.</span></span>

1. <span data-ttu-id="e787a-197">运行 notebook 创建创建建议 API 所需的 Azure 资源提供的、 给定用户的前 10 个电影建议。</span><span class="sxs-lookup"><span data-stu-id="e787a-197">Run the notebook to create the Azure resources required to create a recommendation API that provides the top-10 movie recommendations for a given user.</span></span>

## <a name="related-architectures"></a><span data-ttu-id="e787a-198">相关体系结构</span><span class="sxs-lookup"><span data-stu-id="e787a-198">Related architectures</span></span>

<span data-ttu-id="e787a-199">我们还构建了使用 Spark 和 Azure Databricks 执行计划[批处理计分流程][batch-scoring]的参考体系结构。</span><span class="sxs-lookup"><span data-stu-id="e787a-199">We have also built a reference architecture that uses Spark and Azure Databricks to execute scheduled [batch-scoring processes][batch-scoring].</span></span> <span data-ttu-id="e787a-200">请参阅该参考体系结构，以了解推荐用于定期生成新建议的推荐方法。</span><span class="sxs-lookup"><span data-stu-id="e787a-200">See that reference architecture to understand a recommended approach for generating new recommendations routinely.</span></span>

<!-- links -->
[aci]: /azure/container-instances/container-instances-overview
[aad]: /azure/active-directory-b2c/active-directory-b2c-overview
[adbauthentication]: https://docs.azuredatabricks.net/api/latest/authentication.html#generate-a-token
[aks]: /azure/aks/intro-kubernetes
[als]: https://spark.apache.org/docs/latest/ml-collaborative-filtering.html
[als-example]: https://github.com/Microsoft/Recommenders/blob/master/notebooks/05_operationalize/als_movie_o16n.ipynb
[autoscaling]: https://docs.azuredatabricks.net/user-guide/clusters/sizing.html
[autoscale]: https://docs.azuredatabricks.net/user-guide/clusters/sizing.html#autoscaling
[availability]: /azure/architecture/checklist/availability
[batch-scoring]: /azure/architecture/reference-architectures/ai/batch-scoring-databricks
[blob]: /azure/storage/blobs/storage-blobs-introduction
[blog]: https://blogs.technet.microsoft.com/machinelearning/2018/03/20/scaling-azure-container-service-cluster/
[clusters]: https://docs.azuredatabricks.net/user-guide/clusters/configure.html
[cosmosdb]: /azure/cosmos-db/introduction
[data-source]: https://docs.azuredatabricks.net/spark/latest/data-sources/index.html
[databricks]: /azure/azure-databricks/what-is-azure-databricks
[dsvm]: /azure/machine-learning/data-science-virtual-machine/overview
[dsvm-ubuntu]: /azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro
[eval-guide]: https://github.com/Microsoft/Recommenders/blob/master/notebooks/03_evaluate/evaluation.ipynb
[free]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[github]: https://github.com/Microsoft/Recommenders
[guide]: https://github.com/Microsoft/Recommenders/blob/master/notebooks/01_prepare_data/data_split.ipynb
[latency]: https://github.com/jessebenson/azure-performance
[mls]: /azure/machine-learning/service/
[n-tier]: /azure/architecture/reference-architectures/n-tier/n-tier-cassandra
[ndcg]: https://en.wikipedia.org/wiki/Discounted_cumulative_gain
[nodes]: /azure/aks/scale-cluster
[notebook]: https://github.com/Microsoft/Recommenders/notebooks/00_quick_start/als_pyspark_movielens.ipynb
[partition-data]: /azure/cosmos-db/partition-data
[redis]: /azure/redis-cache/cache-overview
[regions]: https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all
[resiliency]: /azure/architecture/resiliency/
[ru]: /azure/cosmos-db/request-units
[sec-docs]: /azure/security/
[setup]: https://github.com/Microsoft/Recommenders/blob/master/SETUP.md#repository-installation
[setupo16n]: https://github.com/Microsoft/Recommenders/blob/master/SETUP.md#prepare-azure-databricks-for-operationalization
[scale]: /azure/aks/tutorial-kubernetes-scale
[sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/
[vm-size]: /azure/virtual-machines/virtual-machines-linux-change-vm-size
[workspace]: https://docs.azuredatabricks.net/getting-started/index.html
