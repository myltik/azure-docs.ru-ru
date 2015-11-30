<properties 
	pageTitle="Настройка пакета SDK для Application Insights с использованием файла ApplicationInsights.config или ApplicationInsights.xml" 
	description="Включение или отключение модулей сбора данных и добавление счетчиков производительности, а также других параметров" 
	services="application-insights"
    documentationCenter="" 
	authors="OlegAnaniev-MSFT"
    editor="alancameronwills" 
	manager="douge"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/05/2015" 
	ms.author="awills"/>

# Настройка пакета SDK для Application Insights с использованием файла ApplicationInsights.config или ApplicationInsights.xml

Пакет SDK .NET Application Insights состоит из нескольких пакетов NuGet. [Основной пакет](http://www.nuget.org/packages/Microsoft.ApplicationInsights) предоставляет API для отправки телеметрии в Application Insights. [Дополнительные пакеты](http://www.nuget.org/packages?q=Microsoft.ApplicationInsights) предоставляют _модули_ и _инициализаторы_ телеметрии для автоматического отслеживания телеметрии вашего приложения и его контекста. При настройке файла конфигурации можно включить или отключить модули телеметрии и задать для них параметры.

Имя файла конфигурации – `ApplicationInsights.config` или `ApplicationInsights.xml` в зависимости от типа приложения. Он добавляется в проект автоматически при [установке большинства версий пакета SDK][start]. Кроме того, [монитор состояний на сервере IIS][redfield] добавляет этот файл в веб-приложение. Он также добавляется при [выборе расширения Appplication Insights для веб-сайта или виртуальной машины Azure][azure].

Нет эквивалентного файла для управления [пакетом SDK на веб-странице][client].

В этом документе описываются разделы файла конфигурации и то, как они управляют компонентами пакета SDK. Кроме того, в нем содержатся сведения о пакетах NuGet, загружающих эти компоненты.

## Модули телеметрии (ASP.NET)

Каждый модуль телеметрии собирает определенный тип данных и использует основной API для их отправки. Модули устанавливаются разными пакетами NuGet, что также добавляет необходимые строки в CONFIG-файл.

В файле конфигурации есть узел для каждого модуля. Чтобы удалить модуль, удалите узел или закомментируйте его.



### Отслеживание зависимостей

Функция [отслеживания зависимостей](app-insights-dependencies.md) собирает данные телеметрии о вызовах, выполняемых приложением к базам данных и внешним службам. Чтобы этот модуль работал на сервере IIS, [установите монитор состояний][redfield]. Чтобы использовать его в веб-приложениях или виртуальных машинах Azure, [выберите расширение Application Insights][azure].

Можно также написать код отслеживания зависимостей, используя [API TrackDependency](app-insights-api-custom-events-metrics.md#track-dependency).


* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* Пакет NuGet [Microsoft.ApplicationInsights.DependencyCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector).

### Сборщик данных производительности

[Собирает данные счетчиков производительности системы](app-insights-web-monitor-performance.md#system-performance-counters), например ЦП, памяти и сетевой нагрузки из установок IIS. Можно указать конкретные счетчики для сбора данных, включая счетчики производительности, настроенные самостоятельно.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* Пакет NuGet [Microsoft.ApplicationInsights.PerfCounterCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector).


### Телеметрия диагностики Application Insights

`DiagnosticsTelemetryModule` информирует об ошибках в самом коде инструментирования Application Insights, например, если код не может получить доступ к счетчикам производительности или `ITelemetryInitializer` вызывает исключение. Телеметрия трассировки, собранная этим модулем, отображается в результатах [диагностического поиска][diagnostic].
 
* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* Пакет NuGet [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights). Если просто установить этот пакет, файл ApplicationInsights.config не создается автоматически. 

### Режим разработчика

`DeveloperModeWithDebuggerAttachedTelemetryModule` заставляет Application Insights `TelemetryChannel` отправлять данные незамедлительно (по одному элементу телеметрии за один раз), как только к процессу приложения присоединяется отладчик. Это сокращает период времени между моментом получения данных телеметрии вашим приложением и моментом их отображения на портале Application Insights. Это вызывает значительную нагрузку на процессор и пропускную способность сети.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* Пакет NuGet [Application Insights Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)

### Отслеживание веб-запросов

Передает данные о [времени отклика и коде результата](app-insights-start-monitoring-app-health-usage.md) HTTP-запросов.

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* Пакет NuGet [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web).

### Отслеживание исключений

`ExceptionTrackingTelemetryModule` отслеживает количество необработанных исключений в вашем веб-приложении. См. статью [Ошибки и исключения][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* Пакет NuGet [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web).


* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule` отслеживает [незамеченные исключения задач](http://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx).
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule` отслеживает необработанные исключения для рабочих ролей, служб Windows и консольных приложений.
* Пакет NuGet [Application Insights Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/).

### Основной API

Основной пакет содержит [основной API](https://msdn.microsoft.com/library/mt420197.aspx) пакета SDK. Его используют другие модули телеметрии, кроме того, вы можете [использовать его для определения собственной телеметрии](app-insights-api-custom-events-metrics.md).

* Нет записей в файле ApplicationInsights.config.
* Пакет NuGet [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights). Если просто установить этот пакет NuGet, CONFIG-файл не создается.

## Канал телеметрии

Канал телеметрии управляет буферизацией и передачей данных телеметрии в службу Application Insights.

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` — канал по умолчанию для служб. Он создает буфер данных в памяти.
* `Microsoft.ApplicationInsights.PersistenceChannel` — альтернатива для консольных приложений. Когда приложение закрывается, он сохраняет неочищенные данные в постоянное хранилище и отправляет их при повторном запуске приложения.


## Инициализаторы телеметрии (ASP.NET)

Инициализаторы телеметрии задают свойства контекста, которые отправляются вместе с каждым элементом телеметрии.

Вы можете [написать собственные инициализаторы](app-insights-api-filtering-sampling.md#add-properties) для задания свойств контекста.

* `OperationNameTelemetryInitializer` обновляет свойство контекста `Operation.Id` всех элементов телеметрии, отслеживаемых при обработке запроса с автоматически сформированным `RequestTelemetry.Id`.


* `AccountIdTelemetryInitializer` задает свойство AccountId.
* `AuthenticatedUserIdTelemetryInitializer` задает свойство AuthenticatedUserId, как задано пакетом SDK JavaScript.
* `AzureRoleEnvironmentTelemetryInitializer` обновляет свойства `RoleName` и `RoleInstance` контекста `Device` для всех элементов телеметрии с учетом данных, извлеченных из среды выполнения Azure.
* `BuildInfoConfigComponentVersionTelemetryInitializer` обновляет свойство `Version` контекста `Component` для всех элементов телеметрии с учетом значения, извлеченного из файла `BuildInfo.config`, созданного платформой MSBuild.
* `ClientIpHeaderTelemetryInitializer` обновляет свойство `Ip` контекста `Location` всех элементов телеметрии в соответствии с HTTP-заголовком запроса `X-Forwarded-For`.
* `DeviceTelemetryInitializer` обновляет следующие свойства контекста `Device` для всех элементов телеметрии.
 - `Type` получает значение «PC» (Компьютер).
 - `Id` получает значение, соответствующее доменному имени компьютера, на котором выполняется веб-приложение.
 - `OemName` получает значение, извлеченное из поля `Win32_ComputerSystem.Manufacturer` с помощью WMI.
 - `Model` получает значение, извлеченное из поля `Win32_ComputerSystem.Model` с помощью WMI.
 - `NetworkType` получает значение, извлеченное из `NetworkInterface`.
 - `Language` получает значение, соответствующее имени `CurrentCulture`.
* `DomainNameRoleInstanceTelemetryInitializer` обновляет свойства `RoleInstance` контекста `Device` для всех элементов телеметрии с учетом доменного имени компьютера, на котором выполняется веб-приложение.
* `OperationNameTelemetryInitializer`обновляет свойство `Name` контекста `RequestTelemetry` и свойство `Name` контекста `Operation` всех элементов телеметрии в соответствии с методом HTTP, а также именами контроллера MVC ASP.NET и действия, вызываемого для обработки запроса.
* `OperationIdTelemetryInitializer` обновляет свойство контекста `Operation.Id` всех элементов телеметрии, отслеживаемых при обработке запроса с автоматически созданным `RequestTelemetry.Id`.
* `SessionTelemetryInitializer` обновляет свойство `Id` контекста `Session` для всех элементов телеметрии со значениями, извлеченными из файла cookie `ai_session`, созданного кодом JavaScript инструментирования Application Insights, который выполняется в браузере пользователя. 
* `SyntheticTelemetryInitializer` обновляет свойства контекстов `User`, `Session` и `Operation` элементов телеметрии, отслеживаемых при обработке запроса от искусственного источника, например теста доступности или программы-робота поисковой системы. По умолчанию [обозреватель метрик](app-insights-metrics-explorer.md) не отображает данные телеметрии искусственных источников.
* `UserAgentTelemetryInitializer` обновляет свойство `UserAgent` контекста `User` всех элементов телеметрии в соответствии с HTTP-заголовком запроса `User-Agent`.
* `UserTelemetryInitializer` обновляет свойства `Id` и `AcquisitionDate` контекста `User` для всех элементов телеметрии со значениями, извлеченными из файла cookie `ai_user`, созданного кодом JavaScript инструментирования Application Insights, который выполняется в браузере пользователя.


## Обработчики данных телеметрии (ASP.NET)

Обработчики данных телеметрии могут фильтровать и изменять любые элементы телеметрии непосредственно перед отправкой из пакета SDK на портал.

Вы можете [написать собственные обработчики данных телеметрии](app-insights-api-filtering-sampling.md#filtering).

Также имеется стандартный [обработчик выборочной телеметрии](app-insights-api-filtering-sampling.md#sampling) (начиная с версии 2.0.1):

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```



## Параметры канала (Java)

Эти параметры влияют на то, как в пакетах SDK для Java хранятся и удаляются собранные ими данные телеметрии.

#### MaxTelemetryBufferCapacity

Этот параметр определяет количество элементов телеметрии, которые могут храниться в хранилище в памяти пакета SDK. По достижении этого количества происходит очистка буфера телеметрии, то есть элементы телеметрии отправляются на сервер Application Insights.

-	Мин. значение: 1.
-	Макс. значение: 1000.
-	Значение по умолчанию: 500.

```

  <ApplicationInsights>
      ...
      <Channel>
       <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity>
      </Channel>
      ...
  </ApplicationInsights>
```

#### FlushIntervalInSeconds 

Этот параметр определяет, как часто следует очищать (отправлять в Application Insights) данные, хранящиеся в хранилище в памяти.

-	Мин. значение: 1.
-	Макс. значение: 300.
-	Значение по умолчанию: 5.

```

    <ApplicationInsights>
      ...
      <Channel>
        <FlushIntervalInSeconds>100</FlushIntervalInSeconds>
      </Channel>
      ...
    </ApplicationInsights>
```

#### MaxTransmissionStorageCapacityInMB

Этот параметр определяет максимальный размер пространства (в МБ), выделенного для постоянного хранилища на локальном диске. Это хранилище используется для хранения элементов телеметрии, которые не удалось передать в конечную точку Application Insights. Если хранилище будет заполнено, новые элементы телеметрии будут отклонены.

-	Мин. значение: 1.
-	Макс. значение: 100.
-	Значение по умолчанию: 10.

```

   <ApplicationInsights>
      ...
      <Channel>
        <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB>
      </Channel>
      ...
   </ApplicationInsights>
```



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
[start]: app-insights-overview.md

<!---HONumber=Nov15_HO4-->