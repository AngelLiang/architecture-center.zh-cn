---
title: CAF：标识基线规则概述
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 与云治理相关的标识基线的说明
author: BrianBlanchard
layout: LandingPage
ms.topic: landing-page
ms.openlocfilehash: 14569b8db68434ee30fa7bff7fba2da5fa537049
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58242028"
---
# <a name="caf-identity-baseline-discipline-overview"></a>CAF：标识基线规则概述

标识基线是 [CAF 治理模型](../overview.md)中的[云管理的五项规则](../governance-disciplines.md)之一。 标识是云中考虑越来越多的主要安全边界，这是从网络安全方面的传统关注点发生的转变。 标识服务提供在 IT 环境中支持访问控制和组织的核心机制，并且标识基线规则通过在云采用工作中一致地应用身份验证和授权要求，对[安全基准规则](../security-baseline/overview.md)进行了补充。

> [!NOTE]
> 标识基线治理不会替代使组织可以管理和保护标识服务的现有 IT 团队、流程和过程。 此规则的主要用途是确定潜在的标识相关业务风险，并向负责实现、维护和运营标识管理基础结构的 IT 人员提供风险缓解指导。 在制定治理策略和流程时，确保在规划和评审流程中让相关 IT 团队参与进来。

CAF 指导中的此部分概述制定标识基线规则作为云治理策略一部分的方法。 本指南的主要读者是组织的云架构师和云治理团队的其他成员。 但是，根据此规则产生的决策、策略和流程应该包含负责实现和管理组织标识管理解决方案的相关 IT 团队成员的参与和讨论。

如果组织缺少标识基线和安全方面的内部专业人员，请考虑聘请外部安全顾问作为此规则的组成部分。 此外请考虑聘请 [Microsoft 咨询服务](https://www.microsoft.com/enterprise/services)、[Microsoft FastTrack](https://azure.microsoft.com/programs/azure-fasttrack) 云采用服务或其他外部云采用专家来讨论与此规则相关的问题。

## <a name="policy-statements"></a>策略语句

可操作策略语句和生成的体系结构要求将用作标识基线规则的基础。 若要查看策略语句示例，请参阅关于[标识基线策略语句](./policy-statements.md)的文章。 这些示例可以作为组织治理策略的起点。

> [!CAUTION]
> 示例策略来自普通客户体验。 若要更好地将这些策略与特定云治理需求保持一致，请执行以下步骤来创建满足独特业务需求的策略语句。

## <a name="developing-identity-baseline-governance-policy-statements"></a>开发标识基线治理策略语句

以下六个步骤提供了示例以及在制定标识基线治理时要考虑的可能选项。 使用每个步骤作为起点，以便在云治理团队进行讨论以及与组织中受影响的业务和 IT 团队进行讨论，从而建立缓解标识相关风险所需的策略和流程。

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
                        <h3>标识基线模板</h3>
                        <p class="x-hidden-focus">下载用于记录标识基线规则的模板</p>
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
                        <p class="x-hidden-focus">了解通常与标识基线规则关联的动机和风险。</p>
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
                        <p class="x-hidden-focus">用于了解是否是投资标识基线规则的适当时机的指示器。</p>
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
                        <p class="x-hidden-focus">支持标识基线规则中的策略符合性的建议过程。</p>
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
                        <p class="x-hidden-focus">可以通过实现来支持标识基线规则的 Azure 服务。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

<!-- markdownlint-enable MD033 -->

## <a name="next-steps"></a>后续步骤

通过评估特定环境中的[业务风险](./business-risks.md)开始使用。

> [!div class="nextstepaction"]
> [了解业务风险](./business-risks.md)
