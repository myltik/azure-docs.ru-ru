---
title: "Объекты как параметры в шаблонах Azure Resource Manager | Документация Майкрософт"
description: "Описывается, как расширить функциональные возможности шаблонов Azure Resource Manager для использования объектов как параметров."
services: azure-resource-manager, guidance
documentationcenter: na
author: petertay
manager: christb
editor: 
ms.service: guidance
ms.topic: article
ms.date: 05/01/2017
ms.author: mspnp
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 0ab00cc3455d4bff7bfe1dfb62bafa550d65dea8
ms.contentlocale: ru-ru
ms.lasthandoff: 05/03/2017


---

# <a name="patterns-for-extending-the-functionality-of-azure-resource-manager-templates---objects-as-parameters"></a>Примеры расширения функциональных возможностей шаблонов Azure Resource Manager. Объекты как параметры

Шаблоны Azure Resource Manager поддерживает параметры, позволяющие указать значения для настройки развертывания ресурсов. Эта полезная функция позволяет создавать сложные развертывания, однако каждый шаблон ограничен 255 параметрами. Если использовать по одному параметру для каждого свойства в ресурсе, выполняя крупномасштабное развертывание, то параметров может не хватить.

## <a name="create-object-as-parameter"></a>Создание объекта как параметра

Одним из способов решения этой проблемы является использование объекта в качестве параметра. Используется такой принцип: параметр указывается как объект в шаблоне, а затем объект предоставляется как значение или массив значений. Сошлитесь на его подсвойства с помощью функции `parameter()` и оператора-точки. Например:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "VNetSettings":{"type":"object"}
  },
  "variables": {},
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
  ],          
  "outputs": {}
}

```

Соответствующий файл параметров выглядит следующим образом:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
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
}
```

В этом примере все значения, указанные для виртуальной сети, берутся из одного параметра — `VNetSettings`. С помощью такого подхода удобно управлять значениями свойств, так как он позволяет хранить все значения для определенного ресурса в одном объекте. В данном примере используется объект как параметр, но также можно использовать массив объектов как параметр. Для ссылки на объекты используется индекс массива.

## <a name="use-object-instead-of-multiple-arrays"></a>Использование объекта вместо нескольких массивов

Возможно, вы использовали аналогичный подход при создании нескольких экземпляров ресурса, создавая несколько массивов значений свойств и выполняя итерацию каждого массива для выбора значения. Этот принцип хорошо подходит для создания нескольких ресурсов одного типа, но он может быть неудобным при создании дочерних ресурсов. 

Для этого есть две причины. Во-первых, Resource Manager пытается развернуть дочерние ресурсы в параллельном режиме, но развертывание завершается ошибкой, когда два дочерних ресурса одновременно обновляют родительский ресурс. 

Во-вторых, итерация каждого массива значений свойств выполняется параллельно, и если массивы отличаются по форме, то возникает ошибка. Например, рассмотрим следующие массивы свойств:

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
        "one","two"
    ]
}
```

Типичный способ назначения этих значений свойствам внутри цикла копирования — получить доступ к свойству с помощью функции `variables()` и использовать `copyIndex()` как индекс массива. Например:

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
Обратите внимание, что значение `count` цикла копирования основано на количестве свойств в массиве `firstProperty`. Однако в массиве `secondProperty` не такое же количество свойств. При проверке этого шаблона происходит сбой, так как длина массива `secondProperty` отличается.

Тем не менее, если включить все свойства в один объект, то будет гораздо проще заметить отсутствие какого-то значения. Например:

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

## <a name="use-with-sequential-copy"></a>Использование с последовательным копированием

Этот подход становится еще более удобным в сочетании со [схемой последовательного копирования](resource-manager-sequential-loop.md), особенно при развертывании дочерних ресурсов. В следующем примере шаблона развертывается группа безопасности сети с двумя правилами безопасности. Первый ресурс с именем `NSG1` развертывает группу безопасности сети. Вторая группа ресурсов с именем `loop-0` выполняет две функции: во-первых, она зависит (`dependsOn`) от группы безопасности сети, поэтому ее развертывание не начинается, пока `NSG1` не завершит развертывание. И это является первой итерацией последовательного цикла. Третий ресурс — это вложенный шаблон, который развертывает правила безопасности, используя объект для получения значений своих параметров, как показано в последнем примере.

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
                "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
            "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Соответствующий файл параметров выглядит следующим образом:

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

## <a name="try-the-template"></a>Пробное использование шаблона

Если вы хотите поэкспериментировать с этими шаблонами, выполните следующие действия:

1.    Перейдите на портал Azure, щелкните значок "+" и найдите тип ресурса "Развертывание шаблона". Выберите его в результатах поиска.
2.    На открывшейся странице "Развертывание шаблона" нажмите кнопку **Создать**. В результате откроется колонка "Настраиваемое развертывание".
3.    Нажмите кнопку **Изменить шаблон**.
4.    Удалите пустой шаблон в правой области.
5.    Скопируйте и вставьте пример шаблона в область справа.
6.    Нажмите кнопку **Сохранить**.
7.    Когда вы вернетесь в область "Настраиваемое развертывание", нажмите кнопку **Изменить параметры**.
8.  В колонке "Изменить параметры" удалите существующий шаблон.
9.  Скопируйте и вставьте пример шаблона параметров, приведенный выше.
10. Нажмите кнопку **Сохранить**, чтобы вернуться к колонке "Настраиваемое развертывание".
11. В колонке "Настраиваемое развертывание" выберите свою подписку, создайте или используйте существующую группу ресурсов, а также выберите расположение. Ознакомьтесь с условиями и установите флажок "Принимаю".
12.    Нажмите кнопку **Приобрести**.

## <a name="next-steps"></a>Дальнейшие действия

Если вам требуется более 255 параметров, которые максимально допускаются в одном развертывании, то используйте этот пример, чтобы указать меньшее число параметров в своем шаблоне. Этот пример также можно использовать, чтобы упростить управление свойствами ресурсов и развернуть их без конфликтов, используя схему последовательного цикла.

* Чтобы ознакомиться с функцией `parameter()` и использованием массивов, см. статью [Функции шаблона Resource Manager](resource-group-template-functions.md).
* Этот подход также реализован в [проекте стандартных блоков шаблона](https://github.com/mspnp/template-building-blocks) и [эталонных архитектурах Azure](/azure/architecture/reference-architectures/).
