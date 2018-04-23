---
title: Триггеры и привязки в решении "Функции Azure"
description: Узнайте, как подключить выполнение кода к интерактивным мероприятиям и облачным службам, используя триггеры и привязки в Функциях Azure.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: функции azure, функции, обработка событий, веб-перехватчики, динамические вычисления, независимая архитектура
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/07/2018
ms.author: tdykstra
ms.openlocfilehash: 56b0f8e24dfc38b542f4bbfc7975f1704d70f22c
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Основные понятия триггеров и привязок в Функциях Azure

В этой статье приведены общие сведения о триггерах и привязках в решении "Функции Azure". Здесь описаны функции, которые являются общими для всех привязок и всех поддерживаемых языков.

## <a name="overview"></a>Обзор

*Триггер* определяет способ вызова функции. У функции должен быть только один триггер. С триггерами могут быть связанны данные, которые обычно являются полезными и активируют функцию.

Входные и выходные *привязки* реализуют декларативный способ подключения к данным из кода. Привязки необязательны, а у функции может быть несколько входных и выходных привязок. 

Триггеры и привязки позволяют не включать в код программ сведения о конкретных службах, с которыми вы работаете. Функция получает нужные данные (например, содержимое сообщения из очереди) в виде параметров. Вы отправляете данные (например, для создания в очереди сообщения) через возвращаемое значение функции, параметр `out`, или [объект "сборщик"](functions-reference-csharp.md#writing-multiple-output-values).

Если вы создаете функции на портале Azure, триггеры и привязки настраиваются в файле *function.json*. Портал предоставляет для этой настройки пользовательский интерфейс, но файл можно изменять напрямую, перейдя в **расширенный редактор**.

Если для создания функций вы пишете библиотеку класса в Visual Studio, для настройки триггеров и привязок методам и параметрам присваиваются атрибуты.

## <a name="supported-bindings"></a>Поддерживаемые привязки

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Сведения о том, какие привязки доступны в предварительной версии или утверждены для использования в рабочей среде, см. в статье [Поддерживаемые языки в решении "Функции Azure"](supported-languages.md).

## <a name="register-binding-extensions"></a>Регистрация расширений привязки

В версии 2.х среды выполнения решения "Функции Azure" нужно явно зарегистрировать [расширения привязки](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/README.md), используемые в приложении-функции. 

Расширения предоставляются как пакеты NuGet, имя которых обычно начинается с [microsoft.azure.webjobs.extensions](https://www.nuget.org/packages?q=microsoft.azure.webjobs.extensions).  Способ установки и регистрации расширений привязки зависит от способа разработки функций: 

+ [локально в C# с использованием Visual Studio или VS Code](#local-c-development-using-visual-studio-or-vs-code);
+ [локально с использованием основных инструментов решения "Функции Azure"](#local-development-azure-functions-core-tools);
+ [на портале Azure](#azure-portal-development). 

В версии 2.x существует базовый набор привязок, которые не предоставляются как расширения. Для следующих триггеров и привязок не нужно регистрировать расширения: HTTP, таймер и служба хранилища Azure. 

Сведения о настройке приложения-функции для использования среды выполнения Функций версии 2.x см. в статье [Выбор целевых версий среды выполнения Функций Azure](set-runtime-version.md). Версия 2.х среды выполнения решения "Функции" в настоящее время находится в предварительной версии. 

Версии пакетов, упомянутые в этом разделе, приведены только в качестве примеров. Перейдите на сайт [NuGet.org](https://www.nuget.org/packages?q=microsoft.azure.webjobs.extensions), чтобы определить, какая версия данного расширения требуется для других зависимостей в приложении-функции.    

###  <a name="local-c-development-using-visual-studio-or-vs-code"></a>Локальная разработка на C# с помощью Visual Studio или VS Code 

При использовании Visual Studio или Visual Studio Code для разработки функций в C# локально необходимо просто добавить пакет NuGet для расширения. 

+ **Visual Studio**. Воспользуйтесь диспетчером пакетов NuGet. Следующая команда [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) устанавливает расширение Azure Cosmos DB из консоли диспетчера пакетов:

    ```
    Install-Package Microsoft.Azure.WebJobs.Extensions.CosmosDB -Version 3.0.0-beta6 
    ```
+ **Visual Studio Code**. Пакеты можно установить из командной строки, используя команду [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) в .NET CLI, как показано ниже.

    ```
    dotnet add package Microsoft.Azure.WebJobs.Extensions.CosmosDB --version 3.0.0-beta6 
    ```

### <a name="local-development-azure-functions-core-tools"></a>Локальная разработка основных инструментов решения "Функции Azure"

[!INCLUDE [Full bindings table](../../includes/functions-core-tools-install-extension.md)]

### <a name="azure-portal-development"></a>Разработка на портале Azure

При создании функции или добавлении привязки в имеющуюся функцию появится запрос, если для добавления триггера или привязки требуется регистрация.   

Когда появится предупреждение о том, что конкретное расширение установлено, выберите **Установить**, чтобы зарегистрировать расширение. Достаточно установить каждое расширение по одному разу для конкретного приложения-функции. 

>[!Note] 
>В рамках плана потребления процесс установки на портале может занять до 10 минут.

## <a name="example-trigger-and-binding"></a>Пример триггера и привязки

Предположим, вам нужно, чтобы каждый раз, когда в хранилище очередей Azure появляется новое сообщение, создавалась строка в хранилище таблиц Azure. Этот сценарий можно реализовать с помощью триггера для хранилища очередей Azure и выходной привязки хранилища таблиц Azure. 

Ниже представлен пример файла *function.json* для этого сценария. 

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "name": "$return",
      "type": "table",
      "direction": "out",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

Первым элементом массива `bindings` является триггер хранилища очередей. Свойства `type` и `direction` идентифицируют триггер. Свойство `name` идентифицирует параметр функции, который будет получать содержимое из сообщения очереди. Отслеживаемая очередь имеет имя `queueName`, а строка подключения содержится в параметре приложения, указанном в `connection`.

Вторым элементом массива `bindings` является выходная привязка хранилища таблиц Azure. Свойства `type` и `direction` идентифицируют привязку. Свойство `name` указывает, как функция предоставит новую строку таблицы. В нашем примере используется возвращаемое значение функции. Таблица имеет имя `tableName`, а строка подключения содержится в параметре приложения, указанном в `connection`.

Чтобы просмотреть и отредактировать содержимое файла *function.json* на портале Azure, щелкните параметр **Расширенный редактор** на вкладке **Интегрировать** этой функции.

> [!NOTE]
> Параметр `connection` содержит имя параметра приложения, в котором хранится строка подключения, но не саму строку. Привязки используют параметры приложения для хранения строк подключения, чтобы обеспечить соблюдение рекомендаций: файл *function.json* не должен содержать секреты службы.

Ниже приведен код скрипта C#, который работает с этим триггером и этой привязкой. Обратите внимание, что содержимое сообщения очереди предоставляется в параметре `order`. Это имя является обязательным, поскольку свойство `name` в файле *function.json* имеет значение `order`. 

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, TraceWriter log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

Этот же файл function.json можно использовать и в функции JavaScript:

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

В библиотеке классов эта информация о триггере и привязке &mdash; имена очередей и таблиц, учетные записи хранения, входные и выходные параметры функции &mdash; передаются в виде атрибутов вместо файла function.json. Ниже приведен пример:

```csharp
 public static class QueueTriggerTableOutput
 {
     [FunctionName("QueueTriggerTableOutput")]
     [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
     public static Person Run(
         [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order, 
         TraceWriter log)
     {
         return new Person() {
                 PartitionKey = "Orders",
                 RowKey = Guid.NewGuid().ToString(),
                 Name = order["Name"].ToString(),
                 MobileNumber = order["MobileNumber"].ToString() };
     }
 }

 public class Person
 {
     public string PartitionKey { get; set; }
     public string RowKey { get; set; }
     public string Name { get; set; }
     public string MobileNumber { get; set; }
 }
```

## <a name="binding-direction"></a>Направление привязки

Все триггеры и привязки имеют свойство `direction` в файле *function.json*:

- Для триггеров направление всегда входящее (`in`).
- Для входных и выходных привязок используется как входящее (`in`), так и исходящее (`out`) направление.
- Некоторые привязки поддерживают специальное направление `inout`. При использовании `inout` на вкладке **Интегрировать** доступен только параметр **Расширенный редактор**.

Если вы используете для настройки триггеров и привязок [атрибуты в библиотеке классов](functions-dotnet-class-library.md), направление задается в конструкторе атрибута или вычисляется по типу параметра.

## <a name="using-the-function-return-value"></a>Использование возвращаемого значения функции

В языках, где есть возвращаемое значение, можно применить выходную привязку к возвращаемому значению:

* В библиотеке классов C# примените атрибут выходной привязки к возвращаемому значению метода.
* В других языках задайте для свойства `name` значение `$return` в файле *function.json*.

Если вам нужно написать больше одного элемента, используйте объект [collector object](functions-reference-csharp.md#writing-multiple-output-values) вместо возвращаемого значения. При наличии нескольких выходных привязок используйте возвращаемое значение только для одной из них.

Языковой пример см. в разделах:

* [C#](#c-example)
* [Скрипт C# (CSX)](#c-script-example)
* [F#](#f-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>Пример C#

Это код C#, который использует возвращаемое значение для выходной привязки, за которым следует пример асинхронной функции:

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static string Run([QueueTrigger("inputqueue")]WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static Task<string> Run([QueueTrigger("inputqueue")]WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

### <a name="c-script-example"></a>Пример сценария C#

Выходная привязка в файле *function.json*:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Ниже приведен код скрипта C#, за которым следует пример асинхронной функции:

```cs
public static string Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
public static Task<string> Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

### <a name="f-example"></a>Пример F#

Выходная привязка в файле *function.json*:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Ниже показан код F#.

```fsharp
let Run(input: WorkItem, log: TraceWriter) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.Info(sprintf "F# script processed queue message '%s'" json)
    json
```

### <a name="javascript-example"></a>Пример JavaScript

Выходная привязка в файле *function.json*:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

В JavaScript возвращаемое значение передается в качестве второго параметра в `context.done`:

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

## <a name="binding-datatype-property"></a>Свойство привязки dataType

В .NET тип входных данных определяется типом параметра. Например, используйте `string` для привязки к тексту триггера очереди, массив байтов — для чтения в двоичном формате и пользовательский тип — для десериализации объекта POCO.

В языках с динамическим типированием, таких как JavaScript, используйте свойство `dataType` в файле *function.json*. Например, задайте для `dataType` значение `binary`, чтобы прочитать содержимое HTTP-запроса в двоичном формате.

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Другие варианты для `dataType` — `stream` и `string`.

## <a name="binding-expressions-and-patterns"></a>Выражения привязки и шаблоны

*Выражения привязки* — это одна из самых эффективных функций триггеров и привязок. В файле *function.json* и в параметрах функции и коде можно использовать выражения, которые разрешаются в значения из различных источников.

Большинство выражений определяются путем их заключения в фигурные скобки. Например, в функции триггера очереди `{queueTrigger}` разрешается в текст сообщения очереди. Если свойство `path` для выходной привязки большого двоичного объекта — `container/{queueTrigger}`, а функция активируется сообщением очереди `HelloWorld`, создается большой двоичный объект с именем `HelloWorld`.

Типы выражений привязки

* [Параметры приложения](#binding-expressions---app-settings)
* [Имя файла триггера](#binding-expressions---trigger-file-name)
* [Метаданные триггера](#binding-expressions---trigger-metadata)
* [Полезные данные JSON](#binding-expressions---json-payloads)
* [Новый GUID](#binding-expressions---create-guids)
* [Текущая дата и время](#binding-expressions---current-time)

### <a name="binding-expressions---app-settings"></a>Выражения привязки. Параметры приложения

Для управления секретами и строками подключения рекомендуется использовать параметры приложения, а не файлы конфигурации. Это ограничивает доступ к таким секретам и обеспечивает безопасное хранение файлов, таких как *function.json*, в общедоступном репозитории системы управления версиями.

Параметры приложений также удобно использовать при необходимости изменить конфигурации в соответствии со средой. Например, в тестовой среде можно отслеживать другую очередь или контейнер хранилища BLOB-объектов.

Выражения привязки параметров приложения определяются иначе, чем другие выражения привязки. Они заключены в символы процента, а не в фигурные скобки. Например, если путь выходной привязки большого двоичного объекта — `%Environment%/newblob.txt`, а значение параметра приложения `Environment` — `Development`, в контейнере `Development` будет создан большой двоичный объект.

Если функция выполняется локально, значения параметра приложения поступают из файла *local.settings.json*.

Обратите внимание, что свойство `connection` триггеров и привязок является особым случаем и автоматически разрешает значения как параметры приложения без знаков процента. 

Следующий пример представляет собой триггер хранилища очередей Azure. Этот триггер использует параметр приложения `%input-queue-name%`, чтобы определить очередь, для которой он должен срабатывать.

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "%input-queue-name%",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

Тот же подход можно использовать в библиотеках классов:

```csharp
[FunctionName("QueueTrigger")]
public static void Run(
    [QueueTrigger("%input-queue-name%")]string myQueueItem, 
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
}
```

### <a name="binding-expressions---trigger-file-name"></a>Выражения привязки. Имя файла триггера

`path` для триггера большого двоичного объекта может быть шаблоном, который позволяет ссылаться на имя большого двоичного объекта, активирующего триггер, в других привязках и коде функции. Шаблон может также включать критерии фильтрации, которые определяют, какие большие двоичные объекты могут активировать вызов функции.

Например, в следующей привязке триггера большого двоичного объекта шаблон `path` — `sample-images/{filename}`, который создает выражение привязки с именем `filename`:

```json
{
  "bindings": [
    {
      "name": "image",
      "type": "blobTrigger",
      "path": "sample-images/{filename}",
      "direction": "in",
      "connection": "MyStorageConnection"
    },
    ...
```

Затем выражение `filename` можно использовать в выходной привязке для указания имени создаваемого большого двоичного объекта:

```json
    ...
    {
      "name": "imageSmall",
      "type": "blob",
      "path": "sample-images-sm/{filename}",
      "direction": "out",
      "connection": "MyStorageConnection"
    }
  ],
}
```

Код функции получает доступ к тому же значению, используя `filename` в качестве имени параметра:

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, TraceWriter log)  
{
    log.Info($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->

Возможность применять выражения и шаблоны привязки распространяется и на атрибуты в библиотеке классов. В следующем примере параметры конструктора атрибута совпадают со значениями `path` в предыдущих примерах *function.json*: 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    TraceWriter log)
{
    log.Info($"Blob trigger processing: {filename}");
    // ...
}

```

Можно также создать выражения для частей имени файла, таких как расширение. Дополнительные сведения о том, как использовать выражения и шаблоны в строке пути к большому двоичному объекту, см. в статье [Привязки хранилища BLOB-объектов Azure для службы "Функции Azure"](functions-bindings-storage-blob.md).
 
### <a name="binding-expressions---trigger-metadata"></a>Выражения привязки. Метаданные триггера

Помимо полезных данных, предоставляемых триггером (например, содержимое сообщения очереди, инициирующего вызов функции), многие триггеры предоставляют также дополнительные значения метаданных. Эти значения можно использовать в качестве входных параметров в C# и F# или свойств объекта `context.bindings` в JavaScript. 

Например, триггер хранилища очередей Azure поддерживает следующие свойства:

* QueueTrigger (содержимое активирующего сообщения, если строка допустима)
* DequeueCount
* ExpirationTime
* Идентификатор
* InsertionTime
* NextVisibleTime
* PopReceipt

Значения этих метаданных доступны в свойствах файла *function.json*. Предположим, что вы используете триггер очереди, а сообщение в очереди содержит имя большого двоичного объекта, который вам нужно прочитать. В файле *function.json* укажите свойство `queueTrigger` в метаданных большого двоичного объекта `path`, как показано в следующем примере:

```json
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "direction": "in",
      "connection": "MyStorageConnection"
    }
  ]
```

Сведения о свойствах метаданных для каждого триггера приведены в соответствующих статьях документации. Пример см. в разделе [о метаданных триггера очередей](functions-bindings-storage-queue.md#trigger---message-metadata). Документация доступна также на портале на вкладке **Интегрировать** в разделе **Документация** под областью конфигурации привязки.  

### <a name="binding-expressions---json-payloads"></a>Выражения привязки. Полезные данные JSON

Если полезные данные представлены в виде JSON, на его свойства можно ссылаться в конфигурации других привязок в той же функции и ее коде.

В следующем примере показан файл *function.json* для функции веб-перехватчика, который получает имя большого двоичного объекта в JSON: `{"BlobName":"HelloWorld.txt"}`. Входная привязка большого двоичного объекта считывает этот объект, а привязка для вывода HTTP возвращает содержимое большого двоичного объекта в ответе HTTP. Обратите внимание, что входная привязка большого двоичного объекта получает имя такого объекта, обращаясь напрямую к свойству `BlobName` (`"path": "strings/{BlobName}"`).

```json
{
  "bindings": [
    {
      "name": "info",
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson"
    },
    {
      "name": "blobContents",
      "type": "blob",
      "direction": "in",
      "path": "strings/{BlobName.FileName}.{BlobName.Extension}",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

Для этого в C# и F# нужен класс, определяющий поля, которые должны быть десериализованы, как показано в следующем примере:

```csharp
using System.Net;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

В JavaScript десериализация JSON выполняется автоматически.

```javascript
module.exports = function (context, info) {
    if ('BlobName' in info) {
        context.res = {
            body: { 'data': context.bindings.blobContents }
        }
    }
    else {
        context.res = {
            status: 404
        };
    }
    context.done();
}
```

#### <a name="dot-notation"></a>Точечная нотация

Если некоторые свойства в полезных данных JSON являются объектами со свойствами, к ним можно обратиться напрямую с помощью точечной нотации. Например, ваш JSON выглядит следующим образом:

```json
{"BlobName": {
  "FileName":"HelloWorld",
  "Extension":"txt"
  }
}
```

На `FileName` можно непосредственно ссылаться, используя формат `BlobName.FileName`. С этим форматом JSON свойство `path` из предыдущего примера будет выглядеть следующим образом:

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

В C# потребовалось бы два класса:

```csharp
public class BlobInfo
{
    public BlobName BlobName { get; set; }
}
public class BlobName
{
    public string FileName { get; set; }
    public string Extension { get; set; }
}
```

### <a name="binding-expressions---create-guids"></a>Выражения привязки. Создание глобальных уникальных идентификаторов

Выражение привязки `{rand-guid}` создает идентификатор GUID. Следующий путь к большому двоичному объекту в файле `function.json` создает большой двоичный объект с именем следующего вида: *50710cb5-84b9-4d87-9d83-a03d6976a682.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="binding-expressions---current-time"></a>Выражения привязки. Текущее время

Выражение привязки `DateTime` разрешается в `DateTime.UtcNow`. Следующий путь к большому двоичному объекту в файле `function.json` создает большой двоичный объект с именем следующего вида: *2018-02-16T17-59-55Z.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```

## <a name="binding-at-runtime"></a>Привязка во время выполнения

В C# и других языках .NET можно использовать шаблон императивной привязки, которая отличается от декларативной привязки в файле *function.json* или в атрибутах. Императивную привязку удобно использовать, когда параметры привязки должны вычисляться не при проектировании, а во время выполнения. Дополнительные сведения см. в [справочнике разработчика C#](functions-dotnet-class-library.md#binding-at-runtime) и [справочнике разработчика скриптов C#](functions-reference-csharp.md#binding-at-runtime).

## <a name="functionjson-file-schema"></a>Схема файла function.json

Схему файла *function.json* можно найти здесь: [http://json.schemastore.org/function](http://json.schemastore.org/function).

## <a name="handling-binding-errors"></a>Обработка ошибок привязки

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

Ссылки на соответствующие разделы с описанием ошибок различных служб, поддерживаемых в Функциях Azure, см. в разделе [Binding error codes](functions-bindings-error-pages.md#binding-error-codes) (Коды ошибок привязки) статьи [Azure Functions error handling](functions-bindings-error-pages.md) (Обработка ошибок службы "Функции Azure").  

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о конкретной привязке см. в следующих статьях:

- [HTTP и вызовы webhook](functions-bindings-http-webhook.md)
- [Таймер](functions-bindings-timer.md)
- [Хранилище очередей](functions-bindings-storage-queue.md)
- [Хранилище BLOB-объектов](functions-bindings-storage-blob.md)
- [Хранилище таблиц](functions-bindings-storage-table.md)
- [Концентратор событий](functions-bindings-event-hubs.md)
- [Служебная шина](functions-bindings-service-bus.md)
- [База данных Azure Cosmos](functions-bindings-cosmosdb.md)
- [Microsoft Graph](functions-bindings-microsoft-graph.md)
- [SendGrid](functions-bindings-sendgrid.md)
- [Twilio](functions-bindings-twilio.md)
- [Центры уведомлений](functions-bindings-notification-hubs.md)
- [Мобильные приложения](functions-bindings-mobile-apps.md)
- [Внешний файл](functions-bindings-external-file.md)
