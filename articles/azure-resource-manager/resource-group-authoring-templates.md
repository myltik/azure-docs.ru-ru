---
title: "Создание шаблонов Azure Resource Manager | Документация Майкрософт"
description: "Создание шаблонов диспетчера ресурсов Azure с помощью декларативного синтаксиса JSON для развертывания приложений в Azure."
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
ms.date: 12/01/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: a3a1fc856dc4fb39e3d3b765e943662799c75398
ms.openlocfilehash: 62b51e2c6235011019d0ad837fe58388cf85e8d0


---
# <a name="authoring-azure-resource-manager-templates"></a>Создание шаблонов диспетчера ресурсов Azure
В этой статье описана структура шаблона Azure Resource Manager. Статья содержит информацию о разных разделах шаблона и свойствах, которые доступны в этих разделах. Шаблон состоит из JSON и выражений, на основе которых можно создавать значения для развертывания. 

Сведения о том, как просмотреть шаблон для развернутого ресурса, см. в статье [Экспорт шаблона Azure Resource Manager из существующих ресурсов](resource-manager-export-template.md). Рекомендации по созданию шаблона см. в [пошаговом руководстве по созданию шаблона Resource Manager](resource-manager-template-walkthrough.md). Дополнительные рекомендации по созданию шаблонов см. в статье [Рекомендации по созданию шаблонов Azure Resource Manager](resource-manager-template-best-practices.md).

Хороший редактор JSON может упростить создание шаблонов. Сведения об использовании шаблонов в Visual Studio см. в статье [Создание и развертывание групп ресурсов Azure с помощью Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). Сведения об использовании VS Code см. в статье [Работа с шаблонами Azure Resource Manager в Visual Studio Code](resource-manager-vs-code.md).

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

Разделы шаблона мы рассмотрим более подробно далее в этой статье.

## <a name="expressions-and-functions"></a>Выражения и функции
Базовый синтаксис шаблона — это JSON. Тем не менее выражения и функции расширяют JSON, доступный в шаблоне. С помощью выражений можно создавать значения, которые не являются строгими значениями литералов. Выражения заключаются в квадратные скобки ([ и ]) и вычисляются при развертывании шаблона. Выражения могут встречаться в любом месте строкового значения JSON и всегда возвращают другое значение JSON. Если нужно использовать строковый литерал, который начинается с квадратной скобки ([), необходимо указать две квадратные скобки ([[).

Как правило, выражения используются с функциями для выполнения операций по настройке развертывания. Как и в языке JavaScript, вызовы функций форматируются в виде **functionName(arg1,arg2,arg3)**. Обращение к свойствам производится с помощью точки и операторов [index].

Вот пример использования нескольких функций во время создания значений.

```json
"variables": {
   "location": "[resourceGroup().location]",
   "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
   "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
}
```

Полный список функций шаблонов см. в статье [Функции шаблонов диспетчера ресурсов Azure](resource-group-template-functions.md). 

## <a name="parameters"></a>Параметры
В разделе параметров шаблона указываются значения, которые вы можете вводить во время развертывания ресурсов. Значения этих параметров позволяют настраивать развертывание путем предоставления значений, предназначенных для конкретной среды (например, для среды разработки, тестирования и рабочей среды). Предоставление параметров в шаблоне не является обязательным требованием, однако без параметров шаблон всегда будет развертывать одни и те же ресурсы с одинаковыми именами, расположениями и свойствами.

Эти значения параметров можно использовать по всему шаблону, чтобы задать значения для развернутых ресурсов. В других разделах шаблона можно использовать только параметры, которые объявлены в разделе параметров.

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
| type |Да |Тип значения параметра. Ниже приведен список разрешенных типов. |
| defaultValue |Нет |Значение параметра, используемое по умолчанию, если пользователь не задал иное значение. |
| allowedValues |Нет |Массив допустимых значений параметра, по которому сверяются правильные значения. |
| minValue |Нет |Минимальное значение для параметров типа int. Это включающее значение. |
| maxValue |Нет |Максимальное значение для параметров типа int. Это включающее значение. |
| minLength |Нет |Минимальная длина параметров типа string, secureString и array. Это включающее значение. |
| maxLength |Нет |Максимальная длина параметров типа string, secureString и array. Это включающее значение. |
| Описание |Нет |Описание параметра, отображающееся для пользователей шаблона через интерфейс пользовательского шаблона портала. |

Допустимые типы и значения:

* **string**
* **secureString**;
* **int**
* **bool**;
* **object**; 
* **secureObject**;
* **array**.

Чтобы указать параметр как необязательный, задайте defaultValue (это может быть пустая строка). 

Если указать параметр, имя которого совпадает с именем одного из параметров в команде, с помощью которой развертывается шаблон, вам будет предложено указать значение для этого параметра с постфиксом **FromTemplate**. Например, если добавить в шаблон параметр с именем **ResourceGroupName**, который соответствует параметру **ResourceGroupName** в командлете [New-AzureRmResourceGroupDeployment][deployment2cmdlet], то вам будет предложено указать значение для **ResourceGroupNameFromTemplate**. В общем случае следует избегать этой путаницы, не присваивая параметрам имена параметров, используемых для операций развертывания.

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
В разделе переменных вы создаете значения, которые можно использовать в разных частях шаблона. Как правило, переменные основываются на указанных значениях параметров. Переменные определять не обязательно, однако они часто упрощают шаблон, снижая число сложных выражений.

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
В разделе resources определяются ресурсы, которые развертываются или обновляются. Этот раздел может еще больше усложниться, так как вы должны понимать принципы работы развертываемых типов для предоставления правильных значений. 

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
     }
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
| name |Да |Имя ресурса. Имя должно соответствовать ограничениям компонентов URI, определенным в RFC3986. Кроме того, службы Azure, которые предоставляют имя ресурса внешним пользователям, проверяют это имя, чтобы убедиться, что это не попытка подделки другого удостоверения. Ознакомьтесь со статьей [Проверка имени ресурса](https://msdn.microsoft.com/library/azure/mt219035.aspx). |
| location |Varies |Поддерживаемые географические расположения указанного ресурса. Вы можете выбрать любое из доступных расположений. Но обычно имеет смысл выбрать расположение, которое находится недалеко от пользователей. Кроме того, целесообразно разместить взаимодействующие ресурсы в одном регионе. Большинству типов ресурсов нужно расположение, но некоторым типам (например, назначению роли) оно не требуется. |
| tags |Нет |Теги, связанные с ресурсом. |
| комментарии |Нет |Заметки по ресурсам в шаблоне |
| Свойство dependsOn |Нет |Ресурсы, которые должны быть развернуты перед развертыванием этого ресурса. Resource Manager оценивает зависимости между ресурсами и развертывает эти ресурсы в правильном порядке. Если ресурсы не зависят друг от друга, они развертываются параллельно. Значение может представлять собой разделенный запятыми список имен ресурсов или уникальных идентификаторов ресурсов. Выводится только список ресурсов, развертываемых в этом шаблоне. Ресурсы, которые не определены в этом шаблоне, уже должны существовать. Дополнительные сведения см. в статье [Определение зависимостей в шаблонах диспетчера ресурсов Azure](resource-group-define-dependencies.md). |
| properties |Нет |Параметры конфигурации ресурса. Значения свойств совпадают со значениями, указываемыми в тексте запроса для операции REST API (метод PUT) для создания ресурса. Ссылки на документацию по схеме ресурсов или REST API см. в статье [Поставщики Resource Manager, регионы, версии API и схемы](resource-manager-supported-services.md). |
| копирование |Нет |Количество создаваемых ресурсов (если нужно несколько экземпляров). Дополнительные сведения см. в статье [Создание нескольких экземпляров ресурсов в Azure Resource Manager](resource-group-create-multiple.md). |
| ресурсов |Нет |Дочерние ресурсы, которые зависят от определяемого ресурса. Можно указать только те типы ресурсов, которые разрешены для использования в схеме родительского ресурса. Полное имя типа дочернего ресурса содержит тип родительского ресурса, например **Microsoft.Web/sites/extensions**. Зависимость от родительского ресурса не подразумевается; необходимо явно определить нужную зависимость. |

Не всегда можно сразу понять, какие значения следует задать для **apiVersion**, **type** и **location**. К счастью эти значения можно определить с помощью Azure PowerShell или интерфейса командной строки Azure.

Чтобы получить список всех поставщиков ресурсов с помощью **PowerShell**, используйте следующую команду:

```powershell
Get-AzureRmResourceProvider -ListAvailable
```

В возвращенном списке найдите интересующих поставщиков ресурсов. Чтобы получить список типов ресурсов для поставщика ресурсов (например, хранилище), используйте следующую команду:

```powershell
(Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes
```

Чтобы получить список версий API для типа ресурса (например, учетные записи хранения), используйте следующую команду:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes | Where-Object ResourceTypeName -eq storageAccounts).ApiVersions
```

Чтобы получить список поддерживаемых расположений для типа ресурса, используйте следующую команду:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes | Where-Object ResourceTypeName -eq storageAccounts).Locations
```

Чтобы получить список всех поставщиков ресурсов с помощью **интерфейса командной строки Azure**, используйте следующую команду:

    azure provider list

В возвращенном списке найдите интересующих поставщиков ресурсов. Чтобы получить список типов ресурсов для поставщика ресурсов (например, хранилище), используйте следующую команду:

    azure provider show Microsoft.Storage

Чтобы получить список поддерживаемых расположений и версий API, используйте следующую команду:

    azure provider show Microsoft.Storage --details --json

Дополнительные сведения о поставщиках ресурсов см. в статье [Поставщики Resource Manager, регионы, версии API и схемы](resource-manager-supported-services.md).

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

В следующем примере показан ресурс **Microsoft.Web/serverfarms**, а также ресурс **Microsoft.Web/sites** с дочерним ресурсом **Extensions**. Обратите внимание, что сайт помечен как зависимый от фермы серверов, поскольку ферма серверов должна существовать перед развертыванием сайта. Кроме того, обратите внимание, что ресурс **Extensions** является дочерним для сайта.

```json
"resources": [
  {
    "apiVersion": "2015-08-01",
    "name": "[parameters('hostingPlanName')]",
    "type": "Microsoft.Web/serverfarms",
    "location": "[resourceGroup().location]",
    "tags": {
      "displayName": "HostingPlan"
    },
    "sku": {
      "name": "[parameters('skuName')]",
      "capacity": "[parameters('skuCapacity')]"
    },
    "properties": {
      "name": "[parameters('hostingPlanName')]",
      "numberOfWorkers": 1
    }
  },
  {
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[parameters('siteName')]",
    "location": "[resourceGroup().location]",
    "tags": {
      "environment": "test",
      "team": "Web"
    },
    "dependsOn": [
      "[concat(parameters('hostingPlanName'))]"
    ],
    "properties": {
      "name": "[parameters('siteName')]",
      "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
    },
    "resources": [
      {
        "apiVersion": "2015-08-01",
        "type": "extensions",
        "name": "MSDeploy",
        "dependsOn": [
          "[concat('Microsoft.Web/sites/', parameters('siteName'))]"
        ],
        "properties": {
          "packageUri": "https://auxmktplceprod.blob.core.windows.net/packages/StarterSite-modified.zip",
          "dbType": "None",
          "connectionString": "",
          "setParameters": {
            "Application Path": "[parameters('siteName')]"
          }
        }
      }
    ]
  }
]
```

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



<!--HONumber=Dec16_HO1-->


