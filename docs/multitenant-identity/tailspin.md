---
title: 关于 Tailspin Surveys 应用程序
description: Tailspin Surveys 应用程序概述
author: MikeWasson
ms.date: 07/21/2017
pnp.series.title: Manage Identity in Multitenant Applications
pnp.series.prev: index
pnp.series.next: authenticate
ms.openlocfilehash: a1c357bd1b5306d1255c66aaea96d86be55e7b77
ms.sourcegitcommit: e7e0e0282fa93f0063da3b57128ade395a9c1ef9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2018
ms.locfileid: "52902062"
---
# <a name="the-tailspin-scenario"></a><span data-ttu-id="1578e-103">Tailspin 方案</span><span class="sxs-lookup"><span data-stu-id="1578e-103">The Tailspin scenario</span></span>

<span data-ttu-id="1578e-104">[![GitHub](../_images/github.png) 示例代码][sample application]</span><span class="sxs-lookup"><span data-stu-id="1578e-104">[![GitHub](../_images/github.png) Sample code][sample application]</span></span>

<span data-ttu-id="1578e-105">Tailspin 是一家虚构公司，开发名为 Surveys 的 SaaS 应用程序。</span><span class="sxs-lookup"><span data-stu-id="1578e-105">Tailspin is a fictitious company that is developing a SaaS application named Surveys.</span></span> <span data-ttu-id="1578e-106">组织可以使用此应用程序创建并发布在线调查。</span><span class="sxs-lookup"><span data-stu-id="1578e-106">This application enables organizations to create and publish online surveys.</span></span>

* <span data-ttu-id="1578e-107">组织可以注册使用此应用程序。</span><span class="sxs-lookup"><span data-stu-id="1578e-107">An organization can sign up for the application.</span></span>
* <span data-ttu-id="1578e-108">组织注册后，用户可以使用组织的凭据登录到此应用程序。</span><span class="sxs-lookup"><span data-stu-id="1578e-108">After the organization is signed up, users can sign into the application with their organizational credentials.</span></span>
* <span data-ttu-id="1578e-109">用户可以创建、编辑和发布调查。</span><span class="sxs-lookup"><span data-stu-id="1578e-109">Users can create, edit, and publish surveys.</span></span>

> [!NOTE]
> <span data-ttu-id="1578e-110">要开始使用此应用程序，请参阅[运行 Surveys 应用程序]。</span><span class="sxs-lookup"><span data-stu-id="1578e-110">To get started with the application, see [Run the Surveys application].</span></span>
> 
> 

## <a name="users-can-create-edit-and-view-surveys"></a><span data-ttu-id="1578e-111">用户可以创建、编辑和查看调查</span><span class="sxs-lookup"><span data-stu-id="1578e-111">Users can create, edit, and view surveys</span></span>
<span data-ttu-id="1578e-112">经过身份验证的用户可以查看自己创建的或拥有参与者权限的所有调查，还可以创建新调查。</span><span class="sxs-lookup"><span data-stu-id="1578e-112">An authenticated user can view all the surveys that he or she has created or has contributor rights to, and create new surveys.</span></span> <span data-ttu-id="1578e-113">请注意，用户使用其组织身份 `bob@contoso.com` 登录。</span><span class="sxs-lookup"><span data-stu-id="1578e-113">Notice that the user is signed in with his organizational identity, `bob@contoso.com`.</span></span>

![Surveys 应用](./images/surveys-screenshot.png)

<span data-ttu-id="1578e-115">此屏幕截图显示“编辑调查”页面：</span><span class="sxs-lookup"><span data-stu-id="1578e-115">This screenshot shows the Edit Survey page:</span></span>

![编辑调查](./images/edit-survey.png)

<span data-ttu-id="1578e-117">用户还可以查看同一租户中其他用户创建的任何调查。</span><span class="sxs-lookup"><span data-stu-id="1578e-117">Users can also view any surveys created by other users within the same tenant.</span></span>

![租户调查](./images/tenant-surveys.png)

## <a name="survey-owners-can-invite-contributors"></a><span data-ttu-id="1578e-119">调查所有者可以邀请参与者</span><span class="sxs-lookup"><span data-stu-id="1578e-119">Survey owners can invite contributors</span></span>
<span data-ttu-id="1578e-120">用户创建调查时，他/她可以邀请其他人成为调查的参与者。</span><span class="sxs-lookup"><span data-stu-id="1578e-120">When a user creates a survey, he or she can invite other people to be contributors on the survey.</span></span> <span data-ttu-id="1578e-121">参与者可以编辑调查，但无法删除或发布调查。</span><span class="sxs-lookup"><span data-stu-id="1578e-121">Contributors can edit the survey, but cannot delete or publish it.</span></span>  

![添加参与者](./images/add-contributor.png)

<span data-ttu-id="1578e-123">用户可以从其他租户添加参与者，从而实现跨租户资源共享。</span><span class="sxs-lookup"><span data-stu-id="1578e-123">A user can add contributors from other tenants, which enables cross-tenant sharing of resources.</span></span> <span data-ttu-id="1578e-124">在此屏幕截图中，Bob (`bob@contoso.com`) 将 Alice (`alice@fabrikam.com`) 添加为他创建的调查的参与者。</span><span class="sxs-lookup"><span data-stu-id="1578e-124">In this screenshot, Bob (`bob@contoso.com`) is adding Alice (`alice@fabrikam.com`) as a contributor to a survey that Bob created.</span></span>

<span data-ttu-id="1578e-125">Alice 登录时，会看到“我可以参与的调查”下列出了该调查。</span><span class="sxs-lookup"><span data-stu-id="1578e-125">When Alice logs in, she sees the survey listed under "Surveys I can contribute to".</span></span>

![调查参与者](./images/contributor.png)

<span data-ttu-id="1578e-127">请注意，Alice 是登录到她自己的租户，而不是作为 Contoso 租户的来宾登录。</span><span class="sxs-lookup"><span data-stu-id="1578e-127">Note that Alice signs into her own tenant, not as a guest of the Contoso tenant.</span></span> <span data-ttu-id="1578e-128">Alice 仅拥有对该调查的参与者权限 &mdash; 而无法查看 Contoso 租户中的其他调查。</span><span class="sxs-lookup"><span data-stu-id="1578e-128">Alice has contributor permissions only for that survey &mdash; she cannot view other surveys from the Contoso tenant.</span></span>

## <a name="architecture"></a><span data-ttu-id="1578e-129">体系结构</span><span class="sxs-lookup"><span data-stu-id="1578e-129">Architecture</span></span>
<span data-ttu-id="1578e-130">Surveys 应用程序由 Web 前端和 Web API 后端组成。</span><span class="sxs-lookup"><span data-stu-id="1578e-130">The Surveys application consists of a web front end and a web API backend.</span></span> <span data-ttu-id="1578e-131">两者都通过使用 [ASP.NET Core] 实现。</span><span class="sxs-lookup"><span data-stu-id="1578e-131">Both are implemented using [ASP.NET Core].</span></span>

<span data-ttu-id="1578e-132">Web 应用程序使用 Azure Active Directory (Azure AD) 对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="1578e-132">The web application uses Azure Active Directory (Azure AD) to authenticate users.</span></span> <span data-ttu-id="1578e-133">Web 应用程序还调用 Azure AD 来获取 Web API 的 OAuth 2 访问令牌。</span><span class="sxs-lookup"><span data-stu-id="1578e-133">The web application also calls Azure AD to get OAuth 2 access tokens for the Web API.</span></span> <span data-ttu-id="1578e-134">访问令牌在 Azure Redis 缓存中进行缓存。</span><span class="sxs-lookup"><span data-stu-id="1578e-134">Access tokens are cached in Azure Redis Cache.</span></span> <span data-ttu-id="1578e-135">通过缓存，多个实例可以共享同一令牌缓存（例如，在服务器场中）。</span><span class="sxs-lookup"><span data-stu-id="1578e-135">The cache enables multiple instances to share the same token cache (e.g., in a server farm).</span></span>

![体系结构](./images/architecture.png)

<span data-ttu-id="1578e-137">[**下一篇**][authentication]</span><span class="sxs-lookup"><span data-stu-id="1578e-137">[**Next**][authentication]</span></span>

<!-- Links -->

[authentication]: authenticate.md

[运行 Surveys 应用程序]: ./run-the-app.md
[Run the Surveys application]: ./run-the-app.md
[ASP.NET Core]: /aspnet/core
[sample application]: https://github.com/mspnp/multitenant-saas-guidance
