---
title: "Справочник разработчика по Функциям Azure | Документация Майкрософт"
description: "Узнайте, как разрабатывать Функции Azure с помощью C#."
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "функции azure, функции, обработка событий, веб-перехватчики, динамические вычисления, независимая архитектура"
ms.assetid: f28cda01-15f3-4047-83f3-e89d5728301c
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/13/2016
ms.author: chrande
translationtype: Human Translation
ms.sourcegitcommit: da9da90e7ccd5d324b8f87a3585555ea5d5ed475
ms.openlocfilehash: d587ff744fea5393a34d5a576e6af32cac5d2b44


---
# <a name="azure-functions-c-developer-reference"></a>Справочник разработчика C# по функциям Azure
> [!div class="op_single_selector"]
> * [Сценарий C#](functions-reference-csharp.md)
> * [Скрипт F#](functions-reference-fsharp.md)
> * [Node.js](functions-reference-node.md)
> 
> 

Взаимодействие с C# для Функций Azure основано на пакете SDK с веб-заданиями Azure. Данные поступают в функцию C# через аргументы метода. Имена аргументов указываются в `function.json`, и есть предварительно определенные имена для доступа к таким объектам, как средство ведения журнала функций и маркеры отмены.

В этой статье предполагается, что вы уже прочли [справочник разработчика по Функциям Azure](functions-reference.md).

## <a name="how-csx-works"></a>Как работает формат CSX
Формат `.csx` позволяет писать меньше стандартного кода и сосредоточиться на написании только функции C#. Для Функций Azure следует просто указать ссылки на необходимые сборки и пространства имен, как обычно, а затем вместо помещения всего кода в пространство имен и класс можно просто определить метод `Run` . Если необходимо включить какие-либо классы, например для определения объектов POCO, можно включить класс в тот же файл.   

## <a name="binding-to-arguments"></a>Привязка к аргументам
Различные привязки связываются с функцией C# через свойство `name` в файле конфигурации *function.json*. У каждой привязки есть собственные поддерживаемые типы, задокументированные для каждой привязки. Например, триггер BLOB-объектов может поддерживать строку, POCO и несколько других типов. Можно использовать тот тип, который лучше всего соответствует вашим потребностям. Для каждого свойства объекта POCO нужно определить метод задания и считывания. 

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

## <a name="logging"></a>Ведение журналов
Для записи выходных данных в потоковые журналы в C# можно включить аргумент с типом `TraceWriter` . Рекомендуем присвоить ему имя `log`. В Функциях Azure рекомендуется избегать использования метода `Console.Write` .

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

## <a name="async"></a>Асинхронный режим
Чтобы сделать функцию асинхронной, используйте ключевое слово `async` и верните объект `Task`.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName, 
        Stream blobInput,
        Stream blobOutput)
    {
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
```

## <a name="cancellation-token"></a>Токен отмены
В некоторых случаях используются операции, чувствительные к завершению работы. Всегда лучше написать код, который может обрабатывать сбои, однако в случаях, когда нужно обрабатывать запросы на нормальное завершение работы, можно определить аргумент с типом [`CancellationToken`](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx).  В случае завершения работы узла будет предоставлен маркер `CancellationToken` . 

```csharp
public async static Task ProcessQueueMessageAsyncCancellationToken(
        string blobName, 
        Stream blobInput,
        Stream blobOutput,
        CancellationToken token)
    {
        await blobInput.CopyToAsync(blobOutput, 4096, token);
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
* `Microsoft.Azure.WebJobs.Host`.

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

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`.

Кроме того, следующие сборки представляют собой частные случаи, и к ним можно обращаться по простому имени (например, `#r "AssemblyName"`).

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNet.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

Если необходимо указать ссылку на закрытую сборку, можно отправить файл сборки в папку `bin`, связанную с функцией, и указать его по имени файла (например, `#r "MyAssembly.dll"`). Дополнительные сведения о передаче файлов в папку функции см. в следующем разделе.

## <a name="package-management"></a>Управление пакетами
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

Поддерживается только версия платформы .NET Framework 4.6, поэтому убедитесь, что ваш файл *project.json* определяет `net46`, как показано ниже.

При отправке файла *project.json* среда выполнения получает пакеты и автоматически добавляет ссылки на сборки пакетов. Добавлять директивы `#r "AssemblyName"` не нужно. Просто добавьте необходимые инструкции `using` в файл *run.csx* для использования типов, определенных в пакетах NuGet.

### <a name="how-to-upload-a-projectjson-file"></a>Отправка файла project.json
1. Сначала убедитесь, что приложение функции запущено, для чего можно открыть эту функцию на портале Azure. 
   
    Это также обеспечивает доступ к потоковым журналам, где будут отображаться выходные данные установки пакета. 
2. Чтобы отправить файл project.json, используйте один из методов, описанных в разделе **Как обновить файлы приложения-функции** статьи [Справочник разработчика по функциям Azure](functions-reference.md#fileupdate). 
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

Обычно для задания строго типизированных аргументов в функциях с использованием объекта POCO применяется общий файл с расширением *CSX*. В следующем упрощенном примере триггер HTTP и очереди совместно используют объект POCO с именем `Order` для задания строго типизированных данных о заказах:

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

Директива `#load` работает только с *CSX*-файлами (сценариями C#), но не с *CS*-файлами. 

## <a name="next-steps"></a>Дальнейшие действия
Для получения дополнительных сведений см. следующие ресурсы:

* [Best Practices for Azure Functions](functions-best-practices.md) (Рекомендации по Функциям Azure)
* [Справочник разработчика по функциям Azure](functions-reference.md)
* [Справочник разработчика F# по функциям Azure](functions-reference-fsharp.md)
* [Справочник разработчика NodeJS по функциям Azure](functions-reference-node.md)
* [Azure Functions triggers and bindings (Триггеры и привязки в Функциях Azure)](functions-triggers-bindings.md)




<!--HONumber=Nov16_HO3-->


