<properties
	pageTitle="Справочник разработчика по функциям Azure | Microsoft Azure"
	description="Узнайте, как разрабатывать Функции Azure с помощью C#."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="функции azure, функции, обработка событий, веб-перехватчики, динамические вычисления, независимая архитектура"/>

<tags
	ms.service="functions"
	ms.devlang="dotnet"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="05/13/2016"
	ms.author="chrande"/>

# Справочник разработчика C# по функциям Azure

> [AZURE.SELECTOR]
- [Сценарий C#](../articles/azure-functions/functions-reference-csharp.md)
- [Скрипт F#](../articles/azure-functions/functions-reference-fsharp.md)
- [Node.js](../articles/azure-functions/functions-reference-node.md)
 
Взаимодействие с C# для Функций Azure основано на пакете SDK с веб-заданиями Azure. Данные поступают в функцию C# через аргументы метода. Имена аргументов указываются в `function.json`, и есть предварительно определенные имена для доступа к таким объектам, как средство ведения журнала функций и маркеры отмены.

В этой статье предполагается, что вы уже прочли [справочник разработчика по Функциям Azure](functions-reference.md).

## Как работает формат CSX

Формат `.csx` позволяет писать меньше стандартного кода и сосредоточиться на написании только функции C#. Для Функций Azure следует просто указать ссылки на необходимые сборки и пространства имен, как обычно, а затем вместо помещения всего кода в пространство имен и класс можно просто определить метод `Run`. Если необходимо включить какие-либо классы, например для определения объектов POCO, можно включить класс в тот же файл.

## Привязка к аргументам

Различные привязки связываются с функцией C# через свойство `name` в конфигурации файла *function.json*. У каждой привязки есть собственные поддерживаемые типы, задокументированные для каждой привязки. Например, триггер BLOB-объектов может поддерживать строку, POCO и несколько других типов. Можно использовать тот тип, который лучше всего соответствует вашим потребностям.

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

## Ведение журналов

Для записи выходных данных в потоковые журналы в C# можно включить аргумент с типом `TraceWriter`. Рекомендуем присвоить ему имя `log`. В Функциях Azure рекомендуется избегать использования метода `Console.Write`.

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

## Асинхронный режим

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

## Токен отмены

В некоторых случаях используются операции, чувствительные к завершению работы. Всегда лучше написать код, который может обрабатывать сбои, однако в случаях, когда нужно обрабатывать запросы на нормальное завершение работы, можно определить аргумент с типом [`CancellationToken`](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx). В случае завершения работы узла будет предоставлен маркер `CancellationToken`.

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

## Импорт пространств имен

Если требуется импортировать пространства имен, это можно сделать как обычно — с помощью предложения `using`.

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

## Ссылки на внешние сборки

Для сборок платформы добавьте ссылки с помощью директивы `#r "AssemblyName"`.

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

Следующие сборки автоматически добавляются средой внешнего размещения Функций Azure:

* `mscorlib`;
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
* `Microsoft.AspNEt.WebHooks.Common`.

Если необходимо указать ссылку на закрытую сборку, можно отправить файл сборки в папку `bin`, связанную с функцией, и указать его по имени файла (например, `#r "MyAssembly.dll"`). Дополнительные сведения о передаче файлов в папку функции см. в следующем разделе.

## Управление пакетами

Чтобы использовать пакеты NuGet в функции C#, отправьте файл *project.json* в папку соответствующей функции в файловой системе приложения-функции. Ниже приведен пример файла *project.json*, который добавляет ссылку на Microsoft.ProjectOxford.Face версии 1.1.0.

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


### Отправка файла project.json

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

## Переменные среды

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

## Повторное использование кода CSX

В файле *run.csx* можно использовать классы и методы, определенные в других *CSX*-файлах. Для этого используйте директивы `#load` в файле *run.csx*, как показано в примере ниже.

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

В директиве `#load` можно использовать относительный путь:

* `#load "mylogger.csx"` загружает файл, расположенный в папке функции.

* `#load "loadedfiles\mylogger.csx"` загружает файл, расположенный в папке, которая содержится в папке функции.

* `#load "..\shared\mylogger.csx"` загружает файл, расположенный в папке на том же уровне, что и папка функции, то есть непосредственно в разделе *wwwroot*.
 
Директива `#load` работает только с файлами формата *CSX* (скрипт C#), но не с файлами формата *CS*.

## Дальнейшие действия

Для получения дополнительных сведений см. следующие ресурсы:

* [Справочник разработчика по функциям Azure](functions-reference.md)
* [Справочник разработчика NodeJS по функциям Azure](functions-reference-fsharp.md)
* [Справочник разработчика NodeJS по функциям Azure](functions-reference-node.md)
* [Azure Functions triggers and bindings (Триггеры и привязки в Функциях Azure)](functions-triggers-bindings.md)

<!---HONumber=AcomDC_0921_2016-->