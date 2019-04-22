---
title: 使用 Azure Monitor 监视 Azure Databricks
description: 一种 scala 库，用于在 Azure Log Analytics 中启用 Azure Databricks 的监视功能
author: petertaylor9999
ms.date: 03/26/2019
ms.openlocfilehash: 93798ccf74735a880eab2999008b1495e6a63e10
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59640526"
---
# <a name="monitoring-azure-databricks"></a><span data-ttu-id="f3735-103">监视 Azure Databricks</span><span class="sxs-lookup"><span data-stu-id="f3735-103">Monitoring Azure Databricks</span></span>

<span data-ttu-id="f3735-104">[Azure Databricks](/azure/azure-databricks/) 是一种功能强大的基于 [Apache Spark](https://spark.apache.org/) 的快速分析服务，用于轻松地快速开发和部署大数据分析和人工智能 (AI) 解决方案。</span><span class="sxs-lookup"><span data-stu-id="f3735-104">[Azure Databricks](/azure/azure-databricks/) is a fast, powerful [Apache Spark](https://spark.apache.org/)–based analytics service that makes it easy to rapidly develop and deploy big data analytics and artificial intelligence (AI) solutions.</span></span> <span data-ttu-id="f3735-105">许多用户在其 Azure Databricks 解决方案中充分利用了笔记本的简单性。</span><span class="sxs-lookup"><span data-stu-id="f3735-105">Many users take advantage of the simplicity of notebooks in their Azure Databricks solutions.</span></span> <span data-ttu-id="f3735-106">对于需要更可靠的计算选项的用户，Azure Databricks 支持分布式执行自定义应用程序代码。</span><span class="sxs-lookup"><span data-stu-id="f3735-106">For users that require more robust computing options, Azure Databricks supports the distributed execution of custom application code.</span></span>

<span data-ttu-id="f3735-107">监视是任何生产级解决方案的关键部分，Azure Databricks 提供了可靠的功能，用于监视自定义应用程序指标、流式处理查询事件和应用程序日志消息。</span><span class="sxs-lookup"><span data-stu-id="f3735-107">Monitoring is a critical part of any production-level solution, and Azure Databricks offers robust functionality for monitoring custom application metrics, streaming query events, and application log messages.</span></span> <span data-ttu-id="f3735-108">Azure Databricks 可以将此监视数据发送到不同的日志记录服务。</span><span class="sxs-lookup"><span data-stu-id="f3735-108">Azure Databricks can send this monitoring data to different logging services.</span></span>

<span data-ttu-id="f3735-109">以下文章说明如何将监视数据从 Azure Databricks 发送到 [Azure Monitor](/azure/azure-monitor/overview)（Azure 的监视数据平台）。</span><span class="sxs-lookup"><span data-stu-id="f3735-109">The following articles show how to send monitoring data from Azure Databricks to [Azure Monitor](/azure/azure-monitor/overview), the monitoring data platform for Azure.</span></span> <span data-ttu-id="f3735-110">应按顺序遵循以下文章中的说明执行操作。</span><span class="sxs-lookup"><span data-stu-id="f3735-110">You should follow these articles in order.</span></span>

1. [<span data-ttu-id="f3735-111">配置 Azure Databricks 以将指标发送到 Azure Monitor</span><span class="sxs-lookup"><span data-stu-id="f3735-111">Configure Azure Databricks to send metrics to Azure Monitor</span></span>](./configure-cluster.md)
1. [<span data-ttu-id="f3735-112">将 Azure Databricks 应用程序日志发送到 Azure Monitor</span><span class="sxs-lookup"><span data-stu-id="f3735-112">Send Azure Databricks application logs to Azure Monitor</span></span>](./application-logs.md)
1. [<span data-ttu-id="f3735-113">使用仪表板将 Azure Databricks 指标可视化</span><span class="sxs-lookup"><span data-stu-id="f3735-113">Use dashboards to visualize Azure Databricks metrics</span></span>](./dashboards.md)

<span data-ttu-id="f3735-114">这些文章随附的代码库扩展了 Azure Databricks 的核心监视功能，以便将 Spark 指标、事件和日志记录信息发送到 Azure Monitor。</span><span class="sxs-lookup"><span data-stu-id="f3735-114">The code library that accompanies these articles extends the core monitoring functionality of Azure Databricks to send Spark metrics, events, and logging information to Azure Monitor.</span></span>

<span data-ttu-id="f3735-115">这些文章和随附的代码库的受众是 Apache Spark 和 Azure Databricks 解决方案开发人员。</span><span class="sxs-lookup"><span data-stu-id="f3735-115">The audience for these articles and the accompanying code library are Apache Spark and Azure Databricks solution developers.</span></span> <span data-ttu-id="f3735-116">代码必须内置于 Java 存档 (JAR) 文件中，并随后部署到 Azure Databricks 群集。</span><span class="sxs-lookup"><span data-stu-id="f3735-116">The code must be built into Java Archive (JAR) files and then deployed to an Azure Databricks cluster.</span></span> <span data-ttu-id="f3735-117">代码是 [Scala](https://www.scala-lang.org/) 和 Java 的组合，以及一组用于生成输出 JAR 文件的对应 [Maven](https://maven.apache.org) 项目对象模型 (POM) 文件。</span><span class="sxs-lookup"><span data-stu-id="f3735-117">The code is a combination of [Scala](https://www.scala-lang.org/) and Java, with a corresponding set of [Maven](https://maven.apache.org) project object model (POM) files to build the output JAR files.</span></span> <span data-ttu-id="f3735-118">建议先了解 Java、Scala 和 Maven，因为这是必备要求。</span><span class="sxs-lookup"><span data-stu-id="f3735-118">Understanding of Java, Scala, and Maven are recommended as prerequisites.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f3735-119">后续步骤</span><span class="sxs-lookup"><span data-stu-id="f3735-119">Next steps</span></span>

<span data-ttu-id="f3735-120">首先生成代码库并将其部署到 Azure Databricks 群集。</span><span class="sxs-lookup"><span data-stu-id="f3735-120">Start by building the code library and deploying it to your Azure Databricks cluster.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="f3735-121">配置 Azure Databricks 以将指标发送到 Azure Monitor</span><span class="sxs-lookup"><span data-stu-id="f3735-121">Configure Azure Databricks to send metrics to Azure Monitor</span></span>](./configure-cluster.md)