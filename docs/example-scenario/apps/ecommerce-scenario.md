---
title: 电子商务前端
titleSuffix: Azure Example Scenarios
description: 在 Azure 上托管电子商务站点。
author: masonch
ms.date: 7/13/18
ms.custom: fasttrack
ms.openlocfilehash: d6587218813fa450b284f3a300c7254a3c9fe41f
ms.sourcegitcommit: bb7fcffbb41e2c26a26f8781df32825eb60df70c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/20/2018
ms.locfileid: "53643945"
---
# <a name="an-e-commerce-front-end-on-azure"></a><span data-ttu-id="78e52-103">Azure 上的电子商务前端</span><span class="sxs-lookup"><span data-stu-id="78e52-103">An e-commerce front end on Azure</span></span>

<span data-ttu-id="78e52-104">本示例方案详述了如何使用 Azure 平台即服务 (PaaS) 工具实现一个电子商务前端。</span><span class="sxs-lookup"><span data-stu-id="78e52-104">This example scenario walks you through an implementation of an e-commerce front end using Azure platform as a service (PaaS) tools.</span></span> <span data-ttu-id="78e52-105">许多电子商务网站在一段时间过后都面临季节性和流量不稳定的问题。</span><span class="sxs-lookup"><span data-stu-id="78e52-105">Many e-commerce websites face seasonality and traffic variability over time.</span></span> <span data-ttu-id="78e52-106">对你的产品或服务的需求增加时（不管是可预见的还是不可预见的），可以通过 PaaS 工具自动应对客户数量和交易数量增加的情况。</span><span class="sxs-lookup"><span data-stu-id="78e52-106">When demand for your products or services takes off, whether predictably or unpredictably, using PaaS tools will allow you to handle more customers and more transactions automatically.</span></span> <span data-ttu-id="78e52-107">另外，本方案只要求你支付所使用的容量，让你充分利用云经济。</span><span class="sxs-lookup"><span data-stu-id="78e52-107">Additionally, this scenario takes advantage of cloud economics by paying only for the capacity you use.</span></span>

<span data-ttu-id="78e52-108">本文档介绍各种 Azure PaaS 组件和注意事项，方便你部署示例性的电子商务应用程序 *Relecloud Concerts*，这是一个在线音乐会订票平台。</span><span class="sxs-lookup"><span data-stu-id="78e52-108">This document will help you will learn about various Azure PaaS components and considerations used to bring together to deploy a sample e-commerce application, *Relecloud Concerts*, an online concert ticketing platform.</span></span>

## <a name="relevant-use-cases"></a><span data-ttu-id="78e52-109">相关用例</span><span class="sxs-lookup"><span data-stu-id="78e52-109">Relevant use cases</span></span>

<span data-ttu-id="78e52-110">其他相关用例包括：</span><span class="sxs-lookup"><span data-stu-id="78e52-110">Other relevant use cases include:</span></span>

- <span data-ttu-id="78e52-111">生成一个需要通过弹性缩放来应对不同时间的用户数突增的应用程序。</span><span class="sxs-lookup"><span data-stu-id="78e52-111">Building an application that needs elastic scale to handle bursts of users at different times.</span></span>
- <span data-ttu-id="78e52-112">生成一个根据设计可以在全世界的不同 Azure 区域运行并可确保高可用性的应用程序。</span><span class="sxs-lookup"><span data-stu-id="78e52-112">Building an application that is designed to operate at high availability in different Azure regions around the world.</span></span>

## <a name="architecture"></a><span data-ttu-id="78e52-113">体系结构</span><span class="sxs-lookup"><span data-stu-id="78e52-113">Architecture</span></span>

![适用于电子商务应用程序的示例性方案体系结构][architecture]

<span data-ttu-id="78e52-115">本方案介绍如何通过某个电子商务站点购票。数据流经方案的情形如下所示：</span><span class="sxs-lookup"><span data-stu-id="78e52-115">This scenario covers purchasing tickets from an e-commerce site, the data flows through the scenario as follows:</span></span>

1. <span data-ttu-id="78e52-116">Azure 流量管理器会将用户的请求路由到托管在 Azure 应用服务中的电子商务站点。</span><span class="sxs-lookup"><span data-stu-id="78e52-116">Azure Traffic Manager routes a user's request to the e-commerce site hosted in Azure App Service.</span></span>
2. <span data-ttu-id="78e52-117">Azure CDN 为用户提供静态图像和内容。</span><span class="sxs-lookup"><span data-stu-id="78e52-117">Azure CDN serves static images and content to the user.</span></span>
3. <span data-ttu-id="78e52-118">用户通过 Azure Active Directory B2C 租户登录到应用程序。</span><span class="sxs-lookup"><span data-stu-id="78e52-118">User signs in to the application through an Azure Active Directory B2C tenant.</span></span>
4. <span data-ttu-id="78e52-119">用户使用 Azure 搜索来搜索音乐会。</span><span class="sxs-lookup"><span data-stu-id="78e52-119">User searches for concerts using Azure Search.</span></span>
5. <span data-ttu-id="78e52-120">网站从 Azure SQL 数据库拉取音乐会详细信息。</span><span class="sxs-lookup"><span data-stu-id="78e52-120">Web site pulls concert details from Azure SQL Database.</span></span>
6. <span data-ttu-id="78e52-121">网站引用 Blob 存储中的已购票的图像。</span><span class="sxs-lookup"><span data-stu-id="78e52-121">Web site refers to purchased ticket images in Blob Storage.</span></span>
7. <span data-ttu-id="78e52-122">系统将数据库查询结果缓存在 Azure Redis 缓存中，以确保更好的性能。</span><span class="sxs-lookup"><span data-stu-id="78e52-122">Database query results are cached in Azure Redis Cache for better performance.</span></span>
8. <span data-ttu-id="78e52-123">用户提交订票单和音乐会评价，系统将其放入队列中。</span><span class="sxs-lookup"><span data-stu-id="78e52-123">User submits ticket orders and concert reviews, which are placed in the queue.</span></span>
9. <span data-ttu-id="78e52-124">Azure Functions 处理订单付款和音乐会评价。</span><span class="sxs-lookup"><span data-stu-id="78e52-124">Azure Functions processes order payment and concert reviews.</span></span>
10. <span data-ttu-id="78e52-125">认知服务对音乐会评价进行分析，确定其情绪（正面或负面）。</span><span class="sxs-lookup"><span data-stu-id="78e52-125">Cognitive services provide an analysis of the concert review to determine the sentiment (positive or negative).</span></span>
11. <span data-ttu-id="78e52-126">Application Insights 提供用于监视 Web 应用程序运行状况的性能指标。</span><span class="sxs-lookup"><span data-stu-id="78e52-126">Application Insights provides performance metrics for monitoring the health of the web application.</span></span>

### <a name="components"></a><span data-ttu-id="78e52-127">组件</span><span class="sxs-lookup"><span data-stu-id="78e52-127">Components</span></span>

- <span data-ttu-id="78e52-128">[Azure CDN][docs-cdn] 从靠近用户的位置提供静态缓存内容，以便减轻延迟。</span><span class="sxs-lookup"><span data-stu-id="78e52-128">[Azure CDN][docs-cdn] delivers static, cached content from locations close to users to reduce latency.</span></span>
- <span data-ttu-id="78e52-129">[Azure 流量管理器][docs-traffic-manager]控制不同 Azure 区域中服务终结点的用户流量分配。</span><span class="sxs-lookup"><span data-stu-id="78e52-129">[Azure Traffic Manager][docs-traffic-manager] controls the distribution of user traffic for service endpoints in different Azure regions.</span></span>
- <span data-ttu-id="78e52-130">[应用服务 - Web 应用][docs-webapps]托管的 Web 应用程序可以实现自动缩放和高可用性，不需管理基础结构。</span><span class="sxs-lookup"><span data-stu-id="78e52-130">[App Services - Web Apps][docs-webapps] hosts web applications allowing autoscale and high availability without having to manage infrastructure.</span></span>
- <span data-ttu-id="78e52-131">[Azure Active Directory - B2C][docs-b2c] 是一项标识管理服务，用于自定义和控制客户在应用程序中的注册、登录和管理配置文件的方式。</span><span class="sxs-lookup"><span data-stu-id="78e52-131">[Azure Active Directory - B2C][docs-b2c] is an identity management service that enables customization and control over how customers sign up, sign in, and manage their profiles in an application.</span></span>
- <span data-ttu-id="78e52-132">[存储队列][docs-storage-queues]存储大量可供应用程序访问的队列消息。</span><span class="sxs-lookup"><span data-stu-id="78e52-132">[Storage Queues][docs-storage-queues] stores large numbers of queue messages that can be accessed by an application.</span></span>
- <span data-ttu-id="78e52-133">[Functions][docs-functions] 是无服务器计算选项，可以让应用程序按需运行，不需管理基础结构。</span><span class="sxs-lookup"><span data-stu-id="78e52-133">[Functions][docs-functions] are serverless compute options that allow applications to run on-demand without having to manage infrastructure.</span></span>
- <span data-ttu-id="78e52-134">[认知服务 - 情绪分析][docs-sentiment-analysis]使用机器学习 API，可让开发人员轻松地在应用程序中添加智能功能 - 例如情绪和视频检测；面部、语音与视觉识别；语音与语言理解。</span><span class="sxs-lookup"><span data-stu-id="78e52-134">[Cognitive Services - Sentiment Analysis][docs-sentiment-analysis] uses machine learning APIs and enables developers to easily add intelligent features – such as emotion and video detection; facial, speech, and vision recognition; and speech and language understanding – into applications.</span></span>
- <span data-ttu-id="78e52-135">[Azure 搜索][docs-search]是一种搜索即服务云解决方案，它可以根据 Web、移动和企业应用程序中的专用异类内容提供丰富的搜索体验。</span><span class="sxs-lookup"><span data-stu-id="78e52-135">[Azure Search][docs-search] is a search-as-a-service cloud solution that provides a rich search experience over private, heterogenous content in web, mobile, and enterprise applications.</span></span>
- <span data-ttu-id="78e52-136">[存储 Blob][docs-storage-blobs] 最适合存储大量的非结构化数据，例如文本或二进制数据。</span><span class="sxs-lookup"><span data-stu-id="78e52-136">[Storage Blobs][docs-storage-blobs] are optimized to store large amounts of unstructured data, such as text or binary data.</span></span>
- <span data-ttu-id="78e52-137">[Redis 缓存][docs-redis-cache]可以提高严重依赖于后端数据存储的系统的性能和可伸缩性，其方式是将经常访问的数据暂时复制到靠近应用程序的快速存储。</span><span class="sxs-lookup"><span data-stu-id="78e52-137">[Redis Cache][docs-redis-cache] improves the performance and scalability of systems that rely heavily on back-end data stores by temporarily copying frequently accessed data to fast storage located close to the application.</span></span>
- <span data-ttu-id="78e52-138">[SQL 数据库][docs-sql-database]是 Microsoft Azure 中通用的关系数据库托管服务，支持关系数据、JSON、空间和 XML 等结构。</span><span class="sxs-lookup"><span data-stu-id="78e52-138">[SQL Database][docs-sql-database] is a general-purpose relational database managed service in Microsoft Azure that supports structures such as relational data, JSON, spatial, and XML.</span></span>
- <span data-ttu-id="78e52-139">[Application Insights][docs-application-insights] 旨在持续提高性能与可用性，其方式是通过内置的分析工具了解用户使用某个应用进行的操作，以便自动检测性能异常。</span><span class="sxs-lookup"><span data-stu-id="78e52-139">[Application Insights][docs-application-insights] is designed to help you continuously improve performance and usability by automatically detecting performance anomalies through built-in analytics tools to help understand what users do with an app.</span></span>

### <a name="alternatives"></a><span data-ttu-id="78e52-140">备选项</span><span class="sxs-lookup"><span data-stu-id="78e52-140">Alternatives</span></span>

<span data-ttu-id="78e52-141">可以通过许多其他技术来生成面向客户的应用程序，侧重于大规模的电子商务。</span><span class="sxs-lookup"><span data-stu-id="78e52-141">Many other technologies are available for building a customer facing application focused on e-commerce at scale.</span></span> <span data-ttu-id="78e52-142">这涉及到应用程序的前端以及数据层。</span><span class="sxs-lookup"><span data-stu-id="78e52-142">These cover both the front end of the application as well as the data tier.</span></span>

<span data-ttu-id="78e52-143">其他适用于 Web 层和函数的选项包括：</span><span class="sxs-lookup"><span data-stu-id="78e52-143">Other options for the web tier and functions include:</span></span>

- <span data-ttu-id="78e52-144">[Service Fabric][docs-service-fabric] - 一个平台，侧重于生成分布式组件，而此类组件适合在严格控制的群集上部署和运行。</span><span class="sxs-lookup"><span data-stu-id="78e52-144">[Service Fabric][docs-service-fabric] - A platform focused around building distributed components that benefit from being deployed and run across a cluster with a high degree of control.</span></span> <span data-ttu-id="78e52-145">Service Fabric 也可用于托管容器。</span><span class="sxs-lookup"><span data-stu-id="78e52-145">Service Fabric can also be used to host containers.</span></span>
- <span data-ttu-id="78e52-146">[Azure Kubernetes 服务][docs-kubernetes-service] - 一个平台，用于生成和部署基于容器的解决方案，这些方案可以用作微服务体系结构的某个实现。</span><span class="sxs-lookup"><span data-stu-id="78e52-146">[Azure Kubernetes Service][docs-kubernetes-service] - A platform for building and deploying container-based solutions that can be used as one implementation of a microservices architecture.</span></span> <span data-ttu-id="78e52-147">这样就可以确保应用程序的不同组件按照需求灵活地进行独立缩放。</span><span class="sxs-lookup"><span data-stu-id="78e52-147">This allows for agility of different components of the application to be able to scale independently on demand.</span></span>
- <span data-ttu-id="78e52-148">[Azure 容器实例][docs-container-instances] - 这是一种方法，可以快速地部署和运行生命周期短的容器。</span><span class="sxs-lookup"><span data-stu-id="78e52-148">[Azure Container Instances][docs-container-instances] - A way of quickly deploying and running containers with a short lifecycle.</span></span> <span data-ttu-id="78e52-149">部署此处的容器是为了运行快速处理作业，例如处理一条消息或执行一项计算，然后在它们完成后尽快取消预配。</span><span class="sxs-lookup"><span data-stu-id="78e52-149">Containers here are deployed to run a quick processing job such as processing a message or performing a calculation and then deprovisioned as soon as they are complete.</span></span>
- <span data-ttu-id="78e52-150">[服务总线][service-bus]可以用来替代存储队列。</span><span class="sxs-lookup"><span data-stu-id="78e52-150">[Service Bus][service-bus] could be used in place of Storage Queue's.</span></span>

<span data-ttu-id="78e52-151">可用于数据层的其他选项包括：</span><span class="sxs-lookup"><span data-stu-id="78e52-151">Other options for the data tier include:</span></span>

- <span data-ttu-id="78e52-152">[Cosmos DB](/azure/cosmos-db/introduction)：Microsoft 推出的全球分布式多模型数据库。</span><span class="sxs-lookup"><span data-stu-id="78e52-152">[Cosmos DB](/azure/cosmos-db/introduction): Microsoft's globally distributed, multi-model database.</span></span> <span data-ttu-id="78e52-153">此服务提供的平台适合运行其他数据模型，例如 Mongo DB、Cassandra、Graph 数据或简单的表存储。</span><span class="sxs-lookup"><span data-stu-id="78e52-153">This service provides a platform to run other data models such as Mongo DB, Cassandra, Graph data, or simple table storage.</span></span>

## <a name="considerations"></a><span data-ttu-id="78e52-154">注意事项</span><span class="sxs-lookup"><span data-stu-id="78e52-154">Considerations</span></span>

### <a name="availability"></a><span data-ttu-id="78e52-155">可用性</span><span class="sxs-lookup"><span data-stu-id="78e52-155">Availability</span></span>

- <span data-ttu-id="78e52-156">考虑在生成云应用程序时，利用[针对可用性的典型设计模式][design-patterns-availability]。</span><span class="sxs-lookup"><span data-stu-id="78e52-156">Consider leveraging the [typical design patterns for availability][design-patterns-availability] when building your cloud application.</span></span>
- <span data-ttu-id="78e52-157">在适当的[应用服务 Web 应用程序参考体系结构][app-service-reference-architecture]中审核可用性注意事项</span><span class="sxs-lookup"><span data-stu-id="78e52-157">Review the availability considerations in the appropriate [App Service web application reference architecture][app-service-reference-architecture]</span></span>
- <span data-ttu-id="78e52-158">若要针对可用性进行其他的考量，请参阅 Azure 体系结构中心的[可用性核对清单][availability]。</span><span class="sxs-lookup"><span data-stu-id="78e52-158">For additional considerations concerning availability, see the [availability checklist][availability] in the Azure Architecture Center.</span></span>

### <a name="scalability"></a><span data-ttu-id="78e52-159">可伸缩性</span><span class="sxs-lookup"><span data-stu-id="78e52-159">Scalability</span></span>

- <span data-ttu-id="78e52-160">生成云应用程序时，请弄清楚[针对可伸缩性的典型设计模式][design-patterns-scalability]。</span><span class="sxs-lookup"><span data-stu-id="78e52-160">When building a cloud application be aware of the [typical design patterns for scalability][design-patterns-scalability].</span></span>
- <span data-ttu-id="78e52-161">在适当的[应用服务 Web 应用程序参考体系结构][app-service-reference-architecture]中审核可伸缩性注意事项</span><span class="sxs-lookup"><span data-stu-id="78e52-161">Review the scalability considerations in the appropriate [App Service web application reference architecture][app-service-reference-architecture]</span></span>
- <span data-ttu-id="78e52-162">若要了解其他可伸缩性主题，请参阅 Azure 体系结构中心提供的[可伸缩性核对清单][scalability]。</span><span class="sxs-lookup"><span data-stu-id="78e52-162">For other scalability topics, see the [scalability checklist][scalability] available in the Azure Architecture Center.</span></span>

### <a name="security"></a><span data-ttu-id="78e52-163">安全</span><span class="sxs-lookup"><span data-stu-id="78e52-163">Security</span></span>

- <span data-ttu-id="78e52-164">考虑在适当情况下利用[针对安全性的典型设计模式][design-patterns-security]。</span><span class="sxs-lookup"><span data-stu-id="78e52-164">Consider leveraging the [typical design patterns for security][design-patterns-security] where appropriate.</span></span>
- <span data-ttu-id="78e52-165">在适当的[应用服务 Web 应用程序参考体系结构][app-service-reference-architecture]中审核安全性注意事项。</span><span class="sxs-lookup"><span data-stu-id="78e52-165">Review the security considerations in the appropriate [App Service web application reference architecture][app-service-reference-architecture].</span></span>
- <span data-ttu-id="78e52-166">考虑跟进某个[安全开发生命周期][secure-development]流程，帮助开发人员生成更安全的软件并解决安全符合性要求问题，同时减少开发成本。</span><span class="sxs-lookup"><span data-stu-id="78e52-166">Consider following a [secure development lifecycle][secure-development] process to help developers build more secure software and address security compliance requirements while reducing development cost.</span></span>
- <span data-ttu-id="78e52-167">查看蓝图体系结构，以确定 [Azure PCI DSS 符合性][pci-dss-blueprint]。</span><span class="sxs-lookup"><span data-stu-id="78e52-167">Review the blueprint architecture for [Azure PCI DSS compliance][pci-dss-blueprint].</span></span>

### <a name="resiliency"></a><span data-ttu-id="78e52-168">复原</span><span class="sxs-lookup"><span data-stu-id="78e52-168">Resiliency</span></span>

- <span data-ttu-id="78e52-169">考虑在应用程序的某个部分不可用的情况下，利用[断路器模式][circuit-breaker]进行有效的错误处理。</span><span class="sxs-lookup"><span data-stu-id="78e52-169">Consider leveraging the [circuit breaker pattern][circuit-breaker] to provide graceful error handling should one part of the application not be available.</span></span>
- <span data-ttu-id="78e52-170">审核[针对复原的典型设计模式][design-patterns-resiliency]，考虑在适当情况下实施这些模式。</span><span class="sxs-lookup"><span data-stu-id="78e52-170">Review the [typical design patterns for resiliency][design-patterns-resiliency] and consider implementing these where appropriate.</span></span>
- <span data-ttu-id="78e52-171">可以在 Azure 体系结构中心找到许多[建议用于应用服务的做法][resiliency-app-service]。</span><span class="sxs-lookup"><span data-stu-id="78e52-171">You can find a number of [recommended practices for App Service][resiliency-app-service] in the Azure Architecture Center.</span></span>
- <span data-ttu-id="78e52-172">考虑对数据层使用活动的[异地复制][sql-geo-replication]，对图像和队列使用[异地冗余][storage-geo-redudancy]存储。</span><span class="sxs-lookup"><span data-stu-id="78e52-172">Consider using active [geo-replication][sql-geo-replication] for the data tier and [geo-redundant][storage-geo-redudancy] storage for images and queues.</span></span>
- <span data-ttu-id="78e52-173">有关[复原][resiliency]的更深入讨论，请查看 Azure 体系结构中心的相关文章。</span><span class="sxs-lookup"><span data-stu-id="78e52-173">For a deeper discussion on [resiliency][resiliency], see the relevant article in the Azure Architecture Center.</span></span>

## <a name="deploy-the-scenario"></a><span data-ttu-id="78e52-174">部署方案</span><span class="sxs-lookup"><span data-stu-id="78e52-174">Deploy the scenario</span></span>

<span data-ttu-id="78e52-175">若要部署本方案，可以按照此[分步教程][end-to-end-walkthrough]的说明操作，手动部署每个组件。</span><span class="sxs-lookup"><span data-stu-id="78e52-175">To deploy this scenario, you can follow this [step-by-step tutorial][end-to-end-walkthrough] demonstrating how to manually deploy each component.</span></span> <span data-ttu-id="78e52-176">本教程还提供了 .NET 示例应用程序，该程序运行简单的购票应用程序。</span><span class="sxs-lookup"><span data-stu-id="78e52-176">This tutorial also provides a .NET sample application that runs a simple ticket purchasing application.</span></span> <span data-ttu-id="78e52-177">此外还有一个资源管理器模板，可以自动部署大多数 Azure 资源。</span><span class="sxs-lookup"><span data-stu-id="78e52-177">Additionally, there is a Resource Manager template to automate the deployment of most of the Azure resources.</span></span>

## <a name="pricing"></a><span data-ttu-id="78e52-178">定价</span><span class="sxs-lookup"><span data-stu-id="78e52-178">Pricing</span></span>

<span data-ttu-id="78e52-179">为了方便用户了解运行本方案的成本，我们已在成本计算器中预配置了所有服务。</span><span class="sxs-lookup"><span data-stu-id="78e52-179">Explore the cost of running this scenario, all of the services are pre-configured in the cost calculator.</span></span> <span data-ttu-id="78e52-180">若要了解自己的特定用例的定价变化情况，请按预期的流量更改相应的变量。</span><span class="sxs-lookup"><span data-stu-id="78e52-180">To see how the pricing would change for your particular use case change the appropriate variables to match your expected traffic.</span></span>

<span data-ttu-id="78e52-181">我们已根据你预期接收的流量提供了三个示例性的成本配置文件：</span><span class="sxs-lookup"><span data-stu-id="78e52-181">We have provided three sample cost profiles based on amount of traffic you expect to get:</span></span>

- <span data-ttu-id="78e52-182">[小型][small-pricing]：此定价示例表示生成最低生产级别实例所需的组件。</span><span class="sxs-lookup"><span data-stu-id="78e52-182">[Small][small-pricing]: This pricing example represents the components necessary to build the out for a minimum production level instance.</span></span> <span data-ttu-id="78e52-183">在这里，我们假定用户数量很少，每月只有数千。</span><span class="sxs-lookup"><span data-stu-id="78e52-183">Here we are assuming a small number of users, numbering only in a few thousand per month.</span></span> <span data-ttu-id="78e52-184">此应用使用某个标准 Web 应用的单个实例，这对于启动自动缩放来说已足够。</span><span class="sxs-lookup"><span data-stu-id="78e52-184">The app is using a single instance of a standard web app that will be enough to enable autoscaling.</span></span> <span data-ttu-id="78e52-185">其他组件都缩放成一个基本层，可以尽量减少成本，但仍可确保有 SLA 支持和足够的容量，可以处理生产级别的工作负荷。</span><span class="sxs-lookup"><span data-stu-id="78e52-185">Each of the other components are scaled to a basic tier that will allow for a minimum amount of cost but still ensure that there is SLA support and enough capacity to handle a production level workload.</span></span>
- <span data-ttu-id="78e52-186">[中型][medium-pricing]：此定价示例表示组件情况符合中等大小的部署。</span><span class="sxs-lookup"><span data-stu-id="78e52-186">[Medium][medium-pricing]: This pricing example represents the components indicative of a moderate size deployment.</span></span> <span data-ttu-id="78e52-187">在这里，我们估计一个月有大约 100,000 个用户使用此系统。</span><span class="sxs-lookup"><span data-stu-id="78e52-187">Here we estimate approximately 100,000 users using the system over the course of a month.</span></span> <span data-ttu-id="78e52-188">预计的流量在处于中等标准层的单个应用服务实例中处理。</span><span class="sxs-lookup"><span data-stu-id="78e52-188">The expected traffic is handled in a single app service instance with a moderate standard tier.</span></span> <span data-ttu-id="78e52-189">另外，认知和搜索服务的中型层也会添加到计算器中。</span><span class="sxs-lookup"><span data-stu-id="78e52-189">Additionally, moderate tiers of cognitive and search services are added to the calculator.</span></span>
- <span data-ttu-id="78e52-190">[大型][large-pricing]：此定价示例表示应用程序需进行大规模的处理，每月的用户数在百万级别，移动的数据量为数 TB。</span><span class="sxs-lookup"><span data-stu-id="78e52-190">[Large][large-pricing]: This pricing example represents an application meant for high scale, at the order of millions of users per month moving terabytes of data.</span></span> <span data-ttu-id="78e52-191">对于这种级别的使用量，必须将高级层 Web 应用部署在多个区域，并且需前置一个流量管理器。</span><span class="sxs-lookup"><span data-stu-id="78e52-191">At this level of usage high performance, premium tier web apps deployed in multiple regions fronted by traffic manager is required.</span></span> <span data-ttu-id="78e52-192">存储数据的存储空间、数据库和 CDN 均按 TB 级数据量进行配置。</span><span class="sxs-lookup"><span data-stu-id="78e52-192">Data consists of the following: storage, databases, and CDN, are configured for terabytes of data.</span></span>

## <a name="related-resources"></a><span data-ttu-id="78e52-193">相关资源</span><span class="sxs-lookup"><span data-stu-id="78e52-193">Related resources</span></span>

- <span data-ttu-id="78e52-194">[多区域 Web 应用程序的参考体系结构][multi-region-web-app]</span><span class="sxs-lookup"><span data-stu-id="78e52-194">[Reference Architecture for Multi-Region Web Application][multi-region-web-app]</span></span>
- <span data-ttu-id="78e52-195">[容器中的网上商店参考示例][microservices-ecommerce]</span><span class="sxs-lookup"><span data-stu-id="78e52-195">[eShop on Containers Reference Example][microservices-ecommerce]</span></span>

<!-- links -->
[architecture]: ./media/architecture-ecommerce-scenario.png
[small-pricing]: https://azure.com/e/90fbb6a661a04888a57322985f9b34ac
[medium-pricing]: https://azure.com/e/38d5d387e3234537b6859660db1c9973
[large-pricing]: https://azure.com/e/f07f99b6c3134803a14c9b43fcba3e2f
[app-service-reference-architecture]: ../../reference-architectures/app-service-web-app/basic-web-app.md
[availability]: /azure/architecture/checklist/availability
[circuit-breaker]: /azure/architecture/patterns/circuit-breaker
[design-patterns-availability]: /azure/architecture/patterns/category/availability
[design-patterns-resiliency]: /azure/architecture/patterns/category/resiliency
[design-patterns-scalability]: /azure/architecture/patterns/category/performance-scalability
[design-patterns-security]: /azure/architecture/patterns/category/security
[docs-application-insights]: /azure/application-insights/app-insights-overview
[docs-b2c]: /azure/active-directory-b2c/active-directory-b2c-overview
[docs-cdn]: /azure/cdn/cdn-overview
[docs-container-instances]: /azure/container-instances/
[docs-kubernetes-service]: /azure/aks/
[docs-functions]: /azure/azure-functions/functions-overview
[docs-redis-cache]: /azure/redis-cache/cache-overview
[docs-search]: /azure/search/search-what-is-azure-search
[docs-service-fabric]: /azure/service-fabric/
[docs-sentiment-analysis]: /azure/cognitive-services/welcome
[docs-sql-database]: /azure/sql-database/sql-database-technical-overview
[docs-storage-blobs]: /azure/storage/blobs/storage-blobs-introduction
[docs-storage-queues]: /azure/storage/queues/storage-queues-introduction
[docs-traffic-manager]: /azure/traffic-manager/traffic-manager-overview
[docs-webapps]: /azure/app-service/app-service-web-overview
[end-to-end-walkthrough]: https://github.com/Azure/fta-customerfacingapps/tree/master/ecommerce/articles
[microservices-ecommerce]: https://github.com/dotnet-architecture/eShopOnContainers
[multi-region-web-app]: /azure/architecture/reference-architectures/app-service-web-app/multi-region
[pci-dss-blueprint]: /azure/security/blueprints/payment-processing-blueprint
[resiliency-app-service]: /azure/architecture/checklist/resiliency-per-service#app-service
[resiliency]: /azure/architecture/checklist/resiliency
[scalability]: /azure/architecture/checklist/scalability
[secure-development]: https://www.microsoft.com/SDL/process/design.aspx
[sql-geo-replication]: /azure/sql-database/sql-database-geo-replication-overview
[storage-geo-redudancy]: /azure/storage/common/storage-redundancy-grs
