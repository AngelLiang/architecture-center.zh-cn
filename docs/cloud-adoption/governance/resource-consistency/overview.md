---
title: CAF：资源一致性规则概述
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 资源一致性规则概述
author: BrianBlanchard
layout: LandingPage
ms.topic: landing-page
ms.openlocfilehash: ce29efab1502d59513528ab4b640173346aee516
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241978"
---
# <a name="caf-resource-consistency-discipline-overview"></a>CAF：资源一致性规则概述

资源一致性是 [CAF 治理模型](../overview.md)中的[云治理的五项规则](../governance-disciplines.md)之一。 此规则侧重于建立与环境、应用程序或工作负荷的操作管理相关的策略的方法。 IT 运营团队通常会提供对应用程序、工作负荷和资产性能的监视。 他们一般还会执行满足缩放需求、纠正性能服务级别协议 (SLA) 违规以及通过自动补救主动避免性能 SLA 违规所需的任务。 在云治理的五项规则中，资源一致性规则可确保资源能够一致配置并满足以下要求：可以被 IT 运营团队发现、包含在恢复解决方案中，并且可载入可重复的操作流程中。

> [!NOTE]
> 资源一致性治理并不会取代现有的 IT 团队、流程和过程，这些团队、流程和过程允许组织有效地管理基于云的资源。 此规则的主要用途是确定潜在业务风险，并向负责管理云中资源的 IT 人员提供风险缓解指导。 在制定治理策略和流程时，确保在规划和评审流程中让相关 IT 团队参与进来。

CAF 的这一部分概述了如何在云治理策略中制定资源一致性规则。 本指南的主要读者是组织的云架构师和云治理团队的其他成员。 但是，根据此规则产生的决策、策略和流程应该包含负责实现和管理组织的资源一致性解决方案的 IT 团队相关成员的参与和讨论。

如果组织缺少资源一致性策略方面的内部专业人员，请考虑聘请外部顾问作为此规则的组成部分。 此外，请考虑聘请 [Microsoft 咨询服务](https://www.microsoft.com/enterprise/services)、[Microsoft FastTrack](https://azure.microsoft.com/programs/azure-fasttrack) 云采用服务或其他外部云采用专家来讨论如何以最佳方式组织、跟踪和优化云资产。

## <a name="policy-statements"></a>策略语句

可操作的策略语句和生成的体系结构要求将用作资源一致性规则的基础。 若要查看策略语句示例，请参阅关于[资源一致性策略语句](./policy-statements.md)的文章。 这些示例可以作为组织治理策略的起点。

> [!CAUTION]
> 示例策略来自普通客户体验。 若要更好地将这些策略与特定云治理需求保持一致，请执行以下步骤来创建满足独特业务需求的策略语句。

## <a name="developing-resource-consistency-governance-policy-statements"></a>制定资源一致性治理策略语句

以下六个步骤提供了示例以及在制定资源一致性治理时要考虑的可能选项。 使用每个步骤作为起点，以便在云治理团队内进行讨论以及与组织中受影响的业务和 IT 团队进行讨论，从而建立缓解资源一致性风险所需的策略和流程。

<!-- markdownlint-disable MD033 -->

<ul class="panelContent cardsE">
<li style="display: flex; flex-direction: column;">
    <a href="./template.md">
        <div class="cardSize">
            <div class="cardPadding" >
                <div class="card" >
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../../_images/governance/process-template.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText" style="padding-left:0px;">
                        <h3>资源一致性模板</h3>
                        <p class="x-hidden-focus">下载用于记录资源一致性规则的模板</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li><li style="display: flex; flex-direction: column;">
    <a href="./business-risks.md">
        <div class="cardSize">
            <div class="cardPadding" >
                <div class="card" >
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../../_images/governance/process-risks.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText" style="padding-left:0px;">
                        <h3>业务风险</h3>
                        <p class="x-hidden-focus">了解通常与资源一致性规则关联的动机和风险。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./metrics-tolerance.md">
        <div class="cardSize">
            <div class="cardPadding" >
                <div class="card" >
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../../_images/governance/process-metrics.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText" style="padding-left:0px;">
                        <h3>指示器和指标</h3>
                        <p class="x-hidden-focus">用于了解是否是投资资源一致性规则的适当时机的指示器。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./compliance-processes.md">
        <div class="cardSize">
            <div class="cardPadding" >
                <div class="card" >
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../../_images/governance/process-enforce.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText" style="padding-left:0px;">
                        <h3>策略遵循过程</h3>
                        <p class="x-hidden-focus">支持资源一致性规则中的策略符合性的建议过程。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./discipline-improvement.md">
        <div class="cardSize">
            <div class="cardPadding" >
                <div class="card" >
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../../_images/governance/process-maturity.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText" style="padding-left:0px;">
                        <h3>成熟度</h3>
                        <p class="x-hidden-focus">将云管理成熟度与云采用阶段保持一致。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./toolchain.md">
        <div class="cardSize">
            <div class="cardPadding" >
                <div class="card" >
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../../_images/governance/process-toolchain.png" class="x-hidden-focus"/>
                        </div>
                    </div>
                    <div class="cardText" style="padding-left:0px;">
                        <h3>工具链</h3>
                        <p class="x-hidden-focus">可以通过实现来支持资源一致性规则的 Azure 服务。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

## <a name="next-steps"></a>后续步骤

通过评估特定环境中的[业务风险](./business-risks.md)开始使用。

> [!div class="nextstepaction"]
> [了解业务风险](./business-risks.md)
