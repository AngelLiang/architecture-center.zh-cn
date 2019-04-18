---
title: 消息传送模式
titleSuffix: Cloud Design Patterns
description: 云应用程序的分布性质要求消息基础结构在理想情况下能以松散耦合的方式连接组件和服务，从而将可伸缩性最大化。 异步消息受到广泛使用并提供了诸多好处，但也带来了许多挑战，如消息排序、有害消息管理和幂等性等。
keywords: 设计模式
author: dragon119
ms.date: 03/01/2018
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: f838288e10acf9af5776c93972028b6b878bd7b0
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59640662"
---
# <a name="messaging-patterns"></a>消息传送模式

云应用程序的分布性质要求消息基础结构在理想情况下能以松散耦合的方式连接组件和服务，从而将可伸缩性最大化。 异步消息受到广泛使用并提供了诸多好处，但也带来了许多挑战，如消息排序、有害消息管理和幂等性等。

| 模式 | 摘要 |
| ------- | ------- |
| [声明检查](../claim-check.md) | 将大型消息拆分成声明检查和有效负载，以免消息总线过载。 |
| [竞争性使用者](../competing-consumers.md) | 使多个并发使用者能够处理同一消息通道上收到的消息。 |
| [管道和筛选器](../pipes-and-filters.md) | 将一个执行复杂处理的任务分解为一系列可重复使用的单个元素。 |
| [优先级队列](../priority-queue.md) | 为发送到服务的请求确定优先级，以便高优先级请求能够得到比低优先级请求更快速地接收和处理。 |
| [Publisher-Subscriber](../publisher-subscriber.md) | 使应用程序能够以异步方式，而无需向接收方的耦合，发件人宣布到多个希望使用者的事件。 |
| [基于队列的负载调控](../queue-based-load-leveling.md) | 使用队列在任务与所调用的服务之间充当缓冲，从而缓解间歇性负载过大现象。 |
| [计划程序代理监督程序](../scheduler-agent-supervisor.md) | 跨一组分布式服务和其他远程资源协调一组操作。 |
