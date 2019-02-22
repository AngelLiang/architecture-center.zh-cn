---
title: CAF：什么是数据分类
description: 什么是数据分类？
author: BrianBlanchard
ms.date: 2/11/2019
ms.openlocfilehash: 07268e7242d92ac2581bf28b378a3c43d166620c
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900534"
---
<!-- markdownlint-disable MD026 -->

# <a name="what-is-data-classification"></a>什么是数据分类？

本文是一篇介绍性文章，内容涉及数据分类常规主题。 所有的治理往往都是从数据分类开始。

## <a name="business-risks-and-governance"></a>业务风险和治理

在大多数组织中，投资治理的主要原因均可归纳为以下三个业务风险：

* 与数据泄露相关的责任
* 故障导致业务中断
* 计划外或意外支出

这三种业务风险可能衍生出许多其他风险。 但它们是最常见的风险。

## <a name="understand-then-mitigate"></a>了解并缓解

在缓解风险之前，必须先了解风险。 对于上面提到的第一种风险，先从数据分类开始了解。 数据分类是将元数据特征与数字财产中的每个资产相关联的过程，它识别与该资产关联的数据类型。

Microsoft 建议，任何已标识为要向云迁移或部署的潜在备选资产都应记录元数据，以记录数据分类、业务关键性和计费责任。 这三个分类点对于了解和缓解风险有很大帮助。

## <a name="microsofts-data-classification"></a>Microsoft 的数据分类

下面是 Microsoft 使用的分类列表。 根据行业或现有安全要求，组织中可能已有数据分类标准。 如果没有标准，建议你使用此示例分类，更好地了解你的数字财产和风险模式。  

* **非业务：** 不属于 Microsoft 的个人生活数据
* **公共：** 可免费获取和批准供公众使用的业务数据
* **常规：** 不面向公众的业务数据
* **机密：** 如果过度共享，可能对 Microsoft 造成损害的业务数据
* **高度机密：** 如果过度共享，会对 Microsoft 造成严重损害的业务数据

## <a name="tagging-data-classification-in-azure"></a>在 Azure 中标记数据分类

每个云提供商都应提供记录任何资产元数据的机制。 元数据对于管理云中的资产至关重要。 就 Azure 而言，资源标记是元数据存储的推荐方法。 有关 Azure 中资源标记的详细信息，请参阅[使用标记来组织 Azure 资源](/azure/azure-resource-manager/resource-group-using-tags)。

## <a name="next-steps"></a>后续步骤

在某个可操作的治理过程中应用数据分类。

> [!div class="nextstepaction"]
> [开始可操作的治理过程](../journeys/overview.md)
