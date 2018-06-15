---
title: Сбор журналов и метрик для служб Azure в Log Analytics | Документация Майкрософт
description: Настройте диагностику ресурсов Azure для записи журналов и метрик в Log Analytics.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: 84105740-3697-4109-bc59-2452c1131bfe
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a748cb0e2a08ed5e8ada5db171d5ef12b2fe121e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32170733"
---
# <a name="collect-azure-service-logs-and-metrics-for-use-in-log-analytics"></a>Сбор журналов и метрик для служб Azure для использования в Log Analytics

Сбор журналов и метрик для служб Azure можно выполнить четырьмя разными способами:

1. Направление диагностики Azure напрямую в Log Analytics (*диагностика* в следующей таблице).
2. Отправка диагностики Azure в службу хранилища Azure, а затем — в Log Analytics (*хранилище* в следующей таблице).
3. Использование соединителей для служб Azure (*соединители* в следующей таблице).
4. Сценарии для сбора и публикации данных в Log Analytics (не указано в следующей таблице, а также для служб, которые не указаны).


| Service                 | Тип ресурса                           | Журналы        | Метрики     | Решение |
| --- | --- | --- | --- | --- |
| Шлюзы приложений    | Microsoft.Network/applicationGateways   | Диагностика | Диагностика | [Анализ шлюзов приложений Azure](log-analytics-azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-log-analytics) |
| Application Insights    |                                         | Соединитель   | Соединитель   | [Соединитель Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) (предварительная версия) |
| Учетные записи службы автоматизации     | Microsoft.Automation/AutomationAccounts | Диагностика |             | [Дополнительные сведения](../automation/automation-manage-send-joblogs-log-analytics.md)|
| Учетные записи пакетной службы          | Microsoft.Batch/batchAccounts           | Диагностика | Диагностика | |
| Классические облачные службы  |                                         | Хранилище     |             | [Дополнительные сведения](log-analytics-azure-storage-iis-table.md) |
| Cognitive Services      | Microsoft.CognitiveServices/accounts    |             | Диагностика | |
| Data Lake Analytics     | Microsoft.DataLakeAnalytics/accounts    | Диагностика |             | |
| Data Lake Store         | Microsoft.DataLakeStore/accounts        | Диагностика |             | |
| пространство имен концентратора событий;     | Microsoft.EventHub/namespaces           | Диагностика | Диагностика | |
| Центры Интернета вещей;                | Microsoft.Devices/IotHubs               |             | Диагностика | |
| хранилище ключей;               | Microsoft.KeyVault/vaults               | Диагностика |             | [Анализ Key Vault](log-analytics-azure-key-vault.md) |
| Балансировщики нагрузки          | Microsoft.Network/loadBalancers         | Диагностика |             |  |
| приложения логики;              | Microsoft.Logic/workflows <br> Microsoft.Logic/integrationAccounts | Диагностика | Диагностика | |
| группы сетевой безопасности; | Microsoft.Network/networksecuritygroups | Диагностика |             | [Анализ групп безопасности сети Azure](log-analytics-azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-log-analytics) |
| Хранилища восстановления         | Microsoft.RecoveryServices/vaults       |             |             | [Служба анализа служб восстановления Azure (предварительная версия)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
| Службы поиска         | Microsoft.Search/searchServices         | Диагностика | Диагностика | |
| Пространство имен служебной шины   | Microsoft.ServiceBus/namespaces         | Диагностика | Диагностика | [Служба анализа служебной шины (предварительная версия)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
| Service Fabric          |                                         | Хранилище     |             | [Анализ Service Fabric (предварительная версия)](log-analytics-service-fabric.md) |
| SQL (версия 12)               | Microsoft.Sql/servers/databases <br> Microsoft.Sql/servers/elasticPools |             | Диагностика | [Службы анализа SQL Azure (предварительная версия)](log-analytics-azure-sql.md) |
| Хранилище                 |                                         |             | Скрипт      | [Служба анализа службы хранилища Azure (предварительная версия)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution) |
| Виртуальные машины        | Microsoft.Compute/virtualMachines       | Добавочный номер   | Добавочный номер <br> Диагностика  | |
| Масштабируемые наборы виртуальных машин | Microsoft.Compute/virtualMachines <br> Microsoft.Compute/virtualMachineScaleSets/virtualMachines |             | Диагностика | |
| Фермы веб-серверов        | Microsoft.Web/serverfarms               |             | Диагностика | |
| Веб-сайты               | Microsoft.Web/sites <br> Microsoft.Web/sites/slots |             | Диагностика | [Служба анализа веб-приложений Azure (предварительная версия)](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureWebAppsAnalyticsOMS?tab=Overview) |


> [!NOTE]
> Для мониторинга виртуальных машин Azure (Linux и Windows) рекомендуется установить [расширение виртуальной машины Analytics журнала](log-analytics-azure-vm-extension.md). Агент предоставляет сведения, собранные в виртуальных машинах. Вы можете также использовать расширение для масштабируемых наборов виртуальных машин.
>
>

## <a name="azure-diagnostics-direct-to-log-analytics"></a>Направление диагностики Azure в Log Analytics
Множество ресурсов Azure могут записывать журналы диагностики и метрики напрямую в Log Analytics. Это предпочтительный способ сбора данных для анализа. При использовании диагностики Azure данные сразу записываются в Log Analytics, поэтому их не нужно сначала записывать в хранилище.

Ресурсы Azure с поддержкой [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) могут отправлять свои журналы и метрики напрямую в Log Analytics.

> [!NOTE]
> Отправка многомерных метрик в Log Analytics с помощью параметров диагностики сейчас не поддерживается. Метрики с измерениями экспортируются как преобразованные в плоскую структуру одномерные метрики, агрегированные по значениям измерений.
>
> *Пример.* Метрику "Входящие сообщения" в концентраторе событий можно изучить и вывести в виде диаграммы на уровне очереди. Но при экспорте с помощью параметров диагностики метрика представлена в виде всех входящих сообщений для всех очередей концентратора событий.
>
>

* Дополнительные сведения о доступных метриках см. в разделе [Метрики, поддерживаемые Azure Monitor](../monitoring-and-diagnostics/monitoring-supported-metrics.md).
* Дополнительные сведения о доступных журналах см. в разделе [Поддерживаемые службы и схемы для журналов диагностики](../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md).

### <a name="enable-diagnostics-with-powershell"></a>Включение диагностики с помощью PowerShell
Вам потребуется выпуск за ноябрь 2016 года (версия 2.3.0) и более поздний выпуск [Azure PowerShell](/powershell/azure/overview).

В следующем примере PowerShell показано, как включить диагностику в группе безопасности сети с использованием [Set-AzureRmDiagnosticSetting](/powershell/module/azurerm.insights/set-azurermdiagnosticsetting). Тот же подход работает для всех поддерживаемых ресурсов. Задайте для параметра `$resourceId` идентификатор ресурса, для которого нужно включить диагностику.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzureRmDiagnosticSetting -ResourceId $ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="enable-diagnostics-with-resource-manager-templates"></a>Включение диагностики с помощью шаблонов Resource Manager

Чтобы включить диагностику при создании ресурса и отправлять ее в рабочую область Log Analytics, можно использовать шаблон, аналогичный приведенному ниже. Этот пример предназначен для учетной записи службы автоматизации, но он также подходит для всех поддерживаемых типов ресурсов.

```json
        {
            "type": "Microsoft.Automation/automationAccounts/providers/diagnosticSettings",
            "name": "[concat(parameters('omsAutomationAccountName'), '/', 'Microsoft.Insights/service')]",
            "apiVersion": "2015-07-01",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('omsAutomationAccountName'))]",
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspaceName'))]"
            ],
            "properties": {
                "workspaceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('omsWorkspaceName'))]",
                "logs": [
                    {
                        "category": "JobLogs",
                        "enabled": true
                    },
                    {
                        "category": "JobStreams",
                        "enabled": true
                    }
                ]
            }
        }
```

[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="azure-diagnostics-to-storage-then-to-log-analytics"></a>Отправка диагностики Azure в хранилище и в Log Analytics

Для сбора журналов из некоторых ресурсов можно отправлять журналы в хранилище Azure, а затем настроить Log Analytics для чтения журналов из хранилища.

Этот подход можно использовать в Log Analytics для сбора диагностических данных из хранилища Azure для таких журналов и ресурсов:

| Ресурс | Журналы |
| --- | --- |
| Service Fabric |Событие трассировки событий Windows <br> Операционное событие <br> Событие субъектов Reliable Actors <br> Событие надежных служб |
| Виртуальные машины |Системный журнал Linux <br> Событие Windows <br> Журнал IIS <br> Событие трассировки событий Windows |
| веб-роли; <br> Рабочие роли |Системный журнал Linux <br> Событие Windows <br> Журнал IIS <br> Событие трассировки событий Windows |

> [!NOTE]
> За хранение и выполнение операций взимается стандартная плата Azure, если вы отправляете данные диагностики в учетную запись хранения, и служба Log Analytics считывает данные из вашей учетной записи хранения.
>
>

Дополнительные сведения о сборе этих журналов в Log Analytics см. в статье [Использование хранилища BLOB-объектов для IIS и хранилища таблиц для событий](log-analytics-azure-storage-iis-table.md).

## <a name="connectors-for-azure-services"></a>Соединители для служб Azure

Для Application Insights существует соединитель, позволяющий отправлять данные, собранные с помощью Application Insights, в Log Analytics.

Дополнительные сведения о [соединителе Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/).

## <a name="scripts-to-collect-and-post-data-to-log-analytics"></a>Сценарии для сбора и отправки данных в Log Analytics

Для служб Azure, в которых не предусмотрена отправка журналов и метрик в Log Analytics напрямую, можно использовать сценарий автоматизации Azure для сбора журналов и метрик. Сценарий может отправить данные в Log Analytics с помощью [API сборщика данных](log-analytics-data-collector-api.md).

В коллекции шаблонов Azure есть [примеры использования службы автоматизации Azure](https://azure.microsoft.com/resources/templates/?term=OMS) для сбора данных из служб и их отправки в Log Analytics.

## <a name="next-steps"></a>Дополнительная информация

* [Используйте хранилище BLOB-объектов для IIS и хранилище таблиц для событий](log-analytics-azure-storage-iis-table.md), чтобы считывать журналы служб Azure, которые записывают диагностические данные в табличное хранилище, или журналы IIS, записанные в хранилище BLOB-объектов.
* [Включите решения](log-analytics-add-solutions.md) , чтобы обеспечить глубокое понимание данных.
* [Воспользуйтесь запросами поиска](log-analytics-log-searches.md) для анализа данных.
