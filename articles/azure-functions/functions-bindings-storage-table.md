---
title: "Привязки таблиц службы хранилища для Функций Azure | Документация Майкрософт"
description: "Узнайте, как использовать привязки службы хранилища Azure в Функциях Azure."
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "функции azure, функции, обработка событий, динамические вычисления, независимая архитектура"
ms.assetid: 65b3437e-2571-4d3f-a996-61a74b50a1c2
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/28/2016
ms.author: chrande
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 486b7c31c914ba7bb2d75e3f83ccf346a09104e8
ms.contentlocale: ru-ru
ms.lasthandoff: 08/29/2017

---
# <a name="azure-functions-storage-table-bindings"></a>Привязки таблиц службы хранилища для Функций Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

В этой статье поясняется, как настроить и запрограммировать привязки для таблиц службы хранилища Azure в Функциях Azure. Функции Azure поддерживают входные и выходные привязки для таблиц службы хранилища Azure.

Привязка таблицы службы хранилища применима в следующих сценариях:

* **Чтение одной строкой в функции C# или Node.** Задайте свойства `partitionKey` и `rowKey`. Свойства `filter` и `take` не используются в этом сценарии.
* **Чтение нескольких строк в функции C#.** Среда выполнения Функций предоставляет объект `IQueryable<T>`, привязанный к таблице. Тип `T` должен быть производным от типа `TableEntity` или реализацией типа `ITableEntity`. Свойства `partitionKey`, `rowKey`, `filter` и `take` не используются в этом сценарии. Для фильтрации можно использовать объект `IQueryable`. 
* **Чтение нескольких строк в функции Node.** Задайте свойства `filter` и `take`. Не определяйте свойства `partitionKey` и `rowKey`.
* **Запись одной или нескольких строк в функции C#.** Среда выполнения Функций предоставляет объект `ICollector<T>` или `IAsyncCollector<T>`, привязанный к таблице, где `T` обозначает схему сущностей, которые нужно добавить. Как правило, тип `T` является производным от `TableEntity` или реализует `ITableEntity`, но это не обязательно. Свойства `partitionKey`, `rowKey`, `filter` и `take` не используются в этом сценарии.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="input"></a>

## <a name="storage-table-input-binding"></a>Входная привязка таблицы службы хранилища
Входная привязка таблицы службы хранилища Azure позволяет использовать таблицу хранилища в функции. 

Входные данные таблицы службы хранилища для функции используют следующий объект JSON в массиве `bindings` файла function.json:

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "table",
    "direction": "in",
    "tableName": "<Name of Storage table>",
    "partitionKey": "<PartitionKey of table entity to read - see below>",
    "rowKey": "<RowKey of table entity to read - see below>",
    "take": "<Maximum number of entities to read in Node.js - optional>",
    "filter": "<OData filter expression for table input in Node.js - optional>",
    "connection": "<Name of app setting - see below>",
}
```

Обратите внимание на следующее. 

* Используйте совместно свойства `partitionKey` и `rowKey` для чтения одной сущности. Эти свойства являются необязательными. 
* Свойство `connection` должно включать в себя имя параметра приложения, содержащего строку подключения к службе хранилища. На портале Azure стандартный редактор на вкладке **Интеграция** автоматически настраивает этот параметр приложения при создании учетной записи хранения или выборе одной из имеющихся. Вы можете также [настроить этот параметр приложения вручную](functions-how-to-use-azure-function-app-settings.md#settings).  

<a name="inputusage"></a>

## <a name="input-usage"></a>Использование входной привязки
В функциях C# можно выполнить привязку к сущности (или сущностям) входной таблицы, используя именованный параметр в сигнатуре функции, например `<T> <name>`,
где `T` — это тип данных, в который требуется десериализировать данные, а `paramName` — это имя, указанное во [входной привязке](#input). В функциях Node.js доступ к сущности (или сущностям) входной таблицы можно получить, используя `context.bindings.<name>`.

Входные данные можно десериализировать в функции Node.js или C#. Десериализированный объект имеет свойства `RowKey` и `PartitionKey`.

В функциях C# также можно выполнить привязку к любому из следующих типов. Среда выполнения Функций попытается десериализировать данные таблицы, используя этот тип:

* Любой тип, реализующий объект `ITableEntity`.
* `IQueryable<T>`

<a name="inputsample"></a>

## <a name="input-sample"></a>Пример входной привязки
Предположим, что у вас есть следующий файл function.json, использующий триггер очереди для чтения одной строки таблицы. JSON указывает `PartitionKey` 
`RowKey`. `"rowKey": "{queueTrigger}"` указывает, что ключ строки получен из строки сообщения очереди.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
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
      "connection": "MyStorageConnection",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Ознакомьтесь с примером для конкретного языка, считывающим одну сущность таблицы.

* [C#](#inputcsharp)
* [F#](#inputfsharp)
* [Node.js](#inputnodejs)

<a name="inputcsharp"></a>

### <a name="input-sample-in-c"></a>Пример входной привязки для языка C# #
```csharp
public static void Run(string myQueueItem, Person personEntity, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    log.Info($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

<a name="inputfsharp"></a>

### <a name="input-sample-in-f"></a>Пример входной привязки для языка F# #
```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.Info(sprintf "Name in Person entity: %s" personEntity.Name)
```

<a name="inputnodejs"></a>

### <a name="input-sample-in-nodejs"></a>Пример входной привязки для Node.js
```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

<a name="output"></a>

## <a name="storage-table-output-binding"></a>Выходная привязка таблицы службы хранилища
Выходная привязка таблицы службы хранилища Azure позволяет записывать сущности в таблицу службы хранилища в функции. 

Выходные данные таблицы службы хранилища для функции используют следующий объект JSON в массиве `bindings` файла function.json:

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "table",
    "direction": "out",
    "tableName": "<Name of Storage table>",
    "partitionKey": "<PartitionKey of table entity to write - see below>",
    "rowKey": "<RowKey of table entity to write - see below>",
    "connection": "<Name of app setting - see below>",
}
```

Обратите внимание на следующее. 

* Используйте совместно свойства `partitionKey` и `rowKey` для записи одной сущности. Эти свойства являются необязательными. При создании объектов сущностей в коде функции можно также указать `PartitionKey` и `RowKey`.
* Свойство `connection` должно включать в себя имя параметра приложения, содержащего строку подключения к службе хранилища. На портале Azure стандартный редактор на вкладке **Интеграция** автоматически настраивает этот параметр приложения при создании учетной записи хранения или выборе одной из имеющихся. Вы можете также [настроить этот параметр приложения вручную](functions-how-to-use-azure-function-app-settings.md#settings). 

<a name="outputusage"></a>

## <a name="output-usage"></a>Использование выходной привязки
В функциях C# можно выполнить привязку к выходным данным таблицы, используя именованный параметр `out` в сигнатуре функции, например `out <T> <name>`, где `T` — это тип данных, в который нужно сериализовать данные, а `paramName` — это имя, указанное в [выходной привязке](#output). В функциях Node.js доступ к выходным данным таблицы можно получить, используя `context.bindings.<name>`.

Вы можете сериализовать объекты в функциях Node.js или C#. В функции C# можно выполнить привязку следующих типов:

* Любой тип, реализующий объект `ITableEntity`.
* `ICollector<T>` (Для вывода нескольких сущностей. Ознакомьтесь с [примером](#outcsharp).).
* `IAsyncCollector<T>` (асинхронная версия `ICollector<T>`).
* `CloudTable` (использование пакета SDK службы хранилища Azure. Ознакомьтесь с [примером](#readmulti).).

<a name="outputsample"></a>

## <a name="output-sample"></a>Пример выходной привязки
В следующем примере с файлами *function.json* и *run.csx* показано, как записать несколько сущностей таблицы.

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
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Ознакомьтесь с примером для конкретного языка, создающим несколько сущностей таблицы.

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Пример выходной привязки для языка C# #
```csharp
public static void Run(string input, ICollector<Person> tableBinding, TraceWriter log)
{
    for (int i = 1; i < 10; i++)
        {
            log.Info($"Adding Person entity {i}");
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
<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>Пример выходной привязки для языка F# #
```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: TraceWriter) =
    for i = 1 to 10 do
        log.Info(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Пример выходной привязки для Node.js
```javascript
module.exports = function (context) {

    context.bindings.tableBinding = [];

    for (var i = 1; i < 10; i++) {
        context.bindings.tableBinding.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });
    }
    
    context.done();
};
```

<a name="readmulti"></a>

## <a name="sample-read-multiple-table-entities-in-c"></a>Пример. Чтение нескольких сущностей таблицы на языке C#  #
В этом примере с файлом *function.json* и кодом C# считываются сущности для ключа секции (определяется в сообщении очереди).

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnection",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

В коде C# добавляется ссылка на пакет SDK для службы хранилища Azure, чтобы тип сущности мог быть производным от `TableEntity`.

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.Info($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

## <a name="next-steps"></a>Дальнейшие действия
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


