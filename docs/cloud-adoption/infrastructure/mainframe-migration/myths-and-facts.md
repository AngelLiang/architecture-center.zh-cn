---
title: 大型机迁移：神话和事实
description: 将应用程序从大型机环境迁移到 Azure，这是经过验证的、高度可用且可缩放的基础结构，适用于当前在大型机上运行的系统。
author: njray
ms.date: 12/27/2018
ms.openlocfilehash: bcad01ec044d2d802b055e328a9496aae7b33311
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900503"
---
# <a name="mainframe-myths-and-facts"></a><span data-ttu-id="e51dd-103">大型机的相关神话和事实</span><span class="sxs-lookup"><span data-stu-id="e51dd-103">Mainframe myths and facts</span></span>

<span data-ttu-id="e51dd-104">大型机在计算史中的地位举足轻重，并且对于高度特定的工作负载仍然可行。</span><span class="sxs-lookup"><span data-stu-id="e51dd-104">Mainframes figure prominently in the history of computing and remain viable for highly specific workloads.</span></span> <span data-ttu-id="e51dd-105">多数人都认为大型机是经过验证的平台，有着久经考验的操作过程，这使其成为了稳定可靠的环境。</span><span class="sxs-lookup"><span data-stu-id="e51dd-105">Most agree that mainframes are a proven platform with long-established operating procedures that make them reliable, robust environments.</span></span> <span data-ttu-id="e51dd-106">软件基于使用情况（每秒百万条指令 (MIPS)）运行，并提供全面的使用报告以进行计费。</span><span class="sxs-lookup"><span data-stu-id="e51dd-106">Software runs based on usage, measured in million instructions per second (MIPS), and extensive usage reports are available for charge backs.</span></span>

<span data-ttu-id="e51dd-107">大型机的可靠性、可用性和处理能力几乎达到了神话般的程度。</span><span class="sxs-lookup"><span data-stu-id="e51dd-107">The reliability, availability, and processing power of mainframes have taken on almost mythical proportions.</span></span> <span data-ttu-id="e51dd-108">要评估最适合 Azure 的大型机工作负载，首先要将这些神话与事实区分开。</span><span class="sxs-lookup"><span data-stu-id="e51dd-108">To evaluate the mainframe workloads that are most suitable for Azure, you first want to distinguish the myths from the reality.</span></span>

## <a name="myth-mainframes-never-go-down-and-have-a-minimum-of-five-9s-of-availability"></a><span data-ttu-id="e51dd-109">神话：大型机在任何情况下都不会停机，并且可用性最少为 5 个 9</span><span class="sxs-lookup"><span data-stu-id="e51dd-109">Myth: Mainframes never go down and have a minimum of five 9s of availability</span></span>

<span data-ttu-id="e51dd-110">大型机硬件和操作系统稳定可靠。</span><span class="sxs-lookup"><span data-stu-id="e51dd-110">Mainframe hardware and operating systems are viewed as reliable and stable.</span></span> <span data-ttu-id="e51dd-111">但事实上，必须针对维护和重启（即启动程序加载 (IPL)）计划停机时间。</span><span class="sxs-lookup"><span data-stu-id="e51dd-111">But the reality is that downtime must be scheduled for maintenance and reboots (referred to as initial program loads or IPLs).</span></span> <span data-ttu-id="e51dd-112">考虑这些任务后，通常大型机解决方案的可用性更接近 2 个或 3 个 9，这相当于基于 Intel 的高端服务器。</span><span class="sxs-lookup"><span data-stu-id="e51dd-112">When these tasks are considered, a mainframe solution often has closer to two or three 9s of availability, which is equivalent to that of high-end, Intel-based servers.</span></span>

<span data-ttu-id="e51dd-113">此外，与任何其他服务器一样，大型机也易受到灾难的攻击，并且需要不间断电源 (UPS) 系统来处理这些故障类型。</span><span class="sxs-lookup"><span data-stu-id="e51dd-113">Mainframes also remain as vulnerable to disasters as any other servers do, and require uninterruptible power supply (UPS) systems to handle these types of failures.</span></span>

## <a name="myth-mainframes-have-limitless-scalability"></a><span data-ttu-id="e51dd-114">神话：大型机可无限伸缩</span><span class="sxs-lookup"><span data-stu-id="e51dd-114">Myth: Mainframes have limitless scalability</span></span>

<span data-ttu-id="e51dd-115">大型机的可伸缩性取决于系统软件（如客户信息控制系统 (CICS)）的容量，以及新的大型机引擎和存储的实例的容量。</span><span class="sxs-lookup"><span data-stu-id="e51dd-115">A mainframe’s scalability depends on the capacity of its system software, such as the customer information control system (CICS), and the capacity of new instances of mainframe engines and storage.</span></span> <span data-ttu-id="e51dd-116">一些使用大型机的大型公司针对性能自定义了其 CICS，并超出了最大可用大型机的能力。</span><span class="sxs-lookup"><span data-stu-id="e51dd-116">Some large companies that use mainframes have customized their CICS for performance, and have otherwise outgrown the capability of the largest available mainframes.</span></span>

## <a name="myth-intel-based-servers-are-not-as-powerful-as-mainframes"></a><span data-ttu-id="e51dd-117">神话：基于 Intel 的服务器不如大型机强大</span><span class="sxs-lookup"><span data-stu-id="e51dd-117">Myth: Intel-based servers are not as powerful as mainframes</span></span>

<span data-ttu-id="e51dd-118">基于 Intel 的新核心密集型系统的计算容量与大型机一样大。</span><span class="sxs-lookup"><span data-stu-id="e51dd-118">The new core-dense, Intel-based systems have as much compute capacity as mainframes.</span></span>

## <a name="myth-the-cloud-cannot-accommodate-mission-critical-applications-for-large-companies-such-as-financial-institutions"></a><span data-ttu-id="e51dd-119">神话：云不适用于大型企业（如金融机构）的任务关键应用程序</span><span class="sxs-lookup"><span data-stu-id="e51dd-119">Myth: The cloud cannot accommodate mission-critical applications for large companies, such as financial institutions</span></span>

<span data-ttu-id="e51dd-120">虽然可能存在一些云解决方案不适用的独立实例，但这通常是因为应用程序算法无法分发。</span><span class="sxs-lookup"><span data-stu-id="e51dd-120">Although there may be some isolated instances where cloud solutions fall short, it is usually becuase the application algorithms cannot be distributed.</span></span> <span data-ttu-id="e51dd-121">这些少数示例属于例外情况，而不是规则。</span><span class="sxs-lookup"><span data-stu-id="e51dd-121">These few examples are the exceptions, not the rule.</span></span>

## <a name="summary"></a><span data-ttu-id="e51dd-122">摘要</span><span class="sxs-lookup"><span data-stu-id="e51dd-122">Summary</span></span>

<span data-ttu-id="e51dd-123">相比之下，Azure 提供了一个替代平台，能够以更低的成本提供同等大型机的功能和特性。</span><span class="sxs-lookup"><span data-stu-id="e51dd-123">By comparison, Azure offers  an alternative platform that is capable of delivering equivalent mainframe functionality and features, and at a much lower cost.</span></span> <span data-ttu-id="e51dd-124">此外，云的基于订阅的、按使用量付费的成本模型的总拥有成本 (TCO) 远远低于大型机。</span><span class="sxs-lookup"><span data-stu-id="e51dd-124">In addition, the total cost of ownership (TCO) of the cloud’s subscription-based, usage-driven cost model is far less expensive than mainframe computers.</span></span>

## <a name="next-steps"></a><span data-ttu-id="e51dd-125">后续步骤</span><span class="sxs-lookup"><span data-stu-id="e51dd-125">Next steps</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="e51dd-126">从大型机切换到 Azure</span><span class="sxs-lookup"><span data-stu-id="e51dd-126">Make the Switch from Mainframes to Azure</span></span>](migration-strategies.md)
