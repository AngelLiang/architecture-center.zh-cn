---
title: 关于 Tailspin Surveys 应用程序
description: Tailspin Surveys 应用程序概述。
author: MikeWasson
ms.date: 07/21/2017
ms.topic: guide
ms.service: architecture-center
ms.subservice: reference-architecture
ms.openlocfilehash: de2830b5c492e027c189a79e45ccc6634cab436b
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58247248"
---
# <a name="the-tailspin-scenario"></a><span data-ttu-id="8b532-103">Tailspin 方案</span><span class="sxs-lookup"><span data-stu-id="8b532-103">The Tailspin scenario</span></span>

<span data-ttu-id="8b532-104">[![GitHub](../_images/github.png) 示例代码][sample application]</span><span class="sxs-lookup"><span data-stu-id="8b532-104">[![GitHub](../_images/github.png) Sample code][sample application]</span></span>

<span data-ttu-id="8b532-105">Tailspin 是一家虚构公司，开发名为 Surveys 的 SaaS 应用程序。</span><span class="sxs-lookup"><span data-stu-id="8b532-105">Tailspin is a fictitious company that is developing a SaaS application named Surveys.</span></span> <span data-ttu-id="8b532-106">组织可以使用此应用程序创建并发布在线调查。</span><span class="sxs-lookup"><span data-stu-id="8b532-106">This application enables organizations to create and publish online surveys.</span></span>

* <span data-ttu-id="8b532-107">组织可以注册使用此应用程序。</span><span class="sxs-lookup"><span data-stu-id="8b532-107">An organization can sign up for the application.</span></span>
* <span data-ttu-id="8b532-108">组织注册后，用户可以使用组织的凭据登录到此应用程序。</span><span class="sxs-lookup"><span data-stu-id="8b532-108">After the organization is signed up, users can sign into the application with their organizational credentials.</span></span>
* <span data-ttu-id="8b532-109">用户可以创建、编辑和发布调查。</span><span class="sxs-lookup"><span data-stu-id="8b532-109">Users can create, edit, and publish surveys.</span></span>

> [!NOTE]
> <span data-ttu-id="8b532-110">要开始使用此应用程序，请参阅[运行 Surveys 应用程序]。</span><span class="sxs-lookup"><span data-stu-id="8b532-110">To get started with the application, see [Run the Surveys application].</span></span>

## <a name="users-can-create-edit-and-view-surveys"></a><span data-ttu-id="8b532-111">用户可以创建、编辑和查看调查</span><span class="sxs-lookup"><span data-stu-id="8b532-111">Users can create, edit, and view surveys</span></span>

<span data-ttu-id="8b532-112">经过身份验证的用户可以查看自己创建的或拥有参与者权限的所有调查，还可以创建新调查。</span><span class="sxs-lookup"><span data-stu-id="8b532-112">An authenticated user can view all the surveys that he or she has created or has contributor rights to, and create new surveys.</span></span> <span data-ttu-id="8b532-113">请注意，用户使用其组织身份 `bob@contoso.com` 登录。</span><span class="sxs-lookup"><span data-stu-id="8b532-113">Notice that the user is signed in with his organizational identity, `bob@contoso.com`.</span></span>

![Surveys 应用](./images/surveys-screenshot.png)

<span data-ttu-id="8b532-115">此屏幕截图显示“编辑调查”页面：</span><span class="sxs-lookup"><span data-stu-id="8b532-115">This screenshot shows the Edit Survey page:</span></span>

![编辑调查](./images/edit-survey.png)

<span data-ttu-id="8b532-117">用户还可以查看同一租户中其他用户创建的任何调查。</span><span class="sxs-lookup"><span data-stu-id="8b532-117">Users can also view any surveys created by other users within the same tenant.</span></span>

![租户调查](./images/tenant-surveys.png)

## <a name="survey-owners-can-invite-contributors"></a><span data-ttu-id="8b532-119">调查所有者可以邀请参与者</span><span class="sxs-lookup"><span data-stu-id="8b532-119">Survey owners can invite contributors</span></span>

<span data-ttu-id="8b532-120">用户创建调查时，他/她可以邀请其他人成为调查的参与者。</span><span class="sxs-lookup"><span data-stu-id="8b532-120">When a user creates a survey, he or she can invite other people to be contributors on the survey.</span></span> <span data-ttu-id="8b532-121">参与者可以编辑调查，但无法删除或发布调查。</span><span class="sxs-lookup"><span data-stu-id="8b532-121">Contributors can edit the survey, but cannot delete or publish it.</span></span>

![添加参与者](./images/add-contributor.png)

<span data-ttu-id="8b532-123">用户可以从其他租户添加参与者，从而实现跨租户资源共享。</span><span class="sxs-lookup"><span data-stu-id="8b532-123">A user can add contributors from other tenants, which enables cross-tenant sharing of resources.</span></span> <span data-ttu-id="8b532-124">在此屏幕截图中，Bob (`bob@contoso.com`) 将 Alice (`alice@fabrikam.com`) 添加为他创建的调查的参与者。</span><span class="sxs-lookup"><span data-stu-id="8b532-124">In this screenshot, Bob (`bob@contoso.com`) is adding Alice (`alice@fabrikam.com`) as a contributor to a survey that Bob created.</span></span>

<span data-ttu-id="8b532-125">Alice 登录时，会看到“我可以参与的调查”下列出了该调查。</span><span class="sxs-lookup"><span data-stu-id="8b532-125">When Alice logs in, she sees the survey listed under "Surveys I can contribute to".</span></span>

![调查参与者](./images/contributor.png)

<span data-ttu-id="8b532-127">请注意，Alice 是登录到她自己的租户，而不是作为 Contoso 租户的来宾登录。</span><span class="sxs-lookup"><span data-stu-id="8b532-127">Note that Alice signs into her own tenant, not as a guest of the Contoso tenant.</span></span> <span data-ttu-id="8b532-128">Alice 仅拥有对该调查的参与者权限 &mdash; 而无法查看 Contoso 租户中的其他调查。</span><span class="sxs-lookup"><span data-stu-id="8b532-128">Alice has contributor permissions only for that survey &mdash; she cannot view other surveys from the Contoso tenant.</span></span>

## <a name="architecture"></a><span data-ttu-id="8b532-129">体系结构</span><span class="sxs-lookup"><span data-stu-id="8b532-129">Architecture</span></span>

<span data-ttu-id="8b532-130">Surveys 应用程序由 Web 前端和 Web API 后端组成。</span><span class="sxs-lookup"><span data-stu-id="8b532-130">The Surveys application consists of a web front end and a web API backend.</span></span> <span data-ttu-id="8b532-131">两者都通过使用 [ASP.NET Core] 实现。</span><span class="sxs-lookup"><span data-stu-id="8b532-131">Both are implemented using [ASP.NET Core].</span></span>

<span data-ttu-id="8b532-132">Web 应用程序使用 Azure Active Directory (Azure AD) 对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="8b532-132">The web application uses Azure Active Directory (Azure AD) to authenticate users.</span></span> <span data-ttu-id="8b532-133">Web 应用程序还调用 Azure AD 来获取 Web API 的 OAuth 2 访问令牌。</span><span class="sxs-lookup"><span data-stu-id="8b532-133">The web application also calls Azure AD to get OAuth 2 access tokens for the Web API.</span></span> <span data-ttu-id="8b532-134">访问令牌在 Azure Redis 缓存中进行缓存。</span><span class="sxs-lookup"><span data-stu-id="8b532-134">Access tokens are cached in Azure Redis Cache.</span></span> <span data-ttu-id="8b532-135">通过缓存，多个实例可以共享同一令牌缓存（例如，在服务器场中）。</span><span class="sxs-lookup"><span data-stu-id="8b532-135">The cache enables multiple instances to share the same token cache (e.g., in a server farm).</span></span>

![体系结构](./images/architecture.png)

<span data-ttu-id="8b532-137">[**下一篇**][authentication]</span><span class="sxs-lookup"><span data-stu-id="8b532-137">[**Next**][authentication]</span></span>

<!-- links -->

[authentication]: authenticate.md

[运行 Surveys 应用程序]: ./run-the-app.md
[Run the Surveys application]: ./run-the-app.md
[ASP.NET Core]: /aspnet/core
[sample application]: https://github.com/mspnp/multitenant-saas-guidance
