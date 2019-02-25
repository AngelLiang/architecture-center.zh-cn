---
title: CAF：安全基线规则改进
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 安全基线规则改进
author: BrianBlanchard
ms.openlocfilehash: 28a971f56c9f8ada1d184bdc1cb3dbb9a17c3507
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900601"
---
# <a name="security-baseline-discipline-improvement"></a>安全基线规则改进

安全基线规则重点围绕创建策略的方法，以保护网络、资产及将位于云提供商解决方案的数据（此内容最为重要）。 在云治理的五个规则中，安全基线包括数字资产和数据的加密。 此外，它还包括对风险、业务容错以及数据、资产和网络安全性的相关缓解策略的说明。 从专业角度而言，它还涉及[加密](../../decision-guides/encryption/overview.md)决策、[网络要求](../../decision-guides/software-defined-network/overview.md)、[混合标识策略](../../decision-guides/identity/overview.md)和用于制定云安全基线策略的[流程](compliance-processes.md)。

本文概述了贵公司可以参与以更好制定和完善安全基线原则的一些潜在任务。 在实现云解决方案中，这些任务可分为计划、构建、采用和操作几个阶段，然后在允许开发[云治理增量方法](../journeys/overview.md#an-incremental-approach-to-cloud-governance)的基础上进行迭代。

![采用的四个阶段](../../_images/adoption-phases.png)

*图 1.云治理增量方法的采用阶段。*

任何一个文档都不可能考虑到所有业务的需求。 因此，本文针对治理成熟过程的每个阶段只概述了建议的最小和可能的示例活动。 这些活动的最初目标是帮助生成[策略 MVP](../journeys/overview.md#an-incremental-approach-to-cloud-governance)，并为增量策略演化建立框架。 云治理团队将需要决定在这些活动需要投入多少成本才能提高安全基线治理能力。

> [!CAUTION]
> 本文列出的最小活动或可能的活动都不针对特定的企业策略或第三方符合性要求。 本指南旨在帮助促进对话，从而使两个需求与云治理模型保持一致。

## <a name="planning-and-readiness"></a>规划和准备情况

这个治理成熟阶段弥合了业务结果与可操作策略之间的分歧。 在此过程中，领导团队定义具体指标，将这些指标对应到数字资产，并开始规划整个迁移工作。

**最小建议的活动：**

- 评估[安全基线工具链](toolchain.md)选项。
- 制定草拟的“体系结构指南”文档并分发给关键的利益干系人。
- 培训受“体系结构指南”制定影响的人员和团队，并使其参与进来。
- 向迁移积压工作 (backlog) 添加优先安全性任务。

**可能的活动：**

- 定义数据分类架构。
- 执行数字资产规划流程，以盘点促进业务流程和支持操作的当前 IT 资产。
- 执行[策略评审](../../governance/policy-compliance/what-is-a-cloud-policy-review.md)，开始实现现有公司 IT 安全策略的现代化，并定义处理已知风险的 MVP 策略。
- 查看云平台的安全指南。 对于 Azure 而言，这些可以在 [Microsoft Service Trust 平台](https://www.microsoft.com/trustcenter/stp/default.aspx)中查找到。
- 确定安全基线策略是否包括[安全开发生命周期](https://www.microsoft.com/securityengineering/sdl/)。
- 根据接下来的一到三个版本，评估网络、数据和与资产相关的业务风险，并评估组织对这些风险的容忍度。
- 查看 Microsoft 的 [网络安全的最新趋势](https://www.microsoft.com/security/operations/security-intelligence-report) 报表，获取当前安全环境的概述。
- 考虑在组织中开发 [Security DevOps](https://www.microsoft.com/en-us/securityengineering/devsecops)（安全 DevOps）角色。

<!-- "en-us" location is required for the URL above. -->

## <a name="build-and-pre-deployment"></a>生成和预部署

成功迁移环境需要许多技术和非技术先决条件。 此过程侧重于推进迁移的决策、准备情况和核心基础结构。

**最小建议的活动：**

- 在预先部署阶段推出，以实现[安全基线工具链](toolchain.md)。
- 更新“体系结构指南”文档并分发给关键的利益干系人。
- 在优先迁移积压工作 (backlog) 上实现安全性任务。
- 制定培训材料和文档、宣传、激励以及其他项目，帮助推动用户采用。

**可能的活动：**

- 确定组织对云托管数据的[加密](../../decision-guides/encryption/overview.md)策略。
- 评估云部署的[标识](../../decision-guides/identity/overview.md)策略。 确定基于云的标识解决方案与本地标识提供者如何共存或集成。
- 为[软件定义网络 (SDN)](../../decision-guides/software-defined-network/overview.md) 设计确定网络边界策略，以确保安全的虚拟网络功能。
- 评估组织的[最小特权访问](/azure/active-directory/users-groups-roles/roles-delegate-by-task)策略，并使用基于任务的角色提供对特定资源的访问。
- 为所有云服务和虚拟机应用安全性和监视机制。
- 尽可能自动执行[安全策略](../../decision-guides/policy-enforcement/overview.md)。
- 查看安全基线策略，并确定是否需要根据最佳做法指导（如[安全开发生命周期](https://www.microsoft.com/securityengineering/sdl/)中所概述的指导）修改计划。

## <a name="adopt-and-migrate"></a>采用和迁移

迁移是一个渐进过程，侧重于现有数字资产中应用程序或工作负载的移动、测试和采用。

**最小建议的活动：**

- 将[安全基线工具链](toolchain.md)从预部署迁移到生产环境。
- 更新“体系结构指南”文档并分发给关键的利益干系人。
- 制定培训材料和文档、宣传、激励以及其他项目，帮助推动用户采用

**可能的活动：**

- 查看最新安全基线和威胁信息，以识别任何新的业务风险。
- 评估组织对处理可能出现的新安全风险的容忍度。
- 识别策略偏离，并强制纠正。
- 调整安全性和访问控制自动化，以确保最大限度的策略符合性。  
- 验证在生成/预部署阶段定义的最佳做法是否正确执行。
- 查看最小特权访问策略并调整访问控制，最大限度地提高安全性。
- 根据工作负载测试安全基线工具链，识别和解决任何漏洞。

## <a name="operate-and-post-implementation"></a>运营和实现后

转换完成后，治理和操作必须依存于应用程序或工作负载的自然生命周期。 治理成熟度的这一阶段侧重于在实现解决方案并且转换周期开始稳定之后通常会出现的活动。

**最小建议的活动：**

- 验证和/或优化[安全基准工具链](toolchain.md)。
- 自定义通知和报表，提醒潜在的安全问题。
- 优化体系结构指南以指导未来的采用流程。
- 定期与受影响的团队进行沟通并进行培训，以确保持续遵守体系结构指南。

**可能的活动：**

- 找出工作负载的模式和行为，并配置监视和报告工具，以识别任何异常活动、访问或资源使用情况并进行通知。
- 持续更新监视和报告策略，以检测最新漏洞、渗透和攻击。
- 部署程序，以便快速阻止未经授权的访问并禁用可能已受攻击者损害的资源。
- 定期查看最新安全最佳做法，并尽可能将建议应用于安全策略、自动化和监视功能。

## <a name="next-steps"></a>后续步骤

了解云安全治理的概念后，接下来请详细了解针对 Azure [Microsoft 提供的安全性和最佳做法指导](azure-security-guidance.md)。

> [!div class="nextstepaction"]
> [了解 Azure 的安全指南](azure-security-guidance.md)
> [Azure 安全性简介](/azure/security/azure-security)
> [了解日志记录、报告和监视](../../decision-guides/log-and-report/overview.md)
