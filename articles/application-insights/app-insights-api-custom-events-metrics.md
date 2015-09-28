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
	ms.date="08/28/2015" 
	ms.author="awills"/>

# API Application Insights для пользовательских событий и метрик 

*Доступна только предварительная версия Application Insights.*

Вставьте несколько строк кода в свое приложение, чтобы узнать, как пользователи его используют, или чтобы диагностировать неполадки. Вы можете отправлять телеметрию из устройств и классических приложений, веб-клиентов и веб-серверов.

Сборщики данных Application Insights используют этот API для отправки стандартной телеметрии, такой как отчеты о количестве просмотров страницы и отчеты об исключениях, но его также можно использовать для отправки пользовательской телеметрии.

## Сводные данные API

Этот API используется на всех платформах, кроме некоторых небольших исключений.

Метод | Область использования
---|---
[`TrackPageView`](#page-views) | Страницы, экраны, колонки или формы
[`TrackEvent`](#track-event) | Действия пользователя и другие события. Используется для отслеживания поведения пользователя или мониторинга производительности.
[`TrackMetric`](#track-metric) | Измерения производительности, не связанные с конкретными событиями, например измерение длины очереди
[`TrackException`](#track-exception)|Регистрация исключений для диагностики. Отслеживайте исключения, связанные с другими событиями, и изучайте трассировку стека.
[`TrackRequest`](#track-request)| Регистрация частоты и длительности запросов к серверу для анализа производительности.
[`TrackTrace`](#track-trace)|Сообщения журналов диагностики. Можно также использовать журналы сторонних приложений.
[`TrackDependency`](#track-dependency)|Регистрация длительности и частоты вызовов внешних компонентов, от которых зависит приложение.

Вы можете [прикрепить свойства и метрики](#properties) к большинству этих вызовов телеметрии.


## <a name="prep"></a>Перед началом работы

Если вы еще не сделали этого, понадобится следующее.

* Добавьте пакет SDK Application Insights в свой проект:
 * [проект ASP.NET][greenbrown];
 * [проект Windows][windows];
 * [проект Java][java]; 
 * [JavaScript на каждой веб-странице][client].   

* В программный код устройства или веб-сервера необходимо добавить:

    *C#:* `using Microsoft.ApplicationInsights;`

    *VB:* `Imports Microsoft.ApplicationInsights`

    *Java:* `import com.microsoft.applicationinsights.TelemetryClient;`

## Создание класса TelemetryClient

Создайте экземпляр TelemetryClient (за исключением JavaScript на веб-страницах):

*C#:*

    private TelemetryClient telemetry = new TelemetryClient();

*VB:*

    Private Dim telemetry As New TelemetryClient

*Java*

    private TelemetryClient telemetry = new TelemetryClient();

Класс TelemetryClient является потокобезопасным.

Рекомендуем использовать экземпляр `TelemetryClient` для каждого модуля приложения. Например, у вас может быть один экземпляр `TelemetryClient` в веб-службе для отчетов о входящих HTTP-запросах и один в среднем классе для отчетов о событиях бизнес-логики. Можно задать свойства, например `TelemetryClient.Context.User.Id` для отслеживания пользователей и сеансов или `TelemetryClient.Context.Device.Id` для идентификации компьютера. Эти данные прикрепляются ко всем событиям, отправляемым экземпляром.


## Отслеживание событий

В Application Insights *пользовательское событие* — это точка данных, которую можно отобразить как суммарное значение в [обозревателе метрик][metrics], а также как отдельные значения на вкладке [Поиск по журналу диагностики][diagnostic]. (Оно не связано с MVC и другими "событиями" платформы.)

Вставьте вызовы TrackEvent в код, чтобы вычислить, как часто пользователи выбирают определенный компонент, как часто они достигают определенной цели или как часто возникают те или иные ошибки.

Например, отправляйте событие в игровом приложении при каждом выигрыше пользователя:

*JavaScript*

    appInsights.trackEvent("WinGame");

*C#*
    
    telemetry.TrackEvent("WinGame");

*VB*


    telemetry.TrackEvent("WinGame")

*Java*

    telemetry.trackEvent("WinGame");

Здесь "WinGame" — это имя, которое отображается на портале Application Insights. Щелкните плитку "Пользовательские события" в колонке "Обзор":

![Перейдите к своему ресурсу приложения на сайте portal.azure.com](./media/app-insights-api-custom-events-metrics/01-custom.png)


Данные диаграммы сгруппированы с использованием пункта "Имя события", чтобы вы могли увидеть относительный вклад наиболее значимых событий. Чтобы изменить это, выберите диаграмму и воспользуйтесь элементом управления "Группирование".

![Выделите диаграмму и установите группирование](./media/app-insights-api-custom-events-metrics/02-segment.png)

Выберите имя события в списке под диаграммой. Щелкните его, чтобы увидеть отдельные вхождения события.

![Подробно просмотрите события](./media/app-insights-api-custom-events-metrics/03-instances.png)

Щелкните любое из них, чтобы просмотреть подробные данные.

## <a name="properties"></a>Фильтрация, поиск и сегментация данных с помощью свойств

Вы можете прикрепить свойства и результаты измерений к своим событиям, а также метрикам, просмотрам страниц, исключениям и другим данным телеметрии.

**Свойства** — это строковые значения, которые можно использовать для фильтрации телеметрии в отчетах об использовании. Например, если приложение содержит несколько игр, имеет смысл к каждому событию присоединять имя игры, чтобы видеть, какие игры наиболее популярны.

В каждой строке может отображаться до 1000 событий. (Если вы хотите отправлять большие блоки данных, используйте параметр сообщения [TrackTrace](#track-trace).)

**Метрики** являются числовыми значениями, которые могут быть представлены в графическом виде. Например, вам может потребоваться увидеть, постепенно ли увеличиваются зарабатываемые игроками очки. Графы можно сегментировать по свойствам, отправленным с событием, чтобы получать отдельные графы или набор графов для разных игр.

Чтобы значения метрик верно отображались, они должны быть >= 0.


Количество используемых [свойств, значений свойств и метрик ограничено](#limits).


*JavaScript*

    appInsights.trackEvent
      ("WinGame",
         // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric metrics:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );

    appInsights.trackPageView
        ("page name", "http://fabrikam.com/pageurl.html",
          // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric metrics:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );
          

*C#*

    // Set up some properties and metrics:
    var properties = new Dictionary <string, string> 
       {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var metrics = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics);


*VB*

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim metrics = New Dictionary (Of String, Double)
    metrics.Add("Score", currentGame.Score)
    metrics.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics)


*Java*
    
    Map<String, String> properties = new HashMap<String, String>();
    properties.put("game", currentGame.getName());
    properties.put("difficulty", currentGame.getDifficulty());
    
    Map<String, Double> metrics = new HashMap<String, Double>();
    metrics.put("Score", currentGame.getScore());
    metrics.put("Opponents", currentGame.getOpponentCount());
    
    telemetry.trackEvent("WinGame", properties, metrics);


> [AZURE.NOTE]Постарайтесь не указывать в свойствах личные сведения.

**При использовании метрик** откройте обозреватель метрик и выберите метрику из группы "Пользовательские":

![Откройте обозреватель метрик, выделите диаграмму и выберите метрику](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

*Если ваша метрика не отображается или заголовок "Пользовательские" отсутствует, закройте колонку выбранной метрики и повторите попытку позже. Иногда для вычисления метрик в конвейере может потребоваться час.*

**При использовании свойств и метрик** сегментируйте метрики по свойствам:


![Задайте группирование, а затем выберите свойство в списке "Группировать по"](./media/app-insights-api-custom-events-metrics/04-segment-metric-event.png)



**В колонке поиска по журналу диагностики** можно просматривать свойства и метрики, щелкая отдельные вхождения события.


![Выберите экземпляр, а затем выберите "..."](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-4.png)


Используйте поле поиска для просмотра вхождений события с определенным значением свойства.


![Введите слово в поле поиска](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-5.png)

[Дополнительная информация о выражениях поиска][diagnostic].

#### Альтернативный способ настройки свойств и метрик

Для удобства вы можете собирать параметры события в отдельный объект:

    var event = new EventTelemetry();

    event.Name = "WinGame";
    event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
    event.Properties["game"] = currentGame.Name;
    event.Properties["difficulty"] = currentGame.Difficulty;
    event.Metrics["Score"] = currentGame.Score;
    event.Metrics["Opponents"] = currentGame.Opponents.Length;

    telemetry.TrackEvent(event);



#### <a name="timed"></a> События времени

Иногда требуется отобразить на диаграмме продолжительность выполнения некоторых действий. Например, может понадобиться определить, сколько времени требуется пользователю для выбора решения в игре. Это полезный пример использования параметра измерения.


*C#*

    var stopwatch = System.Diagnostics.Stopwatch.StartNew();

    // ... perform the timed action ...

    stopwatch.Stop();

    var metrics = new Dictionary <string, double>
       {{"processingTime", stopwatch.Elapsed.TotalMilliseconds}};

    // Set up some properties:
    var properties = new Dictionary <string, string> 
       {{"signalSource", currentSignalSource.Name}};

    // Send the event:
    telemetry.TrackEvent("SignalProcessed", properties, metrics);



## Отслеживание метрик

Используйте TrackMetric для отправки метрик, которые не привязаны к определенным событиям. Например, можно отслеживать длину очереди через регулярные промежутки времени.

Метрики отображаются в качестве статистических графиков в обозревателе метрик, но в отличие от событий, невозможно выполнить поиск отдельных вхождений в поиске в журнале диагностики.

Чтобы значения метрик верно отображались, они должны быть >= 0.


*JavaScript*

    appInsights.trackMetric("Queue", queue.Length);

*C#*

    telemetry.TrackMetric("Queue", queue.Length);

*VB*

    telemetry.TrackMetric("Queue", queue.Length)

*Java*

    telemetry.trackMetric("Queue", queue.Length);

На самом деле это можно выполнить в фоновом потоке:

*C#*

    private void Run() {
     var appInsights = new TelemetryClient();
     while (true) {
      Thread.Sleep(60000);
      appInsights.TrackMetric("Queue", queue.Length);
     }
    }


Чтобы увидеть результаты, откройте обозреватель метрик и добавьте новую диаграмму. Настройте ее для отображения своей метрики.

![Добавьте новую диаграмму или выберите существующую, а в поле "Пользовательские" выберите свою метрику](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

Количество используемых [метрик ограничено](#limits).

## Просмотры страниц

В устройстве или приложении веб-страницы телеметрия просмотров страницы отображается по умолчанию при загрузке каждого экрана или каждой страницы. Однако это можно изменить, чтобы отслеживать количество просмотров страницы в дополнительное или другое время. Например, в приложении, которое отображает вкладки или колонки, может потребоваться отслеживать страницу каждый раз, когда пользователь открывает новую колонку.

![Область "Использование" в колонке "Обзор"](./media/app-insights-api-custom-events-metrics/appinsights-47usage-2.png)

Данные о пользователе и сеансе отправляются в качестве свойств вместе с количеством просмотров страниц, поэтому диаграммы для пользователей и сеансов активируются при наличии телеметрии по количеству просмотров страницы.

#### Пользовательские данные о просмотре страницы

*JavaScript*

    appInsights.trackPageView("tab1");

*C#*

    telemetry.TrackPageView("GameReviewPage");

*VB*

    telemetry.TrackPageView("GameReviewPage")


Если у вас есть несколько вкладок на различных HTML-страницах, можно также указать URL-адрес:

    appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");



## Отслеживание запросов

Используется пакетом SDK для сервера для регистрации HTTP-запросов.

Его можно также вызвать самостоятельно, чтобы смодулировать запросы в контексте, в котором отсутствует выполняющийся модуль веб-службы.

*C#*

    // At start of processing this request:

    // Operation Id and Name are attached to all telemetry and help you identify
    // telemetry associated with one request:
    telemetry.Context.Operation.Id = Guid.NewGuid().ToString();
    telemetry.Context.Operation.Name = requestName;
    
    var stopwatch = System.Diagnostics.Stopwatch.StartNew();

    // ... process the request ...

    stopwatch.Stop();
    telemetryClient.TrackRequest(requestName, DateTime.Now,
       stopwatch.Elapsed, 
       "200", true);  // Response code, success



## Отслеживание исключений

Отправляйте исключения в Application Insights, чтобы [вычислить их количество][metrics], что может указывать на частоту возникновения проблемы, и чтобы[проверить отдельные вхождения][diagnostic]. Отчеты содержат данные по трассировкам стеков.

*C#*

    try
    {
        ...
    }
    catch (Exception ex)
    {
       telemetry.TrackException(ex);
    }

*JavaScript*

    try
    {
       ...
    }
    catch (ex)
    {
       appInsights.trackException(ex);
    }

Пакеты SDK перехватывают многие исключения автоматически, поэтому не всегда нужно явно вызвать метод TrackException.

* ASP.NET: [написание кода для перехвата исключений](app-insights-asp-net-exceptions.md)
* J2EE: [исключения перехватываются автоматически](app-insights-java-get-started.md#exceptions-and-request-failures)
* Приложения Windows: [сбои перехватываются автоматически](app-insights-windows-crashes.md)
* JavaScript: перехватываются автоматически. Если вы хотите отключить автоматический сбор, добавьте строку в фрагмент кода, который вставляется на веб-страницы:

    ```
    ({
      instrumentationKey: "your key"
      , disableExceptionTracking: true
    })
    ```


## Отслеживание трассировки 

Используйте его для диагностики неполадок, отправляя навигационную цепочку в Application Insights. Вы можете отправлять блоки диагностических данных и проверять их в колонке [Поиск по журналу диагностики][diagnostic].

 

[Адаптеры журнала][trace] используют этот API для отправки журналов сторонних производителей на портал.


*C#*

    telemetry.TrackTrace(message, SeverityLevel.Warning, properties);

Ограничения по размеру `message` гораздо выше, чем ограничение для свойств. Вы можете выполнять поиск содержимого сообщения, но (в отличие от значений свойств) не можете фильтровать его.

## Отслеживание зависимостей

Используйте этот вызов для отслеживания времени отклика и процента успешных попыток вызовов во внешнюю часть кода. Результаты отображаются в диаграммах зависимостей на портале.

```C#

            var success = false;
            var startTime = DateTime.UtcNow;
            var timer = System.Diagnostics.Stopwatch.StartNew();
            try
            {
                success = dependency.Call();
            }
            finally
            {
                timer.Stop();
                telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
            }
```

Помните, что серверные пакеты SDK включают [модуль зависимостей](app-insights-dependencies.md), который автоматически обнаруживает и отслеживает определенные вызовы зависимостей, например вызовы к базам данных и API REST. Для работы модуля необходимо установить агент на сервере. Используйте этот вызов, если хотите отслеживать вызовы, которые не отслеживаются автоматически, или если не хотите устанавливать агент.

Чтобы отключить стандартный модуль отслеживания зависимостей, измените [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) и удалите ссылку на `DependencyCollector.DependencyTrackingTelemetryModule`.


## Прошедшие проверку пользователи

В веб-приложении пользователи по умолчанию идентифицируются файлом cookie. Пользователь может быть учтен более одного раза при доступе к приложению с другого компьютера или браузера либо при удалении файлов cookie.

Но если пользователи входят в ваше приложение, можно узнать их более точное количество, указав идентификатор пользователя, прошедшего аутентификацию, в коде браузера:

*JavaScript*

```JS
    // Called when my app has identified the user.
    function Authenticated(signInId) {
      var validatedId = signInId.replace(/[,;=| ]+/g, "_");
      appInsights.setAuthenticatedUserContext(validatedId);
      ...
    }
```

Нет необходимости использовать фактическое учетное имя пользователя. Нужен только уникальный идентификатор этого пользователя. Он не должен содержать пробелы или какой-либо из этих знаков: `,;=|`.

Идентификатор пользователя также задается в файле cookie сеанса и отправляется на сервер. Если установлен серверный пакет SDK, идентификатор пользователя, прошедшего аутентификацию, будет отправляться как часть свойств контекста телеметрии клиента и сервера, что позволяет выполнять фильтрацию и поиск.

Если приложение группирует пользователей по учетным записям, вы также можете передать идентификатор учетной записи (с аналогичными ограничениями знаков).


      appInsights.setAuthenticatedUserContext(validatedId, accountId);

В [обозревателе метрик](app-insights-metrics-explorer.md) можно создать диаграмму **пользователей, прошедших проверку подлинности**, и **учетных записей**.


## <a name="defaults"></a>Установка значений по умолчанию для выбранной пользовательской телеметрии

Если требуется задать значения свойств по умолчанию для некоторых создаваемых пользовательских событий, это можно сделать в классе TelemetryClient. Они прикреплены к каждому элементу телеметрии, отправляемой из этого клиента.

*C#*

    using Microsoft.ApplicationInsights.DataContracts;

    var gameTelemetry = new TelemetryClient();
    gameTelemetry.Context.Properties["Game"] = currentGame.Name;
    // Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame");
    
*VB*

    Dim gameTelemetry = New TelemetryClient()
    gameTelemetry.Context.Properties("Game") = currentGame.Name
    ' Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame")

*Java*

    import com.microsoft.applicationinsights.TelemetryClient;
    import com.microsoft.applicationinsights.TelemetryContext;
    ...


    TelemetryClient gameTelemetry = new TelemetryClient();
    TelemetryContext context = gameTelemetry.getContext();
    context.getProperties().put("Game", currentGame.Name);
    
    gameTelemetry.TrackEvent("WinGame");


    
Вызовы отдельных данных телеметрии могут переопределить значения по умолчанию в словарях свойства.

**Для веб-клиентов JavaScript** [используйте инициализаторы телеметрии JavaScript](#js-initializer).


## <a name="ikey"></a> Установка ключа инструментирования для выбранной пользовательской телеметрии

*C#*
    
    var telemetry = new TelemetryClient();
    telemetry.Context.InstrumentationKey = "---my key---";
    // ...



## Инициализаторы телеметрии

Используйте инициализаторы телеметрии, чтобы определить глобальные свойства, которые передаются со всеми данными телеметрии, и переопределить выбранное поведение стандартных модулей телеметрии.

Например, пакет Application Insights for Web собирает телеметрию о HTTP-запросах. По умолчанию любой запрос с кодом ответа > = 400 он помечает как неудавшийся. Если вам нужно, чтобы значение 400 считалось успешным, задайте инициализатор телеметрии, в котором можно настроить свойство "Успех".

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

Описание не настраиваемых свойств, доступных в telemetryItem, см. в разделе о [модели данных](app-insights-export-data-model.md/#lttelemetrytypegt).

Вы можете добавить любое количество инициализаторов по своему усмотрению.

## <a name="dynamic-ikey"></a> Динамический ключ инструментирования

Во избежание смешивания телеметрии из среды разработки, тестовой и рабочей среды вы можете [создавать отдельные ресурсы Application Insights][create] и менять их ключи в зависимости от среды.

Вместо получения ключа инструментирования из файла конфигурации можно задать его в коде. Задайте ключ в методе инициализации, таком как global.aspx.cs, в службе ASP.NET:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      ...

*JavaScript*

    appInsights.config.instrumentationKey = myKey; 



На веб-странице может потребоваться задать его, используя состояние веб-сервера, а не внедрить его в сценарий. Например, на веб-странице, созданной в приложении ASP.NET:

*JavaScript в Razor*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      @Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...



## Очистка данных

Обычно пакет SDK отправляет данные в момент времени выбранный, чтобы свести влияние на пользователя к минимуму. Однако в некоторых случаях может потребоваться очистить буфер, например, при использовании пакета SDK в приложении, которое завершает работу.

*C#*

    telemetry.Flush();

Обратите внимание, что функция является синхронной.



## Отключение стандартной телеметрии

Вы можете [отключить отдельные части стандартной телеметрии][config], изменив файл `ApplicationInsights.config`. Это можно сделать, если вы, например, хотите отправить собственные данные TrackRequest.

[Подробнее][config].


## <a name="debug"></a>Режим разработчика

Во время отладки полезно передавать телеметрию через конвейер, чтобы результаты можно было увидеть немедленно. Кроме того, вы можете получать дополнительные сообщения, которые помогают трассировать любые проблемы с телеметрией. Отключите этот режим в рабочей среде, так как он может замедлить работу приложения.


*C#*
    
    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;

*VB*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True

## Класс TelemetryContext

Класс TelemetryClient включает свойство Context, содержащее несколько значений, которые отправляются вместе со всеми данными телеметрии. Как правило, их задают модули стандартной телеметрии, но их также можно задать самостоятельно. Например:

    telemetryClient.Context.Operation.Name = “MyOperationName”;

Если задать эти значения самостоятельно, мы советуем удалить соответствующую строку из файла [ApplicationInsights.config][config], чтобы не перепутать собственные значения и стандартные значения.

* **Component** — определяет приложение и его версию.
* **Device** — данные об устройстве, на котором выполняется приложение (в веб-приложениях это сервер или клиентское устройство, с которого отправляется телеметрия).
* **InstrumentationKey** — определяет ресурс Application Insights в Azure, в котором будет отображаться телеметрия. Обычно этот ресурс получается из файла ApplicationInsights.config
* **Location** — определяет географическое расположение устройства.
* **Operation** — текущий HTTP-запрос в веб-приложениях. В приложениях других типов для этого значения можно задать значение "Группировать события совместно".
 * **Id** — созданное значение, которое сопоставляет различные события, чтобы при проверке любого события в колонке "Поиск в журнале диагностики" можно было найти "Связанные элементы".
 * **Name**: идентификатор, обычно URL-адрес HTTP-запроса. 
 * **SyntheticSource** — если эта строка не пустая и не имеет значение null, она указывает, что источник запроса было определен как программа-робот или веб-тест. По умолчанию она будет исключена из вычислений в обозревателе метрик.
* **Properties** — свойства, которые отправляются со всеми данными телеметрии. Это значение можно переопределить в отдельных вызовах отслеживания*.
* **Session** — определяет сеанс пользователя. Для Id задается созданное значение, которое изменяется, если пользователь был неактивным в течение некоторого времени.
* **User**: данные пользователя. 


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




## Ограничения

Количество метрик и событий, используемых в приложении, ограничено.

1. Максимальное количество точек данных телеметрии для каждого ключа инструментирования (т. е. для каждого приложения) — 500. К этим данным относится стандартная телеметрия, отправляемая модулями пакета SDK, а также пользовательские события, метрики и другая телеметрия, отправленная в коде.
1.	Максимальное количество уникальных имен метрик — 200, а уникальных имен свойств — 200. Метрики включают данные, отправленные в TrackMetric, а также измерения по другим типам данных, таким как события. Имена метрик и свойств являются глобальными для каждого ключа инструментирования и не ограничиваются типом данных.
2.	Свойства можно использовать для фильтрации и группирования, только если на каждое свойство приходится менее 100 уникальных значений. После превышения максимального количества уникальных значений (100 уникальных значений), свойство можно по-прежнему использовать для поиска и фильтрации, но не для фильтров.
3.	Максимальное количество уникальных значений для стандартных свойств, таких как Request Name или Page URL, в неделю — 1000. После превышения максимального количества уникальных значений (1000 уникальных значений) дополнительные значения отмечаются как "Другие значения". Исходное значение можно по-прежнему использовать для полнотекстового поиска и фильтрации.

* *Вопрос. Как долго хранятся данные?*

    См. раздел [Хранение данных и конфиденциальность][data].


## Справочная документация

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

 

<!---HONumber=Sept15_HO3-->