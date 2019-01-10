---
title: 安全模式
titleSuffix: Cloud Design Patterns
description: 安全性是一种防止超出设计使用范围的恶意或意外操作，并防止泄露或丢失信息的系统功能。 云应用程序暴露在受信任的本地边界之外的 Internet 上，通常向公众开放，并可能为不受信任的用户提供服务。 应用程序的设计和部署必须要保护它们免受恶意攻击，仅限获得批准的用户访问，并保护敏感数据。
keywords: 设计模式
author: dragon119
ms.date: 06/23/2017
ms.custom: seodec18
ms.openlocfilehash: 259d4bd5178ca8ab85df0cd03e5268c5a7845a3a
ms.sourcegitcommit: 680c9cef945dff6fee5e66b38e24f07804510fa9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2019
ms.locfileid: "54009434"
---
# <a name="security-patterns"></a><span data-ttu-id="48eaa-106">安全模式</span><span class="sxs-lookup"><span data-stu-id="48eaa-106">Security patterns</span></span>

[!INCLUDE [header](../../_includes/header.md)]

<span data-ttu-id="48eaa-107">安全性是一种防止超出设计使用范围的恶意或意外操作，并防止泄露或丢失信息的系统功能。</span><span class="sxs-lookup"><span data-stu-id="48eaa-107">Security is the capability of a system to prevent malicious or accidental actions outside of the designed usage, and to prevent disclosure or loss of information.</span></span> <span data-ttu-id="48eaa-108">云应用程序暴露在受信任的本地边界之外的 Internet 上，通常向公众开放，并可能为不受信任的用户提供服务。</span><span class="sxs-lookup"><span data-stu-id="48eaa-108">Cloud applications are exposed on the Internet outside trusted on-premises boundaries, are often open to the public, and may serve untrusted users.</span></span> <span data-ttu-id="48eaa-109">应用程序的设计和部署必须要保护它们免受恶意攻击，仅限获得批准的用户访问，并保护敏感数据。</span><span class="sxs-lookup"><span data-stu-id="48eaa-109">Applications must be designed and deployed in a way that protects them from malicious attacks, restricts access to only approved users, and protects sensitive data.</span></span>

|                    <span data-ttu-id="48eaa-110">模式</span><span class="sxs-lookup"><span data-stu-id="48eaa-110">Pattern</span></span>                     |                                                                                                         <span data-ttu-id="48eaa-111">摘要</span><span class="sxs-lookup"><span data-stu-id="48eaa-111">Summary</span></span>                                                                                                         |
|------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [<span data-ttu-id="48eaa-112">联合标识</span><span class="sxs-lookup"><span data-stu-id="48eaa-112">Federated Identity</span></span>](../federated-identity.md) |                                                                                <span data-ttu-id="48eaa-113">将身份验证委托给外部标识提供者。</span><span class="sxs-lookup"><span data-stu-id="48eaa-113">Delegate authentication to an external identity provider.</span></span>                                                                                |
|         [<span data-ttu-id="48eaa-114">守护程序</span><span class="sxs-lookup"><span data-stu-id="48eaa-114">Gatekeeper</span></span>](../gatekeeper.md)         | <span data-ttu-id="48eaa-115">通过使用专用的主机实例保护应用程序和服务，该实例用于充当客户端和应用程序或服务之间的中转站、验证和整理请求，并在它们之间传递请求和数据。</span><span class="sxs-lookup"><span data-stu-id="48eaa-115">Protect applications and services by using a dedicated host instance that acts as a broker between clients and the application or service, validates and sanitizes requests, and passes requests and data between them.</span></span> |
|          [<span data-ttu-id="48eaa-116">附属密钥</span><span class="sxs-lookup"><span data-stu-id="48eaa-116">Valet Key</span></span>](../valet-key.md)          |                                                        <span data-ttu-id="48eaa-117">使用令牌或密钥，向客户端授予对特定资源或服务的受限直接访问权限。</span><span class="sxs-lookup"><span data-stu-id="48eaa-117">Use a token or key that provides clients with restricted direct access to a specific resource or service.</span></span>                                                        |
