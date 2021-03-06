---
title: 用于选择数据存储的条件
titleSuffix: Azure Application Architecture Guide
description: Azure 计算选项概述。
author: MikeWasson
ms.date: 06/01/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19
ms.openlocfilehash: 3fd3badd66edbe561bea88576bb80d9fc3e0bb79
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59068916"
---
# <a name="criteria-for-choosing-a-data-store"></a>用于选择数据存储的条件

Azure 支持许多类型的数据存储解决方案，每个都提供了不同的特性和功能。 本文介绍了在评估数据存储时应当使用的比较条件。 其目标是帮助你确定哪些数据存储类型可以满足你的解决方案的要求。

## <a name="general-considerations"></a>一般注意事项

若要开始比较，请尽可能多地收集关于数据需求的以下信息。 该信息可以帮助你确定哪些数据存储类型可满足需求。

### <a name="functional-requirements"></a>功能要求

- **数据格式**。 打算存储什么类型的数据？ 常见类型包括事务数据、JSON 对象、遥测、搜索索引或平面文件。

- **数据大小**。 需要存储的实体有多大？ 这些实体是需要保持为单个文档，还是可以将它们拆分到多个文档、表和集合中？

- **规模和结构**。 需要的存储容量总量是多少？ 是否预期对数据进行分区？

- **数据关系**。 数据是否需要支持一对多或多对多关系？ 关系本身是否是数据的重要部分？ 是否需要联接或组合来自同一数据集或来自外部数据集的数据？

- **一致性模型**。 确保在一个节点中所做更新出现在其他节点中后才能做进一步更改有多重要？ 是否可以接受最终一致性？ 是否需要针对事务实现 ACID 保证？

- **架构灵活性**。 将向数据应用什么类型的架构？ 将使用固定架构、写入时架构方法还是读取时架构方法？

- **并发**。 在更新和同步数据时希望使用什么类型的并发机制？ 应用程序是否将执行可能会冲突的许多更新？ 如果是，你可能需要实施记录锁定和悲观并发控制。 或者，是否可以支持乐观并发控制？ 如果是，采用简单的基于时间戳的并发控制已经足够，还是需要附加的多版本并发控制功能？

- **数据移动**。 解决方案是否需要执行 ETL 任务将数据移动到其他存储或数据仓库？

- **数据生命周期**。 数据是否写入一次读取多次？ 是否可以将其移动到冷存储中？

- **支持的其他功能**。 是否需要任何其他特定功能，例如架构验证、聚合、索引、全文搜索、MapReduce 或其他查询功能？

### <a name="non-functional-requirements"></a>非功能性要求

- **性能和可伸缩性**。 数据性能要求有哪些？ 对数据引入速率和数据处理速率是否有特定要求？ 在引入数据后，查询和聚合数据时可接受的响应时间是多少？ 需要将数据纵向扩展到多大的规模？ 工作负荷是读取开销更重还是写入开销更重？

- **可靠性**。 需要支持什么整体 SLA？ 需要为数据使用者提供什么级别的容错？ 需要什么类型的备份和还原功能？

- **复制**。 数据是否需要分布在多个副本或区域中？ 需要什么类型的数据复制功能？

- **限制**。 特定数据存储的限制是否可以支持你对规模、连接数和吞吐量的要求？

### <a name="management-and-cost"></a>管理和成本

- **托管服务**。 如果可能，请使用托管数据服务，除非你需要只有 IaaS 托管数据存储中才提供的特定功能。

- **区域可用性**。 对于托管服务，服务是否在所有 Azure 区域中都可用？ 解决方案是否需要托管在特定的 Azure 区域中？

- **可移植性**。 将你的数据，需要迁移到在本地、 外部数据中心或其他云托管环境？

- **许可**。 在专有许可证类型与 OSS 许可证类型之间，你是否有偏好？ 对于可以使用哪些类型的许可证，是否有任何其他外部限制？

- **总体成本**。 在你的解决方案内使用此服务的总体成本是多少？ 需要运行多少个实例来支持运行时间和吞吐量要求？ 在此计算中，请考虑运营成本。 首选使用托管服务的一个原因是降低了运营成本。

- **成本效益**。 是否可以对数据进行分区以便经济高效地存储数据？ 例如，是否可以将大型对象从昂贵的关系数据库移动到对象存储中？

### <a name="security"></a>安全

- **安全性**。 需要哪种类型的加密？ 是否需要静态加密？ 希望使用哪种身份验证机制来连接到数据？

- **审核**。 需要生成哪种类型的审核日志？

- **网络要求**。 是否需要限制或管理从其他网络资源对数据的访问？ 是否要求只能从 Azure 环境内访问数据？ 是否要求可以从特定的 IP 地址或子网访问数据？ 是否要求可以从在本地或其他外部数据中心内托管的应用程序或服务访问数据？

### <a name="devops"></a>DevOps

- **技能集**。 是否有你的团队特别善于使用的特定编程语言、操作系统或其他技术？ 是否有你的团队难以使用的其他技术？

- **客户端** 对于你的开发语言是否有良好的客户端支持？

下面各部分从工作负荷配置文件、数据类型和示例用例等方面对各种数据存储模型进行了比较。

## <a name="relational-database-management-systems-rdbms"></a>关系数据库管理系统 (RDBMS)

<!-- markdownlint-disable MD033 -->

<table>
<tr><td><strong>工作负载</strong></td>
    <td>
        <ul>
            <li>新记录的创建和对现有数据的更新都经常发生。</li>
            <li>多个操作必须在单个事务中完成。</li>
            <li>需要使用聚合函数来执行交叉制表。</li>
            <li>需要实现与报告工具的强集成。</li>
            <li>使用数据库约束强制实施关系。</li>
            <li>使用索引来优化查询性能。</li>
            <li>允许访问特定的数据子集。</li>
        </ul>
    </td>
</tr>
<tr><td><strong>数据类型</strong></td>
    <td>
        <ul>
            <li>数据是高度规范化的。</li>
            <li>数据架构是必需的并强制实施。</li>
            <li>数据库中的数据实体之间的多对多关系。</li>
            <li>约束是在架构中定义的，并施加于数据库中的任何数据。</li>
            <li>数据需要具有高完整性。 索引和关系需要准确地维护。</li>
            <li>数据需要具有强一致性。 事务的运行方式将确保所有数据对于所有用户和进程而言都 100% 一致。</li>
            <li>各个数据条目的大小预计为中小型的。</li>
        </ul>
    </td>
</tr>
<tr><td><strong>示例</strong></td>
    <td>
        <ul>
            <li>业务线（人力资本管理、客户关系管理、企业资源规划）</li>
            <li>库存管理</li>
            <li>报告数据库</li>
            <li>计帐</li>
            <li>资产管理</li>
            <li>基金管理</li>
            <li>订单管理</li>
        </ul>
    </td>
</tr>
</table>

## <a name="document-databases"></a>文档数据库

<table>
<tr><td><strong>工作负载</strong></td>
    <td>
        <ul>
            <li>常规用途。</li>
            <li>插入和更新操作很常见。 新记录的创建和对现有数据的更新都经常发生。</li>
            <li>没有对象关系阻抗不匹配。 文档可以更好地匹配应用程序代码中使用的对象结构。</li>
            <li>更常使用乐观并发。</li>
            <li>使用方应用程序必须修改和处理数据。</li>
            <li>数据需要基于多个字段编制索引。</li>
            <li>单个文档将作为单个块进行检索和写入。</li>
    </td>
</tr>
<tr><td><strong>数据类型</strong></td>
    <td>
        <ul>
            <li>可以采用非规范化的方式管理数据。</li>
            <li>单个文档的数据大小相对较小。</li>
            <li>每个文档类型可以使用其自己的架构。</li>
            <li>文档可以包括可选字段。</li>
            <li>文档数据是半结构化的，这意味着每个字段的数据类型不是严格定义的。</li>
            <li>支持数据聚合。</li>
        </ul>
    </td>
</tr>
<tr><td><strong>示例</strong></td>
    <td>
        <ul>
            <li>产品目录</li>
            <li>用户帐户</li>
            <li>材料清单</li>
            <li>个性化</li>
            <li>内容管理</li>
            <li>运营数据</li>
            <li>库存管理</li>
            <li>事务历史记录数据</li>
            <li>其他 NoSQL 存储的具体化视图。 替换文件/BLOB 索引。</li>
        </ul>
    </td>
</tr>
</table>

## <a name="keyvalue-stores"></a>键/值存储

<table>
<tr><td><strong>工作负载</strong></td>
    <td>
        <ul>
            <li>将使用单个 ID 键（例如字典）标识和访问数据。</li>
            <li>高度可伸缩。</li>
            <li>不需要使用联接、锁定或联合。</li>
            <li>不使用聚合机制。</li>
            <li>通常不使用辅助索引。</li>
        </ul>
    </td>
</tr>
<tr><td><strong>数据类型</strong></td>
    <td>
        <ul>
            <li>数据大小通常很大。</li>
            <li>每个键都与单个值相关联，该值是一个非托管数据 BLOB。</li>
            <li>未实施架构。</li>
            <li>实体之间没有关系。</li>
        </ul>
    </td>
</tr>
<tr><td><strong>示例</strong></td>
    <td>
        <ul>
            <li>数据缓存</li>
            <li>会话管理</li>
            <li>用户首选项和配置文件管理</li>
            <li>产品推荐和广告服务</li>
            <li>字典</li>
        </ul>
    </td>
</tr>
</table>

## <a name="graph-databases"></a>图形数据库

<table>
<tr><td><strong>工作负载</strong></td>
    <td>
        <ul>
            <li>数据项之间的关系非常复杂，涉及相关数据项之间的许多跃点。</li>
            <li>数据项之间的关系是动态的并且随时间变化。</li>
            <li>对象之间的关系是头等关系，不需要使用外键和联接进行遍历。</li>
        </ul>
    </td>
</tr>
<tr><td><strong>数据类型</strong></td>
    <td>
        <ul>
            <li>数据由节点和关系组成。</li>
            <li>节点类似于表行或 JSON 文档。</li>
            <li>关系与节点同等重要，并且是直接以查询语言公开的。</li>
            <li>复合对象（例如具有多个电话号码的人员）通常分解为多个单独的较小节点，这些节点通过可遍历的关系组合在一起 </li>
        </ul>
    </td>
</tr>
<tr><td><strong>示例</strong></td>
    <td>
        <ul>
            <li>组织结构图</li>
            <li>社交关系图</li>
            <li>欺诈检测</li>
            <li>分析</li>
            <li>推荐引擎</li>
        </ul>
    </td>
</tr>
</table>

## <a name="column-family-databases"></a>列系列数据库

<table>
<tr><td><strong>工作负载</strong></td>
    <td>
        <ul>
            <li>大多数列系列数据库都极快地执行写入操作。</li>
            <li>更新和删除操作很少发生。</li>
            <li>设计用于提供高吞吐量低延迟访问。</li>
            <li>支持轻松以查询方式访问非常大的记录中的一组特定字段。</li>
            <li>高度可伸缩。</li>
        </ul>
    </td>
</tr>
<tr><td><strong>数据类型</strong></td>
    <td>
        <ul>
            <li>数据存储在由一个键列和一个或多个列系列组成的表中。</li>
            <li>具体的列可能因各个行而异。</li>
            <li>可以通过 get 和 put 命令访问各个单元格</li>
            <li>使用扫描命令返回多个行。</li>
        </ul>
    </td>
</tr>
<tr><td><strong>示例</strong></td>
    <td>
        <ul>
            <li>建议</li>
            <li>个性化</li>
            <li>传感器数据</li>
            <li>遥测</li>
            <li>消息传递</li>
            <li>社交媒体分析</li>
            <li>Web analytics</li>
            <li>活动监视</li>
            <li>天气和其他时序数据</li>
        </ul>
    </td>
</tr>
</table>

## <a name="search-engine-databases"></a>搜索引擎数据库

<table>
<tr><td><strong>工作负载</strong></td>
    <td>
        <ul>
            <li>为来自多个源和服务的数据编制索引。</li>
            <li>查询是即席的，可能会很复杂。</li>
            <li>需要聚合。</li>
            <li>全文搜索是必需的。</li>
            <li>即席自助查询是必需的。</li>
            <li>需要通过所有字段的索引进行数据分析。</li>
        </ul>
    </td>
</tr>
<tr><td><strong>数据类型</strong></td>
    <td>
        <ul>
            <li>半结构化的或非结构化的</li>
            <li>文本</li>
            <li>其中引用了结构化数据的文本</li>
        </ul>
    </td>
</tr>
<tr><td><strong>示例</strong></td>
    <td>
        <ul>
            <li>产品目录</li>
            <li>站点搜索</li>
            <li>日志记录</li>
            <li>分析</li>
            <li>购物网站</li>
        </ul>
    </td>
</tr>
</table>

## <a name="data-warehouse"></a>数据仓库

<table>
<tr><td><strong>工作负载</strong></td>
    <td>
        <ul>
            <li>数据分析</li>
            <li>企业 BI</li>
        </ul>
    </td>
</tr>
<tr><td><strong>数据类型</strong></td>
    <td>
        <ul>
            <li>来自多个源的历史数据。</li>
            <li>通常是非规范化的，采用“星型”或“雪花型”架构，包含事实数据表和维度表。</li>
            <li>通常按计划定期加载新数据。</li>
            <li>维度表通常包括实体的多个历史版本，称为<em>渐变维度</em>。</li>
        </ul>
    </td>
</tr>
<tr><td><strong>示例</strong></td>
    <td>为分析模型、报表和仪表板提供数据的企业数据仓库。
    </td>
</tr>
</table>

## <a name="time-series-databases"></a>时序数据库

<table>
<tr><td><strong>工作负载</strong></td>
    <td>
        <ul>
            <li>绝大部分 (95-99%) 的操作是写入。</li>
            <li>记录通常按时间顺序依次追加。</li>
            <li>很少进行更新。</li>
            <li>删除批量进行，并且针对连续的块或记录执行。</li>
            <li>读取请求可能会大于可用内存。</li>
            <li>通常会有多个读取同时发生。</li>
            <li>数据按升序或降序时间顺序依次读取。</li>
        </ul>
    </td>
</tr>
<tr><td><strong>数据类型</strong></td>
    <td>
        <ul>
            <li>一个用作主键和排序机制的时间戳。</li>
            <li>来自条目的度量或者对条目所代表的内容的描述。</li>
            <li>相关标记，用于定义关于条目的类型、来源和其他信息的附加信息。</li>
        </ul>
    </td>
</tr>
<tr><td><strong>示例</strong></td>
    <td>
        <ul>
            <li>监视和事件遥测。</li>
            <li>传感器或其他 IoT 数据。</li>
        </ul>
    </td>
</tr>
</table>

## <a name="object-storage"></a>对象存储

<table>
<tr><td><strong>工作负载</strong></td>
    <td>
        <ul>
            <li>由键进行标识。</li>
            <li>对象可能可以公开访问，也可能只能以私有方式访问。</li>
            <li>内容通常是诸如电子表格、图像或视频文件的资产。</li>
            <li>内容必须是持久性的（永久性的），并且位于任何应用程序层或虚拟机的外部。</li>
        </ul>
    </td>
</tr>
<tr><td><strong>数据类型</strong></td>
    <td>
        <ul>
            <li>数据大小较大。</li>
            <li>Blob 数据。</li>
            <li>值是不透明的。</li>
        </ul>
    </td>
</tr>
<tr><td><strong>示例</strong></td>
    <td>
        <ul>
            <li>图像、视频、Office 文档、PDF</li>
            <li>CSS、脚本、CSV</li>
            <li>静态 HTML、JSON</li>
            <li>日志和审核文件</li>
            <li>数据库备份</li>
        </ul>
    </td>
</tr>
</table>

## <a name="shared-files"></a>共享文件

<table>
<tr><td><strong>工作负载</strong></td>
    <td>
        <ul>
            <li>从与文件系统进行交互的现有应用进行迁移。</li>
            <li>需要 SMB 接口。</li>
        </ul>
    </td>
</tr>
<tr><td><strong>数据类型</strong></td>
    <td>
        <ul>
            <li>一组分层文件夹中的文件。</li>
            <li>可以通过标准 I/O 库进行访问。</li>
        </ul>
    </td>
</tr>
<tr><td><strong>示例</strong></td>
    <td>
        <ul>
            <li>旧式文件</li>
            <li>可以从许多 VM 或应用实例访问的共享内容</li>
        </ul>
    </td>
</tr>
</table>

<!-- markdownlint-enable MD033 -->
