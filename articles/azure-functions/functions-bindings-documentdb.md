---
title: "Привязки Cosmos DB в Функциях Azure | Документация Майкрософт"
description: "Узнайте, как использовать привязки Azure Cosmos DB в Функциях Azure."
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "функции azure, функции, обработка событий, динамические вычисления, независимая архитектура"
ms.assetid: 3d8497f0-21f3-437d-ba24-5ece8c90ac85
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/18/2016
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 2c0cb8ee1690f9b36b76c87247e3c7223876b269
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017

---
# <a name="azure-functions-cosmos-db-bindings"></a>Привязки Cosmos DB в Функциях Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

В этой статье объясняется, как настроить и запрограммировать привязки Azure Cosmos DB в Функциях Azure. Функции Azure поддерживают входные и выходные привязки для Cosmos DB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Дополнительные сведения о Cosmos DB см. в статьях [Знакомство с DocumentDB: база данных NoSQL JSON](../documentdb/documentdb-introduction.md) и [Руководство по NoSQL. Создание консольного приложения DocumentDB на языке C#](../documentdb/documentdb-get-started.md).

<a id="docdbinput"></a>

## <a name="documentdb-api-input-binding"></a>Входная привязка API DocumentDB
Входная привязка API DocumentDB получает документ Cosmos DB и передает его именованному входному параметру функции. Идентификатор документа можно определить по триггеру, который вызывает функцию. 

Входная привязка API DocumentDB имеет следующие свойства в *function.json*:

- `name` — имя идентификатора, используемого в коде функции для документа.
- `type` — для этого свойства нужно задать значение "documentdb".
- `databaseName` — база данных, содержащая документ.
- `collectionName` — коллекция, содержащая документ.
- `id` — идентификатор документа, который нужно получить. Это свойство поддерживает параметры привязки; см. раздел [Привязка к пользовательским входным свойствам в выражении привязки](functions-triggers-bindings.md#bind-to-custom-input-properties-in-a-binding-expression) статьи [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md).
- `sqlQuery` — SQL-запрос к Cosmos DB, используемый для извлечения нескольких документов. Запрос поддерживает привязки времени выполнения. Например: `SELECT * FROM c where c.departmentId = {departmentId}`
- `connection` — имя параметра приложения, содержащего строку подключения к Cosmos DB.
- `direction` — для этого свойства нужно задать значение `"in"`.

Свойства `id` и `sqlQuery` невозможно указать одновременно. Если не задано ни `id`, ни `sqlQuery`, извлекается вся коллекция.

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
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}"
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

- `name` — идентификатор, используемый в коде функции для нового документа.
- `type` — для этого свойства нужно задать значение `"documentdb"`.
- `databaseName` — база данных, содержащая коллекцию, в которой будет создан документ.
- `collectionName` — коллекция, в которой будет создан документ.
- `createIfNotExists` — логическое значение, указывающее, будет ли создана коллекция при ее отсутствии. Значение по умолчанию — *false*. Это вызвано тем, что коллекции создаются с использованием зарезервированной пропускной способности, с которой связаны ценовые требования. Дополнительные сведения см. на [странице цен](https://azure.microsoft.com/pricing/details/documentdb/).
- `connection` — имя параметра приложения, содержащего строку подключения к Cosmos DB.
- `direction` — для этого свойства нужно задать значение `"out"`.

## <a name="using-a-documentdb-api-output-binding"></a>Использование выходной привязки API DocumentDB
В этом разделе показано, как использовать выходную привязку API DocumentDB в коде функции.

При записи в выходном параметре функции в базе данных по умолчанию создается документ, для которого автоматически создается GUID в качестве идентификатора документа. Идентификатор выходного документа можно определить, указав свойство JSON `id` в выходном параметре. 

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

При этом вам нужно создать документы Cosmos DB в следующем формате для каждой записи:

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

