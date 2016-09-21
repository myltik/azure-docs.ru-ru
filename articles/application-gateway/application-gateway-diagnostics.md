<properties 
   pageTitle="Наблюдение за журналами доступа и производительности для шлюза приложений | Microsoft Azure"
   description="Узнайте, как включить журналы доступа и производительности для шлюза приложений и управлять ими."
   services="application-gateway"
   documentationCenter="na"
   authors="amitsriva"
   manager="rossort"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/02/2016"
   ms.author="amitsriva" />

# Ведение журнала диагностики для шлюза приложений

В Azure можно использовать различные виды журналов для управления шлюзами приложений и устранения возникающих в них неполадок. Доступ к некоторым из этих журналов можно получить через портал. Все журналы можно извлекать из хранилища BLOB-объектов Azure и просматривать с помощью разных средств, включая [аналитику журналов](../log-analytics/log-analytics-azure-networking-analytics.md), Excel и PowerBI. В списке ниже приведены дополнительные сведения о разных типах журналов.

- **Журналы аудита.** В [журналах аудита Azure](../azure-portal/insights-debugging-with-events.md) (прежнее название — операционные журналы) можно просматривать все операции, отправляемые в вашу подписку Azure, и состояние этих операций. По умолчанию журналы аудита включены, и их можно просмотреть на портале предварительной версии Azure.
- **Журналы доступа**. С помощью этого журнала можно просматривать схему доступа к шлюзу приложений и анализировать важную информацию, включая IP-адрес вызывающей стороны, запрошенный URL-адрес, сведения о задержке отклика, возвращаемом коде и переданных и полученных байтах. Данные для журнала доступа собираются каждые 300 секунд. Этот журнал содержит одну запись для каждого экземпляра шлюза приложений. Экземпляр шлюза приложений определяется свойством instanceId.
- **Журналы производительности**. С помощью этого журнала можно просматривать, как работают экземпляры шлюза приложений. Этот журнал записывает сведения о производительности каждого экземпляра, включая общее число обработанных запросов, пропускную способность в байтах, число неудачных запросов, а также число работоспособных и неработоспособных серверных экземпляров. Данные для журнала производительности собираются каждые 60 секунд.

>[AZURE.WARNING] Журналы доступны только для ресурсов, развернутых в модели развертывания диспетчера ресурсов. Журналы нельзя использовать для ресурсов в классической модели развертывания. Чтобы получить более полное представление об этих двух моделях, см. статью [Общие сведения о развертывании диспетчера ресурсов и классическом развертывании](../resource-manager-deployment-model.md).

## Включение ведения журналов
Ведение журналов автоматически включается для каждого ресурса Resource Manager. Нужно включить ведение журналов доступа и производительности, чтобы начать сбор связанных данных. Вот как включить ведение журналов.

1. Запишите идентификатор ресурсов вашей учетной записи хранилища, где хранятся данные журнала, в формате: /subscriptions/<идентификатор\_подписки>/resourceGroups/<имя\_группы\_ресурсов>/providers/Microsoft.Storage/storageAccounts/<имя\_учетной\_записи\_хранения>. Можно использовать любую учетную запись хранения в подписке. Получить эти сведения можно на портале предварительной версии.

	![Портал предварительной версии — диагностика шлюза приложений](./media/application-gateway-diagnostics/diagnostics1.png)

2. Запишите или запомните идентификатор ресурса шлюза приложений, для которого включено ведение журнала. Формат: /subscriptions/<идентификатор\_подписки>/resourceGroups/<имя\_группы\_ресурсов>/providers/Microsoft.Network/applicationGateways/<имя\_шлюза\_приложений>. Получить эти сведения можно на портале предварительной версии.

	![Портал предварительной версии — диагностика шлюза приложений](./media/application-gateway-diagnostics/diagnostics2.png)

3. Включите ведение журнала диагностики с помощью следующего командлета PowerShell.

		Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true 	

>[AZURE.INFORMATION] Для журналов аудита отдельная учетная запись хранения не требуется. За использование хранилища для журналов доступа и производительности взимается плата.

## Журнал аудита

Этот журнал (прежнее название — «операционный журнал») создается в Azure по умолчанию. Журналы хранятся в течение 90 дней в хранилище журналов событий Azure. Дополнительные сведения об этих журналах см. в статье [Просмотр журналов событий и аудита](../azure-portal/insights-debugging-with-events.md).

## Журнал доступа

Этот журнал создается, только если он включен для конкретного шлюза приложений, как описано выше. Данные хранятся в учетной записи хранения, указанной при включении ведения журнала. Каждая операция доступа шлюза приложений регистрируется в журнале в формате JSON, как показано ниже.

	{
		"resourceId": "/SUBSCRIPTIONS/<subscription id>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<application gateway name>",
		"operationName": "ApplicationGatewayAccess",
		"time": "2016-04-11T04:24:37Z",
		"category": "ApplicationGatewayAccessLog",
		"properties": {
			"instanceId":"ApplicationGatewayRole_IN_0",
			"clientIP":"37.186.113.170",
			"clientPort":"12345",
			"httpMethod":"HEAD",
			"requestUri":"/xyz/portal",
			"requestQuery":"",
			"userAgent":"-",
			"httpStatus":"200",
			"httpVersion":"HTTP/1.0",
			"receivedBytes":"27",
			"sentBytes":"202",
			"timeTaken":"359",
			"sslEnabled":"off"
		}
	}

## Журнал производительности

Этот журнал создается, только если он включен для конкретного шлюза приложений, как описано выше. Данные хранятся в учетной записи хранения, указанной при включении ведения журнала. В журнал записываются следующие данные:

	{
		"resourceId": "/SUBSCRIPTIONS/<subscription id>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<application gateway name>",
		"operationName": "ApplicationGatewayPerformance",
		"time": "2016-04-09T00:00:00Z",
		"category": "ApplicationGatewayPerformanceLog",
		"properties": 
		{
			"instanceId":"ApplicationGatewayRole_IN_1",
			"healthyHostCount":"4",
			"unHealthyHostCount":"0",
			"requestCount":"185",
			"latency":"0",
			"failedRequestCount":"0",
			"throughput":"119427"
		}
	}

## Просмотр и анализ журнала аудита

Данные журнала аудита можно просматривать и анализировать с помощью любого из следующих методов.

- **Средства Azure.** Информацию из журналов аудита можно получать с помощью Azure PowerShell, интерфейса командной строки (CLI) Azure, интерфейса REST API Azure или портала предварительной версии Azure. Пошаговые инструкции для каждого метода подробно описаны в статье [Операции аудита с помощью диспетчера ресурсов](../resource-group-audit.md).
- **Power BI.** Если у вас еще нет учетной записи [Power BI](https://powerbi.microsoft.com/pricing), ее можно опробовать бесплатно. Используя [пакет содержимого журналов аудита Azure для Power BI](https://powerbi.microsoft.com/ru-RU/documentation/powerbi-content-pack-azure-audit-logs/), можно анализировать данные с помощью предварительно настроенных панелей мониторинга, которые можно использовать как есть или дополнительно настроить.

## Просмотр и анализ журналов доступа и производительности

Служба [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) в Azure собирает файлы журнала событий и счетчиков из вашей учетной записи хранения BLOB-объектов. Она включает в себя средства визуализации и мощные инструменты поиска для анализа журналов.

Вы также можете подключиться к учетной записи хранения и извлечь записи журнала JSON для журналов доступа и производительности. После загрузки JSON-файлов их можно преобразовать в формат CSV и просматривать в Excel, PowerBI или другом средстве визуализации данных.

>[AZURE.TIP] Если вы знакомы с Visual Studio и основными понятиями изменения значений констант и переменных в C#, можно использовать [средства преобразования журнала](https://github.com/Azure-Samples/networking-dotnet-log-converter), доступные на сайте GitHub.

## Дальнейшие действия

- Визуализация журналов событий и счетчиков с помощью [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md).
- Запись блога [Визуализация журналов аудита Azure с помощью Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx).
- Запись блога [Просмотр и анализ журналов аудита Azure с помощью Power BI и других средств](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).

<!---HONumber=AcomDC_0907_2016-->