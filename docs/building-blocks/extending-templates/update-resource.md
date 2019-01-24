---
title: 在 Azure 资源管理器模板上更新资源
description: 介绍如何扩展 Azure 资源管理器模板的功能来更新资源。
author: petertay
ms.date: 10/31/2018
ms.topic: article
ms.service: architecture-center
ms.subservice: reference-architecture
ms.openlocfilehash: de76e69e94917bbbe94c0f87fda2cdbe415181dc
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54487131"
---
# <a name="update-a-resource-in-an-azure-resource-manager-template"></a><span data-ttu-id="75a9a-103">在 Azure 资源管理器模板上更新资源</span><span class="sxs-lookup"><span data-stu-id="75a9a-103">Update a resource in an Azure Resource Manager template</span></span>

<span data-ttu-id="75a9a-104">在某些情况下，在部署过程中需要更新资源。</span><span class="sxs-lookup"><span data-stu-id="75a9a-104">There are some scenarios in which you need to update a resource during a deployment.</span></span> <span data-ttu-id="75a9a-105">如果在创建其他依赖资源之前无法指定该资源的所有属性，则可能遇到此情况。</span><span class="sxs-lookup"><span data-stu-id="75a9a-105">You might encounter this scenario when you cannot specify all the properties for a resource until other, dependent resources are created.</span></span> <span data-ttu-id="75a9a-106">例如，如果为负载均衡器创建后端池，则可以更新虚拟机 (VM) 上的网络接口 (NIC) 来将其包括在后端池中。</span><span class="sxs-lookup"><span data-stu-id="75a9a-106">For example, if you create a backend pool for a load balancer, you might update the network interfaces (NICs) on your virtual machines (VMs) to include them in the backend pool.</span></span> <span data-ttu-id="75a9a-107">虽然资源管理器支持在部署期间更新资源，但必须正确设计模板以避免错误并确保将部署作为更新进行处理。</span><span class="sxs-lookup"><span data-stu-id="75a9a-107">And while Resource Manager supports updating resources during deployment, you must design your template correctly to avoid errors and to ensure the deployment is handled as an update.</span></span>

<span data-ttu-id="75a9a-108">首先，必须在模板中将资源引用一次来创建资源，然后用同一名称引用资源来对其进行更新。</span><span class="sxs-lookup"><span data-stu-id="75a9a-108">First, you must reference the resource once in the template to create it and then reference the resource by the same name to update it later.</span></span> <span data-ttu-id="75a9a-109">不过，如果两个资源在模板中具有相同的名称，则 Resource Manager 将引发异常。</span><span class="sxs-lookup"><span data-stu-id="75a9a-109">However, if two resources have the same name in a template, Resource Manager throws an exception.</span></span> <span data-ttu-id="75a9a-110">若要避免此错误，请在所链接或包括为子模板的另一个模板中使用 `Microsoft.Resources/deployments` 资源类型指定更新的资源。</span><span class="sxs-lookup"><span data-stu-id="75a9a-110">To avoid this error, specify the updated resource in a second template that's either linked or included as a subtemplate using the `Microsoft.Resources/deployments` resource type.</span></span>

<span data-ttu-id="75a9a-111">其次，必须指定要更改的现有属性的名称或者要在嵌套模板中添加的属性的新名称。</span><span class="sxs-lookup"><span data-stu-id="75a9a-111">Second, you must either specify the name of the existing property to change or a new name for a property to add in the nested template.</span></span> <span data-ttu-id="75a9a-112">还必须指定原始属性及其原始值。</span><span class="sxs-lookup"><span data-stu-id="75a9a-112">You must also specify the original properties and their original values.</span></span> <span data-ttu-id="75a9a-113">如果提供原始属性和值失败，则资源管理器会假定想要创建新资源并将删除原始资源。</span><span class="sxs-lookup"><span data-stu-id="75a9a-113">If you fail to provide the original properties and values, Resource Manager assumes you want to create a new resource and deletes the original resource.</span></span>

## <a name="example-template"></a><span data-ttu-id="75a9a-114">示例模板</span><span class="sxs-lookup"><span data-stu-id="75a9a-114">Example template</span></span>

<span data-ttu-id="75a9a-115">让我们看一下演示此操作的示例模板。</span><span class="sxs-lookup"><span data-stu-id="75a9a-115">Let's look at an example template that demonstrates this.</span></span> <span data-ttu-id="75a9a-116">我们的模板部署一个名为 `firstVNet` 的虚拟网络，其中包含一个名为 `firstSubnet` 的子网。</span><span class="sxs-lookup"><span data-stu-id="75a9a-116">Our template deploys a virtual network named `firstVNet` that has one subnet named `firstSubnet`.</span></span> <span data-ttu-id="75a9a-117">然后，它将部署一个名为 `nic1` 的虚拟网络接口 (NIC)，并将其与我们的子网相关联。</span><span class="sxs-lookup"><span data-stu-id="75a9a-117">It then deploys a virtual network interface (NIC) named `nic1` and associates it with our subnet.</span></span> <span data-ttu-id="75a9a-118">然后，一个名为 `updateVNet` 的部署资源包括通过添加名为 `secondSubnet` 的辅助子网来更新 `firstVNet` 资源的嵌套模板。</span><span class="sxs-lookup"><span data-stu-id="75a9a-118">Then, a deployment resource named `updateVNet` includes a nested template that updates our `firstVNet` resource by adding a second subnet named `secondSubnet`.</span></span>

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
      {
      "apiVersion": "2016-03-30",
      "name": "firstVNet",
      "location":"[resourceGroup().location]",
      "type": "Microsoft.Network/virtualNetworks",
      "properties": {
          "addressSpace":{"addressPrefixes": [
              "10.0.0.0/22"
          ]},
          "subnets":[
              {
                  "name":"firstSubnet",
                  "properties":{
                    "addressPrefix":"10.0.0.0/24"
                  }
              }
            ]
      }
    },
    {
        "apiVersion": "2015-06-15",
        "type":"Microsoft.Network/networkInterfaces",
        "name":"nic1",
        "location":"[resourceGroup().location]",
        "dependsOn": [
            "firstVNet"
        ],
        "properties": {
            "ipConfigurations":[
                {
                    "name":"ipconfig1",
                    "properties": {
                        "privateIPAllocationMethod":"Dynamic",
                        "subnet": {
                            "id": "[concat(resourceId('Microsoft.Network/virtualNetworks','firstVNet'),'/subnets/firstSubnet')]"
                        }
                    }
                }
            ]
        }
    },
    {
      "apiVersion": "2015-01-01",
      "type": "Microsoft.Resources/deployments",
      "name": "updateVNet",
      "dependsOn": [
          "nic1"
      ],
      "properties": {
        "mode": "Incremental",
        "parameters": {},
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
              {
                  "apiVersion": "2016-03-30",
                  "name": "firstVNet",
                  "location":"[resourceGroup().location]",
                  "type": "Microsoft.Network/virtualNetworks",
                  "properties": {
                      "addressSpace": "[reference('firstVNet').addressSpace]",
                      "subnets":[
                          {
                              "name":"[reference('firstVNet').subnets[0].name]",
                              "properties":{
                                  "addressPrefix":"[reference('firstVNet').subnets[0].properties.addressPrefix]"
                                  }
                          },
                          {
                              "name":"secondSubnet",
                              "properties":{
                                  "addressPrefix":"10.0.1.0/24"
                                  }
                          }
                     ]
                  }
              }
          ],
          "outputs": {}
          }
        }
    }
  ],
  "outputs": {}
}
```

<span data-ttu-id="75a9a-119">让我们首先看看 `firstVNet` 资源的资源对象。</span><span class="sxs-lookup"><span data-stu-id="75a9a-119">Let's take a look at the resource object for our `firstVNet` resource first.</span></span> <span data-ttu-id="75a9a-120">请注意，我们将重新指定嵌套模板中的 `firstVNet` 的设置&mdash;这是因为资源管理器不允许同一个模板中有相同的部署名称，而嵌套模板被视为不同的模板。</span><span class="sxs-lookup"><span data-stu-id="75a9a-120">Notice that we respecify the settings for our `firstVNet` in a nested template&mdash;this is because Resource Manager doesn't allow the same deployment name within the same template and nested templates are considered to be a different template.</span></span> <span data-ttu-id="75a9a-121">通过重新指定 `firstSubnet` 资源的值，我们将指示资源管理器更新现有的资源，而不是将其删除并重新部署。</span><span class="sxs-lookup"><span data-stu-id="75a9a-121">By respecifying our values for our `firstSubnet` resource, we are telling Resource Manager to update the existing resource instead of deleting it and redeploying it.</span></span> <span data-ttu-id="75a9a-122">最后，在此更新期间采用 `secondSubnet` 的新设置。</span><span class="sxs-lookup"><span data-stu-id="75a9a-122">Finally, our new settings for `secondSubnet` are picked up during this update.</span></span>

## <a name="try-the-template"></a><span data-ttu-id="75a9a-123">尝试模板</span><span class="sxs-lookup"><span data-stu-id="75a9a-123">Try the template</span></span>

<span data-ttu-id="75a9a-124">[GitHub][github] 上提供了一个示例模板。</span><span class="sxs-lookup"><span data-stu-id="75a9a-124">An example template is available on [GitHub][github].</span></span> <span data-ttu-id="75a9a-125">若要部署该模板，请运行以下 [Azure CLI][cli] 命令：</span><span class="sxs-lookup"><span data-stu-id="75a9a-125">To deploy the template, run the following [Azure CLI][cli] commands:</span></span>

```bash
az group create --location <location> --name <resource-group-name>
az group deployment create -g <resource-group-name> \
    --template-uri https://raw.githubusercontent.com/mspnp/template-examples/master/example1-update/deploy.json
```

<span data-ttu-id="75a9a-126">在部署完成后，打开在门户中指定的资源组。</span><span class="sxs-lookup"><span data-stu-id="75a9a-126">Once deployment has finished, open the resource group you specified in the portal.</span></span> <span data-ttu-id="75a9a-127">将看到名为 `firstVNet` 的虚拟网络和名为 `nic1` 的 NIC。</span><span class="sxs-lookup"><span data-stu-id="75a9a-127">You see a virtual network named `firstVNet` and a NIC named `nic1`.</span></span> <span data-ttu-id="75a9a-128">单击 `firstVNet`，并单击 `subnets`。</span><span class="sxs-lookup"><span data-stu-id="75a9a-128">Click `firstVNet`, then click `subnets`.</span></span> <span data-ttu-id="75a9a-129">会看到原来创建的 `firstSubnet`，还会看到已在 `updateVNet` 资源中添加的 `secondSubnet`。</span><span class="sxs-lookup"><span data-stu-id="75a9a-129">You see the `firstSubnet` that was originally created, and you see the `secondSubnet` that was added in the `updateVNet` resource.</span></span>

![原始子网和更新后的子网](../_images/firstVNet-subnets.png)

<span data-ttu-id="75a9a-131">然后，返回到资源组并单击 `nic1`，并单击 `IP configurations`。</span><span class="sxs-lookup"><span data-stu-id="75a9a-131">Then, go back to the resource group and click `nic1` then click `IP configurations`.</span></span> <span data-ttu-id="75a9a-132">在 `IP configurations` 部分中，`subnet` 设置为 `firstSubnet (10.0.0.0/24)`。</span><span class="sxs-lookup"><span data-stu-id="75a9a-132">In the `IP configurations` section, the `subnet` is set to `firstSubnet (10.0.0.0/24)`.</span></span>

![nic1 IP 配置设置](../_images/nic1-ipconfigurations.png)

<span data-ttu-id="75a9a-134">原始 `firstVNet` 已更新而非重新创建。</span><span class="sxs-lookup"><span data-stu-id="75a9a-134">The original `firstVNet` has been updated instead of recreated.</span></span> <span data-ttu-id="75a9a-135">如果 `firstVNet` 是重新创建的，则 `nic1` 不会与 `firstVNet` 相关联。</span><span class="sxs-lookup"><span data-stu-id="75a9a-135">If `firstVNet` had been recreated, `nic1` would not be associated with `firstVNet`.</span></span>

## <a name="next-steps"></a><span data-ttu-id="75a9a-136">后续步骤</span><span class="sxs-lookup"><span data-stu-id="75a9a-136">Next steps</span></span>

* <span data-ttu-id="75a9a-137">了解如何根据某一条件（例如是否存在某个参数值）部署资源。</span><span class="sxs-lookup"><span data-stu-id="75a9a-137">Learn how deploy a resource based on a condition, such as whether a parameter value is present.</span></span> <span data-ttu-id="75a9a-138">请参阅[在 Azure 资源管理器模板中有条件地部署资源](./conditional-deploy.md)。</span><span class="sxs-lookup"><span data-stu-id="75a9a-138">See [Conditionally deploy a resource in an Azure Resource Manager template](./conditional-deploy.md).</span></span>

[cli]: /cli/azure/?view=azure-cli-latest
[github]: https://github.com/mspnp/template-examples
