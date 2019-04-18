# <a name="choosing-a-compute-option-for-microservices"></a>选择微服务的计算选项

术语“计算”指的是计算资源（应用程序在这些资源上运行）的承载模型。 在微服务体系结构方面，有两种方案特别流行：

- 可管理专用节点 (VM) 上运行的服务的服务业务流程协调程序。
- 使用函数即服务 (FaaS) 的无服务器体系结构。

尽管这不是仅有的两个选项，但两者是用于构建微服务的成熟方案。 应用程序可以包含这两种方案。

## <a name="service-orchestrators"></a>服务业务流程协调程序

业务流程协调程序处理与一组服务的部署和管理相关的任务。 这些任务包括在节点上放置服务、监视服务运行状况、重启不正常的服务、对服务实例之间的网络流量进行负载均衡、服务发现、缩放服务实例的数目，以及应用配置更新。 常用业务流程协调程序包括 Kubernetes、Service Fabric、DC/OS 和 Docker Swarm。

在 Azure 平台上，请考虑以下选项：

- [Azure Kubernetes 服务](/azure/aks/) (AKS) 是托管的 Kubernetes 服务。 AKS 预配 Kubernetes 并公开 Kubernetes API 终结点，但可以承载和管理 Kubernetes 控制平面，并可以执行自动升级、自动修补、自动缩放和其他管理任务。 可将 AKS 视为“Kubernetes API 即服务”。

- [Service Fabric](/azure/service-fabric/) 是用于打包、部署和管理微服务的分布式系统平台。 可将微服务作为容器、二进制可执行文件或 [Reliable Services](/azure/service-fabric/service-fabric-reliable-services-introduction) 部署到 Service Fabric。 借助 Reliable Services 编程模型，服务可以直接使用 Service Fabric 编程 API 来查询系统、报告运行状况、接收有关配置和代码更改的通知，以及发现其他服务。 它与 Service Fabric 之间的重要区别在于，它重点用于构建使用 [Reliable Collections](/azure/service-fabric/service-fabric-reliable-services-reliable-collections) 的有状态服务。

- 其他选项，例如 Docker 企业版和 Mesosphere DC/OS 上 Azure IaaS 环境中运行。 有关部署模板[Azure Marketplace](https://azuremarketplace.microsoft.com)。

## <a name="containers"></a>容器

有时，人们在谈论容器和微服务时将它们看作相同的事物。 尽管这种看法不对 &mdash; 不需要容器即可构建微服务 &mdash; 但是，容器确实有一些专门与微服务相关的优势，例如：

- **可移植性**。 容器映像是一个独立包，无需安装库或其他依赖项即可运行。 因此，它们的部署非常轻松。 容器可以快速启动和停止，因此，我们可以运转新的实例来处理更多负载，或者在发生节点故障后进行恢复。

- **轻量且高效**。 与运行虚拟机相比，容器比较轻量，因为它们共享 OS 资源。 因此，可将多个容器打包到单个节点。当应用程序由许多小型服务构成时，这种做法特别有利。

- **资源隔离**。 可以限制容器可用的内存量和 CPU，这有助于确保失控的进程不会耗尽主机资源。 有关详细信息，请参阅[隔舱模式](../../patterns/bulkhead.md)。

## <a name="serverless-functions-as-a-service"></a>无服务器（函数即服务）

使用[无服务器](https://azure.microsoft.com/solutions/serverless/)体系结构时，无需管理 VM 或虚拟网络基础结构。 可以部署代码，然后让托管服务将该代码放入 VM 并执行。 这种方法往往比较适合用于使用基于事件的触发器协调的小粒度函数。 例如，放入队列的消息可能会触发一个函数，该函数从队列中读取并处理该消息。

[Azure Functions](/azure/azure-functions/)是支持各种函数触发器，包括 HTTP 请求、 服务总线队列和事件中心事件的无服务器计算服务。 有关完整列表，请参阅[Azure Functions 触发器和绑定概念](/azure/azure-functions/functions-triggers-bindings)。 此外应考虑[Azure 事件网格](/azure/event-grid/)，这是在 Azure 中托管的事件路由服务。

<!-- markdownlint-disable MD026 -->

## <a name="orchestrator-or-serverless"></a>选择业务流程协调程序还是无服务器？

<!-- markdownlint-enable MD026 -->

在业务流程协调程序方案与无服务器方案之间做出选择时，请考虑下面一些因素。

**易管理性**无服务器应用程序易于管理，因为平台可自行管理所有计算资源。 尽管业务流程协调程序可将群集管理和配置工作的某些方面抽象化，但它不会完全隐藏底层 VM。 使用业务流程协调程序时，需要考虑负载均衡、CPU 和内存使用率以及网络等方面的问题。

**灵活性和控制**。 在服务与群集的配置和管理方面，业务流程协调程序提供很高的控制度。 弊端是复杂性会增大。 使用无服务器体系结构会牺牲一定的控制度，因为这些细节已抽象化。

**可移植性**。 此处列出的所有业务流程协调程序（Kubernetes、DC/OS、Docker Swarm 和 Service Fabric）都可以在本地或多个公有云中运行。

**应用程序集成**。 使用无服务器体系结构构建复杂应用程序可能有难度。 在 Azure 中，一种做法是使用 [Azure 逻辑应用](/azure/logic-apps/)来协调一组 Azure 函数。 有关此方法的示例，请参阅[创建与 Azure 逻辑应用集成的函数](/azure/azure-functions/functions-twitter-email)。

**成本**。 使用业务流程协调程序时，需要为群集中运行的 VM 付费。 使用无服务器应用程序时，只需为实际消耗的计算资源付费。 在这两种情况下，都需要考虑到任何附加服务（例如存储、数据库和消息传递服务）的成本。

**可伸缩性**。 Azure Functions 可以根据传入事件的数目按需自动缩放。 使用业务流程协调程序时，可以通过增加群集中运行的服务实例数进行横向扩展。 此外，可以通过将更多 VM 添加到群集进行扩展。

我们的参考实现主要使用 Kubernetes，但对“交付历史记录”服务使用了 Azure Functions。 Azure Functions 非常适合此特定服务，因为它是事件驱动的工作负荷。 该服务使用事件中心触发器来调用函数，因此只需少量的代码。 此外，“交付历史记录”服务并非主要工作流的一部分，因此，在 Kubernetes 群集外部运行该服务不会影响用户发起的操作的端到端延迟。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [服务间通信](./interservice-communication.md)
