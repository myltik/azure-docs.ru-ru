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
	ms.date="04/14/2016"
	ms.author="chrande"/>

# Справочник разработчика C# по функциям Azure

Взаимодействие с C# для Функций Azure основано на пакете SDK с веб-заданиями Azure. Данные поступают в функцию C# через аргументы метода. Имена аргументов указываются в `function.json`, и существуют предварительно определенные имена для доступа к таким объектам, как средство ведения журнала функций и токены отмены.

В этой статье предполагается, что вы уже прочли [справочник разработчика по функциям Azure](functions-reference.md).

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
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
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

В некоторых случаях используются операции, чувствительные к завершению работы. Всегда лучше написать код, который может обрабатывать сбои, однако в случаях, когда нужно обрабатывать запросы на нормальное завершение работы, можно определить аргумент с типом [`CancellationToken`](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx). В случае завершения работы узла будет предоставлен токен `CancellationToken`.

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

Если требуется импортировать пространства имен, это можно сделать как обычно — с помощью предложения `using`.

```csharp
using System.Net;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

Следующие пространства имен импортируются автоматически и поэтому являются необязательными:

* `System`
* `System.Collections.Generic`
* `System.Linq`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## Ссылки на внешние сборки

Для сборок платформы следует добавить ссылки с помощью директивы `#r "AssemblyName"`.

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

Кроме того, следующие сборки представляют собой частные случаи, и к ним можно обращаться по простому имени (например, `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Если необходимо указать ссылку на закрытую сборку, можно отправить файл сборки в папку `bin`, связанную с вашей функцией, и указать его по имени файла (например, `#r "MyAssembly.dll"`).

## Управление пакетами

Чтобы использовать пакеты NuGet в функции C#, отправьте файл *project.json* в папку соответствующей функции в файловой системе приложения функции. Ниже приведен пример файла *project.json*, который добавляет ссылку на Microsoft.ProjectOxford.Face версии 1.1.0:

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

При отправке файла *project.json* среда выполнения получает пакеты и автоматически добавляет ссылки на сборки пакетов. Добавлять директивы `#r "AssemblyName"` не нужно. Просто добавьте необходимые инструкции `using` в файл *run.csx* для использования типов, определенных в пакетах NuGet.

### Отправка файла project.json

Сначала убедитесь, что приложение функции запущено, для чего можно открыть эту функцию на портале Azure. Это также обеспечивает доступ к потоковым журналам, где будут отображаться выходные данные установки пакета.

Приложения функций строятся на основе службы приложений, поэтому для этих приложений доступны все [параметры развертывания, доступные для стандартных веб-приложений](../app-service-web/web-sites-deploy.md). Вот некоторые методы, которые можно использовать.

#### Отправка файла project.json с помощью Visual Studio Online (Monaco)

1. На портале Функций Azure щелкните **Параметры приложения функций**.

2. В разделе **Дополнительные параметры** щелкните **Перейти к параметрам службы приложений**.

3. Щелкните **Средства**.

4. В разделе **Разработка** щелкните **Visual Studio Online**.

5. Выберите **Вкл.**, если этот параметр еще не включен, а затем щелкните **Перейти**.

6. После загрузки Visual Studio Online перетащите файл *project.json* вашего проекта в папку функции (папку, имя которой соответствует имени требуемой функции).

#### Отправка файла project.json с использованием конечной точки SCM (Kudu) приложения функции

1. Перейдите на страницу `https://<function_app_name>.scm.azurewebsites.net`.

2. Щелкните **Консоль отладки > CMD**.

3. Перейдите в папку *D:\\home\\site\\wwwroot<имя\_функции>*.

4. Перетащите файл *project.json* в эту папку (на файловую сетку).

#### Отправка файла project.json с помощью FTP

1. Для настройки FTP следуйте инструкциям в [этом разделе](../app-service-web/web-sites-deploy.md#ftp).

2. Подключившись к сайту приложения функции, скопируйте файл *project.json* в расположение */site/wwwroot/<function_name>*.

#### Журнал установки пакета 

После отправки файла *project.json* в потоковом журнале функции отобразятся выходные данные, подобные следующему примеру:

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

## Повторное использование кода CSX

В файле *run.csx* можно использовать классы и методы, определенные в других файлах *CSX*. Для этого используйте директивы `#load` в файле *run.csx*, как показано в примере ниже.

Пример файла *run.csx*:

```csharp
#load "mylogger.csx"

public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Verbose($"Log by run.csx: {DateTime.Now}"); 
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

Пример файла *mylogger.csx*:

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
 
Директива `#load` работает только с файлами формата *CSX* (сценарии C#), но не с файлами формата *CS*.

## Дальнейшие действия

Для получения дополнительных сведений см. следующие ресурсы:

* [Справочник разработчика по функциям Azure](functions-reference.md)
* [Справочник разработчика NodeJS по функциям Azure](functions-reference-node.md)
* [Azure Functions triggers and bindings (Триггеры и привязки в Функциях Azure)](functions-triggers-bindings.md)

<!---HONumber=AcomDC_0427_2016-->