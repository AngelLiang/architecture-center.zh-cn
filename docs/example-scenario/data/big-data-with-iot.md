---
title: 建筑行业中的 IoT 和数据分析
titleSuffix: Azure Example Scenarios
description: 使用 IoT 设备和数据分析提供建筑项目的全面管理和运营。
author: alexbuckgit
ms.date: 08/29/2018
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.custom: IoT, data-analytics
social_image_url: /azure/architecture/example-scenario/data/media/architecture-big-data-with-iot.png
ms.openlocfilehash: bf963c0467acd4ecbb4bdca6272385d7532167f2
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58244768"
---
# <a name="iot-and-data-analytics-in-the-construction-industry"></a>建筑行业中的 IoT 和数据分析

本示例方案与需要将数据从许多 IoT 设备集成到综合性数据分析体系结构，以改善和自动化决策的建筑业组织解决方案相关。 潜在的应用范围包括建筑、采矿、制造，或涉及到从多个基于 IoT 的数据输入中引入大量数据的行业。

在本方案中，某家建筑设备制造商需要制造一些使用 IoT 和 GPS 技术发出遥测数据的车辆、仪表和无人机。 该公司希望将其数据体系结构现代化，以便更好地监视工作条件和设备运行状况。 使用本地基础设施取代公司的旧式解决方案不仅费时费力，而且不能提供足够的可伸缩性来应对预期的数据量。

该公司希望构建一个基于云的“智能建筑”解决方案。 该解决方案全面收集建筑场地的数据集，并将场地中各个要素的操作和维护自动化。 该公司的目标包括：

- 集成并分析所有建筑场地的设备和数据，以尽量减少设备停机和失窃情况。
- 远程自动控制建筑设备，以缓解劳动力短缺造成的影响，最终减少工人数目，并使技能不足的工人能够胜任。
- 尽量降低支持性基础设施的工作成本和人力要求，同时提高生产率和安全性。
- 轻松扩展基础设施，以应对遥测数据的不断增加。
- 在不损害系统可用性的前提下，通过预配资源来符合国家/地区的所有相关法律要求。
- 使用开源软件，最大化对当前工人技能的投资回报。

客户可以使用 IoT 中心和 HDInsight 等 Azure 托管服务快速生成和部署综合性解决方案，同时较低运营成本。 如果你有其他数据分析方面的需求，应该查看 [Azure 中提供的完全托管式数据分析服务][product-category]列表。

## <a name="relevant-use-cases"></a>相关用例

其他相关用例包括：

- 建筑、采矿或设备制造方案
- 要存储和分析的大规模设备数据集合
- 引入和分析大型数据集

## <a name="architecture"></a>体系结构

![建筑行业中 IoT 和数据分析的体系结构][architecture]

数据流经解决方案的情形如下所示：

1. 建筑设备定期收集传感器数据，并将施工结果数据发送到 Azure 虚拟机群集上托管的负载均衡 Web 服务。
2. 自定义 Web 服务引入施工结果数据，并将其存储在同样运行于 Azure 虚拟机上的 Apache Cassandra 群集中。
3. 另一个数据集由不同建筑设备上的 IoT 传感器收集，并发送到 IoT 中心。
4. 收集的原始数据直接从 IoT 中心发送到 Azure Blob 存储，并立即可供查看和分析。
5. 通过 IoT 中心收集的数据由 Azure 流分析作业以近实时的速度进行处理，并存储在 Azure SQL 数据库中。
6. 分析师和最终用户可以使用 Smart Construction Cloud Web 应用程序来查看和分析传感器数据与图像。
7. Web 应用程序的用户按需启动批处理作业。 批处理作业在 Apache Spark on HDInsight 中运行，可分析 Cassandra 群集中存储的新数据。

### <a name="components"></a>组件

- [IoT 中心](/azure/iot-hub/about-iot-hub)充当一个中心消息枢纽，可以使用每个设备的标识在云平台与建筑设备和其他场地要素之间实现安全的双向通信。 IoT 中心可以快速收集每个设备的数据，以便引入到数据分析管道。
- [Azure 流分析](/azure/stream-analytics/stream-analytics-introduction)是一个事件处理引擎，可以分析从设备和其他数据源流式传输的大量数据。 它还支持从数据流提取信息，以便确定模式和关系。 在本方案中，流分析将引入并分析来自 IoT 设备的数据，并将结果存储在 Azure SQL 数据库中。
- [Azure SQL 数据库](/azure/sql-database/sql-database-technical-overview)包含来自 IoT 设备和仪表的分析数据的结果，分析师和用户可以通过基于 Azure 的 Web 应用程序查看这些结果。
- [Blob 存储](/azure/storage/blobs/storage-blobs-introduction)存储从 IoT 中心设备收集的图像数据。 可以通过 Web 应用程序查看图像数据。
- [流量管理器](/azure/traffic-manager/traffic-manager-overview)控制不同 Azure 区域中服务终结点的用户流量分配。
- [负载均衡器](/azure/load-balancer/load-balancer-overview)在基于 VM 的 Web 服务之间分配建筑设备提交的数据，以提供高可用性。
- [Azure 虚拟机](/azure/virtual-machines)托管可在 Apache Cassandra 数据库中接收和引入施工结果数据的 Web 服务。
- [Apache Cassandra](https://cassandra.apache.org) 是一个分布式 NoSQL 数据库，用于存储施工数据，以便稍后由 Apache Spark 进行处理。
- [Web 应用](/azure/app-service/app-service-web-overview)托管可用于查询和查看源数据与图像的最终用户 Web 应用程序。 用户也可以通过应用程序在 Apache Spark 中启动批处理作业。
- [Apache Spark on HDInsight](/azure/hdinsight/spark/apache-spark-overview) 支持使用内存中处理来提升大数据分析应用程序的性能。 本方案使用 Spark 针对 Apache Cassandra 中存储的数据运行复杂算法。

### <a name="alternatives"></a>备选项

- [Cosmos DB](/azure/cosmos-db/introduction) 是一种 NoSQL 数据库替代技术。 Cosmos DB 提供[全球规模的多主数据库支持](/azure/cosmos-db/multi-region-writers)，并提供[多个妥善定义的一致性级别](/azure/cosmos-db/consistency-levels)，以满足各种客户要求。 它还支持 [Cassandra API](/azure/cosmos-db/cassandra-introduction)。
- [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks) 是针对 Azure 优化的基于 Apache Spark 的分析平台。 它与 Azure 集成，可提供一键式安装、简化的工作流和交互式协作工作区。
- [Data Lake Storage](/azure/storage/data-lake-storage) 可以取代 Blob 存储。 对于本方案，Data Lake Storage 在目标区域中不可用。
- 还可以使用 [Web 应用](/azure/app-service)来托管用于引入施工结果数据的 Web 服务。
- 进行实时消息引入、数据存储、流处理、分析数据存储以及分析和报告时，有许多技术选项。 有关这些选项及其功能和主要选择标准的概述，请参阅 Azure 数据体系结构指南中的[大数据体系结构：实时处理](/azure/architecture/data-guide/technology-choices/real-time-ingestion)中[Azure 数据体系结构指南](/azure/architecture/data-guide)。

## <a name="considerations"></a>注意事项

Azure 区域的广泛可用性是本方案的一个重要考虑因素。 在一个国家/地区设立多个区域不仅可以实现灾难恢复，而且还能遵从合同义务和执法要求。 在本方案中，Azure 区域之间的高速通信也是一个重要考虑因素。

Azure 支持开源技术，可让客户利用其现有的劳动力技能。 与使用本地解决方案相比，客户还能以更低的成本和更少的工作负荷加速新技术的采用。

## <a name="pricing"></a>定价

以下因素与本解决方案的绝大部分成本相关。

- 预配更多的实例时，Azure 虚拟机的成本将呈线性增加。 已解除分配的虚拟机只会产生存储成本，而不产生计算成本。 今后在需求提高时，可以重新分配这些已解除分配的虚拟机。
- [IoT 中心](https://azure.microsoft.com/pricing/details/iot-hub)成本由预配的 IoT 单元数以及所选的服务层驱动，后者决定了每个单元每天可以发送的消息数。
- [流分析](https://azure.microsoft.com/pricing/details/stream-analytics)的定价依据是在服务中处理数据所需的流单元数。

## <a name="related-resources"></a>相关资源

若要查看类似体系结构的实现，请阅读 [Komatsu 客户案例][customer-story]。

[Azure 数据体系结构指南](/azure/architecture/data-guide)中提供了大数据体系结构的指导。

<!-- links -->

[product-category]: https://azure.microsoft.com/product-categories/analytics/
[customer-site]: https://home.komatsu/en/
[customer-story]: https://customers.microsoft.com/story/komatsu-manufacturing-azure-iot-hub-japan
[architecture]: ./media/architecture-big-data-with-iot.png
