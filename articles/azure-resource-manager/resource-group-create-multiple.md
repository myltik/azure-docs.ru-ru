---
title: "Развертывание нескольких экземпляров ресурсов | Документация Майкрософт"
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
ms.date: 11/02/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 28bda592a685de4b2b938da21c3f3aa0a60e632d
ms.openlocfilehash: 241a22429e8e0fbd2625292890baf4b2c3d77d81


---
# <a name="create-multiple-instances-of-resources-in-azure-resource-manager"></a>Создание нескольких экземпляров ресурсов в диспетчере ресурсов Azure
В этом разделе показано, как выполнить итерацию в шаблоне диспетчера ресурсов Azure для создания нескольких экземпляров ресурса.

## <a name="copy-copyindex-and-length"></a>copy, copyIndex и length
Чтобы создать несколько экземпляров ресурса, можно определить объект **copy** , указывающий количество итераций. Объект copy имеет следующий формат:

    "copy": { 
        "name": "websitescopy", 
        "count": "[parameters('count')]" 
    } 

Текущее значение итерации можно посмотреть с помощью функции **copyIndex()**. В следующем примере copyIndex используется с функцией concat для создания имени.

    [concat('examplecopy-', copyIndex())]

При создании нескольких ресурсов из массива значений можно использовать функцию **length** , чтобы задать их количество. В качестве параметра функции length используется массив.

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

Копировать объекты можно только на ресурсы верхнего уровня. Эту операцию нельзя выполнять со свойством в типе ресурсов или дочерним ресурсом. В этой статье объясняется, как указать несколько элементов для свойства и создать несколько экземпляров дочернего ресурса. В следующем примере псевдокода показано, где можно применять копирование:

    "resources": [
      {
        "type": "{provider-namespace-and-type}",
        "name": "parentResource",
        "copy": {  
          /* yes, copy can be applied here */
        },
        "properties": {
          "exampleProperty": {
            /* no, copy cannot be applied here */
          }
        },
        "resources": [
          {
            "type": "{provider-type}",
            "name": "childResource",
            /* copy can be applied if resource is promoted to top level */ 
          }
        ]
      }
    ] 

Хотя **копирование** свойства не поддерживается, это свойство по-прежнему будет частью итераций ресурса, который его содержит. Поэтому вы можете использовать функцию **copyIndex()** в свойстве, чтобы указать значения.

Итерация свойства в ресурсе может понадобиться в нескольких случаях. Например, вам нужно указать несколько дисков данных для виртуальной машины. Чтобы узнать, как выполнить итерацию свойства, см. раздел [Создание нескольких экземпляров при невозможности выполнить копирование](#create-multiple-instances-when-copy-wont-work). 

Сведения о работе с дочерними ресурсами см. в разделе [Создание нескольких экземпляров дочернего ресурса](#create-multiple-instances-of-a-child-resource).

## <a name="use-index-value-in-name"></a>Использование значения индекса в имени
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

## <a name="offset-index-value"></a>Смещение значения индекса
В предыдущем примере значение индекса изменяется от 0 до 2. Чтобы сместить значение индекса, можно передать нужное значение в функцию **copyIndex()**, например **copyIndex(1)**. Число выполняемых итераций по-прежнему указывается в элементе копирования, но значение copyIndex сдвигается на указанное значение. Таким образом, если использовать шаблон, аналогичный приведенному в предыдущем примере, но указать **copyIndex(1)** , будут развернуты три следующих веб-сайта:

* examplecopy-1
* examplecopy-2
* examplecopy-3

## <a name="use-copy-with-array"></a>Использование операции копирования с массивом
Операция копирования удобна при работе с массивами, так как позволяет выполнить итерацию по каждому элементу в массиве. Чтобы развернуть три веб-сайта:

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

Разумеется, вы можете указать число копий, отличное от длины массива. Например, вы могли создать массив с большим количеством значений, а затем передать значение параметра, указывающее, сколько элементов массива следует развернуть. В этом случае задайте число копий, как показано в первом примере. 

## <a name="depend-on-resources-in-a-loop"></a>Зависимость от ресурсов в цикле
С помощью элемента **dependsOn** можно определить последовательность развертывания ресурсов. Чтобы развернуть ресурс, который зависит от коллекции ресурсов в цикле, укажите имя цикла копирования в элементе **dependsOn**. В следующем примере показано, как развернуть три учетные записи хранения до развертывания виртуальной машины. Полное определение виртуальной машины не показано. Обратите внимание, что параметр **name** элемента копирования определяется в **storagecopy** и элемент **dependsOn** для виртуальных машин также определяется в **storagecopy**.

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

## <a name="create-multiple-instances-of-a-child-resource"></a>Создание нескольких экземпляров дочернего ресурса
Нельзя включить дочерний ресурс в цикл копирования. Чтобы создать несколько экземпляров ресурса, которые определяются как вложенные в другой ресурс, необходимо создать его как ресурс верхнего уровня. Вы можете определить связь с родительским ресурсом с помощью свойств **type** и **name**.

Предположим, вы определяете набор данных как дочерний ресурс фабрики данных.

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

Чтобы создать несколько экземпляров набора данных, переместите его за пределы фабрики данных. Набор данных должен находиться на том же уровне, что и фабрика данных, но при этом он должен быть дочерним ресурсом фабрики данных. Вы можете сохранить связь между набором данных и фабрикой данных с помощью свойств **type** и **name**. Так как тип нельзя определить по положению в шаблоне, укажите полное имя типа в следующем формате:

 **{пространство_имен_поставщика_ресурсов}/{тип_родительского_ресурса}/{тип_дочернего_ресурса}** 

Чтобы установить связь между родительским и дочерним ресурсами, укажите имя набора данных, которое включает имя родительского ресурса. Используйте следующий формат имени:

**{имя_родительского_ресурса}/{имя_дочернего_ресурса}**.  

В следующем примере показано, как это сделать:

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

## <a name="create-multiple-instances-when-copy-wont-work"></a>Создание нескольких экземпляров при невозможности выполнить копирование
Операцию **копирования** можно применить только к типам ресурсов, а не к свойствам в типе ресурса. Это требование может быть проблемой, если вам нужно создать несколько экземпляров ресурса, который является частью другого ресурса. Создание нескольких дисков данных для виртуальной машины — часто встречающийся сценарий. **Копирование** дисков данных не поддерживается, так как **dataDisks** — это свойство виртуальной машины, а не собственный тип ресурса. Вместо этого необходимо создать массив с требуемым числом дисков данных и передать фактическое число создаваемых дисков данных. В определении виртуальной машины используйте функцию **take** для получения количества только тех элементов, которые требуется получить из массива.

Полный пример этой схемы показан в шаблоне [Создание виртуальной машины с динамическим выбором дисков данных](https://azure.microsoft.com/documentation/templates/201-vm-dynamic-data-disks-selection/) .

Соответствующие разделы шаблона развертывания показаны в примере ниже. Большая часть шаблона удалена, чтобы сфокусировать ваше внимание на разделах, включенных в процесс динамического создания нескольких дисков данных. Обратите внимание на параметр **numDataDisks** , в который можно передать количество создаваемых дисков. 

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

Если необходимо создать несколько экземпляров ресурса с переменным числом элементов для свойства, можно совместно использовать функцию **take** и элемент **copy**. Предположим, вам нужно создать несколько виртуальных машин, каждая из которых имеет разное число дисков данных. Чтобы каждому диску данных присвоить имя, определяющее соответствующую виртуальную машину, поместите массив дисков данных в отдельный шаблон. Включите параметры имени виртуальной машины и числа дисков данных для возврата. В разделе с выходными данными буде возвращено число указанных элементов.

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    },
    "numDataDisks": {
      "type": "int",
      "maxValue": 16,
      "metadata": {
        "description": "This parameter allows the user to select the number of disks they want"
      }
    }
  },
  "variables": {
    "diskArray": [
      {
        "name": "[concat(parameters('vmName'), '-datadisk1')]",
        "vhd": {
          "uri": "[concat('http://', parameters('storageAccountName'),'.blob.core.windows.net/vhds/', parameters('vmName'), '-datadisk1.vhd')]"
        },
        ...
      },
      ...
    ],
  },
  "resources": [
  ],
  "outputs": {
    "result": {
      "type": "array",
      "value": "[take(variables('diskArray'),parameters('numDataDisks'))]"
    }
  }
}
``` 

Укажите в родительском шаблоне число виртуальных машин и массив для числа дисков данных для каждой виртуальной машины.

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
    "numberOfInstances": {
      "type": "int",
      "defaultValue": 2,
      "metadata": {
        "description": "Number of VMs to deploy"
      }
    },
    "numberOfDataDisksPerVM": {
      "type": "array",
      "defaultValue": [1,2]
    }
  },
```

Разверните в разделе ресурсов несколько экземпляров шаблона, который определяет диски данных. 

```
{
  "apiVersion": "2016-09-01",
  "name": "[concat('nested-', copyIndex())]",
  "type": "Microsoft.Resources/deployments",
  "copy": {
    "name": "deploycopy",
    "count": "[parameters('numberOfInstances')]"
  },
  "properties": {
    "mode": "incremental",
    "templateLink": {
      "uri": "{data-disk-template-uri}",
      "contentVersion": "1.0.0.0"
    },
    "parameters": {
      "vmName": { "value": "[concat('myvm', copyIndex())]" },
      "storageAccountName": { "value": "[variables('storageAccountName')]" },
      "numDataDisks": { "value": "[parameters('numberOfDataDisksPerVM')[copyIndex()]]" }
    }
  }
},
```

Разверните в разделе ресурсов несколько экземпляров виртуальной машины. Для дисков данных укажите ссылку на вложенное развертывание, содержащее правильное число дисков данных и их правильные имена.

```
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[concat('myvm', copyIndex())]",
  "copy": {
    "name": "virtualMachineLoop",
      "count": "[parameters('numberOfInstances')]"
  },
  "properties": {
    "storageProfile": {
      ...
      "dataDisks": "[reference(concat('nested-', copyIndex())).outputs.result.value]"
    },
    ...
  },
  ...
}
```

## <a name="return-values-from-a-loop"></a>Значения, возвращаемые из цикла
Хотя создавать сразу несколько экземпляров типа ресурса удобно, возвращение значений из цикла может быть сложной задачей. Чтобы сохранить и вернуть значения, можно выполнить **копирование** с использованием вложенного шаблона и цикл обхода для массива, который содержит все значения для возврата. Предположим, вам нужно создать несколько учетных записей хранения и вернуть основную конечную точку для каждого из них. 

Сначала создайте вложенный шаблон, который создает учетную запись хранения. Обратите внимание, что он принимает параметр массива для URI большого двоичного объекта. Используйте этот параметр, чтобы обойти все значения из предыдущего развертывания. Выходными данными шаблона является массив, который объединяет новый URI большого двоичного объекта с предыдущими URI.

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "indexValue": {
      "type":"int"
    },
    "blobURIs": {
        "type": "array",
      "defaultValue": []
    }
  },
    "variables": {
    "storageName": "[concat('storage', uniqueString(resourceGroup().id), parameters('indexValue'))]"
  },
    "resources": [
    {
        "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "[resourceGroup().location]",
      "sku": {
          "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {  
      }
    }
    ],
    "outputs": {
      "result": {
        "type": "array",
      "value": "[concat(parameters('blobURIs'),split(reference(variables('storageName')).primaryEndpoints.blob, ','))]"
    }
  }
}
```

Теперь создайте родительский шаблон, который включает один статический экземпляр вложенного шаблона и повторяет оставшиеся экземпляры вложенного шаблона. Для каждого экземпляра циклического развертывания передайте массив, который является результатом предыдущего развертывания.

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberofStorage": { "type": "int", "minValue": 2 }
  },
  "resources": [
    {
      "apiVersion": "2016-09-01",
      "name": "nestedTemplate0",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "{storage-template-uri}",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "indexValue": {"value": 0}
        }
      }
    },
    {
      "apiVersion": "2016-09-01",
      "name": "[concat('nestedTemplate', copyIndex(1))]",
      "type": "Microsoft.Resources/deployments",
      "copy": {
        "name": "storagecopy",
        "count": "[sub(parameters('numberofStorage'), 1)]"
      },
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "{storage-template-uri}",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "indexValue": {"value": "[copyIndex(1)]"},
          "blobURIs": {"value": "[reference(concat('nestedTemplate', copyIndex())).outputs.result.value]"}
        }
      }
    }
  ],
  "outputs": {
    "result": {
      "type": "object",
      "value": "[reference(concat('nestedTemplate', sub(parameters('numberofStorage'), 1))).outputs.result]"
    }
  }
}
```

## <a name="next-steps"></a>Дальнейшие действия
* Сведения о разделах шаблона см. в статье, посвященной [созданию шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
* Список функций, которые можно использовать в шаблоне, см. в статье, описывающей [функции шаблонов Azure Resource Manager](resource-group-template-functions.md).
* Инструкции по развертыванию шаблонов см. в статье, посвященной [развертыванию приложения с помощью шаблона Azure Resource Manager](resource-group-template-deploy.md).




<!--HONumber=Dec16_HO3-->


