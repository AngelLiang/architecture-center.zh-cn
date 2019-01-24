---
title: 安全模式
titleSuffix: Cloud Design Patterns
description: 安全性是一种防止超出设计使用范围的恶意或意外操作，并防止泄露或丢失信息的系统功能。 云应用程序暴露在受信任的本地边界之外的 Internet 上，通常向公众开放，并可能为不受信任的用户提供服务。 应用程序的设计和部署必须要保护它们免受恶意攻击，仅限获得批准的用户访问，并保护敏感数据。
keywords: 设计模式
author: dragon119
ms.date: 06/23/2017
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: c18255dccdbd8659705884a4141f5ecd099d9ece
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54482079"
---
# <a name="security-patterns"></a>安全模式

[!INCLUDE [header](../../_includes/header.md)]

安全性是一种防止超出设计使用范围的恶意或意外操作，并防止泄露或丢失信息的系统功能。 云应用程序暴露在受信任的本地边界之外的 Internet 上，通常向公众开放，并可能为不受信任的用户提供服务。 应用程序的设计和部署必须要保护它们免受恶意攻击，仅限获得批准的用户访问，并保护敏感数据。

|                    模式                     |                                                                                                         摘要                                                                                                         |
|------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [联合标识](../federated-identity.md) |                                                                                将身份验证委托给外部标识提供者。                                                                                |
|         [守护程序](../gatekeeper.md)         | 通过使用专用的主机实例保护应用程序和服务，该实例用于充当客户端和应用程序或服务之间的中转站、验证和整理请求，并在它们之间传递请求和数据。 |
|          [附属密钥](../valet-key.md)          |                                                        使用令牌或密钥，向客户端授予对特定资源或服务的受限直接访问权限。                                                        |
