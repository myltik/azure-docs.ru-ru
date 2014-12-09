<properties title="Track custom usage events and metrics in your web app with Application Insights" pageTitle="Отслеживание событий и метрик использования в вашем веб-приложении с помощью Application Insights" description="Insert a few lines of code to find out what users are doing with your website." metaKeywords="analytics monitoring application insights" authors="awills" manager="kamrani"  />
 
<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-10-01" ms.author="awills" />

# Отслеживание использования пользовательских событий и метрик в веб-приложении

*Служба Application Insights доступна в предварительной версии.*

Вставьте несколько строк кода в свое веб-приложение и узнайте, как пользователи его используют. Вы можете отслеживать события, метрики, а также просмотры страниц. Вы сможете просматривать диаграммы и таблицы данных, составленные по всем пользователям. 

> [AZURE.NOTE] В настоящее время полные данные о взаимодействии с пользователем не доступны. Вы можете отправлять настраиваемые события и метрики в Application Insights, а также осуществлять поиск необработанных данных телеметрии в разделе[Поиск данных диагностики][diagnostic]. Сейчас просмотр диаграмм с обработанной статистикой не доступен - эта функция будет добавлена в ближайшее время.

<!-- Sample pic -->

* [Отслеживание клиента и сервера](#clientServer)
* [Перед началом работы](#prep)
* [Отслеживание метрик](#metrics)
* [Отслеживание событий](#events)
* [Отслеживание просмотров страниц](#pageViews)
* [Фильтрация, поиск и сегментация данных с помощью свойств](#properties)
* [Объединение метрик и событий](#measurements)
* [Задание значений свойств по умолчанию](#defaults)
* [Определение нескольких контекстов](#contexts)
* [Отключение и включение телеметрии](#disable)
* [Дальнейшие действия](#next)



## <a name="clientServer"></a> Отслеживание клиента и сервера

Вы можете отправлять телеметрию как со стороны клиента (веб-страница), так и со стороны сервера своего приложения, а также с обеих сторон.

Клиентские и серверные API очень похожи. Вы можете отправлять данные телеметрии одного и того же типа как с веб-браузеров своих пользователей, так и со своего веб-сервера. Различие состоит в объеме данных, которые можно отправить.

* Отслеживание в веб-клиенте особенно полезно при наличии очень активных веб-страниц с большим количеством скриптов Java. Например, вы можете отслеживать, с какой частотой пользователи нажимают на определенную кнопку и часто ли возникают ошибки проверки подлинности.
* Отслеживание на веб-сервере более полезно для мониторинга бизнес-метрик и событий, таких как стоимость товаров в корзине покупателя или количество отмененных заказов.

В обычном веб-приложении ASP.NET на главной веб-странице есть стандартный вызов trackPageView() через JavaScript. Вам остается добавить в программный код серверной части приложения несколько вызовов для отслеживания событий и метрик. Если объем программного кода на стороне клиента очень большой, вы также можете добавить несколько вызовов для отслеживания событий и метрик в клиенте.


## <a name="prep"></a>Перед началом работы

Если вы еще не сделали этого, понадобится следующее.

* Чтобы получить данные телеметрии из веб-приложения ASP.NET:
    [Добавление Application Insights в ваш проект][greenbrown]
    В программный код серверной части необходимо добавить:
    (C#) `using Microsoft.ApplicationInsights;`
	(VB) `Imports Microsoft.ApplicationInsights`
* [Настройка аналитики использования веб-сайта][usage]. Код инициализации JavaScript, который необходимо добавить в каждую веб-страницу, где вы планируете написать код мониторинга, или в главную веб-страницу. 
    Если он работает, вы увидите данные в модуле "Обзор" в разделе "Аналитика использования".

После запуска приложения на компьютере разработчика в режиме отладки результаты появятся в Application Insights через несколько секунд. При развертывании приложения передача данных от вашего сервера и клиентов через конвейер займет больше времени.

<!--
## <a name="metrics"></a> Отслеживание метрик

Для получения основных данных об использовании, таких как данные о просмотре веб-страниц, больше ничего не требуется. Но вы можете написать несколько строк кода, чтобы получить больше информации о том, что ваши пользователи делают с приложением.

Например, если приложение является игрой, вам может потребоваться среднее значение очков, набранных пользователями в игре, чтобы после выпуска новой версии определить, стала ли ваша игра проще или сложнее.

Для отслеживания числовой метрики, например набранных очков, следует добавить в соответствующем месте кода вашего приложения примерно такую строку кода:

JavaScript на стороне клиента

    appInsights.trackMetric("Alerts", notifications.Count);

C# на стороне сервера

    var telemetry = new TelemetryClient();
    telemetry.TrackMetric ("Users online", currentUsers.Count);

VB на стороне сервера

    Dim telemetry = New TelemetryClient
    telemetry.TrackMetric ("Users online", currentUsers.Count)

Протестируйте приложение и используйте его для выполнения вызова trackMetric().


Затем перейдите в приложение в Application Insights и щелкните плитку [Метрики][metrics]. Выберите свою метрику, чтобы просмотреть первые результаты.


На диаграмме отображаются текущие средние значения, зарегистрированные по всем вашим пользователям. 


(Учите: метрики не оптимизированы для диагностирования проблем. Если вам нужна диагностика, см. раздел [Ведение журналов диагностики][diagnostic].) -->


## <a name="events"></a>Отслеживание событий

Для событий можно узнать частоту их возникновения, усредненную по пользователям. Предположим, вы хотите узнать, как часто пользователи полностью проходят вашу игру. Для этого в коде, который завершает игру, вставьте следующую строку:

JavaScript на стороне клиента

    appInsights.trackEvent("EndOfGame");

C# на стороне сервера
    
    var telemetry = new TelemetryClient();
    telemetry.TrackEvent("EndOfGame");

VB на стороне сервера


    Dim telemetry = New TelemetryClient
    telemetry.TrackEvent("EndOfGame")

Если вы отправляете данные телеметрии и с клиента, и с сервера, обязательно присваивайте событиям разные имена.


## <a name="pageViews"></a>Просмотры страницы (только для клиента)

По умолчанию сценарий инициализации в заголовке веб-страницы регистрирует просмотры страницы и присваивает событиям имена в виде URL-адреса соответствующей страницы. Эти вызовы предоставляют основную статистику использования страницы. 

![Usage analytics on main app blade](./media/appinsights/appinsights-05-usageTiles.png)

### Настраиваемые данные страницы

Если это требуется, можно изменить имена, присваиваемые событиям при вызове, или добавить дополнительные вызовы. Например, если на единственной странице вашего веб-приложения отображается несколько вкладок, вам может потребоваться регистрация просмотра страницы при переходе пользователя на другую вкладку. Например: 

JavaScript на стороне клиента:

    appInsights.trackPageView("tab1");

Если у вас есть несколько вкладок на различных HTML-страницах, можно также указать URL-адрес:

    appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");


## <a name="properties"></a>Фильтрация, поиск и сегментация данных с помощью свойств

Вы можете прикрепить свойства и результаты измерений к своим событиям, просмотрам страниц и другим данным телеметрии. 

**Свойства** - это строковые значения, которые можно использовать для фильтрации телеметрии в отчетах об использовании. Например, если приложение содержит несколько игр, имеет смысл к каждому событию присоединять имя игры, чтобы видеть, какие игры наиболее популярны.

**Измерения** - это числовые значения, из которых можно извлечь статистику для отчетов об использовании.


JavaScript на стороне клиента

    appInsights.trackEvent("EndOfGame",
         // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric measurements:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );

C# на стороне сервера

    // Set up some properties:
    var properties = new Dictionary <string, string> 
       {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("endOfGame", properties, measurements);


VB на стороне сервера

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Score", currentGame.Score)
    measurements.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("endOfGame", properties, measurements)


Таким же образом прикрепите свойства к просмотрам страниц:

JavaScript на стороне клиента

    appInsights.trackPageView("Win", 
     {Game: currentGame.Name}, 
     {Score: currentGame.Score});

 

<!--
To see the filters, expand the parent event group, and select a particular event in the table - in this example, we expanded 'open' and selected 'buy':

////// pic //////
-->

> [WACOM.NOTE] Постарайтесь не указывать в свойствах личные сведения.


## События и просмотры страниц с меткой времени

К просмотрам страниц и событиям можно прикрепить данные о времени. Используйте эти вызовы вместо вызова trackEvent или trackPageView:

JavaScript на стороне клиента

    // At the start of the game:
    appInsights.startTrackEvent(game.id);

    // At the end of the game:
    appInsights.stopTrackEvent(game.id, {GameName: game.name}, {Score: game.score});

    // At the start of a page view:
    appInsights.startTrackPage(myPage.name);

    // At the completion of a page view:
    appInsights.stopTrackPage(myPage.name, "http://fabrikam.com/page", properties, measurements);

Используйте ту же строку, что и в первом параметре, для вызовов запуска и остановки.

## <a name="defaults"></a>Задание значений свойств по умолчанию (не на веб-клиенте)

Можно задать значения по умолчанию в TelemetryContext. Они присоединяются ко всем метрикам и событиям, отправляемым из контекста. 
    

C# на стороне сервера

    var context = new TelemetryContext();
    context.Properties["Game"] = currentGame.Name;
    var telemetry = new TelemetryClient(context);
    // Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("EndOfGame");
    
VB на стороне сервера

    Dim context = New TelemetryContext
    context.Properties("Game") = currentGame.Name
    Dim telemetry = New TelemetryClient(context)
    ' Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("EndOfGame")

    
    
Некоторые данные телеметрии могут отличаться от значений по умолчанию.

Если вы хотите переключаться между группами значений свойств по умолчанию, следует настроить несколько контекстов.



## <a name="next"></a>Дальнейшие действия


[Поиск событий и журналов][diagnostic]

[Устранение неполадок][qna]


[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]




