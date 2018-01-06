---
title: "Внешние привязки файла для функций Azure (экспериментальная функция)"
description: "Использование привязок внешних файлов в Функциях Azure"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/27/2017
ms.author: alkarche
ms.openlocfilehash: 4e9c2c336df465d7488de84bd2a02cc5d9e42f30
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/05/2018
---
# <a name="azure-functions-external-file-bindings-experimental"></a>Azure привязки функции внешнего файла (экспериментальная функция)
В этой статье показано, как обработка файлов с разными поставщиками SaaS (например, Dropbox или Google диске) в функциях Azure. Azure поддерживает функции триггера, ввод и вывод привязки для внешних файлов. Эти привязки создать API соединения с поставщиками SaaS или использовать существующие API подключения из группы ресурсов функции приложения.

> [!IMPORTANT]
> Внешний файл привязки экспериментальных и никогда не может перейти в состояние общедоступными (GA). Они включаются только в Azure 1.x, и не планируется добавление функций Azure 2.x. Для сценариев, которым требуется доступ к данным в поставщики SaaS, рассмотрите возможность использования [логику приложения, которые вызывают функции](functions-twitter-email.md). В разделе [соединитель логику приложений файловой системы](../logic-apps/logic-apps-using-file-connector.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="available-file-connections"></a>Подключения доступных файлов

|Соединитель|Триггер|Входные данные|Выходные данные|
|:-----|:---:|:---:|:---:|
|[Box](https://www.box.com)|x|x|x
|[Dropbox](https://www.dropbox.com)|x|x|x
|[FTP](https://docs.microsoft.com/azure/app-service/app-service-deploy-ftp)|x|x|x
|[OneDrive](https://onedrive.live.com)|x|x|x
|[OneDrive для бизнеса](https://onedrive.live.com/about/business/)|x|x|x
|[SFTP](https://docs.microsoft.com/azure/connectors/connectors-create-api-sftp)|x|x|x
|[Диск Google](https://www.google.com/drive/)||x|x|

> [!NOTE]
> Внешние подключения файлов может также использоваться в [приложения логики Azure](https://docs.microsoft.com/azure/connectors/apis-list).

## <a name="trigger"></a>Триггер

Внешний файл триггер позволяет отслеживать удаленную папку и запуск кода функции при обнаружении изменений.

## <a name="trigger---example"></a>Пример триггера

Языковой пример см. в разделах:

* [Сценарий C#](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-script-example"></a>Пример скрипта C# в триггере

В следующем примере показан триггер внешний файл привязки в *function.json* файла и [функции скрипта C#](functions-reference-csharp.md) , использующее эту привязку. Функция записывает содержимое каждого файла, который добавляется в папку отслеживаемых.

Данные привязки в файле *function.json*:

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

Ниже приведен код скрипта C#.

```cs
public static void Run(string myFile, TraceWriter log)
{
    log.Info($"C# File trigger function processed: {myFile}");
}
```

### <a name="trigger---javascript-example"></a>Пример JavaScript в триггере

В следующем примере показан триггер внешний файл привязки в *function.json* файла и [функцию JavaScript](functions-reference-node.md) , использующее эту привязку. Функция записывает содержимое каждого файла, который добавляется в папку отслеживаемых.

Данные привязки в файле *function.json*:

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

Ниже показан код JavaScript.

```javascript
module.exports = function(context) {
    context.log('Node.js File trigger function processed', context.bindings.myFile);
    context.done();
};
```

## <a name="trigger---configuration"></a>Конфигурация триггера

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json*.

|свойство function.json | ОПИСАНИЕ|
|---------|---------|----------------------|
|**type** | Нужно задать значение `apiHubFileTrigger`. Это свойство задается автоматически при создании триггера на портале Azure.|
|**direction** | Нужно задать значение `in`. Это свойство задается автоматически при создании триггера на портале Azure. |
|**name** | Имя переменной, представляющей элемент события в коде функции. | 
|**подключение**| Определяет параметр приложения, в которой хранятся строки подключения. Параметр приложения создается автоматически при добавлении соединения в интеграции пользовательского интерфейса на портале Azure.|
|**path** | Папки для мониторинга и при необходимости шаблон имени.|

### <a name="name-patterns"></a>Шаблоны имен

Шаблон имени файла можно указать в свойстве `path`. Указанная папка должна существовать в поставщике SaaS.

Примеры:

```json
"path": "input/original-{name}",
```

При использовании этого пути будет выполняться поиск файла с именем *original-File1.txt* в папке *input*. В этом примере переменная `name` в коде функции получит значение `File1.txt`.

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

## <a name="trigger---usage"></a>Использование триггера

В функциях C# можно выполнить привязку к данным входного файла, используя именованный параметр в сигнатуре функции, например `<T> <name>`,
где `T` — это тип данных, в который требуется десериализировать данные, а `paramName` — это имя, указанное в [JSON триггера](#trigger). В функциях Node.js доступ к данным входного файла можно получить, используя `context.bindings.<name>`.

Файл можно десериализировать в один из следующих типов:

* Любой [объект](https://msdn.microsoft.com/library/system.object.aspx) используется для сериализованных данных JSON файла.
  Если объявить пользовательский входной тип (например, `FooType`), Функции Azure попытаются десериализировать данные JSON в указанный тип.
* Строка - используется для текстовых данных файла.

В функциях C# также можно выполнить привязку к любому из следующих типов. Среда выполнения Функций попытается десериализировать данные файла, используя этот тип:

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`

<!--- ## Trigger - file receipts
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

## <a name="trigger---poison-files"></a>Триггер - подозрительных файлов

При сбое функции триггера внешнего файла по умолчанию Функции Azure выполняют ее для заданного файла еще 5 раз (включая первую попытку).
В случае сбоя после 5 попыток запуска Функции добавляют сообщение в очередь службы хранилища с именем *webjobs-apihubtrigger-poison*. Сообщением очереди для подозрительных файлов является объект JSON, содержащий следующие свойства:

* FunctionId (идентификатор функции в формате *&lt;имя_приложения-функции>*.Functions.*&lt;имя_функции>*);
* FileType
* FolderName
* FileName
* ETag (идентификатор версии файла, например 0x8D1DC6E70A277EF)

## <a name="input"></a>Входные данные

Входная привязка внешнего файла Azure позволяет использовать файл из внешней папки в функции.

## <a name="input---example"></a>Пример входных данных

Языковой пример см. в разделах:

* [Сценарий C#](#input---c-script-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-script-example"></a>Пример входных данных сценария C#

В следующем примере показано внешний файл привязки ввода-вывода в *function.json* файла и [функции скрипта C#](functions-reference-csharp.md) , использующее эту привязку. Функция входной файл копируется в выходной файл.

Данные привязки в файле *function.json*:

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

Ниже приведен код скрипта C#.

```cs
public static void Run(string myQueueItem, string myInputFile, out string myOutputFile, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputFile = myInputFile;
}
```

### <a name="input---javascript-example"></a>Пример входных данных JavaScript

В следующем примере показано внешний файл привязки ввода-вывода в *function.json* файла и [функцию JavaScript](functions-reference-node.md) , использующее эту привязку. Функция входной файл копируется в выходной файл.

Данные привязки в файле *function.json*:

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

Ниже показан код JavaScript.

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```

## <a name="input---configuration"></a>Входная конфигурация

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json*.

|свойство function.json | ОПИСАНИЕ|
|---------|---------|----------------------|
|**type** | Нужно задать значение `apiHubFile`. Это свойство задается автоматически при создании триггера на портале Azure.|
|**direction** | Нужно задать значение `in`. Это свойство задается автоматически при создании триггера на портале Azure. |
|**name** | Имя переменной, представляющей элемент события в коде функции. | 
|**подключение**| Определяет параметр приложения, в которой хранятся строки подключения. Параметр приложения создается автоматически при добавлении соединения в интеграции пользовательского интерфейса на портале Azure.|
|**path** | Должен содержать имя папки и имя файла. Например, если в вашей функции есть [триггер очереди](functions-bindings-storage-queue.md), можно использовать `"path": "samples-workitems/{queueTrigger}"`, чтобы указать на файл в папке `samples-workitems` с именем, которое соответствует имени файла, определенного в сообщении триггера.   

## <a name="input---usage"></a>Использование входной привязки

В функциях C# можно выполнить привязку к данным входного файла, используя именованный параметр в сигнатуре функции, например `<T> <name>`, `T`имеет тип данных требуется для десериализации данных в, и `name` имеет имя, указанное в входной привязки. В функциях Node.js доступ к данным входного файла можно получить, используя `context.bindings.<name>`.

Файл можно десериализировать в один из следующих типов:

* Любой [объект](https://msdn.microsoft.com/library/system.object.aspx) используется для сериализованных данных JSON файла.
  Если объявить пользовательский входной тип (например, `InputType`), Функции Azure попытаются десериализировать данные JSON в указанный тип.
* Строка - используется для текстовых данных файла.

В функциях C# также можно выполнить привязку к любому из следующих типов. Среда выполнения Функций попытается десериализировать данные файла, используя этот тип:

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`

## <a name="output"></a>Выходные данные

Выходная привязка внешнего файла Azure позволяет записывать файлы во внешнюю папку в функции.

## <a name="output---example"></a>Пример выходных данных

В разделе [примере входной привязки](#input---example).

## <a name="output---configuration"></a>Выходная конфигурация

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json*.

|свойство function.json | ОПИСАНИЕ|
|---------|---------|----------------------|
|**type** | Нужно задать значение `apiHubFile`. Это свойство задается автоматически при создании триггера на портале Azure.|
|**direction** | Нужно задать значение `out`. Это свойство задается автоматически при создании триггера на портале Azure. |
|**name** | Имя переменной, представляющей элемент события в коде функции. | 
|**подключение**| Определяет параметр приложения, в которой хранятся строки подключения. Параметр приложения создается автоматически при добавлении соединения в интеграции пользовательского интерфейса на портале Azure.|
|**path** | Должен содержать имя папки и имя файла. Например, если в вашей функции есть [триггер очереди](functions-bindings-storage-queue.md), можно использовать `"path": "samples-workitems/{queueTrigger}"`, чтобы указать на файл в папке `samples-workitems` с именем, которое соответствует имени файла, определенного в сообщении триггера.   

## <a name="output---usage"></a>Использование выходной привязки

В C# функции, можно привязать к выходному файлу с помощью с таким же именем `out` параметра в подписи функции, такие как `out <T> <name>`, где `T` — требуется для сериализации данных, тип данных и `name` имеет имя, указанное в Привязка для вывода. В функциях Node.js доступ к выходному файлу можно получить, используя `context.bindings.<name>`.

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

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)
