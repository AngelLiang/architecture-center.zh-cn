---
title: 静态内容托管模式
titleSuffix: Cloud Design Patterns
description: 将静态内容部署到基于云的存储服务，再由后者将它们直接传送给客户端。
keywords: 设计模式
author: dragon119
ms.date: 01/04/2019
ms.custom: seodec18
ms.openlocfilehash: cf4f65e935a01e4d84b3cc82b5779edb729bd80e
ms.sourcegitcommit: 036cd03c39f941567e0de4bae87f4e2aa8c84cf8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2019
ms.locfileid: "54058176"
---
# <a name="static-content-hosting-pattern"></a>静态内容托管模式

将静态内容部署到基于云的存储服务，再由后者将它们直接传送给客户端。 这样可降低对可能很昂贵的计算实例的需求。

## <a name="context-and-problem"></a>上下文和问题

Web 应用程序通常包括某些静态内容元素。 此静态内容可能包括 HTML 页面和其他资源，例如可供客户端使用的图像和文档，以 HTML 页面内容（例如内联图像、样式表、客户端 JavaScript 文件）或独立下载项目（例如 PDF 文档）的形式提供。

虽然 Web 服务器已经过优化，可以动态地进行呈现和输出缓存，但仍需处理下载静态内容的请求。 这样会占用那些通常可以有更好用途的处理周期。

## <a name="solution"></a>解决方案

在大多数云托管环境中，可以将应用程序的部分资源和静态页面放置到存储服务中。 存储服务可以为这些资源处理请求，减少那些处理其他 Web 请求的计算资源上的负载。 基于云的存储相对于计算实例来说，其成本通常要低得多。

将应用程序的一些部件托管在存储服务中时，主要考虑因素涉及应用程序的部署，以及保护那些不应提供给匿名用户的资源。

## <a name="issues-and-considerations"></a>问题和注意事项

在决定如何实现此模式时，请考虑以下几点：

- 托管的存储服务必须公开一个 HTTP 终结点，供下载静态资源的用户访问。 某些存储服务也支持 HTTPS，因此可以将资源托管在需要 SSL 的存储服务中。

- 为了尽量提高性能和可用性，可考虑使用内容分发网络 (CDN)，将存储容器的内容缓存在全球的多个数据中心。 但是，可能需要支付 CDN 使用费用。

- 通常情况下，存储帐户会默认进行异地复制，确保在发生可能影响某个数据中心的事件时能够复原。 这意味着，IP 地址可能会变，但 URL 会保持不变。

- 当某些内容位于存储帐户中，而另一些内容却位于托管计算实例中时，部署和更新应用程序会变得更具挑战性。 可能需要执行单独的部署，并对应用程序和内容进行版本控制以方便管理，尤其是在静态内容包含脚本文件或 UI 组件的情况下。 但是，如果只需更新静态资源，则可直接将其上传到存储帐户，不需重新部署应用程序包。

- 存储服务可能不支持使用自定义域名。 在这种情况下，需在链接中指定资源的完整 URL，因为其所在的域不同于动态生成的包含链接的内容所在的域。

- 必须将存储容器配置为允许公开读取访问，但不得将其配置为允许公开写入访问，防止用户上传内容。

- 考虑使用附属密钥或令牌来控制不允许匿名使用的资源的访问。 有关详细信息，请参阅[附属密钥模式](./valet-key.md)。

## <a name="when-to-use-this-pattern"></a>何时使用此模式

此模式适合用于：

- 尽量降低包含了一些静态资源的网站和应用程序的托管成本。

- 尽量降低只包含静态内容和资源的网站的托管成本。 如果托管提供商的存储系统允许，可以将全静态网站整个托管在存储帐户中。

- 公开在其他托管环境或本地服务器上运行的应用程序的静态资源和内容。

- 通过内容分发网络将内容置于多个地理区域，该网络将存储帐户的内容缓存在全球的多个数据中心。

- 监视成本和带宽使用情况。 使用单独的存储帐户来存储部分或全部静态内容，这样可以更容易地将此方面的成本与托管和运行时成本区分开来。

此模式在以下情况中可能不起作用：

- 应用程序需对静态内容进行某些处理，然后才能将其交付给客户端。 例如，可能需要向文档添加时间戳。

- 静态内容的量很小。 从单独的存储检索此内容的开销可能超过将其与计算资源分开获得的成本优势。

## <a name="example"></a>示例

Azure 存储允许直接从存储容器提供静态内容。 文件通过匿名访问请求来获取。 默认情况下，文件的 URL 在 `core.windows.net` 的子域中，例如 `https://contoso.z4.web.core.windows.net/image.png`。 可以配置自定义域名，然后使用 Azure CDN 通过 HTTPS 来访问文件。 有关详细信息，请参阅 [Azure 存储中的静态网站托管](/azure/storage/blobs/storage-blob-static-website)。

![直接从存储服务交付应用程序静态部分的内容](./_images/static-content-hosting-pattern.png)

静态网站托管功能使文件可供匿名访问。 若需控制访问文件的用户，可以将文件存储在 Azure Blob 存储中，然后生成[共享访问签名](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)来限制访问。

交付给客户端的页面中的链接必须指定资源的完整 URL。 如果通过附属密钥（例如共享访问签名）对资源进行保护，则 URL 中必须包含该签名。

[GitHub][sample-app] 上提供了一个示例应用程序，演示了如何使用外部存储来存储静态资源。 此示例使用的配置文件指定了用于存储静态内容的存储帐户和容器。

```xml
<Setting name="StaticContent.StorageConnectionString"
         value="UseDevelopmentStorage=true" />
<Setting name="StaticContent.Container" value="static-content" />
```

StaticContentHosting.Web 项目的 Settings.cs 文件中的 `Settings` 类包含用于提取这些值并生成一个字符串值（其中包含云存储帐户容器 URL）的方法。

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

StaticContentUrlHtmlHelper.cs 文件中的 `StaticContentUrlHtmlHelper` 类公开了一个名为 `StaticContentUrl` 的方法，该方法生成的 URL 包含云存储帐户的路径，但前提是传递给它的 URL 以 ASP.NET 根路径字符 (~) 开头。

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

Views\Home 文件夹中的 Index.cshtml 文件包含的图像元素使用 `StaticContentUrl` 方法为其 `src` 属性创建 URL。

```html
<img src="@Html.StaticContentUrl("~/media/orderedList1.png")" alt="Test Image" />
```

## <a name="related-patterns-and-guidance"></a>相关模式和指南

- [静态内容托管示例][sample-app]。 演示此模式的示例应用程序。
- [辅助密钥模式](./valet-key.md)。 如果不应将目标资源提供给匿名用户，则请使用此模式来限制直接访问。
- [Azure 上的无服务器 Web 应用程序](../reference-architectures/serverless/web-app.md)。 一个参考体系结构，可将静态网站托管功能与 Azure Functions 配合使用，以便实现无服务器 Web 应用。

[sample-app]: https://github.com/mspnp/cloud-design-patterns/tree/master/static-content-hosting
