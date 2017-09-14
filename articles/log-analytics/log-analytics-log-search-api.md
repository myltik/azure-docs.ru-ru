---
title: "REST API поиска по журналам в Log Analytics | Документация Майкрософт"
description: "Данное руководство дает базовые знания о том, как использовать API REST поиска по службе Log Analytics в Operational Insights Suite (OMS), и содержит примеры использования команд."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.assetid: b4e9ebe8-80f0-418e-a855-de7954668df7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: 5b51c6fcc69c8dff6579a1a1221e88822eccc1a3
ms.contentlocale: ru-ru
ms.lasthandoff: 09/07/2017

---
# <a name="log-analytics-log-search-rest-api"></a>API REST поиска по журналам Log Analytics
В этом базовом руководстве представлены примеры использования API REST поиска по службе Log Analytics. Log Analytics входит в пакет Operations Management Suite (OMS).

> [!NOTE]
> Если ваша рабочая область переведена на [новый язык запросов Log Analytics](log-analytics-log-search-upgrade.md), см. [документацию по новой версии API поиска по журналам](https://dev.loganalytics.io/).

> [!NOTE]
> Компонент Log Analytics раньше назывался Operational Insights, поэтому именно такое имя используется в поставщиках ресурсов.
>
>

## <a name="overview-of-the-log-search-rest-api"></a>Обзор API REST поиска по журналу
Для поиска по службе Log Analytics используется API REST категории RESTful — к нему можно получить доступ с помощью API Azure Resource Manager. В этой статье вы найдете примеры, когда доступ к API осуществляется через [ARMClient](https://github.com/projectkudu/ARMClient) — инструмент командной строки с открытым кодом, который упрощает вызов API Azure Resource Manager. Использование ARMClient — это один из множества вариантов получения доступа к API поиска по службе Log Analytics. Другой вариант — использовать модуль Azure PowerShell для Operational Insights, включающий командлеты для доступа к службе поиска. С помощью этих инструментов можно использовать API Azure Resource Manager для осуществления вызовов рабочих областей OMS и выполнения команд поиска в них. API выдает результаты поиска в формате JSON, позволяя программно использовать результаты поиска различными способами.

Azure Resource Manager можно использовать через [библиотеку для .NET](https://msdn.microsoft.com/library/azure/dn910477.aspx), а также через [REST API](https://msdn.microsoft.com/library/azure/mt163658.aspx). Дополнительные сведения см. на связанных веб-страницах.

> [!NOTE]
> При использовании команды агрегирования, например `|measure count()` или `distinct`, каждый вызов службы поиска может возвращать не более 500 000 записей. Операции поиска, в которых не используется команда агрегирования, возвращают не более 5000 записей.
>
>

## <a name="basic-log-analytics-search-rest-api-tutorial"></a>Учебник по основам API REST поиска по службе Log Analytics
### <a name="to-use-armclient"></a>Использование ARMClient
1. Установите [Chocolatey](https://chocolatey.org/), который является диспетчером пакетов с открытым исходным кодом для Windows. Откройте окно командной строки от имени администратора, а затем выполните следующую команду:

    ```
    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
    ```
2. Установите ARMClient, выполнив следующую команду:

    ```
    choco install armclient
    ```

### <a name="to-perform-a-search-using-armclient"></a>Выполнение поиска с помощью ARMClient
1. Войдите в учетную запись Майкрософт или в рабочую или учебную учетную запись:

    ```
    armclient login
    ```

    При успешном выполнении входа появится список всех подписок, привязанных к данной учетной записи:

    ```
    PS C:\Users\SampleUserName> armclient login
    Welcome YourEmail@ORG.com (Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz)
    User: YourEmail@ORG.com, Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz (Example org)
    There are 3 subscriptions
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 1)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 2)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 3)
    ```
2. Получение рабочих областей Operations Management Suite Workspaces:

    ```
    armclient get /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces?api-version=2015-03-20
    ```

    При успешном вызове Get отобразятся все рабочие области, привязанные к подписке:

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
3. Создание своей переменной поиска:

    ```
    $mySearch = "{ 'top':150, 'query':'Error'}";
    ```
4. Поиск с помощью новой переменной поиска:

    ```
    armclient post /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{WORKSPACE NAME}/search?api-version=2015-03-20 $mySearch
    ```

## <a name="log-analytics-search-rest-api-reference-examples"></a>Примеры API REST поиска по службе Log Analytics
На следующих примерах показано, как можно использовать API поиска.

### <a name="search---actionread"></a>Поиск — Действие/Чтение
**Пример URL:**

```
    /subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search?api-version=2015-03-20
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
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```
В следующей таблице описаны имеющиеся свойства.

| **Свойство** | **Описание** |
| --- | --- |
| top |Максимальное количество отображаемых результатов. |
| highlight |Содержит параметры pre и post, которые обычно используются для выделения соответствующих запросу полей |
| pre |Добавляет заданную строку перед соответствующими запросу полями. |
| post |Добавляет заданную строку после соответствующих запросу полей. |
| запрос |Поисковый запрос, используемый для сбора и отображения результатов. |
| start |Начало периода, за который необходимо собрать результаты. |
| end |Окончание периода, за который необходимо собрать результаты. |

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

### <a name="searchid---actionread"></a>Поиск/{ID} - Действие/Чтение
**Запрос содержимого сохраненных результатов поиска:**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search/{SearchId}?api-version=2015-03-20
```

> [!NOTE]
> Если результаты поиска выдаются со статусом "Ожидание", данный API можно использовать для запроса обновленных результатов. Через 6 минут результат поиска удаляется из кэша и выдается статус HTTP Gone. Если по первому поисковому запросу сразу выдаются результаты с состоянием "Успешно", они не добавляются в кэш, в результате чего при запросе этот API выдает статус HTTP Gone. Содержимое результатов HTTP 200 представляется в том же формате, что и у первоначального поискового запроса, но с обновленными значениями.
>
>

### <a name="saved-searches"></a>Сохраненные поисковые запросы
**Запрос списка сохраненных поисковых запросов:**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/savedSearches?api-version=2015-03-20
```

Поддерживаемые методы: GET, PUT и DELETE.

Поддерживаемые методы сбора данных: GET

В следующей таблице описаны имеющиеся свойства.

| Свойство | Описание |
| --- | --- |
| Идентификатор |Уникальный идентификатор. |
| Etag |**Необходимо для обновления**. Обновляется сервером при каждой операции записи. Для обновления требуется, чтобы в качестве значения использовалось текущее сохраненное значение или символ *. Для старых или недопустимых значений возвращается 409. |
| properties.query |**Обязательный**. Поисковый запрос. |
| properties.displayName |**Обязательный**. Определяемое пользователем отображаемое имя запроса. |
| properties.category |**Обязательный**. Определяемая пользователем категория запроса. |

> [!NOTE]
> Сейчас, когда поступают запросы на сохраненные поисковые запросы в рабочей области, API поиска по службе Log Analytics возвращает сохраненные поисковые запросы, созданные пользователем. Сохраненные поисковые запросы, созданные решениями, не возвращаются.
>
>

### <a name="create-saved-searches"></a>Создание сохраненных поисковых запросов
**Запрос:**

```
    $savedSearchParametersJson = "{'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisismyid?api-version=2015-03-20 $savedSearchParametersJson
```

> [!NOTE]
> Имена всех сохраненных поисковых запросов, отчетов и действий, создаваемых с помощью API Log Analytics, должны быть в нижнем регистре.

### <a name="delete-saved-searches"></a>Удаление сохраненных поисковых запросов
**Запрос:**

```
    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20
```

### <a name="update-saved-searches"></a>Изменение сохраненных поисковых запросов
 **Запрос:**

```
    $savedSearchParametersJson = "{'etag': 'W/`"datetime\'2015-04-16T23%3A35%3A35.3182423Z\'`"', 'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20 $savedSearchParametersJson
```

### <a name="metadata---json-only"></a>Метаданные (только JSON)
Вот способ узнать, какие поля есть в журналах данных, собранных в рабочей области. Этот способ подходит для всех типов журналов. Например, если вы хотите узнать, есть ли в типе события поле с именем компьютера, это можно сделать с помощью запроса.

**Запрос полей**

```
    armclient get /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/schema?api-version=2015-03-20
```

**Ответ.**

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

| **Свойство** | **Описание** |
| --- | --- |
| name |Имя поля. |
| displayName |Отображаемое имя поля. |
| type |Тип значения в поле. |
| facetable |Сочетание текущих свойств indexed, stored и facet. |
| display |Текущее свойство display. Если поле отображается при поиске, используется значение true. |
| ownerType |Используются только типы, принадлежащие подключенным IP-адресам. |

## <a name="optional-parameters"></a>Необязательные параметры
Ниже описаны дополнительные параметры, использовать которые необязательно.

### <a name="highlighting"></a>Выделение
Highlight — необязательный параметр, который сообщает поисковой подсистеме, что в ответ нужно добавить набор маркеров.

Эти маркеры указывают на начало и окончание выделяемого текста, который соответствует поисковому запросу.
Начальный и конечный маркеры, которые поисковая система будет использовать для выделения текста, можно задавать.

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
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
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

Обратите внимание, что предыдущий результат содержит сообщение об ошибке с префиксом и постфиксом.

## <a name="computer-groups"></a>Группы компьютеров
Группы компьютеров — специальные сохраненные поисковые запросы, возвращающие набор компьютеров.  Группу компьютеров можно использовать в других запросах, чтобы ограничить результаты компьютерами, которые входят в группу.  Группа компьютеров реализуется как сохраненный поисковый запрос с тегом Group (Группа) и значением Computer (Компьютер).

Ниже представлен пример ответа для группы компьютеров.

```
    "etag": "W/\"datetime'2016-04-01T13%3A38%3A04.7763203Z'\"",
    "properties": {
        "Category": "My Computer Groups",
        "DisplayName": "My Computer Group",
        "Query": "srv* | Distinct Computer",
        "Tags": [{
            "Name": "Group",
            "Value": "Computer"
          }],
    "Version": 1
    }
```

### <a name="retrieving-computer-groups"></a>Извлечение группы компьютеров
Для извлечения группы компьютеров используйте метод Get с идентификатором группы.

```
armclient get /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Group ID}`?api-version=2015-03-20
```

### <a name="creating-or-updating-a-computer-group"></a>Создание или обновление группы компьютеров
Для создания группы компьютеров используйте метод Put с уникальным идентификатором сохраненного поискового запроса. Если используется существующий идентификатор группы компьютеров, он будет изменен. Если группа компьютеров создается на портале Log Analytics, идентификатор создается из группы и имени.

Для правильной работы запрос, используемый для определения группы, должен возвращать набор компьютеров для группы.  Для получения правильных данных рекомендуется заканчивать запрос строкой `| Distinct Computer`.

Для того чтобы поиск классифицировался как группа компьютеров, определение сохраненного поискового запроса должно включать тег Group (Группа) со значением Computer (Компьютер).

```
    $etag=Get-Date -Format yyyy-MM-ddThh:mm:ss.msZ
    $groupName="My Computer Group"
    $groupQuery = "Computer=srv* | Distinct Computer"
    $groupCategory="My Computer Groups"
    $groupID = "My Computer Groups | My Computer Group"

    $groupJson = "{'etag': 'W/`"datetime\'" + $etag + "\'`"', 'properties': { 'Category': '" + $groupCategory + "', 'DisplayName':'"  + $groupName + "', 'Query':'" + $groupQuery + "', 'Tags': [{'Name': 'Group', 'Value': 'Computer'}], 'Version':'1'  }"

    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20 $groupJson
```

### <a name="deleting-computer-groups"></a>Удаление группы компьютеров
Для удаления группы компьютеров используйте метод Delete с идентификатором группы.

```
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20
```


## <a name="next-steps"></a>Дальнейшие действия
* Узнайте больше о [запросах поиска по журналу](log-analytics-log-searches.md) для создания запросов с использованием настраиваемых полей в качестве условия.

