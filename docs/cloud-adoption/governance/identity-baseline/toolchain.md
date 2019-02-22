---
title: CAF：Azure 中的标识基线工具
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: Azure 中的标识基线工具
author: BrianBlanchard
ms.openlocfilehash: 81b0fa9cfee597da98d8b983fb155eac82d97bf8
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900606"
---
# <a name="identity-baseline-tools-in-azure"></a>Azure 中的标识基线工具

[基线](overview.md)是[云治理的五项规则](../governance-disciplines.md)之一。 此规则侧重于建立策略的方法，以确保用户标识的一致性和连续性，而不考虑托管应用程序或工作负荷的云提供程序。

有关混合标识的发现指南中包括以下工具。

**Active Directory（本地）：** Active Directory 是在企业中最常用于存储和验证用户凭据的标识提供者。

**Azure Active Directory：** 等效于 Active Directory 的软件即服务 (SaaS)，能够与本地 Active Directory 联合。

**Active Directory (IaaS)：** 在 Azure 中的虚拟机内运行的 Active Directory 应用程序实例。

标识是用于 IT 安全性的控制平面。 因此，身份验证是组织对云的访问防护。 组织需要一个标识控制平面，以便增强其安全性并使其云应用免受入侵者攻击。

## <a name="cloud-authentication"></a>云身份验证

对于希望将应用移动到云的组织来说，应首先考虑选择正确的身份验证方式。

选择此方法时，Azure AD 会处理用户的登录过程。 结合使用无缝单一登录 (SSO)，用户可以登录到云应用，无需重新输入其凭据。 如果使用的是云身份验证，可以从以下两个选项中选择：

**Azure AD 密码哈希同步**：这是在 Azure AD 中为本地目录对象启用身份验证的最简单方法。 此方法还可以与任何在本地服务器出现故障的情况下用作备份故障转移身份验证方法的方法结合使用。

**Azure AD 直通身份验证**：通过使用在一个或多个本地服务器上运行的软件代理，为 Azure AD 身份验证服务提供持久密码验证。

> [!NOTE]
> 具有即时强制本地用户帐户状态、密码策略和登录小时数生效的安全要求的公司应考虑直通身份验证方法。

**联合身份验证：**

选择此方法时，Azure AD 将身份验证过程传给单独的受信任身份验证系统（例如本地 Active Directory 联合身份验证服务 (AD FS) 或受信任的第三方联合身份验证提供程序）来验证用户的密码。

[为 Azure Active Directory 选择正确的身份验证方法](/azure/security/azure-ad-choose-authn)一文包含决策树，以帮助为组织选择最佳解决方案。

下表列出了可帮助完善支持此治理规则的策略和流程的本机工具。

<!-- markdownlint-disable MD033 -->

|注意事项|密码哈希同步 + 无缝 SSO|直通身份验证 + 无缝 SSO|使用 AD FS 进行联合身份验证|
|:-----|:-----|:-----|:-----|
|身份验证在何处发生？|在云中|在云中，在使用本地身份验证代理进行安全密码验证交换后|本地|
|除预配系统外的本地服务器要求是什么：Azure AD Connect？|无|为额外的每个身份验证代理提供一台服务器|两台或更多 AD FS 服务器<br><br>外围网络中的两台或更多 WAP 服务器|
|除预配系统外的本地 Internet 和网络的要求是什么？|无|从运行身份验证代理的服务器进行[出站 Internet 访问](/azure/active-directory/hybrid/how-to-connect-pta-quick-start)|对外围网络中的 WAP 服务器进行[入站 Internet 访问](/windows-server/identity/ad-fs/overview/ad-fs-requirements)<br><br>从外围网络中的 WAP 服务器对 AD FS 服务器进行入站网络访问<br><br>网络负载均衡|
|是否有 SSL 证书要求？|否|否|是|
|是否有运行状况监视解决方案？|不是必需|[Azure Active Directory 管理中心](/azure/active-directory/hybrid/tshoot-connect-pass-through-authentication)提供的代理状态|[Azure AD Connect Health](/azure/active-directory/hybrid/how-to-connect-health-adfs)|
|用户是否可在公司网络内从加入域的设备单一登录到云资源？|是，同时使用[无缝 SSO](/azure/active-directory/hybrid/how-to-connect-sso)|是，同时使用[无缝 SSO](/azure/active-directory/hybrid/how-to-connect-sso)|是|
|支持哪些登录类型？|UserPrincipalName + 密码<br><br>通过使用[无缝 SSO](/azure/active-directory/hybrid/how-to-connect-sso) 进行的 Windows 集成身份验证<br><br>[备用登录 ID](/azure/active-directory/hybrid/how-to-connect-install-custom)|UserPrincipalName + 密码<br><br>通过使用[无缝 SSO](/azure/active-directory/hybrid/how-to-connect-sso) 进行的 Windows 集成身份验证<br><br>[备用登录 ID](/azure/active-directory/hybrid/how-to-connect-pta-faq)|UserPrincipalName + 密码<br><br>sAMAccountName + 密码<br><br>Windows 集成身份验证<br><br>[证书和智能卡身份验证](/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br><br>[备用登录 ID](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|是否支持 Windows Hello 企业版？|[密钥信任模型](/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[使用 Intune 的证书信任模型](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[密钥信任模型](/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[使用 Intune 的证书信任模型](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[密钥信任模型](/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[证书信任模型](/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|有哪些多重身份验证选项？|[Azure MFA](/azure/multi-factor-authentication/)<br><br>[带条件访问的自定义控件*](/azure/active-directory/conditional-access/controls#custom-controls-1)|[Azure MFA](/azure/multi-factor-authentication/)<br><br>[带条件访问的自定义控件*](/azure/active-directory/conditional-access/controls#custom-controls-1)|[Azure MFA](/azure/multi-factor-authentication/)<br><br>[Azure MFA 服务器](/azure/active-directory/authentication/howto-mfaserver-deploy)<br><br>[第三方 MFA](/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)<br><br>[带条件访问的自定义控件*](/azure/active-directory/conditional-access/controls#custom-controls-1)|
|支持哪些用户帐户状态？|已禁用帐户<br>（最多 30 分钟延迟）|已禁用帐户<br><br>帐户已锁定<br><br>帐户已过期<br><br>密码已过期<br><br>登录小时数|已禁用帐户<br><br>帐户已锁定<br><br>帐户已过期<br><br>密码已过期<br><br>登录小时数|
|有哪些条件访问选项？|[Azure AD 条件访问](/azure/active-directory/active-directory-conditional-access-azure-portal)|[Azure AD 条件访问](/azure/active-directory/active-directory-conditional-access-azure-portal)|[Azure AD 条件访问](/azure/active-directory/active-directory-conditional-access-azure-portal)<br><br>[AD FS 声明规则](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|是否支持阻止旧协议？|[是](/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication)|[是](/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication)|[是](/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|是否可以自定义登录页面的徽标、图像和说明？|[是，可使用 Azure AD Premium](/azure/active-directory/customize-branding)|[是，可使用 Azure AD Premium](/azure/active-directory/customize-branding)|[是](/azure/active-directory/connect/active-directory-aadconnect-federation-management#customlogo)|
|支持哪些高级方案？|[智能密码锁定](/azure/active-directory/active-directory-secure-passwords)<br><br>[已泄漏凭据报告](/azure/active-directory/active-directory-reporting-risk-events)|[智能密码锁定](/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout)|多站点低延迟身份验证系统<br><br>[AD FS Extranet 锁定](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)<br><br>[与第三方标识系统集成](/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility)|

<!-- markdownlint-enable MD033 -->

> [!NOTE]
> Azure AD 条件访问中的自定义控件当前不支持设备注册。

## <a name="next-steps"></a>后续步骤

[混合标识数字化转型框架](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?LCID=EN-US)概述了用于选择和集成每个组件的一些组合和解决方案。

[Azure AD Connect 工具](https://aka.ms/aadconnectwiz)可帮助将本地目录与 Azure AD 集成。
