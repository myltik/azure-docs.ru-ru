---
title: "Обзор журналов диагностики Azure | Документация Майкрософт"
description: "Узнайте, что такое журналы диагностики Azure и как их использовать для просмотра событий, происходящих в ресурсе Azure."
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
ms.date: 08/02/2017
ms.author: johnkem; magoedte
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 8961676a60d922912e383937ca38c5d2f89a348a
ms.contentlocale: ru-ru
ms.lasthandoff: 08/04/2017

---
# <a name="collect-and-consume-log-data-from-your-azure-resources"></a>Сбор и использование данных журнала из ресурсов Azure

## <a name="what-are-azure-resource-diagnostic-logs"></a>Что такое журналы диагностики ресурсов Azure
**Журналы диагностики уровня ресурсов Azure** генерируются ресурсом. Они содержат подробные и своевременные данные о работе этого ресурса. Содержимое этих журналов зависит от типа ресурса. Например, счетчики правила группы безопасности сети и аудит Key Vault представляют две категории журналов ресурсов.

Журналы диагностики уровня ресурса отличаются от [журнала действий](monitoring-overview-activity-logs.md). Журнал действий содержит информацию об операциях, которые были выполнены с ресурсами в вашей подписке с помощью Resource Manager, например о создании виртуальной машины или удалении приложения логики. Журнал действий — это журнал уровня подписки. Журналы диагностики уровня ресурса дают представление об операциях, которые были выполнены в рамках этого ресурса, например о получении секрета из Key Vault.

Журналы диагностики уровня ресурса также отличаются от журналов диагностики уровня гостевой ОС. Журналы диагностики гостевой ОС собирает агент, работающий на виртуальной машине или поддерживаемом ресурсе другого типа. Для сбора журналов диагностики уровня ресурса не требуется агент. В них записываются данные ресурсов из платформы Azure, тогда как в журналы диагностики уровня гостевой ОС записываются данные из операционной системы и приложений, выполняющихся на виртуальной машине.

Не все ресурсы поддерживают описанный здесь новый тип журналов диагностики ресурсов. Эта статья содержит раздел со списком типов ресурсов, поддерживающих новые журналы диагностики уровня ресурса.

![Журналы диагностики ресурсов и другие типы журналов ](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_vs_other_logs_v5.png)

Рисунок 1. Журналы диагностики ресурсов и другие типы журналов

## <a name="what-you-can-do-with-resource-level-diagnostic-logs"></a>Что можно делать с журналами диагностики уровня ресурса
Ниже описано несколько доступных операций с журналами диагностики ресурсов.

![Логическое размещение журналов диагностики ресурсов](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_Actions.png)


* Сохранение журналов в [**учетную запись хранения**](monitoring-archive-diagnostic-logs.md) для аудита или проверки вручную. В **параметрах диагностики ресурсов** вы также можете задать время хранения (в днях).
* [Потоковая передача журналов в **концентраторы событий**](monitoring-stream-diagnostic-logs-to-event-hubs.md) для обработки в сторонней службе или пользовательском аналитическом решении, например в PowerBI.
* Анализ журналов с помощью [OMS Log Analytics](../log-analytics/log-analytics-azure-storage.md)

Вы можете использовать учетную запись хранения или пространство имен концентраторов событий, не входящее в подписку, в которой создаются журналы. Пользователю, который настраивает этот параметр, должен быть предоставлен соответствующий уровень доступа RBAC к обеим подпискам.

## <a name="resource-diagnostic-settings"></a>Параметры диагностики ресурсов
Журналы диагностики для всех ресурсов, кроме вычислительных, настраиваются с помощью параметров диагностики ресурсов. **Параметры диагностики ресурсов** для ресурса определяют следующее:

* Куда будут отправляться журналы диагностики ресурсов (учетная запись хранения, концентраторы событий и (или) OMS Log Analytics).
* Какие категории журналов будут отправляться.
* Как долго должны храниться журналы каждой категории в учетной записи хранения.
    - Срок хранения 0 дней означает, что журналы хранятся неограниченно долго. В противном случае укажите количество дней в диапазоне от 1 до 2 147 483 647.
    - Если политики хранения заданы, но хранение журналов в учетной записи хранения отключено (например, выбраны только варианты концентраторов событий или OMS), политики хранения не будут применены.
    - Политики хранения применяются по дням, поэтому в конце дня (по времени в формате UTC) журналы, срок которых теперь превышает период хранения, будут удалены. Например, если настроена политика хранения в течение одного дня, то в начале текущего дня журналы за вчерашний день будет удалены.

Эти параметры легко настраиваются в колонке диагностики для ресурса на портале Azure, а также с помощью Azure PowerShell, команд интерфейса командной строки или [REST API Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).

> [!WARNING]
> Для журналов диагностики и метрик для вычислительных ресурсов (например, виртуальных машин или Service Fabric) доступен [отдельный механизм настройки и выбора выходных данных](../azure-diagnostics.md).
>
>

## <a name="how-to-enable-collection-of-resource-diagnostic-logs"></a>Как включить сбор журналов диагностики ресурсов
Сбор журналов диагностики ресурсов можно включить [во время создания ресурса с помощью шаблона Resource Manager](./monitoring-enable-diagnostic-logs-using-template.md) или после его создания в колонке ресурса на портале. Сбор этих журналов можно также включить в любой момент с помощью Azure PowerShell, команд интерфейса командной строки или REST API Azure Monitor.

> [!TIP]
> При работе с некоторыми ресурсами эти инструкции могут требовать корректировки. Изучите связи на схеме, представленной в конце этой страницы, чтобы разобраться в дополнительных действиях, которые могут потребоваться при использовании некоторых типов ресурсов.
>
>

### <a name="enable-collection-of-resource-diagnostic-logs-in-the-portal"></a>Включение сбора журналов диагностики ресурсов на портале
Чтобы включить сбор журналов диагностики ресурсов на портале Azure после создания ресурса, сделайте следующее.

1. Перейдите к колонке ресурса и откройте колонку **Диагностика** .
2. Щелкните **Включено** и выберите учетную запись хранения и (или) концентратор событий.

   ![Включение журналов диагностики после создания ресурса](./media/monitoring-overview-of-diagnostic-logs/enable-portal-existing.png)
3. В разделе **Журналы** выберите **категории журналов**, которые вы хотите собирать или передавать.
4. Щелкните **Сохранить**.

### <a name="enable-collection-of-resource-diagnostic-logs-via-powershell"></a>Включение сбора журналов диагностики ресурсов с помощью PowerShell
Чтобы включить сбор журналов диагностики ресурсов с помощью Azure PowerShell, используйте следующие команды.

Выполните приведенную ниже команду, чтобы включить отправку журналов диагностики в учетную запись хранения.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
```

StorageAccountId — это идентификатор ресурса учетной записи хранения, в которую будут отправляться журналы.

Чтобы включить потоковую передачу журналов диагностики в концентратор событий, используйте следующую команду.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your Service Bus rule id] -Enabled $true
```

ServiceBusRuleID — это строка в формате `{Service Bus resource ID}/authorizationrules/{key name}`.

Чтобы включить отправку журналов диагностики в рабочую область Log Analytics, используйте следующую команду.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
```

Идентификатор ресурса рабочей области Log Analytics можно получить с помощью следующей команды.

```powershell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId
```

Можно объединять эти параметры, чтобы получить несколько вариантов вывода.

### <a name="enable-collection-of-resource-diagnostic-logs-via-cli"></a>Включение сбора журналов диагностики ресурсов с помощью интерфейса командной строки
Чтобы включить сбор журналов диагностики ресурсов с помощью Azure CLI, используйте следующие команды.

Выполните приведенную ниже команду, чтобы включить отправку журналов диагностики в учетную запись хранения.

```azurecli
azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
```

StorageAccountId — это идентификатор ресурса учетной записи хранения, в которую будут отправляться журналы.

Чтобы включить потоковую передачу журналов диагностики в концентратор событий, используйте следующую команду.

```azurecli
azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
```

ServiceBusRuleID — это строка в формате `{Service Bus resource ID}/authorizationrules/{key name}`.

Чтобы включить отправку журналов диагностики в рабочую область Log Analytics, используйте следующую команду.

```azurecli
azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
```

Можно объединять эти параметры, чтобы получить несколько вариантов вывода.

### <a name="enable-collection-of-resource-diagnostic-logs-via-rest-api"></a>Включение сбора журналов диагностики ресурсов с помощью REST API
Изменение параметров диагностики с помощью REST API Azure Monitor описано в [этом документе](https://msdn.microsoft.com/library/azure/dn931931.aspx).

## <a name="manage-resource-diagnostic-settings-in-the-portal"></a>Управление параметрами диагностики ресурсов на портале
Убедитесь, что все ресурсы настроены с помощью параметров диагностики. Перейдите в колонку **Мониторинг** на портале и откройте колонку **Журналы диагностики**.

![Колонка "Журналы диагностики" на портале](./media/monitoring-overview-of-diagnostic-logs/manage-portal-nav.png)

Чтобы найти колонку "Мониторинг" может понадобиться щелкнуть "Больше служб".

В этой колонке можно просматривать и фильтровать все ресурсы, поддерживающие журналы диагностики. Таким образом можно проверить, включена ли для них диагностика. Также можно проверить, в какую учетную запись хранения, концентратор событий или рабочую область Log Analytics передаются эти журналы.

![Результаты в колонке "Журналы диагностики" на портале](./media/monitoring-overview-of-diagnostic-logs/manage-portal-blade.png)

Если щелкнуть ресурс, отобразятся все журналы, сохраненные в учетной записи хранения, и вы сможете отключить диагностику или изменить ее параметры. Щелкните значок скачивания, чтобы скачать журналы за определенный период времени.

![Колонка "Журналы диагностики" одного ресурса](./media/monitoring-overview-of-diagnostic-logs/manage-portal-logs.png)

> [!NOTE]
> Это представление содержит журналы диагностики, которые можно скачать, только если вы настроили параметры диагностики для их сохранения в учетную запись хранения.
>
>

Щелкните ссылку **Параметры диагностики**, чтобы открыть одноименную колонку. Здесь вы можете включить, отключить или изменить параметры диагностики для выбранного ресурса.

## <a name="supported-services-and-schema-for-resource-diagnostic-logs"></a>Поддерживаемые службы и схемы для журналов диагностики ресурсов
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

* [Потоковая передача журналов диагностики Azure в **концентраторы событий**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Создание или обновление диагностического параметра](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Сбор журналов и метрик для служб Azure для использования в Log Analytics](../log-analytics/log-analytics-azure-storage.md)

