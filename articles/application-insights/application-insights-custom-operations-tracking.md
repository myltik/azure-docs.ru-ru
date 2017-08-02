---
title: "Отслеживание пользовательских операций с помощью пакета SDK .NET Azure Application Insights | Документы Майкрософт"
description: "Отслеживание пользовательских операций с помощью пакета SDK .NET Azure Application Insights"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 06/31/2017
ms.author: sergkanz
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: 883750c939aa3bd605189f513e5ce74642f91709
ms.contentlocale: ru-ru
ms.lasthandoff: 07/13/2017

---

# <a name="tracking-custom-operations-with-application-insights-net-sdk"></a>Отслеживание пользовательских операций с помощью пакета SDK .NET Application Insights

Пакеты SDK Application Insights автоматически отслеживают входящие HTTP-запросы и вызовы зависимых служб — HTTP-запросы, SQL-запросы и т. д. Используйте приложение, которое создано путем объединения нескольких микрослужб. Отслеживание и корреляция запросов и зависимостей позволяет контролировать скорость реагирования приложения в целом, а также надежность всех микрослужб. Мы собираемся постепенно расширять список и предоставить автоматический сбор сведений для других известных платформ. 

Существует класс шаблонов приложений, который в принципе не поддерживается. Для надлежащего мониторинга таких шаблонов требуется инструментирование кода вручную. В данной статье рассматривается ряд шаблонов, для которых может потребоваться такое инструментирование, включая обработку пользовательских очередей или выполнение длительных фоновых задач.

В данном документе представлены инструкции по организации отслеживания пользовательских операций с помощью пакета SDK ApplicationInsights.

Сведения в данном документе применимы к следующим версиям Application Insights:
- Версия Application Insights для .NET (базовый пакет SDK) `2.4+`
- Версия Application Insights для веб-приложений (выполнение ASP.NET) `2.4+`
- Application Insights для приложений ASP.NET Core `2.1+`

## <a name="overview"></a>Обзор
Под операцией мы понимаем некую логическую часть работы, выполняемую приложением. Она имеет имя, время запуска и длительность, контекст выполнения, такой как имя пользователя, свойства и результат. Если операция `A` была инициирована операцией `B`, то операция `B` задается в качестве родительской по отношению к `A`.  Операция может иметь только одну родительскую операцию и множество дочерних операций. Подробнее об операциях и корреляции данных телеметрии см. [здесь](application-insights-correlation.md).

В пакете SDK .NET Application Insights описывается абстрактным классом [OperationTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Core/Managed/Shared/Extensibility/Implementation/OperationTelemetry.cs) и его потомками [RequestTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Core/Managed/Shared/DataContracts/RequestTelemetry.cs) и [DependencyTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Core/Managed/Shared/DataContracts/DependencyTelemetry.cs).

## <a name="incoming-operations-tracking"></a>Отслеживание входящих операций 
Пакет SDK Application Insights автоматически собирает HTTP-запросы для приложений ASP.NET, которые выполняются в конвейере служб IIS, и всех приложений ASP.NET Core. Существуют решения для других платформ, поддержка которых осуществляется силами сообщества разработчиков. Однако если приложение не поддерживается ни одним стандартом или решением сообщества разработчиков, инструментировать его можно вручную.

Другой пример, в котором требуется настраиваемое отслеживание, — это рабочая роль, которую получают элементы из очереди. Для некоторых очередей вызов для добавления сообщения в эту очередь отслеживается как зависимость. Однако операции высокого уровня, описывающие обработку сообщения, не собираются автоматически.

Давайте посмотрим, как можно отслеживать такие операции.

На высоком уровне целью задачи является создание `RequestTelemetry` и задание известных свойств; после завершения операции целью является отслеживание данных телеметрии. В следующем примере как раз демонстрируется такая задача.

### <a name="http-request-in-owin-self-hosted-app"></a>HTTP-запрос в резидентном приложении Owin
Мы следовали инструкциям из статьи, посвященной [протоколу HTTP для корреляции](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md), поэтому следует ожидать, что будут получены заголовки, которые описаны в этой статье.

``` C#
public class ApplicationInsightsMiddleware : OwinMiddleware
{
    private readonly TelemetryClient telemetryClient = new TelemetryClient(TelemetryConfiguration.Active);
    
    public ApplicationInsightsMiddleware(OwinMiddleware next) : base(next) {}

    public override async Task Invoke(IOwinContext context)
    {
        // Let's create and start RequestTelemetry
        var requestTelemetry = new RequestTelemetry
        {
            Name = $"{context.Request.Method} {context.Request.Uri.GetLeftPart(UriPartial.Path)}"
        };

        // if there is Request-Id recevied from the upstream service, set telemetry context accordingly
        if (context.Request.Headers.ContainsKey("Request-Id"))
        {
            var requestId = context.Request.Headers.Get("Request-Id");
            // get the operation id from the Request-Id (if you follow the 'Http Protocol for Correlation')
            requestTelemetry.Context.Operation.Id = GetOperationId(requestId);
            requestTelemetry.Context.Operation.ParentId = requestId;
        }

        // StartOperation is a helper method that allows to correlate 
        // current operations with nested operations/telemetry
        // and initializes start time and duration on telemetry item
        var operation = telemetryClient.StartOperation(requestTelemetry);

        // process request
        try
        {
            await Next.Invoke(context);
        }
        catch (Exception e)
        {
            requestTelemetry.Success = false;
            telemetryClient.TrackException(e);
            throw;
        }
        finally
        {
            // update status code and success as appropriate
            if (context.Response != null)
            {
                requestTelemetry.ResponseCode = context.Response.StatusCode.ToString();
                requestTelemetry.Success = context.Response.StatusCode >= 200 && context.Response.StatusCode <= 299;
            }
            else
            {
                requestTelemetry.Success = false;
            }

            // now it's time to stop operation (and track telemetry)
            telemetryClient.StopOperation(operation);
        }
    }
    
    public static string GetOperationId(string id)
    {
        // returns root Id from the '|' to first '.' if any
        int rootEnd = id.IndexOf('.');
        if (rootEnd < 0)
            rootEnd = id.Length;

        int rootStart = id[0] == '|' ? 1 : 0;
        return id.Substring(rootStart, rootEnd - rootStart);
    }
}
```

Протокол HTTP для корреляции также объявляет заголовок `Correlation-Context`, однако для простоты мы опустили его.

## <a name="queue-instrumentation"></a>Инструментирование очереди
Для HTTP-соединений мы создали протокол для передачи сведений о корреляции. Некоторые протоколы очереди позволяют передавать дополнительные метаданные вместе с сообщением, а другие нет.

### <a name="service-bus-queue"></a>Очередь служебной шины
[Очередь служебной шины](../service-bus-messaging/index.md) позволяет передавать контейнер свойств вместе с сообщением и используется для передачи идентификатора корреляции.

Очередь служебной шины использует протоколы на основе TCP. Application Insights не предусматривает автоматическое отслеживание операций с очередями, поэтому мы отслеживаем их вручную. Операция вывода из очереди представляет собой API принудительного типа и в принципе не подлежит отслеживанию.

#### <a name="enqueue"></a>Постановка в очередь

```C#
public async Task Enqueue(string payload)
{
    // StartOperation is a helper method that initializes telemetry item
    // and allows to correlate this operation with its parent and children
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queueName);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queueName;

    var message = new BrokeredMessage(payload);
    // ServiceBus Queue allows to pass property bag along with the message
    // we will use them to pass our correlation identifiers (and other context)
    // to the consumer
    message.Properties.Add("ParentId", operation.Telemetry.Id);
    message.Properties.Add("RootId", operation.Telemetry.Context.Operation.Id);

    try
    {
        await queue.SendAsync(message);
        
        // set operation.Telemetry Success and ResponseCode here
        operation.Telemetry.Success = true;
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // set operation.Telemetry Success and ResponseCode here
        operation.Telemetry.Success = false;
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

#### <a name="process"></a>Process
```C#
public async Task Process(BrokeredMessage message)
{
    // once the message is taken from the queue, create ReqeustTelemetry to track its processing
    // it may also make sense to get name from the message
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };

    var rootId = message.Properties["RootId"].ToString();
    var parentId = message.Properties["ParentId"].ToString();
    // get the operation id from the Request-Id (if you follow the 'Http Protocol for Correlation')
    requestTelemetry.Context.Operation.Id = rootId;
    requestTelemetry.Context.Operation.ParentId = parentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // update status code and success as appropriate
        telemetryClient.StopOperation(operation);
    }
}
```

### <a name="azure-storage-queue"></a>Очередь службы хранилища Azure
В следующем примере показано, как отслеживать операции [очереди хранилища Azure](../storage/storage-dotnet-how-to-use-queues.md) и коррелировать данные телеметрии между производителем, потребителем и хранилищем Azure. 

Очередь хранилища Azure имеет API HTTP, и все вызовы в очередь отслеживаются ApplicationInsights DependencyCollector для HTTP-запросов.
Убедитесь, что у вас есть `Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer` в `applicationInsights.config`. При необходимости добавьте его программными средствами, как описано [здесь](app-insights-api-filtering-sampling.md).

При настройке ApplicationInsights вручную убедитесь, что вы создали и инициализировали `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule` так же, как указано ниже:
 
``` C#
DependencyTrackingTelemetryModule module = new DependencyTrackingTelemetryModule();

// you can prevent correlation header injection to some domains by adding it to the excluded list.
// Make sure you add Azure Storage endpoint, otherwise you may experience request signature validation issues on the Storage service side
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
module.Initialize(TelemetryConfiguration.Active);

// do not forget to dispose module during application shutdown
```

Можно также сопоставить идентификатор операции ApplicationInsights с RequestId хранилища Azure. Обратитесь к [этой статье](../storage/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing), чтобы узнать, как задать и получить клиент запроса к хранилищу и идентификатор запроса к хранилищу.

#### <a name="enqueue"></a>Постановка в очередь
Поскольку очереди хранилища Azure поддерживают API HTTP, все операции с очередью автоматически отслеживаются ApplicationInsights. Во многих случаях этого инструментария должно быть достаточно.
Однако для корреляции трассировок на стороне потребителя с трассировками производителя необходимо передать некоторый контекст корреляции, точно так же, как мы сделали это в разделе "Протокол HTTP для корреляции". 

В этом примере мы отслеживаем необязательную операцию `Enqueue`. Она позволяет
 - Сопоставить повторные попытки (если таковые имеются): все они имеют один общий родительский объект, операцию `Enqueue`. В противном случае они отслеживаются как дочерние элементы входящего запроса. Поэтому в случае нескольких логических запросов к очереди может оказаться затруднительным определить, какой вызов совершался повторно.
 - Сопоставить журналы хранилища Azure (при необходимости) с данными телеметрии ApplicationInsights.

Операция `Enqueue` является дочерней по отношению к "родительской" операции (например, входящий HTTP-запроса), а вызов зависимости "HTTP" является дочерним элементом по отношению к операции `Enqueue` и внучатым элементом входящего запроса.

```C#
public async Task Enqueue(CloudQueue queue, string message)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queue.Name);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queue.Name;

    // MessagePayload represents your custom message and also serializes correlation identifiers into payload
    // e.g. if you choose to pass payload serialized to json, it may look like
    // {'RootId' : 'some-id', 'ParentId' : '|some-id.1.2.3.', 'message' : 'your message to process'}
    var jsonPayload = JsonConvert.SerializeObject(new MessagePayload
    {
        RootId = operation.Telemetry.Context.Operation.Id,
        ParentId = operation.Telemetry.Id,
        Payload = message
    });
    
    CloudQueueMessage queueMessage = new CloudQueueMessage(jsonPayload);

    // Add operation.Telemetry.Id to the OperationContext to correlate Azure Storage logs and ApplciationInsights telemetry
    OperationContext context = new OperationContext { ClientRequestID = operation.Telemetry.Id};

    try
    {
        await queue.AddMessageAsync(queueMessage, null, null, new QueueRequestOptions(), context);
    }
    catch (StorageException e)
    {
        operation.Telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.Telemetry.Success = false;
        operation.Telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // update status code and success as appropriate
        telemetryClient.StopOperation(operation);
    }
}  
```

Чтобы сократить объем данных телеметрии, передаваемый приложением, или если не требуется отслеживать операцию `enqueue` по иным причинам, можно напрямую использовать API `Activity`:

- Создайте (и запустите) новый `Activity` вместо запуска операции ApplicationInsights (нет необходимости назначать какие-либо свойства в ней, за исключением имени операции).
- Сериализируйте `yourActivity.Id` в полезные данные сообщения вместо `operation.Telemetry.Id`. Можно также воспользоваться `Activity.Current.Id`


#### <a name="dequeue"></a>Выведение из очереди
Так же, как и `Enqueue`, фактические HTTP-запросы в очередь хранилища Azure автоматически отслеживаются ApplicationInsights. Однако операция `Enqueue` предположительно выполняется в контексте родительского элемента, такого как контекст "входящего" запроса. Пакеты SDK Application Insights автоматически коррелируют такие операции (и их части HTTP) с родительским запросом и другими данными телеметрии, переданными в той же области.

Операция `Dequeue` является довольно сложной: пакет SDK Application Insights автоматически отслеживает HTTP-запросы, однако контекст корреляции не известен до тех пор, пока не будет выполнен синтаксический анализ сообщения. Невозможно коррелировать HTTP-запрос для получения сообщения с остальной частью данных телеметрии.

Зачастую это может быть полезно для корреляции HTTP-запроса с очередью и другими трассировками. В следующем примере показано, как добиться этого.

``` C#
public async Task<MessagePayload> Dequeue(CloudQueue queue)
{
    var telemetry = new DependencyTelemetry
    {
        Type = "Queue",
        Name = "Dequeue " + queue.Name
    };

    telemetry.Start();

    try
    {
        var message = await queue.GetMessageAsync();

        if (message != null)
        {
            var payload = JsonConvert.DeserializeObject<MessagePayload>(message.AsString);

            // if there is a message, we want to correlate Dequeue operation with processing
            // However we will only know what correlation Id to use AFTER we will get it from the message
            // So we will report telemetry once we know the Ids.
            telemetry.Context.Operation.Id = payload.RootId;
            telemetry.Context.Operation.ParentId = payload.ParentId;

            // delete message
            return payload;
        }
    }
    catch (StorageException e)
    {
        telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        telemetry.Success = false;
        telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // update status code and success as appropriate
        telemetry.Stop();
        telemetryClient.Track(telemetry);
    }

    return null;
}
```

#### <a name="process"></a>Process

В следующем примере мы отслеживаем "входящее" сообщение, аналогично тому, как мы организовали трассировку входящего HTTP-запроса.

```C#
public async Task Process(MessagePayload message)
{
    // once the message is dequeued from the queue, create ReqeustTelemetry to track it's processing
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };
    // it may also make sense to get name from the message
    requestTelemetry.Context.Operation.Id = message.RootId;
    requestTelemetry.Context.Operation.ParentId = message.ParentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // update status code and success as appropriate
        telemetryClient.StopOperation(operation);
    }
}
```

Другие операции очереди также можно инструментировать. Операции считывания следует инструментировать так же, как выведение из очереди. Инструментировать операции управления очередью необязательно. Application Insights отслеживает такие операции, как HTTP, и в большинстве случаев этого достаточно.

При инструментировании удаления сообщения убедитесь в том, что заданы идентификаторы операции (корреляция). Кроме того, можно воспользоваться API `Activity` (что не требует задания идентификаторов операций для элементов телеметрии, поскольку ApplicationInsights делает это автоматически):

- Получив элемент из очереди, создайте новую `Activity`
- Используйте `Activity.SetParentId(message.ParentId)` для корреляции журналов потребителя и производителя
- Запустите `Activity`
- Организуйте отслеживание операций выведения из очереди, обработки и удаления с помощью вспомогательных методов `Start/StopOperation`. Это необходимо выполнить из одного и того же асинхронного потока управления (контекста выполнения). Таким образом, они будут сопоставлены должным образом.
- Затем остановите `Activity`
- Используйте `Start/StopOperation` или вручную вызовите телеметрию Track 

### <a name="batch-processing"></a>Пакетная обработка
Некоторые очереди позволяют выводить из очереди несколько сообщений с помощью одного запроса, однако обработка таких сообщений предположительно выполняется независимо и осуществляется другими логическими операциями.
В этом случае невозможно сопоставить операцию `Dequeue` с обработкой конкретного сообщения.

Обработка каждого сообщения должна выполняться в собственном асинхронном потоке управления. Дополнительные сведения об этом см. в разделе [Отслеживание исходящих зависимостей](#outgoing-dependencies-tracking).

## <a name="long-running-background-tasks"></a>Длительные фоновые задачи
Некоторые приложения запускают длительно выполняющиеся операции, которые могут быть вызваны запросами пользователей. С точки зрения трассировки и инструментирования это ничем не отличается от инструментирования запроса или зависимости. 

``` C#
async Task BackgroundTask()
{
    var operation = telemetryClient.StartOperation<RequestTelemetry>(taskName);
    operation.Telemetry.Type = "Background";
    try
    {
        int progress = 0;
        while (progress < 100)
        {
            // process task
            telemetryClient.TrackTrace($"done {progress++}%");
        }
        // update status code and success as appropriate
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // update status code and success as appropriate
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

В этом примере мы используем `telemetryClient.StartOperation` для создания `RequestTelemetry` и заполнения контекста корреляции. Предположим, имеется родительская операция, которая, например, была создана входящими запросами, запланировавшими операцию. Поскольку `BackgroundTask` запускается в том же асинхронном потоке управления, что и входящий запрос, он сопоставляется с той же родительской операцией. `BackgroundTask` и все вложенные элементы телеметрии будут автоматически сопоставлены с запросом, вызвавшим ее, даже после завершения запроса.

Если задача запущена из фонового потока, с которым не связана ни одна операция (Activity), у `BackgroundTask` отсутствуют какие-либо родительские элементы. Тем не менее она может включать вложенные операции, и все элементы телеметрии, полученные от задачи, сопоставляются с `RequestTelemetry`, созданной в `BackgroundTask`.

## <a name="outgoing-dependencies-tracking"></a>Отслеживание исходящих зависимостей
Можно отслеживать собственный тип "зависимостей" или некоторые операции, не поддерживаемые ApplicationInsights.

Примером такого настраиваемого отслеживания может служит метод `Enqueue` в очереди служебной шины или очереди хранилища Azure.

Общий подход к настраиваемому отслеживанию зависимостей заключается в следующем:
- Вызовите метод `TelemetryClient.StartOperation` (расширение), который заполняет свойства `DependencyTelemetry`, необходимые для корреляции, и ряд других свойств (метка времени запуска, длительность).
- Задайте другие настраиваемые свойства `DependencyTelemetry`: имя и любой контекст, который требуется
- Выполните вызов зависимости и ожидайте ее
- Остановите операцию после завершения с помощью `StopOperation`
- Выполните обработку исключений

`StopOperation` только останавливает операцию, которая была запущена: если текущая выполняющаяся операция не совпадает с той, которую нужно остановить, StopOperation не выполняет никаких действий. Это может произойти в случае, если в одном и том же контексте выполнения параллельно запущено несколько операций:

```C#
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstTask = RunMyTaskAsync();

var secondOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 2");
var secondTask = RunMyTaskAsync();

await firstTask;

// this will do nothing and will not report telemetry for the first operation
// as currently secondOperation is active
telemetryClient.StopOperation(firstOperation); 

await secondTask;
```

Поэтому всегда необходимо убедиться в том, что выполнен вызов `StartOperation` и задача выполняется в своем собственном контексте:
```C#
public async Task RunMyTaskAsync()
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
    try 
    {
        var myTask = await StartMyTaskAsync();
        // update status code and success as appropriate
    }
    catch(...) 
    {
        // update status code and success as appropriate
    }
    finally 
    {
        telemetryClient.StopOperation(operation);
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия

- Основе [корреляции данных телеметрии](application-insights-correlation.md) в Application Insights.
- В [этой статье](application-insights-data-model.md) представлены типы данных и модель данных для Application Insights.
- Передача настраиваемых [событий и метрик](app-insights-api-custom-events-metrics.md) в Application Insights.
- Извлеките стандартную [конфигурацию](app-insights-configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet) коллекции свойств контекста.
- Ознакомьтесь с [руководством пользователя по System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md), чтобы узнать, как осуществляется корреляция телеметрии

