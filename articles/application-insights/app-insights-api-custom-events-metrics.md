<properties 
	pageTitle="API Application Insights для пользовательских событий и метрик | Microsoft Azure" 
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
	ms.date="06/07/2016" 
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
 * [проект ASP.NET;][greenbrown]
 * [проект Windows;][windows]
 * [проект Java;][java] 
 * [JavaScript на каждой веб-странице.][client]   

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

Здесь "WinGame" — это имя, которое отображается на портале Application Insights.

Чтобы увидеть число событий, откройте колонку [Обозреватель метрик](app-insights-metrics-explorer.md) колонки, добавьте новую диаграмму и выберите "События".

![](./media/app-insights-api-custom-events-metrics/01-custom.png)

Чтобы сравнить количество различных событий, установите тип диаграммы "Сетка" и выполните группировку по имени события:

![](./media/app-insights-api-custom-events-metrics/07-grid.png)


На сетке щелкните имя события, чтобы увидеть отдельные экземпляры данного события.

![Подробно просмотрите события](./media/app-insights-api-custom-events-metrics/03-instances.png)

Щелкните любое из них, чтобы просмотреть подробные данные.

Чтобы сконцентрироваться на определенных событиях в функции поиска или в обозревателе метрик, задайте в фильтре колонок имена событий, которые вас интересуют:

![Откройте "Фильтры", разверните пункт "Имя события" и выберите одно или несколько значений.](./media/app-insights-api-custom-events-metrics/06-filter.png)

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

#### Время просмотра страниц

По умолчанию время, отображаемое как «Время загрузки страницы», отсчитывается от момента отправки запроса браузером до вызова события загрузки страницы в браузере.

Вместо этого вы можете выбрать один из таких вариантов:

* Задайте явную длительность вызова [trackPageView](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#trackpageview).
 * `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);`
* Используйте вызовы времени просмотра страницы `startTrackPage` и `stopTrackPage`.

*JavaScript*

    // To start timing a page:
    appInsights.startTrackPage("Page1");

...

    // To stop timing and log the page:
    appInsights.stopTrackPage("Page1", url, properties, measurements);

Имя, используемое в качестве первого параметра, связывает вызовы start и stop. По умолчанию используется имя текущей страницы.

Полученные длительности загрузки страниц, отображаемые в обозревателе метрик, являются производными интервала между вызовами start и stop. Выбор рассматриваемого интервала за вами.

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



## Очистка данных

Обычно пакет SDK отправляет данные в момент времени выбранный, чтобы свести влияние на пользователя к минимуму. Однако в некоторых случаях может потребоваться очистить буфер, например, при использовании пакета SDK в приложении, которое завершает работу.

*C#*

    telemetry.Flush();

    // Allow some time for flushing before shutdown.
    System.Threading.Thread.Sleep(1000);

Обратите внимание, что эта функция является асинхронной для каналов в памяти и синхронной при использовании [постоянного канала](app-insights-windows-services.md#persistence-channel).


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

Например, в веб-приложении MVC ASP.NET.

*Razor*

        @if (Request.IsAuthenticated)
        {
            <script>
                appInsights.setAuthenticatedUserContext("@User.Identity.Name
                   .Replace("\", "\\")"
                   .replace(/[,;=| ]+/g, "_"));
            </script>
        }

Нет необходимости использовать фактическое учетное имя пользователя. Нужен только уникальный идентификатор этого пользователя. В нем не должно быть пробелов или символов `,;=|`.

Идентификатор пользователя также задается в файле cookie сеанса и отправляется на сервер. Если установлен серверный пакет SDK, идентификатор пользователя, прошедшего аутентификацию, будет отправляться как часть свойств контекста телеметрии клиента и сервера, что позволяет выполнять фильтрацию и поиск.

Если приложение группирует пользователей по учетным записям, вы также можете передать идентификатор учетной записи (с аналогичными ограничениями знаков).


      appInsights.setAuthenticatedUserContext(validatedId, accountId);

В [обозревателе метрик](app-insights-metrics-explorer.md) можно создать диаграмму с количеством **пользователей, прошедших проверку подлинности**, и количеством **учетных записей**.

Кроме того, можно [выполнить поиск][diagnostic] точек данных клиентов с определенными именами пользователей и учетными записями.

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


> [AZURE.NOTE] Постарайтесь не указывать в свойствах личные сведения.

**При использовании метрик** откройте обозреватель метрик и выберите метрику из группы «Пользовательские»:

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

> [AZURE.WARNING] Не используйте один и тот же экземпляр элемента телеметрии (в этом примере `event`) для многократного вызова Track*(). Это может привести к отправке телеметрии с неверной конфигурацией.

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



## <a name="defaults"></a>Свойства по умолчанию для настраиваемой телеметрии

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

**Чтобы добавить свойства для всей телеметрии**, включая данные из модулей стандартной коллекции, [реализуйте `ITelemetryInitializer`](app-insights-api-filtering-sampling.md#add-properties).


## Выборка, фильтрация и обработка данных телеметрии 

Можно написать код для обработки телеметрии перед отправкой из пакета SDK. Будут обрабатываться данные, отправляемые из модулей стандартной телеметрии, таких как коллекция запросов HTTP и коллекция зависимостей.

* [Добавьте свойства](app-insights-api-filtering-sampling.md#add-properties) в телеметрию, реализовав `ITelemetryInitializer`, например добавьте номера версий или значения, вычисленные на основе других свойств. 
* С помощью [фильтрации](app-insights-api-filtering-sampling.md#filtering) можно изменить или отменить телеметрию перед ее отправкой из пакета SDK. Для этого реализуйте `ITelemetryProcesor`. Она позволяет управлять тем, какие данные отправляются, а какие отклоняются. Однако при этом необходимо учитывать влияние на метрики. В зависимости от способа отклонения элементов можно потерять возможность перехода между связанными элементами.
* [Выборки](app-insights-api-filtering-sampling.md#sampling) представляют собой упакованное решение для сокращения объема данных, отправляемых из приложения на портал. Выборка не влияет на отображаемые метрики и возможность диагностировать проблемы путем перехода между связанными элементами, например исключениями, запросами и просмотрами страниц.

[Подробнее](app-insights-api-filtering-sampling.md)


## Отключение телеметрии

Чтобы **динамически остановить и запустить** сбор и передачу данных телеметрии:

*C#*

```C#

    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```

Чтобы **отключить выбранные стандартные сборщики**, например счетчики производительности, HTTP-запросы или зависимости, удалите или закомментируйте соответствующие строки в файле [ApplicationInsights.config][config]. Это можно сделать, если вы, например, хотите отправить собственные данные TrackRequest.

## <a name="debug"></a>Режим разработчика

Во время отладки полезно передавать телеметрию через конвейер, чтобы результаты можно было увидеть немедленно. Кроме того, вы можете получать дополнительные сообщения, которые помогают трассировать любые проблемы с телеметрией. Отключите этот режим в рабочей среде, так как он может замедлить работу приложения.


*C#*
    
    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;

*VB*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True


## <a name="ikey"></a> Установка ключа инструментирования для выбранной пользовательской телеметрии

*C#*
    
    var telemetry = new TelemetryClient();
    telemetry.Context.InstrumentationKey = "---my key---";
    // ...


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


## Класс TelemetryContext

Класс TelemetryClient включает свойство Context, содержащее несколько значений, которые отправляются вместе со всеми данными телеметрии. Как правило, их задают модули стандартной телеметрии, но их также можно задать самостоятельно. Например:

    telemetryClient.Context.Operation.Name = "MyOperationName";

Если задать эти значения самостоятельно, мы советуем удалить соответствующую строку из файла [ApplicationInsights.config][config], чтобы не перепутать собственные значения и стандартные значения.

* **Component** — определяет приложение и его версию.
* **Device** — данные об устройстве, на котором выполняется приложение (в веб-приложениях это сервер или клиентское устройство, с которого отправляется телеметрия).
* **InstrumentationKey** — определяет ресурс Application Insights в Azure, в котором будет отображаться телеметрия. Обычно этот ресурс получается из файла ApplicationInsights.config
* **Location** — определяет географическое расположение устройства.
* **Operation** — текущий HTTP-запрос в веб-приложениях. В приложениях других типов для этого значения можно задать значение "Группировать события совместно".
 * **Id** — созданное значение, которое сопоставляет различные события, чтобы при проверке любого события в колонке «Поиск в журнале диагностики» можно было найти «Связанные элементы».
 * **Name**: идентификатор, обычно URL-адрес HTTP-запроса. 
 * **SyntheticSource** — если эта строка не пустая и не имеет значение null, она указывает, что источник запроса было определен как программа-робот или веб-тест. По умолчанию она будет исключена из вычислений в обозревателе метрик.
* **Properties** — свойства, которые отправляются со всеми данными телеметрии. Это значение можно переопределить в отдельных вызовах отслеживания*.
* **Session** — определяет сеанс пользователя. Для Id задается созданное значение, которое изменяется, если пользователь был неактивным в течение некоторого времени.
* **User**: информация о пользователе. 

## Ограничения


[AZURE.INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

*Как избежать превышения ограничения на скорость передачи данных?*

* Используйте [выборку](app-insights-sampling.md).

*Как долго хранятся данные?*

* См. раздел [Хранение данных и конфиденциальность][data].


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



* *Существует ли REST API для получения данных из портала?*

    Да, его выпуск ожидается вскоре. Пока рекомендуется использовать [непрерывный экспорт](app-insights-export-telemetry.md).

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
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=AcomDC_0615_2016-->