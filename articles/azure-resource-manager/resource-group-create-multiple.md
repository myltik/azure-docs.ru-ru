---
title: "Развертывание нескольких экземпляров ресурсов Azure | Документация Майкрософт"
description: "Использование операции копирования и массивов в шаблоне диспетчера ресурсов Azure для выполнения нескольких итераций при развертывании ресурсов."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 94d95810-a87b-460f-8e82-c69d462ac3ca
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/12/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: a8e35456af8c9f2cf1bf9e9c364e33641f29a477
ms.contentlocale: ru-ru
ms.lasthandoff: 05/15/2017


---
# <a name="deploy-multiple-instances-of-a-resource-or-property-in-azure-resource-manager-templates"></a>Развертывание нескольких экземпляров ресурса или свойства в шаблонах Azure Resource Manager
В этом разделе показано, как выполнить итерацию в шаблоне диспетчера ресурсов Azure для создания нескольких экземпляров ресурса.

## <a name="resource-iteration"></a>Итерация ресурса
Для создания нескольких экземпляров типа ресурса добавьте элемент `copy` к типу ресурса. В элементе копирования укажите число итераций и имя для этого цикла. Значение count должно быть положительным целым числом не больше 800. Resource Manager создает ресурсы параллельно. Поэтому порядок, в котором они создаются, не гарантируется. Чтобы последовательно создать ресурсы с помощью итерации, ознакомьтесь с разделом [Последовательное копирование](#serial-copy). 

Для многократного создания ресурса используется следующий формат.

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

Обратите внимание, что имя каждого ресурса содержит функцию `copyIndex()`, которая возвращает текущую итерацию в цикле. `copyIndex()` отсчитывается, начиная с нуля. См. приведенный ниже пример.

```json
"name": "[concat('storage', copyIndex())]",
```

Он создает следующие имена:

* storage0;
* storage1;
* storage2.

Чтобы сместить значение индекса, можно передать нужное значение в функцию copyIndex(). Число выполняемых итераций по-прежнему указывается в элементе копирования, но значение copyIndex сдвигается на указанное значение. См. приведенный ниже пример.

```json
"name": "[concat('storage', copyIndex(1))]",
```

Он создает следующие имена:

* storage1;
* storage2;
* storage3.

Операция копирования удобна при работе с массивами, так как позволяет выполнить итерацию по каждому элементу в массиве. Используйте функцию `length` в массиве, чтобы указать число итераций, а также функцию `copyIndex` для получения текущего индекса в массиве. См. приведенный ниже пример.

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

Он создает следующие имена:

* storagecontoso;
* storagefabrikam;
* storagecoho.

## <a name="serial-copy"></a>Последовательное копирование

При использовании элемента copy для создания нескольких экземпляров типа ресурса по умолчанию Resource Manager развертывает эти экземпляры параллельно. Тем не менее можно настроить последовательное развертывание ресурсов. Например, при обновлении рабочей среды может потребоваться дифференцировать обновления, чтобы только определенное число элементов могло быть обновлено в любой момент времени.

Resource Manager предоставляет свойства элемента copy, позволяющие последовательно развернуть несколько экземпляров. В элементе copy задайте для `mode` значение **serial**, а для `batchSize` — число экземпляров, которые могут развертываться одновременно. В последовательном режиме Resource Manager создает зависимость от предыдущих экземпляров в цикле, поэтому он не начинает выполнение следующего пакета до завершения предыдущего.

```json
"copy": {
    "name": "iterator",
    "count": "[parameters('numberToDeploy')]",
    "mode": "serial",
    "batchSize": 2
},
```

Свойство mode также принимает значение **parallel**, которое является значением по умолчанию.

Чтобы проверить последовательное копирование без создания фактических ресурсов, используйте следующий шаблон, который развертывает пустые вложенные шаблоны.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberToDeploy": {
      "type": "int",
      "minValue": 2,
      "defaultValue": 5
    }
  },
  "resources": [
    {
      "apiVersion": "2015-01-01",
      "type": "Microsoft.Resources/deployments",
      "name": "[concat('loop-', copyIndex())]",
      "copy": {
        "name": "iterator",
        "count": "[parameters('numberToDeploy')]",
        "mode": "serial",
        "batchSize": 1
      },
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [],
          "outputs": {
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

В журнале развертывания обратите внимание на то, что вложенные развертывания обрабатываются последовательно.

![Последовательное развертывание](./media/resource-group-create-multiple/serial-copy.png)

В качестве более реалистичного сценария приведен следующий пример. Он одновременно развертывает два экземпляра виртуальной машины Linux из вложенного шаблона.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminUsername": {
            "type": "string",
            "metadata": {
                "description": "User name for the Virtual Machine."
            }
        },
        "adminPassword": {
            "type": "securestring",
            "metadata": {
                "description": "Password for the Virtual Machine."
            }
        },
        "dnsLabelPrefix": {
            "type": "string",
            "metadata": {
                "description": "Unique DNS Name for the Public IP used to access the Virtual Machine."
            }
        },
        "ubuntuOSVersion": {
            "type": "string",
            "defaultValue": "16.04.0-LTS",
            "allowedValues": [
                "12.04.5-LTS",
                "14.04.5-LTS",
                "15.10",
                "16.04.0-LTS"
            ],
            "metadata": {
                "description": "The Ubuntu version for the VM. This will pick a fully patched image of this given Ubuntu version."
            }
        }
    },
    "variables": {
        "templatelink": "https://raw.githubusercontent.com/rjmax/Build2017/master/Act1.TemplateEnhancements/Chapter03.LinuxVM.json"
    },
    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "[concat('nestedDeployment',copyIndex())]",
            "type": "Microsoft.Resources/deployments",
            "copy": {
                "name": "myCopySet",
                "count": 4,
                "mode": "serial",
                "batchSize": 2
            },
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "[variables('templatelink')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "adminUsername": {
                        "value": "[parameters('adminUsername')]"
                    },
                    "adminPassword": {
                        "value": "[parameters('adminPassword')]"
                    },
                    "dnsLabelPrefix": {
                        "value": "[parameters('dnsLabelPrefix')]"
                    },
                    "ubuntuOSVersion": {
                        "value": "[parameters('ubuntuOSVersion')]"
                    },
                    "index":{
                        "value": "[copyIndex()]"
                    }
                }
            }
        }
    ]
}
```

## <a name="depend-on-resources-in-a-loop"></a>Зависимость от ресурсов в цикле
С помощью элемента `dependsOn` можно определить последовательность развертывания ресурсов. Чтобы развернуть ресурс, который зависит от коллекции ресурсов в цикле, укажите имя цикла копирования в элементе dependsOn. В следующем примере показано, как развернуть три учетные записи хранения до развертывания виртуальной машины. Полное определение виртуальной машины не показано. Обратите внимание, что параметр name элемента copy имеет значение `storagecopy`, а элемент dependsOn для виртуальных машин имеет значение `storagecopy`.

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

## <a name="create-multiple-instances-of-a-child-resource"></a>Создание нескольких экземпляров дочернего ресурса
Нельзя включить дочерний ресурс в цикл копирования. Чтобы создать несколько экземпляров ресурса, которые определяются как вложенные в другой ресурс, необходимо создать его как ресурс верхнего уровня. Вы можете определить связь с родительским ресурсом с помощью свойств type и name.

Предположим, вы определяете набор данных как дочерний ресурс фабрики данных.

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
}]
```

Чтобы создать несколько экземпляров набора данных, переместите его за пределы фабрики данных. Набор данных должен находиться на том же уровне, что и фабрика данных, но при этом он должен быть дочерним ресурсом фабрики данных. Вы можете сохранить связь между набором данных и фабрикой данных с помощью свойств type и name. Так как тип нельзя определить по положению в шаблоне, укажите полное имя типа в следующем формате: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

Чтобы установить связь между родительским и дочерним ресурсами, укажите имя набора данных, которое включает имя родительского ресурса. Используйте формат `{parent-resource-name}/{child-resource-name}`.  

В следующем примере показано, как это сделать:

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
    } 
    ...
}]
```

## <a name="next-steps"></a>Дальнейшие действия
* Сведения о разделах шаблона см. в статье, посвященной [созданию шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
* Инструкции по развертыванию шаблонов см. в статье, посвященной [развертыванию приложения с помощью шаблона Azure Resource Manager](resource-group-template-deploy.md).


