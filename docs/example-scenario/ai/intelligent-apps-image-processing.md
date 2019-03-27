---
title: 保险索赔图像分类
titleSuffix: Azure Example Scenarios
description: 将图像处理内置到 Azure 应用程序中。
author: david-stanford
ms.date: 07/05/2018
ms.topic: example-scenario
ms.service: architecture-center
ms.subservice: example-scenario
social_image_url: /azure/architecture/example-scenario/ai/media/architecture-intelligent-apps-image-processing.png
ms.openlocfilehash: 03ef9d15ec9bf64dc743657e9d1e7a7e275c5a8e
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58244798"
---
# <a name="image-classification-for-insurance-claims-on-azure"></a>Azure 上的保险索赔图像分类

本方案适用于需处理图像的企业。

可能的应用包括：对时尚网站的图像分类、分析保险索赔的文本和图像，或者理解游戏屏幕截图中的遥测数据。 传统上，公司需开发机器学习模型方面的专业技术，训练模型，最后再通过自定义过程运行图像，以便从这些图像中获取数据。

使用 Azure 服务（例如计算机视觉 API 和 Azure Functions），公司不需管理各个服务器，既减少了成本，又可充分利用 Microsoft 围绕认知服务图像处理开发的专业技术。 本示例方案专门解决图像处理用例问题。 如果有各种不同的的 AI 需求，可以考虑全套[认知服务](/azure/#pivot=products&panel=ai)。

## <a name="relevant-use-cases"></a>相关用例

其他相关用例包括：

- 对时尚网站上的图像进行分类。
- 对游戏屏幕截图中的遥测数据进行分类。

## <a name="architecture"></a>体系结构

![图像分类的体系结构][architecture]

本方案涉及 Web 或移动应用程序的后端组件。 数据流经方案的情形如下所示：

1. API 层使用 Azure Functions 生成。 应用程序可以通过这些 API 上传图像以及检索 Cosmos DB 中的数据。
2. 通过 API 调用上传图像时，该图像会存储在 Blob 存储中。
3. 向 Blob 存储添加新文件会触发系统将事件网格通知发送给某个 Azure 函数。
4. Azure Functions 会向计算机视觉 API 发送新上传文件的链接，供其分析。
5. 数据从计算机视觉 API 返回以后，Azure Functions 会在 Cosmos DB 中生成一个条目，以便持久保存分析结果和图像元数据。

### <a name="components"></a>组件

- [计算机视觉 API](/azure/cognitive-services/computer-vision/home) 是认知服务套件的一部分，用于检索每个图像的信息。
- [Azure Functions](/azure/azure-functions/functions-overview) 为 Web 应用程序提供后端 API，并为已上传的图像提供事件处理。
- [事件网格](/azure/event-grid/overview)在新图像上传到 Blob 存储时触发一个事件。 该图像然后就会通过 Azure Functions 进行处理。
- [Blob 存储](/azure/storage/blobs/storage-blobs-introduction)存储上传到 Web 应用程序中的所有图像文件，以及 Web 应用程序使用的任何静态文件。
- [Cosmos DB](/azure/cosmos-db/introduction) 存储每个已上传图像的元数据，包括计算机视觉 API 的处理结果。

## <a name="alternatives"></a>备选项

- [自定义视觉服务](/azure/cognitive-services/custom-vision-service/home)。 计算机视觉 API 返回一组[基于分类的类别][cv-categories]。 若需处理不是由计算机视觉 API 返回的信息，则可考虑使用自定义视觉服务，以便生成自定义图像分类器。
- [Azure 搜索](/azure/search/search-what-is-azure-search)。 如果用例需要查询元数据来查找符合特定条件的图像，则可考虑使用 Azure 搜索。 [认知搜索](/azure/search/cognitive-search-concept-intro)目前为预览版，可以无缝集成此工作流。

## <a name="considerations"></a>注意事项

### <a name="scalability"></a>可伸缩性

在本示例方案中使用的大多数组件是托管服务，可以自动进行缩放。 一些需注意的例外：Azure Functions 存在限制，最多可以使用 200 个实例。 如果所需规模超出此限制，可以考虑使用多个区域或应用计划。

Cosmos DB 不会按照预配的请求单位 (RU) 自动缩放。 有关如何估算需求的指南，请参阅文档中的[请求单位](/azure/cosmos-db/request-units)。 若要充分利用 Cosmos DB 中的缩放功能，请了解[分区键](/azure/cosmos-db/partition-data)在 CosmosDB 中的工作方式。

很多情况下，为了确保可用性、可伸缩性和分区，NoSQL 数据库会牺牲一致性（这就是所谓的 CAP 法则）。 本示例方案使用键-值数据模型，很少需要事务一致性，因为大多数操作就其定义来说属于原子操作。 有关如何[选择正确的数据存储](../../guide/technology-choices/data-store-overview.md)的其他指南，请访问 Azure 体系结构中心。 如果实现要求高一致性，则可在 CosmosDB 中[选择一致性级别](/azure/cosmos-db/consistency-levels)。

有关如何设计可缩放解决方案的通用指南，请参阅 Azure 体系结构中心的[可伸缩性核对清单][scalability]。

### <a name="security"></a>安全

[适用于 Azure 资源的托管标识][msi]用于访问帐户的其他内部资源，然后会被系统分配给 Azure Functions。 只允许通过这些标识访问必要的资源，这样可确保不将额外的内容公开给函数（可能还包括客户）。

若需安全解决方案的通用设计指南，请参阅 [Azure 安全性文档][security]。

### <a name="resiliency"></a>复原

本方案中的所有组件都是托管的，因此均可在区域级别自动复原。

若需可复原解决方案的通用设计指南，请参阅[设计适用于 Azure 的可复原应用程序][resiliency]。

## <a name="pricing"></a>定价

为了方便用户了解运行本方案的成本，我们已在成本计算器中预配置了所有服务。 若要了解自己的特定用例的定价变化情况，请按预期的流量更改相应的变量。

我们已根据流量（假定所有图像的大小均为 100 kb）提供了三个示例性的成本配置文件：

- [小][small-pricing]：此定价示例对应于每月处理的图像数 &lt; 5000 的情况。
- [中][medium-pricing]：此定价示例对应于每月处理的图像数为 500,000 的情况。
- [大][large-pricing]：此定价示例对应于每月处理的图像数为 5 千万的情况。

## <a name="related-resources"></a>相关资源

如果需要引导式学习路径，请参阅[在 Azure 中构建无服务器 Web 应用][serverless]。

在生产环境中部署此示例方案之前，请查看用于[优化 Azure Functions 的性能和可靠性][functions-best-practices]的建议做法。

<!-- links -->
[architecture]: ./media/architecture-intelligent-apps-image-processing.png
[small-pricing]: https://azure.com/e/f9b59d238b43423683db73f4a31dc380
[medium-pricing]: https://azure.com/e/7c7fc474db344b87aae93bc29ae27108
[large-pricing]: https://azure.com/e/cbadbca30f8640d6a061f8457a74ba7d
[cognitive-search]: /azure/search/cognitive-search-concept-intro
[serverless]: /azure/functions/tutorial-static-website-serverless-api-with-database
[cv-categories]: /azure/cognitive-services/computer-vision/home#the-86-category-concept
[resiliency]: /azure/architecture/resiliency/
[security]: /azure/security/
[scalability]: /azure/architecture/checklist/scalability
[functions-best-practices]: /azure/azure-functions/functions-best-practices
[msi]: /azure/app-service/app-service-managed-service-identity
