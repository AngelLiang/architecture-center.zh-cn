---
title: 静态内容托管模式
titleSuffix: Cloud Design Patterns
description: 将静态内容部署到基于云的存储服务，再由后者将它们直接传送给客户端。
keywords: 设计模式
author: dragon119
ms.date: 01/04/2019
ms.topic: design-pattern
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 719f0221ecc8d52267cba3136eec20dadef30b99
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54483439"
---
# <a name="static-content-hosting-pattern"></a><span data-ttu-id="3d9af-104">静态内容托管模式</span><span class="sxs-lookup"><span data-stu-id="3d9af-104">Static Content Hosting pattern</span></span>

<span data-ttu-id="3d9af-105">将静态内容部署到基于云的存储服务，再由后者将它们直接传送给客户端。</span><span class="sxs-lookup"><span data-stu-id="3d9af-105">Deploy static content to a cloud-based storage service that can deliver them directly to the client.</span></span> <span data-ttu-id="3d9af-106">这样可降低对可能很昂贵的计算实例的需求。</span><span class="sxs-lookup"><span data-stu-id="3d9af-106">This can reduce the need for potentially expensive compute instances.</span></span>

## <a name="context-and-problem"></a><span data-ttu-id="3d9af-107">上下文和问题</span><span class="sxs-lookup"><span data-stu-id="3d9af-107">Context and problem</span></span>

<span data-ttu-id="3d9af-108">Web 应用程序通常包括某些静态内容元素。</span><span class="sxs-lookup"><span data-stu-id="3d9af-108">Web applications typically include some elements of static content.</span></span> <span data-ttu-id="3d9af-109">此静态内容可能包括 HTML 页面和其他资源，例如可供客户端使用的图像和文档，以 HTML 页面内容（例如内联图像、样式表、客户端 JavaScript 文件）或独立下载项目（例如 PDF 文档）的形式提供。</span><span class="sxs-lookup"><span data-stu-id="3d9af-109">This static content might include HTML pages and other resources such as images and documents that are available to the client, either as part of an HTML page (such as inline images, style sheets, and client-side JavaScript files) or as separate downloads (such as PDF documents).</span></span>

<span data-ttu-id="3d9af-110">虽然 Web 服务器已经过优化，可以动态地进行呈现和输出缓存，但仍需处理下载静态内容的请求。</span><span class="sxs-lookup"><span data-stu-id="3d9af-110">Although web servers are optimized for dynamic rendering and output caching, they still have to handle requests to download static content.</span></span> <span data-ttu-id="3d9af-111">这样会占用那些通常可以有更好用途的处理周期。</span><span class="sxs-lookup"><span data-stu-id="3d9af-111">This consumes processing cycles that could often be put to better use.</span></span>

## <a name="solution"></a><span data-ttu-id="3d9af-112">解决方案</span><span class="sxs-lookup"><span data-stu-id="3d9af-112">Solution</span></span>

<span data-ttu-id="3d9af-113">在大多数云托管环境中，可以将应用程序的部分资源和静态页面放置到存储服务中。</span><span class="sxs-lookup"><span data-stu-id="3d9af-113">In most cloud hosting environments, you can put some of an application's resources and static pages in a storage service.</span></span> <span data-ttu-id="3d9af-114">存储服务可以为这些资源处理请求，减少那些处理其他 Web 请求的计算资源上的负载。</span><span class="sxs-lookup"><span data-stu-id="3d9af-114">The storage service can serve requests for these resources, reducing load on the compute resources that handle other web requests.</span></span> <span data-ttu-id="3d9af-115">基于云的存储相对于计算实例来说，其成本通常要低得多。</span><span class="sxs-lookup"><span data-stu-id="3d9af-115">The cost for cloud-hosted storage is typically much less than for compute instances.</span></span>

<span data-ttu-id="3d9af-116">将应用程序的一些部件托管在存储服务中时，主要考虑因素涉及应用程序的部署，以及保护那些不应提供给匿名用户的资源。</span><span class="sxs-lookup"><span data-stu-id="3d9af-116">When hosting some parts of an application in a storage service, the main considerations are related to deployment of the application and to securing resources that aren't intended to be available to anonymous users.</span></span>

## <a name="issues-and-considerations"></a><span data-ttu-id="3d9af-117">问题和注意事项</span><span class="sxs-lookup"><span data-stu-id="3d9af-117">Issues and considerations</span></span>

<span data-ttu-id="3d9af-118">在决定如何实现此模式时，请考虑以下几点：</span><span class="sxs-lookup"><span data-stu-id="3d9af-118">Consider the following points when deciding how to implement this pattern:</span></span>

- <span data-ttu-id="3d9af-119">托管的存储服务必须公开一个 HTTP 终结点，供下载静态资源的用户访问。</span><span class="sxs-lookup"><span data-stu-id="3d9af-119">The hosted storage service must expose an HTTP endpoint that users can access to download the static resources.</span></span> <span data-ttu-id="3d9af-120">某些存储服务也支持 HTTPS，因此可以将资源托管在需要 SSL 的存储服务中。</span><span class="sxs-lookup"><span data-stu-id="3d9af-120">Some storage services also support HTTPS, so it's possible to host resources in storage services that require SSL.</span></span>

- <span data-ttu-id="3d9af-121">为了尽量提高性能和可用性，可考虑使用内容分发网络 (CDN)，将存储容器的内容缓存在全球的多个数据中心。</span><span class="sxs-lookup"><span data-stu-id="3d9af-121">For maximum performance and availability, consider using a content delivery network (CDN) to cache the contents of the storage container in multiple datacenters around the world.</span></span> <span data-ttu-id="3d9af-122">但是，可能需要支付 CDN 使用费用。</span><span class="sxs-lookup"><span data-stu-id="3d9af-122">However, you'll likely have to pay for using the CDN.</span></span>

- <span data-ttu-id="3d9af-123">通常情况下，存储帐户会默认进行异地复制，确保在发生可能影响某个数据中心的事件时能够复原。</span><span class="sxs-lookup"><span data-stu-id="3d9af-123">Storage accounts are often geo-replicated by default to provide resiliency against events that might affect a datacenter.</span></span> <span data-ttu-id="3d9af-124">这意味着，IP 地址可能会变，但 URL 会保持不变。</span><span class="sxs-lookup"><span data-stu-id="3d9af-124">This means that the IP address might change, but the URL will remain the same.</span></span>

- <span data-ttu-id="3d9af-125">当某些内容位于存储帐户中，而另一些内容却位于托管计算实例中时，部署和更新应用程序会变得更具挑战性。</span><span class="sxs-lookup"><span data-stu-id="3d9af-125">When some content is located in a storage account and other content is in a hosted compute instance, it becomes more challenging to deploy and update the application.</span></span> <span data-ttu-id="3d9af-126">可能需要执行单独的部署，并对应用程序和内容进行版本控制以方便管理，尤其是在静态内容包含脚本文件或 UI 组件的情况下。</span><span class="sxs-lookup"><span data-stu-id="3d9af-126">You might have to perform separate deployments, and version the application and content to manage it more easily&mdash;especially when the static content includes script files or UI components.</span></span> <span data-ttu-id="3d9af-127">但是，如果只需更新静态资源，则可直接将其上传到存储帐户，不需重新部署应用程序包。</span><span class="sxs-lookup"><span data-stu-id="3d9af-127">However, if only static resources have to be updated, they can simply be uploaded to the storage account without needing to redeploy the application package.</span></span>

- <span data-ttu-id="3d9af-128">存储服务可能不支持使用自定义域名。</span><span class="sxs-lookup"><span data-stu-id="3d9af-128">Storage services might not support the use of custom domain names.</span></span> <span data-ttu-id="3d9af-129">在这种情况下，需在链接中指定资源的完整 URL，因为其所在的域不同于动态生成的包含链接的内容所在的域。</span><span class="sxs-lookup"><span data-stu-id="3d9af-129">In this case it's necessary to specify the full URL of the resources in links because they'll be in a different domain from the dynamically-generated content containing the links.</span></span>

- <span data-ttu-id="3d9af-130">必须将存储容器配置为允许公开读取访问，但不得将其配置为允许公开写入访问，防止用户上传内容。</span><span class="sxs-lookup"><span data-stu-id="3d9af-130">The storage containers must be configured for public read access, but it's vital to ensure that they aren't configured for public write access to prevent users being able to upload content.</span></span>

- <span data-ttu-id="3d9af-131">考虑使用附属密钥或令牌来控制不允许匿名使用的资源的访问。</span><span class="sxs-lookup"><span data-stu-id="3d9af-131">Consider using a valet key or token to control access to resources that shouldn't be available anonymously.</span></span> <span data-ttu-id="3d9af-132">有关详细信息，请参阅[附属密钥模式](./valet-key.md)。</span><span class="sxs-lookup"><span data-stu-id="3d9af-132">See the [Valet Key pattern](./valet-key.md) for more information.</span></span>

## <a name="when-to-use-this-pattern"></a><span data-ttu-id="3d9af-133">何时使用此模式</span><span class="sxs-lookup"><span data-stu-id="3d9af-133">When to use this pattern</span></span>

<span data-ttu-id="3d9af-134">此模式适合用于：</span><span class="sxs-lookup"><span data-stu-id="3d9af-134">This pattern is useful for:</span></span>

- <span data-ttu-id="3d9af-135">尽量降低包含了一些静态资源的网站和应用程序的托管成本。</span><span class="sxs-lookup"><span data-stu-id="3d9af-135">Minimizing the hosting cost for websites and applications that contain some static resources.</span></span>

- <span data-ttu-id="3d9af-136">尽量降低只包含静态内容和资源的网站的托管成本。</span><span class="sxs-lookup"><span data-stu-id="3d9af-136">Minimizing the hosting cost for websites that consist of only static content and resources.</span></span> <span data-ttu-id="3d9af-137">如果托管提供商的存储系统允许，可以将全静态网站整个托管在存储帐户中。</span><span class="sxs-lookup"><span data-stu-id="3d9af-137">Depending on the capabilities of the hosting provider's storage system, it might be possible to entirely host a fully static website in a storage account.</span></span>

- <span data-ttu-id="3d9af-138">公开在其他托管环境或本地服务器上运行的应用程序的静态资源和内容。</span><span class="sxs-lookup"><span data-stu-id="3d9af-138">Exposing static resources and content for applications running in other hosting environments or on-premises servers.</span></span>

- <span data-ttu-id="3d9af-139">通过内容分发网络将内容置于多个地理区域，该网络将存储帐户的内容缓存在全球的多个数据中心。</span><span class="sxs-lookup"><span data-stu-id="3d9af-139">Locating content in more than one geographical area using a content delivery network that caches the contents of the storage account in multiple datacenters around the world.</span></span>

- <span data-ttu-id="3d9af-140">监视成本和带宽使用情况。</span><span class="sxs-lookup"><span data-stu-id="3d9af-140">Monitoring costs and bandwidth usage.</span></span> <span data-ttu-id="3d9af-141">使用单独的存储帐户来存储部分或全部静态内容，这样可以更容易地将此方面的成本与托管和运行时成本区分开来。</span><span class="sxs-lookup"><span data-stu-id="3d9af-141">Using a separate storage account for some or all of the static content allows the costs to be more easily separated from hosting and runtime costs.</span></span>

<span data-ttu-id="3d9af-142">此模式在以下情况中可能不起作用：</span><span class="sxs-lookup"><span data-stu-id="3d9af-142">This pattern might not be useful in the following situations:</span></span>

- <span data-ttu-id="3d9af-143">应用程序需对静态内容进行某些处理，然后才能将其交付给客户端。</span><span class="sxs-lookup"><span data-stu-id="3d9af-143">The application needs to perform some processing on the static content before delivering it to the client.</span></span> <span data-ttu-id="3d9af-144">例如，可能需要向文档添加时间戳。</span><span class="sxs-lookup"><span data-stu-id="3d9af-144">For example, it might be necessary to add a timestamp to a document.</span></span>

- <span data-ttu-id="3d9af-145">静态内容的量很小。</span><span class="sxs-lookup"><span data-stu-id="3d9af-145">The volume of static content is very small.</span></span> <span data-ttu-id="3d9af-146">从单独的存储检索此内容的开销可能超过将其与计算资源分开获得的成本优势。</span><span class="sxs-lookup"><span data-stu-id="3d9af-146">The overhead of retrieving this content from separate storage can outweigh the cost benefit of separating it out from the compute resource.</span></span>

## <a name="example"></a><span data-ttu-id="3d9af-147">示例</span><span class="sxs-lookup"><span data-stu-id="3d9af-147">Example</span></span>

<span data-ttu-id="3d9af-148">Azure 存储允许直接从存储容器提供静态内容。</span><span class="sxs-lookup"><span data-stu-id="3d9af-148">Azure Storage supports serving static content directly from a storage container.</span></span> <span data-ttu-id="3d9af-149">文件通过匿名访问请求来获取。</span><span class="sxs-lookup"><span data-stu-id="3d9af-149">Files are served through anonymous access requests.</span></span> <span data-ttu-id="3d9af-150">默认情况下，文件的 URL 在 `core.windows.net` 的子域中，例如 `https://contoso.z4.web.core.windows.net/image.png`。</span><span class="sxs-lookup"><span data-stu-id="3d9af-150">By default, files have a URL in a subdomain of `core.windows.net`, such as `https://contoso.z4.web.core.windows.net/image.png`.</span></span> <span data-ttu-id="3d9af-151">可以配置自定义域名，然后使用 Azure CDN 通过 HTTPS 来访问文件。</span><span class="sxs-lookup"><span data-stu-id="3d9af-151">You can configure a custom domain name, and use Azure CDN to access the files over HTTPS.</span></span> <span data-ttu-id="3d9af-152">有关详细信息，请参阅 [Azure 存储中的静态网站托管](/azure/storage/blobs/storage-blob-static-website)。</span><span class="sxs-lookup"><span data-stu-id="3d9af-152">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

![直接从存储服务交付应用程序静态部分的内容](./_images/static-content-hosting-pattern.png)

<span data-ttu-id="3d9af-154">静态网站托管功能使文件可供匿名访问。</span><span class="sxs-lookup"><span data-stu-id="3d9af-154">Static website hosting makes the files available for anonymous access.</span></span> <span data-ttu-id="3d9af-155">若需控制访问文件的用户，可以将文件存储在 Azure Blob 存储中，然后生成[共享访问签名](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)来限制访问。</span><span class="sxs-lookup"><span data-stu-id="3d9af-155">If you need to control who can access the files, you can store files in Azure blob storage and then generate [shared access signatures](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) to limit access.</span></span>

<span data-ttu-id="3d9af-156">交付给客户端的页面中的链接必须指定资源的完整 URL。</span><span class="sxs-lookup"><span data-stu-id="3d9af-156">The links in the pages delivered to the client must specify the full URL of the resource.</span></span> <span data-ttu-id="3d9af-157">如果通过附属密钥（例如共享访问签名）对资源进行保护，则 URL 中必须包含该签名。</span><span class="sxs-lookup"><span data-stu-id="3d9af-157">If the resource is protected with a valet key, such as a shared access signature, this signature must be included in the URL.</span></span>

<span data-ttu-id="3d9af-158">[GitHub][sample-app] 上提供了一个示例应用程序，演示了如何使用外部存储来存储静态资源。</span><span class="sxs-lookup"><span data-stu-id="3d9af-158">A sample application that demonstrates using external storage for static resources is available on [GitHub][sample-app].</span></span> <span data-ttu-id="3d9af-159">此示例使用的配置文件指定了用于存储静态内容的存储帐户和容器。</span><span class="sxs-lookup"><span data-stu-id="3d9af-159">This sample uses configuration files to specify the storage account and container that holds the static content.</span></span>

```xml
<Setting name="StaticContent.StorageConnectionString"
         value="UseDevelopmentStorage=true" />
<Setting name="StaticContent.Container" value="static-content" />
```

<span data-ttu-id="3d9af-160">StaticContentHosting.Web 项目的 Settings.cs 文件中的 `Settings` 类包含用于提取这些值并生成一个字符串值（其中包含云存储帐户容器 URL）的方法。</span><span class="sxs-lookup"><span data-stu-id="3d9af-160">The `Settings` class in the file Settings.cs of the StaticContentHosting.Web project contains methods to extract these values and build a string value containing the cloud storage account container URL.</span></span>

```csharp
public class Settings
{
  public static string StaticContentStorageConnectionString {
    get
    {
      return RoleEnvironment.GetConfigurationSettingValue(
                              "StaticContent.StorageConnectionString");
    }
  }

  public static string StaticContentContainer
  {
    get
    {
      return RoleEnvironment.GetConfigurationSettingValue("StaticContent.Container");
    }
  }

  public static string StaticContentBaseUrl
  {
    get
    {
      var account = CloudStorageAccount.Parse(StaticContentStorageConnectionString);

      return string.Format("{0}/{1}", account.BlobEndpoint.ToString().TrimEnd('/'),
                                      StaticContentContainer.TrimStart('/'));
    }
  }
}
```

<span data-ttu-id="3d9af-161">StaticContentUrlHtmlHelper.cs 文件中的 `StaticContentUrlHtmlHelper` 类公开了一个名为 `StaticContentUrl` 的方法，该方法生成的 URL 包含云存储帐户的路径，但前提是传递给它的 URL 以 ASP.NET 根路径字符 (~) 开头。</span><span class="sxs-lookup"><span data-stu-id="3d9af-161">The `StaticContentUrlHtmlHelper` class in the file StaticContentUrlHtmlHelper.cs exposes a method named `StaticContentUrl` that generates a URL containing the path to the cloud storage account if the URL passed to it starts with the ASP.NET root path character (~).</span></span>

```csharp
public static class StaticContentUrlHtmlHelper
{
  public static string StaticContentUrl(this HtmlHelper helper, string contentPath)
  {
    if (contentPath.StartsWith("~"))
    {
      contentPath = contentPath.Substring(1);
    }

    contentPath = string.Format("{0}/{1}", Settings.StaticContentBaseUrl.TrimEnd('/'),
                                contentPath.TrimStart('/'));

    var url = new UrlHelper(helper.ViewContext.RequestContext);

    return url.Content(contentPath);
  }
}
```

<span data-ttu-id="3d9af-162">Views\Home 文件夹中的 Index.cshtml 文件包含的图像元素使用 `StaticContentUrl` 方法为其 `src` 属性创建 URL。</span><span class="sxs-lookup"><span data-stu-id="3d9af-162">The file Index.cshtml in the Views\Home folder contains an image element that uses the `StaticContentUrl` method to create the URL for its `src` attribute.</span></span>

```html
<img src="@Html.StaticContentUrl("~/media/orderedList1.png")" alt="Test Image" />
```

## <a name="related-patterns-and-guidance"></a><span data-ttu-id="3d9af-163">相关模式和指南</span><span class="sxs-lookup"><span data-stu-id="3d9af-163">Related patterns and guidance</span></span>

- <span data-ttu-id="3d9af-164">[静态内容托管示例][sample-app]。</span><span class="sxs-lookup"><span data-stu-id="3d9af-164">[Static Content Hosting sample][sample-app].</span></span> <span data-ttu-id="3d9af-165">演示此模式的示例应用程序。</span><span class="sxs-lookup"><span data-stu-id="3d9af-165">A sample application that demonstrates this pattern.</span></span>
- <span data-ttu-id="3d9af-166">[辅助密钥模式](./valet-key.md)。</span><span class="sxs-lookup"><span data-stu-id="3d9af-166">[Valet Key pattern](./valet-key.md).</span></span> <span data-ttu-id="3d9af-167">如果不应将目标资源提供给匿名用户，则请使用此模式来限制直接访问。</span><span class="sxs-lookup"><span data-stu-id="3d9af-167">If the target resources aren't supposed to be available to anonymous users, use this pattern to restrict direct access.</span></span>
- <span data-ttu-id="3d9af-168">[Azure 上的无服务器 Web 应用程序](../reference-architectures/serverless/web-app.md)。</span><span class="sxs-lookup"><span data-stu-id="3d9af-168">[Serverless web application on Azure](../reference-architectures/serverless/web-app.md).</span></span> <span data-ttu-id="3d9af-169">一个参考体系结构，可将静态网站托管功能与 Azure Functions 配合使用，以便实现无服务器 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="3d9af-169">A reference architecture that uses static website hosting with Azure Functions to implement a serverless web app.</span></span>

[sample-app]: https://github.com/mspnp/cloud-design-patterns/tree/master/static-content-hosting
