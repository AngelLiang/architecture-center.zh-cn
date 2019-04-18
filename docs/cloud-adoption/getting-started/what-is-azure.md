---
title: CAF：Azure 如何工作？
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
description: 解释 Azure 的内部功能
author: petertaylor9999
ms.date: 02/11/2019
ms.openlocfilehash: 215e4e4954a2f3e722e3ac865fea19508f6edadd
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59068814"
---
<!-- markdownlint-disable MD026 -->

# <a name="how-does-azure-work"></a>Azure 如何工作？

Azure 是 Microsoft 的公有云平台。 Azure 提供大量服务，包括平台即服务 (PaaS) 基础结构即服务 (IaaS)、 数据库即服务 （dbaas） 相关功能的集合。 但是，确切而言，什么是 Azure，它如何工作？

<!-- markdownlint-disable MD034 -->

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2ixGo]

<!-- markdownlint-enable MD034 -->

与其他云平台一样，Azure 依赖于称为**虚拟化**的技术。 可以在软件中仿真大多数计算机硬件，因为大多数计算机硬件只是在硅片中永久或半永久编码的一组指令。 使用将软件指令映射为硬件指令的仿真层，虚拟化的硬件可以在软件中执行，就像它是实际硬件本身一样。

本质上来说，云是位于一个或多个数据中心内的一组物理服务器，它们代表客户执行虚拟化硬件。 那么，云如何同时为数百万客户创建、启动、停止和删除虚拟化硬件的数百万实例？

为了理解这一点，让我们看一下数据中心内硬件的体系结构。  在每个数据中心内是位于服务器机架中的服务器的集合。 每个服务器机架包含许多服务器**刀片**和一个提供网络连接的网络交换机，以及一个用于供电的配电单元 (PDU)。 机架有时组合到一起形成更大的单元，称为**群集**。

在每个机架或群集中，大多数服务器设计为代表用户运行这些虚拟化硬件实例。 但是，某些服务器运行称为结构控制器的云管理软件。 **结构控制器**是一个有许多职责的分布式应用程序。 它分配服务，监视服务器和在其上运行的服务的运行状况，并且在服务器发生故障时将其修复。

结构控制器的每个实例连接到运行云业务流程软件的另一组服务器，通常称为**前端**。 前端托管着用于云执行的所有功能的 Web 服务、RESTful API 和内部 Azure 数据库。

例如，前端托管的服务进行处理客户请求来分配 Azure 资源，例如[虚拟网络](/azure/virtual-network/virtual-networks-overview)，[虚拟机](/azure/virtual-machines)，和之类的服务[Cosmos DB](/azure/cosmos-db/introduction). 首先，前端对用户进行校验并验证用户是否有权分配所请求的资源。 如果是这样，前端会检查数据库，以找到具有足够容量的服务器机架，然后指示结构控制器来分配资源的机架上。

从根本上说，Azure 是巨大的服务器和网络硬件，这些服务器上运行一组复杂的分布式应用程序来协调配置和虚拟化的硬件和软件的操作集合。 它是此业务流程安排使得 Azure 如此强大&mdash;用户不再负责维护和升级硬件，因为 Azure 会在后台的所有这些。

## <a name="next-steps"></a>后续步骤

现在，你已了解 Azure 的内部，了解有关云资源调控。

> [!div class="nextstepaction"]
> [了解资源治理](what-is-governance.md)

<!-- Links -->

[docs-add-users-to-aad]: /azure/active-directory/add-users-azure-active-directory?toc=/azure/architecture/cloud-adoption-guide/toc.json
