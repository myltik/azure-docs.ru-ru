---
title: "Справочник разработчика C# по функциям Azure"
description: "Узнайте, как разрабатывать Функции Azure с помощью C#."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "функции azure, функции, обработка событий, веб-перехватчики, динамические вычисления, независимая архитектура"
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/12/2017
ms.author: glenga
ms.openlocfilehash: 8a098d2ecc004b1593310579c47c53778858e799
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2018
---
# <a name="azure-functions-c-developer-reference"></a>Справочник разработчика C# по функциям Azure

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Эта статья описывает разработку функций Azure с помощью C# в библиотеках классов .NET.

Решение "Функции Azure" поддерживает языки программирования C# и скрипт C#. Рекомендации по [использованию C# на портале Azure](functions-create-function-app-portal.md) см. в [справочнике разработчика скриптов C# (.csx)](functions-reference-csharp.md).

В статье предполагается, что вы уже прочли следующие статьи:

* [Руководство для разработчиков по Функциям Azure](functions-reference.md)
* [Средства Visual Studio 2017 для Функций Azure](functions-develop-vs.md)

## <a name="functions-class-library-project"></a>Проект библиотеки классов функций

В Visual Studio шаблон проекта **Функции Azure** создает проект библиотеки классов C#, содержащий следующие файлы:

* [host.json](functions-host-json.md) — хранит параметры конфигурации, которые влияют на все функции в проекте при выполнении в локальной среде или в Azure.
* [local.settings.json](functions-run-local.md#local-settings-file) — хранит параметры приложений и строки подключения, которые используются при выполнении в локальной среде.

> [!IMPORTANT]
> Процесс сборки создает файл *function.json* для каждой функции. Этот файл *function.json* не предназначен для непосредственного редактирования. Невозможно изменить конфигурацию привязки или отключить функцию путем редактирования этого файла. Чтобы отключить функцию, используйте атрибут [Отключить](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs). Например, добавьте логический параметр приложения MY_TIMER_DISABLED и примените `[Disable("MY_TIMER_DISABLED")]` к функции. Изменяя значение этого параметра, можно включать и отключать функцию.

### <a name="functionname-and-trigger-attributes"></a>FunctionName и атрибуты триггеров

В библиотеке классов функция — это статический метод с `FunctionName` и атрибутом триггера, как показано в следующем примере:

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
} 
```

Атрибут `FunctionName` помечает метод как точку входа функции. Имя должно быть уникальным в пределах проекта.

Атрибут триггера указывает тип триггера и привязывает входные данные к параметру метода. Пример функции срабатывает по сообщению очереди, а сообщение очереди передается методу в параметре `myQueueItem`.

### <a name="additional-binding-attributes"></a>Дополнительные атрибуты привязок

Могут использоваться дополнительные входные и выходные атрибуты привязки. В следующем примере изменяется предыдущий путем добавления привязки очереди вывода. Функция записывает сообщение очереди ввода в новое сообщение очереди, в другой очереди.

```csharp
public static class SimpleExampleWithOutput
{
    [FunctionName("CopyQueueMessage")]
    public static void Run(
        [QueueTrigger("myqueue-items-source")] string myQueueItem, 
        [Queue("myqueue-items-destination")] out string myQueueItemCopy,
        TraceWriter log)
    {
        log.Info($"CopyQueueMessage function processed: {myQueueItem}");
        myQueueItemCopy = myQueueItem;
    }
}
```

### <a name="conversion-to-functionjson"></a>Преобразование в файл function.json

Процесс сборки создает файл *function.json* в папке функции в папке сборки. Как уже говорилось, этот файл не предназначен для непосредственного редактирования. Невозможно изменить конфигурацию привязки или отключить функцию путем редактирования этого файла. 

Назначение этого файла — предоставить сведения контроллеру масштаба для [принятия решений о масштабировании плана потребления](functions-scale.md#how-the-consumption-plan-works). По этой причине файл содержит только сведения о триггерах, но не о входных или выходных привязках.

Создаваемый файл *function.json* содержит свойство `configurationSource`, которое указывает среде выполнения использовать атрибуты .NET для привязок, вместо конфигурации *function.json*. Ниже приведен пример:

```json
{
  "generatedBy": "Microsoft.NET.Sdk.Functions-1.0.0.0",
  "configurationSource": "attributes",
  "bindings": [
    {
      "type": "queueTrigger",
      "queueName": "%input-queue-name%",
      "name": "myQueueItem"
    }
  ],
  "disabled": false,
  "scriptFile": "..\\bin\\FunctionApp1.dll",
  "entryPoint": "FunctionApp1.QueueTrigger.Run"
}
```

Создание файла *function.json* выполняется пакетом NuGet [Microsoft\.NET\.Sdk\.Functions](http://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). Исходный код доступен в репозитории GitHub [azure\-functions\-vs\-build\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="supported-types-for-bindings"></a>Поддерживаемые типы для привязок

Все привязки поддерживают определенные типы. Например, атрибут триггера большого двоичного объекта можно применить к строковому параметру, параметру POCO, параметру `CloudBlockBlob` или любому из нескольких других поддерживаемых типов. В [справочной статье о привязках для больших двоичных объектов](functions-bindings-storage-blob.md#trigger---usage) содержится список всех поддерживаемых типов параметров. Дополнительные сведения см. в статье о [триггерах и привязках](functions-triggers-bindings.md) и в [справочной документации по каждому типу привязки](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Привязка к возвращаемому значению метода

Используйте возвращаемое значение метода для выходной привязки, как показано в следующем примере:

```csharp
public static class ReturnValueOutputBinding
{
    [FunctionName("CopyQueueMessageUsingReturnValue")]
    [return: Queue("myqueue-items-destination")]
    public static string Run(
        [QueueTrigger("myqueue-items-source-2")] string myQueueItem,
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
        return myQueueItem;
    }
}
```

## <a name="writing-multiple-output-values"></a>Написание нескольких значений выходных данных

Чтобы записать несколько значений в выходную привязку, используйте тип [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) или [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs). Эти типы представляют собой доступные только для записи коллекции, записываемые в выходную привязку по завершении метода.

В следующем примере записываются несколько сообщений очереди в ту же очередь с помощью `ICollector`:

```csharp
public static class ICollectorExample
{
    [FunctionName("CopyQueueMessageICollector")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-3")] string myQueueItem,
        [Queue("myqueue-items-destination")] ICollector<string> myQueueItemCopy,
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
        myQueueItemCopy.Add($"Copy 1: {myQueueItem}");
        myQueueItemCopy.Add($"Copy 2: {myQueueItem}");
    }
}
```

## <a name="logging"></a>Ведение журналов

Для записи выходных данных в потоковые журналы в C# включите аргумент с типом `TraceWriter`. Рекомендуем присвоить ему имя `log`. Не используйте `Console.Write` в Функциях Azure. 

`TraceWriter` определяется в [пакете SDK веб-заданий Azure](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/TraceWriter.cs). Уровень ведения журнала для `TraceWriter` можно настроить в [host.json](functions-host-json.md).

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
} 
```

> [!NOTE]
> Сведения о новой платформе ведения журналов, которую можно использовать вместо `TraceWriter`, см. в разделе [Запись журналов в функциях C#](functions-monitoring.md#write-logs-in-c-functions) статьи **Мониторинг Функций Azure**.

## <a name="async"></a>Асинхронный режим

Чтобы сделать функцию асинхронной, используйте ключевое слово `async` и верните объект `Task`.

```csharp
public static class AsyncExample
{
    [FunctionName("BlobCopy")]
    public static async Task RunAsync(
        [BlobTrigger("sample-images/{blobName}")] Stream blobInput,
        [Blob("sample-images-copies/{blobName}", FileAccess.Write)] Stream blobOutput,
        CancellationToken token,
        TraceWriter log)
    {
        log.Info($"BlobCopy function processed.");
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
}
```

## <a name="cancellation-tokens"></a>Токены отмены

Для некоторых операций необходимо выполнить нормальное завершение работы. Всегда лучше написать код, который может обрабатывать сбои. Но в случаях когда нужно обрабатывать запросы на завершение работы, можно определить аргумент с типом [CancellationToken](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx).  В случае завершения работы узла будет предоставлен маркер `CancellationToken`.

```csharp
public static class CancellationTokenExample
{
    [FunctionName("BlobCopy")]
    public static async Task RunAsync(
        [BlobTrigger("sample-images/{blobName}")] Stream blobInput,
        [Blob("sample-images-copies/{blobName}", FileAccess.Write)] Stream blobOutput,
        CancellationToken token)
    {
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
}
```

## <a name="environment-variables"></a>Переменные среды

Чтобы получить значение переменной среды или значение параметра приложения, используйте `System.Environment.GetEnvironmentVariable`, как показано в следующем примере кода.

```csharp
public static class EnvironmentVariablesExample
{
    [FunctionName("GetEnvironmentVariables")]
    public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
    {
        log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
        log.Info(GetEnvironmentVariable("AzureWebJobsStorage"));
        log.Info(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
    }

    public static string GetEnvironmentVariable(string name)
    {
        return name + ": " +
            System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
    }
}
```

## <a name="binding-at-runtime"></a>Привязка во время выполнения

Для C# и других языков .NET можно использовать шаблон [императивной](https://en.wikipedia.org/wiki/Imperative_programming) привязки, которая отличается от [*декларативной*](https://en.wikipedia.org/wiki/Declarative_programming) привязки в атрибутах. Императивную привязку удобно использовать, когда параметры привязки должны вычисляться не при проектировании, а во время выполнения. С использованием такого шаблона можно моментально выполнить привязку к поддерживаемым входным и выходным привязкам в коде функции.

Определите принудительную привязку следующим образом.

- **Не** добавляйте атрибут в сигнатуру функции для нужных императивных привязок.
- Передайте входной параметр [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) или [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- Используйте следующий шаблон C# для привязки данных,

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  где `BindingTypeAttribute` — атрибут .NET, определяющий пользовательскую привязку, а `T` — входной или выходной тип, поддерживаемый этим типом привязки. `T` не может быть параметром типа `out` (например, `out JObject`). Например, выходная привязка таблицы мобильных приложений поддерживает [шесть выходных типов](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), но с императивной привязкой можно использовать только [ICollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) или [IAsyncCollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs).

### <a name="single-attribute-example"></a>Пример с одним атрибутом

В следующем примере кода создается [выходная привязка большого двоичного объекта службы хранилища](functions-bindings-storage-blob.md#output) с путем к большому двоичному объекту, определенному во время выполнения, а затем записывается строка в большой двоичный объект.

```cs
public static class IBinderExample
{
    [FunctionName("CreateBlobUsingBinder")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-4")] string myQueueItem,
        IBinder binder,
        TraceWriter log)
    {
        log.Info($"CreateBlobUsingBinder function processed: {myQueueItem}");
        using (var writer = binder.Bind<TextWriter>(new BlobAttribute(
                    $"samples-output/{myQueueItem}", FileAccess.Write)))
        {
            writer.Write("Hello World!");
        };
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) определяет входную или выходную привязку [большого двоичного объекта службы хранилища](functions-bindings-storage-blob.md), а [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) представляет собой поддерживаемый тип выходной привязки.

### <a name="multiple-attribute-example"></a>Пример с несколькими атрибутами

В предыдущем примере код получает параметр приложения для строки подключения основной учетной записи хранения приложения-функции (т. е. `AzureWebJobsStorage`). Вы можете указать пользовательский параметр приложения, который следует использовать для учетной записи хранения. Для этого добавьте [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) и передайте массив атрибутов в `BindAsync<T>()`. Используйте параметр `Binder`, а не `IBinder`.  Например: 

```cs
public static class IBinderExampleMultipleAttributes
{
    [FunctionName("CreateBlobInDifferentStorageAccount")]
    public async static Task RunAsync(
            [QueueTrigger("myqueue-items-source-binder2")] string myQueueItem,
            Binder binder,
            TraceWriter log)
    {
        log.Info($"CreateBlobInDifferentStorageAccount function processed: {myQueueItem}");
        var attributes = new Attribute[]
        {
        new BlobAttribute($"samples-output/{myQueueItem}", FileAccess.Write),
        new StorageAccountAttribute("MyStorageAccount")
        };
        using (var writer = await binder.BindAsync<TextWriter>(attributes))
        {
            await writer.WriteAsync("Hello World!!");
        }
    }
}
```

## <a name="triggers-and-bindings"></a>Триггеры и привязки 

В таблице ниже перечислены атрибуты триггеров и привязок, доступные в проекте библиотеки классов Функций Azure. Все атрибуты находятся в пространстве имен `Microsoft.Azure.WebJobs`.

| Триггер | Входные данные | Выходные данные|
|------   | ------    | ------  |
| [BlobTrigger](functions-bindings-storage-blob.md#trigger---attributes)| [Большой двоичный объект](functions-bindings-storage-blob.md#input---attributes)| [Большой двоичный объект](functions-bindings-storage-blob.md#output---attributes)|
| [CosmosDBTrigger](functions-bindings-cosmosdb.md#trigger---attributes)| [DocumentDB](functions-bindings-cosmosdb.md#input---attributes)| [DocumentDB](functions-bindings-cosmosdb.md#output---attributes) |
| [EventHubTrigger](functions-bindings-event-hubs.md#trigger---attributes)|| [EventHub](functions-bindings-event-hubs.md#output---attributes) |
| [HTTPTrigger](functions-bindings-http-webhook.md#trigger---attributes)|||
| [QueueTrigger](functions-bindings-storage-queue.md#trigger---attributes)|| [Очередь](functions-bindings-storage-queue.md#output---attributes) |
| [ServiceBusTrigger](functions-bindings-service-bus.md#trigger---attributes)|| [Служебная шина](functions-bindings-service-bus.md#output---attributes) |
| [TimerTrigger](functions-bindings-timer.md#attributes) | ||
| |[ApiHubFile](functions-bindings-external-file.md)| [ApiHubFile](functions-bindings-external-file.md)|
| |[MobileTable](functions-bindings-mobile-apps.md#input---attributes)| [MobileTable](functions-bindings-mobile-apps.md#output---attributes) | 
| |[Таблица](functions-bindings-storage-table.md#input---attributes)| [Таблица](functions-bindings-storage-table.md#output---attributes)  | 
| ||[NotificationHub](functions-bindings-notification-hubs.md#attributes) |
| ||[SendGrid](functions-bindings-sendgrid.md#attributes) |
| ||[Twilio](functions-bindings-twilio.md#attributes)| 

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Дополнительные сведения о триггерах и привязках](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Дополнительные сведения о рекомендациях по решению "Функции Azure"](functions-best-practices.md)
