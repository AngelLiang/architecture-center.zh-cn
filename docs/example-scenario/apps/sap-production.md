---
title: 使用 Oracle 数据库运行 SAP 生产工作负荷
titleSuffix: Azure Example Scenarios
description: 在 Azure 中使用 Oracle 数据库运行 SAP 生产部署。
author: DharmeshBhagat
ms.date: 09/12/2018
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
ms.custom: fasttrack, SAP, Windows, Linux
social_image_url: /azure/architecture/example-scenario/apps/media/architecture-sap-production.png
ms.openlocfilehash: 03714dbf08c23220fa95a3789adb40d7a5cfac92
ms.sourcegitcommit: 3b15d65e7c35a19506e562c444343f8467b6a073
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2019
ms.locfileid: "54908233"
---
# <a name="running-sap-production-workloads-using-an-oracle-database-on-azure"></a>在 Azure 上使用 Oracle 数据库运行 SAP 生产工作负荷

SAP 系统用于运行任务关键型业务应用程序。 任何服务中断都会干扰关键的流程，并可能导致开支增大或收入损失。 避免这些后果需要一个高度可用的，且能弹性应对故障的 SAP 基础结构。

构建高度可用的 SAP 环境需要消除系统体系结构和流程中的单一故障点。 站点故障、系统组件中的错误甚至人为失误都可能导致单一故障点。

本示例方案演示 Azure 上 Windows 或 Linux 虚拟机 (VM) 中的一个 SAP 部署，以及一个高可用性 (HA) Oracle 数据库。 对于 SAP 部署，可以根据要求使用不同大小的 VM。

## <a name="relevant-use-cases"></a>相关用例

其他相关用例包括：

- SAP 上运行的任务关键型工作负荷。
- 非关键的 SAP 工作负荷。
- 模拟高可用性环境的 SAP 测试环境。

## <a name="architecture"></a>体系结构

![Azure 中生产 SAP 环境的体系结构概况][architecture]

本示例中包含 Oracle 数据库、SAP 中心服务和不同虚拟机上运行的多个 SAP 应用程序服务器的高可用性配置。 出于安全考虑，Azure 网络使用了[中心辐射型拓扑](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)。 数据流经解决方案的情形如下所示：

1. 用户通过 SAP 用户界面、Web 浏览器或其他客户端工具（例如 Microsoft Excel）访问 SAP 系统。 ExpressRoute 连接提供从组织本地网络到 Azure 中运行的资源的访问。
2. ExpressRoute 在 Azure 中的 ExpressRoute 虚拟网络 (VNet) 网关处终止。 网络流量通过中心 VNet 中的创建 ExpressRoute 网关路由到网关子网。
3. 中心 VNet 与分支 VNet 建立对等互连。 应用程序层子网托管在可用性集中运行 SAP 的虚拟机。
4. 标识管理服务器为解决方案提供身份验证服务。
5. 系统管理员使用跳转盒来安全管理 Azure 中部署的资源。

### <a name="components"></a>组件

- 本方案使用[虚拟网络](/azure/virtual-network/virtual-networks-overview)在 Azure 中创建虚拟的中心辐射型拓扑。

- [虚拟机](/azure/virtual-machines/windows/overview)为解决方案的每个层提供计算资源。 每个虚拟机群集配置为[可用性集](/azure/virtual-machines/windows/regions-and-availability#availability-sets)。

- [ExpressRoute](/azure/expressroute/expressroute-introduction) 通过连接服务提供商建立的专用连接将本地网络扩展到 Microsoft 云。

- [网络安全组 (NSG)](/azure/virtual-network/security-overview) 限制对虚拟网络中资源的网络访问。 NSG 包含一个安全规则列表，这些规则可根据源或目标 IP 地址、端口和协议允许或拒绝网络流量。

- [资源组](/azure/azure-resource-manager/resource-group-overview#resource-groups)充当 Azure 资源的逻辑容器。

### <a name="alternatives"></a>备选项

SAP 针对 Azure 环境中的不同操作系统组合、数据库管理系统和 VM 类型提供灵活的选项。 有关详细信息，请参阅 [SAP 说明 1928533](https://launchpad.support.sap.com/#/notes/1928533)“Azure 上的 SAP 应用程序：支持的产品和 Azure VM 类型”。

## <a name="considerations"></a>注意事项

- 我们定义了建议的做法，说明如何在 Azure 中构建高度可用的 SAP 环境。 有关详细信息，请参阅[适用于 SAP NetWeaver 的高可用性体系结构和方案](/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)。 另请参阅 [Azure VM 上的 SAP 应用程序的高可用性](/azure/virtual-machines/workloads/sap/high-availability-guide)。

- Oracle 数据库也提供了适用于 Azure 的建议做法。 有关详细信息，请参阅[在 Azure 中设计和实施 Oracle 数据库](/azure/virtual-machines/workloads/oracle/oracle-design)。

- Oracle Data Guard 用于消除任务关键型 Oracle 数据库的单一故障点。 有关详细信息，请参阅[在 Azure 中的 Linux 虚拟机上实施 Oracle Data Guard](/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)。

- Microsoft Azure 提供基础结构服务用于部署包含 Oracle 数据库的 SAP 产品。 有关详细信息，请参阅[在 Azure 上部署适用于 SAP 工作负荷的 Oracle DBMS](/azure/virtual-machines/workloads/sap/dbms_guide_oracle)。

## <a name="pricing"></a>定价

为帮助你了解运行本方案的成本，我们在以下成本计算器示例中预配置了所有服务。 若要了解自己的特定用例的定价变化情况，请按预期的流量更改相应的变量。

我们已根据你预期接收的流量提供了四个示例成本配置文件：

|大小|SAP|DB VM 类型|DB 存储|(A)SCS VM|(A)SCS 存储|应用 VM 类型|应用存储|Azure 定价计算器|
|----|----|-------|-------|-----|---|---|--------|---------------|
|小型|30000|DS13_v2|4xP20、1xP20|DS11_v2|1xP10|DS13_v2|1xP10|[小型](https://azure.com/e/45880ba0bfdf47d497851a7cf2650c7c)|
|中型|70000|DS14_v2|6xP20、1xP20|DS11_v2|1xP10|4xDS13_v2|1xP10|[中型](https://azure.com/e/9a523f79591347ca9a48c3aaa1406f8a)|
大型|180000|E32s_v3|5xP30、1xP20|DS11_v2|1xP10|6xDS14_v2|1xP10|[大型](https://azure.com/e/f70fccf571e948c4b37d4fecc07cbf42)|
特大型|250000|M64s|6xP30、1xP30|DS11_v2|1xP10|10xDS14_v2|1xP10|[特大型](https://azure.com/e/58c636922cf94faf9650f583ff35e97b)|

> [!NOTE]
> 此定价仅供指导，并且仅指出了 VM 和存储费用。 此费用不包括网络、备份存储和数据传入/传出费用。

- [小型](https://azure.com/e/45880ba0bfdf47d497851a7cf2650c7c)：小型系统，包括适用于数据库服务器的 VM 类型 DS13_v2，提供 8 个 vCPU、56-GB RAM 和 112-GB 临时存储，另外还提供 5 个 512-GB 高级存储磁盘。 使用 DS11_v2 VM 类型的 Server SAP Central 实例，提供 2 个 vCPU、14-GB RAM 和 28-GB 临时存储。 适用于 SAP 应用程序服务器的单个 VM 类型 DS13_v2，提供 8 个 vCPU、56-GB RAM 和 400-GB 临时存储，另外还提供 1 个 128-GB 高级存储磁盘。

- [中型](https://azure.com/e/9a523f79591347ca9a48c3aaa1406f8a)：中型系统，包括适用于数据库服务器的 VM 类型 DS14_v2，提供 16 个 vCPU、112-GB RAM 和 800-GB 临时存储，另外还提供 7 个 512-GB 高级存储磁盘。 使用 DS11_v2 VM 类型的 Server SAP Central 实例，提供 2 个 vCPU、14-GB RAM 和 28-GB 临时存储。 适用于 SAP 应用程序服务器的 4 个 VM 类型 DS13_v2，提供 8 个 vCPU、56-GB RAM 和 400-GB 临时存储，另外还提供 1 个 128-GB 高级存储磁盘。

- [大型](https://azure.com/e/f70fccf571e948c4b37d4fecc07cbf42)：大型系统，包括适用于数据库服务器的 VM 类型 E32s_v3，提供 32 个 vCPU、256-GB RAM 和 800-GB 临时存储，另外还提供 3 个 512-GB 和 1 个 128-GB 高级存储磁盘。 使用 DS11_v2 VM 类型的 Server SAP Central 实例，提供 2 个 vCPU、14-GB RAM 和 28-GB 临时存储。 适用于 SAP 应用程序服务器的 6 个 VM 类型 DS14_v2，提供 16 个 vCPU、112-GB RAM 和 224-GB 临时存储，另外还提供 6 个 128-GB 高级存储磁盘。

- [特大型](https://azure.com/e/58c636922cf94faf9650f583ff35e97b)：特大型系统，包括适用于数据库服务器的 M64s VM 类型，提供 64 个 vCPU、1024-GB RAM 和 2000-GB 临时存储，另外还提供 7 个 1024-GB 高级存储磁盘。 使用 DS11_v2 VM 类型的 Server SAP Central 实例，提供 2 个 vCPU、14-GB RAM 和 28-GB 临时存储。 适用于 SAP 应用程序服务器的 10 个 VM 类型 DS14_v2，提供 16 个 vCPU、112-GB RAM 和 224-GB 临时存储，另外还提供 10 个 128-GB 高级存储磁盘。

## <a name="deployment"></a>部署

使用以下链接部署本方案的底层基础结构。

<!-- markdownlint-disable MD033 -->

<a
href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Fsolution-architectures%2Fmaster%2Fapps%2Fsap-3tier-distributed-ora%2Fazuredeploy.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

<!-- markdownlint-enable MD033 -->

> [!NOTE]
> 部署过程中不会安装 SAP 和 Oracle。 需要单独部署这些组件。

## <a name="related-resources"></a>相关资源

有关在 Azure 中运行 SAP 生产工作负荷的其他信息，请查看以下参考体系结构：

- [适用于 AnyDB 的 SAP NetWeaver](/azure/architecture/reference-architectures/sap/sap-netweaver)
- [SAP S/4HANA](/azure/architecture/reference-architectures/sap/sap-s4hana)
- [SAP HANA 大型实例](/azure/architecture/reference-architectures/sap/hana-large-instances)

<!-- links -->
[architecture]: media/architecture-sap-production.png
