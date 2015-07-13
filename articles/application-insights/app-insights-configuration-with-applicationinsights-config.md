<properties 
	pageTitle="Настройка пакета SDK для Application Insights с использованием файла ApplicationInsights.config или ApplicationInsights.xml" 
	description="Включение или отключение модулей сбора данных и добавление счетчиков производительности, а также других параметров" 
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
	ms.date="06/04/2015" 
	ms.author="awills"/>

# Настройка пакета SDK для Application Insights с использованием файла ApplicationInsights.config или ApplicationInsights.xml

Пакет SDK для Application Insights состоит из нескольких модулей. Основной модуль предоставляет основной API, который отправляет данные телеметрии на портал Application Insights. Дополнительные модули собирают данные приложения и его контекста. Настроив файл конфигурации, вы можете включить или отключить модули и задать параметры для некоторых из них.

Имя файла конфигурации – `ApplicationInsights.config` или `ApplicationInsights.xml` в зависимости от типа приложения. Файл автоматически добавляется в проект во время [установки пакета SDK][start]. Кроме того, [монитор состояний на сервере IIS][redfield] добавляет этот файл в веб-приложение. Он также добавляется при [выборе расширения Appplication Insights для веб-сайта или виртуальной машины Azure][azure].

Нет эквивалентного файла для управления [пакетом SDK на веб-странице][client].


## Модули телеметрии

В файле конфигурации есть узел для каждого модуля. Чтобы удалить модуль, удалите узел или закомментируйте его.

#### Implementation.Tracing.DiagnosticsTelemetryModule

Этот модуль сообщает об ошибках в пакете SDK. Например, если в пакете SDK нет доступа к счетчикам производительности или пользовательский TelemetryInitializer вызывает исключение.

Данные отображаются в колонке [Поиск по журналу диагностики][diagnostic].

#### RuntimeTelemetry.RemoteDependencyModule

Этот модуль собирает данные о скорости реагирования внешних компонентов, которые использует ваше приложение. Чтобы этот модуль работал на сервере IIS, [установите монитор состояний][redfield]. Чтобы использовать его в веб-приложениях или виртуальных машинах Azure, [выберите расширение Application Insights][azure].

#### Web.WebApplicationLifecycleModule

Этот модуль выполняет попытки очистить все буферы в памяти данных телеметрии, чтобы при завершении процесса данные не были потеряны.

#### Web.RequestTracking.TelemetryModules.WebRequestTrackingTelemetryModule

Этот модуль подсчитывает количество запросов, поступающих в веб-приложение, и измеряет время ответа.

#### Web.RequestTracking.TelemetryModules.WebExceptionTrackingTelemetryModule

Этот модуль подсчитывает количество необработанных исключений в веб-приложении. См. статью [Ошибки и исключения][exceptions].



#### Web.TelemetryModules.DeveloperModeWithDebuggerAttachedTelemetryModule



## Модуль сборщика данных производительности

#### PerfCollector.PerformanceCollectorModule

По умолчанию этот модуль используется для сбора данных различных счетчиков производительности Windows. Эти счетчики можно увидеть, открыв колонку «Фильтры» в обозревателе метрик.

Вы также можете выполнять мониторинг дополнительных счетчиков производительности – стандартных счетчиков Windows или любых других добавленных счетчиков.
      
Для сбора данных дополнительных счетчиков производительности используйте следующий синтаксис:
      
      <Counters>
        <Add PerformanceCounter="\MyCategory\MyCounter" />
        <Add PerformanceCounter="\Process(??APP_WIN32_PROC??)\Handle Count" ReportAs="Process handle count" />
        ...
      </Counters>
      
для параметра `PerformanceCounter` необходимо задать значение `\CategoryName(InstanceName)\CounterName` или `\CategoryName\CounterName`.
      
Если указать атрибут `ReportAs`, он будет использоваться как имя, отображаемое в Application Insights.

При создании отчетов в Application Insights имена счетчиков должны включать в себя только буквы, круглые скобки, косые черты, дефисы, символы подчеркивания, пробелы и точки.

Атрибут ReportAs необходимо использовать, если нужно выполнить мониторинг счетчика, который содержит недопустимые символы, например «#» или цифры.
      
В качестве `InstanceName` поддерживаются следующие заполнители:

    ?APP_WIN32_PROC?? - instance name of the application process  for Win32 counters.
    ??APP_W3SVC_PROC?? - instance name of the application IIS worker process for IIS/ASP.NET counters.
    ??APP_CLR_PROC?? - instance name of the application CLR process for .NET counters.

## Параметры канала (Java)

Эти параметры влияют на то, как в пакетах SDK для Java хранятся и удаляются собранные ими данные телеметрии.

#### MaxTelemetryBufferCapacity

Этот параметр определяет количество элементов телеметрии, которые могут храниться в хранилище в памяти пакета SDK. По достижении этого количества происходит очистка буфера телеметрии, то есть элементы телеметрии отправляются на сервер Application Insights.

-	Мин. значение: 1.
-	Макс. значение: 1000.
-	Значение по умолчанию: 500.

    <ApplicationInsights> ... <Channel> <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity> </Channel> ... </ApplicationInsights>

#### FlushIntervalInSeconds 

Этот параметр определяет, как часто следует очищать (отправлять в Application Insights) данные, хранящиеся в хранилище в памяти.

-	Мин. значение: 1.
-	Макс. значение: 300.
-	Значение по умолчанию: 5.

    <ApplicationInsights> ... <Channel> <FlushIntervalInSeconds>100</FlushIntervalInSeconds> </Channel> ... </ApplicationInsights>

#### MaxTransmissionStorageCapacityInMB

Этот параметр определяет максимальный размер пространства (в МБ), выделенного для постоянного хранилища на локальном диске. Это хранилище используется для хранения элементов телеметрии, которые не удалось передать в конечную точку Application Insights. Если хранилище будет заполнено, новые элементы телеметрии будут отклонены.

-	Мин. значение: 1.
-	Макс. значение: 100.
-	Значение по умолчанию: 10.

    <ApplicationInsights> ... <Channel> <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB> </Channel> ... </ApplicationInsights>


## Инициализаторы контекста

Эти компоненты собирают данные платформы. Собранные данные сохраняются в [объект TelemetryContext][api].

#### BuildInfoConfigComponentVersionContextInitializer

#### DeviceContextInitializer

#### MachineNameContextInitializer

#### ComponentContextInitializer

#### Web.AzureRoleEnvironmentContextInitializer

#### Web.DomainNameRoleInstanceContextInitializer

#### Web.BuildInfoConfigComponentVersionContextInitializer

#### Web.DeviceContextInitializer



## Инициализаторы телеметрии

Эти компоненты добавляют данные для каждого события телеметрии, отправленного в Application Insights.


#### Web.TelemetryInitializers.WebSyntheticTelemetryInitializer

Этот компонент определяет HTTP-запросы, полученные от программ-роботов, например от поисковых систем и веб-тестов. Он задает TelemetryClient.Context.Operation.SyntheticSource.

#### Web.TelemetryInitializers.WebOperationNameTelemetryInitializer

Этот инициализатор добавляет имя операции для каждого элемента телеметрии. Для веб-приложений «операция» означает HTTP-запрос. Он также задает значение параметра TelemetryClient.Context.Operation.Name.

#### Web.TelemetryInitializers.WebOperationIdTelemetryInitializer

Этот компонент включает функцию «поиск события в одном запросе» в разделе [Поиск по журналу диагностики][diagnostic]. Он также задает TelemetryClient.Context.Operation.Id

и добавляет идентификатор операции в каждый элемент данных, отправленных в Application Insights. Для веб-приложений «операция» означает HTTP-запрос. Таким образом, к примеру, у запроса и любых пользовательских событий и трассировок, которые являются частью обработки запроса, будут одинаковые идентификаторы операций.

#### Web.TelemetryInitializers.WebUserTelemetryInitializer

Этот инициализатор добавляет идентификатор анонимного пользователя в каждый элемент телеметрии. Это позволяет отфильтровать только те события, которые связаны с действиями одного пользователя при поиске по журналу диагностики. Например, если придет сообщение об исключении, вы сможете трассировать действия пользователя.

Также задает telemetryClient.Context.User.

#### Web.TelemetryInitializers.WebSessionTelemetryInitializer

Этот инициализатор добавляет идентификатор сеанса для каждого события и задает telemetryClient.Context.Session.

## Пользовательские инициализаторы


Если для вашего приложения не подходят стандартные инициализаторы, вы можете создать собственные.

Используйте пользовательские инициализаторы, чтобы задать значения, которые будут использоваться для инициализации каждого клиента телеметрии. Например, если вы опубликовали нескольких версий приложения, можно убедиться, что вы можете разделить данные, отфильтровав их по настраиваемому свойству:

    plublic class MyContextInitializer: IContextInitializer
    {
        public void Initialize(TelemetryContext context)
        {
          context.Properties["AppVersion"] = "v2.1";
        }
    }

Используйте инициализаторы телеметрии, чтобы добавить обработку каждого события. Например, любой запрос с кодом ответа >=400 веб-пакет SDK помечает как неудавшийся. Такое поведение можно переопределить:

    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as RequestTelemetry;
            if (requestTelemetry == null) return;
            int code;
            bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
            if (!parsed) return;
            if (code >= 400 && code < 500)
            {
                requestTelemetry.Success = true;
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }            
        }
    }
 
Чтобы установить свои инициализаторы, добавьте строки в файл ApplicationInsights.config:

    <TelemetryInitializers> <!-- or ContextInitializers -->
    <Add Type="MyNamespace.MyTelemetryInitializer, MyAssemblyName" />


Можно также написать код для установки инициализатора на раннем этапе выполнения приложения. Например:


    // In the app initializer such as Global.asax.cs:

    protected void Application_Start()
    {
      TelemetryConfiguration.Active.TelemetryInitializers.Add(
                new MyTelemetryInitializer());
            ...




## InstrumentationKey

Он определяет ресурс Application Insights, в котором отображаются данные. Обычно создается отдельный ресурс с отдельным ключом инструментирования для каждого приложения.

Если необходимо задать ключ динамически, например если вам нужно отправлять результаты из приложения в различные ресурсы, можно удалить ключ в файле конфигурации и задать его в коде.

Чтобы задать ключ для всех экземпляров TelemetryClient, в том числе стандартных модулей телеметрии, задайте его в разделе TelemetryConfiguration.Active. Задайте ключ в методе инициализации, таком как global.aspx.cs, в службе ASP.NET:

```C#

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      //...
```

Если нужно только отправлять определенный набор событий в другой ресурс, можно задать ключ для конкретного TelemetryClient:

```C#

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

[Подробнее об API][api].

Чтобы получить новый ключ, [создайте новый ресурс на портале Application Insights][new].

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-web-failures-exceptions.md
[netlogs]: app-insights-asp-net-trace-logs.md
[new]: app-insights-create-new-resource.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-get-started.md

 

<!---HONumber=62-->