<properties 
	pageTitle="Выборка, фильтрация и предварительная обработка в пакете SDK для Application Insights" 
	description="Напишите плагины, чтобы пакет SDK мог выполнять фильтрацию, выборку данных или добавление свойств к данным перед отправкой данных телеметрии на портал Application Insights." 
	services="application-insights"
    documentationCenter="" 
	authors="alancameronwills" 
	manager="douge"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/04/2015" 
	ms.author="awills"/>

# Выборка, фильтрация и предварительная обработка данных телеметрии в пакете SDK для Application Insights

*Application Insights находится в состоянии предварительной версии.*

Чтобы настроить способ сбора и обработки данных телеметрии перед их отправкой в службу Application Insights, можно написать и настроить подключаемые модули для пакета SDK Application Insights.

В настоящее время эти функции доступны для пакета SDK ASP.NET.

* [Выборка](#sampling) сокращает объем данных телеметрии, не искажая статистические данные. Благодаря выборке связанные точки данных хранятся вместе, что облегчает навигацию между ними во время диагностики проблемы. На портале общее количество умножается, чтобы компенсировать выборку.
 * Выборка с фиксированной частотой позволяет определять процентную долю переданных событий.
 * Адаптивная выборка (используется для пакета SDK ASP.NET версии 2.0.0 бета-версии 3 по умолчанию) автоматически настраивает частоту выборки в соответствии с объемом данных телеметрии. Вы можете задать целевой том.
* [Фильтрация](#filtering) позволяет выбрать или изменить данные телеметрии в пакете SDK перед отправкой на сервер. Например, можно уменьшить объем данных телеметрии, исключив запросы от роботов. Это более простой подход к снижению трафика, нежели выборки. Такой подход обеспечивает более жесткий контроль над передаваемыми данными, однако нужно помнить о том, что это влияет и на статистику: например, если отфильтровывать все успешные запросы.
* [Добавьте свойства](#add-properties) к любым данным телеметрии, отправляемым из вашего приложения, включая данные телеметрии из стандартных модулей. Например, можно добавить вычисляемые значения или номера версий, по которым будут отфильтрованы данные на портале.
* [API пакета SDK](app-insights-api-custom-events-metrics.md) используется для отправки пользовательских событий и показателей.

Перед началом работы:

* Установите [пакет SDK Application Insights](app-insights-start-monitoring-app-health-usage.md) в свое приложение. Вручную установите пакеты NuGet и выберите последнюю *предварительную* версию.
* Попробуйте использовать [API Application Insights](app-insights-api-custom-events-metrics.md). 


## Выборка

*Эта функция доступна в режиме бета-версии.*

[Выборка](app-insights-sampling.md) — рекомендуемый способ сократить трафик при сохранении точной статистики. Фильтр выбирает связанные элементы, позволяя переходить между элементами диагностики. Счетчики событий настраиваются в обозревателе метрик, чтобы компенсировать отфильтрованные элементы.

* Рекомендуется адаптивная выборка. Она автоматически корректирует процент выборки для достижения определенного объема запросов. Сейчас этот модуль доступен только в рамках серверной телеметрии ASP.NET.  
* Также доступна функция выборки с фиксированной частотой. Процент выборки задает пользователь. Модуль доступен для кода веб-приложения ASP.NET и веб-страниц JavaScript. Благодаря тому, что клиент и сервер будут синхронизировать свои выборки, при поиске вы сможете перемещаться между связанными представлениями страниц и запросами.

### Включение выборки

**Обновите пакеты NuGet** до последней *предварительной* версии Application Insights. Для этого в обозревателе решений щелкните проект правой кнопкой мыши, выберите "Управление пакетами NuGet", установите флажок **Включить предварительный выпуск** и выполните поиск Microsoft.ApplicationInsights.Web.

В файле [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) можно настроить максимальную частоту телеметрии, к которой стремится адаптивный алгоритм:

    <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>

### Выборка на стороне клиента

Чтобы получить выборку данных с веб-страниц с фиксированной скоростью, добавьте дополнительную строку во вставленный [фрагмент Application Insights](app-insights-javascript.md) (обычно на главную страницу, например \_Layout.cshtml):

*JavaScript*

```JavaScript

	}({ 

	samplingPercentage: 10.0, 

	instrumentationKey:...
	}); 
```

* Задайте значение в процентах (в данном примере это 10), равное 100/N, где N — целое число, например 50 (=100/2), 33,33 (=100/3), 25 (=100/4) или 10 (=100/10). 
* Если вы включите [выборку с фиксированной частотой](app-insights-sampling.md) и на сервере, выборка будет синхронизироваться на стороне клиента и сервера, поэтому при поиске вы сможете перемещаться между связанными представлениями страниц и запросами.

[Дополнительная информация о выборке](app-insights-sampling.md).

## Фильтрация

Такой подход позволяет более тщательно и непосредственно контролировать включение элементов в поток данных телеметрии и исключение из него. Этот метод можно использовать совместно с выборкой или по отдельности.

Для фильтрации данных телеметрии нужно создать обработчик данных телеметрии и зарегистрировать его с помощью пакета SDK. Все данные телеметрии обрабатываются процессором, и можно исключить их из потока или добавить свойства. Сюда входят данные телеметрии из стандартных модулей, таких как сборщик запросов HTTP и сборщик зависимостей, а также данные телеметрии, созданные вами самостоятельно. Например, можно отфильтровать данные телеметрии о запросах из программ-роботов или успешных вызовах зависимости.

> [AZURE.WARNING]Фильтрация данных телеметрии, отправленных из пакета SDK с помощью обработчиков, может исказить отображаемую на портале статистику и затруднить отслеживание связанных элементов.
> 
> Вместо этого попробуйте использовать [выборку](#sampling).

### Создание обработчика данных телеметрии

1. Обновите пакет SDK для Application Insights до последней версии (2.0.0-beta2 или более поздней версии). Щелкните правой кнопкой мыши проект в обозревателе решений Visual Studio и выберите "Управление пакетами NuGet". В диспетчере пакетов NuGet выберите **Включить предварительный выпуск** и выполните поиск по тексту "Microsoft.ApplicationInsights"

1. Чтобы создать фильтр, реализуйте обработчик ITelemetryProcessor. Это еще одна точка расширения, как и модуль телеметрии, инициализатор телеметрии или канал телеметрии.

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
2. В файл ApplicationInsights.config вставьте следующее: 

```XML

    <TelemetryProcessors>
      <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
         <!-- Set public property -->
         <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
      </Add>
    </TelemetryProcessors>

```

(Обратите внимание, что это тот же раздел, где инициализируется фильтр выборки).

Можно передавать строковые значения из файла конфигурации, указав открытые именованные свойства в классе.

> [AZURE.WARNING]Будьте внимательны и задайте имя типа и имена свойств в файле конфигурации, совпадающие с именами классов и свойств в коде. Если файл конфигурации ссылается на несуществующий тип или свойство, пакет SDK может не суметь отправить данные телеметрии без уведомления.

 
**Другой способ** — инициализировать фильтр в коде. Вставьте обработчик в цепочку в соответствующем классе инициализации, например AppStart в Global.asax.cs:

```C#

    var builder = TelemetryConfiguration.Active.GetTelemetryProcessorChainBuilder();
    builder.Use((next) => new SuccessfulDependencyFilter(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

Клиенты TelemetryClient, созданные после этой точки, будут использовать обработчики.

### Примеры фильтров

#### Искусственные запросы

Вы можете отфильтровывать программы-роботы и веб-тесты. Хотя обозреватель метрик позволяет отфильтровывать искусственные источники, этот вариант сокращает трафик, фильтруя источники в пакете SDK.

``` C#

    public void Process(ITelemetry item)
    {
      if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

      // Send everything else: 
      this.Next.Process(item);
    }

```

#### Сбой проверки подлинности

Отфильтруйте запросы с ответом 401.

```C#

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

#### Фильтрация быстрых удаленных вызовов зависимостей

Если требуется диагностировать только вызовы, которые выполняются медленно, можно отфильтровать быстрые вызовы.

> [AZURE.NOTE]Это приведет к искажению статистических данных, отображаемых на портале. Диаграмма зависимостей будет выглядеть так, как будто все вызовы зависимостей завершились ошибкой.

``` C#

public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;
            
    if (request != null && request.Duration.Milliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}

```


## Добавление свойств

Используйте инициализаторы телеметрии, чтобы определить глобальные свойства, которые передаются со всеми данными телеметрии, и переопределить выбранное поведение стандартных модулей телеметрии.

Например, пакет Application Insights for Web собирает телеметрию о HTTP-запросах. По умолчанию любой запрос с кодом ответа > = 400 он помечает как неудавшийся. Если вам нужно, чтобы значение 400 считалось успешным, задайте инициализатор телеметрии, в котором можно настроить свойство Success.

Если задан инициализатор телеметрии, он вызывается всякий раз, когда вызывается любой метод Track*(). Это относится также к модулям, вызываемым модулями стандартной телеметрии. Обычно эти модули не задают свойство, которое уже задал инициализатор.

**Определение инициализатора**

*C#*

```C#

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

*Другой способ* — создать экземпляр инициализатора в коде, например в Global.aspx.cs.


```C#
    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


[Дополнительную информацию см. здесь.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

<a name="js-initializer"></a>
### Инициализаторы телеметрии JavaScript

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

Краткое описание ненастраиваемых свойств, доступных в коллекции telemetryItem, см. в разделе о [модели данных](app-insights-export-data-model.md/#lttelemetrytypegt).

Вы можете добавить любое количество инициализаторов по своему усмотрению.


## Справочная документация

* [Обзор API](app-insights-api-custom-events-metrics.md)

* [Справочник по ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)


## Код пакета SDK

* [Базовый пакет SDK для ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [Пакет SDK для JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)


## <a name="next"></a>Дальнейшие действия


[Поиск событий и журналов][diagnostic]

[Примеры и пошаговые руководства](app-insights-code-samples.md)

[Устранение неполадок][qna]


<!--Link references-->

[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[create]: app-insights-create-new-resource.md
[data]: app-insights-data-retention-privacy.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=AcomDC_1125_2015-->