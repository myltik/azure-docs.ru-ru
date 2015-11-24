<properties
	pageTitle="Аналитика для приложений Windows Phone и приложений Магазина | Microsoft Azure"
	description="Анализ использования и производительности приложения для устройства Windows."
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
	ms.date="11/11/2015"
	ms.author="awills"/>

# Аналитики для приложений Windows Phone и приложений Магазина



С помощью Visual Studio Application Insights можно отслеживать показатели использования и производительности опубликованного приложения.


> [AZURE.NOTE]Для создания отчетов об аварийном завершении, аналитики, распространения и обратной связи рекомендуем использовать [HockeyApp](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-store-apps-and-windows-phone-store-apps).

![](./media/app-insights-windows-get-started/appinsights-d018-oview.png)


## Настройка Application Insights для проекта устройства Windows

Вам потребуется следующее:

* подписка на [Microsoft Azure][azure];
* Visual Studio 2013 или более поздняя версия.

**Приложения C++ UAP** — см. [руководство по установке приложений C++ для Application Insights](https://github.com/Microsoft/ApplicationInsights-CPP).

### <a name="new"></a> Если создается новый проект приложения Windows

В диалоговом окне **Новый проект** выберите **Application Insights**.

Если отобразится предложение войти, воспользуйтесь данными вашей учетной записи Azure.

![](./media/app-insights-windows-get-started/appinsights-d21-new.png)


### <a name="existing"></a>Или, если это существующий проект

Добавьте Application Insights из обозревателя решений.


![](./media/app-insights-windows-get-started/appinsights-d22-add.png) **Универсальные приложения Windows**. Повторите эти действия для проектов Windows Phone и Магазина. [Пример универсального приложения для Windows 8.1](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/Windows%208.1%20Universal).

## <a name="network"></a>3. Включение доступа к сети для вашего приложения

Если приложение еще не [запросило доступ к Интернету](https://msdn.microsoft.com/library/windows/apps/hh452752.aspx), вам нужно добавить это в его манифест как [обязательную возможность](https://msdn.microsoft.com/library/windows/apps/br211477.aspx).

## <a name="run"></a>4. Запуск проекта

[Запустите приложение с помощью клавиши F5](http://msdn.microsoft.com/library/windows/apps/bg161304.aspx) и используйте его, чтобы создать некоторый объем телеметрии.

В Visual Studio вы увидите число полученных событий.

![](./media/app-insights-windows-get-started/appinsights-09eventcount.png)

В режиме отладки телеметрия отправляется сразу же после ее создания. В режиме выпуска телеметрия хранится на устройстве и отправляется только тогда, когда приложение возобновляет работу.


## <a name="monitor"></a>5. Просмотр данных мониторинга

Войдите на [портал Azure](https://portal.azure.com) и откройте ресурс Application Insights, который вы создали ранее.

Сначала вы увидите только одну или две точки. Например:

![Щелкните плитки, чтобы увидеть больше данных](./media/app-insights-windows-get-started/appinsights-26-devices-01.png)

Если вам требуется больше данных, нажмите кнопку **Обновить** через несколько секунд.

Щелкните любую диаграмму, чтобы просмотреть более подробные сведения.


## <a name="deploy"></a>5. Публикация приложения в Магазине

[Опубликуйте свое приложение](http://dev.windows.com/publish) и наблюдайте за данными, которые собираются по мере загрузки и использования этого приложения пользователями.

## Настройка телеметрии

#### Выбор сборщиков

Пакет SDK Application Insights включает несколько сборщиков, которые автоматически собирают различные типы данных из приложения. По умолчанию все они активны, однако сборщики для инициализации в конструкторе приложений можно выбрать.

    WindowsAppInitializer.InitializeAsync( "00000000-0000-0000-0000-000000000000",
       WindowsCollectors.Metadata
       | WindowsCollectors.PageView
       | WindowsCollectors.Session
       | WindowsCollectors.UnhandledException);

#### Отправка собственных данных телеметрии

Для отправки событий, метрик и данных диагностики в службу Application Insights используйте [API][api]. Ниже приведены эти инструкции.

```C#

 var tc = new TelemetryClient(); // Call once per thread

 // Send a user action or goal:
 tc.TrackEvent("Win Game");

 // Send a metric:
 tc.TrackMetric("Queue Length", q.Length);

 // Provide properties by which you can filter events:
 var properties = new Dictionary{"game", game.Name};

 // Provide metrics associated with an event:
 var measurements = new Dictionary{"score", game.score};

 tc.TrackEvent("Win Game", properties, measurements);

```

Дополнительную информацию см. в статье [Общие сведения об API. Пользовательские события и метрики][api].

## Что дальше?

* [Обнаружение и диагностика сбоев в приложении][windowsCrash]
* [Дополнительная информация о метриках][metrics]
* [Дополнительная информация о поиске по журналу диагностики][diagnostic]




## Обновление до новой версии пакета SDK

После [выпуска новой версии пакета SDK](app-insights-release-notes-windows.md):

* Щелкните правой кнопкой мыши проект и выберите "Управление пакетами NuGet".
* Выберите установленные пакеты Application Insights и щелкните **Обновить**.


## <a name="usage"></a>Дальнейшие действия


[Обнаружение и диагностика сбоев в приложении][windowsCrash]

[Ведение журналов диагностики и поиск по ним][diagnostic]


[Отслеживание использования приложения][windowsUsage]

[Использование API для отправки пользовательской телеметрии][api]

[Устранение неполадок][qna]



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[azure]: ../insights-perf-analytics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[windowsCrash]: app-insights-windows-crashes.md
[windowsUsage]: app-insights-windows-usage.md

<!---HONumber=Nov15_HO4-->