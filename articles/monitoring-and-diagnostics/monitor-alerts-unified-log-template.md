---
title: Создание оповещения журнала с помощью шаблона Resource Manager
description: Узнайте, как создать оповещение журнала с помощью шаблона Azure Resource Manager и API.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 5afa34a5eadf5367b3ab28749735197ca6ed82bd
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263207"
---
# <a name="create-a-log-alert-with-a-resource-manager-template"></a>Создание оповещения журнала с помощью шаблона Resource Manager
В этой статье показано, как программно управлять [оповещениями журнала](monitor-alerts-unified-log.md) в соответствующем масштабе в Azure с помощью [шаблона Azure Resource Manager](..//azure-resource-manager/resource-group-authoring-templates.md) и [Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) или [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md). Сейчас функция "Оповещения Azure" поддерживает оповещения журнала в запросах из [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) и [Azure Application Insights](../application-insights/app-insights-analytics-tour.md).

## <a name="managing-log-alert-on-log-analytics"></a>Управление оповещениями журнала в Log Analytics
Оповещения журнала для [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) интегрированы в [новый интерфейс функции "Оповещения Azure"](monitoring-overview-unified-alerts.md). При этом по-прежнему предоставляются интерфейсы API Log Analytics и сохраняется совместимость со схемой, используемой ранее для управления [оповещениями на портале OMS](..//log-analytics/log-analytics-alerts-creating.md).

> [!NOTE]
> Начиная с 14 мая 2018 года все оповещения в рабочей области будут автоматически распространяться в Azure. Пользователь может по собственному желанию инициировать отображение оповещений в Azure до 14 мая 2018 года. Дополнительные сведения см. в разделе [Extend (copy) alerts from OMS portal into Azure](monitoring-alerts-extend.md) (Отображение (копирование) оповещений с портала OMS в Azure). 

### <a name="using-azure-resource-manager-template"></a>Использование шаблона Azure Resource Manager
Оповещения журнала для Log Analytics создаются правилами генерации оповещений, которые выполняют сохраненный поиск через равные промежутки времени. Если результаты запроса соответствуют указанным условиям, то создается запись оповещения и выполняются одно или несколько действий. 

Шаблон ресурсов для [сохраненного поиска Log Analytics](../log-analytics/log-analytics-log-searches.md) и [оповещений Log Analytics](../log-analytics/log-analytics-alerts.md) доступен в разделе документации по Log Analytics. Узнайте больше о [добавлении сохраненных поисков и оповещений Log Analytics](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md), включая демонстрационные примеры и сведения о схеме.

### <a name="using-resource-template-via-apipowershell"></a>Использование шаблона ресурсов с помощью API или PowerShell
В службе Log Analytics используется REST API, поддерживающий RESTful. К нему можно получить доступ с помощью REST API Azure Resource Manager. Следовательно, к этому API можно обратиться из командной строки PowerShell, и он будет выдавать результаты поиска в формате JSON, позволяя программно использовать эти результаты различными способами.

Ознакомьтесь с разделом [Создание правил генерации оповещений и управление ими в Log Analytics с помощью REST API](../log-analytics/log-analytics-api-alerts.md), включая примеры доступа к API из PowerShell.

## <a name="managing-log-alert-on-application-insights"></a>Управление оповещением журнала в Application Insights
Оповещения журнала для Azure Application Insights были представлены как часть новых оповещений Azure в Azure Monitor. Таким образом, они выполняются в API Azure Monitor как группа операций REST для [правил запланированных запросов](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/).

### <a name="using-azure-resource-manager-template"></a>Использование шаблона Azure Resource Manager
Оповещение журнала для ресурсов Application Insights имеет тип `Microsoft.Insights/scheduledQueryRules/`. Дополнительные сведения о данном типе ресурса приведены в разделе [Scheduled Query Rules](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/) (Правила запланированных запросов).

Ниже приведена структура для [создания правил запланированных запросов](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/createorupdate) на основе шаблона ресурсов, содержащего пример набора данных в качестве переменных.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0", 
    "parameters": {      
    },   
    "variables": {
    "alertLocation": "southcentralus",
    "alertName": "samplelogalert",
    "alertTag": "hidden-link:/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
    "alertDesription": "Sample log search alert",
    "alertStatus": "true",
    "alertSource":{
        "Query":"requests",
        "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
        "Type":"ResultCount"
         },
     "alertSchedule":{
         "Frequency": 15,
         "Time": 60
         },
     "alertActions":{
         "SeverityLevel": "4"
         },
      "alertTrigger":{
        "Operator":"GreaterThan",
        "Threshold":"1"
         },
       "actionGrp":{
        "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/actiongroups/sampleAG",
        "Subject": "Customized Email Header",
        "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"           
         }
  },
  "resources":[ {
    "name":"[variables('alertName')]",
    "type":"Microsoft.Insights/scheduledQueryRules",
    "apiVersion": "2018-04-16",
    "location": "[variables('alertLocation')]",
    "tags":{"[variables('alertTag')]": "Resource"},
    "properties":{
       "description": "[variables('alertDesription')]",
       "enabled": "[variables('alertStatus')]",
       "source": {
           "query": "[variables('alertSource').Query]",
           "dataSourceId": "[variables('alertSource').SourceId]",
           "queryType":"[variables('alertSource').Type]"
       },
      "schedule":{
           "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
           "timeWindowInMinutes": "[variables('alertSchedule').Time]"    
       },
      "action":{
           "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
           "severity":"[variables('alertActions').SeverityLevel]",
           "aznsAction":{
               "actionGroup":"[array(variables('actionGrp').ActionGroup)]",
               "emailSubject":"[variables('actionGrp').Subject]",
               "customWebhookPayload":"[variables('actionGrp').Webhook]"
           },
       "trigger":{
               "thresholdOperator":"[variables('alertTrigger').Operator]",
               "threshold":"[variables('alertTrigger').Threshold]"
           }
       }
     }
   }
 ]
}
```
> [!IMPORTANT]
> Поле тега со скрытой ссылкой на целевой ресурс является обязательным при вызове API [правил запланированных запросов](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/) или использовании шаблона ресурсов. 

В приведенном выше примере JSON можно сохранить как sampleScheduledQueryRule.json, чтобы использовать его в данном пошаговом руководстве. Его можно развернуть с помощью [Azure Resource Manager на портале Azure](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

### <a name="using-resource-template-via-clipowershell"></a>Использование шаблона ресурсов с помощью интерфейса командной строки или PowerShell
API правил запланированных вызовов Azure Monitor — это интерфейс REST API, который полностью совместим с REST API Azure Resource Manager. Поэтому он может использоваться в PowerShell с помощью командлета Resource Manager, а также в Azure CLI.

Ниже показано использование командлета PowerShell для Azure Resource Manager для примера шаблона ресурсов, приведенного выше (sampleScheduledQueryRule.json).
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile "D:\Azure\Templates\sampleScheduledQueryRule.json"
```
Ниже показано использование команды Azure Resource Manager в Azure CLI для примера шаблона ресурсов, приведенного выше (sampleScheduledQueryRule.json).

```azurecli
az group deployment create --resource-group myRG --template-file sampleScheduledQueryRule.json
```
После успешного выполнения возвращается код 201, который означает, что новое правило создано. Если было изменено существующее правило генерации оповещений, то возвращается код 200.


## <a name="next-steps"></a>Дополнительная информация
* Общие сведения о [действиях веб-перехватчиков для оповещений журнала](monitor-alerts-unified-log-webhook.md)
* Узнайте больше о новой функции [Оповещения Azure](monitoring-overview-unified-alerts.md).
* Дополнительные сведения об [Application Insights](../application-insights/app-insights-analytics.md)
* Дополнительные сведения о [Log Analytics](../log-analytics/log-analytics-overview.md).   
