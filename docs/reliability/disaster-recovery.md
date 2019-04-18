---
title: Azure 应用程序的故障和灾难恢复
description: 在 Azure 中概述的灾难恢复方法
author: MikeWasson
ms.date: 04/10/2019
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-design-principles
ms.openlocfilehash: f00341b06b8092c798d6260317226190cbace66b
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59646494"
---
# <a name="failure-and-disaster-recovery-for-azure-applications"></a>Azure 应用程序的故障和灾难恢复

*灾难恢复*是还原应用程序中的灾难性损失唤醒功能的过程。

发生灾难期间降低的功能的容忍程度是从到下一个应用程序而异的业务决策。 它可能是可接受的某些应用程序是完全不可用或部分可用并且具有降低的功能，或者延迟一段时间的处理。 对于其他应用程序，任何缩减的功能是不可接受的。

## <a name="disaster-recovery-plan"></a>灾难恢复计划

首先创建恢复计划。 经过充分测试后，该计划被视为完成。 包括人员、 流程和还原中已为你的客户定义的服务级别协议 (SLA) 的功能所需的应用程序。

请考虑以下建议在创建和测试灾难恢复计划时：

- 在计划中，包括与支持部门联系以及升级事务的过程。 此信息将有助于避免长时间的停机时间，因为第一次解决恢复过程。
- 评估应用程序故障造成的业务影响。
- 选择用于关键任务应用程序的跨区域恢复体系结构。
- 标识包括自动化和测试在内的灾难恢复计划的特定所有者。
- 记录的过程，尤其是任何手动步骤。
- 自动执行尽可能多地过程。
- 定期建立的所有引用和事务数据和测试备份还原的备份策略。
- 设置您的应用程序所使用的 Azure 服务堆栈的警报。
- 培训操作人员执行计划。
- 执行常规灾难模拟以验证并改进计划。

如果您使用的[Azure Site Recovery](/azure/site-recovery/)若要复制的虚拟机 (Vm)，创建完全自动的恢复计划故障转移整个应用程序。

## <a name="manual-responses"></a>手动响应

尽管自动化是理想之选，但某些灾难恢复策略将需要手动响应。

### <a name="alerts"></a>警报

监视应用程序中是否出现了可能需要主动干预的警示。 例如，如果 Azure SQL 数据库或 Azure Cosmos DB 在不断地限制应用程序，您可能需要增大数据库容量或优化查询。 即使应用程序可能会以透明方式处理限制错误，遥测也仍应该引发警报，以便能够跟进。

有关服务限制和配额阈值，我们建议对 Azure 资源指标和诊断日志配置警报。 如果可能，请设置指标，将延迟低于诊断日志的警报。

通过 [资源运行状况](/azure/service-health/resource-health-checks-resource-types)，Azure 提供了一些内置的运行状况状态检查可帮助你诊断 Azure 服务限制问题。

### <a name="failover"></a>故障转移

配置每个 Azure 应用程序和其 Azure 服务的灾难恢复策略。 可接受的部署策略，以支持灾难恢复可能会因所需的所有组件的每个应用程序的 Sla。  

Azure 提供不同功能在许多 Azure 服务中以允许为手动故障转移，如 [redis 缓存地域副本](/azure/azure-cache-for-redis/cache-how-to-geo-replication)，或为自动故障转移，如 [SQL 自动故障转移组](/azure/sql-database/sql-database-auto-failover-group)。 例如：

- 对于主要使用虚拟机的应用程序，可以为 web 应用和逻辑层使用 Azure Site Recovery。 有关详细信息，请参阅[Azure 到 Azure 灾难恢复体系结构](/azure/site-recovery/azure-to-azure-architecture)。 对于 Vm 上的 SQL Server，请使用[SQL Server Always On 可用性组](/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-dr)。
- 对于使用应用服务和 Azure SQL 数据库的应用程序，可以使用较小的层应用服务计划配置在次要区域发生故障转移时哪些措施。 对数据库层使用故障转移组。

在任一方案中， [Azure 流量管理器](/azure/traffic-manager/traffic-manager-overview) 跨区域提供自动故障转移配置文件。 [负载均衡器](/azure/load-balancer/load-balancer-overview)或 [应用程序网关](/azure/application-gateway/overview) 应设置次要区域中来支持更快故障转移时的可用性。

### <a name="operational-readiness-testing"></a>操作就绪性测试

针对故障转移到次要区域和故障回复到主要区域，请执行操作就绪性测试。 许多 Azure 服务支持通过手动故障转移或测试性故障转移进行灾难恢复演练。 或者，你可以通过关闭或删除 Azure 服务模拟服务中断。

## <a name="application-failure"></a>应用程序故障

应用程序故障是可恢复或不可恢复。 您可以缓解可恢复的错误，但不可恢复的错误会使应用程序停止。

- 可以通过自动处理故障并采取备用措施以透明方式解决一些失败。 例如，流量管理器会自动处理主机虚拟机中的底层硬件或操作系统软件导致失败。
- 但出现一些错误，应用程序可以继续处理用户请求，并降低的功能。
- 更严重的服务中断可能会使应用程序不可用。

设计良好的系统将在服务级别的职责分隔开来&mdash;在设计时和运行时。 这种分离可防止中断导致整个应用程序依赖的服务中断。 例如，考虑以下模块与 web 电子商务应用程序：

![将链接添加到艺术](./_images/disaster-recovery.png)

如果托管订单数据库出现故障，订单处理服务无法处理销售事务。 根据体系结构，它可能是不可能为订单提交和订单处理服务，以继续。 但是，如果产品数据存储在不同的位置中，产品目录是仍然可用，即使应用程序的其他部分可能不可用。

由你来确定应用程序将如何通知用户的所有临时问题并生成到系统的相应通知。 在上一示例中，应用程序可能允许查看产品以及将它们添加到购物车。 但是，当客户尝试进行购买，应用程序应通知订购功能是暂时不可用。 尽管不适合客户，这种方法可以防止应用程序范围的服务中断。

## <a name="data-corruption-and-restoration"></a>数据损坏和还原

如果数据存储区失败，可能有数据不一致时再次可用，尤其是如果复制了数据。 了解恢复时间目标 (RTO) 和恢复点目标 (RPO) 的复制的数据存储可以帮助您预测的数据丢失量。

若要了解跨区域故障转移是否已启动手动或通过 Microsoft，查看 Azure 服务 Sla。 对于与没有相应的 Sla 跨区域故障转移的服务，Microsoft 通常决定何时将故障转移，并通常优先恢复主要区域中的数据。 如果主要区域中的数据被视为不可恢复，Microsoft 故障转移到次要区域。

### <a name="restoring-data-from-backups"></a>从备份还原数据

备份保护您免受因意外删除或数据损坏导致的应用程序组件的丢失。 它们保留从较早的时间，可用于将其还原组件的功能版本。

灾难恢复策略也不能取代对于备份，但定期备份应用程序数据支持某些灾难恢复方案。 备份存储选项应基于您的灾难恢复策略。

运行备份过程的频率决定 RPO。 例如，如果执行每小时备份，而灾难发生在备份之前两分钟，你将失去 58 分钟的数据。 灾难恢复计划应包括将如何处理丢失的数据。

它是常见的一种数据存储到另一个存储中的引用数据中的数据。 例如，考虑具有一列的 SQL 数据库链接到 Azure 存储中的 blob。 如果备份不会同时发生，数据库可能有一个指针指向在发生故障之前未备份的 blob。 应用程序或灾难恢复计划必须实现过程来恢复之后处理这种不一致。

> [!NOTE]
> 在某些情况下，例如，使用备份的 Vm [Azure 备份](/azure/backup/backup-azure-vms-first-look-arm)，可以仅从同一区域中的备份还原。 其他 Azure 服务，如[适用于 Redis 的 Azure 缓存](/azure/azure-cache-for-redis/cache-how-to-geo-replication)，提供异地复制的备份，可以使用跨区域还原服务。

### <a name="azure-storage-and-azure-sql-database"></a>Azure 存储和 Azure SQL 数据库

Azure 自动将三次不同的容错域中的 Azure 存储和 SQL 数据库数据存储在同一区域中。 如果使用异地复制，则再将这些数据在另一个区域内存储三次。 但是，如果数据已损坏，或在主要副本中删除 （例如，由于用户错误），所做的更改复制到其他副本。

具有用于管理潜在数据损坏或删除的两个选项：

- **创建自定义备份策略。** 您可以将备份存储在 Azure 或本地，具体取决于你的业务要求和治理监管。
- **使用时间点还原选项**来恢复 SQL 数据库。

#### <a name="azure-storage-recovery"></a>Azure 存储恢复

可以为 Azure 存储开发自定义备份过程，也可以使用许多第三方备份工具之一。

Azure 存储提供数据复原能力，通过自动化副本，但它不会阻止应用程序代码或用户会损坏数据。 保持数据保真应用程序或用户错误后的需要更高级的技术，如将数据复制到辅助存储位置和审核日志。 有几种选项：

- [**块 blob。**](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) 创建每个块 Blob 的时间点快照。 对于每个快照，则收取仅用于存储自上一快照状态以来 blob 内的更改所需的存储。 因此，我们建议将复制到另一个 blob，或甚至另一个存储帐户时，依赖于原始 blob 快照。 此方法可确保保护备份数据不受意外删除。 使用[AzCopy](/azure/storage/common/storage-use-azcopy)或[Azure PowerShell](/azure/storage/common/storage-powershell-guide-full)将 blob 复制到另一个存储帐户。

    有关详细信息，请参阅 [Creating a Snapshot of a Blob](/rest/api/storageservices/creating-a-snapshot-of-a-blob)（创建 Blob 的快照）。

- [**Azure 文件。**](/azure/storage/files/storage-files-introduction) 使用[共享快照](/azure/storage/files/storage-snapshots-files)，AzCopy 或 PowerShell 将文件复制到另一个存储帐户。
- [**Azure 表存储。**](/azure/storage/tables/table-storage-overview) 使用 AzCopy 将表数据导出到其他区域中的其他存储帐户。

#### <a name="sql-database-recovery"></a>SQL 数据库恢复

若要从数据丢失，SQL 数据库自动保护你的业务执行完整数据库备份的组合每周、 每小时、 差异数据库备份和事务日志备份每 5 到 10 分钟。 对于基本、 标准和高级 SQL 数据库层中，使用时间点还原到还原到较早的时间的数据库。 有关详细信息，请参阅以下文章：

- [使用自动数据库备份恢复 Azure SQL 数据库](/azure/sql-database/sql-database-recovery-using-backups)
- [使用 Azure SQL 数据库确保业务连续性的相关概述](/azure/sql-database/sql-database-business-continuity)

另一种选择是要用于自动将数据库更改复制到相同或不同 Azure 区域中的辅助数据库的 SQL 数据库活动异地复制。 有关详细信息，请参阅[创建和使用活动异地复制](/azure/sql-database/sql-database-active-geo-replication)。

您还可以使用手动程度更高的方法进行备份和还原：

- 使用**数据库复制**命令以创建事务上一致的数据库的备份副本。
- 使用 Azure SQL 数据库导入/导出服务，它支持将数据库导出到 BACPAC 文件 （包含数据库架构和关联的数据压缩文件） 存储在 Azure Blob 存储中。 若要防止区域范围的服务中断，可将 BACPAC 文件复制到备用区域。

### <a name="sql-server-on-vms"></a>VM 上的 SQL Server

对于在 Vm 上运行的 SQL Server，有两个选项： 传统备份和日志传送。

- 与传统的备份，可以还原到某个特定点的时间，但恢复过程较慢。 还原传统备份要求先进行初始的完整备份，然后将应用所有增量备份。
- 你可以配置日志传送会话，延迟日志备份的还原。 这提供了一个窗口来从主要副本上进行的错误中恢复。

### <a name="azure-database-for-mysql-and-azure-database-for-postgresql"></a>Azure Database for MySQL 和 Azure Database for PostgreSQL

在 Azure Database for MySQL 和 PostgreSQL 的 Azure 数据库，数据库服务每隔五分钟自动进行备份。 可以使用这些自动的备份还原服务器和其数据库从早的时间点的时间到新的服务器。 有关详细信息，请参阅：

- [如何使用 Azure 门户在 Azure Database for MySQL 中备份和还原服务器](/azure/mysql/howto-restore-server-portal)
- [如何备份和还原使用 Azure 门户的 PostgreSQL 的 Azure 数据库中的服务器](/azure/postgresql/howto-restore-server-portal)

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Cosmos DB 自动定期进行备份。 备份单独存储在另一个存储服务，全球复制，以防受到区域灾难影响。 如果意外删除了数据库或集合，可以提交支持票证或联系 Azure 支持，以便从上一次自动备份中还原数据。 有关详细信息，请参阅[联机备份和 Azure Cosmos DB 中的按需还原](/azure/cosmos-db/online-backup-and-restore)。

### <a name="azure-virtual-machines"></a>Azure 虚拟机

若要从应用程序错误或意外删除后保护 Azure 虚拟机，请使用[Azure 备份](/azure/backup/)。 创建的备份是一致的跨多个 VM 磁盘。 此外，Azure 备份保管库可以进行跨区域以支持从区域损失恢复复制。

## <a name="network-outage"></a>网络中断

当 Azure 网络的某些部分中断时，您可能不能访问你的应用程序或数据。 在这种情况下，我们建议设计灾难恢复策略，以运行大多数应用程序功能减弱。

如果减弱功能不会选择使用，剩余的选项将为应用程序停机时间或故障转移到备用区域。

在缩减的功能方案中：

- 如果你的应用程序不能因 Azure 网络中断而访问其数据，您可以通过使用缓存的数据与应用程序功能减弱本地运行。
- 您可能能够将数据存储在其他位置，直到连接恢复。

## <a name="dependent-service-failure"></a>从属服务故障

对于每个依赖服务，你应了解的服务中断以及应用程序将响应的方式的影响。 许多服务包括支持复原能力和可用性，因此，单独评估每个服务可能会提高灾难恢复计划的功能。 例如，Azure 事件中心支持[故障转移](/azure/event-hubs/event-hubs-geo-dr#setup-and-failover-flow)到辅助命名空间。

## <a name="region-wide-service-disruptions"></a>区域范围的服务中断

许多故障是同一个 Azure 区域内可管理的。 但是，在不太可能事件中的全区域服务中断，你的数据的本地冗余副本不可用。 如果已启用异地复制，有 blob 和表在不同区域中的 3 个副本。 如果 Microsoft 声明区域丢失，Azure 将重新映射到次要区域的所有 DNS 条目。

> [!NOTE]
> 此过程仅对区域范围的服务中断的进行，不在控件内。 考虑使用 [Azure Site Recovery](/azure/site-recovery/) 获取更有利的 RPO 和 RTO。 使用 Site Recovery，决定什么是可接受的服务中断以及何时故障转移到复制的 Vm。

对区域范围的服务中断的响应取决于你的部署和灾难恢复计划。

- 作为一种成本控制策略，对于非关键应用程序不需要保证的恢复时间，可能会有用才能重新部署到不同的区域。
- 对于托管在与已部署角色的另一个区域中但不在区域间分布流量的应用程序 (*主动/被动部署*)，切换到备用区域中的辅助托管服务。
- 对于在另一个区域中具有全面的辅助部署应用程序 (*主动/主动部署*)，将流量路由到该区域。

若要了解有关的全区域服务中断后恢复的详细信息，请参阅[的全区域服务中断后恢复](../resiliency/recovery-loss-azure-region.md)。

### <a name="vm-recovery"></a>VM 恢复

对于关键应用程序，计划发生区域范围的服务中断时恢复 Vm。

- 使用 Azure 备份或另一种备份方法来创建应用程序一致的跨区域备份。 （备份保管库的复制必须在配置的创建时间。）
- 使用 Site Recovery 来复制跨区域进行一次单击应用程序故障转移和故障转移测试。
- 使用流量管理器自动执行用户流量故障转移到另一个区域。

若要了解详细信息，请参阅[恢复的全区域服务中断后，虚拟机](../resiliency/recovery-loss-azure-region.md#virtual-machines)。

### <a name="storage-recovery"></a>存储恢复

若要在发生区域范围的服务中断时保护你的存储：

- 使用异地冗余存储。
- 知道你的存储是异地复制。 这会影响你在其中部署你需要与你的存储保持区域关系的数据的其他实例。
- 故障转移后检查数据的一致性，并且如有必要，还原从备份。

若要了解详细信息，请参阅[设计高度可用的应用程序使用 RA-GRS](/azure/storage/common/storage-designing-ha-apps-with-ragrs)。

### <a name="sql-database-and-sql-server"></a>SQL 数据库和 SQL Server

Azure SQL 数据库提供两种类型的恢复：

- 使用异地还原从另一个区域中的备份副本还原数据库。 有关详细信息，请参阅[恢复 Azure SQL 数据库使用自动的数据库备份](/azure/sql-database/sql-database-recovery-using-backups)。
- 使用活动异地复制故障转移到辅助数据库。 有关详细信息，请参阅[创建和使用活动异地复制](/azure/sql-database/sql-database-active-geo-replication)。

在 Vm 上运行的 SQL Server，请参阅[的 Azure 虚拟机中 SQL Server 的高可用性和灾难恢复](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr/)。

## <a name="service-specific-guidance"></a>服务特定指南

以下文章介绍了特定 Azure 服务的灾难恢复：

| 服务                       | 文章                                                                                                                                                                                       |
|-------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Database for MySQL      | [有关使用 Azure Database for MySQL 确保业务连续性的概述](/azure/mysql/concepts-business-continuity)                                                  |
| Azure Database for PostgreSQL | [有关使用 Azure Database for PostgreSQL 确保业务连续性的概述](/azure/postgresql/concepts-business-continuity)                                        |
| Azure 云服务          | [发生影响 Azure 云服务的 Azure 服务中断时该怎么办](/azure/cloud-services/cloud-services-disaster-recovery-guidance) |
| Cosmos DB                     | [使用 Azure Cosmos DB 的高可用性](/azure/cosmos-db/high-availability)                                                                                |
| Azure 密钥保管库               | [Azure Key Vault 可用性和冗余](/azure/key-vault/key-vault-disaster-recovery-guidance)                                                        |
| Azure 存储                 | [Azure 存储中的灾难恢复和存储帐户故障转移（预览版）](/azure/storage/common/storage-disaster-recovery-guidance)                       |
| SQL 数据库                  | [还原 Azure SQL 数据库或故障转移到次要区域](/azure/sql-database/sql-database-disaster-recovery)                                       |
| 虚拟机              | [要执行 Azure 发生服务中断时影响 Azure 云](/azure/cloud-services/cloud-services-disaster-recovery-guidance)               |
| Azure 虚拟网络         | [虚拟网络 - 业务连续性](/azure/virtual-network/virtual-network-disaster-recovery-guidance)                                                  |

## <a name="next-steps"></a>后续步骤

- [从数据损坏或意外删除中恢复](../resiliency/recovery-data-corruption.md)
- [全区域服务中断后恢复](../resiliency/recovery-loss-azure-region.md)