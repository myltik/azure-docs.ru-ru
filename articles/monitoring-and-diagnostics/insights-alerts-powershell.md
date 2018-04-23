---
title: Создание оповещений для служб Azure с помощью PowerShell | Документация Майкрософт
description: Узнайте, как активировать сообщения электронной почты, уведомления, вызовы URL-адресов веб-сайтов (webhook) или автоматизированные операции при выполнении заданных условий.
author: rboucher
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d26ab15b-7b7e-42a9-81c8-3ce9ead5d252
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2018
ms.author: robb
ms.openlocfilehash: ca9df36c642334d9323736abb51a931a6610fd3e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="create-classic-metric-alerts-in-azure-monitor-for-azure-services---powershell"></a>Создание классических оповещений на основе метрик в Azure Monitor для служб Azure с помощью PowerShell
> [!div class="op_single_selector"]
> * [Портал](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](insights-alerts-command-line-interface.md)
>
>

## <a name="overview"></a>Обзор

> [!NOTE]
> В этой статье объясняется, как создавать классические оповещения метрик прежней версии. Azure Monitor теперь поддерживает [новые улучшенные оповещения на основе метрик](monitoring-near-real-time-metric-alerts.md). Эти оповещения поддерживают мониторинг нескольких метрик и охватывают метрики размерностей. В PowerShell поддержка новых оповещений на основе метрик ожидается в ближайшее время.
>
>

В этой статье показано, как настроить классические оповещения метрик Azure с помощью PowerShell.  

Вы можете получать оповещения на основе отслеживания метрик или событий в службах Azure.

* **Значения метрик**. Оповещение активируется, когда значение указанной метрики выходит за рамки заданного порогового значения. То есть сначала оно активируется, когда условие выполняется, а затем — когда условие перестает выполняться.    
* **События журнала действий**. Оповещение может активироваться при *каждом* событии или только тогда, когда выполняется определенное событие. Чтобы узнать больше об оповещениях журнала действий, [щелкните здесь](monitoring-activity-log-alerts.md).

Для классического оповещения метрики можно настроить действие, выполняемое при его активации:

* отправка уведомлений по электронной почте администратору службы и соадминистраторам;
* отправка уведомления на указанные дополнительные электронные адреса;
* вызов webhook;
* запуск выполнения Runbook Azure (только на портале Azure).

Для настройки правил генерации оповещений и получении сведений о них можно использовать:

* [портал Azure](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [интерфейс командной строки (CLI)](insights-alerts-command-line-interface.md)
* [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

Чтобы получить дополнительную информацию, всегда можно ввести ```Get-Help``` и команду PowerShell, справку по которой требуется получить.

## <a name="create-alert-rules-in-powershell"></a>Создание правил генерации оповещений в PowerShell
1. Войдите в Azure.   

    ```PowerShell
    Connect-AzureRmAccount

    ```
2. Получите список доступных вам подписок. Убедитесь, что вы работаете с нужной подпиской. В противном случае задайте нужную вам подписку, воспользовавшись выходными данными `Get-AzureRmSubscription`.

    ```PowerShell
    Get-AzureRmSubscription
    Get-AzureRmContext
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```
3. Чтобы получить список существующих правил для группы ресурсов, используйте следующую команду.

   ```PowerShell
   Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
   ```
4. Чтобы создать правило, необходимо сначала получить определенные важные сведения.

  * **Идентификатор ресурса** , для которого необходимо задать оповещение.
  * Доступные **определения метрик** для этого ресурса.

     Получить идентификатор ресурса можно на портале Azure. Если ресурс уже создан, выберите его на портале. В следующей колонке в разделе *Параметры* выберите *Свойства*. В следующей колонке отображается поле **Идентификатор ресурса**. Кроме того, для получения идентификатора ресурса можно использовать [Azure Resource Explorer](https://resources.azure.com/).

     Ниже приведен пример идентификатора ресурса для веб-приложения.

     ```
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     С помощью `Get-AzureRmMetricDefinition` можно просмотреть список всех определений метрик для конкретного ресурса.

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id>
     ```

     В следующем примере создается таблица, содержащая имена метрик и их единицы измерения.

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit

     ```
     Полный список доступных параметров Get-AzureRmMetricDefinition можно получить, выполнив `Get-Help Get-AzureRmMetricDefinition -Detailed`.
5. Следующий пример настраивает оповещение о ресурсе веб-сайта. Оповещение активируется, когда любой трафик непрерывно поступает в течении 5 минут, и затем снова активируется, если в течение 5 минут трафик не поступает.

    ```PowerShell
    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Description "alert on any website activity"

    ```
6. Чтобы при активации оповещения создавался webhook или отправлялось электронное сообщение, необходимо сначала создать электронный адрес и (или) веб-перехватчики webhook. Сразу после этого создайте правило с тегом -Actions, как показано в следующем примере. С помощью PowerShell невозможно связать webhook или электронные адреса с уже созданными правилами.

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail, $actionWebhook -Description "alert on any website activity"
    ```

7. Можно проверить, правильно ли созданы оповещения, просмотрев отдельные правила.

    ```PowerShell
    Get-AzureRmAlertRule -Name myMetricRuleWithWebhookAndEmail -ResourceGroup myresourcegroup -DetailedOutput

    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```
8. Удалите свои оповещения. Приведенные ниже команды удаляют правила, которые были созданы ранее в этой статье.

    ```PowerShell
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myrule
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myMetricRuleWithWebhookAndEmail
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myLogAlertRule
    ```

## <a name="next-steps"></a>Дополнительная информация
* [Ознакомьтесь с общими сведениями о мониторинге Azure](monitoring-overview.md) , включая типы информации, которую можно собирать и отслеживать.
* Узнайте, как [настроить веб-перехватчики в оповещениях](insights-webhooks-alerts.md).
* Узнайте, как [настроить оповещения о событиях журнала действий](monitoring-activity-log-alerts.md).
* Узнайте больше о [модулях Runbook службы автоматизации Azure](../automation/automation-starting-a-runbook.md).
* Ознакомьтесь с [обзором сбора журналов диагностики](monitoring-overview-of-diagnostic-logs.md) , чтобы собирать подробные метрики о службе с высокой частотой.
* Прочитайте [обзор сбора метрики](insights-how-to-customize-monitoring.md) и узнайте, как можно обеспечить, чтобы служба была доступна и отвечала на запросы.
