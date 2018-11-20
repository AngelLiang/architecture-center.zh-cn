---
layout: LandingPage
ms.topic: landing-page
ms.date: 08/30/2018
ms.openlocfilehash: a1cac753d384c0fee0af204cddaeea1e63213b9f
ms.sourcegitcommit: ae8a1de6f4af7a89a66a8339879843d945201f85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2018
ms.locfileid: "43325853"
---
# <a name="azure-application-architecture-guide"></a>Azure 应用程序体系结构指南

本指南提供了一种结构化的方法，用于在Azure上设计可伸缩性、可复原性和高可用性的应用程序。该方法是基于我们从客户互动中掌握的经过验证的实践做法。

## <a name="introduction"></a>介绍

云正在改变应用程序的设计方式。 应用程序被分解成更小的、分散的服务，而不是单一的服务。 这些服务通过 API 或者使用异步消息传送或事件传送进行通信。 根据需要添加新的实例即可实现应用程序横向扩展。 

这些趋势带来了新的挑战。 应用程序状态是分布式的。 操作以并行和异步方式完成。 发生故障时，整个系统必须具有复原能力。 部署必须自动化且可预测。 监视和遥测对于深入了解该系统至关重要。 《Azure 应用程序体系结构指南》旨在帮助读者探索这些变革。 

<table>
<thead>
    <tr><th>传统的本地部署</th><th>现代云</th></tr>
</thead>
<tbody>
<tr><td>单体集中式<br/>
采用可预测的可伸缩性设计<br/>
关系型数据库<br/>
强一致性<br/>
串行和同步处理<br/>
防故障设计 (MTBF)<br/>
偶发性大规模更新<br/>
手动管理<br/>
雪花型服务器</td>
<td>
分解分散<br/>
采用弹性缩放设计<br/>
Polyglot 持久性（存储技术的混合）<br/>
最终一致性<br/>
并行和异步处理<br/>
防故障设计 (MTTR)<br/>
经常性小规模更新<br/>
自动自我管理<br/>
不可变的基础结构<br/>
</td>
</tbody>
</table>

本指南面向应用程序架构师、开发人员和运营团队。 它并不是一份介绍如何使用单个 Azure 服务的操作说明指南。 阅读本指南后，您将会了解体系结构模式，以及有关在 Azure 云平台上生成解决方案时的最佳做法。 也可以下载[电子书版本的指南][ebook]。

## <a name="how-this-guide-is-structured"></a>本指南的结构

《Azure 应用程序体系结构指南》组织成一系列步骤：从体系结构和设计到实施。 每个步骤都有支持性的指导，可帮助设计应用程序体系结构。

### <a name="architecture-styles"></a>架构样式

第一个决策点至关重要。 要生成哪种类型的体系结构？ 它可能是微服务体系结构、更传统的 N 层应用程序，或大数据解决方案。 我们已确定了多种不同的体系结构样式。 这些样式各有利弊。

了解更多：

- [体系结构样式](./architecture-styles/index.md)

### <a name="technology-choices"></a>技术选择

应该尽早在两种技术选择中做出决定，因为它们会影响整个体系结构。 这两种选择是计算服务和数据存储。 “计算”指的是运行应用程序的计算资源的承载模型。 “数据存储”不仅包括数据库，而且还包括消息队列、缓存、日志以及应用程序可能持久存储的其他任何内容所用的存储。 

了解更多：

- [选择计算服务](./technology-choices/compute-overview.md)
- [选择数据存储](./technology-choices/data-store-overview.md)

### <a name="design-principles"></a>设计原理

我们已确定了十条高级设计原则，遵循这些设计原则可以提高应用程序的可伸缩性、可复原性和易管理性。 这些设计原则适用于任何体系结构样式。 在整个设计过程中，请牢记这十条高级设计原则。 然后考虑一套适用于体系结构特定方面（如自动缩放、缓存、数据分区、API 设计等）的最佳做法。

了解更多：

- [设计原则](./design-principles/index.md)


### <a name="quality-pillars"></a>质量要点

一个成功的云应用程序应注重软件质量的五大构成要点：可伸缩性、可用性、可复原性、管理和安全性。 使用我们的设计评审查检表可根据这些质量要点评审体系结构。

- [质量要点](./pillars.md)


[ebook]: https://azure.microsoft.com/campaigns/cloud-application-architecture-guide/
