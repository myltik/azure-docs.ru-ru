<properties
   pageTitle="API поиска по журналу в службе Operational Insights"
   description="Содержит общие сведения об API поиска по журналу в Operational Insights, а также примеры, в которых показано, как его использовать."
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="07/21/2015"
   ms.author="banders" />



# Обзор и справочная информация по API поиска в службе Operational Insights

Данное руководство дает базовые знания о том, как можно использовать API поиска по журналу в службе Operational Insights и содержит примеры использования команд.

## Обзор API поиска по журналу

API поиска по журналу в службе Operational Insights относится к категории RESTful и может запускаться через API диспетчера ресурсов Azure. В этом документе вы найдете примеры, когда доступ к API осуществляется через [ARMClient](https://github.com/projectkudu/ARMClient) — инструмент командной строки с открытым исходным кодом, который упрощает вызов API диспетчера ресурсов Azure. Использование ARMClient и PowerShell — это один из множества вариантов получения доступа к API поиска по журналу в службе Operational Insights. С помощью этих средств можно использовать API диспетчера ресурсов Azure категории RESTful для осуществления вызовов рабочих областей Operational Insights и выполнения команд поиска в них. API будет выдавать результаты поиска в формате JSON, позволяя программно использовать результаты поиска различными способами.

Диспетчер ресурсов Azure можно использовать через [Библиотеку для .NET](https://msdn.microsoft.com/library/azure/dn910477.aspx), а также через интерфейс [API REST](https://msdn.microsoft.com/library/azure/mt163658.aspx). Для получения дополнительных сведений просмотрите популярные веб-страницы.

## Учебник по основам API поиска

### Чтобы использовать клиент ARM

1. Установите [Chocolatey](https://chocolatey.org/), который является диспетчером машинных пакетов с открытым исходным кодом для Windows.
2. Откройте окно PowerShell от имени администратора, а затем выполните следующую команду:

    ```
    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
    ```

3. Установите ARMClient, открыв новое окно командной строки, и выполните следующую команду:

    ```
    choco install armclient
    ```

### Для выполнения простого поиска с помощью ARMClient

1. Выполните вход в учетную запись Майкрософт или OrgID:

    ```
    armclient login
```
  При успешном выполнении входа появится список всех подписок, привязанных к данной учетной записи. Например:

    ```
    PS C:\Users\SampleUserName> armclient login
    Welcome YourEmail@ORG.com (Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz)
    User: YourEmail@ORG.com, Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz (Example org)
    There are 3 subscriptions
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 1)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 2)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 3)
    ```

2. Получение рабочих областей Operations Management Suite Workspaces. Например:

    ```
    armclient get /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces?api-version=2014-10-10
    ```

    При успешном вызове Get отобразятся все рабочие области, привязанные к подписке. Например:

    ```
    {
    "value": [
    {
      "properties": {
    "source": "External",
    "customerId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "portalUrl": "https://eus.login.mms.microsoft.com/SignIn.aspx?returnUrl=https%3a%2f%2feus.mms.microsoft.com%2fMain.aspx%3fcid%xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
      },
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/{WORKSPACE NAME}",
      "name": "{WORKSPACE NAME}",
      "type": "Microsoft.OperationalInsights/workspaces",
      "location": "East US"
       ]
    }
    ```
3. Создание своей переменной поиска. Например:

    ```
    $mySearch = "{ 'top':150, 'query':'Error'}”;
    ```
4. Поиск с помощью новой переменной поиска. Например:

    ```
    armclient post /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{WORKSPACE NAME}/search?api-version=2014-10-10 $mySearch
    ```

## Примеры API поиска
На следующих примерах показано, как можно использовать API поиска.

### Поиск — Действие/Чтение

**Пример URL:**

```
	/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search?api-version=2014-10-10
```

**Запрос:**

```
	$savedSearchParametersJson =
	{
	  "top":150,
	  "highlight":{
	    "pre":"{[hl]}",
	    "post":"{[/hl]}"
	  },
	  "query":"*",
	  "start":"2015-02-04T21:03:29.231Z",
	  "end":"2015-02-11T21:03:29.231Z"
	}
	armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2014-10-10 $searchParametersJson
```
В следующей таблице описаны имеющиеся свойства.

|**Свойство**|**Описание**|
|---|---|
|top|Максимальное количество отображаемых результатов.|
|highlight|Содержит параметры pre и post, которые обычно используются для выделения соответствующих запросу полей|
|pre|Добавляет заданную строку перед соответствующими запросу полями.|
|post|Добавляет заданную строку после соответствующих запросу полей.|
|запрос|Поисковый запрос, используемый для сбора и отображения результатов.|
|start|Начало периода, за который необходимо собрать результаты.|
|end|Окончание периода, за который необходимо собрать результаты.|


**Ответ.**

```
	{
	  "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
	  "__metadata" : {
	    "resultType" : "raw",
	    "total" : 1455,
	    "top" : 150,
	    "StartTime" : "2015-02-11T21:09:07.0345815Z",
	    "Status" : "Successful",
	    "LastUpdated" : "2015-02-11T21:09:07.331463Z",
	    "CoreResponses" : [],
	    "sort" : [{
	      "name" : "TimeGenerated",
	      "order" : "desc"
	    }],
	    "requestTime" : 450
	  },
	  "value": [{
	    "SourceSystem" : "OpsManager",
	    "TimeGenerated" : "2015-02-07T14:07:33Z",
	    "Source" : "SideBySide",
	    "EventLog" : "Application",
	    "Computer" : "BAMBAM",
	    "EventCategory" : 0,
	    "EventLevel" : 1,
	    "EventLevelName" : "Error",
	    "UserName" : "N/A",
	    "EventID" : 78,
	    "MG" : "00000000-0000-0000-0000-000000000001",
	    "TimeCollected" : "2015-02-07T14:10:04.69Z",
	    "ManagementGroupName" : "AOI-5bf9a37f-e841-462b-80d2-1d19cd97dc40",
	    "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
	    "Type" : "Event",
	    "__metadata" : {
	      "Type" : "Event",
	      "TimeGenerated" : "2015-02-07T14:07:33Z",
	      "highlighting" : {
	      "EventLevelName" : ["{[hl]}Error{[/hl]}"]
	    }
	  }]
	],
	        "start" : "2015-02-04T21:03:29.231Z",
	        "end" : "2015-02-11T21:03:29.231Z"
	      }
	    }
	  }]
	}
```

### Поиск/{ID} - Действие/Чтение

**Запрос содержимого сохраненных результатов поиска:**

```
	armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search/{SearchId}?api-version=2014-10-10
```

>[AZURE.NOTE]Если результаты поиска выдаются со статусом "Ожидание", данный API можно использовать для запроса обновленных результатов. Через 6 минут результат поиска удаляется из кэша и выдается статус Http Gone. Если по первому поисковому запросу сразу выдаются результаты со статусом "Успешно", результаты не добавляются в кэш, в результате чего при запросе этот API выдает статус Http Gone. Содержимое результатов Http 200 будет представлено в том же формате, что и у первоначального поискового запроса, но с обновленными значениями.

### Сохраненные поисковые запросы — только REST

**Запрос списка сохраненных поисковых запросов:**

```
	armclient get /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/savedSearches?api-version=2014-10-10
```

Поддерживаемые методы: GET, PUT и DELETE.

Поддерживаемые методы сбора данных: GET

В следующей таблице описаны имеющиеся свойства.

|Свойство|Описание|
|---|---|
|Идентификатор|Уникальный идентификатор.|
|Etag|**Необходимо для обновления**. Обновляется сервером при каждой операции записи. Для обновления требуется, чтобы в качестве значения использовалось текущее сохраненное значение или символ *. Для старых или недопустимых значений возвращается 409.|
|properties.query|**Обязательное свойство**. Поисковый запрос.|
|properties.displayName|**Обязательное свойство**. Определяемое пользователем отображаемое имя запроса. Если смоделировать как ресурс Azure, это будет тег.|
|properties.category|**Обязательное свойство**. Определяемая пользователем категория запроса. Если смоделировать как ресурс Azure, это будет тег.|

>[AZURE.NOTE]Сейчас, когда поступают запросы на сохраненные поисковые запросы в рабочей области, API поиска в службе Operational Insights возвращает сохраненные поисковые запросы, созданные пользователем. Сохраненные поисковые запросы, созданные решениями, пока не возвращаются. Эта возможность будет добавлена позже.

### Удаление сохраненных поисковых запросов

**Запрос**

```
	armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2014-10-10
```

### Изменение сохраненных поисковых запросов

 **Запрос**

```
	$savedSearchParametersJson = "{'etag': 'W/`"datetime\'2015-04-16T23%3A35%3A35.3182423Z\'`"', 'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
	armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2014-10-10 $savedSearchParametersJson
```

### Метаданные (только JSON)

Вот способ узнать, какие поля есть в журналах данных, собранных в рабочей области. Этот способ подходит для всех типов журналов. Например, если вы хотите узнать, есть ли в типе события поле с именем компьютера, это можно сделать несколькими способами, в частности и этим.

**Запрос полей**

```
	armclient get /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/schema?api-version=2014-10-10
```

**Ответ**

```
	{
	  "__metadata" : {
	    "schema" : {
	      "name" : "Example Name",
	      "version" : 2
	    },
	    "resultType" : "schema",
	    "requestTime" : 35
	  },
	  "value" : [{
	      "name" : "MG",
	      "displayName" : "MG",
	      "type" : "Guid",
	      "facetable" : true,
	      "display" : false,
	      "ownerType" : ["PerfHourly", "ProtectionStatus", "Capacity_SMBUtilizationByHost", "Capacity_ArrayUtilization", "Capacity_SMBShareUtilization", "SQLAssessmentRecommendation", "Event", "ConfigurationChange", "ConfigurationAlert", "ADAssessmentRecommendation", "ConfigurationObject", "ConfigurationObjectProperty"]
	    }, {
	      "name" : "ManagementGroupName",
	      "displayName" : "ManagementGroupName",
	      "type" : "String",
	      "facetable" : true,
	      "display" : true,
	      "ownerType" : ["PerfHourly", "ProtectionStatus", "Event", "ConfigurationChange", "ConfigurationAlert", "W3CIISLog", "AlertHistory", "Recommendation", "Alert", "SecurityEvent", "UpdateAgent", "RequiredUpdate", "ConfigurationObject", "ConfigurationObjectProperty"]
	    }
	  ]
	}
```

В следующей таблице описаны имеющиеся свойства.

|**Свойство**|**Описание**|
|---|---|
|name|Имя поля.|
|displayName|Отображаемое имя поля.|
|type|Тип значения в поле.|
|facetable|Сочетание текущих свойств indexed, stored и facet.|
|display|Текущее свойство display. Если поле отображается при поиске, используется значение true.|
|ownerType|Используются только типы, принадлежащие подключенным IP-адресам.|


## Необязательные параметры
Ниже описаны дополнительные параметры, использовать которые необязательно.

### Выделение

Highlight — необязательный параметр, который сообщает поисковой подсистеме, что в ответ нужно добавить набор маркеров.

Эти маркеры указывают на начало и окончание выделяемого текста, который соответствует поисковому запросу. Начальный и конечный маркеры, которые поисковая система будет использовать для выделения текста, можно задавать.

**Пример поискового запроса**

```
	$savedSearchParametersJson =
	{
	  "top":150,
	  "highlight":{
	    "pre":"{[hl]}",
	    "post":"{[/hl]}"
	  },
	  "query":"*",
	  "start":"2015-02-04T21:03:29.231Z",
	  "end":"2015-02-11T21:03:29.231Z"
	}
	armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2014-10-10 $searchParametersJson
```

**Пример результата:**

```
	{
	    "TimeGenerated":
	    "2015-05-18T23:55:59Z", "Source":
	    "EventLog": "Application",
	    "Computer": "smokedturkey.net",
	    "EventCategory": 0,
	    "EventLevel":1,
	    "EventLevelName":
	    "Error"
	    "Manager ", "__metadata":
	    {
	        "Type": "Event",
	        "TimeGenerated": "2015-05-18T23:55:59Z",
	        "highlighting": {
	            "EventLevelName":
	            ["{[hl]}Error{[/hl]}"]
	        }
	    }
	}
```

Обратите внимание, что вышеуказанный результат содержит сообщение об ошибке с префиксом и постфиксом.

<!---HONumber=August15_HO6-->