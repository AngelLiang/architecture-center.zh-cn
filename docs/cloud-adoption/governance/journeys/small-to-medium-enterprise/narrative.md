---
title: CAF：中小型企业 - 治理策略背后的初始说明
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 此说明为小中型企业治理过程建立了一个用例。
author: BrianBlanchard
ms.openlocfilehash: 6173b01f310169017761110d6641acfa51d45df8
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900678"
---
# <a name="small-to-medium-enterprise-the-narrative-behind-the-governance-strategy"></a>中小型企业：治理策略背后的说明

以下说明介绍了针对[小中型企业治理过程](./overview.md)的用例。 实施此过程之前，请务必理解本说明中反映的假设和推理。 这有助于你更好地使治理策略与自己组织的发展过程保持一致。

## <a name="back-story"></a>背景情况

今年年初，董事会计划通过多种方式为企业注入活力。 他们正在推动领导层，以期改善客户体验，进而获得市场份额。 此外，他们还推动新产品和新服务，使公司成为业内的思想领袖。 他们还同时发起了一项减少浪费和削减不必要成本的活动。 尽管充满压力，但董事会和领导层的行动表明，这一工作正在全力整合资本用于未来的发展。

过去，公司的 CIO 一直被排除在这些策略对话之外。 然而，由于未来愿景本质上与技术发展相关，因此，IT 人员在会议桌上也有了一席之地，帮助指导这些宏大的计划。 IT 现在应能够以新的方式提供支持。 该团队并没有真正为这些变化做好准备，并且很可能对学习曲线感到困扰。

## <a name="business-characteristics"></a>业务特征

该公司具有以下业务配置文件：

- 所有销售和运营都集中在一个国家/地区，全球客户的比例很低。
- 企业作为单个业务单元运营，预算按照部门职能（包括销售、营销、运营和 IT）来制定。
- 公司将大多数 IT 视为资本流失或成本中心。

## <a name="current-state"></a>当前状态

以下是该公司 IT 和云运营的当前状态：

- IT 运营两个托管的基础结构环境。 一个环境包含生产资产。 另一个环境包含灾难恢复和一些开发/测试资产。 这些环境由两个不同的提供商托管。 IT 将这些两个数据中心分别称为“生产”和“灾难恢复”。
- IT 通过将所有最终用户的电子邮件帐户迁移到 Office 365 进入云。 此迁移已在六个月前完成。 仅少量 IT 资产已部署到云。
- 应用程序开发团队正致力于以开发/测试的身份了解云原生能力。
- 商业智能 (BI) 团队正在尝试云中的大数据和新平台上的数据管理。
- 该公司有一个松散定义的策略，规定客户个人身份信息 (PII) 和财务数据不能托管在云中，这限制了当前部署中的关键任务型应用程序。
- IT 投资主要由资本支出 (CapEx) 控制。 这些投资每年计划一次。 在过去几年中，投资只包括基本的维护要求。

## <a name="future-state"></a>未来状态

预计在未来几年会有以下改变：

- CIO 正在审查关于 PII 和财务数据的策略，以实现未来的状态目标。
- 应用程序开发和 BI 团队希望基于客户参与和新产品的愿景，在未来 24 个月内向生产环境发布基于云的解决方案。
- 今年，IT 团队会通过将 2,000 个 VM 迁移到云中，完成停用 DR 数据中心的灾难恢复工作负载。 预计这将在未来五年节省 2,500 万美元的成本。
    ![相比于 Azure 成本，本地成本未来五年的回报为 2,500 万美元](../../../_images/governance/calculator-small-to-medium-enterprise.png)
- 该公司计划通过在 IT 内将已承诺的 CapEx 重置为运营费用 (OpEx) 来变更 IT 投资的方式。 此变更将提供更好的成本控制，并使 IT 加速其他规划工作。

## <a name="next-steps"></a>后续步骤

该公司已制定出公司策略，以形成治理实施。 该公司策略将推动很多技术决策。

> [!div class="nextstepaction"]
> [查看初始公司策略](./initial-corporate-policy.md)
