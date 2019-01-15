---
title: 在 Azure 资源管理器模板上更新资源
description: 介绍如何扩展 Azure 资源管理器模板的功能来更新资源。
author: petertay
ms.date: 10/31/2018
ms.openlocfilehash: 927826283163b2ae45575035168d6238de98dc00
ms.sourcegitcommit: 1f4cdb08fe73b1956e164ad692f792f9f635b409
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2019
ms.locfileid: "54113410"
---
# <a name="update-a-resource-in-an-azure-resource-manager-template"></a>在 Azure 资源管理器模板上更新资源

在某些情况下，在部署过程中需要更新资源。 如果在创建其他依赖资源之前无法指定该资源的所有属性，则可能遇到此情况。 例如，如果为负载均衡器创建后端池，则可以更新虚拟机 (VM) 上的网络接口 (NIC) 来将其包括在后端池中。 虽然资源管理器支持在部署期间更新资源，但必须正确设计模板以避免错误并确保将部署作为更新进行处理。

首先，必须在模板中将资源引用一次来创建资源，然后用同一名称引用资源来对其进行更新。 不过，如果两个资源在模板中具有相同的名称，则 Resource Manager 将引发异常。 若要避免此错误，请在所链接或包括为子模板的另一个模板中使用 `Microsoft.Resources/deployments` 资源类型指定更新的资源。

其次，必须指定要更改的现有属性的名称或者要在嵌套模板中添加的属性的新名称。 还必须指定原始属性及其原始值。 如果提供原始属性和值失败，则资源管理器会假定想要创建新资源并将删除原始资源。

## <a name="example-template"></a>示例模板

让我们看一下演示此操作的示例模板。 我们的模板部署一个名为 `firstVNet` 的虚拟网络，其中包含一个名为 `firstSubnet` 的子网。 然后，它将部署一个名为 `nic1` 的虚拟网络接口 (NIC)，并将其与我们的子网相关联。 然后，一个名为 `updateVNet` 的部署资源包括通过添加名为 `secondSubnet` 的辅助子网来更新 `firstVNet` 资源的嵌套模板。

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

让我们首先看看 `firstVNet` 资源的资源对象。 请注意，我们将重新指定嵌套模板中的 `firstVNet` 的设置&mdash;这是因为资源管理器不允许同一个模板中有相同的部署名称，而嵌套模板被视为不同的模板。 通过重新指定 `firstSubnet` 资源的值，我们将指示资源管理器更新现有的资源，而不是将其删除并重新部署。 最后，在此更新期间采用 `secondSubnet` 的新设置。

## <a name="try-the-template"></a>尝试模板

[GitHub][github] 上提供了一个示例模板。 若要部署该模板，请运行以下 [Azure CLI][cli] 命令：

```bash
az group create --location <location> --name <resource-group-name>
az group deployment create -g <resource-group-name> \
    --template-uri https://raw.githubusercontent.com/mspnp/template-examples/master/example1-update/deploy.json
```

在部署完成后，打开在门户中指定的资源组。 将看到名为 `firstVNet` 的虚拟网络和名为 `nic1` 的 NIC。 单击 `firstVNet`，并单击 `subnets`。 会看到原来创建的 `firstSubnet`，还会看到已在 `updateVNet` 资源中添加的 `secondSubnet`。

![原始子网和更新后的子网](../_images/firstVNet-subnets.png)

然后，返回到资源组并单击 `nic1`，并单击 `IP configurations`。 在 `IP configurations` 部分中，`subnet` 设置为 `firstSubnet (10.0.0.0/24)`。

![nic1 IP 配置设置](../_images/nic1-ipconfigurations.png)

原始 `firstVNet` 已更新而非重新创建。 如果 `firstVNet` 是重新创建的，则 `nic1` 不会与 `firstVNet` 相关联。

## <a name="next-steps"></a>后续步骤

* 了解如何根据某一条件（例如是否存在某个参数值）部署资源。 请参阅[在 Azure 资源管理器模板中有条件地部署资源](./conditional-deploy.md)。

[cli]: /cli/azure/?view=azure-cli-latest
[github]: https://github.com/mspnp/template-examples
