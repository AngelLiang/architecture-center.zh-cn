---
title: 设计适用于 Azure 的可复原应用程序
description: 如何在 Azure 中生成可复原应用程序，以实现高可用性和灾难恢复。
author: MikeWasson
ms.date: 12/18/2018
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-design-principles
ms.custom: resiliency
ms.openlocfilehash: 4f33a3bef236ce157955f6348e7befea02a8ce80
ms.sourcegitcommit: 548374a0133f3caed3934fda6a380c76e6eaecea
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2019
ms.locfileid: "58420067"
---
# <a name="designing-resilient-applications-for-azure"></a>设计适用于 Azure 的可复原应用程序

在分布式系统中，故障时有发生。 硬件可能发生故障。 网络也有可能发生暂时性故障。 极少数情况下，整个服务或区域可能会遇到中断，但这些故障必须在计划之内。

在云中生成可靠应用程序不同于在企业设置中生成可靠应用程序。 尽管在传统上我们可以采购可纵向扩展的高端硬件，但在云环境中，我们必须使用可横向扩展而不是纵向扩展的硬件。 可以使用市售硬件来保持云环境的较低成本。 目标不是尝试完全防止故障，而是最大程度地降低系统中某个故障造成的影响。

本文概述如何在 Microsoft Azure 中生成可复原的应用程序。 首先解释术语“复原”的定义和相关概念。 然后，介绍在应用程序从设计和实施到部署和运营的整个生存期内，使用结构化的方法实现复原的过程。

<!-- markdownlint-disable MD026 -->

## <a name="what-is-resiliency"></a>什么是复原？

<!-- markdownlint-enable MD026 -->

**复原**是指系统能够在发生故障后进行恢复，然后继续正常运行。 复原并不旨在避免故障发生，而是通过响应故障来避免故障时间或数据丢失。 复原的目标是在故障发生后将应用程序恢复到可完全正常运行的状态。

复原的两个重要方面是高可用性和灾难恢复。

- **高可用性** (HA) 是指应用程序能够在正常状态下继续运行，而没有显著增加的故障时间。 所谓的“正常状态”是指，应用程序有响应，用户可以连接到应用程序，并与之交互。  
- **灾难恢复** (DR) 是指能够从罕见但非常重大的事件（非暂时性的大规模故障，如影响整个区域的服务中断）中恢复。 灾难恢复包括数据备份和存档，并且可能包括手动干预，如通过备份还原数据库。

选择 HA 还是 DR 的一种思路是，如果故障造成的影响超过了 HA 的应对能力，则应该首选 DR。  

设计复原能力时，必须了解可用性要求。 可以接受多长的故障时间？ 这在一定程度上取决于成本。 潜在的停机会给业务造成多大的损失？ 使应用程序保持高可用性需要投入多少资金？ 此外，必须定义应用程序的可用性具体指的是什么。 例如，应用程序“故障”是否指的是客户可以提交订单，但系统无法在正常时限内处理该订单？ 此外，还要考虑发生特定类型的服务中断的概率，以及缓解策略是否经济高效。

另一个常见术语是**业务连续性** (BC)，指的是在发生不利条件（例如自然灾难或服务中断）期间和之后，执行关键业务功能的能力。 BC 涵盖整个业务运营，包括物理设施、人员、通信、运输和 IT。 本文的重点是云应用程序，但必须在总体业务连续性要求的上下文中进行复原规划。

**数据备份**是灾难恢复的关键组成部分。 如果应用程序的无状态组件发生故障，始终可以重新部署它们。 但是，如果数据丢失，则系统无法恢复稳定状态。 最好在不同的区域将数据备份，以防发生区域范围的灾难。

备份不同于*数据复制*。 数据复制涉及近乎实时地复制数据，以便系统可以快速故障转移到副本。 很多数据库系统支持复制；例如，SQL Server 支持 SQL Server Always On 可用性组。 数据复制可以减少故障后进行恢复所要花费的时间，它确保始终有一个待机的数据副本。 但是，数据复制无法防范人为错误。 如果数据由于人为错误而破坏，则损坏的数据只复制到副本。 因此，仍需在灾难恢复策略中包含长期备份。

## <a name="process-to-achieve-resiliency"></a>实现复原的过程

复原能力不是一个外加的功能。 必须将其融入设计，并在运营中付诸实践。 下面是要遵循的常规模型：

1. 根据业务需求**定义**可用性要求。
2. **设计**应用程序的复原能力。 从遵循经过证实的做法的体系结构着手，识别该体系结构中可能存在的故障点。
3. **实施**策略来检测故障并从中恢复。
4. 通过模拟故障和触发强制故障转移来**测试**实施项目。
5. 使用可靠、可重复的过程将应用程序**部署**到生产环境中。
6. **监视**应用程序以检测故障。 通过监视系统，可以衡量应用程序的运行状况，并在必要的情况下对事件做出响应。
7. 发生需要人工干预的故障时做出**响应**。

本文的余下部分将更详细地介绍上述每个步骤。

## <a name="define-your-availability-requirements"></a>定义可用性要求

复原规划从业务要求开始。 可参考以下几点思路来考虑这项规划。

### <a name="decompose-by-workload"></a>按工作负荷分解

许多云解决方案包括多个应用程序工作负荷。 在此语境中，术语“工作负荷”是指某个离散的功能或计算任务，该任务根据业务逻辑和数据存储要求可与其他任务逻辑分离。 例如，电子商务应用可能包含以下工作负荷：

- 浏览和搜索产品目录。
- 创建和跟踪订单。
- 查看推荐商品。

这些工作负荷在可用性、可伸缩性、数据一致性和灾难恢复方面可能有不同的要求。 进行业务决策时，需要对成本和风险进行平衡。

另外需要考虑使用模式。 系统是否必须在某些关键时段保持可用？ 例如，税务申报服务不能在申报截止日期之前出现故障，视频流服务在重大赛事期间必须保持正常运行，等等。 在关键时段，可以在不同的区域采用冗余部署，以便在某个区域发生故障时，应用程序可以故障转移。 但是，多区域部署的成本可能更高，因此，在非关键时段，可以在单个区域运行应用程序。 在某些情况下，可以使用新式的无服务器技术来减少额外的支出。该技术根据使用情况计费，在计算资源未充分使用的情况下不会收费。

### <a name="rto-and-rpo"></a>RTO 和 RPO

要考虑的两个重要指标是恢复时间目标和恢复点目标，因为它们与灾难恢复有关。

- **恢复时间目标** (RTO) 是指发生某个事件后，可接受应用程序不可用的最长时间。 如果 RTO 是 90 分钟，则从发生灾难开始，必须能够在 90 分钟内将应用程序还原到正常运行状态。 如果 RTO 极低，可以让另一个区域部署以备用方式持续运行一个主动/被动配置，以防范区域性服务中断。 在某些情况下，可以部署主动/主动配置，将 RTO 进一步降低。

- **恢复点目标** (RPO) 是指发生灾难期间，可接受数据丢失的最大持续时间。 例如，如果在单个数据库中存储数据并且未将数据复制到其他数据库，而是执行每小时备份，则最长可能会丢失一小时的数据。

RTO 和 RPO 是系统的非功能性要求，应该符合业务要求。 若要派生这些值，最好是进行风险评估，清楚地了解停机或数据丢失造成的后果。

### <a name="mttr-and-mtbf"></a>MTTR 和 MTBF

另外两个常见的可用性度量值是平均恢复时间 (MTTR) 和平均故障间隔时间 (MTBF)。 这些度量值通常供服务提供商内部使用，用于确定在何处向云服务添加冗余，以及向客户提供哪些 SLA。

**平均恢复时间** (MTTR) 是在故障后还原某个组件所需要的平均时间。 MTTR 是有关组件的经验事实。 可以根据每个组件的 MTTR 来估算整个应用程序的 MTTR。 根据多个 MTTR 值低的组件生成应用程序时，应用程序的总体 MTTR 也低 &mdash; 这意味着可以快速地进行故障恢复。

**平均故障间隔时间** (MTBF) 是指根据合理预期组件在两次中断之间可以持续运行的时间。 可以通过此指标计算服务变得不可用的频率。 不可靠的组件 MTBF 值低，因此其 SLA 值也低。 不过，可以通过部署组件的多个实例并在其间实现故障转移来缓解 MTBF 值低的情况。

> [!NOTE]
> 如果高可用性设置中的组件的任意 MTTR 值超过系统的 RTO，则系统故障会导致不可接受的业务中断。 无法在定义的 RTO 内将系统还原。

### <a name="slas"></a>SLA

在 Azure 中，[服务级别协议][sla] (SLA) 描述 Microsoft 关于运行时间和连接方面的承诺。 如果针对特定服务的 SLA 为 99.9%，则意味着该服务应该在 99.9% 的时间内可用。

> [!NOTE]
> Azure SLA 还包括有关在无法满足 SLA 的情况下获取服务积点的条款，以及每个服务的“可用性”具体定义。 SLA 的此项规定充当强制策略。

应该针对解决方案中的每个工作负荷定义自己的目标 SLA。 通过 SLA 可以评估体系结构是否满足业务要求。 执行依赖项映射练习，确定内部和外部依赖项，例如 Active Directory 或第三方服务（如付款提供商或电子邮件消息服务）。 具体说来，请注意可能在出现某个事件时成为单一故障点或导致瓶颈现象的任何外部依赖项。 例如，如果工作负荷的运行时间需要达到 99.99%，但它依赖于 SLA 为 99.9 % 的服务，则该服务不能是系统中的单一故障点。 一种补救措施是建立回退路径以防该服务发生故障，或者采取其他措施，以便在该服务发生故障时进行恢复。

下表显示了各个 SLA 级别的潜在累积停机时间。

| SLA | 每周故障时间 | 每月故障时间 | 每年故障时间 |
| --- | --- | --- | --- |
| 99% |1.68 小时 |7.2 小时 |3.65 天 |
| 99.9% |10.1 分钟 |43.2 分钟 |8.76 小时 |
| 99.95% |5 分钟 |21.6 分钟 |4.38 小时 |
| 99.99% |1.01 分钟 |4.32 分钟 |52.56 分钟 |
| 99.999% |6 秒 |25.9 秒 |5.26 分钟 |

当然，在其他条件相同的情况下，可用性越高越好。 不过，如果追求更多的 9，则实现该可用性级别所带来的成本和复杂性也会增大。 99.99% 运行时间相当于每月的总停机时间大约为 5 分钟。 是否值得提高复杂性和成本来实现五个 9 的可用性？ 答案取决于业务要求。

下面是定义 SLA 时需要考虑的其他一些因素：

- 若要实现四个 9 (99.99%)，也许就不能依赖于人工干预来从故障中恢复。 应用程序必须能够自我诊断和自我修复。
- 如果超过四个 9，将很难根据 SLA 的要求以足够快的速度检测到服务中断。
- 请考虑衡量 SLA 时所依据的时限。 该时限越小，容限就越严格。 根据每小时或每日运行时间定义 SLA 可能没有意义。
- 考虑 MTBF 和 MTTR 度量。 SLA 越低，服务中断的频率越低，服务恢复的速度越快。

### <a name="composite-slas"></a>复合 SLA

请考虑一个要在 Azure SQL 数据库中写入数据的应用服务 Web 应用。 在撰写本文时，这些 Azure 服务的 SLA 如下：

- 应用服务 Web 应用 = 99.95%
- SQL 数据库 = 99.99%

![复合 SLA](./images/sla1.png)

此应用程序的预期最大停机时间是多少？ 如果任一服务发生故障，整个应用程序也会发生故障。 一般情况下，每个服务发生故障的概率是自主性的，因此，此应用程序的复合 SLA 为 99.95% &times; 99.99%= 99.94%。 这比单个 SLA 更低，但不让人意外，因为依赖于多个服务的应用程序具有更多的潜在故障点。

另一方面，可以通过创建独立的回退路径来提高复合 SLA。 例如，如果 SQL 数据库不可用，可将事务放入队列供稍后处理。

![复合 SLA](./images/sla2.png)

如果采用这种设计，即使应用程序无法连接到数据库，它也能保持可用性。 但是，如果数据库和队列同时发生故障，则应用程序也会发生故障。 同时发生故障的预期时间百分比是 0.0001 &times; 0.001，因此，此组合路径的复合 SLA 是：  

- 数据库或队列 = 1.0 &minus; (0.0001 &times; 0.001) = 99.99999%

总复合 SLA 是：

- Web 应用和（数据库或队列）= 99.95% &times; 99.99999%= ~99.95%

但是，这种方法存在几个缺点。 应用程序逻辑更复杂，因此需要支付队列费用，另外，可能还要考虑到数据一致性问题。

**多区域部署的 SLA**。 另一种 HA 技术是在多个区域中部署应用程序，当一个区域中的应用程序发生故障时，可以使用 Azure 流量管理器实现故障转移。 对于多区域部署，复合 SLA 的计算方式如下。

令 *N* 为一个区域中部署的应用程序的复合 SLA，令 *R* 为部署应用程序的区域数。 应用程序在所有区域中同时发生故障的预期可能性为 ((1 &minus; N) ^ R)。

例如，如果单个区域 SLA 为 99.95%，则

- 两个区域的组合 SLA = (1 &minus; (1 &minus; 0.9995) ^ 2) = 99.999975%
- 四个区域的组合 SLA = (1 &minus; (1 &minus; 0.9995) ^ 4) = 99.999999%

此外还必须考虑[流量管理器的 SLA][tm-sla]。 在撰写本文时，流量管理器的 SLA 为 99.99%。

另外，在主动-被动配置中，故障转移不能瞬间完成，因此在故障转移期间可能会造成一段时间的停机。 请参阅[流量管理器终结点监视和故障转移][tm-failover]。

计算出的 SLA 数字是个有用的基线，但不能反映可用性的方方面面。 通常，在非关键路径发生故障时，应用程序的可用性可以正常降级。 假设某个应用程序显示图书目录。 如果该应用程序无法检索封面的缩略图图像，它可能显示占位符图像。 在这种情况下，无法获取该图像并不会减少应用程序的运行时间，不过会影响用户体验。  

## <a name="design-for-resiliency"></a>复原设计

在设计阶段，应执行故障模式分析 (FMA)。 FMA 旨在识别潜在的故障点，并定义应用程序如何对这些故障做出响应。

- 应用程序如何检测此类故障？
- 应用程序如何对此类故障做出响应？
- 如何记录和监视此类故障？

有关 FMA 过程的详细信息以及针对 Azure 的具体建议，请参阅 [Azure 复原指南：故障模式分析][fma]。

### <a name="example-of-identifying-failure-modes-and-detection-strategy"></a>确定故障模式和检测策略的示例

**故障点：** 调用外部 Web 服务/API。

| 故障模式 | 检测策略 |
| --- | --- |
| 服务不可用 |HTTP 5xx |
| 限制 |HTTP 429（请求过多） |
| Authentication |HTTP 401（未授权） |
| 响应速度慢 |请求超时 |

### <a name="redundancy-and-designing-for-failure"></a>冗余和故障设计

故障的影响范围各不相同。 某些硬件故障（例如磁盘故障）可能影响单个主机。 网络交换机故障可能影响整个服务器机架。 中断整个数据中心的故障（例如数据中心断电）不太常见。 在极少数情况下，整个区域可能不可用。

冗余是让应用程序保持弹性的方法之一。 但是，需要在计划应用程序时规划这种冗余。 此外，所需的冗余级别取决于业务要求 &mdash; 并非每个应用程序都需要跨区域的冗余才能防范区域性服务中断。 一般情况下，提高冗余和可靠性的弊端就是增大成本和复杂性。  

Azure 提供许多功能用于实现每个故障级别的应用程序冗余，从单个 VM 到整个区域。

![Azure 复原功能](./images/redundancy.svg)

**单个 VM**。 Azure 针对单个 VM 提供[运行时间 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines)。 （VM 必须对所有操作系统磁盘和数据磁盘使用高级存储。）尽管可以通过运行两个或更多个 VM 来获得更高的 SLA，则对于某些工作负荷而言，单个 VM 可能已足够可靠。 但是，对于生产工作负荷，我们建议使用两个或更多个 VM 来实现冗余。

**可用性集**。 若要防范局部性硬件故障（例如磁盘或网络交换机故障），请在可用性集中部署两个或更多个 VM。 可用性集包括两个或更多个容错域，它们共用一个电源和网络交换机。 可用性集中的 VM 分布在不同的容错域中，因此，如果硬件故障影响了一个容错域，仍可将网络流量路由到其他容错域中的 VM。 有关可用性集的详细信息，请参阅[在 Azure 中管理 Windows 虚拟机的可用性](/azure/virtual-machines/windows/manage-availability)。

**可用性区域**。  可用性区域是 Azure 区域中的物理独立区域。 每个可用性区域有独立的电源、网络和散热设备。 跨可用性区域部署 VM 有助于在发生数据中心范围的故障时保护应用程序。 并非所有地区都支持可用性区域。 如需支持的地区和服务的列表，请参阅 [Azure 中的可用性区域是什么？](/azure/availability-zones/az-overview)。

如果打算在部署中使用可用性区域，请先验证应用程序体系结构和代码库能否支持该配置。 若要部署商用现货软件，请先咨询软件供应商并进行充分的测试，然后再将软件部署到生产环境中。 在配置的区域中出现中断时，应用程序必须能够保留状态并防止数据丢失。 应用程序必须支持在弹性分布式基础结构中运行，且不在代码库中指定任何硬编码的体系结构组件。

**Azure Site Recovery**。  将 Azure 虚拟机复制到另一个 Azure 区域以满足业务连续性和灾难恢复需求。 可以执行定期的 DR 演练以确保操作满足符合性需求。 源区域中发生故障时，VM 将按照指定的设置复制到所选区域，以便恢复应用程序。 有关详细信息，请参阅[使用 ASR 复制 Azure VM][site-recovery]。 在这里请考虑一下解决方案的 RTO 和 RPO 值，确保测试时的恢复时间和恢复点符合需求。

**配对区域**。 若要在发生区域性服务中断时保护应用程序，可以跨多个区域部署应用程序，并使用 Azure 流量管理器将 Internet 流量分发到不同的区域。 每个 Azure 区域与另一个区域配对。 它们共同构成了[区域对](/azure/best-practices-availability-paired-regions)。 除巴西南部以外，区域对位于同一区域，以符合税务和执法管辖范围方面的数据驻留要求。

设计多区域应用程序时，请注意跨区域的网络延迟高于区域内部的网络延迟。 例如，若要复制数据库以启用故障转移，可在一个区域中使用同步数据复制，但跨区域复制时应使用异步数据复制。

选择配对区域时，请确保两个区域都有必需的 Azure 服务。 如需按区域划分的服务的列表，请参阅[可用产品（按区域）](https://azure.microsoft.com/global-infrastructure/services/)。 另外，必须选择适用于灾难恢复的部署拓扑，尤其是在 RPO/RTO 较短的情况下。 若要确保故障转移区域有足够的容量来支持工作负荷，请选择主动/被动（完整副本）拓扑或主动/主动拓扑。 请记住，这些部署拓扑可能会提高复杂性和成本，因为次要区域的资源已提前预配，这样做可能会导致资源处于空闲状态。 有关详细信息，请参阅[灾难恢复的部署拓扑][deployment-topologies]

| &nbsp; | 可用性集 | 可用性区域 | Azure 站点恢复/配对区域 |
|--------|------------------|-------------------|---------------|
| 故障范围 | 机架 | 数据中心 | 区域 |
| 请求路由 | 负载均衡器 | 跨区域负载均衡器 | 流量管理器 |
| 网络延迟 | 极低 | 低 | 中到高 |
| 虚拟网络  | VNet | VNet | 跨区域 VNet 对等互连 |

## <a name="implement-resiliency-strategies"></a>实施复原策略

本部分提供一些常见复原策略的调查。 其中的大多数策略并不局限于特定的技术。 本部分中的说明汇总了每种技术背后的一般思路，并提供了其他阅读材料的链接。

**重试暂时性故障**。 暂时性故障可能是由于短暂的网络连接中断、删除了数据库连接或服务因繁忙而超时造成的。 通常，只需重试请求即可解决暂时性故障。 针对许多 Azure 服务，客户端 SDK 能够以对调用方透明的方式实施自动重试；请参阅[重试服务指南][retry-service-specific guidance]。

每次重试都会增大总延迟时间。 此外，如果失败的请求过多，可能会导致出现瓶颈，因为挂起的请求会在队列中累积。 这些被阻止的请求可能占用关键的系统资源，例如内存、线程、数据库连接等，从而导致连发故障。 为避免此问题，可增大每两次重试的延迟时间，并限制失败请求的总数。

![重试尝试图](./images/retry.png)

**在实例之间进行负载均衡**。 在可伸缩性方面，云应用程序应该能够通过添加更多实例来横向扩展。 此方法也会改进复原能力，因为可以将不正常的实例从轮转阵容中删除。 例如：

- 将两个或更多个 VM 放在负载均衡器的后面。 负载均衡器可将流量分配到所有 VM。 请参阅[运行负载均衡的 VM 以实现可伸缩性和可用性][ra-multi-vm]。
- 将 Azure 应用服务应用横向扩展到多个实例。 应用服务可自动在实例之间进行负载均衡。 请参阅[基本 Web 应用程序][ra-basic-web]。
- 使用 [Azure 流量管理器][tm]在一组终结点之间分配流量。

**复制数据**。 复制数据是处理数据存储中非暂时性故障的常规策略。 许多存储技术（包括 Azure 存储、Azure SQL 数据库、Cosmos DB 和 Apache Cassandra）提供内置复制。 必须同时考虑读取和写入路径。 根据所用的存储技术，可以创建多个可写副本，或者创建单个可写副本和多个只读副本。

为了最大程度地提高可用性，可将副本放在多个区域。 但是，这会增大复制数据时的延迟。 跨区域复制通常是以异步方式执行的，这意味着，如果某个副本发生故障，将无法遵循最终一致性模型，并可能会丢失数据。

可以使用 [Azure Site Recovery][site-recovery] 将 Azure 虚拟机从一个区域复制到另一个区域。 Site Recovery 将向目标区域持续复制数据。 主站点发生故障时，可以故障转移到辅助位置

**正常降级**。 如果某个服务发生故障且没有故障转移路径，应用程序有时能够正常降级，同时仍能提供可接受的用户体验。 例如：

- 将工作项放入队列供稍后处理。
- 返回一个估计值。
- 使用本地缓存的数据。
- 向用户显示错误消息。 （这种做法比应用程序停止响应请求更好。）

**限制高访问量用户**。 有时，少量的用户会造成过高的负载。 这可能会对其他用户造成影响，降低应用程序的总体可用性。

当单个客户端发出过多的请求时，应用程序可在特定的一段时间内限制该客户端。 在限制期间，应用程序会拒绝来自该客户端的一部分或所有请求（取决于确切的限制策略）。 限制阈值可以根据客户的服务层确定。

限制并不意味着该客户端一定是恶意的，只表示它超出了其服务配额。 在某些情况下，使用者可能一贯地超出其配额或行为异常。 在此情况下，可以进一步阻止该用户。 为此，通常可以阻止其 API 密钥或 IP 地址范围。 有关详细信息，请参阅[限制模式][throttling-pattern]。

**使用断路器**。 [断路器模式][circuit-breaker-pattern]可以防止应用程序重复尝试执行很可能失败的操作。 断路器会包装对服务的调用，并跟踪最近发生故障的次数。 如果故障计数超过阈值，断路器将开始返回错误代码而不会调用该服务。 这使得服务有时间来恢复。

**使用负载调节来平缓流量高峰**。
应用程序可能会遇到突发流量高峰，导致后端上的服务瘫痪。 如果后端服务无法以足够快的速度响应请求，可能会导致请求排队（备份），或导致服务限制应用程序。 为了避免此问题，可以使用队列作为缓冲区。 出现新的工作项时，应用程序不必立即调用后端服务，而可以将工作项排队，以便以异步方式运行它。 队列充当可平缓负载高峰的缓冲区。 有关详细信息，请参阅[基于队列的负载调控模式][load-leveling-pattern]。

**隔离关键资源**。 一个子系统发生故障有时会造成连锁反应，导致应用程序的其他部分发生故障。 如果某个故障导致某些资源（例如线程或套接字）无法及时释放，导致资源耗尽，则可能就会发生这种连锁反应。

为了避免此问题，可将系统分区为独立的组，使一个分区中的故障不会导致整个系统瘫痪。 此方法有时称为[隔舱模式][bulkhead-pattern]。

示例：

- 将数据库分区（例如，按租户），并为每个分区分配独立的 Web 服务器实例池。  
- 使用单独的线程池来隔离对不同服务发出的调用。 这有助于防止其中一个服务发生故障时出现连发故障。 有关示例，请参阅 Netflix [Hystrix 库][hystrix]。
- 使用[容器][containers]来限制特定子系统可用的资源。

![隔舱模式图](./images/bulkhead.png)

**应用补偿事务**。 [补偿事务][compensating-transaction-pattern]是用于消除另一个已完成事务所造成的影响的事务。 在分布式系统中，可能很难实现较强的事务一致性。 补偿事务是使用一系列可在每个步骤中撤消的较小独立事务，实现一致性的方式。

例如，若要预订行程，客户可能需要预约租车、客房和航班。 如果其中的任何步骤失败，整个操作就会失败。 我们可以针对每个步骤定义一个补偿事务，而不用尝试对整个操作使用单个分布式事务。 例如，若要撤消租车，可以取消预订。 为了完成整个操作，协调人员会执行每个步骤。 如果任一步骤失败，协调人员可应用补偿事务，以撤消前面已完成的任何步骤。

## <a name="test-for-resiliency"></a>测试复原能力

一般情况下，无法像测试应用程序功能（运行单元测试等）一样来测试复原能力。 必须在故障状态下测试端到端工作负荷的执行情况，而这种状态只能间歇性地出现。

测试是一个迭代过程。 需要测试应用程序、测量结果、分析并解决出现的任何故障，并重复该过程。

**故障注入测试**。 通过触发实际故障或模拟故障，测试系统在故障期间的复原能力。 下面提供了一些常见的故障情景供测试：

- 关闭 VM 实例。
- 进程崩溃。
- 证书过期。
- 更改访问密钥。
- 关闭域控制器上的 DNS 服务。
- 限制可用的系统资源，例如 RAM 或线程数。
- 卸载磁盘。
- 重新部署 VM。

测量恢复时间，并验证是否满足业务要求。 同时测试故障模式的组合。 确保故障不会造成连锁反应，并且能够以隔离的方式予以处理。

这是为何有必要在设计阶段分析潜在故障点的另一个原因。 该分析的结果应该成为测试计划的输入。

**负载测试**。 要识别只有在承受负载的情况下才会发生的故障（例如，后端数据库瘫痪，或者服务受限制），执行负载测试至关重要。 使用生产数据或尽量与生产数据接近的合成数据测试峰值负载。 目标是检查应用程序在实际条件下的行为方式。

**灾难恢复演练**。 仅有适当的良好灾难恢复计划是不够的。 还需要定期测试它以确保恢复计划在应发挥作用时正常运行。 对于 Azure 虚拟机，可以使用 [Azure Site Recovery][site-recovery] 复制并[执行 DR 演练][site-recovery-test-failover]而不会影响生产应用程序或正在进行的复制。

## <a name="deploy-using-reliable-processes"></a>使用可靠的过程进行部署

将应用程序部署到生产环境后，更新操作就成了一个可能的出错来源。 在最坏的情况下，不当的更新可能导致停机。 为了避免此问题，部署过程必须可预测且可重复。 部署包括预配 Azure 资源、部署应用程序代码和应用配置设置。 更新可能包括上述所有三个步骤，或其中的一部分。

要害的一点在于，手动部署容易出错。 因此，我们建议采用可按需运行的自动化幂等过程，在某个环节出错时，可以重新运行。

- 若要自动预配 Azure 资源，可以使用 [Terraform][terraform]、[Ansible][ansible]、[Chef][chef]、[Puppet][puppet]、[PowerShell][powershell]、[CLI][cli] 或 [Azure 资源管理器模板][template-deployment]
- 使用 [Azure 自动化 Desired State Configuration][dsc] (DSC) 配置 VM。 对于 Linux VM，可以使用 [Cloud-init][cloud-init]。
- 可以使用 [Azure DevOps Services][azure-devops-services] 或 [Jenkins][jenkins] 自动完成应用程序部署。

“基础结构即代码”和“不可变基础结构”是与可复原部署相关的两个概念。

- “基础结构即代码”是指使用代码来预配和配置基础结构的做法。 “基础结构即代码”可以使用声明性方法或命令性方法（或两者的组合）。 资源管理器模板就是声明性方法的一个例子。 PowerShell 脚本是命令性方法的例子。
- “不可变基础结构”是指在将基础结构部署到生产环境后不应对其进行修改的原则。 否则，可能会陷入这种状态：应用即席更改后，很难确切地知道哪些内容已更改，且很难对系统做出推断。

另一个问题是如何推出应用程序更新。 我们建议采用蓝绿部署或“金丝雀发布”方法，以高度受控的方式推送更新，尽量减小不当部署造成的影响。

- [蓝绿部署][blue-green]方法将更新部署到独立于实时应用程序的生产环境。 验证部署后，可将流量改为路由到更新的版本。 例如，Azure 应用服务 Web 应用会在[过渡槽][staging-slots]中启用此方法。
- [金丝雀发布][canary-release]类似于蓝绿部署。 使用此方法时，我们不是将所有流量切换到更新的版本，而是通过将一部分流量路由到新部署，向少部分用户推出更新。 如果出现问题，则回退并还原到旧部署。 否则，将更多的流量路由到新版本，直到路由的流量达到 100%。

不管采用哪种方法，都应该确保在新版本无法正常运行的情况下，可以回滚到上次已知正常的部署。 另请制定一个策略，以便回退数据库更改以及针对依赖的服务所做的任何其他更改。 如果出错，应用程序日志必须指出哪个版本导致了错误。

## <a name="monitor-to-detect-failures"></a>监视以检测故障

监视对于复原能力至关重要。 如果某个组件发生故障，我们需要知道该组件已发生故障，并深入分析故障原因。

监视大规模分布式系统是一个很大的难题。 假设某个应用程序在几十个 VM 上运行 &mdash; 逐个地登录每个 VM，仔细查看日志文件，再尝试排查问题的做法不切实际。 另外，VM 实例的数目可能不是静态的。VM 会随着应用程序的缩减和扩展而不断添加或删除，有时，某个实例可能会发生故障，需要重新预配。 此外，典型的云应用程序可能会使用多个数据存储（Azure 存储、SQL 数据库、Cosmos DB、Redis 缓存），而单个用户操作可能跨多个子系统。

可将监视过程视为包含多个不同阶段的管道：

![复合 SLA](./images/monitoring.png)

- **检测** 要监视的原始数据来自各种源，包括[应用程序日志](/azure/application-insights/app-insights-overview?toc=/azure/azure-monitor/toc.json)、[操作系统性能指标](/azure/azure-monitor/platform/agents-overview)、[Azure 资源](/azure/monitoring-and-diagnostics/monitoring-supported-metrics?toc=/azure/azure-monitor/toc.json)、[Azure 订阅](/azure/service-health/service-health-overview)和 [Azure 租户](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)。 大多数 Azure 服务会公开那些经配置后可以用来分析和确定问题原因的[指标](/azure/azure-monitor/platform/data-collection)。
- **收集和存储**。 可以使用各种格式将原始检测数据保存在不同的位置（例如，应用程序跟踪日志、IIS 日志、性能计数器）。 可以收集、合并这些不同的源并将其放入可靠的数据存储，例如 Application Insights、Azure Monitor 指标、服务运行状况、存储帐户和 Log Analytics。
- **分析和诊断**。 将这些不同数据存储中的数据合并以后，可对其进行分析，以排查问题并提供应用程序运行状况的总体视图。 通常情况下，可以使用 [Kusto 查询](/azure/log-analytics/log-analytics-queries)在 Application Insights 和 Log Analytics 中搜索数据。 Azure 顾问提供的建议着重于[复原能力](/azure/advisor/advisor-high-availability-recommendations)和[优化](/azure/advisor/advisor-performance-recommendations)。
- **可视化和警报**。 在此阶段，将以适当的方式呈现遥测数据，使操作员能够快速发现问题或趋势。 示例包括仪表板或电子邮件警报。 使用 [Azure 仪表板](/azure/azure-portal/azure-portal-dashboards)时，可以生成单一窗格的玻璃视图，其中的监视图源自 Application Insights、Log Analytics、Azure Monitor 指标和服务运行状况。 使用 [Azure Monitor 警报](/azure/monitoring-and-diagnostics/monitoring-overview-alerts?toc=/azure/azure-monitor/toc.json)时，可以创建有关服务运行状况和资源运行状况的警报。

监视与故障检测不同。 例如，应用程序可以检测暂时性错误然后重试，这样就不会导致停机。 但是，它还应该记录重试操作，使我们能够监视错误率，了解应用程序的大致运行状况。

应用程序日志是诊断数据的重要来源。 在应用程序日志记录方面，最佳做法包括：

- 在生产环境中记录日志。 否则，在最需要洞察数据的时候却缺少此类数据。
- 记录服务边界的事件。 包含跨服务边界流动的关联 ID。 如果某个事务流经多个服务，而其中一个服务发生故障，则关联 ID 可帮助查明事务失败的原因。
- 使用语义日志记录，也称为结构化日志记录。 使用非结构化日志很难将日志数据的使用和分析自动化，而云规模的运营需要这种自动化技术。
- 使用异步日志记录。 否则，日志记录系统本身可能导致应用程序发生故障，因为它会导致请求进入备份队列，使请求在等待写入日志记录事件时阻塞。
- 应用程序日志记录与审核不同。 审核可能是出于符合性或法规原因执行的。 在这种情况下，审核记录必须完整，处理事务时不可丢弃任何记录。 如果应用程序需要审核，应该独立于诊断日志记录执行审核。

有关监视和诊断的详细信息，请参阅[监视和诊断指南][monitoring-guidance]。

## <a name="respond-to-failures"></a>对故障做出响应

前面的部分着重于自动化恢复策略，这是实现高可用性的关键所在。 但是，有时需要人工干预。

- **警报**。 监视应用程序中是否出现了可能需要主动干预的警示。 例如，如果看到 SQL 数据库或 Cosmos DB 在不断地限制应用程序，则可能需要增大数据库容量或优化查询。 在此示例中，即使应用程序能够以透明方式处理限制错误，遥测也仍应该引发警报，使我们能够跟进问题。 建议针对服务限制和配额阈值配置有关 Azure 资源指标和诊断日志的警报。 建议设置有关指标的警报，因为与诊断日志相比，其延迟更低。 另外，Azure 可以通过[资源运行状况](/azure/service-health/resource-health-checks-resource-types)提供一些现成的运行状况，这有助于诊断 Azure 服务的限制问题。
- **故障转移**。 为应用程序配置灾难恢复策略。 相应的策略取决于 SLA。 对于大多数方案来说，进行主动-被动实现就可以了。 有关详细信息，请参阅[灾难恢复的部署拓扑](./disaster-recovery-azure-applications.md#deployment-topologies-for-disaster-recovery)。 大多数 Azure 服务允许进行手动或自动故障转移。 例如，在 IaaS 应用程序中，将 [Azure Site Recovery](/azure/site-recovery/azure-to-azure-architecture) 用于 Web 和逻辑层，将 [SQL AlwaysOn 可用性组](/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-dr)用于数据库层。 可以通过[流量管理器](/azure/traffic-manager/traffic-manager-overview)跨区域进行自动故障转移。
- **操作就绪性测试**。 针对故障转移到次要区域和故障回复到主要区域的方案执行操作就绪性测试。 许多 Azure 服务支持通过手动故障转移或测试性故障转移进行灾难恢复演练。 也可通过关闭或删除服务来模拟中断。
- **数据一致性检查**。 如果数据存储发生故障，原因可能是该存储再次可用后存在数据不一致情况，尤其是数据是复制过来的情况下。 对于提供跨区域复制的 Azure 服务，可以通过查看 RTO 和 RPO 来了解故障时预期会出现的数据丢失情况。 查看 Azure 服务的 SLA，了解是可以由用户手动启动跨区域故障转移，还是只能由 Microsoft 启动它。 某些服务由 Microsoft 决定何时执行故障转移。 Microsoft 会评估主要区域的数据恢复的优先级，只有在认为主要区域的数据无法恢复的情况下才将其故障转移到次要区域。 例如，[异地冗余存储](/azure/storage/common/storage-redundancy-grs)和 [Key Vault](/azure/key-vault/key-vault-disaster-recovery-guidance) 遵循这种模式。
- **从备份还原**。 某些情况下，只能在同一区域从备份进行还原。 [Azure VM 备份](/azure/backup/backup-azure-vms-first-look-arm)就是这种情况。 其他 Azure 服务提供异地复制备份，例如 [Redis 缓存异地副本](/azure/redis-cache/cache-how-to-geo-replication)。 备份的目的是防止意外删除或破坏数据，在出现这种情况时将应用程序还原到以前的正常运行版本。 因此，虽然备份可以在某些情况下充当灾难恢复解决方案，但反之却不尽然：灾难恢复不能防止意外删除或破坏数据。  

阐述和测试灾难恢复计划。 评估应用程序故障造成的业务影响。 尽量将过程自动化，并阐述所有手动步骤，例如，手动故障转移或者从备份还原数据的步骤。 定期测试灾难恢复过程，以验证并改进计划。 针对应用程序使用的 Azure 服务设置警报。

## <a name="summary"></a>摘要

本文整体性地讨论了复原能力，并将重点放在云的某些独特难题上。 这些难题包括云计算的分散性、市售硬件的使用，以及暂时性网络故障的存在。

下面是本文阐述的要点：

- 复原能力可提高可用性，减少故障恢复平均时间。
- 在云中实现复原能力需要采用有别于传统本地解决方案的技术组合。
- 复原不是巧合。 必须从一开始就规划并建立复原能力。
- 复原涉及到应用程序生命周期的每个环节：从规划、编程到运营。
- 此外，还要进行测试和监视！

<!-- links -->

[blue-green]: https://martinfowler.com/bliki/BlueGreenDeployment.html
[canary-release]: https://martinfowler.com/bliki/CanaryRelease.html
[circuit-breaker-pattern]: ../patterns/circuit-breaker.md
[compensating-transaction-pattern]: ../patterns/compensating-transaction.md
[containers]: https://en.wikipedia.org/wiki/Operating-system-level_virtualization
[dsc]: /azure/automation/automation-dsc-overview
[contingency-planning-guide]: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-34r1.pdf
[fma]: ./failure-mode-analysis.md
[hystrix]: https://medium.com/netflix-techblog/introducing-hystrix-for-resilience-engineering-13531c1ab362
[jmeter]: https://jmeter.apache.org/
[load-leveling-pattern]: ../patterns/queue-based-load-leveling.md
[monitoring-guidance]: ../best-practices/monitoring.md
[ra-basic-web]: ../reference-architectures/app-service-web-app/basic-web-app.md
[ra-multi-vm]: ../reference-architectures/virtual-machines-windows/multi-vm.md
[checklist]: ../checklist/resiliency.md
[retry-pattern]: ../patterns/retry.md
[retry-service-specific guidance]: ../best-practices/retry-service-specific.md
[sla]: https://azure.microsoft.com/support/legal/sla/
[throttling-pattern]: ../patterns/throttling.md
[tm]: https://azure.microsoft.com/services/traffic-manager/
[tm-failover]: /azure/traffic-manager/traffic-manager-monitoring
[tm-sla]: https://azure.microsoft.com/support/legal/sla/traffic-manager
[site-recovery]: /azure/site-recovery/azure-to-azure-quickstart/
[site-recovery-test-failover]: /azure/site-recovery/azure-to-azure-tutorial-dr-drill/
[site-recovery-failover]: /azure/site-recovery/azure-to-azure-tutorial-failover-failback/
[deployment-topologies]: ./disaster-recovery-azure-applications.md#deployment-topologies-for-disaster-recovery
[bulkhead-pattern]: ../patterns/bulkhead.md
[terraform]: /azure/virtual-machines/windows/infrastructure-automation#terraform
[ansible]: /azure/virtual-machines/windows/infrastructure-automation#ansible
[chef]: /azure/virtual-machines/windows/infrastructure-automation#chef
[puppet]: /azure/virtual-machines/windows/infrastructure-automation#puppet
[template-deployment]: /azure/azure-resource-manager/resource-group-overview#template-deployment
[cloud-init]: /azure/virtual-machines/windows/infrastructure-automation#cloud-init
[azure-devops-services]: /azure/virtual-machines/windows/infrastructure-automation#azure-devops-services
[jenkins]: /azure/virtual-machines/windows/infrastructure-automation#jenkins
[staging-slots]: /azure/app-service/deploy-staging-slots
[powershell]: /powershell/azure/overview
[cli]: /cli/azure
