---
title: Использование REST API оповещений Log Analytics в OMS
description: REST API оповещений Log Analytics позволяет создавать оповещения и управлять ими в Log Analytics — компоненте Operations Management Suite (OMS).  В этой статье приводятся сведения об интерфейсе API и примеры выполнения различных операций.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 628ad256-7181-4a0d-9e68-4ed60c0f3f04
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ed0ac6e2041ef503470f7317a5736deecd1d2b8f
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Создание правил генерации оповещений и управление ими в Log Analytics с помощью REST API
REST API оповещений в Log Analytics позволяет создавать оповещения и управлять ими в Operations Management Suite (OMS).  В этой статье приводятся сведения об интерфейсе API и примеры выполнения различных операций.

Для поиска в службе Log Analytics используется REST API категории RESTful — к нему можно получить доступ с помощью REST API Azure Resource Manager. В этом документе вы найдете примеры, когда доступ к API из командной строки PowerShell осуществляется через [ARMClient](https://github.com/projectkudu/ARMClient) — программу командной строки с открытым кодом, которая упрощает вызов API Azure Resource Manager. Использование ARMClient и PowerShell — это один из множества вариантов получения доступа к API поиска по службе Log Analytics. С помощью этих инструментов можно использовать API Azure Resource Manager с поддержкой REST для осуществления вызовов рабочих областей OMS и выполнения команд поиска в них. API будет выдавать результаты поиска в формате JSON, позволяя программно использовать результаты поиска различными способами.

## <a name="prerequisites"></a>предварительным требованиям
В настоящее время оповещения могут создаваться только с помощью сохраненного поиска в службе Log Analytics.  Дополнительные сведения см. в статье [REST API поиска в журналах](log-analytics-log-search-api.md).

## <a name="schedules"></a>Расписания
Сохраненный поиск может включать одно расписание или несколько. Расписание определяет, как часто выполняется поиск, а также интервал времени для определения условий.
У расписаний есть свойства, приведенные в таблице ниже.

| Свойство | ОПИСАНИЕ |
|:--- |:--- |
| Интервал |Насколько часто выполняется поиск. Измеряется в минутах. |
| QueryTimeSpan |Интервал времени для вычисления условий. Значение должно быть больше или равно значению свойства Interval. Измеряется в минутах. |
| Version (версия) |Используемая версия API.  Сейчас это свойство всегда должно иметь значение 1. |

Например, рассмотрим запрос событий с интервалом (свойство Interval) 15 минут и временным диапазоном (свойство TimeSpan) 30 минут. В этом случае запрос будет выполняться каждые 15 минут, а оповещение будет создаваться, если условие по-прежнему соблюдается по прошествии 30-минутного периода.

### <a name="retrieving-schedules"></a>Получение расписаний
Используйте метод Get для получения всех расписаний для сохраненного поиска.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

Чтобы получить конкретное расписание для сохраненного поискового запроса, используйте метод Get с идентификатором расписания.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

Ниже приведен пример ответа для расписания.

```json
{
    "value": [{
        "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
        "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
        "properties": {
            "Interval": 15,
            "QueryTimeSpan": 15
        }
    }]
}
```

### <a name="creating-a-schedule"></a>Создание расписания
Чтобы создать расписание, используйте метод Put с уникальным идентификатором расписания.  Обратите внимание, что нельзя использовать один и тот же идентификатор для двух расписаний, даже если они связаны с разными сохраненными поисковыми запросами.  При создании расписания в консоли OMS для идентификатора расписания создается GUID.

> [!NOTE]
> Имена всех сохраненных поисковых запросов, отчетов и действий, создаваемых с помощью API Log Analytics, должны быть в нижнем регистре.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>Изменение расписания
Чтобы изменить расписание, используйте метод Put с идентификатором существующего расписания для того же сохраненного поискового запроса.  Текст запроса должен содержать Etag расписания.

      $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' } }"
      armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>Удаление расписаний
Чтобы удалить расписание, используйте метод Delete с идентификатором расписания.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>Действия
Расписание может включать несколько действий. Действие может определять один выполняемый процесс или несколько (например, отправку почты или запуск модуля Runbook) или задавать пороговое значение, определяющее, когда результаты поиска отвечают некоторым условиям.  Некоторые действия будут определять оба элемента так, чтобы процессы выполнялись при достижении порогового значения.

У всех действий есть свойства, приведенные в таблице ниже.  Разные типы оповещений имеют различные дополнительные свойства, которые описаны ниже.

| Свойство | ОПИСАНИЕ |
|:--- |:--- |
| type |Тип действия.  Сейчас возможными значениями являются Alert и Webhook. |
| ИМЯ |Отображаемое имя оповещения. |
| Version (версия) |Используемая версия API.  Сейчас это свойство всегда должно иметь значение 1. |

### <a name="retrieving-actions"></a>Получение действий

> [!NOTE]
> С 23 апреля 2018 года все оповещения в рабочей области будут автоматически отображаться в Azure. Пользователь может добровольно инициировать отображение оповещений в Azure до 23 апреля 2018 года. Дополнительные сведения см. в разделе [Extend (copy) alerts from OMS portal into Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md) (Отображение (копирование) оповещений с портала OMS в Azure). Теперь действия пользователей, которые отображают оповещения в Azure, контролируются в группах действий Azure. Если рабочая область и ее оповещения перенесены в Azure, можно извлечь или добавить действия с помощью [API группы действий](https://docs.microsoft.com/en-us/rest/api/monitor/actiongroups).

Используйте метод Get для получения всех действий для расписания.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

Используйте метод Get с идентификатором действия для получения конкретного действия для расписания.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>Создание или изменение действий
Чтобы создать действие, используйте метод Put с идентификатором действия, уникальным для расписания.  При создании действия в консоли OMS для идентификатора действия отображается GUID.

> [!NOTE]
> Имена всех сохраненных поисковых запросов, отчетов и действий, создаваемых с помощью API Log Analytics, должны быть в нижнем регистре.

Чтобы изменить расписание, используйте метод Put с идентификатором существующего действия для того же сохраненного поискового запроса.  Текст запроса должен содержать Etag расписания.

Формат запроса для создания действия зависит от типа действия, поэтому эти примеры приведены в разделах ниже.

### <a name="deleting-actions"></a>Удаление действий

> [!NOTE]
> С 23 апреля 2018 года все оповещения в рабочей области будут автоматически отображаться в Azure. Пользователь может добровольно инициировать отображение оповещений в Azure до 23 апреля 2018 года. Дополнительные сведения см. в разделе [Extend (copy) alerts from OMS portal into Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md) (Отображение (копирование) оповещений с портала OMS в Azure). Теперь действия пользователей, которые отображают оповещения в Azure, контролируются в группах действий Azure. Если рабочая область и ее оповещения перенесены в Azure, можно извлечь или добавить действия с помощью [API группы действий](https://docs.microsoft.com/en-us/rest/api/monitor/actiongroups).

Для удаления действия используйте метод Delete с идентификатором действия.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Действия оповещений
Расписание должно включать одно-единственное действие оповещения.  Действия оповещений включают один или несколько разделов в таблице ниже.  Они более подробно описаны далее.

| Раздел | ОПИСАНИЕ | Использование |
|:--- |:--- |:--- |
| Threshold (Пороговое значение) |Условие для запуска действия.| Требуется для каждого оповещения до или после его отображения в Azure. |
| Уровень серьезности |Метка, используемая для классификации оповещения при его активации.| Требуется для каждого оповещения до или после его отображения в Azure. |
| Группы действий |Идентификаторы группы ActionGroup Azure, в которой указаны необходимые действия, например сообщения электронной почты, текстовые сообщения, голосовые вызовы, веб-перехватчики, модули runbook службы автоматизации, соединители ITSM и т. д.| Требуется после переноса оповещений в Azure.|
| Настройка действий|Изменение стандартного потока вывода для выбора действий из ActionGroup.| Необязательно для каждого оповещения, может использоваться после переноса оповещений в Azure. |
| EmailNotification |Отправка сообщения нескольким получателям. | Не требуется, если оповещения перенесены в Azure.|
| Исправление |Запуск модуля Runbook в службе автоматизации Azure для попытки исправления выявленной проблемы. |Не требуется, если оповещения перенесены в Azure.|
| Действия веб-перехватчика | Отправка данных оповещений в формате JSON в требуемую службу. |Не требуется, если оповещения перенесены в Azure.|

> [!NOTE]
> С 23 апреля 2018 года все оповещения в рабочей области будут автоматически отображаться в Azure. Пользователь может добровольно инициировать отображение оповещений в Azure до 23 апреля 2018 года. Дополнительные сведения см. в разделе [Extend (copy) alerts from OMS portal into Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md) (Отображение (копирование) оповещений с портала OMS в Azure).

#### <a name="thresholds"></a>Пороговые значения
Действие оповещения должно включать одно-единственное пороговое значение.  Когда результат сохраненного поиска соответствует пороговому значению в действии, связанном с этим поиском, запускаются другие процессы в этом действии.  Действие также может содержать только пороговое значение, чтобы его можно было использовать с действиями других типов, которые не содержат пороговых значений.

У пороговых значений есть свойства, приведенные в таблице ниже.

| Свойство | ОПИСАНИЕ |
|:--- |:--- |
| Оператор |Оператор для сравнения порогового значения. <br> gt — больше <br> lt = меньше чем |
| Значение |Пороговое значение. |

Например, рассмотрим запрос событий с интервалом (свойство Interval) 15 минут, временным диапазоном (свойство TimeSpan) 30 минут и пороговым значением больше 10. В этом случае запрос будет выполняться каждые 15 минут, а оповещение будет создаваться при возврате 10 событий, созданных за 30-минутный период.

Ниже приведен пример ответа для действия, содержащего только пороговое значение.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Version": 1
    }

Чтобы создать действие с пороговым значением для расписания, используйте метод Put с уникальным идентификатором действия.  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

Чтобы изменить действие с пороговым значением для расписания, используйте метод Put с идентификатором существующего действия.  Текст запроса должен содержать Etag действия.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="severity"></a>Уровень серьезности
Log Analytics позволяет классифицировать оповещения по категориям, чтобы упростить управление и рассмотрение. По уровню серьезности оповещение может быть информационным, предупреждающим и критическим. Эти уровни соответствуют нормализованной шкале серьезности оповещений Azure следующим образом.

|Уровень серьезности Log Analytics  |Уровень серьезности оповещений Azure  |
|---------|---------|
|критические ошибки. |Серьезность 0|
|Предупреждение |Серьезность 1|
|Информация | Серьезность 2|

Ниже приведен пример ответа для действия, содержащего только пороговое значение и серьезность. 

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Severity": "critical",
        "Version": 1    }

Чтобы создать действие для расписания с указанием серьезности, используйте метод Put с уникальным идентификатором действия.  

    $thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

Чтобы изменить действие серьезности для расписания, используйте метод Put с идентификатором существующего действия.  Текст запроса должен содержать Etag действия.

    $thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

#### <a name="action-groups"></a>Группы действий
Все оповещения в Azure используют группу действий в качестве механизма обработки действий по умолчанию. С помощью группы действий можно указать действия один раз, а затем связать группу действий для несколькими оповещениями в Azure. Это избавляет от необходимости повторно объявить одни и те же действия снова и снова. Группы действий поддерживает несколько действий, включая сообщения электронной почты, текстовые сообщения, голосовые вызовы, подключения ITSM, runbook службы автоматизации, универсальный код ресурса (URI) веб-перехватчика и многое другое. 

Теперь для пользователей, перенесших оповещения в Azure, расписание должно передавать сведения о группе действий вместе с пороговым значением, чтобы обеспечить создание оповещения. Сведения о сообщениях электронной почты, URL-адреса веб-перехватчиков, данные runbook службы автоматизации и другие действия должны быть определены в группе действий перед созданием оповещения. Можно создать [группу действий в Azure Monitor](../monitoring-and-diagnostics/monitoring-action-groups.md) на портале или использовать [API группы действий](https://docs.microsoft.com/en-us/rest/api/monitor/actiongroups).

Чтобы добавить связь группы действий с оповещением, укажите уникальный идентификатор Azure Resource Manager группы действий в определении оповещения. Ниже приведен наглядный пример.

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a descriptio here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ]
        },
        "Severity": "critical",
        "Version": 1
      },

Используйте метод Put с уникальным идентификатором действия, чтобы привязать уже существующую группу действий к расписанию.  Ниже приведен пример использования.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Чтобы изменить группу действий, связанную с расписанием, используйте метод Put с идентификатором существующего действия.  Текст запроса должен содержать Etag действия.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="customize-actions"></a>Настройка действий
По умолчанию действия используют стандартные шаблон и формат для уведомлений. Однако пользователь может настроить некоторые действия, даже если ими управляют группы действий. В настоящее время можно настроить тему сообщения электронной почты и полезные данные веб-перехватчика.

##### <a name="customize-e-mail-subject-for-action-group"></a>Настройка темы сообщения электронной почты для группы действий
По умолчанию используется следующая тема сообщения электронной почты для оповещений: "Alert Notification <AlertName> for <WorkspaceName>" (Уведомление <AlertName> для <WorkspaceName>). Однако ее можно настроить, указав определенные слова или теги, чтобы вы могли легко применять правила фильтрации в папке "Входящие". Сведения о пользовательском заголовке электронного сообщения необходимо отправлять вместе с данными ActionGroup, как показано в следующем примере.

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a descriptio here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ]
          "CustomEmailSubject": "Azure Alert fired"
        },
        "Severity": "critical",
        "Version": 1
      },

Используйте метод Put с уникальным идентификатором действия, чтобы привязать уже существующую группу действий с измененными параметрами к расписанию.  Ниже приведен пример использования.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Чтобы изменить группу действий, связанную с расписанием, используйте метод Put с идентификатором существующего действия.  Текст запроса должен содержать Etag действия.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

##### <a name="customize-webhook-payload-for-action-group"></a>Настройка полезных данных веб-перехватчика для группы действий
По умолчанию веб-перехватчик, отправляемый группой действий в Log Analytics, имеет фиксированную структуру. Но с помощью специальных поддерживаемых переменных можно настроить полезные данные JSON, чтобы соблюсти требования конечной точки веб-перехватчика. Дополнительные сведения см. в статье [Действия веб-перехватчика для правил оповещений журнала](../monitoring-and-diagnostics/monitor-alerts-unified-log-webhook.md). 

Сведения о пользовательском веб-перехватчике необходимо отправлять вместе с данными ActionGroup. Они будут применены для всех универсальных кодов ресурса (URI) веб-перехватчиков, указанных в группе действий, как показано в следующем примере.

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a descriptio here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ]
          "CustomWebhookPayload": "{\"field1\":\"value1\",\"field2\":\"value2\"}",
          "CustomEmailSubject": "Azure Alert fired"
        },
        "Severity": "critical",
        "Version": 1
      },

Используйте метод Put с уникальным идентификатором действия, чтобы привязать уже существующую группу действий с измененными параметрами к расписанию.  Ниже приведен пример использования.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Чтобы изменить группу действий, связанную с расписанием, используйте метод Put с идентификатором существующего действия.  Текст запроса должен содержать Etag действия.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="email-notification"></a>Уведомление по электронной почте
Уведомления отправляются для одного или нескольких получателей по электронной почте.  Уведомления могут включать свойства, приведенные в таблице ниже.

> [!NOTE]
> С 23 апреля 2018 года все оповещения в рабочей области будут автоматически отображаться в Azure. Пользователь может добровольно инициировать отображение оповещений в Azure до 23 апреля 2018 года. Дополнительные сведения см. в разделе [Extend (copy) alerts from OMS portal into Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md) (Отображение (копирование) оповещений с портала OMS в Azure). Теперь действия (например, оповещение по электронной почте) пользователей, которые отображают оповещения в Azure, контролируются в группах действий Azure. Если рабочая область и ее оповещения перенесены в Azure, можно извлечь или добавить действия с помощью [API группы действий](https://docs.microsoft.com/en-us/rest/api/monitor/actiongroups).
   

| Свойство | ОПИСАНИЕ |
|:--- |:--- |
| Recipients |Список адресов электронной почты. |
| Субъект |Тема сообщения электронной почты. |
| Вложение |Вложения пока не поддерживаются, поэтому это свойство всегда будет иметь значение None. |

Ниже приведен пример ответа для действия уведомления по электронной почте с пороговым значением.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My email action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "EmailNotification": {
            "Recipients": [
                "recipient1@contoso.com",
                "recipient2@contoso.com"
            ],
            "Subject": "This is the subject",
            "Attachment": "None"
        },
        "Version": 1
    }

Чтобы создать действие почты для расписания, используйте метод Put с уникальным идентификатором действия.  В следующем примере создается уведомление по электронной почте с пороговым значением, которое отправляется в случае, если результаты сохраненного поиска превышают пороговое значение.

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

Чтобы изменить действие почты для расписания, используйте метод Put с идентификатором существующего действия.  Текст запроса должен содержать Etag действия.

    $emailJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

#### <a name="remediation-actions"></a>Действия исправления
Служба исправлений запускает модуль Runbook в службе автоматизации Azure, который пытается устранить проблему, указанную в оповещении.  Вам потребуется создать объект webhook для модуля Runbook, используемого в действии исправления, а затем указать его универсальный код ресурса (URI) в свойстве WebhookUri.  При создании этого действия с помощью консоли OMS новый объект webhook для модуля Runbook создается автоматически.

> [!NOTE]
> С 23 апреля 2018 года все оповещения в рабочей области будут автоматически отображаться в Azure. Пользователь может добровольно инициировать отображение оповещений в Azure до 23 апреля 2018 года. Дополнительные сведения см. в разделе [Extend (copy) alerts from OMS portal into Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md) (Отображение (копирование) оповещений с портала OMS в Azure). Теперь действия (например, исправление с помощью runbook) пользователей, которые отображают оповещения в Azure, контролируются в группах действий Azure. Если рабочая область и ее оповещения перенесены в Azure, можно извлечь или добавить действия с помощью [API группы действий](https://docs.microsoft.com/en-us/rest/api/monitor/actiongroups).

Исправления могут включать свойства, приведенные в таблице ниже.

| Свойство | ОПИСАНИЕ |
|:--- |:--- |
| RunbookName |Имя модуля Runbook. Оно должно соответствовать имени опубликованного модуля Runbook в учетной записи автоматизации, настроенной в решении автоматизации в рабочей области OMS. |
| WebhookUri |Универсальный код ресурса (URI) объекта webhook. |
| Expiry |Дата и время окончания срока действия объекта webhook.  Если объект webhook не имеет срока действия, это может быть любая допустимая дата в будущем. |

Ниже приведен пример ответа для действия исправления с пороговым значением.

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My remediation action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Remediation": {
            "RunbookName": "My-Runbook",
            "WebhookUri": "https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d",
            "Expiry": "2018-02-25T18:27:20"
            },
        "Version": 1
    }

Чтобы создать действие исправления для расписания, используйте метод Put с уникальным идентификатором действия.  В следующем примере создается исправление с пороговым значением для запуска модуля Runbook в случае, если результаты сохраненного поиска превышают пороговое значение.

    $remediateJson = "{'properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

Чтобы изменить действие исправления для расписания, используйте метод Put с идентификатором существующего действия.  Текст запроса должен содержать Etag действия.

    $remediateJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

#### <a name="example"></a>Пример
Ниже приведен полный пример создания оповещения по электронной почте.  Создается новое расписание, а также действие, содержащее пороговое значение и сообщение электронной почты.

    $subscriptionId = "3d56705e-5b26-5bcc-9368-dbc8d2fafbfc"
    $resourceGroup  = "MyResourceGroup"    
    $workspaceName    = "MyWorkspace"
    $searchId       = "MySearch"
    $scheduleId     = "MySchedule"
    $thresholdId    = "MyThreshold"
    $actionId       = "MyEmailAction"

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/savedSearches/$searchId/schedules/$scheduleId/?api-version=2015-03-20 $scheduleJson

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Severity':'Warning', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/savedSearches/$searchId/schedules/$scheduleId/actions/$actionId/?api-version=2015-03-20 $emailJson

#### <a name="webhook-actions"></a>Действия webhook
Действия webhook запускают процесс путем вызова URL-адреса и, при необходимости, предоставления полезных данных для отправки.  Они похожи на действия исправления, но предназначены для объектов webhook, которые могут вызывать процессы, отличные от модулей Runbook службы автоматизации Azure.  Они также включают дополнительную возможность предоставления полезных данных, доставляемых в удаленный процесс.

> [!NOTE]
> С 23 апреля 2018 года все оповещения в рабочей области будут автоматически отображаться в Azure. Пользователь может добровольно инициировать отображение оповещений в Azure до 23 апреля 2018 года. Дополнительные сведения см. в разделе [Extend (copy) alerts from OMS portal into Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md) (Отображение (копирование) оповещений с портала OMS в Azure). Теперь действия (например, веб-перехватчики) пользователей, которые отображают оповещения в Azure, контролируются в группах действий Azure. Если рабочая область и ее оповещения перенесены в Azure, можно извлечь или добавить действия с помощью [API группы действий](https://docs.microsoft.com/en-us/rest/api/monitor/actiongroups).


Действиям webhook не назначается пороговое значение, однако их необходимо добавлять в расписание, которое включает действие оповещения с пороговым значением.  

Ниже приведен пример ответа для действия webhook и соответствующее действие оповещения с пороговым значением.

    {
        "__metadata": {},
        "value": [
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/72884702-acf9-4653-bb67-f42436b342b4",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"",
                "properties": {
                    "Type": "Webhook",
                    "Name": "My Webhook Action",
                    "WebhookUri": "https://oaaswebhookdf.cloudapp.net/webhooks?token=VfkYTIlpk%2fc%2bJBP",
                    "CustomPayload": "{\"fielld1\":\"value1\",\"field2\":\"value2\"}",
                    "Version": 1
                }
            },
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/90a27cf8-71b7-4df2-b04f-54ed01f1e4b6",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.565204Z'\"",
                "properties": {
                    "Type": "Alert",
                    "Name": "Threshold for my webhook action",
                    "Threshold": {
                        "Operator": "gt",
                        "Value": 10
                    },
                    "Version": 1
                }
            }
        ]
    }

##### <a name="create-or-edit-a-webhook-action"></a>Создание или изменение действий webhook
Чтобы создать действие webhook для расписания, используйте метод Put с уникальным идентификатором действия.  В следующем примере создается действие webhook и запускается действие оповещения с пороговым значением, если результаты сохраненного поиска превышают пороговое значение.

    $thresholdAction = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdAction

    $webhookAction = "{'properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

Чтобы изменить действие webhook для расписания, используйте метод Put с идентификатором существующего действия.  Текст запроса должен содержать Etag действия.

    $webhookAction = "{'etag': 'W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"','properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction


## <a name="next-steps"></a>Дополнительная информация
* Используйте [REST API для выполнения поиска в журналах](log-analytics-log-search-api.md) в службе Log Analytics.
* Узнайте больше об [оповещениях журналов в службе оповещений Azure](../monitoring-and-diagnostics/monitor-alerts-unified-log.md).

