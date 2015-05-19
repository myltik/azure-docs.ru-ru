<properties 
	pageTitle="Application Insights: платформы" 
	description="Можно ли использовать Application Insights  с...?" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="awills"/>
 
# Application Insights: платформы

[AZURE.INCLUDE [app-insights-selector-get-started](../includes/app-insights-selector-get-started.md)]

#### Можно ли использовать Application Insights с...?


## Языки

+ [C\#, VB](app-insights-start-monitoring-app-health-usage.md)
+ [Веб-страницы JavaScript](app-insights-web-track-usage.md)
+ [Приложения JavaScript Магазина Windows](#cordova)
+ [Java](app-insights-java.md)
+ [Ruby](https://rubygems.org/gems/application_insights) 
+ [PHP](https://github.com/Microsoft/AppInsights-PHP)
+ [Python](https://pypi.python.org/pypi/applicationinsights/0.1.0)

## Платформы

+ [ASP.NET](app-insights-start-monitoring-app-health-usage.md)
+ [Веб-приложения и виртуальные машины Azure](insights-perf-analytics.md)
+ [Android](https://github.com/Microsoft/AppInsights-Android)
+ [iOS](https://github.com/Microsoft/AppInsights-iOS)
+ [Cordova](#cordova)
+ [Angular](https://www.npmjs.com/package/angular-applicationinsights)
+ [Node.JS](https://www.npmjs.com/package/applicationinsights)
+ [Joomla](https://github.com/fidmor89/AppInsights-Joomla)
+ [SharePoint](app-insights-sharepoint.md)
+ [WordPress](https://wordpress.org/plugins/application-insights/)
+ [Классические приложения Windows](app-insights-windows-desktop.md)


## Платформы ведения журналов

+	[Log4Net, NLog или System.Diagnostics.Trace](app-insights-diagnostic-search.md)
+	[Java, Log4J или Logback](app-insights-java-trace-logs.md)


## Проекты

Посетите также [проект пакета SDK для Application Insights на GitHub](https://github.com/Microsoft/AppInsights-Home)


### <a name="cordova"></a>Приложения JavaScript Магазина Windows и Cordova

В Visual Studio щелкните правой кнопкой мыши проект и выберите **Управление пакетами NuGet**.

Выберите **Интернет** и выполните поиск по Application Insights.

Установите **API Application Insights для приложений JavaScript**.

Используйте стандартный клиентский [сценарий веб-приложения](app-insights-web-track-usage.md), но с одним изменением.

При получении сценария на портале Application Insights вставьте строку после ключа инструментирования:

    ...{
        instrumentationKey:"00000000-662d-4479-0000-40c89770e67c",
        endpointUrl:"https://dc.services.visualstudio.com/v2/track"
    } ...

[Cordova](http://cordova.apache.org/)

[Приложения Магазина Windows, созданные с помощью JavaScript](https://msdn.microsoft.com/library/windows/apps/br211385.aspx)

<!--Link references-->



<!--HONumber=54-->