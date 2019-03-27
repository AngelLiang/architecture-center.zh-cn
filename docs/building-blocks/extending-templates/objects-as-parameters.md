---
title: 将对象用作 Azure 资源管理器模板中的参数
description: 介绍如何扩展 Azure 资源管理器模板的功能，以便将对象用作参数。
author: petertay
ms.date: 10/30/2018
ms.topic: article
ms.service: architecture-center
ms.subservice: reference-architecture
ms.openlocfilehash: d7ef704670aa78738098f08d7a81fb74fa5ad59a
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58244188"
---
# <a name="use-an-object-as-a-parameter-in-an-azure-resource-manager-template"></a><span data-ttu-id="93f98-103">将对象用作 Azure 资源管理器模板中的参数</span><span class="sxs-lookup"><span data-stu-id="93f98-103">Use an object as a parameter in an Azure Resource Manager template</span></span>

<span data-ttu-id="93f98-104">[创建 Azure 资源管理器模板][azure-resource-manager-create-template]时，可在模板中直接指定资源属性值，或在部署过程中定义参数并提供值。</span><span class="sxs-lookup"><span data-stu-id="93f98-104">When you [author Azure Resource Manager templates][azure-resource-manager-create-template], you can either specify resource property values directly in the template or define a parameter and provide values during deployment.</span></span> <span data-ttu-id="93f98-105">可以将每个属性值的参数用于小型部署，但每个部署限 255 个参数。</span><span class="sxs-lookup"><span data-stu-id="93f98-105">It's fine to use a parameter for each property value for small deployments, but there is a limit of 255 parameters per deployment.</span></span> <span data-ttu-id="93f98-106">在遇到更大、更复杂的部署时，可能会用完参数。</span><span class="sxs-lookup"><span data-stu-id="93f98-106">Once you get to larger and more complex deployments you may run out of parameters.</span></span>

<span data-ttu-id="93f98-107">解决此问题的方法之一是将对象用作参数，而不是值。</span><span class="sxs-lookup"><span data-stu-id="93f98-107">One way to solve this problem is to use an object as a parameter instead of a value.</span></span> <span data-ttu-id="93f98-108">为此，请在模板中定义参数，并在部署过程中指定 JSON 对象，而不是单个值。</span><span class="sxs-lookup"><span data-stu-id="93f98-108">To do this, define the parameter in your template and specify a JSON object instead of a single value during deployment.</span></span> <span data-ttu-id="93f98-109">然后，在模板中使用 [`parameter()` 函数][azure-resource-manager-functions]和点运算符引用参数的子属性。</span><span class="sxs-lookup"><span data-stu-id="93f98-109">Then, reference the subproperties of the parameter using the [`parameter()` function][azure-resource-manager-functions] and dot operator in your template.</span></span>

<span data-ttu-id="93f98-110">我们看看部署虚拟网络资源的示例。</span><span class="sxs-lookup"><span data-stu-id="93f98-110">Let's take a look at an example that deploys a virtual network resource.</span></span> <span data-ttu-id="93f98-111">首先，在模板中指定一个 `VNetSettings` 参数并将 `type` 设置为 `object`：</span><span class="sxs-lookup"><span data-stu-id="93f98-111">First, let's specify a `VNetSettings` parameter in our template and set the `type` to `object`:</span></span>

```json
...
"parameters": {
    "VNetSettings":{"type":"object"}
},
```

<span data-ttu-id="93f98-112">接下来，为 `VNetSettings` 对象提供值：</span><span class="sxs-lookup"><span data-stu-id="93f98-112">Next, let's provide values for the `VNetSettings` object:</span></span>

> [!NOTE]
> <span data-ttu-id="93f98-113">若要了解如何在部署过程中提供参数值，请参阅[了解 Azure 资源管理器模板的结构和语法][azure-resource-manager-authoring-templates]的“参数”部分。</span><span class="sxs-lookup"><span data-stu-id="93f98-113">To learn how to provide parameter values during deployment, see the **parameters** section of [understand the structure and syntax of Azure Resource Manager templates][azure-resource-manager-authoring-templates].</span></span>

```json
"parameters":{
    "VNetSettings":{
        "value":{
            "name":"VNet1",
            "addressPrefixes": [
                {
                    "name": "firstPrefix",
                    "addressPrefix": "10.0.0.0/22"
                }
            ],
            "subnets":[
                {
                    "name": "firstSubnet",
                    "addressPrefix": "10.0.0.0/24"
                },
                {
                    "name":"secondSubnet",
                    "addressPrefix":"10.0.1.0/24"
                }
            ]
        }
    }
}
```

<span data-ttu-id="93f98-114">如你所见，单个参数实际上指定了三个子属性：`name`、`addressPrefixes` 和 `subnets`。</span><span class="sxs-lookup"><span data-stu-id="93f98-114">As you can see, our single parameter actually specifies three subproperties: `name`, `addressPrefixes`, and `subnets`.</span></span> <span data-ttu-id="93f98-115">每个子属性指定一个值或其他子属性。</span><span class="sxs-lookup"><span data-stu-id="93f98-115">Each of these subproperties either specifies a value or other subproperties.</span></span> <span data-ttu-id="93f98-116">因此，单个参数指定了部署虚拟网络所需的所有值。</span><span class="sxs-lookup"><span data-stu-id="93f98-116">The result is that our single parameter specifies all the values necessary to deploy our virtual network.</span></span>

<span data-ttu-id="93f98-117">我们看一下模板的其余部分，了解如何使用 `VNetSettings` 对象：</span><span class="sxs-lookup"><span data-stu-id="93f98-117">Now let's have a look at the rest of our template to see how the `VNetSettings` object is used:</span></span>

```json
...
"resources": [
    {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Network/virtualNetworks",
        "name": "[parameters('VNetSettings').name]",
        "location":"[resourceGroup().location]",
        "properties": {
          "addressSpace":{
              "addressPrefixes": [
                "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
              ]
          },
          "subnets":[
              {
                  "name":"[parameters('VNetSettings').subnets[0].name]",
                  "properties": {
                      "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
                  }
              },
              {
                  "name":"[parameters('VNetSettings').subnets[1].name]",
                  "properties": {
                      "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
                  }
              }
          ]
        }
    }
  ]
```

<span data-ttu-id="93f98-118">`VNetSettings` 对象的值被应用到虚拟网络资源所需要的属性，该资源使用具有 `[]` 数组索引器和点运算符的 `parameters()` 函数。</span><span class="sxs-lookup"><span data-stu-id="93f98-118">The values of our `VNetSettings` object are applied to the properties required by our virtual network resource using the `parameters()` function with both the `[]` array indexer and the dot operator.</span></span> <span data-ttu-id="93f98-119">如果只是想静态地将参数对象的值应用到资源，那么此方法适用。</span><span class="sxs-lookup"><span data-stu-id="93f98-119">This approach works if you just want to statically apply the values of the parameter object to the resource.</span></span> <span data-ttu-id="93f98-120">但是，如果希望在部署过程中动态分配属性值数组，则可使用[复制循环][azure-resource-manager-create-multiple-instances]。</span><span class="sxs-lookup"><span data-stu-id="93f98-120">However, if you want to dynamically assign an array of property values during deployment you can use a [copy loop][azure-resource-manager-create-multiple-instances].</span></span> <span data-ttu-id="93f98-121">为了使用复制循环，可提供资源属性值的 JSON 数组，复制循环再将值动态地应用到资源的属性中。</span><span class="sxs-lookup"><span data-stu-id="93f98-121">To use a copy loop, you provide a JSON array of resource property values and the copy loop dynamically applies the values to the resource's properties.</span></span>

<span data-ttu-id="93f98-122">如果使用动态方法，需注意一个问题。</span><span class="sxs-lookup"><span data-stu-id="93f98-122">There is one issue to be aware of if you use the dynamic approach.</span></span> <span data-ttu-id="93f98-123">为演示此问题，让我们看看属性值的典型数组。</span><span class="sxs-lookup"><span data-stu-id="93f98-123">To demonstrate the issue, let's take a look at a typical array of property values.</span></span> <span data-ttu-id="93f98-124">在此示例中，属性的值存储在一个变量中。</span><span class="sxs-lookup"><span data-stu-id="93f98-124">In this example the values for our properties are stored in a variable.</span></span> <span data-ttu-id="93f98-125">请注意，此处有两个数组&mdash;一个名为 `firstProperty`，另一个名为 `secondProperty`。</span><span class="sxs-lookup"><span data-stu-id="93f98-125">Notice we have two arrays here&mdash;one named `firstProperty` and one named `secondProperty`.</span></span>

```json
"variables": {
    "firstProperty": [
        {
            "name": "A",
            "type": "typeA"
        },
        {
            "name": "B",
            "type": "typeB"
        },
        {
            "name": "C",
            "type": "typeC"
        }
    ],
    "secondProperty": [
        "one","two", "three"
    ]
}
```

<span data-ttu-id="93f98-126">现在我们来看看如何使用复制循环访问变量中的属性。</span><span class="sxs-lookup"><span data-stu-id="93f98-126">Now let's take a look at the way we access the properties in the variable using a copy loop.</span></span>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    ...
    "copy": {
        "name": "copyLoop1",
        "count": "[length(variables('firstProperty'))]"
    },
    ...
    "properties": {
        "name": { "value": "[variables('firstProperty')[copyIndex()].name]" },
        "type": { "value": "[variables('firstProperty')[copyIndex()].type]" },
        "number": { "value": "[variables('secondProperty')[copyIndex()]]" }
    }
}
```

<span data-ttu-id="93f98-127">`copyIndex()` 函数返回复制循环的当前迭代，我们将它同时用作两个数组中的索引。</span><span class="sxs-lookup"><span data-stu-id="93f98-127">The `copyIndex()` function returns the current iteration of the copy loop, and we use that as an index into each of the two arrays simultaneously.</span></span>

<span data-ttu-id="93f98-128">当这两个数组长度相同时，这种方法没有任何问题。</span><span class="sxs-lookup"><span data-stu-id="93f98-128">This works fine when the two arrays are the same length.</span></span> <span data-ttu-id="93f98-129">如果出错并且两个数组的长度不同，就会出现问题&mdash;在这种情况下，模板将在部署过程中验证失败。</span><span class="sxs-lookup"><span data-stu-id="93f98-129">The issue arises if you've made a mistake and the two arrays are different lengths&mdash;in this case your template will fail validation during deployment.</span></span> <span data-ttu-id="93f98-130">通过将所有属性包含在单个对象中可以避免此问题，因为这样更容易了解什么时候丢失了一个值。</span><span class="sxs-lookup"><span data-stu-id="93f98-130">You can avoid this issue by including all your properties in a single object, because it is much easier to see when a value is missing.</span></span> <span data-ttu-id="93f98-131">例如，我们来看看另一个参数对象，其中 `propertyObject` 数组的每个元素都是前面 `firstProperty` 和 `secondProperty` 数组的联合。</span><span class="sxs-lookup"><span data-stu-id="93f98-131">For example, let's take a look another parameter object in which each element of the `propertyObject` array is the union of the `firstProperty` and `secondProperty` arrays from earlier.</span></span>

```json
"variables": {
    "propertyObject": [
        {
            "name": "A",
            "type": "typeA",
            "number": "one"
        },
        {
            "name": "B",
            "type": "typeB",
            "number": "two"
        },
        {
            "name": "C",
            "type": "typeC"
        }
    ]
}
```

<span data-ttu-id="93f98-132">注意到数组中的第三个元素吗？</span><span class="sxs-lookup"><span data-stu-id="93f98-132">Notice the third element in the array?</span></span> <span data-ttu-id="93f98-133">它缺少 `number` 属性，但是当以这种方式创建参数值时，更容易注意到该属性丢失。</span><span class="sxs-lookup"><span data-stu-id="93f98-133">It's missing the `number` property, but it's much easier to notice that you've missed it when you're authoring the parameter values this way.</span></span>

## <a name="using-a-property-object-in-a-copy-loop"></a><span data-ttu-id="93f98-134">在复制循环中使用属性对象</span><span class="sxs-lookup"><span data-stu-id="93f98-134">Using a property object in a copy loop</span></span>

<span data-ttu-id="93f98-135">此方法在与[串行复制循环][azure-resource-manager-create-multiple] 结合使用时更为有用，特别是在部署子资源时。</span><span class="sxs-lookup"><span data-stu-id="93f98-135">This approach becomes even more useful when combined with the [serial copy loop][azure-resource-manager-create-multiple], particularly for deploying child resources.</span></span>

<span data-ttu-id="93f98-136">为了说明这一点，我们看一下使用两个安全规则部署[网络安全组 (NSG)][nsg] 的模板。</span><span class="sxs-lookup"><span data-stu-id="93f98-136">To demonstrate this, let's look at a template that deploys a [network security group (NSG)][nsg] with two security rules.</span></span>

<span data-ttu-id="93f98-137">首先，我们看看参数。</span><span class="sxs-lookup"><span data-stu-id="93f98-137">First, let's take a look at our parameters.</span></span> <span data-ttu-id="93f98-138">在查看模板时会看到，我们已经定义了一个名为 `networkSecurityGroupsSettings` 的参数，其中包括一个名为 `securityRules` 的数组。</span><span class="sxs-lookup"><span data-stu-id="93f98-138">When we look at our template we'll see that we've defined one parameter named `networkSecurityGroupsSettings` that includes an array named `securityRules`.</span></span> <span data-ttu-id="93f98-139">该数组包含两个为安全规则指定多个设置的 JSON 对象。</span><span class="sxs-lookup"><span data-stu-id="93f98-139">This array contains two JSON objects that specify a number of settings for a security rule.</span></span>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
      "networkSecurityGroupsSettings": {
      "value": {
          "securityRules": [
            {
              "name": "RDPAllow",
              "description": "allow RDP connections",
              "direction": "Inbound",
              "priority": 100,
              "sourceAddressPrefix": "*",
              "destinationAddressPrefix": "10.0.0.0/24",
              "sourcePortRange": "*",
              "destinationPortRange": "3389",
              "access": "Allow",
              "protocol": "Tcp"
            },
            {
              "name": "HTTPAllow",
              "description": "allow HTTP connections",
              "direction": "Inbound",
              "priority": 200,
              "sourceAddressPrefix": "*",
              "destinationAddressPrefix": "10.0.1.0/24",
              "sourcePortRange": "*",
              "destinationPortRange": "80",
              "access": "Allow",
              "protocol": "Tcp"
            }
          ]
        }
      }
    }
  }
```

<span data-ttu-id="93f98-140">现在我们看看模板。</span><span class="sxs-lookup"><span data-stu-id="93f98-140">Now let's take a look at our template.</span></span> <span data-ttu-id="93f98-141">名为 `NSG1` 的第一个资源部署 NSG。</span><span class="sxs-lookup"><span data-stu-id="93f98-141">Our first resource named `NSG1` deploys the NSG.</span></span> <span data-ttu-id="93f98-142">名为 `loop-0` 的第二个资源执行两个函数：首先，对 NSG 执行 `dependsOn`，使其部署只有在完成 `NSG1` 后才开始，这是顺序循环的第一次迭代。</span><span class="sxs-lookup"><span data-stu-id="93f98-142">Our second resource named `loop-0` performs two functions: first, it `dependsOn` the NSG so its deployment doesn't begin until `NSG1` is completed, and it is the first iteration of the sequential loop.</span></span> <span data-ttu-id="93f98-143">第三个资源是嵌套模板，如最后一个示例中所示，它使用某个对象作为参数值部署安全规则。</span><span class="sxs-lookup"><span data-stu-id="93f98-143">Our third resource is a nested template that deploys our security rules using an object for its parameter values as in the last example.</span></span>

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "networkSecurityGroupsSettings": {"type":"object"}
  },
  "variables": {},
  "resources": [
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "NSG1",
      "location":"[resourceGroup().location]",
      "properties": {
          "securityRules":[]
      }
    },
    {
        "apiVersion": "2015-01-01",
        "type": "Microsoft.Resources/deployments",
        "name": "loop-0",
        "dependsOn": [
            "NSG1"
        ],
        "properties": {
            "mode":"Incremental",
            "parameters":{},
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {},
                "variables": {},
                "resources": [],
                "outputs": {}
            }
        }
    },
    {
        "apiVersion": "2015-01-01",
        "type": "Microsoft.Resources/deployments",
        "name": "[concat('loop-', copyIndex(1))]",
        "dependsOn": [
          "[concat('loop-', copyIndex())]"
        ],
        "copy": {
          "name": "iterator",
          "count": "[length(parameters('networkSecurityGroupsSettings').securityRules)]"
        },
        "properties": {
          "mode": "Incremental",
          "template": {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
           "parameters": {},
            "variables": {},
            "resources": [
                {
                    "name": "[concat('NSG1/' , parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].name)]",
                    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
                    "apiVersion": "2016-09-01",
                    "location":"[resourceGroup().location]",
                    "properties":{
                        "description": "[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].description]",
                        "priority":"[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].priority]",
                        "protocol":"[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].protocol]",
                        "sourcePortRange": "[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].sourcePortRange]",
                        "destinationPortRange": "[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].destinationPortRange]",
                        "sourceAddressPrefix": "[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].sourceAddressPrefix]",
                        "destinationAddressPrefix": "[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].destinationAddressPrefix]",
                        "access":"[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].access]",
                        "direction":"[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].direction]"
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

<span data-ttu-id="93f98-144">让我们详细了解如何在 `securityRules` 子资源中指定属性值。</span><span class="sxs-lookup"><span data-stu-id="93f98-144">Let's take a closer look at how we specify our property values in the `securityRules` child resource.</span></span> <span data-ttu-id="93f98-145">所有的属性都使用 `parameter()` 函数进行引用，然后我们使用点运算符引用根据迭代的当前值建立索引的 `securityRules` 数组。</span><span class="sxs-lookup"><span data-stu-id="93f98-145">All of our properties are referenced using the `parameter()` function, and then we use the dot operator to reference our `securityRules` array, indexed by the current value of the iteration.</span></span> <span data-ttu-id="93f98-146">最后，使用另一个点运算符引用对象名称。</span><span class="sxs-lookup"><span data-stu-id="93f98-146">Finally, we use another dot operator to reference the name of the object.</span></span>

## <a name="try-the-template"></a><span data-ttu-id="93f98-147">尝试模板</span><span class="sxs-lookup"><span data-stu-id="93f98-147">Try the template</span></span>

<span data-ttu-id="93f98-148">[GitHub][github] 上提供了一个示例模板。</span><span class="sxs-lookup"><span data-stu-id="93f98-148">An example template is available on [GitHub][github].</span></span> <span data-ttu-id="93f98-149">若要部署该模板，请克隆存储库并运行以下 [Azure CLI][cli] 命令：</span><span class="sxs-lookup"><span data-stu-id="93f98-149">To deploy the template, clone the repo and run the following [Azure CLI][cli] commands:</span></span>

```bash
git clone https://github.com/mspnp/template-examples.git
cd template-examples/example3-object-param
az group create --location <location> --name <resource-group-name>
az group deployment create -g <resource-group-name> \
    --template-uri https://raw.githubusercontent.com/mspnp/template-examples/master/example3-object-param/deploy.json \
    --parameters deploy.parameters.json
```

## <a name="next-steps"></a><span data-ttu-id="93f98-150">后续步骤</span><span class="sxs-lookup"><span data-stu-id="93f98-150">Next steps</span></span>

- <span data-ttu-id="93f98-151">了解如何创建一个模板来循环访问对象数组并将其转换为 JSON 架构。</span><span class="sxs-lookup"><span data-stu-id="93f98-151">Learn how to create a template that iterates through an object array and transforms it into a JSON schema.</span></span> <span data-ttu-id="93f98-152">请参阅[在 Azure 资源管理器模板中实现属性转换器和收集器](./collector.md)</span><span class="sxs-lookup"><span data-stu-id="93f98-152">See [Implement a property transformer and collector in an Azure Resource Manager template](./collector.md)</span></span>

<!-- links -->

[azure-resource-manager-authoring-templates]: /azure/azure-resource-manager/resource-group-authoring-templates
[azure-resource-manager-create-template]: /azure/azure-resource-manager/resource-manager-create-first-template
[azure-resource-manager-create-multiple-instances]: /azure/azure-resource-manager/resource-group-create-multiple
[azure-resource-manager-functions]: /azure/azure-resource-manager/resource-group-template-functions-resource
[nsg]: /azure/virtual-network/virtual-networks-nsg
[cli]: /cli/azure/?view=azure-cli-latest
[github]: https://github.com/mspnp/template-examples
