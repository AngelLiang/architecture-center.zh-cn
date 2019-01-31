---
title: Azure 上的电影推荐
description: 使用机器学习和 Azure Data Science Virtual Machines (DSVM) 在 Azure 上训练一个模型，以通过机器学习自动生成电影、产品和其他方面的推荐。
author: njray
ms.date: 1/9/2019
ms.custom: azcat-ai
social_image_url: /azure/architecture/example-scenario/ai/media/architecture-movie-recommender.png
ms.openlocfilehash: 9e68f38cb61d7a3255b76a662c58907704914052
ms.sourcegitcommit: 3b15d65e7c35a19506e562c444343f8467b6a073
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2019
ms.locfileid: "54908250"
---
# <a name="movie-recommendations-on-azure"></a>Azure 上的电影推荐

本示例方案演示某家企业如何使用机器学习来自动为其客户生成产品推荐。 将使用 Azure Data Science Virtual Machine (DSVM) 在 Azure 上训练某个模型，以便根据电影的给定评分，向用户推荐电影。

在、零售、新闻、媒体等众多行业中，推荐功能非常有用。 潜在的应用场景包括，在虚拟商店中推荐产品、推荐新闻或发布内容，或者推荐音乐。 在传统上，企业必须招聘并培训助理，以便向客户提供个性化的推荐。 现在，我们可以利用 Azure 来训练模型以了解客户的偏好，并借此提供大规模的个性化推荐。

## <a name="relevant-use-cases"></a>相关用例

以下用例可以考虑本方案：

* 在网站上推荐电影。
* 在移动应用中推荐消费产品。
* 在流媒体中推荐新闻。

## <a name="architecture"></a>体系结构

![用于训练影片建议的机器学习模型的体系结构][architecture]

此方案对电影评分数据集使用 Spark [交替最小二乘法][als] (ALS) 算法来训练和评估机器学习模型。 此方案的步骤如下：

1. 前端网站或应用服务收集用户与电影之间的历史交互数据，这些数据在用户、项与数字评分元组的表中表示。

2. 收集的历史数据存储在 Blob 存储中。

3. 通常使用 DSVM 来试验 Spark ALS 推荐器模型或将其产品化。 使用训练数据集训练 ALS 模型。该数据集是在其中应用相应的数据拆分策略后生成的。 例如，可根据业务要求，随机、按时间顺序或以分层方式将该数据集拆分为多个集。 类似于其他机器学习任务，可以使用计算指标验证（例如，精度 \@*k*、召回率 \@*k*、[MAP][map]、[nDCG\@k][ndcg]）验证推荐器。

4. Azure 机器学习服务用于协调试验，例如超参数扫描和模型管理。

5. 训练后的模型保留在 Azure Cosmos DB 中，然后，可以应用该模型向给定的用户推荐排名靠前的 *k* 部电影。

6. 然后，使用 Azure 容器实例或 Azure Kubernetes 服务将该模型部署到 Web 应用或应用服务。

有关生成和缩放推荐器服务的深入指导，请参阅[在 Azure 上生成实时推荐 API][ref-arch]。

### <a name="components"></a>组件

* [Data Science Virtual Machine][dsvm] (DSVM) 是一种 Azure 虚拟机，其中的深度学习框架和工具可用于机器学习和数据科学。 DSVM 具有一个可用于运行 ALS 的独立 Spark 环境。

* [Azure Blob 存储][blob]存储电影推荐结果的数据集。

* [Azure 机器学习服务][mls]用于加速机器学习模型的生成、管理和部署。

* [Azure Cosmos DB][cosmosdb] 实现全局分布式的多模型数据库存储。

* [Azure 容器实例][aci]用于将训练的模型部署到 Web 或应用服务（可以选择性地使用[Azure Kubernetes 服务][aks]）。

### <a name="alternatives"></a>备选项

[Azure Databricks][databricks]是执行模型训练和评估的 Spark 托管群集。 可在几分钟内设置好一个 Spark 托管环境，并可[自动缩放][autoscale]（扩展和缩减），以帮助减少手动缩放群集所需的资源和成本。 另一种节省资源的做法是将非活动[群集][clusters]配置为自动终止。

## <a name="considerations"></a>注意事项

### <a name="availability"></a>可用性

机器学习生成的应用拆分为两个资源组件：用于训练的资源、用于提供服务的资源。 用于训练的资源通常不需要高可用性，因为实时生产请求不直接访问这些资源。 用于提供服务的资源需要高可用性，以便能够为客户请求提供服务。

全球[多个区域][regions]都已推出了用于训练的、符合虚拟机[服务级别协议][sla] (SLA) 的 DSVM。 Azure Kubernetes 服务提供[高度可用][ha]基础结构用于提供服务。 代理节点也遵守虚拟机的 [SLA][sla-aks]。

### <a name="scalability"></a>可伸缩性

对于大型数据，可以缩放 DSVM 以缩短训练时间。 可以通过[更改 VM 大小][vm-size] 来扩展或缩减 VM。 选择足够大的内存来适应内存中的数据集和更高的 vCPU 计数，以减少训练所需的时间。

### <a name="security"></a>安全

此方案可以使用 Azure Active Directory 对[访问 DSVM][dsvm-id]（包含代码、模型和（内存中）数据）的用户进行身份验证。 数据先存储在 Azure 存储中，然后再加载到 DSVM。在 DSVM 上，使用[存储服务加密][storage-security]自动将数据加密。 可以通过 Azure Active Directory 身份验证或基于角色的访问控制来管理权限。

## <a name="deploy-this-scenario"></a>部署此方案

**先决条件**：必须已经有 Azure 帐户。 如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户][free]。

[Microsoft 推荐器存储库][github]中提供了此方案的所有代码。

请遵循以下步骤运行 [ALS 快速入门 Notebook][notebook]：

1. 从 Azure 门户[创建 DSVM][dsvm-ubuntu]。

2. 克隆 Notebooks 文件夹中的存储库：

    ```shell
    cd notebooks
    git clone https://github.com/Microsoft/Recommenders
    ```

3. 遵循 [SETUP.md][setup] 文件中所述的步骤安装 conda 依赖项。

4. 在浏览器中，转到 jupyterlab VM 并导航到 `notebooks/00_quick_start/als_pyspark_movielens.ipynb`。

5. 执行 Notebook。

## <a name="related-resources"></a>相关资源

有关生成和缩放推荐器服务的深入指导，请参阅[在 Azure 上生成实时推荐 API][ref-arch]。 有关推荐系统的教程和示例，请参阅 [Microsoft 推荐器存储库][github]。

[architecture]: ./media/architecture-movie-recommender.png
[aci]: /azure/container-instances/container-instances-overview
[aad]: /azure/active-directory-b2c/active-directory-b2c-overview
[aks]: /azure/aks/intro-kubernetes
[als]: https://spark.apache.org/docs/latest/ml-collaborative-filtering.html
[autoscale]: https://docs.azuredatabricks.net/user-guide/clusters/sizing.html#autoscaling
[blob]: /azure/storage/blobs/storage-blobs-introduction
[clusters]: https://docs.azuredatabricks.net/user-guide/clusters/configure.html
[cosmosdb]: /azure/cosmos-db/introduction
[databricks]: /azure/azure-databricks/what-is-azure-databricks
[dsvm]: /azure/machine-learning/data-science-virtual-machine/overview
[dsvm-id]: /azure/machine-learning/data-science-virtual-machine/dsvm-common-identity
[dsvm-ubuntu]: /azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro
[free]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[github]: https://github.com/Microsoft/Recommenders
[ha]: /azure/aks/container-service-quotas
[map]: https://en.wikipedia.org/wiki/Evaluation_measures_(information_retrieval)
[mls]: /azure/machine-learning/service/
[n-tier]: /azure/architecture/reference-architectures/n-tier/n-tier-cassandra
[ndcg]: https://en.wikipedia.org/wiki/Discounted_cumulative_gain
[notebook]: https://github.com/Microsoft/Recommenders/notebooks/00_quick_start/als_pyspark_movielens.ipynb
[ref-arch]: /azure/architecture/reference-architectures/ai/real-time-recommendation
[regions]: https://azure.microsoft.com/en-us/global-infrastructure/services/?products=virtual-machines&regions=all
[resiliency]: /azure/architecture/resiliency/
[sec-docs]: /azure/security/
[setup]: https://github.com/Microsoft/Recommenders/blob/master/SETUP.md%60
[sla]: https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines/v1_8/
[sla-aks]: https://azure.microsoft.com/en-us/support/legal/sla/kubernetes-service/v1_0/
[storage-security]: /azure/storage/common/storage-service-encryption
[vm-size]: /azure/virtual-machines/virtual-machines-linux-change-vm-size
