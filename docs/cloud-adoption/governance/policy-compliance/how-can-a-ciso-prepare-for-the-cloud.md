---
title: CAF：CISO 就绪情况
description: CISO 如何为采用云做好准备
author: BrianBlanchard
ms.date: 10/03/2018
ms.openlocfilehash: cedb86488304e2fc84897e1da373730768adce66
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900668"
---
# <a name="ciso-cloud-readiness-guide"></a>CISO 云就绪情况指南

Azure 云采用框架 (CAF) 等 Microsoft 指南并不旨在确定或指导本文档支持的数千个企业的唯一安全约束。 迁移到云时，首席信息安全官 (CISO) 或 CISO 办公室的作用不会被云技术所取代。 恰恰相反，CISO 和 CISO 办公室变得更加根深蒂固和相互协调。 读者需要熟悉 CISO 流程，并试图通过现代化这些流程来启用云转换，才能更好地理解本指南。

借助云采用，可启用传统 IT 环境中通常不考虑的服务。 自助服务或自动部署通常由应用程序开发团队，或一直不与生产部署保持一致的其他 IT 团队执行。 在一些组织中，业务构成部分同样有自助服务功能。 这可能会触发本地环境中以前不需要的新安全要求。 集中安全更具挑战性，安全性通常成为整个业务和 IT 文化中的共同责任。 本文可有助于 CISO 为采用这种方法做好准备，并参与增量治理。

## <a name="how-can-the-ciso-prepare-for-the-cloud"></a>CISO 如何为采用云做好准备

与大多数策略一样，组织内的安全和治理策略往往会有组织地扩展。 当安全事件发生时，他们会制定策略，以通知用户，并减少重复发生的可能性。 虽然这很自然，但这种方法会造成策略膨胀和技术依赖。 云转换之旅创造了独一无二的策略现代化和重置机会。 为任何转换之旅做准备时，CISO 可以担任[策略评审](./what-is-a-cloud-policy-review.md)中的主要利益干系人，从而直接创造可度量的价值。

在此类评审中，CISO 的作用是，在现有策略/合规性的约束与云提供商的改进后安全态势之间取得安全平衡。 此进度可采用多种度量形式，通常是以安全卸载到云提供商的安全策略数为度量依据。

**转移安全风险**：随着服务迁移到基础结构即服务 (IaaS) 托管模型中，企业在硬件预配方面承担的直接风险越来越少。 风险不是被消除，而是转移给云供应商。 如果云供应商的硬件预配方法可实现相同级别的风险缓解，那么在安全的可重复流程中，硬件预配执行风险会从企业策略中消除。 现在可以替换采用新策略来验证这些流程，但执行风险会重新分配，从而降低整体安全风险。

随着解决方案进一步“向上堆叠”以整合平台即服务 (PaaS) 或软件即服务 (SaaS) 模型，可以缓解、转移和替换其他风险。 将风险安全地转移给云提供商后，也可以安全地降低与执行、监视和强制采用安全策略或其他合规性策略相关的成本。

**成长型思维模式**：对于企业以及技术实现者来说，改变可能有点可怕。 我们发现，当 CISO 引领组织改为采用成长型思维模式时，他们天生的恐惧就会被对安全和策略合规性日益浓厚的兴趣所取代。 以成长型思维模式进行[策略评审](./what-is-a-cloud-policy-review.md)、转型之旅或简单的实现评审，团队可以快速行动，但不会以公平且可管理的风险状况为代价。

## <a name="resources-for-the-chief-information-security-officer"></a>面向首席信息安全官的资源

若要以成长型思维模式进行[策略评审](./what-is-a-cloud-policy-review.md)，云知识至关重要。 以下资源可有助于 CISO 更好地了解 Microsoft Azure 平台的安全态势。

安全平台资源：

* [安全开发周期、内部审核](https://www.microsoft.com/sdl/)
* [强制性安全培训、背景检查](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx)
* [渗透测试、入侵检测、DDoS、审核和日志记录](https://www.microsoft.com/trustcenter/Security/AuditingAndLogging)
* [最先进的数据中心](https://www.microsoft.com/cloud-platform/global-datacenters)、物理安全性和[安全网络](/azure/security/security-network-overview)
* [云中的 Microsoft Azure 安全响应 (PDF)](http://aka.ms/SecurityResponsePaper)

隐私和控制：

* [随时进行数据管理](https://www.microsoft.com/trustcenter/Privacy/You-own-your-data)
* [控制数据位置](https://www.microsoft.com/trustcenter/Privacy/Where-your-data-is-located)
* [根据条件提供数据访问](https://www.microsoft.com/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms)
* [响应执法部门](https://www.microsoft.com/trustcenter/Privacy/Responding-to-govt-agency-requests-for-customer-data)
* [严格的隐私标准](https://www.microsoft.com/TrustCenter/Privacy/We-set-and-adhere-to-stringent-standards)

合规性：

* [信任中心](https://www.microsoft.com/trustcenter/default.aspx)
* [通用控制中心](https://www.microsoft.com/trustcenter/Common-Controls-Hub)
* [云服务审慎调查清单](https://www.microsoft.com/trustcenter/Compliance/Due-Diligence-Checklist)
* [按服务、位置和行业划分的合规性](https://www.microsoft.com/trustcenter/Compliance/default.aspx)

透明度：

* [Microsoft 如何保护 Azure 服务中的客户数据](https://www.microsoft.com/trustcenter/Transparency/default.aspx)
* [Microsoft 如何管理 Azure 服务中的数据位置](http://azuredatacentermap.azurewebsites.net/)
* [Microsoft 中的哪些人员可以根据哪些条款访问数据](https://www.microsoft.com/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms)
* [Microsoft 如何保护 Azure 服务中的客户数据](https://www.microsoft.com/trustcenter/Transparency/default.aspx)
* [查看 Azure 服务的认证、透明度中心](https://www.microsoft.com/trustcenter/Compliance/default.aspx)

## <a name="next-steps"></a>后续步骤

在任何治理策略中，采取的第一步行动是[策略评审](./what-is-a-cloud-policy-review.md)。 在策略评审期间，可参考的实用指南是[策略与合规性](./overview.md)。

> [!div class="nextstepaction"]
> [为策略评审做好准备](./what-is-a-cloud-policy-review.md)