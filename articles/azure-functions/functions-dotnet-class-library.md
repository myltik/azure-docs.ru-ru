---
title: "Использование библиотек классов .NET с помощью Функций Azure | Документация Майкрософт"
description: "Узнайте, как создавать библиотеки классов .NET, которые можно использовать с Функциями Azure."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "функции azure, функции, обработка событий, динамические вычисления, независимая архитектура"
ms.assetid: 9f5db0c2-a88e-4fa8-9b59-37a7096fc828
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/10/2017
ms.author: glenga
ms.openlocfilehash: a3bc07623505371b4f3c230ebadeb577a70fdb5e
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2017
---
# <a name="using-net-class-libraries-with-azure-functions"></a>Использование библиотек классов .NET с помощью Функций Azure

Помимо файлов сценариев Функции Azure поддерживают публикацию библиотеки классов в качестве реализации одной или нескольких функций. Мы советуем использовать [инструменты Visual Studio 2017 для Функций Azure](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/).

## <a name="prerequisites"></a>Предварительные требования 

Для работы с этой статьей необходимы следующие компоненты:

- [Visual Studio 2017 версии 15.3](https://www.visualstudio.com/vs/) или более поздней.
- Установка рабочей нагрузки **разработки Azure**.

## <a name="functions-class-library-project"></a>Проект библиотеки классов функций

Создайте проект Функций Azure в Visual Studio. Шаблон проекта создает файлы *host.json* и *local.settings.json*. Вы можете [настроить параметры среды выполнения Функций Azure в файле host.json](functions-host-json.md). 

Файл *local.settings.json* хранит параметры приложения, строки подключения и параметры основных инструментов Функций Azure. Дополнительные сведения об этой структуре см. в статье [Как программировать и тестировать функции Azure в локальной среде](functions-run-local.md#local-settings).

### <a name="functionname-attribute"></a>Атрибут FunctionName

Атрибут [`FunctionNameAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/FunctionNameAttribute.cs) помечает метод как точку входа функции. Он должен иметь только один триггер и 0 или более входных и выходных привязок.

### <a name="conversion-to-functionjson"></a>Преобразование в файл function.json

При выполнении сборки проекта Функций Azure в каталоге функции создается файл *function.json*. Имя каталога совпадает с именем функции, указанным атрибутом `[FunctionName]`. Файл *function.json* содержит триггеры и привязки, а также указывает на файл сборки проекта.

Это преобразование выполняется с помощью пакета NuGet [Microsoft\.NET\.Sdk\.Functions](http://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). Источник доступен в репозитории GitHub [azure\-functions\-vs\-build\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="triggers-and-bindings"></a>Триггеры и привязки 

В таблице ниже перечислены триггеры и привязки, доступные в проекте библиотеки классов Функций Azure. Все атрибуты находятся в пространстве имен `Microsoft.Azure.WebJobs`.

| Привязка | Атрибут | Пакет NuGet |
|------   | ------    | ------        |
| [Привязки триггера, входные и выходные привязки для хранилища BLOB-объектов](#blob-storage) | [BlobAttribute], [StorageAccountAttribute] | [Microsoft.Azure.WebJobs] | [Хранилище BLOB-объектов] |
| [Триггер Cosmos DB](#cosmos-db) | [CosmosDBTriggerAttribute] | [Microsoft.Azure.WebJobs.Extensions.DocumentDB] | 
| [Входные и выходные данные Cosmos DB](#cosmos-db) | [DocumentDBAttribute] | [Microsoft.Azure.WebJobs.Extensions.DocumentDB] |
| [Привязки триггера и выходные привязки для концентраторов событий](#event-hub) | [EventHubTriggerAttribute], [EventHubAttribute] | [Microsoft.Azure.WebJobs.ServiceBus] |
| [Входная и выходная привязка для внешнего файла](#api-hub) | [ApiHubFileAttribute] | [Microsoft.Azure.WebJobs.Extensions.ApiHub] |
| [Триггер HTTP и веб-перехватчика](#http) | [HttpTriggerAttribute] | [Microsoft.Azure.WebJobs.Extensions.Http] |
| [Входные и выходные привязки для мобильных приложений](#mobile-apps) | [MobileTableAttribute] | [Microsoft.Azure.WebJobs.Extensions.MobileApps] | 
| [Выходные привязки для Центров уведомлений](#nh) | [NotificationHubAttribute] | [Microsoft.Azure.WebJobs.Extensions.NotificationHubs] | 
| [Привязки триггера и выходные привязки для хранилища очередей](#queue) | [QueueAttribute], [StorageAccountAttribute] | [Microsoft.Azure.WebJobs] | 
| [Выходные привязки SendGrid](#sendgrid) | [SendGridAttribute] | [Microsoft.Azure.WebJobs.Extensions.SendGrid] | 
| [Привязки триггера и выходные привязки для служебной шины](#service-bus) | [ServiceBusAttribute], [ServiceBusAccountAttribute] | [Microsoft.Azure.WebJobs.ServiceBus]
| [Входные и выходные привязки для хранилища таблиц](#table) | [TableAttribute], [StorageAccountAttribute] | [Microsoft.Azure.WebJobs] | 
| [Триггер таймера](#timer) | [TimerTriggerAttribute] | [Microsoft.Azure.WebJobs.Extensions] | 
| [Выходные привязки Twilio](#twilio) | [TwilioSmsAttribute] | [Microsoft.Azure.WebJobs.Extensions.Twilio] | 

<a name="blob-storage"></a>

### <a name="blob-storage-trigger-input-bindings-and-output-bindings"></a>Привязки триггера, входные и выходные привязки для хранилища BLOB-объектов

Функции Azure поддерживают привязки триггера, а также входные и выходные привязки для хранилища BLOB-объектов Azure. Дополнительные сведения о выражениях привязки и метаданных см. в статье [Привязки хранилища BLOB-объектов для Функций Azure](functions-bindings-storage-blob.md).

Триггер большого двоичного объекта определяется с помощью атрибута `[BlobTrigger]`. Используйте атрибут `[StorageAccount]`, чтобы определить имя параметра приложения, которое содержит строку подключения к учетной записи хранения, которая используется целой функцией или классом.

```csharp
[StorageAccount("AzureWebJobsStorage")]
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Входные и выходные привязки для большого двоичного объекта определяются с помощью атрибута `[Blob]`, а также параметра `FileAccess`, указывающего операции чтения или записи. В следующем примере используется выходная привязка и привязка триггера большого двоичного объекта.

```csharp
[FunctionName("ResizeImage")]
[StorageAccount("AzureWebJobsStorage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall, 
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    var imageBuilder = ImageResizer.ImageBuilder.Current;
    var size = imageDimensionsTable[ImageSize.Small];

    imageBuilder.Build(image, imageSmall,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);

    image.Position = 0;
    size = imageDimensionsTable[ImageSize.Medium];

    imageBuilder.Build(image, imageMedium,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);
}

public enum ImageSize { ExtraSmall, Small, Medium }

private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
    { ImageSize.ExtraSmall, (320, 200) },
    { ImageSize.Small,      (640, 400) },
    { ImageSize.Medium,     (800, 600) }
};
```        

<a name="cosmos-db"></a>

### <a name="cosmos-db-trigger-input-bindings-and-output-bindings"></a>Привязки триггера, входные и выходные привязки Cosmos DB

Служба "Функции Azure" поддерживает привязки триггера, входные и выходные привязки для Cosmos DB. Дополнительные сведения о возможностях привязки Cosmos DB см. в статье [Привязки Cosmos DB в Функциях Azure](functions-bindings-documentdb.md).

Чтобы использовать триггер из документа Cosmos DB, используйте атрибут `[CosmosDBTrigger]` в пакете NuGet [Microsoft.Azure.WebJobs.Extensions.DocumentDB]. Далее приведены примеры триггеров из определенных `database` и `collection`. Параметр `myCosmosDB` содержит подключение к экземпляру Cosmos DB. 

```csharp
[FunctionName("DocumentUpdates")]
public static void Run(
    [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
IReadOnlyList<Document> documents, TraceWriter log)
{
        log.Info("Documents modified " + documents.Count);
        log.Info("First document Id " + documents[0].Id);
}
```

Чтобы привязать документ Cosmos DB, используйте атрибут `[DocumentDB]` в пакете NuGet [Microsoft.Azure.WebJobs.Extensions.DocumentDB]. В следующем примере используется привязка триггера очереди и выходная привязка API DocumentDB:

```csharp
[FunctionName("QueueToDocDB")]        
public static void Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem, 
    [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
{
    document = new { Text = myQueueItem, id = Guid.NewGuid() };
}

```

<a name="event-hub"></a>

### <a name="event-hubs-trigger-and-output"></a>Привязки триггера и выходные привязки для концентраторов событий

Функции Azure поддерживают привязки триггера и выходные привязки для концентраторов событий Azure. Дополнительные сведения см. в статье [Привязки концентратора событий функций Azure](functions-bindings-event-hubs.md).

Типы `[Microsoft.Azure.WebJobs.ServiceBus.EventHubTriggerAttribute]` и `[Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute]` определены в пакете NuGet [Microsoft.Azure.WebJobs.ServiceBus]. 

В следующем примере используется триггер концентратора событий:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnection")] string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

Следующий пример содержит выходные данные концентратора событий. В качестве типа выходных данных используется возвращаемое значение метода:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnection")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

<a name="api-hub"></a>

### <a name="external-file-input-and-output"></a>Входная и выходная привязка для внешнего файла

Функции Azure поддерживают привязки триггера, а также входные и выходные привязки для внешних файлов, таких как Google Drive, Dropbox и OneDrive. Дополнительные сведения см. в статье [Привязки внешних файлов в Функциях Azure (предварительная версия)](functions-bindings-external-file.md). Атрибуты `[ExternalFileTrigger]` и `[ExternalFile]` определены в пакете NuGet [Microsoft.Azure.WebJobs.Extensions.ApiHub].

В следующем примере C# показана входная и выходная привязка для внешнего файла. Код копирует входной файл в выходной.

```csharp
[StorageAccount("MyStorageConnection")]
[FunctionName("ExternalFile")]
[return: ApiHubFile("MyFileConnection", "samples-workitems/{queueTrigger}-Copy", FileAccess.Write)]
public static string Run([QueueTrigger("myqueue-items")] string myQueueItem, 
    [ApiHubFile("MyFileConnection", "samples-workitems/{queueTrigger}", FileAccess.Read)] string myInputFile, 
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    return myInputFile;
}
```

<a name="http"></a>

### <a name="http-and-webhooks"></a>HTTP и вызовы webhook

Используйте атрибут `HttpTrigger`, чтобы определить триггер HTTP или веб-перехватчик. Этот атрибут определен в пакете NuGet [Microsoft.Azure.WebJobs.Extensions.Http]. Вы можете настроить уровень авторизации, тип веб-перехватчика, маршрут и методы. В следующем примере определяется триггер HTTP с анонимной аутентификацией и типом веб-перехватчика _genericJson_.

```csharp
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run([HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a name="mobile-apps"></a>

### <a name="mobile-apps-input-and-output"></a>Входные и выходные привязки для мобильных приложений

Функции Azure поддерживают входные и выходные привязки для мобильных приложений. Дополнительные сведения см. в статье [Привязки мобильных приложений в функциях Azure](functions-bindings-mobile-apps.md). Атрибут `[MobileTable]` определен в пакете NuGet [Microsoft.Azure.WebJobs.Extensions.MobileApps].

В следующем примере показана выходная привязка для мобильных приложений:

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run([QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem, TraceWriter log)
{
    return new { Text = $"I'm running in a C# function! {myQueueItem}" };
}
```

<a name="nh"></a>

### <a name="notification-hubs-output"></a>Выходные привязки для Центров уведомлений

Функции Azure поддерживают выходную привязку для Центров уведомлений. Дополнительные сведения см. в статье [Выходная привязка центра уведомлений для функций Azure](functions-bindings-notification-hubs.md). Атрибут `[NotificationHub]` определен в пакете NuGet [Microsoft.Azure.WebJobs.Extensions.NotificationHubs].

<a name="queue"></a>

### <a name="queue-storage-trigger-and-output"></a>Привязки триггера и выходные привязки для хранилища очередей

Функции Azure поддерживают привязки триггера и выходные привязки для очередей Azure. Дополнительные сведения см. в статье [Привязки очередей службы хранилища для Функций Azure](functions-bindings-storage-queue.md).

В указанном ниже примере показано, как использовать возвращаемый функцией тип с выходной привязкой очереди с помощью атрибута `[Queue]`. 

```csharp
[StorageAccount("AzureWebJobsStorage")]
public static class QueueFunctions
{
    // HTTP trigger with queue output binding
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  TraceWriter log)
    {
        log.Info($"C# function processed: {input.Text}");
        return input.Text;
    }
}

```

Чтобы определить триггер очереди, используйте атрибут `[QueueTrigger]`.
```csharp
[StorageAccount("AzureWebJobsStorage")]
public static class QueueFunctions
{
    // Queue trigger
    [FunctionName("QueueTrigger")]
    [StorageAccount("AzureWebJobsStorage")]
    public static void QueueTrigger([QueueTrigger("myqueue-items")] string myQueueItem, TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}

```


<a name="sendgrid"></a>

### <a name="sendgrid-output"></a>Выходные привязки SendGrid

Функции Azure поддерживают выходную привязку SendGrid для отправки электронных сообщений программным способом. Дополнительные сведения см. в статье [Привязки SendGrid для Функций Azure](functions-bindings-sendgrid.md).

Атрибут `[SendGrid]` определен в пакете NuGet [Microsoft.Azure.WebJobs.Extensions.SendGrid]. Привязка SendGrid требует параметр приложения `AzureWebJobsSendGridApiKey`, которое содержит ключ API SendGrid. Это имя параметра по умолчанию вашего ключа API SendGrid. Если нужно несколько ключей SendGrid или требуется выбрать другое имя параметра, вы можете установить это имя, используя свойство `ApiKey` атрибута привязки `SendGrid`, как показано в следующем примере:

    [SendGrid(ApiKey = "MyCustomSendGridKeyName")]

Ниже приведен пример использования триггера очереди служебной шины и привязки выходных данных SendGrid с помощью `SendGridMessage`:

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid] out SendGridMessage message)
{
    message = new SendGridMessage();
    message.AddTo(email.To);
    message.AddContent("text/html", email.Body);
    message.SetFrom(new EmailAddress(email.From));
    message.SetSubject(email.Subject);
}

public class OutgoingEmail
{
    public string To { get; set; }
    public string From { get; set; }
    public string Subject { get; set; }
    public string Body { get; set; }
}
```
Обратите внимание, что в этом примере требуется ключ API SendGrid хранилища в параметре приложения с именем `AzureWebJobsSendGridApiKey`.

<a name="service-bus"></a>

### <a name="service-bus-trigger-and-output"></a>Привязки триггера и выходные привязки для служебной шины

Функции Azure поддерживают привязки триггера и выходные привязки для очередей и разделов служебной шины. Дополнительные сведения о настройке привязок см. в статье [Привязки служебной шины в Функциях Azure](functions-bindings-service-bus.md).

Атрибуты `[ServiceBusTrigger]` и `[ServiceBus]` определены в пакете NuGet [Microsoft.Azure.WebJobs.ServiceBus]. 

Далее приведен пример триггера очереди служебной шины:

```csharp
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run([ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

Ниже приведен пример выходной привязки служебной шины, которая в качестве типа выходных данных использует тип возвращаемого значения метода:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, TraceWriter log)
{
    log.Info($"C# function processed: {input.Text}");
    return input.Text;
}
```        

<a name="table"></a>

### <a name="table-storage-input-and-output"></a>Входные и выходные привязки для хранилища таблиц

Функции Azure поддерживают входные и выходные привязки для хранилища таблиц Azure. Дополнительные сведения см. в статье [Привязки таблиц службы хранилища для Функций Azure](functions-bindings-storage-table.md).

В следующем примере показан класс с 2 функциями, демонстрирующий входные и выходные привязки хранилища таблиц. 

```csharp
[StorageAccount("AzureWebJobsStorage")]
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, TraceWriter log)
    {
        log.Info($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }

    // use the metadata parameter "queueTrigger" to bind the queue payload
    [FunctionName("TableInput")]
    public static void TableInput([QueueTrigger("table-items")] string input, [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, TraceWriter log)
    {
        log.Info($"C# function processed: {poco.Text}");
    }
}

```

<a name="timer"></a>

### <a name="timer-trigger"></a>Триггер таймера

Функции Azure поддерживают привязку триггеров к таймерам, благодаря чему вы можете выполнять код функции по определенному расписанию. Дополнительные сведения о возможностях привязки см. в статье [Триггеры с таймерами в функциях Azure](functions-bindings-timer.md).

В плане потребления можно определить расписания с помощью [выражений CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression). Если используется план службы приложений, вы также можете использовать строку TimeSpan. 

В следующем примере определяется триггер таймера, который выполняется каждые 5 минут:

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

<a name="twilio"></a>

### <a name="twilio-output"></a>Выходные привязки Twilio

Функции Azure поддерживают выходные привязки Twilio, позволяя вашим функциям отправлять текстовые SMS-сообщения. Дополнительные сведения см. в статье [Отправка SMS-сообщений из Функций Azure с использованием привязки для вывода Twilio](functions-bindings-twilio.md). 

Атрибут `[TwilioSms]` определен в пакете [Microsoft.Azure.WebJobs.Extensions.Twilio].

В следующем примере C# используется триггер очереди и выходная привязка Twilio:

```csharp
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX" )]
public static SMSMessage Run([QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {order}");

    var message = new SMSMessage()
    {
        Body = $"Hello {order["name"]}, thanks for your order!",
        To = order["mobileNumber"].ToString()
    };

    return message;
}
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об использовании Функций Azure в написании сценариев C# см. в статье [Справочник разработчика C# по \#функциям Azure](functions-reference-csharp.md).

[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


<!-- NuGet packages --> 
[Microsoft.Azure.WebJobs]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.DocumentDB]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB/1.1.0-beta4
[Microsoft.Azure.WebJobs.ServiceBus]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.MobileApps]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps/1.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.NotificationHubs]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs/1.1.0-beta1
[Microsoft.Azure.WebJobs.ServiceBus]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.SendGrid]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.Http]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http/1.0.0-beta1
[Microsoft.Azure.WebJobs.Extensions.BotFramework]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.BotFramework/1.0.15-beta
[Microsoft.Azure.WebJobs.Extensions.ApiHub]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ApiHub/1.0.0-beta4
[Microsoft.Azure.WebJobs.Extensions.Twilio]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio/1.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions/2.1.0-beta1


<!-- Links to source --> 
[DocumentDBAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs
[CosmosDBTriggerAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.DocumentDB/Trigger/CosmosDBTriggerAttribute.cs
[EventHubAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs
[EventHubTriggerAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubTriggerAttribute.cs
[MobileTableAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs
[NotificationHubAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs 
[ServiceBusAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs
[ServiceBusAccountAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs
[QueueAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs
[StorageAccountAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs
[BlobAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs
[TableAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs
[TwilioSmsAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs
[SendGridAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs
[HttpTriggerAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs
[ApiHubFileAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.ApiHub/ApiHubFileAttribute.cs
[TimerTriggerAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs
