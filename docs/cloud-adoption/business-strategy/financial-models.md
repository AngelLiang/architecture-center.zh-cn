---
title: CAF：为云转换创建财务模型
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
description: 如何为云转换创建财务模型。
author: BrianBlanchard
ms.date: 12/10/2018
ms.topic: guide
ms.openlocfilehash: 1f3ed8a84b84ba577ad5e5db8b1becd318dc04a3
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59068865"
---
# <a name="create-a-financial-model-for-cloud-transformation"></a>为云转换创建财务模型

创建一个财务模型用于准确全面反映任何云转换的商业价值的过程可能十分复杂。 每家组织的财务模型和业务理由往往各不相同。 本文将会建立一些公式，并指出在创建财务模型时经常遗漏的一些事项。

## <a name="return-on-investment-roi"></a>投资回报 (ROI)

对于企业决策层和董事会而言，投资回报 (ROI) 往往是一个重要标准。 ROI 用于比较投资有限资本性资源的不同方式。 ROI 的公式相当简单。 但是，创建公式中每个输入所需的详细信息可能并不简单。 本质上，ROI 是初始投资产生的回报金额。 它通常以百分比表示：

![投资回报 (ROI) = (投资收益 – 投资成本) / 投资成本](../_images/formula-roi.png)

<!-- markdownlint-disable MD036 -->
<!--*ROI = (Gain from Investment &minus; Initial Investment) / Initial Investment*-->
<!-- markdownlint-enable MD036 -->

后续部分将逐步讲解计算初始投资和投资收益（收入）所需的数据。

## <a name="calculating-initial-investment"></a>计算初始投资

初始投资是完成转换所需的资本支出 (CapEx) 和运营支出 (OpEx)。 成本分类根据会计模型和 CFO 的偏好不同而异。 但是，此类别包含如下所述的项目：完成转换所需的专业服务、专门在转换期间使用的软件许可证、转换期间的云服务成本，以及转换期间带薪员工的潜在成本。

将这些成本相加可以估算出初始投资。

## <a name="calculating-the-gain-from-investment"></a>计算投资收益

计算投资收益通常需要用到另一个公式，该公式与业务成果密切相关，并与技术更改相关联。 计算收入不如计算成本缩减那样简单。

若要计算收入，需要两个变量：

![投资收益 = 收入增量 + 成本增量](../_images/formula-gain-from-investment.png)

<!-- markdownlint-disable MD036 -->
<!--*Gain from Investment = Revenue Deltas + Cost Deltas*-->
<!-- markdownlint-enable MD036 -->

下面描述了每个项目。

## <a name="revenue-delta"></a>收入增量

应该与业务部门合作预测收入增量。 业务利益干系人议定收入影响后，可以使用该信息来改善收入状况。

## <a name="cost-deltas"></a>成本增量

成本增量是转换造成的成本增加或减少量。 有大量的独立变量，它们可能会影响成本增量数据。 收入在很大程度上取决于硬性成本，例如资本支出缩减、成本规避、运营成本缩减和折旧。 以下部分提供了要考虑的成本增量示例。

### <a name="depreciation-reductions-or-acceleration"></a>折旧和折旧加速

如需有关折旧的指导，请咨询 CFO 或财务团队。 以下内容是有关折旧主题的一般性参考。

投资采购资产时，该笔投资可用于财务或税务目的，以便在该资产的预期寿命内持续产生收益。 有些公司将折旧视为正面的税务优势。 还有些公司则将它视为承诺的持续支出，类似于构成年度 IT 预算的其他重复性支出。

请咨询财务办公室，以确定是否可以消除折旧，以及折旧是否能够为成本增量提供积极贡献。

### <a name="physical-asset-recovery"></a>物理资产恢复

在某些情况下，可以出售已淘汰的资产，这也是一种收入来源。 通常，为简单起见，这种收入将归并到成本缩减。 但是，它确实提高了收入，因此可能要计税。 请咨询财务办公室来了解此选项的可行性，以及如何计算产生的收入。

### <a name="operational-cost-reductions"></a>运营成本缩减

运营企业所需的重复性支出通常称为运营支出 (OpEx)。 OpEx 的类别非常广泛。 在大多数会计模型中，它包括软件许可、托管支出、电费帐单、不动产租赁、散热支出、运营所需的临时员工、设备租赁、更换部件、维护合同、维修服务、业务连续性/灾难恢复 (BC/DR) 服务，以及其他许多不需要资本支出审批的支出。

考虑运行转换时，此类别是最大收入区域之一。 使此列表变得详尽所投入的时间很少会有浪费。 请向 CIO 和财务团队提问，以确保考虑到所有运营成本。

### <a name="cost-avoidance"></a>成本规避

如果预期需要投入某项运营支出 (OpEx)，但尚未将它列入已批准的预算，则该支出不能划归到成本缩减类别。 例如，如果 VMWare 和 Microsoft 许可证需要重新议价并在下一年付费，则它们并不完全算作成本。 在计算成本增量时，这些预期成本的缩减将被视为类似于运营成本。 但是，正式地讲，在议价和预算审批完成之前，这些成本应该称作“成本规避”。

### <a name="soft-cost-reductions"></a>软性成本缩减

某些公司还可能包含软性成本，例如，运营复杂性的下降，或者运营数据中心所需的全职员工数量的减少。 但是，包括软性成本可能是不明智的。 包括软性成本会引入一种未经阐述的假设：成本缩减等于实际成本节省。 技术项目很少会产生实际的软性成本恢复。

### <a name="headcount-reductions"></a>人数缩减

员工的时间节省通常包括在软性成本缩减中。 将这些时间节省映射到 IT 薪金或人员配备的实际缩减时，可将它单独计算为人数缩减。

即，本地所需的技能通常映射到云中所需的类似技能集（或更高级别）。 这意味着，在云迁移后一般不会裁员。

一种例外情况是运营容量由第三方或托管服务提供商 (MSP) 提供。 如果 IT 系统由第三方管理，则运营成本可由云原生解决方案或云原生 MSP 取代。 云原生 MSP 的运营效率可能更高，且成本可能更低。 如果是这样，则运营成本缩减应包括在硬性成本计算中。

### <a name="capital-expense-reductions-or-avoidance"></a>资本支出缩减或规避

资本支出 (CapEx) 与运营支出略有不同。 一般而言，此类别由更新周期或数据中心扩建驱动。 数据中心扩建的示例是购置新的高性能群集来托管大数据解决方案或数据仓库，通常属于 CapEx 类别。 更常见的资本支出是基本的更新周期。 某些公司已刷新周期的刚性硬件、 含义资产的停用和替换正则周期 （通常每隔三个、 五个，或八年）。 这些周期通常与资产租赁周期或者设备的预测寿命相一致。 到达更新周期时，IT 部门将申请 CapEx 来采购新设备。

如果更新周期经过批准且加入预算，则云转换可以帮助消除该成本。 如果更新周期已计划但尚未获批准，则云转换可以实现 CapEx 成本规避。 这两种情况都会添加到成本增量。

## <a name="next-steps"></a>后续步骤

阅读云转换上下文中的一些财务结果示例。

> [!div class="nextstepaction"]
> [财务结果示例](./business-outcomes/fiscal-outcomes.md)