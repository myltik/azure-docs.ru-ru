---
title: "Оповещения на основе метрик практически в реальном времени в Azure Monitor | Документация Майкрософт"
description: "Оповещения на основе метрик практически в реальном времени позволяют выполнять мониторинг метрик ресурсов Azure каждую минуту."
author: snehithm
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: snmuvva
ms.custom: 
ms.openlocfilehash: d3e88a98e0ba93a630d131c25ca4dd5cb16f1b1a
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2018
---
# <a name="near-real-time-metric-alerts-preview"></a>Оповещения на основе метрик практически в реальном времени (предварительная версия)
Azure Monitor теперь поддерживает новый тип оповещений на основе метрик, который называется "Оповещения на основе метрик практически в реальном времени" (предварительная версия). Эта функция сейчас доступна в виде общедоступной предварительной версии.
Далее приведены сведения об отличиях этих оповещений от обычных оповещений на основе метрик.

- **Низкая задержка.** С помощью оповещений на основе метрик практически в реальном времени можно выполнять мониторинг изменений значений метрик каждую минуту.
- **Дополнительный контроль метрик.** Оповещения на основе метрик практически в реальном времени позволяют пользователям определить расширенные правила генерации оповещений. Оповещения теперь поддерживают мониторинг максимальных, минимальных, средних и общих значений метрик.
- **Объединенный мониторинг нескольких метрик.** Оповещения на основе метрик практически в реальном времени позволяют выполнять мониторинг нескольких метрик (сейчас только двух метрик) с помощью одного правила. Оповещения активируются, если обе метрики превышают соответствующие пороговые значения для заданного периода времени.
- **Модульная система отправки уведомлений.** Оповещения на основе метрик практически в реальном времени используют [группы действий](monitoring-action-groups.md). Эта функция позволяет пользователям создавать действия в модульном виде и повторно использовать их для множества правил генерации оповещений.

> [!NOTE]
> Функция оповещений на основе метрик практически в реальном времени сейчас доступна в виде общедоступной версии. Функциональные возможности и условия взаимодействия с пользователем могут изменяться.
>

## <a name="what-resources-can-i-create-near-real-time-metric-alerts-for"></a>Для каких ресурсов можно создать оповещения на основе метрик практически в реальном времени?
Далее приведен список типов ресурсов, поддерживаемых оповещениями на основе метрик практически в реальном времени:

* Microsoft.ApiManagement/service
* Microsoft.Automation/automationAccounts
* Microsoft.Batch/batchAccounts
* Microsoft.Cache/Redis;
* Microsoft.Compute/virtualMachines
* Microsoft.Compute/virtualMachineScaleSets;
* Microsoft.DataFactory/factories;
* Microsoft.DBforMySQL/servers
* Microsoft.DBforPostgreSQL/servers
* Microsoft.EventHub/namespaces
* Microsoft.Logic/workflows
* Microsoft.Network/applicationGateways
* Microsoft.Network/publicipaddresses;
* Microsoft.Search/searchServices
* Microsoft.ServiceBus/namespaces
* Microsoft.Storage/storageAccounts
* Microsoft.Storage/storageAccounts/services
* Microsoft.StreamAnalytics/streamingjobs
* Microsoft.CognitiveServices/accounts

## <a name="near-real-time-metric-alerts-on-metrics-with-dimensions"></a>Оповещения на основе метрик с измерениями практически в реальном времени
Оповещения на основе метрик практически в реальном времени поддерживают оповещения на основе метрик с измерениями. Измерения — это способ фильтрации метрик до необходимого уровня. Оповещения на основе метрик с измерениями практически в реальном времени поддерживаются для следующих типов ресурсов:

* Microsoft.ApiManagement/service
* Microsoft.Storage/storageAccounts (поддерживается только для учетных записей хранения в регионах США);
* Microsoft.Storage/storageAccounts/services (поддерживается только для учетных записей хранения в регионах США).


## <a name="create-a-near-real-time-metric-alert"></a>Создание оповещений на основе метрик практически в реальном времени
Сейчас оповещения на основе метрик практически в реальном времени можно создать только на портале Azure. Реализация поддержки настройки оповещений на основе метрик практически в реальном времени с помощью PowerShell, интерфейса командной строки и REST API Azure Monitor ожидается в ближайшем времени.

Возможность создания оповещений на основе метрик практически в реальном времени перенесена в новый интерфейс **Оповещения (предварительная версия)**. Несмотря на то что на текущей странице оповещений отображается элемент **Add Near Real-Time Metric alert** (Добавить оповещение на основе метрик практически в реальном времени), вы будете перенаправлены в новый интерфейс.

Вы можете создать оповещение на основе метрик практически в реальном времени, следуя инструкциям, описанным [здесь](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal).

## <a name="managing-near-real-time-metric-alerts"></a>Управление оповещениями на основе метрик практически в реальном времени
Чтобы управлять созданным **оповещением на основе метрик практически в реальном времени**, выполните действия, приведенные [здесь](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal).

## <a name="payload-schema"></a>Схема полезных данных

Операция POST содержит следующие полезные данные и схему JSON для всех оповещений на основе метрик практически в реальном времени.

```json
{
    "WebhookName": "Alert1510875839452",
    "RequestBody": {
        "status": "Activated",
        "context": {
            "condition": {
                "metricName": "Percentage CPU",
                "metricUnit": "Percent",
                "metricValue": "17.7654545454545",
                "threshold": "1",
                "windowSize": "10",
                "timeAggregation": "Average",
                "operator": "GreaterThan"
            },
            "resourceName": "ContosoVM1",
            "resourceType": "microsoft.compute/virtualmachines",
            "resourceRegion": "westus",
            "portalLink": "https://portal.azure.com/#resource/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/automationtest/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
            "timestamp": "2017-11-16T23:54:03.9517451Z",
            "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/microsoft.insights/alertrules/VMMetricAlert1",
            "name": "VMMetricAlert1",
            "description": "A metric alert for the VM Win2012R2",
            "conditionType": "Metric",
            "subscriptionId": "00000000-0000-0000-0000-000000000000",
            "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
            "resourceGroupName": "ContosoVM"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
    },
    "RequestHeader": {
        "Connection": "Keep-Alive",
        "Host": "s1events.azure-automation.net",
        "User-Agent": "azure-insights/0.9",
        "x-ms-request-id": "00000000-0000-0000-0000-000000000000"
    }
}
```

## <a name="next-steps"></a>Дополнительная информация

* [Подробнее о новом интерфейсе "Оповещения (предварительная версия)"](monitoring-overview-unified-alerts.md)
* [Подробнее об оповещениях журналов в интерфейсе Azure "Оповещения (предварительная версия)"](monitor-alerts-unified-log.md)
* [Подробнее об оповещениях в Azure](monitoring-overview-alerts.md)