---
title: "Несколько копий свойства ресурса Azure | Документация Майкрософт"
description: "В шаблоне Azure Resource Manager можно создать несколько экземпляров свойства для типа ресурса. Для этого сценария вместо цикла копирования необходимо использовать функцию take."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/17/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: da79ff58e892bde5a35456992105de9bdbe36364
ms.lasthandoff: 04/18/2017


---
# <a name="create-multiple-instances-of-property-on-resource-type"></a>Создание нескольких экземпляров свойства для типа ресурса
Операцию [копирования](resource-group-create-multiple.md) можно применить только к типам ресурсов, а не к свойствам в типе ресурса. Это требование может быть проблемой, если вам нужно создать несколько экземпляров ресурса, который является частью другого ресурса. Создание нескольких дисков данных для виртуальной машины — часто встречающийся сценарий. Копирование дисков данных не поддерживается, так как dataDisks — это свойство виртуальной машины, а не собственный тип ресурса. Вместо этого необходимо создать массив с требуемым числом дисков данных и передать фактическое число создаваемых дисков данных. В определении виртуальной машины используйте функцию `take` для получения количества только тех элементов, которые требуется получить из массива.

Полный пример этой схемы показан в шаблоне [Создание виртуальной машины с динамическим выбором дисков данных](https://azure.microsoft.com/documentation/templates/201-vm-dynamic-data-disks-selection/) .

Соответствующие разделы шаблона развертывания показаны ниже. Часть шаблона удалена, чтобы сосредоточить ваше внимание на разделах, участвующих в процессе динамического создания нескольких дисков данных. 

## <a name="define-template-with-variable-for-the-property"></a>Определение шаблона с переменной для свойства

Чтобы создать виртуальную машину с несколькими дисками данных, добавьте переменную массива, которая определяет больше дисков данных, чем требуется на данный момент. В параметре `numDataDisks` передайте фактическое количество создаваемых дисков данных. Используйте `take`, чтобы указать точное число элементов в массиве для виртуальной машины.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
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
      ... /* not all elements shown */
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
    {
      "type": "Microsoft.Compute/virtualMachines",
      "properties": {
        "storageProfile": {
          "dataDisks": "[take(variables('diskArray'),parameters('numDataDisks'))]"
        },
        ...
      }
      ...
    }
  ]
}
```

## <a name="create-multiple-virtual-machines-with-multiple-data-disks"></a>Создание нескольких виртуальных машин с несколькими дисками данных

Если необходимо создать несколько экземпляров ресурса с переменным числом элементов для свойства, можно совместно использовать функцию **take** и элемент **copy**. Предположим, вам нужно создать несколько виртуальных машин, каждая из которых имеет разное число дисков данных. 

Чтобы каждому диску данных присвоить имя, определяющее соответствующую виртуальную машину, поместите массив дисков данных в отдельный шаблон. Включите параметры имени виртуальной машины и числа дисков данных для возврата. В разделе с выходными данными буде возвращено число указанных элементов.

```json
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

```json
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

```json
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

```json
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

## <a name="next-steps"></a>Дальнейшие действия
* Вводные сведения о создании нескольких экземпляров ресурса см. в разделе [Развертывание нескольких экземпляров ресурсов в шаблонах Azure Resource Manager](resource-group-create-multiple.md).
* Чтобы последовательно создать ресурсы, изучите раздел [Примеры расширения функциональных возможностей шаблонов Azure Resource Manager. Последовательные циклы](resource-manager-sequential-loop.md).
* Инструкции по развертыванию шаблонов см. в статье, посвященной [развертыванию приложения с помощью шаблона Azure Resource Manager](resource-group-template-deploy.md).


