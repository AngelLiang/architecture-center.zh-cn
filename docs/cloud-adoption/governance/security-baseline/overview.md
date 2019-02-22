---
title: CAF：安全基线规则概述
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 安全基线规则概述
author: BrianBlanchard
layout: LandingPage
ms.topic: landing-page
ms.openlocfilehash: 81398ff943a9a582ae3cc29d9ff7519a0d1f8e54
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900527"
---
# <a name="caf-security-baseline-discipline-overview"></a>CAF：安全基线规则概述

安全基线是 [CAF 治理模型](../overview.md)中的[云管理的五项规则](../governance-disciplines.md)之一。 安全性是任何 IT 部署的组件，而云引入了独有的安全问题。 许多企业受制于法规要求，这些要求使保护敏感数据在考虑云转换时成为一种主要的组织优先事项。 确定云环境面临的潜在安全威胁并建立用于解决这些威胁的流程和程序应是任何 IT 安全或网络安全团队的优先事项。 安全基线规则可确保随着技术要求和安全约束成熟，将这些要求和约束始终如一地应用于云环境。

> [!NOTE]
> 安全基线治理不会替代组织用于保护云部署资源的现有 IT 团队、流程和过程。 此规则的主要用途是确定与安全相关的业务风险，并向负责安全基础结构的 IT 人员提供风险缓解指导。 在制定治理策略和流程时，确保在规划和评审流程中让相关 IT 团队参与进来。

本文概述制定安全基线规则作为云治理策略一部分的方法。 本指南的主要读者是组织的云架构师和云治理团队的其他成员。 但是，源自此规则的决策、策略和流程应涉及相关 IT 和安全团队成员的参与和讨论，尤其是负责实现网络、加密和标识服务的技术主管。

做出正确的安全决策对云部署的成功和更广泛的业务成功至关重要。 如果组织缺少网络安全方面的内部专业人员，请考虑聘请外部安全顾问作为此规则的组成部分。 此外请考虑聘请 [Microsoft 咨询服务](https://www.microsoft.com/enterprise/services)、[Microsoft FastTrack](https://azure.microsoft.com/programs/azure-fasttrack/) 云采用服务或其他外部云采用专家来讨论与此规则相关的问题。

## <a name="policy-statements"></a>策略语句

可操作策略语句和生成的体系结构要求将用作安全基线规则的基础。 若要查看策略语句示例，请参阅关于[安全基线策略语句](./policy-statements.md)的文章。 这些示例可以作为组织治理策略的起点。

> [!CAUTION]
> 示例策略来自普通客户体验。 若要更好地将这些策略与特定云治理需求保持一致，请执行以下步骤来创建满足独特业务需求的策略语句。

## <a name="developing-security-baseline-governance-policy-statements"></a>开发安全基线治理策略语句

以下六个步骤提供了示例以及在制定安全基线治理时要考虑的可能选项。 使用每个步骤作为起点，以便在云治理团队进行讨论以及与组织中受影响的业务、IT 和安全团队进行讨论，从而建立缓解安全相关风险所需的策略和流程。

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
                        <h3>安全基线模板</h3>
                        <p class="x-hidden-focus">下载用于记录安全基线规则的模板</p>
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
                        <p class="x-hidden-focus">了解通常与安全基线规则关联的动机和风险。</p>
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
                        <p class="x-hidden-focus">用于了解是否是投资安全基线规则的适当时机的指示器。</p>
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
                        <p class="x-hidden-focus">支持安全基线规则中的策略符合性的建议过程。</p>
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
                        <p class="x-hidden-focus">可以通过实现来支持安全基线规则的 Azure 服务。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

<!-- markdownlint-enable MD033 -->

## <a name="next-steps"></a>后续步骤

通过评估特定环境中的业务风险开始使用。

> [!div class="nextstepaction"]
> [了解业务风险](./business-risks.md)
