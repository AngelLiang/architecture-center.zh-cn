---
title: 将混合 ETL 与现有本地 SSIS 和 Azure 数据工厂配合使用
titleSuffix: Azure Example Scenarios
description: 将混合 ETL 与现有本地 SQL Server Integration Services (SSIS) 部署和 Azure 数据工厂配合使用。
author: alhieng
ms.date: 09/20/2018
ms.custom: tsp-team
ms.openlocfilehash: a2ca3817ed172e6d2332a92f68970ea2a5ad8f6c
ms.sourcegitcommit: 1f4cdb08fe73b1956e164ad692f792f9f635b409
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2019
ms.locfileid: "54110384"
---
# <a name="hybrid-etl-with-existing-on-premises-ssis-and-azure-data-factory"></a><span data-ttu-id="88b28-103">将混合 ETL 与现有本地 SSIS 和 Azure 数据工厂配合使用</span><span class="sxs-lookup"><span data-stu-id="88b28-103">Hybrid ETL with existing on-premises SSIS and Azure Data Factory</span></span>

<span data-ttu-id="88b28-104">将 SQL Server 数据库迁移到云的组织可以实现巨大的成本节省、性能提升以及更高的灵活性和可伸缩性。</span><span class="sxs-lookup"><span data-stu-id="88b28-104">Organizations that migrate their SQL Server databases to the cloud can realize tremendous cost savings, performance gains, added flexibility, and greater scalability.</span></span> <span data-ttu-id="88b28-105">但是，重新设计通过 SQL Server Integration Services (SSIS) 构建的现有提取、转换和加载 (ETL) 过程可能会给迁移造成阻碍。</span><span class="sxs-lookup"><span data-stu-id="88b28-105">However, reworking existing extract, transform, and load (ETL) processes built with SQL Server Integration Services (SSIS) can be a migration roadblock.</span></span> <span data-ttu-id="88b28-106">在其他情况下，数据加载过程需要复杂的逻辑和/或特定的数据工具组件，而 Azure 数据工厂 v2 尚不支持这些组件。</span><span class="sxs-lookup"><span data-stu-id="88b28-106">In other cases, the data load process requires complex logic and/or specific data tool components that are not yet supported by Azure Data Factory v2.</span></span> <span data-ttu-id="88b28-107">常用的 SSIS 功能包括模糊查找和模糊分组转换、变更数据捕获 (CDC)、渐变维度 (SCD) 以及数据质量服务 (DQS)。</span><span class="sxs-lookup"><span data-stu-id="88b28-107">Commonly used SSIS capabilities include Fuzzy Lookup and Fuzzy Grouping transformations, Change Data Capture (CDC), Slowly Changing Dimensions (SCD), and Data Quality Services (DQS).</span></span>

<span data-ttu-id="88b28-108">为了简化现有 SQL 数据库的直接迁移，混合 ETL 方法可能是最合适的选项。</span><span class="sxs-lookup"><span data-stu-id="88b28-108">To facilitate a lift-and-shift migration of an existing SQL database, a hybrid ETL approach may be the most suitable option.</span></span> <span data-ttu-id="88b28-109">混合方法使用数据工厂作为主要业务流程引擎，但会持续利用现有的 SSIS 包来清理数据和处理本地资源。</span><span class="sxs-lookup"><span data-stu-id="88b28-109">A hybrid approach uses Data Factory as the primary orchestration engine, but continues to leverage existing SSIS packages to clean data and work with on-premise resources.</span></span> <span data-ttu-id="88b28-110">此方法使用数据工厂 SQL Server 集成运行时 (IR) 将现有数据库直接迁移到云中，同时使用现有的代码和 SSIS 包。</span><span class="sxs-lookup"><span data-stu-id="88b28-110">This approach uses the Data Factory SQL Server Integrated Runtime (IR) to enable a lift-and-shift of existing databases into the cloud, while using existing code and SSIS packages.</span></span>

<span data-ttu-id="88b28-111">本示例方案与要将数据库迁移到云、正在考虑使用数据工厂作为主要的基于云的 ETL 引擎，同时要将现有 SSIS 包整合到新的云数据工作流的组织相关。</span><span class="sxs-lookup"><span data-stu-id="88b28-111">This example scenario is relevant to organizations that are moving databases to the cloud and are considering using Data Factory as their primary cloud-based ETL engine while incorporating existing SSIS packages into their new cloud data workflow.</span></span> <span data-ttu-id="88b28-112">许多组织已投入大笔资金来为特定数据任务开发 SSIS ETL 包。</span><span class="sxs-lookup"><span data-stu-id="88b28-112">Many organizations have significant invested in developing SSIS ETL packages for specific data tasks.</span></span> <span data-ttu-id="88b28-113">重新编写这些包可能颇费周折。</span><span class="sxs-lookup"><span data-stu-id="88b28-113">Rewriting these packages can be daunting.</span></span> <span data-ttu-id="88b28-114">此外，许多现有的代码包依赖于本地资源，导致无法迁移到云。</span><span class="sxs-lookup"><span data-stu-id="88b28-114">Also, many existing code packages have dependencies on local resources, preventing migration to the cloud.</span></span>

<span data-ttu-id="88b28-115">数据工厂允许客户利用其现有的 ETL 包，同时可以遏制在本地 ETL 开发方面的进一步投资。</span><span class="sxs-lookup"><span data-stu-id="88b28-115">Data Factory lets customers take advantage of their existing ETL packages while limiting further investment in on-premises ETL development.</span></span> <span data-ttu-id="88b28-116">本示例讨论有关通过 Azure 数据工厂 v2 在新的云数据工作流中利用现有 SSIS 包的潜在用例。</span><span class="sxs-lookup"><span data-stu-id="88b28-116">This example discusses potential use cases for leveraging existing SSIS packages as part of a new cloud data workflow using Azure Data Factory v2.</span></span>

## <a name="potential-use-cases"></a><span data-ttu-id="88b28-117">可能的用例</span><span class="sxs-lookup"><span data-stu-id="88b28-117">Potential use cases</span></span>

<span data-ttu-id="88b28-118">一直以来，许多 SQL Server 数据专家都会选择 SSIS 作为 ETL 工具来转换和加载数据。</span><span class="sxs-lookup"><span data-stu-id="88b28-118">Traditionally, SSIS has been the ETL tool of choice for many SQL Server data professionals for data transformation and loading.</span></span> <span data-ttu-id="88b28-119">有时，会使用特定的 SSIS 功能或第三方插件来加速开发工作。</span><span class="sxs-lookup"><span data-stu-id="88b28-119">Sometimes, specific SSIS features or third-party plugging components have been used to accelerate the development effort.</span></span> <span data-ttu-id="88b28-120">可能无法替换或重新开发这些包，导致客户无法将其数据库迁移到云中。</span><span class="sxs-lookup"><span data-stu-id="88b28-120">Replacement or redevelopment of these packages may not be an option, which prevents customers from migrating their databases to the cloud.</span></span> <span data-ttu-id="88b28-121">客户正在寻求一些可将其现有数据库迁移到云，并可利用其现有 SSIS 包的低影响性方法。</span><span class="sxs-lookup"><span data-stu-id="88b28-121">Customers are looking for low impact approaches to migrating their existing databases to the cloud and taking advantage of their existing SSIS packages.</span></span>

<span data-ttu-id="88b28-122">下面列出了几个潜在的本地用例：</span><span class="sxs-lookup"><span data-stu-id="88b28-122">Several potential on-premises use cases are listed below:</span></span>

- <span data-ttu-id="88b28-123">将网络路由器日志加载到数据库进行分析。</span><span class="sxs-lookup"><span data-stu-id="88b28-123">Loading network router logs to a database for analysis.</span></span>
- <span data-ttu-id="88b28-124">准备人力资源招聘数据进行分析报告。</span><span class="sxs-lookup"><span data-stu-id="88b28-124">Preparing human resources employment data for analytical reporting.</span></span>
- <span data-ttu-id="88b28-125">将产品和销售数据载入数据仓库进行销售预测。</span><span class="sxs-lookup"><span data-stu-id="88b28-125">Loading product and sales data into a data warehouse for sales forecasting.</span></span>
- <span data-ttu-id="88b28-126">自动加载财务和会计方面的运营数据存储或数据仓库。</span><span class="sxs-lookup"><span data-stu-id="88b28-126">Automating loading of operational data stores or data warehouses for finance and accounting.</span></span>

## <a name="architecture"></a><span data-ttu-id="88b28-127">体系结构</span><span class="sxs-lookup"><span data-stu-id="88b28-127">Architecture</span></span>

![使用 Azure 数据工厂的混合 ETL 过程的体系结构概览][architecture-diagram]

1. <span data-ttu-id="88b28-129">数据源自 Blob 存储，并载入数据工厂。</span><span class="sxs-lookup"><span data-stu-id="88b28-129">Data is sourced from Blob storage into Data Factory.</span></span>
2. <span data-ttu-id="88b28-130">数据工厂管道调用某个存储过程，以通过集成运行时执行本地托管的 SSIS 作业。</span><span class="sxs-lookup"><span data-stu-id="88b28-130">The Data Factory pipeline invokes a stored procedure to execute an SSIS job hosted on-premises via the Integrated Runtime.</span></span>
3. <span data-ttu-id="88b28-131">执行数据清理作业，以准备下游使用的数据。</span><span class="sxs-lookup"><span data-stu-id="88b28-131">The data cleansing jobs are executed to prepare the data for downstream consumption.</span></span>
4. <span data-ttu-id="88b28-132">数据清理任务成功完成后，执行复制任务将清理数据载入 Azure。</span><span class="sxs-lookup"><span data-stu-id="88b28-132">Once the data cleansing task completes successfully, a copy task is executed to load the clean data into Azure.</span></span>
5. <span data-ttu-id="88b28-133">然后，将清理数据载入 SQL 数据仓库中的表。</span><span class="sxs-lookup"><span data-stu-id="88b28-133">The clean data is then loaded into tables in the SQL Data Warehouse.</span></span>

### <a name="components"></a><span data-ttu-id="88b28-134">组件</span><span class="sxs-lookup"><span data-stu-id="88b28-134">Components</span></span>

- <span data-ttu-id="88b28-135">[Blob 存储][docs-blob-storage]用于存储文件，并充当数据工厂检索数据的源。</span><span class="sxs-lookup"><span data-stu-id="88b28-135">[Blob storage][docs-blob-storage] is used to store files and as a source for Data Factory to retrieve data.</span></span>
- <span data-ttu-id="88b28-136">[SQL Server Integration Services][docs-ssis] 包含用于执行任务特定的工作负荷的本地 ETL 包。</span><span class="sxs-lookup"><span data-stu-id="88b28-136">[SQL Server Integration Services][docs-ssis] contains the on-premises ETL packages used to execute task-specific workloads.</span></span>
- <span data-ttu-id="88b28-137">[Azure 数据工厂][docs-data-factory]是云业务流程引擎，它从多个源提取数据，合并、协调数据并将其载入数据仓库。</span><span class="sxs-lookup"><span data-stu-id="88b28-137">[Azure Data Factory][docs-data-factory] is the cloud orchestration engine that takes data from multiple sources and combines, orchestrates, and loads the data into a data warehouse.</span></span>
- <span data-ttu-id="88b28-138">[SQL 数据仓库][docs-sql-data-warehouse]在云中将数据集中化，以便能够使用标准的 ANSI SQL 查询轻松访问数据。</span><span class="sxs-lookup"><span data-stu-id="88b28-138">[SQL Data Warehouse][docs-sql-data-warehouse] centralizes data in the cloud for easy access using standard ANSI SQL queries.</span></span>

### <a name="alternatives"></a><span data-ttu-id="88b28-139">备选项</span><span class="sxs-lookup"><span data-stu-id="88b28-139">Alternatives</span></span>

<span data-ttu-id="88b28-140">数据工厂可以调用使用其他技术（例如 Databricks Notebook、Python 脚本或虚拟机中运行的 SSIS 实例）实现的数据清理过程。</span><span class="sxs-lookup"><span data-stu-id="88b28-140">Data Factory could invoke data cleansing procedures implemented using other technologies, such as a Databricks notebook, Python script, or SSIS instance running in a virtual machine.</span></span> <span data-ttu-id="88b28-141">[为 Azure SSIS 集成运行时安装付费或授权的自定义组件](/azure/data-factory/how-to-develop-azure-ssis-ir-licensed-components)可能是混合方法的可行替代方案。</span><span class="sxs-lookup"><span data-stu-id="88b28-141">[Installing paid or licensed custom components for the Azure-SSIS integration runtime](/azure/data-factory/how-to-develop-azure-ssis-ir-licensed-components) may be a viable alternative to the hybrid approach.</span></span>

## <a name="considerations"></a><span data-ttu-id="88b28-142">注意事项</span><span class="sxs-lookup"><span data-stu-id="88b28-142">Considerations</span></span>

<span data-ttu-id="88b28-143">集成运行时 (IR) 支持两种模型：自承载 IR 或 Azure 承载的 IR。</span><span class="sxs-lookup"><span data-stu-id="88b28-143">The Integrated Runtime (IR) supports two models: self-hosted IR or Azure-hosted IR.</span></span> <span data-ttu-id="88b28-144">首先必须在这两个选项之间做出抉择。</span><span class="sxs-lookup"><span data-stu-id="88b28-144">You first must decide between these two options.</span></span> <span data-ttu-id="88b28-145">自托管更具成本效益，但会增大维护和管理开销。</span><span class="sxs-lookup"><span data-stu-id="88b28-145">Self-hosting is more cost effective but has more overhead for maintenance and management.</span></span> <span data-ttu-id="88b28-146">有关详细信息，请参阅[自承载 IR](/azure/data-factory/concepts-integration-runtime#self-hosted-integration-runtime)。</span><span class="sxs-lookup"><span data-stu-id="88b28-146">For more information, see [Self-hosted IR](/azure/data-factory/concepts-integration-runtime#self-hosted-integration-runtime).</span></span> <span data-ttu-id="88b28-147">在确定要使用哪种 IR 时如需帮助，请参阅[确定要使用哪种 IR](/azure/data-factory/concepts-integration-runtime#determining-which-ir-to-use)。</span><span class="sxs-lookup"><span data-stu-id="88b28-147">If you need help determining which IR to use, see [Determining which IR to use](/azure/data-factory/concepts-integration-runtime#determining-which-ir-to-use).</span></span>

<span data-ttu-id="88b28-148">对于 Azure 承载方法，应确定处理数据所需的处理能力。</span><span class="sxs-lookup"><span data-stu-id="88b28-148">For the Azure-hosted approach, you should decide how much power is required to process your data.</span></span> <span data-ttu-id="88b28-149">Azure 承载配置允许在执行配置步骤期间选择 VM 大小。</span><span class="sxs-lookup"><span data-stu-id="88b28-149">The Azure-hosted configuration allows you to select the VM size as part of the configuration steps.</span></span> <span data-ttu-id="88b28-150">若要详细了解如何选择 VM 大小，请参阅 [VM 性能注意事项](/azure/cloud-services/cloud-services-sizes-specs#performance-considerations)。</span><span class="sxs-lookup"><span data-stu-id="88b28-150">To learn more about selecting VM sizes, see [VM performance considerations](/azure/cloud-services/cloud-services-sizes-specs#performance-considerations).</span></span>

<span data-ttu-id="88b28-151">如果已有包含本地依赖项（例如，无法从 Azure 访问的数据源或文件）的现有 SSIS 包，则决策就容易得多。</span><span class="sxs-lookup"><span data-stu-id="88b28-151">The decision is much easier when you already have existing SSIS packages that have on-premise dependencies such as data sources or files that are not accessible from Azure.</span></span> <span data-ttu-id="88b28-152">在这种情况下，唯一的选项是自承载 IR。</span><span class="sxs-lookup"><span data-stu-id="88b28-152">In this scenario, your only option is the self-hosted IR.</span></span> <span data-ttu-id="88b28-153">通过此方法可以最灵活地利用云作为业务流程引擎，而无需重新编写现有的包。</span><span class="sxs-lookup"><span data-stu-id="88b28-153">This approach provides the most flexibility to leverage the cloud as the orchestration engine, without having to rewrite existing packages.</span></span>

<span data-ttu-id="88b28-154">最终的意图是将处理的数据迁移到云中以做进一步的优化，或者与云中存储的其他数据合并。</span><span class="sxs-lookup"><span data-stu-id="88b28-154">Ultimately, the intent is to move the processed data into the cloud for further refinement or combining with other data stored in the cloud.</span></span> <span data-ttu-id="88b28-155">在设计过程中，应跟踪数据工厂管道中使用的活动数。</span><span class="sxs-lookup"><span data-stu-id="88b28-155">As part of the design process, keep track of the number of activities used in the Data Factory pipelines.</span></span> <span data-ttu-id="88b28-156">有关详细信息，请参阅 [Azure 数据工厂中的管道和活动](/azure/data-factory/concepts-pipelines-activities)。</span><span class="sxs-lookup"><span data-stu-id="88b28-156">For more information, see [Pipelines and activities in Azure Data Factory](/azure/data-factory/concepts-pipelines-activities).</span></span>

## <a name="pricing"></a><span data-ttu-id="88b28-157">定价</span><span class="sxs-lookup"><span data-stu-id="88b28-157">Pricing</span></span>

<span data-ttu-id="88b28-158">使用数据工厂可以经济高效地协调云中的数据移动。</span><span class="sxs-lookup"><span data-stu-id="88b28-158">Data Factory is a cost-effective way to orchestrate data movement in the cloud.</span></span> <span data-ttu-id="88b28-159">费用取决于多种因素。</span><span class="sxs-lookup"><span data-stu-id="88b28-159">The cost is based on the several factors.</span></span>

- <span data-ttu-id="88b28-160">管道执行的数目</span><span class="sxs-lookup"><span data-stu-id="88b28-160">Number of pipeline executions</span></span>
- <span data-ttu-id="88b28-161">管道中使用的实体/活动数目</span><span class="sxs-lookup"><span data-stu-id="88b28-161">Number of entities/activities used within the pipeline</span></span>
- <span data-ttu-id="88b28-162">监视操作的数目</span><span class="sxs-lookup"><span data-stu-id="88b28-162">Number of monitoring operations</span></span>
- <span data-ttu-id="88b28-163">集成运行（Azure 承载的 IR 或自承载 IR）的数目</span><span class="sxs-lookup"><span data-stu-id="88b28-163">Number of Integration Runs (Azure-hosted IR or self-hosted IR)</span></span>

<span data-ttu-id="88b28-164">数据工厂使用基于消耗量的计费模式。</span><span class="sxs-lookup"><span data-stu-id="88b28-164">Data Factory uses consumption-based billing.</span></span> <span data-ttu-id="88b28-165">因此，只会在管道执行和监视期间才会产生费用。</span><span class="sxs-lookup"><span data-stu-id="88b28-165">Therefore, cost is only incurred during pipeline executions and monitoring.</span></span> <span data-ttu-id="88b28-166">执行基本管道的费用低至 50 美分，而监视的费用低至 25 美分。</span><span class="sxs-lookup"><span data-stu-id="88b28-166">The execution of a basic pipeline would cost as little as 50 cents and the monitoring as little as 25 cents.</span></span> <span data-ttu-id="88b28-167">可以使用 [Azure 成本计算器](https://azure.microsoft.com/pricing/calculator/)根据特定的工作负荷估算更准确的费用。</span><span class="sxs-lookup"><span data-stu-id="88b28-167">The [Azure cost calculator](https://azure.microsoft.com/pricing/calculator/) can be used to create a more accurate estimate based on your specific workload.</span></span>

<span data-ttu-id="88b28-168">运行混合 ETL 工作负荷时，必须考虑到用于承载 SSIS 包的虚拟机的费用。</span><span class="sxs-lookup"><span data-stu-id="88b28-168">When running a hybrid ETL workload, you must factor in the cost of the virtual machine used to host your SSIS packages.</span></span> <span data-ttu-id="88b28-169">此费用基于 VM 大小。VM 大小范围为 D1v2（单核，3.5 GB RAM，50 GB 磁盘）到 E64V3（64 核，432 GB RAM，1600 GB 磁盘）。</span><span class="sxs-lookup"><span data-stu-id="88b28-169">This cost is based on the size of the VM ranging from a D1v2 (1 core, 3.5 GB RAM, 50 GB Disk) to E64V3 (64 cores, 432 GB RAM, 1600 GB disk).</span></span> <span data-ttu-id="88b28-170">如需有关选择适当 VM 大小的进一步指导，请参阅 [VM 性能注意事项](/azure/cloud-services/cloud-services-sizes-specs#performance-considerations)。</span><span class="sxs-lookup"><span data-stu-id="88b28-170">If you need further guidance on selection the appropriate VM size, see [VM performance considerations](/azure/cloud-services/cloud-services-sizes-specs#performance-considerations).</span></span>

## <a name="next-steps"></a><span data-ttu-id="88b28-171">后续步骤</span><span class="sxs-lookup"><span data-stu-id="88b28-171">Next Steps</span></span>

- <span data-ttu-id="88b28-172">详细了解 [Azure 数据工厂](https://azure.microsoft.com/services/data-factory/)。</span><span class="sxs-lookup"><span data-stu-id="88b28-172">Learn more about [Azure Data Factory](https://azure.microsoft.com/services/data-factory/).</span></span>
- <span data-ttu-id="88b28-173">遵循[分步教程](/azure/data-factory/#step-by-step-tutorials)开始使用 Azure 数据工厂。</span><span class="sxs-lookup"><span data-stu-id="88b28-173">Get started with Azure Data Factory by following the [Step-by-step tutorial](/azure/data-factory/#step-by-step-tutorials).</span></span>
- <span data-ttu-id="88b28-174">[在 Azure 数据工厂中预配 Azure-SSIS Integration Runtime](/azure/data-factory/tutorial-deploy-ssis-packages-azure)。</span><span class="sxs-lookup"><span data-stu-id="88b28-174">[Provision the Azure-SSIS Integration Runtime in Azure Data Factory](/azure/data-factory/tutorial-deploy-ssis-packages-azure).</span></span>

<!-- links -->
[architecture-diagram]: ./media/architecture-diagram-hybrid-etl-with-adf.png
[small-pricing]: https://azure.com/e/
[medium-pricing]: https://azure.com/e/
[large-pricing]: https://azure.com/e/
[availability]: /azure/architecture/checklist/availability
[resource-groups]: /azure/azure-resource-manager/resource-group-overview
[resiliency]: /azure/architecture/resiliency/
[security]: /azure/security/
[scalability]: /azure/architecture/checklist/scalability
[docs-blob-storage]: /azure/storage/blobs/
[docs-data-factory]: /azure/data-factory/introduction
[docs-resource-groups]: /azure/azure-resource-manager/resource-group-overview
[docs-ssis]: /sql/integration-services/sql-server-integration-services
[docs-sql-data-warehouse]: /azure/sql-data-warehouse/sql-data-warehouse-overview-what-is
