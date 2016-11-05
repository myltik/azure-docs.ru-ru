---
title: Развертывание нескольких экземпляров ресурсов | Microsoft Docs
description: Использование операции копирования и массивов в шаблоне диспетчера ресурсов Azure для выполнения нескольких итераций при развертывании ресурсов.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: wpickett
editor: ''

ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/30/2016
ms.author: tomfitz

---
# Создание нескольких экземпляров ресурсов в диспетчере ресурсов Azure
В этом разделе показано, как выполнить итерацию в шаблоне диспетчера ресурсов Azure для создания нескольких экземпляров ресурса.

## copy, copyIndex и length
Чтобы создать несколько экземпляров ресурса, можно определить объект **copy**, указывающий количество итераций. Объект copy имеет следующий формат:

    "copy": { 
        "name": "websitescopy", 
        "count": "[parameters('count')]" 
    } 

Вы можете получить доступ к текущему значению итерации с помощью функции **copyIndex()**, как показано ниже с использованием функции concat.

    [concat('examplecopy-', copyIndex())]

При создании нескольких ресурсов из массива значений можно использовать функцию **length**, чтобы задать их количество. В качестве параметра функции length используется массив.

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

## Использование значения индекса в имени
Вы можете использовать операцию копирования для создания нескольких экземпляров ресурса, каждому из которых присвоено уникальное имя на основе увеличивающегося индекса. Например, вам может потребоваться добавить уникальный номер в конец имени каждого развертываемого ресурса. Чтобы развернуть три веб-сайта:

* examplecopy-0
* examplecopy-1
* examplecopy-2

используйте следующий шаблон.

    "parameters": { 
      "count": { 
        "type": "int", 
        "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', copyIndex())]", 
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

## Смещение значения индекса
В предыдущем примере можно заметить, что значение индекса изменяется от нуля до 2. Для смещения значения индекса можно передать значение в функцию **copyIndex()**, например **copyIndex(1)**. Число выполняемых итераций по-прежнему указывается в элементе копирования, но значение copyIndex сдвигается на указанное значение. Таким образом, если использовать шаблон, аналогичный приведенному в предыдущем примере, но указать **copyIndex(1)**, будут развернуты три следующих веб-сайта:

* examplecopy-1
* examplecopy-2
* examplecopy-3

## Использование операции копирования с массивом
Эта операция копирования особенно удобна при работе с массивами, поскольку можно выполнить итерацию по каждому элементу в массиве. Чтобы развернуть три веб-сайта:

* examplecopy-Contoso
* examplecopy-Fabrikam
* examplecopy-Coho

используйте следующий шаблон.

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

Конечно, вы указали число копий, отличное от длины массива. Например, вы могли создать массив с большим количеством значений, а затем передать значение параметра, указывающее, сколько элементов массива следует развернуть. В этом случае задайте число копий, как показано в первом примере.

## Зависимость от ресурсов в цикле
С помощью элемента **dependsOn** можно указать, что один ресурс должен развертываться после другого. Если необходимо развернуть ресурс, который зависит от коллекции ресурсов в цикле, можно указать имя цикла копирования в элементе **dependsOn**. В следующем примере показано, как развернуть 3 учетные записи хранения до развертывания виртуальной машины. Полное определение виртуальной машины не показано. Обратите внимание, что **имя** элемента копии установлено в **storagecopy** и элемент **dependsOn** для виртуальных машин также установлен в **storagecopy**.

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

## Выполнение цикла с вложенным ресурсом
Включить вложенный ресурс в цикл копирования нельзя. Если требуется создать несколько экземпляров ресурса, который обычно определяется как вложенный в другой ресурс, необходимо сначала создать его как ресурс верхнего уровня, а затем задать его отношения с родительским ресурсом с помощью свойств **type** и **name**.

Предположим, например, что набор данных определяется в качестве вложенного ресурса фабрики данных.

    "parameters": {
        "dataFactoryName": {
            "type": "string"
         },
         "dataSetName": {
            "type": "string"
         }
    },
    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "[parameters('dataFactoryName')]",
        ...
        "resources": [
        {
            "type": "datasets",
            "name": "[parameters('dataSetName')]",
            "dependsOn": [
                "[parameters('dataFactoryName')]"
            ],
            ...
        }
    }]

Чтобы создать несколько экземпляров набора, необходимо внести изменения в шаблон, как показано ниже. Обратите внимание на то, что полный тип и имя содержат имя фабрики.

    "parameters": {
        "dataFactoryName": {
            "type": "string"
         },
         "dataSetName": {
            "type": "array"
         }
    },
    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "[parameters('dataFactoryName')]",
        ...
    },
    {
        "type": "Microsoft.DataFactory/datafactories/datasets",
        "name": "[concat(parameters('dataFactoryName'), '/', parameters('dataSetName')[copyIndex()])]",
        "dependsOn": [
            "[parameters('dataFactoryName')]"
        ],
        "copy": { 
            "name": "datasetcopy", 
            "count": "[length(parameters('dataSetName'))]" 
        } 
        ...
    }]

## Создание нескольких экземпляров при невозможности выполнить копирование
Операцию **копирования** можно применить только к типам ресурсов, а не к свойствам в типе ресурса. Это может быть проблемой, когда требуется создать несколько экземпляров того, что является частью ресурса. Создание нескольких дисков данных для виртуальной машины — часто встречающийся сценарий. Использовать **копирование** с дисками данных нельзя из-за того, что **dataDisks** — это свойство виртуальной машины, а не собственный тип ресурса. Вместо этого необходимо создать массив с требуемым количеством дисков данных и передать фактическое количество создаваемых дисков данных. В определении виртуальной машины используйте функцию **take** для получения количества только тех элементов, которые требуется получить из массива.

Полный пример этой схемы показан в шаблоне [Создание виртуальной машины с динамическим выбором дисков данных](https://azure.microsoft.com/documentation/templates/201-vm-dynamic-data-disks-selection/).

Соответствующие разделы шаблона развертывания показаны ниже. Значительная часть шаблона была удалена, чтобы выделить разделы, участвующие в динамическом создании нескольких дисков данных. Обратите внимание на параметр **numDataDisks**, в который можно передать количество создаваемых дисков.

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
    "numDataDisks": {
      "type": "int",
      "maxValue": 64,
      "metadata": {
        "description": "This parameter allows you to select the number of disks you want"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'dynamicdisk')]",
    "sizeOfDataDisksInGB": 100,
    "diskCaching": "ReadWrite",
    "diskArray": [
      {
        "name": "datadisk1",
        "lun": 0,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk1.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk2",
        "lun": 1,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk2.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk3",
        "lun": 2,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk3.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk4",
        "lun": 3,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk4.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      ...
      {
        "name": "datadisk63",
        "lun": 62,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk63.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk64",
        "lun": 63,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk64.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      }
    ]
  },
  "resources": [
    ...
    {
      "type": "Microsoft.Compute/virtualMachines",
      "properties": {
        ...
        "storageProfile": {
          ...
          "dataDisks": "[take(variables('diskArray'),parameters('numDataDisks'))]"
        },
        ...
      }
      ...
    }
  ]
}
```


## Дальнейшие действия
* Сведения о разделах шаблона см. в статье [Создание шаблонов диспетчера ресурсов Azure](resource-group-authoring-templates.md).
* Список функций, которые можно использовать в шаблоне, см. в статье [Функции шаблонов в диспетчере ресурсов Azure](resource-group-template-functions.md).
* Инструкции по развертыванию шаблонов см. в статье [Развертывание приложения с помощью шаблона диспетчера ресурсов Azure](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0706_2016-->