---
title: "Привязки внешних файлов в Функциях Azure (предварительная версия) | Документация Майкрософт"
description: "Использование привязок внешних файлов в Функциях Azure"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: erikre
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2017
ms.author: alkarche
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 4400ebce2fbed709dcadf41cd2b834fd36416c15
ms.contentlocale: ru-ru
ms.lasthandoff: 05/02/2017


---
# <a name="azure-functions-external-file-bindings-preview"></a>Привязки внешних файлов в Функциях Azure (предварительная версия)
В этой статье показано, как управлять файлами различных поставщиков SaaS (например, OneDrive, Dropbox) внутри функции, использующей встроенные привязки. Функции Azure поддерживают привязки триггера, а также входные и выходные привязки для внешних файлов.

Привязка создает подключения API к поставщикам SaaS или использует существующие подключения API из группы ресурсов приложения-функции.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="supported-file-connections"></a>Поддерживаемые подключения файлов

|Соединитель|Триггер|Входные данные|Выходные данные|
|:-----|:---:|:---:|:---:|
|[Box](https://www.box.com)|x|x|x
|[Dropbox](https://www.dropbox.com)|x|x|x
|[Перемещение данных в локальную файловую систему или из нее с помощью фабрики данных Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-using-file-connector)|x|x|x
|[FTP](https://docs.microsoft.com/azure/app-service-web/app-service-deploy-ftp)|x|x|x
|[OneDrive](https://onedrive.live.com)|x|x|x
|[OneDrive для бизнеса](https://onedrive.live.com/about/business/)|x|x|x
|[SFTP](https://docs.microsoft.com/azure/connectors/connectors-create-api-sftp)|x|x|x
|[Хранилище BLOB-объектов Azure](https://azure.microsoft.com/services/storage/blobs/)||x|x|
|[Диск Google](https://www.google.com/drive/)||x|x|

> [!NOTE]
> В [Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list) могут также использоваться подключения к внешним файлам.

## <a name="external-file-trigger-binding"></a>Привязка триггера внешнего файла

Триггер внешнего файла Azure позволяет отслеживать удаленную папку и выполнять код функции при обнаружении изменений.

Триггер внешнего файла использует следующий объект JSON в массиве `bindings` файла function.json.

```json
{
  "type": "apiHubFileTrigger",
  "name": "<Name of input parameter in function signature>",
  "direction": "in",
  "path": "<folder to monitor, and optionally a name pattern - see below>",
  "connection": "<name of external file connection - see above>"
}
```
<!---
See one of the following subheadings for more information:

* [Name patterns](#pattern)
* [File receipts](#receipts)
* [Handling poison files](#poison)
--->

<a name="pattern"></a>

### <a name="name-patterns"></a>Шаблоны имен
Шаблон имени файла можно указать в свойстве `path`. Например:

```json
"path": "input/original-{name}",
```

При использовании этого пути будет выполняться поиск файла с именем *original-File1.txt* в папке *input*. В этом примере переменная `name` в коде функции получит значение `File1`.

Другой пример:

```json
"path": "input/{filename}.{fileextension}",
```

При использовании этого пути также будет выполняться поиск с именем *original-File1.txt*, но в код функции будут переданы другие переменные: `filename` и `fileextension` со значениями *original-File1* и *txt* соответственно.

Тип файлов можно ограничить, используя фиксированное значение для расширения файла. Например:

```json
"path": "samples/{name}.png",
```

В этом случае функцию активируют только файлы в формате *PNG* в папке *samples*.

Фигурные скобки используются в качестве специальных символов в шаблонах имен. Чтобы использовать в именах файлов фигурные скобки, нужно добавить две фигурные скобки вместо одной.
Например:

```json
"path": "images/{{20140101}}-{name}",
```

При использовании этого пути будет выполняться поиск файла с именем *{20140101}-soundfile.mp3* в папке *images*, а для переменной `name` в коде функции будет задано значение *soundfile.mp3*.

<a name="receipts"></a>

<!--- ### File receipts
The Azure Functions runtime makes sure that no external file trigger function gets called more than once for the same new or updated file.
It does so by maintaining *file receipts* to determine if a given file version has been processed.

File receipts are stored in a folder named *azure-webjobs-hosts* in the Azure storage account for your function app
(specified by the `AzureWebJobsStorage` app setting). A file receipt has the following information:

* The triggered function ("*&lt;function app name>*.Functions.*&lt;function name>*", for example: "functionsf74b96f7.Functions.CopyFile")
* The folder name
* The file type ("BlockFile" or "PageFile")
* The file name
* The ETag (a file version identifier, for example: "0x8D1DC6E70A277EF")

To force reprocessing of a file, delete the file receipt for that file from the *azure-webjobs-hosts* folder manually.
--->
<a name="poison"></a>

### <a name="handling-poison-files"></a>Обработка подозрительных файлов
При сбое функции триггера внешнего файла по умолчанию Функции Azure выполняют ее для заданного файла еще 5 раз (включая первую попытку).
В случае сбоя после 5 попыток запуска Функции добавляют сообщение в очередь службы хранилища с именем *webjobs-apihubtrigger-poison*. Сообщением очереди для подозрительных файлов является объект JSON, содержащий следующие свойства:

* FunctionId (идентификатор функции в формате *&lt;имя_приложения-функции>*.Functions.*&lt;имя_функции>*);
* FileType
* FolderName
* FileName
* ETag (идентификатор версии файла, например 0x8D1DC6E70A277EF)


<a name="triggerusage"></a>

## <a name="trigger-usage"></a>Использование триггера
В функциях C# можно выполнить привязку к данным входного файла, используя именованный параметр в сигнатуре функции, например `<T> <name>`,
где `T` — это тип данных, в который требуется десериализировать данные, а `paramName` — это имя, указанное в [JSON триггера](#trigger). В функциях Node.js доступ к данным входного файла можно получить, используя `context.bindings.<name>`.

Файл можно десериализировать в один из следующих типов:

* Любой [объект](https://msdn.microsoft.com/library/system.object.aspx) используется для сериализованных данных JSON файла.
  Если объявить пользовательский входной тип (например, `FooType`), Функции Azure попытаются десериализировать данные JSON в указанный тип.
* Строка - используется для текстовых данных файла.

В функциях C# также можно выполнить привязку к любому из следующих типов. Среда выполнения Функций попытается десериализировать данные файла, используя этот тип:

* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `IEnumerable<CloudBlockBlob>`
* `IEnumerable<CloudPageBlob>`
* другие типы, десериализованные с помощью [ICloudBlobStreamBinder](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md#icbsb)


## <a name="trigger-sample"></a>Пример триггера
Предположим, что у вас есть следующий файл function.json, определяющий триггер внешнего файла:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myFile",
            "type": "apiHubFileTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection": "<name of external file connection>"
        }
    ]
}
```

Ознакомьтесь с примером для конкретного языка, регистрирующим содержимое каждого файла, который добавляется в отслеживаемую папку.

* [C#](#triggercsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-usage-in-c"></a>Использование триггера на языке C# #

```cs
public static void Run(string myFile, TraceWriter log)
{
    log.Info($"C# File trigger function processed: {myFile}");
}
```

<!--
<a name="triggerfsharp"></a>
### Trigger usage in F# ##
```fsharp

```
-->

<a name="triggernodejs"></a>

### <a name="trigger-usage-in-nodejs"></a>Использование триггера для Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js File trigger function processed', context.bindings.myFile);
    context.done();
};
```

<a name="input"></a>

## <a name="external-file-input-binding"></a>Входная привязка внешнего файла
Входная привязка внешнего файла Azure позволяет использовать файл из внешней папки в функции.

Входные данные внешнего файла для функции используют следующий объект JSON в массиве `bindings` файла function.json:

```json
{
  "name": "<Name of input parameter in function signature>",
  "type": "apiHubFile",
  "direction": "in",
  "path": "<Path of input file - see below>",
  "connection": "<name of external file connection>"
},
```

Обратите внимание на следующее.

* Значение `path` должно содержать имя папки и имя файла. Например, если в вашей функции есть [триггер очереди](functions-bindings-storage-queue.md), можно использовать `"path": "samples-workitems/{queueTrigger}"`, чтобы указать на файл в папке `samples-workitems` с именем, которое соответствует имени файла, определенного в сообщении триггера.   

<a name="inputusage"></a>

## <a name="input-usage"></a>Использование входной привязки
В функциях C# можно выполнить привязку к данным входного файла, используя именованный параметр в сигнатуре функции, например `<T> <name>`,
где `T` — это тип данных, в который требуется десериализировать данные, а `paramName` — это имя, указанное во [входной привязке](#input). В функциях Node.js доступ к данным входного файла можно получить, используя `context.bindings.<name>`.

Файл можно десериализировать в один из следующих типов:

* Любой [объект](https://msdn.microsoft.com/library/system.object.aspx) используется для сериализованных данных JSON файла.
  Если объявить пользовательский входной тип (например, `InputType`), Функции Azure попытаются десериализировать данные JSON в указанный тип.
* Строка - используется для текстовых данных файла.

В функциях C# также можно выполнить привязку к любому из следующих типов. Среда выполнения Функций попытается десериализировать данные файла, используя этот тип:

* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`


<a name="output"></a>

## <a name="external-file-output-binding"></a>Выходная привязка внешнего файла
Выходная привязка внешнего файла Azure позволяет записывать файлы во внешнюю папку в функции.

Выходные данные внешнего файла для функции используют следующий объект JSON в массиве `bindings` файла function.json:

```json
{
  "name": "<Name of output parameter in function signature>",
  "type": "apiHubFile",
  "direction": "out",
  "path": "<Path of input file - see below>",
  "connection": "<name of external file connection>"
}
```

Обратите внимание на следующее.

* Значение `path` должно содержать имя папки и имя файла, в который выполняется запись. Например, если в вашей функции есть [триггер очереди](functions-bindings-storage-queue.md), можно использовать `"path": "samples-workitems/{queueTrigger}"`, чтобы указать на файл в папке `samples-workitems` с именем, которое соответствует имени файла, определенного в сообщении триггера.   

<a name="outputusage"></a>

## <a name="output-usage"></a>Использование выходной привязки
В функциях C# можно выполнить привязку к выходному файлу, используя именованный параметр `out` в сигнатуре функции, например `out <T> <name>`, где `T` — это тип данных, в который нужно сериализовать данные, а `paramName` — это имя, указанное в [выходной привязке](#output). В функциях Node.js доступ к выходному файлу можно получить, используя `context.bindings.<name>`.

Запись в выходной файл можно выполнить, используя любой из следующих типов:

* Любой [объект](https://msdn.microsoft.com/library/system.object.aspx) используется для сериализации JSON.
  Если объявить пользовательский выходной тип (например, `out OutputType paramName`), Функции Azure попытаются сериализовать объект в JSON. Если при выходе из функции выходной параметр имеет значение null, среда выполнения Функций создает файл в качестве пустого объекта.
* Строка - (`out string paramName`) используется для текстовых данных файла. Среда выполнения Функций создает файл, только если при выходе из функции для параметра строки не задано значение null.

В функциях C# можно выполнить вывод одного из следующих типов:

* `TextWriter`
* `Stream`
* `CloudFileStream`
* `ICloudFile`
* `CloudBlockFile`
* `CloudPageFile`

<a name="outputsample"></a>

<a name="sample"></a>

## <a name="input--output-sample"></a>Пример входных и выходных данных
Предположим, у вас есть следующий файл function.json, определяющий [триггер очереди службы хранилища](functions-bindings-storage-queue.md), а также входные и выходные данные внешнего файла:

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
      "name": "myInputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "<name of external file connection>",
      "direction": "in"
    },
    {
      "name": "myOutputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "<name of external file connection>",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Ознакомьтесь с примером для конкретного языка, копирующим входной файл в выходной.

* [C#](#incsharp)
* [Node.js](#innodejs)

<a name="incsharp"></a>

### <a name="usage-in-c"></a>Использование в языке C# #

```cs
public static void Run(string myQueueItem, string myInputFile, out string myOutputFile, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputFile = myInputFile;
}
```

<!--
<a name="infsharp"></a>
### Input usage in F# ##
```fsharp

```
-->

<a name="innodejs"></a>

### <a name="usage-in-nodejs"></a>Использование для Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```

## <a name="next-steps"></a>Дальнейшие действия
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

