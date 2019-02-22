---
title: CAF：驱动部署加速的动机和业务风险
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 了解作为云治理战略一部分的部署加速的规则。
author: alexbuckgit
ms.openlocfilehash: 854b1fd420de605a665922e9b207e6aecbfab2f0
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900456"
---
# <a name="deployment-acceleration-motivations-and-business-risks"></a>部署加速的动机和业务风险

本文讨论客户通常在云治理策略中采用部署加速规则的原因。 此外，它还提供了驱动策略声明的业务风险的几个示例。

<!-- markdownlint-disable MD026 -->

## <a name="is-deployment-acceleration-relevant"></a>部署加速是否相关？

本地系统通常是使用基准映像或安装脚本来部署的。 通常还需要额外的配置，这可能涉及多个步骤或人为干预。 这些手动过程很容易出错且经常导致“配置偏差”，需要大量时间进行故障排除和修复任务。

大部分 Azure 资源都可以通过 Azure 门户来手动部署和配置。 当你只有几个要管理的资源时，此方法或许能够满足你的需求。 但是，随着云资产的增长，你的组织应自动执行云资源的部署，以利用 Azure 提供的缩放、故障转移和灾难恢复功能。 采用 DevOps 或 DevSecOps 方法通常是管理部署的最佳方法。

可靠的部署加速计划可确保正确和一致地部署、更新和配置你的云资源，并始终以这样的方式进行。 部署加速策略的成熟度也是你的[成本管理策略](../cost-management/overview.md)中的一个重要因素。 自动执行云资源的预配和配置，使你能够在需求较低或有时间限制时减少或取消分配资源，这样你只在需要资源时才支付费用。

## <a name="business-risk"></a>业务风险

部署加速规则尝试解决以下业务风险。 在云采用过程中监视以下各项的相关性：

- **服务中断**。 缺乏可预测的可重复部署进程或无法管理对系统配置的更改，可能会中断正常操作并可导致生产或业务的损失。
- **成本超支**。 在配置系统资源过程中的意外更改，可增加标识问题根本原因的难度，从而增加开发、运营和维护的成本。
- **组织效率低下**。 开发、运营和安全团队之间的沟通障碍为有效采用云技术和开发统一的云治理模型带来巨大挑战。

## <a name="next-steps"></a>后续步骤

使用[云管理模板](./template.md)，记录可能由当前云采用计划引入的业务风险。

确立了对实际业务风险的了解后，下一步是记录业务对风险的容忍度，以及监视该容忍度的指示器和关键指标。

> [!div class="nextstepaction"]
> [指标、指示器和风险容忍度](./metrics-tolerance.md)
