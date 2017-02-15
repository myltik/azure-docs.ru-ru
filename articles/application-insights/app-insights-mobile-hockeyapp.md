---
title: "Мониторинг производительности мобильных веб-приложений с помощью аналитических средств для разработчиков | Документация Майкрософт"
description: "Мониторинг производительности и использования приложения для разработчиков мобильных приложений. Использование HockeyApp и Application Insights для классических приложений, приложений веб-служб и внутренних приложений."
author: alancameronwills
services: application-insights
documentationcenter: 
manager: douge
ms.assetid: 93e0f108-9605-4d8b-8fce-512bfe8c3f0f
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 41ce9b0e323c0938b6db98b99d8d687d1ed0f0ef
ms.openlocfilehash: 4d394ff22e57d162c5adf39c492ca76f3d1f9d3a


---
# <a name="mobile-analytics-with-hockeyapp-and-application-insights"></a>Использование мобильной аналитики с HockeyApp и Application Insights
Мониторинг производительности и использования мобильных и классических приложений на этапах бета-тестирования и развертывания с помощью [HockeyApp](https://hockeyapp.net/). Мониторинг вспомогательных приложений веб-служб и внутренних приложений с помощью [Azure Application Insights](app-insights-overview.md). Анализ данных из клиентского и серверного приложений в Аналитике и отображение диаграмм рядом на панели мониторинга Azure.

Набор средств Microsoft Developer Analytics предлагает два решения для мониторинга производительности приложений:

* **HockeyApp для мобильных** и классических приложений:
  * Распространение тестовых версий среди выбранных пользователей.
  * Анализ сбоев.
  * Пользовательские данные телеметрии для анализа сведений об использовании.
* **Application Insights для веб-сайтов**, служб и внутренних приложений:
  * Метрики производительности и использования и соответствующие оповещения.
  * Отчеты об исключениях и диагностическая трассировка.
  * Поиск в диагностических данных с возможностью фильтрации и связанными ссылками на данные телеметрии.

Оба решения обеспечивают следующие возможности.

* Мощный **[язык запросов аналитики](app-insights-analytics.md)** для диагностики и анализа.
* **[Экспорт данных](app-insights-export-telemetry.md)** в собственное хранилище.
* **Встроенная панель мониторинга** с аналитическими диаграммами и таблицами.

## <a name="monitor-your-app-components"></a>Мониторинг компонентов приложения
Следуйте инструкциям на этих страницах, чтобы запрограммировать установку пакета SDK в коде и начать отслеживание своего приложения.

### <a name="web-apps---application-insights"></a>Веб-приложения — Application Insights
* [Веб-приложение ASP.NET](app-insights-asp-net.md) 
* [Устранение неполадок, а также вопросы и ответы по Application Insights для Java;](app-insights-java-get-started.md)
* [Веб-приложение Node.js](https://github.com/Microsoft/ApplicationInsights-node.js)
* [облачных служб Azure](app-insights-cloudservices.md)

### <a name="mobile-apps---hockeyapp"></a>Мобильные приложения — HockeyApp
* [Приложение iOS](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-ios)
* [Приложение Mac OS X](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-mac-os-x)
* [Приложение Android](https://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk)
* [Универсальное приложение Windows](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/how-to-create-an-app-for-uwp)
* [Приложение Windows Phone 8 и 8.1](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-phone-silverlight-apps-80-and-81)
* [Приложение Windows Presentation Foundation](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-wpf-apps)

Для классических приложений для Windows рекомендуется HockeyApp. Но, разумеется, вы можете также [отправлять данные телеметрии из классического приложения для Windows в Application Insights](app-insights-windows-desktop.md). Вы можете это сделать, чтобы поэкспериментировать с Application Insights.

## <a name="analytics-and-export-for-hockeyapp-telemetry"></a>Аналитика и экспорта для телеметрии HockeyApp
Чтобы ознакомиться с пользовательской телеметрией и телеметрией журналов, использующих функции аналитики и непрерывного экспорта Application Insights, [настройте мост](app-insights-hockeyapp-bridge-app.md).




<!--HONumber=Nov16_HO3-->


