---
title: 使用仪表板将 Azure Databricks 指标可视化
description: 如何部署 Grafana 仪表板来监视 Azure Databricks 中的性能
author: petertaylor9999
ms.date: 03/26/2019
ms.openlocfilehash: a84203a9188848e6363a80ac455332e8f6a73cda
ms.sourcegitcommit: 579c39ff4b776704ead17a006bf24cd4cdc65edd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59640305"
---
# <a name="use-dashboards-to-visualize-azure-databricks-metrics"></a><span data-ttu-id="29858-103">使用仪表板将 Azure Databricks 指标可视化</span><span class="sxs-lookup"><span data-stu-id="29858-103">Use dashboards to visualize Azure Databricks metrics</span></span>

<span data-ttu-id="29858-104">本文介绍如何设置 Grafana 仪表板，监视 Azure Databricks 作业有关的性能问题。</span><span class="sxs-lookup"><span data-stu-id="29858-104">This article shows how to set up a Grafana dashboard to monitor Azure Databricks jobs for performance issues.</span></span>

<span data-ttu-id="29858-105">[Azure Databricks](/azure/azure-databricks/)是快速、 功能强大，和协作[Apache Spark](https://spark.apache.org/)– 基于分析服务，轻松地快速开发和部署大数据分析和人工智能 (AI) 解决方案。</span><span class="sxs-lookup"><span data-stu-id="29858-105">[Azure Databricks](/azure/azure-databricks/) is a fast, powerful, and collaborative [Apache Spark](https://spark.apache.org/)–based analytics service that makes it easy to rapidly develop and deploy big data analytics and artificial intelligence (AI) solutions.</span></span> <span data-ttu-id="29858-106">监视是运行在生产环境中的 Azure Databricks 工作负荷的关键组件。</span><span class="sxs-lookup"><span data-stu-id="29858-106">Monitoring is a critical component of operating Azure Databricks workloads in production.</span></span> <span data-ttu-id="29858-107">第一步是收集到用于分析的工作区的指标。</span><span class="sxs-lookup"><span data-stu-id="29858-107">The first step is to gather metrics into a workspace for analysis.</span></span> <span data-ttu-id="29858-108">在 Azure 中，用于管理日志数据的最佳解决方案是[Azure Monitor](/azure/azure-monitor/)。</span><span class="sxs-lookup"><span data-stu-id="29858-108">In Azure, the best solution for managing log data is [Azure Monitor](/azure/azure-monitor/).</span></span> <span data-ttu-id="29858-109">Azure Databricks 本身不支持将日志数据发送到 Azure monitor，但[此功能的库](https://github.com/mspnp/spark-monitoring)现已推出[Github](https://github.com)。</span><span class="sxs-lookup"><span data-stu-id="29858-109">Azure Databricks does not natively support sending log data to Azure monitor, but a [library for this functionality](https://github.com/mspnp/spark-monitoring) is available in [Github](https://github.com).</span></span>

<span data-ttu-id="29858-110">此库启用日志记录的 Azure Databricks 服务度量值，以及 Apache Spark 流式处理查询事件指标的结构。</span><span class="sxs-lookup"><span data-stu-id="29858-110">This library enables logging of Azure Databricks service metrics as well as Apache Spark structure streaming query event metrics.</span></span> <span data-ttu-id="29858-111">一旦您已成功部署到 Azure Databricks 群集中的此库，可以进一步部署一套[Grafana](https://granfana.com)仪表板，你可以将其部署为生产环境的一部分。</span><span class="sxs-lookup"><span data-stu-id="29858-111">Once you've successfully deployed this library to an Azure Databricks cluster, you can further deploy a set of [Grafana](https://granfana.com) dashboards that you can deploy as part of your production environment.</span></span>

![仪表板的屏幕截图](./_images/dashboard-screenshot.png)

## <a name="prerequisites"></a><span data-ttu-id="29858-113">必备组件</span><span class="sxs-lookup"><span data-stu-id="29858-113">Prerequisites</span></span>

<span data-ttu-id="29858-114">克隆[Github 存储库](https://github.com/mspnp/spark-monitoring)并[遵循的部署说明](./configure-cluster.md)构建和配置 Azure Databricks 库将日志发送到 Azure Log Analytics 工作区的 Azure Monitor 日志记录。</span><span class="sxs-lookup"><span data-stu-id="29858-114">Clone the [Github repository](https://github.com/mspnp/spark-monitoring) and [follow the deployment instructions](./configure-cluster.md) to build and configure the Azure Monitor logging for Azure Databricks library to send logs to your Azure Log Analytics workspace.</span></span>

## <a name="deploy-the-azure-log-analytics-workspace"></a><span data-ttu-id="29858-115">部署 Azure Log Analytics 工作区</span><span class="sxs-lookup"><span data-stu-id="29858-115">Deploy the Azure Log Analytics workspace</span></span>

<span data-ttu-id="29858-116">若要部署 Azure Log Analytics 工作区，请按照下列步骤：</span><span class="sxs-lookup"><span data-stu-id="29858-116">To deploy the Azure Log Analytics workspace, follow these steps:</span></span>

1. <span data-ttu-id="29858-117">导航到`/perftools/deployment/loganalytics`目录。</span><span class="sxs-lookup"><span data-stu-id="29858-117">Navigate to the `/perftools/deployment/loganalytics` directory.</span></span>
1. <span data-ttu-id="29858-118">部署**logAnalyticsDeploy.json** Azure 资源管理器模板。</span><span class="sxs-lookup"><span data-stu-id="29858-118">Deploy the **logAnalyticsDeploy.json** Azure Resource Manager template.</span></span> <span data-ttu-id="29858-119">有关部署资源管理器模板的详细信息，请参阅[使用资源管理器模板和 Azure CLI 部署资源][rm-cli]。</span><span class="sxs-lookup"><span data-stu-id="29858-119">For more information about deploying Resource Manager templates, see [Deploy resources with Resource Manager templates and Azure CLI][rm-cli].</span></span> <span data-ttu-id="29858-120">该模板具有以下参数：</span><span class="sxs-lookup"><span data-stu-id="29858-120">The template has the following parameters:</span></span>

    * <span data-ttu-id="29858-121">**location**：Log Analytics 工作区和仪表板的部署所在的区域。</span><span class="sxs-lookup"><span data-stu-id="29858-121">**location**: The region where the Log Analytics workspace and dashboards are deployed.</span></span>
    * <span data-ttu-id="29858-122">**serviceTier**:按照工作区定价层。</span><span class="sxs-lookup"><span data-stu-id="29858-122">**serviceTier**: Rhe workspace pricing tier.</span></span> <span data-ttu-id="29858-123">请参阅[这里][ sku]有关有效值的列表。</span><span class="sxs-lookup"><span data-stu-id="29858-123">See [here][sku] for a list of valid values.</span></span>
    * <span data-ttu-id="29858-124">**dataRetention** （可选）：在 Log Analytics 工作区中保留的数天的日志数据。</span><span class="sxs-lookup"><span data-stu-id="29858-124">**dataRetention** (optional): The number of days log data is retained in the Log Analytics workspace.</span></span> <span data-ttu-id="29858-125">默认值为 30 天。</span><span class="sxs-lookup"><span data-stu-id="29858-125">The default value is 30 days.</span></span> <span data-ttu-id="29858-126">如果定价层为`Free`，数据保留期必须是 7 天。</span><span class="sxs-lookup"><span data-stu-id="29858-126">If the pricing tier is `Free`, the data retention must be 7 days.</span></span>
    * <span data-ttu-id="29858-127">**WorkspaceName** （可选）：工作区的名称。</span><span class="sxs-lookup"><span data-stu-id="29858-127">**workspaceName** (optional): A name for the workspace.</span></span> <span data-ttu-id="29858-128">如果未指定，该模板生成的名称。</span><span class="sxs-lookup"><span data-stu-id="29858-128">If not specified, the template generates a name.</span></span>

    ```bash
    az group deployment create --resource-group <resource-group-name> --template-file logAnalyticsDeploy.json --parameters location='East US' serviceTier='Standalone'
    ```

<span data-ttu-id="29858-129">此模板创建工作区，并还会创建一组预定义仪表板使用的查询。</span><span class="sxs-lookup"><span data-stu-id="29858-129">This template creates the workspace and also creates a set of predefined queries that are used by dashboard.</span></span>

## <a name="deploy-grafana-in-a-virtual-machine"></a><span data-ttu-id="29858-130">在虚拟机中部署 Grafana</span><span class="sxs-lookup"><span data-stu-id="29858-130">Deploy Grafana in a virtual machine</span></span>

<span data-ttu-id="29858-131">Grafana 是一个开放源代码项目，可部署为使用适用于 Azure Monitor Grafana 插件在 Azure Log Analytics 工作区中存储的时间系列度量值可视化。</span><span class="sxs-lookup"><span data-stu-id="29858-131">Grafana is an open source project you can deploy to visualize the time series metrics stored in your Azure Log Analytics workspace using the Grafana plugin for Azure Monitor.</span></span> <span data-ttu-id="29858-132">Grafana 虚拟机 (VM) 上执行，并需要存储帐户、 虚拟网络和其他资源。</span><span class="sxs-lookup"><span data-stu-id="29858-132">Grafana executes on a virtual machine (VM) and requires a storage account, virtual network, and other resources.</span></span> <span data-ttu-id="29858-133">若要部署虚拟机使用 bitnami 认证 Grafana 映像和关联的资源，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="29858-133">To deploy a virtual machine with the bitnami certified Grafana image and associated resources, follow these steps:</span></span>

1. <span data-ttu-id="29858-134">使用 Azure CLI Grafana 接受 Azure Marketplace 映像条款。</span><span class="sxs-lookup"><span data-stu-id="29858-134">Use the Azure CLI to accept the Azure Marketplace image terms for Grafana.</span></span>

    ```bash
    az vm image accept-terms --publisher bitnami --offer grafana --plan default
    ```

1. <span data-ttu-id="29858-135">导航到`/spark-monitoring/perftools/deployment/grafana`目录中的 GitHub 存储库本地副本。</span><span class="sxs-lookup"><span data-stu-id="29858-135">Navigate to the `/spark-monitoring/perftools/deployment/grafana` directory in your local copy of the GitHub repo.</span></span>
1. <span data-ttu-id="29858-136">部署**grafanaDeploy.json**资源管理器模板，如下所示：</span><span class="sxs-lookup"><span data-stu-id="29858-136">Deploy the **grafanaDeploy.json** Resource Manager template as follows:</span></span>

    ```bash
    export DATA_SOURCE="https://raw.githubusercontent.com/mspnp/spark-monitoring/master/perftools/deployment/grafana/AzureDataSource.sh"
    az group deployment create \
        --resource-group <resource-group-name> \
        --template-file grafanaDeploy.json \
        --parameters adminPass='<vm password>' dataSource=$DATA_SOURCE
    ```

<span data-ttu-id="29858-137">部署完成后，虚拟机上安装 Grafana bitnami 映像。</span><span class="sxs-lookup"><span data-stu-id="29858-137">Once the deployment is complete, the bitnami image of Grafana is installed on the virtual machine.</span></span>

## <a name="update-the-grafana-password"></a><span data-ttu-id="29858-138">Grafana 密码更新</span><span class="sxs-lookup"><span data-stu-id="29858-138">Update the Grafana password</span></span>

<span data-ttu-id="29858-139">作为安装过程的一部分，Grafana 安装脚本将输出的临时密码**管理员**用户。</span><span class="sxs-lookup"><span data-stu-id="29858-139">As part of the setup process, the Grafana installation script outputs a temporary password for the **admin** user.</span></span> <span data-ttu-id="29858-140">需要此临时密码进行登录。</span><span class="sxs-lookup"><span data-stu-id="29858-140">You need this temporary password to sign in.</span></span> <span data-ttu-id="29858-141">若要获取临时密码，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="29858-141">To obtain the temporary password, follow these steps:</span></span>  

1. <span data-ttu-id="29858-142">登录到 Azure 门户。</span><span class="sxs-lookup"><span data-stu-id="29858-142">Log in to the Azure portal.</span></span>  
1. <span data-ttu-id="29858-143">选择已部署资源的资源组。</span><span class="sxs-lookup"><span data-stu-id="29858-143">Select the resource group where the resources were deployed.</span></span>
1. <span data-ttu-id="29858-144">选择已安装 Grafana 的 VM。</span><span class="sxs-lookup"><span data-stu-id="29858-144">Select the VM where Grafana was installed.</span></span> <span data-ttu-id="29858-145">如果在部署模板中使用的默认参数名称，VM 名称开头，但**sparkmonitoring vm grafana**。</span><span class="sxs-lookup"><span data-stu-id="29858-145">If you used the default parameter name in the deployment template, the VM name is prefaced with **sparkmonitoring-vm-grafana**.</span></span>
1. <span data-ttu-id="29858-146">在中**支持 + 故障排除**部分中，单击**启动诊断**以打开启动诊断页。</span><span class="sxs-lookup"><span data-stu-id="29858-146">In the **Support + troubleshooting** section, click **Boot diagnostics** to open the boot diagnostics page.</span></span>
1. <span data-ttu-id="29858-147">单击**串行日志**启动诊断页上。</span><span class="sxs-lookup"><span data-stu-id="29858-147">Click **Serial log** on the boot diagnostics page.</span></span>
1. <span data-ttu-id="29858-148">搜索以下字符串："设置为 Bitnami 应用程序密码"。</span><span class="sxs-lookup"><span data-stu-id="29858-148">Search for the following string: "Setting Bitnami application password to".</span></span>
1. <span data-ttu-id="29858-149">将密码复制到一个安全位置。</span><span class="sxs-lookup"><span data-stu-id="29858-149">Copy the password to a safe location.</span></span>

<span data-ttu-id="29858-150">接下来，通过执行以下步骤更改 Grafana 管理员密码：</span><span class="sxs-lookup"><span data-stu-id="29858-150">Next, change the Grafana administrator password by following these steps:</span></span>

1. <span data-ttu-id="29858-151">在 Azure 门户中，选择 VM，并单击**概述**。</span><span class="sxs-lookup"><span data-stu-id="29858-151">In the Azure portal, select the VM and click **Overview**.</span></span>
1. <span data-ttu-id="29858-152">复制公共 IP 地址，</span><span class="sxs-lookup"><span data-stu-id="29858-152">Copy the public IP address.</span></span>
1. <span data-ttu-id="29858-153">打开 web 浏览器并导航到以下 URL: `http://<IP address>:3000`。</span><span class="sxs-lookup"><span data-stu-id="29858-153">Open a web browser and navigate to the following URL: `http://<IP address>:3000`.</span></span>
1. <span data-ttu-id="29858-154">在 Grafana 登录屏幕，输入**管理员**的用户名，并使用前面步骤中的 Grafana 密码。</span><span class="sxs-lookup"><span data-stu-id="29858-154">At the Grafana log in screen, enter **admin** for the user name, and use the Grafana password from the previous steps.</span></span>
1. <span data-ttu-id="29858-155">登录后，选择**配置**（齿轮图标）。</span><span class="sxs-lookup"><span data-stu-id="29858-155">Once logged in, select **Configuration** (the gear icon).</span></span>
1. <span data-ttu-id="29858-156">选择**服务器管理员**。</span><span class="sxs-lookup"><span data-stu-id="29858-156">Select **Server Admin**.</span></span>
1. <span data-ttu-id="29858-157">上**用户**选项卡上，选择**管理员**登录名。</span><span class="sxs-lookup"><span data-stu-id="29858-157">On the **Users** tab, select the **admin** login.</span></span>
1. <span data-ttu-id="29858-158">更新的密码。</span><span class="sxs-lookup"><span data-stu-id="29858-158">Update the password.</span></span>

## <a name="create-an-azure-monitor-data-source"></a><span data-ttu-id="29858-159">创建 Azure Monitor 数据源</span><span class="sxs-lookup"><span data-stu-id="29858-159">Create an Azure Monitor data source</span></span>

1. <span data-ttu-id="29858-160">创建服务主体允许 Grafana 来管理对 Log Analytics 工作区的访问。</span><span class="sxs-lookup"><span data-stu-id="29858-160">Create a service principal that allows Grafana to manage access to your Log Analytics workspace.</span></span> <span data-ttu-id="29858-161">有关详细信息，请参阅[使用 Azure CLI 创建 Azure 服务主体](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)</span><span class="sxs-lookup"><span data-stu-id="29858-161">For more information, see [Create an Azure service principal with Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)</span></span>

    ```bash
    az ad sp create-for-rbac --name http://<service principal name> --role "Log Analytics Reader"
    ```

1. <span data-ttu-id="29858-162">请记下 appId、 密码和租户，此命令的输出中的值：</span><span class="sxs-lookup"><span data-stu-id="29858-162">Note the values for appId, password, and tenant in the output from this command:</span></span>

    ```json
    {
        "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "displayName": "azure-cli-2019-03-27-00-33-39",
        "name": "http://<service principal name>",
        "password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. <span data-ttu-id="29858-163">登录到 Grafana 所述前面。</span><span class="sxs-lookup"><span data-stu-id="29858-163">Log into Grafana as described earlier.</span></span> <span data-ttu-id="29858-164">选择**配置**（齿轮图标），然后**数据源**。</span><span class="sxs-lookup"><span data-stu-id="29858-164">Select **Configuration** (the gear icon) and then **Data Sources**.</span></span>
1. <span data-ttu-id="29858-165">在中**数据源**选项卡上，单击**添加数据源**。</span><span class="sxs-lookup"><span data-stu-id="29858-165">In the **Data Sources** tab, click **Add data source**.</span></span>
1. <span data-ttu-id="29858-166">选择**Azure Monitor**作为数据源类型。</span><span class="sxs-lookup"><span data-stu-id="29858-166">Select **Azure Monitor** as the data source type.</span></span>
1. <span data-ttu-id="29858-167">在中**设置**部分中，输入中的数据源的名称**名称**文本框中。</span><span class="sxs-lookup"><span data-stu-id="29858-167">In the **Settings** section, enter a name for the data source in the **Name** textbox.</span></span>
1. <span data-ttu-id="29858-168">在中**Azure 监视器 API 详细信息**部分中，输入以下信息：</span><span class="sxs-lookup"><span data-stu-id="29858-168">In the **Azure Monitor API Details** section, enter the following information:</span></span>

    * <span data-ttu-id="29858-169">订阅 ID:Azure 订阅 ID。</span><span class="sxs-lookup"><span data-stu-id="29858-169">Subscription Id: Your Azure subscription ID.</span></span>
    * <span data-ttu-id="29858-170">租户 Id:来自前面的租户 ID。</span><span class="sxs-lookup"><span data-stu-id="29858-170">Tenant Id: The tenant ID from earlier.</span></span>
    * <span data-ttu-id="29858-171">客户端 Id:从前面的"appId"的值。</span><span class="sxs-lookup"><span data-stu-id="29858-171">Client Id: The value of "appId" from earlier.</span></span>
    * <span data-ttu-id="29858-172">客户端密码：从前面的"password"的值。</span><span class="sxs-lookup"><span data-stu-id="29858-172">Client Secret: The value of "password" from earlier.</span></span>

1. <span data-ttu-id="29858-173">在中**Azure Log Analytics API 详细信息**部分中，选择**相同的详细信息，作为 Azure Monitor API**复选框。</span><span class="sxs-lookup"><span data-stu-id="29858-173">In the **Azure Log Analytics API Details** section, check the **Same Details as Azure Monitor API** checkbox.</span></span>
1. <span data-ttu-id="29858-174">单击**保存与测试**。</span><span class="sxs-lookup"><span data-stu-id="29858-174">Click **Save & Test**.</span></span> <span data-ttu-id="29858-175">如果正确配置 Log Analytics 数据源，将显示一条成功消息。</span><span class="sxs-lookup"><span data-stu-id="29858-175">If the Log Analytics data source is correctly configured, a success message is displayed.</span></span>

## <a name="create-the-dashboard"></a><span data-ttu-id="29858-176">创建仪表板</span><span class="sxs-lookup"><span data-stu-id="29858-176">Create the dashboard</span></span>

<span data-ttu-id="29858-177">在 Grafana 中创建仪表板，通过执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="29858-177">Create the dashboards in Grafana by following these steps:</span></span>

1. <span data-ttu-id="29858-178">导航到`/perftools/dashboards/grafana`目录中的 GitHub 存储库本地副本。</span><span class="sxs-lookup"><span data-stu-id="29858-178">Navigate to the `/perftools/dashboards/grafana` directory in your local copy of the GitHub repo.</span></span>
1. <span data-ttu-id="29858-179">运行以下脚本：</span><span class="sxs-lookup"><span data-stu-id="29858-179">Run the following script:</span></span>

    ```bash
    export WORKSPACE=<your Azure Log Analytics workspace ID>
    export LOGTYPE=SparkListenerEvent_CL

    sh DashGen.sh
    ```

    <span data-ttu-id="29858-180">该脚本的输出是名为的文件**SparkMonitoringDash.json**。</span><span class="sxs-lookup"><span data-stu-id="29858-180">The output from the script is a file named **SparkMonitoringDash.json**.</span></span>

1. <span data-ttu-id="29858-181">返回到 Grafana 仪表板并选择**创建**（加号图标）。</span><span class="sxs-lookup"><span data-stu-id="29858-181">Return to the Grafana dashboard and select **Create** (the plus icon).</span></span>
1. <span data-ttu-id="29858-182">选择“导入”。</span><span class="sxs-lookup"><span data-stu-id="29858-182">Select **Import**.</span></span>
1. <span data-ttu-id="29858-183">单击 **.json 文件上传**。</span><span class="sxs-lookup"><span data-stu-id="29858-183">Click **Upload .json File**.</span></span>
1. <span data-ttu-id="29858-184">选择**SparkMonitoringDash.json**步骤 2 中创建的文件。</span><span class="sxs-lookup"><span data-stu-id="29858-184">Select the **SparkMonitoringDash.json** file created in step 2.</span></span>
1. <span data-ttu-id="29858-185">在中**选项**部分中，在**ALA**，选择前面创建的 Azure Monitor 数据源。</span><span class="sxs-lookup"><span data-stu-id="29858-185">In the **Options** section, under **ALA**, select the Azure Monitor data source created earlier.</span></span>
1. <span data-ttu-id="29858-186">单击“导入”。</span><span class="sxs-lookup"><span data-stu-id="29858-186">Click **Import**.</span></span>

## <a name="visualizations-in-the-dashboards"></a><span data-ttu-id="29858-187">在仪表板中的可视化效果</span><span class="sxs-lookup"><span data-stu-id="29858-187">Visualizations in the dashboards</span></span>

<span data-ttu-id="29858-188">Azure Log Analytics 和 Grafana 仪表板包括一系列时间序列的可视化效果。</span><span class="sxs-lookup"><span data-stu-id="29858-188">Both the Azure Log Analytics and Grafana dashboards include a set of time-series visualizations.</span></span> <span data-ttu-id="29858-189">每个关系图是 Apache Spark 与相关的指标数据的时间序列绘图[作业](https://spark.apache.org/docs/latest/job-scheduling.html)，作业和任务构成每个阶段的阶段。</span><span class="sxs-lookup"><span data-stu-id="29858-189">Each graph is time-series plot of metric data related to an Apache Spark [job](https://spark.apache.org/docs/latest/job-scheduling.html), stages of the job, and tasks that make up each stage.</span></span>

<span data-ttu-id="29858-190">可视化效果是，如下所示：</span><span class="sxs-lookup"><span data-stu-id="29858-190">The visualizations are as follows:</span></span>

### <a name="job-latency"></a><span data-ttu-id="29858-191">作业滞后时间</span><span class="sxs-lookup"><span data-stu-id="29858-191">Job latency</span></span>

<span data-ttu-id="29858-192">此可视化效果中显示的作业，这是上一个作业的整体性能的粗略视图执行延迟。</span><span class="sxs-lookup"><span data-stu-id="29858-192">This visualization shows execution latency for a job, which is a coarse view on the overall performance of a job.</span></span> <span data-ttu-id="29858-193">显示作业执行持续时间从开始到完成。</span><span class="sxs-lookup"><span data-stu-id="29858-193">Displays the job execution duration from start to completion.</span></span> <span data-ttu-id="29858-194">请注意在作业开始时间不是作为作业提交时间相同。</span><span class="sxs-lookup"><span data-stu-id="29858-194">Note that the job start time is not the same as the job submission time.</span></span> <span data-ttu-id="29858-195">滞后时间表示为百分位数 （10%，30%、 50%，90%）按群集 ID 和应用程序进行索引的作业执行的 id。</span><span class="sxs-lookup"><span data-stu-id="29858-195">Latency is represented as percentiles (10%, 30%, 50%, 90%) of job execution indexed by cluster ID and application ID.</span></span>

### <a name="stage-latency"></a><span data-ttu-id="29858-196">阶段延迟</span><span class="sxs-lookup"><span data-stu-id="29858-196">Stage latency</span></span>

<span data-ttu-id="29858-197">可视化效果中显示的每个群集，每个应用程序，以及每个单独阶段每个阶段的延迟。</span><span class="sxs-lookup"><span data-stu-id="29858-197">The visualization shows the latency of each stage per cluster, per application, and per individual stage.</span></span> <span data-ttu-id="29858-198">此可视化效果可用于标识某一特定阶段的运行速度变慢。</span><span class="sxs-lookup"><span data-stu-id="29858-198">This visualization is useful for identifying a particular stage that is running slowly.</span></span>

### <a name="task-latency"></a><span data-ttu-id="29858-199">任务延迟</span><span class="sxs-lookup"><span data-stu-id="29858-199">Task latency</span></span>

<span data-ttu-id="29858-200">此可视化效果中显示的任务执行延迟。</span><span class="sxs-lookup"><span data-stu-id="29858-200">This visualization shows task execution latency.</span></span> <span data-ttu-id="29858-201">滞后时间表示为每个群集，阶段名称和应用程序的任务执行的百分位数。</span><span class="sxs-lookup"><span data-stu-id="29858-201">Latency is represented as a percentile of task execution per cluster, stage name, and application.</span></span>

### <a name="sum-task-execution-per-host"></a><span data-ttu-id="29858-202">每个主机的总和任务执行</span><span class="sxs-lookup"><span data-stu-id="29858-202">Sum Task Execution per host</span></span>

<span data-ttu-id="29858-203">此可视化效果显示每个主机群集上运行的任务执行延迟的总和。</span><span class="sxs-lookup"><span data-stu-id="29858-203">This visualization shows the sum of task execution latency per host running on a cluster.</span></span> <span data-ttu-id="29858-204">查看每个主机的任务执行延迟标识具有更高版本的整体任务延迟时间比其他主机的主机。</span><span class="sxs-lookup"><span data-stu-id="29858-204">Viewing task execution latency per host identifies hosts that have much higher overall task latency than other hosts.</span></span> <span data-ttu-id="29858-205">这可能意味着，任务已效率低下或不均衡地分发到主机。</span><span class="sxs-lookup"><span data-stu-id="29858-205">This may mean that tasks have been inefficiently or unevenly distributed to hosts.</span></span>

### <a name="task-metrics"></a><span data-ttu-id="29858-206">任务度量值</span><span class="sxs-lookup"><span data-stu-id="29858-206">Task metrics</span></span>

<span data-ttu-id="29858-207">此可视化效果显示一组用于执行给定的任务的执行度量值。</span><span class="sxs-lookup"><span data-stu-id="29858-207">This visualization shows a set of the execution metrics for a given task's execution.</span></span> <span data-ttu-id="29858-208">这些指标包括大小和数据无序播放的持续时间、 序列化和反序列化操作的持续时间和其他人。</span><span class="sxs-lookup"><span data-stu-id="29858-208">These metrics include the size and duration of a data shuffle, duration of serialization and deserialization operations, and others.</span></span> <span data-ttu-id="29858-209">有关全套指标，查看面板中的 Log Analytics 查询。</span><span class="sxs-lookup"><span data-stu-id="29858-209">For the full set of metrics, view the Log Analytics query for the panel.</span></span> <span data-ttu-id="29858-210">此可视化效果可用于了解构成了一个任务，标识的资源消耗，每个操作的操作。</span><span class="sxs-lookup"><span data-stu-id="29858-210">This visualization is useful for understanding the operations that make up a task and identifying resource consumption of each operation.</span></span> <span data-ttu-id="29858-211">在图中的峰值表示应进行调查的成本高昂的操作。</span><span class="sxs-lookup"><span data-stu-id="29858-211">Spikes in the graph represent costly operations that should be investigated.</span></span>

### <a name="cluster-throughput"></a><span data-ttu-id="29858-212">群集吞吐量</span><span class="sxs-lookup"><span data-stu-id="29858-212">Cluster throughput</span></span>

<span data-ttu-id="29858-213">此可视化效果是由群集和应用程序来表示每个群集和应用程序完成的工作量编制索引的工作项的高级别视图。</span><span class="sxs-lookup"><span data-stu-id="29858-213">This visualization is a high level view of work items indexed by cluster and application to represent the amount of work done per cluster and application.</span></span> <span data-ttu-id="29858-214">它显示了作业、 任务和阶段每个群集、 应用程序和以一分钟为增量阶段完成的数。</span><span class="sxs-lookup"><span data-stu-id="29858-214">It shows the number of jobs, tasks, and stages completed per cluster, application, and stage in one minute increments.</span></span> 

### <a name="streaming-throughputlatency"></a><span data-ttu-id="29858-215">流式处理吞吐量/延迟</span><span class="sxs-lookup"><span data-stu-id="29858-215">Streaming Throughput/Latency</span></span>

<span data-ttu-id="29858-216">与结构化流式处理查询关联的度量值与此可视化效果。</span><span class="sxs-lookup"><span data-stu-id="29858-216">This visualization is related to the metrics associated with a structured streaming query.</span></span> <span data-ttu-id="29858-217">此关系图显示每秒的输入行数和每秒处理的行数。</span><span class="sxs-lookup"><span data-stu-id="29858-217">The graphs shows the number of input rows per second and the number of rows processed per second.</span></span> <span data-ttu-id="29858-218">流式处理度量值也表示每个应用程序。</span><span class="sxs-lookup"><span data-stu-id="29858-218">The streaming metrics are also represented per application.</span></span> <span data-ttu-id="29858-219">处理结构化流式处理查询和可视化效果表示生成 OnQueryProgress 事件时发送这些指标流式处理延迟的时间量，以毫秒为单位，执行所用查询批次。</span><span class="sxs-lookup"><span data-stu-id="29858-219">These metrics are sent when the OnQueryProgress event is generated as the structured streaming query is processed and the visualization represents streaming latency as the amount of time, in milliseconds, taken to execute a query batch.</span></span>

### <a name="resource-consumption-per-executor"></a><span data-ttu-id="29858-220">每个执行器的资源消耗</span><span class="sxs-lookup"><span data-stu-id="29858-220">Resource consumption per executor</span></span>

<span data-ttu-id="29858-221">接下来是资源的一系列仪表板显示特定类型和如何使用每个执行器在每个群集上的可视化效果。</span><span class="sxs-lookup"><span data-stu-id="29858-221">Next is a set of visualizations for the dashboard show the particular type of resource and how it is consumed per executor on each cluster.</span></span> <span data-ttu-id="29858-222">这些可视化效果有助于标识每个执行器的资源消耗中离群值。</span><span class="sxs-lookup"><span data-stu-id="29858-222">These visualizations help identify outliers in resource consumption per executor.</span></span> <span data-ttu-id="29858-223">例如，如果特定执行器的工作分配有偏差，将相对于其他执行器在群集上运行提升资源消耗。</span><span class="sxs-lookup"><span data-stu-id="29858-223">For example, if the work allocation for a particular executor is skewed, resource consumption will be elevated in relation to other executors running on the cluster.</span></span> <span data-ttu-id="29858-224">这可以通过执行器的资源消耗的峰值来确定。</span><span class="sxs-lookup"><span data-stu-id="29858-224">This can be identified by spikes in the resource consumption for an executor.</span></span>

### <a name="executor-compute-time-metrics"></a><span data-ttu-id="29858-225">执行器计算时间指标</span><span class="sxs-lookup"><span data-stu-id="29858-225">Executor compute time metrics</span></span>

<span data-ttu-id="29858-226">接下来是一组显示的执行器序列化时，反序列化时间、 CPU 时间和 Java 虚拟机时间之间的整体执行器计算转换的仪表板的可视化效果。</span><span class="sxs-lookup"><span data-stu-id="29858-226">Next is a set of visualizations for the dashboard that show the ratio of executor serialize time, deserialize time, CPU time, and Java virtual machine time to overall executor compute time.</span></span> <span data-ttu-id="29858-227">此示例演示对整体执行器处理以可视方式以及每种这些四个指标的有影响。</span><span class="sxs-lookup"><span data-stu-id="29858-227">This demonstrates visually how much each of these four metrics are contributing to overall executor processing.</span></span>

### <a name="shuffle-metrics"></a><span data-ttu-id="29858-228">随机选择指标</span><span class="sxs-lookup"><span data-stu-id="29858-228">Shuffle metrics</span></span>

<span data-ttu-id="29858-229">最终的可视化效果显示数据无序播放与结构化流查询跨所有执行程序相关的指标集。</span><span class="sxs-lookup"><span data-stu-id="29858-229">The final set of visualizations show the data shuffle metrics associated with a structured streaming query across all executors.</span></span> <span data-ttu-id="29858-230">其中包括无序播放字节读取、 无序播放写入的字节数、 无序播放内存和磁盘使用情况在查询中使用文件系统位置。</span><span class="sxs-lookup"><span data-stu-id="29858-230">These include shuffle bytes read, shuffle bytes written, shuffle memory, and disk usage in queries where the file system is used.</span></span>

## <a name="next-steps"></a><span data-ttu-id="29858-231">后续步骤</span><span class="sxs-lookup"><span data-stu-id="29858-231">Next steps</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="29858-232">对性能瓶颈进行故障排除</span><span class="sxs-lookup"><span data-stu-id="29858-232">Troubleshoot performance bottlenecks</span></span>](./performance-troubleshooting.md)

<!-- links -->

[rm-cli]: /azure/azure-resource-manager/resource-group-template-deploy-cli
[sku]: /azure/templates/Microsoft.OperationalInsights/2015-11-01-preview/workspaces#sku-object