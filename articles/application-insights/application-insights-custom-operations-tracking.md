---
title: Отслеживание пользовательских операций с помощью пакета SDK Azure Application Insights для .NET | Документы Майкрософт
description: Отслеживание пользовательских операций с помощью пакета SDK .NET Azure Application Insights
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 06/30/2017
ms.author: sergkanz
ms.openlocfilehash: 94424a3d8aad56cf4504cccd8adb1a45523d95e0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="track-custom-operations-with-application-insights-net-sdk"></a>Отслеживание пользовательских операций с помощью пакета SDK Application Insights для .NET

Пакеты SDK Azure Application Insights автоматически отслеживают входящие HTTP-запросы и вызовы зависимых служб, в том числе HTTP-запросы и SQL-запросы. Отслеживание и корреляция запросов и зависимостей позволяет контролировать скорость реагирования приложения в целом, а также надежность всех микрослужб, составляющих это приложение. 

Существует класс шаблонов приложений, которые в принципе не поддерживаются. Для надлежащего мониторинга таких шаблонов требуется инструментирование кода вручную. В этой статье описывается несколько шаблонов, которые могут потребовать ручного инструментирования, например обработка пользовательской очереди сообщений и выполнение продолжительных фоновых задач.

В данном документе представлены инструкции по организации отслеживания пользовательских операций с помощью пакета SDK Application Insights. Эта документация предназначена для:

- Application Insights для .NET (базовый пакет SDK) версии 2.4 или более поздней версии;
- Application Insights для веб-приложений (выполнение ASP.NET) версии 2.4 или более поздней версии;
- Application Insights для ASP.NET Core версии 2.1 или более поздней версии.

## <a name="overview"></a>Обзор
Операция — это логический элемент работы, выполняемый приложением. У нее есть имя, время запуска, длительность, результат и контекст выполнения, такой как имя пользователя, свойства и результат. Если операция A была инициирована операцией B, то операция B является родительской для A. У операции может быть только одна родительская операция, но множество дочерних операций. Дополнительные сведения об операциях и корреляции телеметрии см. в разделе [Корреляция данных телеметрии в Application Insights](application-insights-correlation.md).

В пакете SDK Application Insights для .NET операция описывается абстрактным классом [OperationTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Extensibility/Implementation/OperationTelemetry.cs) и его потомками [RequestTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/DataContracts/RequestTelemetry.cs) и [DependencyTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/DataContracts/DependencyTelemetry.cs).

## <a name="incoming-operations-tracking"></a>Отслеживание входящих операций 
Пакет SDK Application Insights автоматически собирает HTTP-запросы для приложений ASP.NET, которые выполняются в конвейере служб IIS, и всех приложений ASP.NET Core. Существуют решения для других платформ, поддержка которых осуществляется силами сообщества разработчиков. Однако если приложение не поддерживается ни одним стандартным решением или решением сообщества разработчиков, его можно инструментировать вручную.

Другой пример, в котором требуется настраиваемое отслеживание, — это рабочая роль, которая получает элементы из очереди. Для некоторых очередей вызов для добавления сообщения в эту очередь отслеживается как зависимость. Однако операции высокого уровня, описывающие обработку сообщения, не собираются автоматически.

Посмотрим, как можно отследить эти операции.

На высоком уровне задачей является создание `RequestTelemetry` и настройка известных свойств. После завершения этой операции можно отслеживать данные телеметрии. Эта задача показана в следующем примере.

### <a name="http-request-in-owin-self-hosted-app"></a>HTTP-запрос в резидентном приложении Owin
В этом примере контекст трассировки распространяется согласно [протоколу HTTP для корреляции](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Следует ожидать заголовки, которые в нем описаны.

```csharp
public class ApplicationInsightsMiddleware : OwinMiddleware
{
    private readonly TelemetryClient telemetryClient = new TelemetryClient(TelemetryConfiguration.Active);
    
    public ApplicationInsightsMiddleware(OwinMiddleware next) : base(next) {}

    public override async Task Invoke(IOwinContext context)
    {
        // Let's create and start RequestTelemetry.
        var requestTelemetry = new RequestTelemetry
        {
            Name = $"{context.Request.Method} {context.Request.Uri.GetLeftPart(UriPartial.Path)}"
        };

        // If there is a Request-Id received from the upstream service, set the telemetry context accordingly.
        if (context.Request.Headers.ContainsKey("Request-Id"))
        {
            var requestId = context.Request.Headers.Get("Request-Id");
            // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
            requestTelemetry.Context.Operation.Id = GetOperationId(requestId);
            requestTelemetry.Context.Operation.ParentId = requestId;
        }

        // StartOperation is a helper method that allows correlation of 
        // current operations with nested operations/telemetry
        // and initializes start time and duration on telemetry items.
        var operation = telemetryClient.StartOperation(requestTelemetry);

        // Process the request.
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
            // Update status code and success as appropriate.
            if (context.Response != null)
            {
                requestTelemetry.ResponseCode = context.Response.StatusCode.ToString();
                requestTelemetry.Success = context.Response.StatusCode >= 200 && context.Response.StatusCode <= 299;
            }
            else
            {
                requestTelemetry.Success = false;
            }

            // Now it's time to stop the operation (and track telemetry).
            telemetryClient.StopOperation(operation);
        }
    }
    
    public static string GetOperationId(string id)
    {
        // Returns the root ID from the '|' to the first '.' if any.
        int rootEnd = id.IndexOf('.');
        if (rootEnd < 0)
            rootEnd = id.Length;

        int rootStart = id[0] == '|' ? 1 : 0;
        return id.Substring(rootStart, rootEnd - rootStart);
    }
}
```

Протокол HTTP для корреляции также объявляет заголовок `Correlation-Context`. Однако здесь он опускается для простоты.

## <a name="queue-instrumentation"></a>Инструментирование очереди
Пока есть [протокол HTTP для корреляции](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md), который передает сведения о корреляции с помощью HTTP-запроса, каждый протокол очереди должен определить, как одни и те же сведения передаются вместе с сообщением очереди. Некоторые протоколы очереди (например, AMQP) разрешают передавать дополнительные метаданные. Другим протоколам (например, очереди службы хранилища Azure) требуется контекст для кодирования в полезных данных сообщения.

### <a name="service-bus-queue"></a>Очередь служебной шины
Application Insights отслеживает вызовы обмена сообщениями в службе "Служебная шина" с новым [клиентом службы "Служебная шина Microsoft Azure" для .NET](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/) версии 3.0.0 и выше.
Если вы используете [шаблон обработчика сообщений](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) для обработки, больше ничего делать не нужно: все вызовы служебной шины, выполненные вашей службой, автоматически отслеживаются и связываются с другими элементами телеметрии. Если вы обрабатываете сообщения вручную, см. статью о [трассировке клиента служебной шины с помощью Microsoft Application Insights](../service-bus-messaging/service-bus-end-to-end-tracing.md).

Если вы используете пакет [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/), продолжайте читать эту статью. Следующие примеры демонстрируют, как отслеживать (и коррелировать) вызовы в служебную шину, если очередь служебной шины использует протокол AMQP и Application Insights не отслеживает операции очереди автоматически.
Идентификаторы корреляции передаются в свойствах сообщения.

#### <a name="enqueue"></a>Постановка в очередь

```csharp
public async Task Enqueue(string payload)
{
    // StartOperation is a helper method that initializes the telemetry item
    // and allows correlation of this operation with its parent and children.
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queueName);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queueName;

    var message = new BrokeredMessage(payload);
    // Service Bus queue allows the property bag to pass along with the message.
    // We will use them to pass our correlation identifiers (and other context)
    // to the consumer.
    message.Properties.Add("ParentId", operation.Telemetry.Id);
    message.Properties.Add("RootId", operation.Telemetry.Context.Operation.Id);

    try
    {
        await queue.SendAsync(message);
        
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = true;
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Set operation.Telemetry Success and ResponseCode here.
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
```csharp
public async Task Process(BrokeredMessage message)
{
    // After the message is taken from the queue, create RequestTelemetry to track its processing.
    // It might also make sense to get the name from the message.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };

    var rootId = message.Properties["RootId"].ToString();
    var parentId = message.Properties["ParentId"].ToString();
    // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
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
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

### <a name="azure-storage-queue"></a>Очередь службы хранилища Azure
В следующем примере показано, как отслеживать операции [очереди службы хранилища Azure](../storage/queues/storage-dotnet-how-to-use-queues.md) и сопоставлять данные телеметрии производителя, потребителя и службы хранилища Azure. 

Очередь службы хранилища использует API HTTP. Все вызовы, отправляемые к очереди, отслеживаются сборщиком зависимостей Application Insights на наличие HTTP-запросов.
Убедитесь, что в `applicationInsights.config` есть `Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer`. В противном случае добавьте его программно, как описано в разделе [Фильтрация и предварительная обработка данных телеметрии в пакете SDK для Application Insights](app-insights-api-filtering-sampling.md).

При настройке ApplicationInsights вручную убедитесь, что вы создали и инициализировали `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule` так же, как указано ниже.
 
```csharp
DependencyTrackingTelemetryModule module = new DependencyTrackingTelemetryModule();

// You can prevent correlation header injection to some domains by adding it to the excluded list.
// Make sure you add a Storage endpoint. Otherwise, you might experience request signature validation issues on the Storage service side.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
module.Initialize(TelemetryConfiguration.Active);

// Do not forget to dispose of the module during application shutdown.
```

Кроме того, может потребоваться сопоставить идентификатор операции Application Insights с идентификатором запроса службы хранилища. Сведения о том, как настроить и получить идентификатор клиента для запроса службы хранилища и идентификатор запроса сервера, см. в разделе [Мониторинг, диагностика и устранение неполадок с хранилищем Azure](../storage/common/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing).

#### <a name="enqueue"></a>Постановка в очередь
Так как очереди службы хранилища Azure поддерживают API HTTP, все операции с очередью автоматически отслеживаются Application Insights. Во многих случаях этого инструментария должно быть достаточно. Однако для сопоставления трассировок на стороне потребителя с трассировками производителя необходимо передать некоторый контекст корреляции, точно так же, как мы сделали это для протокола HTTP для корреляции. 

В этом примере показано, как отслеживать операцию `Enqueue`. Вы можете:

 - **Сопоставить повторные попытки (если таковые имеются)**: все они имеют одну общую родительскую операцию, `Enqueue`. В противном случае они отслеживаются как дочерние элементы входящего запроса. В случае нескольких логических запросов к очереди может оказаться затруднительным определить, какой вызов совершался повторно.
 - **Сопоставить журналы службы хранилища Azure (при необходимости)**: они сопоставляются с данными телеметрии Application Insights.

Операция `Enqueue` является дочерним элементом родительской операции (например, входящего HTTP-запроса). Вызовов зависимости HTTP является дочерним элементом операции `Enqueue` и внучатым элементом входящего запроса.

```csharp
public async Task Enqueue(CloudQueue queue, string message)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queue.Name);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queue.Name;

    // MessagePayload represents your custom message and also serializes correlation identifiers into payload.
    // For example, if you choose to pass payload serialized to JSON, it might look like
    // {'RootId' : 'some-id', 'ParentId' : '|some-id.1.2.3.', 'message' : 'your message to process'}
    var jsonPayload = JsonConvert.SerializeObject(new MessagePayload
    {
        RootId = operation.Telemetry.Context.Operation.Id,
        ParentId = operation.Telemetry.Id,
        Payload = message
    });
    
    CloudQueueMessage queueMessage = new CloudQueueMessage(jsonPayload);

    // Add operation.Telemetry.Id to the OperationContext to correlate Storage logs and Application Insights telemetry.
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
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}  
```

Чтобы сократить объем данных телеметрии, передаваемой приложением, или если не требуется отслеживать операцию `Enqueue` по иным причинам, можно напрямую использовать API `Activity`.

- Создайте (и запустите) новый элемент `Activity` вместо запуска операции Application Insights. *Не* нужно назначать в нем какие-либо свойства, за исключением имени операции.
- Сериализируйте `yourActivity.Id` в полезные данные сообщения вместо `operation.Telemetry.Id`. Кроме того, можно использовать `Activity.Current.Id`.


#### <a name="dequeue"></a>Выведение из очереди
Так же, как и `Enqueue`, фактические HTTP-запросы к очереди службы хранилища Azure автоматически отслеживаются Application Insights. Однако операция `Enqueue` предположительно выполняется в контексте родительского элемента, такого как контекст входящего запроса. Пакеты SDK Application Insights автоматически сопоставляют такие операции (и их части HTTP) с родительским запросом и другими данными телеметрии, переданными в той же области.

С операцией `Dequeue` все не так просто. Пакет SDK Application Insights автоматически отслеживает HTTP-запросы. Тем не менее ему не известен контекст корреляции, пока выполняется анализ сообщения. Невозможно сопоставить HTTP-запрос для получения сообщения с остальной частью данных телеметрии.

Зачастую может быть удобно сопоставить HTTP-запрос с очередью и другими трассировками. В следующем примере показано, как это сделать.

```csharp
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

            // If there is a message, we want to correlate the Dequeue operation with processing.
            // However, we will only know what correlation ID to use after we get it from the message,
            // so we will report telemetry after we know the IDs.
            telemetry.Context.Operation.Id = payload.RootId;
            telemetry.Context.Operation.ParentId = payload.ParentId;

            // Delete the message.
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
        // Update status code and success as appropriate.
        telemetry.Stop();
        telemetryClient.Track(telemetry);
    }

    return null;
}
```

#### <a name="process"></a>Process

В следующем примере мы отслеживаем входящее сообщение так же, как выполняем трассировку входящего HTTP-запроса.

```csharp
public async Task Process(MessagePayload message)
{
    // After the message is dequeued from the queue, create RequestTelemetry to track its processing.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };
    // It might also make sense to get the name from the message.
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
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

Другие операции очереди также можно инструментировать. Операцию заглядывания следует инструментировать так же, как операцию выведения из очереди. Инструментировать операции управления очередью необязательно. Application Insights отслеживает такие операции, как HTTP, и в большинстве случаев этого достаточно.

При инструментировании удаления сообщения убедитесь в том, что заданы идентификаторы операции (корреляция). Кроме того, можно использовать API `Activity`. Вам не нужно задавать идентификаторы операций для элементов телеметрии, так как пакет SDK для Application Insights делает это автоматически.

- Получив элемент из очереди, создайте `Activity`.
- Используйте `Activity.SetParentId(message.ParentId)` для сопоставления журналов потребителя и производителя.
- Запустите `Activity`.
- Отслеживайте операции выведения из очереди, обработки и удаления с помощью вспомогательных методов `Start/StopOperation`. Это необходимо выполнить из одного и того же асинхронного потока управления (контекста выполнения). Таким образом они будут сопоставлены должным образом.
- Остановите `Activity`.
- Используйте `Start/StopOperation` или вручную вызовите телеметрию `Track`.

### <a name="batch-processing"></a>Пакетная обработка
Некоторые очереди поддерживают вывод из очереди нескольких сообщений с помощью одного запроса. Обработка таких сообщений предположительно является независимой и относится к разным логическим операциям. В этом случае невозможно сопоставить операцию `Dequeue` с обработкой конкретного сообщения.

Обработка каждого сообщения должна выполняться в собственном асинхронном потоке управления. Дополнительные сведения см. в разделе [Отслеживание исходящих зависимостей](#outgoing-dependencies-tracking).

## <a name="long-running-background-tasks"></a>Длительные фоновые задачи
Некоторые приложения запускают длительные операции, которые могут быть вызваны запросами пользователей. С точки зрения трассировки и инструментирования это ничем не отличается от инструментирования запроса или зависимости. 

```csharp
async Task BackgroundTask()
{
    var operation = telemetryClient.StartOperation<RequestTelemetry>(taskName);
    operation.Telemetry.Type = "Background";
    try
    {
        int progress = 0;
        while (progress < 100)
        {
            // Process the task.
            telemetryClient.TrackTrace($"done {progress++}%");
        }
        // Update status code and success as appropriate.
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Update status code and success as appropriate.
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

В этом примере `telemetryClient.StartOperation` создает `RequestTelemetry` и заполняет контекст корреляции. Предположим, имеется родительская операция, которая была создана входящими запросами, запланировавшими эту операцию. Так как `BackgroundTask` запускается в том же асинхронном потоке управления, что и входящий запрос, он сопоставляется с этой родительской операцией. `BackgroundTask` и все вложенные элементы телеметрии автоматически сопоставляются с запросом, вызвавшим ее, даже после завершения запроса.

Если задача запущена из фонового потока, с которым не связана ни одна операция (`Activity`), у задачи `BackgroundTask` отсутствуют какие-либо родительские элементы. Тем не менее у нее могут быть вложенные операции. Все элементы телеметрии, полученные от этой задачи, сопоставляются с элементом `RequestTelemetry`, созданным в `BackgroundTask`.

## <a name="outgoing-dependencies-tracking"></a>Отслеживание исходящих зависимостей
Можно отслеживать собственный тип зависимостей или операцию, не поддерживаемую Application Insights.

Примером такого настраиваемого отслеживания может служить метод `Enqueue` в очереди служебной шины или очереди службы хранилища Azure.

Общий подход к настраиваемому отслеживанию зависимостей заключается в следующем.

- Вызовите метод `TelemetryClient.StartOperation` (расширение), который заполняет свойства `DependencyTelemetry`, необходимые для корреляции, и ряд других свойств (метка времени запуска, длительность).
- Задайте другие настраиваемые свойства `DependencyTelemetry`, например имя и любой контекст, который требуется.
- Создайте вызов зависимости и дождитесь его выполнения.
- Остановите операцию после завершения с помощью `StopOperation`.
- Выполните обработку исключений.

```csharp
public async Task RunMyTaskAsync()
{
    using (var operation = telemetryClient.StartOperation<DependencyTelemetry>("task 1"))
    {
        try 
        {
            var myTask = await StartMyTaskAsync();
            // Update status code and success as appropriate.
        }
        catch(...) 
        {
            // Update status code and success as appropriate.
        }
    }
}
```

Если операция удаляется, обработка останавливается. Поэтому вы можете удалить операцию вместо того, чтобы вызывать метод `StopOperation`.

*Предупреждение.* В некоторых случаях необработанное исключение может [помешать](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-finally) вызову `finally`. Поэтому операции могут не отслеживаться.

### <a name="parallel-operations-processing-and-tracking"></a>Обработка и отслеживание параллельных операций

`StopOperation` останавливает только операцию, которая была запущена. Если текущая выполняемая операция не совпадает с операцией, которую нужно остановить, то `StopOperation` не выполняет никаких действий. Это может произойти в случае, если в одном и том же контексте выполнения параллельно запущено несколько операций.

```csharp
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstTask = RunMyTaskAsync();

var secondOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 2");
var secondTask = RunMyTaskAsync();

await firstTask;

// FAILURE!!! This will do nothing and will not report telemetry for the first operation
// as currently secondOperation is active.
telemetryClient.StopOperation(firstOperation); 

await secondTask;
```

Всегда вызывайте метод `StartOperation` и обрабатывайте операцию в том же методе **async**, чтобы изолировать параллельные операции. Если операция является синхронной (или не асинхронной), создайте оболочку для процесса и отслеживайте его с помощью команды `Task.Run`:

```csharp
public void RunMyTask(string name)
{
    using (var operation = telemetryClient.StartOperation<DependencyTelemetry>(name))
    {
        Process();
        // Update status code and success as appropriate.
    }
}

public async Task RunAllTasks()
{
    var task1 = Task.Run(() => RunMyTask("task 1"));
    var task2 = Task.Run(() => RunMyTask("task 2"));
    
    await Task.WhenAll(task1, task2);
}
```

## <a name="next-steps"></a>Дополнительная информация

- Изучите основы [корреляции данных телеметрии](application-insights-correlation.md) в Application Insights.
- В [этой статье](application-insights-data-model.md) представлены типы данных и модель данных для Application Insights.
- Передача настраиваемых [событий и метрик](app-insights-api-custom-events-metrics.md) в Application Insights.
- Изучите стандартную [конфигурацию](app-insights-configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet) коллекции свойств контекста.
- Ознакомьтесь с [руководством пользователя по System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md), чтобы узнать, как осуществляется корреляция данных телеметрии.
