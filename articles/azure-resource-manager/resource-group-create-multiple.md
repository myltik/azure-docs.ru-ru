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
ms.date: 04/17/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 8ecf7c058b90fd18e41fd4e1cbc29e22dfeb0883
ms.lasthandoff: 04/18/2017


---
# <a name="deploy-multiple-instances-of-resources-in-azure-resource-manager-templates"></a>Развертывание нескольких экземпляров ресурсов в шаблонах Azure Resource Manager
В этом разделе показано, как выполнить итерацию в шаблоне диспетчера ресурсов Azure для создания нескольких экземпляров ресурса.

## <a name="copy-and-copyindex"></a>copy и copyIndex
Для многократного создания ресурса используется следующий формат.

```json
"resources": [ 
  { 
      "name": "[concat('examplecopy-', copyIndex())", 
      "type": "Microsoft.Web/sites", 
      "location": "East US", 
      "apiVersion": "2015-08-01",
      "copy": { 
         "name": "websitescopy", 
         "count": "[parameters('count')]" 
      }, 
      "properties": {
          "serverFarmId": "hostingPlanName"
      } 
  } 
]
```

Обратите внимание, что в объекте copy указано число итераций.

```json
"copy": { 
    "name": "websitescopy", 
    "count": "[parameters('count')]" 
} 
```

Значение count должно быть положительным целым числом не больше 800.

Обратите внимание, что имя каждого ресурса содержит функцию `copyIndex()`, которая возвращает текущую итерацию в цикле.

```json
"name": "[concat('examplecopy-', copyIndex())]",
```

В случае развертывания трех веб-сайтов будут называться:

* examplecopy-0
* examplecopy-1
* examplecopy-2

Чтобы сместить значение индекса, можно передать нужное значение в функцию copyIndex(), например `copyIndex(1)`. Число выполняемых итераций по-прежнему указывается в элементе копирования, но значение copyIndex сдвигается на указанное значение. Таким образом, если использовать шаблон, аналогичный приведенному в предыдущем примере, но указать copyIndex(1), будут развернуты три следующих веб-сайта:

* examplecopy-1
* examplecopy-2
* examplecopy-3

Resource Manager создает ресурсы параллельно. Поэтому порядок, в котором они создаются, не гарантируется. Чтобы последовательно создать ресурсы, изучите раздел [Примеры расширения функциональных возможностей шаблонов Azure Resource Manager. Последовательные циклы](resource-manager-sequential-loop.md). 

Копировать объекты можно только на ресурсы верхнего уровня. Эту операцию нельзя выполнять со свойством в типе ресурсов или дочерним ресурсом. В следующем примере псевдокода показано, где можно применять копирование:

```json
"resources": [
  {
    "type": "{provider-namespace-and-type}",
    "name": "parentResource",
    "copy": {  
      /* Yes, copy can be applied here */
    },
    "properties": {
      "exampleProperty": {
        /* No, copy cannot be applied here */
      }
    },
    "resources": [
      {
        "type": "{provider-type}",
        "name": "childResource",
        /* No, copy cannot be applied here. The resource must be promoted to top-level. */ 
      }
    ]
  }
] 
```

Сведения об итеративном создании дочерних ресурсов см. в разделе [Создание нескольких экземпляров дочернего ресурса](#create-multiple-instances-of-a-child-resource).

Хотя копирование свойства не поддерживается, это свойство по-прежнему будет частью итераций ресурса, который его содержит. Поэтому вы можете использовать функцию copyIndex() в свойстве, чтобы указать значения. Создание нескольких значений свойства рассматривается в разделе [Создание нескольких экземпляров свойства для типа ресурса](resource-manager-property-copy.md).

## <a name="use-copy-with-array"></a>Использование операции копирования с массивом
Операция копирования удобна при работе с массивами, так как позволяет выполнить итерацию по каждому элементу в массиве. Чтобы развернуть три веб-сайта:

* examplecopy-Contoso
* examplecopy-Fabrikam
* examplecopy-Coho

используйте следующий шаблон.

```json
"parameters": { 
  "org": { 
     "type": "array", 
     "defaultValue": [ 
         "Contoso", 
         "Fabrikam", 
         "Coho" 
      ] 
  }
}, 
"resources": [ 
  { 
      "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]", 
      "type": "Microsoft.Web/sites", 
      "location": "East US", 
      "apiVersion": "2015-08-01",
      "copy": { 
         "name": "websitescopy", 
         "count": "[length(parameters('org'))]" 
      }, 
      "properties": {
          "serverFarmId": "hostingPlanName"
      } 
  } 
]
```

Обратите внимание на то, что для указания количества используется функция `length`. В качестве параметра функции length используется массив.

```json
"copy": {
    "name": "websitescopy",
    "count": "[length(parameters('siteNames'))]"
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
            "apiVersion": "2015-06-15",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id), copyIndex())]",
            "location": "[resourceGroup().location]",
            "properties": {
                "accountType": "Standard_LRS"
            },
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
* Чтобы последовательно создать ресурсы, изучите раздел [Примеры расширения функциональных возможностей шаблонов Azure Resource Manager. Последовательные циклы](resource-manager-sequential-loop.md).
* Инструкции по развертыванию шаблонов см. в статье, посвященной [развертыванию приложения с помощью шаблона Azure Resource Manager](resource-group-template-deploy.md).


