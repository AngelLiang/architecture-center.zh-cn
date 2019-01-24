---
title: 基本 Web 应用程序
titleSuffix: Azure Reference Architectures
description: 建议用于 Azure 中运行的基本 Web 应用程序的体系结构。
author: MikeWasson
ms.date: 12/12/2017
ms.topic: reference-architecture
ms.service: architecture-center
ms.subservice: reference-architecture
ms.custom: seodec18
ms.openlocfilehash: 95f634284fe821386704174894a85a4dbca815f7
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54485071"
---
# <a name="run-a-basic-web-application-in-azure"></a><span data-ttu-id="44da2-103">在 Azure 中运行基本的 Web 应用程序</span><span class="sxs-lookup"><span data-stu-id="44da2-103">Run a basic web application in Azure</span></span>

<span data-ttu-id="44da2-104">此参考体系结构演示使用 [Azure 应用服务][app-service]和 [Azure SQL 数据库][sql-db]的 Web 应用程序适用的成熟做法。</span><span class="sxs-lookup"><span data-stu-id="44da2-104">This reference architecture shows proven practices for a web application that uses [Azure App Service][app-service] and [Azure SQL Database][sql-db].</span></span> <span data-ttu-id="44da2-105">[**部署此解决方案**](#deploy-the-solution)。</span><span class="sxs-lookup"><span data-stu-id="44da2-105">[**Deploy this solution**](#deploy-the-solution).</span></span>

![适用于 Azure 中的基本 Web 应用程序的参考体系结构](./images/basic-web-app.png)

<span data-ttu-id="44da2-107">下载此体系结构的 [Visio 文件][visio-download]。</span><span class="sxs-lookup"><span data-stu-id="44da2-107">*Download a [Visio file][visio-download] of this architecture.*</span></span>

## <a name="architecture"></a><span data-ttu-id="44da2-108">体系结构</span><span class="sxs-lookup"><span data-stu-id="44da2-108">Architecture</span></span>

> [!NOTE]
> <span data-ttu-id="44da2-109">此体系结构不侧重于应用程序开发，也不假设采用任何特定的应用程序框架。</span><span class="sxs-lookup"><span data-stu-id="44da2-109">This architecture does not focus on application development, and does not assume any particular application framework.</span></span> <span data-ttu-id="44da2-110">目标是让读者了解如何将各种 Azure 服务拟合在一起。</span><span class="sxs-lookup"><span data-stu-id="44da2-110">The goal is to understand how various Azure services fit together.</span></span>
>

<span data-ttu-id="44da2-111">此体系结构具有以下组件：</span><span class="sxs-lookup"><span data-stu-id="44da2-111">The architecture has the following components:</span></span>

- <span data-ttu-id="44da2-112">资源组。</span><span class="sxs-lookup"><span data-stu-id="44da2-112">**Resource group**.</span></span> <span data-ttu-id="44da2-113">[资源组](/azure/azure-resource-manager/resource-group-overview)是 Azure 资源的逻辑容器。</span><span class="sxs-lookup"><span data-stu-id="44da2-113">A [resource group](/azure/azure-resource-manager/resource-group-overview) is a logical container for Azure resources.</span></span>

- <span data-ttu-id="44da2-114">**应用服务应用**。</span><span class="sxs-lookup"><span data-stu-id="44da2-114">**App Service app**.</span></span> <span data-ttu-id="44da2-115">[Azure 应用服务][app-service]是用于创建和部署云应用程序的完全托管平台。</span><span class="sxs-lookup"><span data-stu-id="44da2-115">[Azure App Service][app-service] is a fully managed platform for creating and deploying cloud applications.</span></span>

- <span data-ttu-id="44da2-116">**应用服务计划**。</span><span class="sxs-lookup"><span data-stu-id="44da2-116">**App Service plan**.</span></span> <span data-ttu-id="44da2-117">[应用服务计划][app-service-plans]提供用于承载应用的托管虚拟机 (VM)。</span><span class="sxs-lookup"><span data-stu-id="44da2-117">An [App Service plan][app-service-plans] provides the managed virtual machines (VMs) that host your app.</span></span> <span data-ttu-id="44da2-118">与某个计划关联的所有应用在同一个 VM 实例上运行。</span><span class="sxs-lookup"><span data-stu-id="44da2-118">All apps associated with a plan run on the same VM instances.</span></span>

- <span data-ttu-id="44da2-119">**部署槽位**。</span><span class="sxs-lookup"><span data-stu-id="44da2-119">**Deployment slots**.</span></span>  <span data-ttu-id="44da2-120">使用[部署槽位][deployment-slots]可以分阶段完成部署，然后将阶段部署（过渡部署）交换为生产部署。</span><span class="sxs-lookup"><span data-stu-id="44da2-120">A [deployment slot][deployment-slots] lets you stage a deployment and then swap it with the production deployment.</span></span> <span data-ttu-id="44da2-121">这样，便可以避免直接部署到生产环境。</span><span class="sxs-lookup"><span data-stu-id="44da2-121">That way, you avoid deploying directly into production.</span></span> <span data-ttu-id="44da2-122">有关具体的建议，请参阅[可管理性](#manageability-considerations)部分。</span><span class="sxs-lookup"><span data-stu-id="44da2-122">See the [Manageability](#manageability-considerations) section for specific recommendations.</span></span>

- <span data-ttu-id="44da2-123">**IP 地址**。</span><span class="sxs-lookup"><span data-stu-id="44da2-123">**IP address**.</span></span> <span data-ttu-id="44da2-124">应用服务应用具有一个公共 IP 地址和域名。</span><span class="sxs-lookup"><span data-stu-id="44da2-124">The App Service app has a public IP address and a domain name.</span></span> <span data-ttu-id="44da2-125">域名是 `azurewebsites.net` 的子域，例如 `contoso.azurewebsites.net`。</span><span class="sxs-lookup"><span data-stu-id="44da2-125">The domain name is a subdomain of `azurewebsites.net`, such as `contoso.azurewebsites.net`.</span></span>

- <span data-ttu-id="44da2-126">**Azure DNS**。</span><span class="sxs-lookup"><span data-stu-id="44da2-126">**Azure DNS**.</span></span> <span data-ttu-id="44da2-127">[Azure DNS][azure-dns] 是 DNS 域的托管服务，它使用 Microsoft Azure 基础结构提供名称解析。</span><span class="sxs-lookup"><span data-stu-id="44da2-127">[Azure DNS][azure-dns] is a hosting service for DNS domains, providing name resolution using Microsoft Azure infrastructure.</span></span> <span data-ttu-id="44da2-128">通过在 Azure 中托管域，可以使用与其他 Azure 服务相同的凭据、API、工具和计费来管理 DNS 记录。</span><span class="sxs-lookup"><span data-stu-id="44da2-128">By hosting your domains in Azure, you can manage your DNS records using the same credentials, APIs, tools, and billing as your other Azure services.</span></span> <span data-ttu-id="44da2-129">若要使用自定义域名（例如 `contoso.com`），请创建可将自定义域名映射到 IP 地址的 DNS 记录。</span><span class="sxs-lookup"><span data-stu-id="44da2-129">To use a custom domain name (such as `contoso.com`) create DNS records that map the custom domain name to the IP address.</span></span> <span data-ttu-id="44da2-130">有关详细信息，请参阅[在 Azure 应用服务中配置自定义域名][custom-domain-name]。</span><span class="sxs-lookup"><span data-stu-id="44da2-130">For more information, see [Configure a custom domain name in Azure App Service][custom-domain-name].</span></span>

- <span data-ttu-id="44da2-131">**Azure SQL 数据库**。</span><span class="sxs-lookup"><span data-stu-id="44da2-131">**Azure SQL Database**.</span></span> <span data-ttu-id="44da2-132">[SQL 数据库][sql-db]是云中的关系数据库即服务。</span><span class="sxs-lookup"><span data-stu-id="44da2-132">[SQL Database][sql-db] is a relational database-as-a-service in the cloud.</span></span> <span data-ttu-id="44da2-133">SQL 数据库与 Microsoft SQL Server 数据库引擎共享其代码库。</span><span class="sxs-lookup"><span data-stu-id="44da2-133">SQL Database shares its code base with the Microsoft SQL Server database engine.</span></span> <span data-ttu-id="44da2-134">也可使用 [Azure Database for MySQL](/azure/mysql) 或 [Azure Database for PostgreSQL](/azure/postgresql)，具体取决于应用程序要求。</span><span class="sxs-lookup"><span data-stu-id="44da2-134">Depending on your application requirements, you can also use [Azure Database for MySQL](/azure/mysql) or [Azure Database for PostgreSQL](/azure/postgresql).</span></span> <span data-ttu-id="44da2-135">这些是完全托管的数据库服务，分别基于开源 MySQL 服务器和 Postgres 数据库引擎。</span><span class="sxs-lookup"><span data-stu-id="44da2-135">These are fully managed database services, based on the open source MySQL Server and Postgres database engines, respectively.</span></span>

- <span data-ttu-id="44da2-136">**逻辑服务器**。</span><span class="sxs-lookup"><span data-stu-id="44da2-136">**Logical server**.</span></span> <span data-ttu-id="44da2-137">在 Azure SQL 数据库中，逻辑服务器承载你的数据库。</span><span class="sxs-lookup"><span data-stu-id="44da2-137">In Azure SQL Database, a logical server hosts your databases.</span></span> <span data-ttu-id="44da2-138">可为每个逻辑服务器创建多个数据库。</span><span class="sxs-lookup"><span data-stu-id="44da2-138">You can create multiple databases per logical server.</span></span>

- <span data-ttu-id="44da2-139">**Azure 存储**。</span><span class="sxs-lookup"><span data-stu-id="44da2-139">**Azure Storage**.</span></span> <span data-ttu-id="44da2-140">创建一个包含 Blob 容器的 Azure 存储帐户用于存储诊断日志。</span><span class="sxs-lookup"><span data-stu-id="44da2-140">Create an Azure storage account with a blob container to store diagnostic logs.</span></span>

- <span data-ttu-id="44da2-141">**Azure Active Directory (Azure AD)**。</span><span class="sxs-lookup"><span data-stu-id="44da2-141">**Azure Active Directory (Azure AD)**.</span></span> <span data-ttu-id="44da2-142">使用 Azure AD 或其他标识提供者进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="44da2-142">Use Azure AD or another identity provider for authentication.</span></span>

## <a name="recommendations"></a><span data-ttu-id="44da2-143">建议</span><span class="sxs-lookup"><span data-stu-id="44da2-143">Recommendations</span></span>

<span data-ttu-id="44da2-144">你的要求可能不同于此处描述的体系结构。</span><span class="sxs-lookup"><span data-stu-id="44da2-144">Your requirements might differ from the architecture described here.</span></span> <span data-ttu-id="44da2-145">请使用本部分中的建议作为入手点。</span><span class="sxs-lookup"><span data-stu-id="44da2-145">Use the recommendations in this section as a starting point.</span></span>

### <a name="app-service-plan"></a><span data-ttu-id="44da2-146">应用服务计划</span><span class="sxs-lookup"><span data-stu-id="44da2-146">App Service plan</span></span>

<span data-ttu-id="44da2-147">使用标准或高级层，因为它们支持横向扩展、自动缩放和安全套接字层 (SSL)。</span><span class="sxs-lookup"><span data-stu-id="44da2-147">Use the Standard or Premium tiers, because they support scale out, autoscale, and secure sockets layer (SSL).</span></span> <span data-ttu-id="44da2-148">每个层支持若干种实例大小，具体数量根据核心数和内存而异。</span><span class="sxs-lookup"><span data-stu-id="44da2-148">Each tier supports several *instance sizes* that differ by number of cores and memory.</span></span> <span data-ttu-id="44da2-149">创建计划后，可以更改层或实例大小。</span><span class="sxs-lookup"><span data-stu-id="44da2-149">You can change the tier or instance size after you create a plan.</span></span> <span data-ttu-id="44da2-150">有关应用服务计划的详细信息，请参阅[应用服务定价][app-service-plans-tiers]。</span><span class="sxs-lookup"><span data-stu-id="44da2-150">For more information about App Service plans, see [App Service Pricing][app-service-plans-tiers].</span></span>

<span data-ttu-id="44da2-151">即使应用已停止，应用服务计划中的实例也会产生费用。</span><span class="sxs-lookup"><span data-stu-id="44da2-151">You are charged for the instances in the App Service plan, even if the app is stopped.</span></span> <span data-ttu-id="44da2-152">请务必删除未使用的计划（例如测试部署）。</span><span class="sxs-lookup"><span data-stu-id="44da2-152">Make sure to delete plans that you aren't using (for example, test deployments).</span></span>

### <a name="sql-database"></a><span data-ttu-id="44da2-153">SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="44da2-153">SQL Database</span></span>

<span data-ttu-id="44da2-154">使用 SQL 数据库 [V12 版][sql-db-v12]。</span><span class="sxs-lookup"><span data-stu-id="44da2-154">Use the [V12 version][sql-db-v12] of SQL Database.</span></span> <span data-ttu-id="44da2-155">SQL 数据库支持基本、标准和高级[服务层][sql-db-service-tiers]，每个层中包含多个以[数据库事务单位 (DTU)][sql-dtu] 计量的性能级别。</span><span class="sxs-lookup"><span data-stu-id="44da2-155">SQL Database supports Basic, Standard, and Premium [service tiers][sql-db-service-tiers], with multiple performance levels within each tier measured in [Database Transaction Units (DTUs)][sql-dtu].</span></span> <span data-ttu-id="44da2-156">请执行容量规划，并选择符合要求的层和性能级别。</span><span class="sxs-lookup"><span data-stu-id="44da2-156">Perform capacity planning and choose a tier and performance level that meets your requirements.</span></span>

### <a name="region"></a><span data-ttu-id="44da2-157">区域</span><span class="sxs-lookup"><span data-stu-id="44da2-157">Region</span></span>

<span data-ttu-id="44da2-158">在同一个区域中预配应用服务计划和 SQL 数据库，以尽量降低网络延迟。</span><span class="sxs-lookup"><span data-stu-id="44da2-158">Provision the App Service plan and the SQL Database in the same region to minimize network latency.</span></span> <span data-ttu-id="44da2-159">一般情况下，应选择离用户最近的区域。</span><span class="sxs-lookup"><span data-stu-id="44da2-159">Generally, choose the region closest to your users.</span></span>

<span data-ttu-id="44da2-160">资源组中还有一个区域指定了部署元数据的存储位置。</span><span class="sxs-lookup"><span data-stu-id="44da2-160">The resource group also has a region, which specifies where deployment metadata is stored.</span></span> <span data-ttu-id="44da2-161">请资源组及其资源放入同一个区域。</span><span class="sxs-lookup"><span data-stu-id="44da2-161">Put the resource group and its resources in the same region.</span></span> <span data-ttu-id="44da2-162">这可以在部署过程中提高可用性。</span><span class="sxs-lookup"><span data-stu-id="44da2-162">This can improve availability during deployment.</span></span>

## <a name="scalability-considerations"></a><span data-ttu-id="44da2-163">可伸缩性注意事项</span><span class="sxs-lookup"><span data-stu-id="44da2-163">Scalability considerations</span></span>

<span data-ttu-id="44da2-164">Azure 应用服务的主要优势是能够根据负载缩放应用程序。</span><span class="sxs-lookup"><span data-stu-id="44da2-164">A major benefit of Azure App Service is the ability to scale your application based on load.</span></span> <span data-ttu-id="44da2-165">下面是在计划缩放应用程序时需要考虑的一些注意事项。</span><span class="sxs-lookup"><span data-stu-id="44da2-165">Here are some considerations to keep in mind when planning to scale your application.</span></span>

### <a name="scaling-the-app-service-app"></a><span data-ttu-id="44da2-166">缩放应用服务应用</span><span class="sxs-lookup"><span data-stu-id="44da2-166">Scaling the App Service app</span></span>

<span data-ttu-id="44da2-167">可通过两种方法缩放应用服务应用：</span><span class="sxs-lookup"><span data-stu-id="44da2-167">There are two ways to scale an App Service app:</span></span>

- <span data-ttu-id="44da2-168">纵向缩放：指更改实例大小。</span><span class="sxs-lookup"><span data-stu-id="44da2-168">*Scale up*, which means changing the instance size.</span></span> <span data-ttu-id="44da2-169">实例大小决定了每个 VM 实例上的内存、核心数和存储。</span><span class="sxs-lookup"><span data-stu-id="44da2-169">The instance size determines the memory, number of cores, and storage on each VM instance.</span></span> <span data-ttu-id="44da2-170">可以通过更改实例大小或计划层进行手动纵向缩放。</span><span class="sxs-lookup"><span data-stu-id="44da2-170">You can scale up manually by changing the instance size or the plan tier.</span></span>

- <span data-ttu-id="44da2-171">横向缩放：指添加实例来处理增大的负载。</span><span class="sxs-lookup"><span data-stu-id="44da2-171">*Scale out*, which means adding instances to handle increased load.</span></span> <span data-ttu-id="44da2-172">每个定价层包含的实例数存在上限。</span><span class="sxs-lookup"><span data-stu-id="44da2-172">Each pricing tier has a maximum number of instances.</span></span>

  <span data-ttu-id="44da2-173">可以通过更改实例计数进行手动横向缩放，或者使用[自动缩放][web-app-autoscale]，让 Azure 根据计划和/或性能指标自动添加或删除实例。</span><span class="sxs-lookup"><span data-stu-id="44da2-173">You can scale out manually by changing the instance count, or use [autoscaling][web-app-autoscale] to have Azure automatically add or remove instances based on a schedule and/or performance metrics.</span></span> <span data-ttu-id="44da2-174">每项缩放操作可快速完成 &mdash; 通常只需几秒钟。</span><span class="sxs-lookup"><span data-stu-id="44da2-174">Each scale operation happens quickly&mdash;typically within seconds.</span></span>

  <span data-ttu-id="44da2-175">若要启用自动缩放，请创建自动缩放配置文件，用于定义最小和最大实例数。</span><span class="sxs-lookup"><span data-stu-id="44da2-175">To enable autoscaling, create an autoscale *profile* that defines the minimum and maximum number of instances.</span></span> <span data-ttu-id="44da2-176">可以计划配置文件。</span><span class="sxs-lookup"><span data-stu-id="44da2-176">Profiles can be scheduled.</span></span> <span data-ttu-id="44da2-177">例如，可为工作日和周末单独创建配置文件。</span><span class="sxs-lookup"><span data-stu-id="44da2-177">For example, you might create separate profiles for weekdays and weekends.</span></span> <span data-ttu-id="44da2-178">（可选）配置文件包含有关何时添加或删除实例的规则。</span><span class="sxs-lookup"><span data-stu-id="44da2-178">Optionally, a profile contains rules for when to add or remove instances.</span></span> <span data-ttu-id="44da2-179">（示例：如果 CPU 使用率高于 70% 达到 5 分钟，则添加两个实例。）</span><span class="sxs-lookup"><span data-stu-id="44da2-179">(Example: Add two instances if CPU usage is above 70% for 5 minutes.)</span></span>

<span data-ttu-id="44da2-180">有关缩放 Web 应用的建议：</span><span class="sxs-lookup"><span data-stu-id="44da2-180">Recommendations for scaling a web app:</span></span>

- <span data-ttu-id="44da2-181">尽量避免纵向扩展和缩减，因为这可能会触发应用程序重启。</span><span class="sxs-lookup"><span data-stu-id="44da2-181">As much as possible, avoid scaling up and down, because it may trigger an application restart.</span></span> <span data-ttu-id="44da2-182">应选择满足典型负载下的性能要求的层和大小，然后横向缩放实例来处理流量变化。</span><span class="sxs-lookup"><span data-stu-id="44da2-182">Instead, select a tier and size that meet your performance requirements under typical load and then scale out the instances to handle changes in traffic volume.</span></span>
- <span data-ttu-id="44da2-183">启用自动缩放。</span><span class="sxs-lookup"><span data-stu-id="44da2-183">Enable autoscaling.</span></span> <span data-ttu-id="44da2-184">如果应用程序具有可预测的常规工作负荷，请提前创建配置文件来计划实例计数。</span><span class="sxs-lookup"><span data-stu-id="44da2-184">If your application has a predictable, regular workload, create profiles to schedule the instance counts ahead of time.</span></span> <span data-ttu-id="44da2-185">如果工作负荷不可预测，请使用基于规则的自动缩放，对发生的负载变化做出反应。</span><span class="sxs-lookup"><span data-stu-id="44da2-185">If the workload is not predictable, use rule-based autoscaling to react to changes in load as they occur.</span></span> <span data-ttu-id="44da2-186">可以组合这两种方法。</span><span class="sxs-lookup"><span data-stu-id="44da2-186">You can combine both approaches.</span></span>
- <span data-ttu-id="44da2-187">CPU 使用率通常是自动缩放规则的适当指标。</span><span class="sxs-lookup"><span data-stu-id="44da2-187">CPU usage is generally a good metric for autoscale rules.</span></span> <span data-ttu-id="44da2-188">但是，应该对应用程序进行负载测试，识别潜在瓶颈，并使自动缩放规则基于该数据。</span><span class="sxs-lookup"><span data-stu-id="44da2-188">However, you should load test your application, identify potential bottlenecks, and base your autoscale rules on that data.</span></span>
- <span data-ttu-id="44da2-189">自动缩放规则包括一个冷却期，即，在完成某项缩放操作之后、启动新的缩放操作之前所要等待的时间间隔。</span><span class="sxs-lookup"><span data-stu-id="44da2-189">Autoscale rules include a *cool-down* period, which is the interval to wait after a scale action has completed before starting a new scale action.</span></span> <span data-ttu-id="44da2-190">冷却期可让系统在再次缩放之前变稳定。</span><span class="sxs-lookup"><span data-stu-id="44da2-190">The cool-down period lets the system stabilize before scaling again.</span></span> <span data-ttu-id="44da2-191">若要添加实例，请设置较短的冷却期；若要删除实例，请设置较长的冷却期。</span><span class="sxs-lookup"><span data-stu-id="44da2-191">Set a shorter cool-down period for adding instances, and a longer cool-down period for removing instances.</span></span> <span data-ttu-id="44da2-192">例如，设置 5 分钟来添加实例，但设置 60 分钟来删除实例。</span><span class="sxs-lookup"><span data-stu-id="44da2-192">For example, set 5 minutes to add an instance, but 60 minutes to remove an instance.</span></span> <span data-ttu-id="44da2-193">在负载加重的情况下，最好是快速添加新实例来处理额外的流量，然后逐渐缩减。</span><span class="sxs-lookup"><span data-stu-id="44da2-193">It's better to add new instances quickly under heavy load to handle the additional traffic, and then gradually scale back.</span></span>

### <a name="scaling-sql-database"></a><span data-ttu-id="44da2-194">缩放 SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="44da2-194">Scaling SQL Database</span></span>

<span data-ttu-id="44da2-195">如果需要为 SQL 数据库使用更高的服务层或性能级别，可以纵向扩展单个数据库，且无需关闭应用程序。</span><span class="sxs-lookup"><span data-stu-id="44da2-195">If you need a higher service tier or performance level for SQL Database, you can scale up individual databases with no application downtime.</span></span> <span data-ttu-id="44da2-196">有关详细信息，请参阅 [SQL 数据库选项和性能：了解每个服务层中有哪些可用资源][sql-db-scale]。</span><span class="sxs-lookup"><span data-stu-id="44da2-196">For more information, see [SQL Database options and performance: Understand what's available in each service tier][sql-db-scale].</span></span>

## <a name="availability-considerations"></a><span data-ttu-id="44da2-197">可用性注意事项</span><span class="sxs-lookup"><span data-stu-id="44da2-197">Availability considerations</span></span>

<span data-ttu-id="44da2-198">在撰写本文时，应用服务的服务级别协议 (SLA) 为 99.95%，基本、标准和高级层 SQL 数据库的 SLA 为 99.99%。</span><span class="sxs-lookup"><span data-stu-id="44da2-198">At the time of writing, the service level agreement (SLA) for App Service is 99.95% and the SLA for SQL Database is 99.99% for Basic, Standard, and Premium tiers.</span></span>

> [!NOTE]
> <span data-ttu-id="44da2-199">应用服务 SLA 适用于单个和多个实例。</span><span class="sxs-lookup"><span data-stu-id="44da2-199">The App Service SLA applies to both single and multiple instances.</span></span>
>

### <a name="backups"></a><span data-ttu-id="44da2-200">备份</span><span class="sxs-lookup"><span data-stu-id="44da2-200">Backups</span></span>

<span data-ttu-id="44da2-201">如果发生数据丢失，SQL 数据库可提供时间点还原和异地还原。</span><span class="sxs-lookup"><span data-stu-id="44da2-201">In the event of data loss, SQL Database provides point-in-time restore and geo-restore.</span></span> <span data-ttu-id="44da2-202">这些功能已在所有层中提供，并会自动启用。</span><span class="sxs-lookup"><span data-stu-id="44da2-202">These features are available in all tiers and are automatically enabled.</span></span> <span data-ttu-id="44da2-203">不需要计划或管理备份。</span><span class="sxs-lookup"><span data-stu-id="44da2-203">You don't need to schedule or manage the backups.</span></span>

- <span data-ttu-id="44da2-204">使用时间点还原可将数据库还原到以前的某个时间点，从而可[在发生人为失误后进行恢复][sql-human-error]。</span><span class="sxs-lookup"><span data-stu-id="44da2-204">Use point-in-time restore to [recover from human error][sql-human-error] by returning the database to an earlier point in time.</span></span>
- <span data-ttu-id="44da2-205">使用异地还原可从异地冗余的备份还原数据库，从而可[在发生服务中断后进行恢复][sql-outage-recovery]。</span><span class="sxs-lookup"><span data-stu-id="44da2-205">Use geo-restore to [recover from a service outage][sql-outage-recovery] by restoring a database from a geo-redundant backup.</span></span>

<span data-ttu-id="44da2-206">有关详细信息，请参阅[云业务连续性与使用 SQL 数据库进行数据库灾难恢复][sql-backup]。</span><span class="sxs-lookup"><span data-stu-id="44da2-206">For more information, see [Cloud business continuity and database disaster recovery with SQL Database][sql-backup].</span></span>

<span data-ttu-id="44da2-207">应用服务为应用程序文件提供[备份和还原][web-app-backup]功能。</span><span class="sxs-lookup"><span data-stu-id="44da2-207">App Service provides a [backup and restore][web-app-backup] feature for your application files.</span></span> <span data-ttu-id="44da2-208">但请注意，备份的文件包含纯文本形式的应用设置，而这些内容可能包含机密，例如连接字符串。</span><span class="sxs-lookup"><span data-stu-id="44da2-208">However, be aware that the backed-up files include app settings in plain text and these may include secrets, such as connection strings.</span></span> <span data-ttu-id="44da2-209">请避免使用应用服务备份功能备份 SQL 数据库，因为它会将数据库导出到 SQL .bacpac 文件，因而会消耗 [DTU][sql-dtu]。</span><span class="sxs-lookup"><span data-stu-id="44da2-209">Avoid using the App Service backup feature to back up your SQL databases because it exports the database to a SQL .bacpac file, consuming [DTUs][sql-dtu].</span></span> <span data-ttu-id="44da2-210">应使用前面所述的 SQL 数据库时间点还原。</span><span class="sxs-lookup"><span data-stu-id="44da2-210">Instead, use SQL Database point-in-time restore described above.</span></span>

## <a name="manageability-considerations"></a><span data-ttu-id="44da2-211">可管理性注意事项</span><span class="sxs-lookup"><span data-stu-id="44da2-211">Manageability considerations</span></span>

<span data-ttu-id="44da2-212">为生产、开发和测试环境创建单独的资源组。</span><span class="sxs-lookup"><span data-stu-id="44da2-212">Create separate resource groups for production, development, and test environments.</span></span> <span data-ttu-id="44da2-213">这可以更方便地管理部署、删除测试部署，以及分配访问权限。</span><span class="sxs-lookup"><span data-stu-id="44da2-213">This makes it easier to manage deployments, delete test deployments, and assign access rights.</span></span>

<span data-ttu-id="44da2-214">将资源分配到资源组时，请注意以下事项：</span><span class="sxs-lookup"><span data-stu-id="44da2-214">When assigning resources to resource groups, consider the following:</span></span>

- <span data-ttu-id="44da2-215">生命周期</span><span class="sxs-lookup"><span data-stu-id="44da2-215">Lifecycle.</span></span> <span data-ttu-id="44da2-216">一般情况下，应将具有相同生命周期的资源放入同一个资源组。</span><span class="sxs-lookup"><span data-stu-id="44da2-216">In general, put resources with the same lifecycle into the same resource group.</span></span>
- <span data-ttu-id="44da2-217">访问。</span><span class="sxs-lookup"><span data-stu-id="44da2-217">Access.</span></span> <span data-ttu-id="44da2-218">可以使用[基于角色的访问控制][rbac] (RBAC) 向组中的资源应用访问策略。</span><span class="sxs-lookup"><span data-stu-id="44da2-218">You can use [role-based access control][rbac] (RBAC) to apply access policies to the resources in a group.</span></span>
- <span data-ttu-id="44da2-219">计费。</span><span class="sxs-lookup"><span data-stu-id="44da2-219">Billing.</span></span> <span data-ttu-id="44da2-220">可以查看资源组的累计成本。</span><span class="sxs-lookup"><span data-stu-id="44da2-220">You can view the rolled-up costs for the resource group.</span></span>

<span data-ttu-id="44da2-221">有关详细信息，请参阅 [Azure 资源管理器概述](/azure/azure-resource-manager/resource-group-overview)。</span><span class="sxs-lookup"><span data-stu-id="44da2-221">For more information, see [Azure Resource Manager overview](/azure/azure-resource-manager/resource-group-overview).</span></span>

### <a name="deployment"></a><span data-ttu-id="44da2-222">部署</span><span class="sxs-lookup"><span data-stu-id="44da2-222">Deployment</span></span>

<span data-ttu-id="44da2-223">部署涉及两个步骤：</span><span class="sxs-lookup"><span data-stu-id="44da2-223">Deployment involves two steps:</span></span>

1. <span data-ttu-id="44da2-224">预配 Azure 资源。</span><span class="sxs-lookup"><span data-stu-id="44da2-224">Provisioning the Azure resources.</span></span> <span data-ttu-id="44da2-225">我们建议使用 [Azure 资源管理器模板][arm-template]完成此步骤。</span><span class="sxs-lookup"><span data-stu-id="44da2-225">We recommend that you use [Azure Resource Manager templates][arm-template] for this step.</span></span> <span data-ttu-id="44da2-226">使用模板可以通过 PowerShell 或 Azure 命令行接口 (CLI) 更轻松地自动完成部署。</span><span class="sxs-lookup"><span data-stu-id="44da2-226">Templates make it easier to automate deployments via PowerShell or the Azure command line interface (CLI).</span></span>
2. <span data-ttu-id="44da2-227">部署应用程序（代码、二进制文件和内容文件）。</span><span class="sxs-lookup"><span data-stu-id="44da2-227">Deploying the application (code, binaries, and content files).</span></span> <span data-ttu-id="44da2-228">可以使用多个选项，包括从本地 Git 存储库部署、使用 Visual Studio，或者通过基于云的源代码管理进行持续部署。</span><span class="sxs-lookup"><span data-stu-id="44da2-228">You have several options, including deploying from a local Git repository, using Visual Studio, or continuous deployment from cloud-based source control.</span></span> <span data-ttu-id="44da2-229">请参阅[将应用部署到 Azure 应用服务][deploy]。</span><span class="sxs-lookup"><span data-stu-id="44da2-229">See [Deploy your app to Azure App Service][deploy].</span></span>

<span data-ttu-id="44da2-230">应用服务应用始终有一个名为 `production` 的部署槽位，该槽位表示实际生产站点。</span><span class="sxs-lookup"><span data-stu-id="44da2-230">An App Service app always has one deployment slot named `production`, which represents the live production site.</span></span> <span data-ttu-id="44da2-231">我们建议创建过渡槽位用于部署更新。</span><span class="sxs-lookup"><span data-stu-id="44da2-231">We recommend creating a staging slot for deploying updates.</span></span> <span data-ttu-id="44da2-232">使用过渡槽位的好处包括：</span><span class="sxs-lookup"><span data-stu-id="44da2-232">The benefits of using a staging slot include:</span></span>

- <span data-ttu-id="44da2-233">在将部署交换到生产环境之前，可以验证部署是否成功。</span><span class="sxs-lookup"><span data-stu-id="44da2-233">You can verify the deployment succeeded, before swapping it into production.</span></span>
- <span data-ttu-id="44da2-234">部署到过渡槽位可确保所有实例在交换到生产环境之前已预热。</span><span class="sxs-lookup"><span data-stu-id="44da2-234">Deploying to a staging slot ensures that all instances are warmed up before being swapped into production.</span></span> <span data-ttu-id="44da2-235">许多应用程序的预热和冷启动时间很长。</span><span class="sxs-lookup"><span data-stu-id="44da2-235">Many applications have a significant warmup and cold-start time.</span></span>

<span data-ttu-id="44da2-236">此外，我们还建议创建第三个槽位用于保存上次已知正常的部署。</span><span class="sxs-lookup"><span data-stu-id="44da2-236">We also recommend creating a third slot to hold the last-known-good deployment.</span></span> <span data-ttu-id="44da2-237">交换过渡部署和生产部署之后，请将以前的生产部署（现在为过渡部署）移到上次已知正常的槽位中。</span><span class="sxs-lookup"><span data-stu-id="44da2-237">After you swap staging and production, move the previous production deployment (which is now in staging) into the last-known-good slot.</span></span> <span data-ttu-id="44da2-238">这样，如果以后发现问题，可以快速还原到上次已知正常的版本。</span><span class="sxs-lookup"><span data-stu-id="44da2-238">That way, if you discover a problem later, you can quickly revert to the last-known-good version.</span></span>

<span data-ttu-id="44da2-239">![[1]][1]</span><span class="sxs-lookup"><span data-stu-id="44da2-239">![[1]][1]</span></span>

<span data-ttu-id="44da2-240">若要还原到以前的版本，请确保数据库架构发生的任何更改可向后兼容。</span><span class="sxs-lookup"><span data-stu-id="44da2-240">If you revert to a previous version, make sure any database schema changes are backward compatible.</span></span>

<span data-ttu-id="44da2-241">不要使用生产部署中的槽位进行测试，因为同一应用服务计划中的所有应用共享相同的 VM 实例。</span><span class="sxs-lookup"><span data-stu-id="44da2-241">Don't use slots on your production deployment for testing because all apps within the same App Service plan share the same VM instances.</span></span> <span data-ttu-id="44da2-242">例如，负载测试可能使实时生产站点降级。</span><span class="sxs-lookup"><span data-stu-id="44da2-242">For example, load tests might degrade the live production site.</span></span> <span data-ttu-id="44da2-243">应该为生产和测试创建单独的应用服务计划。</span><span class="sxs-lookup"><span data-stu-id="44da2-243">Instead, create separate App Service plans for production and test.</span></span> <span data-ttu-id="44da2-244">如果将测试部署放入单独的计划，则可将其与生产版本相隔离。</span><span class="sxs-lookup"><span data-stu-id="44da2-244">By putting test deployments into a separate plan, you isolate them from the production version.</span></span>

### <a name="configuration"></a><span data-ttu-id="44da2-245">配置</span><span class="sxs-lookup"><span data-stu-id="44da2-245">Configuration</span></span>

<span data-ttu-id="44da2-246">将配置设置存储为[应用设置][app-settings]。</span><span class="sxs-lookup"><span data-stu-id="44da2-246">Store configuration settings as [app settings][app-settings].</span></span> <span data-ttu-id="44da2-247">在资源管理器模板中或者使用 PowerShell 定义应用设置。</span><span class="sxs-lookup"><span data-stu-id="44da2-247">Define the app settings in your Resource Manager templates, or using PowerShell.</span></span> <span data-ttu-id="44da2-248">在运行时，应用程序可将应用设置用作环境变量。</span><span class="sxs-lookup"><span data-stu-id="44da2-248">At runtime, app settings are available to the application as environment variables.</span></span>

<span data-ttu-id="44da2-249">切勿将密码、访问密钥或连接字符串签入源代码管理。</span><span class="sxs-lookup"><span data-stu-id="44da2-249">Never check passwords, access keys, or connection strings into source control.</span></span> <span data-ttu-id="44da2-250">应将这些值作为参数传递给可将其存储为应用设置的部署脚本。</span><span class="sxs-lookup"><span data-stu-id="44da2-250">Instead, pass these as parameters to a deployment script that stores these values as app settings.</span></span>

<span data-ttu-id="44da2-251">交换部署槽位时，默认会交换应用设置。</span><span class="sxs-lookup"><span data-stu-id="44da2-251">When you swap a deployment slot, the app settings are swapped by default.</span></span> <span data-ttu-id="44da2-252">如果需要对生产部署和过渡部署使用不同的设置，可以创建依附在某个槽位上的、不被交换的应用设置。</span><span class="sxs-lookup"><span data-stu-id="44da2-252">If you need different settings for production and staging, you can create app settings that stick to a slot and don't get swapped.</span></span>

### <a name="diagnostics-and-monitoring"></a><span data-ttu-id="44da2-253">诊断和监控</span><span class="sxs-lookup"><span data-stu-id="44da2-253">Diagnostics and monitoring</span></span>

<span data-ttu-id="44da2-254">启用[诊断日志记录][diagnostic-logs]，包括应用程序日志记录和 Web 服务器日志记录。</span><span class="sxs-lookup"><span data-stu-id="44da2-254">Enable [diagnostics logging][diagnostic-logs], including application logging and web server logging.</span></span> <span data-ttu-id="44da2-255">将日志记录配置为使用 Blob 存储。</span><span class="sxs-lookup"><span data-stu-id="44da2-255">Configure logging to use Blob storage.</span></span> <span data-ttu-id="44da2-256">出于性能方面的原因，请创建单独的存储帐户来存储诊断日志。</span><span class="sxs-lookup"><span data-stu-id="44da2-256">For performance reasons, create a separate storage account for diagnostic logs.</span></span> <span data-ttu-id="44da2-257">不要对日志和应用程序数据使用相同的存储帐户。</span><span class="sxs-lookup"><span data-stu-id="44da2-257">Don't use the same storage account for logs and application data.</span></span> <span data-ttu-id="44da2-258">有关日志记录的更详细指导，请参阅[监视和诊断指南][monitoring-guidance]。</span><span class="sxs-lookup"><span data-stu-id="44da2-258">For more detailed guidance on logging, see [Monitoring and diagnostics guidance][monitoring-guidance].</span></span>

<span data-ttu-id="44da2-259">使用 [New Relic][new-relic] 或 [Application Insights][app-insights] 等服务来监视承受负载时的应用程序性能和行为。</span><span class="sxs-lookup"><span data-stu-id="44da2-259">Use a service such as [New Relic][new-relic] or [Application Insights][app-insights] to monitor application performance and behavior under load.</span></span> <span data-ttu-id="44da2-260">请注意 Application Insights 的[数据率限制][app-insights-data-rate]。</span><span class="sxs-lookup"><span data-stu-id="44da2-260">Be aware of the [data rate limits][app-insights-data-rate] for Application Insights.</span></span>

<span data-ttu-id="44da2-261">使用 [Azure DevOps][azure-devops]、[Visual Studio Team Foundation Server][tfs] 等工具执行负载测试。</span><span class="sxs-lookup"><span data-stu-id="44da2-261">Perform load testing, using a tool such as [Azure DevOps][azure-devops] or [Visual Studio Team Foundation Server][tfs].</span></span> <span data-ttu-id="44da2-262">有关云应用程序中性能分析的一般性概述，请参阅[性能分析入门][perf-analysis]。</span><span class="sxs-lookup"><span data-stu-id="44da2-262">For a general overview of performance analysis in cloud applications, see [Performance Analysis Primer][perf-analysis].</span></span>

<span data-ttu-id="44da2-263">有关排查应用程序问题的提示：</span><span class="sxs-lookup"><span data-stu-id="44da2-263">Tips for troubleshooting your application:</span></span>

- <span data-ttu-id="44da2-264">使用 Azure 门户中的[故障排除边栏选项卡][troubleshoot-blade]找到常见问题的解决方法。</span><span class="sxs-lookup"><span data-stu-id="44da2-264">Use the [troubleshoot blade][troubleshoot-blade] in the Azure portal to find solutions to common problems.</span></span>
- <span data-ttu-id="44da2-265">启用[日志流][web-app-log-stream]可以近乎实时地查看日志记录信息。</span><span class="sxs-lookup"><span data-stu-id="44da2-265">Enable [log streaming][web-app-log-stream] to see logging information in near-real time.</span></span>
- <span data-ttu-id="44da2-266">[Kudu 仪表板][kudu]提供了多个工具用于监视和调试应用程序。</span><span class="sxs-lookup"><span data-stu-id="44da2-266">The [Kudu dashboard][kudu] has several tools for monitoring and debugging your application.</span></span> <span data-ttu-id="44da2-267">有关详细信息，请参阅博客文章 [Azure Websites online tools you should know about][kudu]（应该了解的 Azure 网站联机工具）。</span><span class="sxs-lookup"><span data-stu-id="44da2-267">For more information, see [Azure Websites online tools you should know about][kudu] (blog post).</span></span> <span data-ttu-id="44da2-268">可以通过 Azure 门户访问 Kudu 仪表板。</span><span class="sxs-lookup"><span data-stu-id="44da2-268">You can reach the Kudu dashboard from the Azure portal.</span></span> <span data-ttu-id="44da2-269">打开应用的边栏选项卡，依次单击“工具”、“Kudu”。</span><span class="sxs-lookup"><span data-stu-id="44da2-269">Open the blade for your app and click **Tools**, then click **Kudu**.</span></span>
- <span data-ttu-id="44da2-270">如果使用的是 Visual Studio，请参阅[使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除][troubleshoot-web-app]一文，获取有关调试和故障排除的提示。</span><span class="sxs-lookup"><span data-stu-id="44da2-270">If you use Visual Studio, see the article [Troubleshoot a web app in Azure App Service using Visual Studio][troubleshoot-web-app] for debugging and troubleshooting tips.</span></span>

## <a name="security-considerations"></a><span data-ttu-id="44da2-271">安全注意事项</span><span class="sxs-lookup"><span data-stu-id="44da2-271">Security considerations</span></span>

<span data-ttu-id="44da2-272">本部分列出专门与本文中所述 Azure 服务相关的安全注意事项，</span><span class="sxs-lookup"><span data-stu-id="44da2-272">This section lists security considerations that are specific to the Azure services described in this article.</span></span> <span data-ttu-id="44da2-273">内容并非安全最佳做法的完整列表。</span><span class="sxs-lookup"><span data-stu-id="44da2-273">It's not a complete list of security best practices.</span></span> <span data-ttu-id="44da2-274">有关其他一些安全注意事项，请参阅[保护 Azure 应用服务中的应用][app-service-security]。</span><span class="sxs-lookup"><span data-stu-id="44da2-274">For some additional security considerations, see [Secure an app in Azure App Service][app-service-security].</span></span>

### <a name="sql-database-auditing"></a><span data-ttu-id="44da2-275">SQL 数据库审核</span><span class="sxs-lookup"><span data-stu-id="44da2-275">SQL Database auditing</span></span>

<span data-ttu-id="44da2-276">借助审核可以保持合规，洞察可能表示需要企业引以关注的问题或疑似出现安全违规的偏差和异常。</span><span class="sxs-lookup"><span data-stu-id="44da2-276">Auditing can help you maintain regulatory compliance and get insight into discrepancies and irregularities that could indicate business concerns or suspected security violations.</span></span> <span data-ttu-id="44da2-277">请参阅 [SQL 数据库审核入门][sql-audit]。</span><span class="sxs-lookup"><span data-stu-id="44da2-277">See [Get started with SQL database auditing][sql-audit].</span></span>

### <a name="deployment-slots"></a><span data-ttu-id="44da2-278">部署槽</span><span class="sxs-lookup"><span data-stu-id="44da2-278">Deployment slots</span></span>

<span data-ttu-id="44da2-279">每个部署槽位有一个公共 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="44da2-279">Each deployment slot has a public IP address.</span></span> <span data-ttu-id="44da2-280">使用 [Azure Active Directory 登录][aad-auth]保护非生产槽位，以便只有开发团队和 DevOps 团队的成员可以访问这些终结点。</span><span class="sxs-lookup"><span data-stu-id="44da2-280">Secure the nonproduction slots using [Azure Active Directory login][aad-auth] so that only members of your development and DevOps teams can reach those endpoints.</span></span>

### <a name="logging"></a><span data-ttu-id="44da2-281">日志记录</span><span class="sxs-lookup"><span data-stu-id="44da2-281">Logging</span></span>

<span data-ttu-id="44da2-282">日志中不可记录诸如用户密码或其他可用于实行身份诈骗的信息。</span><span class="sxs-lookup"><span data-stu-id="44da2-282">Logs should never record users' passwords or other information that might be used to commit identity fraud.</span></span> <span data-ttu-id="44da2-283">在存储这些数据之前，请清除其中的这些详细信息。</span><span class="sxs-lookup"><span data-stu-id="44da2-283">Scrub those details from the data before storing it.</span></span>

### <a name="ssl"></a><span data-ttu-id="44da2-284">SSL</span><span class="sxs-lookup"><span data-stu-id="44da2-284">SSL</span></span>

<span data-ttu-id="44da2-285">应用服务应用在子域 `azurewebsites.net` 中免费包含一个 SSL 终结点。</span><span class="sxs-lookup"><span data-stu-id="44da2-285">An App Service app includes an SSL endpoint on a subdomain of `azurewebsites.net` at no additional cost.</span></span> <span data-ttu-id="44da2-286">该 SSL 终结点包括 `*.azurewebsites.net` 域的通配符证书。</span><span class="sxs-lookup"><span data-stu-id="44da2-286">The SSL endpoint includes a wildcard certificate for the `*.azurewebsites.net` domain.</span></span> <span data-ttu-id="44da2-287">如果使用自定义域名，必须提供与该自定义域匹配的证书。</span><span class="sxs-lookup"><span data-stu-id="44da2-287">If you use a custom domain name, you must provide a certificate that matches the custom domain.</span></span> <span data-ttu-id="44da2-288">最简单的方法是直接通过 Azure 门户购买证书。</span><span class="sxs-lookup"><span data-stu-id="44da2-288">The simplest approach is to buy a certificate directly through the Azure portal.</span></span> <span data-ttu-id="44da2-289">也可以从其他证书颁发机构导入证书。</span><span class="sxs-lookup"><span data-stu-id="44da2-289">You can also import certificates from other certificate authorities.</span></span> <span data-ttu-id="44da2-290">有关详细信息，请参阅[为 Azure 应用服务购买和配置 SSL 证书][ssl-cert]。</span><span class="sxs-lookup"><span data-stu-id="44da2-290">For more information, see [Buy and Configure an SSL Certificate for your Azure App Service][ssl-cert].</span></span>

<span data-ttu-id="44da2-291">作为一项安全最佳做法，应用应该通过重定向 HTTP 请求来强制实施 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="44da2-291">As a security best practice, your app should enforce HTTPS by redirecting HTTP requests.</span></span> <span data-ttu-id="44da2-292">可以在应用程序内部实施此做法，或者根据[为 Azure 应用服务中的应用启用 HTTPS][ssl-redirect] 中所述使用 URL 重写规则。</span><span class="sxs-lookup"><span data-stu-id="44da2-292">You can implement this inside your application or use a URL rewrite rule as described in [Enable HTTPS for an app in Azure App Service][ssl-redirect].</span></span>

### <a name="authentication"></a><span data-ttu-id="44da2-293">身份验证</span><span class="sxs-lookup"><span data-stu-id="44da2-293">Authentication</span></span>

<span data-ttu-id="44da2-294">我们建议通过 Azure AD、Facebook、Google 或 Twitter 等标识提供者 (IDP) 进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="44da2-294">We recommend authenticating through an identity provider (IDP), such as Azure AD, Facebook, Google, or Twitter.</span></span> <span data-ttu-id="44da2-295">为身份验证流使用 OAuth 2 或 OpenID Connect (OIDC)。</span><span class="sxs-lookup"><span data-stu-id="44da2-295">Use OAuth 2 or OpenID Connect (OIDC) for the authentication flow.</span></span> <span data-ttu-id="44da2-296">Azure AD 提供管理用户和组、创建应用程序角色、集成本地标识，以及使用 Office 365 和 Skype for Business 等后端服务的功能。</span><span class="sxs-lookup"><span data-stu-id="44da2-296">Azure AD provides functionality to manage users and groups, create application roles, integrate your on-premises identities, and consume backend services such as Office 365 and Skype for Business.</span></span>

<span data-ttu-id="44da2-297">请避免让应用程序直接管理用户登录名和凭据，因为这会造成潜在的攻击面。</span><span class="sxs-lookup"><span data-stu-id="44da2-297">Avoid having the application manage user logins and credentials directly, as it creates a potential attack surface.</span></span>  <span data-ttu-id="44da2-298">最起码需要配置电子邮件确认、密码恢复和多重身份验证；验证密码强度；安全存储密码哈希。</span><span class="sxs-lookup"><span data-stu-id="44da2-298">At a minimum, you would need to have email confirmation, password recovery, and multi-factor authentication; validate password strength; and store password hashes securely.</span></span> <span data-ttu-id="44da2-299">大型标识提供者可自动处理所有这些操作，并会持续监视及改进其安全做法。</span><span class="sxs-lookup"><span data-stu-id="44da2-299">The large identity providers handle all of those things for you, and are constantly monitoring and improving their security practices.</span></span>

<span data-ttu-id="44da2-300">请考虑使用[应用服务身份验证][app-service-auth]来实施 OAuth/OIDC 身份验证流。</span><span class="sxs-lookup"><span data-stu-id="44da2-300">Consider using [App Service authentication][app-service-auth] to implement the OAuth/OIDC authentication flow.</span></span> <span data-ttu-id="44da2-301">应用服务身份验证的好处包括：</span><span class="sxs-lookup"><span data-stu-id="44da2-301">The benefits of App Service authentication include:</span></span>

- <span data-ttu-id="44da2-302">易于配置。</span><span class="sxs-lookup"><span data-stu-id="44da2-302">Easy to configure.</span></span>
- <span data-ttu-id="44da2-303">对于简单的身份验证方案无需编写代码。</span><span class="sxs-lookup"><span data-stu-id="44da2-303">No code is required for simple authentication scenarios.</span></span>
- <span data-ttu-id="44da2-304">支持使用 OAuth 访问令牌实现委托授权，以代表用户使用资源。</span><span class="sxs-lookup"><span data-stu-id="44da2-304">Supports delegated authorization using OAuth access tokens to consume resources on behalf of the user.</span></span>
- <span data-ttu-id="44da2-305">提供内置的令牌缓存。</span><span class="sxs-lookup"><span data-stu-id="44da2-305">Provides a built-in token cache.</span></span>

<span data-ttu-id="44da2-306">应用服务身份验证的一些限制：</span><span class="sxs-lookup"><span data-stu-id="44da2-306">Some limitations of App Service authentication:</span></span>

- <span data-ttu-id="44da2-307">自定义选项有限。</span><span class="sxs-lookup"><span data-stu-id="44da2-307">Limited customization options.</span></span>
- <span data-ttu-id="44da2-308">仅限针对每个登录会话的一个后端资源使用委托授权。</span><span class="sxs-lookup"><span data-stu-id="44da2-308">Delegated authorization is restricted to one backend resource per login session.</span></span>
- <span data-ttu-id="44da2-309">如果使用了多个 IDP，没有内置的机制可用于执行主领域发现。</span><span class="sxs-lookup"><span data-stu-id="44da2-309">If you use more than one IDP, there is no built-in mechanism for home realm discovery.</span></span>
- <span data-ttu-id="44da2-310">对于多租户方案，应用程序必须实施验证令牌颁发者的逻辑。</span><span class="sxs-lookup"><span data-stu-id="44da2-310">For multi-tenant scenarios, the application must implement the logic to validate the token issuer.</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="44da2-311">部署解决方案</span><span class="sxs-lookup"><span data-stu-id="44da2-311">Deploy the solution</span></span>

<span data-ttu-id="44da2-312">[GitHub][paas-basic-arm-template] 上提供了此体系结构的示例资源管理器模板。</span><span class="sxs-lookup"><span data-stu-id="44da2-312">An example Resource Manager template for this architecture is [available on GitHub][paas-basic-arm-template].</span></span>

<span data-ttu-id="44da2-313">若要使用 PowerShell 部署该模板，请运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="44da2-313">To deploy the template using PowerShell, run the following commands:</span></span>

```powershell
New-AzureRmResourceGroup -Name <resource-group-name> -Location "West US"

$parameters = @{"appName"="<app-name>";"environment"="dev";"locationShort"="uw";"databaseName"="app-db";"administratorLogin"="<admin>";"administratorLoginPassword"="<password>"}

New-AzureRmResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile .\PaaS-Basic.json -TemplateParameterObject  $parameters
```

<span data-ttu-id="44da2-314">有关详细信息，请参阅[使用 Azure 资源管理器模板部署资源][deploy-arm-template]。</span><span class="sxs-lookup"><span data-stu-id="44da2-314">For more information, see [Deploy resources with Azure Resource Manager templates][deploy-arm-template].</span></span>

<!-- links -->

[aad-auth]: /azure/app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication
[app-insights]: /azure/application-insights/app-insights-overview
[app-insights-data-rate]: /azure/application-insights/app-insights-pricing
[app-service]: /azure/app-service/
[app-service-auth]: /azure/app-service-api/app-service-api-authentication
[app-service-plans]: /azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview
[app-service-plans-tiers]: https://azure.microsoft.com/pricing/details/app-service/
[app-service-security]: /azure/app-service-web/web-sites-security
[app-settings]: /azure/app-service-web/web-sites-configure
[arm-template]: /azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-devops]: /azure/devops/
[azure-dns]: /azure/dns/dns-overview
[custom-domain-name]: /azure/app-service-web/web-sites-custom-domain-name
[deploy]: /azure/app-service-web/web-sites-deploy
[deploy-arm-template]: /azure/resource-group-template-deploy
[deployment-slots]: /azure/app-service-web/web-sites-staged-publishing
[diagnostic-logs]: /azure/app-service-web/web-sites-enable-diagnostic-log
[kudu]: https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/
[monitoring-guidance]: ../../best-practices/monitoring.md
[new-relic]: https://newrelic.com/
[paas-basic-arm-template]: https://github.com/mspnp/reference-architectures/tree/master/managed-web-app/basic-web-app/Paas-Basic/Templates
[perf-analysis]: https://github.com/mspnp/performance-optimization/blob/master/Performance-Analysis-Primer.md
[rbac]: /azure/active-directory/role-based-access-control-what-is
[resource-group]: /azure/azure-resource-manager/resource-group-overview
[sla]: https://azure.microsoft.com/support/legal/sla/
[sql-audit]: /azure/sql-database/sql-database-auditing-get-started
[sql-backup]: /azure/sql-database/sql-database-business-continuity
[sql-db]: /azure/sql-database/
[sql-db-overview]: /azure/sql-database/sql-database-technical-overview
[sql-db-scale]: /azure/sql-database/sql-database-service-tiers#scaling-up-or-scaling-down-a-single-database
[sql-db-service-tiers]: /azure/sql-database/sql-database-service-tiers
[sql-db-v12]: /azure/sql-database/sql-database-features
[sql-dtu]: /azure/sql-database/sql-database-service-tiers
[sql-human-error]: /azure/sql-database/sql-database-business-continuity#recover-a-database-after-a-user-or-application-error
[sql-outage-recovery]: /azure/sql-database/sql-database-business-continuity#recover-a-database-to-another-region-from-an-azure-regional-data-center-outage
[ssl-redirect]: /azure/app-service-web/web-sites-configure-ssl-certificate#bkmk_enforce
[sql-resource-limits]: /azure/sql-database/sql-database-resource-limits
[ssl-cert]: /azure/app-service-web/web-sites-purchase-ssl-web-site
[troubleshoot-blade]: https://azure.microsoft.com/updates/self-service-troubleshooting-for-app-service-web-apps-customers/
[tfs]: /tfs/index
[troubleshoot-web-app]: /azure/app-service-web/web-sites-dotnet-troubleshoot-visual-studio
[visio-download]: https://archcenter.blob.core.windows.net/cdn/app-service-reference-architectures.vsdx
[web-app-autoscale]: /azure/app-service-web/web-sites-scale
[web-app-backup]: /azure/app-service-web/web-sites-backup
[web-app-log-stream]: /azure/app-service-web/web-sites-enable-diagnostic-log#streamlogs
[1]: ./images/paas-basic-web-app-staging-slots.png "交换生产部署和过渡部署的槽位"
