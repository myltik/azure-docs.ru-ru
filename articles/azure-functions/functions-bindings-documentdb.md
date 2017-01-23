---
title: "Привязки DocumentDB в функциях Azure | Документация Майкрософт"
description: "Узнайте, как использовать привязки Azure DocumentDB в функциях Azure."
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
ms.date: 11/10/2016
ms.author: chrande; glenga
translationtype: Human Translation
ms.sourcegitcommit: c9e736f7ce5330823f3890c669da40e2bb1ecf43
ms.openlocfilehash: 13b69118c6732ed872bec11e880737db3b8fa3c5


---
# <a name="azure-functions-documentdb-bindings"></a>Привязки Azure DocumentDB в функциях Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Эта статья объясняет, как настроить и запрограммировать привязки Azure DocumentDB в функциях Azure. Функции Azure поддерживают входные и выходные привязки для DocumentDB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Дополнительные сведения о DocumentDB см. в статьях [Знакомство с DocumentDB: база данных NoSQL JSON](../documentdb/documentdb-introduction.md) и [Руководство по NoSQL. Создание консольного приложения DocumentDB на языке C](../documentdb/documentdb-get-started.md).

<a id="docdbinput"></a>

## <a name="documentdb-input-binding"></a>Входная привязка DocumentDB
Входная привязка DocumentDB получает документ DocumentDB и передает его именованному входному параметру функции. Идентификатор документа можно определить по триггеру, который вызывает функцию. 

Входные данные DocumentDB для функции используют следующий объект JSON в массиве `bindings` файла function.json:

```json
{
  "name": "<Name of input parameter in function signature>",
  "type": "documentDB",
  "databaseName": "<Name of the DocumentDB database>",
  "collectionName": "<Name of the DocumentDB collection>",
  "id": "<Id of the DocumentDB document - see below>",
  "connection": "<Name of app setting with connection string - see below>",
  "direction": "in"
},
```

Обратите внимание на следующее.

* Свойство `id` поддерживает привязки, аналогичные `{queueTrigger}`, в которых в качестве идентификатора документа используется строковое значение сообщения очереди.
* Значение свойства `connection` должно соответствовать имени параметра приложения, указывающего на конечную точку учетной записи DocumentDB (со значением `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>`). При создании учетной записи DocumentDB в пользовательском интерфейсе портала Функций автоматически создается параметр приложения. Чтобы использовать существующую учетную запись DocumentDB, необходимо [настроить этот параметр приложения вручную](). 
* Если указанный документ не найден, для именованного входного параметра функции задается значение `null`. 

## <a name="input-usage"></a>Использование входной привязки
В этом разделе показано, как использовать входную привязку DocumentDB в коде функции.

В функциях C# и F# любые изменения, внесенные во входной документ (именованный входной параметр), будут автоматически отправляться обратно в коллекцию после успешного выхода из функции. В функциях Node.js обновления документа во входной привязке не отправляются обратно в коллекцию. Однако для обновления входных документов можно использовать `context.bindings.<documentName>In` и `context.bindings.<documentName>Out`. Для этого ознакомьтесь с [примером Node.js](#innodejs).

<a name="inputsample"></a>

## <a name="input-sample"></a>Пример входной привязки
Предположим, что у вас есть следующая входная привязка DocumentDB в массиве `bindings` файла function.json:

```json
{
  "name": "inputDocument",
  "type": "documentDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "id" : "{queueTrigger}",
  "connection": "MyAccount_DOCUMENTDB",     
  "direction": "in"
}
```

Чтобы обновить текстовое значение документа, см. пример для конкретного языка, использующий эту входную привязку.

* [C#](#incsharp)
* [F#](#infsharp)
* [Node.js](#innodejs)

<a name="incsharp"></a>
### <a name="input-sample-in-c"></a>Пример входной привязки для языка C# #

```cs
public static void Run(string myQueueItem, dynamic inputDocument)
{   
  inputDocument.text = "This has changed.";
}
```
<a name="infsharp"></a>

### <a name="input-sample-in-f"></a>Пример входной привязки для языка F# #

```fsharp
open FSharp.Interop.Dynamic
let Run(myQueueItem: string, inputDocument: obj) =
  inputDocument?text <- "This has changed."
```

Необходимо добавить файл `project.json`, указывающий зависимости NuGet `FSharp.Interop.Dynamic` и `Dynamitey`:

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

<a name="innodejs"></a>

### <a name="input-sample-in-nodejs"></a>Пример входной привязки для Node.js

```javascript
module.exports = function (context) {   
  context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
  context.bindings.inputDocumentOut.text = "This was updated!";
  context.done();
};
```

## <a name="a-iddocdboutputadocumentdb-output-binding"></a><a id="docdboutput"></a>Выходная привязка DocumentDB
Выходная привязка DocumentDB позволяет записать новый документ в базу данных Azure DocumentDB. 

Выходная привязка использует следующий объект JSON в массиве `bindings` файла function.json: 

```json
{
  "name": "<Name of output parameter in function signature>",
  "type": "documentDB",
  "databaseName": "<Name of the DocumentDB database>",
  "collectionName": "<Name of the DocumentDB collection>",
  "createIfNotExists": <true or false - see below>,
  "connection": "<Value of AccountEndpoint in Application Setting - see below>",
  "direction": "out"
}
```

Обратите внимание на следующее.

* Задайте для параметра `createIfNotExists` значение `true`, чтобы создать базу данных и коллекцию, если она не существует. По умолчанию используется значение `false`. Коллекции создаются с использованием зарезервированной пропускной способности, с которой связаны ценовые требования. Дополнительные сведения см. в статье [DocumentDB. Цены](https://azure.microsoft.com/pricing/details/documentdb/).
* Значение свойства `connection` должно соответствовать имени параметра приложения, указывающего на конечную точку учетной записи DocumentDB (со значением `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>`). При создании учетной записи DocumentDB в пользовательском интерфейсе портала Функций автоматически создается параметр приложения. Чтобы использовать существующую учетную запись DocumentDB, необходимо [настроить этот параметр приложения вручную](). 

## <a name="output-usage"></a>Использование выходной привязки
В этом разделе показано, как использовать выходную привязку DocumentDB в коде функции.

При записи в выходном параметре функции в базе данных по умолчанию создается документ, для которого автоматически создается GUID в качестве идентификатора документа. Идентификатор выходного документа можно определить, указав свойство JSON `id` в выходном параметре. Если документ с таким идентификатором уже существует, выходной документ перезаписывает его. 

Запись выходных данных можно выполнить, используя любой из следующих типов.

* Любой [объект](https://msdn.microsoft.com/library/system.object.aspx) используется для сериализации JSON.
  Если объявить пользовательский выходной тип (например, `out FooType paramName`), Функции Azure попытаются сериализовать объект в JSON. Если при выходе из функции выходной параметр имеет значение null, среда выполнения Функций создает большой двоичный объект в качестве пустого объекта.
* Строка (`out string paramName`) используется для данных текстовых больших двоичных объектов. Среда выполнения Функций создает большой двоичный объект, только если при выходе из функции для параметра строки не задано значение null.

В функциях C# можно выполнить вывод одного из следующих типов:

* `TextWriter`
* `Stream`
* `CloudBlobStream`
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

Для вывода в несколько документов можно также выполнить привязку к `ICollector<T>` или `IAsyncCollector<T>`, где `T` — любой из поддерживаемых типов.


<a name="outputsample"></a>

## <a name="output-sample"></a>Пример выходной привязки
Предположим, что у вас есть следующая выходная привязка DocumentDB в массиве `bindings` файла function.json:

```json
{
  "name": "employeeDocument",
  "type": "documentDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "createIfNotExists": true,
  "connection": "MyAccount_DOCUMENTDB",     
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

При этом вам нужно создать документы DocumentDB в следующем формате для каждой записи:

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
* [Node.js](#outnodejs)

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

Необходимо добавить файл `project.json`, указывающий зависимости NuGet `FSharp.Interop.Dynamic` и `Dynamitey`:

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

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Пример выходной привязки для Node.js

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



<!--HONumber=Dec16_HO1-->


