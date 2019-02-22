---
title: 大型机迁移：大型机应用程序迁移
description: 将应用程序从大型机环境迁移到 Azure，这是经过验证的、高度可用且可缩放的基础结构，适用于当前在大型机上运行的系统
author: njray
ms.date: 12/26/2018
ms.openlocfilehash: dcae5077e26ab8ba9b08e0da71a5e69d0d9f62e3
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900494"
---
# <a name="mainframe-application-migration"></a>大型机应用程序迁移

将应用程序从大型机环境迁移到 Azure 时，大多数团队都遵循这一有效方法：尽可能地随时随地重用，然后开始分阶段部署，重新编写或替换应用程序。

应用程序迁移通常涉及以下一种或多种策略：

- 重新托管：可以从大型机移动现有代码、程序和应用程序，然后重新编译代码，以在云实例中托管的大型机仿真器上运行。 此方法通常首先将应用程序移至基于云的仿真器，然后将数据库迁移到基于云的数据库。 需要进行一些工程和重构以及数据和文件转换。

    或者，可以使用传统的托管提供程序重新托管。 云的主要优点之一是外包基础结构管理。 可以找到托管大型机工作负荷的数据中心提供程序。 该模型可能会争得时间，减少供应商锁定，并节省临时成本。

- 停用：在迁移之前，应停用所有不再需要的应用程序。

- 重新生成：一些组织选择使用现代技术完全重写程序。 鉴于此方法增加了成本和复杂性，它并不像直接迁移方法那样普遍。 通常在此类迁移后，使用代码转换引擎开始替换模块和代码是有意义的。

- 将：此方法将大型机功能替换为云中的等效功能。 服务型软件 (SaaS) 是一个选项，该选项使用专门为企业关系（例如，财务、人力资源、制造业或企业资源计划）而创建的解决方案。 此外，许多特定于行业的应用现可用于解决自定义大型机解决方案以前解决的问题。

应考虑计划要最初迁移的工作负荷开始，然后确定移动关联应用程序、旧版代码库和数据库的需求。

## <a name="mainframe-emulation-in-azure"></a>Azure 中的大型机仿真

Azure 云服务可以仿真传统的大型机环境，由此可重用现有的大型机代码和应用程序。 可仿真的常见服务器组件包括联机事务处理 (OLTP)、批和数据引入系统。

### <a name="oltp-systems"></a>OLTP 系统

许多大型机都有 OLTP 系统，可以为大量用户处理数千或数百万次更新。 这些应用程序通常使用事务处理和屏幕窗体处理软件，例如客户信息控制系统 (CICS)、信息管理系统 (IMS) 和终端接口处理器 (TIP)。

将 OLTP 应用程序移至 Azure 时，大型机事务处理 (TP) 监视器的仿真器可以使用 Azure 上的虚拟机 (VM) 作为基础结构即服务 (IaaS) 运行。 此外可以通过 Web 服务器实现屏幕处理和窗体功能。 此方法可以与数据库 API 结合使用，例如 ActiveX 数据对象 (ADO)、开放式数据库连接 (ODBC) 和用于数据访问和事务的 Java 数据库连接 (JDBC)。

### <a name="time-constrained-batch-updates"></a>时间约束的批量更新

许多大型机系统每月或每年更新数百万个帐户记录，例如银行、保险和政府中使用的记录。 大型机通过提供高吞吐量数据处理系统来处理这些类型的工作负荷。 大型机批处理作业通常是串行的，并且取决于大型机主干提供的每秒输入/输出操作 (IOPS) 以提高性能。

基于云的批环境使用并行计算和高速网络来提高性能。 如果需要优化批性能，Azure 提供了各种计算、存储和网络选项。

### <a name="data-ingestion-systems"></a>数据引入系统

大型机从零售业、金融服务业、制造业和其他处理解决方案中获取大批量数据。 借助 Azure，可以使用简单的命令行实用程序（例如，[AzCopy](/azure/storage/common/storage-use-azcopy)）将数据复制到存储位置或从存储位置复制数据。 还可以使用 [Azure 数据工厂](/azure/data-factory/introduction)服务，由此可从不同的数据存储中引入数据，以便创建和计划数据驱动的工作流。

除了仿真环境，Azure 还提供可以增强现有大型机环境的平台即服务 (PaaS) 和分析服务。

## <a name="migrate-oltp-workloads-to-azure"></a>将 OLTP 工作负荷迁移到 Azure

直接迁移方法是将现有应用程序快速迁移到 Azure 的无代码选项。 每个应用程序按原样进行迁移，既发挥了云的优势，又无需承担更改代码所带来的风险或成本。 在 Azure 上使用用于大型机事务处理 (TP) 监视器的仿真器支持此方法。

TP 监视器可从各种供应商处获得，并在虚拟机上运行，这是 Azure 上的基础结构即服务 (IaaS) 选项。 下面的前后图显示了 IBM DB 2（关系数据库管理系统 (DBMS)）支持的联机应用程序在 IBM z/OS 大型机上的迁移。 用于 z/OS 的 DB2 使用虚拟存储访问方法 (VSAM) 文件来存储数据，并使用索引顺序访问方法 (ISAM) 来处理平面文件。 此体系结构还使用 CICS 进行事务监视。

![使用仿真软件将大型机环境直接迁移到 Azure](../../_images/mainframe-migration/mainframe-vs-azure.png)

在 Azure 上，仿真环境用于运行 TP 管理器和使用 JCL 的批处理作业。 在数据层中，DB2 替换为 [Azure SQL 数据库](/azure/sql-database/sql-database-technical-overview)，但也可以使用 Microsoft SQL Server、DB2 LUW 或 Oracle Database。 仿真器支持 IMS、VSAM 和 SEQ。 大型机的系统管理工具替换为在 VM 中运行的 Azure 服务和其他供应商提供的软件。

通常使用 Web 服务器实现屏幕处理和窗体输入功能，此功能可以与数据库 API（如ADO、ODBC 和 JDBC）结合使用，进行数据访问和事务处理。 要使用的 Azure IaaS 组件的确切阵容取决于你喜欢的操作系统。 例如：

- 基于 Windows 的 VM：用于屏幕处理和业务逻辑的 Internet Information Server (IIS) 以及 ASP.NET。 使用 ADO.NET 进行数据访问和事务处理。

- 基于 Linux 的 VM：提供基于 Java 的应用程序服务器，例如用于屏幕处理和基于 Java 的业务功能的 Apache Tomcat。 使用 JDBC 进行数据访问和事务处理。

## <a name="migrate-batch-workloads-to-azure"></a>将批工作负荷迁移到 Azure

Azure 中的批操作与大型机上的典型批环境不同。 大型机批处理作业通常是串行的，并且取决于大型机主干提供的 (IOPS) 以提高性能。 基于云的批环境使用并行计算和高速网络来提高性能。

要使用 Azure 优化批性能，请考虑[计算](/azure/virtual-machines/windows/overview)、[存储](/azure/storage/blobs/storage-blobs-introduction)、[网络](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)以及[监视](/azure/azure-monitor/overview)选项，如下所示。

### <a name="compute"></a>计算

使用：

- 具有最高时钟速度的 VM。 大型机应用程序通常是单线程的，并且大型机 CPU 具有非常高的时钟速度。

- 具有较大内存容量的 VM 允许缓存数据和应用程序工作区域。

- 如果应用程序支持多线程，具有更高密度 vCPU 的 VM 可以利用多线程处理。

- 并行处理，因为 Azure 可以轻松横向扩展以进行并行处理，为批运行提供更多的计算能力。

### <a name="storage"></a>存储

使用：

- [Azure 高级 SSD](/azure/virtual-machines/windows/premium-storage) 或 [Azure 超级 SSD](/azure/virtual-machines/windows/disks-ultra-ssd) 适用于最大的可用 IOPS。

- 使用多个磁盘进行条带化，以在每个存储大小中获得更多 IOPS。

- 对存储进行分区以在多个 Azure 存储设备上分配 IO。

### <a name="networking"></a>网络

- 使用 [Azure 加速网络](/azure/virtual-network/create-vm-accelerated-networking-powershell)以尽量降低延迟。

### <a name="monitoring"></a>监视

- 使用监视工具 [Azure Monitor](/azure/azure-monitor/overview)、[Azure Application Insights](/azure/application-insights/app-insights-overview)，甚至 Azure 日志使管理员能够监视批运行的任何过度性能并帮助消除瓶颈。

## <a name="migrate-development-environments"></a>迁移开发环境

云的分布式体系结构依赖于一组不同的开发工具，这些工具提供了新式做法和编程语言的优点。 要简化此转换，可以将开发环境与其他旨在仿真 IBM z/OS 环境的工具结合使用。 以下列表显示 Microsoft 和其他供应商提供的选项：

| 组件        | Azure 选项                                                                                                                                  |
|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|
| z/OS             | Windows、Linux 或 UNIX                                                                                                                      |
| CICS             | Azure 服务由 Micro Focus、Oracle、GT Software (Fujitsu)、TmaxSoft、Raincode 和 NTT 数据提供，或使用 kubernetes 重写 |
| IMS              | Micro Focus 和 Oracle 所提供的 Azure 服务                                                                                  |
| 汇编程序        | Raincode 和 TmaxSoft 提供的 Azure 服务；或 COBOL、C 或 Java，或映射到操作系统函数               |
| JCL              | JCL、PowerShell 或其他脚本工具                                                                                                   |
| COBOL            | COBOL、C 或 Java                                                                                                                            |
| Natural          | Natural、COBOL、C 或 Java                                                                                                                  |
| FORTRAN 和 PL/I | FORTRAN、PL/I、COBOL、C 或 Java                                                                                                           |
| REXX 和 PL/I    | REXX、PowerShell 或其他脚本工具                                                                                                  |

## <a name="migrate-databases-and-data"></a>将数据库和数据迁移

应用程序迁移通常涉及重新托管的数据层。 借助 [Azure 数据库迁移服务](/azure/dms/dms-overview)，可以将 SQL Server、开放源代码和其他关系数据库迁移到 Azure 上完全托管的解决方案，例如 [Azure SQL 数据库托管实例](/azure/sql-database/sql-database-managed-instance)、[Azure Database Service for PostgreSQL](/azure/postgresql/overview) 和 [Azure Database for MySQL](/azure/mysql/overview)。

例如，如果大型机数据层使用以下项，则可以进行迁移：

- IBM DB2 数据库或 IMS 数据库，请在 Azure 上使用 Azure SQL 数据库、SQL Server、DB2 LUW 或 Oracle Database。

- VSAM 和其他平面文件，使用适用于 Azure SQL、SQL Server、DB2 LUW 或 Oracle 的索引顺序访问方法 (ISAM) 平面文件。

- Generation Date Groups (GDG)，迁移到 Azure 上使用命名约定的文件和提供与 GDG 类似功能的文件扩展名的文件。

IBM 数据层包括一些也必须迁移的关键组件。 例如，迁移数据库时，还要迁移池中包含的数据集合（每个数据集合都包含 dbextents，其中 dbextents 是 z/OS VSAM 数据集）。 迁移必须包括标识存储池中数据位置的目录。 此外，迁移计划必须考虑数据库日志，其中包含对数据库执行操作的记录。 数据库可以有一个、两个（双日志或备用日志）日志或四个（双日志和备用日志）日志。

数据库迁移还包含以下组件：

- 数据库管理器：提供对数据库中数据的访问。 在 z/OS 环境中，数据库管理器可在其自己的分区中运行。

- 应用程序请求方：在将请求传递到应用程序服务器之前，接受来自应用程序的请求。

- 联机资源适配器：包括用于 CICS 事务的应用程序请求程序组件。

- Batch 资源适配器:Z/OS 批应用程序的实现应用程序请求方组件。

- 交互式 SQL (ISQL)：作为 CICS 应用程序和接口运行，使用户能够输入 SQL 语句或运算符命令。

- CICS 应用程序：使用 CICS 中的可用资源和数据源在 CICS 的控制下运行。

- Batch 应用程序：运行流程逻辑而无需与用户进行交互式通信，例如，生成大量数据更新或从数据库生成报告。

## <a name="optimize-scale-and-throughput-for-azure"></a>优化 Azure 的规模和吞吐量

通常情况下，大型机纵向扩展，而云横向扩展。要优化在 Azure 上运行的大型机样式应用程序的规模和吞吐量，了解大型机如何分离和隔离应用程序是非常重要的。 A z/OS 大型机使用名为逻辑分区 (LPARS) 的功能来隔离和管理单个实例上特定应用程序的资源。

例如，大型机对具有关联 COBOL 程序的 CICS 区域使用一个逻辑分区 (LPAR)，对于 DB2 使用单独的 LPAR。 其他 LPAR 通常用于开发、测试和过渡环境。

在 Azure 上，更常见的做法是使用单独的 VM 来实现这一目的。 Azure 体系结构通常为应用层部署 VM、为数据层部署单独一组 VM、为开发部署另一组 VM 等。 每个处理层都可以使用最适合该环境的 VM 类型和功能进行优化。

此外，每一层还可以提供适当的灾难恢复服务。 例如，生产和数据库 VM 可能需要热或温恢复，而开发和测试 VM 支持冷恢复。

下图显示了使用主站点和辅助站点进行 Azure 部署的可能性。 在主站点中，以高可用性部署生产、预生产和测试 VM。 辅助站点用于备份和灾难恢复。

![使用主站点和辅助站点进行 Azure 部署的可能性](../../_images/mainframe-migration/migration-backup-DR.png)

## <a name="perform-a-staged-mainframe-to-azure"></a>对 Azure 执行分阶段大型机任务

将解决方案从大型机移至 Azure 可能需要进行分阶段迁移，从而先移动一些应用程序，而其他应用程序则暂时或永久地保留在大型机上。 此方法通常需要允许应用程序和数据库在大型机和 Azure 之间进行互操作的系统。

常见方案是将应用程序移至 Azure，同时将应用程序使用的数据保存在大型机上。 特定软件用于使 Azure 上的应用程序能够访问大型机中的数据。 幸运的是，广泛的解决方案提供 Azure 和现有大型机环境之间的集成、对混合场景的支持以及随时间推移的迁移。 Microsoft 合作伙伴、独立软件供应商和系统集成商可以在过程中为你提供帮助。

一种选项是 [Microsoft Host Integration Server](https://docs.microsoft.com/host-integration-server/) (HIS)，该解决方案提供了 Azure 中的应用程序访问大型机上保留的 DB2 中的数据所需的分布式关系数据库体系结构 (DRDA)。 大型机到 Azure 集成的其他选项包括 IBM、Attunity、Codit 和其他供应商提供的解决方案以及开源选项。

## <a name="partner-solutions"></a>合作伙伴解决方案

如果正在考虑大型机迁移，合作伙伴生态系统可提供帮助。

Azure 为当前在大型机上运行的系统提供了经过验证的、高度可用且可缩放的基础结构。 可相对轻松地迁移某些工作负荷。 可以使用合作伙伴解决方案重新托管依赖于旧版系统软件（如 CICS 和 IMS）的其他工作负荷，并随着时间的推移迁移到 Azure。 无论你做出何种选择，Microsoft 和我们的合作伙伴都可以帮助你优化 Azure，同时保持大型机系统软件功能。

有关选择合作伙伴解决方案的详细指南，请参阅[平台现代化联盟](https://www.platformmodernization.org/pages/mainframe.aspx)。

## <a name="learn-more"></a>了解详细信息

有关详细信息，请参阅以下资源：

- [Azure 入门](/azure)

- [平台现代化联盟：大型机迁移](https://www.platformmodernization.org/pages/mainframe.aspx)

- [Deploy IBM DB2 pureScale on Azure](https://azure.microsoft.com/resources/deploy-ibm-db2-purescale-on-azure)（在 Azure 上部署 IBM DB2 pureScale）

- [Host Integration Server (HIS) 文档](https://docs.microsoft.com/host-integration-server/)
