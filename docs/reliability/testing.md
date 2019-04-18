---
title: 测试 Azure 应用程序的复原能力和可用性
description: 在 Azure 中测试应用程序的可靠性的方法
author: MikeWasson
ms.date: 04/10/2019
ms.topic: article
ms.service: architecture-center
ms.subservice: cloud-design-principles
ms.openlocfilehash: cf33a859540810279b1cb85be5a6fb2ebe4500dc
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59646492"
---
# <a name="testing-azure-applications-for-resiliency-and-availability"></a>测试 Azure 应用程序的复原能力和可用性

若要测试复原能力，您应该验证端到端工作负荷出现间歇性故障条件下的执行方式。

在使用这两个合成和真实用户数据的生产环境中运行测试。 测试和生产环境很少完全相同，所以务必要验证你的应用程序在生产中使用[蓝绿](https://martinfowler.com/bliki/BlueGreenDeployment.html)或[canary 部署](https://martinfowler.com/bliki/CanaryRelease.html)。 这种方式，以便你可以确保它将正常按预期方式在完全部署后，您要测试实际条件下的应用程序。

作为测试计划的一部分，包括：

- 自动部署前测试
- 错误注入测试
- 峰值负载测试
- 灾难恢复测试
- 第三方服务测试

测试是一个迭代过程。 需要测试应用程序、测量结果、分析并解决出现的任何故障，并重复该过程。

## <a name="perform-fault-injection-testing"></a>执行故障注入测试

对于错误注入测试，检查系统的复原能力在发生故障，期间通过触发实际故障或模拟故障。 下面是一些策略来引入故障：

- 关闭虚拟机 (VM) 实例。
- 使进程崩溃。
- 证书过期。
- 更改访问密钥。
- 关闭域控制器上的 DNS 服务。
- 限制可用的系统资源，例如 RAM 或线程数。
- 卸载磁盘。
- 重新部署 VM。

测试计划应包含在设计阶段，常见的故障案例除了标识可能的故障点：

- 尽可能地生产的环境中测试你的应用程序。
- 在组合中测试失败。
- 测量恢复时间，并确保满足您的业务需求。
- 验证故障不级联和隔离的方式来处理的。

有关故障方案的详细信息，请参阅[Azure 应用程序的故障和灾难恢复](./disaster-recovery.md)。

## <a name="test-under-peak-loads"></a>在峰值负载下测试

负载测试的标识的负载，例如后端数据库瘫痪，才会发生故障或服务限制至关重要。 峰值负载和峰值负载，使用生产数据或生产数据尽可能接近的合成数据预期的增加的测试。 你的目标是了解应用程序在实际情况下的行为方式。

## <a name="conduct-disaster-recovery-drills"></a>执行灾难恢复演练

开始良好的灾难恢复计划，并测试定期以确保它能够正常工作。

对于 Azure 虚拟机，可以使用[Azure Site Recovery](/azure/site-recovery/azure-to-azure-quickstart/)复制并执行[灾难恢复演练](/azure/site-recovery/azure-to-azure-tutorial-dr-drill/)而不会影响生产应用程序或正在进行的复制。

### <a name="failover-and-failback-testing"></a>故障转移和故障回复测试

测试故障转移和故障回复以验证，应用程序的依赖服务恢复以同步方式在灾难恢复过程。 对系统和操作的更改可能会影响故障转移和故障回复的功能，但可能检测到影响，直到主系统发生故障或过载。 测试故障转移功能*之前*需要它们来弥补实时问题。 此外请确保依赖服务故障转移和故障回复中正确的顺序。

如果使用的[Azure Site Recovery](/azure/site-recovery/)若要将 Vm 复制，灾难恢复演练定期运行通过执行测试故障转移，验证复制策略。 测试故障转移不会影响正在进行的 VM 复制或生产环境。 有关详细信息，请参阅[运行到 Azure 的灾难恢复演练](/azure/site-recovery/site-recovery-test-failover-to-azure)。

### <a name="simulation-testing"></a>模拟测试

模拟测试涉及创建小型的现实情况。 模拟在恢复计划中演示的解决方案的有效性，并突出显示任何未充分解决的问题。

在执行模拟测试时，请遵循最佳实践：

- 应用程序执行模拟不会中断实际业务，但让人感觉像真实情况的方式。
- 请确保模拟的方案是完全可控。 如果恢复计划似乎将失败，则可以还原到正常还原这种情况，而不会导致损坏。
- 通知有关何时以及如何将执行模拟练习的管理。 时间范围和在模拟期间受影响的资源，你的计划应详细说明。

## <a name="test-health-probes-for-load-balancers-and-traffic-managers"></a>测试负载均衡器和流量管理器的运行状况探测

配置和测试的负载均衡器和流量管理器的运行状况探测。 请确保运行状况终结点检查系统的关键部分，并做出相应的响应。

- 有关[Azure 流量管理器](/azure/traffic-manager/traffic-manager-overview/)，运行状况探测确定是否将故障转移到另一个区域。 运行状况终结点应检查在同一区域内部署所有关键依赖项。
- 有关[Azure 负载均衡器](/azure/load-balancer/load-balancer-overview/)，运行状况探测确定是否从轮转中删除 VM。 运行状况终结点应报告 VM 的运行状况。 不要包含其他层或外部服务。 否则，在 VM 外部发生的故障会导致负载均衡器从轮转项目中删除该 VM。

有关在应用程序中实施运行状况监视的指导，请参阅[运行状况终结点监视模式](../patterns/health-endpoint-monitoring.md)。

## <a name="test-monitoring-systems"></a>测试监视系统

包括测试计划中监视系统。 自动的故障转移和故障回复系统依赖于监视和检测的正确运行。 若要可视化系统运行状况和运算符警报的仪表板也依赖于具有准确地监视和检测。 如果这些元素发生故障、遗漏重要信息或报告错误的数据，操作人员可能不知道系统不正常或即将发生故障。

## <a name="include-third-party-services-in-test-scenarios"></a>在测试方案中包括第三方服务

如果你的应用程序在第三方服务上具有依赖项，确定位置和如何这些服务可能会失败并有何影响这些故障将对你的应用程序。 请记住服务级别协议 (SLA 的第三方服务和效果这可能会对灾难恢复计划)。

第三方服务可能会提供监视和诊断功能，因此它是重要要记录其调用并将其关联应用程序的运行状况和诊断日志记录使用的唯一标识符。 有关监视和诊断的成熟做法的详细信息，请参阅[监视和诊断指南](../best-practices/monitoring.md)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [部署的弹性和可用性](./deploy.md)
