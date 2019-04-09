---
title: CAF：什么是云资源调控？
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.date: 02/11/2019
description: 有关 Azure 上的云资源治理的说明
author: petertaylor9999
ms.openlocfilehash: 0989a5aad8a6290cce07fd71771c690bbd615e0d
ms.sourcegitcommit: 0a8a60d782facc294f7f78ec0e9033e3ee16bf4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59068848"
---
<!-- markdownlint-disable MD026 -->

# <a name="what-is-cloud-resource-governance"></a>什么是云资源调控？

在中[Azure 如何工作？](what-is-azure.md)，你了解 Azure 是一系列服务器和网络硬件代表用户运行虚拟化的硬件和软件。 Azure 使你组织的应用程序开发和 IT 部门可通过轻松地创建、 读取、 更新和删除资源，根据需要具备敏捷性。

但是，虽然不受限制的访问资源可以使开发人员非常灵活，则可以还会导致意外的成本。 例如，某个开发团队获批部署一组用于测试的资源，但完成测试后忘记删除这些资源。 这些资源仍将产生成本，即使它们不再是已批准也没有必要。

解决方法是资源访问管理。 **监管**是持续的过程的管理、 监视和审核使用 Azure 资源以满足你的组织的要求。

<!-- markdownlint-disable MD034 -->

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2ii94]

<!-- markdownlint-enable MD034 -->

这些要求是唯一的每个组织，因此监管的通用方法不是很有帮助。 相反，这取决于每个组织设计其使用 Azure 的两个主要管理工具的监管模型：**基于角色的访问控制 (RBAC)** 并**资源策略**。

RBAC 定义角色，角色和角色定义的每个用户分配该角色的功能。 例如，**所有者**角色允许所有功能 （创建、 读取、 更新和删除） 的资源，而**读取器**角色都允许仅读取的功能。 可以使用适用于很多的资源类型的广泛范围或适用于少量的狭窄作用域定义角色。

资源策略定义有关创建资源的规则。 例如，资源策略可以限制为特定的预先批准大小的虚拟机的 SKU。 发出请求来创建资源时，另一个资源策略可以强制实施分配的成本中心的标记的应用程序。

在配置这些工具时，务必管理和组织敏捷性之间实现平衡。 限制性更强您管理的策略，不太敏捷开发人员和 IT 工作人员将为。 严格的管理策略可能要求更多的手动步骤，如要求填写表单或电子邮件发送给管理团队的成员，才能手动创建资源的开发人员。 管理团队具有有限的容量，可能会成为瓶颈，从而导致无效率地等待其资源会产生成本，在删除之前创建的或不需要的资源的开发团队。

## <a name="next-steps"></a>后续步骤

现在，你已了解云资源调控的概念，了解有关如何在 Azure 中管理资源访问权限的详细信息。

> [!div class="nextstepaction"]
> [了解如何在 Azure 中的资源访问权限](azure-resource-access.md)
