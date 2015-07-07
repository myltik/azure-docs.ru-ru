<properties 
	pageTitle="Application Insights для классических приложений и служб для Windows" 
	description="Анализ использования и производительности приложения Windows с помощью Application Insights." 
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
	ms.date="06/13/2015" 
	ms.author="awills"/>

# Application Insights в классических приложениях и службах для Windows

*Доступна только предварительная версия Application Insights.*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

С помощью Application Insights можно наблюдать за использованием и производительностью вашего развернутого приложения.

Основной пакет SDK для Application Insights поддерживает классические приложения и службы для Windows. Этот пакет SDK обеспечивает полную поддержку API для всех данных телеметрии, но не содержит возможность автоматического сбора телеметрии.


## <a name="add"></a> Создание ресурса Application Insights


1.  На [портале Azure][portal] создайте новый ресурс Application Insights. В качестве типа приложения выберите приложение Магазина Windows. 

    ![Нажмите «Создать» и «Application Insights»](./media/app-insights-windows-desktop/01-new.png)


2.  Сделайте копию ключа инструментирования

    ![Нажмите «Свойства», выберите ключ и нажмите сочетание клавиш CTRL + C](./media/app-insights-windows-desktop/02-props.png)

## <a name="sdk"></a>Установка пакета SDK в приложении


1. В Visual Studio отредактируйте пакеты NuGet вашего проекта классического приложения. ![Щелкните проект правой кнопкой мыши и выберите пункт «Управление пакетами Nuget»](./media/app-insights-windows-desktop/03-nuget.png)

2. Установите пакет SDK для Application Insights.

    ![Выберите узел **Online** (В сети), укажите **Include prerelease** (Включить предварительный выпуск) и выполните поиск «Application Insights»](./media/app-insights-windows-desktop/04-ai-nuget.png)

3. Отредактируйте файл ApplicationInsights.config (который был добавлен установкой NuGet). Вставьте следующий фрагмент непосредственно перед закрывающим тегом:

    &lt;InstrumentationKey&gt;*скопированный ключ*&lt;/InstrumentationKey&gt;

    Тот же самый результат можно получить и с помощью следующего кода:
    
    `TelemetryConfiguration.Active.InstrumentationKey = "your key";`


## <a name="telemetry"></a>Вставка вызовов телеметрии

Создайте экземпляр `TelemetryClient`, а затем [используйте его для отправки телеметрии][api].

Используйте `TelemetryClient.Flush` для отправки сообщений перед закрытием приложения. (Это не рекомендуется для других типов приложений.)

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
            }
            base.OnClosing(e);
        }

```

Используйте любой из [API Application Insights][api] для отправки телеметрии. В классических приложениях Windows телеметрия не отправляется автоматически. Обычно следует использовать:

* TrackPageView(pageName) при переключении форм, страниц или вкладок;
* TrackEvent(eventName) для других действий пользователя;
* TrackMetric(имя, значение) в фоновой задаче для отправки периодических отчетов с метриками, не присоединенными к определенным событиям.
* TrackTrace(logEvent) для [ведения журнала диагностики][diagnostic];
* TrackException(исключение) в предложениях catch;

#### Инициализаторы контекста

В качестве альтернативы заданию данных сеанса в каждом экземпляре TelemetryClient можно использовать инициализатор контекста:

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

В поиске по журналу диагностики будут отображаться первые события.

Если вам требуется больше данных, нажмите кнопку «Обновить» через несколько секунд.

Если вы использовали TrackMetric параметр measurements в TrackEvent, откройте [обозреватель метрик][metrics] и откройте колонку «Фильтры», где увидите метрики.



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
 

<!---HONumber=62-->