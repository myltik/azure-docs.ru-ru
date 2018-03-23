---
title: Оповещения на основе метрик практически в реальном времени в Azure Monitor | Документация Майкрософт
description: Узнайте, как использовать оповещения на основе метрик практически в реальном времени для отслеживания метрик ресурсов Azure с частотой в 1 минуту.
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
ms.date: 02/26/2018
ms.author: snmuvva, vinagara
ms.custom: ''
ms.openlocfilehash: 88995b1f3350fe485e28efccc93779ae0a42eb97
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/12/2018
---
# <a name="near-real-time-metric-alerts-preview"></a>Оповещения на основе метрик практически в реальном времени (предварительная версия)
Azure Monitor поддерживает новый тип оповещения, которое называется оповещением на основе метрик практически в реальном времени (предварительная версия). Эта функция сейчас доступна в виде общедоступной предварительной версии.

Далее приведены сведения об отличиях этих оповещений от обычных оповещений на основе метрик.

- **Низкая задержка.** С помощью оповещений на основе метрик практически в реальном времени можно выполнять мониторинг изменений значений метрик каждую минуту.
- **Дополнительный контроль метрик.** В оповещениях на основе метрик практически в реальном времени можно определить расширенные правила генерации оповещений. Оповещения поддерживают мониторинг максимальных, минимальных, средних и общих значений метрик.
- **Метрики из журналов**. Из данных популярных журналов, поступающих в [Log Analytics](../log-analytics/log-analytics-overview.md), можно извлекать данные метрик в Azure Monitor и получать оповещения практически в режиме реального времени.
- **Объединенный мониторинг нескольких метрик.** Оповещения на основе метрик практически в реальном времени позволяют выполнять мониторинг нескольких метрик (сейчас только двух метрик) с помощью одного правила. Оповещение активируется, если обе метрики превышают соответствующие пороговые значения для заданного периода времени.
- **Модульная система отправки уведомлений.** Оповещения на основе метрик практически в реальном времени используют [группы действий](monitoring-action-groups.md). Группы действий позволяют создавать модульные действия. Группы действий можно повторно использовать для нескольких правил генерации оповещений.

> [!NOTE]
> Функция оповещений на основе метрик практически в реальном времени сейчас доступна в виде общедоступной версии. А функции метрик из журналов доступны в виде *ограниченной* общедоступной предварительной версии. Функциональные возможности и условия взаимодействия с пользователем могут изменяться.
>

## <a name="metrics-and-dimensions-supported"></a>Поддерживаемые метрики и измерения
Оповещения на основе метрик практически в реальном времени поддерживают оповещения для метрик, использующих измерения. Измерения можно использовать для фильтрации метрик до необходимого уровня. Все поддерживаемые метрики с применимыми измерениями можно изучить и визуализировать с помощью [обозревателя метрик Azure Monitor (предварительная версия)](monitoring-metric-charts.md).

Далее приведен полный список источников метрик на основе Azure Monitor, поддерживаемых оповещениями на основе метрик практически в реальном времени.

|Имя и описание метрики  |Поддерживаемые измерения  |
|---------|---------|
|Microsoft.ApiManagement/service     | Yes        |
|Microsoft.Automation/automationAccounts     |     Недоступно    |
|Microsoft.Automation/automationAccounts     |   Недоступно      |
|Microsoft.Cache/Redis;     |    Недоступно     |
|Microsoft.Compute/virtualMachines     |    Недоступно     |
|Microsoft.Compute/virtualMachineScaleSets;     |   Недоступно      |
|Microsoft.DataFactory/factories;     |   Недоступно      |
|Microsoft.DBforMySQL/servers     |   Недоступно      |
|Microsoft.DBforPostgreSQL/servers     |    Недоступно     |
|Microsoft.EventHub/namespaces     |   Недоступно      |
|Microsoft.Logic/workflows     |     Недоступно    |
|Microsoft.Network/applicationGateways     |    Недоступно     |
|Microsoft.Network/publicipaddresses;     |  Недоступно       |
|Microsoft.Search/searchServices     |   Недоступно      |
|Microsoft.ServiceBus/namespaces     |  Недоступно       |
|Microsoft.Storage/storageAccounts     |    Yes     |
|Microsoft.Storage/storageAccounts/services     |     Yes    |
|Microsoft.StreamAnalytics/streamingjobs     |  Недоступно       |
|Microsoft.CognitiveServices/accounts     |    Недоступно     |


В настоящее время поддерживаются метрики на основе следующих популярных журналов OMS.
- [Счетчики производительности](../log-analytics/log-analytics-data-sources-performance-counters.md) для компьютеров Windows и Linux.
- Записи пульсов для компьютеров.
- Записи [управления обновлениями](../operations-management-suite/oms-solution-update-management.md).

Ниже приведен полный список источников метрик на основе журналов OMS Monitor, поддерживаемых оповещениями на основе метрик практически в реальном времени.

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

> [!NOTE]
> Конкретная метрика и (или) измерение отображается, только если для него существуют данные за выбранный период.

## <a name="create-a-near-real-time-metric-alert"></a>Создание оповещений на основе метрик практически в реальном времени
В настоящее время только на портале Azure можно создать оповещения на основе метрик практически в реальном времени. Реализация поддержки настройки оповещений на основе метрик практически в реальном времени с помощью PowerShell, интерфейса командной строки Azure (Azure CLI) и REST API Azure Monitor ожидается в ближайшем времени.

Возможность создания оповещений на основе метрик практически в реальном времени теперь доступна на новой странице **Оповещения (предварительная версия)**. Несмотря на то что на текущей странице оповещений отображается **добавление оповещения на основе метрик практически в реальном времени**, вы будете перенаправлены на новую страницу **Оповещения (предварительная версия)**.

Дополнительные сведения о том, как создать оповещение практически в реальном времени, см. в разделе [Create an alert rule with the Azure portal](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal) (Создание правила генерации оповещений с помощью портала Azure).

## <a name="manage-near-real-time-metric-alerts"></a>Управление оповещениями на основе метрик практически в реальном времени
После создания оповещения на основе метрик практически в реальном времени можно управлять оповещением с помощью шагов, описанных в разделе об [управлении оповещениями на портале Azure](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal).

## <a name="payload-schema"></a>Схема полезных данных

Если используется соответствующим образом настроенная [группа действий](monitoring-action-groups.md), то операция POST содержит следующие полезные данные и схему JSON для всех оповещений на основе метрик практически в реальном времени.

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

* [Подробнее о новом интерфейсе "Оповещения (предварительная версия)"](monitoring-overview-unified-alerts.md)
* [Подробнее об оповещениях журналов в интерфейсе Azure "Оповещения (предварительная версия)"](monitor-alerts-unified-log.md)
* [Подробнее об оповещениях в Azure](monitoring-overview-alerts.md)
