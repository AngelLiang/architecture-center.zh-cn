---
title: 从数据损坏或意外删除中恢复
description: 了解如何从数据损坏或意外数据删除中恢复、如何设计有复原能力和高可用性的容错应用程序，以及如何对灾难恢复进行规划。
author: MikeWasson
ms.date: 11/11/2018
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-design-principles
ms.custom: resiliency
ms.openlocfilehash: e28f26683c6d7dba196d4351ef3942830c9e7fc2
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54486291"
---
# <a name="recover-from-data-corruption-or-accidental-deletion"></a><span data-ttu-id="15961-103">从数据损坏或意外删除中恢复</span><span class="sxs-lookup"><span data-stu-id="15961-103">Recover from data corruption or accidental deletion</span></span>

<span data-ttu-id="15961-104">健全的业务连续性计划的一部分是针对数据损坏或意外删除制定计划。</span><span class="sxs-lookup"><span data-stu-id="15961-104">Part of a robust business continuity plan is having a plan if your data gets corrupted or accidentally deleted.</span></span> <span data-ttu-id="15961-105">下面提供有关应用程序错误或操作人员失误造成数据损坏或意外删除后进行恢复的信息。</span><span class="sxs-lookup"><span data-stu-id="15961-105">The following is information about recovery after data has been corrupted or accidentally deleted, due to application errors or operator error.</span></span>

## <a name="virtual-machines"></a><span data-ttu-id="15961-106">虚拟机</span><span class="sxs-lookup"><span data-stu-id="15961-106">Virtual Machines</span></span>

<span data-ttu-id="15961-107">若要在发生应用程序错误或意外删除后保护 Azure 虚拟机 (VM)，可以使用 [Azure 备份](/azure/backup/)。</span><span class="sxs-lookup"><span data-stu-id="15961-107">To protect Azure Virtual Machines (VMs) from application errors or accidental deletion, use [Azure Backup](/azure/backup/).</span></span> <span data-ttu-id="15961-108">使用 Azure 备份可跨多个 VM 磁盘创建一致的备份。</span><span class="sxs-lookup"><span data-stu-id="15961-108">Azure Backup enables the creation of backups that are consistent across multiple VM disks.</span></span> <span data-ttu-id="15961-109">此外，可以跨区域复制备份保管库，以便在发生区域服务中断时进行恢复。</span><span class="sxs-lookup"><span data-stu-id="15961-109">In addition, the Backup Vault can be replicated across regions to provide recovery from region loss.</span></span>

## <a name="storage"></a><span data-ttu-id="15961-110">存储</span><span class="sxs-lookup"><span data-stu-id="15961-110">Storage</span></span>

<span data-ttu-id="15961-111">Azure 存储通过自动化副本提供数据复原能力。</span><span class="sxs-lookup"><span data-stu-id="15961-111">Azure Storage provides data resiliency through automated replicas.</span></span> <span data-ttu-id="15961-112">但是，这不会防止应用程序代码或用户有意或无意损坏数据。</span><span class="sxs-lookup"><span data-stu-id="15961-112">However, this does not prevent application code or users from corrupting data, whether accidentally or maliciously.</span></span> <span data-ttu-id="15961-113">在遇到应用程序或用户错误时保持数据保真需要更为先进的技术，如将数据和审核日志复制到辅助存储位置。</span><span class="sxs-lookup"><span data-stu-id="15961-113">Maintaining data fidelity in the face of application or user error requires more advanced techniques, such as copying the data to a secondary storage location with an audit log.</span></span>

- <span data-ttu-id="15961-114">**块 Blob**。</span><span class="sxs-lookup"><span data-stu-id="15961-114">**Block blobs**.</span></span> <span data-ttu-id="15961-115">创建每个块 Blob 的时间点快照。</span><span class="sxs-lookup"><span data-stu-id="15961-115">Create a point-in-time snapshot of each block blob.</span></span> <span data-ttu-id="15961-116">有关详细信息，请参阅 [Creating a Snapshot of a Blob](/rest/api/storageservices/creating-a-snapshot-of-a-blob)（创建 Blob 的快照）。</span><span class="sxs-lookup"><span data-stu-id="15961-116">For more information, see [Creating a Snapshot of a Blob](/rest/api/storageservices/creating-a-snapshot-of-a-blob).</span></span> <span data-ttu-id="15961-117">对于每个快照，只需要支付存储自上次快照状态以来 Blob 内的更改所需的存储空间费用。</span><span class="sxs-lookup"><span data-stu-id="15961-117">For each snapshot, you are only charged for the storage required to store the differences within the blob since the last snapshot state.</span></span> <span data-ttu-id="15961-118">快照依赖于它们所基于的原始 Blob 的存在，因此建议复制到另一个 Blob 甚至另一个存储帐户。</span><span class="sxs-lookup"><span data-stu-id="15961-118">The snapshots are dependent on the existence of the original blob they are based on, so a copy operation to another blob or even another storage account is advisable.</span></span> <span data-ttu-id="15961-119">这可以确保正确保护备份数据不受意外删除的影响。</span><span class="sxs-lookup"><span data-stu-id="15961-119">This ensures that backup data is properly protected against accidental deletion.</span></span> <span data-ttu-id="15961-120">可以使用 [AzCopy](/azure/storage/common/storage-use-azcopy) 或 [Azure PowerShell](/azure/storage/common/storage-powershell-guide-full) 将 Blob 复制到其他存储帐户。</span><span class="sxs-lookup"><span data-stu-id="15961-120">You can use [AzCopy](/azure/storage/common/storage-use-azcopy) or [Azure PowerShell](/azure/storage/common/storage-powershell-guide-full) to copy the blobs to another storage account.</span></span>

- <span data-ttu-id="15961-121">**文件**。</span><span class="sxs-lookup"><span data-stu-id="15961-121">**Files**.</span></span> <span data-ttu-id="15961-122">使用[共享快照](/azure/storage/files/storage-snapshots-files)、AzCopy 或 PowerShell 将文件复制到其他存储帐户。</span><span class="sxs-lookup"><span data-stu-id="15961-122">Use [share snapshots](/azure/storage/files/storage-snapshots-files), or use AzCopy or PowerShell to copy your files to another storage account.</span></span>

- <span data-ttu-id="15961-123">**表**。</span><span class="sxs-lookup"><span data-stu-id="15961-123">**Tables**.</span></span> <span data-ttu-id="15961-124">使用 AzCopy 将表数据导出到其他区域中的其他存储帐户。</span><span class="sxs-lookup"><span data-stu-id="15961-124">Use AzCopy to export the table data into another storage account in another region.</span></span>

## <a name="database"></a><span data-ttu-id="15961-125">数据库</span><span class="sxs-lookup"><span data-stu-id="15961-125">Database</span></span>

### <a name="azure-sql-database"></a><span data-ttu-id="15961-126">Azure SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="15961-126">Azure SQL Database</span></span>

<span data-ttu-id="15961-127">SQL 数据库每周自动执行完整数据库备份，每小时自动执行差异数据库备份，每 5 到 10 分钟自动执行事务日志备份，防止企业丢失数据。</span><span class="sxs-lookup"><span data-stu-id="15961-127">SQL Database automatically performs a combination of full database backups weekly, differential database backups hourly, and transaction log backups every five - ten minutes to protect your business from data loss.</span></span> <span data-ttu-id="15961-128">使用时间点还原将数据库还原到以前的某个时间。</span><span class="sxs-lookup"><span data-stu-id="15961-128">Use point-in-time restore to restore a database to an earlier time.</span></span> <span data-ttu-id="15961-129">有关详细信息，请参阅：</span><span class="sxs-lookup"><span data-stu-id="15961-129">For more information, see:</span></span>

- [<span data-ttu-id="15961-130">使用自动数据库备份恢复 Azure SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="15961-130">Recover an Azure SQL database using automated database backups</span></span>](/azure/sql-database/sql-database-recovery-using-backups)

- [<span data-ttu-id="15961-131">使用 Azure SQL 数据库确保业务连续性的相关概述</span><span class="sxs-lookup"><span data-stu-id="15961-131">Overview of business continuity with Azure SQL Database</span></span>](/azure/sql-database/sql-database-business-continuity)

### <a name="sql-server-on-vms"></a><span data-ttu-id="15961-132">VM 上的 SQL Server</span><span class="sxs-lookup"><span data-stu-id="15961-132">SQL Server on VMs</span></span>

<span data-ttu-id="15961-133">对于 VM 上运行的 SQL Server，可以使用两个选项：传统备份和日志传送。</span><span class="sxs-lookup"><span data-stu-id="15961-133">For SQL Server running on VMs, there are two options: traditional backups and log shipping.</span></span> <span data-ttu-id="15961-134">使用传统备份可以还原到某个特定的时间点，但恢复过程较慢。</span><span class="sxs-lookup"><span data-stu-id="15961-134">Traditional backups enables you to restore to a specific point in time, but the recovery process is slow.</span></span> <span data-ttu-id="15961-135">还原传统备份要求从初始的完整备份开始，然后应用在该备份之后创建的所有备份。</span><span class="sxs-lookup"><span data-stu-id="15961-135">Restoring traditional backups requires starting with an initial full backup, and then applying any backups taken after that.</span></span> <span data-ttu-id="15961-136">第二个选项是配置日志传送会话，延迟日志备份的还原（例如，延迟两小时）。</span><span class="sxs-lookup"><span data-stu-id="15961-136">The second option is to configure a log shipping session to delay the restore of log backups (for example, by two hours).</span></span> <span data-ttu-id="15961-137">这就为从主要数据库发生的错误恢复提供了时间窗口。</span><span class="sxs-lookup"><span data-stu-id="15961-137">This provides a window to recover from errors made on the primary.</span></span>

### <a name="azure-cosmos-db"></a><span data-ttu-id="15961-138">Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="15961-138">Azure Cosmos DB</span></span>

<span data-ttu-id="15961-139">Azure Cosmos DB 会定期自动创建备份。</span><span class="sxs-lookup"><span data-stu-id="15961-139">Azure Cosmos DB automatically takes backups at regular intervals.</span></span> <span data-ttu-id="15961-140">备份单独存储在另一个存储服务中并在全球复制，以便在发生区域性的灾难时可以复原。</span><span class="sxs-lookup"><span data-stu-id="15961-140">Backups are stored separately in another storage service, and those backups are globally replicated for resiliency against regional disasters.</span></span> <span data-ttu-id="15961-141">如果意外删除了数据库或集合，可以提交支持票证或联系 Azure 支持，以便从上一次自动备份中还原数据。</span><span class="sxs-lookup"><span data-stu-id="15961-141">If you accidentally delete your database or collection, you can file a support ticket or call Azure support to restore the data from the last automatic backup.</span></span> <span data-ttu-id="15961-142">有关详细信息，请参阅[使用 Azure Cosmos DB 自动执行在线备份和还原](/azure/cosmos-db/online-backup-and-restore)。</span><span class="sxs-lookup"><span data-stu-id="15961-142">For more information, see [Automatic online backup and restore with Azure Cosmos DB](/azure/cosmos-db/online-backup-and-restore).</span></span>

### <a name="azure-database-for-mysql-azure-database-for-postgresql"></a><span data-ttu-id="15961-143">Azure Database for MySQL、Azure Database for PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="15961-143">Azure Database for MySQL, Azure Database for PostgreSQL</span></span>

<span data-ttu-id="15961-144">使用 Azure Database for MySQL 或 Azure Database for PostgreSQL 时，数据库服务每隔 5 分钟自动备份服务。</span><span class="sxs-lookup"><span data-stu-id="15961-144">When using Azure Database for MySQL or Azure Database for PostgreSQL, the database service automatically makes a backup of the service every five minutes.</span></span> <span data-ttu-id="15961-145">通过此自动备份功能，用户可将服务器及其所有数据库还原到某个较早时间点，并还原到新服务器。</span><span class="sxs-lookup"><span data-stu-id="15961-145">Using this automatic backup feature you may restore the server and all its databases into a new server to an earlier point-in-time.</span></span> <span data-ttu-id="15961-146">有关详细信息，请参阅：</span><span class="sxs-lookup"><span data-stu-id="15961-146">For more information, see:</span></span>

- [<span data-ttu-id="15961-147">如何使用 Azure 门户在 Azure Database for MySQL 中备份和还原服务器</span><span class="sxs-lookup"><span data-stu-id="15961-147">How to back up and restore a server in Azure Database for MySQL by using the Azure portal</span></span>](/azure/mysql/howto-restore-server-portal)

- [<span data-ttu-id="15961-148">如何使用 Azure 门户在 Azure Database for PostgreSQL 中备份和还原服务器</span><span class="sxs-lookup"><span data-stu-id="15961-148">How to backup and restore a server in Azure Database for PostgreSQL using the Azure portal</span></span>](/azure/postgresql/howto-restore-server-portal)
