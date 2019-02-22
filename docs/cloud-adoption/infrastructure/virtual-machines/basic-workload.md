---
title: CAF：部署基本工作负荷
description: 介绍如何将基本工作负荷部署到 Azure
author: petertaylor9999
ms.date: 12/31/2018
ms.openlocfilehash: bd3d006100e68f2fa1d71deeb0c72180ad4c19ea
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900641"
---
# <a name="deploy-a-basic-workload-in-azure"></a>在 Azure 中部署基本工作负荷

术语“工作负荷”通常定义为一个任意的功能单元，例如应用程序或服务。 可帮助从部署到服务器的代码项目的角度考虑工作负荷，也可以从应用程序特定的其他服务的角度考虑。 这对于本地应用程序或服务来说可能是有用的定义，但对于云应用程序而言，则需要进行扩展。

在云中，工作负荷不仅涵盖所有项目，而且还包括云资源。 由于存在所谓的“基础结构即代码”概念，因此将云资源作为定义的一部分包括进来。 如 [Azure 工作原理](../../getting-started/what-is-azure.md)中所述，Azure 中的资源由业务流程协调程序服务部署。 这项业务流程协调程序服务通过 Web API 公开，可以使用 PowerShell、Azure 命令行接口 (CLI) 和 Azure 门户等多种工具来调用该 Web API。 这意味着，可以指定计算机可读文件中的资源，并将该文件可与应用程序关联的代码项目一起存储。

这样，我们便可以从代码项目和所需云资源的角度来定义工作负荷，从而进一步隔离工作负荷。 可以根据资源的组织方式、网络拓扑或其他属性隔离工作负荷。 隔离工作负荷的目的是将工作负荷的特定资源关联到某个团队，使该团队能够独立管理这些资源的所有方面。 这样，多个团队便可以在 Azure 中共享资源管理服务，同时防止意外删除或修改对方的资源。

这种隔离还成就了另一个称作 DevOps 的概念。 DevOps 包括软件开发实践（例如上述软件开发和 IT 运营），并会尽量添加更多的自动化功能。 DevOps 的原理之一称为持续集成和持续交付 (CI/CD)。 持续集成是指每当开发人员提交代码更改时运行的自动生成过程。 持续交付是指将此代码部署到各种环境（例如，部署到开发环境进行测试，或部署到生产环境以完成最终部署）的自动化过程。

## <a name="basic-workload"></a>基本工作负荷

根据定义，基本工作负荷通常是指单个 Web 应用程序或包含虚拟机 (VM) 的虚拟网络 (VNet)。

> [!NOTE]
> 本指南不会介绍应用程序开发。 有关在 Azure 上开发应用程序的详细信息，请参阅 [Azure 应用程序体系结构指南](/azure/architecture/guide/)。

不管工作负荷是 Web 应用程序还是 VM，其中的每个部署都需要一个*资源组*。 有权创建资源组的用户必须先执行此操作，然后才能执行以下步骤。

## <a name="basic-web-application-paas"></a>基本 Web 应用程序 (PaaS)

对于基本 Web 应用程序，请从 [Web 应用文档](/azure/app-service?toc=/azure/architecture/cloud-adoption-guide/toc.json)中选择一篇为时 5 分钟的快速入门，并遵循其中的步骤。

> [!NOTE]
> 某些快速入门指南默认会部署资源组。 在这种情况下，不需显式创建资源组。 否则，应将 Web 应用程序部署到前面创建的资源组。

部署简单的工作负荷后，可以详细了解将[基本 Web 应用程序](/azure/architecture/reference-architectures/app-service-web-app/basic-web-app?toc=/azure/architecture/cloud-adoption-guide/toc.json)部署到 Azure 的可靠实践。

## <a name="single-windows-or-linux-vm-iaas"></a>单个 Windows 或 Linux VM (IaaS)

对于 VM 上运行的简单工作负荷，第一步是部署虚拟网络。 Azure 中的所有基础结构即服务 (IaaS) 资源（例如虚拟机、负载均衡器和网关）都需要虚拟网络。 了解 [Azure 虚拟网络](/azure/virtual-network/virtual-networks-overview?toc=/azure/architecture/cloud-adoption-guide/toc.json)，然后遵循相应的步骤，[使用门户将虚拟网络部署到 Azure](/azure/virtual-network/quick-create-portal?toc=/azure/architecture/cloud-adoption-guide/toc.json)。 在 Azure 门户中指定虚拟网络的设置时，请务必指定前面创建的资源组的名称。

下一步是确定是否部署单个 Windows 或 Linux VM。 对于 Windows VM，请遵循相应的步骤，[使用门户将 Windows VM 部署到 Azure](/azure/virtual-machines/windows/quick-create-portal?toc=/azure/architecture/cloud-adoption-guide/toc.json)。 同样，在 Azure 门户中指定虚拟机的设置时，请指定前面创建的资源组的名称。

遵循这些步骤并部署 VM 后，可以了解[有关在 Azure 上运行 Windows VM 的成熟做法](/azure/architecture/reference-architectures/virtual-machines-windows/single-vm?toc=/azure/architecture/cloud-adoption-guide/toc.json)。 对于 Linux VM，请遵循相应的步骤，[使用门户将 Linux VM 部署到 Azure](/azure/virtual-machines/linux/quick-create-portal?toc=/azure/architecture/cloud-adoption-guide/toc.json)。 还可以详细了解[有关在 Azure 上运行 Linux VM 的成熟做法](/azure/architecture/reference-architectures/virtual-machines-linux/single-vm?toc=/azure/architecture/cloud-adoption-guide/toc.json)。

## <a name="next-steps"></a>后续步骤

请参阅[体系结构决策指南](../../decision-guides/overview.md)，了解如何在 Azure 云中使用核心基础结构组件。
