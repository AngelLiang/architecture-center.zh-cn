---
title: 使用仪表板直观显示 Azure Databricks 指标
description: 如何部署 Grafana 仪表板来监视 Azure Databricks 中的性能
author: petertaylor9999
ms.date: 03/26/2019
ms.openlocfilehash: dbc04b00a781dd20c3224b5a031a8d98ddadce94
ms.sourcegitcommit: 9854bd27fb5cf92041bbfb743d43045cd3552a69
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58503381"
---
# <a name="use-dashboards-to-visualize-azure-databricks-metrics"></a>使用仪表板直观显示 Azure Databricks 指标

[Azure Databricks](/azure/azure-databricks/)是快速、 功能强大，和协作[Apache Spark](https://spark.apache.org/)– 基于分析服务，轻松地快速开发和部署大数据分析和人工智能 (AI) 解决方案。 监视是运行在生产环境中的 Azure Databricks 工作负荷的关键组件。 第一步是收集到用于分析的工作区的指标。 在 Azure 中，用于管理日志数据的最佳解决方案是[Azure Monitor](/azure/azure-monitor/)。 Azure Databricks 本身不支持将日志数据发送到 Azure monitor，但[此功能的库](https://github.com/mspnp/spark-monitoring)现已推出[Github](https://github.com)。

此库启用日志记录的 Azure Databricks 服务度量值，以及 Apache Spark 流式处理查询事件指标的结构。 一旦您已成功部署到 Azure Databricks 群集中的此库，可以进一步部署一套[Azure Monitor](/azure/azure-monitor/)或[Grafana](https://granfana.com)可以对生产环境的一部分部署的仪表板环境。 本文档介绍了常见的性能问题以及如何确定使用这些仪表板对其类型。

![仪表板的屏幕截图](./_images/dashboard-screenshot.png)

## <a name="prequisites"></a>先决条件

克隆[Github 存储库](https://github.com/mspnp/spark-monitoring)并[遵循的部署说明](./configure-cluster.md)构建和配置 Azure Databricks 库将日志发送到 Azure Log Analytics 工作区的 Azure Monitor 日志记录。

## <a name="deploy-the-azure-log-analytics-workspace"></a>部署 Azure Log Analytics 工作区

若要部署 Azure Log Analytics 工作区，请按照下列步骤：

1. 导航到`/perftools/deployment/loganalytics`目录。
1. 部署**logAnalyticsDeploy.json** Azure 资源管理器模板。 有关部署资源管理器模板的详细信息，请参阅[使用资源管理器模板和 Azure CLI 部署资源][rm-cli]。 该模板具有以下参数：

    * **location**：Log Analytics 工作区和仪表板的部署所在的区域。
    * **serviceTier**:按照工作区定价层。 请参阅[这里][ sku]有关有效值的列表。
    * **dataRetention** （可选）：在 Log Analytics 工作区中保留的数天的日志数据。 默认值为 30 天。 如果定价层为`Free`，数据保留期必须是 7 天。
    * **WorkspaceName** （可选）：工作区的名称。 如果未指定，该模板生成的名称。

    ```bash
    az group deployment create --resource-group <resource-group-name> --template-file logAnalyticsDeploy.json --parameters location='East US' serviceTier='Standalone'
    ```

此模板创建工作区，并还会创建一组预定义仪表板的使用的查询。

## <a name="deploy-grafana-in-a-virtual-machine"></a>在虚拟机中部署 Grafana

Grafana 是一个开放源代码项目，可部署为使用适用于 Azure Monitor Grafana 插件在 Azure Log Analytics 工作区中存储的时间系列度量值可视化。 Grafana 虚拟机 (VM) 上执行，并需要存储帐户、 虚拟网络和其他资源。 若要部署虚拟机使用 bitnami 认证 Grafana 映像和关联的资源，请执行以下步骤：

1. 使用 Azure CLI Grafana 接受 Azure Marketplace 映像条款。

    ```bash
    az vm image accept-terms --publisher bitnami --offer grafana --plan default
    ```

1. 导航到`/spark-monitoring/perftools/deployment/grafana`目录中的 GitHub 存储库本地副本。
1. 部署**grafanaDeploy.json**资源管理器模板，如下所示：

    ```bash
    export DATA_SOURCE="https://raw.githubusercontent.com/mspnp/spark-monitoring/master/perftools/deployment/grafana/AzureDataSource.sh"
    az group deployment create \
        --resource-group <resource-group-name> \
        --template-file grafanaDeploy.json \
        --parameters adminPass='<vm password>' dataSource=$DATA_SOURCE
    ```

部署完成后，虚拟机上安装 Grafana bitnami 映像。

## <a name="update-the-grafana-password"></a>Grafana 密码更新

作为安装过程的一部分，Grafana 安装脚本将输出的临时密码**管理员**用户。 需要此临时密码进行登录。 若要获取临时密码，请执行以下步骤：  

1. 登录到 Azure 门户。  
1. 选择已部署资源的资源组。
1. 选择已安装 Grafana 的 VM。 如果在部署模板中使用的默认参数名称，VM 名称开头，但**sparkmonitoring vm grafana**。
1. 在中**支持 + 故障排除**部分中，单击**启动诊断**以打开启动诊断页。
1. 单击**串行日志**启动诊断页上。
1. 搜索以下字符串："设置为 Bitnami 应用程序密码"。
1. 将密码复制到一个安全位置。

接下来，通过执行以下步骤更改 Grafana 管理员密码：

1. 在 Azure 门户中，选择 VM，并单击**概述**。
1. 复制公共 IP 地址，
1. 打开 web 浏览器并导航到以下 URL: `http://<IP addresss>:3000`。
1. 在 Grafana 登录屏幕，输入**管理员**的用户名，并使用前面步骤中的 Grafana 密码。
1. 登录后，选择**配置**（齿轮图标）。
1. 选择**服务器管理员**。
1. 上**用户**选项卡上，选择**管理员**登录名。
1. 更新的密码。

## <a name="create-an-azure-monitor-data-source"></a>创建 Azure Monitor 数据源

1. 创建服务主体允许 Grafana 来管理对 Log Analytics 工作区的访问。 有关详细信息，请参阅[使用 Azure CLI 创建 Azure 服务主体](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)

    ```bash
    az ad sp create-for-rbac --name http://<service principal name> --role "Log Analytics Reader"
    ```

1. 请记下 appId、 密码和租户，此命令的输出中的值：

    ```json
    {
        "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "displayName": "azure-cli-2019-03-27-00-33-39",
        "name": "http://<service principal name>",
        "password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. 登录到 Grafana 所述前面。 选择**配置**（齿轮图标），然后**数据源**。
1. 在中**数据源**选项卡上，单击**添加数据源**。
1. 选择**Azure Monitor**作为数据源类型。
1. 在中**设置**部分中，输入中的数据源的名称**名称**文本框中。
1. 在中**Azure 监视器 API 详细信息**部分中，输入以下信息：

    * 订阅 ID:Azure 订阅 ID。
    * 租户 Id:来自前面的租户 ID。
    * 客户端 Id:从前面的"appId"的值。
    * 客户端密码：从前面的"password"的值。

1. 在中**Azure Log Analytics API 详细信息**部分中，选择**相同的详细信息，作为 Azure Monitor API**复选框。
1. 单击**保存与测试**。 如果正确配置 Log Analytics 数据源，将显示一条成功消息。

## <a name="create-the-dashboard"></a>创建仪表板

在 Grafana 中创建仪表板，通过执行以下步骤：

1. 导航到`/perftools/dashboards/grafana`目录中的 GitHub 存储库本地副本。
1. 运行以下脚本：

    ```bash
    export WORKSPACE=<your Azure Log Analytics workspace ID>
    export LOGTYPE=SparkListenerEvent_CL

    sh DashGen.sh
    ```

    该脚本的输出是名为的文件**SparkMonitoringDash.json**。

1. 返回到 Grafana 仪表板并选择**创建**（加号图标）。
1. 选择“导入”。
1. 单击 **.json 文件上传**。
1. 选择**SparkMonitoringDash.json**步骤 2 中创建的文件。
1. 在中**选项**部分中，在**ALA**，选择前面创建的 Azure Monitor 数据源。
1. 单击“导入”。

## <a name="visualizations-in-the-dashboards"></a>在仪表板中的可视化效果

Azure Log Analytics 和 Grafana 仪表板包括一系列时间序列的可视化效果。 每个关系图是 Apache Spark 与相关的指标数据的时间序列绘图[作业](https://spark.apache.org/docs/latest/job-scheduling.html)，作业和任务构成每个阶段的阶段。

可视化效果是，如下所示：

### <a name="job-latency"></a>作业滞后时间

此可视化效果中显示的作业，这是作业的总体性能的粗略视图执行延迟。 显示作业执行持续时间从开始到完成。 请注意在作业开始时间不是作为作业提交时间相同。 滞后时间表示为百分位数 （10%，30%、 50%，90%）按群集 ID 和应用程序进行索引的作业执行的 id。

### <a name="stage-latency"></a>阶段延迟

可视化效果中显示的每个群集，每个应用程序，以及每个单独阶段每个阶段的延迟。 此可视化效果可用于标识某一特定阶段的运行速度变慢。

### <a name="task-latency"></a>任务延迟

此可视化效果中显示的任务执行延迟。 滞后时间表示为每个群集，阶段名称和应用程序的任务执行的百分位数。

### <a name="sum-task-execution-per-host"></a>每个主机的总和任务执行

此可视化效果显示每个主机群集上运行的任务执行延迟的总和。 查看每个主机的任务执行延迟标识具有更高版本的整体任务延迟时间比其他主机的主机。 这可能意味着，任务已效率低下或不均衡地分发到主机。

### <a name="task-metrics"></a>任务度量值

此可视化效果显示一组用于执行给定的任务的执行度量值。 这些指标包括大小和数据无序播放的持续时间、 序列化和反序列化操作的持续时间和其他人。 有关全套指标，查看面板中的 Log Analytics 查询。 此可视化效果可用于了解构成了一个任务，标识的资源消耗，每个操作的操作。 在图中的峰值表示应进行调查的成本高昂的操作。

### <a name="cluster-throughput"></a>群集吞吐量

此可视化效果是由群集和应用程序来表示每个群集和应用程序完成的工作量编制索引的工作项的高级别视图。 它显示了作业、 任务和阶段每个群集、 应用程序和以一分钟为增量阶段完成的数。 

### <a name="streaming-throughputlatency"></a>流式处理吞吐量/延迟

此 visualzation 与结构化流查询与关联的指标。 此关系图显示每秒的输入行数和每秒处理的行数。 流式处理度量值也表示每个应用程序。 处理结构化流式处理查询和可视化效果表示生成 OnQueryProgress 事件时发送这些指标流式处理延迟的时间量，以毫秒为单位，执行所用查询批次。

### <a name="resource-consumption-per-executor"></a>每个执行器的资源消耗

接下来是资源的一系列仪表板显示特定类型和如何使用每个执行器在每个群集上的可视化效果。 这些可视化效果有助于标识每个执行器的资源消耗中离群值。 例如，如果特定执行器的工作分配有偏差，将相对于其他执行器在群集上运行提升资源消耗。 这可以通过执行器的资源消耗的峰值来确定。

### <a name="executor-compute-time-metrics"></a>执行器计算时间指标

接下来是一组显示的执行器序列化时，反序列化时间、 CPU 时间和 Java 虚拟机时间之间的整体执行器计算转换的仪表板的可视化效果。 此示例演示对整体执行器处理以可视方式以及每种这些四个指标的有影响。

### <a name="shuffle-metrics"></a>随机选择指标

最终的可视化效果显示数据无序播放与结构化流查询跨所有执行程序相关的指标集。 其中包括无序播放字节读取、 无序播放写入的字节数、 无序播放内存和磁盘使用情况在查询中使用文件系统位置。

<!-- links -->

[rm-cli]: /azure/azure-resource-manager/resource-group-template-deploy-cli
[sku]: /azure/templates/Microsoft.OperationalInsights/2015-11-01-preview/workspaces#sku-object