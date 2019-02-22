---
title: 数据分区策略
titleSuffix: Best practices for cloud applications
description: 有关隔离数据分区以便对其进行单独管理和访问的指导。
author: dragon119
ms.date: 11/04/2018
ms.topic: best-practice
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 4f973a6173e882d6ae839833bd3c5bf86f8d7fb6
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55898130"
---
# <a name="data-partitioning-strategies"></a>数据分区策略

本文介绍在各种 Azure 数据存储中进行数据分区的一些策略。 有关数据分区时间和最佳做法的常规指南，请参阅[数据分区](./data-partitioning.md)

## <a name="partitioning-azure-sql-database"></a>Azure SQL 数据库分区

单个 SQL 数据库对其包含的数据列施加了限制。 吞吐量受体系结构因素及数据库支持的并发连接数的约束。

[弹性池](/azure/sql-database/sql-database-elastic-pool)支持对 SQL 数据库进行水平缩放。 使用弹性池，可以将数据分区到分布于多个 SQL 数据库的分片中。 此外，还可以随着需要处理的数据量的增长和缩减，增加或删除分片。 使用弹性池还有助于在数据库之间分散负载，减少争用。

每个分片将作为 SQL 数据库实施。 一个分片可以保存多个数据集（称为 *shardlet*）。 每个数据库将维护描述其所包含的 shardlet 的元数据。 shardlet 可以是单个数据项，也可以是一组共享同一 shardlet 键的项。 例如，在多租户应用程序中，shardlet 键可以是租户 ID，一个租户的所有数据可以保存在同一 shardlet 中。

客户端应用程序负责将数据集与 shardlet 键相关联。 独立的 SQL 数据库将充当全局分片映射管理器。 此数据库有一个列表，其中包含系统中的所有分片和 shardlet。 应用程序在连接到分片映射管理器数据库后，可以获取分片映射的副本。 它在本地缓存分片映射，并使用该映射将数据请求路由到相应的分片。 此功能隐藏在[弹性数据库客户端库](/azure/sql-database/sql-database-elastic-database-client-library)（适用于 Java 和 .NET）中的一系列 API 之后。

有关弹性工具的详细信息，请参阅[使用 Azure SQL 数据库进行横向扩展](/azure/sql-database/sql-database-elastic-scale-introduction)。

若要减少延迟并提高可用性，可以复制全局分片映射管理器数据库。 使用高级定价层时，可以配置活动异地复制，将数据持续复制到其他区域中的数据库。

也可使用 [Azure SQL 数据同步](/azure/sql-database/sql-database-sync-data)或 [Azure 数据工厂](/azure/data-factory/)，跨区域复制分片映射管理器数据库。 这种形式的复制将定期运行，更适合分片映射不经常更改的情况，且不需要高级层。

弹性数据库提供了两种方案用于将数据映射到 shardlet 并将 shardlet 存储在分片中：

- **列表分片映射**将单个键关联到 shardlet。 例如，在多租户系统中，每个租户的数据可与唯一的键相关联，并存储在自身的 shardlet 中。 为了保证隔离，可将每个 shardlet 都保存在自身的分片中。

    ![使用列表分片映射将租户数据存储在独立分片中](./images/data-partitioning/PointShardlet.png)

- **范围分片映射**将一组连续键值关联到一个 shardlet。 例如，可以将一组租户（每个都有自己的键）的数据组合到同一 shardlet 中。 此方案的开销低于第一种方案，因为租户共享数据存储，但隔离性较低。

    ![使用范围分片映射来存储分片中租户范围的数据](./images/data-partitioning/RangeShardlet.png)

单个分片可以包含多个 shardlet 的数据。 例如，可以使用列表 shardlet 将不同非连续租户的数据存储在同一分片中。 还可以混合同一分片中的范围 shardlet 和列表 shardlet，虽然这些 shardlet 会通过不同映射来寻址。 下图显示了此方式：

![实施多个分片映射](./images/data-partitioning/MultipleShardMaps.png)

在数据量伸缩时，可以通过弹性池添加和删除分片。 客户端应用程序可以动态创建和删除分片，并以透明方式更新分片映射管理器。 但删除分片是破坏性操作，还需要删除该分片中的所有数据。

如果应用程序需要将一个分片拆分成两个独立的分片或者将分片组合在一起，请使用[拆分/合并工具](/azure/sql-database/sql-database-elastic-scale-overview-split-and-merge)。 该工具作为 Azure Web 服务运行，可以在分片之间安全地迁移数据。

分区方案可能会显著影响系统的性能。 它还会影响添加或删除分片的速率，或者跨分片对数据重新分区的速率。 请考虑以下要点：

- 将一起使用的数据组合到同一分片中，避免从多个分片访问数据的操作。 分片本身就是 SQL 数据库，跨数据库联接必须在客户端执行。

    尽管 SQL 数据库不支持跨数据库联接，但你可以使用弹性数据库工具执行[多分片查询](/azure/sql-database/sql-database-elastic-scale-multishard-querying)。 多分片查询会将单个查询发送到每个数据库，然后将结果合并在一起。

- 请不要设计在分片之间具有依赖性的系统。 一个数据库中的引用完整性约束、触发器和存储过程不能引用另一个数据库中的对象。

- 如果引用数据频繁地供查询使用，请考虑跨分片复制该数据。 此方法不需跨数据库联接数据。 在理想的情况下，此类数据应该是静态或不怎么变化的，以最大限度地减少复制工作量并减少数据变陈旧的可能性。

- 属于同一分片映射的 shardlet 应该有相同的架构。 SQL 数据库不会强制实施此规则，但如果每个 shardlet 都有不同的架构，则数据管理和查询将变得非常复杂。 为了避免这种情况，请为每个架构创建单独的分配映射。 请记住，属于不同 shardlet 的数据可以存储在相同的分片中。

- 只有分片中的数据支持事务操作，而跨分片的数据并不支持。 事务可以跨 shardlet，前提是它们属于同一分片。 因此，如果业务逻辑需要执行事务，请将数据存储在同一分片中，或者实施最终一致性。

- 将分片放置在访问这些分片中的数据的用户的附近。 此策略有助于缩短延迟。

- 避免混合高度活跃和相对不活跃的分片。 尽量跨分片平均分散负载。 这可能需要对分片键进行哈希处理。 如果要按地域查找分片，请确保哈希键映射到的 shardlet 保存在访问该数据的用户附近存储的分片中。

### <a name="partitioning-azure-table-storage"></a>将 Azure 表存储分区

Azure 表存储是存储的键/值，专为分区而设计。 所有实体都存储在分区中，分区在内部由 Azure 表存储管理。 存储在表中的每个实体需要提供一个包含两个部分的分键，其中包括：

- **分区键**。 这是一个字符串值，用于确定 Azure 表存储会在哪个分区中放置实体。 具有相同分区键的所有实体存储在同一分区中。

- **行键**。 这是一个字符串值，用于标识分区中的实体。 分区中的所有实体已按此键的词法升序排序。 每个实体的分区键/行键组合必须是唯一的，且长度不能超过 1 KB。

如果将具有先前未用过的分区键的实体添加到表中，Azure 表存储将为此实体创建新的分区。 具有相同分区键的其他实体将存储在同一分区中。

此机制将有效地实施自动向外扩展策略。 每个分区存储在 Azure 数据中心的同一服务器上，这样可以确保从单个分区检索数据的查询快速运行。

Microsoft 已发布 Azure 存储的[可伸缩性目标]。 如果系统可能会超出这些限制，请考虑将实体拆分成多个表。 使用垂直分区，并将字段分割成很有可能一起访问的组。

下图显示了示例存储帐户的逻辑结构。 存储帐户包含三个表：“客户信息”、“产品信息”和“订单信息”。

![示例存储帐户中的表和分区](./images/data-partitioning/TableStorage.png)

每个表有多个分区。

- 在“客户信息”表中，数据已根据客户所在的城市分区。 行键包含客户 ID。
- 在“产品信息”表中，产品按产品类别分区，行键包含产品编号。
- 在“订单信息”表中，订单按订购日期分区，行键指定收到订单的时间。 请注意，所有数据都已按行键在每个分区中排序。

在设计 Azure 表存储的实体时，请注意以下几点：

- 按数据访问方式选择分区键和行键。 选择支持大多数查询的分区键/行键组合。 最有效的查询将通过指定分区键和行键来检索数据。 可以通过扫描单个分区来满足指定分区键和行键范围的查询。 这是相当快速的方法，因为数据以行键的顺序保存。 如果查询未指定要扫描的分区，则必须扫描每个分区。

- 如果实体有一个自然键，请使用它作为分区键，并指定空白字符串作为行键。 如果实体的复合键包含两个属性，请选择最不容易变化的属性作为分区键，另一个属性作为行键。 如果实体有两个以上的键属性，请使用属性的串联来提供分区键和行键。

- 如果使用分区键和行键以外的字段定期执行查找数据的查询，请考虑实施[索引表模式](../patterns/index-table.md)，或者考虑使用另一个支持索引编制操作的数据存储，例如 Cosmos DB。

- 如果使用单调序列（例如“0001”、“0002”、“0003”）生成分区键，而每个分区只包含有限的数据量，则 Azure 表存储可用物理方式将同一服务器上的这些分区分组合在一起。 Azure 存储假设应用程序很可能在连续范围的分区中执行查询（范围查询），并已针对此情况进行优化。 但是，这种方法可能会导致热点，因为新实体的所有插入可能集中在连续范围的一端。 这也会降低伸缩性。 若要更均衡地分散负载，请考虑对分区键进行哈希处理。

- Azure 表存储支持属于相同分区的实体的事务操作。 应用程序可以原子单位的形式执行多次插入、更新、删除、替换或合并操作，前提是事务不包含 100 个以上的实体，且请求的有效负载不超过 4 MB。 跨多个分区的操作不是事务式的，并且可能需要实施最终一致性。 有关表存储和事务的详细信息，请参阅[执行实体组事务]。

- 考虑分区键的粒度：

  - 对每个实体使用相同的分区键会生成单个分区，该分区保存在一个服务器上。 这可以防止分区横向扩展，可以将负载集中在单个服务器上。 因此，这种方法只适合存储少量实体。 但是，它确实能确保所有实体都可以参与实体组事务。

  - 对每个实体使用唯一的分区键使表存储服务为每个实体创建不同的分区，可能会导致大量的小分区。 这种方法比使用单个分区键更具伸缩性，但是无法进行实体组事务。 此外，检索多个实体的查询可能涉及到读取多台服务器。 但是，如果应用程序执行范围查询，则对分区键使用单调序列可能有助于优化这些查询。

  - 跨实体子集共享分区键可将相同分区中的相关实体分组。 涉及使用实体组事务执行的相关实体的操作，以及提取一组相关实体的查询，可能通过访问单个服务器即可满足。

有关详细信息，请参阅 [Azure 存储表设计指南]。

## <a name="partitioning-azure-blob-storage"></a>将 Azure Blob 存储分区

Azure Blob 存储可保存大型二进制对象。 在需要快速上传或下载大量数据的情况下，请使用块 Blob。 对需要随机而不是串行访问部分数据的应用程序使用页 Blob。

每个 Blob（块或页）保存在 Azure 存储帐户中的容器内。 可以使用容器将具有相同安全需求的相关 Blob 分组在一起。 这种分组是逻辑性的，而不是物理性的。 在容器中，每个 Blob 都有唯一的名称。

Blob 的分区键是帐户名称 + 容器名称 + Blob 名称。 分区键用于将数据分区成多个范围，会在整个系统上对这些范围进行负载均衡。 尽管可以在众多服务器间分布 Blob 以便扩大对其的访问权限，但只能由单个服务器为单个 Blob 提供服务。

如果命名方案使用时间戳或数字标识符，则可能导致过多的流量进入一个分区，限制系统进行有效的负载均衡。 例如，如果日常操作使用有时间戳（如 *yyyy-mm-dd*）的 Blob 对象，则该操作的所有流量都会进入单个分区服务器。 为了避免这种情况，考虑在名称前加上 3 位数哈希。 有关详细信息，请参阅[分区命名约定](/azure/storage/common/storage-performance-checklist#subheading47)。

写入单个块或页的操作是原子操作，但跨块、页或 Blob 的操作不是。 如果需要在跨块、页和 Blob 写入操作时确保一致性，需要使用 Blob 租约取消写入锁。

## <a name="partitioning-azure-storage-queues"></a>将 Azure 存储队列分区

Azure 存储队列可让你在进程之间实施异步消息传送。 Azure 存储帐户可以包含任意数目的队列，而每个队列可以包含任意数目的消息。 唯一的限制是存储帐户中的可用空间。 单个消息的大小上限是 64 KB。 如果需要比这个限制更大的消息，请考虑改用 Azure 服务总线队列。

每个存储队列在其所属的存储帐户中都有唯一的名称。 Azure 根据名称将队列分区。 同一个队列的所有消息存储在同一分区中，由单个服务器所控制。 不同的队列可以由不同的服务器管理，以帮助平衡负载。 服务器的队列分配对于应用程序和用户而言是透明的。

在大型应用程序中，请勿将相同的存储队列用于应用程序的所有实例，因为这种方法可能使托管队列的服务器成为热点。 请将不同的队列用于应用程序的不同功能区域。 Azure 存储队列不支持事务，因此将消息定向到不同的队列对消息传送一致性的影响应该很小。

Azure 存储队列每秒最多可以处理 2,000 个消息。 如果需要以更高的速率处理消息，请考虑创建多个队列。 例如，在全局应用程序中的独立存储帐户中创建独立的存储队列，以处理每个区域中运行的应用程序实例。

## <a name="partitioning-azure-service-bus"></a>将 Azure 服务总线分区

Azure 服务总线使用消息中转站处理发送到服务总线队列或主题的消息。 默认情况下，所有发送到队列或主题的消息都由相同的消息中转站进程处理。 这种体系结构可限制消息队列的总体吞吐量。 但是，还可以在创建队列或主题时，将队列或主题分区。 为此，可将队列或主题描述的 *EnablePartitioning* 属性设为 *true*。

分区的队列或主题将分割成多个段，每个段都受到独立消息存储和消息中转站的支持。 服务总线负责创建和管理这些段。 当应用程序将消息发布到分区的队列或主题时，服务总线会将消息分配给该队列或主题的段。 当应用程序收到队列或订阅的消息时，服务总线会检查每个段中的下一条可用消息，然后将其传递给应用程序进行处理。

这种结构有助于跨消息代理和消息存储分散负载，提高伸缩性并提高可用性。 如果有一个段的消息中转站或消息存储暂时无法使用，服务总线可以从某一个剩余的可用段检索消息。

服务总线将消息分配给段，如下所示：

- 如果消息属于会话，则 *SessionId* 属性值相同的所有消息都会发送到相同的段。

- 如果消息不属于会话，但发件人已指定 *PartitionKey* 属性的值，则具有相同 *PartitionKey* 值的所有消息都将发送到相同的段。

  > [!NOTE]
  > 如果同时指定 *SessionId* 和 *PartitionKey* 属性，则两者需要设置为相同的值，否则消息会被拒绝。

- 如果未指定消息的 *SessionId* 和 *PartitionKey* 属性，但已启用重复侦测，则将使用 *MessageId* 属性。 具有相同 *MessageId* 的所有消息定向到相同的段。

- 如果消息不包含 *SessionId、PartitionKey* 或 *MessageId* 属性，则服务总线以有序方式案将消息分配给段。 如果某个段不可用，服务总线将移到下一个段。 这意味着，消息传送基础结构中的暂时故障不造成消息发送操作失败。

确定是否或者如何将服务总线消息队列或主题分区时，请注意以下几点：

- 服务总线队列和主题在服务总线命名空间的范围内创建。 服务总线当前允许为每个命名空间最多创建 100 个分区的队列或主题。

- 每个服务总线命名空间都会给可用资源规定配额，例如每个主题的订阅数、每秒的并发发送和接收请求数，以及可以建立的最大并发连接数。 [服务总线配额]中记录了这些配额。 如果预期会超过这些值，请创建更多包含自身队列和主题的命名空间，并跨这些命名空间分散工作。 例如，在每个区域的全局应用程序中创建不同的命名空间，并将应用程序实例配置为使用最接近命名空间中的队列和主题。

- 作为事务一部分发送的消息必须指定分区键。 这可能是 *SessionId*、*PartitionKey* 或 *MessageId* 属性。 作为相同事务一部分发送的所有消息必须指定相同的分区键，因为它们需要由相同的消息代理进程进行处理。 无法将消息发送到同一事务中的不同队列或主题。

- 无法将分区的队列或主题配置为在空闲状态时自动删除。

- 如果要构建跨平台解决方案或混合解决方案，当前无法将分区的队列和主题与高级消息队列协议 (AMQP) 配合使用。

## <a name="partitioning-cosmos-db"></a>将 Cosmos DB 分区

Azure Cosmos DB 是可以使用 [Azure Cosmos DB SQL API][cosmosdb-sql-api] 存储 JSON 文档的 NoSQL 数据库。 Cosmos DB 数据库中的文档是对象或其他数据片段的 JSON 序列化表示形式。 没有强制实施固定的架构，但是每个文档必须包含唯一 ID。

文档已组织成集合。 可将相关的文档统一分组在集合中。 例如，在维护博客文章的系统中，可以将每篇博客文章的内容存储为某个集合中的文档。 还可以为每个主题类型创建集合。 或者，在多租户应用程序（例如可让不同作者控制和管理自己的博客文章的系统）中，可以根据作者将博客分区，并为每位作者创建独立的集合。 分配给集合的存储空间有弹性，并且可以根据需要缩小或增大。

Cosmos DB 支持按应用程序定义的分区键对数据自动分区。 “逻辑分区”是指用于存储单个分区键值对应的所有数据的分区。 分区键值相同的所有文档放置在同一逻辑分区内。 Cosmos DB 根据分区键的哈希来分配值。 逻辑分区的最大大小为 10 GB。 因此，选择分区键是设计时的重要决定。 请选择值甚至访问模式的范围广的属性。 有关详细信息，请参阅 [Azure Cosmos DB 中的分区和规模](/azure/cosmos-db/partition-data)。

> [!NOTE]
> 每个 Cosmos DB 数据库都有一个性能级别用于确定它所获取的资源量。 性能级别与*请求单位* (RU) 比率限制关联。 RU 比率限制指定要保留的并可供该集合独占使用的资源量。 集合的成本取决于为该集合选择的性能级别。 性能级别（以及 RU 比率限制）越高，则费用就越高。 可以使用 Azure 门户来调整集合的性能级别。 有关详细信息，请参阅 [Azure Cosmos DB 中的请求单位][cosmos-db-ru]。

如果 Cosmos DB 提供的分区机制不够用，则可能需要在应用程序级别进行数据分片。 文档集合提供一个自然机制用于在单一数据库中将数据分区。 实施分片的最简单方法是为每个分片创建一个集合。 容器是逻辑资源，可以跨一个或多个服务器。 固定大小的容器最大限制为 10 GB，10,000 RU/s 吞吐量。 不限大小的容器没有最大存储大小，但必须指定分区键。 使用应用程序分片时，客户端应用程序必须将请求定向到适当的分片，这通常是根据定义分片键的某些数据属性，通过实施自身的映射机制来实现的。

所有数据库在 Cosmos DB 数据库帐户的上下文中创建。 单个帐户可以包含多个数据库，并指定要在其中创建数据库的区域。 每个帐户还强制实施自身访问控制。 可以使用 Cosmos DB 帐户异地查找靠近需要访问帐户的用户的分片（数据库中的集合），并强制实施限制，以便只有这些用户才能连接到这些帐户。

确定如何使用 Cosmos DB SQL API 将数据分区时，请注意以下几点：

- **Cosmos DB 数据库的可用资源受限于帐户的配额限制**。 每个数据库可以保存许多集合，每个集合都与控制该集合 RU 比率限制（保留吞吐量）的性能级别相关联。 有关详细信息，请参阅 [Azure 订阅和服务限制、配额与约束][azure-limits]。

- **每个文档必须有一个可用于在保存该文档的集合中唯一标识该文档的属性**。 此属性与分片键不同，后者定义文档要保存在哪个集合中。 一个集合可以包含大量的文档。 理论上只受限于文档 ID 的最大长度。 文档 ID 最多可包含 255 个字符。

- **针对文档的所有操作都在事务的上下文中执行。事务的范围包含该文档的集合。** 如果操作失败，已执行的工作会回滚。 当文档正在接受某项操作时，所做的任何更改将受限于快照级隔离。 例如，如果创建新文档的请求失败，此机制将保证另一个同时查询数据库的用户不会看到当时删除的部分文档。

- **数据库查询的范围也限于集合级别**。 单个查询只能从一个集合检索数据。 如果需要从多个集合检索数据，则必须分别查询每个集合，并将结果合并到应用程序代码中。

- **Cosmos DB 支持可与文档一起全部存储在集合中的可编程项**。 这包括存储过程、用户定义的函数和触发器（以 JavaScript 编写）。 这些项可以访问同一集合中的任何文档。 此外，这些项在环境事务的范围内运行（如果是由于针对文档执行了创建、删除或替换操作而激发了触发器），或者通过启动新的事务执行（如果是由于显式客户端请求结果而运行的存储过程）。 如果可编程项中的代码引发异常，事务会回滚。 可以使用存储过程和触发器来维护文档之间的完整性和一致性，但这些文档必须属于同一集合。

- **要在数据库中保存的集合应该不太可能会超过由集合的性能级别定义的吞吐量限制**。 有关详细信息，请参阅 [Azure Cosmos DB 中的请求单位][cosmos-db-ru]。 如果预计会达到这些限制，请考虑在不同的帐户中跨数据库拆分集合，以减少每个集合的负载。

## <a name="partitioning-azure-search"></a>将 Azure 搜索分区

搜索数据的功能通常是许多 web 应用程序提供的主要导航和浏览方法。 它可以帮助用户根据搜索条件的组合快速查找资源（例如，电子商务应用程序中的产品）。 Azure 搜索服务针对 Web 内容提供全文搜索功能，并包括自动提示、根据近似的匹配内容建议查询，以及分面导航等功能。 有关详细信息，请参阅[什么是 Azure 搜索？]。

Azure 搜索将可搜索的内容存储为数据库中的 JSON 文档。 可以定义一些索引，用于在这些文档中指定可搜索的字段，并将这些定义提供给 Azure 搜索。 当用户提交搜索请求时，Azure 搜索将使用适当的索引来查找匹配项。

为了减少争用，Azure 搜索使用的存储可以分割为 1、2、3、4、6 或 12 个分区，并且每个分区最多可以复制 6 次。 分区数目乘以副本数目的乘积称为*搜索单位* (SU)。 Azure 搜索的单个实例最多可以包含 36 个 SU（具有 12 个分区的数据库最多只支持 3 个副本）。

需要支付分配给服务的每个 SU 的费用。 当可搜索内容的数量增加或搜索请求的比率增大时，可以将 SU 添加到 Azure 搜索的现有实例以处理额外的负载。 Azure 搜索本身可以跨分区平均分配文档。 目前不支持任何手动分区策略。

每个分区最多可以包含 1500 万个文档或占用 300 GB 存储空间（取两者中较小者）。 最多可以创建 50 个索引。 服务的性能因文档的复杂性、可用索引以及网络延迟的影响而有所不同。 一般而言，单个副本 (1 SU) 每秒应该可以处理 15 个查询 (QPS)，不过，我们建议使用自己的数据执行基准计算，以获取更精确的吞吐量测量值。 有关详细信息，请参阅 [Azure 搜索中的服务限制]。

> [!NOTE]
> 可以将有限的一组数据类型存储在可搜索文档中，这些类型包括字符串、布尔值、数字数据、日期时间数据和一些地理数据。 有关详细信息，请参阅 Microsoft 网站上的 [支持的数据类型（Azure 搜索）]页。

只能有限地控制 Azure 搜索如何对每个服务实例的数据分区。 但是，在全局环境中，可以通过使用以下任一策略将服务本身分区，以进一步提高性能并减少延迟和争用：

- 在每个地理区域中创建 Azure 搜索的实例，并确保客户端应用程序定向到最靠近的可用实例。 此策略要求跨服务的所有实例及时复制对可搜索内容所做的任何更新。

- 创建双层 Azure 搜索：

  - 每个区域中一个本地服务，其中包含用户在该区域中最常访问的数据。 用户可将请求定向到此处以加快查询速度，但返回的结果有限。
  - 一个包含所有数据的全局服务。 用户可将请求定向到此处以返回更完整的结果，但查询速度会变慢。

当搜索的数据存在明显的区域性差异时，最适合使用此方法。

## <a name="partitioning-azure-redis-cache"></a>将 Azure Redis 缓存分区

Azure Redis 缓存在云中提供基于 Redis 键-值数据存储的共享缓存服务。 顾名思义，Azure Redis 缓存旨在用作缓存解决方案。 它只用于保存暂时性数据，而不是用作永久性的数据存储。 如果缓存不可用，使用 Azure Redis 缓存的应用程序应能够继续工作。 Azure Redis 缓存支持主要/辅助复制，可提供高可用性，但目前缓存大小上限为 53 GB。 如果需要更多的空间，则必须创建更多缓存。 有关详细信息，请参阅 [Azure Redis 缓存]。

将 Redis 数据存储分区涉及到跨 Redis 服务的实例拆分数据。 每个实例构成单个分区。 Azure Redis 缓存将抽象化幕后的 Redis 服务，而不直接公开它们。 实施分区的最简单方法是创建多个 Azure Redis 缓存实例，并在其中分散数据。

可以将每个数据项与指定要在哪个缓存中存储该数据项的标识符（分区键）相关联。 然后，客户端应用程序逻辑可以使用此标识符将请求路由到相应的分区。 此方案非常简单，但如果分区方案发生更改（例如，如果已创建其他 Azure Redis 缓存实例），则可能需要重新配置客户端应用程序。

本机 Redis（非 Azure Redis 缓存）支持基于 Redis 群集的服务器端分区。 使用此方法时，可以使用哈希机制跨服务器平均分割数据。 每个 Redis 服务器将存储用于描述分区保存的哈希键范围的元数据，同时还包含有关哪些哈希键位于其他服务器上的分区中的信息。

客户端应用程序只需将请求发送到任何参与方 Redis 服务器（可能是最靠近的服务器）。 Redis 服务器检查客户端请求。 如果可以在本地解决，则执行请求的操作。 否则将请求转发到相应的服务器。

此模型是使用 Redis 群集实施的，Redis 网站上的 [Redis 群集教程]页上提供了更详细的说明。 Redis 群集对客户端应用程序而言是透明的。 其他 Redis 服务器可以添加到群集（数据将重新分区），而无需重新配置客户端。

> [!IMPORTANT]
> Azure Redis 缓存目前仅在[高级](/azure/azure-cache-for-redis/cache-how-to-premium-clustering)层中支持 Redis 群集功能。

Redis 网站上的 [Partitioning: how to split data among multiple Redis instances]（分区：如何在多个 Redis 实例之间拆分数据）页提供了有关使用 Redis 实施分区的更多信息。 本部分的余下内容假设要实施客户端分区或代理辅助分区。

在确定如何使用 Azure Redis 缓存将数据分区时，请注意以下几点：

- Azure Redis 缓存并非旨在用作永久性的数据存储，因此无论实施哪种分区方案，应用程序代码都必须能够从非缓存中位置检索数据。

- 应该将经常访问的数据一起保存在同一分区中。 Redis 是一个功能强大的键-值存储，提供多种高度优化的机制用于建构数据。 这些机制可能是下列其中一种：
  - 简单字符串（长度最大为 512 MB 的二进制数据）
  - 列表等聚合类型（可充当队列和堆栈）
  - 集（已排序和未排序）
  - 哈希（可将相关的字段分组在一起，例如表示对象中字段的项）

- 聚合类型可让你将许多相关值与同一个键相关联。 Redis 键标识列表、集或哈希，而不是它包含的数据项。 可以在 Azure Redis 缓存中使用这些类型，Redis 网站上的 [数据类型]页已提供了说明。 例如，跟踪客户所下订单的电子商务系统部件中，每位客户的详细信息可能存储在 Redis 哈希中，使用客户 ID 键控。 每个哈希可以保存该客户的订单 ID 集合。 一个独立的 Redis 集可以保存订单（同样已结构化为哈希），使用订单 ID 键控。 图 8 显示了此结构。 请注意，Redis 不实施任何形式的引用完整性，因此开发人员需要负责维护客户与订单之间的关系。

![记录客户订单及其详细信息的 Redis 存储中的建议结构](./images/data-partitioning/RedisCustomersandOrders.png)

*图 8.* 记录客户订单及其详细信息的 Redis 存储中的建议结构。

> [!NOTE]
> 在 Redis 中，所有键都是二进制数据值（例如 Redis 字符串），最多可包含 512 MB 的数据。 在理论上，键几乎可以包含任何信息。 但是，我们建议为键采用可以描述数据类型并标识实体的一致命名约定，但是该约定不可过长。 常见的做法是使用“entity_type:ID”形式的键。 例如，可以使用“customer:99”来表示客户 ID 99 的键。

- 可以通过将相关信息存储在同一数据库的不同聚合中来实施垂直分区。 例如，在电子商务应用程序中，可以将经常访问的产品相关信息存储在一个 Redis 哈希中，将较少使用的详细信息存储在另一个 Redis 哈希中。 这两个哈希可以使用同一产品 ID 作为键的一部分。 例如，可以使用“product: *nn*”（其中 *nn* 是产品 ID）来表示产品信息，使用“product_details: *nn*”来表示详细数据。 此策略可以帮助减少大多数查询可能检索的数据量。

- 可以重新分区 Redis 数据存储，但请记住，这是一项复杂且耗时的任务。 Redis 群集可以自动将数据重新分区，但是此功能无法在 Azure Redis 缓存中使用。 因此，在设计分区方案时，应该尽量在每个分区中保留足够的可用空间，以适应一段时间后数据预期增长。 但请记住，Azure Redis 缓存旨在暂时缓存数据，而保存在缓存中的数据具有有限的生存期（以生存时间 (TTL) 值指定）。 对于相对容易变化的数据而言，TTL 可以短一点，但对于静态数据而言，TTL 可以更长一些。 如果此数据量可能会填满缓存，则应避免在缓存中存储大量长时间留存的数据。 如果空间价格不菲，可以指定一个逐出策略，让 Azure Redis 缓存删除数据。

  > [!NOTE]
  > 使用 Azure Redis 缓存时，可以通过选择适当的定价层来指定缓存的最大大小（从 250 MB 到 53 GB）。 但是，创建 Azure Redis 缓存后，无法增大（或减小）其大小。

- Redis 批处理与事务不能跨多个连接，因此受批处理或事务影响的所有数据应保存在同一数据库（分片）中。

  > [!NOTE]
  > Redis 事务中的操作序列不一定是原子性的。 构成事务的命令已经过验证，并在执行之前已排入队列。 如果在此阶段期间发生错误，会丢弃整个队列。 但是，成功提交事务后，排入队列的命令就会按顺序运行。 如果任一命令失败，只有该命令停止运行。 队列中所有前面与后面的命令都将执行。 有关详细信息，请转到 Redis 网站上的 [Transactions]（事务）页。

- Redis 支持有限数量的原子操作。 这种类型的、支持多个键和值的操作只有 MGET 和 MSET 操作。 MGET 操作返回指定键列表的值集合，MSET 操作可以存储指定键列表的值集合。 如果需要使用这些操作，必须将 MSET 和 MGET 命令引用的键-值对存储在同一数据库中。

## <a name="partitioning-azure-service-fabric"></a>将 Azure Service Fabric 分区

Azure Service Fabric 是在云中提供分布式应用程序的运行时的微服务平台。 Service Fabric 支持 .Net guest 可执行文件、有状态和无状态服务以及容器。 有状态服务提供[可靠集合][service-fabric-reliable-collections]以永久存储 Service Fabric 群集内的键值集合中的数据。 若要详细了解可靠集合中分区键的策略，请参阅 [Azure Service Fabric 中的可靠集合的相关指导原则和建议]。

### <a name="more-information"></a>详细信息

- [Azure Service Fabric 概述]是有关 Azure Service Fabric 的简介。

- [分区 Service Fabric 可靠服务]提供有关 Azure Service Fabric 中的可靠服务的详细信息。

## <a name="partitioning-azure-event-hubs"></a>将 Azure 事件中心分区

[Azure 事件中心][event-hubs]可用于大规模数据流，并且分区内置于服务中以启用水平缩放。 每个使用者只读取消息流的的特定分区。

事件发布者只知道其分区密钥，而不知道事件要发布到的分区。 键与分区的这种分离使发送者无需了解有关下游处理的过多信息。 （它也可能将事件直接发送到给定的分区，但通常不建议这样做。）  

选择分区计数时，请考虑长期规模。 创建事件中心后，将无法更改分区数。

有关在事件中心使用分区的详细信息，请参阅[什么是事件中心？]。

有关可用性和一致性之间的权衡，请参阅[事件中心中的可用性和一致性]。

[事件中心中的可用性和一致性]: /azure/event-hubs/event-hubs-availability-and-consistency
[azure-limits]: /azure/azure-subscription-service-limits
[Azure Content Delivery Network]: /azure/cdn/cdn-overview
[Azure Redis 缓存]: https://azure.microsoft.com/services/cache/
[Azure Storage Scalability and Performance Targets]: /azure/storage/storage-scalability-targets
[Azure 存储表设计指南]: /azure/storage/storage-table-design-guide
[Building a Polyglot Solution]: https://msdn.microsoft.com/library/dn313279.aspx
[cosmos-db-ru]: /azure/cosmos-db/request-units
[Data Access for Highly-Scalable Solutions: Using SQL, NoSQL, and Polyglot Persistence]: https://msdn.microsoft.com/library/dn271399.aspx
[Data consistency primer]: https://aka.ms/Data-Consistency-Primer
[Data Partitioning Guidance]: https://msdn.microsoft.com/library/dn589795.aspx
[数据类型]: https://redis.io/topics/data-types
[cosmosdb-sql-api]: /azure/cosmos-db/sql-api-introduction
[Elastic Database features overview]: /azure/sql-database/sql-database-elastic-scale-introduction
[event-hubs]: /azure/event-hubs
[Federations Migration Utility]: https://code.msdn.microsoft.com/vstudio/Federations-Migration-ce61e9c1
[Azure Service Fabric 中的可靠集合的相关指导原则和建议]: /azure/service-fabric/service-fabric-reliable-services-reliable-collections-guidelines
[Multi-shard querying]: /azure/sql-database/sql-database-elastic-scale-multishard-querying
[Azure Service Fabric 概述]: /azure/service-fabric/service-fabric-overview
[分区 Service Fabric 可靠服务]: /azure/service-fabric/service-fabric-concepts-partitioning
[Partitioning: how to split data among multiple Redis instances]: https://redis.io/topics/partitioning
[执行实体组事务]: /rest/api/storageservices/Performing-Entity-Group-Transactions
[Redis 群集教程]: https://redis.io/topics/cluster-tutorial
[Running Redis on a CentOS Linux VM in Azure]: https://blogs.msdn.microsoft.com/tconte/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure/
[Scaling using the Elastic Database split-merge tool]: /azure/sql-database/sql-database-elastic-scale-overview-split-and-merge
[Using Azure Content Delivery Network]: /azure/cdn/cdn-create-new-endpoint
[服务总线配额]: /azure/service-bus-messaging/service-bus-quotas
[service-fabric-reliable-collections]: /azure/service-fabric/service-fabric-reliable-services-reliable-collections
[Azure 搜索中的服务限制]:  /azure/search/search-limits-quotas-capacity
[Sharding pattern]: ../patterns/sharding.md
[支持的数据类型（Azure 搜索）]:  https://msdn.microsoft.com/library/azure/dn798938.aspx
[Transactions]: https://redis.io/topics/transactions
[什么是事件中心？]: /azure/event-hubs/event-hubs-what-is-event-hubs
[什么是 Azure 搜索？]: /azure/search/search-what-is-azure-search
[What is Azure SQL Database?]: /azure/sql-database/sql-database-technical-overview
[可伸缩性目标]: /azure/storage/common/storage-scalability-targets
