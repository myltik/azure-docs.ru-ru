---
title: 'Application Insights: языки программирования, платформы, интеграция | Документация Майкрософт'
description: Языки программирования, платформы и варианты интеграции для Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 974db106-54ff-4318-9f8b-f7b3a869e536
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2016
ms.author: mbullwin; olegan
ms.openlocfilehash: 8aec8567fd15865887a5fe5ded8b45cad0290bf9
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
---
# <a name="developer-analytics-languages-platforms-and-integrations"></a>Аналитические средства для разработчиков: языки программирования, платформы, интеграция
Ниже перечислены известные нам реализации [Application Insights](app-insights-overview.md) , в том числе реализации от сторонних производителей.

## <a name="languages---officially-supported-by-application-insights-team"></a>Языки программирования, официально поддерживаемые командой Application Insights
* [C#|VB (.NET)](app-insights-asp-net.md)
* [Java](app-insights-java-get-started.md)
* [Веб-страницы JavaScript](app-insights-javascript.md)
* [Node.JS](app-insights-nodejs.md)

## <a name="languages---community-supported"></a>Языки программирования, поддерживаемые сообществом
* [F#](https://safe-stack.github.io/docs/template-azure-ai/)
* [PHP](https://github.com/Microsoft/ApplicationInsights-PHP)
* [Python](https://pypi.python.org/pypi/applicationinsights/0.1.0)
* [Ruby](https://rubygems.org/gems/application_insights)
* [Другие варианты](#projects)

## <a name="platforms-and-frameworks"></a>Платформы и среды
* [ASP.NET](app-insights-asp-net.md)
* [ASP.NET — для приложений, которые уже доступны в Интернете](app-insights-monitor-performance-live-website-now.md)
* [ASP.NET Core](app-insights-asp-net-core.md)
* [Android](app-insights-mobile-center-quickstart.md) (Центр приложений)
* [Android](https://github.com/Microsoft/ApplicationInsights-Android) (Центр приложений)
* [Angular](https://github.com/MarkPieszak/angular-application-insights)
* [Веб-приложения Azure](app-insights-azure-web-apps.md)
* [Облачные службы Azure](app-insights-cloudservices.md) — рабочие роли и веб-роли
* [Функции Azure](https://github.com/christopheranderson/azure-functions-app-insights-sample)
* [Docker](app-insights-docker.md)
* [Glimpse](https://azure.microsoft.com/blog/glimpse-application-insights/)
* [iOS](app-insights-mobile-center-quickstart.md) (Центр приложений)
* [Ionic](https://github.com/SoftwarePioniere/ionic-application-insights)
* [iOS](https://github.com/Microsoft/ApplicationInsights-iOS) (Центр приложений)
* [J2EE](app-insights-java-get-started.md)
* [J2EE — для приложений, которые уже доступны в Интернете](app-insights-java-live.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [OSX](https://github.com/Microsoft/ApplicationInsights-OSX)
* [SAFE Stack](https://safe-stack.github.io/docs/template-azure-ai/)
* [Spring](http://joe.blog.freemansoft.com/2015/12/enabling-microsoft-application-insight.html)
* [Универсальное приложение для Windows](app-insights-mobile-center-quickstart.md) (Центр приложений)
* [WCF](https://github.com/Microsoft/ApplicationInsights-SDK-Labs/blob/master/WCF/readme.md)
* [Классические приложения, службы и рабочие роли Windows](app-insights-windows-desktop.md)
* [Другие варианты](#projects)

## <a name="logging-frameworks"></a>Платформы ведения журналов
* [Log4Net, NLog или System.Diagnostics.Trace](app-insights-diagnostic-search.md)
* [Java, Log4J или Logback](app-insights-java-trace-logs.md)
* [Semantic Logging (SLAB)](https://github.com/fidmor89/SLAB_AppInsights) — интеграция с [Semantic Logging Application Block](https://msdn.microsoft.com/library/dn440729.aspx)
* [Облачное нагрузочное тестирование](http://blogs.msdn.com/b/visualstudioalm/archive/2015/07/30/getting-application-insights-counters-with-cloud-based-load-testing.aspx)
* [Подключаемый модуль LogStash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Служба Log Analytics](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)
* [Logary](https://www.nuget.org/packages/Logary.Targets.AppInsights/)
* [Logrus](https://github.com/jjcollinge/logrus-appinsights)

## <a name="content-management-systems"></a>Системы управления содержимым
* [Concrete](https://github.com/fidmor89/appInsights-Concrete)
* [Drupal](https://github.com/fidmor89/AppInsights-Drupal)
* [Joomla](https://github.com/fidmor89/AppInsights-Joomla)
* [Orchard](https://azure.microsoft.com/blog/integrating-application-insights-into-a-modular-cms-and-a-multi-tenant-public-saas/preview/)
* [SharePoint](app-insights-sharepoint.md)
* [WordPress](https://wordpress.org/plugins/application-insights/)

## <a name="export-and-data-analysis"></a>Экспорт и анализ данных
* [Alooma](https://www.alooma.com/blog/application-insights-amazon-redshift)
* [Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Анализ потока](app-insights-export-power-bi.md)

## <a name="projects"></a> Создание собственного пакета SDK
Если для языка программирования или платформы, которые вы используете, не существует пакета SDK, возможно, вы захотите создать его. Просмотрите код существующих пакетов SDK, перечисленных в описании [проекта пакета SDK для Application Insights на GitHub](https://github.com/Microsoft/AppInsights-Home).
