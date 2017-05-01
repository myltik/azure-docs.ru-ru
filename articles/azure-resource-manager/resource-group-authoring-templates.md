---
title: "Структура и синтаксис шаблона Azure Resource Manager | Документация Майкрософт"
description: "Описывается создание структуры и свойств шаблонов Azure Resource Manager с помощью декларативного синтаксиса JSON."
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
ms.date: 03/23/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: de6016130666774577fb4ef598a44f6191ba9a76
ms.lasthandoff: 04/14/2017


---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Описание структуры и синтаксиса шаблонов Azure Resource Manager
В этой статье описана структура шаблона Azure Resource Manager. Статья содержит информацию о разных разделах шаблона и свойствах, которые доступны в этих разделах. Шаблон состоит из JSON и выражений, на основе которых можно создавать значения для развертывания. Пошаговое руководство по созданию шаблона приведено в разделе [Создание первого шаблона Azure Resource Manager](resource-manager-create-first-template.md).

Задайте максимальный размер шаблона, равный 1 МБ, а максимальный размер каждого файла параметров — 64 КБ. Ограничение в 1 МБ применяется к конечному состоянию шаблона после расширения с использованием итеративных определений ресурсов, а также значений переменных и параметров. 

## <a name="template-format"></a>Формат шаблона
Шаблон с самой простой структурой содержит следующие элементы:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
    "parameters": {  },
    "variables": {  },
    "resources": [  ],
    "outputs": {  }
}
```

| Имя элемента | Обязательно | Описание |
|:--- |:--- |:--- |
| $schema |Да |Расположение файла схемы JSON, который описывает версию языка шаблона. Используйте URL-адрес из предыдущего примера. |
| contentVersion |Да |Версия шаблона (например, 1.0.0.0). Для этого элемента можно предоставить любое значение. При развертывании ресурсов с помощью шаблона это значение позволяет убедиться в том, что используется нужный шаблон. |
| parameters |Нет |Значения, которые предоставляются при выполнении развертывания для настройки развертывания ресурсов. |
| variables |Нет |Значения, используемые в виде фрагментов JSON в шаблоне для упрощения выражений на языке шаблона. |
| ресурсов |Да |Типы ресурсов, которые развертываются или обновляются в группе ресурсов. |
| outputs |Нет |Значения, возвращаемые после развертывания. |

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
        "<variable-name>": { 
            <variable-complex-type-value> 
        }
    },
    "resources": [
      {
          "apiVersion": "<api-version-of-resource>",
          "type": "<resource-provider-namespace/resource-type-name>",
          "name": "<name-of-the-resource>",
          "location": "<location-of-resource>",
          "tags": "<name-value-pairs-for-resource-tagging>",
          "comments": "<your-reference-notes>",
          "dependsOn": [
              "<array-of-related-resource-names>"
          ],
          "properties": "<settings-for-the-resource>",
          "copy": {
              "name": "<name-of-copy-loop>",
              "count": "<number-of-iterations>"
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

Разделы шаблона мы рассмотрим более подробно далее в этой статье.

## <a name="expressions-and-functions"></a>Выражения и функции
Базовый синтаксис шаблона — это JSON. Тем не менее выражения и функции расширяют значения JSON, доступные в шаблоне.  Выражения записываются в строковых литералах JSON, первым и последним знаком которых являются квадратные скобки: `[` и `]` соответственно. Значение выражения вычисляется при развертывании шаблона. Хотя результат вычисления выражения и записывается как строковый литерал, он может иметь другой тип JSON, например массив или целое число, в зависимости от фактического выражения.  Чтобы строковый литерал, начинающийся с квадратной скобки (`[`), не интерпретировался как выражение, добавьте дополнительную скобку, чтобы строка начиналась со знака `[[`.

Как правило, выражения используются с функциями для выполнения операций по настройке развертывания. Как и в языке JavaScript, вызовы функций форматируются так: `functionName(arg1,arg2,arg3)`. Обращение к свойствам производится с помощью точки и операторов [index].

Вот пример использования нескольких функций во время создания значений.

```json
"variables": {
    "location": "[resourceGroup().location]",
    "usernameAndPassword": "[concat(parameters('username'), ':', parameters('password'))]",
    "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
}
```

Полный список функций шаблонов см. в статье [Функции шаблонов диспетчера ресурсов Azure](resource-group-template-functions.md). 

## <a name="parameters"></a>Параметры
В разделе параметров шаблона указываются значения, которые вы можете вводить во время развертывания ресурсов. Значения этих параметров позволяют настраивать развертывание путем предоставления значений, предназначенных для конкретной среды (например, для среды разработки, тестирования и рабочей среды). Предоставление параметров в шаблоне не является обязательным требованием, однако без параметров шаблон всегда будет развертывать одни и те же ресурсы с одинаковыми именами, расположениями и свойствами.

Параметры определяются с помощью следующей структуры.

```json
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
}
```

| Имя элемента | Обязательно | Описание |
|:--- |:--- |:--- |
| имя_параметра |Да |Имя параметра. Должно быть допустимым идентификатором JavaScript. |
| type |Да |Тип значения параметра. Ознакомьтесь со списком допустимых типов после данной таблицы. |
| defaultValue |Нет |Значение параметра, используемое по умолчанию, если пользователь не задал иное значение. |
| allowedValues |Нет |Массив допустимых значений параметра, по которому сверяются правильные значения. |
| minValue |Нет |Минимальное значение для параметров типа int. Это включающее значение. |
| maxValue |Нет |Максимальное значение для параметров типа int. Это включающее значение. |
| minLength |Нет |Минимальная длина параметров типа string, secureString и array. Это включающее значение. |
| maxLength |Нет |Максимальная длина параметров типа string, secureString и array. Это включающее значение. |
| Описание |Нет |Описание параметра, отображаемого для пользователей на портале. |

Допустимые типы и значения:

* **string**
* **secureString**;
* **int**
* **bool**;
* **object**; 
* **secureObject**;
* **array**.

Чтобы указать параметр как необязательный, задайте defaultValue (это может быть пустая строка). 

Если указать в шаблоне имя параметра, которое совпадает с параметром в команде развертывания шаблона, то возникнет потенциальная неоднозначность заданных значений. Resource Manager устраняет эту путаницу, добавляя постфикс **FromTemplate** к параметру шаблона. Предположим, вы добавили в шаблон параметр **ResourceGroupName**, и он конфликтует с параметром **ResourceGroupName** в командлете [New-AzureRmResourceGroupDeployment][deployment2cmdlet]. При развертывании вам будет предложено указать значение для параметра **ResourceGroupNameFromTemplate**. В общем случае следует избегать этой путаницы, не присваивая параметрам имена параметров, используемых для операций развертывания.

> [!NOTE]
> Все пароли, ключи и другие секреты данные должны использовать тип **secureString** . При передаче конфиденциальных данных в объекте JSON используйте тип **secureObject**. Параметры шаблона с типами secureString и secureObject невозможно прочитать после развертывания ресурса. 
> 
> Например, следующая запись в журнале развертывания содержит значения для строки и объекта, но не содержит значений для secureString и secureObject.
>
> ![показать значения развертывания](./media/resource-group-authoring-templates/show-parameters.png)  
>

В следующем примере показано, как определять параметры.

```json
"parameters": {
    "siteName": {
        "type": "string",
        "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
    },
    "hostingPlanName": {
        "type": "string",
        "defaultValue": "[concat(parameters('siteName'),'-plan')]"
    },
    "skuName": {
        "type": "string",
        "defaultValue": "F1",
        "allowedValues": [
          "F1",
          "D1",
          "B1",
          "B2",
          "B3",
          "S1",
          "S2",
          "S3",
          "P1",
          "P2",
          "P3",
          "P4"
        ]
    },
    "skuCapacity": {
        "type": "int",
        "defaultValue": 1,
        "minValue": 1
    }
}
```

Сведения о том, как вводить значения параметров во время развертывания, см. в статье [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](resource-group-template-deploy.md). 

## <a name="variables"></a>Переменные
В разделе переменных вы создаете значения, которые можно использовать в разных частях шаблона. Переменные определять не обязательно, однако они часто упрощают шаблон, снижая число сложных выражений.

Переменные определяются с помощью следующей структуры:

```json
"variables": {
    "<variable-name>": "<variable-value>",
    "<variable-name>": { 
        <variable-complex-type-value> 
    }
}
```

В следующем примере показано, как определить переменную, которая состоит из двух значений параметра.

```json
"variables": {
    "connectionString": "[concat('Name=', parameters('username'), ';Password=', parameters('password'))]"
}
```

В следующем примере показана переменная, которая является сложным типом JSON, и переменные, построенные на основе других переменных.

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
"variables": {
    "environmentSettings": {
        "test": {
            "instancesSize": "Small",
            "instancesCount": 1
        },
        "prod": {
            "instancesSize": "Large",
            "instancesCount": 4
        }
    },
    "currentEnvironmentSettings": "[variables('environmentSettings')[parameters('environmentName')]]",
    "instancesSize": "[variables('currentEnvironmentSettings').instancesSize]",
    "instancesCount": "[variables('currentEnvironmentSettings').instancesCount]"
}
```

## <a name="resources"></a>Ресурсы
В разделе resources определяются ресурсы, которые развертываются или обновляются. Этот раздел может еще больше усложниться, так как вы должны понимать принципы работы развертываемых типов для предоставления правильных значений. Сведения о конкретных значениях ресурсов (apiVersion, тип и свойства), которые необходимо задать, см. в разделе [Define resources in Azure Resource Manager templates](/azure/templates/) (Определение ресурсов в шаблонах Azure Resource Manager). 

Ресурсы определяются с помощью следующей структуры:

```json
"resources": [
  {
      "apiVersion": "<api-version-of-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "name": "<name-of-the-resource>",
      "location": "<location-of-resource>",
      "tags": "<name-value-pairs-for-resource-tagging>",
      "comments": "<your-reference-notes>",
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "properties": "<settings-for-the-resource>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": "<number-of-iterations>"
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Имя элемента | Обязательно | Описание |
|:--- |:--- |:--- |
| версия_API |Да |Версия REST API, которая будет использована для создания ресурса. |
| type |Да |Тип ресурса. Это значение представляет собой сочетание пространства имен поставщика ресурсов и типа ресурса (например, **Microsoft.Storage/storageAccounts**). |
| name |Да |Имя ресурса. Имя должно соответствовать ограничениям компонентов URI, определенным в RFC3986. Кроме того, службы Azure, которые предоставляют имя ресурса внешним пользователям, проверяют это имя, чтобы убедиться, что это не попытка подделки другого удостоверения. |
| location |Varies |Поддерживаемые географические расположения указанного ресурса. Вы можете выбрать любое из доступных расположений. Но обычно имеет смысл выбрать расположение, которое находится недалеко от пользователей. Кроме того, целесообразно разместить взаимодействующие ресурсы в одном регионе. Большинству типов ресурсов нужно расположение, но некоторым типам (например, назначению роли) оно не требуется. Ознакомьтесь с разделом [Определение расположения ресурса в шаблонах Azure Resource Manager](resource-manager-template-location.md). |
| tags |Нет |Теги, связанные с ресурсом. Ознакомьтесь с разделом [Присвоение тегов ресурсам в шаблоне Azure Resource Manager](resource-manager-template-tags.md). |
| комментарии |Нет |Заметки по ресурсам в шаблоне |
| Свойство dependsOn |Нет |Ресурсы, которые должны быть развернуты перед развертыванием этого ресурса. Resource Manager оценивает зависимости между ресурсами и развертывает эти ресурсы в правильном порядке. Если ресурсы не зависят друг от друга, они развертываются параллельно. Значение может представлять собой разделенный запятыми список имен ресурсов или уникальных идентификаторов ресурсов. Выводится только список ресурсов, развертываемых в этом шаблоне. Ресурсы, которые не определены в этом шаблоне, уже должны существовать. Избегайте добавления ненужных зависимостей, так как это может замедлить развертывание и привести к созданию циклических зависимостей. Рекомендации по настройке зависимостей см. в статье [Определение зависимостей в шаблонах диспетчера ресурсов Azure](resource-group-define-dependencies.md). |
| properties |Нет |Параметры конфигурации ресурса. Значения свойств совпадают со значениями, указываемыми в тексте запроса для операции REST API (метод PUT) для создания ресурса. |
| копирование |Нет |Количество создаваемых ресурсов (если нужно несколько экземпляров). Дополнительные сведения см. в статье [Создание нескольких экземпляров ресурсов в Azure Resource Manager](resource-group-create-multiple.md). |
| ресурсов |Нет |Дочерние ресурсы, которые зависят от определяемого ресурса. Следует указать только те типы ресурсов, которые разрешены в схеме родительского ресурса. Полное имя типа дочернего ресурса содержит тип родительского ресурса, например **Microsoft.Web/sites/extensions**. Зависимость от родительского ресурса не подразумевается. Ее необходимо определить явным образом. |

Раздел ресурсов содержит набор ресурсов для развертывания. Внутри каждого ресурса можно также определить набор дочерних ресурсов. Таким образом раздел ресурсов может иметь примерно следующую структуру:

```json
"resources": [
  {
      "name": "resourceA",
  },
  {
      "name": "resourceB",
      "resources": [
        {
            "name": "firstChildResourceB",
        },
        {   
            "name": "secondChildResourceB",
        }
      ]
  },
  {
      "name": "resourceC",
  }
]
```      

Дополнительные сведения об определении дочерних ресурсов см. в разделе [Указание имени и типа дочернего ресурса в шаблоне Resource Manager](resource-manager-template-child-resource.md).

## <a name="outputs"></a>outputs
В разделе выходных данных следует указать значения, которые возвращаются после развертывания. Например, можно возвращать URI для доступа к развернутому ресурсу.

В следующем примере показана структура определения выходных данных:

```json
"outputs": {
    "<outputName>" : {
        "type" : "<type-of-output-value>",
        "value": "<output-value-expression>"
    }
}
```

| Имя элемента | Обязательно | Описание |
|:--- |:--- |:--- |
| outputName |Да |Имя выходного значения. Должно быть допустимым идентификатором JavaScript. |
| type |Да |Тип выходного значения. Выходные значения поддерживает те же типы, что и входные параметры шаблона. |
| value |Да |Выражение на языке шаблона, которое вычисляется и возвращается в качестве выходного значения. |

В следующем примере показано значение, которое возвращается в разделе выходных данных.

```json
"outputs": {
    "siteUri" : {
        "type" : "string",
        "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
    }
}
```

Дополнительные сведения о работе с выходными данными см. в статье [Совместное использование состояния в шаблонах Resource Manager](best-practices-resource-manager-state.md).

## <a name="next-steps"></a>Дальнейшие действия
* Полные шаблоны для различных типов решений доступны на странице [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/documentation/templates/).
* Дополнительные сведения о функциях, которые можно использовать в шаблонах, см. в статье [Функции шаблонов Azure Resource Manager](resource-group-template-functions.md).
* Инструкции по объединению нескольких шаблонов при развертывании см. в статье [Использование связанных шаблонов в Azure Resource Manager](resource-group-linked-templates.md).
* Может потребоваться использовать ресурсы, которые существуют в другой группе ресурсов. Это распространенная ситуация при работе с учетными записями хранения или виртуальными сетями, которые совместно используются в нескольких группах ресурсов. Дополнительные сведения см. в описании [функции resourceId](resource-group-template-functions.md#resourceid).

[deployment2cmdlet]: https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.2.0/new-azurermresourcegroupdeployment

