---
title: 事件驱动的架构样式
titleSuffix: Azure Application Architecture Guide
description: 介绍 Azure 上事件驱动体系结构和 IoT 体系结构的优点、挑战和最佳做法。
author: MikeWasson
ms.date: 08/30/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seojan19
ms.openlocfilehash: b83a919e4ccd41d20b508b10604365a0b4ca90af
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58245638"
---
# <a name="event-driven-architecture-style"></a>事件驱动的架构样式

事件驱动的架构由生成事件流的事件生成者和侦听事件的事件使用者组成 。

![事件驱动体系结构样式图](./images/event-driven.svg)

事件可几乎实时发送，因此使用者可在事件发生时立即做出响应。 生成者脱离使用者 &mdash; 生成者不知道哪个使用者正在侦听。 使用者之间也能彼此脱离，且每个使用者都能看到所有事件。 这与[使用者竞争][competing-consumers]模式不同，在此模式中，使用者从队列中拉取消息，且消息仅处理一次（假设没有错误）。 在某些系统中（例如 IoT），必须大量引入事件。

事件驱动的架构可以使用发布/订阅模式或事件流模式。

- **发布/订阅**：消息传递基础结构会跟踪订阅。 事件发布后，它会将事件发送给每位订阅者。 事件在接收后，便无法重播，新订阅者也看不见此事件。

- **事件流式处理**：事件会写入日志。 事件是（在分区中）经过严格排序的，而且具有持久性。 客户端不会订阅流，但是客户端可以读取该流的任何部分。 客户端负责提升它在流中的位置。 这意味着客户端可以随时加入，并可以重播事件。

在使用者端，有一些常见的变化：

- **简单事件处理**。 事件会立即触发使用者中的某项操作。 例如，可以将 Azure Functions 与服务总线触发器配合使用，每当消息发布到服务总线主题后，函数便开始执行。

- **复杂事件处理**。 使用者使用 Azure 流分析或 Apache Storm 之类的技术处理一系列事件，寻找事件数据中的模式。 例如，如果移动平均超过特定阈值，便可聚合在某个时间范围内从嵌入式设备读取的信息，并生成通知。

- **事件流处理**。 使用 Azure IoT 中心或 Apache Kafka 等数据流平台作为管道引入事件并将其馈送到流处理器。 此流处理器可处理或转换流。 不同应用程序子系统可能有多种流处理器。 此方法非常适合 IoT 工作负荷。

事件可能来源于系统之外，例如 IoT 解决方案中的物理设备。 在这种情况下，系统必须能够以数据源需要的容量和吞吐量来引入数据。

在上面的逻辑图中，每种类型的使用者都显示为单个框。 实际情况中通常有多个使用者实例，可避免使用者成为系统中的单点故障。 处理事件的容量和频率可能还需要多个实例。 此外，单个使用者可以处理多个线程上的事件。 如果必须按照顺序处理事件，或者需要“恰一次”语义，这就会带来一些挑战。 请参阅[尽量减少协调][minimize-coordination]。

## <a name="when-to-use-this-architecture"></a>何时使用此架构

- 多个子系统必须处理相同的事件。
- 延迟时间最短的实时处理。
- 复杂事件处理，如模式匹配或时间范围内的聚合。
- 大量且快速的数据，如 IoT。

## <a name="benefits"></a>优点

- 生成者和使用者相脱离。
- 没有点到点的集成。 容易向系统添加新使用者。
- 使用者在事件发生时便可立即响应。
- 具有高缩放性，分布广泛。
- 子系统具有独立的事件流视图。

## <a name="challenges"></a>挑战

- 有保证的传递。 在某些系统中，尤其是在 IoT 方案中，保证数据传递至关重要。
- 按顺序或者“恰一次”处理事件。 每种使用者类型通常都在多个实例中运行，以提供复原能力和可伸缩性。 如果必须按顺序（在使用者类型中）处理事件，或处理逻辑不是幂等的，就会带来挑战。

 <!-- links -->

[competing-consumers]: ../../patterns/competing-consumers.md
[minimize-coordination]: ../design-principles/minimize-coordination.md
