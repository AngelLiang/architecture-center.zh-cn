---
title: Azure Databricks 上 Spark 模型的批量评分
description: 生成可缩放的解决方案，以使用 Azure Databricks 定期对 Apache Spark 分类模型进行批量评分。
author: njray
ms.date: 02/07/2019
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: azcat-ai
ms.openlocfilehash: cba8d272ddbdbf2c2da94f68b288e9fb79be7de2
ms.sourcegitcommit: 1a3cc91530d56731029ea091db1f15d41ac056af
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2019
ms.locfileid: "58887805"
---
# <a name="batch-scoring-of-spark-machine-learning-models-on-azure-databricks"></a><span data-ttu-id="6f697-103">在 Azure Databricks 上的 Spark 机器学习批处理评分模型</span><span class="sxs-lookup"><span data-stu-id="6f697-103">Batch scoring of Spark machine learning models on Azure Databricks</span></span>

<span data-ttu-id="6f697-104">此参考体系结构展示了如何生成可缩放的解决方案，以使用 Azure Databricks（更适合 Azure 的基于 Apache Spark 的分析平台）定期对 Apache Spark 分类模型进行批量评分。</span><span class="sxs-lookup"><span data-stu-id="6f697-104">This reference architecture shows how to build a scalable solution for batch scoring an Apache Spark classification model on a schedule using Azure Databricks, an Apache Spark-based analytics platform optimized for Azure.</span></span> <span data-ttu-id="6f697-105">可以将此解决方案用作能推广到其他应用场景中的模板。</span><span class="sxs-lookup"><span data-stu-id="6f697-105">The solution can be used as a template that can be generalized to other scenarios.</span></span>

<span data-ttu-id="6f697-106"> [GitHub][github] 中提供了本体系结构的参考实现。</span><span class="sxs-lookup"><span data-stu-id="6f697-106">A reference implementation for this architecture is available on [GitHub][github].</span></span>

![Azure Databricks 上 Spark 模型的批量评分](./_images/batch-scoring-spark.png)

<span data-ttu-id="6f697-108">**场景**：资产密集型行业中的某企业希望最大限度地减少与意外机械故障相关的成本和故障时间。</span><span class="sxs-lookup"><span data-stu-id="6f697-108">**Scenario**: A business in an asset-heavy industry wants to minimize the costs and downtime associated with unexpected mechanical failures.</span></span> <span data-ttu-id="6f697-109">使用从计算机收集的 IoT 数据，他们可以创建预测性维护模型。</span><span class="sxs-lookup"><span data-stu-id="6f697-109">Using IoT data collected from their machines, they can create a predictive maintenance model.</span></span> <span data-ttu-id="6f697-110">借助此模型，企业可以主动维护组件，并维修组件以免它们发生故障。</span><span class="sxs-lookup"><span data-stu-id="6f697-110">This model enables the business to maintain components proactively and repair them before they fail.</span></span> <span data-ttu-id="6f697-111">通过最大限度地利用机械组件，他们可以控制成本，并减少故障时间。</span><span class="sxs-lookup"><span data-stu-id="6f697-111">By maximizing mechanical component use, they can control costs and reduce downtime.</span></span>

<span data-ttu-id="6f697-112">预测性维护模型从计算机收集数据，并保留组件故障的历史示例。</span><span class="sxs-lookup"><span data-stu-id="6f697-112">A predictive maintenance model collects data from the machines and retains historical examples of component failures.</span></span> <span data-ttu-id="6f697-113">然后，此模型可用于监视组件的当前状态，并预测给定组件是否会在不久的将来出现故障。</span><span class="sxs-lookup"><span data-stu-id="6f697-113">The model can then be used to monitor the current state of the components and predict if a given component will fail in the near future.</span></span> <span data-ttu-id="6f697-114">有关常见用例和建模方法，请参阅 [Azure AI 预测性维护解决方案指南][ai-guide]。</span><span class="sxs-lookup"><span data-stu-id="6f697-114">For common use cases and modeling approaches, see [Azure AI guide for predictive maintenance solutions][ai-guide].</span></span>

<span data-ttu-id="6f697-115">此参考体系结构专为组件计算机中的新数据触发的工作负载而设计。</span><span class="sxs-lookup"><span data-stu-id="6f697-115">This reference architecture is designed for workloads that are triggered by the presence of new data from the component machines.</span></span> <span data-ttu-id="6f697-116">包括以下处理步骤：</span><span class="sxs-lookup"><span data-stu-id="6f697-116">Processing involves the following steps:</span></span>

1. <span data-ttu-id="6f697-117">将数据从外部数据存储引入到 Azure Databricks 数据存储中。</span><span class="sxs-lookup"><span data-stu-id="6f697-117">Ingest the data from the external data store onto an Azure Databricks data store.</span></span>

2. <span data-ttu-id="6f697-118">将数据转换为定型数据集，再生成 Spark MLlib 模型，从而定型机器学习模型。</span><span class="sxs-lookup"><span data-stu-id="6f697-118">Train a machine learning model by transforming the data into a training data set, then building a Spark MLlib model.</span></span> <span data-ttu-id="6f697-119">MLlib 包含最常用的机器学习算法和实用工具，它们更适合利用 Spark 数据可伸缩性功能。</span><span class="sxs-lookup"><span data-stu-id="6f697-119">MLlib consists of most common machine learning algorithms and utilities optimized to take advantage of Spark data scalability capabilities.</span></span>

3. <span data-ttu-id="6f697-120">将数据转换为评分数据集，以通过应用定型后的模型来预测（分类）组件故障。</span><span class="sxs-lookup"><span data-stu-id="6f697-120">Apply the trained model to predict (classify) component failures by transforming the data into a scoring data set.</span></span> <span data-ttu-id="6f697-121">使用 Spark MLLib 模型对数据进行评分。</span><span class="sxs-lookup"><span data-stu-id="6f697-121">Score the data with the Spark MLLib model.</span></span>

4. <span data-ttu-id="6f697-122">将结果存储在 Databricks 数据存储中，以供在处理后使用。</span><span class="sxs-lookup"><span data-stu-id="6f697-122">Store results on the Databricks data store for post-processing consumption.</span></span>

<span data-ttu-id="6f697-123"> [GitHub][github] 上提供了执行上述每项任务的笔记本。</span><span class="sxs-lookup"><span data-stu-id="6f697-123">Notebooks are provided on [GitHub][github] to perform each of these tasks.</span></span>

## <a name="architecture"></a><span data-ttu-id="6f697-124">体系结构</span><span class="sxs-lookup"><span data-stu-id="6f697-124">Architecture</span></span>

<span data-ttu-id="6f697-125">此体系结构根据一组按顺序执行的[笔记本][notebooks]，定义了完全包含在 [Azure Databricks][databricks] 中的数据流。</span><span class="sxs-lookup"><span data-stu-id="6f697-125">The architecture defines a data flow that is entirely contained within [Azure Databricks][databricks] based on a set of sequentially executed [notebooks][notebooks].</span></span> <span data-ttu-id="6f697-126">它包含以下组成部分：</span><span class="sxs-lookup"><span data-stu-id="6f697-126">It consists of the following components:</span></span>

<span data-ttu-id="6f697-127">**[数据文件][github]**。</span><span class="sxs-lookup"><span data-stu-id="6f697-127">**[Data files][github]**.</span></span> <span data-ttu-id="6f697-128">此参考实现使用五个静态数据文件中包含的模拟数据集。</span><span class="sxs-lookup"><span data-stu-id="6f697-128">The reference implementation uses a simulated data set contained in five static data files.</span></span>

<span data-ttu-id="6f697-129">**[引入][notebooks]**。</span><span class="sxs-lookup"><span data-stu-id="6f697-129">**[Ingestion][notebooks]**.</span></span> <span data-ttu-id="6f697-130">数据引入笔记本将输入数据文件下载到 Databricks 数据集的集合中。</span><span class="sxs-lookup"><span data-stu-id="6f697-130">The data ingestion notebook downloads the input data files into a collection of Databricks data sets.</span></span> <span data-ttu-id="6f697-131">在实际应用场景中，IoT 设备中的数据会流式传输到 Databricks 可访问的存储中，如 Azure SQL Server 或 Azure Blob 存储。</span><span class="sxs-lookup"><span data-stu-id="6f697-131">In a real-world scenario, data from IoT devices would stream onto Databricks-accessible storage such as Azure SQL Server or Azure Blob storage.</span></span> <span data-ttu-id="6f697-132">Databricks 支持多个[数据源][data-sources]。</span><span class="sxs-lookup"><span data-stu-id="6f697-132">Databricks supports multiple [data sources][data-sources].</span></span>

<span data-ttu-id="6f697-133">**定型管道**。</span><span class="sxs-lookup"><span data-stu-id="6f697-133">**Training pipeline**.</span></span> <span data-ttu-id="6f697-134">此笔记本执行特征工程笔记本，以根据引入的数据创建分析数据集。</span><span class="sxs-lookup"><span data-stu-id="6f697-134">This notebook executes the feature engineering notebook to create an analysis data set from the ingested data.</span></span> <span data-ttu-id="6f697-135">然后，它执行模型生成笔记本，即使用 [Apache Spark MLlib][mllib] 可缩放机器学习库来定型机器学习模型。</span><span class="sxs-lookup"><span data-stu-id="6f697-135">It then executes a model building notebook that trains the machine learning model using the [Apache Spark MLlib][mllib] scalable machine learning library.</span></span>

<span data-ttu-id="6f697-136">**评分管道**。</span><span class="sxs-lookup"><span data-stu-id="6f697-136">**Scoring pipeline**.</span></span> <span data-ttu-id="6f697-137">此笔记本执行特征工程笔记本，以根据引入的数据创建评分数据集，同时还执行评分笔记本。</span><span class="sxs-lookup"><span data-stu-id="6f697-137">This notebook executes the feature engineering notebook to create scoring data set from the ingested data and executes the scoring notebook.</span></span> <span data-ttu-id="6f697-138">评分笔记本使用定型后的 [Spark MLlib][mllib-spark] 模型，针对评分数据集中的观察结果生成预测。</span><span class="sxs-lookup"><span data-stu-id="6f697-138">The scoring notebook uses the trained [Spark MLlib][mllib-spark] model to generate predictions for the observations in the scoring data set.</span></span> <span data-ttu-id="6f697-139">预测结果存储在结果存储中，这是 Databricks 数据存储中的新数据集。</span><span class="sxs-lookup"><span data-stu-id="6f697-139">The predictions are stored in the results store, a new data set on the Databricks data store.</span></span>

<span data-ttu-id="6f697-140">**计划程序**。</span><span class="sxs-lookup"><span data-stu-id="6f697-140">**Scheduler**.</span></span> <span data-ttu-id="6f697-141">定期 Databricks [作业][job]使用 Spark 模型来处理批量评分。</span><span class="sxs-lookup"><span data-stu-id="6f697-141">A scheduled Databricks [job][job] handles batch scoring with the Spark model.</span></span> <span data-ttu-id="6f697-142">此作业执行评分管道笔记本，通过笔记本形参传递变量实参，以指定关于构造评分数据集，以及结果数据集存储位置的详细信息。</span><span class="sxs-lookup"><span data-stu-id="6f697-142">The job executes the scoring pipeline notebook, passing variable arguments through notebook parameters to specify the details for constructing the scoring data set and where to store the results data set.</span></span>

<span data-ttu-id="6f697-143">此应用场景构造为管道流。</span><span class="sxs-lookup"><span data-stu-id="6f697-143">The scenario is constructed as a pipeline flow.</span></span> <span data-ttu-id="6f697-144">每个笔记本都更适合在批量上下文中执行每个操作：引入、特征工程、模型生成和模型评分。</span><span class="sxs-lookup"><span data-stu-id="6f697-144">Each notebook is optimized to perform in a batch setting for each of the operations: ingestion, feature engineering, model building, and model scorings.</span></span> <span data-ttu-id="6f697-145">为此，特征工程笔记本旨在为任何定型、校准、测试或评分操作生成通用数据集。</span><span class="sxs-lookup"><span data-stu-id="6f697-145">To accomplish this, the feature engineering notebook is designed to generate a general data set for any of the training, calibration, testing, or scoring operations.</span></span> <span data-ttu-id="6f697-146">在此应用场景中，我们对这些操作使用临时拆分策略，以便将笔记本形参用于设置日期范围筛选。</span><span class="sxs-lookup"><span data-stu-id="6f697-146">In this scenario, we use a temporal split strategy for these operations, so the notebook parameters are used to set date-range filtering.</span></span>

<span data-ttu-id="6f697-147">因为此应用场景创建了批处理管道，所以我们提供了一组可选的检查笔记本来探索管道笔记本的输出。</span><span class="sxs-lookup"><span data-stu-id="6f697-147">Because the scenario creates a batch pipeline, we provide a set of optional examination notebooks to explore the output of the pipeline notebooks.</span></span> <span data-ttu-id="6f697-148">可以在 GitHub 存储库中找到下面这些笔记本：</span><span class="sxs-lookup"><span data-stu-id="6f697-148">You can find these in the GitHub repository:</span></span>

- `1a_raw-data_exploring`
- `2a_feature_exploration`
- `2b_model_testing`
- `3b_model_scoring_evaluation`

## <a name="recommendations"></a><span data-ttu-id="6f697-149">建议</span><span class="sxs-lookup"><span data-stu-id="6f697-149">Recommendations</span></span>

<span data-ttu-id="6f697-150">Databricks 已经过设置，可便于加载和部署定型后的模型，以使用新数据进行预测。</span><span class="sxs-lookup"><span data-stu-id="6f697-150">Databricks is set up so you can load and deploy your trained models to make predictions with new data.</span></span> <span data-ttu-id="6f697-151">我们对此应用场景使用了 Databricks，因为它可以提供下列额外优势：</span><span class="sxs-lookup"><span data-stu-id="6f697-151">We used Databricks for this scenario because it provides these additional advantages:</span></span>

- <span data-ttu-id="6f697-152">单一登录支持（使用 Azure Active Directory 凭据）。</span><span class="sxs-lookup"><span data-stu-id="6f697-152">Single sign-on support using Azure Active Directory credentials.</span></span>
- <span data-ttu-id="6f697-153">执行生产管道作业的作业计划程序。</span><span class="sxs-lookup"><span data-stu-id="6f697-153">Job scheduler to execute jobs for production pipelines.</span></span>
- <span data-ttu-id="6f697-154">包含协作功能、仪表板、REST API 的完全交互式笔记本。</span><span class="sxs-lookup"><span data-stu-id="6f697-154">Fully interactive notebook with collaboration, dashboards, REST APIs.</span></span>
- <span data-ttu-id="6f697-155">可以缩放到任意大小的无限个群集。</span><span class="sxs-lookup"><span data-stu-id="6f697-155">Unlimited clusters that can scale to any size.</span></span>
- <span data-ttu-id="6f697-156">高级安全性、基于角色的访问控制和审核日志。</span><span class="sxs-lookup"><span data-stu-id="6f697-156">Advanced security, role-based access controls, and audit logs.</span></span>

<span data-ttu-id="6f697-157">若要与 Azure Databricks 服务进行交互，请使用 Web 浏览器中的 Databricks [工作区][workspace]界面，或使用[命令行接口][cli] (CLI)。</span><span class="sxs-lookup"><span data-stu-id="6f697-157">To interact with the Azure Databricks service, use the Databricks [Workspace][workspace] interface in a web browser or the [command-line interface][cli] (CLI).</span></span> <span data-ttu-id="6f697-158">从任何支持 Python 2.7.9 到 3.6 的平台访问 Databricks CLI。</span><span class="sxs-lookup"><span data-stu-id="6f697-158">Access the Databricks CLI from any platform that supports Python 2.7.9 to 3.6.</span></span>

<span data-ttu-id="6f697-159">此参考实现使用[笔记本][notebooks]按顺序执行任务。</span><span class="sxs-lookup"><span data-stu-id="6f697-159">The reference implementation uses [notebooks][notebooks] to execute tasks in sequence.</span></span> <span data-ttu-id="6f697-160">每个笔记本将中间数据项目（定型、测试、评分或结果数据集）存储到输入数据所在的相同数据存储中。</span><span class="sxs-lookup"><span data-stu-id="6f697-160">Each notebook stores intermediate data artifacts (training, test, scoring, or results data sets) to the same data store as the input data.</span></span> <span data-ttu-id="6f697-161">目标是可便于根据需要在特定用例中轻松使用它。</span><span class="sxs-lookup"><span data-stu-id="6f697-161">The goal is to make it easy for you to use it as needed in your particular use case.</span></span> <span data-ttu-id="6f697-162">实际上，可以将数据源连接到 Azure Databricks 实例，这样笔记本就能直接在存储中读取和写回数据。</span><span class="sxs-lookup"><span data-stu-id="6f697-162">In practice, you would connect your data source to your Azure Databricks instance for the notebooks to read and write directly back into your storage.</span></span>

<span data-ttu-id="6f697-163">可以根据需要通过 Databricks 用户界面、数据存储或 Databricks [CLI][cli] 来监视作业执行情况。</span><span class="sxs-lookup"><span data-stu-id="6f697-163">You can monitor job execution through the Databricks user interface, the data store, or the Databricks [CLI][cli] as necessary.</span></span> <span data-ttu-id="6f697-164">使用[事件日志][log]以及 Databricks 提供的其他[指标][metrics]来监视群集。</span><span class="sxs-lookup"><span data-stu-id="6f697-164">Monitor the cluster using the [event log][log] and other [metrics][metrics] that Databricks provides.</span></span>

## <a name="performance-considerations"></a><span data-ttu-id="6f697-165">性能注意事项</span><span class="sxs-lookup"><span data-stu-id="6f697-165">Performance considerations</span></span>

<span data-ttu-id="6f697-166">Azure Databricks 群集默认启用自动缩放，这样在运行时期间，Databricks 可以动态重新分配工作进程，以将作业特征考虑在内。</span><span class="sxs-lookup"><span data-stu-id="6f697-166">An Azure Databricks cluster enables autoscaling by default so that during runtime, Databricks dynamically reallocates workers to account for the characteristics of your job.</span></span> <span data-ttu-id="6f697-167">管道的某些部分在计算方面的要求可能比另一些部分更高。</span><span class="sxs-lookup"><span data-stu-id="6f697-167">Certain parts of your pipeline may be more computationally demanding than others.</span></span> <span data-ttu-id="6f697-168">Databricks 会在作业的这些阶段添加额外工作进程（并在不再需要它们时将其删除）。</span><span class="sxs-lookup"><span data-stu-id="6f697-168">Databricks adds additional workers during these phases of your job (and removes them when they’re no longer needed).</span></span> <span data-ttu-id="6f697-169">通过自动缩放，可以更轻松地实现高[群集利用率][cluster]，因为无需通过预配群集来匹配工作负载。</span><span class="sxs-lookup"><span data-stu-id="6f697-169">Autoscaling makes it easier to achieve high [cluster utilization][cluster], because you don’t need to provision the cluster to match a workload.</span></span>

<span data-ttu-id="6f697-170">此外，通过将 [Azure 数据工厂][adf]与 Azure Databricks 结合使用，可以开发更复杂的定期管道。</span><span class="sxs-lookup"><span data-stu-id="6f697-170">Additionally, more complex scheduled pipelines can be developed by using [Azure Data Factory][adf] with Azure Databricks.</span></span>

## <a name="storage-considerations"></a><span data-ttu-id="6f697-171">存储注意事项</span><span class="sxs-lookup"><span data-stu-id="6f697-171">Storage considerations</span></span>

<span data-ttu-id="6f697-172">在此参考实现中，为了简单起见，数据直接存储在 Databricks 存储中。</span><span class="sxs-lookup"><span data-stu-id="6f697-172">In this reference implementation, the data is stored directly within Databricks storage for simplicity.</span></span> <span data-ttu-id="6f697-173">不过，在生产上下文中，数据可以存储在 [Azure Blob 存储][blob]等云数据存储中。</span><span class="sxs-lookup"><span data-stu-id="6f697-173">In a production setting, however, the data can be stored on cloud data storage such as [Azure Blob Storage][blob].</span></span> <span data-ttu-id="6f697-174">[Databricks][databricks-connect] 还支持 Azure Data Lake Store、Azure SQL 数据仓库、Azure Cosmos DB、Apache Kafka 和 Hadoop。</span><span class="sxs-lookup"><span data-stu-id="6f697-174">[Databricks][databricks-connect] also supports Azure Data Lake Store, Azure SQL Data Warehouse, Azure Cosmos DB, Apache Kafka, and Hadoop.</span></span>

## <a name="cost-considerations"></a><span data-ttu-id="6f697-175">成本注意事项</span><span class="sxs-lookup"><span data-stu-id="6f697-175">Cost considerations</span></span>

<span data-ttu-id="6f697-176">Azure Databricks 是一项有相关成本的 Spark 高级产品/服务。</span><span class="sxs-lookup"><span data-stu-id="6f697-176">Azure Databricks is a premium Spark offering with an associated cost.</span></span> <span data-ttu-id="6f697-177">此外，还有标准和高级 Databricks [定价层][pricing]。</span><span class="sxs-lookup"><span data-stu-id="6f697-177">In addition, there are standard and premium Databricks [pricing tiers][pricing].</span></span>

<span data-ttu-id="6f697-178">对于此应用场景，标准定价层就足够了。</span><span class="sxs-lookup"><span data-stu-id="6f697-178">For this scenario, the standard pricing tier is sufficient.</span></span> <span data-ttu-id="6f697-179">不过，如果具体应用程序需要自动缩放群集来处理更大的工作负载或交互式 Databricks 仪表板，高级级别可能会进一步增加成本。</span><span class="sxs-lookup"><span data-stu-id="6f697-179">However, if your specific application requires automatically scaling clusters to handle larger workloads or interactive Databricks dashboards, the premium level could increase costs further.</span></span>

<span data-ttu-id="6f697-180">解决方案笔记本可以在任何基于 Spark 的平台上运行，只需一点点编辑，即可删除 Databricks 专用包。</span><span class="sxs-lookup"><span data-stu-id="6f697-180">The solution notebooks can run on any Spark-based platform with minimal edits to remove the Databricks-specific packages.</span></span> <span data-ttu-id="6f697-181">请参阅以下适用于各种 Azure 平台的类似解决方案：</span><span class="sxs-lookup"><span data-stu-id="6f697-181">See the following similar solutions for various Azure platforms:</span></span>

- <span data-ttu-id="6f697-182">[Azure 机器学习工作室上的 Python][python-aml]</span><span class="sxs-lookup"><span data-stu-id="6f697-182">[Python on Azure Machine Learning Studio][python-aml]</span></span>
- <span data-ttu-id="6f697-183">[SQL Server R 服务][sql-r]</span><span class="sxs-lookup"><span data-stu-id="6f697-183">[SQL Server R services][sql-r]</span></span>
- <span data-ttu-id="6f697-184">[Azure Data Science Virtual Machine 上的 PySpark][py-dvsm]</span><span class="sxs-lookup"><span data-stu-id="6f697-184">[PySpark on an Azure Data Science Virtual Machine][py-dvsm]</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="6f697-185">部署解决方案</span><span class="sxs-lookup"><span data-stu-id="6f697-185">Deploy the solution</span></span>

<span data-ttu-id="6f697-186">若要部署此参考体系结构，请按照  [GitHub][github] 存储库中的步骤操作，生成可缩放的解决方案，以在 Azure Databricks 中对 Spark 模型进行批量评分。</span><span class="sxs-lookup"><span data-stu-id="6f697-186">To deploy this reference architecture, follow the steps described in the [GitHub][github] repository to build a scalable solution for scoring Spark models in batch on Azure Databricks.</span></span>

## <a name="related-architectures"></a><span data-ttu-id="6f697-187">相关体系结构</span><span class="sxs-lookup"><span data-stu-id="6f697-187">Related architectures</span></span>

<span data-ttu-id="6f697-188">我们还生成了以下参考体系结构：使用 Spark 生成包含脱机预计算分数的[实时推荐系统][recommendation]。</span><span class="sxs-lookup"><span data-stu-id="6f697-188">We have also built a reference architecture that uses Spark for building [real-time recommendation systems][recommendation] with offline, pre-computed scores.</span></span> <span data-ttu-id="6f697-189">这些推荐系统是批量处理分数的常见应用场景。</span><span class="sxs-lookup"><span data-stu-id="6f697-189">These recommendation systems are common scenarios where scores are batch-processed.</span></span>

[adf]: https://azure.microsoft.com/blog/operationalize-azure-databricks-notebooks-using-data-factory/
[ai-guide]: /azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance
[blob]: https://docs.databricks.com/spark/latest/data-sources/azure/azure-storage.html
[cli]: https://docs.databricks.com/user-guide/dev-tools/databricks-cli.html
[cluster]: https://docs.azuredatabricks.net/user-guide/clusters/sizing.html
[databricks]: /azure/azure-databricks/
[databricks-connect]: /azure/azure-databricks/databricks-connect-to-data-sources
[data-sources]: https://docs.databricks.com/spark/latest/data-sources/index.html
[github]: https://github.com/Azure/BatchSparkScoringPredictiveMaintenance
[job]: https://docs.databricks.com/user-guide/jobs.html
[log]: https://docs.databricks.com/user-guide/clusters/event-log.html
[metrics]: https://docs.databricks.com/user-guide/clusters/metrics.html
[mllib]: https://docs.databricks.com/spark/latest/mllib/index.html
[mllib-spark]: https://docs.databricks.com/spark/latest/mllib/index.html#apache-spark-mllib
[notebooks]: https://docs.databricks.com/user-guide/notebooks/index.html
[pricing]: https://azure.microsoft.com/en-us/pricing/details/databricks/
[python-aml]: https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1
[py-dvsm]: https://gallery.azure.ai/Tutorial/Predictive-Maintenance-using-PySpark
[recommendation]: /azure/architecture/reference-architectures/ai/real-time-recommendation
[sql-r]: https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1
[workspace]: https://docs.databricks.com/user-guide/workspace.html
