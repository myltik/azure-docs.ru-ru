---
title: Привязки Azure Cosmos DB для службы "Функций Azure" версии 2.х (предварительная версия)
description: Узнайте, как использовать триггеры и привязки Azure Cosmos DB в службе "Функции Azure".
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: функции azure, функции, обработка событий, динамические вычисления, независимая архитектура
ms.service: functions; cosmos-db
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: tdykstra
ms.openlocfilehash: e40ba6bcfa1b6247f62849626d4a7803a76362a1
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234875"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-2x-preview"></a>Привязки Azure Cosmos DB для службы "Функции Azure" версии 2.х (предварительная версия)

> [!div class="op_single_selector" title1="Select the version of the Azure Functions runtime you are using: "]
> * [Версия 1 — общедоступная](functions-bindings-cosmosdb.md)
> * [Версия 2 — предварительная](functions-bindings-cosmosdb-v2.md)

В этой статье описано, как использовать привязки [Azure Cosmos DB](..\cosmos-db\serverless-computing-database.md) в службе "Функции Azure" версии 2.x. Служба "Функции Azure" поддерживает привязки триггера, а также входные и выходные привязки для Azure Cosmos DB.

> [!NOTE]
> Эта статья применима к службе [Функции Azure версии 2.x](functions-versions.md), которая доступна в предварительной версии.  Для получения дополнительных сведений об использовании Функций версии 1.x см. раздел [Функции 1.х](functions-bindings-cosmosdb.md).
>
> Эта привязка называлась DocumentDB. В Функциях версии 2.x триггер, привязки и пакет вместе называются Cosmos DB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Пакеты — Функции 2.x

Привязки Azure Cosmos DB для Функций версии 2.x доступны в пакете NuGet [Microsoft.Azure.WebJobs.Extensions.CosmosDB](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) версии 3.х. Исходный код для привязок находится в репозитории GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Триггер

Триггер Azure Cosmos DB использует [канал изменений Azure Cosmos DB](../cosmos-db/change-feed.md) для ожидания вставок и обновлений в разных разделах. На канале изменений публикуются вставки и обновления, а не удаления.

## <a name="trigger---example"></a>Пример триггера

Языковой пример см. в разделах:

* [C#](#trigger---c-example)
* [Скрипт C# (CSX)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

[Пропустить примеры триггеров](#trigger---attributes)

### <a name="trigger---c-example"></a>Пример C# в триггере

В следующем примере показана [функция C#](functions-dotnet-class-library.md), вызываемая при вставке или обновлении в указанной базе данных и коллекции.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;

namespace CosmosDBSamplesV2
{
    public static class CosmosTrigger
    {
        [FunctionName("CosmosTrigger")]
        public static void Run([CosmosDBTrigger(
            databaseName: "ToDoItems",
            collectionName: "Items",
            ConnectionStringSetting = "CosmosDBConnection",
            LeaseCollectionName = "leases",
            CreateLeaseCollectionIfNotExists = true)]IReadOnlyList<Document> documents, 
            TraceWriter log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.Info($"Documents modified: {documents.Count}");
                log.Info($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

[Пропустить примеры триггеров](#trigger---attributes)

### <a name="trigger---c-script-example"></a>Пример скрипта C# в триггере

В следующем примере показаны привязка триггера Cosmos DB в файле *function.json* и [функция сценария C#](functions-reference-csharp.md), которая использует эту привязку. При изменении записей в Cosmos DB функция записывает сообщения в журнале.

Данные привязки в файле *function.json*:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Ниже приведен код скрипта C#.
 
```cs 
    #r "Microsoft.Azure.Documents.Client"
    
    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    

    public static void Run(IReadOnlyList<Document> documents, TraceWriter log)
    {
      log.Verbose("Documents modified " + documents.Count);
      log.Verbose("First document Id " + documents[0].Id);
    }
```

[Пропустить примеры триггеров](#trigger---attributes)

### <a name="trigger---javascript-example"></a>Пример JavaScript в триггере

В следующем примере показаны привязка триггера Cosmos DB в файле *function.json* и [функция JavaScript](functions-reference-node.md), которая использует эту привязку. При изменении записей в Cosmos DB функция записывает сообщения в журнале.

Данные привязки в файле *function.json*:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Ниже показан код JavaScript.

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

## <a name="trigger---attributes"></a>Атрибуты триггера

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте атрибут [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs).

Конструктор атрибута принимает имя базы данных и имя коллекции. Дополнительные сведения о параметрах и других свойствах, которые можно настроить, см. в разделе [Конфигурация триггера](#trigger---configuration). Ниже приведен пример атрибута `CosmosDBTrigger` в сигнатуре метода:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        TraceWriter log)
    {
        ...
    }
```

Полный пример см. в разделе [Пример C# в триггере](#trigger---c-example).

## <a name="trigger---configuration"></a>Конфигурация триггера

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `CosmosDBTrigger`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**type** || Нужно задать значение `cosmosDBTrigger`. |
|**direction** || Нужно задать значение `in`. Этот параметр задается автоматически при создании триггера на портале Azure. |
|**name** || Имя переменной, используемое в коде функции, представляющей список документов с изменениями. | 
|**connectionStringSetting**|**ConnectionStringSetting** | Имя параметра приложения, содержащего строку подключения, используемую для подключения к отслеживаемой учетной записи Azure Cosmos DB. |
|**databaseName**|**DatabaseName**  | Имя базы данных Azure Cosmos DB с отслеживаемой коллекцией. |
|**collectionName** |**CollectionName** | Имя отслеживаемой коллекции. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (Необязательно.) Имя параметра приложения, содержащее строку подключения для службы, содержащей коллекцию аренды. Если значение не задано, используется значение `connectionStringSetting`. Этот параметр задается автоматически при создании привязки на портале. Строка подключения для коллекции аренд должна иметь разрешения на запись.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Необязательно.) Имя базы данных, в которой содержится коллекция, используемая для хранения аренд. Если значение не задано, используется значение параметра `databaseName`. Этот параметр задается автоматически при создании привязки на портале. |
|**leaseCollectionName** | **LeaseCollectionName** | (Необязательно.) Имя коллекции, используемой для хранения аренд. Если значение не задано, используется значение `leases`. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | (Необязательно.) Если задано значение `true`, коллекция аренд создается автоматически, если она не создана. По умолчанию используется значение `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| (Необязательно.) Определяет количество единиц запросов для назначения при создании коллекции аренд. Этот параметр используется, только если для `createLeaseCollectionIfNotExists` задано значение `true`. Этот параметр задается автоматически при создании привязки с помощью портала.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| (Дополнительно) Если параметр задан, то он добавляет префикс к созданной аренде в коллекции аренды для этой функции, позволяя двум разным функциям Azure совместно эффективно использовать коллекцию аренд с помощью различных префиксов.
|**feedPollDelay**| **FeedPollDelay**| (Дополнительно) Если параметр задан, то он определяет задержку между опросами секции на наличие новых изменений в веб-канале, после того как все текущие изменения будут утеряны (в миллисекундах). По умолчанию это 5000 (5 секунд).
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (Дополнительно) Если параметр задан, то он определяет интервал для запуска задачи вычисления при условии равномерности распределения секций между известными экземплярами узла (в миллисекундах). По умолчанию это 13000 (13 секунд).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| (Дополнительно) Если параметр задан, то он определяет интервал, за который берется аренда, представляющая секцию (в миллисекундах). Если аренда не будет обновлена в течение этого интервала, это приведет к ее истечению и владение секцией перейдет к другому экземпляру. По умолчанию это 60000 (60 секунд).
|**leaseRenewInterval**| **LeaseRenewInterval**| (Дополнительно) Если параметр задан, то он определяет интервал продления для всех аренд в разделах, которые на данный момент занятые экземплярами (в миллисекундах). По умолчанию это 17000 (17 секунд).
|**checkpointFrequency**| **CheckpointFrequency**| (Дополнительно) Если параметр задан, то он определяет интервал между контрольными точками аренды (в миллисекундах). После успешного вызова функции всегда используется значение по умолчанию.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| (Дополнительно) Если параметр задан, то он устанавливает максимально получаемое количество элементов за вызов функции.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Использование триггера

Триггеру требуется вторая коллекция, которая используется для хранения _аренды_ в разделах. Отслеживаемая коллекция и та, в которой содержатся аренды, должны быть доступны для успешной работы триггера.

>[!IMPORTANT]
> Если несколько функций настроены на использование триггера Cosmos DB для одной и той же коллекции, то каждая из функций должна использовать выделенную коллекцию аренды или указывать на другой префикс `LeaseCollectionPrefix` для каждой функции. В противном случае активируется только одна из функций. Сведения о префиксе см. в разделе с [конфигурацией](#trigger---configuration).

Триггер не указывает, был ли документ обновлен или вставлен. Вместо этого он представляет сам документ. Если операции обновления и вставки необходимо обрабатывать по-разному, это можно сделать, внедрив поля меток времени для операций обновления и вставки.

## <a name="input"></a>Входные данные

Входная привязка Azure Cosmos DB извлекает один или несколько документов из Azure Cosmos DB и передает их входному параметру функции. Идентификатор документа или параметры запроса можно определить по триггеру, который вызывает функцию. 

>[!NOTE]
> Если вы используете учетную запись Cosmos DB с API MongoDB, не применяйте входные и выходные привязки Azure Cosmos DB. Это может вызвать повреждение данных.

## <a name="input---examples"></a>Примеры входных данных

См. примеры (для разных языков), в которых для считывания одного документа указывается значение идентификатора:

* [C#](#input---c-examples)
* [Скрипт C# (CSX)](#input---c-script-examples)
* [JavaScript](#input---javascript-examples)
* [F#](#input---f-examples)

[Пропустить примеры входных данных](#input---attributes)

### <a name="input---c-examples"></a>Примеры входных данных C#

В этом разделе содержатся следующие примеры:

* [Триггер очереди, поисковый идентификатор из JSON](#queue-trigger-look-up-id-from-json-c)
* [Триггер HTTP, поисковый идентификатор из строки запроса](#http-trigger-look-up-id-from-query-string-c)
* [Триггер HTTP, поисковый идентификатор из данных маршрута](#http-trigger-look-up-id-from-route-data-c)
* [Триггер HTTP, поисковый идентификатор из данных маршрута, используется SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [Триггер HTTP, получение нескольких документов, используется SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [Триггер HTTP, получение нескольких документов, используется DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c)

В примерах используется `ToDoItem` простого типа:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

[Пропустить примеры входных данных](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-c"></a>Триггер очереди, поисковый идентификатор из JSON (c#)

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая получает один документ. Функция активируется сообщением из очереди, который содержит объект JSON. Очередь триггера анализирует JSON в объект с именем `ToDoItemLookup`, который содержит идентификатор для поиска. Этот идентификатор используется для получения `ToDoItem` документа из указанной базы данных и коллекции.

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItemLookup
    {
        public string ToDoItemId { get; set; }
    }
}
```

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromJSON
    {
        [FunctionName("DocByIdFromJSON")]
        public static void Run(
            [QueueTrigger("todoqueueforlookup")] ToDoItemLookup toDoItemLookup,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection", 
                Id = "{ToDoItemId}")]ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId}");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
        }
    }
}
```

[Пропустить примеры входных данных](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-c"></a>Триггер HTTP, поисковый идентификатор из строки запроса (C#)

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая получает один документ. Функция инициируется HTTP-запросом, который в строке запроса указывает идентификатор для поиска. Этот идентификатор используется для получения документа `ToDoItem` из указанной базы данных и коллекции.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromQueryString
    {
        [FunctionName("DocByIdFromQueryString")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection", 
                Id = "{Query.id}")] ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

[Пропустить примеры входных данных](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-c"></a>Триггер HTTP, поисковый идентификатор из данных маршрута (C#)

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая получает один документ. Функция инициируется HTTP-запросом, в котором данные маршрута используются, чтобы указать идентификатор для поиска. Этот идентификатор используется для получения документа `ToDoItem` из указанной базы данных и коллекции.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", 
                Route = "todoitems/{id}")]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection", 
                Id = "{id}")] ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

[Пропустить примеры входных данных](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>Триггер HTTP, поисковый идентификатор из данных маршрута, используется SqlQuery (C#)

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая получает один документ. Функция инициируется HTTP-запросом, в котором с помощью данных маршрута указывается идентификатор для поиска. Этот идентификатор используется для получения документа `ToDoItem` из указанной базы данных и коллекции. 

В примере показано, как использовать выражение привязки в параметре `SqlQuery`. Вы можете передать данные маршрута в параметр `SqlQuery`, как показано здесь, но сейчас [невозможно передать значения строки запроса](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583).


```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteDataUsingSqlQuery
    {
        [FunctionName("DocByIdFromRouteDataUsingSqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", 
                Route = "todoitems2/{id}")]HttpRequest req,
            [CosmosDB("ToDoItems", "Items", 
                ConnectionStringSetting = "CosmosDBConnection", 
                SqlQuery = "select * from ToDoItems r where r.id = {id}")]
                IEnumerable<ToDoItem> toDoItems,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}
```

[Пропустить примеры входных данных](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c"></a>Триггер HTTP, получение нескольких документов, используется SqlQuery (C#)

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая получает список документов. Функция активируется с помощью HTTP-запроса. Запрос указывается в свойстве атрибута `SqlQuery`.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;

namespace CosmosDBSamplesV2
{
    public static class DocsBySqlQuery
    {
        [FunctionName("DocsBySqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection", 
                SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")]
                IEnumerable<ToDoItem> toDoItems,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}

```

[Пропустить примеры входных данных](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>Триггер HTTP, получение нескольких документов, используется DocumentClient (C#)

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая получает список документов. Функция активируется с помощью HTTP-запроса. Код использует экземпляр `DocumentClient`, предоставленный привязкой Azure Cosmos DB для считывания списка документов. Экземпляр `DocumentClient` может также использоваться для операций записи.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace CosmosDBSamplesV2
{
    public static class DocsByUsingDocumentClient
    {
        [FunctionName("DocsByUsingDocumentClient")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", 
                Route = null)]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")] DocumentClient client,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            var searchterm = req.Query["searchterm"];
            if (string.IsNullOrWhiteSpace(searchterm))
            {
                return (ActionResult)new NotFoundResult();
            }

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");

            log.Info($"Searching for: {searchterm}");

            IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
                .Where(p => p.Description.Contains(searchterm))
                .AsDocumentQuery();

            while (query.HasMoreResults)
            {
                foreach (ToDoItem result in await query.ExecuteNextAsync())
                {
                    log.Info(result.Description);
                }
            }
            return new OkResult();
        }
    }
}
```

[Пропустить примеры входных данных](#input---attributes)

### <a name="input---c-script-examples"></a>Примеры входных данных скрипта C#

В этом разделе содержатся следующие примеры, в которых один документ считывается при указании значения идентификатора из разных источников:

* Триггер очереди, поисковый идентификатор из сообщения очереди
* Триггер очереди, поисковый идентификатор из сообщения очереди, используется SqlQuery

[Пропустить примеры входных данных](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-queue-message-c-script"></a>Триггер очереди, поисковый идентификатор из сообщения очереди (скрипт C#)

В следующем примере показаны входная привязка Cosmos DB в файле *function.json* и [функция сценария C#](functions-reference-csharp.md), которая использует эту привязку. Функция считывает один документ и обновляет текстовое значение в документе.

Данные привязки в файле *function.json*:

```json
{
    "name": "inputDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connectionStringSetting": "MyAccount_COSMOSDB",     
    "direction": "in"
}
```
В разделе [Конфигурация](#input---configuration) описываются эти свойства.

Ниже приведен код скрипта C#.

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding 
    public static void Run(string myQueueItem, dynamic inputDocument)
    {   
      inputDocument.text = "This has changed.";
    }
```

[Пропустить примеры входных данных](#input---attributes)

#### <a name="queue-trigger-look-up-id-queue-message-using-sqlquery-c-script"></a>Триггер очереди, поисковый идентификатор сообщения очереди, используется SqlQuery (скрипт C#)

В следующем примере показана входная привязка Azure Cosmos DB в файле *function.json* и [функция сценария C#](functions-reference-csharp.md), которая использует эту привязку. Функция извлекает несколько документов, указанных SQL-запросом, используя триггер очереди для настройки параметров запроса.

Триггер очереди предоставляет параметр `departmentId`. Сообщение из очереди `{ "departmentId" : "Finance" }` возвратит все записи для финансового отдела. 

Данные привязки в файле *function.json*:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

В разделе [Конфигурация](#input---configuration) описываются эти свойства.

Ниже приведен код скрипта C#.

```csharp
    public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
    {   
        foreach (var doc in documents)
        {
            // operate on each document
        }    
    }

    public class QueuePayload
    {
        public string departmentId { get; set; }
    }
```

[Пропустить примеры входных данных](#input---attributes)

### <a name="input---javascript-examples"></a>Примеры входных данных JavaScript

В этом разделе содержатся следующие примеры, в которых один документ считывается при указании значения идентификатора из разных источников:

* Триггер очереди, поисковый идентификатор из сообщения очереди
* Триггер очереди, поисковый идентификатор из сообщения очереди, используется SqlQuery

[Пропустить примеры входных данных](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-queue-message-javascript"></a>Триггер очереди, поисковый идентификатор из сообщения очереди (JavaScript)

В следующем примере показана входная привязка Cosmos DB в файле *function.json* и функция [JavaScript](functions-reference-node.md), которая использует привязку. Функция считывает один документ и обновляет текстовое значение в документе.

Данные привязки в файле *function.json*:

```json
{
    "name": "inputDocumentIn",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",     
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```
В разделе [Конфигурация](#input---configuration) описываются эти свойства.

Ниже показан код JavaScript.

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {   
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

[Пропустить примеры входных данных](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-queue-message-using-sqlquery-javascript"></a>Триггер очереди, поисковый идентификатор из сообщения очереди, используется (JavaScript)

В следующем примере показана входная привязка Azure Cosmos DB в файле *function.json* и [функция JavaScript](functions-reference-node.md), которая использует привязку. Функция извлекает несколько документов, указанных SQL-запросом, используя триггер очереди для настройки параметров запроса.

Триггер очереди предоставляет параметр `departmentId`. Сообщение из очереди `{ "departmentId" : "Finance" }` возвратит все записи для финансового отдела. 

Данные привязки в файле *function.json*:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

В разделе [Конфигурация](#input---configuration) описываются эти свойства.

Ниже показан код JavaScript.

```javascript
    module.exports = function (context, input) {    
        var documents = context.bindings.documents;
        for (var i = 0; i < documents.length; i++) {
            var document = documents[i];
            // operate on each document
        }       
        context.done();
    };
```

[Пропустить примеры входных данных](#input---attributes)

<a name="infsharp"></a>

### <a name="input---f-examples"></a>Примеры входных данных F#

В следующем примере показаны привязка Cosmos DB в файле *function.json* и [функция F#](functions-reference-fsharp.md), которая использует эту привязку. Функция считывает один документ и обновляет текстовое значение в документе.

Данные привязки в файле *function.json*:

```json
{
    "name": "inputDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "connectionStringSetting": "MyAccount_COSMOSDB",     
    "direction": "in"
}
```

В разделе [Конфигурация](#input---configuration) описываются эти свойства.

Ниже показан код F#.

```fsharp
    (* Change input document contents using Azure Cosmos DB input binding *)
    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, inputDocument: obj) =
    inputDocument?text <- "This has changed."
```

Для этого примера нужен файл `project.json`, указывающий зависимости NuGet `FSharp.Interop.Dynamic` и `Dynamitey`:

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

Чтобы добавить файл `project.json`, см. раздел [об управлении пакетом F#](functions-reference-fsharp.md#package).

## <a name="input---attributes"></a>Входные атрибуты

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте атрибут [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs).

Конструктор атрибута принимает имя базы данных и имя коллекции. Дополнительные сведения о параметрах и других свойствах, которые можно настроить, см. в следующем разделе о [настройке](#input---configuration). 

## <a name="input---configuration"></a>Входная конфигурация

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `CosmosDB`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**type**     || Нужно задать значение `cosmosDB`.        |
|**direction**     || Нужно задать значение `in`.         |
|**name**     || Имя параметра привязки, представляющего документ в функции.  |
|**databaseName** |**DatabaseName** |База данных, содержащая документ.        |
|**collectionName** |**CollectionName** | Имя коллекции, содержащей документ. |
|**id**    | **Id** | Идентификатор документа, который нужно получить. Это свойство поддерживает [выражения привязок](functions-triggers-bindings.md#binding-expressions-and-patterns). Не задавайте свойства **id** или **sqlQuery** одновременно. Если не задать ни одного из них, извлекается вся коллекция. |
|**sqlQuery**  |**SqlQuery**  | SQL-запрос к Azure Cosmos DB, используемый для извлечения нескольких документов. Свойство поддерживает привязки времени выполнения, как показано в примере: `SELECT * FROM c where c.departmentId = {departmentId}`. Не задавайте свойства **id** или **sqlQuery** одновременно. Если не задать ни одного из них, извлекается вся коллекция.|
|**connectionStringSetting**     |**ConnectionStringSetting**|Имя параметра приложения, содержащего строку подключения к Azure Cosmos DB.        |
|**partitionKey**|**PartitionKey**|Задает значение ключа секции для поиска. Может включать параметры привязки.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Использование входной привязки

В функциях C# и F# любые изменения, внесенные во входной документ посредством именованных входных параметров, сохраняются автоматически после успешного выхода из функции. 

В функциях JavaScript изменения не обрабатываются автоматически при выходе из функции. Для внесения изменений используйте `context.bindings.<documentName>In` и `context.bindings.<documentName>Out`. Ознакомьтесь с [примером на языке JavaScript](#input---javascript-example).

## <a name="output"></a>Выходные данные

Выходная привязка Azure Cosmos DB позволяет записать новый документ в базу данных Azure Cosmos DB. 

>[!NOTE]
> Если вы используете учетную запись Cosmos DB с API MongoDB, не применяйте входные и выходные привязки Azure Cosmos DB. Это может вызвать повреждение данных.

## <a name="output---example"></a>Пример выходных данных

Языковой пример см. в разделах:

* [C#](#output---c-examples)
* [Скрипт C# (CSX)](#output---c-script-examples)
* [JavaScript](#output---javascript-examples)
* [F#](#output---f-examples)

См. также [пример входных данных](#input---c-examples), в котором используется `DocumentClient`.

[Пропустить примеры вывода](#output---attributes)

### <a name="ouput---c-examples"></a>Примеры выходных данных C#

В этом разделе содержатся следующие примеры:

* Триггер очереди, запись одного документа
* Триггер очереди, запись документов при помощи IAsyncCollector

В примерах используется `ToDoItem` простого типа:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

[Пропустить примеры вывода](#output---attributes)

#### <a name="queue-trigger-write-one-doc-c"></a>Триггер очереди, запись одного документа (C#)

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая добавляет документ в базу данных, используя данные, полученные из сообщения хранилища очередей.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System;

namespace CosmosDBSamplesV2
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            TraceWriter log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.Info($"C# Queue trigger function inserted one row");
            log.Info($"Description={queueMessage}");
        }
    }
}
```

[Пропустить примеры вывода](#output---attributes)

#### <a name="queue-trigger-write-docs-using-iasynccollector-c"></a>Триггер очереди, запись документов с помощью IAsyncCollector (C#)

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая добавляет коллекцию документов в базу данных с помощью данных, полученных из JSON сообщения очереди.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;

namespace CosmosDBSamplesV2
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            TraceWriter log)
        {
            log.Info($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.Info($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

[Пропустить примеры вывода](#output---attributes)

### <a name="output---c-script-examples"></a>Примеры выходных данных скрипта C#

В следующем примере показаны выходная привязка Azure Cosmos DB в файле *function.json* и [функция сценария C#](functions-reference-csharp.md), которая использует эту привязку. Функция использует входную привязку очереди для очереди, которую получает JSON в следующем формате:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Функция создает документы Azure Cosmos DB для каждой записи в следующем формате:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Данные привязки в файле *function.json*:

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",     
    "direction": "out"
}
```

В разделе [Конфигурация](#output---configuration) описываются эти свойства.

Ниже приведен код скрипта C#.

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;

    public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
    {
      log.Info($"C# Queue trigger function processed: {myQueueItem}");

      dynamic employee = JObject.Parse(myQueueItem);

      employeeDocument = new {
        id = employee.name + "-" + employee.employeeId,
        name = employee.name,
        employeeId = employee.employeeId,
        address = employee.address
      };
    }
```

Для создания нескольких документов можно выполнить привязку к `ICollector<T>` или к `IAsyncCollector<T>`, где `T` — любой из поддерживаемых типов.

[Пропустить примеры вывода](#output---attributes)

### <a name="output---javascript-examples"></a>Примеры выходных данных JavaScript

В следующем примере показана выходная привязка Azure Cosmos DB в файле *function.json* и [функция JavaScript](functions-reference-node.md), которая использует эту привязку. Функция использует входную привязку очереди для очереди, которую получает JSON в следующем формате:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Функция создает документы Azure Cosmos DB для каждой записи в следующем формате:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Данные привязки в файле *function.json*:

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",     
    "direction": "out"
}
```

В разделе [Конфигурация](#output---configuration) описываются эти свойства.

Ниже показан код JavaScript.

```javascript
    module.exports = function (context) {

      context.bindings.employeeDocument = JSON.stringify({ 
        id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
        name: context.bindings.myQueueItem.name,
        employeeId: context.bindings.myQueueItem.employeeId,
        address: context.bindings.myQueueItem.address
      });

      context.done();
    };
```

[Пропустить примеры вывода](#output---attributes)

### <a name="output---f-examples"></a>Примеры выходных данных F#

В следующем примере показаны выходная привязка Azure Cosmos DB в файле *function.json* и [функция F#](functions-reference-fsharp.md), которая использует эту привязку. Функция использует входную привязку очереди для очереди, которую получает JSON в следующем формате:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Функция создает документы Azure Cosmos DB для каждой записи в следующем формате:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Данные привязки в файле *function.json*:

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",     
    "direction": "out"
}
```
В разделе [Конфигурация](#output---configuration) описываются эти свойства.

Ниже показан код F#.

```fsharp
    open FSharp.Interop.Dynamic
    open Newtonsoft.Json

    type Employee = {
      id: string
      name: string
      employeeId: string
      address: string
    }

    let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
      log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
      let employee = JObject.Parse(myQueueItem)
      employeeDocument <-
        { id = sprintf "%s-%s" employee?name employee?employeeId
          name = employee?name
          employeeId = employee?employeeId
          address = employee?address }
```

Для этого примера нужен файл `project.json`, указывающий зависимости NuGet `FSharp.Interop.Dynamic` и `Dynamitey`:

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

Чтобы добавить файл `project.json`, см. раздел [об управлении пакетом F#](functions-reference-fsharp.md#package).

## <a name="output---attributes"></a>Выходные атрибуты

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте атрибут [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/master/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs).

Конструктор атрибута принимает имя базы данных и имя коллекции. Дополнительные сведения о параметрах и других свойствах, которые можно настроить, см. в разделе [Выходная конфигурация](#output---configuration). Ниже приведен пример атрибута `CosmosDB` в сигнатуре метода:

```csharp
    [FunctionName("QueueToDocDB")]        
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

Полный пример см. в разделе [Пример выходных данных C#](#output---c-example).

## <a name="output---configuration"></a>Выходная конфигурация

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `CosmosDB`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**type**     || Нужно задать значение `cosmosDB`.        |
|**direction**     || Нужно задать значение `out`.         |
|**name**     || Имя параметра привязки, представляющего документ в функции.  |
|**databaseName** | **DatabaseName**|База данных, содержащая коллекцию, в которой создается документ.     |
|**collectionName** |**CollectionName**  | Имя коллекции, в которой создается документ. |
|**createIfNotExists**  |**CreateIfNotExists**    | Логическое значение, указывающее, будет ли создана коллекция при ее отсутствии. Значение по умолчанию — *false*, так как коллекции создаются с использованием зарезервированной пропускной способности, с которой связаны ценовые требования. Дополнительные сведения см. на [странице с расценками](https://azure.microsoft.com/pricing/details/cosmos-db/).  |
|**partitionKey**|**PartitionKey** |Если для `CreateIfNotExists` задано значение true, определяется путь к ключу раздела для созданной коллекции.|
|**CollectionThroughput**|**CollectionThroughput**| Если для `CreateIfNotExists` задано значение true, определяется [пропускная способность](../cosmos-db/set-throughput.md) созданной коллекции.|
|**connectionStringSetting**    |**ConnectionStringSetting** |Имя параметра приложения, содержащего строку подключения к Azure Cosmos DB.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Использование выходной привязки

При записи в параметре вывода функции в базе данных по умолчанию создается документ, для которого автоматически создается GUID в качестве идентификатора документа. Идентификатор выходного документа можно определить, указав свойство `id` в объекте JSON, передаваемом в параметр вывода. 

> [!Note]  
> Если указать идентификатор существующего документа, он перезаписывается новым выходным документом. 

## <a name="exceptions-and-return-codes"></a>Исключения и коды возврата

| Привязка | Справочные материалы |
|---|---|
| Cosmos DB | [Коды ошибок Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Создание функции, активируемой с помощью Azure Cosmos DB](functions-create-cosmos-db-triggered-function.md)

> [!div class="nextstepaction"]
> [Azure Cosmos DB: обработка данных бессерверных баз данных с помощью службы "Функции Azure"](..\cosmos-db\serverless-computing-database.md)

> [!div class="nextstepaction"]
> [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)
