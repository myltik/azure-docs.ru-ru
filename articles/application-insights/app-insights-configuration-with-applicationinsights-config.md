---
title: Справочник по файлу ApplicationInsights.config в Azure | Документация Майкрософт
description: Включение или отключение модулей сбора данных и добавление счетчиков производительности, а также других параметров.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 6e397752-c086-46e9-8648-a1196e8078c2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 05/03/2017
ms.reviewer: olegan
ms.author: mbullwin
ms.openlocfilehash: 664f31d64ac037acea2fb45a8d8b813da52b6da5
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294706"
---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>Настройка пакета SDK для Application Insights с использованием файла ApplicationInsights.config или ApplicationInsights.xml
Пакет SDK .NET Application Insights состоит из нескольких пакетов NuGet. [Основной пакет](http://www.nuget.org/packages/Microsoft.ApplicationInsights) предоставляет API для отправки телеметрии в Application Insights. [Дополнительные пакеты](http://www.nuget.org/packages?q=Microsoft.ApplicationInsights) предоставляют *модули* и *инициализаторы* телеметрии для автоматического отслеживания телеметрии вашего приложения и его контекста. При настройке файла конфигурации можно включить или отключить модули телеметрии и задать для них параметры.

Имя файла конфигурации – `ApplicationInsights.config` или `ApplicationInsights.xml` в зависимости от типа приложения. Он добавляется в проект автоматически при [установке большинства версий пакета SDK][start]. Кроме того, [монитор состояний на сервере IIS][redfield] добавляет этот файл в веб-приложение. Он также добавляется при [выборе расширения Application Insights для веб-сайта или виртуальной машины Azure](app-insights-azure-web-apps.md).

Эквивалентного файла для управления [пакетом SDK на веб-странице][client] не существует.

В этом документе описываются разделы файла конфигурации и то, как они управляют компонентами пакета SDK. Кроме того, в нем содержатся сведения о пакетах NuGet, загружающих эти компоненты.

> [!NOTE]
> ApplicationInsights.config и XML-файлы инструкций не применяются к пакету SDK для .NET Core. Для изменений в приложении .NET Core обычно используется файл appsettings.json. Пример этого можно найти в [документации по Snapshot Debugger](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications).

## <a name="telemetry-modules-aspnet"></a>Модули телеметрии (ASP.NET)
Каждый модуль телеметрии собирает определенный тип данных и использует основной API для их отправки. Модули устанавливаются разными пакетами NuGet, что также добавляет необходимые строки в CONFIG-файл.

В файле конфигурации есть узел для каждого модуля. Чтобы удалить модуль, удалите узел или закомментируйте его.

### <a name="dependency-tracking"></a>Отслеживание зависимостей
[Dependency tracking](app-insights-asp-net-dependencies.md) собирает данные телеметрии о вызовах, выполняемых приложением к базам данных и внешним службам. Чтобы этот модуль работал на сервере IIS, [установите монитор состояний][redfield]. Чтобы использовать его в веб-приложениях или виртуальных машинах Azure, [выберите расширение Application Insights](app-insights-azure-web-apps.md).

Можно также написать код отслеживания зависимостей, используя [API TrackDependency](app-insights-api-custom-events-metrics.md#trackdependency).

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.DependencyCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) .

### <a name="performance-collector"></a>Сборщик данных производительности
[Собирает данные счетчиков производительности системы](app-insights-performance-counters.md), например ЦП, памяти и сетевой нагрузки из установок IIS. Можно указать конкретные счетчики для сбора данных, включая счетчики производительности, настроенные самостоятельно.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft.ApplicationInsights.PerfCounterCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) .

### <a name="application-insights-diagnostics-telemetry"></a>Телеметрия диагностики Application Insights
`DiagnosticsTelemetryModule` информирует об ошибках в самом коде инструментирования Application Insights, например, если код не может получить доступ к счетчикам производительности или `ITelemetryInitializer` вызывает исключение. Данные телеметрии трассировки, собранные этим модулем, отображаются в колонке [Diagnostic Search][diagnostic] (Поиск в диагностических данных). Отправляет диагностические данные по адресу dc.services.vsallin.net.

* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) . Если просто установить этот пакет, файл ApplicationInsights.config не создается автоматически.

### <a name="developer-mode"></a>Режим разработчика
`DeveloperModeWithDebuggerAttachedTelemetryModule` заставляет Application Insights `TelemetryChannel` отправлять данные незамедлительно (по одному элементу телеметрии за один раз), как только к процессу приложения присоединяется отладчик. Это сокращает период времени между моментом получения данных телеметрии вашим приложением и моментом их отображения на портале Application Insights. Это вызывает значительную нагрузку на процессор и пропускную способность сети.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Application Insights Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) 

### <a name="web-request-tracking"></a>Отслеживание веб-запросов
Передает данные о [времени отклика и коде результата](app-insights-asp-net.md) HTTP-запросов.

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) 

### <a name="exception-tracking"></a>Отслеживание исключений
`ExceptionTrackingTelemetryModule` отслеживает количество необработанных исключений в вашем веб-приложении. Ознакомьтесь со статьей [Ошибки и исключения][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) 
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule` отслеживает [незамеченные исключения задач](http://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx).
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule` отслеживает необработанные исключения для рабочих ролей, служб Windows и консольных приложений.
* [Application Insights Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) .

### <a name="eventsource-tracking"></a>Отслеживание EventSource
`EventSourceTelemetryModule` позволяет настроить отправку событий EventSource в Application Insights в качестве трассировок. Сведения об отслеживании событий EventSource см. в разделе [Использование событий EventSource](app-insights-asp-net-trace-logs.md#using-eventsource-events).

* `Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule`
* [Microsoft.ApplicationInsights.EventSourceListener](http://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener) 

### <a name="etw-event-tracking"></a>Трассировка событий Windows
`EtwCollectorTelemetryModule` позволяет настроить отправку событий от поставщиков трассировки событий Windows в Application Insights в качестве трассировок. Сведения об отслеживании событий трассировки событий Windows см. в разделе [Использование событий трассировки событий Windows](app-insights-asp-net-trace-logs.md#using-etw-events).

* `Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule`
* [Microsoft.ApplicationInsights.EtwCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector) 

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights
Пакет Microsoft.ApplicationInsights предоставляет [основной API](https://msdn.microsoft.com/library/mt420197.aspx) пакета SDK. Его используют другие модули телеметрии, кроме того, вы можете [использовать его для определения собственной телеметрии](app-insights-api-custom-events-metrics.md).

* Нет записей в файле ApplicationInsights.config.
* [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) . Если просто установить этот пакет NuGet, CONFIG-файл не создается.

## <a name="telemetry-channel"></a>Канал телеметрии
Канал телеметрии управляет буферизацией и передачей данных телеметрии в службу Application Insights.

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` — канал по умолчанию для служб. Он создает буфер данных в памяти.
* `Microsoft.ApplicationInsights.PersistenceChannel` — альтернатива для консольных приложений. Когда приложение закрывается, он сохраняет неочищенные данные в постоянное хранилище и отправляет их при повторном запуске приложения.

## <a name="telemetry-initializers-aspnet"></a>Инициализаторы телеметрии (ASP.NET)
Инициализаторы телеметрии задают свойства контекста, которые отправляются вместе с каждым элементом телеметрии.

Вы можете [написать собственные инициализаторы](app-insights-api-filtering-sampling.md#add-properties) для задания свойств контекста.

Все стандартные инициализаторы задаются пакетами NuGet Web или WindowsServer.

* `AccountIdTelemetryInitializer` задает свойство AccountId.
* `AuthenticatedUserIdTelemetryInitializer` задает свойство AuthenticatedUserId, как задано пакетом SDK JavaScript.
* `AzureRoleEnvironmentTelemetryInitializer` обновляет свойства `RoleName` и `RoleInstance` контекста `Device` для всех элементов телеметрии с учетом данных, извлеченных из среды выполнения Azure.
* `BuildInfoConfigComponentVersionTelemetryInitializer` обновляет свойство `Version` контекста `Component` для всех элементов телеметрии с учетом значения, извлеченного из файла `BuildInfo.config`, созданного платформой MSBuild.
* `ClientIpHeaderTelemetryInitializer` обновляет свойство `Ip` контекста `Location` всех элементов телеметрии в соответствии с HTTP-заголовком запроса `X-Forwarded-For`.
* `DeviceTelemetryInitializer` обновляет следующие свойства контекста `Device` для всех элементов телеметрии.
  * `Type` получает значение «PC» (Компьютер).
  * `Id` получает значение, соответствующее доменному имени компьютера, на котором выполняется веб-приложение.
  * `OemName` получает значение, извлеченное из поля `Win32_ComputerSystem.Manufacturer` с помощью WMI.
  * `Model` получает значение, извлеченное из поля `Win32_ComputerSystem.Model` с помощью WMI.
  * `NetworkType` получает значение, извлеченное из `NetworkInterface`.
  * `Language` получает значение, соответствующее имени `CurrentCulture`.
* `DomainNameRoleInstanceTelemetryInitializer` обновляет свойства `RoleInstance` контекста `Device` для всех элементов телеметрии с учетом доменного имени компьютера, на котором выполняется веб-приложение.
* `OperationNameTelemetryInitializer`обновляет свойство `Name` контекста `RequestTelemetry` и свойство `Name` контекста `Operation` всех элементов телеметрии в соответствии с методом HTTP, а также именами контроллера MVC ASP.NET и действия, вызываемого для обработки запроса.
* `OperationIdTelemetryInitializer` или `OperationCorrelationTelemetryInitializer` обновляет свойство контекста `Operation.Id` всех элементов телеметрии, отслеживаемых при обработке запроса с автоматически созданным `RequestTelemetry.Id`.
* `SessionTelemetryInitializer` обновляет свойство `Id` контекста `Session` для всех элементов телеметрии со значениями, извлеченными из файла cookie `ai_session`, созданного кодом JavaScript инструментирования Application Insights, который выполняется в браузере пользователя.
* `SyntheticTelemetryInitializer` или `SyntheticUserAgentTelemetryInitializer` обновляет свойства контекстов `User`, `Session` и `Operation` элементов телеметрии, отслеживаемых при обработке запроса от искусственного источника, например теста доступности или программы-робота поисковой системы. По умолчанию [обозреватель метрик](app-insights-metrics-explorer.md) не отображает данные телеметрии искусственных источников.

    `<Filters>` задает идентификационные свойства запросов.
* `UserTelemetryInitializer` обновляет свойства `Id` и `AcquisitionDate` контекста `User` для всех элементов телеметрии со значениями, извлеченными из файла cookie `ai_user`, созданного кодом JavaScript инструментирования Application Insights, который выполняется в браузере пользователя.
* `WebTestTelemetryInitializer` задает идентификатор пользователя, идентификатор сеанса и свойства искусственного источника для HTTP-запросов, поступающих из [тестов доступности](app-insights-monitor-web-app-availability.md).
  `<Filters>` задает идентификационные свойства запросов.

Для приложений .NET, работающих в Service Fabric, можно добавить пакет NuGet `Microsoft.ApplicationInsights.ServiceFabric`. Данный пакет содержит компонент `FabricTelemetryInitializer`, который добавляет свойства Service Fabric в элементы телеметрии. Дополнительные сведения см. на [странице GitHub](https://go.microsoft.com/fwlink/?linkid=848457), посвященной свойствам, добавляемым пакетом NuGet.

## <a name="telemetry-processors-aspnet"></a>Обработчики данных телеметрии (ASP.NET)
Обработчики данных телеметрии могут фильтровать и изменять любые элементы телеметрии непосредственно перед отправкой из пакета SDK на портал.

Вы можете [написать собственные обработчики данных телеметрии](app-insights-api-filtering-sampling.md#filtering).

#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>Обработчик адаптивной выборки телеметрии (начиная с версии 2.0.0-beta3)
Эта функция включена по умолчанию. Если приложение отправляет слишком много телеметрических данных, обработчик удаляет часть из них.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

С помощью параметра можно задать целевой показатель, которого алгоритм будет пытаться достичь. Каждый экземпляр пакета SDK работает независимо от других, поэтому если сервер представляет собой кластер из нескольких машин, фактический объем телеметрических данных будет кратен их количеству.

[Дополнительная информация о выборке](app-insights-sampling.md).

#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>Обработчик выборки телеметрии с фиксированной частотой (начиная с версии 2.0.0-beta1)
Также имеется стандартный [обработчик выборочной телеметрии](app-insights-api-filtering-sampling.md) (начиная с версии 2.0.1):

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```



## <a name="channel-parameters-java"></a>Параметры канала (Java)
Эти параметры влияют на то, как в пакетах SDK для Java хранятся и удаляются собранные ими данные телеметрии.

#### <a name="maxtelemetrybuffercapacity"></a>MaxTelemetryBufferCapacity
Этот параметр определяет количество элементов телеметрии, которые могут храниться в хранилище в памяти пакета SDK. По достижении этого количества происходит очистка буфера телеметрии, то есть элементы телеметрии отправляются на сервер Application Insights.

* Мин. значение: 1.
* Макс. значение: 1000.
* Значение по умолчанию: 500.

```

  <ApplicationInsights>
      ...
      <Channel>
       <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity>
      </Channel>
      ...
  </ApplicationInsights>
```

#### <a name="flushintervalinseconds"></a>FlushIntervalInSeconds
Этот параметр определяет, как часто следует очищать (отправлять в Application Insights) данные, хранящиеся в хранилище в памяти.

* Мин. значение: 1.
* Макс. значение: 300.
* Значение по умолчанию: 5.

```

    <ApplicationInsights>
      ...
      <Channel>
        <FlushIntervalInSeconds>100</FlushIntervalInSeconds>
      </Channel>
      ...
    </ApplicationInsights>
```

#### <a name="maxtransmissionstoragecapacityinmb"></a>MaxTransmissionStorageCapacityInMB
Этот параметр определяет максимальный размер пространства (в МБ), выделенного для постоянного хранилища на локальном диске. Это хранилище используется для хранения элементов телеметрии, которые не удалось передать в конечную точку Application Insights. Если хранилище будет заполнено, новые элементы телеметрии будут отклонены.

* Мин. значение: 1.
* Макс. значение: 100.
* Значение по умолчанию: 10.

```

   <ApplicationInsights>
      ...
      <Channel>
        <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB>
      </Channel>
      ...
   </ApplicationInsights>
```



## <a name="instrumentationkey"></a>InstrumentationKey
Он определяет ресурс Application Insights, в котором отображаются данные. Обычно создается отдельный ресурс с отдельным ключом инструментирования для каждого приложения.

Если необходимо задать ключ динамически, например если вам нужно отправлять результаты из приложения в различные ресурсы, можно удалить ключ в файле конфигурации и задать его в коде.

Чтобы задать ключ для всех экземпляров TelemetryClient, в том числе стандартных модулей телеметрии, задайте его в разделе TelemetryConfiguration.Active. Задайте ключ в методе инициализации, таком как global.aspx.cs, в службе ASP.NET:

```csharp

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey =
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      //...
```

Если нужно только отправлять определенный набор событий в другой ресурс, можно задать ключ для конкретного TelemetryClient:

```csharp

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

Чтобы получить новый ключ, [создайте новый ресурс на портале Application Insights][new].



## <a name="applicationid-provider"></a>Поставщик идентификаторов приложений

_Доступно начиная с версии 2.6.0_.

Этот поставщик предназначен для поиска идентификатора приложения по ключу инструментирования. Идентификатор приложения включен в RequestTelemetry и DependencyTelemetry и используется для определения корреляции на портале.

Чтобы использовать его, установите `TelemetryConfiguration.ApplicationIdProvider` в коде или в конфигурации приложения.

### <a name="interface-iapplicationidprovider"></a>Интерфейс: IApplicationIdProvider

```csharp
public interface IApplicationIdProvider
{
    bool TryGetApplicationId(string instrumentationKey, out string applicationId);
}
```


В пакете SDK [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) мы предоставляем две его реализации: `ApplicationInsightsApplicationIdProvider` и `DictionaryApplicationIdProvider`.

### <a name="applicationinsightsapplicationidprovider"></a>ApplicationInsightsApplicationIdProvider

Этот интерфейс является оболочкой API-интерфейса Profile. Он регулирует запросы и кэширует их результаты.

Этот поставщик добавляется в файл конфигурации после установки [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) или [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/).

Этот класс содержит необязательное свойство `ProfileQueryEndpoint`.
По умолчанию оно имеет значение `https://dc.services.visualstudio.com/api/profiles/{0}/appId`.
Если для этой конфигурации вам нужен прокси-сервер, мы рекомендуем настроить на нем базовый адрес и добавить /api/profiles/{0}/appId. Обратите внимание, что во время выполнения заполнитель {0} заменяется ключом инструментирования для каждого конкретного запроса.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Пример настройки в ApplicationInsights.config:
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
        <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>Пример настройки напрямую в коде:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new ApplicationInsightsApplicationIdProvider();
```

### <a name="dictionaryapplicationidprovider"></a>DictionaryApplicationIdProvider

Это статический поставщик, который использует настроенные пары ключей инструментирования и идентификаторов приложений.

Этот класс имеет свойство `Defined`, в котором в формате Dictionary<строка,строка> сохраняются пары ключей инструментирования и идентификаторов приложений.

Этот класс содержит необязательное свойство `Next`, с помощью которого вы можете настроить другой поставщик, который будет использоваться при запросе тех ключей инструментирования, которых не существует в вашей конфигурации.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Пример настройки в ApplicationInsights.config:
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.DictionaryApplicationIdProvider, Microsoft.ApplicationInsights">
        <Defined>
            <Type key="InstrumentationKey_1" value="ApplicationId_1"/>
            <Type key="InstrumentationKey_2" value="ApplicationId_2"/>
        </Defined>
        <Next Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights" />
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>Пример настройки напрямую в коде:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new DictionaryApplicationIdProvider{
 Defined = new Dictionary<string, string>
    {
        {"InstrumentationKey_1", "ApplicationId_1"},
        {"InstrumentationKey_2", "ApplicationId_2"}
    }
};
```




## <a name="next-steps"></a>Дополнительная информация
[Узнайте больше об API.][api]

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[netlogs]: app-insights-asp-net-trace-logs.md
[new]: app-insights-create-new-resource.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
