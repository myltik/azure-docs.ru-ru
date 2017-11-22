---
title: "Привязки хранилища таблиц для службы \"Функции Azure\""
description: "Узнайте, как использовать привязки службы хранилища таблиц Azure в службе \"Функции Azure\"."
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "функции azure, функции, обработка событий, динамические вычисления, независимая архитектура"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/08/2017
ms.author: chrande
ms.openlocfilehash: 2f54df931d03318a50e9397211e3c50d0898556d
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2017
---
# <a name="azure-functions-table-storage-bindings"></a>Привязки хранилища таблиц для службы "Функции Azure"

В этой статье рассматривается работа с привязками хранилища таблиц Azure в Функциях Azure. Функции Azure поддерживают входные и выходные привязки для хранилища таблиц Azure.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="table-storage-input-binding"></a>Входная привязка хранилища таблиц

Используйте входную привязку хранилища таблиц Azure для чтения таблицы в учетной записи хранения Azure.

## <a name="input---example"></a>Пример входных данных

Языковой пример см. в разделах:

* [Чтение одной сущности в предкомпилированном коде C#](#input---c-example-1);
* [Чтение нескольких сущностей в предкомпилированном коде C#](#input---c-example-2);
* [Скрипт C# — чтение одной сущности](#input---c-script-example-1);
* [Скрипт C# — чтение нескольких сущностей](#input---c-script-example-2);
* [F#](#input---f-example-2)
* [JavaScript](#input---javascript-example)

### <a name="input---c-example-1"></a>Пример 1 входных данных C#

В следующем примере показан [предкомпилированный код C#](functions-dotnet-class-library.md), который считывает одну строку таблицы. 

Значение ключа строки {queueTrigger} указывает, что ключ строки получен из строки сообщения очереди.

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition", "{queueTrigger}")] MyPoco poco, 
        TraceWriter log)
    {
        log.Info($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}";
    }
}
```

### <a name="input---c-example-2"></a>Пример 2 входных данных C#

В следующем примере показан [предкомпилированный код C#](functions-dotnet-class-library.md), который считывает несколько строк таблицы. Обратите внимание, что класс `MyPoco` является производным от `TableEntity`.

```csharp
public class TableStorage
{
    public class MyPoco : TableEntity
    {
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition")] IQueryable<MyPoco> pocos, 
        TraceWriter log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.Info($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}";
        }
    }
}
```

### <a name="input---c-script-example-1"></a>Пример 1 входных данных скрипта C#

В следующем примере показана входная привязка таблицы в файле *function.json* и коде [скрипта C#](functions-reference-csharp.md), который использует привязку. Функция использует триггер очереди для чтения одной строки таблицы. 

Файл *function.json* определяет `partitionKey` и `rowKey`. Значение `rowKey` "{queueTrigger}" указывает, что ключ строки получен из строки сообщения очереди.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
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
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

В разделе [Конфигурация](#input---configuration) описываются эти свойства.

Ниже приведен код скрипта C#.

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

### <a name="input---c-script-example-2"></a>Пример 2 входных данных скрипта C#

В следующем примере показана входная привязка таблицы в файле *function.json* и коде [скрипта C#](functions-reference-csharp.md), который использует привязку. Функция считывает сущности ключа раздела, который указан в очереди сообщений.

Ниже показан файл *function.json*.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnectionAppSetting",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

В разделе [Конфигурация](#input---configuration) описываются эти свойства.

В коде скрипта C# добавляется ссылка на пакет SDK для службы хранилища Azure, чтобы тип сущности мог быть производным от `TableEntity`.

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

### <a name="input---f-example"></a>Пример входных данных F#

В следующем примере показана входная привязка таблицы в файле *function.json* и коде [скрипта F#](functions-reference-fsharp.md), который использует привязку. Функция использует триггер очереди для чтения одной строки таблицы. 

Файл *function.json* определяет `partitionKey` и `rowKey`. Значение `rowKey` "{queueTrigger}" указывает, что ключ строки получен из строки сообщения очереди.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
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
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

В разделе [Конфигурация](#input---configuration) описываются эти свойства.

Ниже показан код F#.

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

### <a name="input---javascript-example"></a>Пример входных данных JavaScript

В следующем примере показана входная привязка таблицы в файле *function.json* и [коде JavaScript] (functions-reference-node.md), который использует привязку. Функция использует триггер очереди для чтения одной строки таблицы. 

Файл *function.json* определяет `partitionKey` и `rowKey`. Значение `rowKey` "{queueTrigger}" указывает, что ключ строки получен из строки сообщения очереди.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
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
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

В разделе [Конфигурация](#input---configuration) описываются эти свойства.

Ниже показан код JavaScript.

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

## <a name="input---attributes-for-precompiled-c"></a>Входные данные атрибутов для предкомпилированного кода C#
 
Для функций [предкомпилированного кода C#](functions-dotnet-class-library.md) используйте следующие атрибуты, позволяющие настроить входную привязку таблицы:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), определенный в пакете NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs).

  Конструктор атрибута принимает имя таблицы, ключ раздела и строки. Его можно использовать с параметром вывода или возвращаемым значением функции, как показано в следующем примере.

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      TraceWriter log)
  ```

  Чтобы указать учетную запись хранения, которую нужно использовать, можно задать свойство `Connection`, как показано в следующем примере.

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}", Connection = "StorageConnectionAppSetting")] MyPoco poco, 
      TraceWriter log)
  ```

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs), определенный в пакете NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs).

  Предоставляет еще один способ указать используемую учетную запись хранения. Конструктор принимает имя параметра приложения, содержащего строку подключения к службе хранилища. Атрибут может применяться на уровне класса, метода или параметра. Ниже показан пример уровня класса и метода.

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("TableInput")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  ```

Используемая учетная запись хранения определяется в следующем порядке:

* Свойство `Table` атрибута `Connection`.
* Атрибут `StorageAccount`, примененный к тому же параметру, что и `Table`.
* Атрибут `StorageAccount`, примененный к функции.
* Атрибут `StorageAccount`, примененный к классу.
* Учетная запись хранения по умолчанию для приложения-функции (параметр приложения AzureWebJobsStorage).

## <a name="input---configuration"></a>Входная конфигурация

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `Table`.

|свойство function.json | Свойство атрибута |Описание|
|---------|---------|----------------------|
|**type** | Недоступно | Нужно задать значение `table`. Это свойство задается автоматически при создании привязки на портале Azure.|
|**direction** | Недоступно | Нужно задать значение `in`. Это свойство задается автоматически при создании привязки на портале Azure. |
|**name** | Недоступно | Имя переменной, представляющей таблицу или сущность в коде функции. | 
|**tableName** | **TableName** | Это имя таблицы.| 
|**partitionKey** | **PartitionKey** |необязательный параметр. Ключ раздела сущности таблицы, которую нужно считать. Дополнительные сведения о том, как использовать это свойство, см. в этом [разделе](#input---usage).| 
|**rowKey** |**RowKey** | необязательный параметр. Ключ строки сущности таблицы, которую нужно считать. Дополнительные сведения о том, как использовать это свойство, см. в этом [разделе](#input---usage).| 
|**take** |**take** | необязательный параметр. Максимальное количество считываемых сущностей в JavaScript. Дополнительные сведения о том, как использовать это свойство, см. в этом [разделе](#input---usage).| 
|**filter** |**Filter** | необязательный параметр. Выражение фильтра OData для входных данных таблицы в JavaScript. Дополнительные сведения о том, как использовать это свойство, см. в этом [разделе](#input---usage).| 
|**подключение** |**Connection** | Имя параметра приложения, содержащего строку подключения к службе хранилища, используемой для этой привязки. Если имя параметра приложения начинается с AzureWebJobs, можно указать только остальную часть имени. Например, если задать для `connection` значение MyStorage, среда выполнения службы "Функции" будет искать параметр приложения с именем AzureWebJobsMyStorage. Если оставить строку `connection` пустой, среда выполнения службы "Функции" будет использовать строку подключения к службе хранилища по умолчанию для параметра приложения с именем `AzureWebJobsStorage`.<br/>При локальной разработке параметры приложения перейдут к значениям файла [local.settings.json](functions-run-local.md#local-settings-file).|

## <a name="input---usage"></a>Использование входной привязки

Входная привязка хранилища таблиц применима в следующих сценариях:

* **Чтение одной строки в коде или скрипте C#**

  Задайте значения для `partitionKey` и `rowKey`. Получите доступ к данным таблицы с помощью параметра метода `T <paramName>`. В скрипте C# `paramName` — это значение, заданное в свойстве `name` файла *function.json*. `T` обычно представляет собой тип, реализующий `ITableEntity`, или является производным от `TableEntity`. Свойства `filter` и `take` не используются в этом сценарии. 

* **Чтение одной или нескольких строк в коде или скрипте C#**

  Получите доступ к данным таблицы с помощью параметра метода `IQueryable<T> <paramName>`. В скрипте C# `paramName` — это значение, заданное в свойстве `name` файла *function.json*. `T` должен представлять собой тип, реализующий `ITableEntity`, или быть производным от `TableEntity`. Для выполнения фильтрации можно использовать методы `IQueryable`. Свойства `partitionKey`, `rowKey`, `filter` и `take` не используются в этом сценарии.  

> [!NOTE]
> `IQueryable` не работает в .NET Core и поэтому не работает в [среде выполнения службы "Функции" версии 2](functions-versions.md).

  Альтернативой является использование параметра метода `CloudTable paramName` для чтения таблицы с помощью пакета SDK службы хранилища Azure.

* **Чтение одной или нескольких строк в JavaScript**

  Укажите свойства `filter` и `take`. Не определяйте свойства `partitionKey` и `rowKey`. Получите доступ к сущности (или сущностям) входной таблицы, используя `context.bindings.<name>`. Десериализированный объект имеет свойства `RowKey` и `PartitionKey`.

## <a name="table-storage-output-binding"></a>Выходная привязка хранилища таблиц

Используйте выходную привязку хранилища таблиц Azure для записи сущностей в таблицу в учетной записи хранения Azure.

## <a name="output---example"></a>Пример выходных данных

Языковой пример см. в разделах:

* [Предкомпилированный код C#](#output---c-example)
* [Сценарий C#](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Пример выходных данных C#

В следующем примере показан [предкомпилированный код C#](functions-dotnet-class-library.md), который использует триггер HTTP для записи одной строки таблицы. 

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, TraceWriter log)
    {
        log.Info($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

### <a name="output---c-script-example"></a>Пример выходных данных скрипта C#

В следующем примере показана выходная привязка таблицы в файле *function.json* и коде [скрипта C#](functions-reference-csharp.md), который использует привязку. Эта функция записывает несколько сущностей в таблице.

Ниже показан файл *function.json*.

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
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

В разделе [Конфигурация](#output---configuration) описываются эти свойства.

Ниже приведен код скрипта C#.

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

### <a name="output---f-example"></a>Пример выходных данных F#

В следующем примере показана выходная привязка таблицы в файле *function.json* и коде [скрипта F#](functions-reference-fsharp.md), который использует привязку. Эта функция записывает несколько сущностей в таблице.

Ниже показан файл *function.json*.

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
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

В разделе [Конфигурация](#output---configuration) описываются эти свойства.

Ниже показан код F#.

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

### <a name="output---javascript-example"></a>Пример выходных данных JavaScript

В следующем примере показана выходная привязка таблицы в файле *function.json* и функции [JavaScript](functions-reference-node.md), которая использует привязку. Эта функция записывает несколько сущностей в таблице.

Ниже показан файл *function.json*.

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
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

В разделе [Конфигурация](#output---configuration) описываются эти свойства.

Ниже показан код JavaScript.

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

## <a name="output---attributes-for-precompiled-c"></a>Выходные данные атрибутов для предкомпилированного кода C#

 Для функций [предкомпилированного кода C#](functions-dotnet-class-library.md) используйте атрибут [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), определенный в пакете NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs).

Конструктор атрибута использует имя таблицы. Его можно использовать с параметром `out` или возвращаемым значением функции, как показано в следующем примере.

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    TraceWriter log)
```

Чтобы указать учетную запись хранения, которую нужно использовать, можно задать свойство `Connection`, как показано в следующем примере.

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    TraceWriter log)
```

Чтобы указать учетную запись хранения на уровне класса, метода или параметра, можно использовать атрибут `StorageAccount`. Дополнительные сведения см. в разделе [Входные данные атрибутов для предкомпилированного кода C#](#input---attributes-for-precompiled-c).

## <a name="output---configuration"></a>Выходная конфигурация

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `Table`.

|свойство function.json | Свойство атрибута |Описание|
|---------|---------|----------------------|
|**type** | Недоступно | Нужно задать значение `table`. Это свойство задается автоматически при создании привязки на портале Azure.|
|**direction** | Недоступно | Нужно задать значение `out`. Это свойство задается автоматически при создании привязки на портале Azure. |
|**name** | Недоступно | Имя переменной, используемое в функции кода, которая представляет таблицу или сущность. Задайте значение `$return`, ссылающееся на возвращаемое значение функции.| 
|**tableName** |**TableName** | Это имя таблицы.| 
|**partitionKey** |**PartitionKey** | Ключ раздела сущности таблицы, которую нужно записать. Дополнительные сведения о том, как использовать это свойство, см. в этом [разделе](#output---usage).| 
|**rowKey** |**RowKey** | Ключ строки сущности таблицы, которую нужно записать. Дополнительные сведения о том, как использовать это свойство, см. в этом [разделе](#output---usage).| 
|**подключение** |**Connection** | Имя параметра приложения, содержащего строку подключения к службе хранилища, используемой для этой привязки. Если имя параметра приложения начинается с AzureWebJobs, можно указать только остальную часть имени. Например, если задать для `connection` значение MyStorage, среда выполнения службы "Функции" будет искать параметр приложения с именем AzureWebJobsMyStorage. Если оставить строку `connection` пустой, среда выполнения службы "Функции" будет использовать строку подключения к службе хранилища по умолчанию для параметра приложения с именем `AzureWebJobsStorage`.<br/>При локальной разработке параметры приложения перейдут к значениям файла [local.settings.json](functions-run-local.md#local-settings-file).|

## <a name="output---usage"></a>Использование выходной привязки

Выходная привязка хранилища таблиц применима в следующих сценариях:

* **Запись одной строки на любом языке**

  В коде и скрипте C# получите доступ к сущности выходной таблицы с помощью параметра метода (например, `out T paramName`) или возвращаемого значения функции. В скрипте C# `paramName` — это значение, заданное в свойстве `name` файла *function.json*. `T` может быть любым сериализируемым типом, если ключ раздела и строки предоставляются в файле *function.json* или атрибуте `Table`. В противном случае `T` должен быть типом со свойствами `PartitionKey` и `RowKey`. В этом сценарии `T` обычно реализует `ITableEntity` или является производным от `TableEntity` (но необязательно).

* **Запись одной или нескольких строк в коде или скрипте C#**

  В коде и скрипте C# получите доступ к сущности выходной таблицы с помощью параметра метода (например, `ICollector<T> paramName` или `ICollectorAsync<T> paramName`). В скрипте C# `paramName` — это значение, заданное в свойстве `name` файла *function.json*. В `T` указывается схема сущностей, которые нужно добавить. Как правило, `T` является производным от `TableEntity` или реализует `ITableEntity`, но это необязательно. Значения ключа раздела и строки в *function.json* или конструктора атрибута `Table` не используются в этом сценарии.

  Альтернативой является использование параметра метода `CloudTable paramName` для записи в таблицу с помощью пакета SDK службы хранилища Azure.

* **Запись одной или нескольких строк в JavaScript**

  В функциях JavaScript доступ к выходным данным таблицы можно получить, используя `context.bindings.<name>`.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)
