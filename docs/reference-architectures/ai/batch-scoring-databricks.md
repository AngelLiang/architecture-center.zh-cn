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
# <a name="batch-scoring-of-spark-machine-learning-models-on-azure-databricks"></a>在 Azure Databricks 上的 Spark 机器学习批处理评分模型

此参考体系结构展示了如何生成可缩放的解决方案，以使用 Azure Databricks（更适合 Azure 的基于 Apache Spark 的分析平台）定期对 Apache Spark 分类模型进行批量评分。 可以将此解决方案用作能推广到其他应用场景中的模板。

 [GitHub][github] 中提供了本体系结构的参考实现。

![Azure Databricks 上 Spark 模型的批量评分](./_images/batch-scoring-spark.png)

**场景**：资产密集型行业中的某企业希望最大限度地减少与意外机械故障相关的成本和故障时间。 使用从计算机收集的 IoT 数据，他们可以创建预测性维护模型。 借助此模型，企业可以主动维护组件，并维修组件以免它们发生故障。 通过最大限度地利用机械组件，他们可以控制成本，并减少故障时间。

预测性维护模型从计算机收集数据，并保留组件故障的历史示例。 然后，此模型可用于监视组件的当前状态，并预测给定组件是否会在不久的将来出现故障。 有关常见用例和建模方法，请参阅 [Azure AI 预测性维护解决方案指南][ai-guide]。

此参考体系结构专为组件计算机中的新数据触发的工作负载而设计。 包括以下处理步骤：

1. 将数据从外部数据存储引入到 Azure Databricks 数据存储中。

2. 将数据转换为定型数据集，再生成 Spark MLlib 模型，从而定型机器学习模型。 MLlib 包含最常用的机器学习算法和实用工具，它们更适合利用 Spark 数据可伸缩性功能。

3. 将数据转换为评分数据集，以通过应用定型后的模型来预测（分类）组件故障。 使用 Spark MLLib 模型对数据进行评分。

4. 将结果存储在 Databricks 数据存储中，以供在处理后使用。

 [GitHub][github] 上提供了执行上述每项任务的笔记本。

## <a name="architecture"></a>体系结构

此体系结构根据一组按顺序执行的[笔记本][notebooks]，定义了完全包含在 [Azure Databricks][databricks] 中的数据流。 它包含以下组成部分：

**[数据文件][github]**。 此参考实现使用五个静态数据文件中包含的模拟数据集。

**[引入][notebooks]**。 数据引入笔记本将输入数据文件下载到 Databricks 数据集的集合中。 在实际应用场景中，IoT 设备中的数据会流式传输到 Databricks 可访问的存储中，如 Azure SQL Server 或 Azure Blob 存储。 Databricks 支持多个[数据源][data-sources]。

**定型管道**。 此笔记本执行特征工程笔记本，以根据引入的数据创建分析数据集。 然后，它执行模型生成笔记本，即使用 [Apache Spark MLlib][mllib] 可缩放机器学习库来定型机器学习模型。

**评分管道**。 此笔记本执行特征工程笔记本，以根据引入的数据创建评分数据集，同时还执行评分笔记本。 评分笔记本使用定型后的 [Spark MLlib][mllib-spark] 模型，针对评分数据集中的观察结果生成预测。 预测结果存储在结果存储中，这是 Databricks 数据存储中的新数据集。

**计划程序**。 定期 Databricks [作业][job]使用 Spark 模型来处理批量评分。 此作业执行评分管道笔记本，通过笔记本形参传递变量实参，以指定关于构造评分数据集，以及结果数据集存储位置的详细信息。

此应用场景构造为管道流。 每个笔记本都更适合在批量上下文中执行每个操作：引入、特征工程、模型生成和模型评分。 为此，特征工程笔记本旨在为任何定型、校准、测试或评分操作生成通用数据集。 在此应用场景中，我们对这些操作使用临时拆分策略，以便将笔记本形参用于设置日期范围筛选。

因为此应用场景创建了批处理管道，所以我们提供了一组可选的检查笔记本来探索管道笔记本的输出。 可以在 GitHub 存储库中找到下面这些笔记本：

- `1a_raw-data_exploring`
- `2a_feature_exploration`
- `2b_model_testing`
- `3b_model_scoring_evaluation`

## <a name="recommendations"></a>建议

Databricks 已经过设置，可便于加载和部署定型后的模型，以使用新数据进行预测。 我们对此应用场景使用了 Databricks，因为它可以提供下列额外优势：

- 单一登录支持（使用 Azure Active Directory 凭据）。
- 执行生产管道作业的作业计划程序。
- 包含协作功能、仪表板、REST API 的完全交互式笔记本。
- 可以缩放到任意大小的无限个群集。
- 高级安全性、基于角色的访问控制和审核日志。

若要与 Azure Databricks 服务进行交互，请使用 Web 浏览器中的 Databricks [工作区][workspace]界面，或使用[命令行接口][cli] (CLI)。 从任何支持 Python 2.7.9 到 3.6 的平台访问 Databricks CLI。

此参考实现使用[笔记本][notebooks]按顺序执行任务。 每个笔记本将中间数据项目（定型、测试、评分或结果数据集）存储到输入数据所在的相同数据存储中。 目标是可便于根据需要在特定用例中轻松使用它。 实际上，可以将数据源连接到 Azure Databricks 实例，这样笔记本就能直接在存储中读取和写回数据。

可以根据需要通过 Databricks 用户界面、数据存储或 Databricks [CLI][cli] 来监视作业执行情况。 使用[事件日志][log]以及 Databricks 提供的其他[指标][metrics]来监视群集。

## <a name="performance-considerations"></a>性能注意事项

Azure Databricks 群集默认启用自动缩放，这样在运行时期间，Databricks 可以动态重新分配工作进程，以将作业特征考虑在内。 管道的某些部分在计算方面的要求可能比另一些部分更高。 Databricks 会在作业的这些阶段添加额外工作进程（并在不再需要它们时将其删除）。 通过自动缩放，可以更轻松地实现高[群集利用率][cluster]，因为无需通过预配群集来匹配工作负载。

此外，通过将 [Azure 数据工厂][adf]与 Azure Databricks 结合使用，可以开发更复杂的定期管道。

## <a name="storage-considerations"></a>存储注意事项

在此参考实现中，为了简单起见，数据直接存储在 Databricks 存储中。 不过，在生产上下文中，数据可以存储在 [Azure Blob 存储][blob]等云数据存储中。 [Databricks][databricks-connect] 还支持 Azure Data Lake Store、Azure SQL 数据仓库、Azure Cosmos DB、Apache Kafka 和 Hadoop。

## <a name="cost-considerations"></a>成本注意事项

Azure Databricks 是一项有相关成本的 Spark 高级产品/服务。 此外，还有标准和高级 Databricks [定价层][pricing]。

对于此应用场景，标准定价层就足够了。 不过，如果具体应用程序需要自动缩放群集来处理更大的工作负载或交互式 Databricks 仪表板，高级级别可能会进一步增加成本。

解决方案笔记本可以在任何基于 Spark 的平台上运行，只需一点点编辑，即可删除 Databricks 专用包。 请参阅以下适用于各种 Azure 平台的类似解决方案：

- [Azure 机器学习工作室上的 Python][python-aml]
- [SQL Server R 服务][sql-r]
- [Azure Data Science Virtual Machine 上的 PySpark][py-dvsm]

## <a name="deploy-the-solution"></a>部署解决方案

若要部署此参考体系结构，请按照  [GitHub][github] 存储库中的步骤操作，生成可缩放的解决方案，以在 Azure Databricks 中对 Spark 模型进行批量评分。

## <a name="related-architectures"></a>相关体系结构

我们还生成了以下参考体系结构：使用 Spark 生成包含脱机预计算分数的[实时推荐系统][recommendation]。 这些推荐系统是批量处理分数的常见应用场景。

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
