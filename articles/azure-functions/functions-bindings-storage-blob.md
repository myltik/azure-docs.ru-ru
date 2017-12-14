---
title: "Привязки хранилища BLOB-объектов Azure для службы \"Функции Azure\""
description: "Узнайте, как использовать триггеры и привязки хранилища BLOB-объектов Azure в службе \"Функции Azure\"."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "функции azure, функции, обработка событий, динамические вычисления, независимая архитектура"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/27/2017
ms.author: glenga
ms.openlocfilehash: 576167502fdb77c98c449dc5a448323dc5b23f35
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/29/2017
---
# <a name="azure-blob-storage-bindings-for-azure-functions"></a>Привязки хранилища BLOB-объектов Azure для службы "Функции Azure"

В этой статье рассматривается работа с привязками хранилища BLOB-объектов Azure в службе "Функции Azure". Служба "Функции Azure" поддерживает привязки триггера, а также входные и выходные привязки для больших двоичных объектов.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> [Учетные записи хранения только для больших двоичных объектов](../storage/common/storage-create-storage-account.md#blob-storage-accounts) не поддерживаются. Триггерам и привязкам хранилища BLOB-объектов требуется учетная запись хранения общего назначения. 

## <a name="trigger"></a>Триггер

Используйте триггер хранилища BLOB-объектов, чтобы запустить функцию при обнаружении нового или обновленного большого двоичного объекта. Содержимое BLOB-объекта предоставляется в качестве входных данных функции.

> [!NOTE]
> Если используется триггер BLOB-объекта в плане потребления, то после того как приложение-функция стало неактивным, обработка новых BLOB-объектов может осуществляться с задержкой до 10 минут. После запуска приложения-функции BLOB-объекты обрабатываются немедленно. Во избежание этой начальной задержки рассмотрите один из следующих вариантов:
> - Используйте план службы приложений с включенной функцией AlwaysOn.
> - Используйте другой механизм для активации обработки большого двоичного объекта, например сообщение очереди, содержащее имя большого двоичного объекта. Пример см. в разделе этой статьи с [образцом входных и выходных привязок большого двоичного объекта](#input--output---example).

## <a name="trigger---example"></a>Пример триггера

Языковой пример см. в разделах:

* [Предкомпилированный код C#](#trigger---c-example)
* [Сценарий C#](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Пример C# в триггере

В следующем примере показан [предкомпилированный код C#](functions-dotnet-class-library.md), который делает запись в журнал при добавлении или обновлении большого двоичного объекта в контейнере `samples-workitems`.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Дополнительные сведения об атрибуте `BlobTrigger` см. в разделе [Атрибуты триггера для предкомпилированного кода C#](#trigger---attributes-for-precompiled-c).

### <a name="trigger---c-script-example"></a>Пример скрипта C# в триггере

В следующем примере показана привязка триггера большого двоичного объекта в файле *function.json* и коде [скрипта C#](functions-reference-csharp.md), который использует привязку. Функция делает запись в журнал при добавлении или обновлении большого двоичного объекта в контейнере `samples-workitems`.

Данные привязки в файле *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

В разделе [Конфигурация](#trigger---configuration) описываются эти свойства.

Ниже приведен код скрипта C#, который выполняет привязку к `Stream`.

```cs
public static void Run(Stream myBlob, TraceWriter log)
{
   log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Ниже приведен код скрипта C#, который выполняет привязку к `CloudBlockBlob`.

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

### <a name="trigger---javascript-example"></a>Пример JavaScript в триггере

В следующем примере показана привязка триггера большого двоичного объекта в файле *function.json* и [коде JavaScript] (functions-reference-node.md), который использует привязку. Функция делает запись в журнал при добавлении или обновлении большого двоичного объекта в контейнере `samples-workitems`.

Ниже показан файл *function.json*.

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

В разделе [Конфигурация](#trigger---configuration) описываются эти свойства.

Ниже показан код JavaScript.

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

## <a name="trigger---attributes"></a>Атрибуты триггера

В функциях [предкомпилированного кода C#](functions-dotnet-class-library.md) используйте следующие атрибуты для настройки триггера большого двоичного объекта:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobTriggerAttribute.cs), определенный в пакете NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs).

  Конструктор атрибута принимает строку пути, которая указывает на контейнер для просмотра, и при необходимости [шаблон имени большого двоичного объекта](#trigger---blob-name-patterns). Ниже приведен пример:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image, 
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Чтобы указать учетную запись хранения, которую нужно использовать, можно задать свойство `Connection`, как показано в следующем примере.

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image, 
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Полный пример см. в разделе [Пример C# в триггере](#trigger---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs), определенный в пакете NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs).

  Предоставляет еще один способ указать используемую учетную запись хранения. Конструктор принимает имя параметра приложения, содержащего строку подключения к службе хранилища. Атрибут может применяться на уровне класса, метода или параметра. Ниже показан пример уровня класса и метода.

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("BlobTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ....
  }
  ```

Используемая учетная запись хранения определяется в следующем порядке:

* Свойство `BlobTrigger` атрибута `Connection`.
* Атрибут `StorageAccount`, примененный к тому же параметру, что и `BlobTrigger`.
* Атрибут `StorageAccount`, примененный к функции.
* Атрибут `StorageAccount`, примененный к классу.
* Учетная запись хранения по умолчанию для приложения-функции (параметр приложения AzureWebJobsStorage).

## <a name="trigger---configuration"></a>Конфигурация триггера

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `BlobTrigger`.

|свойство function.json | Свойство атрибута |Описание|
|---------|---------|----------------------|
|**type** | Недоступно | Нужно задать значение `blobTrigger`. Это свойство задается автоматически при создании триггера на портале Azure.|
|**direction** | Недоступно | Нужно задать значение `in`. Это свойство задается автоматически при создании триггера на портале Azure. Исключения приведены в этом [разделе](#trigger---usage). |
|**name** | Недоступно | Имя переменной, представляющей большой двоичный объект в коде функции. | 
|**path** | **BlobPath** |Контейнер для мониторинга.  Может быть [шаблоном имени большого двоичного объекта](#trigger-blob-name-patterns). | 
|**подключение** | **Connection** | Имя параметра приложения, содержащего строку подключения к службе хранилища, используемой для этой привязки. Если имя параметра приложения начинается с AzureWebJobs, можно указать только остальную часть имени. Например, если задать для `connection` значение MyStorage, среда выполнения службы "Функции" будет искать параметр приложения с именем AzureWebJobsMyStorage. Если оставить строку `connection` пустой, среда выполнения службы "Функции" будет использовать строку подключения к службе хранилища по умолчанию для параметра приложения с именем `AzureWebJobsStorage`.<br><br>Строка подключения необходима для учетной записи хранения общего назначения, а не [учетной записи хранения только для больших двоичных объектов](../storage/common/storage-create-storage-account.md#blob-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Использование триггера

В коде и скрипте C# для доступа к данным большого двоичного объекта используйте параметр метода, например `Stream paramName`. В скрипте C# `paramName` — это значение, заданное в свойстве `name` файла *function.json*. Вы можете выполнить привязку к одному из следующих типов:

* `TextReader`
* `Stream`
* `ICloudBlob` (требует направления привязки inout в *function.json*);
* `CloudBlockBlob` (требует направления привязки inout в *function.json*);
* `CloudPageBlob` (требует направления привязки inout в *function.json*);
* `CloudAppendBlob` (требует направления привязки inout в *function.json*).

Как уже отмечалось выше, некоторые из этих типов требуют направления привязки `inout` в файле *function.json*. Это направление не поддерживается стандартным редактором на портале Azure, поэтому необходимо использовать расширенный редактор.

Если ожидаются текстовые большие двоичные объекты, можно выполнить привязку к типу `string`. Это рекомендуется делать только в том случае, если BLOB-объект имеет небольшой размер, так как в память загружается все содержимое BLOB-объекта. Как правило, предпочтительнее использовать тип `Stream` или `CloudBlockBlob`.

В JavaScript доступ к данным входного большого двоичного объекта можно получить, используя `context.bindings.<name>`.

## <a name="trigger---blob-name-patterns"></a>Шаблоны имени большого двоичного объекта в триггере

Вы можете указать шаблон имени большого двоичного объекта в свойстве `path` в файле *function.json* или в конструкторе атрибута `BlobTrigger`. Шаблон имени может быть [выражением фильтра или привязки](functions-triggers-bindings.md#binding-expressions-and-patterns).

### <a name="filter-on-blob-name"></a>Фильтрация по имени большого двоичного объекта

Следующий пример активируется только для больших двоичных объектов в контейнере `input`, который начинается со строки original-.

```json
"path": "input/original-{name}",
```
 
Если *original-Blob1.txt* — имя большого двоичного объекта, значением переменной `name` в коде функции будет `Blob1`.

### <a name="filter-on-file-type"></a>Фильтрация по типу файла

Далее приведены примеры триггеров только из файлов *PNG*:

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>Фильтрация по фигурным скобкам в именах файлов

Чтобы найти фигурные скобки в именах файлов, экранируйте скобки, используя две фигурные скобки. В следующем примере показана фильтрация по большим двоичным объектам, имена которых содержат фигурные скобки:

```json
"path": "images/{{20140101}}-{name}",
```

Если *{20140101}-soundfile.mp3* — имя большого двоичного объекта, значением переменной `name` в коде функции будет *soundfile.mp3*. 

### <a name="get-file-name-and-extension"></a>Получение имени и расширения файла

В следующем примере показано, как выполнить привязку имени и расширения файла большого двоичного объекта по отдельности:

```json
"path": "input/{blobname}.{blobextension}",
```
Если большой двоичный объект имеет имя *original-Blob1.txt*, он передает в код функции переменные `blobname` и `blobextension` со значениями *original-Blob1* и *txt* соответственно.

## <a name="trigger---metadata"></a>Метаданные триггера

Триггер BLOB-объектов предоставляет несколько свойств метаданных. Эти свойства можно использовать как часть выражений привязки в других привязках или как параметры в коде. Эти значения имеют ту же семантику, что и тип [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob?view=azure-dotnet).


|Свойство  |Тип  |Описание  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Путь к большому двоичному объекту, активирующему триггер.|
|`Uri`|`System.Uri`|Код URI BLOB-объекта для основного расположения.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties)|Системные свойства BLOB-объекта. |
|`Metadata` |`IDictionary<string,string>`|Определяемые пользователем метаданные для BLOB-объекта.|

## <a name="trigger---blob-receipts"></a>Уведомления о получении большого двоичного объекта в триггере

Среда выполнения Функций Azure гарантирует, что для одного и того же нового или обновленного BLOB-объекта функция активации BLOB-объекта будет вызываться только один раз. Для определения того, обработана ли версия этого BLOB-объекта, сохраняются *уведомления о получении BLOB-объекта*.

Функции Azure сохраняют уведомления о получении BLOB-объектов в контейнере с именем *azure-webjobs-hosts* в учетной записи хранения Azure для приложения-функции (указывается с помощью параметра приложения `AzureWebJobsStorage`). Уведомление о получении большого двоичного объекта содержит следующую информацию:

* активированная функция (*&lt;имя_приложения-функции>*.Functions.*&lt;имя_функции>*, например: MyFunctionApp.Functions.CopyBlob);
* имя контейнера;
* тип большого двоичного объекта (BlockBlob или PageBlob);
* имя большого двоичного объекта;
* ETag (идентификатор версии больших двоичных объектов, например 0x8D1DC6E70A277EF).

Чтобы выполнить принудительную повторную обработку большого двоичного объекта, удалите уведомление о получении этого большого двоичного объекта из контейнера *azure-webjobs-hosts* вручную.

## <a name="trigger---poison-blobs"></a>Триггеры подозрительных больших двоичных объектов

При сбое функции триггера BLOB-объекта Функции Azure по умолчанию выполняют ее для этого BLOB-объекта еще 5 раз. 

В случае сбоя после 5 попыток запуска Функции Azure добавляют сообщение в очередь службы хранилища с именем *webjobs-blobtrigger-poison*. Сообщением очереди для подозрительных больших двоичных объектов является объект JSON, содержащий следующие свойства:

* FunctionId (идентификатор функции в формате *&lt;имя_приложения-функции>*.Functions.*&lt;имя_функции>*);
* BlobType (BlockBlob или PageBlob);
* ContainerName;
* BlobName
* ETag (идентификатор версии BLOB-объектов, например 0x8D1DC6E70A277EF)

## <a name="trigger---polling-for-large-containers"></a>Опрос для больших контейнеров в триггере

Если отслеживаемый контейнер BLOB-объектов содержит более 10 000 BLOB-объектов, среда выполнения Функций проверяет файлы журналов, чтобы найти новые или измененные BLOB-объекты. Этот процесс может привести к задержкам. После создания большого двоичного объекта функция может не вызываться несколько минут или даже дольше. Кроме того, [журналы службы хранилища создаются по принципу лучшего из возможного](/rest/api/storageservices/About-Storage-Analytics-Logging), Регистрация всех событий не гарантируется. В некоторых случаях журналы могут пропускаться. Если требуется повысить скорость или надежность обработки BLOB-объектов, при создании BLOB-объекта рекомендуем создать [сообщение очереди](../storage/queues/storage-dotnet-how-to-use-queues.md). Затем для обработки BLOB-объекта используйте [триггер очереди](functions-bindings-storage-queue.md) вместо триггера BLOB-объекта. Другой вариант — использовать службу "Сетка событий". Дополнительные сведения см. в руководстве [Автоматическое изменение размера переданных изображений с помощью службы "Сетка событий"](../event-grid/resize-images-on-storage-blob-upload-event.md).

## <a name="input--output"></a>Использование входной и выходной привязок

Используйте входные и выходные привязки хранилища BLOB-объектов для чтения и записи больших двоичных объектов.

## <a name="input--output---example"></a>Пример входных и выходных данных

Языковой пример см. в разделах:

* [Предкомпилированный код C#](#input--output---c-example)
* [Сценарий C#](#input--output---c-script-example)
* [JavaScript](#input--output---javascript-example)

### <a name="input--output---c-example"></a>Пример входных и выходных данных C#

Ниже приведен пример функции [на базе предкомпилированного кода C#](functions-dotnet-class-library.md), которая использует триггер BLOB-объекта и две выходных привязки BLOB-объектов. Эта функция активируется путем создания большого двоичного объекта образа в контейнере *sample-images*. Она создает небольшие и средние копии большого двоичного объекта образа. 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall, 
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    var imageBuilder = ImageResizer.ImageBuilder.Current;
    var size = imageDimensionsTable[ImageSize.Small];

    imageBuilder.Build(image, imageSmall,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);

    image.Position = 0;
    size = imageDimensionsTable[ImageSize.Medium];

    imageBuilder.Build(image, imageMedium,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);
}

public enum ImageSize { ExtraSmall, Small, Medium }

private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
    { ImageSize.ExtraSmall, (320, 200) },
    { ImageSize.Small,      (640, 400) },
    { ImageSize.Medium,     (800, 600) }
};
```        

### <a name="input--output---c-script-example"></a>Пример входных и выходных данных скрипта C#

В примере ниже показаны входная и выходная привязки BLOB-объектов в файле *function.json* и код [скрипта C#](functions-reference-csharp.md), который использует эти привязки. Функция копирует большой двоичный объект. Она активируется сообщением очереди, содержащим имя копируемого большого двоичного объекта. Новый большой двоичный объект получает имя *{originalblobname}-Copy*.

В файле *function.json* свойство метаданных `queueTrigger` используется для указания имени большого двоичного объекта в свойствах `path`:

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
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

В разделе [Конфигурация](#input--output---configuration) описываются эти свойства.

Ниже приведен код скрипта C#.

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="input--output---javascript-example"></a>Пример входных и выходных данных JavaScript

В следующем примере показаны входная и выходная привязки BLOB-объекта в файле *function.json* и [код JavaScript] (functions-reference-node.md), который использует эти привязки. Эта функция копирует большой двоичный объект. Она активируется сообщением очереди, содержащим имя копируемого большого двоичного объекта. Новый большой двоичный объект получает имя *{originalblobname}-Copy*.

В файле *function.json* свойство метаданных `queueTrigger` используется для указания имени большого двоичного объекта в свойствах `path`:

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
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

В разделе [Конфигурация](#input--output---configuration) описываются эти свойства.

Ниже показан код JavaScript.

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

## <a name="input--output---attributes"></a>Атрибуты входных и выходных данных

Для функций [предкомпилированного кода C#](functions-dotnet-class-library.md) используйте атрибут [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs), определенный в пакете NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs).

Конструктор атрибута принимает путь к большому двоичному объекту и параметр `FileAccess`, указывающий на чтение или запись, как показано в следующем примере:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

Чтобы указать учетную запись хранения, которую нужно использовать, можно задать свойство `Connection`, как показано в следующем примере.

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

Полный пример см. в разделе [Пример входных и выходных данных C#](#input--output---c-example).

Чтобы указать учетную запись хранения на уровне класса, метода или параметра, можно использовать атрибут `StorageAccount`. Дополнительные сведения см. в разделе [Атрибуты триггера для предкомпилированного кода C#](#trigger---attributes-for-precompiled-c).

## <a name="input--output---configuration"></a>Входная и выходная конфигурация

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `Blob`.

|свойство function.json | Свойство атрибута |Описание|
|---------|---------|----------------------|
|**type** | Недоступно | Нужно задать значение `blob`. |
|**direction** | Недоступно | Входной привязке должно быть присвоено значение `in`, а выходной — out. Исключения приведены в этом [разделе](#input--output---usage). |
|**name** | Недоступно | Имя переменной, представляющей большой двоичный объект в коде функции.  Задайте значение `$return`, ссылающееся на возвращаемое значение функции.|
|**path** |**BlobPath** | Путь к BLOB-объекту. | 
|**подключение** |**Connection**| Имя параметра приложения, содержащего строку подключения к службе хранилища, используемой для этой привязки. Если имя параметра приложения начинается с AzureWebJobs, можно указать только остальную часть имени. Например, если задать для `connection` значение MyStorage, среда выполнения службы "Функции" будет искать параметр приложения с именем AzureWebJobsMyStorage. Если оставить строку `connection` пустой, среда выполнения службы "Функции" будет использовать строку подключения к службе хранилища по умолчанию для параметра приложения с именем `AzureWebJobsStorage`.<br><br>Строка подключения необходима для учетной записи хранения общего назначения, а не [учетной записи хранения только для больших двоичных объектов](../storage/common/storage-create-storage-account.md#blob-storage-accounts).|
|Недоступно | **Access** | Указывает, какая операция будет выполняться (запись или чтение). |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input--output---usage"></a>Использование входной и выходной привязки

В предкомпилированном коде и скрипте C# получите доступ к большому двоичному объекту с помощью параметра метода (например, `Stream paramName`). В скрипте C# `paramName` — это значение, заданное в свойстве `name` файла *function.json*. Вы можете выполнить привязку к одному из следующих типов:

* `out string`
* `TextWriter` 
* `TextReader`
* `Stream`
* `ICloudBlob` (требует направления привязки inout в *function.json*);
* `CloudBlockBlob` (требует направления привязки inout в *function.json*);
* `CloudPageBlob` (требует направления привязки inout в *function.json*);
* `CloudAppendBlob` (требует направления привязки inout в *function.json*).

Как уже отмечалось выше, некоторые из этих типов требуют направления привязки `inout` в файле *function.json*. Это направление не поддерживается стандартным редактором на портале Azure, поэтому необходимо использовать расширенный редактор.

При чтении текста больших двоичных объектов можно выполнить привязку к типу `string`. Этот тип рекомендуется использовать, только если большой двоичный объект имеет небольшой размер, так как в память загружается все содержимое большого двоичного объекта. Как правило, предпочтительнее использовать тип `Stream` или `CloudBlockBlob`.

В JavaScript получите доступ к данным больших двоичных объектов с помощью `context.bindings.<name>`.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Создание функции, активируемой хранилищем BLOB-объектов Azure](functions-create-storage-blob-triggered-function.md)

> [!div class="nextstepaction"]
> [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)
