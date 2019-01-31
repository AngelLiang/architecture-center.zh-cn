---
title: 用于销售和市场营销的数据仓库和分析
titleSuffix: Azure Example Scenarios
description: 合并来自多个源的数据并优化数据分析。
author: alexbuckgit
ms.date: 09/15/2018
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.custom: data-analytics
social_image_url: /azure/architecture/example-scenario/data/media/architecture-data-warehouse.png
ms.openlocfilehash: 9eaa9cc472e2222b5cde04a3b84ba38623f65543
ms.sourcegitcommit: 3b15d65e7c35a19506e562c444343f8467b6a073
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2019
ms.locfileid: "54908179"
---
# <a name="data-warehousing-and-analytics-for-sales-and-marketing"></a>用于销售和市场营销的数据仓库和分析

本示例方案演示一个将大量数据从多个源集成到 Azure 中统一分析平台的数据管道。 此特定方案基于某个销售和营销解决方案，但设计模式与需要对大型数据集进行高级分析的多个行业（例如电子商务、零售和医疗保健）相关。

本示例演示了一家需要创建激励计划的销售和营销公司。 这些计划涉及到客户、供应商、销售人员和员工的奖励。 数据是这些计划的基础，该公司希望改善使用 Azure 通过数据分析获得的见解。

该公司需要采用新式方法来分析数据，以便在适当的时间使用适当的数据做出决策。 该公司的目标包括：

- 将不同种类的数据源合并到一个云规模的平台。
- 将源数据转换为常见的分类和结构，使数据保持一致并可轻松进行比较。
- 使用支持数千个激励计划的高度并行化方法加载数据，并避免部署和维护本地基础结构所产生的较高成本。
- 大幅减少收集和转换数据所需的时间，以便可以专注于分析数据。

## <a name="relevant-use-cases"></a>相关用例

此方法还可以用来：

- 建立数据仓库，用作数据的单一事实源。
- 将关系数据源与其他非结构化数据集相集成。
- 使用语义建模和强大的可视化工具来简化数据分析。

## <a name="architecture"></a>体系结构

![Azure 中数据仓库和分析方案的体系结构][architecture]

数据流经解决方案的情形如下所示：

1. 对于每个数据源，会定期将所有更新导出到 Azure Blob 存储中的临时区域。
2. 数据工厂逐渐将数据从 Blob 存储载入 SQL 数据仓库中的临时表。 在此过程中，会清理并转换数据。 Polybase 可将大型数据集的处理过程并行化。
3. 将新的一批数据载入仓库后，会刷新以前创建的 Analysis Services 表格模型。 此语义模型简化了业务数据和关系的分析。
4. 业务分析师使用 Microsoft Power BI 通过 Analysis Services 语义模型来分析数据仓库数据。

### <a name="components"></a>组件

该公司的数据源位于多个不同的平台上：

- 本地 SQL Server
- 本地 Oracle 平台
- Azure SQL 数据库
- Azure 表存储
- Cosmos DB

使用多个 Azure 组件从不同的数据源加载数据：

- 将源数据载入 SQL 数据仓库之前，先使用 [Blob 存储](/azure/storage/blobs/storage-blobs-introduction)暂存这些数据。
- [数据工厂](/azure/data-factory)在 SQL 数据仓库的通用结构中协调临时数据的转换。 数据工厂[在将数据载入 SQL 数据仓库时使用 Polybase](/azure/data-factory/connector-azure-sql-data-warehouse#use-polybase-to-load-data-into-azure-sql-data-warehouse)，以最大化吞吐量。
- [SQL 数据仓库](/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is)是用于存储和分析大型数据集的分布式系统。 它使用大规模并行处理 (MPP)，因此很适合用于运行高性能分析。 SQL 数据仓库可以使用 [PolyBase](/sql/relational-databases/polybase/polybase-guide) 快速从 Blob 存储加载数据。
- [Analysis Services](/azure/analysis-services) 为数据提供语义模型。 分析数据时，它还可以提高系统性能。
- [Power BI](/power-bi) 是一套业务分析工具，可以分析数据和分享见解。 Power BI 可以查询 Analysis Services 中存储的语义模型，或者直接查询 SQL 数据仓库。
- [Azure Active Directory (Azure AD)](/azure/active-directory) 通过 Power BI 对连接到 Analysis Services 服务器的用户进行身份验证。 数据工厂还可以使用 Azure AD 通过服务主体或 [Azure 资源的托管标识](/azure/active-directory/managed-identities-azure-resources/overview)对 SQL 数据仓库进行身份验证。

### <a name="alternatives"></a>备选项

- 示例管道包含多种不同的数据源。 此体系结构可以处理各种关系和非关系数据源。
- 数据工厂协调数据管道的工作流。 如果你只想加载数据一次或按需加载数据，可以使用 SQL Server 批量复制 (bcp) 和 AzCopy 等工具将数据复制到 Blob 存储中。 然后，可以使用 Polybase 直接将数据载入 SQL 数据仓库。
- 对于极大型的数据集，请考虑使用 [Data Lake Storage](/azure/storage/data-lake-storage/introduction)，它可为分析数据提供无限存储。
- 还可以使用本地 [SQL Server 并行数据仓库](/sql/analytics-platform-system)设备进行大数据处理。 但是，使用 SQL 数据仓库等基于云的托管解决方案通常可以大幅较低运营成本。
- SQL 数据仓库不很适合 OLTP 工作负荷或小于 250GB 的数据集。 对于这种情况，应使用 Azure SQL 数据库或 SQL Server。
- 有关其他备选方案的比较，请参阅：

  - [在 Azure 中选择数据管道业务流程技术](/azure/architecture/data-guide/technology-choices/pipeline-orchestration-data-movement)
  - [在 Azure 中选择批处理技术](/azure/architecture/data-guide/technology-choices/batch-processing)
  - [在 Azure 中选择分析型数据存储](/azure/architecture/data-guide/technology-choices/analytical-data-stores)
  - [在 Azure 中选择数据分析技术](/azure/architecture/data-guide/technology-choices/analysis-visualizations-reporting)

## <a name="considerations"></a>注意事项

之所以选择此体系结构中的技术，是因为它们符合公司在可伸缩性与可用性方面的要求，同时有助于控制成本。

- SQL 数据仓库的[大规模并行处理体系结构](/azure/sql-data-warehouse/massively-parallel-processing-mpp-architecture)提供可伸缩性和高性能。
- SQL 数据仓库提供[有保障的 SLA](https://azure.microsoft.com/support/legal/sla/sql-data-warehouse)，以及[实现高可用性的建议做法](/azure/sql-data-warehouse/sql-data-warehouse-best-practices)。
- 当分析活动较少时，公司可以[按需缩减 SQL 数据仓库](/azure/sql-data-warehouse/sql-data-warehouse-manage-compute-overview)，以减少甚至暂停计算资源，从而较低成本。
- 当查询工作负荷较高时，可以[横向扩展](/azure/analysis-services/analysis-services-scale-out) Azure Analysis Services 来减少响应时间。 也可将处理与查询池分开，使处理操作不会减慢客户端查询的速度。
- Azure Analysis Services 也提供[有保障的 SLA](https://azure.microsoft.com/support/legal/sla/analysis-services)，以及[实现高可用性的建议做法](/azure/analysis-services/analysis-services-bcdr)。
- [SQL 数据仓库安全模型](/azure/sql-data-warehouse/sql-data-warehouse-overview-manage-security)通过 Azure AD 或 SQL Server 身份验证和加密提供连接安全性、[身份验证和授权](/azure/sql-data-warehouse/sql-data-warehouse-authentication)。 [Azure Analysis Services](/azure/analysis-services/analysis-services-manage-users) 使用 Azure AD 进行标识管理和用户身份验证。

## <a name="pricing"></a>定价

请通过 Azure 定价计算器了解[数据仓库方案的定价示例][calculator]。 请调整值，查看要求如何影响成本。

- [SQL 数据仓库](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2)允许独立缩放计算和存储级别。 计算资源按小时计费，可按需缩放或暂停这些资源。 存储资源按 TB 计费，因此，引入的数据越多，费用就越高。
- [数据工厂](https://azure.microsoft.com/pricing/details/data-factory)的费用根据读/写操作数目、监视操作数目以及在一个工作负荷中执行的业务流程活动数目来计收。 每增加一个数据流和每增加一个单位的处理数据量，数据工厂的费用都会增加。
- 可在开发人员层、基本层和标准层使用 [Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services)。 实例按查询处理单位 (QPU) 和可用内存定价。 为了控制成本，请尽量减少运行的查询数、查询处理的数据量以及查询的运行频率。
- [Power BI](https://powerbi.microsoft.com/pricing) 提供不同的产品选项来满足不同的要求。 [Power BI Embedded](https://azure.microsoft.com/pricing/details/power-bi-embedded) 提供基于 Azure 的选项用于在应用程序中嵌入 Power BI 功能。 上述定价示例包括 Power BI Embedded 实例。

## <a name="next-steps"></a>后续步骤

- 查看[自动化企业 BI 的 Azure 参考体系结构](/azure/architecture/reference-architectures/data/enterprise-bi-adf)，其中包含了有关在 Azure 中部署此体系结构的实例的说明。
- 阅读 [Maritz Motivation Solutions 客户案例][source-document]。 该案例介绍了管理客户数据的一种类似方法。
- 在 [Azure 数据体系结构指南](/azure/architecture/data-guide)中查找有关数据管道、数据仓库、联机分析处理 (OLAP) 和大数据的综合体系结构指导。

<!-- links -->

[source-document]: https://customers.microsoft.com/story/maritz
[calculator]: https://azure.com/e/b798fb70c53e4dd19fdeacea4db78276
[architecture]: ./media/architecture-data-warehouse.png
