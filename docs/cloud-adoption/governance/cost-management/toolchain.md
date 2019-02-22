---
title: CAF：Azure 中的成本管理工具
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Azure 中的成本管理工具
author: BrianBlanchard
ms.openlocfilehash: 58dfa604863f704fd9b9fbb8d0693447cecdaf84
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900498"
---
# <a name="cost-management-tools-in-azure"></a>Azure 中的成本管理工具

[成本管理](overview.md)是[云治理的五项规则](../governance-disciplines.md)之一。 此规则侧重于建立云支出计划、分配云预算、监视和实施云预算、检测成本高昂的异常情况以及在实际支出不一致时调整云治理计划的方法。

下面的 Azure 本机工具列表可帮助完善支持此治理规则的策略和过程。

|  | [Azure 门户](https://azure.microsoft.com/features/azure-portal/)  | [Azure 成本管理](/azure/cost-management/overview-cost-mgt)  | [Azure EA 内容包](/power-bi/service-connect-to-azure-enterprise)  | [Azure Policy](/azure/governance/policy/overview) |
|---------|---------|---------|---------|---------|
|需要企业协议？     | 否         | 是（使用 [Cloudyn](/azure/cost-management/overview) 时不需要）         | 是         | 否         |
|预算控制     | 否         | 是         | 否         | 是         |
|监视单一资源的支出    | 是         | 是         | 是         | 否         |
|监视多个资源的支出    | 否         | 是        | 是         | 否         |
|控制单一资源的支出     | 是 - 手动调整         | 是         | 否         | 是         |
|跨多个资源强制实施支出    | 否         | 是         | 否         | 是         |
|监视和检测趋势     | 是 - 受限         | 是        | 是         | 否         |
|检测支出异常     | 否         | 是        | 是         | 否        |
|社交偏差     | 否        | 是        | 是        | 否        |
