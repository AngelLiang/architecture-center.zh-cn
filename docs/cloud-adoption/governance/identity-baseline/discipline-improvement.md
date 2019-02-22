---
title: CAF：标识基线规则改进
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 标识基线规则改进
author: BrianBlanchard
ms.openlocfilehash: c96a638af549782fec22b2068c9b4943df4b943a
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900468"
---
# <a name="identity-baseline-discipline-improvement"></a>标识基线规则改进

标识基线规则侧重于建立策略的方法，以确保用户标识的一致性和连续性，而不考虑托管应用程序或工作负载的云提供商。 在云治理的五个规则中，标识基线包括有关[混合标识策略](../../decision-guides/identity/overview.md)的决策、标识存储库的评估和扩展、单一登录（相同登录）的实现、对未经授权使用或恶意参与者的审核和监控。 在某些情况下，还可能涉及到对多个标识提供者进行现代化、合并或集成的决策。

本文概述了贵公司可以参与以更好制定和完善标识基线规则的一些潜在任务。 在实现云解决方案中，这些任务可分为计划、构建、采用和操作几个阶段，然后在允许开发[云治理增量方法](../journeys/overview.md#an-incremental-approach-to-cloud-governance)的基础上进行迭代。

![采用的四个阶段](../../_images/adoption-phases.png)

*图 1.云治理增量方法的采用阶段。*

单个文档很难涵盖所有的业务需求。 因此，本文针对治理成熟过程的每个阶段只概述了建议的最小和可能的示例活动。 这些活动的初衷是帮助生成[策略 MVP](../journeys/overview.md#an-incremental-approach-to-cloud-governance)，并为增量策略演化建立框架。 云治理团队将需要决定在这些活动需要投入多少成本才能提高标识基线治理能力。

> [!CAUTION]
> 本文列出的最小活动或可能的活动都不针对特定的企业策略或第三方合规性要求。 本指南旨在帮助促进对话，使两个需求与云治理模型保持一致。

## <a name="planning-and-readiness"></a>规划和准备情况

这个治理成熟阶段弥合了业务结果与可操作策略之间的分歧。 在此过程中，领导团队定义具体指标，将这些指标对应到数字资产，并开始规划整个迁移工作。

最小建议的活动：

* 评估[标识工具链](toolchain.md)选项并实现适用于组织的混合策略。
* 制定草拟的“体系结构指南”文档并分发给关键的利益干系人。
* 培训受“体系结构指南”制定影响的人员和团队，并使其参与进来。

可能的活动：

* 定义将控制云中标识和访问管理的角色和工作。
* 定义本地组并映射到相应的基于云的角色。
* 清单标识提供者（包括自定义应用程序使用的数据库驱动的标识）。
* 在存在重复的情况下，考虑采用合并或集成标识提供者的选项，以简化整体标识解决方案。
* 评估现有标识提供者的混合兼容性。
* 对于非混合兼容的标识提供者，评估合并或替换选项。

## <a name="build-and-pre-deployment"></a>生成和预部署

成功迁移环境需要许多技术和非技术性的先决条件。 此过程侧重于推进迁移的决策、准备情况和核心基础结构。

最小建议的活动：

* 在实现[标识工具链](toolchain.md)之前，建议进行一个试点测试，确保它尽可能的简化用户体验。
* 然后，将来自试点测试的反馈应用到预部署中。 重复此操作，直到结果可接受。
* 更新“体系结构指南”文档，以包含部署和用户采用计划，并分发给关键的利益干系人。
* 考虑建立一个早期采用者计划，并向限定数量的用户推出。
* 持续对受体系结构指导规则影响最大的人员和团队进行培训。

可能的活动：

* 评估逻辑和物理体系结构，并确定[混合标识策略](../../decision-guides/identity/overview.md)。
* 映射标识访问管理策略（如登录 ID 分配），并为 Azure AD 选择适当的身份验证方法。
  * 如果是联合身份验证，则为管理帐户启用租户限制。
* 集成本地和云目录。
* 请考虑使用以下访问模型：
  * [最低访问权限](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models)模型
  * [特权标识基线](/azure/active-directory/privileged-identity-management/pim-configure)访问模型
* 完成所有预集成详细信息并查看[标识最佳做法](/azure/security/azure-security-identity-management-best-practices)。
  * 启用单一标识、单一登录 (SSO) 或无缝 SSO
  * 为管理员配置多重身份验证 (MFA)
  * 必要时合并或集成标识提供者
  * 实现集中管理标识所需的工具
  * 启用实时 (JIT) 访问和角色更改警报
  * 对分配给内置角色的关键管理活动进行风险分析
  * 考虑为所有用户更新更强的身份验证
  * 为其他管理角色启用 JIT（使用有时间限制的激活）的特权标识基线 (PIM)
  * 将用户帐户与全局管理员帐户分开（确保管理员不会无意中打开电子邮件或运行与其全局管理员帐户关联的程序）

## <a name="adopt-and-migrate"></a>采用和迁移

迁移是侧重于在现有数字资产中移动、测试和采用应用程序或工作负载的增量过程。

最小建议的活动：

* 将[标识工具链](toolchain.md)从开发迁移到生产环境。
* 更新“体系结构指南”文档并分发给关键的利益干系人。
* 制定培训材料和文档、宣传、激励以及其他项目，帮助推动用户采用。

可能的活动：

* 验证在生成/预部署阶段定义的最佳做法是否正确执行。
* 验证和/或改进[混合标识策略](../../decision-guides/identity/overview.md)。
* 确保每个应用程序或工作负载继续与发布前的标识策略保持一致。
* 验证应用程序的单一登录 (SSO) 和无缝 SSO 是否正常运行。
* 尽可能减少或消除替代标识存储的数量。
* 仔细检查任何应用内或数据库内标识存储的需求。 不属于正确标识提供者（第一方或第三方）的标识可能会给应用程序和用户带来风险。
* 启用对[本地联合应用程序](/azure/active-directory/active-directory-device-registration-on-premises-setup)的条件访问。
* 在多个中心跨全局区域分布标识，并在区域之间同步。
* 建立中央基于角色的访问控制 (RBAC) 联合。

## <a name="operate-and-post-implementation"></a>运营和实施后

转换完成后，治理和运维必须依存于应用程序或工作负载的自然生命周期。 治理成熟度这一阶段侧重于在实施解决方案并且转换周期开始稳定之后通常出现的活动。

最小建议的活动：

* 根据组织不断变化的标识需求的变更，自定义你的[标识基线工具链](toolchain.md)。
* 自动化通知和报告，提醒潜在的恶意威胁。
* 监控和报告系统使用情况和用户采用进度。
* 报告部署后的指标并分发给利益干系人。
* 优化体系结构指南以指导未来的采用流程。
* 定期与受影响的团队进行沟通并持续培训，确保持续遵守体系结构指南。

可能的活动：

* 对标识策略和遵循实践进行定期审核。
* 定期扫描恶意参与者和数据泄露，特别是那些与标识欺诈相关的行为，如潜在管理员帐户接管。
* 配置监控和报告工具。
* 考虑更紧密地与安全和防欺诈系统集成。
* 定期查看提升的用户或角色的访问权限。
  * 确定每个有资格激活管理权限的用户。
* 评审载入、调动和凭据更新流程。
* 调查标识访问管理 (IAM) 模块之间自动化和通信级别的提升。
* 考虑实施开发安全操作 (DevSecOps) 方法。
* 执行影响分析，以评估成本、安全性和用户采用的结果。
* 定期生成影响报告，以显示系统创建的指标变化，并评估[混合标识策略](../../decision-guides/identity/overview.md)的业务影响。
* 建立 [Azure 安全中心](/azure/security-center/security-center-intro)推荐的集成监控。

## <a name="next-steps"></a>后续步骤

至此，你已了解云标识治理的概念，接下来，请检查[标识基线工具链](toolchain.md)，确定在 Azure 平台上开发标识基线治理规则时所需的 Azure 工具和功能。

> [!div class="nextstepaction"]
> [适用于 Azure 的标识基线工具链](toolchain.md)