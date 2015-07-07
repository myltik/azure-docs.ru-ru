<properties 
	pageTitle="Обнаружение и диагностика ошибок и исключений в веб-приложениях" 
	description="Обнаружение и диагностика ошибок и исключений в веб-приложениях" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="awills"/>
 
# Диагностика ошибок и исключений в веб-приложениях с помощью Application Insights

[Visual Studio Application Insights][start] представляет собой мощный инструмент для обнаружения и диагностики ошибок в приложениях. В этой статье мы сосредоточим внимание на веб-приложениях (Application Insights можно использовать на [различных других платформах][platforms]).

## Настройка приложения с помощью Application Insights

Добавьте пакет SDK в проект своего приложения. После выпуска и выполнения приложения пакет SDK будет отправлять телеметрию о его производительности на портал Application Insights.

* [Добавление Application Insights в приложения ASP.NET][greenbrown]
* [Добавление Application Insights в приложения Java][java]

Если веб-страницы содержат существенные сценарии, возможно, вам также потребуется *[добавить Application Insights на веб-страницы][track].


## Поиск по журналу диагностики



Откройте колонку «Поиск по журналу диагностики», чтобы просмотреть телеметрию, отправляемую пакетом SDK автоматически.

![](./media/app-insights-web-failures-exceptions/appinsights-45diagnostic.png)

![](./media/app-insights-web-failures-exceptions/appinsights-31search.png)

Подробная информация зависит от типа приложения. Вы можете щелкнуть любое отдельное событие, чтобы просмотреть подробную информацию о нем.

##<a name="events"></a>Пользовательские события

Пользовательские события отображаются в колонке [Поиск по журналу диагностики][diagnostic] и [обозревателе метрик][metrics]. Их можно отправлять с устройств, веб-страниц и серверных приложений. Они могут использоваться для диагностики, а также для [понимания закономерностей использования][track].

Пользовательское событие имеет имя и может содержать свойства и числовые значения, которые можно отфильтровать.

JavaScript на стороне клиента

    appInsights.trackEvent("WinGame",
         // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric measurements:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );

C#на стороне сервера

    // Set up some properties:
    var properties = new Dictionary <string, string> 
       {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements);


VB на стороне сервера

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Score", currentGame.Score)
    measurements.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements)

### Запустите приложение и просмотрите результаты.

Откройте колонку «Поиск по журналу диагностики».

Выберите пользовательское событие и имя конкретного события.

![](./media/app-insights-web-failures-exceptions/appinsights-332filterCustom.png)


Отфильтруйте данные дополнительно, введя условие поиска для значения свойства.

![](./media/app-insights-web-failures-exceptions/appinsights-23-customevents-5.png)

Детализируйте отдельное событие, чтобы просмотреть дополнительные свойства.

![](./media/app-insights-web-failures-exceptions/appinsights-23-customevents-4.png)



##<a name="trace"></a>Телеметрия трассировки

Телеметрии трассировки — это код, который внедряется специально для создания журналов диагностики.

Например, можно вставить следующие вызовы:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");


####  Установка адаптера для вашей платформы ведения журнала

Можно также выполнять поиск журналов, созданных с помощью платформы ведения журналов, такой как log4Net, NLog или System.Diagnostics.Trace.

1. Если вы планируете использовать log4Net или NLog, установите его в свой проект. 
2. В обозревателе решений щелкните правой кнопкой мыши ваш проект и выберите **Управление пакетами NuGet**.
3. Выберите Online > All, выберите **Include Prerelease** и выполните поиск по тексту «Microsoft.ApplicationInsights»

    ![Получите предварительную версию соответствующего адаптера](./media/app-insights-web-failures-exceptions/appinsights-36nuget.png)

4. Выберите соответствующий пакет из списка.
  + Microsoft.ApplicationInsights.TraceListener (для захвата вызовов System.Diagnostics.Trace)
  + Microsoft.ApplicationInsights.NLogTarget
  + Microsoft.ApplicationInsights.Log4NetAppender

Пакет NuGet устанавливает необходимые сборки, а также вносит изменения в файл web.config или app.config.

#### <a name="pepper"></a>Вставка вызовов журнала диагностики

При использовании System.Diagnostics.Trace типичный вызов будет выглядеть следующим образом:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

если вы предпочитаете log4net или NLog:

    logger.Warn("Slow response - database01");

Запустите приложение в режиме отладки или разверните его.

При выборе фильтра «Трассировка» в колонке «Поиск по журналу диагностики» отобразятся сообщения.

### <a name="exceptions"></a>Исключения

Отчеты об исключениях в Application Insights очень эффективны, особенно потому, что вы можете переключаться между неудачно завершенными запросами и исключениями, а также считывать стек исключений.

Вы также можете написать код для отправки телеметрии исключений:

JavaScript

    try 
    { ...
    }
    catch (ex)
    {
      appInsights.TrackException(ex, "handler loc",
        {Game: currentGame.Name, 
         State: currentGame.State.ToString()});
    }

C\#

    var telemetry = new TelemetryClient();
    ...
    try 
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string> 
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }

VB

    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)
  
      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try

Свойства и значения параметров не являются обязательными, но они полезны для фильтрации и добавления дополнительной информации. Например, если у вас есть приложение, которое запускает несколько игр, вы можете просматривать все отчеты об исключениях для каждой конкретной игры. Вы можете добавить в каждый словарь столько элементов, сколько вам нужно.

#### Просмотр исключений

Сводка по исключениям отображается в колонке «Обзор», которую можно щелкнуть, чтобы просмотреть дополнительную информацию. Например:


![](./media/app-insights-web-failures-exceptions/appinsights-039-1exceptions.png)

Щелкните любой тип исключения, чтобы просмотреть конкретные вхождения:

![](./media/app-insights-web-failures-exceptions/appinsights-333facets.png)

Колонку «Поиск по журналу диагностики» можно также открыть напрямую, отфильтровать исключения и выбрать соответствующий тип отображаемых исключений.

### Уведомление о необработанных исключениях

Application Insights сообщает о необработанных исключениях, случающихся в поддерживаемых устройствах, [веб-браузерах][usage] или веб-серверах, которыми управляет [монитор состояний][redfield] или [пакет SDK для Application Insights][greenbrown].

> [AZURE.NOTE]При включении файлов сценария из сетей CDN или других доменов в веб-браузере убедитесь, что сценарий содержит атрибут ```crossorigin="anonymous"``` и сервер отправляет заголовки CORS для получения трассировки стека и информации для необработанных исключений Javascript из этих ресурсов.

Но в некоторых случаях служба не может этого сделать, так как платформа .NET перехватывает эти исключения. Таким образом, чтобы убедиться, что отображаются все исключения, необходимо написать небольшой обработчик исключений. Самая подходящая процедура зависит от используемой технологии. Дополнительную информацию см. в [этом блоге](http://blogs.msdn.com/b/visualstudioalm/archive/2014/12/12/application-insights-exception-telemetry.aspx).

### Связывание со сборкой

При чтении журналов диагностики вполне вероятно, что исходный код изменится с момента развертывания кода в реальном времени.

Поэтому полезно добавить информацию о сборке, например URL-адрес текущей версии, а также каждое исключение и трассировку в свойство.

Вместо того, чтобы добавлять свойство в каждый вызов исключения отдельно, можно задать информацию в контексте по умолчанию.

    // Telemetry initializer class
    public class MyTelemetryInitializer : IContextInitializer
    {
        public void Initialize (TelemetryContext context)
        {
            context.Properties["AppVersion"] = "v2.1";
        }
    }

В инициализаторе приложения, например Global.asax.cs:

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.ContextInitializers
        .Add(new MyTelemetryInitializer());
    }

###<a name="requests"></a>Веб-запросы сервера

При [установке монитора состояний на веб-сервере][redfield] или [добавлении Application Insights в веб-проект][greenbrown] телеметрия запросов отправляется автоматически. Она также передается в диаграммы по времени запросов и откликов в обозревателе метрик и на странице «Обзор».

Если требуется отправлять дополнительные события, можно использовать API TrackRequest().

## <a name="questions"></a>Вопросы и ответы

### <a name="emptykey"></a>Появляется сообщение об ошибке «ключ инструментирования не может быть пустым»

Вероятно, установка пакета Nuget адаптера ведения журнала была выполнена без установки Application Insights.

В обозревателе решений щелкните правой кнопкой мыши `ApplicationInsights.config` и выберите **Обновить Application Insights**. Появится диалоговое окно с предложением войти в Azure и создать новый ресурс Application Insights или повторно использовать уже существующий. Это должно исправить проблему.

### <a name="limits"></a>Какой объем данных сохраняется?

До 500 событий в секунду от каждого приложения. События сохраняются в течение семи дней.

## <a name="add"></a>Дальнейшие действия

* [Настройка тестов доступности и скорости реагирования][availability]
* [Устранение неполадок][qna]





<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[platforms]: app-insights-platforms.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-get-started.md
[track]: app-insights-custom-events-metrics-api.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=62-->