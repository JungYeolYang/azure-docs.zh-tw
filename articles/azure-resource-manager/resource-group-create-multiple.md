---
title: 部署 Azure 資源的多個執行個體 | Microsoft Docs
description: 使用「Azure 資源管理員」範本中的複製作業和陣列，並在部署資源時多次逐一執行。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
editor: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/01/2019
ms.author: tomfitz
ms.openlocfilehash: 05b68fde30587967f65ee362344eea9a258f89a7
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205980"
---
# <a name="deploy-more-than-one-instance-of-a-resource-or-property-in-azure-resource-manager-templates"></a>在 Azure Resource Manager 範本中部署資源或屬性的多個執行個體

本文示範如何逐一查看您的 Azure Resource Manager 範本，以建立資源的多個執行個體。 若需要指定是否要部署資源，請參閱[條件元素](resource-group-authoring-templates.md#condition)。

如需教學課程，請參閱[教學課程：使用 Resource Manager 範本建立多個資源執行個體](./resource-manager-tutorial-create-multiple-instances.md)。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-iteration"></a>資源反覆項目

當您在部署期間必須決定要建立資源的一個或多個執行個體時，請將 `copy` 元素新增至資源類型。 在複製元素中，您可以指定反覆項目的數目以及此迴圈的名稱。 計數值必須是正整數且不得超過 800。 

要多次建立的資源會採用下列格式：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 3
      }
    }
  ],
  "outputs": {}
}
```

請注意，每個資源的名稱均包含 `copyIndex()` 函式，並會傳回目前的反覆項目迴圈。 `copyIndex()`是以零為基礎。 因此，下列範例：

```json
"name": "[concat('storage', copyIndex())]",
```

會建立這些名稱︰

* storage0
* storage1
* storage2.

若要位移索引值，您可以傳遞 copyIndex() 函式中的值。 要执行的迭代次数仍被指定在 copy 元素中，但 copyIndex 的值已按指定的值发生了偏移。 因此，下列範例：

```json
"name": "[concat('storage', copyIndex(1))]",
```

将创建以下名称：

* storage1
* storage2
* storage3

使用陣列時，複製作業會有幫助，因為您可以逐一查看陣列中的每個項目。 使用陣列上的 `length` 函式指定反覆運算的計數，並使用 `copyIndex` 來擷取陣列中目前的索引。 因此，下列範例：

```json
"parameters": { 
  "org": { 
    "type": "array", 
    "defaultValue": [ 
      "contoso", 
      "fabrikam", 
      "coho" 
    ] 
  }
}, 
"resources": [ 
  { 
    "name": "[concat('storage', parameters('org')[copyIndex()])]", 
    "copy": { 
      "name": "storagecopy", 
      "count": "[length(parameters('org'))]" 
    }, 
    ...
  } 
]
```

會建立這些名稱︰

* storagecontoso
* storagefabrikam
* storagecoho

根據預設，Resource Manager 會以平行方式建立資源。 不保證資源會循序建立。 不過，建議您指定將資源部署在序列中。 例如，在更新生產環境時，您可以錯開更新，因此任何一次就只會更新特定數目。

若要以序列方式部署資源的多個執行個體，請將 `mode` 設定為 **serial** 並將 `batchSize` 設為一次要部署的執行個體數目。 透過序列模式，Resource Manager 會在迴圈中較早的執行個體上建立相依性，因此在前一批次完成之前，它不會啟動一個批次。

例如，若要以序列方式一次部署兩個儲存體帳戶，請使用：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 4,
        "mode": "serial",
        "batchSize": 2
      }
    }
  ],
  "outputs": {}
}
```

mode 屬性也接受**平行**，這是預設值。

如需使用巢狀範本中的複製資訊，請參閱[使用複製](resource-group-linked-templates.md#using-copy)。

## <a name="property-iteration"></a>屬性反覆運算

若要在資源上為某個屬性建立多個值，請在 properties 元素中新增 `copy` 陣列。 此陣列包含物件，且每個物件具有下列屬性：

* name - 要建立數個值的屬性名稱
* count - 要建立的值數目。 計數值必須是正整數且不得超過 800。
* input - 包含要指派給屬性之值的物件  

下列範例示範如何將 `copy` 套用至虛擬機器的 dataDisks 屬性：

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "copy": [{
        "name": "dataDisks",
        "count": 3,
        "input": {
          "lun": "[copyIndex('dataDisks')]",
          "createOption": "Empty",
          "diskSizeGB": "1023"
        }
      }],
      ...
```

請注意，在屬性反覆運算內使用 `copyIndex` 時，您必須提供反覆運算的名稱。 搭配資源反覆運算使用時，您不必提供名稱。

Resource Manager 會在部署期間展開 `copy` 陣列。 陣列名稱會變成屬性名稱。 輸入值會變成物件屬性。 已部署的範本會變成：

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": "1023"
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": "1023"
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": "1023"
        }
      ],
      ...
```

copy 元素為一個陣列，因此，您可以針對資源指定一個以上的屬性。 針對每個要建立的屬性新增物件。

```json
{
  "name": "string",
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "properties": {
    "copy": [
      {
        "name": "loadBalancingRules",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      },
      {
        "name": "probes",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      }
    ]
  }
}
```

您可以一起使用資源和屬性反覆運算。 依名稱參考屬性反覆運算。

```json
{
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[concat(parameters('vnetname'), copyIndex())]",
  "apiVersion": "2018-04-01",
  "copy":{
    "count": 2,
    "name": "vnetloop"
  },
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[parameters('addressPrefix')]"
      ]
    },
    "copy": [
      {
        "name": "subnets",
        "count": 2,
        "input": {
          "name": "[concat('subnet-', copyIndex('subnets'))]",
          "properties": {
            "addressPrefix": "[variables('subnetAddressPrefix')[copyIndex('subnets')]]"
          }
        }
      }
    ]
  }
}
```

## <a name="variable-iteration"></a>變數反覆項目

若要建立變數的多個執行個體，請在變數區段中使用 `copy` 屬性。 您可以建立一個由 `input` 屬性中的值建構的元素陣列。 您可以在變數中使用 `copy` 屬性，也可以在變數部分的最上層使用。 在變數反覆項目內使用 `copyIndex` 時，您必須提供反覆項目的名稱。

如需通过简单示例来了解如何创建字符串值的数组，请参阅[复制数组模板](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/copy-array/azuredeploy.json)。

下列範例示範使用動態建構的元素建立陣列變數的幾種不同方式。 它示範如何在變數內使用複製來建立物件和字串的陣列。 它也會示範如何在最上層使用複製來建立物件、字串和整數的陣列。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "disk-array-on-object": {
      "copy": [
        {
          "name": "disks",
          "count": 5,
          "input": {
            "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
            "diskSizeGB": "1",
            "diskIndex": "[copyIndex('disks')]"
          }
        },
        {
          "name": "diskNames",
          "count": 5,
          "input": "[concat('myDataDisk', copyIndex('diskNames', 1))]"
        }
      ]
    },
    "copy": [
      {
        "name": "top-level-object-array",
        "count": 5,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('top-level-object-array', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('top-level-object-array')]"
        }
      },
      {
        "name": "top-level-string-array",
        "count": 5,
        "input": "[concat('myDataDisk', copyIndex('top-level-string-array', 1))]"
      },
      {
        "name": "top-level-integer-array",
        "count": 5,
        "input": "[copyIndex('top-level-integer-array')]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "exampleObject": {
      "value": "[variables('disk-array-on-object')]",
      "type": "object"
    },
    "exampleArrayOnObject": {
      "value": "[variables('disk-array-on-object').disks]",
      "type" : "array"
    },
    "exampleObjectArray": {
      "value": "[variables('top-level-object-array')]",
      "type" : "array"
    },
    "exampleStringArray": {
      "value": "[variables('top-level-string-array')]",
      "type" : "array"
    },
    "exampleIntegerArray": {
      "value": "[variables('top-level-integer-array')]",
      "type" : "array"
    }
  }
}
```

创建的变量的类型取决于输入对象。 例如，在上一示例中名为 **top-level-object-array** 的变量返回：

```json
[
  {
    "name": "myDataDisk1",
    "diskSizeGB": "1",
    "diskIndex": 0
  },
  {
    "name": "myDataDisk2",
    "diskSizeGB": "1",
    "diskIndex": 1
  },
  {
    "name": "myDataDisk3",
    "diskSizeGB": "1",
    "diskIndex": 2
  },
  {
    "name": "myDataDisk4",
    "diskSizeGB": "1",
    "diskIndex": 3
  },
  {
    "name": "myDataDisk5",
    "diskSizeGB": "1",
    "diskIndex": 4
  }
]
```

名为 **top-level-string-array** 的变量返回：

```json
[
  "myDataDisk1",
  "myDataDisk2",
  "myDataDisk3",
  "myDataDisk4",
  "myDataDisk5"
]
```

## <a name="depend-on-resources-in-a-loop"></a>依迴圈中的資源而定

您可以透過使用 `dependsOn` 元素，讓某個資源在另一個資源之後才部署。 若要部署相依於迴圈中資源集合的資源時，請在 dependsOn 元素中提供複製迴圈的名稱。 下列範例示範如何在部署虛擬機器之前部署三個儲存體帳戶。 不會顯示完整的虛擬機器定義。 請注意，複製元素將名稱設定為 `storagecopy`，並將虛擬機器的 dependsOn 元素設定為 `storagecopy`。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 3
      }
    },
    {
      "apiVersion": "2015-06-15", 
      "type": "Microsoft.Compute/virtualMachines", 
      "name": "[concat('VM', uniqueString(resourceGroup().id))]",  
      "dependsOn": ["storagecopy"],
      ...
    }
  ],
  "outputs": {}
}
```

<a id="looping-on-a-nested-resource" />

## <a name="iteration-for-a-child-resource"></a>子系資源反覆項目
您無法為子資源使用複製迴圈。 若要為通常會在另一個資源內定義為巢狀的資源建立多個執行個體，您必須改為將該資源建立為最上層資源。 您可以透過類型和名稱屬性，定義和父資源之間的關聯性。

例如，假設您通常將資料集定義為 Data Factory 中的子資源。

```json
"resources": [
{
  "type": "Microsoft.DataFactory/datafactories",
  "name": "exampleDataFactory",
  ...
  "resources": [
    {
      "type": "datasets",
      "name": "exampleDataSet",
      "dependsOn": [
        "exampleDataFactory"
      ],
      ...
    }
  ]
```

若要建立多個資料集，請將它移出資料處理站。 資料集必須位於與資料處理站相同的等級，但它仍是資料處理站的子資源。 您可以透過類型和名稱屬性保留資料集與資料處理站之間的關聯性。 由於無法再從類型位於範本中的位置來推斷類型，您必須以此格式提供完整的類型︰`{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`。

若要与数据工厂的实例建立父/子关系，提供的数据集的名称应包含父资源名称。 使用格式︰`{parent-resource-name}/{child-resource-name}`。  

下列範例顯示實作：

```json
"resources": [
{
  "type": "Microsoft.DataFactory/datafactories",
  "name": "exampleDataFactory",
  ...
},
{
  "type": "Microsoft.DataFactory/datafactories/datasets",
  "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
  "dependsOn": [
    "exampleDataFactory"
  ],
  "copy": {
    "name": "datasetcopy",
    "count": "3"
  },
  ...
}]
```

## <a name="example-templates"></a>範本的範例

下列範例顯示為資源或屬性建立多個執行個體的常見案例。

|範本  |描述  |
|---------|---------|
|[複製儲存體](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |利用名稱中的索引編號來部署多個儲存體帳戶。 |
|[序列複製儲存體](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |一次一個部署數個儲存體帳戶。 名稱包含索引編號。 |
|[以陣列複製儲存體](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |部署數個儲存體帳戶。 名稱包含陣列中的值。 |
|[以可變的資料磁碟數目部署 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |利用虛擬機器部署數個資料磁碟。 |
|[複製變數](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |演示循环访问变量的不同方式。 |
|[多個安全性規則](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |將數個安全性規則部署至網路安全性群組。 從參數建構安全性規則。 如需參數，請參閱[多個 NSG 參數檔案](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json)。 |

## <a name="next-steps"></a>後續步驟

* 如須逐步瀏覽教學課程，請參閱[教學課程：使用 Resource Manager 範本建立多個資源執行個體](./resource-manager-tutorial-create-multiple-instances.md)。

* 若要了解範本區段的相關資訊，請參閱[編寫 Azure Resource Manager 範本](resource-group-authoring-templates.md)。
* 若要了解如何部署模板，请参阅 [使用 Azure Resource Manager 模板部署应用程序](resource-group-template-deploy.md)。

