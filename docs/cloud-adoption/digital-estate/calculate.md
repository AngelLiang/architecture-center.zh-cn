---
title: CAF：将成本模型与数字资产匹配
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
description: 将成本模型与数字资产匹配以预测云成本。
author: BrianBlanchard
ms.date: 12/10/2018
ms.topic: guide
ms.openlocfilehash: b37d833106ad487faadab7c4b7ae397fa237d3d3
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55897783"
---
# <a name="align-cost-models-with-the-digital-estate-to-forecast-cloud-costs"></a>将成本模型与数字资产匹配以预测云成本

数字资产合理化以后，就可以通过所选云提供商将它与等效的成本模型匹配。 如果不专注于一个具体的云提供商，则难以讨论成本模型。 在本文中，为了提供切实的示例，假定 Azure 是云提供商。

可以使用 Azure 定价工具透明而准确地管理云支出，以便充分利用 Azure 和其他云。 提供用于监视、分配和优化云成本的工具，使客户能信心十足地加快未来投入。

- [Azure Migrate](/azure/migrate/migrate-overview)。 若要进行模型配准，Azure Migrate 可能是最经济有效的方式。 此工具将数字资产[清单](inventory.md)、[有限合理化](rationalize.md)和成本计算功能集于一个工具中。

- [总拥有成本 (TCO) 计算器](https://azure.com/tco)。 通过 Azure 云平台降低本地基础结构的总拥有成本 (TCO)。 使用 Azure TCO 计算器估算将应用程序工作负荷迁移到 Azure 可节省的成本。 提供本地环境的简单介绍即可获取即时报告。

- [定价计算器](https://azure.microsoft.com/en-in/pricing/)。 使用我们的定价计算器估算预期的每月账单开支。 使用计费门户随时跟踪实际帐户使用情况和账单。 设置通过电子邮件自动发送计费警告，在消费额超出配置的金额时发送通知。

- [Azure 成本管理](https://azure.microsoft.com/services/cost-management/)。 Microsoft 子公司 Cloudyn 授权的 Azure 成本管理是一个多云成本管理解决方案，可帮助你最佳地利用并管理 Azure 和其他云资源。 通过 Azure、Amazon Web Services 和 Google Cloud Platform 提供的应用程序编程接口 (API)，收集云使用情况和计费数据。 通过该数据，可在单个统一视图中完整查看各云平台的资源使用情况和成本。 持续监视云使用情况和成本趋势。 针对预算跟踪实际云开支，避免超支。 检测开支异常和使用效率低的情况。 使用历史数据提升云使用和支出的预测准确性。
