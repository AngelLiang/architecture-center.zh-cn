---
title: 云应用程序的性能反模式
titleSuffix: Azure Architecture Center
description: 可能导致可伸缩性问题的常见做法。
author: dragon119
ms.date: 06/05/2017
ms.custom: seodec18
ms.openlocfilehash: 9ce1177bac2c93139faf6bc757f2866d6b7eac2d
ms.sourcegitcommit: 680c9cef945dff6fee5e66b38e24f07804510fa9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2019
ms.locfileid: "54009723"
---
# <a name="performance-antipatterns-for-cloud-applications"></a><span data-ttu-id="39d50-103">云应用程序的性能反模式</span><span class="sxs-lookup"><span data-stu-id="39d50-103">Performance antipatterns for cloud applications</span></span>

<span data-ttu-id="39d50-104">性能反模式是指在应用程序承受压力时，可能导致可伸缩性问题的某种常见做法。</span><span class="sxs-lookup"><span data-stu-id="39d50-104">A *performance antipattern* is a common practice that is likely to cause scalability problems when an application is under pressure.</span></span>

<span data-ttu-id="39d50-105">下面是一个常见的情景：在测试期间，某个应用程序的行为正常。</span><span class="sxs-lookup"><span data-stu-id="39d50-105">Here is a common scenario: An application behaves well during performance testing.</span></span> <span data-ttu-id="39d50-106">然后，该应用程序被发布到生产环境，并开始处理实际工作负荷。</span><span class="sxs-lookup"><span data-stu-id="39d50-106">It's released to production, and begins to handle real workloads.</span></span> <span data-ttu-id="39d50-107">在这时起，它开始表现异常 &mdash; 拒绝用户请求、停滞或引发异常。</span><span class="sxs-lookup"><span data-stu-id="39d50-107">At that point, it starts to perform poorly &mdash; rejecting user requests, stalling, or throwing exceptions.</span></span> <span data-ttu-id="39d50-108">此时，开发团队面临两个问题：</span><span class="sxs-lookup"><span data-stu-id="39d50-108">The development team is then faced with two questions:</span></span>

- <span data-ttu-id="39d50-109">在测试期间为何未发现此行为？</span><span class="sxs-lookup"><span data-stu-id="39d50-109">Why didn't this behavior show up during testing?</span></span>
- <span data-ttu-id="39d50-110">如何解决？</span><span class="sxs-lookup"><span data-stu-id="39d50-110">How do we fix it?</span></span>

<span data-ttu-id="39d50-111">第一个问题的答案非常简单。</span><span class="sxs-lookup"><span data-stu-id="39d50-111">The answer to the first question is straightforward.</span></span> <span data-ttu-id="39d50-112">在测试环境中很难模拟真实的用户、他们的行为模式，以及他们可能执行的工作量。</span><span class="sxs-lookup"><span data-stu-id="39d50-112">It's very difficult in a test environment to simulate real users, their behavior patterns, and the volumes of work they might perform.</span></span> <span data-ttu-id="39d50-113">了解系统在承受负载时的行为方式的唯一有把握方法是在生产环境中对它进行观察。</span><span class="sxs-lookup"><span data-stu-id="39d50-113">The only completely sure way to understand how a system behaves under load is to observe it in production.</span></span> <span data-ttu-id="39d50-114">要澄清的一点是，我们并不建议跳过性能测试。</span><span class="sxs-lookup"><span data-stu-id="39d50-114">To be clear, we aren't suggesting that you should skip performance testing.</span></span> <span data-ttu-id="39d50-115">性能测试对于获得基准性能指标至关重要。</span><span class="sxs-lookup"><span data-stu-id="39d50-115">Performance tests are crucial for getting baseline performance metrics.</span></span> <span data-ttu-id="39d50-116">但是，必须准备好观察并纠正应用程序在实时系统中出现的性能问题。</span><span class="sxs-lookup"><span data-stu-id="39d50-116">But you must be prepared to observe and correct performance issues when they arise in the live system.</span></span>

<span data-ttu-id="39d50-117">至于第二个问题 - 如何解决的答案，就不是那么直接。</span><span class="sxs-lookup"><span data-stu-id="39d50-117">The answer to the second question, how to fix the problem, is less straightforward.</span></span> <span data-ttu-id="39d50-118">问题可能由或多或少的因素造成，有时，问题只会在特定的条件下出现。</span><span class="sxs-lookup"><span data-stu-id="39d50-118">Any number of factors might contribute, and sometimes the problem only manifests under certain circumstances.</span></span> <span data-ttu-id="39d50-119">检测和日志记录对于查找根本原因非常关键，但是，还必须知道要查看哪些信息。</span><span class="sxs-lookup"><span data-stu-id="39d50-119">Instrumentation and logging are key to finding the root cause, but you also have to know what to look for.</span></span>

<span data-ttu-id="39d50-120">根据与 Microsoft Azure 客户的互动，我们已识别了客户在生产环境中遇到的最常见性能问题。</span><span class="sxs-lookup"><span data-stu-id="39d50-120">Based on our engagements with Microsoft Azure customers, we've identified some of the most common performance issues that customers see in production.</span></span> <span data-ttu-id="39d50-121">对于每个反模式，我们介绍了通常出现该反模式的原因、该反模式的症状，以及解决问题的方法。</span><span class="sxs-lookup"><span data-stu-id="39d50-121">For each antipattern, we describe why the antipattern typically occurs, symptoms of the antipattern, and techniques for resolving the problem.</span></span> <span data-ttu-id="39d50-122">此外，我们还提供了示例代码用于演示对立模式和建议的解决方法。</span><span class="sxs-lookup"><span data-stu-id="39d50-122">We also provide sample code that illustrates both the antipattern and a suggested solution.</span></span>

<span data-ttu-id="39d50-123">阅读介绍时，其中一些反模式似乎很明显，但它们出现的次数比想象的更频繁。 </span><span class="sxs-lookup"><span data-stu-id="39d50-123">Some of these antipatterns may seem obvious when you read the descriptions, but they occur more often than you might think.</span></span> <span data-ttu-id="39d50-124">有时，应用程序继承了本地工作的设计，但无法在云中缩放。</span><span class="sxs-lookup"><span data-stu-id="39d50-124">Sometimes an application inherits a design that worked on-premises, but doesn't scale in the cloud.</span></span> <span data-ttu-id="39d50-125">应用程序的设计一开始可能非常干净，但随着新功能的添加，其中一种或多种反模式就会渗入。</span><span class="sxs-lookup"><span data-stu-id="39d50-125">Or an application might start with a very clean design, but as new features are added, one or more of these antipatterns creeps in.</span></span> <span data-ttu-id="39d50-126">不管怎样，本指南都可帮助你识别并解决这些反模式。</span><span class="sxs-lookup"><span data-stu-id="39d50-126">Regardless, this guide will help you to identify and fix these antipatterns.</span></span>

<span data-ttu-id="39d50-127">下面是我们已识别的反模式列表：</span><span class="sxs-lookup"><span data-stu-id="39d50-127">Here is the list of the antipatterns that we've identified:</span></span>

| <span data-ttu-id="39d50-128">反模式</span><span class="sxs-lookup"><span data-stu-id="39d50-128">Antipattern</span></span> | <span data-ttu-id="39d50-129">Description</span><span class="sxs-lookup"><span data-stu-id="39d50-129">Description</span></span> |
|-------------|-------------|
| <span data-ttu-id="39d50-130">[繁忙数据库][BusyDatabase]</span><span class="sxs-lookup"><span data-stu-id="39d50-130">[Busy Database][BusyDatabase]</span></span> | <span data-ttu-id="39d50-131">将过多的处理工作附加到数据存储。</span><span class="sxs-lookup"><span data-stu-id="39d50-131">Offloading too much processing to a data store.</span></span> |
| <span data-ttu-id="39d50-132">[繁忙前端][BusyFrontEnd]</span><span class="sxs-lookup"><span data-stu-id="39d50-132">[Busy Front End][BusyFrontEnd]</span></span> | <span data-ttu-id="39d50-133">将资源密集型任务转移到后台线程。</span><span class="sxs-lookup"><span data-stu-id="39d50-133">Moving resource-intensive tasks onto background threads.</span></span> |
| <span data-ttu-id="39d50-134">[琐碎 I/O][ChattyIO]</span><span class="sxs-lookup"><span data-stu-id="39d50-134">[Chatty I/O][ChattyIO]</span></span> | <span data-ttu-id="39d50-135">持续发送许多小型网络请求。</span><span class="sxs-lookup"><span data-stu-id="39d50-135">Continually sending many small network requests.</span></span> |
| <span data-ttu-id="39d50-136">[超量提取][ExtraneousFetching]</span><span class="sxs-lookup"><span data-stu-id="39d50-136">[Extraneous Fetching][ExtraneousFetching]</span></span> | <span data-ttu-id="39d50-137">检索的数据量超过需要，导致不必要的 I/O。</span><span class="sxs-lookup"><span data-stu-id="39d50-137">Retrieving more data than is needed, resulting in unnecessary I/O.</span></span> |
| <span data-ttu-id="39d50-138">[不当实例化][ImproperInstantiation]</span><span class="sxs-lookup"><span data-stu-id="39d50-138">[Improper Instantiation][ImproperInstantiation]</span></span> | <span data-ttu-id="39d50-139">反复创建和销毁原本应该共享并重复使用的对象。</span><span class="sxs-lookup"><span data-stu-id="39d50-139">Repeatedly creating and destroying objects that are designed to be shared and reused.</span></span> |
| <span data-ttu-id="39d50-140">[整体持久性][MonolithicPersistence]</span><span class="sxs-lookup"><span data-stu-id="39d50-140">[Monolithic Persistence][MonolithicPersistence]</span></span> | <span data-ttu-id="39d50-141">对采用截然不同的使用模式的数据使用相同的数据存储。</span><span class="sxs-lookup"><span data-stu-id="39d50-141">Using the same data store for data with very different usage patterns.</span></span> |
| <span data-ttu-id="39d50-142">[无缓存][NoCaching]</span><span class="sxs-lookup"><span data-stu-id="39d50-142">[No Caching][NoCaching]</span></span> | <span data-ttu-id="39d50-143">无法缓存数据。</span><span class="sxs-lookup"><span data-stu-id="39d50-143">Failing to cache data.</span></span> |
| <span data-ttu-id="39d50-144">[同步 I/O][SynchronousIO]</span><span class="sxs-lookup"><span data-stu-id="39d50-144">[Synchronous I/O][SynchronousIO]</span></span> | <span data-ttu-id="39d50-145">I/O 完成时阻塞调用线程。</span><span class="sxs-lookup"><span data-stu-id="39d50-145">Blocking the calling thread while I/O completes.</span></span> |

[BusyDatabase]: ./busy-database/index.md
[BusyFrontEnd]: ./busy-front-end/index.md
[ChattyIO]: ./chatty-io/index.md
[ExtraneousFetching]: ./extraneous-fetching/index.md
[ImproperInstantiation]: ./improper-instantiation/index.md
[MonolithicPersistence]: ./monolithic-persistence/index.md
[NoCaching]: ./no-caching/index.md
[SynchronousIO]: ./synchronous-io/index.md
