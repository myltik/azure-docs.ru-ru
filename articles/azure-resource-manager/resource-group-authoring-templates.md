---
title: Структура и синтаксис шаблона Azure Resource Manager | Документация Майкрософт
description: Описывается создание структуры и свойств шаблонов Azure Resource Manager с помощью декларативного синтаксиса JSON.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 19694cb4-d9ed-499a-a2cc-bcfc4922d7f5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/01/2018
ms.author: tomfitz
ms.openlocfilehash: 3b70817f973f0bfbdcec2aa8c76a431eec308bcf
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Описание структуры и синтаксиса шаблонов Azure Resource Manager
В этой статье описана структура шаблона Azure Resource Manager. Статья содержит информацию о разных разделах шаблона и свойствах, которые доступны в этих разделах. Шаблон состоит из JSON и выражений, на основе которых можно создавать значения для развертывания. Пошаговое руководство по созданию шаблона приведено в разделе [Создание первого шаблона Azure Resource Manager](resource-manager-create-first-template.md).

## <a name="template-format"></a>Формат шаблона
Шаблон с самой простой структурой содержит следующие элементы:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
    "parameters": {  },
    "variables": {  },
    "functions": {  },
    "resources": [  ],
    "outputs": {  }
}
```

| Имя элемента | Обязательно | ОПИСАНИЕ |
|:--- |:--- |:--- |
| $schema |Yes |Расположение файла схемы JSON, который описывает версию языка шаблона. Используйте URL-адрес из предыдущего примера. |
| contentVersion |Yes |Версия шаблона (например, 1.0.0.0). Для этого элемента можно предоставить любое значение. При развертывании ресурсов с помощью шаблона это значение позволяет убедиться в том, что используется нужный шаблон. |
| parameters |Нет  |Значения, которые предоставляются при выполнении развертывания для настройки развертывания ресурсов. |
| variables |Нет  |Значения, используемые в виде фрагментов JSON в шаблоне для упрощения выражений на языке шаблона. |
| functions |Нет  |Определяемые пользователем функции, доступные в шаблоне. |
| ресурсов |Yes |Типы ресурсов, которые развертываются или обновляются в группе ресурсов. |
| outputs |Нет  |Значения, возвращаемые после развертывания. |

Каждый элемент содержит свойства, которые можно задать. В следующем примере приведен полный синтаксис шаблона.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
    "parameters": {  
        "<parameter-name>" : {
            "type" : "<type-of-parameter-value>",
            "defaultValue": "<default-value-of-parameter>",
            "allowedValues": [ "<array-of-allowed-values>" ],
            "minValue": <minimum-value-for-int>,
            "maxValue": <maximum-value-for-int>,
            "minLength": <minimum-length-for-string-or-array>,
            "maxLength": <maximum-length-for-string-or-array-parameters>,
            "metadata": {
                "description": "<description-of-the parameter>" 
            }
        }
    },
    "variables": {
        "<variable-name>": "<variable-value>",
        "<variable-object-name>": {
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
    },
    "functions": [
      {
        "namespace": "<namespace-for-your-function>",
        "members": {
          "<function-name>": {
            "parameters": [
              {
                "name": "<parameter-name>",
                "type": "<type-of-parameter-value>"
              }
            ],
            "output": {
              "type": "<type-of-output-value>",
              "value": "<function-expression>"
            }
          }
        }
      }
    ],
    "resources": [
      {
          "condition": "<boolean-value-whether-to-deploy>",
          "apiVersion": "<api-version-of-resource>",
          "type": "<resource-provider-namespace/resource-type-name>",
          "name": "<name-of-the-resource>",
          "location": "<location-of-resource>",
          "tags": {
              "<tag-name1>": "<tag-value1>",
              "<tag-name2>": "<tag-value2>"
          },
          "comments": "<your-reference-notes>",
          "copy": {
              "name": "<name-of-copy-loop>",
              "count": "<number-of-iterations>",
              "mode": "<serial-or-parallel>",
              "batchSize": "<number-to-deploy-serially>"
          },
          "dependsOn": [
              "<array-of-related-resource-names>"
          ],
          "properties": {
              "<settings-for-the-resource>",
              "copy": [
                  {
                      "name": ,
                      "count": ,
                      "input": {}
                  }
              ]
          },
          "resources": [
              "<array-of-child-resources>"
          ]
      }
    ],
    "outputs": {
        "<outputName>" : {
            "type" : "<type-of-output-value>",
            "value": "<output-value-expression>"
        }
    }
}
```

В этой статье подробнее описаны разделы шаблона.

## <a name="syntax"></a>Синтаксис
Базовый синтаксис шаблона — это JSON. Тем не менее выражения и функции расширяют значения JSON, доступные в шаблоне.  Выражения записываются в строковых литералах JSON, первым и последним знаком которых являются квадратные скобки: `[` и `]` соответственно. Значение выражения вычисляется при развертывании шаблона. Хотя результат вычисления выражения и записывается как строковый литерал, он может иметь другой тип JSON, например массив или целое число, в зависимости от фактического выражения.  Чтобы строковый литерал, начинающийся с квадратной скобки (`[`), не интерпретировался как выражение, добавьте дополнительную скобку, чтобы строка начиналась со знака `[[`.

Как правило, выражения используются с функциями для выполнения операций по настройке развертывания. Как и в языке JavaScript, вызовы функций форматируются так: `functionName(arg1,arg2,arg3)`. Обращение к свойствам производится с помощью точки и операторов [index].

В следующем примере показано, как использовать несколько функций при создании значения:

```json
"variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
}
```

Полный список функций шаблонов см. в статье [Функции шаблонов диспетчера ресурсов Azure](resource-group-template-functions.md). 

## <a name="parameters"></a>Параметры
В разделе параметров шаблона указываются значения, которые вы можете вводить во время развертывания ресурсов. Значения этих параметров позволяют настраивать развертывание путем предоставления значений, предназначенных для конкретной среды (например, для среды разработки, тестирования и рабочей среды). Предоставление параметров в шаблоне не является обязательным требованием, однако без параметров шаблон всегда будет развертывать одни и те же ресурсы с одинаковыми именами, расположениями и свойствами.

В следующем примере показано определение простого параметра:

```json
"parameters": {
  "siteNamePrefix": {
    "type": "string",
    "metadata": {
      "description": "The name prefix of the web app that you wish to create."
    }
  },
},
```

Дополнительные сведения об определении параметров см. в описании [шаблонов Azure Resource Manager](resource-manager-templates-parameters.md).

## <a name="variables"></a>Переменные
В разделе переменных вы создаете значения, которые можно использовать в разных частях шаблона. Переменные определять не обязательно, однако они часто упрощают шаблон, снижая число сложных выражений.

В следующем примере показано определение простой переменной:

```json
"variables": {
  "webSiteName": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
},
```

Дополнительные сведения об определении переменных см. в описании [шаблонов Azure Resource Manager](resource-manager-templates-variables.md).

## <a name="functions"></a>Функции Azure

В шаблоне можно создать свои собственные функции. Эти функции доступны для использования в шаблоне. Как правило, определяется сложное выражение, которое вы не хотите повторять в шаблоне. Создаются определяемые пользователем функции на основе выражений и [функции](resource-group-template-functions.md), которые поддерживаются в шаблонах.

При определении пользовательской функции есть несколько ограничений:

* Функция не может обращаться к переменным.
* Для функции нельзя использовать [ссылочную функцию](resource-group-template-functions-resource.md#reference).
* Для параметров этой функции нельзя задавать значения по умолчанию.

Для функции требуется значение пространства имен, чтобы избежать конфликтов именования с функциями шаблона. В следующем примере показана функция, которая возвращает имя учетной записи хранения:

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

Вызов функции выполняется с помощью следующего кода:

```json
"resources": [
  {
    "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "location": "South Central US",
    "tags": {},
    "properties": {}
  }
]
```

## <a name="resources"></a>Ресурсы
В разделе resources определяются ресурсы, которые развертываются или обновляются. Этот раздел может еще больше усложниться, так как вы должны понимать принципы работы развертываемых типов для предоставления правильных значений.

```json
"resources": [
  {
    "apiVersion": "2016-08-01",
    "name": "[variables('webSiteName')]",
    "type": "Microsoft.Web/sites",
    "location": "[resourceGroup().location]",
    "properties": {
      "serverFarmId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Web/serverFarms/<plan-name>"
    }
  }
],
```

Дополнительные сведения о ресурсах см. в описании [шаблонов Azure Resource Manager](resource-manager-templates-resources.md).

## <a name="outputs"></a>outputs
В разделе выходных данных следует указать значения, которые возвращаются после развертывания. Например, можно возвращать URI для доступа к развернутому ресурсу.

```json
"outputs": {
  "newHostName": {
    "type": "string",
    "value": "[reference(variables('webSiteName')).defaultHostName]"
  }
}
```

Дополнительные сведения о выходных данных см. в описании [шаблонов Azure Resource Manager](resource-manager-templates-outputs.md).

## <a name="template-limits"></a>Ограничения шаблонов

Задайте максимальный размер шаблона, равный 1 МБ, а максимальный размер каждого файла параметров — 64 КБ. Ограничение в 1 МБ применяется к конечному состоянию шаблона после расширения с использованием итеративных определений ресурсов, а также значений переменных и параметров. 

Кроме того, ограничение распространяется на:

* 256 параметров;
* 256 переменных;
* 800 ресурсов (включая число копий);
* 64 выходных значения;
* 24 576 знаков в выражении шаблона.

Некоторые ограничения можно превысить, используя вложенные шаблоны. Дополнительные сведения см. разделе [Использование связанных шаблонов в при развертывании ресурсов Azure](resource-group-linked-templates.md). Чтобы уменьшить число параметров, переменных или выходных данных, можно объединить несколько значений в объект. См. дополнительные сведения об [использовании объектов как параметров](resource-manager-objects-as-parameters.md).

## <a name="next-steps"></a>Дополнительная информация
* Полные шаблоны для различных типов решений доступны на странице [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/documentation/templates/).
* Дополнительные сведения о функциях, которые можно использовать в шаблонах, см. в статье [Функции шаблонов Azure Resource Manager](resource-group-template-functions.md).
* Инструкции по объединению нескольких шаблонов при развертывании см. в статье [Использование связанных шаблонов в Azure Resource Manager](resource-group-linked-templates.md).
* Может потребоваться использовать ресурсы, которые существуют в другой группе ресурсов. Это распространенная ситуация при работе с учетными записями хранения или виртуальными сетями, которые совместно используются в нескольких группах ресурсов. Дополнительные сведения см. в описании [функции resourceId](resource-group-template-functions-resource.md#resourceid).
