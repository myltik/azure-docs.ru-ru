---
title: "Привязки мобильных приложений в Функциях Azure | Документация Майкрософт"
description: "Узнайте, как использовать привязки мобильных приложений Azure в функциях Azure."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "функции azure, функции, обработка событий, динамические вычисления, независимая архитектура"
ms.assetid: faad1263-0fa5-41a9-964f-aecbc0be706a
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/31/2016
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: d2c0e4e233761584bad2df05a8e702e4fc77e84f
ms.contentlocale: ru-ru
ms.lasthandoff: 08/29/2017

---
# <a name="azure-functions-mobile-apps-bindings"></a>Привязки мобильных приложений в функциях Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Эта статья объясняет, как настроить и запрограммировать привязки [мобильных приложений Azure](../app-service-mobile/app-service-mobile-value-prop.md) в Функциях Azure. Функции Azure поддерживают входные и выходные привязки для мобильных приложений.

Входные и выходные привязки мобильных приложений позволяют выполнять [чтение и запись в таблицах данных](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations) в вашем мобильном приложении.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="input"></a>

## <a name="mobile-apps-input-binding"></a>Входная привязка мобильных приложений
Входная привязка мобильных приложений загружает запись из конечной точки мобильной таблицы и передает ее в функцию. В функциях C# и F# любые изменения, внесенные в запись, автоматически отправляются обратно в таблицу после успешного выхода из функции.

Входные данные мобильных приложений для функции используют следующий объект JSON в массиве `bindings` файла function.json:

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "mobileTable",
    "tableName": "<Name of your mobile app's data table>",
    "id" : "<Id of the record to retrieve - see below>",
    "connection": "<Name of app setting that has your mobile app's URL - see below>",
    "apiKey": "<Name of app setting that has your mobile app's API key - see below>",
    "direction": "in"
}
```

Обратите внимание на следующее.

* Параметр `id` может быть статическим или определяться по триггеру, который вызывает функцию. Например, если вы используете [триггер очереди]() для функции, то `"id": "{queueTrigger}"` использует строковое значение сообщения очереди в качестве идентификатора записи, который нужно получить.
* Параметр `connection` должен содержать имя параметра приложения в приложении-функции, которое, в свою очередь, содержит URL-адрес мобильного приложения. Функция использует этот URL-адрес для создания необходимых операций REST с мобильным приложением. [Создайте параметр приложения в приложении-функции](), содержащем URL-адрес мобильного приложения (который выглядит следующим образом: `http://<appname>.azurewebsites.net`), а затем укажите имя параметра приложения в свойстве `connection` во входной привязке. 
* При [внедрении ключа API в серверной части мобильного приложения Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key) или [.NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key) необходимо указать `apiKey`. Для этого [создайте параметр приложения в приложении-функции](), содержащем ключ API, а затем добавьте свойство `apiKey` в свою входную привязку с именем параметра приложения. 
  
  > [!IMPORTANT]
  > Этот ключ API не должен использоваться совместно с клиентами мобильного приложения. Его нужно безопасно распространять среди клиентов на стороне службы, таких как Функции Azure. 
  > 
  > [!NOTE]
  > Функции Azure хранят сведения о подключении и ключи API в качестве параметров приложения, чтобы они не возвращались в репозиторий системы управления версиями. Таким образом обеспечивается защита конфиденциальной информации.
  > 
  > 

<a name="inputusage"></a>

## <a name="input-usage"></a>Использование входной привязки
В этом разделе показано, как использовать входную привязку мобильных приложений в коде функции. 

Когда будет найдена запись с указанной таблицей и идентификатором записи, она передается в именованный параметр [JObject](http://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) (в Node.js она передается в объект `context.bindings.<name>`). Если запись не найдена, параметр имеет значение `null`. 

В функциях C# и F# любые изменения, внесенные во входную запись (входной параметр), будут автоматически отправляться обратно в таблицу мобильных приложений после успешного выхода из функции. Для доступа к входной записи в функциях Node.js используйте `context.bindings.<name>`. Изменить запись в Node.js невозможно.

<a name="inputsample"></a>

## <a name="input-sample"></a>Пример входной привязки
Предположим, что у вас есть следующий файл function.json, извлекающий запись таблицы мобильного приложения с идентификатором сообщения триггера очереди:

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id" : "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
],
"disabled": false
}
```

Ознакомьтесь с примером для конкретного языка, использующим входную запись из привязки. Примеры C# и F # также изменяют свойство записи `text`.

* [C#](#inputcsharp)
* [Node.js](#inputnodejs)

<a name="inputcsharp"></a>

### <a name="input-sample-in-c"></a>Пример входной привязки для языка C# #

```cs
#r "Newtonsoft.Json"    
using Newtonsoft.Json.Linq;

public static void Run(string myQueueItem, JObject record)
{
    if (record != null)
    {
        record["Text"] = "This has changed.";
    }    
}
```

<!--
<a name="inputfsharp"></a>
### Input sample in F# ## 

```fsharp
#r "Newtonsoft.Json"    
open Newtonsoft.Json.Linq
let Run(myQueueItem: string, record: JObject) =
  inputDocument?text <- "This has changed."
```
-->

<a name="inputnodejs"></a>

### <a name="input-sample-in-nodejs"></a>Пример входной привязки для Node.js

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

<a name="output"></a>

## <a name="mobile-apps-output-binding"></a>Выходная привязка мобильных приложений
Используйте выходную привязку мобильных приложений, чтобы сделать новую запись в конечную точку таблицы мобильных приложений.  

Выходные данные мобильных приложений для функции используют следующий объект JSON в массиве `bindings` файла function.json:

```json
{
    "name": "<Name of output parameter in function signature>",
    "type": "mobileTable",
    "tableName": "<Name of your mobile app's data table>",
    "connection": "<Name of app setting that has your mobile app's URL - see below>",
    "apiKey": "<Name of app setting that has your mobile app's API key - see below>",
    "direction": "out"
}
```

Обратите внимание на следующее.

* Параметр `connection` должен содержать имя параметра приложения в приложении-функции, которое, в свою очередь, содержит URL-адрес мобильного приложения. Функция использует этот URL-адрес для создания необходимых операций REST с мобильным приложением. [Создайте параметр приложения в приложении-функции](), содержащем URL-адрес мобильного приложения (который выглядит следующим образом: `http://<appname>.azurewebsites.net`), а затем укажите имя параметра приложения в свойстве `connection` во входной привязке. 
* При [внедрении ключа API в серверной части мобильного приложения Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key) или [.NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key) необходимо указать `apiKey`. Для этого [создайте параметр приложения в приложении-функции](), содержащем ключ API, а затем добавьте свойство `apiKey` в свою входную привязку с именем параметра приложения. 
  
  > [!IMPORTANT]
  > Этот ключ API не должен использоваться совместно с клиентами мобильного приложения. Его нужно безопасно распространять среди клиентов на стороне службы, таких как Функции Azure. 
  > 
  > [!NOTE]
  > Функции Azure хранят сведения о подключении и ключи API в качестве параметров приложения, чтобы они не возвращались в репозиторий системы управления версиями. Таким образом обеспечивается защита конфиденциальной информации.
  > 
  > 

<a name="outputusage"></a>

## <a name="output-usage"></a>Использование выходной привязки
В этом разделе показано, как использовать выходную привязку мобильных приложений в коде функции. 

Для доступа к выходной записи в функциях C# используйте именованный выходной параметр типа `out object`. Для доступа к выходной записи в функциях Node.js используйте `context.bindings.<name>`.

<a name="outputsample"></a>

## <a name="output-sample"></a>Пример выходной привязки
Предположим, что у вас есть следующий файл function.json, определяющий триггер очереди и выходные данные мобильных приложений:

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
    "name": "record",
    "type": "mobileTable",
    "tableName": "MyTable",
    "connection": "My_MobileApp_Url",
    "apiKey": "My_MobileApp_Key",
    "direction": "out"
    }
],
"disabled": false
}
```

Ознакомьтесь с примером для конкретного языка, который создает запись в таблице конечной точки мобильных приложений с содержимым сообщения очереди.

* [C#](#outcsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Пример выходной привязки для языка C# #

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

<!--
<a name="outfsharp"></a>
### Output sample in F# ## 
```fsharp

```
-->
<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Пример выходной привязки для Node.js

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="next-steps"></a>Дальнейшие действия
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


