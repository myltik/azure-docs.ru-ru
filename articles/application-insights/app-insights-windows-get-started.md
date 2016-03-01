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
	ms.date="02/19/2016"
	ms.author="awills"/>

# Аналитики для приложений Windows Phone и приложений Магазина

Корпорация Майкрософт предоставляет два решения DevOps для мобильных устройств. Это [HockeyApp](http://hockeyapp.net/) для анализа на клиенте, а также [Application Insights](app-insights-overview.md) для анализа на сервере.

[HockeyApp](http://hockeyapp.net/) — это наше решение DevOps для приложений, используемых на мобильных устройствах под управлением iOS, OS X, Android или Windows, а также для межплатформенных приложений на платформах Xamarin, Cordova и Unity. Это решение позволяет передавать сборки тест-инженерам, собирать данные о сбоях, а также получать метрики и отзывы пользователей. Решение интегрируется с Visual Studio Team Services, поддерживая простые развертывания и интеграцию с рабочими элементами.

Справочные ресурсы:

* [HockeyApp](http://support.hockeyapp.net/kb)
* [Блог о решении HockeyApp Blog](http://hockeyapp.net/blog/)
* Используйте [предварительную версию Hockeyapp](http://hockeyapp.net/preseason/), чтобы получить первые выпуски.

Если у приложения есть серверная часть, используйте [Application Insights](app-insights-overview.md) для мониторинга веб-сервера приложения на платформе [ASP.NET](app-insights-asp-net.md) или [J2EE](app-insights-java-get-started.md).

## Пакет SDK Application Insights для устройств под управлением Windows

Хотя мы рекомендуем использовать HockeyApp, есть ранняя версия пакета SDK Application Insights, которую можно применять для наблюдения за [сбоями][windowsCrash] и [использованием][windowsUsage] в приложениях для устройств Windows.

Обратите внимание, что поддержка SDK для старых устройств будет прекращена.

![](./media/app-insights-windows-get-started/appinsights-d018-oview.png)


Для установки ранней версии пакета SDK вам понадобятся следующие компоненты:

* подписка на [Microsoft Azure][azure].
* Visual Studio 2013 или более поздняя версия.


### 1\. Получение ресурса Application Insights 

На [портале Azure][portal] создайте ресурс Application Insights.

Создание нового ресурса

![Последовательно выберите пункты "Создать", "Службы для разработчиков", "Application Insights"](./media/app-insights-windows-get-started/01-new.png)

[Ресурс][roles] в Azure — это экземпляр службы. В этом ресурсе будет анализироваться и представляться телеметрия из вашего приложения.

#### Копирование ключа инструментирования

Этот ключ идентифицирует ресурс. Он скоро потребуется вам, чтобы настроить пакет SDK для отправки данных в ресурс.

![Откройте раскрывающуюся панель Essentials и выберите ключ инструментирования](./media/app-insights-windows-get-started/02-props.png)


### 2\. Добавление пакета SDK Application Insights в приложение

В Visual Studio добавьте соответствующий пакет SDK в свой проект.


* Если проект — приложение C++, используйте [пакет SDK для C++](https://github.com/Microsoft/ApplicationInsights-CPP) вместо пакета для NuGet на рисунке ниже.

Если это универсальное приложение для Windows, повторите эти шаги для проекта Windows Phone и для проекта Windows.

1. Щелкните правой кнопкой мыши проект в обозревателе решений и выберите пункт **Управление пакетами NuGet**.

    ![](./media/app-insights-windows-get-started/03-nuget.png)

2. Найдите Application Insights.

    ![](./media/app-insights-windows-get-started/04-ai-nuget.png)

3. Выберите **Application Insights для приложений Windows**.

4. Добавьте файл ApplicationInsights.config в корневой каталог проекта и вставьте скопированный c портала ключ инструментирования. Ниже приведен пример XML-кода для этого файла конфигурации.

	```xml

		<?xml version="1.0" encoding="utf-8" ?>
		<ApplicationInsights>
			<InstrumentationKey>YOUR COPIED INSTRUMENTATION KEY</InstrumentationKey>
		</ApplicationInsights>
	```

    Задайте для файла ApplicationInsights.config такие свойства: **Действие при построении** == **Содержимое**, **Копировать в выходной каталог** == **Копировать всегда**.
	
	![](./media/app-insights-windows-get-started/AIConfigFileSettings.png)

5. Добавьте приведенный ниже код инициализации. Мы рекомендуем добавить этот код в конструктор `App()`. Если выполнить это действие в другом месте, можно пропустить автоматический сбор сведений о первых просмотрах страниц.

```C#

	public App()
	{
	   // Add this initilization line. 
	   WindowsAppInitializer.InitializeAsync();
	
	   this.InitializeComponent();
	   this.Suspending += OnSuspending;
	}  
```

**Универсальные приложения Windows**. Повторите эти действия для проектов Windows Phone и Магазина Windows. [Пример универсального приложения для Windows 8.1](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/Windows%208.1%20Universal).

### <a name="network"></a>3. Включение доступа к сети для вашего приложения

Если приложение еще не [запросило исходящий доступ к сети](https://msdn.microsoft.com/library/windows/apps/hh452752.aspx), вам нужно добавить это в его манифест как [обязательную возможность](https://msdn.microsoft.com/library/windows/apps/br211477.aspx).

### <a name="run"></a>4. Запуск проекта

[Запустите приложение с помощью клавиши F5](http://msdn.microsoft.com/library/windows/apps/bg161304.aspx) и используйте его, чтобы создать некоторый объем телеметрии.

В Visual Studio вы увидите число полученных событий.

![](./media/app-insights-windows-get-started/appinsights-09eventcount.png)

В режиме отладки телеметрия отправляется сразу же после ее создания. В режиме выпуска телеметрия хранится на устройстве и отправляется только тогда, когда приложение возобновляет работу.


### <a name="monitor"></a>5. Просмотр данных мониторинга

Откройте Application Insights из проекта.

![Щелкните проект правой кнопкой мыши и откройте портал Azure](./media/app-insights-windows-get-started/appinsights-04-openPortal.png)


Сначала вы увидите только одну или две точки. Например:

![Щелкните плитки, чтобы увидеть больше данных](./media/app-insights-windows-get-started/appinsights-26-devices-01.png)

Нажмите кнопку «Обновить» через несколько секунд, если ожидаете дополнительные данные.

Щелкните любую диаграмму, чтобы просмотреть более подробные сведения.


### <a name="deploy"></a>5. Публикация приложения в Магазине

[Опубликуйте свое приложение](http://dev.windows.com/publish) и наблюдайте за данными, которые собираются по мере загрузки и использования этого приложения пользователями.

### Настройка телеметрии

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

Дополнительные сведения см. в статье [Пользовательские события и метрики][api].

## Что дальше?

* [Обнаружение и диагностика сбоев в приложении][windowsCrash]
* [Дополнительная информация о метриках][metrics]
* [Дополнительная информация о поиске по журналу диагностики][diagnostic]
* [Отслеживание использования приложения][windowsUsage]
* [Использование API для отправки пользовательской телеметрии][api]
* [Устранение неполадок][qna]

* [Использование HockeyApp для анализа сбоев, бета-распределения и отзывов о приложении](http://hockeyapp.net/)




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

<!---HONumber=AcomDC_0224_2016-->