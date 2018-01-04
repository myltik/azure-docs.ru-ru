---
title: "Azure Application Insights для консольных приложений | Документы Microsoft"
description: "Отслеживайте доступность, производительность и использование веб-приложений."
services: application-insights
documentationcenter: .net
author: lmolkova
manager: bfung
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: lmolkova
ms.openlocfilehash: 1b6bef88d729a2febfca2bd236a5382d2c11bd69
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/03/2018
---
# <a name="application-insights-for-net-console-applications"></a>Application Insights для .NET консольных приложений
[Application Insights](app-insights-overview.md) позволяет вести наблюдение за веб-приложения для обеспечения доступности, производительности и использования.

Вам потребуется подписка с [Microsoft Azure](http://azure.com). Войдите с учетной записью Майкрософт, который можно использовать для Windows, Xbox Live или других облачных служб Майкрософт. Возможно, у вашей группы есть подписка организации Azure: попросите ее владельца добавить вас к ней с помощью вашей учетной записи Майкрософт.

## <a name="getting-started"></a>Приступая к работе

* На [портале Azure](https://portal.azure.com) [создайте ресурс Application Insights](app-insights-create-new-resource.md). Для параметра типа приложения выберите приложение ASP.NET.
* Сделайте копию ключа инструментирования. Найти параметр в раскрывающемся списке Essentials созданный новый ресурс. 
* Установить последние [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) пакета.
* Установите ключ инструментирования в коде перед любой телеметрии отслеживания (или задать переменную среды APPINSIGHTS_INSTRUMENTATIONKEY). После этого можно будет вручную отслеживать телеметрии и отображается на портале Azure

```C#
TelemetryConfiguration.Active.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient();
telemetryClient.TrackTrace("Hello World!");
```

* Установите последнюю версию [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) пакета - автоматически отслеживает HTTP, SQL или некоторые другие вызовы внешних зависимостей.

Можно инициализировать и настроить Application Insights из кода или с помощью `ApplicationInsights.config` файла. Убедитесь, что инициализация происходит, как можно раньше.

### <a name="using-config-file"></a>С помощью файла конфигурации

По умолчанию, пакет SDK Application Insights ищет `ApplicationInsights.config` файл в рабочий каталог при `TelemetryConfiguration` создается

```C#
TelemetryConfiguration config = TelemetryConfiguration.Active; // Read ApplicationInsights.config file if present
```

Можно также указать путь к файлу конфигурации.

```C#
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration("ApplicationInsights.config");
```

Дополнительные сведения см. в разделе [ссылка на файл конфигурации](app-insights-configuration-with-applicationinsights-config.md).

Полный пример файла конфигурации можно получить, установив последнюю версию [Microsoft.ApplicationInsights.WindowsServer](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer) пакета. Вот **минимальные** конфигурации для коллекции зависимости, которое эквивалентно значению в примере кода.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryInitializers>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer, Microsoft.AI.DependencyCollector"/>
  </TelemetryInitializers>
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
      <ExcludeComponentCorrelationHttpHeadersOnDomains>
        <Add>core.windows.net</Add>
        <Add>core.chinacloudapi.cn</Add>
        <Add>core.cloudapi.de</Add>
        <Add>core.usgovcloudapi.net</Add>
        <Add>localhost</Add>
        <Add>127.0.0.1</Add>
      </ExcludeComponentCorrelationHttpHeadersOnDomains>
      <IncludeDiagnosticSourceActivities>
        <Add>Microsoft.Azure.ServiceBus</Add>
        <Add>Microsoft.Azure.EventHubs</Add>
      </IncludeDiagnosticSourceActivities>
    </Add>
  </TelemetryModules>
  <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel"/>
</ApplicationInsights>

```

### <a name="configuring-telemetry-collection-from-code"></a>Настройка сбора данных телеметрии из кода

* Во время запуска приложения создайте и настройте `DependencyTrackingTelemetryModule` экземпляра - он должен быть одиночным и должен сохраняться в течение всего времени существования приложения.

```C#
var module = new DependencyTrackingTelemetryModule();

// prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
//...

// enable known dependency tracking, note that in future versions, we will extend this list. 
// please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/NuGet/ApplicationInsights.config.install.xdt#L20
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");
//....

// initialize the module
module.Initialize(configuration);
```

* Добавить общие инициализаторы телеметрии

```C#
// stamps telemetry with correlation identifiers
TelemetryConfiguration.Active.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());

// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
TelemetryConfiguration.Active.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

* Для приложения .NET Framework ОС Windows можно также установить и инициализировать модуль счетчика производительности сборщика, как описано [здесь](http://apmtips.com/blog/2017/02/13/enable-application-insights-live-metrics-from-code/)

#### <a name="full-example"></a>Полный пример

```C#
static void Main(string[] args)
{
    TelemetryConfiguration configuration = TelemetryConfiguration.Active;

    configuration.InstrumentationKey = "removed";
    configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
    configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());

    var telemetryClient = new TelemetryClient();
    using (IntitializeDependencyTracking(configuration))
    {
        telemetryClient.TrackTrace("Hello World!");

        using (var httpClient = new HttpClient())
        {
            // Http dependency is automatically tracked!
            httpClient.GetAsync("https://microsoft.com").Wait();
        }
    }

    // run app...

    // when application stops or you are done with dependency tracking, do not forget to dispose the module
    dependencyTrackingModule.Dispose();

    telemetryClient.Flush();
}

static DependencyTrackingTelemetryModule IntitializeDependencyTracking(TelemetryConfiguration configuration)
{
    // prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
    module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
    module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.chinacloudapi.cn");
    module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.cloudapi.de");    
    module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.usgovcloudapi.net");
    module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("localhost");
    module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("127.0.0.1");

    // enable known dependency tracking, note that in future versions, we will extend this list. 
    // please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/NuGet/ApplicationInsights.config.install.xdt#L20
    module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
    module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");

    // initialize the module
    module.Initialize(configuration);

    return module;
}
```

## <a name="next-steps"></a>Дальнейшие действия
* [Мониторинг зависимостей](app-insights-asp-net-dependencies.md) для просмотра, если REST, SQL или другим внешним ресурсам замедляя вы.
* [Используйте API](app-insights-api-custom-events-metrics.md) для отправки собственных событий и метрик для более четкого представления о производительности и использовании приложения.
