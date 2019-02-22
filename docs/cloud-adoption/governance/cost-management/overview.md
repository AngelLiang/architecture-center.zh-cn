---
title: CAF：成本管理规则
description: 与云治理相关的成本管理说明
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
author: BrianBlanchard
layout: LandingPage
ms.topic: landing-page
ms.openlocfilehash: a86b1a75da57cec9c9aaf88abb70049f70731561
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900546"
---
# <a name="cost-management-discipline-overview"></a>成本管理规则概述

成本管理是 [CAF 治理模型](../overview.md)中的某个[云治理五项规则](../governance-disciplines.md)。 对于许多客户而言，采用云技术时，治理成本是主要考虑因素。 平衡性能需求、采用速度和云服务成本可能有难度。 这在实施云技术的主要业务转型期间尤为重要。 本部分概述了一种方法，用于制定成本管理规则，使其作为云治理策略一部分。  

> [!NOTE]
> 成本管理治理不会替换现有的业务团队、记帐行为和过程，这些行为涵盖于组织关于 IT 成本的财务管理中。 此规则的主要用途是标识与 IT 支出相关的潜在云相关风险，并为负责部署和管理云部署的业务和 IT 团队负提供风险缓解指导。 制定治理策略和进程时，请务必让相关的业务和 IT 人员参与计划和评审过程。

本指南的主要读者是组织的云架构师和云治理团队的其他成员。 但是，根据此规则产生的决策、策略和流程应该涉及与相关业务和 IT 团队成员进行互动和讨论，尤其是那些负责拥有、管理和支付基于云的工作负荷的领导者。

## <a name="policy-statements"></a>策略语句

可操作策略语句和生成的体系结构要求将用作成本管理规则的基础。 若要查看策略语句示例，请参阅关于[成本管理策略语句](./policy-statements.md)的文章。 这些示例可以作为组织治理策略的起点。

> [!CAUTION]
> 示例策略来自普通客户体验。 若要更好地将这些策略与特定云治理需求保持一致，请执行以下步骤来创建满足独特业务需求的策略语句。

## <a name="developing-cost-management-governance-policy-statements"></a>制定成本管理治理策略语句

以下六个步骤将帮助你定义治理策略，用于控制环境中的成本。

<!-- markdownlint-disable MD033 -->

<ul  class="panelContent cardsE">
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
                        <h3>成本管理模板</h3>
                        <p class="x-hidden-focus">下载模板，用于记录成本管理规则</p>
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
                        <p class="x-hidden-focus">了解通常与成本管理规则相关的动机和风险。</p>
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
                        <p class="x-hidden-focus">用于了解是否是投资云管理规则的正确时机的指示器。</p>
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
                        <p class="x-hidden-focus">支持成本管理规则中策略符合性的建议过程。</p>
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
                        <p class="x-hidden-focus">可实现为支持成本管理规则的 Azure 服务。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

## <a name="next-steps"></a>后续步骤

通过评估特定环境中的业务风险开始使用。

> [!div class="nextstepaction"]
> [了解业务风险](./business-risks.md)

<!-- markdownlint-enable MD033 -->
