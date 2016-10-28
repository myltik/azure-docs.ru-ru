<properties
	pageTitle="Привязки HTTP и webhook в функциях Azure | Microsoft Azure"
	description="Узнайте, как использовать триггеры и привязки HTTP и webhook в функциях Azure."
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
	ms.date="08/22/2016"
	ms.author="chrande"/>

# Привязки HTTP и webhook в функциях Azure

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Эта статья объясняет, как настроить и запрограммировать триггеры и привязки HTTP и webhook в функциях Azure.

[AZURE.INCLUDE [общие сведения](../../includes/functions-bindings-intro.md)]

## Файл function.json для привязок HTTP и webhook

В файле *function.json* содержатся свойства, которые относятся как к запросу, так и к ответу.

Свойства HTTP-запроса:

- `name` — имя переменной, используемой в коде функции для объекта запроса (или для текста запроса, если используются функции Node.js).
- `type` — для этого свойства нужно задать значение *httpTrigger*.
- `direction` — для этого свойства необходимо задать значение *in*.
- `webHookType` — для триггеров webhook допустимыми значениями являются *github*, *slack* и *genericJson*. Для триггера HTTP, отличного от триггера веб-перехватчика, в качестве значения для этого свойства задается пустая строка. Дополнительные сведения о webhook см. в приведенном ниже разделе [Триггеры webhook](#webhook-triggers).
- `authLevel` — это свойство не применяется к триггерам webhook. Задайте значение function, чтобы появлялся запрос на ключ API, anonymous, чтобы требование ключа API игнорировалось, или admin, чтобы появлялся запрос на главный ключ API. Дополнительные сведения см. в приведенном ниже разделе [Ключи API](#apikeys).

Свойства HTTP-ответа:

- `name` — имя переменной, используемой в коде функции для объекта ответа.
- `type` — для этого свойства нужно задать значение *http*.
- `direction` — для этого свойства необходимо задать значение *out*.
 
Пример файла *function.json*:

```json
{
  "bindings": [
    {
      "webHookType": "",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
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

## Триггеры веб-перехватчика

Триггер веб-перехватчика — это триггер HTTP, обладающий следующими возможностями, предназначенными для веб-перехватчиков:

* Для отдельных поставщиков веб-перехватчиков (сейчас поддерживаются GitHub и Slack) среда выполнения Функций проверяет подпись поставщика.
* Для функций Node.js среда выполнения Функций предоставляет текст запроса, а не объект запроса. Функции C# не обрабатываются особым образом, так как вы управляете предоставляемыми объектами, указывая тип параметра. Указывая `HttpRequestMessage`, вы получаете объект запроса. При указании типа POCO среда выполнения Функций пытается проанализировать объект JSON в тексте запроса, чтобы указать свойства объекта.
* Для активации функции веб-перехватчика HTTP-запрос должен содержать ключ API. Для триггеров HTTP, отличных от веб-перехватчиков, это требование необязательно.

Сведения о настройке webhook GitHub см. в статье [Creating WebHooks](http://go.microsoft.com/fwlink/?LinkID=761099&clcid=0x409) (Создание webhook) на сайте GitHub Developer.

## URL-адрес для запуска функции

Для запуска функции необходимо отправить HTTP-запрос на URL-адрес, который состоит из URL-адреса приложения-функции и имени функции:

```
 https://{function app name}.azurewebsites.net/api/{function name} 
```

## Ключи API

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

## Пример кода C# для функции триггера HTTP 

В примере кода выполняется поиск параметра `name` в строке запроса или в тексте HTTP-запроса.

```csharp
using System.Net;
using System.Threading.Tasks;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

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

## Пример кода F# для функции триггера HTTP

В примере кода выполняется поиск параметра `name` в строке запроса или в тексте HTTP-запроса.

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic

let Run(req: HttpRequestMessage) =
    async {
        let q =
            req.GetQueryNameValuePairs()
                |> Seq.tryFind (fun kv -> kv.Key = "name")
        match q with
        | Some kv ->
            return req.CreateResponse(HttpStatusCode.OK, "Hello " + kv.Value)
        | None ->
            let! data = Async.AwaitTask(req.Content.ReadAsAsync<obj>())
            try
                return req.CreateResponse(HttpStatusCode.OK, "Hello " + data?name)
            with e ->
                return req.CreateErrorResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
    } |> Async.StartAsTask
```

Вам потребуется файл `project.json`, который использует NuGet для ссылки на сборки `FSharp.Interop.Dynamic` и `Dynamitey`, как показано ниже.

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

С помощью NuGet файл извлекает зависимости и создает на них ссылки в сценарии.

## Пример кода Node.js для функции триггера HTTP 

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

## Пример кода C# для функции веб-перехватчика GitHub 

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

    log.Info($"WebHook was triggered! Comment: {data.comment.body}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        body = $"New GitHub comment: {data.comment.body}"
    });
}
```

## Пример кода F# для функции перехватчика webhook на GitHub

В этом примере кода регистрируются комментарии о выдаче GitHub.

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Response = {
    body: string
}

let Run(req: HttpRequestMessage, log: TraceWriter) =
    async {
        let! content = req.Content.ReadAsStringAsync() |> Async.AwaitTask
        let data = content |> JsonConvert.DeserializeObject
        log.Info(sprintf "GitHub WebHook triggered! %s" data?comment?body)
        return req.CreateResponse(
            HttpStatusCode.OK,
            { body = sprintf "New GitHub comment: %s" data?comment?body })
    } |> Async.StartAsTask
```

## Пример кода Node.js для функции веб-перехватчика GitHub 

В этом примере кода регистрируются комментарии о выдаче GitHub.

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## Дальнейшие действия

[AZURE.INCLUDE [дальнейшие действия](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0921_2016-->