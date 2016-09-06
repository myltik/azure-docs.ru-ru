<properties
	pageTitle="Аналитика для приложений Windows Phone и приложений Магазина | Microsoft Azure"
	description="Анализ использования и сбоев приложения для устройства Windows."
	services="application-insights"
    documentationCenter="windows"
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/26/2016"
	ms.author="awills"/>

# Аналитики для приложений Windows Phone и приложений Магазина

Корпорация Майкрософт предоставляет два решения DevOps для мобильных устройств. Это [HockeyApp](http://hockeyapp.net/) для анализа на клиенте, а также [Application Insights](app-insights-overview.md) для анализа на сервере.

[HockeyApp](http://hockeyapp.net/) — это наше решение DevOps для приложений, используемых на мобильных устройствах под управлением iOS, OS X, Android или Windows, а также для межплатформенных приложений на платформах Xamarin, Cordova и Unity. Это решение позволяет передавать сборки тест-инженерам, собирать данные о сбоях, а также получать метрики и отзывы пользователей. Решение интегрируется с Visual Studio Team Services, поддерживая простые развертывания и интеграцию с рабочими элементами.

## Начало работы с HockeyApp

Справочные ресурсы:

* [HockeyApp](http://support.hockeyapp.net/kb)
* [HockeyApp для Windows](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone)
* [Блог о решении HockeyApp Blog](http://hockeyapp.net/blog/)
* Используйте [предварительную версию Hockeyapp](http://hockeyapp.net/preseason/), чтобы получать первые выпуски.

Если у приложения есть серверная часть, используйте [Application Insights](app-insights-overview.md) для мониторинга веб-сервера приложения на платформе [ASP.NET](app-insights-asp-net.md) или [J2EE](app-insights-java-get-started.md).


Вы также можете использовать [Application Insights для классических приложений Windows](app-insights-windows-desktop.md).

## Аналитика, экспорт и доступ API к данным HockeyApp 

[Настройте мост HockeyApp](app-insights-hockeyapp-bridge-app.md) в Application Insights. Это позволяет:

* использовать эффективный язык запросов [аналитики](app-insights-analytics.md) для телеметрии;
* [экспортировать данные телеметрии](app-insights-export-telemetry.md) в хранилище BLOB-объектов Azure.

## Дальнейшие действия

* [Начало работы с HockeyApp для Windows](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone)

<!---HONumber=AcomDC_0831_2016-->