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
ms.date: 05/11/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: e98fa067c0ed385fe20f66645311c9fd51cd6456
ms.contentlocale: ru-ru
ms.lasthandoff: 05/11/2017


---
# <a name="deploy-multiple-instances-of-a-resource-or-property-in-azure-resource-manager-templates"></a>Развертывание нескольких экземпляров ресурса или свойства в шаблонах Azure Resource Manager
В этом разделе показано, как выполнить итерацию в шаблоне диспетчера ресурсов Azure для создания нескольких экземпляров ресурса.

## <a name="resource-iteration"></a>Итерация ресурса
Для создания нескольких экземпляров типа ресурса добавьте элемент `copy` к типу ресурса. В элементе копирования укажите число итераций и имя для этого цикла. Значение count должно быть положительным целым числом не больше 800. Resource Manager создает ресурсы параллельно. Поэтому порядок, в котором они создаются, не гарантируется. Чтобы последовательно создать ресурсы, изучите раздел [Примеры расширения функциональных возможностей шаблонов Azure Resource Manager. Последовательные циклы](resource-manager-sequential-loop.md). 

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
* Чтобы последовательно создать ресурсы, изучите раздел [Примеры расширения функциональных возможностей шаблонов Azure Resource Manager. Последовательные циклы](resource-manager-sequential-loop.md).
* Инструкции по развертыванию шаблонов см. в статье, посвященной [развертыванию приложения с помощью шаблона Azure Resource Manager](resource-group-template-deploy.md).


