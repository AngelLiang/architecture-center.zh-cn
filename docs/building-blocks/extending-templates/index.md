---
title: 扩展 Azure 资源管理器模板功能
description: 介绍有关如何扩展 Azure 资源管理器模板功能的提示和技巧。
author: petertay
ms.date: 06/09/2017
ms.topic: article
ms.service: architecture-center
ms.subservice: reference-architecture
ms.openlocfilehash: 108d82066d9867682c246c4de802849e2e561cbc
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54486431"
---
# <a name="extend-azure-resource-manager-template-functionality"></a>扩展 Azure 资源管理器模板功能

2016 年，Microsoft 模式和实践团队创建了一组 Azure 资源管理器[模板构建基块](https://github.com/mspnp/template-building-blocks/wiki)，旨在简化资源部署。 每个构建基块包含一组预建模板，用于部署不同参数文件指定的资源集。

构建基块模板需组合在一起，以创建更大、更复杂的部署。 例如，在 Azure 中部署虚拟机需要虚拟网络、存储帐户和其他资源。 [虚拟网络构建基块模板](https://github.com/mspnp/template-building-blocks/wiki/VNet-(v1))部署虚拟网络和子网。 [虚拟机构建基块模板](https://github.com/mspnp/template-building-blocks/wiki/Windows-and-Linux-VMs-(v1))部署存储帐户、网络接口和实际的 VM。 然后，可以创建脚本或模板并结合相应的参数文件来调用这两个构建基块模板，通过一个操作部署完整的体系结构。

开发构建基块模板时，模式和实践团队 (p&p) 设计了几个概念用于扩展 Azure 资源管理器模板功能。 本系列文章将会介绍这些概念，使大家可以在自己的模板中使用。

> [!NOTE]
> 这些文章假设读者对 Azure 资源管理器模板有深度的了解。