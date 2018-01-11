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
ms.openlocfilehash: 8bd46adc475af35d32b9e329a3765e064120a6e3
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/05/2018
---
# <a name="azure-functions-c-developer-reference"></a>Справочник разработчика C# по функциям Azure

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Эта статья содержит общие сведения о разработке функции Azure с помощью C# в библиотеках классов .NET.

Функции Azure поддерживает C# и C# сценария языков программирования. Если вы ищете рекомендации [с помощью C# на портале Azure](functions-create-function-app-portal.md), в разделе [Справочник разработчика скрипт (.csx) C#](functions-reference-csharp.md).

В этой статье предполагается, что вы уже ознакомились со следующими статьями:

* [Руководство для разработчиков Azure функции](functions-reference.md)
* [Azure функции набора средств Visual Studio 2017 г.](functions-develop-vs.md)

## <a name="functions-class-library-project"></a>Проект библиотеки классов функций

В Visual Studio **функции Azure** шаблон проекта создает C# проект библиотеки классов, содержащий следующие файлы:

* [Host.JSON](functions-host-json.md) -хранит параметры настройки, которые влияют на все функции в проекте при выполнении локально или в Azure.
* [Local.Settings.JSON](functions-run-local.md#local-settings-file) -хранит параметры приложений и строки соединения, которые используются, когда выполняется локально.

### <a name="functionname-and-trigger-attributes"></a>Атрибуты FunctionName и триггер

В библиотеке классов функции — это статический метод с `FunctionName` и атрибут триггер, как показано в следующем примере:

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

`FunctionName` Помечает метод как точку входа функции. Имя должно быть уникальным в рамках проекта.

Атрибут триггер указывает тип триггера и привязывает входные данные параметра метода. Пример функции очереди сообщений, а также сообщения очереди передается методу в `myQueueItem` параметра.

### <a name="additional-binding-attributes"></a>Атрибуты дополнительные привязки

Могут использоваться дополнительные входные и выходные атрибуты привязки. В следующем примере изменяется предыдущим, добавив привязку очереди вывода. Функция записывает сообщение очереди ввода нового сообщения очереди в другую очередь.

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

Процесс построения создает *function.json* файл в папке функции в папке построения. Этот файл не предназначен для непосредственного редактирования. Невозможно изменить конфигурацию привязки или отключите функцию путем редактирования этого файла. 

Этот файл предназначен для предоставления сведений о шкале контроллера для [масштабировании решения в плане потребления](functions-scale.md#how-the-consumption-plan-works). По этой причине файл имеет только сведения триггера, не входного или выходного привязок.

Созданный *function.json* файл, содержащий `configurationSource` свойство, которое сообщает среде выполнения используются атрибуты .NET для привязок, а не *function.json* конфигурации. Ниже приведен пример:

{«generatedBy»: «Microsoft.NET.Sdk.Functions-1.0.0.0», «configurationSource»: «атрибуты», «привязки»: [{«тип»: «queueTrigger», «имя»: «% входных данных очереди %», «имя»: «myQueueItem»}], «отключено»: значение "false", «scriptFile»: «.. \\bin\\FunctionApp1.dll», «entryPoint»: «FunctionApp1.QueueTrigger.Run»}

*Function.json* выполняется создание файла пакета NuGet [Microsoft\.NET\.Sdk\.функции](http://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). Исходный код доступен в репозитории GitHub [azure\-функции\-vs\-построения\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="supported-types-for-bindings"></a>Поддерживаемые типы для привязки

Каждая привязка имеет свой собственный поддерживаемые типы; для экземпляра атрибут триггер больших двоичных объектов может применяться к строковый параметр, параметр POCO, `CloudBlockBlob` параметр или любого из некоторых других поддерживаемых типов. [Статье привязки для привязки больших двоичных объектов](functions-bindings-storage-blob.md#trigger---usage) список всех поддерживаемых типов параметров. Дополнительные сведения см. в разделе [триггеры и привязки](functions-triggers-bindings.md) и [docs ссылки привязки для каждого типа привязки](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Привязка к возвращаемому значению метода

Возвращаемое значение метода можно использовать для привязки выходные данные, как показано в следующем примере:

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
> Сведения о новой framework ведения журнала, который можно использовать вместо `TraceWriter`, в разделе [записи журналов в C# функции](functions-monitoring.md#write-logs-in-c-functions) в **монитор функции Azure** статьи.

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

Для некоторых операций необходимо выполнить нормальное завершение работы. Когда всегда лучше написать код, который может обрабатывать аварийное завершение работы, с в случаях, когда необходимо обработать определить запросов закрытия [CancellationToken](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) типом аргумента.  В случае завершения работы узла будет предоставлен маркер `CancellationToken`.

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

В C# и других языков .NET, можно использовать [императивного](https://en.wikipedia.org/wiki/Imperative_programming) привязки шаблона, в отличие от [ *декларативный* ](https://en.wikipedia.org/wiki/Declarative_programming) привязок в атрибутах. Императивную привязку удобно использовать, когда параметры привязки должны вычисляться не при проектировании, а во время выполнения. С этим шаблоном можно привязать к поддерживаемой входные и выходные данные привязки на лету в коде функции.

Определите принудительную привязку следующим образом.

- **Нет** включения атрибута в сигнатуру функции для нужного императивного привязок.
- Передайте входной параметр [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) или [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- Используйте следующий шаблон C# для привязки данных,

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute`является атрибутом .NET, который определяет пользовательскую привязку и `T` — это входной или выходной тип, поддерживаемый этого типа привязки. `T`не может быть `out` тип параметра (например, `out JObject`). Например, в таблице мобильные приложения выводиться привязка поддерживает [шесть выходные типы](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), но можно использовать только [ICollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) или [IAsyncCollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs)с использованием императивного привязки.

### <a name="single-attribute-example"></a>Пример одного атрибута

В следующем примере кода создается [выходная привязка большого двоичного объекта службы хранилища](functions-bindings-storage-blob.md#input--output) с путем к большому двоичному объекту, определенному во время выполнения, а затем записывается строка в большой двоичный объект.

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

### <a name="multiple-attribute-example"></a>Пример нескольких атрибута

Предыдущий пример возвращает параметр приложения для приложения функции основного хранилища строка подключения для учетной записи (который является `AzureWebJobsStorage`). Вы можете указать пользовательский параметр приложения, который следует использовать для учетной записи хранения. Для этого добавьте [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) и передайте массив атрибутов в `BindAsync<T>()`. Используйте `Binder` параметра, не `IBinder`.  Например: 

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

Ниже перечислены триггер и атрибуты привязки, доступные в проекте библиотеки классов функций Azure. Все атрибуты находятся в пространстве имен `Microsoft.Azure.WebJobs`.

| Триггер | Входные данные | Выходные данные|
|------   | ------    | ------  |
| [BlobTrigger](functions-bindings-storage-blob.md#trigger---attributes)| [Большой двоичный объект](functions-bindings-storage-blob.md#input--output---attributes)| [Большой двоичный объект](functions-bindings-storage-blob.md#input--output---attributes)|
| [CosmosDBTrigger](functions-bindings-documentdb.md#trigger---attributes)| [DocumentDB](functions-bindings-documentdb.md#input---attributes)| [DocumentDB](functions-bindings-documentdb.md#output---attributes) |
| [EventHubTrigger](functions-bindings-event-hubs.md#trigger---attributes)|| [EventHub](functions-bindings-event-hubs.md#output---attributes) |
| [HTTPTrigger](functions-bindings-http-webhook.md#trigger---attributes)|||
| [QueueTrigger](functions-bindings-storage-queue.md#trigger---attributes)|| [Очередь](functions-bindings-storage-queue.md#output---attributes) |
| [ServiceBusTrigger](functions-bindings-service-bus.md#trigger---attributes)|| [Служебная шина](functions-bindings-service-bus.md#output---attributes) |
| [TimerTrigger](functions-bindings-timer.md#attributes) | ||
| |[ApiHubFile](functions-bindings-external-file.md)| [ApiHubFile](functions-bindings-external-file.md)|
| |[MobileTable](functions-bindings-mobile-apps.md#input---attributes)| [MobileTable](functions-bindings-mobile-apps.md#output---attributes) | 
| |[Таблица](functions-bindings-storage-table.md#input---attributes)| [Таблица](functions-bindings-storage-table.md#output---attributes)  | 
| ||[Концентратора уведомлений](functions-bindings-notification-hubs.md#attributes) |
| ||[SendGrid](functions-bindings-sendgrid.md#attributes) |
| ||[Twilio](functions-bindings-twilio.md#attributes)| 

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Дополнительные сведения о триггерах и привязки](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Дополнительные сведения о рекомендациях для функций Azure](functions-best-practices.md)
