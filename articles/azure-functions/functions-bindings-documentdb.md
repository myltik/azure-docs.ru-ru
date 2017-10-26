---
title: "Привязки Azure Cosmos DB для службы \"Функции\" | Документация Майкрософт"
description: "Узнайте, как использовать триггеры и привязки Azure Cosmos DB в службе \"Функции Azure\"."
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "функции azure, функции, обработка событий, динамические вычисления, независимая архитектура"
ms.assetid: 3d8497f0-21f3-437d-ba24-5ece8c90ac85
ms.service: functions; cosmos-db
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/19/2017
ms.author: glenga
ms.openlocfilehash: d05c0342e771e229a7175570ad227c4359980990
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2017
---
# <a name="azure-cosmos-db-bindings-for-functions"></a>Привязки Azure Cosmos DB для службы "Функции"
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

В этой статье объясняется, как настроить и запрограммировать привязки Azure Cosmos DB в Функциях Azure. Служба "Функции" поддерживает привязки триггера, а также входные и выходные привязки для Azure Cosmos DB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Дополнительные сведения о бессерверных вычислениях с помощью Azure Cosmos DB см. в разделе [Azure Cosmos DB: обработка данных бессерверных баз данных с помощью службы "Функции Azure"](..\cosmos-db\serverless-computing-database.md).

<a id="trigger"></a>
<a id="cosmosdbtrigger"></a>

## <a name="azure-cosmos-db-trigger"></a>Триггер Azure Cosmos DB

Триггер Azure Cosmos DB использует [канал изменений Azure Cosmos DB](../cosmos-db/change-feed.md) для ожидания передачи данных изменений между разделами. Триггеру требуется вторая коллекция, которая используется для хранения _аренды_ в разделах.

Отслеживаемая коллекция и та, в которой содержатся аренды, должны быть доступны для успешной работы триггера.

Триггер Azure Cosmos DB поддерживает следующие свойства:

|Свойство  |Описание  |
|---------|---------|
|**type** | Нужно задать значение `cosmosDBTrigger`. |
|**name** | Имя переменной, используемое в коде функции, представляющей список документов с изменениями. | 
|**direction** | Нужно задать значение `in`. Этот параметр задается автоматически при создании триггера на портале Azure. |
|**connectionStringSetting** | Имя параметра приложения, содержащего строку подключения, используемую для подключения к отслеживаемой учетной записи Azure Cosmos DB. |
|**databaseName** | Имя базы данных Azure Cosmos DB с отслеживаемой коллекцией. |
|**collectionName** | Имя отслеживаемой коллекции. |
| **leaseConnectionStringSetting** | (Необязательно.) Имя параметра приложения, содержащее строку подключения для службы, содержащей коллекцию аренды. Если значение не задано, используется значение `connectionStringSetting`. Этот параметр задается автоматически при создании привязки на портале. |
| **leaseDatabaseName** | (Необязательно.) Имя базы данных, в которой содержится коллекция, используемая для хранения аренд. Если значение не задано, используется значение параметра `databaseName`. Этот параметр задается автоматически при создании привязки на портале. |
| **leaseCollectionName** | (Необязательно.) Имя коллекции, используемой для хранения аренд. Если значение не задано, используется значение `leases`. |
| **createLeaseCollectionIfNotExists** | (Необязательно.) Если задано значение `true`, коллекция аренд создается автоматически, если она не создана. По умолчанию используется значение `false`. |
| **leaseCollectionThroughput** | (Необязательно.) Определяет количество единиц запросов для назначения при создании коллекции аренд. Этот параметр используется, только если для `createLeaseCollectionIfNotExists` задано значение `true`. Этот параметр задается автоматически при создании привязки с помощью портала.

>[!NOTE] 
>Строка подключения, используемая для подключения к коллекции аренд, должна иметь разрешения на запись.

Эти свойства можно задать на вкладке "Интеграция" для функции на портале Azure или путем изменения файла проекта `function.json`.

## <a name="using-an-azure-cosmos-db-trigger"></a>Использование триггера Azure Cosmos DB

В этом разделе содержатся примеры использования триггера Azure Cosmos DB. В этих примерах требуются примерно такие метаданные триггера:

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
 
Пример того, как создать триггер Azure Cosmos DB из приложения-функции на портале, см. в статье [Создание функции, активируемой с помощью Azure Cosmos DB](functions-create-cosmos-db-triggered-function.md). 

### <a name="trigger-sample-in-c"></a>Пример триггера на языке C# #
```cs 
    #r "Microsoft.Azure.Documents.Client"
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    using System;
    public static void Run(IReadOnlyList<Document> documents, TraceWriter log)
    {
        log.Verbose("Documents modified " + documents.Count);
        log.Verbose("First document Id " + documents[0].Id);
    }
```


### <a name="trigger-sample-in-javascript"></a>Пример триггера для JavaScript
```javascript
    module.exports = function (context, documents) {
        context.log('First document Id modified : ', documents[0].id);

        context.done();
    }
```

<a id="docdbinput"></a>

## <a name="documentdb-api-input-binding"></a>Входная привязка API DocumentDB
Входная привязка API DocumentDB получает документ Azure Cosmos DB и передает его именованному входному параметру функции. Идентификатор документа можно определить по триггеру, который вызывает функцию. 

Входная привязка API DocumentDB имеет следующие свойства в *function.json*:

|Свойство  |Описание  |
|---------|---------|
|**name**     | Имя параметра привязки, представляющего документ в функции.  |
|**type**     | Нужно задать значение `documentdb`.        |
|**databaseName** | База данных, содержащая документ.        |
|**collectionName**  | Имя коллекции, содержащей документ. |
|**id**     | Идентификатор документа, который нужно получить. Это свойство поддерживает параметры привязок. Дополнительные сведения см. в разделе [Привязка к пользовательским входным свойствам в выражении привязки](functions-triggers-bindings.md#bind-to-custom-input-properties-in-a-binding-expression). |
|**sqlQuery**     | SQL-запрос к Azure Cosmos DB, используемый для извлечения нескольких документов. Запрос поддерживает привязки времени выполнения, например `SELECT * FROM c where c.departmentId = {departmentId}`.        |
|**подключение**     |Имя параметра приложения, содержащего строку подключения к Azure Cosmos DB.        |
|**direction**     | Нужно задать значение `in`.         |

Невозможно задать одновременно свойства **id** и **sqlQuery**. Если ни одно свойство не задано, извлекается вся коллекция.

## <a name="using-a-documentdb-api-input-binding"></a>Использование входной привязки API DocumentDB

* В функциях C# и F# любые изменения, внесенные во входной документ посредством именованных входных параметров, сохраняются автоматически после успешного выхода из функции. 
* В функциях JavaScript изменения не обрабатываются автоматически при выходе из функции. Для внесения изменений используйте `context.bindings.<documentName>In` и `context.bindings.<documentName>Out`. См. [пример JavaScript](#injavascript).

<a name="inputsample"></a>

## <a name="input-sample-for-single-document"></a>Пример входной привязки для отдельного документа
Предположим, что у вас есть следующая входная привязка API DocumentDB в массиве `bindings` файла function.json:

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

Чтобы обновить текстовое значение документа, см. пример для конкретного языка, использующий эту входную привязку.

* [C#](#incsharp)
* [F#](#infsharp)
* [JavaScript](#injavascript)

<a name="incsharp"></a>
### <a name="input-sample-in-c"></a>Пример входной привязки для языка C# #

```cs
// Change input document contents using DocumentDB API input binding 
public static void Run(string myQueueItem, dynamic inputDocument)
{   
  inputDocument.text = "This has changed.";
}
```
<a name="infsharp"></a>

### <a name="input-sample-in-f"></a>Пример входной привязки для языка F# #

```fsharp
(* Change input document contents using DocumentDB API input binding *)
open FSharp.Interop.Dynamic
let Run(myQueueItem: string, inputDocument: obj) =
  inputDocument?text <- "This has changed."
```

В этом примере нужен файл `project.json`, указывающий зависимости NuGet `FSharp.Interop.Dynamic` и `Dynamitey`:

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

<a name="injavascript"></a>

### <a name="input-sample-in-javascript"></a>Пример входной привязки для JavaScript

```javascript
// Change input document contents using DocumentDB API input binding, using context.bindings.inputDocumentOut
module.exports = function (context) {   
  context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
  context.bindings.inputDocumentOut.text = "This was updated!";
  context.done();
};
```

## <a name="input-sample-with-multiple-documents"></a>Пример входной привязки с несколькими документами

Предположим, что нужно извлечь несколько документов, указанных SQL-запросом, используя триггер очереди для настройки параметров запроса. 

В этом примере триггер очереди предоставляет параметр `departmentId`. Сообщение очереди `{ "departmentId" : "Finance" }` возвращает все записи для финансового отдела. Используйте в *function.json* следующий код:

```
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

### <a name="input-sample-with-multiple-documents-in-c"></a>Пример входной привязки с несколькими документами для языка C#

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

### <a name="input-sample-with-multiple-documents-in-javascript"></a>Пример входной привязки с несколькими документами для JavaScript

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

## <a id="docdboutput"></a>Выходная привязка API DocumentDB
Выходная привязка API DocumentDB позволяет записать новый документ в Azure Cosmos DB. Он содержит следующие свойства в *function.json*:

|Свойство  |Описание  |
|---------|---------|
|**name**     | Имя параметра привязки, представляющего документ в функции.  |
|**type**     | Нужно задать значение `documentdb`.        |
|**databaseName** | База данных, содержащая коллекцию, в которой создается документ.     |
|**collectionName**  | Имя коллекции, в которой создается документ. |
|**createIfNotExists**     | Логическое значение, указывающее, будет ли создана коллекция при ее отсутствии. Значение по умолчанию — *false*. Это вызвано тем, что коллекции создаются с использованием зарезервированной пропускной способности, с которой связаны ценовые требования. Дополнительные сведения см. на [странице цен](https://azure.microsoft.com/pricing/details/documentdb/).  |
|**подключение**     |Имя параметра приложения, содержащего строку подключения к Azure Cosmos DB.        |
|**direction**     | Нужно задать значение `out`.         |

## <a name="using-a-documentdb-api-output-binding"></a>Использование выходной привязки API DocumentDB
В этом разделе показано, как использовать выходную привязку API DocumentDB в коде функции.

При записи в параметре вывода функции в базе данных по умолчанию создается документ, для которого автоматически создается GUID в качестве идентификатора документа. Идентификатор выходного документа можно определить, указав свойство `id` в объекте JSON, передаваемом в параметр вывода. 

>[!Note]  
>Если указать идентификатор существующего документа, он перезаписывается новым выходным документом. 

Для вывода в несколько документов можно также выполнить привязку к `ICollector<T>` или `IAsyncCollector<T>`, где `T` — любой из поддерживаемых типов.

<a name="outputsample"></a>

## <a name="documentdb-api-output-binding-sample"></a>Пример выходной привязки API DocumentDB
Предположим, что у вас есть следующая выходная привязка API DocumentDB в массиве `bindings` файла function.json:

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

Кроме того, у вас есть входная привязка очереди для очереди, которая получает JSON в следующем формате:

```json
{
  "name": "John Henry",
  "employeeId": "123456",
  "address": "A town nearby"
}
```

При этом вам нужно создать документы Azure Cosmos DB в следующем формате для каждой записи:

```json
{
  "id": "John Henry-123456",
  "name": "John Henry",
  "employeeId": "123456",
  "address": "A town nearby"
}
```

Чтобы добавить документы в базу данных, см. пример для конкретного языка, использующий эту выходную привязку.

* [C#](#outcsharp)
* [F#](#outfsharp)
* [JavaScript](#outjavascript)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Пример выходной привязки для языка C# #

```cs
#r "Newtonsoft.Json"

using System;
using Newtonsoft.Json;
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

<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>Пример выходной привязки для языка F# #

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

В этом примере нужен файл `project.json`, указывающий зависимости NuGet `FSharp.Interop.Dynamic` и `Dynamitey`:

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

<a name="outjavascript"></a>

### <a name="output-sample-in-javascript"></a>Пример выходной привязки для JavaScript

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
