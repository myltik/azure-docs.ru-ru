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
	ms.date="08/19/2015" 
	ms.author="awills"/>

# Application Insights в классических приложениях, службах и рабочих ролях для Windows

*Доступна только предварительная версия Application Insights.*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

С помощью Application Insights можно наблюдать за использованием и производительностью вашего развернутого приложения.

Все приложения для Windows, включая классические приложения, фоновые службы и рабочие роли, могут использовать базовый пакет SDK для Application Insights для отправки телеметрии в Application Insights. Базовый пакет SDK только предоставляет API: в отличие от пакетов SDK для веб-приложений или устройств, он не содержит каких-либо модулей, автоматически собирающих данные, поэтому нужно написать код для отправки собственной телеметрии.


## <a name="add"></a> Создание ресурса Application Insights


1.  На [портале Azure][portal] создайте новый ресурс Application Insights. В качестве типа приложения выберите приложение Магазина Windows. 

    ![Нажмите «Создать» и «Application Insights»](./media/app-insights-windows-desktop/01-new.png)

    (Выбор типа приложения определяет содержимое колонки «Обзор» и свойства, доступные в [обозревателе метрик][metrics].)

2.  Сделайте копию ключа инструментирования

    ![Нажмите «Свойства», выберите ключ и нажмите сочетание клавиш CTRL + C](./media/app-insights-windows-desktop/02-props.png)

## <a name="sdk"></a>Установка пакета SDK в приложении


1. В Visual Studio отредактируйте пакеты NuGet вашего проекта классического приложения.

    ![Щелкните проект правой кнопкой мыши и выберите пункт «Управление пакетами Nuget»](./media/app-insights-windows-desktop/03-nuget.png)

2. Установите основной пакет API для Application Insights.

    ![Поиск Application Insights](./media/app-insights-windows-desktop/04-core-nuget.png)

    Можно установить другие пакеты, например пакет счетчика производительности или пакет записи журналов, если вы хотите использовать их возможности.

3. Укажите ключ InstrumentationKey в коде, например в main().

    `TelemetryConfiguration.Active.InstrumentationKey = "your key";`

*Почему отсутствует ApplicationInsights.config?*

* Файл конфигурации не устанавливается при помощи основного пакета API, который используется только для настройки сборщиков телеметрии. Поэтому вы пишете свой собственный код, устанавливающий ключ инструментирования и отправляющий телеметрию.
* Если установлен какой-либо другой пакет, у вас будет CONFIG-файл. Вы можете вставить ключ инструментирования в него, а не задать его в коде.

*Можно ли использовать другой пакет NuGet?*

* Да, можно использовать пакет веб-сервера (Microsoft.ApplicationInsights.Web), который установит сборщики для различных модулей сбора данных, таких как счетчики производительности. Будет установлен файл конфигурации, в котором вы укажите свой ключ инструментирования. Используйте [ApplicationInsights.config для отключения ненужных вам модулей](app-insights-configuration-with-applicationinsights-config.md), например сборщика HTTP-запросов. 
* Если вы хотите использовать [пакеты сборщика журналов или трассировки](app-insights-asp-net-trace-logs.md), начните с пакета веб-сервера. 

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
            }
            base.OnClosing(e);
        }

```

Используйте любой из [API Application Insights][api] для отправки телеметрии. В классических приложениях Windows телеметрия не отправляется автоматически. Обычно следует использовать:

* TrackPageView(pageName) при переключении форм, страниц или вкладок;
* TrackEvent(eventName) для других действий пользователя;
* TrackMetric(имя, значение) в фоновой задаче для отправки периодических отчетов с метриками, не присоединенными к определенным событиям;
* TrackTrace(logEvent) для [ведения журнала диагностики][diagnostic];
* TrackException(исключение) в предложениях перехвата.


Чтобы убедиться, что перед закрытием приложения отправляется вся телеметрия, используйте `TelemetryClient.Flush()`. Как правило, телеметрия передается в пакетном режиме через регулярные интервалы. (Очистка рекомендуется только в том случае, если вы используете только основной API. Пакеты SDK для веб-приложений и устройств реализуют это поведение автоматически.)


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
 

<!---HONumber=August15_HO8-->