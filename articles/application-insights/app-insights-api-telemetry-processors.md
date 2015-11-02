<properties 
	pageTitle="API Application Insights для пользовательских событий и метрик" 
	description="Вставьте несколько строк кода в устройство или классическое приложение, на веб-страницу или в службу, чтобы отслеживать использование приложения и диагностировать проблемы." 
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
	ms.date="09/23/2015" 
	ms.author="awills"/>

# Обработка данных телеметрии в пакете SDK для Application Insights

*Application Insights находится в состоянии предварительной версии.*

Чтобы настроить способ сбора и обработки данных телеметрии перед их отправкой в службу Application Insights, можно написать и настроить подключаемые модули для пакета SDK Application Insights.

* [Выборка](#sampling) сокращает объем данных телеметрии, не искажая при этом статистические данные. Выборка позволяет хранить вместе связанные точки данных, что упрощает поиск.
* [Обработчики данных телеметрии](#telemetry-processors) инициализируют и фильтруют данные в пакете SDK, а также извлекают образцы таких данных перед их отправкой на сервер. Например, можно добавить вычисляемые свойства к некоторым данным телеметрии или сократить объем данных телеметрии, исключив запросы от программ-роботов. 
* [Инициализаторы телеметрии](#telemetry-initializers) добавляют свойства по умолчанию ко всем отправляемым данным телеметрии и могут некоторым образом переопределять поведение стандартных модулей телеметрии. Свойства вычисляются для каждого вызова отслеживания, поэтому эти значения можно изменять.
* [Инициализаторы контекста](#context-initializers) также задают глобальные свойства. Свойства задаются один раз во время запуска. Их нельзя изменить позднее. Это позволяет сэкономить некоторое время работы ЦП (в отличие от инициализаторов телеметрии), но накладывает жесткие ограничения в работе.
* [API пакета SDK](app-insights-api-custom-events-metrics.md) используется для отправки пользовательских событий и метрик.

Этим разным механизмам присуща некоторая избыточность, и, скорее всего, в будущем в инициализаторы будут внесены изменения.

Перед началом работы:

* Установите пакет [SDK для Application Insights](app-insights-asp-net.md) в своем приложении.   
* Попробуйте использовать [API Application Insights](app-insights-api-custom-events-metrics.md). 


## Выборка

*Эта функция доступна в режиме бета-версии.*

Это рекомендуемый способ сократить трафик при сохранении точной статистики. Фильтр выбирает связанные элементы, позволяя переходить между элементами диагностики. Счетчики событий настраиваются в обозревателе метрик, чтобы компенсировать отфильтрованные элементы.

Для серверов ASP.NET вставьте следующий код в подходящее расположение инициализации, например Application\_Start:

*C#*

```C#

    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    // This configures sampling percentage at 10%:
    TelemetryConfiguration.Active.TelemetryChannel = new TelemetryChannelBuilder().UseSampling(10.0).Build();

```

Для веб-страниц добавьте дополнительную строку во вставленный [фрагмент Application Insights](app-insights-javascript.md) (обычно на главную страницу, например \_Layout.cshtml):

*JavaScript*

```JavaScript

	}({ 

	samplingPercentage: 10.0, 

	instrumentationKey:...
	}); 
```

* Задайте значение в процентах (10 в этих примерах), равное 100/N, где N — целое число, например 50 (=1/2), 33,33 (=1/3), 25 (=1/4), 10 (=1/5).
* Если выборка настроена на веб-странице и сервере, процент выборки и там, и там должен быть одинаковым.
* Клиент и сервер будут координироваться, чтобы выбирать связанные элементы.

[Дополнительная информация о выборке](app-insights-sampling.md).

## Обработчики данных телеметрии

Напишите обработчики данных телеметрии для фильтрации и предварительной обработки данных телеметрии, созданных пакетом SDK для Application Insights, перед их отправкой на внутренний портал. Сюда входят данные телеметрии из стандартных модулей, таких как сборщик запросов HTTP и сборщик зависимостей. Например, можно отфильтровать данные телеметрии о запросах из программ-роботов или успешных вызовах зависимости.

> [AZURE.WARNING]Фильтрация данных телеметрии, отправленных из пакета SDK с помощью обработчиков, может исказить отображаемую на портале статистику и затруднить отслеживание связанных элементов.
> 
> Вместо этого попробуйте использовать [выборку](#sampling).

### Создание обработчика данных телеметрии

1. Чтобы создать фильтр, реализуйте обработчик ITelemetryProcessor. Это еще одна точка расширения, как и модуль телеметрии, инициализатор телеметрии или канал телеметрии. 

    Обратите внимание, что обработчики данных телеметрии создают цепь обработки. При создании экземпляра обработчика данных телеметрии ссылка передается в следующий обработчик в цепочке. Когда точка данных телеметрии передается в метод Process, он выполняет свою работу и затем вызывает следующий обработчик данных телеметрии в цепочке.

    ``` C#

    namespace FilteringTelemetryProcessor
    {
      using Microsoft.ApplicationInsights.Channel;
      using Microsoft.ApplicationInsights.DataContracts;

      class UnauthorizedRequestFilteringProcessor : ITelemetryProcessor
      {
        public UnauthorizedRequestFilteringProcessor(ITelemetryProcessor next)
		//Initialization will fail without this constructor. Link processors to each other
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
        }      private ITelemetryProcessor Next { get; set; }
      }
    }

    ```
2. Вставьте обработчик в цепочку в соответствующем классе инициализации, например AppStart в Global.asax.cs:

    ```C#

    var builder = new TelemetryChannelBuilder();
    builder.Use((next) => new UnauthorizedRequestFilteringProcessor(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    TelemetryConfiguration.Active.TelemetryChannel = builder.Build();

    ```

    Клиенты TelemetryClient, созданные после этой точки, будут использовать обработчики.

### Примеры фильтров

#### Искусственные запросы

Вы можете отфильтровывать программы-роботы и веб-тесты. Хотя обозреватель метрик позволяет отфильтровывать искусственные источники, этот вариант сокращает трафик, фильтруя источники в пакете SDK.

``` C#

public void Process(ITelemetry item)
{
    if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource))
    { return; }

    this.Next.Process(item);
}

```

#### Сбой проверки подлинности

Вы можете отфильтровать запросы с ответом 401.

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


## Инициализаторы телеметрии

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

Описание ненастраиваемых свойств, доступных в telemetryItem, см. в разделе о [модели данных](app-insights-export-data-model.md/#lttelemetrytypegt).

Вы можете добавить любое количество инициализаторов по своему усмотрению.






## <a name="default-properties"></a>Инициализаторы контекста: настройка свойств по умолчанию для всей телеметрии

Можно настроить универсальный инициализатор, чтобы все новые клиенты телеметрии TelemetryClient автоматически использовали ваш контекст. Сюда входит стандартная телеметрия, отправляемая модулями телеметрии платформы, например данные по отслеживанию запросов веб-сервера.

Как правило, идентифицируется телеметрия из разных версий или компонентов приложения. На портале вы можете фильтровать или группировать результаты по свойству "Версия приложения".

В общем случае [рекомендуется использовать инициализаторы телеметрии, а не инициализаторы контекста](http://apmtips.com/blog/2015/06/09/do-not-use-context-initializers/).

#### Определение инициализатора контекста


*C#*

```C#

    using System;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MyNamespace
    {
      // Context initializer class
      public class MyContextInitializer : IContextInitializer
      {
        public void Initialize (TelemetryContext context)
        {
            if (context == null) return;

            context.Component.Version = "v2.1";
        }
      }
    }
```

*Java*

```Java

    import com.microsoft.applicationinsights.extensibility.ContextInitializer;
    import com.microsoft.applicationinsights.telemetry.TelemetryContext;

    public class MyContextInitializer implements ContextInitializer {
      @Override
      public void initialize(TelemetryContext context) {
        context.Component.Version = "2.1";
      }
    }
```

#### Загрузка инициализатора контекста

В ApplicationInsights.config.:

    <ApplicationInsights>
      <ContextInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MyNamespace.MyContextInitializer, MyAssemblyName"/> 
        ...
      </ContextInitializers>
    </ApplicationInsights>

*Другой способ* — создать экземпляр инициализатора в коде.

*C#*

```C#

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.ContextInitializers
        .Add(new MyContextInitializer());
    }
```

*Java*

```Java

    // load the context initializer
    TelemetryConfiguration.getActive().getContextInitializers().add(new MyContextInitializer());
```



## Справочная документация

* [Обзор API](app-insights-api-custom-events-metrics.md)

* [Справочник по ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)
* [Справочник по Java](http://dl.windowsazure.com/applicationinsights/javadoc/)
* [Справочник по JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
* [Android SDK](https://github.com/Microsoft/ApplicationInsights-Android)
* [iOS SDK](https://github.com/Microsoft/ApplicationInsights-iOS)


## Код пакета SDK

* [Базовый пакет SDK для ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [Android SDK](https://github.com/Microsoft/ApplicationInsights-Android)
* [Пакет SDK для Java](https://github.com/Microsoft/ApplicationInsights-Java)
* [Пакет SDK для JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)
* [iOS SDK](https://github.com/Microsoft/ApplicationInsights-iOS)
* [Все платформы](https://github.com/Microsoft?utf8=%E2%9C%93&query=applicationInsights)

## Вопросы

* *Могут ли создаваться исключения при вызовах Track\_()?*
    
    Нет. Вам не нужно помещать их в предложения try-catch. Если пакет SDK сталкивается с проблемами, он добавляет в журнал сообщения, которые отображаются в консоли отладки и, если сообщения проходят, — в диагностическом поиске.



* *Создан ли REST API?*

    Да, но мы его еще не опубликовали.

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

 

<!---HONumber=Oct15_HO4-->