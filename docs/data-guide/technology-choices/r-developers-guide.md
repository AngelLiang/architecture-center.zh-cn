---
title: Azure 中的 R 开发指南 - R 编程 | Microsoft Docs
description: 本文概述数据科学家在 Azure 中利用现有 R 编程语言技能的各种方式。 Azure 提供了很多 R 开发人员可以使用扩展其数据科学工作负载分配到云的服务。
services: machine-learning
author: AnalyticJeremy
ms.service: machine-learning
ms.workload: data-services
ms.devlang: R
ms.topic: article
ms.date: 03/19/2019
ms.author: jepeach
ms.openlocfilehash: a14c8ce76f78baa7274f22b939eb28cb025ef87e
ms.sourcegitcommit: 1a3cc91530d56731029ea091db1f15d41ac056af
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2019
ms.locfileid: "58887958"
---
# <a name="r-developers-guide-to-azure"></a>Azure 中的 R 开发指南

<img src="../images/logo_r.svg" alt="R logo" align="right" width="200" />

处理日益增长的数据量的许多数据科学家正在寻求利用云计算的强大能力进行分析。  本文概述数据科学家在 Azure 中利用现有 [R 编程语言](https://www.r-project.org)技能的各种方式。

Microsoft 完全接受将 R 编程语言作为数据科学家的第一类工具。  本公司为 R 开发人员提供许多不同的选项让他们在 Azure 中运行其代码，并使数据科学家在处理大型项目时能够将其数据科学工作负荷扩展到云中。

让我们了解各个选项，以及每个选项的最具吸引力的方案。

## <a name="azure-services-with-r-language-support"></a>支持 R 语言的 Azure 服务

本文介绍支持 R 语言的以下 Azure 服务：

|服务                                                          |描述                                                                       |
|-----------------------------------------------------------------|----------------------------------------------------------------------------------|
|[数据科学虚拟机](#data-science-virtual-machine)    |用作数据科学工作站或自定义计算目标的自定义 VM|
|[ML Services on HDInsight](#ml-services-on-hdinsight)            |基于群集的系统，用于对跨多个节点的大型数据集运行 R 分析   |
|[Azure Databricks](#azure-databricks)                            |支持 R 和其他语言的协作型 Spark 环境               |
|[Azure 机器学习工作室](#azure-machine-learning-studio)  |在 Azure 的机器学习试验中运行自定义 R 脚本                      |
|[Azure 批处理](#azure-batch)                                      |提供各种选项用于以经济节省的方式对群集中的多个节点运行 R 代码|
|[Azure Notebook](#azure-notebooks)                              |Jupyter Notebook 的基于云的免费版本                  |
|[Azure SQL 数据库](#azure-sql-database)                        |在 SQL Server 数据库引擎内部运行 R 脚本                            |

## <a name="data-science-virtual-machine"></a>数据科学虚拟机

[Data Science Virtual Machine](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) (DSVM) 是专为开展数据科学构建的 Microsoft Azure 云平台上的自定义 VM 映像。 其中包含许多热门的数据科学工具，包括：

* [Microsoft R Open](https://mran.microsoft.com/open/)
* [Microsoft 机器学习服务器](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)
* [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop)
* [RStudio Server](https://www.rstudio.com/products/rstudio/#Server)

可以在 DSVM 上预配 Windows 或 Linux 操作系统。  可通过两种方式使用 DSVM：用作交互式工作站，或用作自定义群集的计算平台。

### <a name="as-a-workstation"></a>用作工作站

若要在云中快速轻松地开始使用 R，则这是最佳选项。  在本地工作站上用过 R 的任何人都会熟悉该环境。  但是，R 环境不是使用本地资源，而是在云中的 VM 上运行。  如果数据已存储在 Azure 中，则此环境还能带来另一种优势：R 脚本可以在“更靠近数据”的位置运行。 无需通过 Internet 传输数据，可以通过 Azure 的内部网络访问数据，因此访问速度要快得多。

DSVM 可能对小型 R 开发人员团队特别有用。  无需为每个开发人员投资购买高配的工作站并要求团队成员同步他们使用的各个软件包版本，每个开发人员可以根据需要运转 DSVM 的实例。

### <a name="as-a-compute-platform"></a>用作计算平台

除了用作工作站以外，DSVM 还可用作 R 项目的弹性可缩放计算平台。  使用[ `AzureDSVM` ](https://github.com/Azure/AzureDSVM) R 包，您可以以编程方式控制创建和删除 DSVM 实例。  可将实例组建成群集，并部署要在云中执行的分布式分析。  可以通过本地工作站上运行的 R 代码控制整个过程。

若要了解有关 DSVM 的详细信息，请参阅[适用于 Linux 和 Windows 的 Azure 数据科学虚拟机简介](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview)。

## <a name="ml-services-on-hdinsight"></a>ML Services on HDInsight

[Microsoft ML Services](https://docs.microsoft.com/azure/hdinsight/r-server/r-server-overview) 可让数据科学家、统计师和 R 程序员按需访问 HDInsight 上可缩放的分布式分析方法。  此解决方案提供最新的功能，可针对载入 Azure Blob 或 Data Lake Storage 的几乎任何大小的数据集执行基于 R 的分析。

这是一个企业级的解决方案，允许在整个群集中缩放 R 代码。  通过利用 Microsoft 的中的函数 [`RevoScaleR`](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)
包中，你在 HDInsight 上的 R 脚本可以数据处理函数并行运行跨多个节点在群集中。  这样，R 便可以使用工作站上运行的单线程 R，以远超寻常的规模处理数据。

这种大规模处理能力使得 ML Services on HDInsight 成了需要处理巨量数据集的 R 开发人员的极佳选项。  它提供一个灵活、可缩放的平台用于在云中运行 R 脚本。

创建机器学习服务群集的演练，请参阅[开始使用 Azure HDInsight 上的机器学习服务](https://docs.microsoft.com/azure/hdinsight/r-server/r-server-get-started)。

## <a name="azure-databricks"></a>Azure Databricks

[Azure Databricks](https://azure.microsoft.com/services/databricks/) 是基于 Apache Spark 的分析平台，已针对 Microsoft Azure 云服务平台进行优化。  我们与 Apache Spark 的创建者一起设计了 Databricks，并将其与 Azure 集成以提供一键式安装程序、简化的工作流程以及交互式工作区，从而使数据科学家、数据工程师和业务分析员之间可以进行合作。

Databricks 中的协作由平台的 Notebook 系统启用。  用户可以创建、编辑 Notebook 并与系统的其他用户共享。  用户可以使用这些 Notebook 编写针对 Databricks 环境中托管的 Spark 群集执行的代码。  这些 Notebook 完全支持 R，并可让用户通过 `SparkR` 和 `sparklyr` 包访问 Spark。

由于 Databricks 构建在 Spark 基础之上并且侧重于协作，该平台往往由共同解决大型数据集复杂分析的数据科学家团队使用。  由于 Databricks 中的 Notebook 除了支持 R 以外还支持其他语言，因此，它对于在主要工作中使用不同语言的分析师团队非常有用。

文章[什么是 Azure Databricks？](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks)可以提供有关平台和帮助您入门的更多详细信息。

## <a name="azure-machine-learning-studio"></a>Azure 机器学习工作室

[Azure 机器学习工作室](https://azure.microsoft.com/services/machine-learning-studio/)是一个协作型拖放式工具，可用于在云中构建、测试和部署预测分析解决方案。  越来越多的数据科学家正在使用它来创建和部署机器学习模型，而无需编写大量的代码。

Azure 机器学习工作室支持 R 和 Python。  两种方式，可以通过 Azure 机器学习工作室中使用 R。

### <a name="custom-r-scripts-in-your-experiments"></a>试验中的自定义 R 脚本

首先，可以通过编写自定义 R 脚本来扩展机器学习工作室的数据处理和机器学习功能。
机器学习工作室包含各种模块用于准备和分析数据，但还比不上 R 等成熟语言的功能。因此，当提供的模块无法满足需求时，该服务允许你引入自己的自定义 R 脚本。

若要利用此功能，请将“执行 R 脚本”模块拖到试验中。  然后，使用“属性”窗格中的代码编辑器编写新的 R 脚本，或粘贴现有脚本。  在脚本中，可以引用外部 R 包。  若要处理的数据或训练复杂不是标准的 Azure 机器学习工作室模型库的一部分的机器学习模型，可以使用该脚本。

有关在机器学习工作室试验中使用 R 的全面介绍，请参阅[R 编程语言中 Azure 机器学习工作室入门](https://docs.microsoft.com/azure/machine-learning/studio/r-quickstart)。

### <a name="create-manage-and-deploy-experiments-from-your-local-r-environment"></a>在本地 R 环境中创建、管理和部署试验

您可以通过 Azure 机器学习工作室中使用 R 的其他方法是使用[ `AzureML` ](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html)包来监视和控制与 R 编程环境对试验过程。  此包，由 Microsoft 维护，可以上传和下载数据集与 Azure 机器学习工作室中，以询问试验，发布 R 函数作为 web 服务，运行 R 数据并通过现有 web 服务检索输出。

此包，可以更方便地为你的 R 代码作为可缩放的部署平台使用 Azure 机器学习工作室。  无需在 UI 中不断地单击和拖放，而可以使用熟知的工具将整个部署过程自动化。

## <a name="azure-batch"></a>Azure 批处理

对于大规模 R 作业，可以使用 [Azure Batch](https://azure.microsoft.com/services/batch/)。  此服务提供云规模的作业计划和计算管理，可让你缩放跨数十、数百甚至数千个虚拟机的 R 工作负荷。  由于它是一个通用化的计算平台，在 Azure Batch 上运行 R 作业的选项有很多。

一种选择是使用 Microsoft [ `doAzureParallel` ](https://github.com/Azure/doAzureParallel)包。  此 R 包是 `foreach` 包的并行后端。  使用此包可在 Azure Batch 群集中的节点上并行运行 `foreach` 循环的每个迭代。  有关包的介绍，请参阅博客文章[doAzureParallel:充分利用 Azure 的灵活计算直接从 R 会话](https://azure.microsoft.com/blog/doazureparallel/)。

在 Azure Batch 中运行 R 脚本的另一个选项是在 Azure 门户中使用“RScript.exe”将代码捆绑为 Batch 应用。  有关详细的演练，请查阅[Azure Batch 上的 R 工作负荷](https://azure.microsoft.com/blog/r-workloads-on-azure-batch/)。

第三个选项是使用 [Azure 分布式数据工程工具包](https://github.com/Azure/aztk) (AZTK)。该工具包可让你使用 Azure Batch 中的 Docker 容器预配按需 Spark 群集。  这样，便可以经济节省的方式在 Azure 中运行 Spark 作业。  使用 [SparklyR 和 AZTK](https://github.com/Azure/aztk/wiki/SparklyR-on-Azure-with-AZTK) 可以在云中轻松、经济节省地扩展 R 脚本。

## <a name="azure-notebooks"></a>Azure Notebook

[Azure Notebooks](https://notebooks.azure.com) 是一种成本低、冲突少的服务，适合偏向于使用 Notebook 将代码部署到 Azure 的 R 开发人员。  它是一个免费的服务，面向使用 [Jupyter](https://jupyter.org/)（一个开源项目，可将 markdown 文本信息、可执行代码和图形合并到一个画布上）在浏览器中开发和运行代码的任何用户。

Azure Notebooks 的免费服务层是适用于小规模项目的可行选项，因为它将每个 Notebook 的处理能力限制为 4 GB 内存和 1 GB 数据集。 但是，如果需要超出这些限制的计算和数据处理能力，则可以在 Data Science Virtual Machine 实例中运行 Notebook。 有关详细信息，请参阅[管理和配置 Azure Notebooks 项目 - 计算层](/azure/notebooks/configure-manage-azure-notebooks-projects#compute-tier)。

## <a name="azure-sql-database"></a>Azure SQL 数据库

[Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/)是 Microsoft 提供的完全托管式的智能关系型云数据库服务。  它可以让你使用 SQL Server 的完整功能，省去了设置基础结构的麻烦。  这包括[SQL Server 中机器学习服务](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning?view=sql-server-2017)，这是 SQL 的较新功能之一。

此功能提供嵌入式预测分析和数据科学引擎，该引擎可将 SQL Server 数据库中的 R 代码作为存储过程、包含 R 语句的 T-SQL 脚本或包含 T-SQL 的 R 代码来执行。  无需从数据库中提取数据并将其载入 R 环境，而可以直接将 R 代码载入数据库，使其连接数据一起运行。

从 2016 年开始，机器学习服务已划归到本地 SQL Server，是 Azure SQL 数据库的相对较新的功能。  它目前以[受限预览版](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#azure-sql-database-roadmap)的形式提供，同时在不断改善。


### <a name="next-steps"></a>后续步骤

* [使用 mrsdeploy 在 Azure 上运行 R 代码](https://blog.revolutionanalytics.com/2017/03/running-your-r-code-azure.html)
* [机器学习在云中的服务器](https://docs.microsoft.com/machine-learning-server/install/machine-learning-server-in-the-cloud)
* [机器学习服务器和 Microsoft R 的其他资源](https://docs.microsoft.com/machine-learning-server/resources-more)
* [R on Azure](https://github.com/yueguoguo/r-on-azure) - 概述在 Azure 中使用 R 所需的包、工具和相关案例研究

---

<sub>R 徽标&copy;2016年 R Foundation 和使用的条款[Creative Commons Attribution-sharealike 4.0 国际许可证](https://creativecommons.org/licenses/by-sa/4.0/)。</sub>
