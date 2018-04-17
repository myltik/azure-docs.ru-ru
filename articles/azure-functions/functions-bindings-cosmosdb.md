---
title: Привязки Azure Cosmos DB для службы "Функции"
description: Узнайте, как использовать триггеры и привязки Azure Cosmos DB в службе "Функции Azure".
services: functions
documentationcenter: na
author: ggailey777
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
ms.author: glenga
ms.openlocfilehash: ac869cc45d352bdeed16bb3ca926ec7a921d1f75
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
# <a name="azure-cosmos-db-bindings-for-azure-functions"></a>Привязки Azure Cosmos DB для службы "Функции Azure"

В этой статье описывается использование привязок [Azure Cosmos DB](..\cosmos-db\serverless-computing-database.md) в службе "Функции Azure". Служба "Функции Azure" поддерживает привязки триггера, а также входные и выходные привязки для Azure Cosmos DB.

> [!NOTE]
> Эта привязка называлась DocumentDB. В Функциях версии 1.x только триггер был переименован в Cosmos DB. Входная и выходная привязка, а также пакет NuGet сохраняют имя DocumentDB. В [Функциях версии 2.x](functions-versions.md) привязки и пакет также были переименованы в Cosmos DB. В этой статье используются имена для версии 1.х.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>Пакеты

Привязки Cosmos DB для Функций версии 1.x доступны в пакете NuGet [Microsoft.Azure.WebJobs.Extensions.DocumentDB](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB). Для Функций версии 2.x используйте пакет [Microsoft.Azure.WebJobs.Extensions.CosmosDB](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB). Исходный код для привязок находится в репозитории GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/).

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="trigger"></a>Триггер

Триггер Azure Cosmos DB использует [канал изменений Azure Cosmos DB](../cosmos-db/change-feed.md) для ожидания передачи данных изменений между разделами. На канале изменений публикуются вставки и обновления, а не удаления. 

## <a name="trigger---example"></a>Пример триггера

Языковой пример см. в разделах:

* [C#](#trigger---c-example)
* [Скрипт C# (CSX)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Пример C# в триггере

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая запускается из заданной базы данных и коллекции.

```cs
    using System.Collections.Generic;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;

    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        TraceWriter log)
    {
            log.Info("Documents modified " + documents.Count);
            log.Info("First document Id " + documents[0].Id);
    }
```

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
| |**LeaseOptions** | Настройте параметры аренды, задав свойства в экземпляре класса [ChangeFeedHostOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.changefeedprocessor.changefeedhostoptions).

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Использование триггера

Триггеру требуется вторая коллекция, которая используется для хранения _аренды_ в разделах. Отслеживаемая коллекция и та, в которой содержатся аренды, должны быть доступны для успешной работы триггера.

 >[!IMPORTANT]
 > Если несколько функций настроены на использование триггера Cosmos DB для одной и той же коллекции, то каждая из функций должна использовать выделенную коллекцию аренды. В противном случае активируется только одна из функций. 

Триггер не указывает, был ли документ обновлен или вставлен. Вместо этого он представляет сам документ. Если операции обновления и вставки необходимо обрабатывать по-разному, это можно сделать, внедрив поля меток времени для операций обновления и вставки.

## <a name="input"></a>Входные данные

Входная привязка Azure Cosmos DB извлекает один или несколько документов из Azure Cosmos DB и передает их входному параметру функции. Идентификатор документа или параметры запроса можно определить по триггеру, который вызывает функцию. 

>[!NOTE]
> Если вы используете учетную запись Cosmos DB с API MongoDB, не применяйте входные и выходные привязки Azure Cosmos DB. Это может вызвать повреждение данных.

## <a name="input---example-1"></a>Пример входных данных 1

Ознакомьтесь с примером конкретного языка, считывающим один документ:

* [C#](#input---c-example)
* [Скрипт C# (CSX)](#input---c-script-example)
* [F#](#input---f-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-example"></a>Пример входных данных C#

В следующем примере показана [функция C#](functions-dotnet-class-library.md), при помощи которой извлекается один документ из заданной базы данных и коллекции. 

Сначала значения `Id` и `Maker` для экземпляра `CarReview` передаются в очередь. В привязке Cosmos DB используются `Id` и `Maker` из сообщения очереди, чтобы извлечь документ из базы данных.

```cs
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;

    namespace CosmosDB
    {
        public static class SingleEntry
        {
            [FunctionName("SingleEntry")]
            public static void Run(
                [QueueTrigger("car-reviews", Connection = "StorageConnectionString")] CarReview carReview,
                [DocumentDB("cars", "car-reviews", PartitionKey = "{maker}", Id= "{id}", ConnectionStringSetting = "CarReviewsConnectionString")] CarReview document,
                TraceWriter log)
            {
                log.Info( $"Selected Review - {document?.Review}"); 
            }
        }
    }
```

Ниже показаны библиотеки POCO `CarReview`:

 ```cs
    public class CarReview
    {
        public string Id { get; set; }
        public string Maker { get; set; }
        public string Description { get; set; }
        public string Model { get; set; }
        public string Image { get; set; }
        public string Review { get; set; }
    }
 ```

### <a name="input---c-script-example"></a>Пример входных данных сценария C#

В следующем примере показаны входная привязка Cosmos DB в файле *function.json* и [функция сценария C#](functions-reference-csharp.md), которая использует эту привязку. Функция считывает один документ и обновляет текстовое значение в документе.

Данные привязки в файле *function.json*:

```json
{
    "name": "inputDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connection": "MyAccount_COSMOSDB",     
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

<a name="infsharp"></a>

### <a name="input---f-example"></a>Пример входных данных F#

В следующем примере показаны привязка Cosmos DB в файле *function.json* и [функция F#](functions-reference-fsharp.md), которая использует эту привязку. Функция считывает один документ и обновляет текстовое значение в документе.

Данные привязки в файле *function.json*:

```json
{
    "name": "inputDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "connection": "MyAccount_COSMOSDB",     
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

### <a name="input---javascript-example"></a>Пример входных данных JavaScript

В следующем примере показана входная привязка Cosmos DB в файле *function.json* и функция [JavaScript](functions-reference-node.md), которая использует привязку. Функция считывает один документ и обновляет текстовое значение в документе.

Данные привязки в файле *function.json*:

```json
{
    "name": "inputDocumentIn",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",     
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",
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

## <a name="input---example-2"></a>Пример входных данных 2

Ознакомьтесь с примером конкретного языка, считывающим несколько документов:

* [C#](#input---c-example-2)
* [Скрипт C# (CSX)](#input---c-script-example-2)
* [JavaScript](#input---javascript-example-2)

### <a name="input---c-example-2"></a>Пример 2 входных данных C#

В следующем примере показано, как с помощью [функции C#](functions-dotnet-class-library.md) выполнить SQL-запрос. Чтобы использовать параметр `SqlQuery`, необходимо установить последнюю бета-версию пакета NuGet `Microsoft.Azure.WebJobs.Extensions.DocumentDB`.

```csharp
    using System.Net;
    using System.Net.Http;
    using System.Collections.Generic;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Extensions.Http;

    [FunctionName("CosmosDBSample")]
    public static HttpResponseMessage Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get")] HttpRequestMessage req,
        [DocumentDB("test", "test", ConnectionStringSetting = "CosmosDB", SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")] IEnumerable<object> documents)
    {
        return req.CreateResponse(HttpStatusCode.OK, documents);
    }
```

### <a name="input---c-script-example-2"></a>Пример 2 входных данных скрипта C#

В следующем примере показана входная привязка Azure Cosmos DB в файле *function.json* и [функция сценария C#](functions-reference-csharp.md), которая использует эту привязку. Функция извлекает несколько документов, указанных SQL-запросом, используя триггер очереди для настройки параметров запроса.

Триггер очереди предоставляет параметр `departmentId`. Сообщение из очереди `{ "departmentId" : "Finance" }` возвратит все записи для финансового отдела. 

Данные привязки в файле *function.json*:

```json
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
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

### <a name="input---javascript-example-2"></a>Пример входных данных JavaScript 2

В следующем примере показана входная привязка Azure Cosmos DB в файле *function.json* и [функция JavaScript](functions-reference-node.md), которая использует привязку. Функция извлекает несколько документов, указанных SQL-запросом, используя триггер очереди для настройки параметров запроса.

Триггер очереди предоставляет параметр `departmentId`. Сообщение из очереди `{ "departmentId" : "Finance" }` возвратит все записи для финансового отдела. 

Данные привязки в файле *function.json*:

```json
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
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

## <a name="input---attributes"></a>Входные атрибуты

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте атрибут [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs).

Конструктор атрибута принимает имя базы данных и имя коллекции. Дополнительные сведения о параметрах и других свойствах, которые можно настроить, см. в следующем разделе о [настройке](#input---configuration). 

## <a name="input---configuration"></a>Входная конфигурация

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `DocumentDB`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**type**     || Нужно задать значение `documentdb`.        |
|**direction**     || Нужно задать значение `in`.         |
|**name**     || Имя параметра привязки, представляющего документ в функции.  |
|**databaseName** |**DatabaseName** |База данных, содержащая документ.        |
|**collectionName** |**CollectionName** | Имя коллекции, содержащей документ. |
|**id**    | **Id** | Идентификатор документа, который нужно получить. Это свойство поддерживает [выражения привязок](functions-triggers-bindings.md#binding-expressions-and-patterns). Не задавайте свойства **id** или **sqlQuery** одновременно. Если не задать ни одного из них, извлекается вся коллекция. |
|**sqlQuery**  |**SqlQuery**  | SQL-запрос к Azure Cosmos DB, используемый для извлечения нескольких документов. Свойство поддерживает привязки времени выполнения, как показано в примере: `SELECT * FROM c where c.departmentId = {departmentId}`. Не задавайте свойства **id** или **sqlQuery** одновременно. Если не задать ни одного из них, извлекается вся коллекция.|
|**подключение**     |**ConnectionStringSetting**|Имя параметра приложения, содержащего строку подключения к Azure Cosmos DB.        |
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

* [C#](#output---c-example)
* [Скрипт C# (CSX)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Пример выходных данных C#

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая добавляет документ в базу данных, используя данные, полученные из сообщения хранилища очередей.

```cs
    using System;
    using Microsoft.Azure.WebJobs;

    [FunctionName("QueueToDocDB")]        
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        document = new { Text = myQueueItem, id = Guid.NewGuid() };
    }
```

### <a name="output---c-script-example"></a>Пример выходных данных скрипта C#

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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",     
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

### <a name="output---f-example"></a>Пример выходных данных F#

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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",     
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

### <a name="output---javascript-example"></a>Пример выходных данных JavaScript

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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",     
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

## <a name="output---attributes"></a>Выходные атрибуты

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте атрибут [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs).

Конструктор атрибута принимает имя базы данных и имя коллекции. Дополнительные сведения о параметрах и других свойствах, которые можно настроить, см. в разделе [Выходная конфигурация](#output---configuration). Ниже приведен пример атрибута `DocumentDB` в сигнатуре метода:

```csharp
    [FunctionName("QueueToDocDB")]        
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

Полный пример см. в разделе [Пример выходных данных C#](#output---c-example).

## <a name="output---configuration"></a>Выходная конфигурация

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `DocumentDB`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**type**     || Нужно задать значение `documentdb`.        |
|**direction**     || Нужно задать значение `out`.         |
|**name**     || Имя параметра привязки, представляющего документ в функции.  |
|**databaseName** | **DatabaseName**|База данных, содержащая коллекцию, в которой создается документ.     |
|**collectionName** |**CollectionName**  | Имя коллекции, в которой создается документ. |
|**createIfNotExists**  |**CreateIfNotExists**    | Логическое значение, указывающее, будет ли создана коллекция при ее отсутствии. Значение по умолчанию — *false*, так как коллекции создаются с использованием зарезервированной пропускной способности, с которой связаны ценовые требования. Дополнительные сведения см. на [странице с расценками](https://azure.microsoft.com/pricing/details/documentdb/).  |
|**partitionKey**|**PartitionKey** |Если для `CreateIfNotExists` задано значение true, определяется путь к ключу раздела для созданной коллекции.|
|**CollectionThroughput**|**CollectionThroughput**| Если для `CreateIfNotExists` задано значение true, определяется [пропускная способность](../cosmos-db/set-throughput.md) созданной коллекции.|
|**подключение**    |**ConnectionStringSetting** |Имя параметра приложения, содержащего строку подключения к Azure Cosmos DB.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Использование выходной привязки

При записи в параметре вывода функции в базе данных по умолчанию создается документ, для которого автоматически создается GUID в качестве идентификатора документа. Идентификатор выходного документа можно определить, указав свойство `id` в объекте JSON, передаваемом в параметр вывода. 

> [!Note]  
> Если указать идентификатор существующего документа, он перезаписывается новым выходным документом. 

## <a name="exceptions-and-return-codes"></a>Исключения и коды возврата

| Привязка | Справочные материалы |
|---|---|
| Cosmos DB | [Коды ошибок Cosmos DB](https://docs.microsoft.com/en-us/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Создание функции, активируемой с помощью Azure Cosmos DB](functions-create-cosmos-db-triggered-function.md)

> [!div class="nextstepaction"]
> [Azure Cosmos DB: обработка данных бессерверных баз данных с помощью службы "Функции Azure"](..\cosmos-db\serverless-computing-database.md)

> [!div class="nextstepaction"]
> [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)
