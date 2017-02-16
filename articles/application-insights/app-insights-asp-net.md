---
title: "Настройка аналитики веб-приложения для ASP.NET с помощью Application Insights | Документация Майкрософт"
description: "Настройка средств аналитики производительности, доступности и использования для веб-сайта ASP.NET, размещенного локально или в Azure."
services: application-insights
documentationcenter: .net
author: NumberByColors
manager: douge
ms.assetid: d0eee3c0-b328-448f-8123-f478052751db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/13/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: dc95c922b71d18cf791ea98f4ab1a02d2bac2c3b
ms.openlocfilehash: 5103c28047e6d5e7be5f4f3b7933196de7045eeb


---
# <a name="set-up-application-insights-for-aspnet"></a>Настройка Application Insights для ASP.NET
[Azure Application Insights](app-insights-overview.md) выполняет мониторинг работы действующего приложения, благодаря чему вы можете [обнаруживать и диагностировать проблемы, связанные с производительностью и исключениями](app-insights-detect-triage-diagnose.md), а также [просматривать показатели использования приложения](app-insights-overview-usage.md).  Эта услуга работает для приложений, размещенных на локальных серверах IIS или на облачных виртуальных машинах, а также для веб-приложений Azure.

## <a name="before-you-start"></a>Перед началом работы
Вам необходимы:

* Visual Studio 2013 с обновлением 3 или более новая версия. Чем новее версия, тем лучше.
* подписка на [Microsoft Azure](http://azure.com). Если у вашей группы или организации есть подписка Azure, владелец может добавить вас в нее с помощью вашей [учетной записи Майкрософт](http://live.com). 

Существуют и другие статьи, к которым можно обратиться, если вас интересуют такие темы, как:

* [инструментирование веб-приложения во время выполнения;](app-insights-monitor-performance-live-website-now.md)
* [облачные службы Azure.](app-insights-cloudservices.md)

## <a name="a-nameidea-1-add-application-insights-sdk"></a><a name="ide"></a> 1. Добавление пакета SDK для Application Insights
### <a name="if-its-a-new-project"></a>Добавление в новый проект
При создании нового проекта в Visual Studio установите флажок для компонента Application Insights. 

![Создание проекта ASP.NET](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)

### <a name="-or-if-its-an-existing-project"></a>... или, если это существующий проект
В обозревателе решений щелкните проект правой кнопкой мыши и выберите пункт **Добавить телеметрию Application Insights** или **Настроить Application Insights**.

![Выберите пункт Add Application Insights](./media/app-insights-asp-net/appinsights-03-addExisting.png)

* Если же это проект ASP.NET Core, [следуйте этим инструкциям, чтобы исправить несколько строк кода](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started#add-application-insights-instrumentation-code-to-startupcs). 

## <a name="a-nameruna-2-run-your-app"></a><a name="run"></a> 2. Запуск приложения для Android
Запустите приложение, нажав клавишу F5, и попробуйте открывать разные страницы, чтобы создать некоторый объем данных телеметрии.

В Visual Studio вы увидите число записанных в журнал событий. 

![В Visual Studio кнопка Application Insights доступна во время отладки.](./media/app-insights-asp-net/54.png)

## <a name="3-see-your-telemetry"></a>3. Просмотр данных телеметрии
### <a name="-in-visual-studio"></a>В Visual Studio
Откройте окно Application Insights в Visual Studio: нажмите кнопку Application Insights или щелкните проект в обозревателе решений правой кнопкой мыши:

![В Visual Studio кнопка Application Insights доступна во время отладки.](./media/app-insights-asp-net/55.png)

В этом представлении (данные, полученные в ходе сеанса отладки) отображаются данные телеметрии, созданные в серверной части приложения. Поэкспериментируйте с фильтрами и щелкните любое событие, чтобы просмотреть подробные сведения.

* *Данные отсутствуют? Проверьте диапазон времени и щелкните значок поиска.*

[Дополнительные сведения о средствах Application Insights в Visual Studio](app-insights-visual-studio.md).

<a name="monitor"></a> 

### <a name="-in-the-portal"></a>На портале
Если не выбран вариант *Установить только пакет SDK* , вы можете также просматривать данные телеметрии на веб-портале Application Insights. 

На портале представлено больше дополнительных диаграмм, средств анализа и панелей мониторинга, чем в Visual Studio. 

Откройте ресурс Application Insights: войдите на [портал Azure](https://portal.azure.com/) и найдите его там или щелкните правой кнопкой мыши проект в Visual Studio и перейдите к нему.

![Щелкните проект правой кнопкой мыши и откройте портал Azure](./media/app-insights-asp-net/appinsights-04-openPortal.png)

* *Ошибка доступа? Если у вас есть несколько наборов учетных данных Майкрософт, возможно, вы использовали для входа неправильный набор. На портале повторите процедуру выхода и входа.*

Откроется представление с данными телеметрии из приложения: ![Обзорная страница Application Insights](./media/app-insights-asp-net/66.png)

Щелкните любую диаграмму, чтобы просмотреть подробные сведения.

### <a name="more-detail-in-the-portal"></a>Подробные сведения на портале

* [**Live Metrics Stream**](app-insights-metrics-explorer.md#live-metrics-stream) отображает данные телеметрии практически мгновенно.

    ![В колонке "Обзор" щелкните "Live Stream"](./media/app-insights-asp-net/livestream.png)

    Откройте Live Stream, когда ваше приложение выполняется, чтобы обеспечить подключение.

    Live Stream отображает данные телеметрии только в течение минуты после их отправки. Дополнительные исторические данные можно просмотреть с помощью поиска, обозревателя метрик и аналитики. Чтобы эти компоненты отобразили данные, может потребоваться несколько минут.

* [**Поиск**](app-insights-diagnostic-search.md) отображает отдельные события, например запросы, исключения и просмотры страниц. Можно использовать фильтрацию по типу события, совпадению терминов и значениям свойств. Щелкните любое событие, чтобы просмотреть его свойства и связанные события. 

    ![В колонке "Обзор" щелкните "Поиск"](./media/app-insights-asp-net/search.png)

 * В режиме разработки может отобразиться много событий зависимостей (AJAX). Они представляют синхронизации, выполняемые для браузера и эмулятора сервера. Чтобы скрыть их, щелкните фильтр "Зависимости".
* [**Сводные метрики**](app-insights-metrics-explorer.md), например показатель частоты запросов и сбоев, отобразятся на диаграммах. Щелкните любую диаграмму, чтобы открыть колонку с более подробной информацией. Щелкните **Изменить** на любой диаграмме, чтобы задать фильтры, размер и т. д.
    
    ![В колонке "Обзор" щелкните любую диаграмму](./media/app-insights-asp-net/metrics.png)

[Дополнительные сведения об использовании Application Insights на портале Azure](app-insights-dashboards.md).

## <a name="4-publish-your-app"></a>4. Публикация приложения
Опубликуйте приложение на сервере IIS или в Azure. Просмотрите [динамический поток метрик](app-insights-metrics-explorer.md#live-metrics-stream) , чтобы убедиться в бесперебойной работе приложения.

Вы увидите, как создается телеметрия на портале Application Insights, где можно отслеживать метрики, выполнять поиск данных телеметрии и настраивать [панели мониторинга](app-insights-dashboards.md). Можно также использовать эффективный [язык запросов аналитики](app-insights-analytics.md) для анализа использования и производительности или поиска определенных событий. 

Кроме того, можно продолжить анализировать телеметрию в [Visual Studio](app-insights-visual-studio.md) с помощью таких средств, как поиск по журналу диагностики и [тренды](app-insights-visual-studio-trends.md).

> [!NOTE]
> Если приложение отправляет достаточно данных телеметрии для достижения [пределов регулирования](app-insights-pricing.md#limits-summary), включается автоматическая [выборка](app-insights-sampling.md). Выборка позволяет уменьшить количество данных телеметрии, отправляемых из приложения, сохраняя связанные данные в целях диагностики.
> 
> 

## <a name="a-namelanda-what-did-add-application-insights-do"></a><a name="land"></a> Что делает команда "Добавить Application Insights"
Application Insights отправляет данные телеметрии из приложения на портал Application Insights (размещенный в Microsoft Azure):

![](./media/app-insights-asp-net/01-scheme.png)

Эта команда выполняет три действия:

1. Добавляет в проект пакет NuGet с веб-пакетом SDK для Application Insights. Чтобы увидеть его в Visual Studio, щелкните правой кнопкой мыши проект и выберите пункт "Управление пакетами NuGet".
2. Создает ресурс Application Insights на [портале Azure](https://portal.azure.com/). Здесь будут отображаться ваши данные. Команда извлекает *ключ инструментирования* , идентифицирующий ресурс.
3. Вставляет ключ инструментирования в `ApplicationInsights.config`, чтобы пакет SDK мог отправлять данные телеметрии на портал.

При необходимости вы можете выполнить эти действия вручную для проекта [ASP.NET 4](app-insights-windows-services.md) или [ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started).

### <a name="to-upgrade-to-future-sdk-versions"></a>Обновление до будущих версий пакета SDK
Чтобы установить [новый выпуск пакета SDK](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases), еще раз откройте диспетчер пакетов NuGet и выполните фильтрацию по установленным пакетам. Выберите элемент Microsoft.ApplicationInsights.Web, а затем — элемент "Обновление".

Если были выполнены какие-либо настройки файла ApplicationInsights.config, то, прежде чем выполнять обновление, сохраните его копию, а затем объедините изменения в новой версии.

## <a name="add-more-telemetry"></a>Добавление данных телеметрии
### <a name="web-pages-and-single-page-apps"></a>Веб-страницы и одностраничные приложения
1. [Добавьте фрагмент JavaScript](app-insights-javascript.md) на веб-страницы, чтобы в колонках обзора и использования отображались данные о просмотре страниц, времени загрузки, исключениях браузера, производительности вызовов AJAX, количестве пользователей и сеансов.
2. [Добавьте в приложение код для пользовательских событий](app-insights-api-custom-events-metrics.md), таких как события количества, времени или измерения, касающиеся действий пользователя.

### <a name="dependencies-exceptions-and-performance-counters"></a>Зависимости, исключения и счетчики производительности
[Установите монитор состояний](app-insights-monitor-performance-live-website-now.md) на всех серверных компьютерах, чтобы получать дополнительные телеметрические данные о приложении. Что вы получаете:

* [Счетчики производительности](app-insights-performance-counters.md) - 
   — счетчики использования ЦП, памяти, диска и другие счетчики производительности, относящиеся к приложению. 
* [Исключения](app-insights-asp-net-exceptions.md) — более подробные данные телеметрии для некоторых исключений.
* [Зависимости](app-insights-asp-net-dependencies.md) — вызовы служб REST API или SQL. Узнайте, вызывают ли медленные ответы внешних компонентов проблемы производительности в приложении. (Если приложение использует .NET 4.6, для получения телеметрии не требуется устанавливать монитор состояния.)

### <a name="diagnostic-code"></a>Код для диагностики
Возникла проблема? Если вы хотите вставить код для диагностики приложения, можно выбрать один из нескольких вариантов:

* [Журнал трассировки.](app-insights-asp-net-trace-logs.md) Если вы уже используете Log4N, NLog или System.Diagnostics.Trace для записи событий трассировки, можно настроить отправку выходных данных в Application Insights, чтобы вы могли сопоставлять эти данные с запросами, просматривать и анализировать их. 
* [Пользовательские события и метрики.](app-insights-api-custom-events-metrics.md) Используйте элемент TrackEvent() и TrackMetric() в коде сервера или веб-страницы.
* [Дополнительные свойства для данных телеметрии.](app-insights-api-filtering-sampling.md#add-properties)

Используйте [функцию поиска](app-insights-diagnostic-search.md) для поиска и сопоставления определенных событий и инструмент [аналитики](app-insights-analytics.md) для выполнения более эффективных запросов.

## <a name="alerts"></a>Оповещения
Узнавайте о проблемах в приложении первыми, а не ждите, пока вам сообщат об этом пользователи. 

* [Создавайте веб-тесты](app-insights-monitor-web-app-availability.md), позволяющие проверить, доступен ли ваш сайт в Интернете.
* [Упреждающая диагностика](app-insights-proactive-diagnostics.md) выполняется автоматически (если приложение имеет определенный минимальный объем трафика). Вам не нужно ее настраивать. Благодаря ей вы узнаете о необычном количестве неудачных запросов.
* [Настройте оповещения о метриках](app-insights-alerts.md), чтобы получать уведомления в случае, если метрика превысила пороговое значение. Их можно настроить для пользовательских метрик, добавляемых в код приложения.

По умолчанию уведомления об оповещениях отправляются владельцу подписки Azure. 

![Пример оповещения по электронной почте](./media/app-insights-asp-net/alert-email.png)

## <a name="version-and-release-tracking"></a>Отслеживание версии и выпуска
### <a name="track-application-version"></a>Отслеживание версии приложения
Убедитесь, что в процессе сборки MSBuild создается `buildinfo.config` . Добавьте в CSPROJ-файл:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup> 
```

При наличии данных сборки веб-модуль Application Insights автоматически добавляет **версию приложения** как свойство для каждого элемента телеметрии. Это позволяет применить фильтр по версии при [диагностическом поиске](app-insights-diagnostic-search.md) или [изучении метрик](app-insights-metrics-explorer.md). 

Обратите внимание, что номер версии сборки создается только MS Build, а не в процессе сборки в Visual Studio.

### <a name="release-annotations"></a>Примечания к выпуску
Если используется Visual Studio Team Services, можно настроить [добавление маркера заметки](app-insights-annotations.md) к диаграммам при выпуске новой версии.

![Пример заметки о новом выпуске](./media/app-insights-asp-net/release-annotation.png)

## <a name="next-steps"></a>Дальнейшие действия
|  |
| --- | --- |
| **[Работа с Application Insights в Visual Studio](app-insights-visual-studio.md)**<br/>Отладка с помощью телеметрии, поиск по журналу диагностики, детализация кода. |
| **[Работа с порталом Application Insights](app-insights-dashboards.md)**<br/>Панели мониторинга, эффективные средства диагностики и анализа, оповещения, карта динамических зависимостей приложения, а также экспорт данных телеметрии. |
| **[Добавление данных](app-insights-asp-net-more.md)**<br/>Мониторинг использования, доступности, зависимостей и исключений. Интеграция трассировок из платформ ведения журналов. Написание пользовательской телеметрии. |




<!--HONumber=Dec16_HO3-->


