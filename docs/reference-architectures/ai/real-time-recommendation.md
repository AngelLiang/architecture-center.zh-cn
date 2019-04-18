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
# <a name="build-a-real-time-recommendation-api-on-azure"></a>在 Azure 上生成实时建议 API

本参考体系结构演示如何使用 Azure Databricks 训练一个建议模型，并使用 Azure Cosmos DB、Azure 机器学习和 Azure Kubernetes 服务 (AKS) 将其作为 API 部署。 本体系结构可以通用于大多数建议引擎方案，包括针对产品、影片和新闻的建议。

[GitHub][als-example] 中提供了本体系结构的参考实现。

![用于训练影片建议的机器学习模型的体系结构](./_images/recommenders-architecture.png)

**场景**：一家媒体组织希望向其用户提供影片或视频建议。 通过提供个性化建议，组织可以满足多个业务目标，包括提高点击率、提高站点参与度以及提高用户满意度。

本参考体系结构用于训练和部署实时推荐器服务 API，以便为给定用户提供排名前 10 的影片的建议。

此建议模型的数据流如下：

1. 跟踪用户行为。 例如，后端服务可能会记录用户何时对影片进行了评分或单击了某个产品或某篇新闻文章。

2. 从可用的[数据源][data-source]将数据加载到 Azure Databricks 中。

3. 准备数据并将其拆分为训练集和测试集，以便训练模型。 （[此指南][guide]介绍拆分数据的选项。）

4. 根据数据对 [Spark 协作筛选][als]模型进行拟合。

5. 使用评分和排名指标对模型质量进行评估。 （[此指南][eval-guide]详细介绍了可以用来评估推荐器的指标。）

6. 按用户预先计算排名前 10 的建议，并在 Azure Cosmos DB 中将其作为缓存存储。

7. 使用 Azure 机器学习 API 将 API 服务部署到 AKS，以便容器化和部署 API。

8. 当后端服务从用户获取请求时，请调用托管在 AKS 中的建议 API，以便获取排名前 10 的建议并将其显示给用户。

## <a name="architecture"></a>体系结构

该体系结构包括以下组件：

[Azure Databricks][databricks]。 Databricks 是一个开发环境，用于在 Spark 群集上准备输入数据并训练推荐器模型。 Azure Databricks 还提供一个交互式工作区，用于运行笔记本并在其上进行协作，以便执行数据处理或机器学习任务。

[Azure Kubernetes 服务][aks] (AKS)。 AKS 用于在 Kubernetes 群集上部署机器学习模型服务 API 并使之可操作。 AKS 托管容器化模型，提供符合吞吐量要求的可伸缩性，同时提供标识和访问管理以及日志记录和运行状况监视功能。

[Azure Cosmos DB][cosmosdb]。 Cosmos DB 是一种全球分布式数据库服务，用于为每位用户存储排名前 10 的推荐影片。 Azure Cosmos DB 特别适合本方案，因为它的延迟低（99% 的情况下为 10 毫秒），可以为给定用户读取排名靠前的建议项。

[Azure 机器学习服务][mls]。 此服务用于跟踪和管理机器学习模型，然后将这些模型打包并部署到可缩放的 AKS 环境。

[Microsoft 推荐器][github]。 此开源存储库包含实用程序代码和示例，有助于用户完成推荐器系统的生成、评估和可操作方面的入门。

## <a name="performance-considerations"></a>性能注意事项

性能是实时建议的主要考量，因为建议通常落在用户在站点上发出的请求的关键路径中。

将 AKS 和 Azure Cosmos DB 组合在一起以后，此体系结构就可以为中型工作负荷提供建议，其开销极小。 在进行 200 个并发用户的负载测试时，此体系结构可以在中等延迟（大约 60 毫秒）情况下提供建议，表现出的吞吐量为每秒 180 个请求。 负载测试是针对默认部署配置（一个 3x D3 v2 AKS 群集，包含 12 个 vCPU、42 GB 内存、11,000 [请求单元 (RU)/秒][ru]，针对 Azure Cosmos DB 进行预配）运行的。

![性能图](./_images/recommenders-performance.png)

![吞吐量图](./_images/recommenders-throughput.png)

建议使用 Azure Cosmos DB 是因为它的统包式全球分发功能，以及它可以用来满足应用的任何数据库要求。 可以考虑使用 [Azure Redis 缓存][redis]而不是 Azure Cosmos DB 来提供查找服务，这样可以稍微[降低延迟][latency]。 对于高度依赖后端存储中数据的系统，Redis 缓存可以改进其性能。

## <a name="scalability-considerations"></a>可伸缩性注意事项

如果不打算使用 Spark，或者因工作负荷较小而不需要分发，则可考虑使用 [Data Science Virtual Machine][dsvm] (DSVM) 而不是 Azure Databricks。 DSVM 是搭载有机器学习和数据科学深度学习框架和工具的 Azure 虚拟机。 就像使用 Azure Databricks 一样，在 DSVM 中创建的任何模型都可以通过 Azure 机器学习作为 AKS 上的服务来操作。

在训练期间，请在 Azure Databricks 中预配一个较大的固定大小的 Spark 群集，或者配置[自动缩放][autoscaling]。 在启用自动缩放的情况下，Databricks 会监视群集上的负载，并根据需要进行纵向缩放。 如果数据大，而你需要缩短数据准备或建模任务的完成时间，则可预配或横向扩展较大型的群集。

根据性能和吞吐量要求缩放 AKS 群集。 请谨慎地增加 [Pod][scale] 数以充分利用群集，并按服务需求缩放群集的[节点][nodes]。 若要详细了解如何根据推荐器服务的性能和吞吐量要求来缩放群集，请参阅 [Scaling Azure Container Service Clusters][blog]（缩放 Azure 容器服务群集）。

若要管理 Azure Cosmos DB 性能，请估算每秒所需的读取数，然后预配所需的 [RU/秒][ru]值（吞吐量）。 使用最佳做法进行[分区和水平缩放][partition-data]。

## <a name="cost-considerations"></a>成本注意事项

此方案中的主要成本动因包括：

- 训练所需的 Azure Databricks 群集大小。
- 满足性能要求所需的 AKS 群集大小。
- 根据性能要求预配的 Azure Cosmos DB RU。

通过降低重新训练的频率以及在 Spark 群集不使用的情况下将其关闭，对 Azure Databricks 成本进行管理。 AKS 和 Azure Cosmos DB 的成本与站点所需的吞吐量和性能相关，并且会根据入站流量上下浮动。

## <a name="deploy-the-solution"></a>部署解决方案

若要部署此体系结构，请按照**Azure Databricks**中的说明[安装程序文档][setup]。 简单地说，说明要求你：

1. 创建 [Azure Databricks 工作区][workspace]。

1. 使用 Azure Databricks 中的以下配置创建新的群集：

    - 群集模式：标准
    - Databricks 运行时版本：4.3 （包括 Apache Spark 2.3.1 版 Scala 2.11）
    - Python 版本：3
    - 驱动程序类型：Standard\_DS3\_v2
    - 辅助进程类型：Standard\_DS3\_v2（视需要选择最小值和最大值）
    - 自动终止：（视需要进行选择）
    - Spark 配置：（视需要进行选择）
    - 环境变量：（视需要进行选择）

1. 创建个人访问令牌内的[Azure Databricks 工作区][workspace]。 请参阅 Azure Databricks 身份验证[文档][ adbauthentication]有关详细信息。

1. 克隆[Microsoft 建议者][ github]存储库到环境下都无法执行脚本 （例如在本地计算机）。

1. 请按照**快速安装**设置为说明[安装相关的库][ setup] Azure Databricks 上。

1. 请按照**快速安装**设置为说明[准备操作化的 Azure Databricks][setupo16n]。

1. 导入[ALS 电影操作化 notebook] [ als-example]到你的工作区。 登录到 Azure Databricks 工作区后，请执行以下操作：

    a. 单击**主页**在工作区的左侧。

    b. 右键单击主目录中的空白区域。 选择“导入”。

    c. 选择**URL**，并粘贴到文本字段的以下： `https://github.com/Microsoft/Recommenders/blob/master/notebooks/05_operationalize/als_movie_o16n.ipynb`

    d. 单击“导入”。

1. 打开在 Azure Databricks notebook，并附加配置的群集。

1. 运行 notebook 创建创建建议 API 所需的 Azure 资源提供的、 给定用户的前 10 个电影建议。

## <a name="related-architectures"></a>相关体系结构

我们还构建了使用 Spark 和 Azure Databricks 执行计划[批处理计分流程][batch-scoring]的参考体系结构。 请参阅该参考体系结构，以了解推荐用于定期生成新建议的推荐方法。

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
