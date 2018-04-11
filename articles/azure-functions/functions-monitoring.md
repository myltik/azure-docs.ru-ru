---
title: Мониторинг Функций Azure
description: Сведения об использовании Azure Application Insights с решением "Функции Azure" для мониторинга выполнения функций.
services: functions
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: функции azure, функции, обработка событий, веб-перехватчики, динамические вычисления, независимая архитектура
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/15/2017
ms.author: tdykstra
ms.openlocfilehash: 5b141924266630bfd3b63ec5129f9f225da3170b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="monitor-azure-functions"></a>Мониторинг Функций Azure

## <a name="overview"></a>Обзор 

Решение [Функции Azure](functions-overview.md) предлагает встроенную интеграцию со службой [Azure Application Insights](../application-insights/app-insights-overview.md) для мониторинга функций. В этой статье показано, как настроить отправку данных телеметрии из Функций в Application Insights.

![Обозреватель метрик в Application Insights](media/functions-monitoring/metrics-explorer.png)

Функции также содержат встроенные средства для мониторинга, в которых не используется Application Insights. Мы рекомендуем использовать службу Application Insights, которая предоставляет дополнительные данные и больше возможностей для их анализа. Сведения о встроенных средствах мониторинга см. в [последнем разделе этой статьи](#monitoring-without-application-insights).

## <a name="enable-application-insights-integration"></a>Включение интеграции с Application Insights

Для отправки данных в Application Insights приложению-функции требуется ключ инструментирования для экземпляра Application Insights. Вы можете создать такое подключение на [портале Azure](https://portal.azure.com) двумя способами:

* [создание подключенного экземпляра Application Insights одновременно с созданием приложения-функции](#new-function-app);
* [подключение экземпляра Application Insights к существующему приложению-функции](#existing-function-app).

### <a name="new-function-app"></a>Новое приложение-функция

Включите Application Insights на странице **Создать** для приложения-функции, выполнив следующие действия.

1. Установите переключатель **Application Insights** в состояние **Включено**.

2. Выберите **расположение Application Insights**.

   ![Включение Application Insights при создании приложения-функции](media/functions-monitoring/enable-ai-new-function-app.png)

### <a name="existing-function-app"></a>Существующее приложение-функция

Получите ключ инструментирования и сохраните его в приложении-функции, сделав следующее:

1. Создайте экземпляр Application Insights. Задайте тип приложения **Общие**.

   ![Создание экземпляра Application Insights с типом "Общие"](media/functions-monitoring/ai-general.png)

2. Скопируйте ключ инструментирования на странице **Основные компоненты** в экземпляре Application Insights. Наведите указатель мыши на конец отображаемого значения ключа, чтобы появилась кнопка **Щелкните, чтобы скопировать**.

   ![Копирование ключа инструментирования Application Insights](media/functions-monitoring/copy-ai-key.png)

1. На странице приложения-функции **Параметры приложения** [добавьте параметр приложения](functions-how-to-use-azure-function-app-settings.md#settings), выбрав **Add new setting** (Добавление нового параметра). Присвойте имя новому параметру APPINSIGHTS_INSTRUMENTATIONKEY и вставьте скопированный ключ инструментирования.

   ![Добавление ключа инструментирования в настройках приложения](media/functions-monitoring/add-ai-key.png)

1. Выберите команду **Сохранить**.

## <a name="disable-built-in-logging"></a>Отключение встроенного ведения журнала

При включении Application Insights рекомендуется отключить [встроенное ведение журнала, которое использует хранилище Azure](#logging-to-storage). Встроенное ведение журнала полезно для тестирования с легкими рабочими нагрузками, но не предназначено для использования в рабочей среде с высокой нагрузкой. Для мониторинга рабочей среды рекомендуем использовать Application Insights. Если встроенное ведение журнала используется в рабочей среде, записи журнала могут быть неполными из-за регулирования службы хранилища Azure.

Чтобы отключить встроенное ведение журнала, удалите параметр приложения `AzureWebJobsDashboard`. Дополнительные сведения о том, как удалять параметры приложения на портале Azure, см. в разделе **Параметры приложения** статьи [Управление приложением-функцией на портале Azure](functions-how-to-use-azure-function-app-settings.md#settings).

При включении Application Insights и отключении встроенного ведения журнала с вкладки **Монитор** для функции на портале Azure вы перейдете в Application Insights.

## <a name="view-telemetry-data"></a>Просмотр данных телеметрии

Чтобы перейти к подключенному экземпляру Application Insights из приложения-функции на портале, щелкните ссылку **Application Insights** на странице **Обзор** для приложения-функции.

Дополнительные сведения об использовании Application Insights см. в [документации по Application Insights](https://docs.microsoft.com/azure/application-insights/). В этом разделе представлено несколько примеров просмотра данных в Application Insights. Если вы уже знакомы с Application Insights, можете сразу переходить к [разделам, посвященным конфигурации и настройке данных телеметрии](#configure-categories-and-log-levels).

В [обозревателе метрик](../application-insights/app-insights-metrics-explorer.md) можно создавать диаграммы и оповещения на основе таких метрик, как количество вызовов функции, время выполнения и процент успешных операций.

![Обозреватель метрик](media/functions-monitoring/metrics-explorer.png)

На вкладке [Сбои](../application-insights/app-insights-asp-net-exceptions.md) можно создавать диаграммы и оповещения на основе сбоев функции и исключений сервера. **Имя операции** обозначает имя функции. Сбои в зависимостях здесь не отображаются, если вы не настроили для зависимостей [пользовательскую телеметрию](#custom-telemetry-in-c-functions).

![Сбои](media/functions-monitoring/failures.png)

На вкладке [Производительность](../application-insights/app-insights-performance-counters.md) вы можете анализировать проблемы производительности.

![Производительность](media/functions-monitoring/performance.png)

Вкладка **Серверы** отображает использование ресурсов и пропускную способность для каждого сервера. Эти данные можно использовать для отладки в тех случаях, когда функции создают чрезмерную нагрузку на базовые ресурсы. Серверы здесь называются **экземплярами облачных ролей**.

![Серверы](media/functions-monitoring/servers.png)

Вкладка [Live Metrics Stream](../application-insights/app-insights-live-stream.md) отображает создаваемые данные метрик в режиме реального времени.

![Live Stream](media/functions-monitoring/live-stream.png)

## <a name="query-telemetry-data"></a>Запросы к данным телеметрии

[Аналитика в Application Insights](../application-insights/app-insights-analytics.md) предоставляет доступ ко всем данным телеметрии в формате таблиц в базе данных. Функция аналитики поддерживает язык запросов для извлечения, обработки и визуализации данных.

![Выбор функции аналитики](media/functions-monitoring/select-analytics.png)

![Пример данных аналитики](media/functions-monitoring/analytics-traces.png)

Ниже приведен пример запроса. Он демонстрирует распределение запросов по рабочим ролям за последние 30 минут.

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

Все доступные таблицы представлены на вкладке **Схема** в левой области. Данные, создаваемые при вызовах функций, вы найдете в следующих таблицах:

* **traces** — журналы, созданные средой выполнения и кодом функций;
* **requests** — по одному запросу для каждого вызова функции;
* **exceptions** — любые исключения в среде выполнения;
* **customMetrics** — число успешных и неудачных вызовов, доля успешных попыток, длительность;
* **customEvents** — события, отслеживаемые средой выполнения, например HTTP-запросы, которые активируют функции;
* **performanceCounters** — сведения о производительности серверов, на которых выполняются функции.

Остальные таблицы предназначены для проверок доступности, а также телеметрии клиента и (или) браузера. Вы можете реализовать пользовательскую телеметрию, чтобы добавлять в них данные.

В каждой таблице есть поле `customDimensions`, где хранится часть данных о конкретной функции.  Например, следующий запрос получает все трассировки с уровнем журнала `Error`.

```
traces 
| where customDimensions.LogLevel == "Error"
```

Среда выполнения предоставляет `customDimensions.LogLevel` и `customDimensions.Category`. Вы можете указать дополнительные поля в журналах, которые указываются в коде функции. См. раздел [Структурированное ведение журнала](#structured-logging) далее в этой статье.

## <a name="configure-categories-and-log-levels"></a>Настройка категорий и уровней ведения журнала

Вы можете использовать Application Insights, не изменяя настроек по умолчанию, но в этом случае может создаваться чрезмерный объем данных. Если вы используете подписку Azure для Visual Studio, объем данных Application Insights может достигнуть установленного верхнего предела. В оставшейся части этой статьи мы покажем, как настроить данные, отправляемые функциями в Application Insights.

### <a name="categories"></a>Категории

В средстве ведения журнала Функций Azure предусмотрена *категория* для каждого журнала. Категория указывает, какая часть кода среды выполнения или кода функции записывала данные в этот журнал. 

Среда выполнения Функций Azure создает журналы, название категории для которых начинается со слова Host. Например, журналы function started, function executed и function completed имеют категорию Host.Executor. 

Если вы сохраняете данные в журнал из кода функции, ему присваивается категория Function.

### <a name="log-levels"></a>Уровни журнала

В средстве ведения журнала Функций Azure также предусмотрен *уровень ведения* каждого журнала. Параметр [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel#Microsoft_Extensions_Logging_LogLevel) (Уровень ведения журнала) является перечислением целочисленных значений, которые обозначают относительную важность.

|LogLevel    |Код|
|------------|---|
|Трассировка       | 0 |
|Отладка       | 1 |
|Информация | 2 |
|Предупреждение     | 3 |
|Ошибка       | 4. |
|критические ошибки.    | 5 |
|None        | 6 |

Уровень ведения журнала `None` описан в следующем разделе. 

### <a name="configure-logging-in-hostjson"></a>Настройка ведения журналов в host.json

Файл *host.json* определяет, какой объем информации приложение-функция отправляет в журнал Application Insights. В каждой категории вы можете указать минимальный уровень ведения журнала для отправки данных. Ниже приведен пример:

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

В этом примере настраиваются следующие правила:

1. Для журналов с категорией Host.Results или Function в Application Insights отправляются только данные с уровнем `Error` и выше. Данные журнала с уровнем `Warning` и ниже игнорируются.
2. Для журналов с категорией Host.Aggregator в Application Insights отправляются только данные с уровнем `Information` и выше. Данные журнала с уровнем `Debug` и ниже игнорируются.
3. Для всех остальных журналов в Application Insights отправляются данные с уровнем `Information` и выше.

Значение категории в *host.json* управляет ведением журнала для всех категорий, название которых начинается с аналогичного значения. Например, значение Host в *host.json* управляет ведением журнала для Host.General, Host.Executor, Host.Results и т. д.

Если *host.json* содержит несколько категорий с одинаковым началом строки, сопоставление начинается с более длинных строк. Предположим, вы хотите регистрировать все данные среды выполнения, кроме данных категории Host.Aggregator, на уровне `Information`, а данные категории Host.Aggregator — на уровне `Error`.

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

Чтобы не вести журналы для определенной категории, установите для нее уровень ведения журнала `None`. Теперь для этой категории не будут сохраняться никакие журналы, так как более высокий уровень ведения журнала не существует.

В следующих разделах описаны основные категории журналов, создаваемых средой выполнения. 

### <a name="category-hostresults"></a>Категория Host.Results

Эти журналы отображаются в Application Insights как "запросы" (requests). Они содержат сведения об успешном выполнении или сбое функций.

![Диаграмма запросов](media/functions-monitoring/requests-chart.png)

Все эти журналы ведутся на уровне `Information`, поэтому никакие данные при значении фильтра `Warning` или выше не отобразятся.

### <a name="category-hostaggregator"></a>Категория Host.Aggregator

Эти журналы содержат счетчики и средние значения по вызовам функций за [настраиваемый](#configure-the-aggregator) период времени. По умолчанию используется период 30 секунд или 1000 результатов в зависимости от того, что из этого наступит раньше. 

Журналы доступны в таблице **customMetrics** в Application Insights. Например, здесь вы найдете число запусков, долю успешных попыток и длительность выполнения.

![Запрос по customMetrics](media/functions-monitoring/custom-metrics-query.png)

Все эти журналы ведутся на уровне `Information`, поэтому никакие данные при значении фильтра `Warning` или выше не отобразятся.

### <a name="other-categories"></a>Другие категории

Все журналы для категорий, кроме перечисленных выше, доступны в таблице **трассировок** в Application Insights.

![Запрос по трассировкам](media/functions-monitoring/analytics-traces.png)

Все журналы с категориями, имена которых начинаются со слова Host, сохраняются средой выполнения Функций Azure. Журналы function started, function executed и function completed имеют категорию Host.Executor. Успешные выполнения сохраняются в этих журналах на уровне `Information`, а исключения — на уровне `Error`. Кроме того, среда выполнения создает журналы уровня `Warning`, например очередь сообщений, отправленных в очередь подозрительных сообщений.

Журналы, сохраняемые в коде функций, имеют категорию Function и могут иметь любой уровень ведения журнала.

## <a name="configure-the-aggregator"></a>Настройка агрегатора

Как отмечалось в предыдущем разделе, среда выполнения собирает данные о выполнении функции за определенный период времени. По умолчанию используется период в 30 секунд или 1000 запусков в зависимости от того, что из этого наступит раньше. Этот параметр можно настроить в файле *host.json*.  Ниже приведен пример:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Настройка выборки

В Application Insights есть функция [выборки](../application-insights/app-insights-sampling.md), которая позволят избежать создания слишком большого объема данных телеметрии в периоды пиковой нагрузки. Если объем данных телеметрии превышает заданное значение, служба Application Insights будет случайным образом игнорировать часть поступающих элементов. Вы можете настроить выборку в файле *host.json*.  Ниже приведен пример:

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```

## <a name="write-logs-in-c-functions"></a>Запись журналов в функциях C#

В коде функции вы можете сохранять журналы, которые отображаются в виде трассировок в Application Insights.

### <a name="ilogger"></a>ILogger

Используйте параметр [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) в функциях вместо параметра `TraceWriter`. Журналы, созданные с помощью `TraceWriter`, тоже отправляются в Application Insights, но `ILogger` позволяют использовать [структурированное ведение журналов](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Объект `ILogger` позволяет вызывать для создания журналов [методы расширения ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loggerextensions#Methods_) `Log<level>`. Например, следующий код записывает журнал `Information` с категорией Function.

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Структурированное ведение журнала

Использование параметров в сообщении журнала определяется порядком заполнителей, а не их именами. Предположим, что у вас есть следующий код:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Если вы примените эту же строку сообщения с обратным порядком параметров, в тексте сообщения значения окажутся на неправильных местах.

Такой метод обработки заполнителей позволяет выполнять структурированное ведение журналов. Application Insights сохраняет не только строку сообщения, но и параметры в формате пар "имя — значение". Благодаря этому все аргументы сообщения становятся полями, по которым можно выполнять запросы.

Например, если используется метод ведения журнала из предыдущего примера, вы сможете отправить запрос к полю `customDimensions.prop__rowKey`. При этом добавляется префикс `prop__`, чтобы не возникало конфликтов между полями, которые добавляет среда выполнения и которые добавляет код вашей функции.

Исходную строку сообщения можно получить, указав в запросе поле `customDimensions.prop__{OriginalFormat}`.  

Ниже приведен пример JSON-представления для данных `customDimensions`.

```json
{
  customDimensions: {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="logging-custom-metrics"></a>Сохранение в журнале пользовательских метрик  

В функциях на языке C# вы можете использовать метод расширения `LogMetric` для `ILogger`, чтобы создать пользовательские метрики в Application Insights. Ниже приведен пример вызова метода.

```csharp
logger.LogMetric("TestMetric", 1234); 
```

Этот пример кода действует так же, как вызов `TrackMetric` с использованием [API Application Insights для .NET](#custom-telemetry-in-c-functions).

## <a name="write-logs-in-javascript-functions"></a>Ведение журналов в функциях JavaScript

В функциях Node.js для ведения журналов следует использовать `context.log`. Структурированное ведение журнала не используется.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="logging-custom-metrics"></a>Сохранение в журнале пользовательских метрик  

В функциях Node.js вы можете использовать метод `context.log.metric`, чтобы создать пользовательские метрики в Application Insights. Ниже приведен пример вызова метода.

```javascript
context.log.metric("TestMetric", 1234); 
```

Этот пример кода действует так же, как вызов `trackMetric` с использованием [пакета SDK Node.js для Application Insights](#custom-telemetry-in-javascript-functions).

## <a name="custom-telemetry-in-c-functions"></a>Пользовательские данные телеметрии в функциях C#

Вы можете использовать пакет NuGet [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) для отправки пользовательских данных телеметрии в Application Insights.

Ниже приведен пример кода C#, который использует [пользовательский API телеметрии](../application-insights/app-insights-api-custom-events-metrics.md). Пример приведен для библиотеки классов .NET, но код Application Insights в скрипте C# будет точно таким же.

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Azure.WebJobs;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Linq;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        private static string key = TelemetryConfiguration.Active.InstrumentationKey = 
            System.Environment.GetEnvironmentVariable(
                "APPINSIGHTS_INSTRUMENTATIONKEY", EnvironmentVariableTarget.Process);

        private static TelemetryClient telemetryClient = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;
         
            // Track an Event
            var evt = new EventTelemetry("Function called");
            UpdateTelemetryContext(evt.Context, context, name);
            telemetryClient.TrackEvent(evt);
            
            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            UpdateTelemetryContext(metric.Context, context, name);
            telemetryClient.TrackMetric(metric);
            
            // Track a Dependency
            var dependency = new DependencyTelemetry
                {
                    Name = "GET api/planets/1/",
                    Target = "swapi.co",
                    Data = "https://swapi.co/api/planets/1/",
                    Timestamp = start,
                    Duration = DateTime.UtcNow - start,
                    Success = true
                };
            UpdateTelemetryContext(dependency.Context, context, name);
            telemetryClient.TrackDependency(dependency);
            
            return name == null
                ? req.CreateResponse(HttpStatusCode.BadRequest, 
                    "Please pass a name on the query string or in the request body")
                : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
        }
        
        // This correllates all telemetry with the current Function invocation
        private static void UpdateTelemetryContext(TelemetryContext context, ExecutionContext functionContext, string userName)
        {
            context.Operation.Id = functionContext.InvocationId.ToString();
            context.Operation.ParentId = functionContext.InvocationId.ToString();
            context.Operation.Name = functionContext.FunctionName;
            context.User.Id = userName;
        }
    }    
}
```

Не вызывайте `TrackRequest` или `StartOperation<RequestTelemetry>`, ведь при этом будут отображаться повторные запросы на вызов функции.  Среда выполнения Функций Azure автоматически отслеживает запросы.

Не указывайте `telemetryClient.Context.Operation.Id`. Это глобальный параметр, указав который, можно вызвать неправильную корреляцию при одновременном выполнении многих функций. Вместо этого создайте экземпляр телеметрии (`DependencyTelemetry`, `EventTelemetry`) и измените его свойство `Context`. Затем передайте экземпляр телеметрии в соответствующий метод `Track` в `TelemetryClient` (`TrackDependency()`, `TrackEvent()`). Это гарантирует правильность сведений о корреляции телеметрии для текущего вызова функции.

## <a name="custom-telemetry-in-javascript-functions"></a>Пользовательские данные телеметрии в функциях JavaScript

[Пакет SDK Node.js для Application Insights](https://www.npmjs.com/package/applicationinsights) сейчас доступен в бета-версии. Ниже приведен пример кода, который отправляет пользовательские данные телеметрии в Application Insights.

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    client.trackEvent({name: "my custom event", tagOverrides:{"ai.operation.id": context.invocationId}, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackTrace({message: "trace message", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:{"ai.operation.id": context.invocationId}});

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

Наборов параметров `tagOverrides` присваивает параметру `operation_Id` значение идентификатора вызова функции. Этот параметр позволяет сопоставлять все автоматически создаваемые и пользовательские данные телеметрии с конкретным вызовом функции.

## <a name="known-issues"></a>Известные проблемы

### <a name="dependencies"></a>Зависимости

Зависимости, которые имеют функции для других служб, не отображаются автоматически, но их можно отобразить, написав пользовательский код. Пример такого кода представлен в [разделе о пользовательских данных телеметрии на C#](#custom-telemetry-in-c-functions). Этот пример кода создает в Application Insights *карту приложения* следующего вида.

![Схема сопоставления приложений](media/functions-monitoring/app-map.png)

### <a name="report-issues"></a>Сообщение о проблемах

Чтобы сообщить о проблеме с интеграцией Функций Azure с Application Insights, внести предложение или отправить запрос, [создайте обращение в GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="monitoring-without-application-insights"></a>Мониторинг без использования Application Insights

Мы рекомендуем использовать для мониторинга функций службу Application Insights, которая предоставляет дополнительные данные и больше возможностей для их анализа. Но вы также можете найти журналы и данные телеметрии на страницах приложения-функции на портале Azure.

### <a name="logging-to-storage"></a>Ведение журналов в хранилище

Встроенное ведение журнала использует учетную запись хранения, указанную в строке подключения в параметре приложения `AzureWebJobsDashboard`. Если параметр приложения настроен, данные ведения журнала отображаются на портале Azure. В ресурсе хранилища перейдите к файлам, выберите службу файлов для функции, а затем перейдите к `LogFiles > Application > Functions > Function > your_function` для просмотра файла журнала. На странице приложения-функции выберите функцию, а затем выберите вкладку **Монитор**, и вы увидите список выполнений этой функции. Щелкнув конкретное выполнение, можно просмотреть его длительность, входные данные, ошибки и связанные файлы журнала.

При использовании Application Insights и [отключенном встроенном ведении журнала](#disable-built-in-logging) на вкладке **Монитор** происходит переход к Application Insights.

### <a name="real-time-monitoring"></a>Мониторинг в реальном времени

Вы можете настроить потоковую передачу файлов журнала в сеанс командной строки на локальной рабочей станции с помощью [Azure CLI 2.0](/cli/azure/install-azure-cli) или [Azure PowerShell](/powershell/azure/overview).  

Для Azure CLI 2.0 используйте следующие команды, чтобы войти, выбрать подписку и включить потоковую передачу файлов журнала:

```
az login
az account list
az account set <subscriptionNameOrId>
az appservice web log tail --resource-group <resource group name> --name <function app name>
```

Для Azure PowerShell используйте следующие команды, чтобы добавить учетную запись Azure, выбрать подписку и включить потоковую передачу файлов журнала:

```
PS C:\> Add-AzureAccount
PS C:\> Get-AzureSubscription
PS C:\> Get-AzureSubscription -SubscriptionName "<subscription name>" | Select-AzureSubscription
PS C:\> Get-AzureWebSiteLog -Name <function app name> -Tail
```

Дополнительные сведения см. в разделе [Практическое руководство. Потоковая передача журналов](../app-service/web-sites-enable-diagnostic-log.md#streamlogs).

### <a name="viewing-log-files-locally"></a>Просмотр файлов журнала в локальной среде

[!INCLUDE [functions-local-logs-location](../../includes/functions-local-logs-location.md)]

## <a name="next-steps"></a>Дополнительная информация

Для получения дополнительных сведений см. следующие ресурсы:

* [Application Insights](/azure/application-insights/)
* [Общие сведения о ведении журналов в ASP.NET Core](/aspnet/core/fundamentals/logging/)
