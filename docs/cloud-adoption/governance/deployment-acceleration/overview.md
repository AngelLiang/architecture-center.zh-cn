---
title: CAF：部署加速规则概述
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 2/11/2019
description: 与云治理相关的部署加速的说明。
author: BrianBlanchard
layout: LandingPage
ms.topic: landing-page
ms.openlocfilehash: ba48bb32292f0bb27ed83550483ceecd725071ff
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900588"
---
# <a name="caf-deployment-acceleration-discipline-overview"></a>CAF：部署加速规则概述

部署加速是 [CAF 治理模型](../overview.md)中[云治理的五项规则](../governance-disciplines.md)之一。 此规则主要介绍建立策略来管理资产配置或部署的方法。 在云治理的五项规则中，部署加速包括部署、配置对齐和脚本可重用性。 这可以通过手动活动或完全自动化的 DevOps 活动来实现。 无论哪种情况，策略基本上将保持不变。 随着此项规则日趋成熟，云治理团队可以通过应用可重用资产加速部署并消除云采用的障碍，从而在 DevOps 和部署策略中充当合作伙伴。

本文概述了公司在实现云解决方案的规划、构建、采用和运维阶段所经历的加速部署过程。 任何单个文档都很难涵盖所有的业务需求。 因此，本文的每一部分都概述了建议的最小和潜在活动。 这些活动的目标是帮助生成[策略 MVP](../policy-compliance/overview.md#minimum-viable-product-mvp-for-policy)，并为[增量策略](../policy-compliance/overview.md#incremental-policy-growth)演化建立框架。 云治理团队应决定在这些活动中投入多少，以改进部署加速的位置。

> [!NOTE]
> 部署加速规则并不会取代让你的组织有效部署和配置基于云资源的现有 IT 团队、流程和过程。 此规则的主要用途是找出潜在业务风险，并向负责管理云中资源的 IT 人员提供风险缓解指导。 在制定治理策略和流程时，请确保在规划和评审流程中有相关 IT 团队参与。

本指南的主要读者是组织中的云架构师和云治理团队的其他成员。 但是，根据此规则形成的决策、策略和流程应包含相关业务及 IT 团队成员的参与和讨论，尤其是那些负责部署和配置基于云的工作负载的领导者。

## <a name="policy-statements"></a>策略语句

可操作策略声明和生成的体系结构要求将用作部署加速规则的基础。 若要查看策略声明示例，请参阅[部署加速策略声明](./policy-statements.md)一文。 这些示例可以作为组织治理策略的起点。

> [!CAUTION]
> 这些示例策略均来自常见的客户体验。 若要更好地将这些策略与特定云治理需求保持一致，请执行以下步骤来创建满足独特业务需求的策略声明。

## <a name="developing-deployment-acceleration-governance-policy-statements"></a>制定部署加速治理策略声明

以下六个步骤将帮助你定义治理策略，以控制云环境中资源的部署和配置。

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
                        <h3>部署加速模板</h3>
                        <p class="x-hidden-focus">下载用于记录部署加速规则的模板</p>
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
                        <p class="x-hidden-focus">了解通常与部署加速规则关联的动机和风险。</p>
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
                        <p class="x-hidden-focus">用于了解是否是投入部署加速规则适当时机的指示器。</p>
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
                        <h3>策略遵循流程</h3>
                        <p class="x-hidden-focus">支持部署加速规则中策略符合性的建议流程。</p>
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
                        <p class="x-hidden-focus">使云管理成熟度与云采用阶段保持一致。</p>
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
                        <p class="x-hidden-focus">可实现以支持部署加速规则的 Azure 服务。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

## <a name="next-steps"></a>后续步骤

首先评估特定环境中的[业务风险](./business-risks.md)。

> [!div class="nextstepaction"]
> [了解业务风险](./business-risks.md)

<!-- markdownlint-enable MD033 -->