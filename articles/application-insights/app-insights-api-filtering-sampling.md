<properties 
	pageTitle="Фильтрация и предварительная обработка в пакете SDK для Application Insights | Microsoft Azure" 
	description="Из этой статьи вы узнаете, как создать обработчики и инициализаторы телеметрии для того, чтобы пакет SDK мог выполнять фильтрацию, и как добавлять свойства к данным перед отправкой данных телеметрии на портал Application Insights." 
	services="application-insights"
    documentationCenter="" 
	authors="beckylino" 
	manager="douge"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="08/30/2016" 
	ms.author="borooji"/>

# Фильтрация и предварительная обработка данных телеметрии в пакете SDK для Application Insights

*Доступна только предварительная версия Application Insights.*

Чтобы настроить способ сбора и обработки данных телеметрии перед их отправкой в службу Application Insights, можно написать и настроить подключаемые модули для пакета SDK Application Insights.

В настоящее время эти функции доступны для пакета SDK ASP.NET.

* [Выборка](app-insights-sampling.md) сокращает объем данных телеметрии, не искажая статистические данные. Благодаря выборке связанные точки данных хранятся вместе, что облегчает навигацию между ними во время диагностики проблемы. На портале общее количество умножается, чтобы компенсировать выборку.
* [Фильтрация с использованием обработчиков данных телеметрии](#filtering) позволяет выбирать или изменять данные телеметрии в пакете SDK перед отправкой на сервер. Например, можно уменьшить объем данных телеметрии, исключив запросы от роботов. Но с помощью фильтрации сократить трафик проще, чем при помощи выборки. Хотя фильтрация обеспечивает более жесткий контроль над передаваемыми данными, следует не забывать и о влиянии на статистику (например, когда отфильтровываются все успешные запросы).
* [Инициализаторы телеметрии добавляют свойства](#add-properties) к любым данным телеметрии, отправляемым из вашего приложения, включая данные телеметрии из стандартных модулей. Например, можно добавить вычисляемые значения или номера версий, по которым будут отфильтрованы данные на портале.
* [API пакета SDK](app-insights-api-custom-events-metrics.md) используется для отправки пользовательских событий и показателей.


Перед началом работы:

* Установите [пакет SDK Application Insights для ASP.NET версии 2](app-insights-asp-net.md) в свое приложение.


<a name="filtering"></a>
## Фильтрация: ITelemetryProcessor

Такой подход позволяет более тщательно и непосредственно контролировать включение элементов в поток данных телеметрии и исключение из него. Этот метод можно использовать совместно с выборкой или по отдельности.

Для фильтрации данных телеметрии нужно создать обработчик данных телеметрии и зарегистрировать его с помощью пакета SDK. Все данные телеметрии обрабатываются процессором, и можно исключить их из потока или добавить свойства. Сюда входят данные телеметрии из стандартных модулей, таких как сборщик запросов HTTP и сборщик зависимостей, а также данные телеметрии, созданные вами самостоятельно. Например, можно отфильтровать данные телеметрии о запросах из программ-роботов или успешных вызовах зависимости.

> [AZURE.WARNING] Фильтрация данных телеметрии, отправленных из пакета SDK с помощью обработчиков, может исказить отображаемую на портале статистику и затруднить отслеживание связанных элементов.
> 
> Вместо этого попробуйте использовать [выборку](app-insights-sampling.md).

### Создание обработчика данных телеметрии

1. Убедитесь, что в проекте используется пакет SDK Application Insights 2.0.0 или более поздней версии. Щелкните правой кнопкой мыши проект в обозревателе решений Visual Studio и выберите "Управление пакетами NuGet". В диспетчере пакетов NuGet выберите Microsoft.ApplicationInsights.Web.

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

(Это тот же раздел, где инициализируется фильтр выборки.)

Можно передавать строковые значения из файла конфигурации, указав открытые именованные свойства в классе.

> [AZURE.WARNING] Будьте внимательны и задайте имя типа и имена свойств в файле конфигурации, совпадающие с именами классов и свойств в коде. Если файл конфигурации ссылается на несуществующий тип или свойство, пакет SDK может не суметь отправить данные телеметрии без уведомления.

 
**Другой способ** — инициализировать фильтр в коде. Вставьте обработчик в цепочку в соответствующем классе инициализации, например AppStart в Global.asax.cs:

```C#

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
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

> [AZURE.NOTE] Это приведет к искажению статистических данных, отображаемых на портале. Диаграмма зависимостей будет выглядеть так, как будто все вызовы зависимостей завершились ошибкой.

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

#### Неполадки диагностики зависимостей

В [этом блоге](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) описан проект, в котором диагностика неполадок с зависимостями реализована в виде автоматического опрашивания зависимостей.


<a name="add-properties"></a>
## Добавление свойств: ITelemetryInitializer

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

Краткое описание ненастраиваемых свойств, доступных в коллекции telemetryItem, см. в разделе о [модели данных](app-insights-export-data-model.md#lttelemetrytypegt).

Вы можете добавить любое количество инициализаторов по своему усмотрению.


## ITelemetryProcessor и ITelemetryInitializer

Различия между обработчиком и инициализатором данных телеметрии

* Обработчик и инициализатор данных телеметрии можно использовать для выполнения одинаковых заданий, например для добавления свойств в телеметрию.
* Свойство TelemetryInitializers всегда выполняется перед TelemetryProcessors.
* Свойство TelemetryProcessors позволяет полностью заменить или удалить элемент телеметрии.
* Свойство TelemetryProcessors не обрабатывает данные телеметрии счетчика производительности.



## Канал сохраняемости 

Если приложение запускается в среде с нестабильным или медленным подключением к Интернету, рекомендуем использовать канал сохраняемости вместо стандартного канала в памяти.

Стандартный канал в памяти теряет все данные телеметрии, которые не были отправлены до закрытия приложения. Хотя можно использовать `Flush()`, чтобы попытаться отправить остающиеся в буфере данные, эти данные все равно теряются, если нет подключения к Интернету или если приложение завершает работу до завершения передачи.

Канал сохраняемости, напротив, копирует данные телеметрии в файл, прежде чем отправить их на портал. `Flush()` гарантирует сохранение данных в файле. Если данные не отправляются до закрытия приложения, они остаются в файле. Данные отправляются при перезапуске приложения, если доступно подключение к Интернету. Данные накапливаются в файле вплоть до установки этого подключения.

### Использование канала сохраняемости

1. Импортируйте пакет NuGet [Microsoft.ApplicationInsights.PersistenceChannel](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PersistenceChannel/1.2.3).
2. Вставьте этот код в приложение в соответствующее расположение для инициализации:
 
    ```C# 

      using Microsoft.ApplicationInsights.Channel;
      using Microsoft.ApplicationInsights.Extensibility;
      ...

      // Set up 
      TelemetryConfiguration.Active.InstrumentationKey = "YOUR INSTRUMENTATION KEY";
 
      TelemetryConfiguration.Active.TelemetryChannel = new PersistenceChannel();
    
    ``` 
3. Используйте вызов `telemetryClient.Flush()` перед закрытием приложения, чтобы обеспечить отправку данных на портал или их сохранение в файле данных.

    Обратите внимание, что метод Flush() является синхронным для постоянного канала и асинхронным для остальных каналов.

 
Канал сохраняемости оптимизирован для устройств, где число событий, созданных приложением, относительно небольшое, а соединение часто ненадежное. Этот канал сначала записывает события на диск в надежном хранилище, а затем пытается отправить их.

#### Пример

Предположим, вам нужно отслеживать необработанные исключения. Подпишитесь на событие `UnhandledException`. Чтобы данные телеметрии сохранялись, добавьте вызов Flush в функцию обратного вызова.
 
```C# 

AppDomain.CurrentDomain.UnhandledException += CurrentDomain_UnhandledException; 
 
... 
 
private void CurrentDomain_UnhandledException(object sender, UnhandledExceptionEventArgs e) 
{ 
    ExceptionTelemetry excTelemetry = new ExceptionTelemetry((Exception)e.ExceptionObject); 
    excTelemetry.SeverityLevel = SeverityLevel.Critical; 
    excTelemetry.HandledAt = ExceptionHandledAt.Unhandled; 
 
    telemetryClient.TrackException(excTelemetry); 
 
    telemetryClient.Flush(); 
} 

``` 

Когда приложение завершит работу, в расположении `%LocalAppData%\Microsoft\ApplicationInsights` появится файл, содержащий сжатые данные событий.
 
При следующем запуске приложения канал выберет этот файл и отправит данные телеметрии в Application Insights, если это возможно.

#### Пример теста

```C#

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            // Send data from the last time the app ran
            System.Threading.Thread.Sleep(5 * 1000);

            // Set up persistence channel

            TelemetryConfiguration.Active.InstrumentationKey = "YOUR KEY";
            TelemetryConfiguration.Active.TelemetryChannel = new PersistenceChannel();

            // Send some data

            var telemetry = new TelemetryClient();

            for (var i = 0; i < 100; i++)
            {
                var e1 = new Microsoft.ApplicationInsights.DataContracts.EventTelemetry("persistenceTest");
                e1.Properties["i"] = "" + i;
                telemetry.TrackEvent(e1);
            }

            // Make sure it's persisted before we close
            telemetry.Flush();
        }
    }
}

```


Код постоянного канала см. на веб-сайте [GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/v1.2.3/src/TelemetryChannels/PersistenceChannel).


## Справочная документация

* [Обзор API](app-insights-api-custom-events-metrics.md)

* [Справочник по ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)


## Код пакета SDK

* [Базовый пакет SDK для ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [Пакет SDK для JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)


## <a name="next"></a>Дальнейшие действия


* [Поиск событий и журналов][diagnostic]
* [Выборка](app-insights-sampling.md)
* [Устранение неполадок][qna]


<!--Link references-->

[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[create]: app-insights-create-new-resource.md
[data]: app-insights-data-retention-privacy.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=AcomDC_0907_2016-->