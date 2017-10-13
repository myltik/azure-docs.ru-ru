---
title: "Привязки HTTP и webhook в функциях Azure | Microsoft Azure"
description: "Узнайте, как использовать триггеры и привязки HTTP и webhook в функциях Azure."
services: functions
documentationcenter: na
author: mattchenderson
manager: cfowler
editor: 
tags: 
keywords: "функции azure, функции, обработка событий, webhook, динамические вычисления, бессерверная архитектура, HTTP, API, REST"
ms.assetid: 2b12200d-63d8-4ec1-9da8-39831d5a51b1
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/26/2017
ms.author: mahender
ms.openlocfilehash: 3c3247592cbe2bc382d220264b0c646ee566b8a7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-http-and-webhook-bindings"></a>Привязки HTTP и webhook в функциях Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

В этой статье объясняется, как настраивать и использовать триггеры и привязки в функциях Azure.
С их помощью на базе функций Azure можно создавать бессерверные API-интерфейсы и отвечать на вызовы webhook.

Функции Azure предоставляют следующие привязки.
- [Триггер HTTP](#httptrigger) позволяет вызвать функцию с помощью HTTP-запроса. Его можно настроить для ответа на [webhook](#hooktrigger).
- [Привязка для вывода HTTP](#output) дает возможность возвращать ответ на запрос.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

<a name="httptrigger"></a>

## <a name="http-trigger"></a>Триггер HTTP
Триггер HTTP выполняет выбранную функцию в ответ на HTTP-запрос. Его можно настроить для ответа на определенный URL-адрес или набор методов HTTP. Триггер HTTP также можно настроить для ответа на вызовы webhook. 

Если вы используете портал функций, вы сможете быстро начать работу с помощью готового шаблона. Щелкните **Новая функция**, а затем выберите "API и Webhook" из раскрывающегося списка **Сценарий**. Выберите один из шаблонов и нажмите кнопку **Создать**.

По умолчанию триггер HTTP возвращает на запрос ответ с кодом состояния HTTP "200 ОК" и пустым текстом. Чтобы изменить ответ, настройте [привязку для вывода HTTP](#output).

### <a name="configuring-an-http-trigger"></a>Настройка триггера HTTP
Триггер HTTP определяется объектом JSON в массиве `bindings` файла function.json, как показано в следующем примере.

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function",
    "methods": [ "get" ],
    "route": "values/{id}"
},
```
Привязка поддерживает следующие свойства.

|Свойство  |Описание  |
|---------|---------|
| **name** | Обязательное. Имя переменной, из которой в коде функции можно получить запрос или текст запроса. См. раздел [Использование триггера HTTP в коде программы](#httptriggerusage). |
| **type** | Обязательное. Необходимо задать значение `httpTrigger`. |
| **direction** | Обязательное. Необходимо задать значение `in`. |
| **authLevel** | Определяет, какие ключи (если они требуются) должны присутствовать в запросе, чтобы вызвать функцию. Принимается одно из следующих значений: <ul><li><code>anonymous</code>&mdash; — ключи API не требуются.</li><li><code>function</code>&mdash; — требуется ключ API для конкретной функции. Это значение используется по умолчанию, если не указано иное.</li><li><code>admin</code>&mdash; — требуется главный ключ.</li></ul> Дополнительные сведения см. в разделе [Работа с ключами](#keys). |
| **methods** | Массив методов HTTP, на которые отвечает функция. Если свойство не указано, функция отвечает на все методы HTTP. См. раздел [Настройка конечной точки HTTP](#url). |
| **route** | Шаблон маршрута, определяющий URL-адреса запросов, на которые отвечает функция. Если значение не указано, по умолчанию используется `<functionname>`. Дополнительные сведения см. в разделе [Настройка конечной точки HTTP](#url). |
| **webHookType** | Указывает, что триггер HTTP должен работать как получатель веб-перехватчика для указанного поставщика. Не используйте свойство _methods_ при выборе этого параметра. Принимается одно из следующих значений:<ul><li><code>genericJson</code>&mdash; — конечная точка веб-перехватчика общего назначения без логики для конкретного поставщика.</li><li><code>github</code>&mdash; — функция отвечает на вызовы веб-перехватчика GitHub. Не используйте свойство _authLevel_ при выборе этого значения.</li><li><code>slack</code>&mdash; — функция отвечает на вызовы веб-перехватчика Slack. Не используйте свойство _authLevel_ при выборе этого значения.</li></ul> Дополнительные сведения см. в разделе [Ответ на вызовы веб-перехватчика](#hooktrigger). |

<a name="httptriggerusage"></a>
### <a name="working-with-an-http-trigger-from-code"></a>Использование триггера HTTP в коде программы
Для функций на языках C# и F# можно объявить для входных данных триггера тип `HttpRequestMessage` или пользовательский тип .NET. Если вы выберете `HttpRequestMessage`, то получите полный доступ к объекту запроса. При объявлении пользовательского типа .NET Функции попытаются проанализировать текст запроса JSON для задания свойств объекта. 

Для функций Node.js среда выполнения Функций предоставляет текст запроса, а не объект запроса. Дополнительные сведения см. в разделе [Образцы триггеров HTTP](#httptriggersample).


<a name="output"></a>
## <a name="http-response-output-binding"></a>Привязка для вывода HTTP-ответа
Привязка для вывода HTTP используется для ответа отправителю запроса HTTP. Эта привязка требует наличия триггера HTTP и позволяет настроить ответ, возвращаемый на запрос этого триггера. Если привязка для вывода HTTP отсутствует, триггер HTTP возвращает ответ HTTP "200 ОК" с пустым текстом. 

### <a name="configuring-an-http-output-binding"></a>Настройка привязки для вывода HTTP
Привязка для вывода HTTP определяется объектом JSON в массиве `bindings` файла function.json, как показано в следующем примере.

```json
{
    "name": "res",
    "type": "http",
    "direction": "out"
}
```
Ниже приведены обязательные свойства, которые поддерживает привязка.

|Свойство  |Описание  |
|---------|---------|
|**name** | Имя переменной, используемой в коде функции для ответа. См. раздел [Работа с привязкой для вывода HTTP в коде программы](#outputusage). |
| **type** |Нужно задать значение `http`. |
| **direction** | Нужно задать значение `out`. |

<a name="outputusage"></a>
### <a name="working-with-an-http-output-binding-from-code"></a>Работа с привязкой для вывода HTTP в коде программы
Используйте параметр вывода для ответа отправителю запроса HTTP или веб-перехватчика. Вы также можете использовать шаблоны ответов языкового стандарта. Примеры ответов см. в разделах [Образцы триггеров HTTP](#httptriggersample) и [Примеры вызовов веб-перехватчика](#hooktriggersample).


<a name="hooktrigger"></a>
## <a name="responding-to-webhooks"></a>Ответ на вызовы webhook
Триггер HTTP с заданным свойством _webHookType_ отвечает на вызовы [веб-перехватчика](https://en.wikipedia.org/wiki/Webhook). В стандартной конфигурации используется параметр genericJson. Он определяет, что принимаются только запросы HTTP POST с содержимым типа `application/json`.

Кроме того, триггер можно адаптировать для определенного поставщика веб-перехватчика, например [GitHub](https://developer.github.com/webhooks/) или [Slack](https://api.slack.com/outgoing-webhooks). Если указан поставщик, среда выполнения функций автоматически выполнит логику проверки поставщика.  

### <a name="configuring-github-as-a-webhook-provider"></a>Настройка GitHub в качестве поставщика webhook
Чтобы настроить ответ на вызовы веб-перехватчика GitHub, прежде всего создайте функцию с триггером HTTP, для которого свойство **webHookType** будет иметь значение `github`. Затем скопируйте его [URL-адрес](#url) и [ключ API](#keys) в репозиторий GitHub, используя страницу **Добавить веб-перехватчик**. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

Дополнительные сведения см. в статье [Создание функции, активируемой объектом webhook GitHub](functions-create-github-webhook-triggered-function.md).

### <a name="configuring-slack-as-a-webhook-provider"></a>Настройка Slack в качестве поставщика webhook
Webhook Slack создает маркер автоматически и не позволяет вам задать его, поэтому необходимо настроить ключ для конкретной функции с использованием маркера, предоставленного Slack. См. раздел [Работа с ключами](#keys).

<a name="url"></a>
## <a name="customizing-the-http-endpoint"></a>Настройка конечной точки HTTP
По умолчанию при создании функции для триггера HTTP или WebHook маршрут для ее адресации имеет следующий вид.

    http://<yourapp>.azurewebsites.net/api/<funcname> 

Этот маршрут можно настроить с помощью дополнительного свойства `route` привязки для вывода триггера HTTP. Например, приведенный ниже файл *function.json* определяет свойство `route` для триггера HTTP.

```json
{
    "bindings": [
    {
        "type": "httpTrigger",
        "name": "req",
        "direction": "in",
        "methods": [ "get" ],
        "route": "products/{category:alpha}/{id:int?}"
    },
    {
        "type": "http",
        "name": "res",
        "direction": "out"
    }
    ]
}
```

При такой конфигурации функция доступна по приведенному ниже маршруту вместо исходного.

    http://<yourapp>.azurewebsites.net/api/products/electronics/357

Это позволяет использовать в коде функции два параметра, передаваемых в адресе, — _category_ и _id_. Для параметров можно использовать любое [ограничение маршрута веб-API](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints). Приведенный ниже код функции C# используют оба параметра.

```csharp
public static Task<HttpResponseMessage> Run(HttpRequestMessage req, string category, int? id, 
                                                TraceWriter log)
{
    if (id == null)
        return  req.CreateResponse(HttpStatusCode.OK, $"All {category} items were requested.");
    else
        return  req.CreateResponse(HttpStatusCode.OK, $"{category} item with id = {id} has been requested.");
}
```

Ниже приведен код функции Node.js, использующей те же параметры маршрута.

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;

    if (!id) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "All " + category + " items were requested."
        };
    }
    else {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: category + " item with id = " + id + " was requested."
        };
    }

    context.done();
} 
```

По умолчанию все маршруты функций начинаются с префикса *api*. Вы можете настроить или удалить этот префикс с помощью свойства `http.routePrefix` в файле *host.json*. В следующем примере префикс маршрута *api* удаляется. Он заменяется пустой строкой в файле *host.json*.

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

Дополнительные сведения о способах обновления файла *host.json* для функции см. в разделе [Как обновить файлы приложения-функции](functions-reference.md#fileupdate). 

Дополнительные сведения о других свойствах, которые можно настроить в файле *host.json*, см. в [справочнике по host.json](functions-host-json.md).


<a name="keys"></a>
## <a name="working-with-keys"></a>Работа с ключами
Триггеры HTTP позволяют повышать безопасность с помощью ключей. Стандартный триггер HTTP может использовать их в качестве ключа API, то есть требовать наличия ключа в запросе. Вызовы webhook могут использовать ключи для проверки подлинности запросов различными способами, в зависимости от поддерживаемых поставщиком технологий.

Ключи хранятся в Azure в составе приложения-функции в зашифрованном виде. Чтобы просмотреть ключи, создать новые или сменить значения ключей, откройте на портале нужную функцию и выберите "Управление". 

Существует два типа ключей.
- **Ключи узла**, которые являются общими для всех функций в приложении-функции. Если такой ключ используется в качестве ключа API, он предоставляет доступ к любой функции в приложении-функции.
- **Ключи функции**, которые применяются только для конкретных функций, в которых они определены. Если такой ключ используется в качестве ключа API, он предоставляет доступ только к определенной функции.

Каждый ключ имеет имя для удобства использования, а один из ключей (с именем default) используется как ключ по умолчанию на уровне узла и функции. **Главный ключ** — это ключ узла по умолчанию (с именем _master). Он определяется для каждого приложения-функции. Его невозможно отозвать. Он предоставляет административный доступ к API-интерфейсам среды выполнения. Если в объекте JSON для привязки указать параметр `"authLevel": "admin"`, этот ключ должен обязательно присутствовать в запросе. Передача любого другого ключа приведет к ошибке авторизации.

> [!IMPORTANT]  
> Главный ключ предоставляет высокий уровень разрешений, поэтому никогда не передавайте этот ключ третьим лицам и не включайте его в состав клиентских приложений. Соблюдайте осторожность при выборе уровня авторизации для администратора.

### <a name="api-key-authorization"></a>Проверка подлинности с помощью ключа API
По умолчанию триггер HTTP требует наличия ключа API в HTTP-запросе. Поэтому HTTP-запрос обычно выглядит следующим образом:

    https://<yourapp>.azurewebsites.net/api/<function>?code=<ApiKey>

Ключ можно передать в переменной строки запроса с именем `code`, как показано выше, или в заголовке HTTP `x-functions-key`. Значением ключа может быть любой ключ функции, определенный для запрашиваемой функции, или любой ключ узла.

Вы можете разрешить анонимные запросы, не требующие наличие ключей. Вы также можете указать, чтобы использовался главный ключ. Уровень авторизации по умолчанию можно изменить с помощью свойства `authLevel` в объекте JSON для привязки. Дополнительные сведения см. в разделе [Триггер HTTP](#httptrigger).

### <a name="keys-and-webhooks"></a>Ключи и вызовы webhook
Авторизация веб-перехватчика обрабатывается компонентом получателя веб-перехватчика, который входит в состав триггера HTTP. Механизм обработки различается в зависимости от типа веб-перехватчика. Но каждый из этих механизмов использует ключи. По умолчанию используется ключ функции с именем default. Чтобы использовать другой ключ, необходимо настроить поставщик веб-перехватчика так, чтобы он отправлял имя ключа в составе запроса одним из следующих способов.

- **В строке запроса**: поставщик передает имя ключа в параметре `clientid` строки запроса, например `https://<yourapp>.azurewebsites.net/api/<funcname>?clientid=<keyname>`.
- **В заголовке запроса**: поставщик передает имя ключа в заголовке `x-functions-clientid`.

> [!NOTE]
> Ключи функций имеют приоритет над ключами узла. Если определены два ключа с одним именем, всегда используется ключ функции.


<a name="httptriggersample"></a>
## <a name="http-trigger-samples"></a>Образцы триггеров HTTP
Предположим, что вы определили следующий триггер HTTP в массиве `bindings` файла function.json:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

Выберите пример для конкретного языка, который выполняет поиск параметра `name` в строке запроса или в тексте HTTP-запроса.

* [C#](#httptriggercsharp)
* [F#](#httptriggerfsharp)
* [Node.js](#httptriggernodejs)


<a name="httptriggercsharp"></a>
### <a name="http-trigger-sample-in-c"></a>Пример триггера HTTP на языке C# #
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

Вы также можете выполнить привязку к объекту .NET вместо **HttpRequestMessage**. Этот объект создается из текста запроса, интерпретированного как JSON. Аналогичным образом тип можно передать в привязку вывода ответа HTTP. Результатом будет текст ответа с кодом состояния 200.

```csharp
using System.Net;
using System.Threading.Tasks;

public static string Run(CustomObject req, TraceWriter log)
{
    return "Hello " + req?.name;
}

public class CustomObject {
     public String name {get; set;}
}
}
```

<a name="httptriggerfsharp"></a>
### <a name="http-trigger-sample-in-f"></a>Пример триггера HTTP на языке F# #
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

<a name="httptriggernodejs"></a>
### <a name="http-trigger-sample-in-nodejs"></a>Пример триггера HTTP для Node.js
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
     
<a name="hooktriggersample"></a>
## <a name="webhook-samples"></a>Примеры вызовов webhook
Предположим, что вы определили следующий триггер webhook в массиве `bindings` файла function.json:

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

Выберите пример для конкретного языка, который регистрирует комментарии о проблемах на GitHub.

* [C#](#hooktriggercsharp)
* [F#](#hooktriggerfsharp)
* [Node.js](#hooktriggernodejs)

<a name="hooktriggercsharp"></a>

### <a name="webhook-sample-in-c"></a>Пример веб-перехватчика на языке C# #
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

<a name="hooktriggerfsharp"></a>

### <a name="webhook-sample-in-f"></a>Пример веб-перехватчика на языке F# #
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

<a name="hooktriggernodejs"></a>

### <a name="webhook-sample-in-nodejs"></a>Пример webhook для Node.js
```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```


## <a name="next-steps"></a>Дальнейшие действия
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

