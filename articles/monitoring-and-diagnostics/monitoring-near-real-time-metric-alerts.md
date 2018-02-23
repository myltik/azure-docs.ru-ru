---
title: "Оповещения на основе метрик практически в реальном времени в Azure Monitor | Документация Майкрософт"
description: "Узнайте, как использовать оповещения на основе метрик практически в реальном времени для отслеживания метрик ресурсов Azure с частотой в 1 минуту."
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
ms.openlocfilehash: 6370f4596e6b20962c6de0dbcbd5f86c3b7777cc
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2018
---
# <a name="near-real-time-metric-alerts-preview"></a>Оповещения на основе метрик практически в реальном времени (предварительная версия)
Azure Monitor поддерживает новый тип оповещения, которое называется оповещением на основе метрик практически в реальном времени (предварительная версия). Эта функция сейчас доступна в виде общедоступной предварительной версии.

Далее приведены сведения об отличиях этих оповещений от обычных оповещений на основе метрик.

- **Низкая задержка.** С помощью оповещений на основе метрик практически в реальном времени можно выполнять мониторинг изменений значений метрик каждую минуту.
- **Дополнительный контроль метрик.** В оповещениях на основе метрик практически в реальном времени можно определить расширенные правила генерации оповещений. Оповещения поддерживают мониторинг максимальных, минимальных, средних и общих значений метрик.
- **Объединенный мониторинг нескольких метрик.** Оповещения на основе метрик практически в реальном времени позволяют выполнять мониторинг нескольких метрик (сейчас только двух метрик) с помощью одного правила. Оповещение активируется, если обе метрики превышают соответствующие пороговые значения для заданного периода времени.
- **Модульная система отправки уведомлений.** Оповещения на основе метрик практически в реальном времени используют [группы действий](monitoring-action-groups.md). Группы действий позволяют создавать модульные действия. Группы действий можно повторно использовать для нескольких правил генерации оповещений.

> [!NOTE]
> Функция оповещений на основе метрик практически в реальном времени сейчас доступна в виде общедоступной версии. Функциональные возможности и условия взаимодействия с пользователем могут изменяться.
>

## <a name="resources-you-can-use-with-near-real-time-metric-alerts"></a>Ресурсы, которые можно использовать с оповещениями на основе метрик практически в реальном времени
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

## <a name="near-real-time-metric-alerts-for-metrics-that-use-dimensions"></a>Оповещения на основе метрик практически в реальном времени для метрик, использующих измерения
Оповещения на основе метрик практически в реальном времени поддерживают оповещения для метрик, использующих измерения. Измерения можно использовать для фильтрации метрик до необходимого уровня. Оповещения на основе метрик с измерениями практически в реальном времени поддерживаются для следующих типов ресурсов:

* Microsoft.ApiManagement/service
* Microsoft.Storage/storageAccounts (поддерживается только для учетных записей хранения в регионах США);
* Microsoft.Storage/storageAccounts/services (поддерживается только для учетных записей хранения в регионах США).

## <a name="create-a-near-real-time-metric-alert"></a>Создание оповещений на основе метрик практически в реальном времени
В настоящее время только на портале Azure можно создать оповещения на основе метрик практически в реальном времени. Реализация поддержки настройки оповещений на основе метрик практически в реальном времени с помощью PowerShell, интерфейса командной строки Azure (Azure CLI) и REST API Azure Monitor ожидается в ближайшем времени.

Возможность создания оповещений на основе метрик практически в реальном времени теперь доступна на новой странице **Оповещения (предварительная версия)**. Несмотря на то что на текущей странице оповещений отображается **добавление оповещения на основе метрик практически в реальном времени**, вы будете перенаправлены на новую страницу **Оповещения (предварительная версия)**.

Дополнительные сведения о том, как создать оповещение практически в реальном времени, см. в разделе [Create an alert rule with the Azure portal](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal) (Создание правила генерации оповещений с помощью портала Azure).

## <a name="manage-near-real-time-metric-alerts"></a>Управление оповещениями на основе метрик практически в реальном времени
После создания оповещения на основе метрик практически в реальном времени можно управлять оповещением с помощью шагов, описанных в разделе об [управлении оповещениями на портале Azure](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal).

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