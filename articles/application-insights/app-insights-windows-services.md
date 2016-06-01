<properties 
	pageTitle="Application Insights для служб Windows" 
	description="Анализ использования и производительности фоновых служб Windows с помощью Application Insights." 
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
	ms.date="04/27/2016" 
	ms.author="awills"/>

# Application Insights в фоновых службах для Windows

*Доступна только предварительная версия Application Insights.*

С помощью [Visual Studio Application Insights](app-insights-get-started.md) можно отслеживать использование и производительность развернутого приложения.

Все приложения для Windows, включая фоновые службы и рабочие роли, могут использовать пакет SDK для Application Insights для отправки данных телеметрии в Application Insights. Кроме того, можно добавить пакет SDK в проект библиотеки классов.

Можно выбрать стандартные сборщики данных для использования (например, для счетчиков монитора производительности или вызовов зависимостей) или написать собственные телеметрии, используя основной API.

Сначала проверьте, работаете ли вы с другим типом приложений Windows.

* Веб-приложения: перейдите к [ASP.NET 4](app-insights-asp-net.md), [ASP.NET 5](app-insights-asp-net-five.md).
* [Облачные службы Azure](app-insights-cloudservices.md)
* Классические приложения: рекомендуется [HockeyApp](https://hockeyapp.net). С помощью HockeyApp можно управлять распространением, тестированием в реальном времени и отзывами пользователей, а также отслеживать отчеты об использовании и сбоях. Кроме того, вы можете [отправлять данные телеметрии в Application Insights из классического приложения](app-insights-windows-desktop.md). 


## <a name="add"></a> Создание ресурса Application Insights


1.  На [портале Azure][portal] создайте новый ресурс Application Insights. Для параметра типа приложения выберите приложение ASP.NET. 

    ![Нажмите «Создать» и «Application Insights»](./media/app-insights-windows-services/01-new.png)


2.  Сделайте копию ключа инструментирования Найдите ключ в раскрывающемся списке "Основные компоненты" нового ресурса, который вы только что создали. Закройте схему сопоставления приложений или прокрутите влево до колонки "Обзор" для ресурса.

    ![Нажмите "Основные компоненты", выберите ключ и нажмите сочетание клавиш CTRL + C](./media/app-insights-windows-services/10.png)

## <a name="sdk"></a>Установка пакета SDK в приложении


1. В Visual Studio измените пакеты NuGet вашего проекта приложения для Windows.

    ![Щелкните проект правой кнопкой мыши и выберите пункт "Управление пакетами Nuget"](./media/app-insights-windows-services/03-nuget.png)

2. Установите пакет Windows Server для Application Insights: Microsoft.ApplicationInsights.WindowsServer

    ![Поиск Application Insights](./media/app-insights-windows-services/04-ai-nuget.png)

    *Можно ли использовать другие пакеты?*

    Да. Выберите основной API (Microsoft.ApplicationInsights), если вы хотите использовать API для отправки собственной телеметрии. Пакет Windows Server автоматически включает основной API, а также ряд других пакетов, например для сбора данных счетчиков производительности и отслеживания зависимостей.


3. Задайте ключ инструментирования (InstrumentationKey).

    * Если установлен только основной пакет API Microsoft.ApplicationInsights, ключ необходимо задать в коде, например в main(): 

    `TelemetryConfiguration.Active.InstrumentationKey = "` *ваш ключ* `";`

    Если установлен один из других пакетов, ключ можно задать в коде или в файле ApplicationInsights.config:
 
    `<InstrumentationKey>`*ваш ключ*`</InstrumentationKey>`

    Если используется файл ApplicationInsights.config, убедитесь, что его свойства в обозревателе решений имеют следующие значения: **"Действие сборки = содержимое", "Копировать в выходной каталог = копировать"**.

## <a name="telemetry"></a>Вставка вызовов телеметрии

Используйте любой из [API Application Insights][api] для отправки телеметрии. При использовании основного API данные телеметрии не отправляются автоматически. Обычно следует использовать:

* `TrackPageView(pageName)` — при переключении форм, страниц или вкладок.
* `TrackEvent(eventName)` — для других действий пользователя.
* `TrackMetric(name, value)` — в фоновых задачах для отправки периодических отчетов с метриками, не связанными с определенными событиями.
* `TrackTrace(logEvent)` — для [ведения журналов диагностики][diagnostic].
* `TrackException(exception)` — в предложениях catch.
* `Flush()` — для гарантированной отправки всех данных телеметрии до закрытия приложения. Этот вызов применяется лишь в том случае, если вы используете только основной пакет API (Microsoft.ApplicationInsights). Пакеты SDK для веб-приложений реализуют это поведение автоматически. (Если ваше приложение запускается в контексте, где Интернет не всегда доступен, см. также раздел [Канал сохраняемости](#persistence-channel).)


#### Инициализаторы телеметрии

Чтобы просмотреть число пользователей и сеансов, можно задать значения для каждого экземпляра `TelemetryClient`. Кроме того, можно использовать инициализатор телеметрии для выполнения этого добавления для всех клиентов.

```C#

    class UserSessionInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            telemetry.Context.User.Id = Environment.UserName;
            telemetry.Context.Session.Id = Guid.NewGuid().ToString();
        }
        
    }

    static class Program
    {
        ...
        static void Main()
        {
            TelemetryConfiguration.Active.TelemetryInitializers.Add(
                new UserSessionInitializer());
            ...

```



## <a name="run"></a>Запуск проекта

[Запустите приложение с помощью клавиши F5](http://msdn.microsoft.com/library/windows/apps/bg161304.aspx) и используйте его, чтобы создать некоторый объем телеметрии.

В Visual Studio вы увидите число отправленных событий.

![](./media/app-insights-windows-services/appinsights-09eventcount.png)

События также отображаются в окнах диагностики и вывода.

## <a name="monitor"></a>Просмотр данных мониторинга

Вернитесь в колонку вашего приложения на портале Azure.

В окне потока в реальном времени будут отображаться первые события.


## Канал сохраняемости 

Если приложение запускается в среде с нестабильным или медленным подключением к Интернету, рекомендуем использовать канал сохраняемости вместо стандартного канала в памяти.

Стандартный канал в памяти теряет все данные телеметрии, которые не были отправлены до закрытия приложения. Хотя можно использовать `Flush()`, чтобы попытаться отправить данные, остающиеся в буфере, данные все равно будут потеряны, если нет подключения к Интернету или если приложение завершит работу до завершения передачи.

Канал сохраняемости, напротив, копирует данные телеметрии в файл, прежде чем отправить их на портал. `Flush()` гарантирует сохранение данных в файле. Если какие-либо данные не отправляются до закрытия приложения, они остаются в файле. Данные отправляются при перезапуске приложения, если доступно подключение к Интернету. Данные будут накапливаться в файле, пока не появится подключение к Интернету.

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
 

<!---HONumber=AcomDC_0518_2016-->