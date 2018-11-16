---
title: Azure 上的 Web 应用程序监视
description: 监视 Azure 应用服务中托管的 Web 应用程序。
author: adamboeglin
ms.date: 09/12/2018
ms.openlocfilehash: ba008035c37d1d4e2d2f823463344e4941c0b4c4
ms.sourcegitcommit: 0a31fad9b68d54e2858314ca5fe6cba6c6b95ae4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2018
ms.locfileid: "51610747"
---
# <a name="web-application-monitoring-on-azure"></a>Azure 上的 Web 应用程序监视

Azure 平台即服务 (PaaS) 产品/服务为你管理计算资源，并且影响你监视部署的方式。 Azure 包含多个监视服务，其中的每个服务发挥特定的作用。 这些服务共同提供一个综合性的解决方案，可从应用程序及其使用的 Azure 资源收集、分析和处理遥测数据。

本方案探讨可用的监视服务，并介绍可对多个数据源使用的数据流模型。 监视时，可将多个工具和服务用于 Azure 部署。 在本方案中，我们确切地选择了随时可用的服务，因为它们易于使用。 本文稍后会介绍其他监视选项。

## <a name="relevant-use-cases"></a>相关用例

其他相关用例包括：

- 检测一个用于监视遥测的 Web 应用程序。
- 收集 Azure 上部署的某个应用程序的前端和后端遥测数据。
- 监视与 Azure 上的服务关联的指标和配额。

## <a name="architecture"></a>体系结构

![应用监视体系结构示意图][architecture]

本方案使用托管的 Azure 环境来托管应用程序和数据层。 数据流经方案的情形如下所示：

1. 用户与应用程序交互。
2. 浏览器和应用服务发出遥测数据。
3. Application Insights 收集并分析应用程序运行状况、性能和使用情况数据。
4. 开发人员和管理员可以查看运行状况、性能与使用情况信息。
5. Azure SQL 数据库发出遥测数据。
6. Azure Monitor 收集并分析基础结构指标和配额。
7. Log Analytics 收集并分析日志和指标。
8. 开发人员和管理员可以查看运行状况、性能与使用情况信息。

### <a name="components"></a>组件

- [Azure 应用服务](/azure/app-service/)是用于在托管虚拟机中生成和托管应用的 PaaS 服务。 系统会自动管理运行应用的底层计算基础结构。 应用服务提供资源使用配额和应用指标的监视、诊断信息的记录，以及基于指标的警报。 甚至可以使用 Application Insights 来创建[可用性测试][availability-tests]，以便在不同的区域测试应用程序。
- [Application Insights][application-insights] 是支持多个平台的面向开发人员的可扩展应用程序性能管理 (APM) 服务。 它可以监视应用程序、检测应用程序异常（例如性能不佳和故障），以及将遥测数据发送到 Azure 门户。 Application Insights 还可用于日志记录、分布式跟踪和自定义应用程序指标。
- [Azure Monitor][azure-monitor] 针对 Azure 中的大多数服务提供基本级别的基础结构[指标和日志][metrics]。 可通过多种方式来与指标交互，包括在 Azure 门户中绘制指标图表、通过 REST API 访问指标，或者使用 PowerShell 或 CLI 查询指标。 Azure Monitor 还直接在 [Log Analytics 和其他服务]中提供其数据，在这些服务中，可以查询 Azure Monitor 的数据，以及将这些数据与来自其他本地源或云中源的数据合并。
- [Log Analytics][log-analytics] 可以帮助将 Application Insights 收集的使用情况和性能数据与支持应用的 Azure 资源的配置和性能数据相关联。 本方案使用 [Azure Log Analytics 代理][Azure Log Analytics agent]将 SQL Server 审核日志推送到 Log Analytics 中。 可在 Azure 门户的“Log Analytics”边栏选项卡中编写查询和查看数据。

## <a name="considerations"></a>注意事项

建议的做法是在开发期间使用 [Application Insights SDK][Application Insights SDKs] 将 Application Insights 添加到代码，然后根据应用程序进行自定义。 这些开源 SDK 适用于大多数应用程序框架。 若要扩充和控制所收集的数据，可将用于测试和生产部署的 SDK 整合到开发过程中。 应用要满足的主要要求是，通过面向 Internet 的地址直接或间接连接到托管的 Application Insights 引入终结点。 然后，可以添加遥测数据或扩充现有的遥测数据收集。

运行时监视是另一种简便的入门方法。 必须通过配置文件控制收集的遥测数据。 例如，可以包含运行时方法，以便通过 [Application Insights 状态监视器][Application Insights Status Monitor]等工具将 SDK 部署到正确的文件夹，并添加适当的配置开始进行监视。

与 Application Insights 一样，Log Analytics 提供相应的工具用于[分析不同源中的数据][analyzing data across sources]、创建复杂查询，以及根据指定的条件[发送主动警报][sending proactive alerts]。 还可以在 [Azure 门户][the Azure portal]中查看遥测数据。 Log Analytics 增大了 [Azure Monitor][Azure Monitor] 等现有监视服务的价值，同时还能监视本地环境。

Application Insights 和 Log Analytics 都使用 [Azure Log Analytics 查询语言][Azure Log Analytics Query Language]。 你也可以使用[跨资源查询](https://azure.microsoft.com/blog/query-across-resources)在单个查询中分析 Application Insights 和 Log Analytics 收集的遥测数据。

Azure Monitor、Application Insights 和 Log Analytics 都会发送[警报](/azure/monitoring-and-diagnostics/monitoring-overview-alerts)。 例如，Azure Monitor 针对 CPU 利用率等平台级指标发出警报，而 Application Insights 则针对服务器响应时间等应用程序级指标发出警报。 Azure Monitor 针对 Azure 活动日志中的新事件发出警报，而 Log Analytics 则可以针对配置为使用指标或事件数据的服务发出相关警报。 [Azure Monitor 中的统一警报](/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)是 Azure 中使用不同分类的新统一警报体验。

### <a name="alternatives"></a>备选项

本文介绍可方便使用的监视选项和热门功能，但你也可以使用其他许多选项，包括用于创建自己的日志记录机制的选项。 建议的做法是在解决方案中构建层时添加监视服务。 下面是一些可能的扩展和替代方法：

- 使用 [Azure Monitor Data Source For Grafana][Azure Monitor Data Source For Grafana] 在 Grafana 中整合 Azure Monitor 和 Application Insights 指标。
- [Data Dog][data-dog] 为 Azure Monitor 提供连接器
- 使用 [Azure 自动化][Azure Automation]将监视功能自动化。
- 使用 [ITSM 解决方案][ITSM solutions]添加通信功能。
- 使用[管理解决方案][management solution]扩展 Log Analytics。

### <a name="scalability-and-availability"></a>可伸缩性和可用性

此方案侧重于主要用于监视的 PaaS 解决方案，因为这些解决方案可以方便地处理可用性和可伸缩性，并有服务级别协议 (SLA) 的保障。 例如，应用服务为其可用性提供有保障的 [SLA][SLA]。

Application Insights 会[限制][app-insights-limits]每秒可以处理的请求数。 如果超过请求数限制，消息可能会受到限制。 为防止发生限制，可以实施[筛选][message-filtering]或[采样][message-sampling]来降低数据速率

但是，所运行的应用程序的高可用性考虑因素由开发人员负责。 有关规模等方面的信息，请参阅基本 Web 应用程序参考体系结构中的[可伸缩性注意事项](#scalability-considerations)部分。 部署应用后，可以设置测试，以使用 Application Insights [监视其可用性][monitor its availability]。

### <a name="security"></a>安全

敏感信息与合规要求会影响数据的收集、保留和存储。 详细了解 [Application Insights][application-insights] 和 [Log Analytics][log-analytics] 如何处理遥测数据。

此外，可能需要注意以下安全事项：

- 制定一个计划，以便在开发人员有权收集其自己的数据或扩充现有遥测数据时处理个人信息。
- 考虑数据保留期。 例如，Application Insights 会将遥测数据保留 90 天。 使用 Microsoft Power BI、连续导出或 REST API 来存档需要长期访问的数据。 可能会产生存储费。
- 限制对 Azure 资源的访问，以控制数据访问以及谁可以从特定的应用程序查看遥测数据。 若要帮助锁定对监视遥测数据的访问，请参阅 [Application Insights 中的资源、角色和访问控制][Resources, roles, and access control in Application Insights]。
- 考虑是否要在应用程序代码中控制读/写访问，以防止用户添加会限制从应用程序引入数据的版本或标签标记。 使用 Application Insights 时，将各个数据项发送到资源后，便无法对这些项进行控制，因此，如果用户有权访问任何数据，则他们就有权访问单个资源中的所有数据。
- 根据需要添加[监管](/azure/security/governance-in-azure)机制，以便对 Azure 资源强制实施策略或成本控制。 例如，使用 Log Analytics 进行策略和基于角色的访问控制等方面的安全相关监视，或使用 [Azure Policy](/azure/azure-policy/azure-policy-introduction) 来创建、分配和管理策略定义。
- 若要监视潜在安全问题并获取 Azure 资源安全状态的中心视图，请考虑使用 [Azure 安全中心](/azure/security-center/security-center-intro)。

## <a name="pricing"></a>定价

监视费用可能会迅速增加，因此，请考虑提前定价，了解监视的内容，并查看每个服务的相关费用。 Azure Monitor 免费提供[基本指标][basic metrics]，[Application Insights][application-insights-pricing] 和 [Log Analytics][log-analytics] 的监视费用基于引入的数据量和运行的测试数。

可以使用[定价计算器][pricing]估算成本，以帮助计算上手费用。 若要了解特定用例的定价变化，请按预期部署更改不同的选项。

在调试期间以及发布应用后，Application Insights 的遥测数据将发送到 Azure 门户。 若要进行测试并避免产生费用，可以检测有限的遥测数据量。 若要添加更多指标，可以引发遥测限制。 若要提高控制粒度，请参阅 [Application Insights 中的采样][Sampling in Application Insights]。

部署后，可以观察性能指标的[实时指标流][Live Metrics Stream]。 不会存储此数据 &mdash; 实时指标仅供查看 &mdash; 但可以收集遥测数据，并在以后进行分析。 实时流数据不会产生费用。

Log Analytics 根据引入到服务中的数据的 GB 单位数量计费。 每个月引入到 Azure Log Analytics 服务中的前 5 GB 数据是免费的，最初的 31 天，这些数据将在 Log Analytics 工作区中免费保留。 

## <a name="next-steps"></a>后续步骤

请查看以下可以帮助你开始使用自己的监视解决方案的资源：

[基本 Web 应用程序参考体系结构][Basic web application reference architecture]

[开始监视 ASP.NET Web 应用程序][Start monitoring your ASP.NET Web Application]

[收集有关 Azure 虚拟机的数据][Collect data about Azure Virtual Machines]

## <a name="related-resources"></a>相关资源

[监视 Azure 应用程序和资源][Monitoring Azure applications and resources]

[使用 Azure Application Insights 查找并诊断运行时异常][Find and diagnose run-time exceptions with Azure Application Insights]

<!-- links -->
[architecture]: ./media/architecture-app-monitoring.png
[availability-tests]: /azure/application-insights/app-insights-monitor-web-app-availability
[application-insights]: /azure/application-insights/app-insights-overview
[azure-monitor]: /azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor
[metrics]: /azure/monitoring-and-diagnostics/monitoring-supported-metrics
[Log Analytics 和其他服务]: /azure/log-analytics/log-analytics-azure-storage
[log-analytics]: /azure/log-analytics/log-analytics-overview
[Azure Log Analytics agent]: https://blogs.msdn.microsoft.com/sqlsecurity/2017/12/28/azure-log-analytics-oms-agent-now-collects-sql-server-audit-logs/
[application-insights-pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[Application Insights SDKs]: /azure/application-insights/app-insights-asp-net
[Application Insights Status Monitor]: https://azure.microsoft.com/updates/application-insights-status-monitor-and-sdk-updated/
[analyzing data across sources]: /azure/log-analytics/log-analytics-dashboards
[sending proactive alerts]: /azure/log-analytics/log-analytics-alerts
[the Azure portal]: /azure/log-analytics/log-analytics-tutorial-dashboards
[Azure Log Analytics Query Language]: https://docs.loganalytics.io/docs/Learn
[cross-resource queries]: https://azure.microsoft.com/blog/query-across-resources/
[alerts]: /azure/monitoring-and-diagnostics/monitoring-overview-alerts
[Alerts (Preview)]: /azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts
[Azure Monitor Data Source For Grafana]: https://grafana.com/plugins/grafana-azure-monitor-datasource
[Azure Automation]: /azure/automation/automation-intro
[ITSM solutions]: https://azure.microsoft.com/blog/itsm-connector-for-azure-is-now-generally-available/
[management solution]: /azure/monitoring/monitoring-solutions
[SLA]: https://azure.microsoft.com/support/legal/sla/app-service/v1_4/
[monitor its availability]: /azure/application-insights/app-insights-monitor-web-app-availability
[Resources, roles, and access control in Application Insights]: /azure/application-insights/app-insights-resources-roles-access-control
[basic metrics]: /azure/monitoring-and-diagnostics/monitoring-supported-metrics
[pricing]: https://azure.microsoft.com/pricing/calculator/#log-analyticsc126d8c1-ec9c-4e5b-9b51-4db95d06a9b1
[Sampling in Application Insights]: /azure/application-insights/app-insights-sampling
[Live Metrics Stream]: /azure/application-insights/app-insights-live-stream
[Basic web application reference architecture]: /azure/architecture/reference-architectures/app-service-web-app/basic-web-app#scalability-considerations
[Start monitoring your ASP.NET Web Application]: /azure/application-insights/quick-monitor-portal
[Collect data about Azure Virtual Machines]: /azure/log-analytics/log-analytics-quick-collect-azurevm
[Monitoring Azure applications and resources]: /azure/monitoring-and-diagnostics/monitoring-overview
[Find and diagnose run-time exceptions with Azure Application Insights]: /azure/application-insights/app-insights-tutorial-runtime-exceptions
[data-dog]: https://www.datadoghq.com/blog/azure-monitoring-enhancements/
[app-insights-limits]: /azure/azure-subscription-service-limits#application-insights-limits
[message-filtering]: /azure/application-insights/app-insights-api-filtering-sampling
[message-sampling]: /azure/application-insights/app-insights-sampling
