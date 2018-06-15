---
title: Фильтрация и предварительная обработка в пакете SDK для Azure Application Insights | Документация Майкрософт
description: Из этой статьи вы узнаете, как создать обработчики и инициализаторы телеметрии для того, чтобы пакет SDK мог выполнять фильтрацию, и как добавлять свойства к данным перед отправкой данных телеметрии на портал Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 38a9e454-43d5-4dba-a0f0-bd7cd75fb97b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/23/2016
ms.author: mbullwin
ms.openlocfilehash: a8905f4f14b5f4f78e9f3113ec5a655b12599609
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294103"
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Фильтрация и предварительная обработка данных телеметрии в пакете SDK для Application Insights


Чтобы настроить способ сбора и обработки данных телеметрии перед их отправкой в службу Application Insights, можно написать и настроить подключаемые модули для пакета SDK Application Insights.

* [Выборка](app-insights-sampling.md) сокращает объем данных телеметрии, не искажая статистические данные. Благодаря выборке связанные точки данных хранятся вместе, что облегчает навигацию между ними во время диагностики проблемы. На портале общее количество умножается, чтобы компенсировать выборку.
* Фильтрация с использованием обработчиков данных телеметрии для [ASP.NET](#filtering) или [Java](app-insights-java-filter-telemetry.md) позволяет выбирать или изменять данные телеметрии в пакете SDK перед отправкой на сервер. Например, можно уменьшить объем данных телеметрии, исключив запросы от роботов. Но с помощью фильтрации сократить трафик проще, чем при помощи выборки. Хотя фильтрация обеспечивает более жесткий контроль над передаваемыми данными, следует не забывать и о влиянии на статистику (например, когда отфильтровываются все успешные запросы).
* [Инициализаторы телеметрии добавляют свойства](#add-properties) к любым данным телеметрии, отправляемым из вашего приложения, включая данные телеметрии из стандартных модулей. Например, можно добавить вычисляемые значения или номера версий, по которым будут отфильтрованы данные на портале.
* [API пакета SDK](app-insights-api-custom-events-metrics.md) используется для отправки пользовательских событий и показателей.

Перед началом работы:

* Установите [пакет SDK Application Insights для ASP.NET](app-insights-asp-net.md) или [пакет SDK Application Insights для Java](app-insights-java-get-started.md) в свое приложение.

<a name="filtering"></a>

## <a name="filtering-itelemetryprocessor"></a>Фильтрация: ITelemetryProcessor
Такой подход позволяет более тщательно и непосредственно контролировать включение элементов в поток данных телеметрии и исключение из него. Этот метод можно использовать совместно с выборкой или по отдельности.

Для фильтрации данных телеметрии нужно создать обработчик данных телеметрии и зарегистрировать его с помощью пакета SDK. Все данные телеметрии обрабатываются процессором, и можно исключить их из потока или добавить свойства. Сюда входят данные телеметрии из стандартных модулей, таких как сборщик запросов HTTP и сборщик зависимостей, а также данные телеметрии, созданные вами самостоятельно. Например, можно отфильтровать данные телеметрии о запросах из программ-роботов или успешных вызовах зависимости.

> [!WARNING]
> Фильтрация данных телеметрии, отправленных из пакета SDK с помощью обработчиков, может исказить отображаемую на портале статистику и затруднить отслеживание связанных элементов.
>
> Вместо этого попробуйте использовать [выборку](app-insights-sampling.md).
>
>

### <a name="create-a-telemetry-processor-c"></a>Создание обработчика данных телеметрии (C#)
1. Убедитесь, что в проекте используется пакет SDK для Application Insights 2.0.0 или более поздней версии. Щелкните правой кнопкой мыши проект в обозревателе решений Visual Studio и выберите "Управление пакетами NuGet". В диспетчере пакетов NuGet выберите Microsoft.ApplicationInsights.Web.
2. Чтобы создать фильтр, реализуйте обработчик ITelemetryProcessor. Это еще одна точка расширения, как и модуль телеметрии, инициализатор телеметрии или канал телеметрии.

    Обратите внимание, что обработчики данных телеметрии создают цепь обработки. При создании экземпляра обработчика данных телеметрии ссылка передается в следующий обработчик в цепочке. Когда точка данных телеметрии передается в метод Process, он выполняет свою работу и затем вызывает следующий обработчик данных телеметрии в цепочке.

    ``` C#

    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
      {

        private ITelemetryProcessor Next { get; set; }

        // You can pass values from .config
        public string MyParamFromConfigFile { get; set; }

        // Link processors to each other in a chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }
        public void Process(ITelemetry item)
        {
            // To filter out an item, just return
            if (!OKtoSend(item)) { return; }
            // Modify the item if required
            ModifyItem(item);

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }

        // Example: replace with your own modifiers.
        private void ModifyItem (ITelemetry item)
        {
            item.Context.Properties.Add("app-version", "1." + MyParamFromConfigFile);
        }
    }

    ```
1. В файл ApplicationInsights.config вставьте следующее:

```XML

    <TelemetryProcessors>
      <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
         <!-- Set public property -->
         <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
      </Add>
    </TelemetryProcessors>

```

(Это тот же раздел, где инициализируется фильтр выборки.)

Можно передавать строковые значения из файла конфигурации, указав открытые именованные свойства в классе.

> [!WARNING]
> Будьте внимательны и задайте имя типа и имена свойств в файле конфигурации, совпадающие с именами классов и свойств в коде. Если файл конфигурации ссылается на несуществующий тип или свойство, пакет SDK может не суметь отправить данные телеметрии без уведомления.
>
>

**Другой способ** — инициализировать фильтр в коде. Вставьте обработчик в цепочку в соответствующем классе инициализации, например AppStart в Global.asax.cs:

```csharp

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    builder.Use((next) => new SuccessfulDependencyFilter(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

Клиенты TelemetryClient, созданные после этой точки, будут использовать обработчики.

### <a name="example-filters"></a>Примеры фильтров
#### <a name="synthetic-requests"></a>Искусственные запросы
Вы можете отфильтровывать программы-роботы и веб-тесты. Хотя обозреватель метрик позволяет отфильтровывать искусственные источники, этот вариант сокращает трафик, фильтруя источники в пакете SDK.

``` C#

    public void Process(ITelemetry item)
    {
      if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

      // Send everything else:
      this.Next.Process(item);
    }

```

#### <a name="failed-authentication"></a>Сбой проверки подлинности
Отфильтруйте запросы с ответом 401.

```csharp

public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, just terminate the chain:
        return;
    }
    // Send everything else:
    this.Next.Process(item);
}

```

#### <a name="filter-out-fast-remote-dependency-calls"></a>Фильтрация быстрых удаленных вызовов зависимостей
Если требуется диагностировать только вызовы, которые выполняются медленно, можно отфильтровать быстрые вызовы.

> [!NOTE]
> Это приведет к искажению статистических данных, отображаемых на портале. Диаграмма зависимостей будет выглядеть так, как будто все вызовы зависимостей завершились ошибкой.
>
>

``` C#

public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;

    if (request != null && request.Duration.TotalMilliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}

```

#### <a name="diagnose-dependency-issues"></a>Неполадки диагностики зависимостей
[этом блоге](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) описан проект, в котором диагностика неполадок с зависимостями реализована в виде автоматического опрашивания зависимостей.


<a name="add-properties"></a>

## <a name="add-properties-itelemetryinitializer"></a>Добавление свойств: ITelemetryInitializer
Используйте инициализаторы телеметрии, чтобы определить глобальные свойства, которые передаются со всеми данными телеметрии, и переопределить выбранное поведение стандартных модулей телеметрии.

Например, пакет Application Insights for Web собирает телеметрию о HTTP-запросах. По умолчанию любой запрос с кодом ответа > = 400 он помечает как неудавшийся. Если вам нужно, чтобы значение 400 считалось успешным, задайте инициализатор телеметрии, в котором можно настроить свойство Success.

Если задан инициализатор телеметрии, он вызывается всякий раз, когда вызывается любой метод Track*(). Это относится также к модулям, вызываемым модулями стандартной телеметрии. Обычно эти модули не задают свойство, которое уже задал инициализатор.

**Определение инициализатора**

*C#*

```csharp

    using System;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MvcWebRole.Telemetry
    {
      /*
       * Custom TelemetryInitializer that overrides the default SDK
       * behavior of treating response codes >= 400 as failed requests
       *
       */
      public class MyTelemetryInitializer : ITelemetryInitializer
      {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as RequestTelemetry;
            // Is this a TrackRequest() ?
            if (requestTelemetry == null) return;
            int code;
            bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
            if (!parsed) return;
            if (code >= 400 && code < 500)
            {
                // If we set the Success property, the SDK won't change it:
                requestTelemetry.Success = true;
                // Allow us to filter these requests in the portal:
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }
            // else leave the SDK to set the Success property      
        }
      }
    }
```

**Загрузка инициализатора**

В ApplicationInsights.config.:

    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>

*Другой способ* — создать экземпляр инициализатора в коде, например в Global.aspx.cs.

```csharp
    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


[Дополнительную информацию см. здесь.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

<a name="js-initializer"></a>

### <a name="javascript-telemetry-initializers"></a>Инициализаторы телеметрии JavaScript
*JavaScript*

Вставьте инициализатор телеметрии сразу после кода инициализации, полученного на портале:

```JS

    <script type="text/javascript">
        // ... initialization code
        ...({
            instrumentationKey: "your instrumentation key"
        });
        window.appInsights = appInsights;


        // Adding telemetry initializer.
        // This is called whenever a new telemetry item
        // is created.

        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;

                // To check the telemetry item’s type - for example PageView:
                if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                    // this statement removes url from all page view documents
                    telemetryItem.url = "URL CENSORED";
                }

                // To set custom properties:
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["globalProperty"] = "boo";

                // To set custom metrics:
                telemetryItem.measurements = telemetryItem.measurements || {};
                telemetryItem.measurements["globalMetric"] = 100;
            });
        });

        // End of inserted code.

        appInsights.trackPageView();
    </script>
```

Краткое описание ненастраиваемых свойств, доступных в коллекции telemetryItem, см. в разделе [Экспорт модели данных Application Insights](app-insights-export-data-model.md).

Вы можете добавить любое количество инициализаторов по своему усмотрению.

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor и ITelemetryInitializer
Различия между обработчиком и инициализатором данных телеметрии

* Обработчик и инициализатор данных телеметрии можно использовать для выполнения одинаковых заданий, например для добавления свойств в телеметрию.
* Свойство TelemetryInitializers всегда выполняется перед TelemetryProcessors.
* Свойство TelemetryProcessors позволяет полностью заменить или удалить элемент телеметрии.
* Свойство TelemetryProcessors не обрабатывает данные телеметрии счетчика производительности.

## <a name="troubleshooting-applicationinsightsconfig"></a>Устранение неполадок с файлом ApplicationInsights.config
* Убедитесь, что полное имя типа и имя сборки указаны правильно.
* Убедитесь, что файл applicationinsights.config находится в выходном каталоге и содержит все последние изменения.

## <a name="reference-docs"></a>Справочная документация
* [Обзор API](app-insights-api-custom-events-metrics.md)
* [Справочник по ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>Код пакета SDK
* [Базовый пакет SDK для ASP.NET](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [Пакет SDK для ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Пакет SDK для JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next"></a>Дальнейшие действия
* [Поиск в Application Insights](app-insights-diagnostic-search.md)
* [Выборка](app-insights-sampling.md)
* [Устранение неполадок](app-insights-troubleshoot-faq.md)
