<properties
   pageTitle="Создание шаблонов диспетчера ресурсов Azure"
   description="Создание шаблонов диспетчера ресурсов Azure с помощью декларативного синтаксиса JSON для развертывания приложений в Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/30/2015"
   ms.author="tomfitz"/>

# Создание шаблонов диспетчера ресурсов Azure

Приложениям Azure обычно требуется сочетание ресурсов (таких как сервер баз данных, база данных или веб-сайт) для соответствия поставленным целям. Вместо развертывания и управления каждым ресурсом по отдельности можно создать шаблон диспетчера ресурсов Azure, который развертывает и подготавливает все ресурсы для вашего приложения в ходе одной скоординированной операции. В шаблоне определяются ресурсы, необходимые для работы приложения, и указываются параметры развертывания в качестве входных значений для различных сред. Шаблон состоит из JSON и выражений, на основе которых можно создавать значения для развертывания.

В этом разделе описываются разделы шаблона. Фактические схемы см. в разделе [Схемы диспетчера ресурсов Azure](https://github.com/Azure/azure-resource-manager-schemas). Visual Studio предоставляет инструменты для создания шаблонов. Дополнительные сведения об использовании шаблонов в Visual Studio см. в статьях [Создание и развертывание групп ресурсов Azure с помощью Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) и [Редактирование шаблонов диспетчера ресурсов с помощью Visual Studio](vs-azure-tools-resource-group-adding-resources.md).

Задайте максимальный размер шаблона, равный 1 МБ, а максимальный размер каждого файла параметров — 64 КБ. Ограничение в 1 МБ применяется к конечному состоянию шаблона после расширения с итеративным определением ресурсов, значениями переменных и параметров.

## Формат шаблона

В следующем примере показаны разделы, которые составляют базовую структуру шаблона.

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

| Имя элемента | Обязательно | Описание
| :------------: | :------: | :----------
| $schema | Да | Расположение файла схемы JSON, который описывает версию языка шаблона.
| contentVersion | Да | Версия шаблона (например, 1.0.0.0). При развертывании ресурсов с помощью шаблона это значение позволяет убедиться в том, что используется нужный шаблон.
| parameters | Нет | Значения, которые предоставляются при выполнении развертывания для настройки развертывания ресурсов.
| variables | Нет | Значения, используемые в виде фрагментов JSON в шаблоне для упрощения выражений на языке шаблона.
| ресурсов | Да | Типы служб, которые развертываются или обновляются в группе ресурсов.
| outputs | Нет | Значения, возвращаемые после развертывания.

Разделы шаблона мы рассмотрим более подробно далее в этом разделе. Сейчас мы рассмотрим элементы синтаксиса, составляющего шаблон.

## Выражения и функции

Базовым синтаксисом шаблона является JSON; однако выражения и функции расширяют JSON, который доступен в шаблоне, и позволяют создавать значения, не являющиеся строгими значениями литералов. Выражения заключаются в квадратные скобки ([ и ]) и вычисляются при развертывании шаблона. Выражения могут встречаться в любом месте строкового значения JSON и всегда возвращают другое значение JSON. Если нужно использовать строковый литерал, который начинается с квадратной скобки ([), необходимо указать две квадратные скобки ([[).

Как правило, выражения используются с функциями для выполнения операций по настройке развертывания. Как и в языке JavaScript, вызовы функций форматируются в виде **functionName(arg1,arg2,arg3)**. Обращение к свойствам производится с помощью точки и операторов [index].

Вот пример использования нескольких функций во время создания значений.
 
    "variables": {
       "location": "[resourceGroup().location]",
       "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
       "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

Полный список функций шаблонов см. в статье [Функции шаблонов диспетчера ресурсов Azure](./resource-group-template-functions.md).


## Параметры

В разделе параметров шаблона указываются значения, которые пользователь может вводить во время развертывания ресурсов. Эти значения параметров можно использовать по всему шаблону, чтобы задать значения для развернутых ресурсов. В других разделах шаблона можно использовать только параметры, которые объявлены в разделе параметров.

Внутри раздела параметров нельзя использовать значение параметра для создания другого значения параметра. Этот тип операции обычно происходит в разделе переменных.

Параметры определяются с помощью следующей структуры.

    "parameters": {
       "<parameterName>" : {
         "type" : "<type-of-parameter-value>",
         "defaultValue": "<optional-default-value-of-parameter>",
         "allowedValues": [ "<optional-array-of-allowed-values>" ],
         "minValue": <optional-minimum-value-for-int-parameters>,
         "maxValue": <optional-maximum-value-for-int-parameters>,
         "minLength": <optional-minimum-length-for-string-secureString-array-parameters>,
         "maxLength": <optional-maximum-length-for-string-secureString-array-parameters>,
         "metadata": {
             "description": "<optional-description-of-the parameter>" 
         }
       }
    }

| Имя элемента | Обязательно | Описание
| :------------: | :------: | :----------
| имя\_параметра | Да | Имя параметра. Должно быть допустимым идентификатором JavaScript.
| type | Да | Тип значения параметра. Ниже приведен список разрешенных типов.
| defaultValue | Нет | Значение параметра, используемое по умолчанию, если пользователь не задал иное значение.
| allowedValues | Нет | Массив допустимых значений параметра, по которому сверяются правильные значения.
| minValue | Нет | Минимальное значение для параметров типа int. Это включающее значение.
| maxValue | Нет | Максимальное значение для параметров типа int. Это включающее значение.
| minLength | Нет | Минимальная длина параметров типа string, secureString и array. Это включающее значение.
| maxLength | Нет | Максимальная длина параметров типа string, secureString и array. Это включающее значение.
| description | Нет | Описание параметра, которое будет отображаться для пользователей шаблона через интерфейс пользовательского шаблона портала.

Допустимые типы и значения:

- string или secureString: любая допустимая строка JSON;
- int: любое допустимое целое число JSON;
- bool: любое допустимое логическое значение JSON;
- object или secureObject: любой допустимый объект JSON;
- array: любой допустимый массив JSON.

Чтобы обозначить параметр, как необязательный, задайте пустую строку в качестве значения параметра defaultValue.

>[AZURE.NOTE]Все пароли, ключи и другие секреты данные должны использовать тип **secureString**. Параметры шаблона с типом secureString невозможно прочитать после развертывания ресурса.

В следующем примере показано, как определять параметры.

    "parameters": {
       "siteName": {
          "type": "string",
          "minLength": 2,
          "maxLength": 60
       },
       "siteLocation": {
          "type": "string",
          "minLength": 2
       },
       "hostingPlanName": {
          "type": "string"
       },  
       "hostingPlanSku": {
          "type": "string",
          "allowedValues": [
            "Free",
            "Shared",
            "Basic",
            "Standard",
            "Premium"
          ],
          "defaultValue": "Free"
       },
       "instancesCount": {
          "type": "int",
          "maxValue": 10
       },
       "numberOfWorkers": {
          "type": "int",
          "minValue": 1
       }
    }

## Переменные

В разделе переменных вы создаете значения, которые можно использовать для упрощения выражений языка шаблона. Как правило, эти переменные основываются на указанных значениях параметров.

Переменные определяются с помощью следующей структуры:

    "variables": {
       "<variable-name>": "<variable-value>",
       "<variable-name>": { 
           <variable-complex-type-value> 
       }
    }

В следующем примере показано, как определить переменную, которая состоит из двух значений параметра.

    "parameters": {
       "username": {
         "type": "string"
       },
       "password": {
         "type": "secureString"
       }
     },
     "variables": {
       "connectionString": "[concat('Name=', parameters('username'), ';Password=', parameters('password'))]"
    }

В следующем примере показана переменная, которая является сложным типом JSON, и переменные, построенные на основе других переменных.

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
       "instancesSize": "[variables('currentEnvironmentSettings').instancesSize",
       "instancesCount": "[variables('currentEnvironmentSettings').instancesCount"
    }

## Ресурсы

В разделе ресурсов определяются ресурсы, которые развертываются или обновляются.

Ресурсы определяются с помощью следующей структуры:

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
         "resources": [
           "<array-of-dependent-resources>"
         ]
       }
    ]

| Имя элемента | Обязательно | Описание
| :----------------------: | :------: | :----------
| версия\_API | Да | Версия REST API, которая будет использована для создания ресурса. Чтобы определить доступные номера версий заданного типа ресурсов, см. раздел [Поддерживаемые версии API](../resource-manager-supported-services/#supported-api-versions).
| type | Да | Тип ресурса. Это значение представляет собой сочетание пространства имен поставщика ресурсов и типа ресурса, который поддерживается поставщиком ресурсов.
| name | Да | Имя ресурса. Имя должно соответствовать ограничениям компонентов URI, определенным в RFC3986.
| location | Нет | Поддерживаемые географические расположения указанного ресурса.
| tags | Нет | Теги, связанные с ресурсом.
| комментарии | Нет | Заметки по ресурсам в шаблоне
| Свойство dependsOn | Нет | Ресурсы, от которых зависит определяемый ресурс. Вычисляются зависимости между ресурсами, и ресурсы развертываются в порядке зависимости. Если ресурсы не зависят друг от друга, выполняется попытка их параллельного развертывания. Значение может представлять собой разделенный запятыми список имен ресурсов или уникальных идентификаторов ресурсов.
| properties | Нет | Параметры конфигурации для конкретного ресурса.
| ресурсов | Нет | Дочерние ресурсы, которые зависят от определяемого ресурса.

Если имя ресурса не является уникальным, можно использовать вспомогательную функцию **resourceId** (описанную ниже) для получения уникального идентификатора любого ресурса.

Значения элемента **properties** совпадают со значениями, указываемыми в тексте запроса для операции REST API (метод PUT) для создания ресурса. Операции REST API с развертываемым ресурсом описываются в [справочнике по Azure](https://msdn.microsoft.com/library/azure/mt420159.aspx).

В следующем примере показаны ресурс **Microsoft.Web/serverfarms** и ресурс **Microsoft.Web/sites** с вложенным ресурсом **Extensions**:

    "resources": [
        {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/serverfarms",
          "name": "[parameters('hostingPlanName')]",
          "location": "[resourceGroup().location]",
          "properties": {
              "name": "[parameters('hostingPlanName')]",
              "sku": "[parameters('hostingPlanSku')]",
              "workerSize": "0",
              "numberOfWorkers": 1
          }
        },
        {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('siteName')]",
          "location": "[resourceGroup().location]",
          "tags": {
              "environment": "test",
              "team": "ARM"
          },
          "dependsOn": [
              "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
          ],
          "properties": {
              "name": "[parameters('siteName')]",
              "serverFarm": "[parameters('hostingPlanName')]"
          },
          "resources": [
              {
                  "apiVersion": "2014-06-01",
                  "type": "Extensions",
                  "name": "MSDeploy",
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


## Выходные данные

В разделе выходных данных следует указать значения, которые возвращаются после развертывания. Например, можно возвращать URI для доступа к развернутому ресурсу.

В следующем примере показана структура определения выходных данных:

    "outputs": {
       "<outputName>" : {
         "type" : "<type-of-output-value>",
         "value": "<output-value-expression>",
       }
    }

| Имя элемента | Обязательно | Описание
| :------------: | :------: | :----------
| outputName | Да | Имя выходного значения. Должно быть допустимым идентификатором JavaScript.
| type | Да | Тип выходного значения. Выходные значения поддерживает те же типы, что и входные параметры шаблона.
| value | Да | Выражение на языке шаблона, которое будет вычислено и возвращено в качестве выходного значения.


В следующем примере показано значение, которое возвращается в разделе выходных данных.

    "outputs": {
       "siteUri" : {
         "type" : "string",
         "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
       }
    }

## Более сложные сценарии.
В этом разделе содержится вступительный обзор шаблона. Однако в конкретном сценарии могут потребоваться более сложные задачи.

Возможно, понадобится объединить два шаблона или использовать дочерний шаблон внутри родительского. Дополнительные сведения см. в статье [Использование связанных шаблонов в диспетчере ресурсов Azure](resource-group-linked-templates.md).

Указания по выполнению заданного количества циклов итерации при создании типа см. в разделе [Создание нескольких экземпляров ресурсов в диспетчере ресурсов Azure](resource-group-create-multiple.md).

Может потребоваться использовать ресурсы, которые существуют в другой группе ресурсов. Это распространенная ситуация при работе с учетными записями хранения или виртуальными сетями, которые совместно используются в нескольких группах ресурсов. Дополнительные сведения см. в описании [функции resourceId](../resource-group-template-functions#resourceid).

## Полный шаблон
Следующий шаблон развертывает веб-приложение и подготавливает его с помощью кода из ZIP-файла.

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "siteName": {
           "type": "string"
         },
         "hostingPlanName": {
           "type": "string"
         },
         "hostingPlanSku": {
           "type": "string",
           "allowedValues": [
             "Free",
             "Shared",
             "Basic",
             "Standard",
             "Premium"
           ],
           "defaultValue": "Free"
         }
       },
       "resources": [
         {
           "apiVersion": "2014-06-01",
           "type": "Microsoft.Web/serverfarms",
           "name": "[parameters('hostingPlanName')]",
           "location": "[resourceGroup().location]",
           "properties": {
             "name": "[parameters('hostingPlanName')]",
             "sku": "[parameters('hostingPlanSku')]",
             "workerSize": "0",
             "numberOfWorkers": 1
           }
         },
         {
           "apiVersion": "2014-06-01",
           "type": "Microsoft.Web/sites",
           "name": "[parameters('siteName')]",
           "location": "[resourceGroup().location]",
           "tags": {
             "environment": "test",
             "team": "ARM"
           },
           "dependsOn": [
             "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
           ],
           "properties": {
             "name": "[parameters('siteName')]",
             "serverFarm": "[parameters('hostingPlanName')]"
           },
           "resources": [
             {
               "apiVersion": "2014-06-01",
               "type": "Extensions",
               "name": "MSDeploy",
               "dependsOn": [
                 "[resourceId('Microsoft.Web/sites', parameters('siteName'))]"
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
       ],
       "outputs": {
         "siteUri": {
           "type": "string",
           "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
         }
       }
    }

## Дальнейшие действия
- Дополнительные сведения о функциях, которые можно использовать в шаблонах, см. в статье [Функции шаблонов в диспетчере ресурсов Azure](resource-group-template-functions.md).
- Указания по развертыванию созданного шаблона см. в статье [Развертывание приложения с использованием шаблона диспетчера ресурсов Azure](resource-group-template-deploy.md).
- Подробный пример развертывания приложения см. в статье [Предсказуемые подготовка и развертывание микрослужб в Azure](app-service-web/app-service-deploy-complex-application-predictably.md).
- Список доступных схем см. в статье [Схемы диспетчера ресурсов Azure](https://github.com/Azure/azure-resource-manager-schemas).

<!---HONumber=AcomDC_1203_2015-->