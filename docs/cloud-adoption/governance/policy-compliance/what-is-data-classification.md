---
title: CAF：什么是数据分类
description: 什么是数据分类？
author: BrianBlanchard
ms.date: 2/11/2019
ms.openlocfilehash: 07268e7242d92ac2581bf28b378a3c43d166620c
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900534"
---
<!-- markdownlint-disable MD026 -->

# <a name="what-is-data-classification"></a><span data-ttu-id="825fb-103">什么是数据分类？</span><span class="sxs-lookup"><span data-stu-id="825fb-103">What is data classification?</span></span>

<span data-ttu-id="825fb-104">本文是一篇介绍性文章，内容涉及数据分类常规主题。</span><span class="sxs-lookup"><span data-stu-id="825fb-104">This is an introductory article on the general topic of Data Classification.</span></span> <span data-ttu-id="825fb-105">所有的治理往往都是从数据分类开始。</span><span class="sxs-lookup"><span data-stu-id="825fb-105">Data classification is a very common starting point for all governance.</span></span>

## <a name="business-risks-and-governance"></a><span data-ttu-id="825fb-106">业务风险和治理</span><span class="sxs-lookup"><span data-stu-id="825fb-106">Business risks and governance</span></span>

<span data-ttu-id="825fb-107">在大多数组织中，投资治理的主要原因均可归纳为以下三个业务风险：</span><span class="sxs-lookup"><span data-stu-id="825fb-107">In most organizations, the primary reasons for investing in governance can be reduced to three business risks:</span></span>

* <span data-ttu-id="825fb-108">与数据泄露相关的责任</span><span class="sxs-lookup"><span data-stu-id="825fb-108">Liability associated with data breaches</span></span>
* <span data-ttu-id="825fb-109">故障导致业务中断</span><span class="sxs-lookup"><span data-stu-id="825fb-109">Interruption to the business from outages</span></span>
* <span data-ttu-id="825fb-110">计划外或意外支出</span><span class="sxs-lookup"><span data-stu-id="825fb-110">Unplanned or unexpected spending</span></span>

<span data-ttu-id="825fb-111">这三种业务风险可能衍生出许多其他风险。</span><span class="sxs-lookup"><span data-stu-id="825fb-111">There are many variants of these three business risks.</span></span> <span data-ttu-id="825fb-112">但它们是最常见的风险。</span><span class="sxs-lookup"><span data-stu-id="825fb-112">However, the tend to be the most common.</span></span>

## <a name="understand-then-mitigate"></a><span data-ttu-id="825fb-113">了解并缓解</span><span class="sxs-lookup"><span data-stu-id="825fb-113">Understand then mitigate</span></span>

<span data-ttu-id="825fb-114">在缓解风险之前，必须先了解风险。</span><span class="sxs-lookup"><span data-stu-id="825fb-114">Before any risk can be mitigated, it must be understood.</span></span> <span data-ttu-id="825fb-115">对于上面提到的第一种风险，先从数据分类开始了解。</span><span class="sxs-lookup"><span data-stu-id="825fb-115">In the case of data breach liability, that understanding starts with data classification.</span></span> <span data-ttu-id="825fb-116">数据分类是将元数据特征与数字财产中的每个资产相关联的过程，它识别与该资产关联的数据类型。</span><span class="sxs-lookup"><span data-stu-id="825fb-116">Data classification is the process of associating a meta data characteristic to every asset in a digital estate, which identifies the type of data associated with that asset.</span></span>

<span data-ttu-id="825fb-117">Microsoft 建议，任何已标识为要向云迁移或部署的潜在备选资产都应记录元数据，以记录数据分类、业务关键性和计费责任。</span><span class="sxs-lookup"><span data-stu-id="825fb-117">Microsoft suggests that any asset which has been identified as a potential candidate for migration or deployment to the cloud should have documented meta data to record the data classification, business criticality, and billing responsibility.</span></span> <span data-ttu-id="825fb-118">这三个分类点对于了解和缓解风险有很大帮助。</span><span class="sxs-lookup"><span data-stu-id="825fb-118">These three points of classification can go a long way to understanding and mitigating risks.</span></span>

## <a name="microsofts-data-classification"></a><span data-ttu-id="825fb-119">Microsoft 的数据分类</span><span class="sxs-lookup"><span data-stu-id="825fb-119">Microsoft's data classification</span></span>

<span data-ttu-id="825fb-120">下面是 Microsoft 使用的分类列表。</span><span class="sxs-lookup"><span data-stu-id="825fb-120">The following is a list of classifications Microsoft uses.</span></span> <span data-ttu-id="825fb-121">根据行业或现有安全要求，组织中可能已有数据分类标准。</span><span class="sxs-lookup"><span data-stu-id="825fb-121">Depending on your industry or existing security requirements, data classifications standards may already exist within your organization.</span></span> <span data-ttu-id="825fb-122">如果没有标准，建议你使用此示例分类，更好地了解你的数字财产和风险模式。</span><span class="sxs-lookup"><span data-stu-id="825fb-122">If no standard exists, we welcome you to use this sample classification, to help you better understand your digital estate and risk profile.</span></span>  

* <span data-ttu-id="825fb-123">**非业务：** 不属于 Microsoft 的个人生活数据</span><span class="sxs-lookup"><span data-stu-id="825fb-123">**Non-Business:** Data from your personal life that does not belong to Microsoft</span></span>
* <span data-ttu-id="825fb-124">**公共：** 可免费获取和批准供公众使用的业务数据</span><span class="sxs-lookup"><span data-stu-id="825fb-124">**Public:** Business data that is freely available and approved for public consumption</span></span>
* <span data-ttu-id="825fb-125">**常规：** 不面向公众的业务数据</span><span class="sxs-lookup"><span data-stu-id="825fb-125">**General:** Business data that is not meant for a public audience</span></span>
* <span data-ttu-id="825fb-126">**机密：** 如果过度共享，可能对 Microsoft 造成损害的业务数据</span><span class="sxs-lookup"><span data-stu-id="825fb-126">**Confidential:** Business data that could cause harm to Microsoft if over-shared</span></span>
* <span data-ttu-id="825fb-127">**高度机密：** 如果过度共享，会对 Microsoft 造成严重损害的业务数据</span><span class="sxs-lookup"><span data-stu-id="825fb-127">**Highly Confidential:** Business data that would cause extensive harm to Microsoft if over-shared</span></span>

## <a name="tagging-data-classification-in-azure"></a><span data-ttu-id="825fb-128">在 Azure 中标记数据分类</span><span class="sxs-lookup"><span data-stu-id="825fb-128">Tagging data classification in Azure</span></span>

<span data-ttu-id="825fb-129">每个云提供商都应提供记录任何资产元数据的机制。</span><span class="sxs-lookup"><span data-stu-id="825fb-129">Every cloud provider should offer a mechanism for recording metadata about any asset.</span></span> <span data-ttu-id="825fb-130">元数据对于管理云中的资产至关重要。</span><span class="sxs-lookup"><span data-stu-id="825fb-130">Metadata is vital to managing assets in the cloud.</span></span> <span data-ttu-id="825fb-131">就 Azure 而言，资源标记是元数据存储的推荐方法。</span><span class="sxs-lookup"><span data-stu-id="825fb-131">In the case of Azure, resource tags are the suggested approach for metadata storage.</span></span> <span data-ttu-id="825fb-132">有关 Azure 中资源标记的详细信息，请参阅[使用标记来组织 Azure 资源](/azure/azure-resource-manager/resource-group-using-tags)。</span><span class="sxs-lookup"><span data-stu-id="825fb-132">For additional information on resource tagging in Azure, see the article on [Using tags to organize your Azure resources](/azure/azure-resource-manager/resource-group-using-tags).</span></span>

## <a name="next-steps"></a><span data-ttu-id="825fb-133">后续步骤</span><span class="sxs-lookup"><span data-stu-id="825fb-133">Next steps</span></span>

<span data-ttu-id="825fb-134">在某个可操作的治理过程中应用数据分类。</span><span class="sxs-lookup"><span data-stu-id="825fb-134">Apply data classifications during one of the actionable governance journeys.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="825fb-135">开始可操作的治理过程</span><span class="sxs-lookup"><span data-stu-id="825fb-135">Begin an Actionable Governance Journeys</span></span>](../journeys/overview.md)
