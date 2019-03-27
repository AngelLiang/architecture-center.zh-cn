---
title: 云设计模式
titleSuffix: Azure Architecture Center
description: Microsoft Azure 的云设计模式
keywords: Azure
author: dragon119
ms.date: 12/10/2018
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 4229531366f1b0c3257384694cf4358da9e63177
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58241478"
---
# <a name="cloud-design-patterns"></a>云设计模式

[!INCLUDE [header](../../_includes/header.md)]

这些设计模式可用于在云中构建可靠且可缩放的安全应用程序。

每种模式描述了该模式解决的问题、有关应用该模式的注意事项，以及基于 Microsoft Azure 的示例。 大多数模式都包含了代码示例或代码片段，演示如何在 Azure 中实现该模式。 但是，无论托管在 Azure 还是其他云平台中，大多数模式都与任一分布式系统相关。

## <a name="problem-areas-in-the-cloud"></a>云中的问题区域

<!-- markdownlint-disable MD033 -->

<ul id="categories" class="panel">
{%- for category in categories %}
    <li>
    {% include 'pattern-category-card' %}
    </li>
{%- endfor %}
</ul>

<!-- markdownlint-enable MD033 -->

## <a name="catalog-of-patterns"></a>模式目录

| 模式 | 摘要 |
|---------|---------|
|         |         |

{%- for pattern in patterns %} | [{{ pattern.title }}](./{{ pattern.file }}) | {{ pattern.description }} | {%- endfor %}
