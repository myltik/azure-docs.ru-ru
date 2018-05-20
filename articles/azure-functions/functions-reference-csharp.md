---
title: Справочник разработчика скриптов C# по функциям Azure
description: Узнайте, как разрабатывать функции Azure с помощью скрипта C#.
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
ms.openlocfilehash: 9de8119cbde486800639bc5f3559a1a2859ec204
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
---
# <a name="azure-functions-c-script-csx-developer-reference"></a>Справочник разработчика скрипта C# (CSX) по решению "Функции Azure"

<!-- When updating this article, make corresponding changes to any duplicate content in functions-dotnet-class-library.md -->

В этой статье содержатся общие сведения о разработке функций Azure с помощью скрипта C# (*CSX*).

Решение "Функции Azure" поддерживает языки программирования C# и скрипт C#. Рекомендации по [использованию C# в проекте библиотеки классов Visual Studio](functions-develop-vs.md) см. в [справочнике разработчика C#](functions-dotnet-class-library.md).

В этой статье предполагается, что вы уже прочли [руководство для разработчиков по решению "Функции Azure"](functions-reference.md).

## <a name="how-csx-works"></a>Как работает формат CSX

Интерфейс скрипта C# для решения "Функции Azure" основан на [пакете SDK для веб-заданий Azure](https://github.com/Azure/azure-webjobs-sdk/wiki/Introduction). Данные поступают в функцию C# через аргументы метода. Имена аргументов указываются в файле `function.json`. Есть предварительно определенные имена для доступа к таким объектам, как средство ведения журнала функций и маркеры отмены.

Формат *CSX* позволяет писать меньше стандартного кода и сосредоточиться на написании только функции C#. а затем вместо помещения всего кода в пространство имен и класс просто определите метод `Run`. Как обычно, укажите ссылки на необходимые сборки и пространства имен в начале файла,

Файлы *CSX* приложения-функции компилируются при инициализации экземпляра. Этот этап компиляции означает, что такие операции, как холодный запуск, могут занимать больше времени при использовании функций скрипта C#, по сравнению с библиотеками классов C#. Кроме того, благодаря этому этапу компиляции функции скрипта C# доступны для редактирования на портале Azure, в отличие от библиотек классов C#.

## <a name="binding-to-arguments"></a>Привязка к аргументам

Входные или выходные данные привязываются к параметру функции скрипта C# прпи помощи свойства `name` в файле конфигурации *function.json*. В следующем примере представлены файлы *function.json* и *run.csx* для функции, срабатывающей по запросу. Параметр для получения данных из сообщения очереди называется `myQueueItem`, так как это значение свойства `name`.

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem.AsString}");
}
```

Оператор `#r` описан [далее в этой статье](#referencing-external-assemblies).

## <a name="supported-types-for-bindings"></a>Поддерживаемые типы для привязок

Для каждой привязки есть собственные поддерживаемые типы. Например, триггер больших двоичных объектов можно использовать со строковым параметром, параметрами POCO, `CloudBlockBlob` или любым из нескольких других поддерживаемых типов. В [справочной статье о привязках для больших двоичных объектов](functions-bindings-storage-blob.md#trigger---usage) содержится список всех поддерживаемых типов параметров для триггеров больших двоичных объектов. Дополнительные сведения см. в статье о [триггерах и привязках](functions-triggers-bindings.md) и в [справочной документации по каждому типу привязки](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="referencing-custom-classes"></a>Ссылки на пользовательские классы

Если нужно использовать пользовательский класс простых старых объектов CLR (POCO), вы можете включить определении класса в тот же файл или поместить его в отдельный файл.

В следующем примере показан пример файла *run.csx*, содержащего определение класса POCO.

```csharp
public static void Run(string myBlob, out MyClass myQueueItem)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
    myQueueItem = new MyClass() { Id = "myid" };
}

public class MyClass
{
    public string Id { get; set; }
}
```

Для каждого свойства класса POCO нужно определить методы получения и задания.

## <a name="reusing-csx-code"></a>Повторное использование кода CSX

В файле *run.csx* можно использовать классы и методы, определенные в других *CSX*-файлах. Для этого используйте директивы `#load` в файле *run.csx*. В следующем примере к процедуре ведения журнала с именем `MyLogger` предоставляется общий доступ в файле *myLogger.csx*, а также она загружается в файл *run.csx*. Для этого используется директива `#load`:

Пример *run.csx*:

```csharp
#load "mylogger.csx"

public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Verbose($"Log by run.csx: {DateTime.Now}");
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

Пример *mylogger.csx*:

```csharp
public static void MyLogger(TraceWriter log, string logtext)
{
    log.Verbose(logtext);
}
```

Обычно для задания строго типизированных данных, передаваемых между функциями с использованием объекта POCO, применяется общий файл с расширением *CSX*. В указанном ниже упрощенном примере триггер HTTP и очереди совместно используют объект POCO с именем `Order` для задания строго типизированных данных о заказах.

Пример файла *run.csx* для триггера HTTP:

```cs
#load "..\shared\order.csx"

using System.Net;

public static async Task<HttpResponseMessage> Run(Order req, IAsyncCollector<Order> outputQueueItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function received an order.");
    log.Info(req.ToString());
    log.Info("Submitting to processing queue.");

    if (req.orderId == null)
    {
        return new HttpResponseMessage(HttpStatusCode.BadRequest);
    }
    else
    {
        await outputQueueItem.AddAsync(req);
        return new HttpResponseMessage(HttpStatusCode.OK);
    }
}
```

Пример файла *run.csx* для триггера очереди:

```cs
#load "..\shared\order.csx"

using System;

public static void Run(Order myQueueItem, out Order outputQueueItem,TraceWriter log)
{
    log.Info($"C# Queue trigger function processed order...");
    log.Info(myQueueItem.ToString());

    outputQueueItem = myQueueItem;
}
```

Пример файла *order.csx*:

```cs
public class Order
{
    public string orderId {get; set; }
    public string custName {get; set;}
    public string custAddress {get; set;}
    public string custEmail {get; set;}
    public string cartId {get; set; }

    public override String ToString()
    {
        return "\n{\n\torderId : " + orderId +
                  "\n\tcustName : " + custName +             
                  "\n\tcustAddress : " + custAddress +             
                  "\n\tcustEmail : " + custEmail +             
                  "\n\tcartId : " + cartId + "\n}";             
    }
}
```

В директиве `#load` можно использовать относительный путь:

* `#load "mylogger.csx"` загружает файл, расположенный в папке функции.
* `#load "loadedfiles\mylogger.csx"` загружает файл, расположенный в папке, которая содержится в папке функции.
* `#load "..\shared\mylogger.csx"` загружает файл, расположенный в папке на том же уровне, что и папка функции, то есть непосредственно в разделе *wwwroot*.

Директива `#load` работает только с *CSX*-файлами, но не с *CS*-файлами.

## <a name="binding-to-method-return-value"></a>Привязка к возвращаемому значению метода

Используйте возвращаемое значение метода для выходной привязки, указав имя `$return` в *function.json*. Примеры см. в статье о [триггерах и привязках](functions-triggers-bindings.md#using-the-function-return-value).

## <a name="writing-multiple-output-values"></a>Написание нескольких значений выходных данных

Чтобы записать несколько значений в выходную привязку, используйте тип [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) или [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs). Эти типы представляют собой доступные только для записи коллекции, записываемые в выходную привязку по завершении метода.

В следующем примере записываются несколько сообщений очереди в ту же очередь с помощью `ICollector`:

```csharp
public static void Run(ICollector<string> myQueueItem, TraceWriter log)
{
    myQueueItem.Add("Hello");
    myQueueItem.Add("World!");
}
```

## <a name="logging"></a>Ведение журналов

Для записи выходных данных в потоковые журналы в C# включите аргумент с типом `TraceWriter`. Рекомендуем присвоить ему имя `log`. Не используйте `Console.Write` в Функциях Azure. 

`TraceWriter` определяется в [пакете SDK веб-заданий Azure](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/TraceWriter.cs). Уровень ведения журнала для `TraceWriter` можно настроить в [host.json](functions-host-json.md).

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

> [!NOTE]
> Сведения о новой платформе ведения журналов, которую можно использовать вместо `TraceWriter`, см. в разделе [Запись журналов в функциях C#](functions-monitoring.md#write-logs-in-c-functions) статьи **Мониторинг Функций Azure**.

## <a name="async"></a>Асинхронный режим

Чтобы сделать функцию [асинхронной](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/), используйте ключевое слово `async` и верните объект `Task`.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName,
        Stream blobInput,
        Stream blobOutput)
{
    await blobInput.CopyToAsync(blobOutput, 4096);
}
```

## <a name="cancellation-tokens"></a>Токены отмены

Функция может принимать параметр [CancellationToken](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx), который позволяет операционной системе передавать в ваш код сведения о том, что выполнение функции будет завершено. Это уведомление можно использовать для предотвращения ситуации, когда выполнение функции завершается неожиданно, оставляя данные в несогласованном состоянии.

В следующем примере показано, как проверить, не приближается ли завершение выполнения функции.

```csharp
using System;
using System.IO;
using System.Threading;

public static void Run(
    string inputText,
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
```

## <a name="importing-namespaces"></a>Импорт пространств имен

Если требуется импортировать пространства имен, это можно сделать как обычно — с помощью предложения `using` .

```csharp
using System.Net;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

Следующие пространства имен импортируются автоматически и поэтому являются необязательными:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`

## <a name="referencing-external-assemblies"></a>Ссылки на внешние сборки

Для сборок платформы добавьте ссылки с помощью директивы `#r "AssemblyName"` .

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

Следующие сборки автоматически добавляются средой внешнего размещения Функций Azure:

* `mscorlib`
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`

К следующим сборкам можно обращаться по простому имени (например, `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNet.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

## <a name="referencing-custom-assemblies"></a>Ссылки на пользовательские сборки

Чтобы указать ссылку на пользовательские сборки, можно использовать *общую* или *закрытую* сборку.
- Общие сборки совместно используют все функции в приложении-функции. Чтобы добавить ссылку на пользовательскую сборку, отправьте ее в папку с именем `bin` в [корневом каталоге приложения-функции](functions-reference.md#folder-structure) (wwwroot). 
- Закрытые сборки входят в контекст указанной функции и поддерживают загрузку неопубликованных приложений разных версий. Закрытые сборки необходимо отправить в папку `bin` в каталоге функции. Укажите ссылки на сборки, используя имя файла, например `#r "MyAssembly.dll"`. 

Дополнительные сведения о передаче файлов в папку функции см. в разделе об [управлении пакетами](#using-nuget-packages).

### <a name="watched-directories"></a>Каталоги отслеживания

Каталог, содержащий файл сценария функции, автоматически отслеживает изменения в сборках. Чтобы отслеживать изменения сборки в других каталогах, добавьте их в список `watchDirectories` в [host.json](functions-host-json.md).

## <a name="using-nuget-packages"></a>Использование пакетов NuGet

Чтобы использовать пакеты NuGet в функции C#, отправьте файл *project.json* в папку соответствующей функции в файловой системе приложения-функции. Ниже приведен пример файла *project.json* , который добавляет ссылку на Microsoft.ProjectOxford.Face версии 1.1.0.

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

В решении"Функции Azure" 1.x поддерживается только версия платформы .NET Framework 4.6. Поэтому убедитесь, что ваш файл *project.json* определяет `net46`, как показано ниже.

При отправке файла *project.json* среда выполнения получает пакеты и автоматически добавляет ссылки на сборки пакетов. Добавлять директивы `#r "AssemblyName"` не нужно. Для использования типов, определенных в пакетах NuGet, просто добавьте необходимые инструкции `using` в файл *run.csx*. 

В среде выполнения функций восстановление NuGet работает путем сравнения `project.json` и `project.lock.json`. Если метки даты и времени файлов **не** совпадают, выполняется восстановление NuGet и скачиваются обновленные пакеты. Однако если дата и время создания файлов **совпадают**, NuGet не выполняет восстановление. Поэтому не следует развертывать `project.lock.json`, так как в результате NuGet пропустит восстановление пакета. Чтобы предотвратить развертывание блокирующего файла, добавьте `project.lock.json` в файл `.gitignore`.

Чтобы использовать настраиваемые веб-каналы NuGet, укажите веб-канал в файле *Nuget.Config* в корне приложения-функции. Дополнительные сведения см. в статье [Configuring NuGet behavior](/nuget/consume-packages/configuring-nuget-behavior) (Настройка поведения NuGet).

### <a name="using-a-projectjson-file"></a>Использование файла project.json

1. Откройте функцию на портале Azure. На вкладке "Журналы" отображаются выходные данные установки пакета.
2. Чтобы отправить файл project.json, используйте один из методов, описанных в разделе [Как обновить файлы приложения-функции](functions-reference.md#fileupdate) статьи "Руководство для разработчиков по Функциям Azure".
3. После отправки файла *project.json* в потоковом журнале функции отобразятся выходные данные, как в следующем примере.

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189
2016-04-04T19:02:57.189
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>Переменные среды

Чтобы получить значение переменной среды или значение параметра приложения, используйте `System.Environment.GetEnvironmentVariable`, как показано в следующем примере кода.

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
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
```

<a name="imperative-bindings"></a> 

## <a name="binding-at-runtime"></a>Привязка во время выполнения

Для C# и других языков .NET можно использовать шаблон [императивной](https://en.wikipedia.org/wiki/Imperative_programming) привязки, которая отличается от [*декларативной*](https://en.wikipedia.org/wiki/Declarative_programming) привязки в файле *function.json*. Императивную привязку удобно использовать, когда параметры привязки должны вычисляться не при проектировании, а во время выполнения. С использованием такого шаблона можно моментально выполнить привязку к поддерживаемым входным и выходным привязкам в коде функции.

Определите принудительную привязку следующим образом.

- **Не** добавляйте запись для нужных императивных привязок в файл *function.json*.
- Передайте входной параметр [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) или [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- Используйте следующий шаблон C# для привязки данных,

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

где `BindingTypeAttribute` — атрибут .NET, определяющий пользовательскую привязку, а `T` — входной или выходной тип, поддерживаемый этим типом привязки. `T` не может быть параметром типа `out` (например, `out JObject`). Например, выходная привязка таблицы мобильных приложений поддерживает [шесть выходных типов](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), но для `T` можно использовать только [ICollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) или [IAsyncCollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs).

### <a name="single-attribute-example"></a>Пример с одним атрибутом

В следующем примере кода создается [выходная привязка большого двоичного объекта службы хранилища](functions-bindings-storage-blob.md#output) с путем к большому двоичному объекту, определенному во время выполнения, а затем записывается строка в большой двоичный объект.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    using (var writer = await binder.BindAsync<TextWriter>(new BlobAttribute("samples-output/path")))
    {
        writer.Write("Hello World!!");
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) определяет входную или выходную привязку [большого двоичного объекта службы хранилища](functions-bindings-storage-blob.md), а [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) представляет собой поддерживаемый тип выходной привязки.

### <a name="multiple-attribute-example"></a>Пример с несколькими атрибутами

В предыдущем примере код получает параметр приложения для строки подключения основной учетной записи хранения приложения-функции (т. е. `AzureWebJobsStorage`). Вы можете указать пользовательский параметр приложения, который следует использовать для учетной записи хранения. Для этого добавьте [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) и передайте массив атрибутов в `BindAsync<T>()`. Используйте параметр `Binder`, а не `IBinder`.  Например: 

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    var attributes = new Attribute[]
    {    
        new BlobAttribute("samples-output/path"),
        new StorageAccountAttribute("MyStorageAccount")
    };

    using (var writer = await binder.BindAsync<TextWriter>(attributes))
    {
        writer.Write("Hello World!");
    }
}
```

В следующей таблице перечислены атрибуты .NET для каждого типа привязки и пакеты, в которых они определены.

> [!div class="mx-codeBreakAll"]
| Привязка | Атрибут | Ссылка, которую нужно добавить |
|------|------|------|
| База данных Cosmos | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.CosmosDB"` |
| Концентраторы событий | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
| Мобильные приложения | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
| Центры уведомлений | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
| Служебная шина Azure | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
| Очередь службы хранилища | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Большой двоичный объект службы хранилища | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Таблица службы хранилища | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Дополнительные сведения о триггерах и привязках](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Дополнительные сведения о рекомендациях по решению "Функции Azure"](functions-best-practices.md)
