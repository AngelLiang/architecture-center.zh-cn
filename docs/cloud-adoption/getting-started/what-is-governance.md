---
title: CAF：什么是云资源调控？
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.date: 02/11/2019
description: 有关 Azure 上的云资源治理的说明
author: petertaylor9999
ms.openlocfilehash: 602ac81f2b2201c77746df971d282582ceee23f7
ms.sourcegitcommit: 9854bd27fb5cf92041bbfb743d43045cd3552a69
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58503190"
---
<!-- markdownlint-disable MD026 -->

# <a name="what-is-cloud-resource-governance"></a>什么是云资源调控？

在 [Azure 的工作原理](what-is-azure.md)中我们已了解到，Azure 是代表用户运行虚拟化硬件和软件的服务器与网络硬件的集合。 Azure 可让组织根据需要轻松创建、读取、更新和删除资源，使开发工作和 IT 部门变得更加轻盈。

但是，为开发人员授予不受限制的资源访问权限可能会使他们变得随心所欲，此外还可能导致意外的成本。 例如，某个开发团队获批部署一组用于测试的资源，但完成测试后忘记删除这些资源。 这些资源会不断加大成本，即使其用途不再受批准，或者不再有需要。

解决此问题的方法就是采用资源访问**调控**。 调控是指根据组织的目标和要求，对 Azure 资源的使用持续进行管理、监视和审核的过程。

<!-- markdownlint-disable MD034 -->

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2ii94]

<!-- markdownlint-enable MD034 -->

每家组织的目标和要求都是独特的，因此，不存在以一应百的调控方法。 Azure 实施两个主要的管理工具：**基于角色的访问控制 (RBAC)** 和**资源策略**，每家组织可以使用这些工具设计自己的调控模型。

RBAC 定义角色，而角色定义获得该角色的用户的功能。 例如，**所有者**角色可以资源的所有功能（创建、读取、更新和删除），**读取者**角色只能启用读取功能。 可以使用应用到许多资源类型的宽泛范围定义角色，也可以使用应用到少量资源类型的狭窄范围定义角色。

资源策略定义有关创建资源的规则。 例如，资源策略可将 VM 的 SKU 限制为特定的预批准大小。 或者，在发出创建资源的请求时，资源策略可以强制添加包含成本中心的标记。

配置这些工具时，一个重要的考虑因素是在调控与组织灵活性之间实现平衡。 也就是说，调控策略的限制性越强，开发人员和 IT 工作人员的灵活性就越低。 这是因为严格的管理策略可能要求更多的手动步骤，例如，要求填写窗体或将一封电子邮件发送给管理团队的人来手动创建资源的开发人员。 管理团队具有有限的功能，并可能会积压，从而导致等待要创建和不需要的资源产生成本，而它们等待要删除其资源用于生产的开发团队。

## <a name="next-steps"></a>后续步骤

现在，你已了解云资源调控的概念，了解有关如何在 Azure 中管理资源访问权限的详细信息。

> [!div class="nextstepaction"]
> [了解 Azure 中的资源访问](azure-resource-access.md)
