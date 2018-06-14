---
title: Переменные шаблона Azure Resource Manager | Документация Майкрософт
description: В этой статье объясняется, как определить переменные шаблонов Azure Resource Manager с помощью декларативного синтаксиса JSON.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2017
ms.author: tomfitz
ms.openlocfilehash: 08728a3c0b4d4578939004e2d1b1ee2d30a682ab
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359294"
---
# <a name="variables-section-of-azure-resource-manager-templates"></a>Раздел Variables в шаблонах Azure Resource Manager
В разделе переменных вы создаете значения, которые можно использовать в разных частях шаблона. Переменные определять не обязательно, однако они часто упрощают шаблон, снижая число сложных выражений.

## <a name="define-and-use-a-variable"></a>Определение и применение переменной

В приведенном ниже примере показано определение переменной. В нем создается строковое значение имени учетной записи хранения. Чтобы получить значение параметра и сцепить его в уникальную строку, используется несколько функций шаблонов.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Переменная используется при определении ресурса.

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    ...
```

## <a name="available-definitions"></a>Доступные определения

В предыдущем примере показан один из способов определить переменную. Также можно использовать любое из следующих определений:

```json
"variables": {
    "<variable-name>": "<variable-value>",
    "<variable-name>": { 
        <variable-complex-type-value> 
    },
    "<variable-object-name>": {
        "copy": [
            {
                "name": "<name-of-array-property>",
                "count": <number-of-iterations>,
                "input": {
                    <properties-to-repeat>
                }
            }
        ]
    },
    "copy": [
        {
            "name": "<variable-array-name>",
            "count": <number-of-iterations>,
            "input": {
                <properties-to-repeat>
            }
        }
    ]
}
```

## <a name="configuration-variables"></a>Переменные конфигурации

С помощью сложных типов JSON можно определить связанные значения для среды. 

```json
"variables": {
    "environmentSettings": {
        "test": {
            "instanceSize": "Small",
            "instanceCount": 1
        },
        "prod": {
            "instanceSize": "Large",
            "instanceCount": 4
        }
    }
},
```

В параметрах следует создать значение, указывающее, какие значения конфигурации будут использоваться.

```json
"parameters": {
    "environmentName": {
        "type": "string",
        "allowedValues": [
          "test",
          "prod"
        ]
    }
},
```

Получить текущие настройки можно следующим образом:

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

## <a name="use-copy-element-in-variable-definition"></a>Использование элемента copy в определении переменной

Можно использовать синтаксис **copy**, чтобы создать переменную с массивом из нескольких элементов. Укажите счетчик для числа элементов. Каждый элемент содержит свойства в объекте **input**. Можно использовать элемент copy в переменной или для создания переменной. Если вы определяете переменную и используете в ней элемент **copy**, создайте объект со свойством массива. Если вы используете элемент **copy** на верхнем уровне и определяете в нем одну или нескольких переменных, создайте один или несколько массивов. В следующем примере показаны оба подхода.

```json
"variables": {
    "disk-array-on-object": {
        "copy": [
            {
                "name": "disks",
                "count": 3,
                "input": {
                    "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
                    "diskSizeGB": "1",
                    "diskIndex": "[copyIndex('disks')]"
                }
            }
        ]
    },
    "copy": [
        {
            "name": "disks-top-level-array",
            "count": 3,
            "input": {
                "name": "[concat('myDataDisk', copyIndex('disks-top-level-array', 1))]",
                "diskSizeGB": "1",
                "diskIndex": "[copyIndex('disks-top-level-array')]"
            }
        }
    ]
},
```

Переменная **disk-array-on-object** содержит следующий объект с массивом **disks**:

```json
{
  "disks": [
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
    }
  ]
}
```

Переменная **disks-top-level-array** содержит следующий массив:

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
  }
]
```

При создании переменных с помощью синтаксиса copy можно указать несколько объектов. В примере ниже в качестве переменных определены два массива. Один называется **disks-top-level-array** и включает пять элементов. Второй называется **a-different-array** и содержит три элемента.

```json
"variables": {
    "copy": [
        {
            "name": "disks-top-level-array",
            "count": 5,
            "input": {
                "name": "[concat('oneDataDisk', copyIndex('disks-top-level-array', 1))]",
                "diskSizeGB": "1",
                "diskIndex": "[copyIndex('disks-top-level-array')]"
            }
        },
        {
            "name": "a-different-array",
            "count": 3,
            "input": {
                "name": "[concat('twoDataDisk', copyIndex('a-different-array', 1))]",
                "diskSizeGB": "1",
                "diskIndex": "[copyIndex('a-different-array')]"
            }
        }
    ]
},
```

Такой подход полезен, когда необходимо принимать значения параметров и обеспечить их правильный формат для значения шаблона. В следующем примере значения параметров форматируются для использования при определении правил безопасности:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "securityRules": {
      "type": "array"
    }
  },
  "variables": {
    "copy": [
      {
        "name": "securityRules",
        "count": "[length(parameters('securityRules'))]",
        "input": {
          "name": "[parameters('securityRules')[copyIndex('securityRules')].name]",
          "properties": {
            "description": "[parameters('securityRules')[copyIndex('securityRules')].description]",
            "priority": "[parameters('securityRules')[copyIndex('securityRules')].priority]",
            "protocol": "[parameters('securityRules')[copyIndex('securityRules')].protocol]",
            "sourcePortRange": "[parameters('securityRules')[copyIndex('securityRules')].sourcePortRange]",
            "destinationPortRange": "[parameters('securityRules')[copyIndex('securityRules')].destinationPortRange]",
            "sourceAddressPrefix": "[parameters('securityRules')[copyIndex('securityRules')].sourceAddressPrefix]",
            "destinationAddressPrefix": "[parameters('securityRules')[copyIndex('securityRules')].destinationAddressPrefix]",
            "access": "[parameters('securityRules')[copyIndex('securityRules')].access]",
            "direction": "[parameters('securityRules')[copyIndex('securityRules')].direction]"
          }
        }
      }
    ]
  },
  "resources": [
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "NSG1",
      "location": "[resourceGroup().location]",
      "properties": {
        "securityRules": "[variables('securityRules')]"
      }
    }
  ],
  "outputs": {}
}
```

## <a name="recommendations"></a>Рекомендации
Ниже приведены некоторые рекомендации по работе с переменными.

* Применяйте переменные для значений, которые необходимо использовать в шаблоне более одного раза. Если значение используется только один раз, жестко задайте его. Это облегчит чтение шаблона.
* Нельзя использовать функцию [reference](resource-group-template-functions-resource.md#reference) в разделе **переменных** шаблона. Функция **reference** получает свое значение из состояния среды выполнения ресурса, а переменные разрешаются при начальной обработке шаблона. Сформируйте значения, которым требуется функция **reference**, непосредственно в разделе **resources** или **outputs** шаблона.
* Добавьте переменные для имен ресурсов, которые должны быть уникальными.

## <a name="example-templates"></a>Образцы шаблонов

Эти примеры шаблонов демонстрируют разные сценарии использования переменных. Разверните их и изучите, как обрабатываются переменные в различных сценариях. 

|Шаблон  |ОПИСАНИЕ  |
|---------|---------|
| [variable definitions](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Демонстрирует различные типы переменных. Этот шаблон не развертывает ресурсы. При его помощи только создаются и возвращаются значения переменных. |
| [configuration variable](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Демонстрирует использование переменной, определяющей значения конфигурации. Этот шаблон не развертывает ресурсы. При его помощи только создаются и возвращаются значения переменных. |
| [network security rules](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) и [parameter file](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Отвечает за создание массива в правильном формате для назначения правил безопасности в группе безопасности сети. |


## <a name="next-steps"></a>Дополнительная информация
* Полные шаблоны для различных типов решений доступны на странице [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/documentation/templates/).
* Дополнительные сведения о функциях, которые можно использовать в шаблонах, см. в статье [Функции шаблонов Azure Resource Manager](resource-group-template-functions.md).
* Инструкции по объединению нескольких шаблонов при развертывании см. в статье [Использование связанных шаблонов в Azure Resource Manager](resource-group-linked-templates.md).
* Может потребоваться использовать ресурсы, которые существуют в другой группе ресурсов. Это распространенная ситуация при работе с учетными записями хранения или виртуальными сетями, которые совместно используются в нескольких группах ресурсов. Дополнительные сведения см. в описании [функции resourceId](resource-group-template-functions-resource.md#resourceid).