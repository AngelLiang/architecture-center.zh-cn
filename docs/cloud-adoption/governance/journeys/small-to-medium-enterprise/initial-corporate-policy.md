---
title: CAF：中小型企业 - 治理策略背后的初始公司策略
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 2/11/2019
description: 中小型企业 - 治理策略背后的初始公司策略
author: BrianBlanchard
ms.openlocfilehash: 4f49d0aae2c6ab5a5c8feba669cbbb904af2773b
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900479"
---
# <a name="small-to-medium-enterprise-initial-corporate-policy-behind-the-governance-strategy"></a>中小型企业：治理策略背后的初始公司策略

下面的公司策略定义了初始治理位置，即此过程的起点。 本文定义了早期风险、初始策略声明和强制执行策略声明的早期流程。

> [!NOTE]
>公司策略并非技术文档，但它可推动许多技术决策。 [概述](./overview.md)中所描述的治理 MVP 最终源自此策略。 组织应基于自己的目标和业务风险制定公司策略，然后实现治理 MVP。

## <a name="cloud-governance-team"></a>云治理团队

在此叙述性内容中，云治理团队由两个已意识到治理需求的系统管理员组成。 在接下来的几个月里，他们将接手整顿公司云业务治理的工作，从而获得云管理者头衔。 在随后的演变中，可能会更改此头衔。

[!INCLUDE [business-risk](../../../../../includes/cloud-adoption/governance/business-risks.md)]

## <a name="tolerance-indicators"></a>容忍度指标

当前对风险的容忍度较高，而投入云治理的意愿很低。 因此，容忍度指标可作为早期预警系统，以促使投入时间和精力。 如果观察到以下指标，则应改进治理策略。

- 成本管理：部署到云上的资产规模超过 100 项，或者每月开销超过 1000 美元。
- 安全基线：包含已定义的云采用计划中的受保护数据。
- 资源一致性：包含已定义的云采用计划中的任何任务关键型应用程序。

[!INCLUDE [policy-statements](../../../../../includes/cloud-adoption/governance/policy-statements.md)]

## <a name="next-steps"></a>后续步骤

此公司策略使得云治理团队对实现治理 MVP 有所准备，该策略将作为采用的基础。 下一步是实现此 MVP。

> [!div class="nextstepaction"]
> [最佳做法介绍](./best-practice-explained.md)