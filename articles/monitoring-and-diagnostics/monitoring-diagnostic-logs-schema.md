---
title: "Поддерживаемые службы и схемы в журналах диагностики Azure | Документация Майкрософт"
description: "Узнайте о поддерживаемых схемах служб и событий для журналов диагностики Azure."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: fe8887df-b0e6-46f8-b2c0-11994d28e44f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: johnkem
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: aa4fa6e0310b2725005dfa34e3225c89fb4282d6
ms.contentlocale: ru-ru
ms.lasthandoff: 08/24/2017

---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Поддерживаемые службы, схемы и категории для журналов диагностики Azure

[Журналы диагностики ресурсов Azure](monitoring-overview-of-diagnostic-logs.md) — это журналы, созданные ресурсом Azure, описывающие работу данного ресурса. Эти журналы создаются для конкретного типа ресурсов. В этой статье представлены поддерживаемые службы и схема событий для событий, созданных в каждой службе. В эту статью также включен полный список доступных категорий журналов для каждого типа ресурсов.

## <a name="supported-services-and-schemas-for-resource-diagnostic-logs"></a>Поддерживаемые службы и схемы для журналов диагностики ресурсов
Схема для журналов диагностики ресурсов зависит от типа ресурса и категории журнала.   

| служба | Схемы и документы |
| --- | --- |
| Управление API | Схема недоступна. |
| Шлюзы приложений |[Ведение журнала диагностики для шлюза приложений](../application-gateway/application-gateway-diagnostics.md) |
| Служба автоматизации Azure |[Log Analytics для службы автоматизации Azure](../automation/automation-manage-send-joblogs-log-analytics.md) |
| Пакетная служба Azure |[Ведение журналов диагностики пакетной службы Azure](../batch/batch-diagnostics.md) |
| Customer Insights | Схема недоступна. |
| Сеть доставки содержимого | Схема недоступна. |
| Cosmos DB | Схема недоступна. |
| Аналитика озера данных |[Доступ к журналам диагностики для Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Хранилище озера данных |[Доступ к журналам диагностики Azure Data Lake Store](../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Концентраторы событий |[Журналы диагностики концентраторов событий Azure](../event-hubs/event-hubs-diagnostic-logs.md) |
| хранилище ключей; |[Ведение журнала хранилища ключей Azure](../key-vault/key-vault-logging.md) |
| Балансировщик нагрузки |[Log Analytics для Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md) |
| Приложения логики |[Настраиваемая схема отслеживания сообщений B2B для приложений логики](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| группы сетевой безопасности; |[Аналитика журналов для групп безопасности сети](../virtual-network/virtual-network-nsg-manage-log.md) |
| Службы восстановления | Схема недоступна.|
| Поиск |[Включение и использование аналитики поискового трафика](../search/search-traffic-analytics.md) |
| Управление сервером | Схема недоступна. |
| Служебная шина |[Журналы диагностики служебной шины Azure](../service-bus-messaging/service-bus-diagnostic-logs.md) |
| Анализ потока |[Журналы диагностики задания](../stream-analytics/stream-analytics-job-diagnostic-logs.md) |

## <a name="supported-log-categories-per-resource-type"></a>Поддерживаемые категории журнала для каждого типа ресурса
|Тип ресурса|Категория|Отображаемое имя категории|
|---|---|---|
|Microsoft.ApiManagement/service|GatewayLogs|Журналы, относящихся к шлюзу ApiManagement.|
|Microsoft.Automation/automationAccounts|JobLogs|Журналы заданий|
|Microsoft.Automation/automationAccounts|JobStreams|Потоки заданий|
|Microsoft.Automation/automationAccounts|DscNodeStatus|Состояние узла DSC.|
|Microsoft.Batch/batchAccounts|ServiceLog|Журналы служб|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|Возвращает метрики конечной точки, например пропускную способность, исходящий трафик и т. д.|
|Microsoft.CustomerInsights/hubs|AuditEvents|AuditEvents|
|Microsoft.DataLakeAnalytics/accounts|Аудит|Журналы аудита|
|Microsoft.DataLakeAnalytics/accounts|Запросы|Журналы запросов|
|Microsoft.DataLakeStore/accounts|Аудит|Журналы аудита|
|Microsoft.DataLakeStore/accounts|Запросы|Журналы запросов|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.EventHub/namespaces|ArchiveLogs|Журналы архивации|
|Microsoft.EventHub/namespaces|OperationalLogs|Журналы операций|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Журналы автомасштабирования.|
|Microsoft.KeyVault/vaults|AuditEvent|Журналы аудита|
|Microsoft.Logic/workflows|WorkflowRuntime|События диагностики среды выполнения рабочего процесса|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Integration Account track events|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Событие группы безопасности сети|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Счетчик правил группы безопасности сети|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|События оповещения балансировщика нагрузки|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Состояние работоспособности балансировщика нагрузки|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Журнал доступа к шлюзу приложений|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Журнал производительности шлюза приложений|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Журнал брандмауэра шлюза приложений|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Данные отчетов службы архивации Azure|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Задания Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|События Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Реплицированные элементы Azure Site Recovery|
|Microsoft.Search/searchServices|OperationLogs|Журналы операций|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Журналы операций|
|Microsoft.StreamAnalytics/streamingjobs|Выполнение|Выполнение|
|Microsoft.StreamAnalytics/streamingjobs|Разработка|Разработка|

## <a name="next-steps"></a>Дальнейшие действия

* [Сбор и использование данных журнала из ресурсов Azure](monitoring-overview-of-diagnostic-logs.md)
* [Потоковая передача журналов диагностики Azure в **концентраторы событий**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Создание или обновление диагностического параметра](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Сбор журналов и метрик для служб Azure для использования в Log Analytics](../log-analytics/log-analytics-azure-storage.md)

