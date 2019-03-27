---
title: 大型机迁移：神话和事实
description: 将应用程序从大型机环境迁移到 Azure，这是经过验证的、高度可用且可缩放的基础结构，适用于当前在大型机上运行的系统。
author: njray
ms.date: 12/27/2018
ms.openlocfilehash: bcad01ec044d2d802b055e328a9496aae7b33311
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241538"
---
# <a name="mainframe-myths-and-facts"></a>大型机的相关神话和事实

大型机在计算史中的地位举足轻重，并且对于高度特定的工作负载仍然可行。 多数人都认为大型机是经过验证的平台，有着久经考验的操作过程，这使其成为了稳定可靠的环境。 软件基于使用情况（每秒百万条指令 (MIPS)）运行，并提供全面的使用报告以进行计费。

大型机的可靠性、可用性和处理能力几乎达到了神话般的程度。 要评估最适合 Azure 的大型机工作负载，首先要将这些神话与事实区分开。

## <a name="myth-mainframes-never-go-down-and-have-a-minimum-of-five-9s-of-availability"></a>神话：大型机在任何情况下都不会停机，并且可用性最少为 5 个 9

大型机硬件和操作系统稳定可靠。 但事实上，必须针对维护和重启（即启动程序加载 (IPL)）计划停机时间。 考虑这些任务后，通常大型机解决方案的可用性更接近 2 个或 3 个 9，这相当于基于 Intel 的高端服务器。

此外，与任何其他服务器一样，大型机也易受到灾难的攻击，并且需要不间断电源 (UPS) 系统来处理这些故障类型。

## <a name="myth-mainframes-have-limitless-scalability"></a>神话：大型机可无限伸缩

大型机的可伸缩性取决于系统软件（如客户信息控制系统 (CICS)）的容量，以及新的大型机引擎和存储的实例的容量。 一些使用大型机的大型公司针对性能自定义了其 CICS，并超出了最大可用大型机的能力。

## <a name="myth-intel-based-servers-are-not-as-powerful-as-mainframes"></a>神话：基于 Intel 的服务器不如大型机强大

基于 Intel 的新核心密集型系统的计算容量与大型机一样大。

## <a name="myth-the-cloud-cannot-accommodate-mission-critical-applications-for-large-companies-such-as-financial-institutions"></a>神话：云不适用于大型企业（如金融机构）的任务关键应用程序

虽然可能存在一些云解决方案不适用的独立实例，但这通常是因为应用程序算法无法分发。 这些少数示例属于例外情况，而不是规则。

## <a name="summary"></a>摘要

相比之下，Azure 提供了一个替代平台，能够以更低的成本提供同等大型机的功能和特性。 此外，云的基于订阅的、按使用量付费的成本模型的总拥有成本 (TCO) 远远低于大型机。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [从大型机切换到 Azure](migration-strategies.md)
