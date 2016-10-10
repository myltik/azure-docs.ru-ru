<properties
	pageTitle="Создание оповещений для служб Azure с помощью портала Azure | Microsoft Azure | Microsoft Azure"
    description="Используйте PowerShell для создания оповещений Azure, которые могут активировать уведомления или автоматизированные операции при выполнении заданных условий."
	authors="rboucher"
	manager=""
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/23/2016"
	ms.author="robb"/>

# Создание оповещений для служб Azure с помощью PowerShell 

> [AZURE.SELECTOR]
- [Портал](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](insights-alerts-command-line-interface.md)

## Обзор

В этой статье показано, как настроить оповещения Azure с помощью PowerShell.

Вы можете получать оповещения на основе отслеживания метрик или событий в службах Azure.

- **Значения метрик**. Оповещение активируется, когда значение указанной метрики выходит за рамки заданного порогового значения. То есть сначала оно активируется, когда условие выполняется, а затем — когда условие перестает выполняться.
- **События журнала действий**. Оповещение может активироваться при *каждом* событии или только тогда, когда выполняется определенное число событий.

Для оповещения можно настроить действие, выполняемое при активации оповещения:
 
- отправка уведомлений по электронной почте администратору службы и соадминистраторам;
- отправка уведомления на указанные дополнительные электронные адреса;
- вызов webhook;
- запуск выполнения Runbook Azure (только на портале Azure).

Для настройки правил генерации оповещений и получении сведений о них можно использовать:

- [Портал Azure](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [интерфейс командной строки (CLI)](insights-alerts-command-line-interface.md);
- [REST API Azure Insights](https://msdn.microsoft.com/library/azure/dn931945.aspx).


Чтобы получить дополнительную информацию, всегда можно ввести ```get-help``` и команду PowerShell, справку по которой требуется получить.

## Создание правил генерации оповещений в PowerShell

1. Войдите в Azure.
 
	```PowerShell
	Login-AzureRmAccount

	```

2. Получите список доступных вам подписок. Убедитесь, что вы работаете с нужной подпиской. В противном случае задайте нужную вам подписку, воспользовавшись выходными данными `Get-AzureRmSubscription`.

	```PowerShell
	Get-AzureRmSubscription
	Get-AzureRmContext
	Set-AzureRmContext -SubscriptionId <subscriptionid>
	```

3.  Чтобы получить список существующих правил для группы ресурсов, используйте следующую команду.

	```PowerShell
    Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
	```

4. Чтобы создать правило, необходимо сначала получить определенные важные сведения.
	- **Идентификатор ресурса**, для которого необходимо задать оповещение.
	- Доступные **определения метрик** для этого ресурса.
	
	Получить идентификатор ресурса можно на портале Azure. Если ресурс уже создан, выберите его на портале. В следующей колонке в разделе *Параметры* выберите *Свойства*. В следующей колонке отображается поле "Идентификатор ресурса". Кроме того, для получения идентификатора ресурса можно использовать [Azure Resource Explorer](https://resources.azure.com/).

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
    Полный список доступных параметров Get-AzureRmMetricDefinition можно получить, выполнив Get-MetricDefinitions.

 
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


7. Чтобы создать оповещение, которое активируется при возникновении определенных условий в журнале действий, используйте следующую команду.
 	
	```PowerShell
	$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
	$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

	Add-AzureRmLogAlertRule -Name myLogAlertRule -Location "East US" -ResourceGroup myresourcegroup -OperationName microsoft.web/sites/start/action -Status Succeeded -TargetResourceGroup resourcegroupbeingmonitored -Actions $actionEmail, $actionWebhook
	```

    -OperationName соответствует типу события в журнале действий. Примеры: *Microsoft.Compute/virtualMachines/delete*, *microsoft.insights/diagnosticSettings/write*.

    Можно использовать команду PowerShell [Get AzureRmProviderOperation](https://msdn.microsoft.com/library/mt603720.aspx) для получения списка возможных имен операций. Кроме того, можно использовать портал Azure, чтобы запросить журнал действий и найти определенные прошедшие операции, для которых требуется создать оповещение. Операции отображаются в графическом представлении журнала с понятными именами. Найдите в тексте JSON нужную запись и получите значение имя\_операции.

8. Можно проверить, правильно ли созданы оповещения, просмотрев отдельные правила.

	```PowerShell
    Get-AzureRmAlertRule -Name myMetricRuleWithWebhookAndEmail -ResourceGroup myresourcegroup -DetailedOutput

	Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
	```

9. Удалите свои оповещения. Приведенные ниже команды удаляют правила, которые были созданы ранее в этой статье.

	```PowerShell
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myrule
	Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myMetricRuleWithWebhookAndEmail
	Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myLogAlertRule
	```

## Дальнейшие действия

* [Ознакомьтесь с общими сведениями о мониторинге Azure](monitoring-overview.md), включая типы информации, которую можно собирать и отслеживать.
* Узнайте больше о [настройке веб-перехватчиков webhook в оповещениях](insights-webhooks-alerts.md).
* Узнайте больше о [модулях Runbook службы автоматизации Azure](..\automation\automation-starting-a-runbook.md).
* Ознакомьтесь с [обзором сбора журналов диагностики](monitoring-overview-of-diagnostic-logs.md), чтобы собирать подробные метрики о службе с высокой частотой.
* Прочитайте [обзор сбора метрики](insights-how-to-customize-monitoring.md) и узнайте, как можно обеспечить, чтобы служба была доступна и отвечала на запросы.

<!---HONumber=AcomDC_0928_2016-->