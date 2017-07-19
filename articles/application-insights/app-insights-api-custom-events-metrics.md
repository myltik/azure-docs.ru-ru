---
title: "API Application Insights для пользовательских событий и метрик | Документация Майкрософт"
description: "Вставьте несколько строк кода в свое устройство или классическое приложение, на веб-страницу или в службу, чтобы отслеживать использование приложения и диагностировать неполадки."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 80400495-c67b-4468-a92e-abf49793a54d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/17/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: e22bd56e0d111add6ab4c08b6cc6e51c364c7f22
ms.openlocfilehash: 8793744f63388c5df04a167585d5f7b99ec7acee
ms.contentlocale: ru-ru
ms.lasthandoff: 05/19/2017


---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>API Application Insights для пользовательских событий и метрик


Вставьте несколько строк кода в свое приложение, чтобы узнать, как пользователи его используют, или чтобы диагностировать неполадки. Вы можете отправлять телеметрию из устройств и классических приложений, веб-клиентов и веб-серверов. Используйте основной API телеметрии [Azure Application Insights](app-insights-overview.md), чтобы отправлять пользовательские события и метрики, а также собственные версии стандартной телеметрии. Этот же API используется стандартными сборщиками данных Application Insights.

## <a name="api-summary"></a>Сводные данные API
Этот API используется на всех платформах, кроме некоторых небольших исключений.

| Метод | Область использования |
| --- | --- |
| [`TrackPageView`](#page-views) |Страницы, экраны, колонки или формы. |
| [`TrackEvent`](#trackevent) |Действия пользователя и другие события. Используется для отслеживания поведения пользователя или мониторинга производительности. |
| [`TrackMetric`](#trackmetric) |Измерения производительности, не связанные с конкретными событиями, например, измерение длины очереди. |
| [`TrackException`](#trackexception) |Регистрация исключений для диагностики. Отслеживайте исключения, связанные с другими событиями, и изучайте трассировку стека. |
| [`TrackRequest`](#trackrequest) |Регистрация частоты и длительности запросов к серверу для анализа производительности. |
| [`TrackTrace`](#tracktrace) |Сообщения журналов диагностики. Можно также использовать журналы сторонних приложений. |
| [`TrackDependency`](#trackdependency) |Регистрация длительности и частоты вызовов внешних компонентов, от которых зависит приложение. |

Вы можете [прикрепить свойства и метрики](#properties) к большинству этих вызовов телеметрии.

## <a name="prep"></a>Перед началом работы
Если вы еще не сделали этого, понадобится следующее.

* Добавьте пакет SDK Application Insights в свой проект:

  * [проект ASP.NET;](app-insights-asp-net.md)
  * [проект Java;](app-insights-java-get-started.md)
  * [JavaScript на каждой веб-странице.](app-insights-javascript.md) 
* В программный код устройства или веб-сервера необходимо добавить:

    *C#:* `using Microsoft.ApplicationInsights;`

    *Visual Basic:* `Imports Microsoft.ApplicationInsights`

    *Java:* `import com.microsoft.applicationinsights.TelemetryClient;`

## <a name="constructing-a-telemetryclient-instance"></a>Создание экземпляра TelemetryClient
Создайте экземпляр TelemetryClient (за исключением JavaScript на веб-страницах).

*C#*

    private TelemetryClient telemetry = new TelemetryClient();

*Visual Basic*

    Private Dim telemetry As New TelemetryClient

*Java*

    private TelemetryClient telemetry = new TelemetryClient();

Класс TelemetryClient является потокобезопасным.

Рекомендуется использовать экземпляр TelemetryClient для каждого модуля приложения. Например, у вас может быть один экземпляр TelemetryClient в веб-службе для отчетов о входящих HTTP-запросах, а другой в среднем классе для отчетов о событиях бизнес-логики. Можно задать свойства, например `TelemetryClient.Context.User.Id` для отслеживания пользователей и сеансов или `TelemetryClient.Context.Device.Id` для идентификации компьютера. Эти данные прикрепляются ко всем событиям, отправляемым экземпляром.

## <a name="trackevent"></a>TrackEvent (Отслеживание событий)
В Application Insights *пользовательское событие* — это точка данных, которую можно отобразить как суммарное значение в [обозревателе метрик](app-insights-metrics-explorer.md), а также как отдельные значения на вкладке [поиска по журналу диагностики](app-insights-diagnostic-search.md). (Оно не связано с MVC и другими "событиями" платформы.)

Вставьте вызовы TrackEvent в код, чтобы вычислить, как часто пользователи выбирают определенный компонент, как часто они достигают определенных целей, а также как часто возникают те или иные ошибки.

Например, отправляйте событие в игровом приложении при каждом выигрыше пользователя:

*JavaScript*

    appInsights.trackEvent("WinGame");

*C#*

    telemetry.TrackEvent("WinGame");

*Visual Basic*

    telemetry.TrackEvent("WinGame")

*Java*

    telemetry.trackEvent("WinGame");


### <a name="view-your-events-in-the-azure-portal"></a>Просмотр событий на портале Azure
Чтобы увидеть число событий, откройте колонку [Обозреватель метрик](app-insights-metrics-explorer.md), добавьте новую диаграмму и выберите **События**.  

![Просмотр количества пользовательских событий](./media/app-insights-api-custom-events-metrics/01-custom.png)

Чтобы сравнить количество различных событий, установите тип диаграммы **Сетка** и выполните группировку по имени события.

![Задание типа диаграммы и группировка](./media/app-insights-api-custom-events-metrics/07-grid.png)

На сетке щелкните имя события, чтобы увидеть отдельные экземпляры данного события. Щелкните любое из них, чтобы просмотреть подробные данные.

![Подробно просмотрите события](./media/app-insights-api-custom-events-metrics/03-instances.png)

Чтобы сконцентрироваться на определенных событиях в функции поиска или в обозревателе метрик, задайте в фильтре колонки имена событий, которые вас интересуют.

![Откройте "Фильтры", разверните пункт "Имя события" и выберите одно или несколько значений.](./media/app-insights-api-custom-events-metrics/06-filter.png)

### <a name="custom-events-in-analytics"></a>Пользовательские события в службе аналитики

Данные телеметрии доступны в таблице `customEvents` в [службе аналитики Application Insights](app-insights-analytics.md). Каждая строка представляет собой вызов `trackEvent(..)` в приложении. 

Если действует [выборка](app-insights-sampling.md), свойство itemCount будет иметь значение больше 1. Например, itemCount==10 означает, что из 10 вызовов trackEvent() процесс выборки передал только один. Поэтому, чтобы получить правильное количество пользовательских событий, следует использовать такой код, как `customEvent | summarize sum(itemCount)`.


## <a name="trackmetric"></a>TrackMetric (Отслеживание метрик)

Application Insights может создать диаграмму метрик, не привязанных к определенным событиям. Например, можно отслеживать длину очереди через регулярные промежутки времени. Благодаря метрикам отдельные измерения менее интересны, чем вариации и тенденции, и поэтому статистические диаграммы полезны.

Для отправки метрик в Application Insights можно использовать API `TrackMetric(..)`. Отправить метрику можно двумя способами. 

* Отдельное значение. Каждый раз при выполнении измерения в приложении вы отправляете соответствующее значение в Application Insights. Например, предположим, что имеется метрика, описывающая число элементов в контейнере. В течение некоторого периода времени вы сначала помещаете 3 элемента в контейнер, а затем удаляете 2 элемента. Соответственно, `TrackMetric` вызывается дважды: сначала передается значение `3`, а затем значение `-2`. Application Insights сохранит оба значения от вашего имени. 

* Агрегирование. При работе с метриками отдельные измерения редко представляют интерес. Вместо этого обычно важна сводка того, что произошло за определенный период времени. Такая сводка называется _агрегированием_. В приведенном выше примере сумма агрегированной метрики за период времени равна `1`, а число значений метрики — `2`. При использовании агрегирования `TrackMetric` вызывается только один раз для каждого периода времени и отправляются агрегированные значения. Это рекомендуемый подход, так как он может значительно снизить затраты и потери производительности благодаря отправке меньшего числа точек данных в Application Insights. При этом собирается вся важная информация.

### <a name="examples"></a>Примеры:

#### <a name="single-values"></a>Отдельные значения

Отправка значения одной метрики

*JavaScript*

 ```Javascript
     appInsights.trackMetric("queueLength", 42.0);
 ```

*C#, Java*

```C#
    var sample = new MetricTelemetry();
    sample.Name = "metric name";
    sample.Value = 42.3;
    telemetryClient.TrackMetric(sample);
```

#### <a name="aggregating-metrics"></a>Агрегированные метрики

Рекомендуется агрегировать метрики перед их отправкой из приложения, чтобы уменьшить нагрузку на пропускную полосу, затраты и потери производительности.
Ниже представлен пример кода для агрегирования.

*C#*

```C#
using System;
using System.Threading;
using System.Threading.Tasks;

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;

namespace MetricAggregationExample
{
    /// <summary>
    /// Aggregates metric values for a single time period.
    /// </summary>
    internal class MetricAggregator
    {
        private SpinLock _trackLock = new SpinLock();

        public DateTimeOffset StartTimestamp    { get; }
        public int Count                        { get; private set; }
        public double Sum                       { get; private set; }
        public double SumOfSquares              { get; private set; }
        public double Min                       { get; private set; }
        public double Max                       { get; private set; }
        public double Average                   { get { return (Count == 0) ? 0 : (Sum / Count); } }
        public double Variance                  { get { return (Count == 0) ? 0 : (SumOfSquares / Count)
                                                                                  - (Average * Average); } }
        public double StandardDeviation         { get { return Math.Sqrt(Variance); } }

        public MetricAggregator(DateTimeOffset startTimestamp)
        {
            this.StartTimestamp = startTimestamp;
        }

        public void TrackValue(double value)
        {
            bool lockAcquired = false;

            try
            {
                _trackLock.Enter(ref lockAcquired);

                if ((Count == 0) || (value < Min))  { Min = value; }
                if ((Count == 0) || (value > Max))  { Max = value; }
                Count++;
                Sum += value;
                SumOfSquares += value * value;
            }
            finally
            {
                if (lockAcquired)
                {
                    _trackLock.Exit();
                }
            }
        }
    }   // internal class MetricAggregator

    /// <summary>
    /// Accepts metric values and sends the aggregated values at 1-minute intervals.
    /// </summary>
    public sealed class Metric : IDisposable
    {
        private static readonly TimeSpan AggregationPeriod = TimeSpan.FromSeconds(60);

        private bool _isDisposed = false;
        private MetricAggregator _aggregator = null;
        private readonly TelemetryClient _telemetryClient;

        public string Name { get; }

        public Metric(string name, TelemetryClient telemetryClient)
        {
            this.Name = name ?? "null";
            this._aggregator = new MetricAggregator(DateTimeOffset.UtcNow);
            this._telemetryClient = telemetryClient ?? throw new ArgumentNullException(nameof(telemetryClient));

            Task.Run(this.AggregatorLoopAsync);
        }

        public void TrackValue(double value)
        {
            MetricAggregator currAggregator = _aggregator;
            if (currAggregator != null)
            {
                currAggregator.TrackValue(value);
            }
        }

        private async Task AggregatorLoopAsync()
        {
            while (_isDisposed == false)
            {
                try
                {
                    // Wait for end end of the aggregation period:
                    await Task.Delay(AggregationPeriod).ConfigureAwait(continueOnCapturedContext: false);

                    // Atomically snap the current aggregation:
                    MetricAggregator nextAggregator = new MetricAggregator(DateTimeOffset.UtcNow);
                    MetricAggregator prevAggregator = Interlocked.Exchange(ref _aggregator, nextAggregator);

                    // Only send anything is at least one value was measured:
                    if (prevAggregator != null && prevAggregator.Count > 0)
                    {
                        // Compute the actual aggregation period length:
                        TimeSpan aggPeriod = nextAggregator.StartTimestamp - prevAggregator.StartTimestamp;
                        if (aggPeriod.TotalMilliseconds < 1)
                        {
                            aggPeriod = TimeSpan.FromMilliseconds(1);
                        }

                        // Construct the metric telemetry item and send:
                        var aggregatedMetricTelemetry = new MetricTelemetry(
                                Name,
                                prevAggregator.Count,
                                prevAggregator.Sum,
                                prevAggregator.Min,
                                prevAggregator.Max,
                                prevAggregator.StandardDeviation);
                        aggregatedMetricTelemetry.Properties["AggregationPeriod"] = aggPeriod.ToString("c");

                        _telemetryClient.Track(aggregatedMetricTelemetry);
                    }
                }
                catch(Exception ex)
                {
                    // log ex as appropriate for your application
                }
            }
        }

        void IDisposable.Dispose()
        {
            _isDisposed = true;
            _aggregator = null;
        }
    }   // public sealed class Metric
}
```

### <a name="custom-metrics-in-metrics-explorer"></a>Пользовательские метрики в обозревателе метрик

Чтобы увидеть результаты, откройте обозреватель метрик и добавьте новую диаграмму. Измените диаграмму, чтобы отобразить метрику.

> [!NOTE]
> Пользовательская метрика может отобразиться в списке доступных метрик через несколько минут.
>

![Добавьте новую диаграмму или выберите существующую, а в поле Custom (Пользовательские) выберите свою метрику](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

### <a name="custom-metrics-in-analytics"></a>Настраиваемые метрики в аналитике

Данные телеметрии доступны в таблице `customMetrics` в [службе аналитики Application Insights](app-insights-analytics.md). Каждая строка представляет собой вызов `trackMetric(..)` в приложении.
* `valueSum` — это сумма измерений. Чтобы получить среднее значение, разделите текущее значение на значение `valueCount`.
* `valueCount` — число измерений, агрегированных в этот вызов `trackMetric(..)`.

## <a name="page-views"></a>Просмотры страниц
В устройстве или приложении веб-страницы телеметрия просмотров страницы отображается по умолчанию при загрузке каждого экрана или страницы. Однако это можно изменить, чтобы отслеживать количество просмотров страницы в дополнительное или другое время. Например, в приложении, которое отображает вкладки или колонки, может потребоваться отслеживать страницу каждый раз, когда пользователь открывает новую колонку.

![Область "Использование" в колонке "Обзор"](./media/app-insights-api-custom-events-metrics/appinsights-47usage-2.png)

Данные о пользователе и сеансе отправляются в качестве свойств вместе с количеством просмотров страниц, поэтому диаграммы для пользователей и сеансов активируются при наличии телеметрии по количеству просмотров страницы.

### <a name="custom-page-views"></a>Пользовательские данные о просмотре страницы
*JavaScript*

    appInsights.trackPageView("tab1");

*C#*

    telemetry.TrackPageView("GameReviewPage");

*Visual Basic*

    telemetry.TrackPageView("GameReviewPage")


Если у вас есть несколько вкладок на различных HTML-страницах, можно также указать URL-адрес:

    appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");

### <a name="timing-page-views"></a>Время просмотра страниц
По умолчанию время, отображаемое как **Время загрузки страницы**, отсчитывается от момента отправки запроса браузером до вызова события загрузки страницы в браузере.

Вместо этого вы можете выбрать один из таких вариантов:

* Задайте явную длительность вызова [trackPageView](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#trackpageview): `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);`.
* Используйте вызовы времени просмотра страницы `startTrackPage` и `stopTrackPage`.

*JavaScript*

    // To start timing a page:
    appInsights.startTrackPage("Page1");

...

    // To stop timing and log the page:
    appInsights.stopTrackPage("Page1", url, properties, measurements);

Имя, используемое в качестве первого параметра, связывает вызовы start и stop. По умолчанию используется имя текущей страницы.

Полученные длительности загрузки страниц, отображаемые в обозревателе метрик, являются производными интервала между вызовами start и stop. Выбор рассматриваемого интервала за вами.

### <a name="page-telemetry-in-analytics"></a>Телеметрия страниц в службе аналитики

В [службе аналитики](app-insights-analytics.md) две таблицы содержат данные по операциям в браузере.

* Таблица `pageViews` содержит данные по URL-адресу и названию страницы.
* Таблица `browserTimings` содержит данные по производительности клиента, например время, затраченное на обработку входящих данных.

Чтобы узнать, сколько времени требуется браузеру на обработку различных страниц, используйте следующий код:

```
browserTimings | summarize avg(networkDuration), avg(processingDuration), avg(totalDuration) by name 
```

Чтобы установить популярность различных браузеров, используйте следующий код:

```
pageViews | summarize count() by client_Browser
```

Чтобы связать просмотры страниц с вызовами AJAX, выполните объединение с зависимостями:

```
pageViews | join (dependencies) on operation_Id 
```

## <a name="trackrequest"></a>TrackRequest (Отслеживание запросов)
TrackRequest используется в серверном пакете SDK, чтобы регистрировать HTTP-запросы.

Его можно также вызвать самостоятельно, чтобы смодулировать запросы в контексте, в котором отсутствует выполняющийся модуль веб-службы.

Мы рекомендуем отправлять телеметрию запроса, выступающего в качестве <a href="#operation-context">контекста операции</a>.

## <a name="operation-context"></a>Контекст операции
Элементы телеметрии можно связать между собой, присвоив им общий идентификатор операции. Стандартный модуль отслеживания запросов делает это для исключений и других событий, отправляемых во время обработки HTTP-запроса. В [службе поиска](app-insights-diagnostic-search.md) и [службе аналитики](app-insights-analytics.md) с помощью этого идентификатора можно легко найти все события, связанные с запросом.

Самый простой способ задать этот идентификатор — установить контекст операции с помощью приведенного ниже шаблона.

*C#*

```C#

    // Establish an operation context and associated telemetry item:
    using (var operation = telemetry.StartOperation<RequestTelemetry>("operationName"))
    {
        // Telemetry sent in here will use the same operation ID.
        ...
        telemetry.TrackEvent(...); // or other Track* calls
        ...
        // Set properties of containing telemetry item--for example:
        operation.Telemetry.ResponseCode = "200";

        // Optional: explicitly send telemetry item:
        telemetry.StopOperation(operation);

    } // When operation is disposed, telemetry item is sent.
```

Наряду с определением контекста операции `StartOperation` создает элемент телеметрии указанного вами типа и отправляет его при удалении операции или при явном вызове `StopOperation`. При использовании типа телеметрии `RequestTelemetry` ее длительность задается как временной интервал между запуском и остановкой.

Контексты операции не могут быть вложенными. Если контекст операции уже существует, то его идентификатор связан со всеми вложенными элементами, включая элемент, созданный с помощью `StartOperation`.

В колонке поиска контекст операции используется для создания списка **связанных элементов**.

![Связанные элементы](./media/app-insights-api-custom-events-metrics/21.png)

### <a name="requests-in-analytics"></a>Запросы в аналитике 

В [службе аналитики Application Insights](app-insights-analytics.md) запросы приводятся в таблице `requests`.

Если действует [выборка](app-insights-sampling.md), свойство itemCount будет иметь значение больше 1. Например, itemCount==10 означает, что из 10 вызовов trackRequest() процесс выборки передал только один. Чтобы получить правильное число запросов и среднюю длительность, разбитую по именам запросов, используйте следующий код:

```AIQL
requests | summarize count = sum(itemCount), avgduration = avg(duration) by name
```


## <a name="trackexception"></a>TrackException (Отслеживание исключений)
Отправляйте исключения в Application Insights, чтобы:

* [определить их количество](app-insights-metrics-explorer.md) (для указания на частоту возникновения проблемы);
* [проверить отдельные значения.](app-insights-diagnostic-search.md)

Отчеты содержат данные по трассировкам стеков.

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

* ASP.NET: [написание кода для перехвата исключений](app-insights-asp-net-exceptions.md).
* J2EE: [исключения перехватываются автоматически](app-insights-java-get-started.md#exceptions-and-request-failures).
* JavaScript: исключения перехватываются автоматически. Если вы хотите отключить автоматический сбор, добавьте строку в фрагмент кода, который вставляется на веб-страницы.

    ```
    ({
      instrumentationKey: "your key"
      , disableExceptionTracking: true
    })
    ```

### <a name="exceptions-in-analytics"></a>Исключения в службе аналитики

В [службе аналитики Application Insights](app-insights-analytics.md) исключения приводятся в таблице `exceptions`.

Если действует [выборка](app-insights-sampling.md), свойство itemCount будет иметь значение больше 1. Например, itemCount==10 означает, что из 10 вызовов trackException() процесс выборки передал только один. Чтобы получить правильное число исключений, разбитое по типам исключений, используйте следующий код:

```
exceptions | summarize sum(itemCount) by type
```

Большая часть важных сведений о стеке уже извлечена в отдельные переменные, однако вы можете разобрать структуру details, чтобы получить дополнительные сведения. Так как это динамическая структура, результат следует привести к требуемому типу. Например:

```AIQL
exceptions
| extend method2 = tostring(details[0].parsedStack[1].method)
```

Чтобы связать исключения с соответствующими запросами, используйте соединение:

```
exceptions
| join (requests) on operation_Id 
```

## <a name="tracktrace"></a>TrackTrace (Отслеживание трассировки)
Используйте TrackTrace для диагностики неполадок, отправляя навигационную цепочку в Application Insights. Вы можете отправлять блоки диагностических данных и проверять их с помощью [поиска по журналу диагностики](app-insights-diagnostic-search.md).

[Адаптеры журнала](app-insights-asp-net-trace-logs.md) используют этот API для отправки журналов сторонних производителей на портал.

*C#*

    telemetry.TrackTrace(message, SeverityLevel.Warning, properties);


Вы можете выполнять поиск содержимого сообщения, но (в отличие от значений свойств) не можете фильтровать его.

Ограничения по размеру `message` гораздо выше, чем ограничение для свойств.
Преимуществом TrackTrace является возможность добавления в сообщения относительно длинных данных,  например данных POST.  

Кроме того, вы можете настроить для сообщения уровень серьезности. Как и для других данных телеметрии, вы можете добавлять значения свойства, используемые для фильтрации или поиска различных наборов трассировки. Например:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

В колонке [Поиск](app-insights-diagnostic-search.md) вы сможете легко отфильтровать все сообщения с определенной степенью серьезности, относящиеся к определенной базе данных.


### <a name="traces-in-analytics"></a>Трассировки в службе аналитики

В [службе аналитики Application Insights](app-insights-analytics.md) вызовы TrackTrace приводятся в таблице `traces`.

Если действует [выборка](app-insights-sampling.md), свойство itemCount будет иметь значение больше 1. Например, itemCount==10 означает, что из 10 вызовов trackTrace() процесс выборки передал только один. Поэтому, чтобы получить правильное количество вызовов трассировки, следует использовать такой код, как `traces | summarize sum(itemCount)`.

## <a name="trackdependency"></a>TrackDependency (Отслеживание зависимостей)
Используйте вызов TrackDependency для отслеживания времени отклика и процента успешных попыток вызовов во внешнюю часть кода. Результаты отображаются в диаграммах зависимостей на портале.

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

Помните, что серверные пакеты SDK включают [модуль зависимостей](app-insights-asp-net-dependencies.md), который автоматически обнаруживает и отслеживает определенные вызовы зависимостей, например вызовы к базам данных и интерфейсам REST API. Для работы модуля необходимо установить агент на сервере. Используйте этот вызов, если необходимо отследить вызовы, которые не отслеживаются автоматически, или если вы не хотите устанавливать агент.

Чтобы отключить стандартный модуль отслеживания зависимостей, измените файл [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) и удалите ссылку на `DependencyCollector.DependencyTrackingTelemetryModule`.

### <a name="dependencies-in-analytics"></a>Зависимости в службе аналитики

В [службе аналитики Application Insights](app-insights-analytics.md) вызовы trackDependency приводятся в таблице `dependencies`.

Если действует [выборка](app-insights-sampling.md), свойство itemCount будет иметь значение больше 1. Например, itemCount==10 означает, что из 10 вызовов trackDependency() процесс выборки передал только один. Чтобы получить правильное число зависимостей, разбитое по конечным компонентам, используйте следующий код:

```
dependencies | summarize sum(itemCount) by target
```

Чтобы связать зависимости с соответствующими запросами, используйте соединение:

```
dependencies
| join (requests) on operation_Id 
```

## <a name="flushing-data"></a>Очистка данных
Обычно пакет SDK отправляет данные в выбранный момент времени, чтобы свести влияние на пользователя к минимуму. Однако в некоторых случаях может потребоваться очистить буфер, например, при использовании пакета SDK в приложении, которое завершает работу.

*C#*

    telemetry.Flush();

    // Allow some time for flushing before shutdown.
    System.Threading.Thread.Sleep(1000);

Обратите внимание, что для [канала телеметрии сервера](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) эта функция является асинхронной.

## <a name="authenticated-users"></a>Прошедшие проверку пользователи
В веб-приложении пользователи по умолчанию идентифицируются файлами cookie. Пользователь может быть учтен более одного раза при доступе к приложению с другого компьютера или браузера либо при удалении файлов cookie.

Если пользователи входят в ваше приложение, более точное их количество можно узнать, указав идентификатор пользователя, прошедшего аутентификацию, в коде браузера.

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
                   .Replace("\\", "\\\\")"
                   .replace(/[,;=| ]+/g, "_"));
            </script>
        }

Нет необходимости использовать фактическое учетное имя пользователя. Нужен только уникальный идентификатор этого пользователя. В нем не должно быть пробелов или символов `,;=|`.

Идентификатор пользователя также задается в файле cookie сеанса и отправляется на сервер. Если установлен серверный пакет SDK, идентификатор пользователя, прошедшего аутентификацию, отправляется как часть свойств контекста телеметрии клиента и сервера. Затем можно выполнить фильтрацию и поиск.

Если приложение группирует пользователей по учетным записям, вы также можете передать идентификатор учетной записи (с аналогичными ограничениями знаков).

      appInsights.setAuthenticatedUserContext(validatedId, accountId);

В [обозревателе метрик](app-insights-metrics-explorer.md) можно создать диаграмму для отображения количества **пользователей, прошедших проверку подлинности**, а также количества **учетных записей пользователей**.

Кроме того, можно [выполнить поиск](app-insights-diagnostic-search.md) точек данных клиентов с определенными именами пользователей и учетными записями.

## <a name="properties"></a>Фильтрация, поиск и сегментация данных с помощью свойств
Вы можете прикрепить свойства и результаты измерений к своим событиям, а также метрикам, просмотрам страниц, исключениям и другим данным телеметрии.

*Свойства* — это строковые значения, которые можно использовать для фильтрации телеметрии в отчетах об использовании. Например, если приложение содержит несколько игр, можно к каждому событию присоединять имя игры, чтобы видеть, какие игры наиболее популярны.

В каждой строке может отображаться до 8192 событий. (Если вы хотите отправлять большие блоки данных, используйте параметр сообщения [TrackTrace](#track-trace).)

*Метрики* являются числовыми значениями, которые могут быть представлены в графическом виде. Например, вам нужно будет посмотреть, постепенно ли увеличиваются зарабатываемые игроками очки. Графы можно сегментировать по свойствам, отправленным с событием, чтобы получать отдельные графы или набор граф для разных игр.

Чтобы значения метрик отображались правильно, они должны быть больше или равны 0.

Количество используемых [свойств, значений свойств и метрик ограничено](#limits) .

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


*Visual Basic*

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


> [!NOTE]
> Постарайтесь не указывать в свойствах личные сведения.
>
>

*При использовании метрик* откройте обозреватель метрик и выберите метрику из группы **Custom** (Пользовательские).

![Откройте обозреватель метрик, выделите диаграмму и выберите метрику](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

> [!NOTE]
> Если ваша метрика не отображается или заголовок **Custom** (Пользовательские) отсутствует, закройте колонку выбранной метрики и повторите попытку позже. Иногда для вычисления метрик в конвейере может потребоваться час.

*При использовании свойств и метрик*сегментируйте метрики по свойствам:

![Задайте группировку, а затем выберите свойство в списке "Группировать по"](./media/app-insights-api-custom-events-metrics/04-segment-metric-event.png)

*В колонке поиска по журналу диагностики*можно просматривать свойства и метрики, щелкая отдельные вхождения события.

![Выберите экземпляр, а затем выберите "..."](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-4.png)

Используйте поле **поиска** для просмотра вхождений события с определенным значением свойства.

![Введите слово в поле поиска](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-5.png)

[Дополнительные сведения о выражениях поиска.](app-insights-diagnostic-search.md)

### <a name="alternative-way-to-set-properties-and-metrics"></a>Альтернативный способ настройки свойств и метрик
Для удобства вы можете собирать параметры события в отдельный объект:

    var event = new EventTelemetry();

    event.Name = "WinGame";
    event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
    event.Properties["game"] = currentGame.Name;
    event.Properties["difficulty"] = currentGame.Difficulty;
    event.Metrics["Score"] = currentGame.Score;
    event.Metrics["Opponents"] = currentGame.Opponents.Length;

    telemetry.TrackEvent(event);

> [!WARNING]
> Не используйте один и тот же экземпляр элемента телеметрии (в этом примере `event`) для многократного вызова Track*(). Это может привести к отправке телеметрии с неверной конфигурацией.
>
>

### <a name="custom-measurements-and-properties-in-analytics"></a>Пользовательские измерения и свойства в службе аналитики

В [службе аналитики](app-insights-analytics.md) пользовательские метрики и свойства приводятся в атрибутах `customMeasurements` и `customDimensions` каждой записи телеметрии.

Например, если вы добавили свойство game в телеметрию запросов, следующий запрос будет подсчитывать число различных значений свойства game и показывать среднее значение пользовательской метрики score:

```
requests
| summarize sum(itemCount), avg(todouble(customMeasurements.score)) by tostring(customDimensions.game) 
```

Обратите внимание на указанные ниже моменты.

* При извлечении значения из JSON customDimensions или customMeasurements оно имеет динамический тип, поэтому его необходимо привести к `tostring` или `todouble`.
* С учетом возможности [выборки](app-insights-sampling.md) следует использовать `sum(itemCount)`, а не `count()`.



## <a name="timed"></a> События времени
Иногда требуется отобразить на диаграмме продолжительность выполнения действия. Например, может потребоваться определить, сколько времени нужно пользователям для выбора решений в игре. Для этого можно использовать параметр измерения.

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
Если для некоторых создаваемых пользовательских событий требуется задать значения свойств по умолчанию, можно сделать это в экземпляре TelemetryClient. Они прикреплены к каждому элементу телеметрии, отправляемой из этого клиента.

*C#*

    using Microsoft.ApplicationInsights.DataContracts;

    var gameTelemetry = new TelemetryClient();
    gameTelemetry.Context.Properties["Game"] = currentGame.Name;
    // Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame");

*Visual Basic*

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

*Для веб-клиентов JavaScript*[используйте инициализаторы телеметрии JavaScript](#js-initializer).

*Чтобы добавить свойства для всей телеметрии*, включая данные из модулей стандартной коллекции, [реализуйте `ITelemetryInitializer`](app-insights-api-filtering-sampling.md#add-properties).

## <a name="sampling-filtering-and-processing-telemetry"></a>Выборка, фильтрация и обработка данных телеметрии
Для обработки телеметрии перед отправкой из пакета SDK можно написать код. Будут обрабатываться данные, отправляемые из модулей стандартной телеметрии, таких как коллекция запросов HTTP и коллекция зависимостей.

[Добавьте свойства](app-insights-api-filtering-sampling.md#add-properties) в телеметрию, реализовав `ITelemetryInitializer`. Например, можно добавить номера версий или значения, вычисленные на основе других свойств.

Используя [фильтрацию](app-insights-api-filtering-sampling.md#filtering), можно изменить или отменить телеметрию перед ее отправкой из пакета SDK. Для этого реализуйте `ITelemetryProcesor`. Вы сможете управлять тем, какие данные отправляются, а какие отклоняются. Однако при этом необходимо учитывать влияние на метрики. В зависимости от способа отклонения элементов можно потерять возможность перехода между связанными элементами.

[Выборка](app-insights-api-filtering-sampling.md) представляет собой упакованное решение для сокращения объема данных, отправляемых из приложения на портал. На отображаемые метрики выборка не влияет. Возможность диагностировать проблемы путем перехода между связанными элементами, такими как исключения, запросы и просмотры страниц, также не затрагивается.

[Подробнее](app-insights-api-filtering-sampling.md).

## <a name="disabling-telemetry"></a>Отключение телеметрии
Чтобы *динамически остановить и запустить* сбор и передачу данных телеметрии:

*C#*

```C#

    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```

Чтобы *отключить выбранные стандартные сборщики*, например счетчики производительности, HTTP-запросы или зависимости, удалите или закомментируйте соответствующие строки в файле [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Это можно сделать, если вы, например, хотите отправить собственные данные TrackRequest.

## <a name="debug"></a>Режим разработчика
Во время отладки полезно передавать телеметрию через конвейер, чтобы результаты можно было увидеть немедленно. Кроме того, вы можете получать дополнительные сообщения, которые помогают трассировать любые проблемы с телеметрией. Отключите этот режим в рабочей среде, так как он может замедлить работу приложения.

*C#*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;

*Visual Basic*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True


## <a name="ikey"></a> Установка ключа инструментирования для выбранной пользовательской телеметрии
*C#*

    var telemetry = new TelemetryClient();
    telemetry.InstrumentationKey = "---my key---";
    // ...


## <a name="dynamic-ikey"></a> Динамический ключ инструментирования
Во избежание смешивания телеметрии из среды разработки, тестовой и рабочей среды вы можете [создавать отдельные ресурсы Application Insights](app-insights-create-new-resource.md) и менять их ключи в зависимости от среды.

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



На веб-странице может потребоваться задать его, используя состояние веб-сервера, а не внедрить его в сценарий. Например, на веб-странице, созданной в приложении ASP.NET.

*JavaScript в Razor*

    <script type="text/javascript">
    // Standard Application Insights webpage script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      @Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="telemetrycontext"></a>Класс TelemetryContext
Экземпляр TelemetryClient включает свойство Context, содержащее несколько значений, которые отправляются вместе со всеми данными телеметрии. Как правило, их задают модули стандартной телеметрии, но их также можно задать самостоятельно. Например:

    telemetry.Context.Operation.Name = "MyOperationName";

Если задать эти значения самостоятельно, мы советуем удалить соответствующую строку из файла [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), чтобы не перепутать собственные значения и стандартные значения.

* **Component:** приложение и его версия.
* **Device:** данные об устройстве, на котором выполняется приложение. (В веб-приложениях это сервер или клиентское устройство, с которых отправляется телеметрия.)
* **InstrumentationKey:** ресурс Application Insights в Azure, в котором будет отображаться телеметрия. Обычно этот ресурс получают из файла ApplicationInsights.config.
* **Location:** географическое расположение устройства.
* **Operation:** текущий HTTP-запрос в веб-приложениях. В приложениях других типов для этого значения можно задать значение "Группировать события совместно".
  * **Id:** созданное значение, которое сопоставляет различные события, чтобы при проверке любого события в поиске по журналу диагностики можно было найти связанные элементы.
  * **Name**: идентификатор, обычно URL-адрес HTTP-запроса.
  * **SyntheticSource:** если эта строка не пустая и не имеет значение null, она означает, что источник запроса был определен как программа-робот или веб-тест. По умолчанию она будет исключена из вычислений в обозревателе метрик.
* **Properties:** свойства, которые отправляются со всеми данными телеметрии. Это значение можно переопределить в отдельных вызовах Track*.
* **Session:** сеанс пользователя. Для Id задается созданное значение, которое изменяется, если пользователь был неактивным в течение некоторого времени.
* **User:** информация о пользователе.

## <a name="limits"></a>Ограничения
[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

Чтобы избежать превышения ограничения на скорость передачи данных, используйте [выборку](app-insights-sampling.md).

Сведения о том, как определить, как долго хранятся данные, см. в статье [Сбор и хранение данных в Application Insights](app-insights-data-retention-privacy.md).

## <a name="reference-docs"></a>Справочная документация
* [Справочник по ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)
* [Справочник по Java](http://dl.windowsazure.com/applicationinsights/javadoc/)
* [Справочник по JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
* [Android SDK](https://github.com/Microsoft/ApplicationInsights-Android)
* [iOS SDK](https://github.com/Microsoft/ApplicationInsights-iOS)

## <a name="sdk-code"></a>Код пакета SDK
* [Базовый пакет SDK для ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [Пакеты Windows Server](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [Пакет SDK для Java](https://github.com/Microsoft/ApplicationInsights-Java)
* [Пакет SDK для JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)
* [Все платформы](https://github.com/Microsoft?utf8=%E2%9C%93&query=applicationInsights)

## <a name="questions"></a>Вопросы
* *Какие исключения могут создаваться при вызовах Track_()?*

    Отсутствует. Вам не нужно помещать их в предложения try-catch. Если пакет SDK сталкивается с проблемами, он добавляет в журнал сообщения, которые отображаются в консоли отладки и, если сообщения проходят, — в поиске по журналу диагностики.
* *Существует ли REST API для получения данных из портала?*

    Да, [API доступа к данным](https://dev.applicationinsights.io/). К другим способам извлечения данных относятся [экспорт из Analytics в Power BI](app-insights-export-power-bi.md) и [непрерывный экспорт](app-insights-export-telemetry.md).

## <a name="next"></a>Дальнейшие действия
* [Поиск в Application Insights](app-insights-diagnostic-search.md)

* [Устранение неполадок](app-insights-troubleshoot-faq.md)



