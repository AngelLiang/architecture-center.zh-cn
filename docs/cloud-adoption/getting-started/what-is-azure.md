---
title: CAF：Azure 如何工作？
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
description: 解释 Azure 的内部功能
author: petertaylor9999
ms.date: 02/11/2019
ms.openlocfilehash: 724d16a810865dd947a7ade34766818c8ea525a1
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58245268"
---
<!-- markdownlint-disable MD026 -->

# <a name="how-does-azure-work"></a><span data-ttu-id="3f9fd-103">Azure 如何工作？</span><span class="sxs-lookup"><span data-stu-id="3f9fd-103">How does Azure work?</span></span>

<span data-ttu-id="3f9fd-104">Azure 是 Microsoft 的公有云平台。</span><span class="sxs-lookup"><span data-stu-id="3f9fd-104">Azure is Microsoft's public cloud platform.</span></span> <span data-ttu-id="3f9fd-105">Azure 提供了一个大型的服务集合，包括平台即服务 (PaaS)、基础结构即服务 (IaaS)、数据库即服务 (DBaaS) 以及许多其他服务。</span><span class="sxs-lookup"><span data-stu-id="3f9fd-105">Azure offers a large collection of services including platform as a service (PaaS), infrastructure as a service (IaaS), database as a service (DBaaS), and many others.</span></span> <span data-ttu-id="3f9fd-106">但是，确切而言，什么是 Azure，它如何工作？</span><span class="sxs-lookup"><span data-stu-id="3f9fd-106">But what exactly is Azure, and how does it work?</span></span>

<!-- markdownlint-disable MD034 -->

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2ixGo]

<!-- markdownlint-enable MD034 -->

<span data-ttu-id="3f9fd-107">与其他云平台一样，Azure 依赖于称为**虚拟化**的技术。</span><span class="sxs-lookup"><span data-stu-id="3f9fd-107">Azure, like other cloud platforms, relies on a technology known as **virtualization**.</span></span> <span data-ttu-id="3f9fd-108">可以在软件中仿真大多数计算机硬件，因为大多数计算机硬件只是在硅片中永久或半永久编码的一组指令。</span><span class="sxs-lookup"><span data-stu-id="3f9fd-108">Most computer hardware can be emulated in software, because most computer hardware is simply a set of instructions permanently or semi-permanently encoded in silicon.</span></span> <span data-ttu-id="3f9fd-109">使用将软件指令映射为硬件指令的仿真层，虚拟化的硬件可以在软件中执行，就像它是实际硬件本身一样。</span><span class="sxs-lookup"><span data-stu-id="3f9fd-109">Using an emulation layer that maps software instructions to hardware instructions, virtualized hardware can execute in software as if it were the actual hardware itself.</span></span>

<span data-ttu-id="3f9fd-110">本质上来说，云是位于一个或多个数据中心内的一组物理服务器，它们代表客户执行虚拟化硬件。</span><span class="sxs-lookup"><span data-stu-id="3f9fd-110">Essentially, the cloud is a set of physical servers in one or more datacenters that execute virtualized hardware on behalf of customers.</span></span> <span data-ttu-id="3f9fd-111">那么，云如何同时为数百万客户创建、启动、停止和删除虚拟化硬件的数百万实例？</span><span class="sxs-lookup"><span data-stu-id="3f9fd-111">So how does the cloud create, start, stop, and delete millions of instances of virtualized hardware for millions of customers simultaneously?</span></span>

<span data-ttu-id="3f9fd-112">为了理解这一点，让我们看一下数据中心内硬件的体系结构。</span><span class="sxs-lookup"><span data-stu-id="3f9fd-112">To understand this, let's look at the architecture of the hardware in the datacenter.</span></span>  <span data-ttu-id="3f9fd-113">每个数据中心内是位于服务器机架中的服务器集合。</span><span class="sxs-lookup"><span data-stu-id="3f9fd-113">Within each datacenter is a collection of servers sitting in server racks.</span></span> <span data-ttu-id="3f9fd-114">每个服务器机架包含许多服务器**刀片**和一个提供网络连接的网络交换机，以及一个用于供电的配电单元 (PDU)。</span><span class="sxs-lookup"><span data-stu-id="3f9fd-114">Each server rack contains many server **blades** as well as a network switch providing network connectivity and a power distribution unit (PDU) providing power.</span></span> <span data-ttu-id="3f9fd-115">机架有时组合到一起形成更大的单元，称为**群集**。</span><span class="sxs-lookup"><span data-stu-id="3f9fd-115">Racks are sometimes grouped together in larger units known as **clusters**.</span></span>

<span data-ttu-id="3f9fd-116">在每个机架或群集中，大多数服务器设计为代表用户运行这些虚拟化硬件实例。</span><span class="sxs-lookup"><span data-stu-id="3f9fd-116">Within each rack or cluster, most of the servers are designated to run these virtualized hardware instances on behalf of the user.</span></span> <span data-ttu-id="3f9fd-117">但是，有些服务器运行称为结构控制器的云管理软件。</span><span class="sxs-lookup"><span data-stu-id="3f9fd-117">However, a number of the servers run cloud management software known as a fabric controller.</span></span> <span data-ttu-id="3f9fd-118">**结构控制器**是一个有许多职责的分布式应用程序。</span><span class="sxs-lookup"><span data-stu-id="3f9fd-118">The **fabric controller** is a distributed application with many responsibilities.</span></span> <span data-ttu-id="3f9fd-119">它分配服务，监视服务器和在其上运行的服务的运行状况，并且在服务器发生故障时将其修复。</span><span class="sxs-lookup"><span data-stu-id="3f9fd-119">It allocates services, monitors the health of the server and the services running on it, and heals servers when they fail.</span></span>

<span data-ttu-id="3f9fd-120">结构控制器的每个实例连接到运行云业务流程软件的另一组服务器，通常称为**前端**。</span><span class="sxs-lookup"><span data-stu-id="3f9fd-120">Each instance of the fabric controller is connected to another set of servers running cloud orchestration software, typically known as a **front end**.</span></span> <span data-ttu-id="3f9fd-121">前端托管着用于云执行的所有功能的 Web 服务、RESTful API 和内部 Azure 数据库。</span><span class="sxs-lookup"><span data-stu-id="3f9fd-121">The front end hosts the web services, RESTful APIs, and internal Azure databases used for all functions the cloud performs.</span></span>

<span data-ttu-id="3f9fd-122">例如，前端托管的服务对客户请求进行处理来分配 Azure 资源（例如[虚拟网络][vnet]、[虚拟机][vms]）和服务（例如 [Cosmos DB][cosmosdb]）。</span><span class="sxs-lookup"><span data-stu-id="3f9fd-122">For example, the front end hosts the services that handle customer requests to allocate Azure resources such as [virtual networks][vnet], [virtual machines][vms], and services like [Cosmos DB][cosmosdb].</span></span> <span data-ttu-id="3f9fd-123">首先，前端对用户进行校验并验证用户是否有权分配所请求的资源。</span><span class="sxs-lookup"><span data-stu-id="3f9fd-123">First, the front end validates the user and verifies the user is authorized to allocate the requested resources.</span></span> <span data-ttu-id="3f9fd-124">如果有，则前端将请求数据库分配一个具有足够容量的服务器机架，然后指示机架上的结构控制器来分配资源。</span><span class="sxs-lookup"><span data-stu-id="3f9fd-124">If so, the front end consults a database to locate a server rack with sufficient capacity, and then instructs the fabric controller on the rack to allocate the resource.</span></span>

<span data-ttu-id="3f9fd-125">因此，非常简单，Azure 是服务器和网络硬件的巨大集合，并且其中还包含一组复杂的分布式应用程序，它们安排这些服务器上的虚拟化硬件和软件的配置和操作。</span><span class="sxs-lookup"><span data-stu-id="3f9fd-125">So, very simply, Azure is a huge collection of servers and networking hardware, along with a complex set of distributed applications that orchestrate the configuration and operation of the virtualized hardware and software on those servers.</span></span> <span data-ttu-id="3f9fd-126">并且，正是此业务流程安排使得 Azure 如此强大 - 用户不再负责维护和升级硬件，Azure 在幕后执行所有这些操作。</span><span class="sxs-lookup"><span data-stu-id="3f9fd-126">And it is this orchestration that makes Azure so powerful - users are no longer responsible for maintaining and upgrading hardware, Azure does all this behind the scenes.</span></span>

## <a name="next-steps"></a><span data-ttu-id="3f9fd-127">后续步骤</span><span class="sxs-lookup"><span data-stu-id="3f9fd-127">Next steps</span></span>

<span data-ttu-id="3f9fd-128">了解 Azure 的内部功能后，接下来请了解云资源调控。</span><span class="sxs-lookup"><span data-stu-id="3f9fd-128">Now that you understand the internal functioning of Azure, learn about cloud resource governance.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="3f9fd-129">了解资源治理</span><span class="sxs-lookup"><span data-stu-id="3f9fd-129">Learn about resource governance</span></span>](what-is-governance.md)

<!-- Links -->

[cosmosdb]: /azure/cosmos-db/introduction
[docs-add-users-to-aad]: /azure/active-directory/add-users-azure-active-directory?toc=/azure/architecture/cloud-adoption-guide/toc.json
[vms]: /azure/virtual-machines/
[vnet]: /azure/virtual-network/virtual-networks-overview
