---
title: Azure 上的保险索赔图像分类
description: 将图像处理内置到 Azure 应用程序中。
author: david-stanford
ms.date: 07/05/2018
ms.openlocfilehash: 31d328f8e5e27ea255024b7f461f2bfaeffc3ca7
ms.sourcegitcommit: b2a4eb132857afa70201e28d662f18458865a48e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48818524"
---
# <a name="image-classification-for-insurance-claims-on-azure"></a><span data-ttu-id="945b1-103">Azure 上的保险索赔图像分类</span><span class="sxs-lookup"><span data-stu-id="945b1-103">Image classification for insurance claims on Azure</span></span>

<span data-ttu-id="945b1-104">本方案适用于需处理图像的企业。</span><span class="sxs-lookup"><span data-stu-id="945b1-104">This scenario is relevant for businesses that need to process images.</span></span>

<span data-ttu-id="945b1-105">可能的应用包括：对时尚网站的图像分类、分析保险索赔的文本和图像，或者理解游戏屏幕截图中的遥测数据。</span><span class="sxs-lookup"><span data-stu-id="945b1-105">Potential applications include classifying images for a fashion website, analyzing text and images for insurance claims, or understanding telemetry data from game screenshots.</span></span> <span data-ttu-id="945b1-106">传统上，公司需开发机器学习模型方面的专业技术，训练模型，最后再通过自定义过程运行图像，以便从这些图像中获取数据。</span><span class="sxs-lookup"><span data-stu-id="945b1-106">Traditionally, companies would need to develop expertise in machine learning models, train the models, and finally run the images through their custom process to get the data out of the images.</span></span>

<span data-ttu-id="945b1-107">使用 Azure 服务（例如计算机视觉 API 和 Azure Functions），公司不需管理各个服务器，既减少了成本，又可充分利用 Microsoft 围绕认知服务图像处理开发的专业技术。</span><span class="sxs-lookup"><span data-stu-id="945b1-107">By using Azure services such as the Computer Vision API and Azure Functions, companies can eliminate the need to manage individual servers, while reducing costs and leveraging the expertise that Microsoft has already developed around processing images with Cognitive Services.</span></span> <span data-ttu-id="945b1-108">本示例方案专门解决图像处理用例问题。</span><span class="sxs-lookup"><span data-stu-id="945b1-108">This example scenario specifically addresses an image-processing use case.</span></span> <span data-ttu-id="945b1-109">如果有各种不同的的 AI 需求，可以考虑全套[认知服务](/azure/#pivot=products&panel=ai)。</span><span class="sxs-lookup"><span data-stu-id="945b1-109">If you have different AI needs, consider the full suite of [Cognitive Services](/azure/#pivot=products&panel=ai).</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="945b1-110">相关用例</span><span class="sxs-lookup"><span data-stu-id="945b1-110">Relevant use cases</span></span>

<span data-ttu-id="945b1-111">以下用例可以考虑本方案：</span><span class="sxs-lookup"><span data-stu-id="945b1-111">Consider this scenario for the following use cases:</span></span>

* <span data-ttu-id="945b1-112">对时尚网站上的图像分类。</span><span class="sxs-lookup"><span data-stu-id="945b1-112">Classify images on a fashion website.</span></span>
* <span data-ttu-id="945b1-113">对游戏屏幕截图中的遥测数据分类。</span><span class="sxs-lookup"><span data-stu-id="945b1-113">Classify telemetry data from screenshots of games.</span></span>

## <a name="architecture"></a><span data-ttu-id="945b1-114">体系结构</span><span class="sxs-lookup"><span data-stu-id="945b1-114">Architecture</span></span>

![图像分类的体系结构][architecture]

<span data-ttu-id="945b1-116">本方案涉及 Web 或移动应用程序的后端组件。</span><span class="sxs-lookup"><span data-stu-id="945b1-116">This scenario covers the back-end components of a web or mobile application.</span></span> <span data-ttu-id="945b1-117">数据流经方案的情形如下所示：</span><span class="sxs-lookup"><span data-stu-id="945b1-117">Data flows through the scenario as follows:</span></span>

1. <span data-ttu-id="945b1-118">API 层使用 Azure Functions 生成。</span><span class="sxs-lookup"><span data-stu-id="945b1-118">The API layer is built using Azure Functions.</span></span> <span data-ttu-id="945b1-119">应用程序可以通过这些 API 上传图像以及检索 Cosmos DB 中的数据。</span><span class="sxs-lookup"><span data-stu-id="945b1-119">These APIs enable the application to upload images and retrieve data from Cosmos DB.</span></span>
2. <span data-ttu-id="945b1-120">通过 API 调用上传图像时，该图像会存储在 Blob 存储中。</span><span class="sxs-lookup"><span data-stu-id="945b1-120">When an image is uploaded via an API call, it's stored in Blob storage.</span></span>
3. <span data-ttu-id="945b1-121">向 Blob 存储添加新文件会触发系统将事件网格通知发送给某个 Azure 函数。</span><span class="sxs-lookup"><span data-stu-id="945b1-121">Adding new files to Blob storage triggers an Event Grid notification to be sent to an Azure Function.</span></span>
4. <span data-ttu-id="945b1-122">Azure Functions 会向计算机视觉 API 发送新上传文件的链接，供其分析。</span><span class="sxs-lookup"><span data-stu-id="945b1-122">Azure Functions sends a link to the newly uploaded file to the Computer Vision API to analyze.</span></span>
5. <span data-ttu-id="945b1-123">数据从计算机视觉 API 返回以后，Azure Functions 会在 Cosmos DB 中生成一个条目，以便持久保存分析结果和图像元数据。</span><span class="sxs-lookup"><span data-stu-id="945b1-123">Once the data has been returned from the Computer Vision API, Azure Functions makes an entry in Cosmos DB to persist the results of the analysis along with the image metadata.</span></span>

### <a name="components"></a><span data-ttu-id="945b1-124">组件</span><span class="sxs-lookup"><span data-stu-id="945b1-124">Components</span></span>

* <span data-ttu-id="945b1-125">[计算机视觉 API](/azure/cognitive-services/computer-vision/home) 是认知服务套件的一部分，用于检索每个图像的信息。</span><span class="sxs-lookup"><span data-stu-id="945b1-125">[Computer Vision API](/azure/cognitive-services/computer-vision/home) is part of the Cognitive Services suite and is used to retrieve information about each image.</span></span>
* <span data-ttu-id="945b1-126">[Azure Functions](/azure/azure-functions/functions-overview) 为 Web 应用程序提供后端 API，并为已上传的图像提供事件处理。</span><span class="sxs-lookup"><span data-stu-id="945b1-126">[Azure Functions](/azure/azure-functions/functions-overview) provides the back-end API for the web application, as well as the event processing for uploaded images.</span></span>
* <span data-ttu-id="945b1-127">[事件网格](/azure/event-grid/overview)在新图像上传到 Blob 存储时触发一个事件。</span><span class="sxs-lookup"><span data-stu-id="945b1-127">[Event Grid](/azure/event-grid/overview) triggers an event when a new image is uploaded to blob storage.</span></span> <span data-ttu-id="945b1-128">该图像然后就会通过 Azure Functions 进行处理。</span><span class="sxs-lookup"><span data-stu-id="945b1-128">The image is then processed with Azure functions.</span></span>
* <span data-ttu-id="945b1-129">[Blob 存储](/azure/storage/blobs/storage-blobs-introduction)存储上传到 Web 应用程序中的所有图像文件，以及 Web 应用程序使用的任何静态文件。</span><span class="sxs-lookup"><span data-stu-id="945b1-129">[Blob storage](/azure/storage/blobs/storage-blobs-introduction) stores all of the image files that are uploaded into the web application, as well any static files that the web application consumes.</span></span>
* <span data-ttu-id="945b1-130">[Cosmos DB](/azure/cosmos-db/introduction) 存储每个已上传图像的元数据，包括计算机视觉 API 的处理结果。</span><span class="sxs-lookup"><span data-stu-id="945b1-130">[Cosmos DB](/azure/cosmos-db/introduction) stores metadata about each image that is uploaded, including the results of the processing from Computer Vision API.</span></span>

## <a name="alternatives"></a><span data-ttu-id="945b1-131">备选项</span><span class="sxs-lookup"><span data-stu-id="945b1-131">Alternatives</span></span>

* <span data-ttu-id="945b1-132">[自定义视觉服务](/azure/cognitive-services/custom-vision-service/home)。</span><span class="sxs-lookup"><span data-stu-id="945b1-132">[Custom Vision Service](/azure/cognitive-services/custom-vision-service/home).</span></span> <span data-ttu-id="945b1-133">计算机视觉 API 返回一组[基于分类的类别][cv-categories]。</span><span class="sxs-lookup"><span data-stu-id="945b1-133">The Computer Vision API returns a set of [taxonomy-based categories][cv-categories].</span></span> <span data-ttu-id="945b1-134">若需处理不是由计算机视觉 API 返回的信息，则可考虑使用自定义视觉服务，以便生成自定义图像分类器。</span><span class="sxs-lookup"><span data-stu-id="945b1-134">If you need to process information that isn't returned by the Computer Vision API, consider the Custom Vision Service, which lets you build custom image classifiers.</span></span>
* <span data-ttu-id="945b1-135">[Azure 搜索](/azure/search/search-what-is-azure-search)。</span><span class="sxs-lookup"><span data-stu-id="945b1-135">[Azure Search](/azure/search/search-what-is-azure-search).</span></span> <span data-ttu-id="945b1-136">如果用例需要查询元数据来查找符合特定条件的图像，则可考虑使用 Azure 搜索。</span><span class="sxs-lookup"><span data-stu-id="945b1-136">If your use case involves querying the metadata to find images that meet specific criteria, consider using Azure Search.</span></span> <span data-ttu-id="945b1-137">[认知搜索](/azure/search/cognitive-search-concept-intro)目前为预览版，可以无缝集成此工作流。</span><span class="sxs-lookup"><span data-stu-id="945b1-137">Currently in preview, [Cognitive search](/azure/search/cognitive-search-concept-intro) seamlessly integrates this workflow.</span></span>

## <a name="considerations"></a><span data-ttu-id="945b1-138">注意事项</span><span class="sxs-lookup"><span data-stu-id="945b1-138">Considerations</span></span>

### <a name="scalability"></a><span data-ttu-id="945b1-139">可伸缩性</span><span class="sxs-lookup"><span data-stu-id="945b1-139">Scalability</span></span>

<span data-ttu-id="945b1-140">在本示例方案中使用的大多数组件是托管服务，可以自动进行缩放。</span><span class="sxs-lookup"><span data-stu-id="945b1-140">The majority of the components used in this example scenario are managed services that will automatically scale.</span></span> <span data-ttu-id="945b1-141">一些需注意的例外：Azure Functions 存在限制，最多可以使用 200 个实例。</span><span class="sxs-lookup"><span data-stu-id="945b1-141">A couple notable exceptions: Azure Functions has a limit of a maximum of 200 instances.</span></span> <span data-ttu-id="945b1-142">如果所需规模超出此限制，可以考虑使用多个区域或应用计划。</span><span class="sxs-lookup"><span data-stu-id="945b1-142">If you need to scale beyond this limit, consider multiple regions or app plans.</span></span>

<span data-ttu-id="945b1-143">Cosmos DB 不会按照预配的请求单位 (RU) 自动缩放。</span><span class="sxs-lookup"><span data-stu-id="945b1-143">Cosmos DB doesn’t auto-scale in terms of provisioned request units (RUs).</span></span> <span data-ttu-id="945b1-144">有关如何估算需求的指南，请参阅文档中的[请求单位](/azure/cosmos-db/request-units)。</span><span class="sxs-lookup"><span data-stu-id="945b1-144">For guidance on estimating your requirements see [request units](/azure/cosmos-db/request-units) in our documentation.</span></span> <span data-ttu-id="945b1-145">若要充分利用 Cosmos DB 中的缩放功能，请了解[分区键](/azure/cosmos-db/partition-data)在 CosmosDB 中的工作方式。</span><span class="sxs-lookup"><span data-stu-id="945b1-145">To fully take advantage of the scaling in Cosmos DB, understand how [partition keys](/azure/cosmos-db/partition-data) work in CosmosDB.</span></span>

<span data-ttu-id="945b1-146">很多情况下，为了确保可用性、可伸缩性和分区，NoSQL 数据库会牺牲一致性（这就是所谓的 CAP 法则）。</span><span class="sxs-lookup"><span data-stu-id="945b1-146">NoSQL databases frequently trade consistency (in the sense of the CAP theorem) for availability, scalability, and partitioning.</span></span> <span data-ttu-id="945b1-147">本示例方案使用键-值数据模型，很少需要事务一致性，因为大多数操作就其定义来说属于原子操作。</span><span class="sxs-lookup"><span data-stu-id="945b1-147">In this example scenario, a key-value data model is used and transaction consistency is rarely needed as most operations are by definition atomic.</span></span> <span data-ttu-id="945b1-148">有关如何[选择正确的数据存储](../../guide/technology-choices/data-store-overview.md)的其他指南，请访问 Azure 体系结构中心。</span><span class="sxs-lookup"><span data-stu-id="945b1-148">Additional guidance to [Choose the right data store](../../guide/technology-choices/data-store-overview.md) is available in the Azure Architecture Center.</span></span>  <span data-ttu-id="945b1-149">如果实现要求高一致性，则可在 CosmosDB 中[选择一致性级别](/azure/cosmos-db/consistency-levels)。</span><span class="sxs-lookup"><span data-stu-id="945b1-149">If your implementation requires high consistency, you can [choose your consistency level](/azure/cosmos-db/consistency-levels) in CosmosDB.</span></span>

<span data-ttu-id="945b1-150">有关如何设计可缩放解决方案的通用指南，请参阅 Azure 体系结构中心的[可伸缩性核对清单][scalability]。</span><span class="sxs-lookup"><span data-stu-id="945b1-150">For general guidance on designing scalable solutions, see the [scalability checklist][scalability] in the Azure Architecture Center.</span></span>

### <a name="security"></a><span data-ttu-id="945b1-151">安全</span><span class="sxs-lookup"><span data-stu-id="945b1-151">Security</span></span>

<span data-ttu-id="945b1-152">[适用于 Azure 资源的托管标识][msi]用于访问帐户的其他内部资源，然后会被系统分配给 Azure Functions。</span><span class="sxs-lookup"><span data-stu-id="945b1-152">[Managed identities for Azure resources][msi] are used to provide access to other resources internal to your account and then assigned to your Azure Functions.</span></span> <span data-ttu-id="945b1-153">只允许通过这些标识访问必要的资源，这样可确保不将额外的内容公开给函数（可能还包括客户）。</span><span class="sxs-lookup"><span data-stu-id="945b1-153">Only allow access to the requisite resources in those identities to ensure that nothing extra is exposed to your functions (and potentially to your customers).</span></span>

<span data-ttu-id="945b1-154">若需安全解决方案的通用设计指南，请参阅 [Azure 安全性文档][security]。</span><span class="sxs-lookup"><span data-stu-id="945b1-154">For general guidance on designing secure solutions, see the [Azure Security Documentation][security].</span></span>

### <a name="resiliency"></a><span data-ttu-id="945b1-155">复原</span><span class="sxs-lookup"><span data-stu-id="945b1-155">Resiliency</span></span>

<span data-ttu-id="945b1-156">本方案中的所有组件都是托管的，因此均可在区域级别自动复原。</span><span class="sxs-lookup"><span data-stu-id="945b1-156">All of the components in this scenario are managed, so at a regional level they are all resilient automatically.</span></span>

<span data-ttu-id="945b1-157">若需可复原解决方案的通用设计指南，请参阅[设计适用于 Azure 的可复原应用程序][resiliency]。</span><span class="sxs-lookup"><span data-stu-id="945b1-157">For general guidance on designing resilient solutions, see [Designing resilient applications for Azure][resiliency].</span></span>

## <a name="pricing"></a><span data-ttu-id="945b1-158">定价</span><span class="sxs-lookup"><span data-stu-id="945b1-158">Pricing</span></span>

<span data-ttu-id="945b1-159">为了方便用户了解运行本方案的成本，我们已在成本计算器中预配置了所有服务。</span><span class="sxs-lookup"><span data-stu-id="945b1-159">To explore the cost of running this scenario, all of the services are pre-configured in the cost calculator.</span></span> <span data-ttu-id="945b1-160">若要了解自己的特定用例的定价变化情况，请按预期的流量更改相应的变量。</span><span class="sxs-lookup"><span data-stu-id="945b1-160">To see how the pricing would change for your particular use case, change the appropriate variables to match your expected traffic.</span></span>

<span data-ttu-id="945b1-161">我们已根据流量（假定所有图像的大小均为 100 kb）提供了三个示例性的成本配置文件：</span><span class="sxs-lookup"><span data-stu-id="945b1-161">We have provided three sample cost profiles based on amount of traffic (we assume all images are 100 kb in size):</span></span>

* <span data-ttu-id="945b1-162">[小][small-pricing]：此定价示例对应于每月处理的图像数 &lt; 5000 的情况。</span><span class="sxs-lookup"><span data-stu-id="945b1-162">[Small][small-pricing]: this pricing example correlates to processing &lt; 5000 images a month.</span></span>
* <span data-ttu-id="945b1-163">[中][medium-pricing]：此定价示例对应于每月处理的图像数为 500,000 的情况。</span><span class="sxs-lookup"><span data-stu-id="945b1-163">[Medium][medium-pricing]: this pricing example correlates to processing 500,000 images a month.</span></span>
* <span data-ttu-id="945b1-164">[大][large-pricing]：此定价示例对应于每月处理的图像数为 5 千万的情况。</span><span class="sxs-lookup"><span data-stu-id="945b1-164">[Large][large-pricing]: this pricing example correlates to processing 50 million images a month.</span></span>

## <a name="related-resources"></a><span data-ttu-id="945b1-165">相关资源</span><span class="sxs-lookup"><span data-stu-id="945b1-165">Related resources</span></span>

<span data-ttu-id="945b1-166">如需此方案的引导式学习路径，请参阅[在 Azure 中生成无服务器 Web 应用][serverless]。</span><span class="sxs-lookup"><span data-stu-id="945b1-166">For a guided learning path of this scenario, see [Build a serverless web app in Azure][serverless].</span></span>

<span data-ttu-id="945b1-167">将此示例方案部署到生产环境中之前，请参阅 Azure Functions [最佳做法][functions-best-practices]。</span><span class="sxs-lookup"><span data-stu-id="945b1-167">Before deploying this example scenario in a production environment, review the Azure Functions [best practices][functions-best-practices].</span></span>

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
