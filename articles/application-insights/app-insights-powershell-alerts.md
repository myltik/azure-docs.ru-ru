<properties 
	pageTitle="Настройка оповещений в Application Insights с помощью PowerShell" 
	description="Автоматизация настройки Application Insights для получения сообщений электронной почты об изменениях метрик." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/19/2016" 
	ms.author="awills"/>
 
# Настройка оповещений в Application Insights с помощью PowerShell

Вы можете автоматизировать настройку [оповещений](app-insights-alerts.md) в [Visual Studio Application Insights](app-insights-overview.md).

Кроме того, вы можете [установить веб-перехватчики для автоматизации реагирования на оповещение](../azure-portal/insights-webhooks-alerts.md).

## Однократная настройка

Если вы ранее не использовали PowerShell для подписки Azure:

Установите модуль Azure Powershell на компьютере, где требуется выполнять сценарии.

 * Установите [установщик веб-платформы Майкрософт (версии 5 или более поздней)](http://www.microsoft.com/web/downloads/platform.aspx).
 * Используйте его для установки Microsoft Azure PowerShell.


## Подключение к Azure

Запустите Azure PowerShell и [подключитесь к вашей подписке](../powershell-install-configure.md).

```PowerShell

    Add-AzureAccount
    Switch-AzureMode AzureResourceManager
```


## Получение оповещений

    Get-AlertRule -ResourceGroup "Fabrikam" [-Name "My rule"] [-DetailedOutput]

## Добавление оповещения


    Add-AlertRule  -Name "{ALERT NAME}" -Description "{TEXT}" `
     -ResourceGroup "{GROUP NAME}" `
     -ResourceId "/subscriptions/{SUBSCRIPTION ID}/resourcegroups/{GROUP NAME}/providers/microsoft.insights/components/{APP RESOURCE NAME}" `
     -MetricName "{METRIC NAME}" `
     -Operator GreaterThan  `
     -Threshold {NUMBER}   `
     -WindowSize {HH:MM:SS}  `
     [-SendEmailToServiceOwners] `
     [-CustomEmails "EMAIL1@X.COM","EMAIL2@Y.COM" ] `
     -Location "East US"
     -RuleType Metric



## Пример 1

Я хочу получать электронные сообщения, если в среднем за 5 минут ответ сервера на HTTP-запросы выполняется дольше 1 секунды. Мой ресурс Application Insights называется IceCreamWebApp, и он находится в группе ресурсов Fabrikam. Я владелец подписки Azure.

GUID — это идентификатор подписки (не ключ инструментирования приложения).

    Add-AlertRule -Name "slow responses" `
     -Description "email me if the server responds slowly" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "request.duration" `
     -Operator GreaterThan `
     -Threshold 1 `
     -WindowSize 00:05:00 `
     -SendEmailToServiceOwners `
     -Location "East US" -RuleType Metric

## Пример 2

У меня есть приложение, в котором используется [TrackMetric()](app-insights-api-custom-events-metrics.md#track-metric) для предоставления метрики salesPerHour. Я хочу, чтобы моим коллегам отправлялось электронное сообщение, если в среднем за 24 часа salesPerHour станет меньше 100.

    Add-AlertRule -Name "poor sales" `
     -Description "slow sales alert" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "salesPerHour" `
     -Operator LessThan `
     -Threshold 100 `
     -WindowSize 24:00:00 `
     -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
     -Location "East US" -RuleType Metric

Это же правило можно использовать для метрики, передаваемой с помощью [параметра измерения](app-insights-api-custom-events-metrics.md#properties) другого вызова отслеживания, например TrackEvent или trackPageView.

## Имена метрик

Имя метрики | Имя экрана | Описание
---|---|---
`basicExceptionBrowser.count`|Исключения браузера|Число необработанных исключений в браузере.
`basicExceptionServer.count`|Исключения сервера|Число необработанных исключений приложения
`clientPerformance.clientProcess.value`|Время обработки клиента|Время с момента получения последнего байта документа до загрузки модели DOM. Обработка асинхронных запросов может продолжаться.
`clientPerformance.networkConnection.value`|Время подключения к сети при загрузке страницы| Время, необходимое браузеру для подключения к сети. Может быть 0, если страница в кэше.
`clientPerformance.receiveRequest.value`|Время получения ответа| Время с момента отправки браузером запроса до начала получения ответа.
`clientPerformance.sendRequest.value`|Время отправки запроса| Время, необходимое браузеру для отправки запроса.
`clientPerformance.total.value`|Время загрузки страницы в браузере|Время с момента отправки запроса пользователя до загрузки DOM, таблиц стилей, сценариев и изображений.
`performanceCounter.available_bytes.value`|Объем доступной памяти|Физическая память, доступная для использования процессами или системой.
`performanceCounter.io_data_bytes_per_sec.value`|Скорость обработки операций ввода-вывода|Общее число байтов в секунду в операциях чтения и записи в файлы, сеть и устройства.
`performanceCounter.number_of_exceps_thrown_per_sec`|Частота порождения исключений|Количество исключений, порождаемых в секунду.
`performanceCounter.percentage_processor_time.value`|Обработка ЦП|Процент времени, затраченного всеми потоками процессов, используемых процессором для выполнения инструкций для процесса приложения.
`performanceCounter.percentage_processor_total.value`|Процессорное время|Процент времени, затраченного процессором на непростаивающие потоки.
`performanceCounter.process_private_bytes.value`|Количество байтов исключительного использования процессов|Память, выделенная исключительно для процессов наблюдаемого приложения.
`performanceCounter.request_execution_time.value`|Время выполнения запроса ASP.NET|Время выполнения самого последнего запроса.
`performanceCounter.requests_in_application_queue.value`|Число запросов ASP.NET в очереди выполнения|Длина очереди запросов приложений.
`performanceCounter.requests_per_sec`|Частота запросов ASP.NET|Частота всех запросов из ASP.NET к приложению в секунду.
`remoteDependencyFailed.durationMetric.count`|Ошибки зависимости|Количество неудачных вызовов внешних ресурсов серверным приложением.
`request.duration`|Время ответа от сервера|Время с момента получения HTTP-запроса до завершения отправки ответа.
`request.rate`|Частота запросов|Частота всех запросов к приложению в секунду.
`requestFailed.count`|Failed requests (Неудачные запросы)|Число HTTP-запросов, приведших к отображению кода ответа >= 400. 
`view.count`|Просмотры страниц|Количество клиентских запросов пользователя для веб-страницы. Искусственный трафик отфильтровывается.
{имя пользовательской метрики}|{имя метрики}|Значение метрики, переданное [TrackMetric](app-insights-api-custom-events-metrics.md#track-metric) или в [параметре измерения вызова отслеживания](app-insights-api-custom-events-metrics.md#properties).

Метрики отправляются различными модулями телеметрии:

Группа метрик | Модуль сборщика
---|---
basicExceptionBrowser,<br/>clientPerformance,<br/>view | [Browser JavaScript](app-insights-javascript.md)
performanceCounter | [Производительность](app-insights-configuration-with-applicationinsights-config.md#nuget-package-3)
remoteDependencyFailed| [Dependency](app-insights-configuration-with-applicationinsights-config.md#nuget-package-1)
request,<br/>requestFailed|[Server request](app-insights-configuration-with-applicationinsights-config.md#nuget-package-2)

## Объекты Webhook

Вы можете [автоматизировать свой ответ на оповещение](../azure-portal/insights-webhooks-alerts.md). При возникновении оповещения Azure будет вызывать выбранный вами веб-адрес.

## См. также


* [Сценарий настройки Application Insights](app-insights-powershell-script-create-resource.md)
* [Создание ресурсов Application Insights и веб-тестов на основе шаблонов](app-insights-powershell.md)
* [Автоматизация связывания системы диагностики Microsoft Azure с Application Insights](app-insights-powershell-azure-diagnostics.md)
* [Автоматизация реагирования на оповещение](../azure-portal/insights-webhooks-alerts.md)


 

<!---HONumber=AcomDC_0224_2016-->