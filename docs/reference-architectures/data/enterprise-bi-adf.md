---
title: 自动化企业商业智能 (BI)
titleSuffix: Azure Reference Architectures
description: 将 Azure 数据工厂与 SQL 数据仓库配合使用，在 Azure 中自动完成提取、加载和转换 (ELT) 工作流。
author: MikeWasson
ms.date: 11/06/2018
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seodec18
ms.openlocfilehash: 020c401e9db85b76fd48c6df9be9c80d2ba5c7e4
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54481467"
---
# <a name="automated-enterprise-bi-with-sql-data-warehouse-and-azure-data-factory"></a><span data-ttu-id="9b50c-103">将自动化企业 BI 与 SQL 数据仓库和 Azure 数据工厂配合使用</span><span class="sxs-lookup"><span data-stu-id="9b50c-103">Automated enterprise BI with SQL Data Warehouse and Azure Data Factory</span></span>

<span data-ttu-id="9b50c-104">此参考体系结构演示如何在[提取、加载和转换 (ELT)](../../data-guide/relational-data/etl.md#extract-load-and-transform-elt) 管道中执行增量加载。</span><span class="sxs-lookup"><span data-stu-id="9b50c-104">This reference architecture shows how to perform incremental loading in an [extract, load, and transform (ELT)](../../data-guide/relational-data/etl.md#extract-load-and-transform-elt) pipeline.</span></span> <span data-ttu-id="9b50c-105">它使用 Azure 数据工厂将 ELT 管道自动化。</span><span class="sxs-lookup"><span data-stu-id="9b50c-105">It uses Azure Data Factory to automate the ELT pipeline.</span></span> <span data-ttu-id="9b50c-106">该管道以增量方式将最新的 OLTP 数据从本地 SQL Server 数据库移入 SQL 数据仓库。</span><span class="sxs-lookup"><span data-stu-id="9b50c-106">The pipeline incrementally moves the latest OLTP data from an on-premises SQL Server database into SQL Data Warehouse.</span></span> <span data-ttu-id="9b50c-107">事务数据将转换为表格模型以供分析。</span><span class="sxs-lookup"><span data-stu-id="9b50c-107">Transactional data is transformed into a tabular model for analysis.</span></span>

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2Gnz2]

<span data-ttu-id="9b50c-108">[GitHub][github] 中提供了本体系结构的参考实现。</span><span class="sxs-lookup"><span data-stu-id="9b50c-108">A reference implementation for this architecture is available on [GitHub][github].</span></span>

![将自动化企业 BI 与 SQL 数据仓库和 Azure 数据工厂配合使用的体系结构图](./images/enterprise-bi-sqldw-adf.png)

<span data-ttu-id="9b50c-110">此体系结构构建在[企业 BI 与 SQL 数据仓库](./enterprise-bi-sqldw.md)中所述的体系结构基础之上，但添加了一些对企业数据仓库方案而言非常重要的功能。</span><span class="sxs-lookup"><span data-stu-id="9b50c-110">This architecture builds on the one shown in [Enterprise BI with SQL Data Warehouse](./enterprise-bi-sqldw.md), but adds some features that are important for enterprise data warehousing scenarios.</span></span>

- <span data-ttu-id="9b50c-111">使用数据工厂将管道自动化。</span><span class="sxs-lookup"><span data-stu-id="9b50c-111">Automation of the pipeline using Data Factory.</span></span>
- <span data-ttu-id="9b50c-112">增量加载。</span><span class="sxs-lookup"><span data-stu-id="9b50c-112">Incremental loading.</span></span>
- <span data-ttu-id="9b50c-113">集成多个数据源。</span><span class="sxs-lookup"><span data-stu-id="9b50c-113">Integrating multiple data sources.</span></span>
- <span data-ttu-id="9b50c-114">加载地理空间数据和图像等二进制数据。</span><span class="sxs-lookup"><span data-stu-id="9b50c-114">Loading binary data such as geospatial data and images.</span></span>

## <a name="architecture"></a><span data-ttu-id="9b50c-115">体系结构</span><span class="sxs-lookup"><span data-stu-id="9b50c-115">Architecture</span></span>

<span data-ttu-id="9b50c-116">该体系结构包括以下组件。</span><span class="sxs-lookup"><span data-stu-id="9b50c-116">The architecture consists of the following components.</span></span>

### <a name="data-sources"></a><span data-ttu-id="9b50c-117">数据源</span><span class="sxs-lookup"><span data-stu-id="9b50c-117">Data sources</span></span>

<span data-ttu-id="9b50c-118">**本地 SQL Server**。</span><span class="sxs-lookup"><span data-stu-id="9b50c-118">**On-premises SQL Server**.</span></span> <span data-ttu-id="9b50c-119">源数据位于本地的 SQL Server 数据库中。</span><span class="sxs-lookup"><span data-stu-id="9b50c-119">The source data is located in a SQL Server database on premises.</span></span> <span data-ttu-id="9b50c-120">为了模拟本地环境，此体系结构的部署脚本将在 Azure 中预配一个装有 SQL Server 的虚拟机。</span><span class="sxs-lookup"><span data-stu-id="9b50c-120">To simulate the on-premises environment, the deployment scripts for this architecture provision a virtual machine in Azure with SQL Server installed.</span></span> <span data-ttu-id="9b50c-121">[Wide World Importers OLTP 示例数据库][wwi]用作源数据库。</span><span class="sxs-lookup"><span data-stu-id="9b50c-121">The [Wide World Importers OLTP sample database][wwi] is used as the source database.</span></span>

<span data-ttu-id="9b50c-122">**外部数据**。</span><span class="sxs-lookup"><span data-stu-id="9b50c-122">**External data**.</span></span> <span data-ttu-id="9b50c-123">数据仓库的常见方案是集成多个数据源。</span><span class="sxs-lookup"><span data-stu-id="9b50c-123">A common scenario for data warehouses is to integrate multiple data sources.</span></span> <span data-ttu-id="9b50c-124">此参考体系结构加载一个包含不同年份的城市人口的外部数据集，并将它与 OLTP 数据库中的数据集成。</span><span class="sxs-lookup"><span data-stu-id="9b50c-124">This reference architecture loads an external data set that contains city populations by year, and integrates it with the data from the OLTP database.</span></span> <span data-ttu-id="9b50c-125">可以使用此数据获取如下所述的见解：“每个区域的销量增长率是否达到或超过人口增长率？”</span><span class="sxs-lookup"><span data-stu-id="9b50c-125">You can use this data for insights such as: "Does sales growth in each region match or exceed population growth?"</span></span>

### <a name="ingestion-and-data-storage"></a><span data-ttu-id="9b50c-126">引入和数据存储</span><span class="sxs-lookup"><span data-stu-id="9b50c-126">Ingestion and data storage</span></span>

<span data-ttu-id="9b50c-127">**Blob 存储**。</span><span class="sxs-lookup"><span data-stu-id="9b50c-127">**Blob Storage**.</span></span> <span data-ttu-id="9b50c-128">Blob 存储用作临时区域，在将源数据载入 SQL 数据仓库之前，会在此区域中存储这些数据。</span><span class="sxs-lookup"><span data-stu-id="9b50c-128">Blob storage is used as a staging area for the source data before loading it into SQL Data Warehouse.</span></span>

<span data-ttu-id="9b50c-129">**Azure SQL 数据仓库**。</span><span class="sxs-lookup"><span data-stu-id="9b50c-129">**Azure SQL Data Warehouse**.</span></span> <span data-ttu-id="9b50c-130">[SQL 数据仓库](/azure/sql-data-warehouse/)是分布式系统，旨在对大型数据执行分析。</span><span class="sxs-lookup"><span data-stu-id="9b50c-130">[SQL Data Warehouse](/azure/sql-data-warehouse/) is a distributed system designed to perform analytics on large data.</span></span> <span data-ttu-id="9b50c-131">它支持大规模并行处理 (MPP)，因此很适合用于运行高性能分析。</span><span class="sxs-lookup"><span data-stu-id="9b50c-131">It supports massive parallel processing (MPP), which makes it suitable for running high-performance analytics.</span></span>

<span data-ttu-id="9b50c-132">**Azure 数据工厂**。</span><span class="sxs-lookup"><span data-stu-id="9b50c-132">**Azure Data Factory**.</span></span> <span data-ttu-id="9b50c-133">[数据工厂][adf]是一项托管服务，用于协调和自动化数据的移动与转换。</span><span class="sxs-lookup"><span data-stu-id="9b50c-133">[Data Factory][adf] is a managed service that orchestrates and automates data movement and data transformation.</span></span> <span data-ttu-id="9b50c-134">在此体系结构中，数据工厂协调 ELT 过程的各个阶段。</span><span class="sxs-lookup"><span data-stu-id="9b50c-134">In this architecture, it coordinates the various stages of the ELT process.</span></span>

### <a name="analysis-and-reporting"></a><span data-ttu-id="9b50c-135">分析和报告</span><span class="sxs-lookup"><span data-stu-id="9b50c-135">Analysis and reporting</span></span>

<span data-ttu-id="9b50c-136">**Azure Analysis Services**。</span><span class="sxs-lookup"><span data-stu-id="9b50c-136">**Azure Analysis Services**.</span></span> <span data-ttu-id="9b50c-137">[Analysis Services](/azure/analysis-services/) 是提供数据建模功能的完全托管服务。</span><span class="sxs-lookup"><span data-stu-id="9b50c-137">[Analysis Services](/azure/analysis-services/) is a fully managed service that provides data modeling capabilities.</span></span> <span data-ttu-id="9b50c-138">语义模型将载入 Analysis Services。</span><span class="sxs-lookup"><span data-stu-id="9b50c-138">The semantic model is loaded into Analysis Services.</span></span>

<span data-ttu-id="9b50c-139">**Power BI**。</span><span class="sxs-lookup"><span data-stu-id="9b50c-139">**Power BI**.</span></span> <span data-ttu-id="9b50c-140">Power BI 是一套商业分析工具，用于分析数据以获取商业见解。</span><span class="sxs-lookup"><span data-stu-id="9b50c-140">Power BI is a suite of business analytics tools to analyze data for business insights.</span></span> <span data-ttu-id="9b50c-141">在此体系结构中，Power BI 查询 Analysis Services 中存储的语义模型。</span><span class="sxs-lookup"><span data-stu-id="9b50c-141">In this architecture, it queries the semantic model stored in Analysis Services.</span></span>

### <a name="authentication"></a><span data-ttu-id="9b50c-142">身份验证</span><span class="sxs-lookup"><span data-stu-id="9b50c-142">Authentication</span></span>

<span data-ttu-id="9b50c-143">**Azure Active Directory** (Azure AD) 通过 Power BI 对连接到 Analysis Services 服务器的用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="9b50c-143">**Azure Active Directory** (Azure AD) authenticates users who connect to the Analysis Services server through Power BI.</span></span>

<span data-ttu-id="9b50c-144">数据工厂还可以使用服务主体或托管服务标识 (MSI)，通过 Azure AD 对 SQL 数据仓库进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="9b50c-144">Data Factory can use also use Azure AD to authenticate to SQL Data Warehouse, by using a service principal or Managed Service Identity (MSI).</span></span> <span data-ttu-id="9b50c-145">为简单起见，示例部署使用了 SQL Server 身份验证。</span><span class="sxs-lookup"><span data-stu-id="9b50c-145">For simplicity, the example deployment uses SQL Server authentication.</span></span>

## <a name="data-pipeline"></a><span data-ttu-id="9b50c-146">数据管道</span><span class="sxs-lookup"><span data-stu-id="9b50c-146">Data pipeline</span></span>

<span data-ttu-id="9b50c-147">在 [Azure 数据工厂][adf]中，管道是用于协调某个任务的活动的逻辑分组 &mdash; 在本例中，该任务是将数据加载到 SQL 数据仓库中并对其进行转换。</span><span class="sxs-lookup"><span data-stu-id="9b50c-147">In [Azure Data Factory][adf], a pipeline is a logical grouping of activities used to coordinate a task &mdash; in this case, loading and transforming data into SQL Data Warehouse.</span></span>

<span data-ttu-id="9b50c-148">此参考体系结构定义一个运行一系列子管道的主管道。</span><span class="sxs-lookup"><span data-stu-id="9b50c-148">This reference architecture defines a master pipeline that runs a sequence of child pipelines.</span></span> <span data-ttu-id="9b50c-149">每个子管道将数据载入一个或多个数据仓库表。</span><span class="sxs-lookup"><span data-stu-id="9b50c-149">Each child pipeline loads data into one or more data warehouse tables.</span></span>

![Azure 数据工厂中管道的屏幕截图](./images/adf-pipeline.png)

## <a name="incremental-loading"></a><span data-ttu-id="9b50c-151">增量加载</span><span class="sxs-lookup"><span data-stu-id="9b50c-151">Incremental loading</span></span>

<span data-ttu-id="9b50c-152">运行自动化的 ETL 或 ELT 过程时，最有效的做法是仅加载自上次运行以来已发生更改的数据。</span><span class="sxs-lookup"><span data-stu-id="9b50c-152">When you run an automated ETL or ELT process, it's most efficient to load only the data that changed since the previous run.</span></span> <span data-ttu-id="9b50c-153">这称为“增量加载”，相对于加载所有数据的“完全加载”。</span><span class="sxs-lookup"><span data-stu-id="9b50c-153">This is called an *incremental load*, as opposed to a full load that loads all of the data.</span></span> <span data-ttu-id="9b50c-154">若要执行增量加载，需要通过某种方式来识别哪些数据已更改。</span><span class="sxs-lookup"><span data-stu-id="9b50c-154">To perform an incremental load, you need a way to identify which data has changed.</span></span> <span data-ttu-id="9b50c-155">最常用的方法是使用高水印值，即，跟踪源表中某个列的最新值：日期时间列，或唯一整数列。</span><span class="sxs-lookup"><span data-stu-id="9b50c-155">The most common approach is to use a *high water mark* value, which means tracking the latest value of some column in the source table, either a datetime column or a unique integer column.</span></span>

<span data-ttu-id="9b50c-156">从 SQL Server 2016 开始，可以使用[时态表](/sql/relational-databases/tables/temporal-tables)。</span><span class="sxs-lookup"><span data-stu-id="9b50c-156">Starting with SQL Server 2016, you can use [temporal tables](/sql/relational-databases/tables/temporal-tables).</span></span> <span data-ttu-id="9b50c-157">这些表的版本受系统控制，可保留数据更改的完整历史记录。</span><span class="sxs-lookup"><span data-stu-id="9b50c-157">These are system-versioned tables that keep a full history of data changes.</span></span> <span data-ttu-id="9b50c-158">数据库引擎会在单独的历史记录表中自动记录每项更改的历史记录。</span><span class="sxs-lookup"><span data-stu-id="9b50c-158">The database engine automatically records the history of every change in a separate history table.</span></span> <span data-ttu-id="9b50c-159">可以通过将 FOR SYSTEM_TIME 子句添加到查询，来查询历史数据。</span><span class="sxs-lookup"><span data-stu-id="9b50c-159">You can query the historical data by adding a FOR SYSTEM_TIME clause to a query.</span></span> <span data-ttu-id="9b50c-160">在内部，数据库引擎会查询历史记录表，但此操作对于应用程序而言是透明的。</span><span class="sxs-lookup"><span data-stu-id="9b50c-160">Internally, the database engine queries the history table, but this is transparent to the application.</span></span>

> [!NOTE]
> <span data-ttu-id="9b50c-161">对于早期版本的 SQL Server，可以使用[变更数据捕获](/sql/relational-databases/track-changes/about-change-data-capture-sql-server) (CDC)。</span><span class="sxs-lookup"><span data-stu-id="9b50c-161">For earlier versions of SQL Server, you can use [Change Data Capture](/sql/relational-databases/track-changes/about-change-data-capture-sql-server) (CDC).</span></span> <span data-ttu-id="9b50c-162">与时态表相比，此方法不够方便，因为必须查询单独的更改表，而更改是按日志序列号而不是时间戳跟踪的。</span><span class="sxs-lookup"><span data-stu-id="9b50c-162">This approach is less convenient than temporal tables, because you have to query a separate change table, and changes are tracked by a log sequence number, rather than a timestamp.</span></span>
>

<span data-ttu-id="9b50c-163">时态表适用于随时可能更改的维度数据。</span><span class="sxs-lookup"><span data-stu-id="9b50c-163">Temporal tables are useful for dimension data, which can change over time.</span></span> <span data-ttu-id="9b50c-164">事实数据表通常代表不可变的事务（例如销量），在这种情况下，保留系统版本历史记录没有意义。</span><span class="sxs-lookup"><span data-stu-id="9b50c-164">Fact tables usually represent an immutable transaction such as a sale, in which case keeping the system version history doesn't make sense.</span></span> <span data-ttu-id="9b50c-165">相反，事务通常具有一个表示事务日期的列，该日期可用作水印值。</span><span class="sxs-lookup"><span data-stu-id="9b50c-165">Instead, transactions usually have a column that represents the transaction date, which can be used as the watermark value.</span></span> <span data-ttu-id="9b50c-166">例如，在 Wide World Importers OLTP 数据库中，Sales.Invoices 和 Sales.InvoiceLines 表具有一个默认值为 `sysdatetime()` 的 `LastEditedWhen` 字段。</span><span class="sxs-lookup"><span data-stu-id="9b50c-166">For example, in the Wide World Importers OLTP database, the Sales.Invoices and Sales.InvoiceLines tables have a `LastEditedWhen` field that defaults to `sysdatetime()`.</span></span>

<span data-ttu-id="9b50c-167">下面是 ELT 管道的常规流：</span><span class="sxs-lookup"><span data-stu-id="9b50c-167">Here is the general flow for the ELT pipeline:</span></span>

1. <span data-ttu-id="9b50c-168">针对源数据库中的每个表，跟踪最后一个 ELT 作业的运行截止时间。</span><span class="sxs-lookup"><span data-stu-id="9b50c-168">For each table in the source database, track the cutoff time when the last ELT job ran.</span></span> <span data-ttu-id="9b50c-169">将此信息存储在数据仓库中。</span><span class="sxs-lookup"><span data-stu-id="9b50c-169">Store this information in the data warehouse.</span></span> <span data-ttu-id="9b50c-170">（在初始设置时，所有时间设置为“1-1-1900”。）</span><span class="sxs-lookup"><span data-stu-id="9b50c-170">(On initial setup, all times are set to '1-1-1900'.)</span></span>

2. <span data-ttu-id="9b50c-171">在执行数据导出步骤期间，截止时间作为参数传递给源数据库中的一组存储过程。</span><span class="sxs-lookup"><span data-stu-id="9b50c-171">During the data export step, the cutoff time is passed as a parameter to a set of stored procedures in the source database.</span></span> <span data-ttu-id="9b50c-172">这些存储过程会查询截止时间之后更改或创建的所有记录。</span><span class="sxs-lookup"><span data-stu-id="9b50c-172">These stored procedures query for any records that were changed or created after the cutoff time.</span></span> <span data-ttu-id="9b50c-173">对于 Sales 事实数据表，使用 `LastEditedWhen` 列。</span><span class="sxs-lookup"><span data-stu-id="9b50c-173">For the Sales fact table, the `LastEditedWhen` column is used.</span></span> <span data-ttu-id="9b50c-174">对于维度数据，使用版本由系统控制的时态表。</span><span class="sxs-lookup"><span data-stu-id="9b50c-174">For the dimension data, system-versioned temporal tables are used.</span></span>

3. <span data-ttu-id="9b50c-175">完成数据迁移后，更新存储截止时间的表。</span><span class="sxs-lookup"><span data-stu-id="9b50c-175">When the data migration is complete, update the table that stores the cutoff times.</span></span>

<span data-ttu-id="9b50c-176">该表也可用于记录每个 ELT 轮次的沿袭。</span><span class="sxs-lookup"><span data-stu-id="9b50c-176">It's also useful to record a *lineage* for each ELT run.</span></span> <span data-ttu-id="9b50c-177">对于给定的记录，该沿袭会将该记录与生成数据的 ELT 轮次相关联。</span><span class="sxs-lookup"><span data-stu-id="9b50c-177">For a given record, the lineage associates that record with the ELT run that produced the data.</span></span> <span data-ttu-id="9b50c-178">对于每个 ETL 轮次，将为每个表创建新的沿袭记录，其中显示起始和结束加载时间。</span><span class="sxs-lookup"><span data-stu-id="9b50c-178">For each ETL run, a new lineage record is created for every table, showing the starting and ending load times.</span></span> <span data-ttu-id="9b50c-179">每个记录的沿袭键存储在维度表和事实数据表中。</span><span class="sxs-lookup"><span data-stu-id="9b50c-179">The lineage keys for each record are stored in the dimension and fact tables.</span></span>

![城市维度表的屏幕截图](./images/city-dimension-table.png)

<span data-ttu-id="9b50c-181">将一批新数据载入仓库后，刷新 Analysis Services 表格模型。</span><span class="sxs-lookup"><span data-stu-id="9b50c-181">After a new batch of data is loaded into the warehouse, refresh the Analysis Services tabular model.</span></span> <span data-ttu-id="9b50c-182">请参阅[使用 REST API 执行异步刷新](/azure/analysis-services/analysis-services-async-refresh)。</span><span class="sxs-lookup"><span data-stu-id="9b50c-182">See [Asynchronous refresh with the REST API](/azure/analysis-services/analysis-services-async-refresh).</span></span>

## <a name="data-cleansing"></a><span data-ttu-id="9b50c-183">数据清理</span><span class="sxs-lookup"><span data-stu-id="9b50c-183">Data cleansing</span></span>

<span data-ttu-id="9b50c-184">数据清理应是 ELT 过程的一部分。</span><span class="sxs-lookup"><span data-stu-id="9b50c-184">Data cleansing should be part of the ELT process.</span></span> <span data-ttu-id="9b50c-185">在此参考体系结构中，一个错误数据源是城市人口表，其中的某些城市人口为零，可能的原因是没有可用的数据。</span><span class="sxs-lookup"><span data-stu-id="9b50c-185">In this reference architecture, one source of bad data is the city population table, where some cities have zero population, perhaps because no data was available.</span></span> <span data-ttu-id="9b50c-186">在处理期间，ELT 管道会从城市人口表中删除这些城市。</span><span class="sxs-lookup"><span data-stu-id="9b50c-186">During processing, the ELT pipeline removes those cities from the city population table.</span></span> <span data-ttu-id="9b50c-187">针对临时表而不是外部表执行数据清理。</span><span class="sxs-lookup"><span data-stu-id="9b50c-187">Perform data cleansing on staging tables, rather than external tables.</span></span>

<span data-ttu-id="9b50c-188">以下存储过程从城市人口表中删除人口为零的城市。</span><span class="sxs-lookup"><span data-stu-id="9b50c-188">Here is the stored procedure that removes the cities with zero population from the City Population table.</span></span> <span data-ttu-id="9b50c-189">（可在[此处](https://github.com/mspnp/reference-architectures/blob/master/data/enterprise_bi_sqldw_advanced/azure/sqldw_scripts/citypopulation/%5BIntegration%5D.%5BMigrateExternalCityPopulationData%5D.sql)找到源文件。）</span><span class="sxs-lookup"><span data-stu-id="9b50c-189">(You can find the source file [here](https://github.com/mspnp/reference-architectures/blob/master/data/enterprise_bi_sqldw_advanced/azure/sqldw_scripts/citypopulation/%5BIntegration%5D.%5BMigrateExternalCityPopulationData%5D.sql).)</span></span>

```sql
DELETE FROM [Integration].[CityPopulation_Staging]
WHERE RowNumber in (SELECT DISTINCT RowNumber
FROM [Integration].[CityPopulation_Staging]
WHERE POPULATION = 0
GROUP BY RowNumber
HAVING COUNT(RowNumber) = 4)
```

## <a name="external-data-sources"></a><span data-ttu-id="9b50c-190">外部数据源</span><span class="sxs-lookup"><span data-stu-id="9b50c-190">External data sources</span></span>

<span data-ttu-id="9b50c-191">数据仓库通常合并多个源的数据。</span><span class="sxs-lookup"><span data-stu-id="9b50c-191">Data warehouses often consolidate data from multiple sources.</span></span> <span data-ttu-id="9b50c-192">此参考体系结构加载包含人口统计数据的外部数据源。</span><span class="sxs-lookup"><span data-stu-id="9b50c-192">This reference architecture loads an external data source that contains demographics data.</span></span> <span data-ttu-id="9b50c-193">此数据集在 Azure Blob 存储中作为 [WorldWideImportersDW](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/wide-world-importers/sample-scripts/polybase) 示例的一部分提供。</span><span class="sxs-lookup"><span data-stu-id="9b50c-193">This dataset is available in Azure blob storage as part of the [WorldWideImportersDW](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/wide-world-importers/sample-scripts/polybase) sample.</span></span>

<span data-ttu-id="9b50c-194">Azure 数据工厂可以使用 [Blob 存储连接器](/azure/data-factory/connector-azure-blob-storage)直接从 Blob 存储复制。</span><span class="sxs-lookup"><span data-stu-id="9b50c-194">Azure Data Factory can copy directly from blob storage, using the [blob storage connector](/azure/data-factory/connector-azure-blob-storage).</span></span> <span data-ttu-id="9b50c-195">但是，连接器需要连接字符串或共享访问签名，因此它无法用于复制具有公共读取访问权限的 Blob。</span><span class="sxs-lookup"><span data-stu-id="9b50c-195">However, the connector requires a connection string or a shared access signature, so it can't be used to copy a blob with public read access.</span></span> <span data-ttu-id="9b50c-196">解决方法之一是使用 PolyBase 创建基于 Blob 存储的外部表，然后将外部表复制到 SQL 数据仓库中。</span><span class="sxs-lookup"><span data-stu-id="9b50c-196">As a workaround, you can use PolyBase to create an external table over Blob storage and then copy the external tables into SQL Data Warehouse.</span></span>

## <a name="handling-large-binary-data"></a><span data-ttu-id="9b50c-197">处理大型二进制数据</span><span class="sxs-lookup"><span data-stu-id="9b50c-197">Handling large binary data</span></span>

<span data-ttu-id="9b50c-198">在源数据库中，Cities 表包含一个 Location 列，该列保存了 [geography](/sql/t-sql/spatial-geography/spatial-types-geography) 空间数据类型。</span><span class="sxs-lookup"><span data-stu-id="9b50c-198">In the source database, the Cities table has a Location column that holds a [geography](/sql/t-sql/spatial-geography/spatial-types-geography) spatial data type.</span></span> <span data-ttu-id="9b50c-199">SQL 数据仓库原生并不支持 **geography** 类型，因此，在加载期间，此字段将转换为 **varbinary** 类型。</span><span class="sxs-lookup"><span data-stu-id="9b50c-199">SQL Data Warehouse doesn't support the **geography** type natively, so this field is converted to a **varbinary** type during loading.</span></span> <span data-ttu-id="9b50c-200">（请参阅[适用于不支持的数据类型的解决方法](/azure/sql-data-warehouse/sql-data-warehouse-tables-data-types#unsupported-data-types)。）</span><span class="sxs-lookup"><span data-stu-id="9b50c-200">(See [Workarounds for unsupported data types](/azure/sql-data-warehouse/sql-data-warehouse-tables-data-types#unsupported-data-types).)</span></span>

<span data-ttu-id="9b50c-201">但是，PolyBase 支持的最大列大小为 `varbinary(8000)`，这意味着某些数据可能会截断。</span><span class="sxs-lookup"><span data-stu-id="9b50c-201">However, PolyBase supports a maximum column size of `varbinary(8000)`, which means some data could be truncated.</span></span> <span data-ttu-id="9b50c-202">此问题的解决方法之一是在导出期间将数据分解为多个区块，然后重新汇编区块，如下所示：</span><span class="sxs-lookup"><span data-stu-id="9b50c-202">A workaround for this problem is to break the data up into chunks during export, and then reassemble the chunks, as follows:</span></span>

1. <span data-ttu-id="9b50c-203">为 Location 列创建临时表。</span><span class="sxs-lookup"><span data-stu-id="9b50c-203">Create a temporary staging table for the Location column.</span></span>

2. <span data-ttu-id="9b50c-204">对于每个城市，将位置数据拆分为 8000 字节的区块，使每个城市具有 1 &ndash; N 行。</span><span class="sxs-lookup"><span data-stu-id="9b50c-204">For each city, split the location data into 8000-byte chunks, resulting in 1 &ndash; N rows for each city.</span></span>

3. <span data-ttu-id="9b50c-205">若要重新汇编区块，请使用 T-SQL [PIVOT](/sql/t-sql/queries/from-using-pivot-and-unpivot) 运算符将行转换为列，然后连接每个城市的列值。</span><span class="sxs-lookup"><span data-stu-id="9b50c-205">To reassemble the chunks, use the T-SQL [PIVOT](/sql/t-sql/queries/from-using-pivot-and-unpivot) operator to convert rows into columns and then concatenate the column values for each city.</span></span>

<span data-ttu-id="9b50c-206">难点在于，需要根据地理数据的大小，将每个城市拆分为不同数量的行。</span><span class="sxs-lookup"><span data-stu-id="9b50c-206">The challenge is that each city will be split into a different number of rows, depending on the size of geography data.</span></span> <span data-ttu-id="9b50c-207">若要正常使用 PIVOT 运算符，每个城市的行数必须相同。</span><span class="sxs-lookup"><span data-stu-id="9b50c-207">For the PIVOT operator to work, every city must have the same number of rows.</span></span> <span data-ttu-id="9b50c-208">为此，T-SQL 查询（请查看[此文][MergeLocation]）会通过一些技法在行中填充空值，以便在透视后，每个城市的列数相同。</span><span class="sxs-lookup"><span data-stu-id="9b50c-208">To make this work, the T-SQL query (which you can view [here][MergeLocation]) does some tricks to pad out the rows with blank values, so that every city has the same number of columns after the pivot.</span></span> <span data-ttu-id="9b50c-209">生成的查询比每次循环访问一行要快得多。</span><span class="sxs-lookup"><span data-stu-id="9b50c-209">The resulting query turns out to be much faster than looping through the rows one at a time.</span></span>

<span data-ttu-id="9b50c-210">可对图像数据使用相同的方法。</span><span class="sxs-lookup"><span data-stu-id="9b50c-210">The same approach is used for image data.</span></span>

## <a name="slowly-changing-dimensions"></a><span data-ttu-id="9b50c-211">缓慢变化的维度</span><span class="sxs-lookup"><span data-stu-id="9b50c-211">Slowly changing dimensions</span></span>

<span data-ttu-id="9b50c-212">维度数据相对而言是静态的，但仍可能发生变化。</span><span class="sxs-lookup"><span data-stu-id="9b50c-212">Dimension data is relatively static, but it can change.</span></span> <span data-ttu-id="9b50c-213">例如，某个产品可能会重新分配到不同的产品类别。</span><span class="sxs-lookup"><span data-stu-id="9b50c-213">For example, a product might get reassigned to a different product category.</span></span> <span data-ttu-id="9b50c-214">可通过多种方法来处理缓慢变化的维度。</span><span class="sxs-lookup"><span data-stu-id="9b50c-214">There are several approaches to handling slowly changing dimensions.</span></span> <span data-ttu-id="9b50c-215">常用的方法称为[类型 2](https://wikipedia.org/wiki/Slowly_changing_dimension#Type_2:_add_new_row)，即，每当维度发生变化，就添加一条新记录。</span><span class="sxs-lookup"><span data-stu-id="9b50c-215">A common technique, called [Type 2](https://wikipedia.org/wiki/Slowly_changing_dimension#Type_2:_add_new_row), is to add a new record whenever a dimension changes.</span></span>

<span data-ttu-id="9b50c-216">若要实现“类型 2”方法，维度表中需要包含附加的列，用于指定给定记录的有效日期范围。</span><span class="sxs-lookup"><span data-stu-id="9b50c-216">In order to implement the Type 2 approach, dimension tables need additional columns that specify the effective date range for a given record.</span></span> <span data-ttu-id="9b50c-217">此外，源数据库中的主键将会复制，因此，维度表必须包含一个人造主键。</span><span class="sxs-lookup"><span data-stu-id="9b50c-217">Also, primary keys from the source database will be duplicated, so the dimension table must have an artificial primary key.</span></span>

<span data-ttu-id="9b50c-218">下图显示了 Dimension.City 表。</span><span class="sxs-lookup"><span data-stu-id="9b50c-218">The following image shows the Dimension.City table.</span></span> <span data-ttu-id="9b50c-219">`WWI City ID` 列是源数据库中的主键。</span><span class="sxs-lookup"><span data-stu-id="9b50c-219">The `WWI City ID` column is the primary key from the source database.</span></span> <span data-ttu-id="9b50c-220">`City Key` 列是在运行 ETL 管道期间生成的人造键。</span><span class="sxs-lookup"><span data-stu-id="9b50c-220">The `City Key` column is an artificial key generated during the ETL pipeline.</span></span> <span data-ttu-id="9b50c-221">另请注意，该表包含 `Valid From` 和 `Valid To` 列，这些列定义每个行的有效时间范围。</span><span class="sxs-lookup"><span data-stu-id="9b50c-221">Also notice that the table has `Valid From` and `Valid To` columns, which define the range when each row was valid.</span></span> <span data-ttu-id="9b50c-222">当前值包含等于“9999-12-31”的 `Valid To`。</span><span class="sxs-lookup"><span data-stu-id="9b50c-222">Current values have a `Valid To` equal to '9999-12-31'.</span></span>

![城市维度表的屏幕截图](./images/city-dimension-table.png)

<span data-ttu-id="9b50c-224">此方法的优势在于，它会保留历史数据，而这些数据对于分析可能非常有用。</span><span class="sxs-lookup"><span data-stu-id="9b50c-224">The advantage of this approach is that it preserves historical data, which can be valuable for analysis.</span></span> <span data-ttu-id="9b50c-225">但是，这也意味着，同一个实体存在多个行。</span><span class="sxs-lookup"><span data-stu-id="9b50c-225">However, it also means there will be multiple rows for the same entity.</span></span> <span data-ttu-id="9b50c-226">例如，以下记录与 `WWI City ID` = 28561 相匹配：</span><span class="sxs-lookup"><span data-stu-id="9b50c-226">For example, here are the records that match `WWI City ID` = 28561:</span></span>

![城市维度表的第二个屏幕截图](./images/city-dimension-table-2.png)

<span data-ttu-id="9b50c-228">对于每个销售事实，需要将该事实与 City 维度表中对应于发票日期的单个行相关联。</span><span class="sxs-lookup"><span data-stu-id="9b50c-228">For each Sales fact, you want to associate that fact with a single row in City dimension table, corresponding to the invoice date.</span></span> <span data-ttu-id="9b50c-229">在执行 ETL 过程期间，创建一个附加列</span><span class="sxs-lookup"><span data-stu-id="9b50c-229">As part of the ETL process, create an additional column that</span></span> 

<span data-ttu-id="9b50c-230">以下 T-SQL 查询创建一个临时表，用于将每份发票与 City 维度表中的正确 City 键相关联。</span><span class="sxs-lookup"><span data-stu-id="9b50c-230">The following T-SQL query creates a temporary table that associates each invoice with the correct City Key from the City dimension table.</span></span>

```sql
CREATE TABLE CityHolder
WITH (HEAP , DISTRIBUTION = HASH([WWI Invoice ID]))
AS
SELECT DISTINCT s1.[WWI Invoice ID] AS [WWI Invoice ID],
                c.[City Key] AS [City Key]
    FROM [Integration].[Sale_Staging] s1
    CROSS APPLY (
                SELECT TOP 1 [City Key]
                    FROM [Dimension].[City]
                WHERE [WWI City ID] = s1.[WWI City ID]
                    AND s1.[Last Modified When] > [Valid From]
                    AND s1.[Last Modified When] <= [Valid To]
                ORDER BY [Valid From], [City Key] DESC
                ) c

```

<span data-ttu-id="9b50c-231">此表用于填充 Sales 事实数据表中的列：</span><span class="sxs-lookup"><span data-stu-id="9b50c-231">This table is used to populate a column in the Sales fact table:</span></span>

```sql
UPDATE [Integration].[Sale_Staging]
SET [Integration].[Sale_Staging].[WWI Customer ID] =  CustomerHolder.[WWI Customer ID]
```

<span data-ttu-id="9b50c-232">Power BI 查询可以使用此列找到给定销售发票的正确 City 记录。</span><span class="sxs-lookup"><span data-stu-id="9b50c-232">This column enables a Power BI query to find the correct City record for a given sales invoice.</span></span>

## <a name="security-considerations"></a><span data-ttu-id="9b50c-233">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="9b50c-233">Security considerations</span></span>

<span data-ttu-id="9b50c-234">为了提高安全性，可以使用[虚拟网络服务终结点](/azure/virtual-network/virtual-network-service-endpoints-overview)来保护 Azure 服务资源：只允许在你的虚拟网络中访问这些资源。</span><span class="sxs-lookup"><span data-stu-id="9b50c-234">For additional security, you can use [Virtual Network service endpoints](/azure/virtual-network/virtual-network-service-endpoints-overview) to secure Azure service resources to only your virtual network.</span></span> <span data-ttu-id="9b50c-235">这可以完全避免通过公共 Internet 访问这些资源，只允许来自该虚拟网络的流量。</span><span class="sxs-lookup"><span data-stu-id="9b50c-235">This fully removes public Internet access to those resources, allowing traffic only from your virtual network.</span></span>

<span data-ttu-id="9b50c-236">如果使用此方法，请在 Azure 中创建 VNet，并为 Azure 服务创建专用服务终结点。</span><span class="sxs-lookup"><span data-stu-id="9b50c-236">With this approach, you create a VNet in Azure and then create private service endpoints for Azure services.</span></span> <span data-ttu-id="9b50c-237">然后，将这些服务限制为来自该虚拟网络的流量。</span><span class="sxs-lookup"><span data-stu-id="9b50c-237">Those services are then restricted to traffic from that virtual network.</span></span> <span data-ttu-id="9b50c-238">也可以通过网关从本地网络访问这些资源。</span><span class="sxs-lookup"><span data-stu-id="9b50c-238">You can also reach them from your on-premises network through a gateway.</span></span>

<span data-ttu-id="9b50c-239">注意以下限制：</span><span class="sxs-lookup"><span data-stu-id="9b50c-239">Be aware of the following limitations:</span></span>

- <span data-ttu-id="9b50c-240">在创建此参考体系结构时，Azure 存储和 Azure SQL 数据仓库支持 VNet 服务终结点，但 Azure Analysis Service 则不支持此类终结点。</span><span class="sxs-lookup"><span data-stu-id="9b50c-240">At the time this reference architecture was created, VNet service endpoints are supported for Azure Storage and Azure SQL Data Warehouse, but not for Azure Analysis Service.</span></span> <span data-ttu-id="9b50c-241">请在[此处](https://azure.microsoft.com/updates/?product=virtual-network)查看最新状态。</span><span class="sxs-lookup"><span data-stu-id="9b50c-241">Check the latest status [here](https://azure.microsoft.com/updates/?product=virtual-network).</span></span>

- <span data-ttu-id="9b50c-242">如果为 Azure 存储启用了服务终结点，PolyBase 无法将数据从存储复制到 SQL 数据仓库。</span><span class="sxs-lookup"><span data-stu-id="9b50c-242">If service endpoints are enabled for Azure Storage, PolyBase cannot copy data from Storage into SQL Data Warehouse.</span></span> <span data-ttu-id="9b50c-243">此问题有一种缓解方法。</span><span class="sxs-lookup"><span data-stu-id="9b50c-243">There is a mitigation for this issue.</span></span> <span data-ttu-id="9b50c-244">有关详细信息，请参阅[将 VNet 服务终结点与 Azure 存储配合使用的影响](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fvirtual-network%2ftoc.json#impact-of-using-vnet-service-endpoints-with-azure-storage)。</span><span class="sxs-lookup"><span data-stu-id="9b50c-244">For more information, see [Impact of using VNet Service Endpoints with Azure storage](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fvirtual-network%2ftoc.json#impact-of-using-vnet-service-endpoints-with-azure-storage).</span></span>

- <span data-ttu-id="9b50c-245">若要将数据从本地移入 Azure 存储，需要将本地或 ExpressRoute 中的公共 IP 地址加入允许列表。</span><span class="sxs-lookup"><span data-stu-id="9b50c-245">To move data from on-premises into Azure Storage, you will need to whitelist public IP addresses from your on-premises or ExpressRoute.</span></span> <span data-ttu-id="9b50c-246">有关详细信息，请参阅[在虚拟网络中保护 Azure 服务](/azure/virtual-network/virtual-network-service-endpoints-overview#securing-azure-services-to-virtual-networks)。</span><span class="sxs-lookup"><span data-stu-id="9b50c-246">For details, see [Securing Azure services to virtual networks](/azure/virtual-network/virtual-network-service-endpoints-overview#securing-azure-services-to-virtual-networks).</span></span>

- <span data-ttu-id="9b50c-247">若要允许 Analysis Services 从 SQL 数据仓库读取数据，请将一个 Windows VM 部署到包含 SQL 数据仓库服务终结点的虚拟网络。</span><span class="sxs-lookup"><span data-stu-id="9b50c-247">To enable Analysis Services to read data from SQL Data Warehouse, deploy a Windows VM to the virtual network that contains the SQL Data Warehouse service endpoint.</span></span> <span data-ttu-id="9b50c-248">在此 VM 上安装 [Azure 本地数据网关](/azure/analysis-services/analysis-services-gateway)。</span><span class="sxs-lookup"><span data-stu-id="9b50c-248">Install [Azure On-premises Data Gateway](/azure/analysis-services/analysis-services-gateway) on this VM.</span></span> <span data-ttu-id="9b50c-249">然后将 Azure Analysis Services 连接到数据网关。</span><span class="sxs-lookup"><span data-stu-id="9b50c-249">Then connect your Azure Analysis service to the data gateway.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="9b50c-250">部署解决方案</span><span class="sxs-lookup"><span data-stu-id="9b50c-250">Deploy the solution</span></span>

<span data-ttu-id="9b50c-251">若要部署并运行参考实现，请按 [GitHub 自述文件][github]中的步骤操作。</span><span class="sxs-lookup"><span data-stu-id="9b50c-251">To the deploy and run the reference implementation, follow the steps in the [GitHub readme][github].</span></span> <span data-ttu-id="9b50c-252">它将部署以下部分：</span><span class="sxs-lookup"><span data-stu-id="9b50c-252">It deploys the following:</span></span>

- <span data-ttu-id="9b50c-253">一个用于模拟本地数据库服务器的 Windows VM。</span><span class="sxs-lookup"><span data-stu-id="9b50c-253">A Windows VM to simulate an on-premises database server.</span></span> <span data-ttu-id="9b50c-254">该 VM 包含 SQL Server 2017 和相关工具以及 Power BI Desktop。</span><span class="sxs-lookup"><span data-stu-id="9b50c-254">It includes SQL Server 2017 and related tools, along with Power BI Desktop.</span></span>
- <span data-ttu-id="9b50c-255">一个 Azure 存储帐户。该帐户提供 Blob 存储用于保存从 SQL Server 数据库导出的数据。</span><span class="sxs-lookup"><span data-stu-id="9b50c-255">An Azure storage account that provides Blob storage to hold data exported from the SQL Server database.</span></span>
- <span data-ttu-id="9b50c-256">一个 Azure SQL 数据仓库实例。</span><span class="sxs-lookup"><span data-stu-id="9b50c-256">An Azure SQL Data Warehouse instance.</span></span>
- <span data-ttu-id="9b50c-257">一个 Azure Analysis Services 实例。</span><span class="sxs-lookup"><span data-stu-id="9b50c-257">An Azure Analysis Services instance.</span></span>
- <span data-ttu-id="9b50c-258">Azure 数据工厂和 ELT 作业的数据工厂管道。</span><span class="sxs-lookup"><span data-stu-id="9b50c-258">Azure Data Factory and the Data Factory pipeline for the ELT job.</span></span>

## <a name="related-resources"></a><span data-ttu-id="9b50c-259">相关资源</span><span class="sxs-lookup"><span data-stu-id="9b50c-259">Related resources</span></span>

<span data-ttu-id="9b50c-260">可以查看以下 [Azure 示例方案](/azure/architecture/example-scenario)，了解使用部分相同技术的具体解决方案：</span><span class="sxs-lookup"><span data-stu-id="9b50c-260">You may want to review the following [Azure example scenarios](/azure/architecture/example-scenario) that demonstrate specific solutions using some of the same technologies:</span></span>

- [<span data-ttu-id="9b50c-261">用于销售和市场营销的数据仓库和分析</span><span class="sxs-lookup"><span data-stu-id="9b50c-261">Data warehousing and analytics for sales and marketing</span></span>](/azure/architecture/example-scenario/data/data-warehouse)
- [<span data-ttu-id="9b50c-262">将混合 ETL 与现有本地 SSIS 和 Azure 数据工厂配合使用</span><span class="sxs-lookup"><span data-stu-id="9b50c-262">Hybrid ETL with existing on-premises SSIS and Azure Data Factory</span></span>](/azure/architecture/example-scenario/data/hybrid-etl-with-adf)

<!-- links -->

[adf]: /azure/data-factory
[github]: https://github.com/mspnp/azure-data-factory-sqldw-elt-pipeline
[MergeLocation]: https://github.com/mspnp/reference-architectures/blob/master/data/enterprise_bi_sqldw_advanced/azure/sqldw_scripts/city/%5BIntegration%5D.%5BMergeLocation%5D.sql
[wwi]: /sql/sample/world-wide-importers/wide-world-importers-oltp-database
