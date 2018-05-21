---
title: Привязки хранилища таблиц Azure для службы "Функции Azure"
description: Узнайте, как использовать привязки службы хранилища таблиц Azure в службе "Функции Azure".
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: функции azure, функции, обработка событий, динамические вычисления, независимая архитектура
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/08/2017
ms.author: tdykstra
ms.openlocfilehash: 1c8cee149e99786b58e4584e5e7b508b1040389d
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/18/2018
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Привязки хранилища таблиц Azure для службы "Функции Azure"

В этой статье рассматривается работа с привязками хранилища таблиц Azure в Функциях Azure. Функции Azure поддерживают входные и выходные привязки для хранилища таблиц Azure.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>Пакеты

Привязки службы "Хранилище таблиц" доступны в пакете NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs). Исходный код для пакета находится в репозитории GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-package-versions](../../includes/functions-package-versions.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="input"></a>Входные данные

Используйте входную привязку хранилища таблиц Azure для чтения таблицы в учетной записи хранения Azure.

## <a name="input---example"></a>Пример входных данных

Языковой пример см. в разделах:

* [C# — чтение одной сущности](#input---c-example-1);
* [C# — чтение нескольких сущностей](#input---c-example-2);
* [Скрипт C# — чтение одной сущности](#input---c-script-example-1);
* [Скрипт C# — чтение нескольких сущностей](#input---c-script-example-2);
* [F#](#input---f-example-2)
* [JavaScript](#input---javascript-example)

### <a name="input---c-example-1"></a>Пример 1 входных данных C#

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая считывает одну строку таблицы. 

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
        log.Info($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
    }
}
```

### <a name="input---c-example-2"></a>Пример 2 входных данных C#

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая считывает несколько строк таблицы. Обратите внимание, что класс `MyPoco` является производным от `TableEntity`.

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
            log.Info($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
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

## <a name="input---attributes"></a>Входные атрибуты
 
В [библиотеках класса C#](functions-dotnet-class-library.md) используйте следующие атрибуты для настройки входной привязки таблицы:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs)

  Конструктор атрибута принимает имя таблицы, ключ раздела и строки. Его можно использовать с параметром вывода или возвращаемым значением функции, как показано в следующем примере.

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      TraceWriter log)
  {
      ...
  }
  ```

  Чтобы указать учетную запись хранения, которую нужно использовать, можно задать свойство `Connection`, как показано в следующем примере.

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}", Connection = "StorageConnectionAppSetting")] MyPoco poco, 
      TraceWriter log)
  {
      ...
  }
  ```

  Полный пример см. в разделе [примеров входных данных C#](#input---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Предоставляет еще один способ указать используемую учетную запись хранения. Конструктор принимает имя параметра приложения, содержащего строку подключения к службе хранилища. Атрибут может применяться на уровне класса, метода или параметра. Ниже показан пример уровня класса и метода.

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("TableInput")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

Используемая учетная запись хранения определяется в следующем порядке:

* Свойство `Table` атрибута `Connection`.
* Атрибут `StorageAccount`, примененный к тому же параметру, что и `Table`.
* Атрибут `StorageAccount`, примененный к функции.
* Атрибут `StorageAccount`, примененный к классу.
* Учетная запись хранения по умолчанию для приложения-функции (параметр приложения AzureWebJobsStorage).

## <a name="input---configuration"></a>Входная конфигурация

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `Table`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**type** | Недоступно | Нужно задать значение `table`. Это свойство задается автоматически при создании привязки на портале Azure.|
|**direction** | Недоступно | Нужно задать значение `in`. Это свойство задается автоматически при создании привязки на портале Azure. |
|**name** | Недоступно | Имя переменной, представляющей таблицу или сущность в коде функции. | 
|**tableName** | **TableName** | Это имя таблицы.| 
|**partitionKey** | **PartitionKey** |Необязательный элемент. Ключ раздела сущности таблицы, которую нужно считать. Дополнительные сведения о том, как использовать это свойство, см. в этом [разделе](#input---usage).| 
|**rowKey** |**RowKey** | Необязательный элемент. Ключ строки сущности таблицы, которую нужно считать. Дополнительные сведения о том, как использовать это свойство, см. в этом [разделе](#input---usage).| 
|**take** |**take** | Необязательный элемент. Максимальное количество считываемых сущностей в JavaScript. Дополнительные сведения о том, как использовать это свойство, см. в этом [разделе](#input---usage).| 
|**filter** |**Filter** | Необязательный элемент. Выражение фильтра OData для входных данных таблицы в JavaScript. Дополнительные сведения о том, как использовать это свойство, см. в этом [разделе](#input---usage).| 
|**подключение** |**Connection** | Имя параметра приложения, содержащего строку подключения к службе хранилища, используемой для этой привязки. Если имя параметра приложения начинается с AzureWebJobs, можно указать только остальную часть имени. Например, если задать для `connection` значение MyStorage, среда выполнения службы "Функции" будет искать параметр приложения с именем AzureWebJobsMyStorage. Если оставить строку `connection` пустой, среда выполнения службы "Функции" будет использовать строку подключения к службе хранилища по умолчанию для параметра приложения с именем `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Использование входной привязки

Входная привязка хранилища таблиц применима в следующих сценариях:

* **Чтение одной строки в коде или скрипте C#**

  Задайте значения для `partitionKey` и `rowKey`. Получите доступ к данным таблицы с помощью параметра метода `T <paramName>`. В скрипте C# `paramName` — это значение, заданное в свойстве `name` файла *function.json*. `T` обычно представляет собой тип, реализующий `ITableEntity`, или является производным от `TableEntity`. Свойства `filter` и `take` не используются в этом сценарии. 

* **Чтение одной или нескольких строк в коде или скрипте C#**

  Получите доступ к данным таблицы с помощью параметра метода `IQueryable<T> <paramName>`. В скрипте C# `paramName` — это значение, заданное в свойстве `name` файла *function.json*. `T` должен представлять собой тип, реализующий `ITableEntity`, или быть производным от `TableEntity`. Для выполнения фильтрации можно использовать методы `IQueryable`. Свойства `partitionKey`, `rowKey`, `filter` и `take` не используются в этом сценарии.  

> [!NOTE]
> `IQueryable` не поддерживается в [среде выполнения Функций версии 2](functions-versions.md). Альтернативой является [использование параметра метода CloudTable paramName](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) для чтения таблицы с помощью пакета SDK службы хранилища Azure.

* **Чтение одной или нескольких строк в JavaScript**

  Укажите свойства `filter` и `take`. Не определяйте свойства `partitionKey` и `rowKey`. Получите доступ к сущности (или сущностям) входной таблицы, используя `context.bindings.<name>`. Десериализированный объект имеет свойства `RowKey` и `PartitionKey`.

## <a name="output"></a>Выходные данные

Используйте выходную привязку хранилища таблиц Azure для записи сущностей в таблицу в учетной записи хранения Azure.

> [!NOTE]
> Выходная привязка не поддерживает обновление существующих сущностей. Для обновления существующей сущности используйте операцию `TableOperation.Replace` из [пакета SDK для службы хранилища Azure](https://docs.microsoft.com/azure/cosmos-db/table-storage-how-to-use-dotnet#replace-an-entity).   

## <a name="output---example"></a>Пример выходных данных

Языковой пример см. в разделах:

* [C#](#output---c-example)
* [Скрипт C# (CSX)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Пример выходных данных C#

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая использует триггер HTTP для записи одной строки таблицы. 

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

## <a name="output---attributes"></a>Выходные атрибуты

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs).

Конструктор атрибута использует имя таблицы. Его можно использовать с параметром `out` или возвращаемым значением функции, как показано в следующем примере.

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    TraceWriter log)
{
    ...
}
```

Чтобы указать учетную запись хранения, которую нужно использовать, можно задать свойство `Connection`, как показано в следующем примере.

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    TraceWriter log)
{
    ...
}
```

Полный пример см. в разделе [Пример выходных данных C#](#output---c-example).

Чтобы указать учетную запись хранения на уровне класса, метода или параметра, можно использовать атрибут `StorageAccount`. Дополнительные сведения см. в разделе [Входные атрибуты](#input---attributes).

## <a name="output---configuration"></a>Выходная конфигурация

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `Table`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**type** | Недоступно | Нужно задать значение `table`. Это свойство задается автоматически при создании привязки на портале Azure.|
|**direction** | Недоступно | Нужно задать значение `out`. Это свойство задается автоматически при создании привязки на портале Azure. |
|**name** | Недоступно | Имя переменной, используемое в функции кода, которая представляет таблицу или сущность. Задайте значение `$return`, ссылающееся на возвращаемое значение функции.| 
|**tableName** |**TableName** | Это имя таблицы.| 
|**partitionKey** |**PartitionKey** | Ключ раздела сущности таблицы, которую нужно записать. Дополнительные сведения о том, как использовать это свойство, см. в этом [разделе](#output---usage).| 
|**rowKey** |**RowKey** | Ключ строки сущности таблицы, которую нужно записать. Дополнительные сведения о том, как использовать это свойство, см. в этом [разделе](#output---usage).| 
|**подключение** |**Connection** | Имя параметра приложения, содержащего строку подключения к службе хранилища, используемой для этой привязки. Если имя параметра приложения начинается с AzureWebJobs, можно указать только остальную часть имени. Например, если задать для `connection` значение MyStorage, среда выполнения службы "Функции" будет искать параметр приложения с именем AzureWebJobsMyStorage. Если оставить строку `connection` пустой, среда выполнения службы "Функции" будет использовать строку подключения к службе хранилища по умолчанию для параметра приложения с именем `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Использование выходной привязки

Выходная привязка хранилища таблиц применима в следующих сценариях:

* **Запись одной строки на любом языке**

  В коде и скрипте C# получите доступ к сущности выходной таблицы с помощью параметра метода (например, `out T paramName`) или возвращаемого значения функции. В скрипте C# `paramName` — это значение, заданное в свойстве `name` файла *function.json*. `T` может быть любым сериализируемым типом, если ключ раздела и строки предоставляются в файле *function.json* или атрибуте `Table`. В противном случае `T` должен быть типом со свойствами `PartitionKey` и `RowKey`. В этом сценарии `T` обычно реализует `ITableEntity` или является производным от `TableEntity` (но необязательно).

* **Запись одной или нескольких строк в коде или скрипте C#**

  В коде и скрипте C# получите доступ к сущности выходной таблицы с помощью параметра метода (например, `ICollector<T> paramName` или `IAsyncCollector<T> paramName`). В скрипте C# `paramName` — это значение, заданное в свойстве `name` файла *function.json*. В `T` указывается схема сущностей, которые нужно добавить. Как правило, `T` является производным от `TableEntity` или реализует `ITableEntity`, но это необязательно. Значения ключа раздела и строки в *function.json* или конструктора атрибута `Table` не используются в этом сценарии.

  Альтернативой является использование параметра метода `CloudTable paramName` для записи в таблицу с помощью пакета SDK службы хранилища Azure.

* **Запись одной или нескольких строк в JavaScript**

  В функциях JavaScript доступ к выходным данным таблицы можно получить, используя `context.bindings.<name>`.

## <a name="exceptions-and-return-codes"></a>Исключения и коды возврата

| Привязка | Справочные материалы |
|---|---|
| Таблица | [Коды ошибок таблицы](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| Большой двоичный объект, таблица, очередь | [Коды ошибок хранилища](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Большой двоичный объект, таблица, очередь | [Устранение неполадок](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)
