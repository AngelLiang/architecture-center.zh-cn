---
title: CAF：标识决策指南
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 了解 Azure 迁移中作为核心服务的标识。
author: rotycenh
ms.openlocfilehash: addc11928a0bc72917a28b468a04880720a1fdf4
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900536"
---
# <a name="identity-decision-guide"></a>标识决策指南

在任何环境中，无论是本地环境、混合环境，还是纯云环境，IT 都需要控制哪些管理员、用户和组可以访问资源。 标识和访问管理 (IAM) 服务使你能够管理云中的访问控制。

![绘制复杂性从最低到最高的标识选项，并与下面的跳转链接保持一致](../../_images/discovery-guides/discovery-guide-identity.png)

跳转到：[确定标识集成要求](#determine-identity-integration-requirements) | [云原生](#cloud-baseline) | [目录同步](#directory-synchronization) | [云托管的域服务](#cloud-hosted-domain-services) | [Active Directory 联合身份验证服务](#active-directory-federation-services) | [不断演变的标识集成](#evolving-identity-integration) | [了解更多信息](#learn-more)

在云环境中有几种管理标识的方法，它们的成本和复杂性各不相同。 构建基于云的标识服务的一个关键因素是现有本地标识基础结构所需的集成级别。

基于云的软件即服务 (SaaS) 标识解决方案为云资源提供基本级别的访问控制和标识管理。 然而，如果组织的 Active Directory (AD) 基础结构具有复杂的林结构或自定义组织单元 (OU)，则基于云的工作负载可能需要将目录复制到云中，以在本地环境和云环境之间获得一组一致的标识、组和角色。 如果全局解决方案需要目录复制，则复杂性会显著增加。 此外，对依赖于传统身份验证机制的应用程序的支持可能需要在云中部署域服务。

## <a name="determine-identity-integration-requirements"></a>确定标识集成要求

| 问题 | 云基线 | 目录同步 | 云托管的域服务 | AD 联合身份验证服务 |
|------|------|------|------|------|
| 你当前是否缺少本地目录服务？ | 是 | 否 | 否 | 否 |
| 你的工作负载是否需要针对本地标识服务进行身份验证？ | 否 | 是 | 否 | 否 |
| 你的工作负载是否依赖于传统身份验证机制，例如 Kerberos 或 NTLM？ | 否 | 否 | 是 | 否 |
| 云和本地标识服务之间的集成是否不可能？ | 否 | 否 | 是 | 否 |
| 你是否需要跨多个标识提供者进行单一登录？ | 否 | 否 | 否 | 是 |

在迁移到 Azure 的计划中，你需要确定如何以最佳方式集成现有标识管理和云标识服务。 以下是常见集成方案。

### <a name="cloud-baseline"></a>云基线

公有云平台提供了本机 IAM 系统，用于授予用户和组对管理功能的访问权限。 如果组织缺少重要的本地标识解决方案，并且你计划迁移工作负载，以与基于云的身份验证机制兼容，则应使用云原生标识服务构建标识基础结构。

**云基线假设**。 使用纯云原生标识基础结构时假设如下条件：

- 基于云的资源将不会依赖于本地目录服务或 Active Directory 服务器，或者可以修改工作负载来删除这些依赖项。
- 迁移的应用程序或服务工作负载要么支持与云标识提供者兼容的身份验证机制，要么可以轻松进行修改以支持它们。 云原生标识提供者依赖于 Internet 就绪的身份验证机制，如 SAML、OAuth 和 OpenID Connect。 使用 Kerberos 或 NTLM 等协议依赖于传统身份验证方法的现有工作负载在迁移到云之前可能需要进行重构。

> [!TIP]
> 大多数云原生标识服务并不能完全替代传统本地目录。 如果不使用其他工具或服务，目录功能（如计算机管理或组策略）可能不可用。

将标识服务完全迁移到基于云的提供程序，就不需要维护你自己的标识基础结构，从而大大简化了 IT 管理。

### <a name="directory-synchronization"></a>目录同步

对于具有现有标识基础结构的组织，目录同步通常是保存现有用户和访问权限管理的最佳解决方案，同时提供管理云资源所需的 IAM 功能。 这个过程不断在云环境和本地环境之间复制目录信息，允许用户使用单一登录 (SSO)，并在整个组织中使用一致的标识、角色和权限系统。

注意：采用 Office 365 的组织可能已经在其本地 Active Directory 基础结构和 Azure Active Directory 之间实现[目录同步](/office365/enterprise/set-up-directory-synchronization)。

目录同步假设。 使用同步标识解决方案时假设如下条件：

- 你需要跨云和本地 IT 基础结构维护一组通用的用户帐户和组。
- 本地标识服务支持使用云标识提供者进行复制。
- 你需要为访问云和本地标识提供者的用户提供 SSO 机制。

> [!TIP]
> 任何依赖于传统身份验证机制的基于云的工作负载，不受 Azure AD 等基于云的标识服务支持，仍然需要连接到本地域服务，或提供这些服务的云环境中的虚拟服务器。 此外，使用本地标识服务还会引入对云和本地网络之间连接性的依赖项。

### <a name="cloud-hosted-domain-services"></a>云托管的域服务

如果你有依赖于使用传统协议（如 Kerberos 或 NTLM）的基于声明进行身份验证的工作负载，并且这些工作负载无法重构以接受 SAML 或 OAuth 和 OpenID Connect 等新式身份验证协议，则可能需要将某些域服务作为云部署的一部分迁移到云。

这种类型的部署包括在基于云的虚拟网络中部署运行 Active Directory 的虚拟机，以便为云中的资源提供域服务。 迁移到云网络的任何现有应用程序和服务都应该能够使用这些云托管的目录服务器，只需稍加修改即可。

你的现有目录和域服务很可能将继续在本地环境中使用。 在此场景中，建议同时使用目录同步来在云环境和本地环境中提供一组通用的用户和角色。

云托管域服务假设。 执行目录迁移时假设如下条件：

- 你的工作负载依赖于使用 Kerberos 或 NTLM 等协议的基于的身份验证。
- 为管理或应用 Active Directory 组策略，工作负载虚拟机需要加入域。

> [!TIP]
> 虽然目录迁移结合云托管的域服务在迁移现有工作负载时提供了极大的灵活性，但在云虚拟网络中托管虚拟机来提供这些服务确实增加了 IT 管理任务的复杂性。 随着云迁移体验日趋成熟，请检查托管这些服务器的长期维护需求。 考虑重构现有工作负载，以与 Azure Active Directory 等云标识提供者兼容是否可以减少对这些云托管服务器的需求。

### <a name="active-directory-federation-services"></a>Active Directory 联合身份验证服务

联合身份验证将在多个标识管理系统之间建立信任关系，可以支持常见身份验证和授权功能。 然后，你可以支持跨组织内或由客户或业务合作伙伴管理的标识系统中多个域的单一登录功能。

Azure AD 使用 [Active Directory 联合身份验证服务](/azure/active-directory/hybrid/how-to-connect-fed-whatis) (AD FS) 支持本地 Active Directory 域的联合身份验证。 请参阅参考架构[将 AD FS 扩展到 Azure](../../../reference-architectures/identity/adfs.md)，了解如何在 Azure 中实现此功能。

## <a name="evolving-identity-integration"></a>不断演变的标识集成

标识集成是一个迭代过程。 你可能需要从一个云原生解决方案开始，该解决方案具有少量用户和初始部署的相应角色。 随着迁移日趋成熟，可以考虑采用联合模型，或者将本地标识服务的完整目录迁移到云中。 在迁移过程的每个迭代中重新访问标识策略。

## <a name="learn-more"></a>了解详细信息

有关 Azure 平台上标识服务的更多信息，请参见下面的内容。

- [Azure AD](https://azure.microsoft.com/services/active-directory)。 Azure AD 提供基于云的标识服务。 它允许你管理对 Azure 资源的访问权限，并控制标识管理、设备注册、用户预配、应用程序访问控制和数据保护。
- [Azure AD Connect](/azure/active-directory/hybrid/whatis-hybrid-identity)。 Azure AD Connect 工具可将 Azure AD 实例与现有标识管理解决方案连接起来，从而允许对云中的现有目录进行同步。
- [基于角色的访问控制](/azure/role-based-access-control/overview) (RBAC)。 Azure AD 提供了 RBAC，可有效且安全地管理对管理平面中的资源访问。 作业和职责按角色进行组织，用户被赋予这些角色。 RBAC 可控制谁有权访问资源，以及用户可以对该资源执行哪些操作。
- [Azure AD Privileged Identity Management](/azure/active-directory/privileged-identity-management/pim-configure) (PIM)。 PIM 降低了资源访问特权的公开时间，并通过报告和警报增加了对其使用的可见性。 它限制用户采用其“实时”(JIT) 权限，或通过分配持续时间较短的权限（之后权限会自动撤销）来实现这一目的。
- [将本地 Active Directory 域与 Azure Active Directory 集成](../../../reference-architectures/identity/azure-ad.md)。 此参考体系结构提供了本地 Active Directory 域和 Azure AD 之间目录同步的示例。
- [将 Active Directory 域服务 (AD DS) 扩展到 Azure。](../../../reference-architectures/identity/adds-extend-domain.md) 此参考体系结构提供了部署 AD DS 服务器以将域服务扩展到基于云的资源的示例。
- [将 Active Directory 联合身份验证服务 (AD FS) 扩展到 Azure](../../../reference-architectures/identity/adfs.md)。 此参考体系结构配置了 Active Directory 联合身份验证服务 (AD FS)，以使用 Azure AD 目录执行联合身份验证和授权。

## <a name="next-steps"></a>后续步骤

了解如何在云中强制执行策略。

> [!div class="nextstepaction"]
> [策略强制执行](../policy-enforcement/overview.md)
