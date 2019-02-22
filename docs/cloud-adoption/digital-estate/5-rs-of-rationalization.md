---
title: CAF：合理化的 5 个 R
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
description: 查看使数字资产合理化的可用选项。
author: BrianBlanchard
ms.date: 12/10/2018
ms.topic: guide
ms.openlocfilehash: ee196487e6f59b1e1b3c63bab9496cbbf805affd
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55897467"
---
# <a name="the-5-rs-of-rationalization"></a><span data-ttu-id="48c9e-103">合理化的 5 个 R</span><span class="sxs-lookup"><span data-stu-id="48c9e-103">The 5 Rs of rationalization</span></span>

<span data-ttu-id="48c9e-104">云合理化是指评估数字资产，确定在云中迁移或现代化每个资产的最佳方法的过程。</span><span class="sxs-lookup"><span data-stu-id="48c9e-104">Cloud Rationalization is the process of evaluating assets to determine the best approach to migrating or modernizing each asset in the cloud.</span></span> <span data-ttu-id="48c9e-105">有关合理化过程的详细信息，请参阅[什么是数字资产？](overview.md)</span><span class="sxs-lookup"><span data-stu-id="48c9e-105">For more information about the process of rationalization, see [What is a digital estate?](overview.md)</span></span>

<span data-ttu-id="48c9e-106">此处所列的“合理化的 5 个 R”描述合理化的最常用选项。</span><span class="sxs-lookup"><span data-stu-id="48c9e-106">The "5 Rs of rationalization" listed here describe the most common options for rationalization.</span></span>

## <a name="rehost"></a><span data-ttu-id="48c9e-107">重新托管</span><span class="sxs-lookup"><span data-stu-id="48c9e-107">Rehost</span></span>

<span data-ttu-id="48c9e-108">重新托管也称为“直接迁移”，可将当前状态资产转移到所选的云提供商，并且只需对整个体系结构进行极少量的更改。</span><span class="sxs-lookup"><span data-stu-id="48c9e-108">Also known as "lift and shift," a rehost effort moves the current state asset to the chosen cloud provider, with minimal change to overall architecture.</span></span>

<span data-ttu-id="48c9e-109">常见的推动因素可能包括：</span><span class="sxs-lookup"><span data-stu-id="48c9e-109">Common drivers could include:</span></span>

* <span data-ttu-id="48c9e-110">降低 CapEx</span><span class="sxs-lookup"><span data-stu-id="48c9e-110">Reduce CapEx</span></span>
* <span data-ttu-id="48c9e-111">释放数据中心空间</span><span class="sxs-lookup"><span data-stu-id="48c9e-111">Free up datacenter space</span></span>
* <span data-ttu-id="48c9e-112">快速实现云 ROI</span><span class="sxs-lookup"><span data-stu-id="48c9e-112">Quick cloud ROI</span></span>

<span data-ttu-id="48c9e-113">量化分析因素：</span><span class="sxs-lookup"><span data-stu-id="48c9e-113">Quantitative analysis factors:</span></span>

* <span data-ttu-id="48c9e-114">VM 大小（CPU、内存、存储）</span><span class="sxs-lookup"><span data-stu-id="48c9e-114">VM size (CPU, memory, storage)</span></span>
* <span data-ttu-id="48c9e-115">依赖项（网络流量）</span><span class="sxs-lookup"><span data-stu-id="48c9e-115">Dependencies (network traffic)</span></span>
* <span data-ttu-id="48c9e-116">资产兼容性</span><span class="sxs-lookup"><span data-stu-id="48c9e-116">Asset compatibility</span></span>

<span data-ttu-id="48c9e-117">质化分析因素：</span><span class="sxs-lookup"><span data-stu-id="48c9e-117">Qualitative analysis factors:</span></span>

* <span data-ttu-id="48c9e-118">变更容限</span><span class="sxs-lookup"><span data-stu-id="48c9e-118">Tolerance for change</span></span>
* <span data-ttu-id="48c9e-119">业务优先级</span><span class="sxs-lookup"><span data-stu-id="48c9e-119">Business priorities</span></span>
* <span data-ttu-id="48c9e-120">关键业务事件</span><span class="sxs-lookup"><span data-stu-id="48c9e-120">Critical business events</span></span>
* <span data-ttu-id="48c9e-121">过程依赖项</span><span class="sxs-lookup"><span data-stu-id="48c9e-121">Process dependencies</span></span>

## <a name="refactor"></a><span data-ttu-id="48c9e-122">重构</span><span class="sxs-lookup"><span data-stu-id="48c9e-122">Refactor</span></span>

<span data-ttu-id="48c9e-123">平台即服务 (PaaS) 选项可以降低许多应用程序相关的运营成本。</span><span class="sxs-lookup"><span data-stu-id="48c9e-123">Platform as a Service (PaaS) options can reduce operational costs associated with many applications.</span></span> <span data-ttu-id="48c9e-124">稍微重构应用程序以适应基于 PaaS 的模型可能是明智之举。</span><span class="sxs-lookup"><span data-stu-id="48c9e-124">It can be prudent to slightly refactor an application to fit a PaaS based model.</span></span>

<span data-ttu-id="48c9e-125">重构也指在应用程序开发过程中重构代码，使应用程序能够带来新的商机。</span><span class="sxs-lookup"><span data-stu-id="48c9e-125">Refactor also refers to the application development process of refactoring code to allow an application to deliver on new business opportunities.</span></span>

<span data-ttu-id="48c9e-126">常见的推动因素可能包括：</span><span class="sxs-lookup"><span data-stu-id="48c9e-126">Common drivers could include:</span></span>

* <span data-ttu-id="48c9e-127">更新过程更快、更短</span><span class="sxs-lookup"><span data-stu-id="48c9e-127">Faster, shorter, updates</span></span>
* <span data-ttu-id="48c9e-128">代码可移植性</span><span class="sxs-lookup"><span data-stu-id="48c9e-128">Code portability</span></span>
* <span data-ttu-id="48c9e-129">更高的云效率（资源、速度、成本）</span><span class="sxs-lookup"><span data-stu-id="48c9e-129">Greater cloud efficiency (resources, speed, cost)</span></span>

<span data-ttu-id="48c9e-130">量化分析因素：</span><span class="sxs-lookup"><span data-stu-id="48c9e-130">Quantitative analysis factors:</span></span>

* <span data-ttu-id="48c9e-131">应用程序资产大小（CPU、内存、存储）</span><span class="sxs-lookup"><span data-stu-id="48c9e-131">Application asset size (CPU, memory, storage)</span></span>
* <span data-ttu-id="48c9e-132">依赖项（网络流量）</span><span class="sxs-lookup"><span data-stu-id="48c9e-132">Dependencies (network traffic)</span></span>
* <span data-ttu-id="48c9e-133">用户流量（页面查看次数、在页面上停留的时间、加载时间）</span><span class="sxs-lookup"><span data-stu-id="48c9e-133">User traffic (page views, time on page, load time)</span></span>
* <span data-ttu-id="48c9e-134">开发平台（语言、数据平台、中间层服务）</span><span class="sxs-lookup"><span data-stu-id="48c9e-134">Development platform (languages, data platform, middle tier services)</span></span>

<span data-ttu-id="48c9e-135">质化分析因素：</span><span class="sxs-lookup"><span data-stu-id="48c9e-135">Qualitative analysis factors:</span></span>

* <span data-ttu-id="48c9e-136">持续业务投资</span><span class="sxs-lookup"><span data-stu-id="48c9e-136">Continued business investments</span></span>
* <span data-ttu-id="48c9e-137">突发选项/时间线</span><span class="sxs-lookup"><span data-stu-id="48c9e-137">Bursting options/timelines</span></span>
* <span data-ttu-id="48c9e-138">业务流程依赖项</span><span class="sxs-lookup"><span data-stu-id="48c9e-138">Business process dependencies</span></span>

## <a name="rearchitect"></a><span data-ttu-id="48c9e-139">重新架构</span><span class="sxs-lookup"><span data-stu-id="48c9e-139">Rearchitect</span></span>

<span data-ttu-id="48c9e-140">构建应用程序时所做的体系结构决策使得某些老旧的应用程序与云提供商不兼容。</span><span class="sxs-lookup"><span data-stu-id="48c9e-140">Some aging applications aren't compatible with cloud providers because of the architectural decisions made when the application was built.</span></span> <span data-ttu-id="48c9e-141">在这种情况下，应用程序可能需要在转换之前进行重新架构。</span><span class="sxs-lookup"><span data-stu-id="48c9e-141">In these cases, the application may need to be rearchitected prior to transformation.</span></span>

<span data-ttu-id="48c9e-142">在其他情况下，可以通过将解决方案重新架构为云原生的应用程序，使与云兼容的但不提供云原生优势的应用程序产生成本效益和运营效益。</span><span class="sxs-lookup"><span data-stu-id="48c9e-142">In other cases, applications that are cloud compatible, but not cloud native benefits, may produce cost efficiencies and operational efficiencies by rearchitecting the solution to be a cloud native application.</span></span>

<span data-ttu-id="48c9e-143">常见的推动因素可能包括：</span><span class="sxs-lookup"><span data-stu-id="48c9e-143">Common drivers could include:</span></span>

* <span data-ttu-id="48c9e-144">应用程序规模和灵活性</span><span class="sxs-lookup"><span data-stu-id="48c9e-144">Application scale and agility</span></span>
* <span data-ttu-id="48c9e-145">更易于采用的新云功能</span><span class="sxs-lookup"><span data-stu-id="48c9e-145">Easier adoption of new cloud capabilities</span></span>
* <span data-ttu-id="48c9e-146">混合技术堆栈</span><span class="sxs-lookup"><span data-stu-id="48c9e-146">Mix of technology stacks</span></span>

<span data-ttu-id="48c9e-147">量化分析因素：</span><span class="sxs-lookup"><span data-stu-id="48c9e-147">Quantitative analysis factors:</span></span>

* <span data-ttu-id="48c9e-148">应用程序资产大小（CPU、内存、存储）</span><span class="sxs-lookup"><span data-stu-id="48c9e-148">Application asset size (CPU, memory, storage)</span></span>
* <span data-ttu-id="48c9e-149">依赖项（网络流量）</span><span class="sxs-lookup"><span data-stu-id="48c9e-149">Dependencies (network traffic)</span></span>
* <span data-ttu-id="48c9e-150">用户流量（页面查看次数、在页面上停留的时间、加载时间）</span><span class="sxs-lookup"><span data-stu-id="48c9e-150">User traffic (page views, time on page, load time)</span></span>
* <span data-ttu-id="48c9e-151">开发平台（语言、数据平台、中间层服务）</span><span class="sxs-lookup"><span data-stu-id="48c9e-151">Development platform (languages, data platform, middle tier services)</span></span>

<span data-ttu-id="48c9e-152">质化分析因素：</span><span class="sxs-lookup"><span data-stu-id="48c9e-152">Qualitative analysis factors:</span></span>

* <span data-ttu-id="48c9e-153">业务投资不断增大</span><span class="sxs-lookup"><span data-stu-id="48c9e-153">Growing business investments</span></span>
* <span data-ttu-id="48c9e-154">运营成本</span><span class="sxs-lookup"><span data-stu-id="48c9e-154">Operational costs</span></span>
* <span data-ttu-id="48c9e-155">潜在的反馈循环和 DevOps 投资</span><span class="sxs-lookup"><span data-stu-id="48c9e-155">Potential feedback loops and DevOps investments</span></span>

## <a name="rebuild"></a><span data-ttu-id="48c9e-156">重新生成</span><span class="sxs-lookup"><span data-stu-id="48c9e-156">Rebuild</span></span>

<span data-ttu-id="48c9e-157">在某些情况下，为发展应用程序而必须克服的差距可能太大，使进一步的投资没有理由。</span><span class="sxs-lookup"><span data-stu-id="48c9e-157">In some scenarios, the delta that must be overcome to carry forward an application can be too large to justify further investment.</span></span> <span data-ttu-id="48c9e-158">用于满足业务需求，但现在不受支持或者不与目前业务流程执行方式相符的应用程序尤其如此。</span><span class="sxs-lookup"><span data-stu-id="48c9e-158">This is especially true for applications that used to meet the needs of the business, but are now unsupported or misaligned with how the business processes are executed today.</span></span> <span data-ttu-id="48c9e-159">在这种情况下，将会创建新的代码基来与[云原生](https://azure.microsoft.com/overview/cloudnative/)方法相符。</span><span class="sxs-lookup"><span data-stu-id="48c9e-159">In this case, a new code base is created to align with a [cloud native](https://azure.microsoft.com/overview/cloudnative/) approach.</span></span>

<span data-ttu-id="48c9e-160">常见的推动因素可能包括：</span><span class="sxs-lookup"><span data-stu-id="48c9e-160">Common drivers could include:</span></span>

* <span data-ttu-id="48c9e-161">加快创新</span><span class="sxs-lookup"><span data-stu-id="48c9e-161">Accelerate innovation</span></span>
* <span data-ttu-id="48c9e-162">更快地生成应用</span><span class="sxs-lookup"><span data-stu-id="48c9e-162">Build apps faster</span></span>
* <span data-ttu-id="48c9e-163">降低运营成本</span><span class="sxs-lookup"><span data-stu-id="48c9e-163">Reduce operational cost</span></span>

<span data-ttu-id="48c9e-164">量化分析因素：</span><span class="sxs-lookup"><span data-stu-id="48c9e-164">Quantitative analysis factors:</span></span>

* <span data-ttu-id="48c9e-165">应用程序资产大小（CPU、内存、存储）</span><span class="sxs-lookup"><span data-stu-id="48c9e-165">Application asset size (CPU, memory, storage)</span></span>
* <span data-ttu-id="48c9e-166">依赖项（网络流量）</span><span class="sxs-lookup"><span data-stu-id="48c9e-166">Dependencies (network traffic)</span></span>
* <span data-ttu-id="48c9e-167">用户流量（页面查看次数、在页面上停留的时间、加载时间）</span><span class="sxs-lookup"><span data-stu-id="48c9e-167">User traffic (page views, time on page, load time)</span></span>
* <span data-ttu-id="48c9e-168">开发平台（语言、数据平台、中间层服务）</span><span class="sxs-lookup"><span data-stu-id="48c9e-168">Development platform (languages, data platform, middle tier services)</span></span>

<span data-ttu-id="48c9e-169">质化分析因素：</span><span class="sxs-lookup"><span data-stu-id="48c9e-169">Qualitative analysis Factors:</span></span>

* <span data-ttu-id="48c9e-170">最终用户满意度不断下降</span><span class="sxs-lookup"><span data-stu-id="48c9e-170">Declining end user satisfaction</span></span>
* <span data-ttu-id="48c9e-171">按功能限制业务流程</span><span class="sxs-lookup"><span data-stu-id="48c9e-171">Business processes limited by functionality</span></span>
* <span data-ttu-id="48c9e-172">潜在的成本、体验或收入收益</span><span class="sxs-lookup"><span data-stu-id="48c9e-172">Potential cost, experience, or revenue gains</span></span>

## <a name="replace"></a><span data-ttu-id="48c9e-173">将</span><span class="sxs-lookup"><span data-stu-id="48c9e-173">Replace</span></span>

<span data-ttu-id="48c9e-174">通常使用当今可用的最佳技术和方法来实施解决方案。</span><span class="sxs-lookup"><span data-stu-id="48c9e-174">Solutions are generally implemented using the best technology and approach available at the time.</span></span> <span data-ttu-id="48c9e-175">在某些情况下，软件即服务 (SaaS) 应用程序可以满足托管应用程序所需的所有功能。</span><span class="sxs-lookup"><span data-stu-id="48c9e-175">In some cases, Software as a Service (SaaS) applications can meet all of the functionality required of the hosted application.</span></span> <span data-ttu-id="48c9e-176">在这种情况下，可以安排好要更换的工作负荷，并在转换过程中有效将其删除。</span><span class="sxs-lookup"><span data-stu-id="48c9e-176">In these scenarios, a workload could be slated for future replacement, effectively removing it from the transformation effort.</span></span>

<span data-ttu-id="48c9e-177">常见的推动因素可能包括：</span><span class="sxs-lookup"><span data-stu-id="48c9e-177">Common drivers could include:</span></span>

* <span data-ttu-id="48c9e-178">围绕行业最佳做法的标准化</span><span class="sxs-lookup"><span data-stu-id="48c9e-178">Standardize around industry-best practices</span></span>
* <span data-ttu-id="48c9e-179">加速业务流程驱动的方法的采用</span><span class="sxs-lookup"><span data-stu-id="48c9e-179">Accelerate adoption of business process driven approaches</span></span>
* <span data-ttu-id="48c9e-180">将开发投入重新分配到可产生竞争优势的应用程序</span><span class="sxs-lookup"><span data-stu-id="48c9e-180">Reallocate development investments into applications that create competitive differentiation or advantages</span></span>

<span data-ttu-id="48c9e-181">量化分析因素：</span><span class="sxs-lookup"><span data-stu-id="48c9e-181">Quantitative analysis factors:</span></span>

* <span data-ttu-id="48c9e-182">一般性运营成本削减</span><span class="sxs-lookup"><span data-stu-id="48c9e-182">General operating cost reductions</span></span>
* <span data-ttu-id="48c9e-183">VM 大小（CPU、内存、存储）</span><span class="sxs-lookup"><span data-stu-id="48c9e-183">VM size (CPU, memory, storage)</span></span>
* <span data-ttu-id="48c9e-184">依赖项（网络流量）</span><span class="sxs-lookup"><span data-stu-id="48c9e-184">Dependencies (network traffic)</span></span>
* <span data-ttu-id="48c9e-185">要淘汰的资产</span><span class="sxs-lookup"><span data-stu-id="48c9e-185">Assets to be retired</span></span>

<span data-ttu-id="48c9e-186">质化分析因素：</span><span class="sxs-lookup"><span data-stu-id="48c9e-186">Qualitative analysis factors:</span></span>

* <span data-ttu-id="48c9e-187">当前体系结构与 SaaS 解决方案的成本收益分析</span><span class="sxs-lookup"><span data-stu-id="48c9e-187">Cost benefit analysis of the current architecture versus a SaaS solution</span></span>
* <span data-ttu-id="48c9e-188">业务流程图</span><span class="sxs-lookup"><span data-stu-id="48c9e-188">Business process maps</span></span>
* <span data-ttu-id="48c9e-189">数据架构</span><span class="sxs-lookup"><span data-stu-id="48c9e-189">Data schemas</span></span>
* <span data-ttu-id="48c9e-190">自定义或自动化流程</span><span class="sxs-lookup"><span data-stu-id="48c9e-190">Custom or automated processes</span></span>

## <a name="next-steps"></a><span data-ttu-id="48c9e-191">后续步骤</span><span class="sxs-lookup"><span data-stu-id="48c9e-191">Next steps</span></span>

<span data-ttu-id="48c9e-192">总体而言，可将合理化的这 5 个 R 应用到数字资产，以便在每个应用程序的将来状态方面做出合理化决策。</span><span class="sxs-lookup"><span data-stu-id="48c9e-192">Collectively, these 5 Rs of Rationalization can be applied to a digital estate to make rationalization decisions regarding the future state of each application.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="48c9e-193">什么是数字资产？</span><span class="sxs-lookup"><span data-stu-id="48c9e-193">What is a digital estate?</span></span>](overview.md)
