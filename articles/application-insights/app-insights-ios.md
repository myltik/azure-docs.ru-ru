<properties
    pageTitle="Аналитика для приложений iOS | Microsoft Azure"
    description="Анализ использования и производительности приложения iOS. "
    services="application-insights"
    documentationCenter="ios"
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

# Аналитика для приложений iOS

Корпорация Майкрософт предоставляет два решения DevOps для мобильных устройств. Это [HockeyApp](http://hockeyapp.net/) для клиентских устройств, а также [Application Insights](app-insights-overview.md) для сервера и веб-страниц клиента.

[HockeyApp](http://hockeyapp.net/) — это наше решение DevOps для сборки приложений, используемых на мобильных устройствах под управлением iOS, OS X, Android или Windows, а также для межплатформенных приложений на платформах Xamarin, Cordova и Unity. Это решение позволяет передавать сборки тест-инженерам, собирать данные о сбоях, а также получать отзывы пользователей. Решение интегрируется с Visual Studio Team Services, поддерживая простые развертывания и интеграцию с рабочими элементами.


Справочные ресурсы:

* [HockeyApp](http://support.hockeyapp.net/kb)
* [HockeyApp для iOS](http://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-ios)
* [Блог о решении HockeyApp Blog](http://hockeyapp.net/blog/)
* Используйте [предварительную версию Hockeyapp](http://hockeyapp.net/preseason/), чтобы получить первые выпуски.

Если у приложения есть серверная часть, используйте [Application Insights](app-insights-overview.md) для мониторинга веб-сервера приложения на платформе [ASP.NET](app-insights-asp-net.md) или [J2EE](app-insights-java-get-started.md).


## Аналитика, экспорт и доступ API к данным HockeyApp 

[Настройте мост HockeyApp](app-insights-hockeyapp-bridge-app.md) в Application Insights. Это позволяет:

* использовать эффективный язык запросов [аналитики](app-insights-analytics.md) для телеметрии;
* [экспортировать данные телеметрии](app-insights-export-telemetry.md) в хранилище BLOB-объектов Azure.

## Дальнейшие действия

* [Начало работы с HockeyApp для iOS](http://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-ios)

<!---HONumber=AcomDC_0831_2016-->