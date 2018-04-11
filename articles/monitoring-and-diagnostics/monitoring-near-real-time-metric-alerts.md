---
title: Новые оповещения на основе метрик в поддерживаемых ресурсах Azure Monitor | Документация Майкрософт
description: Справочник по поддерживаемым метрикам и журналам для новых оповещений на основе метрик Azure почти в реальном времени.
author: snehithm
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2018
ms.author: snmuvva, vinagara
ms.custom: ''
ms.openlocfilehash: 6ccb095f3739a90bdab2408965a742f9cbc19359
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2018
---
# <a name="newer-metric-alerts-for-azure-services-in-the-azure-portal"></a>Новые оповещения на основе метрик для служб Azure на портале Azure
Azure Monitor теперь поддерживает новый тип оповещений на основе метрик. Далее приведены сведения об отличиях новых оповещений от [классических оповещений метрик](insights-alerts-portal.md).

- **Уменьшение задержки.** Новые оповещения на основе метрик могут выполняться каждую минуту. Раньше оповещения метрик всегда проверялись с частотой в 5 минут. Задержка для оповещений журналов по-прежнему составляет более 1 минуты из-за времени, необходимого для приема журналов. 
- **Поддержка многомерных метрик.** Вы можете создать оповещение по многомерным метрикам, чтобы отслеживать только конкретный сегмент метрики. 
- **Дополнительный контроль метрик.** Вы можете определить расширенные правила генерации оповещений. Новые оповещения поддерживают мониторинг максимальных, минимальных, средних и общих значений метрик. 
- **Объединенный мониторинг нескольких метрик.** Вы сможете выполнять мониторинг нескольких метрик (пока только двух метрик) с помощью одного правила. Оповещение активируется, если обе метрики превышают соответствующие пороговые значения для заданного периода времени. 
- **Усовершенствованная система уведомлений.** Во всех новых оповещениях используются [группы действий](monitoring-action-groups.md). Это именованные группы уведомлений и действий, которые можно использовать в нескольких оповещениях. В классических оповещениях на основе метрик и старых оповещениях Log Analytics группы действий не используются. 
- **Метрики из журналов** (ограниченная общедоступная предварительная версия). Данные журналов, поступающие в службу Log Analytics, теперь можно извлекать и преобразовывать в метрики Azure Monitor и создавать на их основе оповещения, так же как и на основе других метрик. 

Сведения о том, как создать на портале Azure оповещение практически в реальном времени, см. в разделе [Создание правила оповещений с помощью портала Azure](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal). После создания оповещением можно управлять с помощью шагов, описанных в разделе об [управлении оповещениями на портале Azure](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal).


## <a name="portal-powershell-cli-rest-support"></a>Поддержка портала, PowerShell, CLI и REST
В настоящее время оповещения метрик почти в реальном времени можно создать только на портале Azure или через REST API. Скоро будет доступна возможность настраивать новые оповещения с помощью PowerShell и интерфейса командной строки Azure (Azure CLI 2.0).

## <a name="metrics-and-dimensions-supported"></a>Поддерживаемые метрики и измерения
Новые оповещения на основе метрик поддерживают оповещения для метрик, использующих измерения. Измерения можно использовать для фильтрации метрик до необходимого уровня. Все поддерживаемые метрики с применимыми измерениями можно изучить и визуализировать с помощью [обозревателя метрик Azure Monitor (предварительная версия)](monitoring-metric-charts.md).

Вот полный список источников метрик Azure Monitor, которые поддерживаются в новых оповещениях:

|Тип ресурса  |Поддерживаемые измерения  | Доступные метрики|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | Yes        | [Управление API](monitoring-supported-metrics.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     Yes   | [Учетные записи службы автоматизации](monitoring-supported-metrics.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | Недоступно| [Учетные записи пакетной службы](monitoring-supported-metrics.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis;     |    Недоступно     |[Кэш Redis](monitoring-supported-metrics.md#microsoftcacheredis)|
|Microsoft.Compute/virtualMachines     |    Недоступно     | [Виртуальные машины](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets;     |   Недоступно      |[Масштабируемые наборы виртуальных машин](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.DataFactory/factories;     |   Yes     |[Фабрики данных V2](monitoring-supported-metrics.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   Недоступно      |[База данных для MySQL](monitoring-supported-metrics.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    Недоступно     | [База данных для PostgreSQL](monitoring-supported-metrics.md#microsoftdbforpostgresqlservers)|
|Microsoft.EventHub/namespaces     |  Yes      |[Концентраторы событий](monitoring-supported-metrics.md#microsofteventhubnamespaces)|
|Microsoft.Logic/workflows     |     Недоступно    |[Logic Apps](monitoring-supported-metrics.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    Недоступно     | [Шлюзы приложений](monitoring-supported-metrics.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/publicipaddresses;     |  Недоступно       |[Общедоступные IP-адреса](monitoring-supported-metrics.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Search/searchServices     |   Недоступно      |[Службы поиска](monitoring-supported-metrics.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  Yes       |[Служебная шина](monitoring-supported-metrics.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    Yes     | [Учетные записи хранения](monitoring-supported-metrics.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     Yes    | [Службы BLOB-объектов](monitoring-supported-metrics.md#microsoftstoragestorageaccountsblobservices), [службы файлов](monitoring-supported-metrics.md#microsoftstoragestorageaccountsfileservices), [службы очередей](monitoring-supported-metrics.md#microsoftstoragestorageaccountsqueueservices) и [службы таблиц](monitoring-supported-metrics.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  Недоступно       | [Stream Analytics](monitoring-supported-metrics.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft.CognitiveServices/accounts     |    Недоступно     | [Cognitive Services](monitoring-supported-metrics.md#microsoftcognitiveservicesaccounts)|
|Microsoft.OperationalInsights/workspaces (предварительная версия) | Yes|[Рабочие области Log Analytics](#log-analytics-logs-as-metrics-for-alerting)|


## <a name="log-analytics-logs-as-metrics-for-alerting"></a>Журналы Log Analytics в качестве метрик для оповещений 

Новые оповещения на основе метрик можно применять для популярных журналов Log Analytics, извлекаемых в виде метрик из данных предварительного просмотра журналов.  
- [Счетчики производительности](../log-analytics/log-analytics-data-sources-performance-counters.md) для компьютеров Windows и Linux.
- [Записи пульсов для решения "Работоспособность агентов"](../operations-management-suite/oms-solution-agenthealth.md).
- Записи [управления обновлениями](../operations-management-suite/oms-solution-update-management.md).
 
> [!NOTE]
> Конкретная метрика и (или) измерение отображается, только если для него есть данные за выбранный период. Эти метрики доступны для клиентов, у которых рабочие области расположены в восточной части США, западной центральной части США или Западной Европе, и которые зарегистрировались для использования предварительной версии. Если вы хотите поработать с этой предварительной версией, зарегистрируйтесь при помощи [этого опроса](https://aka.ms/MetricLogPreview).

Поддерживаются следующие источники метрик на основе журналов Log Analytics:

Имя и описание метрики  |Поддерживаемые измерения  | Тип журнала  |
|---------|---------|---------|
|Average_Avg. время чтения с диска (с)     |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Windows      |
| Average_Avg. время записи на диск (с)     |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Windows      |
| Average_Current Disk Queue Length   |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Windows      |
| Average_Disk Reads/sec    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Windows      |
| Average_Disk Transfers/sec    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Windows      |
|   Average_% Free Space    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Windows      |
| Average_Available MBytes     |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Windows      |
| Average_% Committed Bytes In Use    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Windows      |
| Average_Bytes Received/sec    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Windows      |
|  Average_Bytes Sent/sec    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Windows      |
|  Average_Bytes Total/sec    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Windows      |
|  Average_% Processor Time    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Windows      |
|   Average_Processor Queue Length    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Windows      |
|   Average_% Free Inodes   |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_% Free Space   |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_% Used Inodes  |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_% Used Space   |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Disk Read Bytes/sec    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Disk Reads/sec |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Disk Transfers/sec |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Disk Write Bytes/sec   |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Disk Writes/sec    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Free Megabytes |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Logical Disk Bytes/sec |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_% Available Memory |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_% Available Swap Space |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_% Used Memory  |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_% Used Swap Space  |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Available MBytes Memory    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Available MBytes Swap  |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Page Reads/sec |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Page Writes/sec    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Pages/sec  |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Used MBytes Swap Space |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Used Memory MBytes |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Total Bytes Transmitted    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Total Bytes Received   |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Total Bytes    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Total Packets Transmitted  |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Total Packets Received |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Total Rx Errors    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Total Tx Errors    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Total Collisions   |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Avg. время чтения с диска (с) |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Avg. время обращения к диску (с) |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Avg. время записи на диск (с)    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Physical Disk Bytes/sec    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Pct Privileged Time    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Pct User Time  |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Used Memory kBytes |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Virtual Shared Memory  |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_% DPC Time |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_% Idle Time    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_% Interrupt Time   |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_% IO Wait Time |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_% Nice Time    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_% Privileged Time  |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_% Processor Time   |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_% User Time    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Free Physical Memory   |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Free Space in Paging Files |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Free Virtual Memory    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Processes  |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Size Stored In Paging Files    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Uptime |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Users  |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Пульс  |     Да — Computer, OSType, Version и SourceComputerId.    |   Записи пульсов |
|    Блокировка изменений |     Да — Computer, Product, Classification, UpdateState, Optional и Approved.    |   управление обновлениями; |



## <a name="payload-schema"></a>Схема полезных данных

Если используется соответствующим образом настроенная [группа действий](monitoring-action-groups.md), то операция POST содержит следующие полезные данные и схему JSON для всех новых оповещений на основе метрик:

```json
{"schemaId":"AzureMonitorMetricAlert","data":
    {
    "version": "2.0",
    "status": "Activated",
    "context": {
    "timestamp": "2018-02-28T10:44:10.1714014Z",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
    "name": "StorageCheck",
    "description": "",
    "conditionType": "SingleResourceMultipleMetricCriteria",
    "condition": {
      "windowSize": "PT5M",
      "allOf": [
        {
          "metricName": "Transactions",
          "dimensions": [
            {
              "name": "AccountResourceId",
              "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
            },
            {
              "name": "GeoType",
              "value": "Primary"
            }
          ],
          "operator": "GreaterThan",
          "threshold": "0",
          "timeAggregation": "PT5M",
          "metricValue": 1.0
        },
      ]
    },
    "subscriptionId": "00000000-0000-0000-0000-000000000000",
    "resourceGroupName": "Contoso",
    "resourceName": "diag500",
    "resourceType": "Microsoft.Storage/storageAccounts",
    "resourceId": "/subscriptions/1e3ff1c0-771a-4119-a03b-be82a51e232d/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500",
    "portalLink": "https://portal.azure.com/#resource//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
  },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
    }
}
```

## <a name="next-steps"></a>Дополнительная информация

* Получите дополнительные сведения об [интерфейсе оповещений](monitoring-overview-unified-alerts.md).
* Ознакомьтесь со сведениями об [оповещениях журналов в Azure](monitor-alerts-unified-log.md).
* [Подробнее об оповещениях в Azure](monitoring-overview-alerts.md)
