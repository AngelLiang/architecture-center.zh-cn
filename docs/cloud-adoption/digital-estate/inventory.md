---
title: 收集数字资产的清单数据
titleSuffix: Enterprise Cloud Adoption
description: 如何创建数字资产的清单
author: BrianBlanchard
ms.date: 12/10/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.openlocfilehash: 5c2f270cf8de81c8a94d1f924f51611e657ed0ed
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54481790"
---
# <a name="enterprise-cloud-adoption-gather-inventory-data-for-a-digital-estate"></a><span data-ttu-id="05aa7-103">企业云的采用：收集数字资产的清单数据</span><span class="sxs-lookup"><span data-stu-id="05aa7-103">Enterprise Cloud Adoption: Gather inventory data for a digital estate</span></span>

<span data-ttu-id="05aa7-104">制定清单是[数字资产规划](overview.md)的第一步。</span><span class="sxs-lookup"><span data-stu-id="05aa7-104">Developing an inventory is the first step in [Digital Estate Planning](overview.md).</span></span> <span data-ttu-id="05aa7-105">在此过程中，将会收集支持特定业务功能的 IT 资产的列表，以便进行后续的分析和合理化操作。</span><span class="sxs-lookup"><span data-stu-id="05aa7-105">In this process, a list of IT assets that support specific business functions would be collected for later analysis and rationalization.</span></span> <span data-ttu-id="05aa7-106">本文假设自下而上的分析方法最符合规划需求。</span><span class="sxs-lookup"><span data-stu-id="05aa7-106">This article assumes that a bottom-up approach to analysis is most appropriate for planning needs.</span></span> <span data-ttu-id="05aa7-107">有关详细信息，请参阅[数字资产规划方法](./approach.md)。</span><span class="sxs-lookup"><span data-stu-id="05aa7-107">For more information, see [Approaches to digital estate planning](./approach.md).</span></span>

## <a name="how-can-a-digital-estate-be-inventoried"></a><span data-ttu-id="05aa7-108">如何清点数字资产？</span><span class="sxs-lookup"><span data-stu-id="05aa7-108">How can a digital estate be inventoried?</span></span>

<span data-ttu-id="05aa7-109">支持数字资产的清单因所需数字转换和相应的转换过程而异。</span><span class="sxs-lookup"><span data-stu-id="05aa7-109">The inventory supporting a digital estate changes depending on the desired Digital Transformation and corresponding Transformation Journey.</span></span>

- <span data-ttu-id="05aa7-110">操作转换：在操作转换过程中，通常建议通过扫描工具来收集清单，以便创建包含所有 VM 和服务器的集中式列表。</span><span class="sxs-lookup"><span data-stu-id="05aa7-110">Operational Transformation: During an operational transformation, it is often advised that the inventory be collected from scanning tools which can create a centralized list of all VMs and servers.</span></span> <span data-ttu-id="05aa7-111">某些工具也可创建网络映射和依赖项，这将有助于定义工作负荷匹配方式。</span><span class="sxs-lookup"><span data-stu-id="05aa7-111">Some tools can also create network mappings and dependencies, which will help define workload alignment.</span></span>

- <span data-ttu-id="05aa7-112">增量转换：增量转换的清单从客户开始。</span><span class="sxs-lookup"><span data-stu-id="05aa7-112">Incremental Transformation: Inventory for an incremental transformation begins with the customer.</span></span> <span data-ttu-id="05aa7-113">不妨从映射客户从开始到结束的体验着手。</span><span class="sxs-lookup"><span data-stu-id="05aa7-113">Mapping the customer experience from start to finish is a good place to begin.</span></span> <span data-ttu-id="05aa7-114">将该映射与应用程序、API、数据和其他资产匹配，即可创建详细的可供分析的清单。</span><span class="sxs-lookup"><span data-stu-id="05aa7-114">Aligning that map to applications, APIs, data, and other assets will create a detailed inventory for analysis.</span></span>

- <span data-ttu-id="05aa7-115">中断性转换：中断性转换专注于产品或服务。</span><span class="sxs-lookup"><span data-stu-id="05aa7-115">Disruptive Transformation: Disruptive transformation focuses on the product or service.</span></span> <span data-ttu-id="05aa7-116">通过这种方式获得的清单会包含中断市场和所需功能的机会的映射。</span><span class="sxs-lookup"><span data-stu-id="05aa7-116">From there an inventory would include a mapping of the opportunities to disrupt the market and the capabilities needed.</span></span>

## <a name="accuracy-and-completeness-of-an-inventory"></a><span data-ttu-id="05aa7-117">清单的准确性和完整性</span><span class="sxs-lookup"><span data-stu-id="05aa7-117">Accuracy and completeness of an inventory</span></span>

<span data-ttu-id="05aa7-118">首次迭代很少能够获得十分完整的清单。</span><span class="sxs-lookup"><span data-stu-id="05aa7-118">An inventory is seldom fully complete in its first iteration.</span></span> <span data-ttu-id="05aa7-119">强烈建议云策略团队的各个成员安排利益干系人和高级用户来验证清单。</span><span class="sxs-lookup"><span data-stu-id="05aa7-119">It is highly advised that various members of the Cloud Strategy team align stakeholders and power users to validate the inventory.</span></span> <span data-ttu-id="05aa7-120">可能情况下，可以使用网络和依赖项分析等其他工具来标识那些为其发送了流量但未在清单中的资产。</span><span class="sxs-lookup"><span data-stu-id="05aa7-120">When possible, additional tools like network and dependency analysis can be used to identify assets that are being sent traffic, but are not in the inventory.</span></span>

## <a name="next-steps"></a><span data-ttu-id="05aa7-121">后续步骤</span><span class="sxs-lookup"><span data-stu-id="05aa7-121">Next steps</span></span>

<span data-ttu-id="05aa7-122">清单在编译并验证完以后即可合理化。</span><span class="sxs-lookup"><span data-stu-id="05aa7-122">Once an inventory is compiled and validated, it can rationalized.</span></span> <span data-ttu-id="05aa7-123">清单合理化是进行数字资产规划的下一步。</span><span class="sxs-lookup"><span data-stu-id="05aa7-123">Inventory Rationalization is the next step to digital estate planning.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="05aa7-124">将数字资产合理化</span><span class="sxs-lookup"><span data-stu-id="05aa7-124">Rationalize the digital estate</span></span>](rationalize.md)