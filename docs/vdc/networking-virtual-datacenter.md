---
title: Azure 虚拟数据中心：网络透视图
description: 了解如何在 Azure 中生成虚拟数据中心
author: tracsman
manager: rossort
tags: azure-resource-manager
ms.service: virtual-network
ms.date: 11/28/2018
ms.author: jonor
ms.openlocfilehash: 1d8a9e860ab1a66104dc4133eb5f22ffb4706b84
ms.sourcegitcommit: 5a3fa0bf35376bbe4a6dd668f2d7d44f9cf9c806
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2018
ms.locfileid: "53411678"
---
# <a name="azure-virtual-datacenter-a-network-perspective"></a>Azure 虚拟数据中心：网络透视图

## <a name="overview"></a>概述

将本地应用程序迁移到 Azure 可为组织提供安全且经济高效的基础结构，使组织从中受益，即使在迁移应用程序时只做最低限度的更改。 但是，若要充分利用云计算的灵活性，企业必须提升体系结构，充分利用 Azure 的功能。 

Microsoft Azure 提供超大规模的服务和基础结构、企业级的功能和可靠性。 这些服务和基础结构在混合连接方面提供的选择很多，因此客户可以选择通过公共 Internet 或专用 Azure ExpressRoute 连接访问它们。 Microsoft 合作伙伴还可以通过提供在 Azure 中运行的优化安全服务和虚拟设备实现增强型功能。

客户可以选择通过 Internet 或 Azure ExpressRoute（提供专用网络连接）访问这些云服务。 客户可以使用 Microsoft Azure 平台将基础结构无缝扩展到云中并构建多层体系结构。 Microsoft 合作伙伴还可以通过提供在 Azure 中运行的优化安全服务和虚拟设备实现增强型功能。

## <a name="what-is-the-virtual-datacenter"></a>什么是虚拟数据中心？

在刚推出时时，云实际上是一个平台，用于托管面向公众的应用程序。 企业开始了解云的价值，开始将内部业务线应用程序移到云中。 这些类型的应用程序带来了安全性、可靠性、性能和成本方面的其他考量，这就要求以更灵活的方式提供云服务。 因此，需要设计新的基础结构和网络服务来提供这种灵活性，同时还需要针对缩放、灾难恢复和其他考量的新功能。

## <a name="what-is-a-virtual-datacenter"></a>什么是虚拟数据中心？
最初，云解决方案旨在托管相对独立的单个应用程序，主要涉及公共范畴。 此方法在刚开始的几年富有成效。 后来，云解决方案的优势日益明显，云上托管的大规模工作负荷越来越多。 解决在一个或多个区域进行部署的安全性、可靠性、性能和成本问题成为重中之重，这项任务贯穿了云服务的整个生命周期。

以下云部署关系图中的**红框**展示了安全隐患的示例。 **黄框**展示了跨工作负荷的优化虚拟网络设备的空间。

[![0]][0]

一方面需要通过缩放为企业工作负荷提供支持，另一方面需要处理在公有云中支持大规模应用程序时遇到的问题，正因为此，虚拟数据中心 (VDC) 的概念应运而生。

VDC 实现不仅代表云中的应用程序工作负荷，还代表网络、安全、管理和基础结构（例如 DNS 和目录服务）。 企业在将越来越多的工作负荷迁移到 Azure 时候，必须考虑放置这些工作负荷的支持基础结构和对象。 认真考虑如何结构化资源可以避免激增成百上千的“工作负荷集群”（这种工作负荷集群必须通过独立数据流、安全模型和符合性质询单独管理）。

VDC 概念是一组建议和最佳做法，用于实现一系列独立但相关的实体，这些实体拥有共同的支持功能、特性和基础结构。 从 VDC 的角度来看工作负荷，你会发现：可以通过规模效益来降低成本，可以通过组件和数据流集中化来提高安全性，还可以简化操作、管理和符合性审核过程。

> [!NOTE]
> 必须认识到 VDC **不是**一款离散的 Azure 产品，而是能够满足确切需求的多项特性和功能的组合。 使用 VDC 可以审视工作负荷与 Azure 的用法，以便在云中实现资源和能力的最大化。 它是一种模块化方法，可以在履行企业的组织角色和责任的同时在 Azure 中建立 IT 服务。

在以下情形下，VDC 实现可帮助企业将工作负荷和应用程序迁移到 Azure：

-   托管多个相关工作负荷
-   将工作负荷从本地环境迁移到 Azure。
-   跨工作负荷实现共享化或中心化的安全性和访问要求。
-   为大型企业适当混合 Azure DevOps 和中心化 IT。

解锁 VDC 优势的关键在于中心化拓扑（中心辐射型）和多项 Azure 功能的混合： 

- [Azure 虚拟网络][VNet]。 
- [网络安全组 (NSG)][NSG]。
- [虚拟网络对等互连][VNetPeering]。 
- [用户定义的路由 (UDR)][UDR]。
- 采用[基于角色的访问控制 (RBAC)][RBAC] 的 Azure 标识服务。 
- （可选）[Azure 防火墙][AzFW]、[Azure DNS][DNS]、[Azure Front Door][AFD] 和 [Azure 虚拟 WAN][vWAN]。

解锁 VDC 优势的关键在于中心辐射型中心化网络拓扑，其中混合了多项 Azure 服务和功能：

* [Azure 虚拟网络][VNet]；
* [网络安全组][NSG]；
* [虚拟网络对等互连][VNetPeering]；
* [用户定义的路由 (UDR)][UDR]；
* 具有[基于角色的访问控制 (RBAC)][RBAC] 的 Azure 标识，以及可选的 [Azure 防火墙][AzFW]、[Azure DNS][DNS]、[Azure Front Door][AFD] 和 [Azure 虚拟 WAN][vWAN]。

## <a name="who-should-implement-a-virtual-datacenter"></a>应该由谁来实现虚拟数据中心？

任何已决定采用云的 Azure 客户都可以配置一组供所有应用程序共同使用的资源，从而提高效率。 即使单个应用程序也可充分利用生成 VDC 实现所用的模式和组件，具体取决于数量级。

如果组织有一个中心化 IT、网络、安全性和/或符合性团队/部门，则实现 VDC 可能有助于强制实施策略点和责任分离，在根据你的要求给予应用程序团队尽可能多的自由和控制权的同时，确保基础公共组件的一致性。

期待 DevOps 的组织也可以利用 VDC 概念提供 Azure 资源的授权包，同时确保在相应的组（公共订阅中的订阅组或资源组）内拥有完全控制权，但网络和安全边界必须按照中心 VNet 和资源组内中心化策略的定义保持合规。

## <a name="considerations-for-implementing-a-virtual-datacenter"></a>实现虚拟数据中心的注意事项

设计 VDC 实现时，需考虑下述多个关键问题：

### <a name="identity-and-directory-services"></a>标识和目录服务
标识和目录服务是所有数据中心（本地数据中心和云数据中心）的关键方面。 标识与 VDC 内服务的访问和授权的各个方面相关。 为确保只有获得授权的用户和进程可以访问 Azure 帐户和资源，Azure 使用几种凭据进行身份验证。 这些凭据包括密码（用于访问 Azure 帐户）、加密密钥、数字签名和安全证书。 

### <a name="identity-and-directory-service"></a>标识和目录服务

标识和目录服务是所有数据中心（本地数据中心和云数据中心）的关键。 标识与 VDC 实现中服务的访问和授权的各个方面相关。 为了帮助确保只有获得授权的用户和进程可以访问 Azure 帐户和资源，Azure 使用几种凭据进行身份验证。 这些凭据包括密码（用于访问 Azure 帐户）、加密密钥、数字签名和安全证书。 [Azure 多重身份验证 (MFA)][MFA] 是一个附加安全层，针对 Azure 服务的访问。 Azure MFA 通过一系列简单的验证选项（电话、短信或移动应用通知）提供强身份验证，客户可根据自己的偏好选择所用的方法。

任何大型企业都需要定义标识管理流程，说明如何在 VDC 实现内部或跨 VDC 实现管理各个标识及其身份验证、授权、角色和权限。 其目标是在减少成本、停机时间和重复人工作业的同时提高安全性和工作效率。

企业/组织可以要求严格混合针对不同业务线 (LOB) 的服务，员工在参与不同的项目时通常会有不同的角色。 VDC 要求不同的团队（每个团队拥有特定的角色定义）精诚合作，确保系统运行并很好地监管。 责任、访问和权限矩阵可能很复杂。 VDC 中的标识管理通过 [*Azure Active Directory* (Azure AD)][AAD] 和基于角色的访问控制 (RBAC) 来实现。

目录服务是用于查找、控制、管理和组织日常项目和网络资源的共享信息基础结构。 这些资源包括卷、文件夹、文件、打印机、用户、组、设备和其他对象。 目录服务器将网络上的每个资源视作一个对象。 资源信息作为与该资源或对象相关的属性集合存储。

所有 Microsoft 联机业务服务都依赖于 Azure Active Directory (Azure AD) 进行登录及满足其他标识需求。 Azure Active Directory 是一个综合性的、高度可用的标识和访问管理云解决方案，它将核心目录服务、高级标识监管和应用程序访问管理结合起来。 Azure AD 可与本地 Active Directory 集成，为基于云的和本地托管的所有应用程序启用单一登录。 本地 Active Directory 的用户属性可以自动同步到 Azure AD。

在 VDC 实现中分配所有权限不需要单个全局管理员。 与之相反，可以为目录服务中的每个具体部门、用户组或服务分配在 VDC 实现中管理其自己的资源所需的权限。 权限的结构化需要均衡。 权限过多会妨碍性能效率，权限过少或过松则会增加安全风险。 Azure 基于角色的访问控制 (RBAC) 可以为 VDC 实现中的资源提供细致的访问管理，因此有助于解决此问题。

#### <a name="security-infrastructure"></a>安全基础结构

安全基础结构是指 VDC 实现的特定虚拟网络段中的流量分离。 此基础结构指定如何在 VDC 实现中控制流入量和流出量。 Azure 基于多租户基础结构，该基础结构通过使用虚拟网络 (VNet) 隔离、访问控制列表 (ACL)、负载均衡器、IP 筛选器和流量流策略，防止部署间的未经授权流量和意外流量。 网络地址转换 (NAT) 将内部网络流量与外部流量分开。

Azure 结构将基础结构资源分配到租户工作负荷，并管理与虚拟机 (VM) 的通信。 Azure 虚拟机监控程序会在 VM 之间强制实施内存和进程隔离，并将网络流量安全路由到来宾 OS 租户。

#### <a name="connectivity-to-the-cloud"></a>与云的连接

VDC 实现需要连接到外部网络才能为客户、合作伙伴和/或内部用户提供服务。 这里的需要连接是指不仅需要连接到 Internet，而且需要连接到本地网络和数据中心。

客户可以使用 [Azure 防火墙][AzFW]或其他类型的虚拟网络设备 (NVA)、结合使用[用户定义的路由][UDR]和自定义路由策略，以及结合使用[网络安全组][NSG]和网络筛选，来控制哪些服务有权访问公共 Internet，以及可通过公共 Internet 访问这些服务。 我们建议使用 [Azure DDoS 防护标准版][DDOS]来进一步保护所有面向 Internet 的资源。

企业可能需要将其 VDC 实现连接到本地数据中心或其他资源。 Azure 与本地网络的这种连接是设计高效体系结构的关键。 企业可以通过两种不同方式实现此互连：通过 Internet 和/或专用直接连接进行传输。

[**Azure 站点到站点 VPN**][VPN] 是使用安全加密连接（IPsec/IKE 隧道）通过 Internet 在本地网络与 VDC 实现之间建立的互连服务。 由于所有连接都通过 Internet 实现，因此 Azure 站点到站点的连接创建起来灵活而快速，不需要任何进一步的采购。

使用大量的 VPN 连接时，[**Azure 虚拟 WAN**][vWAN] 可充当网络服务，通过 Azure 提供经优化的、自动的分支到分支连接。 使用虚拟 WAN 可以连接分支设备，并将其配置为与 Azure 通信。 连接和配置可以通过手动方式来完成，也可以通过虚拟 WAN 合作伙伴使用首选的提供商设备来完成。 使用首选的提供商设备可以降低操作难度、简化连接和进行配置管理。 Azure WAN 的内置仪表板提供即时故障排除见解，可帮助你节省时间，以及方便查看大规模的站点到站点连接。

[**ExpressRoute**][ExR] 是一种 Azure 连接服务，可在 VDC 实现与任何本地网络之间建立专用连接。 ExpressRoute 连接不通过公共 Internet，从而提供更高的安全性、可靠性、更快的速度（最高可达 10 Gbps）和一致延迟。 ExpressRoute 适用于 VDC 实现，因为 ExpressRoute 客户可以受益于与专用连接相关联的符合性规则。 对于带宽需求较高的客户，你可以使用 ExpressRoute Direct,][ExRD] 以 100 Gbps 的速率直接连接到 Microsoft 路由器。

部署 ExpressRoute 连接通常要与 ExpressRoute 服务提供商合作。 需要快速开始通信的客户往往是先使用站点到站点 VPN 在 VDC 实现与本地资源之间建立连接，然后在完成与服务提供商的物理互连后迁移到 ExpressRoute 连接。

#### <a name="connectivity-within-the-cloud"></a>*云内的连接*

[VNet][VNet] 和 [VNet 对等互连][VNetPeering]是 VDC 实现中的基本网络连接服务。 VNet 保证 VDC 资源具有自然隔离边界，VNet 对等互连允许同一 Azure 区域或不同区域的不同 VNet 相互通信。 VNet 内部以及 VNet 之间的流量控制需要与通过访问控制列表（[网络安全组][NSG]）、[网络虚拟设备][NVA]和自定义路由表 ([UDR][UDR]) 指定的一组安全规则匹配。

## <a name="virtual-datacenter-overview"></a>虚拟数据中心概述

### <a name="topology"></a>拓扑

中心辐射模型用于设计虚拟数据中心实现的网络拓扑。 

[![1]][1]

中心是控制和检查不同区域（Internet、本地和辐射）之间入口或出口流量的中心网络区域。 中心辐射型拓扑为 IT 部门提供了在中心位置强制实施安全策略的有效方式。 此外，还减少了配置错误和泄密的可能性。

中心包含辐射使用的常见服务组件。 下面是常见中心服务的示例：

-   Windows Active Directory 基础结构：第三方从不受信任网络访问时，要求进行用户身份验证，验证通过后才可访问辐射中的工作负荷。 这包括相关的 Active Directory 联合身份验证服务 (AD FS)。
-   用于解析分支中工作负荷的命名，以便在 [Azure DNS][DNS] 不可用时访问本地和 Internet 上的资源的 DNS 服务。
-   用于实现工作负荷的单一登录的公钥基础结构 (PKI)。
-   辐射网络区域与 Internet 之间的 TCP 和 UDP 流量的流控制。
-   辐射与本地之间的流控制。
-   根据需要在一个辐射与另一个辐射之间进行流控制。

VDC 通过在多个分支之间使用共享中心基础结构来降低总体成本。

每个辐射的角色可以是托管不同类型的工作负荷。 辐射还可为同一工作负荷的可重复部署提供模块化方法。 示例包括开发和测试、用户验收测试、预生产和生产。 辐射还可用于隔离和启用组织内部的不同组。 示例包括 Azure DevOps 组。 在辐射内，可以通过各层之间的流量控制部署基本工作负荷或复杂的多层工作负荷。

#### <a name="subscription-limits-and-multiple-hubs"></a>订阅限制和多个中心

在 Azure 中，每个组件（无论何种类型）都通过 Azure 订阅进行部署。 不同 Azure 订阅中的 Azure 组件的隔离可满足不同 LOB 的需求，如设置不同级别的访问和授权。

尽管与每个 IT 系统一样具有平台限制，单个 VDC 实现仍能通过纵向扩展拥有大量分支。 中心部署与特定的 Azure 订阅绑定，该订阅具有相关的约束和限制（如 VNet 对等互连上限 - 请参阅 [Azure 订阅和服务限制、配额和约束条件][Limits]获取详细信息）。 如果出现限制问题，体系结构可以通过将模型从单个中心辐射型扩展为中心辐射群集实现进一步纵向扩展。 位于一个或多个 Azure 区域的多个中心可以使用 VNet 对等互连、ExpressRoute、虚拟 WAN 或站点到站点 VPN 实现互连。

[![2]][2]

引入多个中心会增加系统的成本和管理工作量。 引入的唯一原因在于可伸缩性（如系统限制或冗余）和区域复制（如最终用户性能或灾难恢复）。 在要求多个中心的情形下，所有中心应力图提供相同的服务集以方便操作。

#### <a name="interconnection-between-spokes"></a>辐射之间的互连

在单个辐射内，可以实现复杂的多层工作负荷。 在同一 VNet 内使用子网（每层使用一个子网）并使用 NSG 对流进行筛选可以实现多层配置。

架构师可能希望跨多个虚拟网络部署多层工作负荷。 使用虚拟网络对等互连，辐射可以连接到同一中心的其他辐射，也可连接到其他中心的其他辐射。 此方案的典型示例是应用程序处理服务器位于一个辐射或虚拟网络中。 数据库部署在另一个辐射或虚拟网络中。 在这种情况下，可以轻松实现辐射与虚拟网络对等互连之间的互连，从而避免通过中心传输。 应仔细审查体系结构和安全性，确保绕过中心并不会绕过可能只存在于中心的重要安全或审核点。

[![3]][3]

辐射也可以与充当中心的辐射互连。 此方法可以创建一个两层的层次结构：在该层次结构中，较高级别（第 0 层）的辐射成为较低级别（第 1 层）辐射的中心。 需要通过 VDC 实现的分支将流量转发到中心，然后才能将流量传输到本地网络或公共 Internet 中的目标。 具有两层中心的体系结构引入了复杂路由，该路由会消除简单中心辐射关系的优势。

尽管 Azure 允许复杂拓扑，但 VDC 概念的核心原则之一是可重复性和简洁性。 为了尽量减少管理工作量，我们建议使用简单的中心辐射设计作为 VDC 参考体系结构。

### <a name="components"></a>组件

虚拟数据中心由下述四个基本组件类型构成：基础结构、外围网络、工作负荷和监视。

每个组件类型由各种 Azure 功能和资源组成。 VDC 实现由多个组件类型的实例和同一组件类型的多个变体构成。 例如，可能有许多逻辑上分离的不同工作负荷实例表示不同应用程序。 使用这些不同的组件类型和实例最终生成 VDC。

[![4]][4]

上图中 VDC 的概览性概念体系结构显示了中心辐射型拓扑的不同区域使用的不同组件类型。 此关系图显示了该体系结构中各个部分的基础结构组件。

访问权限和特权应该基于组，这是常规情况下的适当做法。 使用组而不是单个用户可以简化访问策略的维护，因为可以跨团队对其进行一致的管理，  并可尽量减少配置错误。 在相应的组中分配或删除用户有助于使特定用户的权限保持最新。

每个角色组的名称应具有唯一的前缀。 参考此前缀可以轻松识别哪个组与哪个工作负荷相关联。 例如，托管身份验证服务的工作负荷应具有名为 **AuthServiceNetOps**、**AuthServiceSecOps**、**AuthServiceDevOps** 和 **AuthServiceInfraOps** 的组。 中心化角色或不与特定服务相关的角色的名称可以 **Corp** 开头。例如 **CorpNetOps**。

许多组织都使用以下组的变体来提供主要角色细分：

-   中心 IT 组 **Corp** 具有可控制基础结构组件的所有权。 示例包括网络和安全性。 该组需要在订阅中拥有参与者角色、拥有中心控制权，并在辐射中拥有网络参与者权限。 大型组织经常在多个团队之间划分这些管理责任。 例如，专门处理网络的网络操作 **CorpNetOps** 组，以及负责防火墙和安全策略的安全操作 **CorpSecOps** 组。 在这种特定情况下，需要创建两个不同的组，用于分配这些自定义角色。
-   开发与测试组 **AppDevOps** 负责部署应用或服务工作负荷。 此组充当 IaaS 部署的虚拟机参与者角色或者一个或多个 PaaS 参与者角色。 请参阅 [Azure 资源的内置角色][Roles]。 （可选）开发与测试团队可能需要对中心或特定辐射内的安全策略 (NSG) 和路由策略 (UDR) 拥有可见性。 除了担任工作负荷参与者角色，此组也需要担任网络读取者角色。
-   操作和维护组 **CorpInfraOps** 或 **AppInfraOps** 负责管理生产中的工作负荷。 此组必须是任何生产订阅中工作负荷的订阅参与者。 一些组织还可能评估他们是否需要在生产和中心订阅中担任订阅参与者角色的附加升级支持团队组。 该附加组修复生产环境中的潜在配置问题。

设计 VDC 时，需确保在创建组时要创建用于管理中心的中心 IT 组，同时在工作负荷级别也要有相应的组。 除了管理中心资源，中心 IT 组还能控制订阅的外部访问和顶级权限。 工作负荷组还能在中心 IT 内独立地控制其 VNet 的资源和权限。

将 VDC 分区可以安全地托管跨不同业务线 (LOB) 的多个项目。 所有项目需要不同的独立环境（如开发、UAT 和生产）。 每个环境的各个 Azure 订阅自然隔离。

[![5]][5]

上方关系图显示组织中项目、用户、组以及部署 Azure 组件的环境之间的关系。

在 IT 中，环境（或层）通常是部署和执行多个应用程序的系统。 大型企业使用开发环境（最初做出更改和测试的环境）和生产环境（最终用户使用的环境）。 这些环境相互隔离，环境之间通常存在多个过渡环境，出现问题时可实现分阶段部署（推出）、测试和回退。 部署体系结构之间存在巨大差异，但通常遵循以下基本流程：始于开发 (DEV)，终于生产 (PROD)。

此类多层环境的常见体系结构包括 Azure DevOps（开发和测试）、UAT（过渡）和生产环境。 组织可以利用单个或多个 Azure AD 租户定义对这些环境的访问和权限。 上方关系图显示使用两个不同 Azure AD 租户的事例：一个用于 Azure DevOps 和 UAT，另一个专门用于生产。

不同的 Azure AD 租户可以强化环境之间的隔离。 同一组用户（如中心 IT）需要使用不同 URI 进行身份验证，从而访问不同的 Azure AD 租户，该租户可修改项目的 Azure DevOps 或生产环境的角色或权限。 通过不同的用户身份验证访问不同的环境可以减少人为失误可能导致的中断和其他问题。

#### <a name="component-type-infrastructure"></a>组件类型：基础结构

大多数支持基础结构都位于此组件类型中。 中心化 IT、安全团队和/或符合性团队的大部分时间花费在该类型上。

[![6]][6]

基础结构组件为 VDC 实现的不同组件提供互连，在中心和分支中都存在。 管理和维护基础结构组件的责任通常分配给中心 IT 和/或安全团队。

IT 基础结构团队的首要任务之一是保证整个企业 IP 地址架构的一致性。 分配给 VDC 实现的专用 IP 地址空间必须一致，并且不能与本地网络上分配的专用 IP 地址重叠。

NAT 在本地边缘路由器或 Azure 环境中时可避免 IP 地址冲突，但会增加基础结构组件的复杂性。 简化管理是 VDC 的关键目标之一，因此不建议使用 NAT 处理 IP 问题。

基础结构组件提供以下功能：

-   [**标识和目录服务**][AAD]。 对 Azure 中的每种资源类型的访问都由目录服务中存储的标识控制。 目录服务不仅存储用户列表，也存储特定 Azure 订阅中资源的访问权限。 这些服务可只存在于云中，也可以与 Active Directory 中存储的本地标识同步。
-   [**虚拟网络**][VPN]。 虚拟网络是 VDC 的主要组件之一，可以用来在 Azure 平台创建流量隔离边界。 虚拟网络由单个或多个虚拟网络段构成，每个虚拟网络段都有一个特定的 IP 网络前缀（子网）。 虚拟网络定义内部外围区域，在该区域中 IaaS 虚拟机和 PaaS 服务可以建立专用通信。 一个虚拟网络中的 VM（和 PaaS 服务）不能与另一个虚拟网络中的 VM（和 PaaS 服务）直接通信，即使这两个虚拟网络是由同一个客户在同一订阅下创建的。 隔离是一个非常关键的属性，可确保客户 VM 与通信在虚拟网络中保持私密性。
-   [**UDR**][UDR]。 虚拟网络中的流量默认基于系统路由表路由。 用户定义的路由是自定义路由表，网络管理员可以将该路由表关联到一个或多个子网，覆盖系统路由表的行为并定义虚拟网络中的通信路径。 UDR 保证辐射的出口流量通过中心和辐射中的特定自定义 VM 和/或虚拟网络设备以及负载均衡器传输。
-   [**NSG**][NSG]。 网络安全组是一系列安全规则，充当 IP 源、IP 目标、协议、IP 源端口和 IP 目标端口的流量筛选器。 NSG 可应用于子网和/或与 Azure VM 关联的虚拟 NIC 卡。 NSG 对在中心和辐射中实现正确的流控制至关重要。 NSG 提供的安全级别由出于何种原因打开哪个端口决定。 客户应通过基于主机的防火墙（如 IPtable 或 Windows 防火墙）对每个 VM 应用附加筛选器。
-   [**DNS**][DNS]。 VDC 实现中 VNet 资源的名称解析通过 DNS 提供。 Azure 提供 DNS 服务用于执行[公共][DNS]和[专用][PrivateDNS]名称解析。 专用区域在虚拟网络内或虚拟网络之间提供名称解析功能。 可以让专用区域跨同一区域的虚拟网络，以及跨区域和跨订阅。 对于公用解析，Azure DNS 提供 DNS 域的托管服务，它使用 Microsoft Azure 基础结构提供名称解析。 通过在 Azure 中托管域，可以使用与其他 Azure 服务相同的凭据、API、工具和计费来管理 DNS 记录。
-   [**订阅**][SubMgmt]和[**资源组管理**][RGMgmt]。 订阅定义在 Azure 创建多个资源组的自然边界。 订阅中的资源聚集在名为资源组的逻辑容器中。 资源组表示组织 VDC 实现资源的逻辑组。
-   [**RBAC**][RBAC]。 通过 RBAC，可以映射组织角色以及访问特定 Azure 资源的权限，允许限制用户只进行特定子网操作。 使用 RBAC，可以通过将相应的角色分配给相关范围内的用户、组和应用程序授予访问权限。 角色分配的范围可以是 Azure 订阅、资源组或单个资源。 RBAC 允许继承权限。 分配在父范围内的角色也会将访问权限授予给其中所含的子范围。 使用 RBAC，可以对职责进行分配，仅向用户授予执行作业所需的访问权限。 例如，使用 RBAC 允许一个员工管理订阅中的虚拟机，而允许另一个员工管理同一订阅中的 SQL DB。
-   [**VNet 对等互连**][VNetPeering]。 用于创建 VDC 基础结构的基本功能是 VNet 对等互连，它是一种通过 Azure 数据中心网络或跨区域的 Azure 全球主干网络连接同一区域内两个虚拟网络 (VNet) 的机制。

#### <a name="component-type-perimeter-networks"></a>组件类型：外围网络

使用[外围网络][DMZ]组件可以在本地的或实际的数据中心网络之间建立网络连接，以及与 Internet 建立连接。 它也是网络和安全团队花费大部分时间的地方。

传入数据包应该在流经中心的安全设备之后才到达辐射中的后端服务器。 示例包括防火墙、IDS 和 IPS。 来自工作负荷的 Internet 绑定数据包在离开网络之前，也应流经外围网络中的安全设备。 这种流动方式的目的是执行策略实施、检查和审核。

外围网络组件提供以下功能：

-   [虚拟网络][VNet]、[UDR][UDR] 和 [NSG][NSG]
-   [网络虚拟设备][NVA]
-   [Azure 负载均衡器][ALB]
-   [Azure 应用程序网关][AppGW]和 [Web 应用程序防火墙 (WAF)][WAF]
-   [公共 IP][PIP]
-   [Azure Front Door][AFD]
-   [Azure 防火墙][AzFW]

通常，中心 IT 和安全团队负责外围网络的需求定义和操作。

[![7]][7]

上图显示了如何实施可以访问 Internet 和本地网络（两者都位于外围网络和 vWAN 中心）的两个外围。 在外围网络中心，可以使用 Web 应用程序防火墙 (WAF) 和/或 Azure 防火墙的多个场来扩展连接到 Internet 的外围网络，以支持大量的 LOB。 在 vWAN 中心，可以根据需要通过 VPN 或 ExpressRoute 来实现高度可缩放的分支到分支连接和分支到 Azure 连接。

[**虚拟网络**][VNet]。 通常基于虚拟网络构建中心，该虚拟网络提供多个子网用于托管不同类型的服务，并通过 NVA、WAF 和 Azure 应用程序网关实例筛选和检查流入或流出 Internet 的流量。

[**UDR**][UDR] 客户可以使用 UDR 部署防火墙、IDS/IPS 和其他虚拟设备，并通过这些安全设备路由网络流量，以实施、审核和检查安全边界策略。 可以同时在中心和分支中创建 UDR，保证流量通过 VDC 实现使用的特定自定义 VM、网络虚拟设备和负载均衡器传输。 若要保证由辐射中的 VM 生成的流量传输到正确的虚拟设备，需要通过将内部负载均衡器的前端 IP 地址设置为下一个跃点在辐射的子网中设置 UDR。 内部负载均衡器将内部流量分配到虚拟设备（负载均衡器后端池）。

[**Azure 防火墙**][AzFW]是托管的基于云的网络安全服务，可保护 Azure 虚拟网络资源。 它是一个服务形式的完全有状态防火墙，具有内置的高可用性和不受限制的云可伸缩性。 可以跨订阅和虚拟网络集中创建、实施和记录应用程序与网络连接策略。 Azure 防火墙对虚拟网络资源使用静态公共 IP 地址。 它允许外部防火墙识别源自虚拟网络的流量。 该服务与用于日志记录和分析的 Azure Monitor 完全集成。

[**网络虚拟设备**][NVA]。 在中心内，可访问 Internet 的外围网络通常是通过 Azure 防火墙实例或防火墙或 Web 应用程序防火墙 (WAF) 的场管理的。

不同的 LOB 通常使用许多 Web 应用程序。 这些应用程序容易出现各种漏洞和遭受潜在攻击。 Web 应用程序防火墙是一种特殊产品，相较通用防火墙而言，它能够更深入的检测对 Web 应用程序 (HTTP/HTTPS) 的攻击。 与传统防火墙技术相比，WAF 拥有一组用于保护内部 Web 服务器免受威胁的特定功能。

Azure 防火墙或 NVA 防火墙使用通用的管理平面，提供一组安全规则来保护分支中托管的工作负荷，并控制对本地网络的访问。 Azure 防火墙内置了可伸缩性，对于 NVA 防火墙，可在负载均衡器后面手动进行缩放。 一般而言，与 WAF 相比，防火墙场的专用软件更少，但应用范围广泛，可以筛选和检查出口和入口的任何流量类型。 如果使用 NVA 方法，可以在 Azure 市场中查找和部署相关软件。

请对源自 Internet 的流量使用一组 Azure 防火墙（或 NVA），对源自本地的流量使用另一组 Azure 防火墙。 对两组网络流量使用一组防火墙会导致安全风险，因为它无法在两组网络流量之间提供安全外围。 使用不同防火墙层可以降低检查安全规则的复杂性，并指明哪项规则与哪项传入网络请求对应。

我们建议对源自 Internet 的流量使用一组 Azure 防火墙实例或 NVA。 对源自本地的流量使用另一组 Azure 防火墙或 NVA。 仅对两组网络流量使用一组防火墙会存在安全风险，因为它不会在两组网络流量之间提供安全外围。 使用不同防火墙层可以降低检查安全规则的复杂性，并指明哪项规则与哪项传入网络请求对应。

[**Azure 负载均衡器**][ALB]提供高度可用的第 4 层（TCP 和 UDP）服务，该服务可以在负载均衡集定义的服务实例间分配传入流量。 可以将由前端终结点（公共 IP 终结点或专用 IP 终结点）发送到负载均衡器的流量重新分配（通过/不通过地址转换）到一组后端 IP 地址池（如网络虚拟设备或 VM）。

Azure 负载均衡器也可以探测各种服务器实例的运行状态，当探测无法响应时，负载均衡器会停止向不正常的实例发送流量。 在 VDC 中，外部负载均衡器部署到中心和分支。 中心的负载均衡器用于将流量高效地路由到分支中的服务，而分支的负载均衡器则用于管理应用程序流量。

[**Azure Front Door**][AFD] (AFD) 是 Microsoft 提供的高度可用且可缩放的 Web 应用程序加速平台、全局 HTTP 负载均衡器、应用程序防护和内容分发网络。 AFD 在 Microsoft 全球网络边缘中的 100 多个位置运行，可用于生成、运行和横向扩展动态 Web 应用程序和静态内容。 AFD 为应用程序提供一流的最终用户性能、统一的区域/戳维护自动化、BCDR 自动化、统一的客户端/用户信息、缓存和服务见解。 该平台提供性能、可靠性和支持方面的 SLA、符合性认证，以及由 Azure 原生制定、运行和支持的可审核安全做法。

[**应用程序网关**][AppGW] Microsoft Azure 应用程序网关是一个专用的虚拟设备，以服务形式提供应用程序传送控制器 (ADC)，并为应用程序提供各种 7 层负载均衡功能。 可以用它将 CPU 密集型 SSL 终点卸载到应用程序网关，优化 Web 场工作效率。 它还提供其他第 7 层路由功能，包括传入流量的轮循机制分配、基于 Cookie 的会话相关性、基于 URL 路径的路由，以及在单个应用程序网关后面托管多个网站的能力。 Web 应用程序防火墙 (WAF) 也作为 WAF SKU 应用程序网关的一部分提供。 此 SKU 可保护 Web 应用程序免受 Web 常见漏洞和攻击的影响。 可以将应用程序网关配置为面向 Internet 的网关、仅内部网关或这两者的组合。 

[**应用程序网关**][AppGW]是一个专用的虚拟设备，以服务形式提供应用程序传送控制器 (ADC)，为应用程序提供各种第 7 层负载均衡功能。 可将 CPU 密集型 SSL 终点卸载到应用程序网关实例，优化 Web 场工作效率。 它还提供其他第 7 层路由功能，示例包括： 
* 传入流量的轮循机制分配。 
* 基于 Cookie 的会话关联。 
* 基于 URL 路径的路由。 
* 能够在单个应用程序网关实例后面托管多个网站 Web 应用程序防火墙 (WAF) 也作为应用程序网关 WAF SKU 的一部分提供。 此 SKU 可保护 Web 应用程序免受 Web 常见漏洞和攻击的影响。 可将应用程序网关配置为面向 Internet 的网关、仅限内部访问的网关或二者合一的网关。 

[**公共 IP**][PIP]。 使用某些 Azure 功能，可将服务终结点关联到某个公共 IP 地址，以便可以从 Internet 访问资源。 该终结点使用网络地址转换 (NAT) 将流量路由到 Azure 虚拟网络上的内部地址和端口。 此路径是外部流量进入虚拟网络的主要方式。 可以配置公共 IP 地址，以确定可以传入哪种流量、如何在虚拟网络上转换该流量以及要将它路由到何处。

[**Azure DDoS 防护标准版**][DDOS]在[基本服务][DDOS]层的基础上，提供了专门针对 Azure 虚拟网络资源优化的更多风险缓解功能。 DDoS 保护标准易于启用，无需更改应用程序。 通过专用流量监控和机器学习算法优化保护策略。 策略应用到与虚拟网络中部署的资源关联的公共 IP 地址。 示例包括 Azure 负载均衡器、Azure 应用程序网关和 Azure Service Fabric 实例。 可在攻击期间通过 Azure Monitor 视图并针对历史记录获得实时遥测。 可通过 Azure 应用程序网关 Web 应用程序防火墙来添加应用程序层保护。 为 IPv4 Azure 公共 IP 地址提供保护。

#### <a name="component-type-monitoring"></a>组件类型：监视

监视组件可以监视所有其他组件类型并报警。 所有团队都应有权监视他们有权访问的组件和服务。 如果你拥有中心化支持人员或操作团队，他们需要对这些组件提供的数据进行集成访问。

Azure 提供不同类型的记录和监视服务，跟踪 Azure 托管资源的行为。 对 Azure 中工作负荷的管理和控制不仅只基于收集日志数据，也基于根据特定报告事件触发操作的能力。

[**Azure Monitor**][Monitor]。 Azure 包括多项可以在监视空间单独执行特定角色或任务的服务。 这些服务组合在一起就可以提供一个全面的解决方案，用于从应用程序以及支持应用程序的 Azure 资源收集遥测数据，对这些数据进行分析并执行操作。 这些服务还监视关键的本地资源，以提供混合监视环境。 为应用程序开发完整监视策略的第一步是了解可用的工具和数据。

Azure 中主要有两种日志类型：

-   [Azure 活动日志][ActLog]（前称为“运行日志”）提供相关信息，方便用户了解对 Azure 订阅中的资源执行的操作。 这些日志报告订阅的控制平面事件。 每个 Azure 资源都会生成审核日志。

-   [Azure Monitor 诊断日志][DiagLog]是资源生成的日志，记录与该资源的操作相关的各种频繁生成的数据。 这些日志的内容因资源类型而异。

[![9]][9]

跟踪 NSG 日志非常重要，尤其是以下信息：

-   [事件日志][NSGLog]提供根据 MAC 地址向 VM 和实例角色应用的 NSG 规则的相关信息。
-   [计数器日志][NSGLog]跟踪拒绝或允许流量的每个 NSG 规则的运行次数。

所有日志都可以存储在 Azure 存储帐户中，以便进行审核、静态分析或备份。 将日志存储在 Azure 存储帐户中时，客户可以对该数据使用不同类型的框架进行检索、准备、分析和可视化操作，以报告云资源的状态和运行状况。 

大型企业应已获取用于监视本地系统的标准框架。 它们可以扩展该框架以集成云部署生成的日志。 组织可以使用 [Azure Log Analytics][https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-queries] 将所有日志保留在云中。 Log Analytics 作为基于云的服务实现。 因此，对基础结构服务进行极少量的投资即可快速使其启动并运行。 Log Analytics 还可与 System Center Operations Manager 等 System Center 组件集成，将现有管理投资扩展到云。 

Log Analytics 是 Azure 中的一个服务，可帮助收集、关联、搜索以及处理由操作系统、应用程序和基础结构云组件生成的日志和性能数据。 它使用集成搜索和自定义仪表板为客户提供实时操作见解，分析 VDC 实现中所有工作负荷的所有记录。

[Azure 网络观察程序][NetWatch]提供所需的工具用于监视、诊断 Azure 虚拟网络中的资源、查看其指标，以及为其启用或禁用日志。 它是一个多面性的服务，可以实现下述和其他功能：
-    监视虚拟机与终结点之间的通信。
-    查看虚拟网络中的资源及其关系。
-    诊断传入或传出 VM 的网络流量筛选问题。
-    诊断 VM 的网络路由问题。
-    诊断 VM 的出站连接。
-    捕获传入和传出 VM 的数据包。
-    诊断 Azure 虚拟网络网关和连接的问题。
-    确定 Azure 区域与 Internet 服务提供商之间的相对延迟。
-    查看网络接口的安全规则。
-    查看网络指标。
-    分析传入或传出网络安全组的流量。
-    查看网络资源的诊断日志。

Operations Management Suite 中的[网络性能监视器][NPM]解决方案提供详细的端到端网络信息。 此信息包括 Azure 网络和本地网络的单个视图。 该解决方案为 ExpressRoute 和公共服务提供特定的监视器。

#### <a name="component-type-workloads"></a>组件类型：工作负荷

工作负荷组件是实际应用程序和服务所在的位置。 它也是应用程序开发团队花费大部分时间的地方。

工作负荷的可能性是无限的。 以下是几种可能的工作负荷类型：

**内部 LOB 应用程序**：业务线应用程序是对企业的日常运营十分重要的计算机应用程序。 LOB 应用程序具有以下常见特征：

-   **交互性**。 输入数据，然后返回结果或报告。
-   **数据驱动** &mdash; 数据密集型应用程序，频繁访问数据库或其他存储。
-   **可集成** &mdash; 可与组织内外的其他系统集成。

**面向客户的网站（面向 Internet 或内部）**：与 Internet 交互的大多数应用程序是网站。 Azure 提供在 IaaS VM 上或从 [Azure Web 应用][WebApps]站点 (PaaS) 运行网站的功能。 Azure Web 应用支持与允许在分支网络区域中部署 Web 应用的 VNet 集成。 面向内部的网站不需公开公共 Internet 终结点，因为可以从专用 VNet 通过不可经 Internet 路由的专用地址访问资源。

**大数据/分析**：当数据需要增加到较大的量时，数据库可能无法正常地纵向扩展。 Hadoop 技术提供可在大量节点上并行运行所分配查询的系统。 客户可以选择在 IaaS VM 或 PaaS ([HDInsight][HDI]) 中运行数据工作负荷。 HDInsight 支持部署到基于位置的 VNet，可以部署到 VDC 分支中的群集。

**事件和消息传送**：[Azure 事件中心][EventHubs]是超大规模的遥测引入服务，可收集、传输和存储数百万的事件。 作为分布式流式处理平台，它提供低延迟和可配置的保留时间，使用户可以将大量遥测数据引入到 Azure 中，并从多个应用程序中读取数据。 通过事件中心，单个流可以同时支持实时管道和基于批处理的管道。

可以通过 [Azure 服务总线][ServiceBus]在应用程序与服务之间实施高度可靠的云消息传送服务。 服务总线提供客户端与服务器之间的异步中转消息传送、结构化先进先出 (FIFO) 消息传送和发布/订阅功能。

[![10]][10]

### <a name="making-the-vdc-highly-available-multiple-vdcs"></a>使 VDC 高度可用：多个 VDC

到目前为止，本文一直在重点讲述如何设计单个 VDC，介绍形成复原能力的基本组件和体系结构。 Azure 功能（如 Azure 负载均衡器、NVA、可用性集、规模集和其他机制）构成一个允许在生产服务中生成稳定 SLA 级别的系统。

然而，由于单个 VDC 通常在单个区域中实现，因此易受到影响整个区域的重大故障的影响。 对 SLA 级别要求高的客户必须将同一项目部署在不同区域的两个（或更多个）VDC 实现中，通过这种方式来保护服务。

除了 SLA 问题之外，以下几种常见情形也适合部署多个 VDC 实施方案：

-   区域或全球影响力。
-   灾难恢复。
-   在数据中心之间转移流量的机制。

#### <a name="regionalglobal-presence"></a>区域/全球影响力

Azure 数据中心遍布世界各地。 选择多个 Azure 数据中心时，客户需要考虑两个相关因素：地理距离和延迟。 为了提供最佳用户体验，请评估每个 VDC 实现之间的地理距离，以及每个 VDC 实现与最终用户之间的距离。

托管 VDC 实现的区域必须符合组织所属的法律管辖区域制定的法规要求。

#### <a name="disaster-recovery"></a>灾难恢复

灾难恢复计划的设计取决于工作负荷的类型以及能否在不同 VDC 实现之间同步这些工作负荷的状态。 理想情况下，大多数客户希望使用快速故障转移机制，这就要求在运行多个 VDC 实现的部署之间同步应用程序数据。 但是，在设计灾难恢复计划时，必须考虑到大多数应用程序对该数据同步可能导致的延迟很敏感。

在不同 VDC 实现中对应用程序进行同步和检测信号监视时，这些实现必须通过网络通信。 可通过以下方式连接不同区域中的两个 VDC 实现：

-   VNet 对等互连 - VNet 对等互连可以跨区域连接多个中心
-   ExpressRoute 专用对等互连：在每个 VDC 实现中的多个中心连接到同一 ExpressRoute 线路时使用
-   通过企业主干连接的多个 ExpressRoute 线路，以及连接到 ExpressRoute 线路的多个 VDC 实现
-   在每个 Azure 区域的 VDC 实现的中心区域之间建立的站点到站点 VPN 连接

通常情况下，VNet 对等互连或 ExpressRoute 连接是首选的网络连接类型，因为通过 Microsoft 主干传输时的带宽更高，且延迟程度也一致。

建议客户通过运行网络资格测试来验证这些连接的延迟和带宽，然后根据结果来确定是适合同步数据复制还是异步数据复制。 此外还必须根据最佳恢复时间目标 (RTO) 来衡量这些结果。

#### <a name="disaster-recovery-diverting-traffic-from-one-region-to-another"></a>灾难恢复：将流量从一个区域转移到另一个区域

[Azure 流量管理器][TM]定期检查不同 VDC 实现中公共终结点的服务运行状况。如果这些终结点出现故障，它会通过域名系统 (DNS) 将流量自动路由到辅助 VDC。 

流量管理器使用 DNS，因此只能与 Azure 公共终结点配合使用。  此服务通常用于控制流量，或将流量转移到 VDC 实现的正常实例中的 Azure VM 和 Web 应用。 流量管理器具有复原能力，即使整个 Azure 区域出现故障，也可根据多个标准控制不同 VDC 中服务终结点的用户流量分配。 例如，特定 VDC 实现中出现了服务故障，或选择了网络延迟最低的 VDC 实现。

### <a name="summary"></a>摘要

可以通过虚拟数据中心进行数据中心迁移，在 Azure 中创建可缩放的体系结构，最大程度地使用云资源、降低成本并简化系统治理。 VDC 基于中心辐射型网络拓扑，在中心提供常见的共享服务并允许分支中存在特定的应用程序/工作负荷。 VDC 还可以与企业角色的结构匹配，方便不同的部门（例如中心 IT 部门、DevOps 部门、运营和维护部门）在履行具体角色时通力合作。 VDC 满足“直接迁移”的需求，也为原生云部署带来众多优势。

## <a name="references"></a>参考

本文档中讨论了以下功能。 请通过以下链接了解更多信息。

| | | |
|-|-|-|
|网络功能|负载均衡|连接|
|[Azure 虚拟网络][VNet]</br>[网络安全组][NSG]</br>[NSG 日志][NSGLog]</br>[用户定义的路由][UDR]</br>[网络虚拟设备][NVA]</br>[公共 IP 地址][PIP]</br>[Azure DDOS][DDOS]</br>[Azure 防火墙][AzFW]</br>[Azure DNS][DNS]|[Azure Front Door][AFD]</br>[Azure 负载均衡器 (L3)][ALB]</br>[应用程序网关 (L7)][AppGW]</br>[Web 应用程序防火墙][WAF]</br>[Azure 流量管理器][TM]</br></br></br></br></br> |[VNet 对等互连][VNetPeering]</br>[虚拟专用网络][VPN]</br>[虚拟 WAN][vWAN]</br>[ExpressRoute][ExR]</br>[ExpressRoute Direct][ExRD]</br></br></br></br></br>
|标识</br>|监视</br>|最佳实践</br>|
|[Azure Active Directory][AAD]</br>[多重身份验证][MFA]</br>[基于角色的访问控制][RBAC]</br>[默认 Azure AD 角色][Roles]</br></br></br> |[网络观察程序][NetWatch]</br>[Azure Monitor][Monitor]</br>[活动日志][ActLog]</br>[诊断日志][DiagLog]</br>[Microsoft Operations Management Suite][OMS]</br>[网络性能监视器][NPM]|[外围网络最佳实践][DMZ]</br>[订阅管理][SubMgmt]</br>[资源组管理][RGMgmt]</br>[Azure 订阅限制][Limits] </br></br></br>|
|其他 Azure 服务|
|[Azure Web 应用][WebApps]</br>[HDInsights (Hadoop)][HDI]</br>[事件中心][EventHubs]</br>[服务总线][ServiceBus]|

## <a name="next-steps"></a>后续步骤

 - 探索 [VNet 对等互连][VNetPeering]，这是 VDC 中心辐射型设计的基础技术
 - 实现 [Azure AD][AAD] 以开始探索 [RBAC][RBAC]
 - 根据所在组织的结构、要求和策略开发订阅与资源管理模型和 RBAC 模型。 最重要的活动是计划。 尽可能根据实际情况进行重组、合并和新产品线等方面的计划。m <!--Image References-->
[0]: ./images/networking-redundant-equipment.png "组件重叠示例" 
[1]: ./images/networking-vdc-high-level.png "中心辐射型 VDC 的概要示例"
[2]: ./images/networking-hub-spokes-cluster.png "中心辐射型群集"
[3]: ./images/networking-spoke-to-spoke.png "辐射到辐射"
[4]: ./images/networking-vdc-block-level-diagram.png "VDC 的块级关系图"
[5]: ./images/networking-users-groups-subsciptions.png "用户、组、订阅和项目"
[6]: ./images/networking-infrastructure-high-level.png "高级别基础结构关系图"
[7]: ./images/networking-highlevel-perimeter-networks.png "高级别基础结构关系图"
[8]: ./images/networking-vnet-peering-perimeter-neworks.png "VNet 对等互连和外围网络"
[9]: ./images/networking-high-level-diagram-monitoring.png "用于监视的高级别关系图"
[10]: ./images/networking-high-level-workloads.png "用于工作负荷的高级别关系图"

<!--Link References-->
[Limits]: /azure/azure-subscription-service-limits
[Roles]: /azure/role-based-access-control/built-in-roles
[VNet]: /azure/virtual-network/virtual-networks-overview
[NSG]: /azure/virtual-network/virtual-networks-nsg
[DNS]: /azure/dns/dns-overview
[PrivateDNS]: /azure/dns/private-dns-overview
[VNetPeering]: /azure/virtual-network/virtual-network-peering-overview 
[UDR]: /azure/virtual-network/virtual-networks-udr-overview 
[RBAC]: /azure/role-based-access-control/overview
[MFA]: /azure/multi-factor-authentication/multi-factor-authentication
[AAD]: /azure/active-directory/active-directory-whatis
[VPN]: /azure/vpn-gateway/vpn-gateway-about-vpngateways 
[ExR]: /azure/expressroute/expressroute-introduction
[ExRD]: https://docs.microsoft.com/en-us/azure/expressroute/expressroute-erdirect-about
[vWAN]: /azure/virtual-wan/virtual-wan-about
[NVA]: /azure/architecture/reference-architectures/dmz/nva-ha
[AzFW]: /azure/firewall/overview
[SubMgmt]: /azure/architecture/cloud-adoption/appendix/azure-scaffold 
[RGMgmt]: /azure/azure-resource-manager/resource-group-overview
[DMZ]: /azure/best-practices-network-security
[ALB]: /azure/load-balancer/load-balancer-overview
[DDOS]: /azure/virtual-network/ddos-protection-overview
[PIP]: /azure/virtual-network/resource-groups-networking#public-ip-address
[AFD]: https://docs.microsoft.com/en-us/azure/frontdoor/front-door-overview
[AppGW]: /azure/application-gateway/application-gateway-introduction
[WAF]: /azure/application-gateway/application-gateway-web-application-firewall-overview
[Monitor]: /azure/monitoring-and-diagnostics/
[ActLog]: /azure/monitoring-and-diagnostics/monitoring-overview-activity-logs 
[DiagLog]: /azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs
[NSGLog]: /azure/virtual-network/virtual-network-nsg-manage-log
[OMS]: /azure/operations-management-suite/operations-management-suite-overview
[NPM]: /azure/log-analytics/log-analytics-network-performance-monitor
[NetWatch]: /azure/network-watcher/network-watcher-monitoring-overview
[WebApps]: /azure/app-service/
[HDI]: /azure/hdinsight/hdinsight-hadoop-introduction
[EventHubs]: /azure/event-hubs/event-hubs-what-is-event-hubs 
[ServiceBus]: /azure/service-bus-messaging/service-bus-messaging-overview
[TM]: /azure/traffic-manager/traffic-manager-overview