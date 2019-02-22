---
title: CAF：收集数字资产的清单数据
titleSuffix: Microsoft Cloud Adoption Framework for Azure
description: 如何收集数字资产的清单。
author: BrianBlanchard
ms.date: 12/10/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.openlocfilehash: 0c68ff1e5ff51395698d37fb9b59c7a76c9479b7
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55897246"
---
# <a name="gather-inventory-data-for-a-digital-estate"></a>收集数字资产的清单数据

制定清单是[数字资产规划](overview.md)的第一步。 在此过程中，将会收集支持特定业务功能的 IT 资产的列表，以便进行后续的分析和合理化操作。 本文假设自下而上的分析方法最符合规划需求。 有关详细信息，请参阅[数字资产规划方法](./approach.md)。

## <a name="take-inventory-of-a-digital-estate"></a>收集数字资产的清单

支持数字资产的清单因所需数字转换和相应的转换过程而异。

- **操作转换**。 在操作转换过程中，通常建议通过扫描工具来收集清单，以便创建包含所有 VM 和服务器的集中式列表。 某些工具也可创建网络映射和依赖项，这将有助于定义工作负荷匹配方式。

- **增量转换。** 增量转换的清单从客户开始。 不妨从映射客户从开始到结束的体验着手。 将该映射与应用程序、API、数据和其他资产匹配，即可创建详细的可供分析的清单。

- **中断性转换。** 中断性转换专注于产品或服务。 通过这种方式获得的清单会包含中断市场和所需功能的机会的映射。

## <a name="accuracy-and-completeness-of-an-inventory"></a>清单的准确性和完整性

首次迭代很少能够获得十分完整的清单。 强烈建议云策略团队的各个成员安排利益干系人和高级用户来验证清单。 可能情况下，可以使用网络和依赖项分析等其他工具来标识那些为其发送了流量但未在清单中的资产。

## <a name="next-steps"></a>后续步骤

清单在编译并验证完以后即可合理化。 清单合理化是进行数字资产规划的下一步。

> [!div class="nextstepaction"]
> [将数字资产合理化](rationalize.md)