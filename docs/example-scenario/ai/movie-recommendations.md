---
title: Azure 上的电影推荐
description: 使用机器学习和 Azure Data Science Virtual Machines (DSVM) 在 Azure 上训练一个模型，以通过机器学习自动生成电影、产品和其他方面的推荐。
author: njray
ms.date: 1/9/2019
ms.custom: azcat-ai
ms.openlocfilehash: 38e883bac032596d4c14b230fa3aa102fde45837
ms.sourcegitcommit: d5ea427c25f9f7799cc859b99f328739ca2d8c1c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2019
ms.locfileid: "54307741"
---
# <a name="movie-recommendations-on-azure"></a><span data-ttu-id="f0900-103">Azure 上的电影推荐</span><span class="sxs-lookup"><span data-stu-id="f0900-103">Movie recommendations on Azure</span></span>

<span data-ttu-id="f0900-104">本示例方案演示某家企业如何使用机器学习来自动为其客户生成产品推荐。</span><span class="sxs-lookup"><span data-stu-id="f0900-104">This example scenario shows how a business can use machine learning to automate product recommendations for their customers.</span></span> <span data-ttu-id="f0900-105">将使用 Azure Data Science Virtual Machine (DSVM) 在 Azure 上训练某个模型，以便根据电影的给定评分，向用户推荐电影。</span><span class="sxs-lookup"><span data-stu-id="f0900-105">An Azure Data Science Virtual Machine (DSVM) is used to train a model on Azure that recommends movies to users based on ratings that have been given to movies.</span></span>

<span data-ttu-id="f0900-106">在、零售、新闻、媒体等众多行业中，推荐功能非常有用。</span><span class="sxs-lookup"><span data-stu-id="f0900-106">Recommendations can be useful in various industries from retail to news to media.</span></span> <span data-ttu-id="f0900-107">潜在的应用场景包括，在虚拟商店中推荐产品、推荐新闻或发布内容，或者推荐音乐。</span><span class="sxs-lookup"><span data-stu-id="f0900-107">Potential applications include providing  product recommendations in a virtual store, providing news or post recommendations, or providing music recommendations.</span></span> <span data-ttu-id="f0900-108">在传统上，企业必须招聘并培训助理，以便向客户提供个性化的推荐。</span><span class="sxs-lookup"><span data-stu-id="f0900-108">Traditionally, businesses had to hire and train assistants to make personalized recommendations to customers.</span></span> <span data-ttu-id="f0900-109">现在，我们可以利用 Azure 来训练模型以了解客户的偏好，并借此提供大规模的个性化推荐。</span><span class="sxs-lookup"><span data-stu-id="f0900-109">Today, we can  provide customized recommendations at scale by utilizing Azure to train models to understand customer preferences.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="f0900-110">相关用例</span><span class="sxs-lookup"><span data-stu-id="f0900-110">Relevant use cases</span></span>

<span data-ttu-id="f0900-111">以下用例可以考虑本方案：</span><span class="sxs-lookup"><span data-stu-id="f0900-111">Consider this scenario for the following use cases:</span></span>

* <span data-ttu-id="f0900-112">在网站上推荐电影。</span><span class="sxs-lookup"><span data-stu-id="f0900-112">Movie recommendations on a website.</span></span>
* <span data-ttu-id="f0900-113">在移动应用中推荐消费产品。</span><span class="sxs-lookup"><span data-stu-id="f0900-113">Consumer product recommendations in a mobile app.</span></span>
* <span data-ttu-id="f0900-114">在流媒体中推荐新闻。</span><span class="sxs-lookup"><span data-stu-id="f0900-114">News recommendations on streaming media.</span></span>

## <a name="architecture"></a><span data-ttu-id="f0900-115">体系结构</span><span class="sxs-lookup"><span data-stu-id="f0900-115">Architecture</span></span>

![用于训练影片建议的机器学习模型的体系结构][architecture]

<span data-ttu-id="f0900-117">此方案对电影评分数据集使用 Spark [交替最小二乘法][als] (ALS) 算法来训练和评估机器学习模型。</span><span class="sxs-lookup"><span data-stu-id="f0900-117">This scenario covers the training and evaluating of the machine learning model using the Spark [alternating least squares][als] (ALS) algorithm on a dataset of movie ratings.</span></span> <span data-ttu-id="f0900-118">此方案的步骤如下：</span><span class="sxs-lookup"><span data-stu-id="f0900-118">The steps for this scenario are as following:</span></span>

1. <span data-ttu-id="f0900-119">前端网站或应用服务收集用户与电影之间的历史交互数据，这些数据在用户、项与数字评分元组的表中表示。</span><span class="sxs-lookup"><span data-stu-id="f0900-119">The front-end website or app service collects historical data of user-movie interactions, which are represented in a table of user, item, and numerical rating tuples.</span></span>

2. <span data-ttu-id="f0900-120">收集的历史数据存储在 Blob 存储中。</span><span class="sxs-lookup"><span data-stu-id="f0900-120">The collected historical data is stored in a blob storage.</span></span>

3. <span data-ttu-id="f0900-121">通常使用 DSVM 来试验 Spark ALS 推荐器模型或将其产品化。</span><span class="sxs-lookup"><span data-stu-id="f0900-121">A DSVM is often used to experiment with or productize a Spark ALS recommender model.</span></span> <span data-ttu-id="f0900-122">使用训练数据集训练 ALS 模型。该数据集是在其中应用相应的数据拆分策略后生成的。</span><span class="sxs-lookup"><span data-stu-id="f0900-122">The ALS model is trained using a training dataset, which is produced from the overall dataset by applying the appropriate data splitting strategy.</span></span> <span data-ttu-id="f0900-123">例如，可根据业务要求，随机、按时间顺序或以分层方式将该数据集拆分为多个集。</span><span class="sxs-lookup"><span data-stu-id="f0900-123">For example, the dataset can be split into sets randomly, chronologically, or stratified, depending on the business requirement.</span></span> <span data-ttu-id="f0900-124">类似于其他机器学习任务，可以使用计算指标验证（例如，精度 \@*k*、召回率 \@*k*、[MAP][map]、[nDCG\@k][ndcg]）验证推荐器。</span><span class="sxs-lookup"><span data-stu-id="f0900-124">Similar to other machine learning tasks, a recommender is validated by using evaluation metrics (for example, precision\@*k*, recall\@*k*, [MAP][map], [nDCG\@k][ndcg]).</span></span>

4. <span data-ttu-id="f0900-125">Azure 机器学习服务用于协调试验，例如超参数扫描和模型管理。</span><span class="sxs-lookup"><span data-stu-id="f0900-125">Azure Machine Learning service is used for coordinating the experimentation, such as hyperparameter sweeping and model management.</span></span>

5. <span data-ttu-id="f0900-126">训练后的模型保留在 Azure Cosmos DB 中，然后，可以应用该模型向给定的用户推荐排名靠前的 *k* 部电影。</span><span class="sxs-lookup"><span data-stu-id="f0900-126">A trained model is preserved on Azure Cosmos DB, which can then be applied for recommending the top *k* movies for a given user.</span></span>

6. <span data-ttu-id="f0900-127">然后，使用 Azure 容器实例或 Azure Kubernetes 服务将该模型部署到 Web 应用或应用服务。</span><span class="sxs-lookup"><span data-stu-id="f0900-127">The model is then deployed onto a web or app service by using Azure Container Instances or Azure Kubernetes Service.</span></span>

<span data-ttu-id="f0900-128">有关生成和缩放推荐器服务的深入指导，请参阅[在 Azure 上生成实时推荐 API][ref-arch]。</span><span class="sxs-lookup"><span data-stu-id="f0900-128">For an in-depth guide to building and scaling a recommender service, see [Build a real-time recommendation API on Azure][ref-arch].</span></span>

### <a name="components"></a><span data-ttu-id="f0900-129">组件</span><span class="sxs-lookup"><span data-stu-id="f0900-129">Components</span></span>

* <span data-ttu-id="f0900-130">[Data Science Virtual Machine][dsvm] (DSVM) 是一种 Azure 虚拟机，其中的深度学习框架和工具可用于机器学习和数据科学。</span><span class="sxs-lookup"><span data-stu-id="f0900-130">[Data Science Virtual Machine][dsvm] (DSVM) is an Azure virtual machine with deep learning frameworks and tools for machine learning and data science.</span></span> <span data-ttu-id="f0900-131">DSVM 具有一个可用于运行 ALS 的独立 Spark 环境。</span><span class="sxs-lookup"><span data-stu-id="f0900-131">The DSVM has a standalone Spark environment that can be used to run ALS.</span></span>

* <span data-ttu-id="f0900-132">[Azure Blob 存储][blob]存储电影推荐结果的数据集。</span><span class="sxs-lookup"><span data-stu-id="f0900-132">[Azure Blob storage][blob] stores the dataset for movie recommendations.</span></span>

* <span data-ttu-id="f0900-133">[Azure 机器学习服务][mls]用于加速机器学习模型的生成、管理和部署。</span><span class="sxs-lookup"><span data-stu-id="f0900-133">[Azure Machine Learning service][mls] is used to accelerate the building, managing, and deploying of machine learning models.</span></span>

* <span data-ttu-id="f0900-134">[Azure Cosmos DB][cosmosdb] 实现全局分布式的多模型数据库存储。</span><span class="sxs-lookup"><span data-stu-id="f0900-134">[Azure Cosmos DB][cosmosdb] enables globally distributed and multi-model database storage.</span></span>

* <span data-ttu-id="f0900-135">[Azure 容器实例][aci]用于将训练的模型部署到 Web 或应用服务（可以选择性地使用[Azure Kubernetes 服务][aks]）。</span><span class="sxs-lookup"><span data-stu-id="f0900-135">[Azure Container Instances][aci] is used to deploy the trained models to web or app services, optionally using [Azure Kubernetes Service][aks].</span></span>

### <a name="alternatives"></a><span data-ttu-id="f0900-136">备选项</span><span class="sxs-lookup"><span data-stu-id="f0900-136">Alternatives</span></span>

<span data-ttu-id="f0900-137">[Azure Databricks][databricks]是执行模型训练和评估的 Spark 托管群集。</span><span class="sxs-lookup"><span data-stu-id="f0900-137">[Azure Databricks][databricks] is a managed Spark cluster where model training and evaluating is performed.</span></span> <span data-ttu-id="f0900-138">可在几分钟内设置好一个 Spark 托管环境，并可[自动缩放][autoscale]（扩展和缩减），以帮助减少手动缩放群集所需的资源和成本。</span><span class="sxs-lookup"><span data-stu-id="f0900-138">You can set up a managed Spark environment in minutes, and [autoscale][autoscale] up and down to help reduce the resources and costs associated with scaling clusters manually.</span></span> <span data-ttu-id="f0900-139">另一种节省资源的做法是将非活动[群集][clusters]配置为自动终止。</span><span class="sxs-lookup"><span data-stu-id="f0900-139">Another resource-saving option is to configure inactive [clusters][clusters] to terminate automatically.</span></span>

## <a name="considerations"></a><span data-ttu-id="f0900-140">注意事项</span><span class="sxs-lookup"><span data-stu-id="f0900-140">Considerations</span></span>

### <a name="availability"></a><span data-ttu-id="f0900-141">可用性</span><span class="sxs-lookup"><span data-stu-id="f0900-141">Availability</span></span>

<span data-ttu-id="f0900-142">机器学习生成的应用拆分为两个资源组件：用于训练的资源、用于提供服务的资源。</span><span class="sxs-lookup"><span data-stu-id="f0900-142">Machine-learning-built apps are split into two resource components: resources for training, and resources for serving.</span></span> <span data-ttu-id="f0900-143">用于训练的资源通常不需要高可用性，因为实时生产请求不直接访问这些资源。</span><span class="sxs-lookup"><span data-stu-id="f0900-143">Resources required for training generally do not need  high availability, as live production requests do not directly hit these resources.</span></span> <span data-ttu-id="f0900-144">用于提供服务的资源需要高可用性，以便能够为客户请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="f0900-144">Resources required for serving need to have high availability to serve customer requests.</span></span>

<span data-ttu-id="f0900-145">全球[多个区域][regions]都已推出了用于训练的、符合虚拟机[服务级别协议][sla] (SLA) 的 DSVM。</span><span class="sxs-lookup"><span data-stu-id="f0900-145">For training, the DSVM is available in [multiple regions][regions] around the globe and meets the [service level agreement][sla] (SLA) for virtual machines.</span></span> <span data-ttu-id="f0900-146">Azure Kubernetes 服务提供[高度可用][ha]基础结构用于提供服务。</span><span class="sxs-lookup"><span data-stu-id="f0900-146">For serving, Azure Kubernetes Service provides a [highly available][ha] infrastructure.</span></span> <span data-ttu-id="f0900-147">代理节点也遵守虚拟机的 [SLA][sla-aks]。</span><span class="sxs-lookup"><span data-stu-id="f0900-147">Agent nodes also follow the [SLA][sla-aks] for virtual machines.</span></span>

### <a name="scalability"></a><span data-ttu-id="f0900-148">可伸缩性</span><span class="sxs-lookup"><span data-stu-id="f0900-148">Scalability</span></span>

<span data-ttu-id="f0900-149">对于大型数据，可以缩放 DSVM 以缩短训练时间。</span><span class="sxs-lookup"><span data-stu-id="f0900-149">If you have a large data size, you can scale your DSVM to shorten training time.</span></span> <span data-ttu-id="f0900-150">可以通过[更改 VM 大小][vm-size] 来扩展或缩减 VM。</span><span class="sxs-lookup"><span data-stu-id="f0900-150">You can scale a VM up or down by changing the [VM size][vm-size].</span></span> <span data-ttu-id="f0900-151">选择足够大的内存来适应内存中的数据集和更高的 vCPU 计数，以减少训练所需的时间。</span><span class="sxs-lookup"><span data-stu-id="f0900-151">Choose a memory size large enough to fit your dataset in-memory and a higher vCPU count in order to decrease the amount of time that training takes.</span></span>

### <a name="security"></a><span data-ttu-id="f0900-152">安全</span><span class="sxs-lookup"><span data-stu-id="f0900-152">Security</span></span>

<span data-ttu-id="f0900-153">此方案可以使用 Azure Active Directory 对[访问 DSVM][dsvm-id]（包含代码、模型和（内存中）数据）的用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="f0900-153">This scenario can use Azure Active Directory to authenticate users for [access to the DSVM][dsvm-id], which contains your code, models, and (in-memory) data.</span></span> <span data-ttu-id="f0900-154">数据先存储在 Azure 存储中，然后再加载到 DSVM。在 DSVM 上，使用[存储服务加密][storage-security]自动将数据加密。</span><span class="sxs-lookup"><span data-stu-id="f0900-154">Data is stored in Azure Storage prior to being loaded on a DSVM, where it is automatically encrypted using [Storage Service Encryption][storage-security].</span></span> <span data-ttu-id="f0900-155">可以通过 Azure Active Directory 身份验证或基于角色的访问控制来管理权限。</span><span class="sxs-lookup"><span data-stu-id="f0900-155">Permissions can be managed via Azure Active Directory authentication or role-based access control.</span></span>

## <a name="deploy-this-scenario"></a><span data-ttu-id="f0900-156">部署此方案</span><span class="sxs-lookup"><span data-stu-id="f0900-156">Deploy this scenario</span></span>

<span data-ttu-id="f0900-157">**先决条件**：必须已经有 Azure 帐户。</span><span class="sxs-lookup"><span data-stu-id="f0900-157">**Prerequisites**: You must have an existing Azure account.</span></span> <span data-ttu-id="f0900-158">如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户][free]。</span><span class="sxs-lookup"><span data-stu-id="f0900-158">If you don't have an Azure subscription, create a [free account][free] before you begin.</span></span>

<span data-ttu-id="f0900-159">[Microsoft 推荐器存储库][github]中提供了此方案的所有代码。</span><span class="sxs-lookup"><span data-stu-id="f0900-159">All the code for this scenario is available in the [Microsoft Recommenders repository][github].</span></span>

<span data-ttu-id="f0900-160">请遵循以下步骤运行 [ALS 快速入门 Notebook][notebook]：</span><span class="sxs-lookup"><span data-stu-id="f0900-160">Follow these steps to run the [ALS quickstart notebook][notebook]:</span></span>

1. <span data-ttu-id="f0900-161">从 Azure 门户[创建 DSVM][dsvm-ubuntu]。</span><span class="sxs-lookup"><span data-stu-id="f0900-161">[Create a DSVM][dsvm-ubuntu] from the Azure portal.</span></span>

2. <span data-ttu-id="f0900-162">克隆 Notebooks 文件夹中的存储库：</span><span class="sxs-lookup"><span data-stu-id="f0900-162">Clone the repo in the Notebooks folder:</span></span>

    ```shell
    cd notebooks
    git clone https://github.com/Microsoft/Recommenders
    ```

3. <span data-ttu-id="f0900-163">遵循 [SETUP.md][setup] 文件中所述的步骤安装 conda 依赖项。</span><span class="sxs-lookup"><span data-stu-id="f0900-163">Install the conda dependencies following the steps described in the [SETUP.md][setup] file.</span></span>

4. <span data-ttu-id="f0900-164">在浏览器中，转到 jupyterlab VM 并导航到 `notebooks/00_quick_start/als_pyspark_movielens.ipynb`。</span><span class="sxs-lookup"><span data-stu-id="f0900-164">In a browser, go to your jupyterlab VM and navigate to `notebooks/00_quick_start/als_pyspark_movielens.ipynb`.</span></span>

5. <span data-ttu-id="f0900-165">执行 Notebook。</span><span class="sxs-lookup"><span data-stu-id="f0900-165">Execute the notebook.</span></span>

## <a name="related-resources"></a><span data-ttu-id="f0900-166">相关资源</span><span class="sxs-lookup"><span data-stu-id="f0900-166">Related resources</span></span>

<span data-ttu-id="f0900-167">有关生成和缩放推荐器服务的深入指导，请参阅[在 Azure 上生成实时推荐 API][ref-arch]。</span><span class="sxs-lookup"><span data-stu-id="f0900-167">For an in-depth guide to building and scaling a recommender service, see [Build a real-time recommendation API on Azure][ref-arch].</span></span> <span data-ttu-id="f0900-168">有关推荐系统的教程和示例，请参阅 [Microsoft 推荐器存储库][github]。</span><span class="sxs-lookup"><span data-stu-id="f0900-168">For tutorials and examples of recommendation systems, see [Microsoft Recommenders repository][github].</span></span>

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
