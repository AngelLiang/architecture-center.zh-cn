---
title: 扩展 Azure 资源管理器模板功能
description: 介绍有关如何扩展 Azure 资源管理器模板功能的提示和技巧。
author: petertay
ms.date: 06/09/2017
ms.topic: article
ms.service: architecture-center
ms.subservice: reference-architecture
ms.openlocfilehash: 108d82066d9867682c246c4de802849e2e561cbc
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58343826"
---
# <a name="extend-azure-resource-manager-template-functionality"></a><span data-ttu-id="9c694-103">扩展 Azure 资源管理器模板功能</span><span class="sxs-lookup"><span data-stu-id="9c694-103">Extend Azure Resource Manager template functionality</span></span>

<span data-ttu-id="9c694-104">2016 年，Microsoft 模式和实践团队创建了一组 Azure 资源管理器[模板构建基块](https://github.com/mspnp/template-building-blocks/wiki)，旨在简化资源部署。</span><span class="sxs-lookup"><span data-stu-id="9c694-104">In 2016, the Microsoft patterns & practices team created a set of Azure Resource Manager [template building blocks](https://github.com/mspnp/template-building-blocks/wiki) with the goal of simplifying resource deployment.</span></span> <span data-ttu-id="9c694-105">每个构建基块包含一组预建模板，用于部署不同参数文件指定的资源集。</span><span class="sxs-lookup"><span data-stu-id="9c694-105">Each building block contains a set of pre-built templates that deploy sets of resources specified by separate parameter files.</span></span>

<span data-ttu-id="9c694-106">构建基块模板需组合在一起，以创建更大、更复杂的部署。</span><span class="sxs-lookup"><span data-stu-id="9c694-106">The building block templates are designed to be combined together to create larger and more complex deployments.</span></span> <span data-ttu-id="9c694-107">例如，在 Azure 中部署虚拟机需要虚拟网络、存储帐户和其他资源。</span><span class="sxs-lookup"><span data-stu-id="9c694-107">For example, deploying a virtual machine in Azure requires a virtual network, storage accounts, and other resources.</span></span> <span data-ttu-id="9c694-108">[虚拟网络构建基块模板](https://github.com/mspnp/template-building-blocks/wiki/VNet-(v1))部署虚拟网络和子网。</span><span class="sxs-lookup"><span data-stu-id="9c694-108">The [virtual network building block template](https://github.com/mspnp/template-building-blocks/wiki/VNet-(v1)) deploys a virtual network and subnets.</span></span> <span data-ttu-id="9c694-109">[虚拟机构建基块模板](https://github.com/mspnp/template-building-blocks/wiki/Windows-and-Linux-VMs-(v1))部署存储帐户、网络接口和实际的 VM。</span><span class="sxs-lookup"><span data-stu-id="9c694-109">The [virtual machine building block template](https://github.com/mspnp/template-building-blocks/wiki/Windows-and-Linux-VMs-(v1)) deploys storage accounts, network interfaces, and the actual VMs.</span></span> <span data-ttu-id="9c694-110">然后，可以创建脚本或模板并结合相应的参数文件来调用这两个构建基块模板，通过一个操作部署完整的体系结构。</span><span class="sxs-lookup"><span data-stu-id="9c694-110">You can then create a script or template to call both building block templates with their corresponding parameter files to deploy a complete architecture with one operation.</span></span>

<span data-ttu-id="9c694-111">开发构建基块模板时，模式和实践团队 (p&p) 设计了几个概念用于扩展 Azure 资源管理器模板功能。</span><span class="sxs-lookup"><span data-stu-id="9c694-111">While developing the building block templates, p&p designed several concepts to extend Azure Resource Manager template functionality.</span></span> <span data-ttu-id="9c694-112">本系列文章将会介绍这些概念，使大家可以在自己的模板中使用。</span><span class="sxs-lookup"><span data-stu-id="9c694-112">In this series, we will describe several of these concepts so you can use them in your own templates.</span></span>

> [!NOTE]
> <span data-ttu-id="9c694-113">这些文章假设读者对 Azure 资源管理器模板有深度的了解。</span><span class="sxs-lookup"><span data-stu-id="9c694-113">These articles assume you have an advanced understanding of Azure Resource Manager templates.</span></span>