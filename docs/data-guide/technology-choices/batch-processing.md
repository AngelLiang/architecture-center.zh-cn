---
title: 选择批处理技术
description: ''
author: zoinerTejada
ms.date: 11/03/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.openlocfilehash: 53f8b233b0e0c1ff83a72a04b2707caa528d6f6b
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58248512"
---
# <a name="choosing-a-batch-processing-technology-in-azure"></a>在 Azure 中选择批处理技术

大数据解决方案经常使用长时间运行的批处理作业来筛选、聚合或者准备数据以用于分析。 这些作业通常涉及从可缩放的存储（例如，HDFS、Azure Data Lake Store 和 Azure 存储）读取源文件，对源文件进行处理，并将输出写入到可缩放存储中的新文件。

对这类批处理引擎的关键要求是需要它们能够扩展计算能力，以便处理大量数据。 但是，不同于实时处理，批处理会有延迟（从数据引入到计算结果之间的时间），延迟范围为数分钟到数小时。

## <a name="technology-choices-for-batch-processing"></a>批处理的技术选择

### <a name="azure-sql-data-warehouse"></a>Azure SQL 数据仓库

[SQL 数据仓库](/azure/sql-data-warehouse/)是分布式系统，旨在对大型数据执行分析。 它支持大规模并行处理 (MPP)，因此很适合用于运行高性能分析。 如果有大量的数据（超过 1 TB），并在运行受益于并行度的分析工作负荷，请考虑使用 SQL 数据仓库。

### <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

[Data Lake Analytics](/azure/data-lake-analytics/data-lake-analytics-overview) 是一项按需分析作业服务。 它经过优化，可以对存储在 Azure Data Lake Store 中的超大型数据集进行分布式处理。

- 语言：[U-SQL](/azure/data-lake-analytics/data-lake-analytics-u-sql-get-started)（包括 Python、R 和 C# 扩展）。
- 集成 Azure Data Lake Store、Azure 存储 Blob、Azure SQL 数据库和 SQL 数据仓库。
- 定价模型为“按作业”。

### <a name="hdinsight"></a>HDInsight

HDInsight 是一项托管型 Hadoop 服务。 可以使用它在 Azure 中部署和托管 Hadoop 群集。 对于批处理，可以使用 [Spark](/azure/hdinsight/spark/apache-spark-overview)、[Hive](/azure/hdinsight/hadoop/hdinsight-use-hive)、[Hive LLAP](/azure/hdinsight/interactive-query/apache-interactive-query-get-started)、[MapReduce](/azure/hdinsight/hadoop/hdinsight-use-mapreduce)。

- 语言：R、Python、Java、Scala、SQL
- 使用 Active Directory 进行的 Kerberos 身份验证、基于 Apache Ranger 的访问控制
- 可以完全控制 Hadoop 群集

### <a name="azure-databricks"></a>Azure Databricks

[Azure Databricks](/azure/azure-databricks/) 是基于 Apache Spark 的分析平台。 可以将它视为“Spark 即服务”。 它是在 Azure 平台上使用 Spark 的最轻松方式。

- 语言：R、Python、Java、Scala、Spark SQL
- 群集启动时间短，自动终止，自动缩放。
- 为你管理 Spark 群集。
- 内置集成 Azure Blob 存储、Azure Data Lake Storage (ADLS)、Azure SQL 数据仓库 (SQL DW) 等服务。 请参阅 [Data Sources](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html)（数据源）。
- 通过 Azure Active Directory 进行用户身份验证。
- 基于 Web 的 [Notebook](https://docs.azuredatabricks.net/user-guide/notebooks/index.html)，适用于协作和数据探索。
- 支持[启用了 GPU 的群集](https://docs.azuredatabricks.net/user-guide/clusters/gpu.html)

### <a name="azure-distributed-data-engineering-toolkit"></a>Azure 分布式数据工程工具包

[分布式数据工程工具包](https://github.com/azure/aztk) (AZTK) 是一项工具，适合在 Azure 中预配按需 Spark on Docker 群集。

AZTK 不是一项 Azure 服务， 而是一项带 CLI 和 Python SDK 界面的客户端工具，在 Azure Batch 基础上构建。 此选项可以在部署 Spark 群集时对基础结构进行最大程度的控制。

- 自带 Docker 映像。
- 使用低优先级 VM 可以获取 80% 的折扣。
- 混合模式群集，可以同时使用低优先级 VM 和专用 VM。
- 为 Azure Blob 存储和 Azure Data Lake 连接提供内置支持。

## <a name="key-selection-criteria"></a>关键选择条件

若要缩小选择范围，请先回答以下问题：

- 你希望使用托管服务还是由你管理自己的服务器？

- 希望以声明方式还是以命令方式创作批处理逻辑？

- 是否会爆发性地执行批处理？ 如果是，请考虑使用允许自动终止群集的选项或其定价模型为“按批处理作业”的选项。

- 是否需要随批处理查询关系数据存储，例如查找参考数据？ 如果是，请考虑使用允许查询外部关系存储的选项。

## <a name="capability-matrix"></a>功能矩阵

以下各表汇总了功能上的关键差异。

### <a name="general-capabilities"></a>常规功能

<!-- markdownlint-disable MD033 -->

| | Azure Data Lake Analytics | Azure SQL 数据仓库 | HDInsight | Azure Databricks |
| --- | --- | --- | --- | --- | --- |
| 是托管服务 | 是 | 是 | 是 <sup>1</sup> | 是 |
| 关系数据存储 | 是 | 是 | 否 | 否 |
| 定价模型 | 按批处理作业 | 按群集小时 | 按群集小时 | Databricks 单位<sup>2</sup> + 群集小时 |

[1] 使用手动配置和缩放。

[2] Databricks 单位 (DBU) 是表示每小时处理能力的单位。

### <a name="capabilities"></a>功能

| | Azure Data Lake Analytics | SQL 数据仓库 | 基于 Spark 的 HDInsight | 基于 Hive 的 HDInsight | 基于 Hive LLAP 的 HDInsight | Azure Databricks |
| --- | --- | --- | --- | --- | --- | --- |
| 自动缩放 | 否 | 否 | 否 | 否 | 否 | 是 |
| 横向扩展粒度  | 按作业 | 按群集 | 按群集 | 按群集 | 按群集 | 按群集 |
| 数据的内存中缓存 | 否 | 是 | 是 | 否 | 是 | 是 |
| 从外部关系存储进行查询 | 是 | 否 | 是 | 否 | 否 | 是 |
| Authentication  | Azure AD | SQL / Azure AD | 否 | Azure AD<sup>1</sup> | Azure AD<sup>1</sup> | Azure AD |
| 审核  | 是 | 是 | 否 | 是 <sup>1</sup> | 是 <sup>1</sup> | 是 |
| 行级别安全性 | 否 | 否 | 否 | 是 <sup>1</sup> | 是 <sup>1</sup> | 否 |
| 支持防火墙 | 是 | 是 | 是 | 是 <sup>2</sup> | 是 <sup>2</sup> | 否 |
| 动态数据掩码 | 否 | 否 | 否 | 是 <sup>1</sup> | 是 <sup>1</sup> | 否 |

<!-- markdownlint-enable MD033 -->

[1] 需要使用[已加入域的 HDInsight 群集](/azure/hdinsight/domain-joined/apache-domain-joined-introduction)。

[2] [在 Azure 虚拟网络中使用](/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)时受支持。
