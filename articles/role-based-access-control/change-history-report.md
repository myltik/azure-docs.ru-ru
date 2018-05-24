---
title: Просмотр журналов действий на наличие изменений в управлении доступом на основе ролей в Azure | Документы Майкрософт
description: Вы можете просматривать журналы действий на наличие изменений в управлении доступом за последние 90 дней.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/23/2017
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e48ea2293c186bbc337f9d70464df374d64b5e61
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34203909"
---
# <a name="view-activity-logs-for-role-based-access-control-changes"></a>Просмотр журналов действий на наличие изменений в управлении доступом

Каждый раз, когда кто-либо вносит изменения в определения ролей или назначения ролей в ваших подписках, изменения регистрируются в [журнале действий Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) в категории "Административная". Вы можете просматривать журналы действий, чтобы увидеть все изменения в управлении доступом на основе ролей (RBAC) за последние 90 дней.

## <a name="operations-that-are-logged"></a>Операции, которые регистрируются в журнале

Ниже приведены операции, связанные с RBAC, которые регистрируются в журнале действий.

- Создание или изменение определения пользовательской роли
- Удаление определения пользовательской роли
- Создание назначения роли
- Удаление назначения роли

## <a name="azure-portal"></a>Портал Azure

Самый простой способ заключается в просмотре журналов действий на портале Azure. На следующем снимке экрана показан пример журнала действий, отфильтрованный для отображения категории **Административная** вместе с операциями определения и назначений ролей. Он также содержит ссылку для скачивания журналов в виде CSV-файла.

![Снимок экрана: просмотр журналов действий на портале](./media/change-history-report/activity-log-portal.png)

Дополнительные сведения см. в статье [Просмотр событий в журнале действий](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json).

## <a name="azure-powershell"></a>Azure PowerShell

Чтобы просмотреть журналы действий с помощью Azure PowerShell, используйте команду [Get-AzureRmLog](/powershell/module/azurerm.insights/get-azurermlog).

Эта команда выводит список всех изменений назначений ролей в подписке за последние семь дней.

```azurepowershell
Get-AzureRmLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Эта команда выводит список всех изменений определений ролей в подписке за последние семь дней.

```azurepowershell
Get-AzureRmLog -ResourceGroupName pharma-sales-projectforecast -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

Эта команда выводит список всех изменений назначений ролей и определений ролей в подписке за последние семь дней.

```azurepowershell
Get-AzureRmLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

```Example
Caller                  : alain@example.com
EventTimestamp          : 4/20/2018 9:18:07 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: 11111111-1111-1111-1111-111111111111

Caller                  : alain@example.com
EventTimestamp          : 4/20/2018 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast"}}

```

## <a name="azure-cli"></a>Инфраструктура CLI Azure

Чтобы просмотреть журналы действий с помощью Azure CLI, используйте команду [az monitor activity-log list](/cli/azure/monitor/activity-log#az-monitor-activity-log-list).

Эта команда выводит список журналов действий в группе ресурсов со времени начала.

```azurecli
az monitor activity-log list --resource-group pharma-sales-projectforecast --start-time 2018-04-20T00:00:00Z
```

Эта команда выводит список журналов действий для поставщика ресурсов авторизации со времени начала.

```azurecli
az monitor activity-log list --resource-provider "Microsoft.Authorization" --start-time 2018-04-20T00:00:00Z
```

## <a name="azure-log-analytics"></a>Azure Log Analytics

Средство [Azure Log Analytics](../log-analytics/log-analytics-overview.md) можно использовать для сбора и анализа изменений управления доступом на основе ролей для всех ресурсов Azure. Log Analytics имеет следующие преимущества:

- создание сложных запросов и логики;
- интеграция с оповещениями, Power BI и другими средствами;
- хранение данных в течение более длительного срока;
- поддержка перекрестных ссылок с другими журналами, такими как журналы безопасности, журналы виртуальных машин и настраиваемые журналы.

Ниже приведены основные шаги, позволяющие приступить к работе.

1. [Создание рабочей области Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md).

1. [Настройка решения аналитики журнала действий](../log-analytics/log-analytics-activity.md#configuration) для рабочей области.

1. [Просмотр журналов действий](../log-analytics/log-analytics-activity.md#using-the-solution). Чтобы быстро перейти к странице "Обзор Activity Log Analytics", нажмите кнопку **Log Analytics**.

   ![Кнопка "Log Analytics" на портале](./media/change-history-report/azure-log-analytics-option.png)

1. При необходимости используйте страницу [Поиска по журналам](../log-analytics/log-analytics-log-search.md) или [портал расширенной аналитики](https://docs.loganalytics.io/docs/Learn) для формирования запросов и просмотра журналов. Дополнительные сведения об этих двух вариантах см. в разделах о [странице "Поиск по журналам" или портале расширенной аналитики](../log-analytics/log-analytics-log-search-portals.md).

Ниже приведен запрос, возвращающий новые назначения ролей, упорядоченные по целевому поставщику ресурсов.

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationName startswith "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

Ниже приведен запрос, возвращающий изменения назначения ролей, отображаемые на схеме.

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationName startswith "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![Снимок экрана: журналы действий на портале расширенной аналитики](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>Дополнительная информация
* [Просмотр событий в журнале действий](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Monitor Subscription Activity with the Azure Activity Log](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (Мониторинг действий подписки с помощью журнала действий Azure)
