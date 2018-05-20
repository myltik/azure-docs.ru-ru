---
title: Привязки хранилища BLOB-объектов Azure для службы "Функции Azure"
description: Узнайте, как использовать триггеры и привязки хранилища BLOB-объектов Azure в службе "Функции Azure".
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
ms.date: 02/12/2018
ms.author: tdykstra
ms.openlocfilehash: a3d1ca210d490e7a8c634fbfb2a2e11f4e82fae4
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2018
---
# <a name="azure-blob-storage-bindings-for-azure-functions"></a>Привязки хранилища BLOB-объектов Azure для службы "Функции Azure"

В этой статье рассматривается работа с привязками хранилища BLOB-объектов Azure в службе "Функции Azure". Служба "Функции Azure" поддерживает привязки триггера, а также входные и выходные привязки для больших двоичных объектов. В этой статье каждой из следующих привязок посвящен раздел:

* [триггер большого двоичного объекта](#trigger);
* [входная привязка большого двоичного объекта](#input);
* [выходная привязка большого двоичного объекта](#output).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>Пакеты

Привязки большого двоичного объекта доступны в пакете NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs). Исходный код для пакета находится в репозитории GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

> [!NOTE]
> Используйте триггер службы "Сетка событий" вместо триггера хранилища BLOB-объектов для учетных записей хранения только для больших двоичных объектов, чтобы обеспечить высокий уровень масштабирования или во избежание задержек при холодном запуске. Дополнительные сведения приведены в разделе **Триггер**. 

## <a name="trigger"></a>Триггер

Триггер хранилища BLOB-объектов запускает функцию при обнаружении нового или обновленного большого двоичного объекта. Содержимое BLOB-объекта предоставляется в качестве входных данных функции.

[Триггер службы "Сетка событий"](functions-bindings-event-grid.md) имеет встроенную поддержку [событий больших двоичных объектов](../storage/blobs/storage-blob-event-overview.md). Кроме того, его можно использовать для запуска функции при обнаружении нового или обновленного большого двоичного объекта. Пример см. в руководстве [Автоматическое изменение размера переданных изображений с помощью службы "Сетка событий"](../event-grid/resize-images-on-storage-blob-upload-event.md).

Используйте службу "Сетка событий" вместо триггера хранилища BLOB-объектов для следующих сценариев:

* Учетные записи хранения только для больших двоичных объектов.
* Большой масштаб.
* Задержка при холодном запуске.

### <a name="blob-only-storage-accounts"></a>Учетные записи хранения только для больших двоичных объектов

[Учетные записи хранения только для больших двоичных объектов](../storage/common/storage-create-storage-account.md#blob-storage-accounts) поддерживаются для входных и выходных привязок больших двоичных объектов, а не для триггеров большого двоичного объекта. Для триггеров хранилища BLOB-объектов требуется учетная запись хранения общего назначения.

### <a name="high-scale"></a>Большой масштаб

Большой масштаб можно определить как контейнеры, содержащие более 100 000 больших двоичных объектов, или как учетные записи хранения, в которых выполняется более 100 обновлений больших двоичных объектов в секунду.

### <a name="cold-start-delay"></a>Задержка при холодном запуске

Если ваше приложение-функция выполняется в рамках плана потребления, обработка новых больших двоичных объектов может осуществляться с задержкой до 10 минут, если приложение-функция стало неактивным. Во избежание подобных задержек при холодном запуске можно переключиться на план службы приложений с включенной функцией Always On или использовать другой тип триггера.

### <a name="queue-storage-trigger"></a>Триггер хранилища очередей

Помимо службы "Сетка событий" для обработки больших двоичных объектов можно использовать триггер хранилища очередей, но он не имеет встроенной поддержки для событий большого двоичного объекта. При создании или обновлении больших двоичных объектов вам придется создавать сообщения очереди. Пример см. в [этом разделе](#input---example).

## <a name="trigger---example"></a>Пример триггера

Языковой пример см. в разделах:

* [C#](#trigger---c-example)
* [Скрипт C# (CSX)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Пример C# в триггере

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая добавляет запись в журнал при добавлении или обновлении большого двоичного объекта в контейнере `samples-workitems`.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Строка `{name}` в пути триггера большого двоичного объекта `samples-workitems/{name}` создает [выражение привязки](functions-triggers-bindings.md#binding-expressions-and-patterns), которое можно использовать в коде функции для получения доступа к имени файла большого двоичного объекта, запускающего триггер. Дополнительные сведения см. в разделе [Шаблоны имени большого двоичного объекта](#trigger---blob-name-patterns).

Дополнительные сведения об атрибуте `BlobTrigger` см. в разделе [Атрибуты триггера для предкомпилированного кода C#](#trigger---attributes).

### <a name="trigger---c-script-example"></a>Пример скрипта C# в триггере

В следующем примере показана привязка триггера большого двоичного объекта в файле *function.json* и коде [скрипта C# (CSX)](functions-reference-csharp.md), который использует привязку. Функция делает запись в журнал при добавлении или обновлении большого двоичного объекта в контейнере `samples-workitems`.

Данные привязки в файле *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Строка `{name}` в пути триггера большого двоичного объекта `samples-workitems/{name}` создает [выражение привязки](functions-triggers-bindings.md#binding-expressions-and-patterns), которое можно использовать в коде функции для получения доступа к имени файла большого двоичного объекта, запускающего триггер. Дополнительные сведения см. в разделе [Шаблоны имени большого двоичного объекта](#trigger---blob-name-patterns).

Дополнительные сведения о свойствах файла *function.json* см. в разделе [Конфигурация](#trigger---configuration).

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

В следующем примере показана привязка триггера большого двоичного объекта в файле *function.json* и [коде JavaScript](functions-reference-node.md), который использует привязку. Функция делает запись в журнал при добавлении или обновлении большого двоичного объекта в контейнере `samples-workitems`.

Ниже показан файл *function.json*.

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Строка `{name}` в пути триггера большого двоичного объекта `samples-workitems/{name}` создает [выражение привязки](functions-triggers-bindings.md#binding-expressions-and-patterns), которое можно использовать в коде функции для получения доступа к имени файла большого двоичного объекта, запускающего триггер. Дополнительные сведения см. в разделе [Шаблоны имени большого двоичного объекта](#trigger---blob-name-patterns).

Дополнительные сведения о свойствах файла *function.json* см. в разделе [Конфигурация](#trigger---configuration).

Ниже показан код JavaScript.

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

## <a name="trigger---attributes"></a>Атрибуты триггера

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте следующие атрибуты для настройки триггера большого двоичного объекта:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobTriggerAttribute.cs)

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

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

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

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**type** | Недоступно | Нужно задать значение `blobTrigger`. Это свойство задается автоматически при создании триггера на портале Azure.|
|**direction** | Недоступно | Нужно задать значение `in`. Это свойство задается автоматически при создании триггера на портале Azure. Исключения приведены в этом [разделе](#trigger---usage). |
|**name** | Недоступно | Имя переменной, представляющей большой двоичный объект в коде функции. | 
|**path** | **BlobPath** |Контейнер для мониторинга.  Может быть [шаблоном имени большого двоичного объекта](#trigger-blob-name-patterns). | 
|**подключение** | **Connection** | Имя параметра приложения, содержащего строку подключения к службе хранилища, используемой для этой привязки. Если имя параметра приложения начинается с AzureWebJobs, можно указать только остальную часть имени. Например, если задать для `connection` значение MyStorage, среда выполнения службы "Функции" будет искать параметр приложения с именем AzureWebJobsMyStorage. Если оставить строку `connection` пустой, среда выполнения службы "Функции" будет использовать строку подключения к службе хранилища по умолчанию для параметра приложения с именем `AzureWebJobsStorage`.<br><br>Строка подключения необходима для учетной записи хранения общего назначения, а не [учетной записи хранения только для больших двоичных объектов](../storage/common/storage-create-storage-account.md#blob-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Использование триггера

В C# и скрипте C# для большого двоичного объекта, запускающего триггер, можно использовать параметры следующих типов:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* объект POCO, сериализуемый как JSON;
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> Требует привязки inout `direction` в файле *function.json* или `FileAccess.ReadWrite` в библиотеке классов C#.

Привязку к `string`, `Byte[]` или POCO рекомендуется использовать, только если большой двоичный объект имеет небольшой размер, так как в память загружается все содержимое большого двоичного объекта. Как правило, предпочтительнее использовать тип `Stream` или `CloudBlockBlob`. Дополнительные сведения см. в разделе о [параллелизме и использовании памяти](#trigger---concurrency-and-memory-usage) далее в этой статье.

В JavaScript доступ к данным входного большого двоичного объекта можно получить, используя `context.bindings.<name from function.json>`.

## <a name="trigger---blob-name-patterns"></a>Шаблоны имени большого двоичного объекта в триггере

Вы можете указать шаблон имени большого двоичного объекта в свойстве `path` в файле *function.json* или в конструкторе атрибута `BlobTrigger`. Шаблон имени может быть [выражением фильтра или привязки](functions-triggers-bindings.md#binding-expressions-and-patterns). Примеры приведены в следующих разделах.

### <a name="get-file-name-and-extension"></a>Получение имени и расширения файла

В следующем примере показано, как выполнить привязку имени и расширения файла большого двоичного объекта по отдельности:

```json
"path": "input/{blobname}.{blobextension}",
```
Если большой двоичный объект имеет имя *original-Blob1.txt*, он передает в код функции переменные `blobname` и `blobextension` со значениями *original-Blob1* и *txt* соответственно.

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

## <a name="trigger---metadata"></a>Метаданные триггера

Триггер BLOB-объектов предоставляет несколько свойств метаданных. Эти свойства можно использовать как часть выражений привязки в других привязках или как параметры в коде. Эти значения имеют ту же семантику, что и тип [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob?view=azure-dotnet).

|Свойство  |type  |ОПИСАНИЕ  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Путь к большому двоичному объекту, активирующему триггер.|
|`Uri`|`System.Uri`|Код URI BLOB-объекта для основного расположения.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties)|Системные свойства BLOB-объекта. |
|`Metadata` |`IDictionary<string,string>`|Определяемые пользователем метаданные для BLOB-объекта.|

Например, следующие примеры скрипта C# и JavaScript записывают путь в большой двоичный объект, активирующий триггер, вместе с контейнером:

```csharp
public static void Run(string myBlob, string blobTrigger, TraceWriter log)
{
    log.Info($"Full blob path: {blobTrigger}");
} 
```

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

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

## <a name="trigger---concurrency-and-memory-usage"></a>Триггеры параллелизма и использования памяти

Триггер больших двоичных объектов использует очередь для внутренних целей, поэтому максимальное число параллельных вызовов функции регулируется [конфигурацией очередей в host.json](functions-host-json.md#queues). При настройках по умолчанию параллелизм ограничен 24 вызовами. Это ограничение применяется отдельно к каждой функции, которая использует триггер больших двоичных объектов.

[План потребления](functions-scale.md#how-the-consumption-plan-works) ограничивает для приложения-функции на одной виртуальной машине объем памяти в 1,5 ГБ. Память используется каждым параллельно выполняющимся экземпляром функции и самой средой выполнения службы "Функции". Если функция, запускаемая триггером больших двоичных объектов, загружает весь такой объект в память, максимальный объем памяти, используемый этой функцией только для больших двоичных объектов, составит 24 * максимальный размер такого объекта. Например, приложение-функция с тремя функциями, запускаемыми триггером больших двоичных объектов, с настройками по умолчанию будет иметь максимальную степень параллелизма на одну виртуальную машину, равную 3 * 24 = 72 вызова функций.

Функции JavaScript загружают весь большой двоичный объект в память, а функции C# делают это в случае привязки к `string`, `Byte[]` или POCO.

## <a name="trigger---polling"></a>Триггер опроса

Если отслеживаемый контейнер BLOB-объектов содержит более 10 000 BLOB-объектов, среда выполнения Функций проверяет файлы журналов, чтобы найти новые или измененные BLOB-объекты. Этот процесс может привести к задержкам. После создания большого двоичного объекта функция может не вызываться несколько минут или даже дольше. Кроме того, [журналы службы хранилища создаются по принципу лучшего из возможного](/rest/api/storageservices/About-Storage-Analytics-Logging), Регистрация всех событий не гарантируется. В некоторых случаях журналы могут пропускаться. Если требуется повысить скорость или надежность обработки BLOB-объектов, при создании BLOB-объекта рекомендуем создать [сообщение очереди](../storage/queues/storage-dotnet-how-to-use-queues.md). Затем для обработки BLOB-объекта используйте [триггер очереди](functions-bindings-storage-queue.md) вместо триггера BLOB-объекта. Другой вариант — использовать службу "Сетка событий". Дополнительные сведения см. в руководстве [Автоматическое изменение размера переданных изображений с помощью службы "Сетка событий"](../event-grid/resize-images-on-storage-blob-upload-event.md).

## <a name="input"></a>Входные данные

Для чтения больших двоичных объектов используйте входную привязку хранилища BLOB-объектов.

## <a name="input---example"></a>Пример входных данных

Языковой пример см. в разделах:

* [C#](#input---c-example)
* [Скрипт C# (CSX)](#input---c-script-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-example"></a>Пример входных данных C#

Ниже приведен пример [функции C#](functions-dotnet-class-library.md), которая использует триггер очереди и входную привязку большого двоичного объекта. Сообщение в очереди содержит имя большого двоичного объекта, а функция записывает в журнал размер большого двоичного объекта.

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```        

### <a name="input---c-script-example"></a>Пример входных данных сценария C#

<!--Same example for input and output. -->

В примере ниже показаны входная и выходная привязки больших двоичных объектов в файле *function.json*, а также код [скрипта C# (CSX)](functions-reference-csharp.md), который использует эти привязки. Функция копирует большой двоичный объект. Она активируется сообщением очереди, содержащим имя копируемого большого двоичного объекта. Новый большой двоичный объект получает имя *{originalblobname}-Copy*.

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

В разделе [Конфигурация](#input---configuration) описываются эти свойства.

Ниже приведен код скрипта C#.

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="input---javascript-example"></a>Пример входных данных JavaScript

<!--Same example for input and output. -->

В примере ниже показаны входная и выходная привязки BLOB-объектов в файле *function.json*, а также код [скрипта JavaScript](functions-reference-node.md), который использует эти привязки. Эта функция копирует большой двоичный объект. Она активируется сообщением очереди, содержащим имя копируемого большого двоичного объекта. Новый большой двоичный объект получает имя *{originalblobname}-Copy*.

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

В разделе [Конфигурация](#input---configuration) описываются эти свойства.

Ниже показан код JavaScript.

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

## <a name="input---attributes"></a>Входные атрибуты

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs).

Конструктор атрибута принимает путь к большому двоичному объекту и параметр `FileAccess`, указывающий на чтение или запись, как показано в следующем примере:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}

```

Чтобы указать учетную запись хранения, которую нужно использовать, можно задать свойство `Connection`, как показано в следующем примере.

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read, Connection = "StorageConnectionAppSetting")] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

Чтобы указать учетную запись хранения на уровне класса, метода или параметра, можно использовать атрибут `StorageAccount`. Дополнительные сведения см. в разделе [Атрибуты триггера для предкомпилированного кода C#](#trigger---attributes).

## <a name="input---configuration"></a>Входная конфигурация

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `Blob`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**type** | Недоступно | Нужно задать значение `blob`. |
|**direction** | Недоступно | Нужно задать значение `in`. Исключения приведены в этом [разделе](#input---usage). |
|**name** | Недоступно | Имя переменной, представляющей большой двоичный объект в коде функции.|
|**path** |**BlobPath** | Путь к BLOB-объекту. | 
|**подключение** |**Connection**| Имя параметра приложения, содержащего строку подключения к службе хранилища, используемой для этой привязки. Если имя параметра приложения начинается с AzureWebJobs, можно указать только остальную часть имени. Например, если задать для `connection` значение MyStorage, среда выполнения службы "Функции" будет искать параметр приложения с именем AzureWebJobsMyStorage. Если оставить строку `connection` пустой, среда выполнения службы "Функции" будет использовать строку подключения к службе хранилища по умолчанию для параметра приложения с именем `AzureWebJobsStorage`.<br><br>Строка подключения необходима для учетной записи хранения общего назначения, а не [учетной записи хранения только для больших двоичных объектов](../storage/common/storage-create-storage-account.md#blob-storage-accounts).|
|Недоступно | **Access** | Указывает, какая операция будет выполняться (запись или чтение). |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Использование входной привязки

В C# и скрипте C# для привязки к входным данным большого двоичного объекта можно использовать параметры следующих типов:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> Требует привязки inout `direction` в файле *function.json* или `FileAccess.ReadWrite` в библиотеке классов C#.

Привязку к `string` или `Byte[]` рекомендуется использовать, только если большой двоичный объект имеет небольшой размер, так как в память загружается все содержимое большого двоичного объекта. Как правило, предпочтительнее использовать тип `Stream` или `CloudBlockBlob`. Дополнительные сведения см. в разделе о [параллелизме и использовании памяти](#trigger---concurrency-and-memory-usage) выше в этой статье.

В JavaScript получите доступ к данным больших двоичных объектов с помощью `context.bindings.<name from function.json>`.

## <a name="output"></a>Выходные данные

Используйте выходные привязки хранилища BLOB-объектов для записи больших двоичных объектов.

## <a name="output---example"></a>Пример выходных данных

Языковой пример см. в разделах:

* [C#](#output---c-example)
* [Скрипт C# (CSX)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Пример выходных данных C#

Ниже приведен пример [функции C#](functions-dotnet-class-library.md), которая использует триггер большого двоичного объекта и две выходные привязки больших двоичных объектов. Эта функция активируется путем создания большого двоичного объекта образа в контейнере *sample-images*. Она создает небольшие и средние копии большого двоичного объекта образа. 

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

### <a name="output---c-script-example"></a>Пример выходных данных скрипта C#

<!--Same example for input and output. -->

В примере ниже показаны входная и выходная привязки больших двоичных объектов в файле *function.json*, а также код [скрипта C# (CSX)](functions-reference-csharp.md), который использует эти привязки. Функция копирует большой двоичный объект. Она активируется сообщением очереди, содержащим имя копируемого большого двоичного объекта. Новый большой двоичный объект получает имя *{originalblobname}-Copy*.

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

В разделе [Конфигурация](#output---configuration) описываются эти свойства.

Ниже приведен код скрипта C#.

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="output---javascript-example"></a>Пример выходных данных JavaScript

<!--Same example for input and output. -->

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

В разделе [Конфигурация](#output---configuration) описываются эти свойства.

Ниже показан код JavaScript.

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

## <a name="output---attributes"></a>Выходные атрибуты

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs).

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

Полный пример см. в разделе [Пример выходных данных C#](#output---c-example).

Чтобы указать учетную запись хранения на уровне класса, метода или параметра, можно использовать атрибут `StorageAccount`. Дополнительные сведения см. в разделе [Атрибуты триггера для предкомпилированного кода C#](#trigger---attributes).

## <a name="output---configuration"></a>Выходная конфигурация

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `Blob`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**type** | Недоступно | Нужно задать значение `blob`. |
|**direction** | Недоступно | Нужно задать значение `out` для выходной привязки. Исключения приведены в этом [разделе](#output---usage). |
|**name** | Недоступно | Имя переменной, представляющей большой двоичный объект в коде функции.  Задайте значение `$return`, ссылающееся на возвращаемое значение функции.|
|**path** |**BlobPath** | Путь к BLOB-объекту. | 
|**подключение** |**Connection**| Имя параметра приложения, содержащего строку подключения к службе хранилища, используемой для этой привязки. Если имя параметра приложения начинается с AzureWebJobs, можно указать только остальную часть имени. Например, если задать для `connection` значение MyStorage, среда выполнения службы "Функции" будет искать параметр приложения с именем AzureWebJobsMyStorage. Если оставить строку `connection` пустой, среда выполнения службы "Функции" будет использовать строку подключения к службе хранилища по умолчанию для параметра приложения с именем `AzureWebJobsStorage`.<br><br>Строка подключения необходима для учетной записи хранения общего назначения, а не [учетной записи хранения только для больших двоичных объектов](../storage/common/storage-create-storage-account.md#blob-storage-accounts).|
|Недоступно | **Access** | Указывает, какая операция будет выполняться (запись или чтение). |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Использование выходной привязки

В C# и в скрипте C# можно привязать следующие типы для записи больших двоичных объектов:

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>1</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>2</sup>
* `CloudBlockBlob`<sup>2</sup>
* `CloudPageBlob`<sup>2</sup>
* `CloudAppendBlob`<sup>2</sup>

<sup>1</sup> Требует привязки in `direction` в файле *function.json* или `FileAccess.Read` в библиотеке классов C#.

<sup>2</sup> Требует привязки inout `direction` в файле *function.json* или `FileAccess.ReadWrite` в библиотеке классов C#.

В асинхронных функциях используйте возвращаемое значение или `IAsyncCollector` вместо параметра `out`.

Привязку к `string` или `Byte[]` рекомендуется использовать, только если большой двоичный объект имеет небольшой размер, так как в память загружается все содержимое большого двоичного объекта. Как правило, предпочтительнее использовать тип `Stream` или `CloudBlockBlob`. Дополнительные сведения см. в разделе о [параллелизме и использовании памяти](#trigger---concurrency-and-memory-usage) выше в этой статье.


В JavaScript получите доступ к данным больших двоичных объектов с помощью `context.bindings.<name from function.json>`.

## <a name="exceptions-and-return-codes"></a>Исключения и коды возврата

| Привязка |  Справочные материалы |
|---|---|
| BLOB-объект | [Коды ошибок больших двоичных объектов](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Большой двоичный объект, таблица, очередь |  [Коды ошибок хранилища](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Большой двоичный объект, таблица, очередь |  [Устранение неполадок](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Создание функции, активируемой хранилищем BLOB-объектов Azure](functions-create-storage-blob-triggered-function.md)

> [!div class="nextstepaction"]
> [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)
