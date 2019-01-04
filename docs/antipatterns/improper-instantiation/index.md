---
title: 不当实例化反模式
titleSuffix: Performance antipatterns for cloud apps
description: 避免连续创建对象的新实例（本应创建一次然后共享）。
author: dragon119
ms.date: 06/05/2017
ms.custom: seodec18
ms.openlocfilehash: b92ae5f5e79a0ababf44d7aa2d771d4d72900cae
ms.sourcegitcommit: 680c9cef945dff6fee5e66b38e24f07804510fa9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2019
ms.locfileid: "54009912"
---
# <a name="improper-instantiation-antipattern"></a><span data-ttu-id="816e8-103">不当实例化反模式</span><span class="sxs-lookup"><span data-stu-id="816e8-103">Improper Instantiation antipattern</span></span>

<span data-ttu-id="816e8-104">连续创建对象的新实例（本应创建一次然后共享），可能会损害性能。</span><span class="sxs-lookup"><span data-stu-id="816e8-104">It can hurt performance to continually create new instances of an object that is meant to be created once and then shared.</span></span>

## <a name="problem-description"></a><span data-ttu-id="816e8-105">问题描述</span><span class="sxs-lookup"><span data-stu-id="816e8-105">Problem description</span></span>

<span data-ttu-id="816e8-106">许多库提供外部资源的抽象。</span><span class="sxs-lookup"><span data-stu-id="816e8-106">Many libraries provide abstractions of external resources.</span></span> <span data-ttu-id="816e8-107">在内部，这些类通常管理其自身与资源之间的连接，在客户端访问资源时充当中转站。 </span><span class="sxs-lookup"><span data-stu-id="816e8-107">Internally, these classes typically manage their own connections to the resource, acting as brokers that clients can use to access the resource.</span></span> <span data-ttu-id="816e8-108">下面是与 Azure 应用程序相关的中转站类的一些示例：</span><span class="sxs-lookup"><span data-stu-id="816e8-108">Here are some examples of broker classes that are relevant to Azure applications:</span></span>

- <span data-ttu-id="816e8-109">`System.Net.Http.HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="816e8-109">`System.Net.Http.HttpClient`.</span></span> <span data-ttu-id="816e8-110">使用 HTTP 来与 Web 服务通信。</span><span class="sxs-lookup"><span data-stu-id="816e8-110">Communicates with a web service using HTTP.</span></span>
- <span data-ttu-id="816e8-111">`Microsoft.ServiceBus.Messaging.QueueClient`。</span><span class="sxs-lookup"><span data-stu-id="816e8-111">`Microsoft.ServiceBus.Messaging.QueueClient`.</span></span> <span data-ttu-id="816e8-112">向服务总线队列发布和接收消息。</span><span class="sxs-lookup"><span data-stu-id="816e8-112">Posts and receives messages to a Service Bus queue.</span></span>
- <span data-ttu-id="816e8-113">`Microsoft.Azure.Documents.Client.DocumentClient`。</span><span class="sxs-lookup"><span data-stu-id="816e8-113">`Microsoft.Azure.Documents.Client.DocumentClient`.</span></span> <span data-ttu-id="816e8-114">连接到 Cosmos DB 实例</span><span class="sxs-lookup"><span data-stu-id="816e8-114">Connects to a Cosmos DB instance</span></span>
- <span data-ttu-id="816e8-115">`StackExchange.Redis.ConnectionMultiplexer`。</span><span class="sxs-lookup"><span data-stu-id="816e8-115">`StackExchange.Redis.ConnectionMultiplexer`.</span></span> <span data-ttu-id="816e8-116">连接到 Redis，包括 Azure Redis 缓存。</span><span class="sxs-lookup"><span data-stu-id="816e8-116">Connects to Redis, including Azure Redis Cache.</span></span>

<span data-ttu-id="816e8-117">这些类应该只实例化一次，并在应用程序的整个生存期内重复使用。</span><span class="sxs-lookup"><span data-stu-id="816e8-117">These classes are intended to be instantiated once and reused throughout the lifetime of an application.</span></span> <span data-ttu-id="816e8-118">但是，一个常见的误解是，只能在有必要时才获取这些类，并应快速将其释放。</span><span class="sxs-lookup"><span data-stu-id="816e8-118">However, it's a common misunderstanding that these classes should be acquired only as necessary and released quickly.</span></span> <span data-ttu-id="816e8-119">（此处正好列出了 .NET 库，但模式不是 .NET 特有的）。以下 ASP.NET 示例创建一个 `HttpClient` 实例来与远程服务通信。</span><span class="sxs-lookup"><span data-stu-id="816e8-119">(The ones listed here happen to be .NET libraries, but the pattern is not unique to .NET.) The following ASP.NET example creates an instance of `HttpClient` to communicate with a remote service.</span></span> <span data-ttu-id="816e8-120">可在[此处][sample-app]找到完整示例。</span><span class="sxs-lookup"><span data-stu-id="816e8-120">You can find the complete sample [here][sample-app].</span></span>

```csharp
public class NewHttpClientInstancePerRequestController : ApiController
{
    // This method creates a new instance of HttpClient and disposes it for every call to GetProductAsync.
    public async Task<Product> GetProductAsync(string id)
    {
        using (var httpClient = new HttpClient())
        {
            var hostName = HttpContext.Current.Request.Url.Host;
            var result = await httpClient.GetStringAsync(string.Format("http://{0}:8080/api/...", hostName));
            return new Product { Name = result };
        }
    }
}
```

<span data-ttu-id="816e8-121">在 Web 应用程序中，此技术不可缩放。</span><span class="sxs-lookup"><span data-stu-id="816e8-121">In a web application, this technique is not scalable.</span></span> <span data-ttu-id="816e8-122">它会为每个用户请求创建一个新的 `HttpClient` 对象。</span><span class="sxs-lookup"><span data-stu-id="816e8-122">A new `HttpClient` object is created for each user request.</span></span> <span data-ttu-id="816e8-123">在重负载下，Web 服务器可能会耗尽可用的套接字，从而导致 `SocketException` 错误。</span><span class="sxs-lookup"><span data-stu-id="816e8-123">Under heavy load, the web server may exhaust the number of available sockets, resulting in `SocketException` errors.</span></span>

<span data-ttu-id="816e8-124">此问题并不局限于 `HttpClient` 类。</span><span class="sxs-lookup"><span data-stu-id="816e8-124">This problem is not restricted to the `HttpClient` class.</span></span> <span data-ttu-id="816e8-125">用于包装资源或者创建开销较高的其他类可能导致类似问题。</span><span class="sxs-lookup"><span data-stu-id="816e8-125">Other classes that wrap resources or are expensive to create might cause similar issues.</span></span> <span data-ttu-id="816e8-126">以下示例创建 `ExpensiveToCreateService` 类的实例。</span><span class="sxs-lookup"><span data-stu-id="816e8-126">The following example creates an instances of the `ExpensiveToCreateService` class.</span></span> <span data-ttu-id="816e8-127">此处的问题不一定是套接字耗尽问题，而只是创建每个实例需要花费多长时间。</span><span class="sxs-lookup"><span data-stu-id="816e8-127">Here the issue is not necessarily socket exhaustion, but simply how long it takes to create each instance.</span></span> <span data-ttu-id="816e8-128">连续创建再销毁此类的实例可能对系统的可伸缩性造成不利影响。</span><span class="sxs-lookup"><span data-stu-id="816e8-128">Continually creating and destroying instances of this class might adversely affect the scalability of the system.</span></span>

```csharp
public class NewServiceInstancePerRequestController : ApiController
{
    public async Task<Product> GetProductAsync(string id)
    {
        var expensiveToCreateService = new ExpensiveToCreateService();
        return await expensiveToCreateService.GetProductByIdAsync(id);
    }
}

public class ExpensiveToCreateService
{
    public ExpensiveToCreateService()
    {
        // Simulate delay due to setup and configuration of ExpensiveToCreateService
        Thread.SpinWait(Int32.MaxValue / 100);
    }
    ...
}
```

## <a name="how-to-fix-the-problem"></a><span data-ttu-id="816e8-129">如何解决问题</span><span class="sxs-lookup"><span data-stu-id="816e8-129">How to fix the problem</span></span>

<span data-ttu-id="816e8-130">如果用于包装外部资源的类可共享且是线程安全的，可创建该类的共享单一实例或可重用实例池。</span><span class="sxs-lookup"><span data-stu-id="816e8-130">If the class that wraps the external resource is shareable and thread-safe, create a shared singleton instance or a pool of reusable instances of the class.</span></span>

<span data-ttu-id="816e8-131">以下示例使用静态 `HttpClient` 实例，因此在所有请求之间共享了连接。</span><span class="sxs-lookup"><span data-stu-id="816e8-131">The following example uses a static `HttpClient` instance, thus sharing the connection across all requests.</span></span>

```csharp
public class SingleHttpClientInstanceController : ApiController
{
    private static readonly HttpClient httpClient;

    static SingleHttpClientInstanceController()
    {
        httpClient = new HttpClient();
    }

    // This method uses the shared instance of HttpClient for every call to GetProductAsync.
    public async Task<Product> GetProductAsync(string id)
    {
        var hostName = HttpContext.Current.Request.Url.Host;
        var result = await httpClient.GetStringAsync(string.Format("http://{0}:8080/api/...", hostName));
        return new Product { Name = result };
    }
}
```

## <a name="considerations"></a><span data-ttu-id="816e8-132">注意事项</span><span class="sxs-lookup"><span data-stu-id="816e8-132">Considerations</span></span>

- <span data-ttu-id="816e8-133">此反模式的关键要素是重复创建和销毁可共享对象的实例。</span><span class="sxs-lookup"><span data-stu-id="816e8-133">The key element of this antipattern is repeatedly creating and destroying instances of a *shareable* object.</span></span> <span data-ttu-id="816e8-134">如果某个类不可共享（不是线程安全的），则此反模式不适用。</span><span class="sxs-lookup"><span data-stu-id="816e8-134">If a class is not shareable (not thread-safe), then this antipattern does not apply.</span></span>

- <span data-ttu-id="816e8-135">共享资源的类型可能决定了是要使用单一实例还是创建池。</span><span class="sxs-lookup"><span data-stu-id="816e8-135">The type of shared resource might dictate whether you should use a singleton or create a pool.</span></span> <span data-ttu-id="816e8-136">`HttpClient` 类旨在进行共享而不是入池。</span><span class="sxs-lookup"><span data-stu-id="816e8-136">The `HttpClient` class is designed to be shared rather than pooled.</span></span> <span data-ttu-id="816e8-137">其他对象可能支持入池，使系统能够将工作负荷分散到多个实例。</span><span class="sxs-lookup"><span data-stu-id="816e8-137">Other objects might support pooling, enabling the system to spread the workload across multiple instances.</span></span>

- <span data-ttu-id="816e8-138">在多个请求之间共享的对象必须是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="816e8-138">Objects that you share across multiple requests *must* be thread-safe.</span></span> <span data-ttu-id="816e8-139">应该以这种方式使用 `HttpClient` 类，但其他类可能不支持并发请求，因此需查看可用文档。</span><span class="sxs-lookup"><span data-stu-id="816e8-139">The `HttpClient` class is designed to be used in this manner, but other classes might not support concurrent requests, so check the available documentation.</span></span>

- <span data-ttu-id="816e8-140">请小心设置共享对象的属性，因为这会导致出现争用条件。</span><span class="sxs-lookup"><span data-stu-id="816e8-140">Be careful about setting properties on shared objects, as this can lead to race conditions.</span></span> <span data-ttu-id="816e8-141">例如，在每个请求前设置 `HttpClient` 类的 `DefaultRequestHeaders` 可产生争用条件。</span><span class="sxs-lookup"><span data-stu-id="816e8-141">For example, setting `DefaultRequestHeaders` on the `HttpClient` class before each request can create a race condition.</span></span> <span data-ttu-id="816e8-142">请将此类属性设置一次（例如，在启动期间），并创建单独的实例（如果需要配置不同的设置）。</span><span class="sxs-lookup"><span data-stu-id="816e8-142">Set such properties once (for example, during startup), and create separate instances if you need to configure different settings.</span></span>

- <span data-ttu-id="816e8-143">某些资源类型很消耗资源，必要时应将其放弃。</span><span class="sxs-lookup"><span data-stu-id="816e8-143">Some resource types are scarce and should not be held onto.</span></span> <span data-ttu-id="816e8-144">数据库连接就是一个例子。</span><span class="sxs-lookup"><span data-stu-id="816e8-144">Database connections are an example.</span></span> <span data-ttu-id="816e8-145">保留打开一个不需要的数据库连接可能导致其他并发用户无法访问数据库。</span><span class="sxs-lookup"><span data-stu-id="816e8-145">Holding an open database connection that is not required may prevent other concurrent users from gaining access to the database.</span></span>

- <span data-ttu-id="816e8-146">在 .NET Framework 中，与外部资源建立连接的许多对象是使用管理这些连接的其他类的静态工厂方法创建的。</span><span class="sxs-lookup"><span data-stu-id="816e8-146">In the .NET Framework, many objects that establish connections to external resources are created by using static factory methods of other classes that manage these connections.</span></span> <span data-ttu-id="816e8-147">应该保存并重复使用这些对象，而不是将其释放并重新创建。</span><span class="sxs-lookup"><span data-stu-id="816e8-147">These objects are intended to be saved and reused, rather than disposed and recreated.</span></span> <span data-ttu-id="816e8-148">例如，在 Azure 服务总线中，`QueueClient` 对象是通过 `MessagingFactory` 对象创建的。</span><span class="sxs-lookup"><span data-stu-id="816e8-148">For example, in Azure Service Bus, the `QueueClient` object is created through a `MessagingFactory` object.</span></span> <span data-ttu-id="816e8-149">在内部，`MessagingFactory` 管理连接。</span><span class="sxs-lookup"><span data-stu-id="816e8-149">Internally, the `MessagingFactory` manages connections.</span></span> <span data-ttu-id="816e8-150">有关详细信息，请参阅[有关使用服务总线消息传送提高性能的最佳做法][service-bus-messaging]。</span><span class="sxs-lookup"><span data-stu-id="816e8-150">For more information, see [Best Practices for performance improvements using Service Bus Messaging][service-bus-messaging].</span></span>

## <a name="how-to-detect-the-problem"></a><span data-ttu-id="816e8-151">如何检测问题</span><span class="sxs-lookup"><span data-stu-id="816e8-151">How to detect the problem</span></span>

<span data-ttu-id="816e8-152">此问题的症状包括吞吐量下降或错误率增多，以及以下一种或多种症状：</span><span class="sxs-lookup"><span data-stu-id="816e8-152">Symptoms of this problem include a drop in throughput or an increased error rate, along with one or more of the following:</span></span>

- <span data-ttu-id="816e8-153">表明套接字、数据库连接、文件句柄等资源耗尽的异常增多。</span><span class="sxs-lookup"><span data-stu-id="816e8-153">An increase in exceptions that indicate exhaustion of resources such as sockets, database connections, file handles, and so on.</span></span>
- <span data-ttu-id="816e8-154">内存用量和垃圾回收次数增多。</span><span class="sxs-lookup"><span data-stu-id="816e8-154">Increased memory use and garbage collection.</span></span>
- <span data-ttu-id="816e8-155">网络、磁盘或数据库活动增多。</span><span class="sxs-lookup"><span data-stu-id="816e8-155">An increase in network, disk, or database activity.</span></span>

<span data-ttu-id="816e8-156">可执行以下步骤来帮助识别此问题：</span><span class="sxs-lookup"><span data-stu-id="816e8-156">You can perform the following steps to help identify this problem:</span></span>

1. <span data-ttu-id="816e8-157">对生产系统执行进程监视，识别响应时间变长，或系统因缺少资源而发生故障的位置。</span><span class="sxs-lookup"><span data-stu-id="816e8-157">Performing process monitoring of the production system, to identify points when response times slow down or the system fails due to lack of resources.</span></span>
2. <span data-ttu-id="816e8-158">检查在这些位置捕获到的遥测数据，确定哪些操作可能在创建和销毁消耗资源的对象。</span><span class="sxs-lookup"><span data-stu-id="816e8-158">Examine the telemetry data captured at these points to determine which operations might be creating and destroying resource-consuming objects.</span></span>
3. <span data-ttu-id="816e8-159">在受控的测试环境而不是生产系统中针对每个可疑的操作执行负载测试。</span><span class="sxs-lookup"><span data-stu-id="816e8-159">Load test each suspected operation, in a controlled test environment rather than the production system.</span></span>
4. <span data-ttu-id="816e8-160">查看源代码，检查中转站对象的管理方式。</span><span class="sxs-lookup"><span data-stu-id="816e8-160">Review the source code and examine the how broker objects are managed.</span></span>

<span data-ttu-id="816e8-161">查看缓慢运行的，或者在系统承受负载时生成异常的操作的堆栈跟踪。</span><span class="sxs-lookup"><span data-stu-id="816e8-161">Look at stack traces for operations that are slow-running or that generate exceptions when the system is under load.</span></span> <span data-ttu-id="816e8-162">此信息可帮助识别这些操作如何利用资源。</span><span class="sxs-lookup"><span data-stu-id="816e8-162">This information can help to identify how these operations are utilizing resources.</span></span> <span data-ttu-id="816e8-163">异常可帮助确定错误是否因共享资源耗尽而导致。</span><span class="sxs-lookup"><span data-stu-id="816e8-163">Exceptions can help to determine whether errors are caused by shared resources being exhausted.</span></span>

## <a name="example-diagnosis"></a><span data-ttu-id="816e8-164">示例诊断</span><span class="sxs-lookup"><span data-stu-id="816e8-164">Example diagnosis</span></span>

<span data-ttu-id="816e8-165">以下部分将这些步骤应用到前面所述的示例应用程序。</span><span class="sxs-lookup"><span data-stu-id="816e8-165">The following sections apply these steps to the sample application described earlier.</span></span>

### <a name="identify-points-of-slow-down-or-failure"></a><span data-ttu-id="816e8-166">识别速度减慢或发生故障的位置</span><span class="sxs-lookup"><span data-stu-id="816e8-166">Identify points of slow down or failure</span></span>

<span data-ttu-id="816e8-167">下图显示使用 [New Relic APM][new-relic] 生成的结果，其中显示了响应时间不佳的操作。</span><span class="sxs-lookup"><span data-stu-id="816e8-167">The following image shows results generated using [New Relic APM][new-relic], showing operations that have a poor response time.</span></span> <span data-ttu-id="816e8-168">在本例中，`NewHttpClientInstancePerRequest` 控制器中的 `GetProductAsync` 方法值得进一步调查。</span><span class="sxs-lookup"><span data-stu-id="816e8-168">In this case, the `GetProductAsync` method in the `NewHttpClientInstancePerRequest` controller is worth investigating further.</span></span> <span data-ttu-id="816e8-169">请注意，在运行这些操作时，错误率也会增多。</span><span class="sxs-lookup"><span data-stu-id="816e8-169">Notice that the error rate also increases when these operations are running.</span></span>

![New Relic 监视仪表板，其中显示示例应用程序正在针对每个请求创建 HttpClient 对象的新实例][dashboard-new-HTTPClient-instance]

### <a name="examine-telemetry-data-and-find-correlations"></a><span data-ttu-id="816e8-171">检查遥测数据并找出关联</span><span class="sxs-lookup"><span data-stu-id="816e8-171">Examine telemetry data and find correlations</span></span>

<span data-ttu-id="816e8-172">下图显示了在上图的对应时段内，使用线程分析捕获的数据。</span><span class="sxs-lookup"><span data-stu-id="816e8-172">The next image shows data captured using thread profiling, over the same period corresponding as the previous image.</span></span> <span data-ttu-id="816e8-173">系统花费了大量的时间来打开套接字连接，而关闭这些连接和处理套接字异常所花费的时间甚至更长。</span><span class="sxs-lookup"><span data-stu-id="816e8-173">The system spends a significant time opening socket connections, and even more time closing them and handling socket exceptions.</span></span>

![New Relic 线程探查器，其中显示示例应用程序正在针对每个请求创建 HttpClient 对象的新实例][thread-profiler-new-HTTPClient-instance]

### <a name="performing-load-testing"></a><span data-ttu-id="816e8-175">执行负载测试</span><span class="sxs-lookup"><span data-stu-id="816e8-175">Performing load testing</span></span>

<span data-ttu-id="816e8-176">使用负载测试模拟用户可能执行的典型操作。</span><span class="sxs-lookup"><span data-stu-id="816e8-176">Use load testing to simulate the typical operations that users might perform.</span></span> <span data-ttu-id="816e8-177">这可以帮助识别系统的哪些部分在承受不同负载的情况下，受到了资源耗尽的影响。</span><span class="sxs-lookup"><span data-stu-id="816e8-177">This can help to identify which parts of a system suffer from resource exhaustion under varying loads.</span></span> <span data-ttu-id="816e8-178">请在受控环境而不是生产系统中执行这些测试。</span><span class="sxs-lookup"><span data-stu-id="816e8-178">Perform these tests in a controlled environment rather than the production system.</span></span> <span data-ttu-id="816e8-179">下图显示了在用户负载增大到 100 个并发用户时，`NewHttpClientInstancePerRequest` 控制器处理的请求吞吐量。</span><span class="sxs-lookup"><span data-stu-id="816e8-179">The following graph shows the throughput of requests handled by the `NewHttpClientInstancePerRequest` controller as the user load increases to 100 concurrent users.</span></span>

![针对每个请求创建 HttpClient 对象新实例的示例应用程序的吞吐量][throughput-new-HTTPClient-instance]

<span data-ttu-id="816e8-181">一开始，每秒处理的请求数量随着工作负荷的增大而增加。</span><span class="sxs-lookup"><span data-stu-id="816e8-181">At first, the volume of requests handled per second increases as the workload increases.</span></span> <span data-ttu-id="816e8-182">但是，在负载变为大约 30 个用户后，成功请求的数量达到限制，并且系统开始生成异常。</span><span class="sxs-lookup"><span data-stu-id="816e8-182">At about 30 users, however, the volume of successful requests reaches a limit, and the system starts to generate exceptions.</span></span> <span data-ttu-id="816e8-183">从那时起，异常数量随着用户负载的增大而逐渐增加。</span><span class="sxs-lookup"><span data-stu-id="816e8-183">From then on, the volume of exceptions gradually increases with the user load.</span></span>

<span data-ttu-id="816e8-184">负载测试将这些故障报告为 HTTP 500（内部服务器）错误。</span><span class="sxs-lookup"><span data-stu-id="816e8-184">The load test reported these failures as HTTP 500 (Internal Server) errors.</span></span> <span data-ttu-id="816e8-185">查看遥测数据发现，这些错误的原因是随着创建的 `HttpClient` 对象越来越多，系统逐渐耗尽套接字资源。</span><span class="sxs-lookup"><span data-stu-id="816e8-185">Reviewing the telemetry showed that these errors were caused by the system running out of socket resources, as more and more `HttpClient` objects were created.</span></span>

<span data-ttu-id="816e8-186">下图显示了针对一个创建自定义 `ExpensiveToCreateService` 对象的控制器执行的类似测试。</span><span class="sxs-lookup"><span data-stu-id="816e8-186">The next graph shows a similar test for a controller that creates the custom `ExpensiveToCreateService` object.</span></span>

![针对每个请求创建 ExpensiveToCreateService 新实例的示例应用程序的吞吐量][throughput-new-ExpensiveToCreateService-instance]

<span data-ttu-id="816e8-188">此时，控制器未生成任何异常，但吞吐量仍保持平稳状态，同时，平均响应时间以 20 为系数增加。</span><span class="sxs-lookup"><span data-stu-id="816e8-188">This time, the controller does not generate any exceptions, but throughput still reaches a plateau, while the average response time increases by a factor of 20.</span></span> <span data-ttu-id="816e8-189">（该图对响应时间和吞吐量使用了对数刻度。）遥测数据显示，创建 `ExpensiveToCreateService` 的新实例是问题的主要原因。</span><span class="sxs-lookup"><span data-stu-id="816e8-189">(The graph uses a logarithmic scale for response time and throughput.) Telemetry showed that creating new instances of the `ExpensiveToCreateService` was the main cause of the problem.</span></span>

### <a name="implement-the-solution-and-verify-the-result"></a><span data-ttu-id="816e8-190">实施解决方案并验证结果</span><span class="sxs-lookup"><span data-stu-id="816e8-190">Implement the solution and verify the result</span></span>

<span data-ttu-id="816e8-191">将 `GetProductAsync` 方法切换为共享单个 `HttpClient` 实例之后，第二次负载测试表明性能有所改进。</span><span class="sxs-lookup"><span data-stu-id="816e8-191">After switching the `GetProductAsync` method to share a single `HttpClient` instance, a second load test showed improved performance.</span></span> <span data-ttu-id="816e8-192">未报告任何错误，并且系统能够处理更高的负载：每秒最多可处理 500 个请求。</span><span class="sxs-lookup"><span data-stu-id="816e8-192">No errors were reported, and the system was able to handle an increasing load of up to 500 requests per second.</span></span> <span data-ttu-id="816e8-193">与前面的测试相比，平均响应时间下降了一半。</span><span class="sxs-lookup"><span data-stu-id="816e8-193">The average response time was cut in half, compared with the previous test.</span></span>

![针对每个请求重复使用 HttpClient 对象新实例的示例应用程序的吞吐量][throughput-single-HTTPClient-instance]

<span data-ttu-id="816e8-195">下图显示了堆栈跟踪遥测数据用于比较。</span><span class="sxs-lookup"><span data-stu-id="816e8-195">For comparison, the following image shows the stack trace telemetry.</span></span> <span data-ttu-id="816e8-196">此时，系统将大部分时间花费在执行实际工作上，而不是花费在打开和关闭套接字上。</span><span class="sxs-lookup"><span data-stu-id="816e8-196">This time, the system spends most of its time performing real work, rather than opening and closing sockets.</span></span>

![New Relic 线程探查器，其中显示示例应用程序正在针对所有请求创建 HttpClient 对象的单个实例][thread-profiler-single-HTTPClient-instance]

<span data-ttu-id="816e8-198">下图显示使用 `ExpensiveToCreateService` 对象的共享实例执行的类似负载测试。</span><span class="sxs-lookup"><span data-stu-id="816e8-198">The next graph shows a similar load test using a shared instance of the `ExpensiveToCreateService` object.</span></span> <span data-ttu-id="816e8-199">同样，处理的请求数量随着用户负载的增大而增加，同时，平均响应时间保持较低水平。</span><span class="sxs-lookup"><span data-stu-id="816e8-199">Again, the volume of handled requests increases in line with the user load, while the average response time remains low.</span></span>

![针对每个请求重复使用 HttpClient 对象新实例的示例应用程序的吞吐量][throughput-single-ExpensiveToCreateService-instance]

[sample-app]: https://github.com/mspnp/performance-optimization/tree/master/ImproperInstantiation
[service-bus-messaging]: /azure/service-bus-messaging/service-bus-performance-improvements
[new-relic]: https://newrelic.com/application-monitoring
[throughput-new-HTTPClient-instance]: _images/HttpClientInstancePerRequest.jpg
[dashboard-new-HTTPClient-instance]: _images/HttpClientInstancePerRequestWebTransactions.jpg
[thread-profiler-new-HTTPClient-instance]: _images/HttpClientInstancePerRequestThreadProfile.jpg
[throughput-new-ExpensiveToCreateService-instance]: _images/ServiceInstancePerRequest.jpg
[throughput-single-HTTPClient-instance]: _images/SingleHttpClientInstance.jpg
[throughput-single-ExpensiveToCreateService-instance]: _images/SingleServiceInstance.jpg
[thread-profiler-single-HTTPClient-instance]: _images/SingleHttpClientInstanceThreadProfile.jpg
