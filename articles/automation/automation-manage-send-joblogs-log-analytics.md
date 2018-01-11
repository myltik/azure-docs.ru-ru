---
title: "Пересылка данных задания службы автоматизации Azure в OMS Log Analytics | Документация Майкрософт"
description: "В этой статье показано, как отправлять состояние задания Runbook и потоки заданий в Log Analytics (Microsoft Operations Management Suite) для получения дополнительных сведений и возможностей управления."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: tysonn
ms.assetid: c12724c6-01a9-4b55-80ae-d8b7b99bd436
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/31/2017
ms.author: magoedte
ms.openlocfilehash: 0319a7b9248dec9d7cdabba9c18a25463d94284b
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2017
---
# <a name="forward-job-status-and-job-streams-from-automation-to-log-analytics-oms"></a>Пересылка состояния задания и потоков заданий из службы автоматизации в Log Analytics (OMS)
Служба автоматизации может отправлять состояние задания Runbook и потоки заданий в рабочую область Log Analytics в Microsoft Operations Management Suite (OMS). На портале Azure или с помощью PowerShell можно просмотреть журналы заданий и потоки заданий для отдельных заданий. Это дает возможность выполнять простые исследования. С помощью Log Analytics теперь можно:

* Получите четкое представление о заданиях автоматизации.
* Триггер, электронной почты или предупреждение в зависимости от состояния задания runbook (например, сбой или приостановлена).
* Напишите сложные запросы через потоков работ.
* Корреляция задания нескольких учетных записей автоматизации.
* Визуализация историю задания со временем.

## <a name="prerequisites-and-deployment-considerations"></a>Предварительные требования и рекомендации по развертыванию
Чтобы начать отправку журналов службы автоматизации в Log Analytics, необходимо следующее.

* Выпуск за ноябрь 2016 года или более поздний выпуск [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) (вер. 2.3.0).
* Рабочая область Log Analytics. Дополнительные сведения см. в статье [Начало работы с Log Analytics](../log-analytics/log-analytics-get-started.md). 
* ResourceId для вашей учетной записи службы автоматизации Azure.


Чтобы найти ResourceId для вашей учетной записи службы автоматизации Azure:

```powershell-interactive
# Find the ResourceId for the Automation Account
Find-AzureRmResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Чтобы найти ResourceId для рабочей области аналитики журналов, выполните следующие команды PowerShell:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Если имеется несколько учетных записей автоматизации или найти рабочие области, в выходных данных выше команды *имя* необходимо настроить и скопируйте значение *ResourceId*.

Если вам нужно найти значение *Name* для своей учетной записи службы автоматизации, на портале Azure выберите свою учетную запись службы автоматизации в колонке **Учетная запись службы автоматизации** и выберите **Все параметры**. В колонке **Все параметры** в разделе **Параметры учетной записи** выберите пункт **Свойства**.  В колонке **Свойства** вы увидите нужные значения.<br> ![Свойства учетной записи службы автоматизации](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="set-up-integration-with-log-analytics"></a>Настройка интеграции с Log Analytics

1. На своем компьютере запустите **Windows PowerShell** на **начальном** экране.
2. Выполните следующие команды PowerShell и изменить значение для `[your resource id]` и `[resource id of the log analytics workspace]` со значениями на предыдущем шаге.

   ```powershell-interactive
   $workspaceId = "[resource id of the log analytics workspace]"
   $automationAccountId = "[resource id of your automation account]"

   Set-AzureRmDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled $true
   ```

После выполнения этого скрипта, вы увидите записи в службе анализа журналов в течение 10 минут новый JobLogs или JobStreams производится запись.

Чтобы просмотреть журналы, выполните следующий запрос в поиске по журналам Log Analytics: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION""`

### <a name="verify-configuration"></a>Проверка конфигурации
Чтобы убедиться, что ваша учетная запись автоматизации отправляет журналы рабочей области аналитики журналов, проверьте правильность настройки диагностики в учетной записи автоматизации с помощью указанной ниже команды PowerShell:

```powershell-interactive
Get-AzureRmDiagnosticSetting -ResourceId $automationAccountId
```

В выходных данных убедитесь в следующем.
+ В разделе *журналы*, значение для *включено* — *True*.
+ Значение *ИД рабочей области* равно ResourceId рабочей области аналитики журналов.

## <a name="log-analytics-records"></a>Записи Log Analytics

Диагностика службы автоматизации Azure создает два типа записей в службе анализа журналов и помечаются как **AzureDiagnostics**. Следующие запросы используют язык обновленный запрос к службе анализа журналов. Сведения на стандартные запросы между языка устаревших запросов и новый язык запросов для анализа журналов Azure [прежних версий, чтобы новый язык запросов анализа журналов Azure памятку](https://docs.loganalytics.io/docs/Learn/References/Legacy-to-new-to-Azure-Log-Analytics-Language)

### <a name="job-logs"></a>Журналы заданий
| Свойство | ОПИСАНИЕ |
| --- | --- |
| TimeGenerated |Дата и время выполнения задания Runbook. |
| RunbookName_s |Имя Runbook. |
| Caller_s |Сторона, инициировавшая операцию. Допустимые значения: электронный адрес или system для запланированных заданий. |
| Tenant_g | GUID, идентифицирующий клиента для вызывающего объекта. |
| JobId_g |GUID, представляющий собой идентификатор задания Runbook. |
| ResultType |Состояние задания Runbook. Возможные значения:<br>Новое<br>Started<br>- Остановлена<br>Приостановлено<br>Сбой<br>Завершено |
| Категория | Классификация типа данных. Для службы автоматизации значением является JobLogs. |
| OperationName | Указывает тип операции, выполняемой в Azure. Для службы автоматизации значением является Job. |
| Ресурс | Имя учетной записи службы автоматизации |
| SourceSystem | Способ сбора данных в Log Analytics. Всегда имеет значение *Azure* для системы диагностики Azure. |
| ResultDescription |Описывает состояние результата задания Runbook. Возможные значения:<br>— Job is started;<br>— Job Failed;<br>- Job Completed. |
| CorrelationId |GUID, представляющий собой идентификатор корреляции задания Runbook. |
| ResourceId |Указывает идентификатор ресурса учетной записи службы автоматизации Azure для модуля Runbook. |
| SubscriptionId | Идентификатор подписки Azure (GUID) для учетной записи службы автоматизации. |
| ResourceGroup | Имя группы ресурсов для учетной записи службы автоматизации. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |


### <a name="job-streams"></a>Потоки заданий
| Свойство | ОПИСАНИЕ |
| --- | --- |
| TimeGenerated |Дата и время выполнения задания Runbook. |
| RunbookName_s |Имя Runbook. |
| Caller_s |Сторона, инициировавшая операцию. Допустимые значения: электронный адрес или system для запланированных заданий. |
| StreamType_s |Тип потока задания. Возможные значения:<br>ход выполнения<br>Выходные данные<br>Предупреждение<br>error<br>debug<br>- Verbose. |
| Tenant_g | GUID, идентифицирующий клиента для вызывающего объекта. |
| JobId_g |GUID, представляющий собой идентификатор задания Runbook. |
| ResultType |Состояние задания Runbook. Возможные значения:<br>- In Progress |
| Категория | Классификация типа данных. Для службы автоматизации значением является JobStreams. |
| OperationName | Указывает тип операции, выполняемой в Azure. Для службы автоматизации значением является Job. |
| Ресурс | Имя учетной записи службы автоматизации |
| SourceSystem | Способ сбора данных в Log Analytics. Всегда имеет значение *Azure* для системы диагностики Azure. |
| ResultDescription |Включает в себя выходной поток из Runbook. |
| CorrelationId |GUID, представляющий собой идентификатор корреляции задания Runbook. |
| ResourceId |Указывает идентификатор ресурса учетной записи службы автоматизации Azure для модуля Runbook. |
| SubscriptionId | Идентификатор подписки Azure (GUID) для учетной записи службы автоматизации. |
| ResourceGroup | Имя группы ресурсов для учетной записи службы автоматизации. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |

## <a name="viewing-automation-logs-in-log-analytics"></a>Просмотр журналов службы автоматизации в Log Analytics
Теперь, когда вы начали отправки журналов заданий автоматизации для анализа журналов, давайте посмотрим, можно сделать с помощью этих журналов в службе анализа журналов.

Чтобы просмотреть журналы, выполните следующий запрос: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Отправка электронного сообщения при сбое или приостановке задания Runbook
Один top клиента запрашивает возможности отправлять сообщения электронной почты или текстовый при возникновении неполадок с задания runbook.   

Чтобы создать правило генерации оповещений, начните с создания поиска в журнале записей заданий Runbook, которые должны вызывать оповещение. Щелкните кнопку **Оповещение**, чтобы создать и настроить правило генерации оповещений.

1. На странице обзора Log Analytics щелкните **Поиск по журналу**.
2. Создайте запрос поиска журнала для оповещения путем ввода следующего условия поиска в поле запроса: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")` задачи можно группировать по RunbookName с помощью:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Если настраивается журналы из более чем одной учетной записи службы автоматизации или подписки в рабочей области, можно сгруппировать оповещения, подписки и учетной записи автоматизации. В поле поиска JobLogs можно найти имя учетной записи автоматизации.
1. Чтобы открыть экран **Добавить правило оповещения**, щелкните **Оповещение** в верхней части страницы. Дополнительные сведения о параметрах для настройки оповещения см. в разделе [Правила оповещения](../log-analytics/log-analytics-alerts.md#alert-rules).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Поиск всех заданий, завершенных с ошибками
Помимо оповещений о сбоях можно узнать, когда задание Runbook вызывает устранимую ошибку. В таких случаях PowerShell создает поток сообщений об ошибках, но устранимые ошибки не вызывают задания для приостановки или ошибкой.    

1. В рабочей области Log Analytics щелкните **Поиск по журналу**.
2. В поле запроса введите `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` и нажмите кнопку **поиска** кнопку.

### <a name="view-job-streams-for-a-job"></a>Просмотр потоков заданий для задания
При отладке задания можно также рассмотреть потоков работ. Приведенный ниже запрос отображает все потоки для одного задания с GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0.   

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Просмотр хронологии состояния задания
Наконец, вам может понадобиться визуализировать журнал задания по прошествии времени. Для поиска для поиска состояния заданий по прошествии времени можно использовать приведенный ниже запрос.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`  
<br> ![Диаграмма хронологии состояния задания в OMS](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="summary"></a>Сводка
Отправляя состояние задания службы автоматизации и поток данных в Log Analytics, можно лучше понять, в каком состоянии находятся ваши задания службы автоматизации. Для этого вы можете:
+ Настройка предупреждений для уведомлений при возникновении проблемы.
+ с помощью пользовательских представлений и поисковых запросов визуализировать результаты модуля Runbook, состояние задания Runbook и другие связанные ключевые индикаторы или метрики.  

Log Analytics предоставляет больший оперативный контроль над заданиями службы автоматизации и позволяет быстрее устранять инциденты.  

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о способах создания разных поисковые запросы и просмотрите журналы заданий службы автоматизации с помощью аналитики журналов см. в разделе [входа поиска аналитики журналов](../log-analytics/log-analytics-log-searches.md).
* Чтобы понять, как создать и получить выходные данные и сообщения об ошибках из модулей Runbook, в разделе [Runbook, выходные и сообщения](automation-runbook-output-and-messages.md).
* Дополнительные сведения о выполнении модулей Runbook, отслеживании заданий модуля Runbook и других технических деталях см. в статье [Выполнение модуля Runbook в службе автоматизации Azure](automation-runbook-execution.md).
* Дополнительные сведения о аналитики журнала OMS и коллекцию источников данных см. в разделе [Azure сбор данных из хранилища в обзоре анализа журналов](../log-analytics/log-analytics-azure-storage.md).
