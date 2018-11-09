---
title: 在 Azure 资源管理器模板中有条件地部署资源
description: 介绍如何扩展 Azure 资源管理器模板的功能，根据参数的值有条件地部署资源
author: petertay
ms.date: 10/30/2018
ms.openlocfilehash: 2c74e17a5f38f9225b696640a23b55b1285276bb
ms.sourcegitcommit: e9eb2b895037da0633ef3ccebdea2fcce047620f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2018
ms.locfileid: "50251832"
---
# <a name="conditionally-deploy-a-resource-in-an-azure-resource-manager-template"></a>在 Azure 资源管理器模板中有条件地部署资源

在一些方案中，需要根据某一条件（例如是否存在参数值）设计模板以部署资源。 例如，模板可能部署虚拟网络并包括参数以指定对等互连的其他虚拟网络。 如果未指定对等互连的参数值，则不希望资源管理器部署对等互连资源。

若要完成此操作，请使用资源中的 [condition 元素][azure-resource-manager-condition]来测试参数数组的长度。 若长度为零，则返回 `false` 以阻止部署，但是所有大于零的值将返回 `true` 以允许部署。

## <a name="example-template"></a>示例模板

让我们看一下演示此操作的示例模板。 我们的模板使用 [condition 元素][azure-resource-manager-condition]来控制 `Microsoft.Network/virtualNetworks/virtualNetworkPeerings` 资源的部署。 此资源创建同一区域中的两个 Azure 虚拟网络之间的对等互连。

让我们看看模板的每个部分。

`parameters` 元素定义名为 `virtualNetworkPeerings` 的单个参数： 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "virtualNetworkPeerings": {
      "type": "array",
      "defaultValue": []
    }
  },
```
`virtualNetworkPeerings` 参数为 `array` 且包含以下方案：

```json
"virtualNetworkPeerings": [
    {
      "name": "firstVNet/peering1",
      "properties": {
          "remoteVirtualNetwork": {
              "id": "[resourceId('Microsoft.Network/virtualNetworks','secondVNet')]"
          },
          "allowForwardedTraffic": true,
          "allowGatewayTransit": true,
          "useRemoteGateways": false
      }
    }
]
```

参数中的属性指定[与对等互连虚拟网络相关的设置][vnet-peering-resource-schema]。 指定 `resources` 部分中的 `Microsoft.Network/virtualNetworks/virtualNetworkPeerings` 资源时，将为这些属性提供值：

```json
"resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "[concat('vnp-', copyIndex())]",
      "condition": "[greater(length(parameters('virtualNetworkPeerings')), 0)]",
      "dependsOn": [
        "firstVNet", "secondVNet"
      ],
      "copy": {
          "name": "iterator",
          "count": "[length(variables('peerings'))]",
          "mode": "serial"
      },
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
          },
          "variables": {
          },
          "resources": [
            {
              "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
              "apiVersion": "2016-06-01",
              "location": "[resourceGroup().location]",
              "name": "[variables('peerings')[copyIndex()].name]",
              "properties": "[variables('peerings')[copyIndex()].properties]"
            }
          ],
          "outputs": {
          }
        }
      }
    }
]
```
模板的此部分需要注意几点。 首先，正在部署的实际资源是包括部署 `Microsoft.Network/virtualNetworks/virtualNetworkPeerings` 的它自己模板的类型 `Microsoft.Resources/deployments` 的内联模板。

内联模板的 `name` 是唯一的，方法是将 `copyIndex()` 的当前迭代与前缀 `vnp-` 连接。 

`condition` 元素指定，当 `greater()` 函数计算结果为 `true` 时，应处理资源。 我们要测试 `virtualNetworkPeerings` 参数数组是否 `greater()` 零。 如果大于零，计算结果为 `true` 且满足 `condition`。 否则为 `false`。

接下来，指定 `copy` 循环。 它是 `serial` 循环，这意味着此循环按顺序执行，上一个资源部署完成前，下一个资源处于等待状态。 `count` 属性指定循环的循环访问次数。 我们通常将其设置为 `virtualNetworkPeerings` 数组的长度，因为它包含指定所需部署的资源的参数对象。 但是，如果我们进行此操作，在数组为空的情况下验证将失败，因为资源管理器会通知我们正尝试访问的属性不存在。 但是我们可以解决这一问题。 让我们看看需要的变量：

```json
  "variables": {
    "workaround": {
       "true": "[parameters('virtualNetworkPeerings')]",
       "false": [{
           "name": "workaround",
           "properties": {}
       }]
     },
     "peerings": "[variables('workaround')[string(greater(length(parameters('virtualNetworkPeerings')), 0))]]"
  },
```

`workaround` 变量包括两个属性，一个名为 `true`，一个名为 `false`。 `true` 属性计算结果为 `virtualNetworkPeerings` 参数数组的值。 `false` 属性计算结果为一个空对象（包括资源管理器预期看到的已命名属性）&mdash;请注意，`false` 实际上是一个数组，它和将符合验证的 `virtualNetworkPeerings` 参数一样。 

`peerings` 变量使用 `workaround` 变量，方法是再次测试 `virtualNetworkPeerings` 参数数组的长度是否大于零。 如果大于零，则 `string` 计算结果为 `true`，`workaround` 变量计算结果为 `virtualNetworkPeerings` 参数数组。 否则，计算结果为 `false`，`workaround` 变量计算结果为数组的第一个元素中的空对象。

解决验证问题之后，可以指定嵌套模板中的 `Microsoft.Network/virtualNetworks/virtualNetworkPeerings` 资源的部署，从 `virtualNetworkPeerings` 参数数组传递 `name` 和 `properties`。 可以在嵌套在资源的 `properties` 元素的 `template` 元素中看到。

## <a name="try-the-template"></a>尝试模板

[GitHub][github] 上提供了一个示例模板。 若要部署该模板，请运行以下 [Azure CLI][cli] 命令：

```bash
az group create --location <location> --name <resource-group-name>
az group deployment create -g <resource-group-name> \
    --template-uri https://raw.githubusercontent.com/mspnp/template-examples/master/example2-conditional/deploy.json
```

## <a name="next-steps"></a>后续步骤

* 使用对象而不是标量值作为模板参数。 请参阅[将对象用作 Azure 资源管理器模板中的参数](./objects-as-parameters.md)

<!-- links -->
[azure-resource-manager-condition]: /azure/azure-resource-manager/resource-group-authoring-templates#resources
[azure-resource-manager-variable]: /azure/azure-resource-manager/resource-group-authoring-templates#variables
[vnet-peering-resource-schema]: /azure/templates/microsoft.network/virtualnetworks/virtualnetworkpeerings
[cli]: /cli/azure/?view=azure-cli-latest
[github]: https://github.com/mspnp/template-examples