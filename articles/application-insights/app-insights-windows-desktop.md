<properties 
	pageTitle="Application Insights для классических приложений и служб Windows" 
	description="Анализ использования и производительности классического приложения для Windows с помощью Application Insights." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/07/2015" 
	ms.author="awills"/>

# Application Insights в классических приложениях, службах и рабочих ролях для Windows

*Доступна только предварительная версия Application Insights.*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

С помощью Application Insights можно наблюдать за использованием и производительностью вашего развернутого приложения.

Все приложения для Windows, включая классические приложения, фоновые службы и рабочие роли, могут использовать базовый пакет SDK для Application Insights для отправки телеметрии в Application Insights. Можно также добавить пакет SDK Application Insights в проект библиотеки классов.

Базовый пакет SDK только предоставляет API: в отличие от пакетов SDK для веб-приложений или устройств, он не содержит каких-либо модулей, автоматически собирающих данные, поэтому нужно написать код для отправки собственной телеметрии. Некоторые другие пакеты, например сборщик данных счетчиков производительности, также будут работать в классическом приложении.


## <a name="add"></a> Создание ресурса Application Insights


1.  На [портале Azure][portal] создайте новый ресурс Application Insights. В качестве типа приложения выберите приложение Магазина Windows. 

    ![Нажмите «Создать» и «Application Insights»](./media/app-insights-windows-desktop/01-new.png)

    (Выбор типа приложения определяет содержимое колонки «Обзор» и свойства, доступные в [обозревателе метрик][metrics].)

2.  Сделайте копию ключа инструментирования

    ![Нажмите "Свойства", выберите ключ и нажмите сочетание клавиш CTRL + C](./media/app-insights-windows-desktop/02-props.png)

## <a name="sdk"></a>Установка пакета SDK в приложении


1. В Visual Studio отредактируйте пакеты NuGet вашего проекта классического приложения.

    ![Щелкните проект правой кнопкой мыши и выберите пункт "Управление пакетами Nuget"](./media/app-insights-windows-desktop/03-nuget.png)

2. Установите основной пакет API для Application Insights — Microsoft.ApplicationInsights.

    ![Поиск Application Insights](./media/app-insights-windows-desktop/04-core-nuget.png)

    *Можно ли использовать другие пакеты?*

    Да, вы можете установить другие пакеты, например пакет счетчика производительности или пакет сборщика зависимостей, если хотите использовать их модули. Пакет Microsoft.ApplicationInsights.Web включает несколько таких пакетов. Если вы хотите использовать [пакеты сборщика журналов или трассировки](app-insights-asp-net-trace-logs.md), начните с пакета веб-сервера.

    (Но не используйте пакет Microsoft.ApplicationInsights.Windows. Он предназначен для приложений Магазина Windows.)

3. Задайте ключ инструментирования (InstrumentationKey).

    * Если установлен только основной пакет API Microsoft.ApplicationInsights, ключ необходимо задать в коде, например в main(): 

     `TelemetryConfiguration.Active.InstrumentationKey = "`*ваш ключ*`";`

    * Если установлен один из других пакетов, ключ можно задать в коде или в файле ApplicationInsights.config:
 
     `<InstrumentationKey>`*ваш ключ*`</InstrumentationKey>`



## <a name="telemetry"></a>Вставка вызовов телеметрии

Создайте экземпляр `TelemetryClient`, а затем [используйте его для отправки телеметрии][api].


Например, в приложении Windows Forms можно написать следующее:

```C#

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.GetUserName();
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

Используйте любой из [API Application Insights][api] для отправки телеметрии. В классических приложениях Windows телеметрия не отправляется автоматически. Обычно следует использовать:

* `TrackPageView(pageName)` — при переключении форм, страниц или вкладок.
* `TrackEvent(eventName)` — для других действий пользователя.
* `TrackMetric(name, value)` — в фоновых задачах для отправки периодических отчетов с метриками, не связанными с определенными событиями.
* `TrackTrace(logEvent)` — для [ведения журналов диагностики][diagnostic].
* `TrackException(exception)` — в предложениях catch.
* `Flush()` — для гарантированной отправки всех данных телеметрии до закрытия приложения. Этот вызов применяется лишь в том случае, если вы используете только основной пакет API (Microsoft.ApplicationInsights). Пакеты SDK для веб-приложений и устройств реализуют это поведение автоматически. (Если ваше приложение запускается в контексте, где Интернет не всегда доступен, см. также раздел [Канал сохраняемости](#persistence-channel).)


#### Инициализаторы контекста

Чтобы просмотреть число пользователей и сеансов, можно задать значения для каждого экземпляра `TelemetryClient`. Кроме того можно использовать инициализатор контекста для выполнения этого добавления для всех клиентов:

```C#

    class UserSessionInitializer: IContextInitializer
    {
        public void Initialize(TelemetryContext context)
        {
            context.User.Id = Environment.UserName;
            context.Session.Id = Guid.NewGuid().ToString();
        }
    }

    static class Program
    {
        ...
        static void Main()
        {
            TelemetryConfiguration.Active.ContextInitializers.Add(
                new UserSessionInitializer());
            ...

```



## <a name="run"></a>Запуск проекта

[Запустите приложение с помощью клавиши F5](http://msdn.microsoft.com/library/windows/apps/bg161304.aspx) и используйте его, чтобы создать некоторый объем телеметрии.

В Visual Studio вы увидите число отправленных событий.

![](./media/app-insights-windows-desktop/appinsights-09eventcount.png)



## <a name="monitor"></a>Просмотр данных мониторинга

Вернитесь в колонку вашего приложения на портале Azure.

В [окне поиска по журналу диагностики](app-insights-diagnostic-search.md) будут отображаться первые события.

Если вам требуется больше данных, нажмите кнопку «Обновить» через несколько секунд.

Если вы использовали TrackMetric или параметр измерений в TrackEvent, откройте [обозреватель метрик][metrics], а затем — колонку «Фильтры». Метрики должны отобразиться в этой колонке, но иногда для их получения по конвейеру может потребоваться некоторое время. В таком случае закройте колонку «Фильтры», немного подождите, а затем обновите страницу.



## Канал сохраняемости 

Если приложение запускается в среде с нестабильным или медленным подключением к Интернету, рекомендуем использовать канал сохраняемости вместо стандартного канала в памяти.

Стандартный канал в памяти теряет все данные телеметрии, которые не были отправлены до закрытия приложения. Хотя можно использовать `Flush()`, чтобы попытаться отправить данные, остающиеся в буфере, данные все равно будут потеряны, если нет подключение к Интернету или приложение завершит работу до завершения передачи.

Канал сохраняемости, напротив, копирует данные телеметрии в файл, прежде чем отправить их на портал. `Flush()` гарантирует сохранение данных в файле. Если какие-либо данные не отправляются до закрытия приложения, они остаются в файле. Данные отправляются при перезапуске приложения, если доступно подключение к Интернету. Данные будут накапливаться в файле, пока не появится подключение к Интернету.

### Использование канала сохраняемости

1. Импортируйте пакет NuGet [Microsoft.ApplicationInsights.PersistenceChannel](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PersistenceChannel).
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

 
Канал сохраняемости оптимизирован для устройств, где число событий, созданных приложением, относительно небольшое, а соединение часто ненадежное. Этот канал сначала записывает события на диск в надежном хранилище, а затем пытается отправить их.

#### Пример

Предположим, вам нужно отслеживать необработанные исключения. Подпишитесь на событие `UnhandledException`. Добавьте вызов Flush в функцию обратного вызова, чтобы гарантировать, что данные телеметрии будут сохранены.
 
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

Когда приложение завершит работу, в расположении `%LocalAppData%\Microsoft\ApplicationInsights` появится файл, содержащий сжатые события.
 
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


Код канала сохраняемости можно найти на сайте [GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/master/src/TelemetryChannels/PersistenceChannel).


## <a name="usage"></a>Дальнейшие действия

[Отслеживание использования приложения][knowUsers]

[Ведение журналов диагностики и поиск по ним][diagnostic]

[Устранение неполадок][qna]




<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[knowUsers]: app-insights-overview-usage.md
[api]: app-insights-api-custom-events-metrics.md
[CoreNuGet]: https://www.nuget.org/packages/Microsoft.ApplicationInsights
 

<!---HONumber=Sept15_HO4-->