<properties 
	pageTitle="Application Insights для ASP.NET 5" 
	description="Отслеживайте доступность, производительность и использование веб-приложений." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/10/2016" 
	ms.author="awills"/>

# Application Insights для ASP.NET 5

Visual Studio Application Insights позволяет отслеживать доступность, производительность и использование вашего веб-приложения. Благодаря получаемым данным о производительности и эффективности работы приложения на практике вы можете принимать осознанные решения о направлении разработки в каждом жизненном цикле.

![Пример](./media/app-insights-asp-net-five/sample.png)

Вам потребуется подписка [Microsoft Azure](http://azure.com). Войдите, используя учетную запись Майкрософт, которая уже может быть у вас для Windows, XBox Live или других облачных служб Майкрософт. Возможно, у вашей группы есть подписка организации Azure: попросите ее владельца добавить вас к ней с помощью вашей учетной записи Майкрософт.

[Демонстрационный пример](https://github.com/aspnet/Docs/tree/master/aspnet/fundamentals/application-insights/sample)

## Приступая к работе

Если вы создали проект в Visual Studio 2015, компонент Application Insights уже должен быть установлен. В противном случае следуйте [руководству по началу работы](https://github.com/Microsoft/ApplicationInsights-aspnet5/wiki/Getting-Started).

## Работа с Application Insights

Войдите на [портал Microsoft Azure](https://portal.azure.com) и найдите созданный ресурс для отслеживания работы приложения.

В отдельном окне браузера некоторое время поработайте с приложением. Вы увидите, что в диаграммах Application Insights появятся данные. (Возможно, потребуется нажать кнопку "Обновить".) Во время разработки объем этих данных будет небольшим, однако эти диаграммы станут по-настоящему полезными после публикации приложения, когда их будет использовать много пользователей.

На странице обзора отображаются диаграммы производительности, которые, как правило, содержат интересные данные: время ответа сервера, время загрузки страницы и количество неудачных запросов. Щелкните любую диаграмму, чтобы просмотреть другие диаграммы и данные.

Представления на портале делятся на две основные категории.

* [Обозреватель метрик](app-insights-metrics-explorer.md) выводит на экран графики, таблицы метрик и счетчики, такие как время ответа, доля сбоев или метрики, которые вы создаете самостоятельно с помощью [API](app-insights-api-custom-events-metrics.md). Вы можете выполнить фильтрацию и сегментацию данных по значениям свойств, чтобы лучше понять, как работает приложение и его пользователи.
* [Обозреватель поиска](app-insights-diagnostic-search.md) перечисляет отдельные события, такие как определенные запросы, исключения, трассировки журналов или события, которые вы создаете самостоятельно с помощью [API](app-insights-api-custom-events-metrics.md). Вы можете выполнить фильтрацию и поиск в событиях и переходить между связанными событиями для выявления проблем.

## Оповещения

* Настройте [тесты доступности](app-insights-monitor-web-app-availability.md) для постоянного тестирования веб-сайта из расположений по всему миру и немедленного получения сообщений электронной почты в случае сбоя проверки.
* Настройте [оповещения о метриках](app-insights-monitor-web-app-availability.md), чтобы получать оповещение о выходе метрик, таких как время отклика или доля исключений, за допустимые границы.


## Дополнительные данные телеметрии

* [Отслеживайте зависимости](app-insights-dependencies.md), чтобы выяснить, что стало причиной медленной работы: REST, SQL или другие внешние ресурсы.
* [Используйте API](app-insights-api-custom-events-metrics.md) для отправки собственных событий и метрик для более четкого представления о производительности и использовании приложения.
* [Тесты доступности](app-insights-monitor-web-app-availability.md) позволяют постоянно проверять работу приложения из всех точек мира. 


## Открытый исходный код

[Чтение кода и дополнительные наработки](https://github.com/Microsoft/ApplicationInsights-aspnet5)


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md
[usage]: app-insights-web-track-usage.md

<!---HONumber=AcomDC_0309_2016-->