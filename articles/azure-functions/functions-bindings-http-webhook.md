---
title: Привязки HTTP и webhook в функциях Azure
description: Узнайте, как использовать триггеры и привязки HTTP и webhook в функциях Azure.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: функции azure, функции, обработка событий, webhook, динамические вычисления, бессерверная архитектура, HTTP, API, REST
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: tdykstra
ms.openlocfilehash: 94a039ab1973cbd4112ddd0cd7548baa69924d26
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
---
# <a name="azure-functions-http-and-webhook-bindings"></a>Привязки HTTP и webhook в функциях Azure

В этой статье объясняется, как работать с привязками HTTP в службе "Функции Azure". Служба "Функции Azure" поддерживает триггеры HTTP и выходные привязки.

Триггер HTTP можно настроить на ответ [веб-перехватчикам](https://en.wikipedia.org/wiki/Webhook). Триггер веб-перехватчика проверяет JSON и принимает только полезные данные JSON. Существуют специальные версии триггера веб-перехватчика, облегчающие управление веб-перехватчиками некоторых поставщиков, например GitHub и Slack.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="packages"></a>Пакеты

Привязки служебной шины доступны в пакете NuGet [Microsoft.Azure.WebJobs.Extensions.Http](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http). Исходный код для пакета находится в репозитории GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Триггер

Триггер HTTP позволяет вызвать функцию с помощью HTTP-запроса. Триггер HTTP можно использовать для создания независимых от сервера API-интерфейсов и для ответа веб-перехватчикам. 

По умолчанию триггер HTTP возвращает на запрос ответ с кодом состояния HTTP "200 ОК" и пустым текстом. Чтобы изменить ответ, настройте [выходную привязку HTTP](#http-output-binding).

## <a name="trigger---example"></a>Пример триггера

Языковой пример см. в разделах:

* [C#](#trigger---c-example)
* [Скрипт C# (CSX)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Пример C# в триггере

В следующем примере показана [функция C#](functions-dotnet-class-library.md), выполняющая поиск параметра `name` в строке запроса или в тексте HTTP-запроса.

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)]HttpRequestMessage req, 
    TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

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

### <a name="trigger---c-script-example"></a>Пример скрипта C# в триггере

В следующем примере показаны привязка триггера в файле *function.json* и [функция сценария C#](functions-reference-csharp.md), которая использует эту привязку. В функции выполняется поиск параметра `name` в строке запроса или в тексте HTTP-запроса.

Данные привязки в файле *function.json*:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

В разделе [Конфигурация](#trigger---configuration) описываются эти свойства.

Ниже приведен код сценария C#, который выполняет привязку к `HttpRequestMessage`:

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

Вы можете выполнить привязку к пользовательскому объекту вместо `HttpRequestMessage`. Этот объект создается из текста запроса, интерпретированного как JSON. Аналогичным образом тип можно передать в привязку вывода ответа HTTP. Результатом будет текст ответа с кодом состояния 200.

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

### <a name="trigger---f-example"></a>Пример F# в триггере

В следующем примере показаны привязка триггера в файле *function.json* и [функция F#](functions-reference-fsharp.md), которая использует эту привязку. В функции выполняется поиск параметра `name` в строке запроса или в тексте HTTP-запроса.

Данные привязки в файле *function.json*:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

В разделе [Конфигурация](#trigger---configuration) описываются эти свойства.

Ниже показан код F#.

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

Вам потребуется файл `project.json`, который использует NuGet для ссылки на сборки `FSharp.Interop.Dynamic` и `Dynamitey`, как показано в следующем примере:

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

### <a name="trigger---javascript-example"></a>Пример JavaScript в триггере

В следующем примере показана привязка триггера в файле *function.json* и [функция JavaScript](functions-reference-node.md), которая использует привязку. В функции выполняется поиск параметра `name` в строке запроса или в тексте HTTP-запроса.

Данные привязки в файле *function.json*:

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

В разделе [Конфигурация](#trigger---configuration) описываются эти свойства.

Ниже показан код JavaScript.

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
     
## <a name="trigger---webhook-example"></a>Пример триггера веб-перехватчика

Языковой пример см. в разделах:

* [C#](#webhook---c-example)
* [Скрипт C# (CSX)](#webhook---c-script-example)
* [F#](#webhook---f-example)
* [JavaScript](#webhook---javascript-example)

### <a name="webhook---c-example"></a>Пример веб-перехватчика в C#

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая отправляет HTTP 200 в ответ на универсальный запрос JSON.

```cs
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run([HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    return req.CreateResponse(HttpStatusCode.OK);
}
```

### <a name="webhook---c-script-example"></a>Пример веб-перехватчика в сценарии C#

В следующем примере показаны привязка триггера веб-перехватчика в файле *function.json* и [функция сценария C#](functions-reference-csharp.md), которая использует эту привязку. Функция регистрирует комментарии к проблемам GitHub.

Данные привязки в файле *function.json*:

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

В разделе [Конфигурация](#trigger---configuration) описываются эти свойства.

Ниже приведен код скрипта C#.

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

### <a name="webhook---f-example"></a>Пример веб-перехватчика в F#

В следующем примере показаны привязка триггера веб-перехватчика в файле *function.json* и [функция F#](functions-reference-fsharp.md), которая использует эту привязку. Функция регистрирует комментарии к проблемам GitHub.

Данные привязки в файле *function.json*:

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

В разделе [Конфигурация](#trigger---configuration) описываются эти свойства.

Ниже показан код F#.

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

### <a name="webhook---javascript-example"></a>Пример веб-перехватчика в JavaScript

В следующем примере показаны привязка триггера веб-перехватчика в файле *function.json* и [функция JavaScript](functions-reference-node.md), которая использует эту привязку. Функция регистрирует комментарии к проблемам GitHub.

Данные привязки в файле *function.json*:

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

В разделе [Конфигурация](#trigger---configuration) описываются эти свойства.

Ниже показан код JavaScript.

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## <a name="trigger---attributes"></a>Атрибуты триггера

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте атрибут [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs).

В параметрах конструктора атрибута можно задать уровень авторизации и допустимые методы HTTP. Имеются свойства для типа веб-перехватчика и шаблона пути. Дополнительные сведения об этих параметрах см. в разделе [Конфигурация триггера](#trigger---configuration). Вот как выглядит атрибут `HttpTrigger` в сигнатуре метода:

```csharp
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    ...
}
 ```

Полный пример см. в разделе [Пример C# в триггере](#trigger---c-example).

## <a name="trigger---configuration"></a>Конфигурация триггера

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `HttpTrigger`.


|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
| **type** | Недоступно| Обязательное. Необходимо задать значение `httpTrigger`. |
| **direction** | Недоступно| Обязательное. Необходимо задать значение `in`. |
| **name** | Недоступно| Обязательное. Имя переменной, из которой в коде функции можно получить запрос или текст запроса. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Определяет, какие ключи (если они требуются) должны присутствовать в запросе, чтобы вызвать функцию. Уровень авторизации может принимать одно из следующих значений: <ul><li><code>anonymous</code>&mdash; — ключи API не требуются.</li><li><code>function</code>&mdash; — требуется ключ API для конкретной функции. Это значение используется по умолчанию, если не указано иное.</li><li><code>admin</code>&mdash; — требуется главный ключ.</li></ul> Дополнительные сведения см. в разделе [Ключи авторизации](#authorization-keys). |
| **methods** |**Методы** | Массив методов HTTP, на которые отвечает функция. Если свойство не указано, функция отвечает на все методы HTTP. Ознакомьтесь с разделом о [настройке конечной точки HTTP](#trigger---customize-the-http-endpoint). |
| **route** | **Route** | Шаблон маршрута, определяющий URL-адреса запросов, на которые отвечает функция. Если значение не указано, по умолчанию используется `<functionname>`. Дополнительные сведения см. в разделе о [настройке конечной точки HTTP](#customize-the-http-endpoint). |
| **webHookType** | **WebHookType** |Указывает, что триггер HTTP должен выступать в качестве получателя [веб-перехватчика](https://en.wikipedia.org/wiki/Webhook) для указанного поставщика. Если вы установите это свойство, не устанавливайте свойство `methods`. Тип веб-перехватчика может принимать одно из следующих значений:<ul><li><code>genericJson</code>&mdash; — конечная точка веб-перехватчика общего назначения без логики для конкретного поставщика. Этот параметр определяет, что принимаются только запросы HTTP POST с содержимым типа `application/json`.</li><li><code>github</code>&mdash; — функция отвечает на вызовы [веб-перехватчиков GitHub](https://developer.github.com/webhooks/). Не используйте свойство _authLevel_ вместе с веб-перехватчиками GitHub. Дополнительные сведения см. в этой статье, в разделе веб-перехватчики GitHub.</li><li><code>slack</code>&mdash; — функция отвечает на вызовы [веб-перехватчиков Slack](https://api.slack.com/outgoing-webhooks). Не используйте свойство _authLevel_ вместе с веб-перехватчиками Slack. Дополнительные сведения см. в разделе о веб-перехватчиках Slack далее в этой статье.</li></ul>|

## <a name="trigger---usage"></a>Использование триггера

Для функций на языках C# и F# можно объявить для входных данных триггера тип `HttpRequestMessage` или пользовательский тип. Если вы выберете `HttpRequestMessage`, то получите полный доступ к объекту запроса. При объявлении пользовательского типа служба "Функции" попытается проанализировать текст запроса JSON для задания свойств объекта. 

Для функций JavaScript среда выполнения службы "Функции" предоставляет текст запроса, а не объект запроса. Дополнительные сведения см. в разделе [Пример JavaScript в триггере](#trigger---javascript-example).

### <a name="github-webhooks"></a>Веб-перехватчики GitHub

Чтобы настроить ответ на вызовы веб-перехватчика GitHub, прежде всего создайте функцию с триггером HTTP, для которого свойство **webHookType** будет иметь значение `github`. Затем скопируйте его URL-адрес и ключ API в репозиторий GitHub, используя страницу **Добавить веб-перехватчик**. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

Дополнительные сведения см. в статье [Создание функции, активируемой объектом webhook GitHub](functions-create-github-webhook-triggered-function.md).

### <a name="slack-webhooks"></a>Веб-перехватчики Slack

Webhook Slack создает маркер автоматически и не позволяет вам задать его, поэтому необходимо настроить ключ для конкретной функции с использованием маркера, предоставленного Slack. Ознакомьтесь с разделом [Ключи авторизации](#authorization-keys).

### <a name="customize-the-http-endpoint"></a>Настройка конечной точки HTTP

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

```
http://<yourapp>.azurewebsites.net/api/products/electronics/357
```

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

Ниже приведен код функции Node.js, в котором используются те же параметры маршрута.

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

По умолчанию все маршруты функций начинаются с префикса *api*. Вы можете настроить или удалить этот префикс с помощью свойства `http.routePrefix` в файле [host.json](functions-host-json.md). В следующем примере префикс маршрута *api* удаляется. Он заменяется пустой строкой в файле *host.json*.

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

### <a name="authorization-keys"></a>Ключи авторизации

Триггеры HTTP позволяют повышать безопасность с помощью ключей. Стандартный триггер HTTP может использовать их в качестве ключа API, то есть требовать наличия ключа в запросе. Вызовы webhook могут использовать ключи для проверки подлинности запросов различными способами, в зависимости от поддерживаемых поставщиком технологий.

> [!NOTE]
> При локальном запуске функций авторизация отключена независимо от значения `authLevel`, установленного в `function.json`. `authLevel` применяется сразу же после публикации в решении "Функции Azure".

Ключи хранятся в Azure в составе приложения-функции в зашифрованном виде. Чтобы просмотреть ключи, создать новые или сменить значения ключей, откройте на портале нужную функцию и выберите "Управление". 

Существует два типа ключей.

- **Ключи узла**, которые являются общими для всех функций в приложении-функции. Если такой ключ используется в качестве ключа API, он предоставляет доступ к любой функции в приложении-функции.
- **Ключи функции**, которые применяются только для конкретных функций, в которых они определены. Если такой ключ используется в качестве ключа API, он предоставляет доступ только к определенной функции.

Каждый ключ имеет имя для удобства использования, а один из ключей (с именем default) используется как ключ по умолчанию на уровне узла и функции. Ключи функций имеют приоритет над ключами узла. Если определены два ключа с одним именем, всегда используется ключ функции.

**Главный ключ** — это ключ узла по умолчанию (с именем _master). Он определяется для каждого приложения-функции. Его невозможно отозвать. Он предоставляет административный доступ к API-интерфейсам среды выполнения. Если в объекте JSON для привязки указать параметр `"authLevel": "admin"`, этот ключ должен обязательно присутствовать в запросе. Передача любого другого ключа приведет к ошибке авторизации.

> [!IMPORTANT]  
> Главный ключ предоставляет высокий уровень разрешений, поэтому никогда не передавайте этот ключ третьим лицам и не включайте его в состав клиентских приложений. Соблюдайте осторожность при выборе уровня авторизации для администратора.

### <a name="api-key-authorization"></a>Проверка подлинности с помощью ключа API

По умолчанию триггер HTTP требует наличия ключа API в HTTP-запросе. Поэтому HTTP-запрос обычно выглядит следующим образом:

    https://<yourapp>.azurewebsites.net/api/<function>?code=<ApiKey>

Ключ можно передать в переменной строки запроса с именем `code`, как показано выше, или в заголовке HTTP `x-functions-key`. Значением ключа может быть любой ключ функции, определенный для запрашиваемой функции, или любой ключ узла.

Вы можете разрешить анонимные запросы, не требующие наличие ключей. Вы также можете указать, чтобы использовался главный ключ. Уровень авторизации по умолчанию можно изменить с помощью свойства `authLevel` в объекте JSON для привязки. Дополнительные сведения см. в разделе [Конфигурация триггера](#trigger---configuration).

### <a name="keys-and-webhooks"></a>Ключи и вызовы webhook

Авторизация веб-перехватчика обрабатывается компонентом получателя веб-перехватчика, который входит в состав триггера HTTP. Механизм обработки различается в зависимости от типа веб-перехватчика. Но каждый из этих механизмов использует ключи. По умолчанию используется ключ функции с именем default. Чтобы использовать другой ключ, необходимо настроить поставщик веб-перехватчика так, чтобы он отправлял имя ключа в составе запроса одним из следующих способов.

- **В строке запроса**: поставщик передает имя ключа в параметре `clientid` строки запроса, например `https://<yourapp>.azurewebsites.net/api/<funcname>?clientid=<keyname>`.
- **В заголовке запроса**: поставщик передает имя ключа в заголовке `x-functions-clientid`.

## <a name="trigger---limits"></a>Триггер — ограничения

Длина HTTP-запроса ограничена 100 МБ (104 857 600 байт), а длина URL-адреса — 4 КБ (4096 байт). Эти ограничения задает элемент `httpRuntime` [файла Web.config](https://github.com/Azure/azure-webjobs-sdk-script/blob/v1.x/src/WebJobs.Script.WebHost/Web.config) среды выполнения.

Если функция, которая использует триггер HTTP, выполняется дольше 2,5 минут, будет превышено время ожидания шлюза и шлюз вернет ошибку HTTP 502. Функция продолжит работу, но вернуть ответ HTTP будет невозможно. Для долго выполняющихся функций рекомендуем следовать асинхронным шаблонам и возвращать расположение, в котором можно проверить состояние запроса. Чтобы узнать, как долго может выполняться функция, см. раздел [Масштабирование и размещение — план](functions-scale.md#consumption-plan). 

## <a name="trigger---hostjson-properties"></a>Свойства host.json в триггере

В файле [host.json](functions-host-json.md) содержатся параметры, управляющие поведением триггера HTTP.

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="output"></a>Выходные данные

Привязка для вывода HTTP используется для ответа отправителю запроса HTTP. Эта привязка требует наличия триггера HTTP и позволяет настроить ответ, возвращаемый на запрос этого триггера. Если привязка для вывода HTTP отсутствует, триггер HTTP возвращает ответ HTTP "200 ОК" с пустым текстом. 

## <a name="output---configuration"></a>Выходная конфигурация

Для библиотек класса C# не предусмотрены свойства конфигурации привязки для выходных данных. Чтобы отправить HTTP-ответ, измените тип возвращаемого значения функции на `HttpResponseMessage` или `Task<HttpResponseMessage>`.

Для других языков выходная привязка HTTP определяется как объект JSON в массиве `bindings` файла function.json, как показано в следующем примере.

```json
{
    "name": "res",
    "type": "http",
    "direction": "out"
}
```

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json*.

|Свойство  |ОПИСАНИЕ  |
|---------|---------|
| **type** |Нужно задать значение `http`. |
| **direction** | Нужно задать значение `out`. |
|**name** | Имя переменной, используемой в коде функции для ответа. |

## <a name="output---usage"></a>Использование выходной привязки

Используйте параметр вывода для ответа отправителю запроса HTTP или веб-перехватчика. Вы также можете использовать шаблоны ответов языкового стандарта. Примеры ответов см. в разделе с [примером триггера](#trigger---example) и [примером веб-перехватчика](#trigger---webhook-example).

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)
