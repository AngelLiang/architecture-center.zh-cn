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
# <a name="hybrid-etl-with-existing-on-premises-ssis-and-azure-data-factory"></a>将混合 ETL 与现有本地 SSIS 和 Azure 数据工厂配合使用

将 SQL Server 数据库迁移到云的组织可以实现巨大的成本节省、性能提升以及更高的灵活性和可伸缩性。 但是，重新设计通过 SQL Server Integration Services (SSIS) 构建的现有提取、转换和加载 (ETL) 过程可能会给迁移造成阻碍。 在其他情况下，数据加载过程需要复杂的逻辑和/或特定的数据工具组件，而 Azure 数据工厂 v2 尚不支持这些组件。 常用的 SSIS 功能包括模糊查找和模糊分组转换、变更数据捕获 (CDC)、渐变维度 (SCD) 以及数据质量服务 (DQS)。

为了简化现有 SQL 数据库的直接迁移，混合 ETL 方法可能是最合适的选项。 混合方法使用数据工厂作为主要业务流程引擎，但会持续利用现有的 SSIS 包来清理数据和处理本地资源。 此方法使用数据工厂 SQL Server 集成运行时 (IR) 将现有数据库直接迁移到云中，同时使用现有的代码和 SSIS 包。

本示例方案与要将数据库迁移到云、正在考虑使用数据工厂作为主要的基于云的 ETL 引擎，同时要将现有 SSIS 包整合到新的云数据工作流的组织相关。 许多组织已投入大笔资金来为特定数据任务开发 SSIS ETL 包。 重新编写这些包可能颇费周折。 此外，许多现有的代码包依赖于本地资源，导致无法迁移到云。

数据工厂允许客户利用其现有的 ETL 包，同时可以遏制在本地 ETL 开发方面的进一步投资。 本示例讨论有关通过 Azure 数据工厂 v2 在新的云数据工作流中利用现有 SSIS 包的潜在用例。

## <a name="potential-use-cases"></a>可能的用例

一直以来，许多 SQL Server 数据专家都会选择 SSIS 作为 ETL 工具来转换和加载数据。 有时，会使用特定的 SSIS 功能或第三方插件来加速开发工作。 可能无法替换或重新开发这些包，导致客户无法将其数据库迁移到云中。 客户正在寻求一些可将其现有数据库迁移到云，并可利用其现有 SSIS 包的低影响性方法。

下面列出了几个潜在的本地用例：

- 将网络路由器日志加载到数据库进行分析。
- 准备人力资源招聘数据进行分析报告。
- 将产品和销售数据载入数据仓库进行销售预测。
- 自动加载财务和会计方面的运营数据存储或数据仓库。

## <a name="architecture"></a>体系结构

![使用 Azure 数据工厂的混合 ETL 过程的体系结构概览][architecture-diagram]

1. 数据源自 Blob 存储，并载入数据工厂。
2. 数据工厂管道调用某个存储过程，以通过集成运行时执行本地托管的 SSIS 作业。
3. 执行数据清理作业，以准备下游使用的数据。
4. 数据清理任务成功完成后，执行复制任务将清理数据载入 Azure。
5. 然后，将清理数据载入 SQL 数据仓库中的表。

### <a name="components"></a>组件

- [Blob 存储][docs-blob-storage]用于存储文件，并充当数据工厂检索数据的源。
- [SQL Server Integration Services][docs-ssis] 包含用于执行任务特定的工作负荷的本地 ETL 包。
- [Azure 数据工厂][docs-data-factory]是云业务流程引擎，它从多个源提取数据，合并、协调数据并将其载入数据仓库。
- [SQL 数据仓库][docs-sql-data-warehouse]在云中将数据集中化，以便能够使用标准的 ANSI SQL 查询轻松访问数据。

### <a name="alternatives"></a>备选项

数据工厂可以调用使用其他技术（例如 Databricks Notebook、Python 脚本或虚拟机中运行的 SSIS 实例）实现的数据清理过程。 [为 Azure SSIS 集成运行时安装付费或授权的自定义组件](/azure/data-factory/how-to-develop-azure-ssis-ir-licensed-components)可能是混合方法的可行替代方案。

## <a name="considerations"></a>注意事项

集成运行时 (IR) 支持两种模型：自承载 IR 或 Azure 承载的 IR。 首先必须在这两个选项之间做出抉择。 自托管更具成本效益，但会增大维护和管理开销。 有关详细信息，请参阅[自承载 IR](/azure/data-factory/concepts-integration-runtime#self-hosted-integration-runtime)。 在确定要使用哪种 IR 时如需帮助，请参阅[确定要使用哪种 IR](/azure/data-factory/concepts-integration-runtime#determining-which-ir-to-use)。

对于 Azure 承载方法，应确定处理数据所需的处理能力。 Azure 承载配置允许在执行配置步骤期间选择 VM 大小。 若要详细了解如何选择 VM 大小，请参阅 [VM 性能注意事项](/azure/cloud-services/cloud-services-sizes-specs#performance-considerations)。

如果已有包含本地依赖项（例如，无法从 Azure 访问的数据源或文件）的现有 SSIS 包，则决策就容易得多。 在这种情况下，唯一的选项是自承载 IR。 通过此方法可以最灵活地利用云作为业务流程引擎，而无需重新编写现有的包。

最终的意图是将处理的数据迁移到云中以做进一步的优化，或者与云中存储的其他数据合并。 在设计过程中，应跟踪数据工厂管道中使用的活动数。 有关详细信息，请参阅 [Azure 数据工厂中的管道和活动](/azure/data-factory/concepts-pipelines-activities)。

## <a name="pricing"></a>定价

使用数据工厂可以经济高效地协调云中的数据移动。 费用取决于多种因素。

- 管道执行的数目
- 管道中使用的实体/活动数目
- 监视操作的数目
- 集成运行（Azure 承载的 IR 或自承载 IR）的数目

数据工厂使用基于消耗量的计费模式。 因此，只会在管道执行和监视期间才会产生费用。 执行基本管道的费用低至 50 美分，而监视的费用低至 25 美分。 可以使用 [Azure 成本计算器](https://azure.microsoft.com/pricing/calculator/)根据特定的工作负荷估算更准确的费用。

运行混合 ETL 工作负荷时，必须考虑到用于承载 SSIS 包的虚拟机的费用。 此费用基于 VM 大小。VM 大小范围为 D1v2（单核，3.5 GB RAM，50 GB 磁盘）到 E64V3（64 核，432 GB RAM，1600 GB 磁盘）。 如需有关选择适当 VM 大小的进一步指导，请参阅 [VM 性能注意事项](/azure/cloud-services/cloud-services-sizes-specs#performance-considerations)。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure 数据工厂](https://azure.microsoft.com/services/data-factory/)。
- 遵循[分步教程](/azure/data-factory/#step-by-step-tutorials)开始使用 Azure 数据工厂。
- [在 Azure 数据工厂中预配 Azure-SSIS Integration Runtime](/azure/data-factory/tutorial-deploy-ssis-packages-azure)。

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
