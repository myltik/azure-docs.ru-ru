<properties 
	pageTitle="Настройка пакета SDK для Application Insights с использованием файла ApplicationInsights.config или ApplicationInsights.xml" 
	description="Включение или отключение модулей сбора данных и добавление счетчиков производительности, а также других параметров" 
	services="application-insights"
    documentationCenter="" 
	authors="OlegAnaniev-MSFT"
    editor="alancameronwills" 
	manager="meravd"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2015" 
	ms.author="awills"/>

# Настройка пакета SDK для Application Insights с использованием файла ApplicationInsights.config или ApplicationInsights.xml

Пакет SDK .NET Application Insights состоит из нескольких пакетов NuGet. [Основной пакет](http://www.nuget.org/packages/Microsoft.ApplicationInsights) предоставляет API для отправки телеметрии в Application Insights. [Дополнительные пакеты](http://www.nuget.org/packages?q=Microsoft.ApplicationInsights) предоставляют _модули_ и _инициализаторы_ телеметрии для автоматического отслеживания телеметрии вашего приложения и его контекста. При настройке файла конфигурации можно включить или отключить модули телеметрии и задать для них параметры.

Имя файла конфигурации – `ApplicationInsights.config` или `ApplicationInsights.xml` в зависимости от типа приложения. Он добавляется в проект автоматически при [установке некоторых версий пакета SDK][start]. Кроме того, [монитор состояний на сервере IIS][redfield] добавляет этот файл в веб-приложение. Он также добавляется при [выборе расширения Appplication Insights для веб-сайта или виртуальной машины Azure][azure].

Нет эквивалентного файла для управления [пакетом SDK на веб-странице][client].

В файле конфигурации есть узел для каждого модуля. Чтобы удалить модуль, удалите узел или закомментируйте его.

## Пакет NuGet [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights)

Пакет NuGet `Microsoft.ApplicationInsights` предоставляет в `ApplicationInsights.config` следующие модули телеметрии.

```
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule, Microsoft.ApplicationInsights" />
  </TelemetryModules>
</ApplicationInsights>
```
`DiagnosticsTelemetryModule` информирует об ошибках сам код инструментирования Application Insights, например, если код не может получить доступ к счетчикам производительности или `ITelemetryInitializer` вызывает исключение. Телеметрия трассировки, собранная этим модулем, отображается в результатах [диагностического поиска][diagnostic].

## Пакет NuGet [Microsoft.ApplicationInsights.DependencyCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector)

Пакет NuGet `Microsoft.ApplicationInsights.DependencyCollector` предоставляет в `ApplicationInsights.config` следующие модули телеметрии.

```
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.ApplicationInsights.Extensibility.DependencyCollector" />
  </TelemetryModules>
</ApplicationInsights>
```
`DependencyTrackingTelemetryModule` отслеживает телеметрию вызовов, совершенных приложениями к внешним зависимостям, например HTTP-запросы или запросы SQL. Чтобы этот модуль работал на сервере IIS, [установите монитор состояний][redfield]. Чтобы использовать его в веб-приложениях или виртуальных машинах Azure, [выберите расширение Application Insights][azure].

Можно также написать код отслеживания зависимостей, используя [API TrackDependency](app-insights-api-custom-events-metrics.md#track-dependency).

## Пакет NuGet [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)

Пакет NuGet `Microsoft.ApplicationInsights.Web` предоставляет в `ApplicationInsights.config` следующие инициализаторы и модули телеметрии.

```
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryInitializers>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.SyntheticTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.ClientIpHeaderTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.UserAgentTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.OperationNameTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.OperationIdTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.UserTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.SessionTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.AzureRoleEnvironmentTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.DomainNameRoleInstanceTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.BuildInfoConfigComponentVersionTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.DeviceTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web"/>
  </TelemetryInitializers>
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.RequestTrackingTelemetryModule, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.ExceptionTrackingTelemetryModule, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.DeveloperModeWithDebuggerAttachedTelemetryModule, Microsoft.ApplicationInsights.Extensibility.Web" />
  </TelemetryModules>
</ApplicationInsights>
```

**Инициализаторы**

* `SyntheticTelemetryInitializer` обновляет свойства контекстов `User`, `Session` и `Operation` элементов телеметрии. отслеживаемых при обработке запроса от искусственного источника, например теста доступности. По умолчанию на портале Application Insights данные искусственной телеметрии не отображаются.
* `ClientIpHeaderTelemetryInitializer` обновляет свойство `Ip` контекста `Location` всех элементов телеметрии в соответствии с HTTP-заголовком запроса `X-Forwarded-For`.
* `UserAgentTelemetryInitializer` обновляет свойство `UserAgent` контекста `User` всех элементов телеметрии в соответствии с HTTP-заголовком запроса `User-Agent`.
* `OperationNameTelemetryInitializer`обновляет свойство `Name` контекста `RequestTelemetry` и свойство `Name` контекста `Operation` всех элементов телеметрии в соответствии с методом HTTP, а также именами MVC-контроллера ASP.NET и действием, вызываемым для обработки запроса.
* `OperationNameTelemetryInitializer` обновляет свойство контекста `Operation.Id` всех элементов телеметрии, отслеживаемых при обработке запроса с автоматически сформированным `RequestTelemetry.Id`.
* `UserTelemetryInitializer` обновляет свойства `Id` и `AcquisitionDate` контекста `User` для всех элементов телеметрии со значениями, извлеченными из файла cookie `ai_user`, созданного JavaScript-кодом инструментирования Application Insights, который выполняется в браузере пользователя.
* `SessionTelemetryInitializer` обновляет свойство `Id` контекста `Session` для всех элементов телеметрии со значениями, извлеченными из файла cookie `ai_session`, созданного JavaScript-кодом инструментирования Application Insights, который выполняется в браузере пользователя. 
* `AzureRoleEnvironmentTelemetryInitializer` обновляет свойства `RoleName` и `RoleInstance` контекста `Device` для всех элементов телеметрии с учетом данных, извлеченных из среды выполнения Azure.
* `DomainNameRoleInstanceTelemetryInitializer` обновляет свойства `RoleInstance` контекста `Device` для всех элементов телеметрии с учетом доменного имени компьютера, на котором выполняется веб-приложение.
* `BuildInfoConfigComponentVersionTelemetryInitializer` обновляет свойство `Version` контекста `Component` для всех элементов телеметрии с учетом значения, извлеченного из файла `BuildInfo.config`, созданного при сборке TFS.
* `DeviceTelemetryInitializer` обновляет следующие свойства контекста `Device` для всех элементов телеметрии:
 - `Type` получает значение PC (Компьютер).
 - `Id` получает значение, соответствующее доменному имени компьютера, на котором выполняется веб-приложение.
 - `OemName` получает значение, извлеченное из поля `Win32_ComputerSystem.Manufacturer` с помощью WMI.
 - `Model` получает значение, извлеченное из поля `Win32_ComputerSystem.Model` с помощью WMI.
 - `NetworkType` получает значение, извлеченное из `NetworkInterface`.
 - `Language` получает значение, соответствующее имени `CurrentCulture`.

**модули**

* `RequestTrackingTelemetryModule` отслеживает запросы, получаемые вашим веб-приложением, и измеряет время отклика.
* `ExceptionTrackingTelemetryModule` отслеживает количество необработанных исключений в вашем веб-приложении. См. статью [Ошибки и исключения][exceptions].
* `DeveloperModeWithDebuggerAttachedTelemetryModule` заставляет Application Insights `TelemetryChannel` отправлять данные незамедлительно (по одному элементу телеметрии за один раз), как только к процессу приложения присоединяется отладчик. Это сокращает период времени между моментом получения данных телеметрии вашим приложением и моментом их отображения на портале Application Insights, но требует значительных ресурсов процессора и нагрузки на пропускную способность сети.

## Пакет NuGet [Microsoft.ApplicationInsights.PerfCounterCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector)

Пакет NuGet `Microsoft.ApplicationInsights.PerfCounterCollector` по умолчанию добавляет в `ApplicationInsights.config` следующие модули телеметрии.

```
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector" />
  </TelemetryModules>
</ApplicationInsights>
```

### PerformanceCollectorModule

`PerformanceCollectorModule` отслеживает количество счетчиков производительности Windows. Эти счетчики можно увидеть, щелкнув диаграмму в обозревателе метрик и открыв таким образом колонку сведений об этой диаграмме.

Вы также можете выполнять мониторинг дополнительных счетчиков производительности – стандартных счетчиков Windows или любых других добавленных счетчиков.
      
Для сбора данных дополнительных счетчиков производительности используйте следующий синтаксис:

```
<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector">
  <Counters>
    <Add PerformanceCounter="\MyCategory\MyCounter" />
    <Add PerformanceCounter="\Process(??APP_WIN32_PROC??)\Handle Count" ReportAs="Process handle count" />
    <!-- ... -->
  </Counters>
</Add>
```      
      
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

## Инициализаторы телеметрии

Можно написать инициализаторы телеметрии, которые будут фильтровать и изменять данные телеметрии, собранные с помощью приложения. Инициализаторы можно запускать из CONFIG-файла вместе со стандартными модулями. [Подробнее](app-insights-api-filtering-sampling.md)


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

<!---HONumber=Nov15_HO1-->