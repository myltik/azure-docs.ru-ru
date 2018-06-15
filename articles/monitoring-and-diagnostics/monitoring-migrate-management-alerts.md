---
title: Перенос оповещений Azure о событиях управления в оповещения журнала действий
description: Оповещения о событиях управления будут удалены 1 октября. Подготовьтесь к этому, перенеся существующие оповещения.
author: johnkemnetz
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/14/2017
ms.author: johnkem
ms.component: alerts
ms.openlocfilehash: 9e4302b780d0c08afbc791a0aec6bfd806aba161
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263710"
---
# <a name="migrate-azure-alerts-on-management-events-to-activity-log-alerts"></a>Перенос оповещений Azure о событиях управления в оповещения журнала действий


> [!WARNING]
> Оповещения о событиях управления будут отключены в течение или после 1 октября. Чтобы понять, есть ли у вас такие оповещения, и перенести их, если они имеются, следуйте приведенным ниже инструкциям.
>
> 

## <a name="what-is-changing"></a>Изменения

В Azure Monitor (прежнее название — Azure Insights) предоставлялась возможность создать оповещение, которое активировалось событиями управления и отправляло уведомления на URL-адрес веб-перехватчика или адреса электронной почты. Эти оповещения можно было создать одним из следующих способов:
* На портале Azure для определенных типов ресурсов, выбрав "Мониторинг" > "Оповещения" > "Добавить оповещение" и задав для параметра "Оповещения включены" значение "События".
* Выполнив командлет PowerShell Add-AzureRmLogAlertRule.
* Непосредственно, используя [REST API оповещений](http://docs.microsoft.com/rest/api/monitor/alertrules) с odata.type = "ManagementEventRuleCondition" и dataSource.odata.type = "RuleManagementEventDataSource".
 
Следующий сценарий PowerShell возвращает список всех оповещений о событиях управления, имеющихся в подписке, а также условий, заданных для каждого оповещения.

```powershell
Connect-AzureRmAccount
$alerts = $null
foreach ($rg in Get-AzureRmResourceGroup ) {
  $alerts += Get-AzureRmAlertRule -ResourceGroup $rg.ResourceGroupName
}
foreach ($alert in $alerts) {
  if($alert.Properties.Condition.DataSource.GetType().Name.Equals("RuleManagementEventDataSource")) {
    "Alert Name: " + $alert.Name
    "Alert Resource ID: " + $alert.Id
    "Alert conditions:"
    $alert.Properties.Condition.DataSource
    "---------------------------------"
  }
} 
```

При отсутствии оповещений о событиях управления приведенный выше командлет PowerShell выдаст ряд предупреждений следующего вида.

`WARNING: The output of this cmdlet will be flattened, i.e. elimination of the properties field, in a future release to improve the user experience.`

Эти предупреждения можно игнорировать. При наличии оповещений о событиях управления выходные данные этого командлета PowerShell будет выглядеть следующим образом.

```
Alert Name: webhookEvent1
Alert Resource ID: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/alertrules/webhookEvent1
Alert conditions:

EventName            : 
EventSource          : 
Level                : 
OperationName        : microsoft.web/sites/start/action
ResourceGroupName    : 
ResourceProviderName : 
Status               : succeeded
SubStatus            : 
Claims               : Microsoft.Azure.Management.Monitor.Management.Models.RuleManagementEventClaimsDataSource
ResourceUri          : /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Web/sites/samplealertapp

---------------------------------
Alert Name: someclilogalert
Alert Resource ID: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/alertrules/someclilogalert
Alert conditions:

EventName            : 
EventSource          : 
Level                : 
OperationName        : Start
ResourceGroupName    : 
ResourceProviderName : 
Status               : 
SubStatus            : 
Claims               : Microsoft.Azure.Management.Monitor.Management.Models.RuleManagementEventClaimsDataSource
ResourceUri          : /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Compute/virtualMachines/Seaofclouds

---------------------------------
```

Оповещения разделены пунктирной линией, и сведения о них включают в себя идентификатор ресурса оповещения и определенное отслеживаемое правило.

Эта функциональная возможность перенесена в [оповещения журнала действий Azure Monitor](monitoring-activity-log-alerts.md). Эти новые оповещения позволяют задать условие для событий журнала действий и получать уведомление, когда новое событие соответствует этому условию. Они также предоставляют ряд усовершенствований по сравнению с оповещениями о событиях управления.
* Вы можете использовать группу получателей уведомлений ("действий") для нескольких оповещений с помощью [групп действий](monitoring-action-groups.md). Это упрощает изменение получателей оповещения.
* Вы можете получать уведомления непосредственно на телефон в виде SMS с помощью группы действий.
* Вы можете [создавать оповещения журнала действий с помощью шаблонов Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md).
* Вы можете создавать более гибкие и сложные условия в соответствии с конкретными потребностями.
* Уведомления доставляются быстрее.
 
## <a name="how-to-migrate"></a>Как осуществить перенос
 
Чтобы создать новое оповещение журнала действий, вы можете сделать следующее.
* Следуйте [нашим указаниям по созданию оповещения на портале Azure](monitoring-activity-log-alerts.md).
* Узнайте, как [создать оповещение с помощью шаблона Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md).
 
Оповещения о событиях управления, которые были созданы ранее, не будут автоматически перенесены в оповещения журнала действий. Необходимо использовать приведенный выше сценарий PowerShell, чтобы получить список оповещений о событиях управления, настроенных на текущий момент, и вручную воссоздать их в виде оповещений журнала действий. Это необходимо сделать до 1 октября, после чего оповещения о событиях управления больше не будут отображаться в вашей подписке Azure. Другие типы оповещений Azure, включая оповещения о метриках Azure Monitor, оповещения Application Insights и оповещения Log Analytics, это изменение не затрагивает. Если у вас возникли вопросы, добавьте их в комментариях ниже.


## <a name="next-steps"></a>Дополнительная информация

* Узнайте больше о [журнале действий](monitoring-overview-activity-logs.md).
* Настройте [оповещения журнала действий на портале Azure](monitoring-activity-log-alerts.md).
* Настройте [оповещения журнала действий с помощью Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md).
* Просмотрите [схему webhook оповещений журнала действий](monitoring-activity-log-alerts-webhook.md).
* Узнайте больше об [уведомлениях службы](monitoring-service-notifications.md).
* Узнайте больше о [группах действий](monitoring-action-groups.md).
