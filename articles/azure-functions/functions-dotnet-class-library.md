---
title: Справочник разработчика C# по функциям Azure
description: Узнайте, как разрабатывать Функции Azure с помощью C#.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: функции azure, функции, обработка событий, веб-перехватчики, динамические вычисления, независимая архитектура
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/12/2017
ms.author: tdykstra
ms.openlocfilehash: c1b04968f83271006240fc0e099175e9017574ae
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
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

## <a name="methods-recognized-as-functions"></a>Методы, распознаваемые как функции

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

Атрибут `FunctionName` помечает метод как точку входа функции. Имя должно быть уникальным в пределах проекта. Шаблоны проектов часто создают метод `Run`, но метод может иметь любое допустимое имя для метода C#.

Атрибут триггера указывает тип триггера и привязывает входные данные к параметру метода. Пример функции срабатывает по сообщению очереди, а сообщение очереди передается методу в параметре `myQueueItem`.

## <a name="method-signature-parameters"></a>Параметры сигнатуры метода

Сигнатура метода может содержать параметры, отличные от используемых с атрибутом триггера. Ниже приведены некоторые дополнительные параметры, которые можно включить:

* [Входные и выходные привязки](functions-triggers-bindings.md), помеченные как таковые путем дополнения атрибутами.  
* Параметр `ILogger` или `TraceWriter` [ведения журнала](#logging).
* Параметр `CancellationToken` для [нормального завершения работы](#cancellation-tokens).
* Параметры [выражений привязок](functions-triggers-bindings.md#binding-expressions-and-patterns) для получения метаданных триггера.

Порядок параметров в сигнатуре функции не имеет значения. Например, можно указать параметры триггера до или после других привязок, а параметр для средства ведения журнала — до или после параметров триггера или привязки.

### <a name="output-binding-example"></a>Пример выходной привязки

В следующем примере изменяется предыдущий путем добавления привязки очереди вывода. Функция записывает сообщение очереди, запускающее функцию для нового сообщения очереди в другую очередь.

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

В справочных статьях по привязкам (например, [Привязки хранилища очередей Azure для службы "Функции Azure"](functions-bindings-storage-queue.md)) объясняется, какие типы параметров можно использовать с триггерами, а также с атрибутами входных или выходных привязок.

### <a name="binding-expressions-example"></a>Пример выражений привязки

Приведенный ниже код позволяет получить имя очереди для мониторинга из настроек приложения и время создания сообщения очереди в параметре `insertionTime`.

```csharp
public static class BindingExpressionsExample
{
    [FunctionName("LogQueueMessage")]
    public static void Run(
        [QueueTrigger("%queueappsetting%")] string myQueueItem,
        DateTimeOffset insertionTime,
        TraceWriter log)
    {
        log.Info($"Message content: {myQueueItem}");
        log.Info($"Created at: {insertionTime}");
    }
}
```

## <a name="autogenerated-functionjson"></a>Автоматически созданный файл function.json

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

## <a name="microsoftnetsdkfunctions"></a>Microsoft.NET.Sdk.Functions

Создание файла *function.json* выполняется пакетом NuGet [Microsoft\.NET\.Sdk\.Functions](http://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). 

Тот же пакет используется в обеих версиях (1.x и 2.x) среды выполнения Функций. Проекты 1.x и 2.x отличаются только требуемой версией .NET Framework. Ниже приведены фрагменты файлов *.csproj* с разными требуемыми версиями .NET Framework и одним пакетом `Sdk`.

**Функции 1.x**

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

**Функции 2.x**

```xml
<PropertyGroup>
  <TargetFramework>netstandard2.0</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

К зависимостям пакетов `Sdk` относятся триггеры и привязки. Проект 1.x ссылается на триггеры и привязки версии 1.x, так как этого требует платформа .NET Framework, а триггерам и привязкам версии 2.x требуется платформа .NET Core.

Пакет `Sdk` также зависит от пакета [Newtonsoft.Json](http://www.nuget.org/packages/Newtonsoft.Json) и косвенно от пакета [WindowsAzure.Storage](http://www.nuget.org/packages/WindowsAzure.Storage). Эти зависимости гарантируют, что проект использует версии пакетов, совместимые с версией среды выполнения Функций, для которой он предназначен. Например, при использовании платформы .NET Framework 4.6.1 пакет `Newtonsoft.Json` имеет версию 11, но среда выполнения Функций, предназначенная для платформы .NET Framework 4.6.1, совместима только с пакетом `Newtonsoft.Json` версии 9.0.1. Поэтому код функции в этом проекте также должен использовать пакет `Newtonsoft.Json` версии 9.0.1.

Исходный код пакета `Microsoft.NET.Sdk.Functions` доступен в репозитории GitHub [azure\-functions\-vs\-build\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="runtime-version"></a>Версия среды выполнения

Visual Studio выполняет проекты Функций с помощью [основных инструментов Функций Azure](functions-run-local.md#install-the-azure-functions-core-tools). Основные инструменты — это интерфейс командной строки среды выполнения Функций.

Если вы установите основные инструменты с помощью npm, это не повлияет на версию инструментов, используемую Visual Studio. Версии основных инструментов среды выполнения Функций 1.x хранятся в файле *%USERPROFILE%\AppData\Local\Azure.Functions.Cli*. Visual Studio использует последнюю версию. Основные инструменты Функций 2.x включены в расширение **Инструменты для Функций Azure и веб-заданий**. Используемую в 1.x и 2.x версию можно просмотреть в выходных данных консоли при запуске проекта Функций:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="supported-types-for-bindings"></a>Поддерживаемые типы для привязок

Все привязки поддерживают определенные типы. Например, атрибут триггера большого двоичного объекта можно применить к строковому параметру, параметру POCO, параметру `CloudBlockBlob` или любому из нескольких других поддерживаемых типов. В [справочной статье о привязках для больших двоичных объектов](functions-bindings-storage-blob.md#trigger---usage) содержится список всех поддерживаемых типов параметров. Дополнительные сведения см. в статье о [триггерах и привязках](functions-triggers-bindings.md) и в [справочной документации по каждому типу привязки](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Привязка к возвращаемому значению метода

Возвращаемое значение метода можно использовать для привязки выходных данных. Для этого примените атрибут к возвращаемому значению метода. Примеры см. в статье о [триггерах и привязках](functions-triggers-bindings.md#using-the-function-return-value).

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

Чтобы сделать функцию [асинхронной](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/), используйте ключевое слово `async` и верните объект `Task`.

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

Функция может принимать параметр [CancellationToken](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx), который позволяет операционной системе передавать в ваш код сведения о том, что выполнение функции будет завершено. Это уведомление можно использовать для предотвращения ситуации, когда выполнение функции завершается неожиданно, оставляя данные в несогласованном состоянии.

В следующем примере показано, как проверить, не приближается ли завершение выполнения функции.

```csharp
public static class CancellationTokenExample
{
    public static void Run(
        [QueueTrigger("inputqueue")] string inputText,
        TextWriter logger,
        CancellationToken token)
    {
        for (int i = 0; i < 100; i++)
        {
            if (token.IsCancellationRequested)
            {
                logger.WriteLine("Function was cancelled at iteration {0}", i);
                break;
            }
            Thread.Sleep(5000);
            logger.WriteLine("Normal processing for queue message={0}", inputText);
        }
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
