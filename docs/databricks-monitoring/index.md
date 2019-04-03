---
title: 使用 Azure Monitor 监视 Azure Databricks
description: 一种 scala 库，用于在 Azure Log Analytics 中启用 Azure Databricks 的监视功能
author: petertaylor9999
ms.date: 03/26/2019
ms.openlocfilehash: 6d3d17b2e49919aea7bb08f59e19032c1c8bdafd
ms.sourcegitcommit: 9854bd27fb5cf92041bbfb743d43045cd3552a69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58503259"
---
# <a name="monitoring-azure-databricks-with-azure-monitor"></a>使用 Azure Monitor 监视 Azure Databricks

[Azure Databricks](/azure/azure-databricks/) 是一种功能强大的基于 [Apache Spark](https://spark.apache.org/) 的快速分析服务，用于轻松地快速开发和部署大数据分析和人工智能 (AI) 解决方案。 许多用户在其 Azure Databricks 解决方案中充分利用了笔记本的简单性。 对于需要更可靠的计算选项的用户，Azure Databricks 支持分布式执行自定义应用程序代码。

监视是任何生产级解决方案的关键部分，Azure Databricks 提供了可靠的功能，用于监视自定义应用程序指标、流式处理查询事件和应用程序日志消息。 Azure Databricks 可以将此监视数据发送到不同的日志记录服务。

以下文章说明如何将监视数据从 Azure Databricks 发送到 [Azure Monitor](/azure/azure-monitor/overview)（Azure 的监视数据平台）。 应按顺序遵循以下文章中的说明执行操作。

1. [配置 Azure Databricks 以将指标发送到 Azure Monitor](./configure-cluster.md)
1. [将 Azure Databricks 应用程序日志发送到 Azure Monitor](./application-logs.md)
1. [使用仪表板将 Azure Databricks 指标可视化](./dashboards.md)

这些文章随附的代码库扩展了 Azure Databricks 的核心监视功能，以便将 Spark 指标、事件和日志记录信息发送到 Azure Monitor。

这些文章和随附的代码库的受众是 Apache Spark 和 Azure Databricks 解决方案开发人员。 代码必须内置于 Java 存档 (JAR) 文件中，并随后部署到 Azure Databricks 群集。 代码是 [Scala](https://www.scala-lang.org/) 和 Java 的组合，以及一组用于生成输出 JAR 文件的对应 [Maven](https://maven.apache.org) 项目对象模型 (POM) 文件。 建议你务必先了解 Java、Scala 和 Maven。

## <a name="next-steps"></a>后续步骤

首先生成代码库并将其部署到 Azure Databricks 群集。

> [!div class="nextstepaction"]
> [配置 Azure Databricks 以将指标发送到 Azure Monitor](./configure-cluster.md)