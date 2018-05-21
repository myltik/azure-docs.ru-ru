---
title: Azure Application Insights для консольных приложений | Документация Майкрософт
description: Отслеживайте доступность, производительность и использование веб-приложений.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: lmolkova; mbullwin
ms.openlocfilehash: 679a5d82fbede4d9c464e137d615fc1367522878
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2018
---
# <a name="application-insights-for-net-console-applications"></a>Application Insights для консольных приложений .NET
[Application Insights](app-insights-overview.md) позволяет отслеживать доступность, производительность и использование вашего веб-приложения.

Вам потребуется подписка [Microsoft Azure](http://azure.com). Войдите с помощью учетной записи Майкрософт, которую вы, возможно, уже используете для Windows, XBox Live или других облачных служб Майкрософт. Возможно, у вашей группы есть подписка организации Azure: попросите ее владельца добавить вас к ней с помощью вашей учетной записи Майкрософт.

## <a name="getting-started"></a>Приступая к работе

* На [портале Azure](https://portal.azure.com) [создайте ресурс Application Insights](app-insights-create-new-resource.md). Для параметра типа приложения выберите приложение **Общий**.
* Сделайте копию ключа инструментирования. Найдите ключ в раскрывающемся списке **Основные компоненты** нового ресурса, который вы создали. 
* Установите последнюю версию пакета [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights).
* Прежде чем отслеживать телеметрию, задайте ключ инструментирования в коде или переменную среды APPINSIGHTS_INSTRUMENTATIONKEY. После этого вы сможете отслеживать телеметрию вручную и просматривать данные телеметрии на портале Azure.

```csharp
TelemetryConfiguration.Active.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient();
telemetryClient.TrackTrace("Hello World!");
```

* Установите последнюю версию пакета [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector), который позволяет автоматически отслеживать HTTP-вызовы, SQL-вызовы или другие внешние вызовы зависимостей.

Вы можете инициализировать и настроить Application Insights с помощью кода или файла `ApplicationInsights.config`. Выполните инициализацию как можно раньше. 

> [!NOTE]
> Инструкции для **ApplicationInsights.config** применимы только к приложениям, которые нацелены на платформу .NET Standard, и не подходят для платформы .NET Core. 

### <a name="using-config-file"></a>Использование файла конфигурации

По умолчанию пакет SDK для Application Insights ищет файл `ApplicationInsights.config` в рабочей папке при создании `TelemetryConfiguration`.

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Reads ApplicationInsights.config file if present
```

Вы также можете указать путь к файлу конфигурации.

```csharp
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration("ApplicationInsights.config");
```

Дополнительные сведения см. в [справочных материалах по файлу конфигурации](app-insights-configuration-with-applicationinsights-config.md).

Чтобы получить полный пример файла конфигурации, установите последнюю версию пакета [Microsoft.ApplicationInsights.WindowsServer](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer). В этом руководстве приводится **минимальная** конфигурация для коллекции зависимостей, как в примере кода ниже.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>Your Key</InstrumentationKey>
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

### <a name="configuring-telemetry-collection-from-code"></a>Настройка сбора данных телеметрии с помощью кода

* При запуске приложения создайте и настройте экземпляр `DependencyTrackingTelemetryModule`. Он должен быть одноэлементным и сохраняться в течение всего времени существования приложения.

```csharp
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

* Добавьте общие инициализаторы телеметрии.

```csharp
// stamps telemetry with correlation identifiers
TelemetryConfiguration.Active.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());

// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
TelemetryConfiguration.Active.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

* Для приложения .NET Framework для Windows можно также установить и инициализировать модуль сборщика для счетчика производительности, как описано [здесь](http://apmtips.com/blog/2017/02/13/enable-application-insights-live-metrics-from-code/).

#### <a name="full-example"></a>Полный пример

```csharp
static void Main(string[] args)
{
    TelemetryConfiguration configuration = TelemetryConfiguration.Active;

    configuration.InstrumentationKey = "removed";
    configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
    configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());

    var telemetryClient = new TelemetryClient();
    using (InitializeDependencyTracking(configuration))
    {
        // run app...
        
        telemetryClient.TrackTrace("Hello World!");

        using (var httpClient = new HttpClient())
        {
            // Http dependency is automatically tracked!
            httpClient.GetAsync("https://microsoft.com").Wait();
        }

    }

    // before exit, flush the remaining data
    telemetryClient.Flush();
    
    // flush is not blocking so wait a bit
    Task.Delay(5000).Wait();

}

static DependencyTrackingTelemetryModule InitializeDependencyTracking(TelemetryConfiguration configuration)
{
    var module = new DependencyTrackingTelemetryModule();
    
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

## <a name="next-steps"></a>Дополнительная информация
* [Отслеживайте зависимости](app-insights-asp-net-dependencies.md), чтобы выяснить, что стало причиной медленной работы — REST, SQL или другие внешние ресурсы.
* [Используйте API](app-insights-api-custom-events-metrics.md) для отправки собственных событий и метрик для более четкого представления о производительности и использовании приложения.
