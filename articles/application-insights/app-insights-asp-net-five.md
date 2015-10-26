<properties 
	pageTitle="Application Insights для ASP.NET 5" 
	description="Отслеживайте доступность, производительность и использование веб-приложений." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/27/2015" 
	ms.author="awills"/>

# Application Insights для ASP.NET 5

Visual Studio Application Insights позволяет отслеживать доступность, производительность и использование вашего веб-приложения. Благодаря получаемым данным о производительности и эффективности работы приложения на практике вы можете принимать осознанные решения о направлении разработки в каждом жизненном цикле.

![Пример](./media/app-insights-asp-net-five/sample.png)

Вам потребуется подписка [Microsoft Azure](http://azure.com). Войдите, используя учетную запись Майкрософт, которая уже может быть у вас для Windows, XBox Live или других облачных служб Майкрософт.

## Создание проекта ASP.NET 5

Создайте проект ASP.NET 5, если вы еще не сделали этого.

Используйте стандартный шаблон проекта ASP.NET 5 в Visual Studio 2015.


## Создание ресурса Application Insights

На [портале Azure][portal] создайте новый ресурс Application Insights. Выберите вариант ASP.NET.

![Последовательно выберите «Создать», «Службы для разработчиков», «Application Insights»](./media/app-insights-asp-net-five/01-new-asp.png)

В отобразившейся колонке [Ресурсы][roles] будут содержаться данные о производительности и использовании приложения. Чтобы возвратиться к нему после очередного входа в Azure, найдите соответствующую плитку на начальном экране. Или щелкните «Обзор», чтобы найти ее.

От выбранного типа приложения зависит содержимое по умолчанию столбцов ресурсов и свойств, отображаемых в [обозревателе метрик][metrics].

##  Настройка проекта с помощью ключа инструментирования

Скопируйте ключ из ресурса Application Insights:

![Нажмите «Свойства», выберите ключ и нажмите сочетание клавиш CTRL + C](./media/app-insights-asp-net-five/02-props-asp.png)

В проекте ASP.NET 5 и вставьте его в файл `config.json`:

    {
      "ApplicationInsights": {
        "InstrumentationKey": "11111111-2222-3333-4444-555555555555"
      }
    }

Или, если нужно, чтобы настройка была динамической, этот код можно добавить в класс Startup приложения:

    configuration.AddApplicationInsightsSettings(
      instrumentationKey: "11111111-2222-3333-4444-555555555555");


## Добавление Application Insights в ваш проект


#### Создание ссылки на пакет NuGet

Найдите пакет NuGet с [самым последним номером выпуска](https://github.com/Microsoft/ApplicationInsights-aspnet5/releases).

Откройте файл `project.json` и измените раздел `dependencies`:

    {
      "dependencies": {
        // Replace 0.* with a specific version:
        "Microsoft.ApplicationInsights.AspNet": "0.*",

       // Add these if they aren't already there:
       "Microsoft.Framework.ConfigurationModel.Interfaces": "1.0.0-beta4",
       "Microsoft.Framework.ConfigurationModel.Json":  "1.0.0-beta4"
      }
    }

#### Выполнение синтаксического анализа файла

В `startup.cs`:

    using Microsoft.ApplicationInsights.AspNet;

    public IConfiguration Configuration { get; set; }

В методе `Startup`:

    public Startup(IHostingEnvironment env, IApplicationEnvironment appEnv)
    {
    	// Setup configuration sources.
    	var builder = new ConfigurationBuilder(appEnv.ApplicationBasePath)
	   		.AddJsonFile("config.json")
	   		.AddJsonFile($"config.{env.EnvironmentName}.json", optional: true);
    	builder.AddEnvironmentVariables();

    	if (env.IsEnvironment("Development"))
    	{
	    	builder.AddApplicationInsightsSettings(developerMode: true);
    	}
    
    	Configuration = builder.build();
    }

В методе `ConfigurationServices`:

    services.AddApplicationInsightsTelemetry(Configuration);

В методе `Configure`:

    // Add Application Insights monitoring to the request pipeline as a very first middleware.
    app.UseApplicationInsightsRequestTelemetry();

    // Any other error handling middleware goes here.

    // Add Application Insights exceptions handling to the request pipeline.
    app.UseApplicationInsightsExceptionTelemetry();

## Добавление инструментирования клиента JavaScript

Если у вас есть файл \_Layout.cshtml, вставьте в него следующий код. В противном случае добавьте этот код в страницу, которую необходимо отслеживать.

Определите внедрение кода в самой верхней части файла:

    @inject Microsoft.ApplicationInsights.Extensibility.TelemetryConfiguration TelemetryConfiguration

Вставьте вспомогательный метод Html между тегом `</head>` и любым другим сценарием. Любые пользовательские телеметрии JavaScript со страницы, о которых нужно сообщить, следует внедрять после этого фрагмента кода:

    <head> 

      @Html.ApplicationInsightsJavaScript(TelemetryConfiguration) 

      <!-- other scripts -->
    </head>

## Запуск приложения для Android

Выполните отладку приложения в Visual Studio или опубликуйте его на веб-сервере.

## Просмотр данных о приложении

Вернитесь на [портал Azure][portal] и перейдите к своему ресурсу Application Insights. Если в колонке «Обзор» нет данных, подождите одну или две минуты и щелкните «Обновить».

* [Отслеживание использования приложения][usage]
* [Диагностика проблем с производительностью][detect]
* [Настройка веб-тестов для отслеживания доступности][availability]



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
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md
[usage]: app-insights-web-track-usage.md

<!---HONumber=Oct15_HO3-->