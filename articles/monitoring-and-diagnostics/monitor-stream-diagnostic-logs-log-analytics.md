---
title: Потоковая передача журналов диагностики Azure в Log Analytics | Документация Майкрософт
description: Узнайте, как настроить потоковую передачу журналов диагностики Azure в рабочую область Log Analytics.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: johnkem
ms.openlocfilehash: 517ce3547f471dd1b40c79b2f087b02ad7f51b85
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics"></a>Потоковая передача журналов диагностики Azure в Log Analytics
Можно настроить потоковую передачу **[журналов диагностики Azure](monitoring-overview-of-diagnostic-logs.md)** почти в реальном времени в Azure Log Analytics с помощью портала, командлетов PowerShell или Azure CLI.

## <a name="what-you-can-do-with-diagnostics-logs-in-log-analytics"></a>Что можно делать с журналами диагностики в Log Analytics

Azure Log Analytics — это гибкий инструмент поиска по журналам и аналитики, который позволяет лучше понять необработанные данные журналов, созданные для ресурсов Azure. Ниже перечислены некоторые возможности:

* **Поиск по журналам**. Создание расширенных запросов для данных журналов, сопоставление журналов из разных источников и даже создание диаграмм, которые можно закрепить на панели мониторинга Azure.
* **Предупреждения**. Настройка получения электронного сообщения или вызов веб-перехватчика, когда одно или несколько событий соответствуют определенному запросу.
* **Решения**. Использование готовых представлений и панелей мониторинга, которые позволяют сразу же получить представление о данных журналов.
* **Расширенная аналитика**. Применение машинного обучения и алгоритмов сопоставления шаблонов для выявления возможных проблем, обнаруженных в журналах.

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics"></a>Включение потоковой передачи журналов диагностики в Log Analytics
Потоковую передачу журналов диагностики можно включить программно, через портал или с помощью [REST API Azure Monitor](https://docs.microsoft.com/rest/api/monitor/servicediagnosticsettings). В любом случае создается параметр диагностики, в котором указывается рабочая область Log Analytics, а также категории журналов и метрики, которые требуется отправить в эту рабочую область. Категория **журналов диагностики** — это тип журналов, которые может предоставить ресурс.

Рабочая область Log Analytics не обязательно должна находиться в той же подписке, в которой находится ресурс, выдающий журналы, если у пользователя, настраивающего параметр, имеется соответствующий доступ RBAC к обеим подпискам.

## <a name="stream-diagnostic-logs-using-the-portal"></a>Потоковая передача журналов диагностики с помощью портала
1. На портале перейдите к Azure Monitor и щелкните **Параметры диагностики**.

    ![Раздел мониторинга Azure Monitor](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-blade.png)

2. При необходимости отфильтруйте список по группе или типу ресурса, а затем щелкните ресурс, для которого необходимо задать параметр диагностики.

3. Если параметров для выбранного ресурса не существует, вам будет предложено создать параметр. Щелкните Turn on diagnostics (Включить диагностику).

   ![Добавление параметра диагностики — имеющиеся параметры отсутствуют](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-none.png)

   Если в ресурсе имеются параметры, для него отобразится список настроенных параметров. Нажмите Add diagnostic setting (Добавить параметр диагностики).

   ![Добавление параметра диагностики — имеющиеся параметры](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-multiple.png)

3. Введите имя параметра и установите флажок для **Отправить в Log Analytics**, затем выберите рабочую область Log Analytics.
   
   ![Добавление параметра диагностики — имеющиеся параметры](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-configure.png)

4. Выберите команду **Сохранить**.

Через несколько секунд новый параметр появится в списке параметров для данного ресурса, и сразу же после создания данных о событии журналы диагностики будут отправлены в необходимую рабочую область. Обратите внимание на то, что между созданием события и его появлением в Log Analytics может пройти до 15 минут.

### <a name="via-powershell-cmdlets"></a>С помощью командлетов PowerShell
Для включения потоковой передачи с помощью [командлетов Azure PowerShell](insights-powershell-samples.md) применяется командлет `Set-AzureRmDiagnosticSetting` с такими параметрами:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

Обратите внимание на то, что свойство workspaceID содержит полный идентификатор рабочей области, а не идентификатор и ключ рабочей области, отображаемые в Log Analytics.

### <a name="via-azure-cli"></a>С помощью интерфейса командной строки Azure
Для включения потоковой передачи через [интерфейс командной строки Azure](insights-cli-samples.md) применяется команда `insights diagnostic set` с такими параметрами:

```azurecli
azure insights diagnostic set --resourceId <resourceID> --workspaceId <workspace resource ID> --categories <list of categories> --enabled true
```

Обратите внимание на то, что свойство workspaceID содержит полный идентификатор рабочей области, а не идентификатор и ключ рабочей области, отображаемые в Log Analytics.

## <a name="how-do-i-query-the-data-in-log-analytics"></a>Как запросить данные из Log Analytics?

В колонке "Поиск по журналу" на портале или в разделе "Расширенная аналитика" в Log Analytics можно запросить журналы диагностики, воспользовавшись решением по управлению журналами в таблице AzureDiagnostics. Существует также [несколько решений для ресурсов Azure](../log-analytics/log-analytics-add-solutions.md), которые можно установить, чтобы мгновенно получать представление о данных журналов, отправляемых в Log Analytics.


## <a name="next-steps"></a>Дополнительная информация
* [Дополнительные сведения о журналах диагностики Azure](monitoring-overview-of-diagnostic-logs.md)
