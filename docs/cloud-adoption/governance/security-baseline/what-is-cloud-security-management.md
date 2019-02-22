---
title: CAF：什么是云安全基线
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 10/10/2018
description: 什么是云安全基线？
author: BrianBlanchard
ms.openlocfilehash: cb6e3372fd76486e5c4467ef822163eac0499573
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900549"
---
<!-- markdownlint-disable MD026 -->

# <a name="what-is-the-cloud-security-baseline"></a>什么是云安全基线？

这是有关云安全基线一般主题的介绍性文章，云安全基线以[云治理的五项规则](../governance-disciplines.md)为基础而构建，来建立治理框架。 [Azure 的受信任云](https://azure.microsoft.com/overview/trusted-cloud/)提供了有关云安全的更多详细信息。 改进组织安全状况的方法可以在[云安全服务目录](https://www.microsoft.com/security/information-protection)中找到

> [!NOTE]
> 本文并非旨在提供足够的上下文以使读者可以实现安全策略。 它仅用于进行一般了解。

## <a name="cloud-security"></a>云安全

云安全是传统信息安全做法的扩展。 传统 IT 安全包括用于治理计算机安全、网络安全、数据保护、信息使用情况等的策略和控制。 云中需要这些相同的策略和控制。 在任何云转换过程中，CISO 都必须主动参与并了解云环境，以确保旧 IT 策略映射到云中的适当控制级别。

任何云安全策略都应至少考虑以下主题：

* 对数据进行分类：进行正确的数据分类，以了解任何专用、受保护或高度机密的数据源。 这将有助于在规划过程中管理风险。
* 规划混合云方案：了解旧的本地网络如何连接到云将有助于 CISO 确定和缓解风险。
* 规划攻击和错误：在转换的头几个月中，团队会在学习过程中犯错。 从低风险和高度受限的部署开始，以便安全地学习。
* 确定隐私优先级：在任何转换的整个过程中，整个团队都应该将客户和员工隐私放在首位。 数据在云中是安全的，但是团队应在处理敏感数据时保持注意并格外谨慎。

## <a name="protecting-data-and-privacy"></a>保护数据和隐私

对于世界各地的组织 &mdash; 无论是政府机构、非营利组织还是企业 &mdash;，云计算已成为其正在进行的 IT 策略的关键部分。 云服务使所有规模的组织都可以访问几乎无限的数据存储，同时使它们无需购买、维护和更新自己的网络和计算机系统。 Microsoft 和其他云提供商可提供 IT 基础结构、平台和软件即服务 (SaaS)，从而使客户可以根据需要快速向上向上或向下扩展，并且仅为所使用的计算能力和存储付费。

但是，随着组织不断利用云服务的好处（如增加选择、敏捷性和灵活性，同时提高效率并降低 IT 成本），它们必须考虑云服务的引入会如何影响其隐私、安全性和符合性状态。 Microsoft 努力使其云产品/服务不仅可缩放、可靠且可管理，而且可确保客户数据受保护并以透明方式进行使用。

安全性是所有联机计算环境中强大数据安全措施的必要组件。 但仅仅只有安全性并不够。 消费者和企业使用特定云计算产品的意愿还取决于其是否能够信任其信息隐私会受到保护，以及其数据仅采用与客户期望一致的方式进行使用。 详细了解 Microsoft 用于[在云中保护数据和隐私](https://go.microsoft.com/fwlink/?LinkId=808242&clcid=0x409)的方法

## <a name="risk-mitigation"></a>风险缓解

任何数据中心内的两个最大风险可以分成两个来源：老化系统和人为错误。 防范这两种风险是定义 IT 安全策略的最低限度。 这同样适用云中的情况。 以下是为缓解风险并增强云安全策略而可以实施的控制的一些示例。

* 旧系统：本地数据中心解决方案中的许多组件由早于当前安全风险的软件、硬件和流程组成。 如果可能，请在云转换过程中修正、替换或停用这些系统。 当然，这并不总是可行。 如果有任何旧系统将保留在混合解决方案中的生产中，请务必在虚拟数据中心设计过程中对这些系统列出清单并进行了解。 这样做可使设计团队能够消除或控制从云中对这些系统进行的访问。
* IT 安全流程和控制：云设计团队至少应对现有 IT 安全流程和控制进行更新，以便可转移到云中。 在理想方案中，IT 安全团队的成员会在网络安全方面接受培训，并专门充当设计和实现团队的成员。
* 监视和审核：设计治理流程和工具时，请确保监视和审核解决方案包括安全风险或违规。

> [!NOTE]
> 技术债务泄露：本主题缺少可操作的后续步骤。 随着时间的推移，附加文章将扩展此主题。 [Azure 的受信任云](https://azure.microsoft.com/overview/trusted-cloud/)提供了有关云安全的更多详细信息。 改进组织安全状况的方法可以在[云安全服务目录](https://www.microsoft.com/security/information-protection)中找到