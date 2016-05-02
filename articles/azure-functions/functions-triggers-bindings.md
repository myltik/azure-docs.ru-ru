<properties
	pageTitle="Триггеры и привязки в Функциях Azure | Microsoft Azure"
	description="Узнайте, как использовать триггеры и привязки в Функциях Azure."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="функции azure, функции, обработка событий, веб-перехватчики, динамические вычисления, независимая архитектура"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="04/07/2016"
	ms.author="chrande"/>

# Справочник разработчика по триггерам и привязкам в Функциях Azure

В этой статье описывается настройка триггеров и привязок в Функциях Azure и их программирование. Большинством из этих привязок можно легко управлять с помощью пользовательского интерфейса **интеграции** на портале Azure. Однако на портале не объясняются все функциональные возможности и параметры каждой привязки.

В этой статье предполагается, что вы уже прочли [cправочник разработчика по функциям Azure](functions-reference.md) и справочные статьи разработчика по [C#](functions-reference-csharp.md) или [Node](functions-reference-node.md).

## Триггеры и привязки HTTP и веб-перехватчика

Триггер HTTP или веб-перехватчика можно использовать для вызова функции в ответ на HTTP-запрос. Запрос должен содержать ключ API, который в данный момент доступен только в пользовательском интерфейсе на портале Azure.

URL-адрес функции состоит из URL-адреса приложения функции и имени функции:

```
 https://{function app name}.azurewebsites.net/api/{function name} 
```

В файле *function.json* содержатся свойства, которые относятся к HTTP-запросу и HTTP-ответу.

Свойства HTTP-запроса:

- `name` — имя переменной, используемой в коде функции для объекта запроса (или для текста запроса, если используются функции Node.js).
- `type` — для этого свойства нужно задать значение *httpTrigger*.
- `direction` — для этого свойства нужно задать значение *in*. 
- `webHookType` — для триггеров веб-перехватчика допустимыми значениями являются *github*, *slack* и *genericJson*. Для триггера HTTP, отличного от триггера веб-перехватчика, в качестве значения для этого свойства задается пустая строка. Дополнительные сведения о веб-перехватчиках см. в приведенном ниже разделе [Триггеры веб-перехватчиков](#webhook-triggers).
- `authLevel` — это свойство неприменимо к триггерам веб-перехватчиков. Задайте значение function, чтобы появлялся запрос на ключ API, anonymous, чтобы требование ключа API игнорировалось, или admin, чтобы появлялся запрос на главный ключ API. Дополнительные сведения см. в приведенном ниже разделе [Ключи API](#apikeys).

Свойства HTTP-ответа:

- `name` — имя переменной, используемой в коде функции для объекта ответа.
- `type` — для этого свойства нужно задать значение *http*.
- `direction` — для этого свойства нужно задать значение *out*. 
 
Пример файла *function.json*:

```json
{
  "bindings": [
    {
      "webHookType": "",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
      "authLevel": "function"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

### Триггеры веб-перехватчика

Триггер веб-перехватчика — это триггер HTTP, обладающий следующими возможностями, предназначенными для веб-перехватчиков:

* Для отдельных поставщиков веб-перехватчиков (сейчас поддерживаются GitHub и Slack) среда выполнения Функций проверяет подпись поставщика.
* Для функций Node.js среда выполнения Функций предоставляет текст запроса, а не объект запроса. Функции C# не обрабатываются особым образом, так как вы управляете предоставляемыми объектами, указывая тип параметра. Указывая `HttpRequestMessage`, вы получаете объект запроса. При указании типа POCO среда выполнения Функций пытается проанализировать объект JSON в тексте запроса, чтобы указать свойства объекта.
* Для активации функции веб-перехватчика HTTP-запрос должен содержать ключ API. Для триггеров HTTP, отличных от веб-перехватчиков, это требование необязательно.

Сведения о настройке веб-перехватчика GitHub см. в статье [Creating WebHooks](http://go.microsoft.com/fwlink/?LinkID=761099&clcid=0x409) (Создание веб-перехватчиков) на сайте GitHub Developer.

### Ключи API

По умолчанию API-ключ следует добавить в HTTP-запрос, чтобы активировать функцию HTTP или веб-перехватчика. Ключ можно добавить в переменную строки запроса с именем `code` или в заголовок HTTP `x-functions-key`. Для функций, отличных от функций веб-перехватчика, можно указать, что ключ API не требуется, задав для свойства `authLevel` значение anonymous в файле *function.json*.

Значения ключа API можно найти в папке с путем *D:\\home\\data\\Functions\\secrets* в файловой системе приложения функций. Главный ключ и ключ функции устанавливаются в файле *host.json*, как показано в следующем примере.

```json
{
  "masterKey": "K6P2VxK6P2VxK6P2VxmuefWzd4ljqeOOZWpgDdHW269P2hb7OSJbDg==",
  "functionKey": "OBmXvc2K6P2VxK6P2VxK6P2VxVvCdB89gChyHbzwTS/YYGWWndAbmA=="
}
```

Ключ функции из файла *host.json* можно использовать для активации любой функции. Однако с его помощью нельзя активировать отключенную функцию. С помощью главного ключа можно активировать любую функцию, даже если она отключена. Функцию можно настроить таким образом, чтобы для ее активации требовался главный ключ. Для этого нужно задать для свойства `authLevel` значение admin.

Если папка *secrets* содержит JSON-файл с тем же именем, что и у функции, с помощью свойства `key` в этом файле можно также активировать эту функцию, а этот ключ можно будет использовать только с функцией, на которую он ссылается. Например, ключ API для функции с именем `HttpTrigger` указывается в файле *HttpTrigger.json* в папке *secrets*. Пример:

```json
{
  "key":"0t04nmo37hmoir2rwk16skyb9xsug32pdo75oce9r4kg9zfrn93wn4cx0sxo4af0kdcz69a4i"
}
```

> [AZURE.NOTE] При настройке триггера веб-перехватчика не предоставляйте поставщику веб-перехватчика общий доступ к главному ключу. Воспользуйтесь ключом, который можно использовать только с функцией, обрабатывающей веб-перехватчик. С помощью главного ключа можно активировать любую функцию, даже отключенную.

### Пример кода C# для функции триггера HTTP 

В примере кода выполняется поиск параметра `name` в строке запроса или в тексте HTTP-запроса.

```csharp
using System.Net;
using System.Threading.Tasks;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Verbose($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

### Пример кода Node.js для функции триггера HTTP 

В этом примере кода выполняется поиск параметра `name` в строке запроса или в тексте HTTP-запроса.

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

### Пример кода C# для функции веб-перехватчика GitHub 

В этом примере кода регистрируются комментарии о выдаче GitHub.

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using System.Threading.Tasks;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    string jsonContent = await req.Content.ReadAsStringAsync();
    dynamic data = JsonConvert.DeserializeObject(jsonContent);

    log.Verbose($"WebHook was triggered! Comment: {data.comment.body}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        body = $"New GitHub comment: {data.comment.body}"
    });
}
```

### Пример кода Node.js для функции веб-перехватчика GitHub 

В этом примере кода регистрируются комментарии о выдаче GitHub.

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## Триггер таймера

Файл *function.json* содержит выражение schedule и выключатель, который указывает, следует ли активировать функцию немедленно.

```json
{
  "bindings": [
    {
      "schedule": "0 * * * * *",
      "runOnStartup": true,
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Триггер таймера автоматически масштабирует несколько экземпляров. Во всех экземплярах будет выполняться только один экземпляр определенной функции таймера.

### Формат выражения schedule

Выражение schedule может быть [выражением CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression), содержащим 6 полей: {second}, {minute}, {hour}, {day}, {month} и {day of the week}. Во многих документах по выражениям CRON в сети отсутствует поле {second}. Поэтому если скопировать текст выражения в одном из них, понадобится добавить дополнительное поле.

Выражение schedule также может быть в формате *чч:мм:сс*. Таким образом можно указать задержку между активациями функции.

Ниже приведены примеры выражений schedule.

Активация через каждые 5 минут:

```json
"schedule": "0 */5 * * * *",
"runOnStartup": false,
```

Немедленная активация, а затем через каждые два часа соответственно:

```json
"schedule": "0 0 */2 * * *",
"runOnStartup": true,
```

Активация через каждые 15 секунд:

```json
"schedule": "00:00:15",
"runOnStartup": false,
```

### Пример кода C# для триггера таймера

В этом примере кода C# при каждой активации функции записывается один журнал.

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Verbose($"C# Timer trigger function executed at: {DateTime.Now}");    
}
```

## Триггеры и привязки службы хранилища Azure (для очередей, больших двоичных объектов, таблиц)

Этот раздел содержит следующие подразделы:

* [Свойство connection службы хранилища Azure в файле function.json](#storageconnection)
* [Триггер очереди службы хранилища Azure](#storagequeuetrigger)
* [Выходная привязка очереди службы хранилища Azure](#storagequeueoutput)
* [Триггер большого двоичного объекта службы хранилища Azure](#storageblobtrigger)
* [Входные и выходные привязки для больших двоичных объектов службы хранилища Azure](#storageblobbindings)
* [Входные и выходные привязки для таблиц службы хранилища Azure](#storagetablesbindings)

### <a id="storageconnection"></a> Свойство connection службы хранилища Azure в файле function.json

В файле *function.json* содержится свойство `connection` для всех триггеров и привязок службы хранилища Azure. Например:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "queueTrigger",
            "direction": "in",
            "queueName": "myqueue-items",
            "connection":""
        }
    ]
}
```

Если не задавать значение для свойства `connection`, триггер или привязка будет использоваться с учетной записью хранения по умолчанию для приложения функций. Если требуется, чтобы триггер или привязка использовались с другой учетной записью хранения, создайте в приложении функций параметр приложения, который указывает на требуемую учетную запись хранения, и установите имя параметра приложения в качестве значения для свойства `connection`. Чтобы добавить параметр приложения, сделайте следующее:

1. В колонке **Приложения функций** на портале Azure щелкните **Параметры приложения функций > Перейти к параметрам службы приложений**.

2. В колонке **Параметры** щелкните раздел **Параметры приложения**.

3. Прокрутите вниз до раздела **Параметры приложения** и добавьте запись с **ключом** (*{некоторое уникальное значение по усмотрению}*) и **значением** (строка подключения для учетной записи хранения).

### <a id="storagequeuetrigger"></a> Триггер очереди службы хранилища Azure

Файл *function.json* содержит имя очереди для опроса и имя переменной для сообщения очереди. Например:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "queueTrigger",
            "direction": "in",
            "queueName": "myqueue-items",
            "connection":""
        }
    ]
}
```

#### Поддерживаемые типы триггеров очереди

Сообщение очереди можно десериализировать в один из следующих типов:

* `string`
* `byte[]`
* объект JSON;   
* `CloudQueueMessage`

#### Метаданные триггера очереди

Метаданные очереди можно добавить в функцию, используя следующие имена переменных:

* expirationTime;
* insertionTime;
* nextVisibleTime;
* id
* popReceipt;
* dequeueCount.
* queueTrigger (другой способ получения текста сообщения очереди в виде строки).

В этом примере кода C# получаются и регистрируются метаданные очереди:

```csharp
public static void Run(string myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Verbose($"C# Queue trigger function processed: {myQueueItem}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

#### Обработка подозрительных сообщений очереди

Сообщения, содержимое которых вызывает сбой функции, называются *подозрительными сообщениями*. При сбое функции сообщение очереди не удаляется и в конечном итоге забирается снова, вызывая повтор цикла. Пакет SDK может автоматически прервать цикл после ограниченного числа итераций или это можно сделать вручную.

Пакет SDK вызывает функцию обработки сообщения очереди до 5 раз. В случае сбоя во время пятой попытки сообщение перемещается в очередь подозрительных сообщений.

Очередь подозрительных сообщений называется *{originalqueuename}*-poison. Можно написать функции для обработки сообщений из очереди подозрительных сообщений путем внесения их в журнал или отправки уведомления о необходимости ручного вмешательства.

Если подозрительные сообщения очереди нужно обработать вручную, можно получить число попыток обработки сообщения, проверив значение свойства `dequeueCount`.

### <a id="storagequeueoutput"></a> Выходная привязка очереди службы хранилища Azure

Файл *function.json* содержит имя выходной очереди и имя переменной для содержимого сообщения. В этом примере используется триггер очереди и записывается сообщение очереди.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myQueue",
      "type": "queue",
      "queueName": "samples-workitems-out",
      "connection": "",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### Поддерживаемые типы выходных привязок очереди

Привязка `queue` может сериализировать следующие типы в сообщения очереди:

* `string` (создает сообщение очереди, если по завершении вызова функции значение параметра не равно null);
* `byte[]` (действует как тип string); 
* `CloudQueueMessage` (действует как тип string); 
* объект JSON (создает сообщение с пустым объектом, если по завершении функции значение параметра — null).

#### Пример кода выходной привязки очереди

В этом примере кода C# записывается по одному сообщению выходной очереди для каждого сообщения входной очереди.

```csharp
public static void Run(string myQueueItem, out string myOutputQueueItem, TraceWriter log)
{
    myOutputQueueItem = myQueueItem + "(next step)";
}
```

В этом примере кода C# записывается несколько сообщений с использованием параметра `ICollector<T>` (используйте `IAsyncCollector<T>` в асинхронной функции):

```csharp
public static void Run(string myQueueItem, ICollector<string> myQueue, TraceWriter log)
{
    myQueue.Add(myQueueItem + "(step 1)");
    myQueue.Add(myQueueItem + "(step 2)");
}
```

### <a id="storageblobtrigger"></a> Триггер большого двоичного объекта службы хранилища Azure

Файл *function.json* содержит путь, по которому расположен отслеживаемый контейнер, и при необходимости шаблон имени большого двоичного объекта. Этот пример активирует все большие двоичные объекты, которые добавлены в контейнер samples-workitems.

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":""
        }
    ]
}
```

> [AZURE.NOTE] Если контейнер больших двоичных объектов, который отслеживает триггер, содержит более 10 000 больших двоичных объектов, среда выполнения Функций проверяет файлы журналов, чтобы найти новые или измененные большие двоичные объекты. Это не происходит в режиме реального времени. После создания большого двоичного объекта функция может не вызываться несколько минут или даже дольше. Кроме того, [журналы хранилища создаются на условиях максимально возможного исполнения](https://msdn.microsoft.com/library/azure/hh343262.aspx): регистрация всех событий не гарантируется. В некоторых случаях журналы могут пропускаться. Если ограниченная скорость и надежность триггеров больших двоичных объектов для больших контейнеров недопустимы для вашего приложения, для обработки большого двоичного объекта во время его создания рекомендуется создать сообщение очереди и использовать триггер очереди вместо триггера большого двоичного объекта.

#### Поддерживаемые типы триггеров больших двоичных объектов

Большие двоичные объекты можно десериализировать в такие типы:

* строка
* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `IEnumerable<CloudBlockBlob>`
* `IEnumerable<CloudPageBlob>`
* другие типы, десериализованные с помощью [ICloudBlobStreamBinder](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md#icbsb). 

#### Пример кода C# триггера большого двоичного объекта

В этом примере кода C# регистрируется содержимое каждого большого двоичного объекта, который добавляется в контейнер.

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
}
```

#### Шаблоны имен триггеров больших двоичных объектов

Шаблон имени большого двоичного объекта можно указать в свойстве `path`. Например:

```json
"path": "input/original-{name}",
```

По этому пути можно найти большой двоичный объект с именем *original-Blob1.txt* в контейнере *input*. В качестве значения переменной `name` в коде функции будет указано `Blob1`.

Другой пример:

```json
"path": "input/{blobname}.{blobextension}",
```

По этому пути можно также найти большой двоичный объект с именем *original-Blob1.txt*. В качестве значений переменных `blobname` и `blobextension` в коде функции будет указано *original-Blob1* и *txt*.

Типы больших двоичных объектов, которые активируют функции, можно ограничить, указав шаблон с фиксированным значением расширения файла. Если задать для свойства `path` значение *samples/{имя}.png*, функцию будут активировать только большие двоичные объекты с расширением *PNG* в контейнере *samples*.

Если необходимо указать шаблон для имен больших двоичных объектов с фигурными скобками, используйте две фигурные скобки. Например, если нужно найти большие двоичные объекты в контейнере *images* с такими именами:

		{20140101}-soundfile.mp3

Используйте для свойства `path` следующее значение:

		images/{{20140101}}-{name}

В этом примере у переменной `name` будет значение *soundfile.mp3*.

#### Уведомления о получении большого двоичного объекта

Среда выполнения Функций Azure гарантирует, что для одного и того же нового или обновленного большого двоичного объекта функция активации большого двоичного объекта будет вызываться только один раз. Это достигается за счет сохранения *уведомлений о получении большого двоичного объекта*, которые позволяют определить, обработана ли версия этого большого двоичного объекта.

Уведомления о получении большого двоичного объекта хранятся в контейнере с именем *azure-webjobs-hosts* в учетной записи хранения Azure, указанной в строке подключения AzureWebJobsStorage. Уведомление о получении большого двоичного объекта содержит следующую информацию:

* функция, вызванная для большого двоичного объекта ("*{имя\_приложения\_функции}*.Functions.*{имя\_функции}*", например functionsf74b96f7.Functions.CopyBlob);
* имя контейнера;
* тип большого двоичного объекта (BlockBlob или PageBlob);
* имя большого двоичного объекта;
* ETag (идентификатор версии больших двоичных объектов, например 0x8D1DC6E70A277EF).

Если вам необходимо выполнить принудительную повторную обработку большого двоичного объекта, уведомление о получении этого большого двоичного объекта можно удалить из контейнера *azure-webjobs-hosts* вручную.

#### Обработка подозрительных больших двоичных объектов

При сбое функции активации большого двоичного объекта пакет SDK вызывает ее снова, если причиной сбоя была временная ошибка. Если сбой вызван содержимым большого двоичного объекта, каждый раз при попытке обработать большой двоичный объект будет происходить сбой функции. По умолчанию пакет SDK вызывает функцию до 5 раз для заданного большого двоичного объекта. Если при пятой попытке происходит сбой, пакет SDK добавляет сообщение в очередь с именем *webjobs-blobtrigger-poison*.

Сообщением очереди для подозрительных больших двоичных объектов является объект JSON, содержащий следующие свойства:

* FunctionId (в формате *{имя\_функции\_приложения}*.Functions.*{имя\_функции}*);
* BlobType (BlockBlob или PageBlob);
* ContainerName;
* BlobName
* ETag (идентификатор версии BLOB-объектов, например 0x8D1DC6E70A277EF)

### <a id="storageblobbindings"></a> Входные и выходные привязки для больших двоичных объектов службы хранилища Azure

Файл *function.json* содержит имя контейнера и имена переменных для имени и содержимого большого двоичного объекта. В этом примере триггер очереди используется для копирования большого двоичного объекта:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### Поддерживаемые типы входных и выходных привязок больших двоичных объектов

Привязка `blob` может сериализировать или десериализировать следующие типы:

* `Stream`
* `TextReader`
* `TextWriter`
* `string` (для выходного большого двоичного объекта: создает большой двоичный объект, только если для параметра строки не задано значение null при возврате функции);
* объект JSON (для выходного большого двоичного объекта: создает большой двоичный объект в качестве объекта с значением null, если значение параметра равно null при завершении функции);
* `CloudBlobStream` (только для выходных больших двоичных объектов);
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

#### Пример кода C# с выходными данными большого двоичного объекта

В этом примере кода C# копируется большой двоичный объект, имя которого поступает в сообщение очереди.

```CSHARP
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Verbose($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a id="storagetablesbindings"></a> Входные и выходные привязки для таблиц службы хранилища Azure

Файл *function.json* для таблиц хранилища содержит следующие свойства:

* `name` — имя переменной, которая используется в коде для привязки таблицы;
* `tableName`
* `partitionKey` и `rowKey` используются вместе для чтения одной сущности в функции C# или Node или записи одной сущности в функцию Node;
* `take` — максимальное число строк, которые нужно считать, для входных данных таблицы в функции Node.
* `filter` — выражение фильтра OData для входных данных таблицы в функции Node.

Эти свойства поддерживают следующие сценарии:

* Чтение одной строкой в функции C# или Node.

	Задайте значения `partitionKey` и `rowKey`. Свойства `filter` и `take` не используются в этом сценарии.

* Чтение нескольких строк в функции C#.

	Среда выполнения Функций предоставляет объект `IQueryable<T>`, привязанный к таблице. Тип `T` должен быть производным от параметра `TableEntity` или реализовать параметр `ITableEntity`. Свойства `partitionKey`, `rowKey`, `filter` и `take` не используются в этом сценарии. Для фильтрации можно использовать объект `IQueryable`.

* Чтение нескольких строк в функции Node.

	Укажите свойства `filter` и `take`. Не задавайте свойство `partitionKey` или `rowKey`.

* Запись одной или нескольких строк в функции C#.

	Среда выполнения Функций предоставляет параметр `ICollector<T>` или `IAsyncCollector<T>`, привязанный к таблице, а в свойстве `T` указывается схема сущностей, которые требуется добавить. Как правило, тип `T` является производным от `TableEntity` или реализует `ITableEntity`, но не во всех случаях. Свойства `partitionKey`, `rowKey`, `filter` и `take` не используются в этом сценарии.

#### Чтение одной сущности таблицы в коде C# или Node

В этом примере *function.json* для чтения одной строки таблицы используется триггер очереди с жестко заданным значением ключа раздела и ключом строки в сообщении очереди.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "",
      "direction": "in"
    }
  ],
  "disabled": false
}
```
Следующий пример кода C# используется с предыдущим файлом *function.json* для чтения одной сущности таблицы. В сообщении очереди содержится значение ключа строки, а сущность таблицы считывается в тип, определенный в файле *run.csx*. Этот тип содержит свойства `PartitionKey` и `RowKey`. Он не является производным от `TableEntity`.

```csharp
public static void Run(string myQueueItem, Person personEntity, TraceWriter log)
{
    log.Verbose($"C# Queue trigger function processed: {myQueueItem}");
    log.Verbose($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

Следующий пример кода Node также используется с предыдущим файлом *function.json* для чтения одной сущности таблицы.

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

#### Чтение нескольких сущностей таблицы на языке C# 

В следующем примере файла *function.json* и кода C# считываются сущности для ключа раздела, указанного в сообщении очереди.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Код C# добавляет ссылку на пакет SDK для службы хранилища Azure, чтобы тип сущности мог быть производным от `TableEntity`.

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, TraceWriter log)
{
    log.Verbose($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.Verbose($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
``` 

#### Создание сущностей таблицы на языке C# 

В следующем примере файлов *function.json* и *run.csx* показано, как создавать сущности таблицы на языке C#.

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

```csharp
public static void Run(string input, ICollector<Person> tableBinding, TraceWriter log)
{
    for (int i = 1; i < 10; i++)
        {
            log.Verbose($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

#### Создание сущности таблицы на платформе Node

В следующем примере файлов *function.json* и *run.csx* показано, как создавать сущности таблицы на платформе Node.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "",
      "name": "personEntity",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": true
}
```

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.bindings.personEntity = {"Name": "Name" + myQueueItem }
    context.done();
};
```

## Привязки Azure DocumentDB

Этот раздел содержит следующие подразделы:

* [Входная привязка для Azure DocumentDB](#docdbinput)
* [Выходная привязка для Azure DocumentDB](#docdboutput)

### <a id="docdbinput"></a> Входная привязка для Azure DocumentDB

Входные привязки могут загрузить документ из коллекции DocumentDB и передать его непосредственно в привязку. Идентификатор документа можно определить по триггеру, который вызвал функцию. В функции C# любые изменения, внесенные в запись, будут автоматически отправляться обратно в коллекцию после успешного выхода из функции.

Файл function.json содержит следующие свойства, которые можно использовать с входной привязкой DocumentDB:

- `name` — имя переменной, используемой в коде функции для документа.
- `type` — для этого свойства необходимо задать значение documentdb.
- `databaseName` — база данных, содержащая документ.
- `collectionName` — коллекция, содержащая документ.
- `id` — идентификатор документа, который нужно получить. Это свойство поддерживает привязки, аналогичные {queueTrigger}, в которых в качестве идентификатора документа будет использоваться значение строки сообщения очереди.
- `connection` — эта строка должна быть строкой параметра приложения, для которой установлена конечная точка учетной записи DocumentDB. При выборе учетной записи на вкладке "Интеграция" будет создан параметр приложения с именем в формате yourAccount\_DOCUMENTDB. Если параметр приложения нужно создать вручную, фактическую строку подключения необходимо записать в следующем формате: AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>;.
- direction — для этого свойства нужно задать значение *in*.

Пример файла function.json:
 
	{
	  "bindings": [
	    {
	      "name": "document",
	      "type": "documentdb",
	      "databaseName": "MyDatabase",
	      "collectionName": "MyCollection",
	      "id" : "{queueTrigger}",
	      "connection": "MyAccount_DOCUMENTDB",     
	      "direction": "in"
	    }
	  ],
	  "disabled": false
	}

#### Пример входного кода Azure DocumentDB для триггера очереди C#
 
Используя пример файла function.json выше, входная привязка DocumentDB извлечет документ с идентификатором, который соответствует строке сообщения очереди, и передаст его в параметр document. Если этот документ не найден, параметр document будет иметь значение null. При выходе из функции документу присваивается новое значение text.
 
	public static void Run(string myQueueItem, dynamic document)
	{   
	    document.text = "This has changed.";
	}

#### Пример входного кода Azure DocumentDB для триггера очереди Node.js
 
Используя пример файла function.json выше, входная привязка DocumentDB извлечет документ с идентификатором, который соответствует строке сообщения очереди, и передаст его в свойство привязки `documentIn`. В функциях Node.js обновленные документы не отправляются обратно в коллекцию. Тем не менее для поддержки обновлений входную привязку можно передать непосредственно в выходную привязку DocumentDB с именем `documentOut`. В этом примере кода значение свойства text входного документа обновляется и устанавливается в качестве выходного документа.
 
	module.exports = function (context, input) {   
	    context.bindings.documentOut = context.bindings.documentIn;
	    context.bindings.documentOut.text = "This was updated!";
	    context.done();
	};

### <a id="docdboutput"></a> Выходные привязки для Azure DocumentDB

Функции могут записать документ JSON в базу данных Azure DocumentDB с помощью выходной привязки для **документов Azure DocumentDB**. Дополнительные сведения об Azure DocumentDB см. в статье [Введение DocumentDB](../documentdb/documentdb-introduction.md) и в [руководстве по началу работы](../documentdb/documentdb-get-started.md).

Файл function.json содержит следующие свойства, которые можно использовать с выходной привязкой DocumentDB:

- `name` — имя переменной, используемой в коде функции для нового документа.
- `type` — для этого свойства необходимо задать значение *documentdb*.
- `databaseName` — база данных, содержащая коллекцию, в которой будет создан документ.
- `collectionName` — коллекция, в которой будет создан документ.
- `createIfNotExists` — логическое значение, указывающее, нужно ли создавать коллекцию, если она не существует. Значение по умолчанию — *false*. Это вызвано тем, что коллекции создаются с использованием зарезервированной пропускной способности, с которой связаны ценовые требования. Дополнительные сведения см. на [странице цен](https://azure.microsoft.com/pricing/details/documentdb/).
- `connection` — эта строка должна быть строкой **параметра приложения**, для которой установлена конечная точка учетной записи DocumentDB. При выборе учетной записи на вкладке **Интеграция** будет создан параметр приложения с именем в формате `yourAccount_DOCUMENTDB`. Если параметр приложения нужно создать вручную, строку подключения необходимо записать в формате `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>;`. 
- `direction` — для этого свойства нужно задать значение *out*. 
 
Пример файла function.json:

	{
	  "bindings": [
	    {
	      "name": "document",
	      "type": "documentdb",
	      "databaseName": "MyDatabase",
	      "collectionName": "MyCollection",
	      "createIfNotExists": false,
	      "connection": "MyAccount_DOCUMENTDB",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}


#### Пример выходного кода Azure DocumentDB для триггера очереди Node.js

	module.exports = function (context, input) {
	   
	    context.bindings.document = {
	        text : "I'm running in a Node function! Data: '" + input + "'"
	    }   
	 
	    context.done();
	};

Выходной документ:

	{
	  "text": "I'm running in a Node function! Data: 'example queue data'",
	  "id": "01a817fe-f582-4839-b30c-fb32574ff13f"
	}
 

#### Пример выходного кода Azure DocumentDB для триггера очереди C#


	using System;

	public static void Run(string myQueueItem, out object document, TraceWriter log)
	{
	    log.Verbose($"C# Queue trigger function processed: {myQueueItem}");
	   
	    document = new {
	        text = $"I'm running in a C# function! {myQueueItem}"
	    };
	}


#### Пример выходного кода Azure DocumentDB для настройки имени файла

Если нужно задать имя документа в функции, просто задайте значение `id`. Например, если содержимое JSON для сотрудника передано в очередь, как в следующем примере:

	{
	  "name" : "John Henry",
      "employeeId" : "123456",
	  "address" : "A town nearby"
	}

В функции триггера очереди можно использовать следующий код C#:
	
	#r "Newtonsoft.Json"
	
	using System;
	using Newtonsoft.Json;
	using Newtonsoft.Json.Linq;
	
	public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
	{
	    log.Verbose($"C# Queue trigger function processed: {myQueueItem}");
	    
	    dynamic employee = JObject.Parse(myQueueItem);
	    
	    employeeDocument = new {
	        id = employee.name + "-" + employee.employeeId,
	        name = employee.name,
	        employeeId = employee.employeeId,
	        address = employee.address
	    };
	}

Выходные данные примера:

	{
	  "id": "John Henry-123456",
	  "name": "John Henry",
	  "employeeId": "123456",
	  "address": "A town nearby"
	}

## Привязки инструмента Easy Tables в мобильных приложениях Azure

Мобильные приложения службы приложений Azure позволяют предоставлять данные конечных точек таблиц мобильным клиентам. Эти же табличные данные можно использовать во входных и выходных привязках в Функциях Azure. При наличии мобильного приложения серверной части Node.js с этими табличными данными можно работать на портале Azure, используя инструмент *Easy Tables*. Инструмент Easy Tables поддерживает динамическую схему, чтобы столбцы добавлялись автоматически в соответствии с формой вставляемых данных. Таким образом упрощается разработка схемы. Динамическая схема включена по умолчанию и должна быть отключена в рабочем мобильном приложении. Дополнительные сведения об Easy Tables в мобильных приложениях см. в разделе [Практическое руководство. Работа с инструментом "Easy Tables" на портале Azure](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#in-portal-editing). Обратите внимание, что сейчас Easy Tables на портале не поддерживаются для мобильных приложений серверной части .NET. Вы можете по-прежнему использовать привязки функций для конечных точек таблиц мобильных приложений серверной части .NET. Однако динамическая схема в этих приложениях не поддерживается.

Этот раздел содержит следующие подразделы:

* [Ключ API Easy Tables мобильных приложений Azure](#easytablesapikey)
* [Входная привязка Easy Tables мобильных приложений Azure](#easytablesinput)
* [Выходная привязка Easy Tables мобильных приложений Azure](#easytablesoutput)

### <a id="easytablesapikey"></a> Использование ключа API для обеспечения безопасного доступа к конечным точкам Easy Tables в мобильных приложениях Azure

В настоящее время Функции Azure не могут получить доступ к конечным точкам, защищенным с помощью аутентификации службы приложений. Это означает, что все конечные точки мобильных приложений, используемые в функциях, с привязками Easy Tables должны разрешать анонимный доступ (настройка по умолчанию). Привязки Easy Tables позволяют указать ключ API — общий секрет, который можно использовать для предотвращения нежелательного доступа из приложений, отличных от функций. Служба мобильных приложений не включает встроенную поддержку аутентификации с помощью ключа API. Тем не менее его можно реализовать в мобильном приложении серверной части Node.js, используя примеры в статье [Azure App Service Mobile Apps backend implementing an API key](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key) (Мобильные приложения службы приложений Azure, реализующие ключ API).

>[AZURE.IMPORTANT] Этот ключ API не следует предоставлять клиентам мобильных приложений. Его нужно безопасно предоставлять клиентам на стороне службы, например Функциям Azure.

### <a id="easytablesinput"></a> Входная привязка Easy Tables мобильных приложений Azure

Входные привязки могут загрузить запись из конечной точки таблицы мобильных приложений и передать ее непосредственно вашей привязке. Идентификатор записи определяется по триггеру, который вызвал функцию. В функции C# любые изменения, внесенные в запись, автоматически отправляются обратно в таблицу после успешного выхода из функции.

Файл function.json поддерживает следующие свойства, которые можно использовать во входных привязках Easy Tables мобильных приложений:

- `name` — имя переменной, используемой в коде функции для новой записи.
- `type` — для типа привязки нужно задать значение *easyTable*.
- `tableName` — таблица, в которой будет создана запись.
- `id` — идентификатор записи, которую нужно получить. Это свойство поддерживает привязки, аналогичные `{queueTrigger}`, в которых в качестве идентификатора записи будет использоваться значение строки сообщения очереди.
- `apiKey` — строка, представляющая собой параметр приложения, который указывает дополнительный ключ API для мобильного приложения. Наличие этой строки обязательно, если мобильное приложение использует ключ API для ограничения клиентского доступа.
- `connection` — строка, представляющая собой параметр приложения, который указывает URI мобильного приложения.
- `direction` — направление привязки. Для этого свойства следует задать значение *in*.

Пример файла function.json:

	{
	  "bindings": [
	    {
	      "name": "record",
	      "type": "easyTable",
	      "tableName": "MyTable",
	      "id" : "{queueTrigger}",
	      "connection": "My_MobileApp_Uri",
	      "apiKey": "My_MobileApp_Key",
	      "direction": "in"
	    }
	  ],
	  "disabled": false
	}

#### Пример кода Easy Tables мобильных приложений Azure для триггера очереди C#

Исходя из примера файла function.json выше, входная привязка извлекает запись с идентификатором, который соответствует строке сообщения очереди, и передает ее в параметр *record*. Если запись не найдена, параметр имеет значение null. При выходе из функции записи присваивается новое значение *text*.

	#r "Newtonsoft.Json"	
	using Newtonsoft.Json.Linq;
	
	public static void Run(string myQueueItem, JObject record)
	{
	    if (record != null)
	    {
	        record["Text"] = "This has changed.";
	    }    
	}

#### Пример кода Easy Tables мобильных приложений Azure для триггера очереди Node.js

Исходя из примера файла function.json выше, входная привязка извлекает запись с идентификатором, который соответствует строке сообщения очереди, и передает ее в параметр *record*. В функциях Node.js обновленные записи не отправляются обратно в таблицу. В этом примере кода полученная запись записывается в журнал.

	module.exports = function (context, input) {    
	    context.log(context.bindings.record);
	    context.done();
	};


### <a id="easytablesoutput"></a> Выходная привязка Easy Tables мобильных приложений Azure

Функция может сделать запись в конечную точку таблицы мобильных приложений, используя выходную привязку Easy Tables.

Файл function.json поддерживает следующие свойства, которые можно использовать в выходных привязках Easy Tables:

- `name` — имя переменной, используемой в коде функции для новой записи.
- `type` — тип привязки, для которого нужно задать значение *easyTable*.
- `tableName` — таблица, в которой создается запись.
- `apiKey` — строка, представляющая собой параметр приложения, который указывает дополнительный ключ API для мобильного приложения. Наличие этой строки обязательно, если мобильное приложение использует ключ API для ограничения клиентского доступа.
- `connection` — строка, представляющая собой параметр приложения, который указывает URI мобильного приложения.
- `direction` — направление привязки. Для этого свойства следует задать значение *out*.

Пример файла function.json:

	{
	  "bindings": [
	    {
	      "name": "record",
	      "type": "easyTable",
	      "tableName": "MyTable",
	      "connection": "My_MobileApp_Uri",
	      "apiKey": "My_MobileApp_Key",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}

#### Пример кода Easy Tables мобильных приложений Azure для триггера очереди C#

В этом примере кода C# новая запись со свойством *Text* вставляется в таблицу, указанную в привязке выше.

	public static void Run(string myQueueItem, out object record)
	{
	    record = new {
	        Text = $"I'm running in a C# function! {myQueueItem}"
	    };
	}

#### Пример кода Easy Tables мобильных приложений Azure для триггера очереди Node.js

В этом примере кода Node.js новая запись со свойством *text* вставляется в таблицу, указанную в привязке выше.

	module.exports = function (context, input) {
	
	    context.bindings.record = {
	        text : "I'm running in a Node function! Data: '" + input + "'"
	    }   
	
	    context.done();
	};

## Выходная привязка для центра уведомлений Azure

Функции могут отправлять push-уведомления в нескольких строках кода, используя настроенный центр уведомлений Azure. Однако центр уведомлений нужно настроить для служб уведомлений платформы (PNS), которые необходимо использовать. Чтобы получить дополнительные сведения о настройке центра уведомлений Azure и разработке клиентских приложений, которые регистрируют для использования уведомлений, перейдите к статье [Приступая к работе с Центрами уведомлений](../notification-hubs/notification-hubs-windows-store-dotnet-get-started.md) и щелкните целевую клиентскую платформу вверху.

Файл function.json содержит следующие свойства, которые можно использовать с выходной привязкой центра уведомлений:

- `name` — имя переменной, используемой в коде функции для сообщения центра уведомлений.
- `type` — для этого свойства следует задать значение *notificationHub*.
- `tagExpression` — с помощью выражений тегов можно указать, что уведомления должны отправляться на устройства, зарегистрированные для получения уведомлений, соответствующих выражению тега. Дополнительные сведения см. в статье [Маршрутизация и выражения тегов](../notification-hubs/notification-hubs-routing-tag-expressions.md).
- `hubName` — имя ресурса центра уведомлений на портале Azure.
- `connection` — эта строка должна быть строкой подключения **параметра приложения**, для которой задано значение *DefaultFullSharedAccessSignature* для центра уведомлений.
- `direction` — для этого свойства нужно задать значение *out*. 
 
Пример файла function.json:

	{
	  "bindings": [
	    {
	      "name": "notification",
	      "type": "notificationHub",
	      "tagExpression": "",
	      "hubName": "my-notification-hub",
	      "connection": "MyHubConnectionString",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}

### Настройка строки подключения к центру уведомлений Azure

Для использования выходной привязки центра уведомлений необходимо настроить отдельную строку подключения. Это можно сделать на вкладке *Интеграция*, просто выбрав центр уведомлений или создав его.

Можно также вручную добавить строку подключения для существующего центра, добавив ее в качестве значения *DefaultFullSharedAccessSignature* для центра уведомлений. Эта строка подключения предоставляет разрешение на доступ к функции для отправки сообщений с уведомлениями. К значению строки подключения *DefaultFullSharedAccessSignature* можно получить доступ, нажав кнопку **Ключи** в главной колонке ресурса центра уведомлений на портале Azure. Чтобы добавить строку подключения для центра вручную, сделайте следующее:

1. В колонке **Приложения функций** на портале Azure щелкните **Параметры приложения функций > Перейти к параметрам службы приложений**.

2. В колонке **Параметры** щелкните раздел **Параметры приложения**.

3. Прокрутите вниз до раздела **Строки подключения** и добавьте именованную запись в качестве значения *DefaultFullSharedAccessSignature* для центра уведомлений. Измените тип на **Пользовательский**.
4. Укажите имя строки подключения в выходных привязках, как и в случае с именем строки подключения **MyHubConnectionString**, использованном в примере выше.

### Пример кода для центра уведомлений Azure для триггера таймера Node.js 

В этом примере отправляется уведомление для [регистрации шаблона](../notification-hubs/notification-hubs-templates.md), содержащее свойства `location` и `message`.

	module.exports = function (context, myTimer) {
	    var timeStamp = new Date().toISOString();
	   
	    if(myTimer.isPastDue)
	    {
	        context.log('Node.js is running late!');
	    }
	    context.log('Node.js timer trigger function ran!', timeStamp);  
	    context.bindings.notification = {
	        location: "Redmond",
	        message: "Hello from Node!"
	    };
	    context.done();
	};

### Пример кода для центра уведомлений Azure для триггера очереди C#

В этом примере отправляется уведомление для [регистрации шаблона](../notification-hubs/notification-hubs-templates.md), содержащее свойство `message`.


	using System;
	using System.Threading.Tasks;
	using System.Collections.Generic;
	 
	public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
	{
	    log.Verbose($"C# Queue trigger function processed: {myQueueItem}");
        notification = GetTemplateProperties(myQueueItem);
	}
	 
	private static IDictionary<string, string> GetTemplateProperties(string message)
	{
	    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
	    templateProperties["message"] = message;
	    return templateProperties;
	}

В этом примере отправляется уведомление для [регистрации шаблона](../notification-hubs/notification-hubs-templates.md), содержащее свойство `message`, с использованием допустимой строки JSON.

	using System;
	 
	public static void Run(string myQueueItem,  out string notification, TraceWriter log)
	{
		log.Verbose($"C# Queue trigger function processed: {myQueueItem}");
		notification = "{"message":"Hello from C#. Processed a queue item!"}";
	}

### Пример кода C# для триггера очереди для центра уведомлений Azure с использованием типа Notification

В этом примере показано, как использовать тип `Notification`, определенный в [библиотеке центров уведомлений Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). Чтобы использовать этот тип и библиотеку, необходимо передать файл *project.json* для приложения функций. Файл project.json — это текстовый JSON-файл, который будет выглядеть следующим образом:

	{
	  "frameworks": {
	    ".NETFramework,Version=v4.6": {
	      "dependencies": {
	        "Microsoft.Azure.NotificationHubs": "1.0.4"
	      }
	    }
	  }
	}

Дополнительные сведения о передаче файла project.json см. в статье о [передаче файла project.json](http://stackoverflow.com/questions/36411536/how-can-i-use-nuget-packages-in-my-azure-functions).

Пример кода:

	using System;
	using System.Threading.Tasks;
	using Microsoft.Azure.NotificationHubs;
	 
	public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
	{
	   log.Verbose($"C# Queue trigger function processed: {myQueueItem}");
	   notification = GetTemplateNotification(myQueueItem);
	}
	private static TemplateNotification GetTemplateNotification(string message)
	{
	    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
	    templateProperties["message"] = message;
	    return new TemplateNotification(templateProperties);
	}

## Дальнейшие действия

Для получения дополнительных сведений см. следующие ресурсы:

* [Справочник разработчика по функциям Azure](functions-reference.md);
* [Справочник разработчика C# по функциям Azure](functions-reference-csharp.md);
* [Справочник разработчика NodeJS по функциям Azure](functions-reference-node.md).

<!---HONumber=AcomDC_0420_2016-->