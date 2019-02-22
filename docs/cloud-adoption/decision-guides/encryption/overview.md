---
title: CAF：加密
titleSuffix: Microsoft Cloud Adoption Framework for Azure
ms.service: architecture-center
ms.subservice: enterprise-cloud-adoption
ms.custom: governance
ms.date: 02/11/2019
description: 了解作为 Azure 迁移中核心服务的加密。
author: rotycenh
ms.openlocfilehash: 660206d57ded9a93d73c57ba9cb8058020d87525
ms.sourcegitcommit: 273e690c0cfabbc3822089c7d8bc743ef41d2b6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55900499"
---
# <a name="encryption-decision-guide"></a><span data-ttu-id="b2e9c-103">加密决策指南</span><span class="sxs-lookup"><span data-stu-id="b2e9c-103">Encryption decision guide</span></span>

<span data-ttu-id="b2e9c-104">对数据进行加密可保护数据免遭未经授权访问。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-104">Encrypting data protects it against unauthorized access.</span></span> <span data-ttu-id="b2e9c-105">正确实现的加密策略可以为基于云的工作负载提供更多重安全保障，并抵御组织和网络内外攻击者和其他未经授权用户的攻击。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-105">Properly implemented encryption policy provides additional layers of security for your cloud-based workloads and guards against attackers and other unauthorized users from both inside and outside your organization and networks.</span></span>

<span data-ttu-id="b2e9c-106">虽然对资源进行加密通常是可取的，但加密确实会产生成本，进而可能会增加延迟和整体资源使用状况。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-106">While encrypting resources is generally desirable, encryption does have costs that can increase latency and overall resource usage.</span></span> <span data-ttu-id="b2e9c-107">对于要求严苛的工作负载，力求在加密和性能之间取得恰当的平衡至关重要。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-107">For demanding workloads, striking the correct balance between encryption and performance is essential.</span></span>

![按复杂性从低到高的顺序绘制了加密选项（与下面的跳转链接保持一致）](../../_images/discovery-guides/discovery-guide-encryption.png)

<span data-ttu-id="b2e9c-109">跳转到：[密钥管理](#key-management) | [数据加密](#data-encryption) | [了解更多](#learn-more)</span><span class="sxs-lookup"><span data-stu-id="b2e9c-109">Jump to: [Key management](#key-management) | [Data encryption](#data-encryption) | [Learn more](#learn-more)</span></span>

<span data-ttu-id="b2e9c-110">确定云加密策略时，导致拐点出现的主要因素是企业策略和合规性要求。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-110">The inflection point when determining a cloud encryption strategy focuses on corporate policy and compliance mandates.</span></span>

<span data-ttu-id="b2e9c-111">在云环境中实现加密的方法有多种，这些方法的成本和复杂性各异。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-111">There are multiple ways to implement encryption in a cloud environment, with varying cost and complexity.</span></span> <span data-ttu-id="b2e9c-112">计划加密策略时，要考虑的首要因素是企业策略和第三方合规性。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-112">Corporate policy and third-party compliance are the biggest drivers when planning an encryption strategy.</span></span> <span data-ttu-id="b2e9c-113">大多数基于云的解决方案都提供了数据加密标准机制，无论数据是静态的，还是在传输中。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-113">Most cloud-based solutions provide standard mechanisms for encrypting data, whether at rest or in transit.</span></span> <span data-ttu-id="b2e9c-114">不过，对于要求更严格控制的策略和合规性要求（如标准化机密和密钥管理、加密使用中的数据或数据专用加密），可能就需要实现复杂的解决方案。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-114">However, for policies and compliance requirements that demand tighter controls, such as standardized secrets and key management, encryption in-use, or data specific encryption, you will likely need to implement a complex solution.</span></span>

## <a name="key-management"></a><span data-ttu-id="b2e9c-115">密钥管理</span><span class="sxs-lookup"><span data-stu-id="b2e9c-115">Key management</span></span>

<span data-ttu-id="b2e9c-116">为了加强保护，新式密钥管理系统应支持使用硬件安全模块 (HSM) 存储密钥。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-116">Modern key management systems should offer support for storing keys using hardware security modules (HSMs) for increased protection.</span></span> <span data-ttu-id="b2e9c-117">因此，组织能否创建和存储加密密钥、重要密码、连接字符串和其他 IT 机密信息，关键在于密钥管理系统。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-117">Thus, a key management system is critical to your organization's ability to create and store cryptographic keys, important passwords, connection strings, and other IT confidential information.</span></span>

<span data-ttu-id="b2e9c-118">计划云迁移时，请参阅下表，其中介绍了如何存储和管理加密密钥、证书和机密，这些对创建安全、可管理的云部署至关重要：</span><span class="sxs-lookup"><span data-stu-id="b2e9c-118">When planning a cloud migration, the following table describes how you can store and manage encryption keys, certificates, and secrets, which are critical for creating secure and manageable cloud deployments:</span></span>

| <span data-ttu-id="b2e9c-119">问题</span><span class="sxs-lookup"><span data-stu-id="b2e9c-119">Question</span></span> | <span data-ttu-id="b2e9c-120">云原生</span><span class="sxs-lookup"><span data-stu-id="b2e9c-120">Cloud Native</span></span> | <span data-ttu-id="b2e9c-121">混合</span><span class="sxs-lookup"><span data-stu-id="b2e9c-121">Hybrid</span></span> | <span data-ttu-id="b2e9c-122">本地</span><span class="sxs-lookup"><span data-stu-id="b2e9c-122">On-premises</span></span> |
|---------------------------------------------------------------------------------------------------------------------------------------|--------------|--------|-------------|
| <span data-ttu-id="b2e9c-123">组织是否缺少集中式密钥和机密管理？</span><span class="sxs-lookup"><span data-stu-id="b2e9c-123">Does your organization lack centralized key and secret management?</span></span>                                                                    | <span data-ttu-id="b2e9c-124">是</span><span class="sxs-lookup"><span data-stu-id="b2e9c-124">Yes</span></span>          | <span data-ttu-id="b2e9c-125">否</span><span class="sxs-lookup"><span data-stu-id="b2e9c-125">No</span></span>     | <span data-ttu-id="b2e9c-126">否</span><span class="sxs-lookup"><span data-stu-id="b2e9c-126">No</span></span>          |
| <span data-ttu-id="b2e9c-127">在云中使用这些密钥时，是否需要限制为只有本地硬件才能创建设备密钥和机密？</span><span class="sxs-lookup"><span data-stu-id="b2e9c-127">Will you need to limit the creation of keys and secrets to devices to your on-premises hardware, while using these keys in the cloud?</span></span> | <span data-ttu-id="b2e9c-128">否</span><span class="sxs-lookup"><span data-stu-id="b2e9c-128">No</span></span>           | <span data-ttu-id="b2e9c-129">是</span><span class="sxs-lookup"><span data-stu-id="b2e9c-129">Yes</span></span>    | <span data-ttu-id="b2e9c-130">否</span><span class="sxs-lookup"><span data-stu-id="b2e9c-130">No</span></span>          |
| <span data-ttu-id="b2e9c-131">组织是否制定了可防止密钥和机密存储在界外的规则或策略？</span><span class="sxs-lookup"><span data-stu-id="b2e9c-131">Does your organization have rules or policies in place that would prevent keys and secrets from being stored offsite?</span></span>                | <span data-ttu-id="b2e9c-132">否</span><span class="sxs-lookup"><span data-stu-id="b2e9c-132">No</span></span>           | <span data-ttu-id="b2e9c-133">否</span><span class="sxs-lookup"><span data-stu-id="b2e9c-133">No</span></span>     | <span data-ttu-id="b2e9c-134">是</span><span class="sxs-lookup"><span data-stu-id="b2e9c-134">Yes</span></span>         |

### <a name="cloud-native"></a><span data-ttu-id="b2e9c-135">云原生</span><span class="sxs-lookup"><span data-stu-id="b2e9c-135">Cloud native</span></span>

<span data-ttu-id="b2e9c-136">通过云原生密钥管理，可以在基于云的保管库中生成、管理和存储所有密钥和机密。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-136">With cloud native key management, all keys and secrets are generated, managed, and stored in a cloud-based vault.</span></span> <span data-ttu-id="b2e9c-137">这种方法简化了许多与密钥管理相关的 IT 任务。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-137">This approach simplifies many IT tasks related to key management.</span></span>

<span data-ttu-id="b2e9c-138">云原生密钥管理先决条件：需要先满足以下先决条件，再使用云原生密钥管理系统：</span><span class="sxs-lookup"><span data-stu-id="b2e9c-138">Cloud native key management assumptions: Using a cloud native key management system assumes the following:</span></span>

- <span data-ttu-id="b2e9c-139">信任由云密钥管理解决方案来创建、管理和托管组织的机密和密钥。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-139">You trust the cloud key management solution with creating, managing, and hosting your organization's secrets and keys.</span></span>
- <span data-ttu-id="b2e9c-140">将所有依赖访问加密服务或机密的本地应用程序和服务启用为，访问云密钥管理系统。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-140">You enable all on-premises applications and services that rely on accessing encryption services or secrets to access the cloud key management system.</span></span>

### <a name="hybrid-bring-your-own-key"></a><span data-ttu-id="b2e9c-141">混合（创建自己的密钥）</span><span class="sxs-lookup"><span data-stu-id="b2e9c-141">Hybrid (bring your own key)</span></span>

<span data-ttu-id="b2e9c-142">使用“创建自己的密钥”这种方法，可以先在本地环境中的专用 HSM 硬件上生成密钥，再将密钥传输到安全的云密钥管理系统，以用于云资源。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-142">With a bring-your-own-key approach, you generate keys on dedicated HSM hardware within your on-premises environment, then transfer the keys to a secure cloud key management system for use with cloud resources.</span></span>

<span data-ttu-id="b2e9c-143">混合密钥管理先决条件：需要先满足以下先决条件，再使用混合密钥管理系统：</span><span class="sxs-lookup"><span data-stu-id="b2e9c-143">Hybrid key management assumptions: Using a hybrid key management system assumes the following:</span></span>

- <span data-ttu-id="b2e9c-144">信任由云平台的基础安全和访问控制基础结构来托管和使用密钥和机密。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-144">You trust the underlying security and access control infrastructure of the cloud platform for hosting and using your keys and secrets.</span></span>
- <span data-ttu-id="b2e9c-145">法规或组织策略要求，必须在本地创建和管理组织的机密和密钥。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-145">You are required by regulatory or organizational policy to keep the creation and management of your organization's secrets and keys on-premises.</span></span>

### <a name="on-premises-hold-your-own-key"></a><span data-ttu-id="b2e9c-146">本地（保留自己的密钥）</span><span class="sxs-lookup"><span data-stu-id="b2e9c-146">On-premises (hold your own key)</span></span>

<span data-ttu-id="b2e9c-147">在某些情况下，可能出于法规、策略或技术原因，无法将密钥存储在公有云服务提供的密钥管理系统上。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-147">In certain scenarios, there may be regulatory, policy, or technical reasons why you can't store keys on a key management system provided by a public cloud service.</span></span> <span data-ttu-id="b2e9c-148">在这种情况下，必须使用本地硬件保留密钥，并预配允许基于云的资源出于加密目的访问这些密钥的机制。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-148">In these cases, you must maintain keys using on-premises hardware, and provision a mechanism to allow cloud-based resource to access these keys for encryption purposes.</span></span> <span data-ttu-id="b2e9c-149">请注意，“保留自己的密钥”方法可能并不与所有云服务都兼容。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-149">Note that a hold your own key approach may not be compatible with all cloud services.</span></span>

<span data-ttu-id="b2e9c-150">本地密钥管理先决条件：需要先满足以下先决条件，再使用本地密钥管理系统：</span><span class="sxs-lookup"><span data-stu-id="b2e9c-150">On-premises key management assumptions: Using an on-premises key management system assumes the following:</span></span>

- <span data-ttu-id="b2e9c-151">法规或组织策略要求，必须在本地创建、管理和托管组织的机密和密钥。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-151">You are required by regulatory or organizational policy to keep the creation, management, and hosting of your organization's secrets and keys on-premises.</span></span>
- <span data-ttu-id="b2e9c-152">所有依赖访问加密服务或机密的基于云的应用程序或服务都可以访问本地密钥管理系统。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-152">Any cloud-based applications or services that rely on accessing encryption services or secrets can access the on-premises key management system.</span></span>

## <a name="data-encryption"></a><span data-ttu-id="b2e9c-153">数据加密</span><span class="sxs-lookup"><span data-stu-id="b2e9c-153">Data encryption</span></span>

<span data-ttu-id="b2e9c-154">计划加密策略时，需要考虑几种不同的数据状态以及各自不同的加密需求：</span><span class="sxs-lookup"><span data-stu-id="b2e9c-154">There are several different states of data with different encryption needs to consider when planning your encryption policy:</span></span>

| <span data-ttu-id="b2e9c-155">数据状态</span><span class="sxs-lookup"><span data-stu-id="b2e9c-155">Data state</span></span> | <span data-ttu-id="b2e9c-156">数据</span><span class="sxs-lookup"><span data-stu-id="b2e9c-156">Data</span></span> |
|-----|-----|
| <span data-ttu-id="b2e9c-157">传输中的数据</span><span class="sxs-lookup"><span data-stu-id="b2e9c-157">Data in transit</span></span> | <span data-ttu-id="b2e9c-158">内部网络流量、Internet 连接、数据中心或虚拟网络之间的连接</span><span class="sxs-lookup"><span data-stu-id="b2e9c-158">Internal network traffic, internet connections, connections between datacenters or virtual networks</span></span> |
| <span data-ttu-id="b2e9c-159">静态数据</span><span class="sxs-lookup"><span data-stu-id="b2e9c-159">Data at rest</span></span>    | <span data-ttu-id="b2e9c-160">数据库、文件、虚拟驱动器、PaaS 存储</span><span class="sxs-lookup"><span data-stu-id="b2e9c-160">Databases, files, virtual drives, PaaS storage</span></span> |
| <span data-ttu-id="b2e9c-161">使用中的数据</span><span class="sxs-lookup"><span data-stu-id="b2e9c-161">Data in use</span></span>     | <span data-ttu-id="b2e9c-162">在 RAM 或 CPU 高速缓存中加载的数据</span><span class="sxs-lookup"><span data-stu-id="b2e9c-162">Data loaded in RAM or in CPU caches</span></span> |

### <a name="data-in-transit"></a><span data-ttu-id="b2e9c-163">传输中的数据</span><span class="sxs-lookup"><span data-stu-id="b2e9c-163">Data in transit</span></span>

<span data-ttu-id="b2e9c-164">传输中的数据是在内部资源之间移动、在数据中心或外部网络之间移动，或通过 Internet 移动的数据。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-164">Data in transit is data moving between resources on the internal, between datacenters or external networks, or over the internet.</span></span>

<span data-ttu-id="b2e9c-165">加密传输中的数据通常通过要求对流量使用 SSL/TLS 协议来完成。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-165">Encrypting data in transit is usually done by requiring SSL/TLS protocols for traffic.</span></span> <span data-ttu-id="b2e9c-166">应始终加密在云托管的资源到外部网络或公共 Internet 之间传输的流量。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-166">Traffic transiting between your cloud-hosted resources to external network or the public internet should always be encrypted.</span></span> <span data-ttu-id="b2e9c-167">PaaS 资源通常也默认对流量强制使用 SSL/TLS 加密。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-167">PaaS resources generally also enforce SSL/TLS encryption to traffic by default.</span></span> <span data-ttu-id="b2e9c-168">是否对虚拟网络中托管的 IaaS 资源之间的流量强制执行加密，由云采用团队和工作负载所有者决定，通常建议执行加密。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-168">Whether you enforce encryption for traffic between IaaS resources hosted inside your virtual networks is a decision for your Cloud Adoption Team and workload owner and is generally recommended.</span></span>

<span data-ttu-id="b2e9c-169">**加密传输中的数据先决条件**。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-169">**Encrypting data in transit assumptions**.</span></span> <span data-ttu-id="b2e9c-170">需要先满足以下先决条件，再为传输中的数据实现适当的加密策略：</span><span class="sxs-lookup"><span data-stu-id="b2e9c-170">Implementing proper encryption policy for data in transit assumes the following:</span></span>

- <span data-ttu-id="b2e9c-171">云环境中所有可公开访问的终结点都将使用 SSL/TLS 协议与公共 Internet 进行通信。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-171">All publicly accessible endpoints in your cloud environment will communicate with the public internet using SSL/TLS protocols.</span></span>
- <span data-ttu-id="b2e9c-172">如果通过公共 Internet 将云网络与本地网络或其他外部网络连接，使用加密 VPN 协议。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-172">When connecting cloud networks with on-premises or other external network over the public internet, use encrypted VPN protocols.</span></span>
- <span data-ttu-id="b2e9c-173">如果使用专用 WAN 连接（如 ExpressRoute）将云网络与本地网络或其他外部网络连接，使用与相应虚拟 VPN 或部署到云网络的加密设备配对的 VPN 或其他本地加密设备。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-173">When connecting cloud networks with on-premises or other external network using a dedicated WAN connection such as ExpressRoute, you will use a VPN or other encryption appliance on-premises paired with a corresponding virtual VPN or encryption appliance deployed to your cloud network.</span></span>
- <span data-ttu-id="b2e9c-174">若有不得添加到对 IT 人员可见的流量日志或其他诊断报告中的敏感数据，加密虚拟网络中资源之间的所有流量。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-174">If you have sensitive data that shouldn't be included in traffic logs or other diagnostics reports visible to IT staff, you will encrypt all traffic between resources in your virtual network.</span></span>

### <a name="data-at-rest"></a><span data-ttu-id="b2e9c-175">静态数据</span><span class="sxs-lookup"><span data-stu-id="b2e9c-175">Data at rest</span></span>

<span data-ttu-id="b2e9c-176">静态数据表示任何未被主动移动或处理的数据，包括文件、数据库、虚拟机驱动器、PaaS 存储帐户或类似资产。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-176">Data at rest represents any data not being actively moved or processed, including files, databases, virtual machine drives, PaaS storage accounts, or similar assets.</span></span> <span data-ttu-id="b2e9c-177">加密已存储的数据可保护虚拟设备或文件免遭外部网络渗透、内部未授权用户或意外泄露造成的未经授权访问。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-177">Encrypting stored data protects virtual devices or files against unauthorized access either from external network penetration, rogue internal users, or accidental releases.</span></span>

<span data-ttu-id="b2e9c-178">默认情况下，通常强制加密 PaaS 存储和数据库资源。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-178">PaaS storage and database resources generally enforce encryption by default.</span></span> <span data-ttu-id="b2e9c-179">可使用密钥管理系统中存储的加密密钥，通过虚拟磁盘加密来保护 IaaS 虚拟资源。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-179">IaaS virtual resources can be secured through virtual disk encryption using cryptographic keys stored in your key management system.</span></span>

<span data-ttu-id="b2e9c-180">静态数据加密还涉及更多高级数据库加密技术（如列级别加密和行级别加密），可便于更好地控制要保护的确切数据。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-180">Encryption for data at rest also encompasses more advanced database encryption techniques, such as column-level and row level encryption, which provides much more control over exactly what data is being secured.</span></span>

<span data-ttu-id="b2e9c-181">应根据总体策略和合规性要求、存储数据的敏感性以及工作负载性能要求，确定哪些资产需要加密。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-181">Your overall policy and compliance requirements, the sensitivity of the data being stored, and the performance requirements of your workloads should determine which assets require encryption.</span></span>

<span data-ttu-id="b2e9c-182">**加密静态数据先决条件**。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-182">**Encrypting Data at Rest Assumptions**.</span></span> <span data-ttu-id="b2e9c-183">需要先满足以下先决条件，再加密静态数据：</span><span class="sxs-lookup"><span data-stu-id="b2e9c-183">Encrypting data at rest assumes the following:</span></span>

- <span data-ttu-id="b2e9c-184">要存储的数据不适合公共使用。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-184">You are storing data that is not meant for public consumption.</span></span>
- <span data-ttu-id="b2e9c-185">工作负载可以接受磁盘加密造成的额外延迟成本。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-185">Your workloads can accept the added latency cost of disk encryption.</span></span>

### <a name="data-in-use"></a><span data-ttu-id="b2e9c-186">使用中的数据</span><span class="sxs-lookup"><span data-stu-id="b2e9c-186">Data in use</span></span>

<span data-ttu-id="b2e9c-187">加密使用中的数据涉及保护非永久性存储（如 RAM 或 CPU 高速缓存）中的数据。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-187">Encryption for data in use involves securing data in nonpersistent storage, such as RAM or CPU caches.</span></span> <span data-ttu-id="b2e9c-188">使用全内存加密等技术，以及 Intel 的 Secure Guard Extensions (SGX) 等 Enclave 技术。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-188">Use of technologies such as full memory encryption, enclave technologies, such as Intel's Secure Guard Extensions (SGX).</span></span> <span data-ttu-id="b2e9c-189">这还包括可用于创建安全、受信任的执行环境的同态加密等加密技术。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-189">This also includes cryptographic techniques, such as homomorphic encryption that can be used to create secure, trusted execution environments.</span></span>

<span data-ttu-id="b2e9c-190">**加密使用中的数据先决条件**。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-190">**Encrypting data in use assumptions**.</span></span> <span data-ttu-id="b2e9c-191">需要先满足以下先决条件，再加密使用中的数据：</span><span class="sxs-lookup"><span data-stu-id="b2e9c-191">Encrypting data in use assumes the following:</span></span>

- <span data-ttu-id="b2e9c-192">要求必须始终将数据所有权与基础云平台分离开来，即使在 RAM 和 CPU 级别，也不例外。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-192">You are required to maintain data ownership separate from the underlying cloud platform at all times, even at the RAM and CPU level.</span></span>

## <a name="learn-more"></a><span data-ttu-id="b2e9c-193">了解详细信息</span><span class="sxs-lookup"><span data-stu-id="b2e9c-193">Learn more</span></span>

<span data-ttu-id="b2e9c-194">若要详细了解 Azure 平台中的加密和密钥管理，请参阅以下资源。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-194">See the following for more information about encryption and key management in the Azure platform.</span></span>

- <span data-ttu-id="b2e9c-195">[Azure 加密概述](/azure/security/security-azure-encryption-overview)。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-195">[Azure encryption overview](/azure/security/security-azure-encryption-overview).</span></span> <span data-ttu-id="b2e9c-196">详细介绍了 Azure 如何使用加密来保护静态数据和传输中的数据。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-196">A detailed description of how Azure uses encryption to secure both data at rest and data in transit.</span></span>
- <span data-ttu-id="b2e9c-197">[Azure Key Vault](/azure/key-vault/key-vault-overview)。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-197">[Azure Key Vault](/azure/key-vault/key-vault-overview).</span></span> <span data-ttu-id="b2e9c-198">Key Vault 是用于在 Azure 中存储和管理加密密钥、机密和证书的主要密钥管理系统。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-198">Key Vault is the primary key management system for storing and managing cryptographic keys, secrets, and certificates within Azure.</span></span>
- <span data-ttu-id="b2e9c-199">[Azure 中的机密计算](/solutions/confidential-compute)。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-199">[Confidential computing in Azure](/solutions/confidential-compute).</span></span> <span data-ttu-id="b2e9c-200">Azure 的机密计算计划提供了工具和技术，可用于创建受信任的执行环境或其他加密机制来保护使用中的数据。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-200">Azure's confidential computing initiative provides tools and technology to create trusted execution environments or other encryption mechanisms to secure data in use.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b2e9c-201">后续步骤</span><span class="sxs-lookup"><span data-stu-id="b2e9c-201">Next steps</span></span>

<span data-ttu-id="b2e9c-202">了解软件定义网络如何为云部署提供虚拟化网络功能。</span><span class="sxs-lookup"><span data-stu-id="b2e9c-202">Learn how Software Defined Networks provide virtualized networking capabilities for cloud deployments.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="b2e9c-203">哪种软件定义网络模式最适合我的部署？</span><span class="sxs-lookup"><span data-stu-id="b2e9c-203">Which Software Defined Network pattern is best for my deployment?</span></span>](../software-defined-network/overview.md)
