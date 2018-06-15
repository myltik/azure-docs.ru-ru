---
title: Справочник разработчика Java по Функциям Azure | Документация Майкрософт
description: Информация о разработке функций на языке Java.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: azure functions, functions, event processing, webhooks, dynamic compute, serverless architecture, java
ms.service: functions
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/07/2017
ms.author: routlaw
ms.openlocfilehash: 3f63cb5a16b74458f9b53fddaea13a61ec1196a5
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31514025"
---
# <a name="azure-functions-java-developer-guide"></a>Руководство разработчика Java по Функциям Azure

## <a name="programming-model"></a>Модель программирования 

Функция Azure должна существовать как метод без отслеживания состояния, который обрабатывает входные данные и создает выходные данные. Вы можете создавать методы экземпляра, но функция не должна зависеть от полей экземпляра класса. Все методы функции должны иметь модификатор доступа `public`.

## <a name="triggers-and-annotations"></a>Триггеры и заметки

Обычно функции Azure вызываются по срабатыванию внешних триггеров. Функция должна обработать такой триггер и связанные с ним входные данные, и создать на их основе одно или несколько выходных значений.

Заметки Java включаются в пакет `azure-functions-java-core` для привязки входных и выходных данных к методам. В следующей таблице перечислены поддерживаемые заметки для входных триггеров и выходных данных.

Привязка | Заметка
---|---
Cosmos DB | Недоступно
HTTP | <ul><li>`HttpTrigger`</li><li>`HttpOutput`</li></ul>
Мобильные приложения | Недоступно
Центры уведомлений | Недоступно
Большой двоичный объект хранилища | <ul><li>`BlobTrigger`</li><li>`BlobInput`</li><li>`BlobOutput`</li></ul>
Очередь службы хранилища | <ul><li>`QueueTrigger`</li><li>`QueueOutput`</li></ul>
Таблица службы хранилища | <ul><li>`TableInput`</li><li>`TableOutput`</li></ul>
Таймер | <ul><li>`TimerTrigger`</li></ul>
Twilio | Недоступно

Входные триггеры и выходные данные для вашего приложения можно также определять в файле [function.json](/azure/azure-functions/functions-reference#function-code).

> [!IMPORTANT] 
> Чтобы локально выполнять триггеры BLOB-объекта, очереди или таблицы хранилища Azure, необходимо настроить учетную запись хранения Azure в файле [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file).

Пример использования заметок.

```java
import com.microsoft.azure.serverless.functions.annotation.HttpTrigger;
import com.microsoft.azure.serverless.functions.ExecutionContext;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

Та же функция без применения заметок:

```java
package com.example;

public class MyClass {
    public static String echo(String in) {
        return in;
    }
}
```

И соответствующий файл `function.json`:

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}

```

## <a name="data-types"></a>Типы данных

Для входных и выходных данных вы можете использовать любые типы данных в Java, в том числе стандартные, пользовательские типы Java или специализированные типы Azure, которые определяются в пакете `azure-functions-java-core`. Среда выполнения Функций Azure попытается преобразовывать полученные входные данные в тот тип, который указан в вашем коде.

### <a name="strings"></a>строк

Значения, передаваемые методам функции, будут приводиться к строковым значениям, если для соответствующего входного параметра функции указан тип `String`. 

### <a name="plain-old-java-objects-pojos"></a>Объекты POJO

Строки в формате JSON будут приводиться к типам Java, если метод функции ожидает входные данные конкретного типа Java. Такая логика преобразования позволяет передавать в функции входные данные в формате JSON и работать в коде с типами Java без создания собственного кода для преобразования.

Типы POJO, используемые как входные данные для функций, должны иметь такой же модификатор доступа `public`, как и соответствующие методы функции. Нет необходимости объявлять модификатор `public`для полей класса POJO. Например, строка JSON `{ "x": 3 }` может быть преобразована в такой тип POJO:

```Java
public class MyData {
    private int x;
}
```

### <a name="binary-data"></a>Двоичные данные

Двоичные данные представляются в коде функций Azure в формате `byte[]`. Чтобы привязать двоичные входные и выходные данные к функции, в файле function.json задайте для поля `dataType` значение `binary`.

```json
 {
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "blob",
      "name": "content",
      "direction": "in",
      "dataType": "binary",
      "path": "container/myfile.bin",
      "connection": "ExampleStorageAccount"
    },
  ]
}
```

Теперь эти данные можно использовать в коде функции так:

```java
// Class definition and imports are omitted here
public static String echoLength(byte[] content) {
}
```

Используйте тип `OutputBinding<byte[]>` для привязки выходных двоичных данных.


## <a name="function-method-overloading"></a>Перегрузка методов функции

Вы можете использовать перегрузку, создавая несколько методов функции с одним именем и разными типами данных. Например, в одном классе можно создать одновременно `String echo(String s)` и `String echo(MyType s)`. Среда выполнения Функций Azure проанализирует фактический тип входных данных и определит, какой метод следует вызвать (в HTTP-данных MIME-тип `text/plain` соответствует `String`, а `application/json` представляет `MyType`).

## <a name="inputs"></a>Входные данные

Входные данные в Функциях Azure делятся на две категории: входные данные от триггера и дополнительные входные данных. Они по-разному представлены в `function.json`, но использование в коде Java полностью идентично. Давайте рассмотрим в качестве примера следующий фрагмент кода:

```java
package com.example;

import com.microsoft.azure.serverless.functions.annotation.BindingName;
import java.util.Optional;

public class MyClass {
    public static String echo(Optional<String> in, @BindingName("item") MyObject obj) {
        return "Hello, " + in.orElse("Azure") + " and " + obj.getKey() + ".";
    }

    private static class MyObject {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
}
```

Заметка `@BindingName` принимает свойство `String`, которое представляет имя привязки или триггера, определенное в `function.json`:

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "put" ],
      "route": "items/{id}"
    },
    {
      "type": "table",
      "name": "item",
      "direction": "in",
      "tableName": "items",
      "partitionKey": "Example",
      "rowKey": "{id}",
      "connection": "ExampleStorageAccount"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}
```

Поэтому при вызове этой функции полезные данные HTTP-запроса передают необязательный параметр `String` в аргументе `in`, а хранилище таблиц Azure передает тип `MyObject` в аргументе `obj`. Используйте тип `Optional<T>` для обработки входных данных функций, которые могут иметь значение NULL.

## <a name="outputs"></a>outputs

Выходные данные можно выразить как возвращаемое значение или как параметры вывода. Если существует только один выход, мы рекомендуем использовать возвращаемое значение. Для нескольких выходов нужно использовать параметры вывода.

Возвращаемое значение является самым простым форматом вывода. Вы просто возвращаете значение любого типа, а среда выполнения Функций Azure пытается маршалировать его в фактический тип (например, HTTP-ответ). В файле `functions.json` используйте `$return` в качестве имени выходной привязки.

Чтобы вернуть несколько выходных значений, используйте тип `OutputBinding<T>`, который определен в пакете `azure-functions-java-core`. Если вам нужно одновременно создать HTTP-ответ и отправить сообщение в очередь, можно использовать примерно такой код:

```java
package com.example;

import com.microsoft.azure.serverless.functions.OutputBinding;
import com.microsoft.azure.serverless.functions.annotation.BindingName;

public class MyClass {
    public static String echo(String body, 
    @QueueOutput(queueName = "messages", connection = "AzureWebJobsStorage", name = "queue") OutputBinding<String> queue) {
        String result = "Hello, " + body + ".";
        queue.setValue(result);
        return result;
    }
}
```

При этом в файле `function.json` будет определена выходная привязка:

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "queue",
      "name": "queue",
      "direction": "out",
      "queueName": "messages",
      "connection": "AzureWebJobsStorage"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}
```
## <a name="specialized-types"></a>Специализированные типы

Иногда функции нужен более точный контроль над входными и выходными данными. В пакете `azure-functions-java-core` предоставляются специализированные типы, которые позволяют управлять сведениями о запросе и изменять возвращаемое состояние триггера HTTP:

| Специализированные типы      |       Цель        | Типичное применение                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Триггер HTTP     | Получение метода, заголовков или запросов |
| `HttpResponseMessage<T>` | Привязка к выходным данным HTTP | Возврат кодов состояния, отличных от 200   |

> [!NOTE] 
> Вы также можете использовать заметки `@BindingName` для получения HTTP-заголовков и запросов. Например, `@BindingName("name") String query` перебирает все заголовки и запросы из HTTP-запроса и передает эти значения методу. Например, `query` примет значение `"test"`, если будет получен запрос с URL-адресом `http://example.org/api/echo?name=test`.

### <a name="metadata"></a>Метаданные

Метаданные поступают из разных источников, таких как заголовки HTTP, HTTP-запросы и [метаданные триггеров](/azure/azure-functions/functions-triggers-bindings#trigger-metadata-properties). Используйте заметку `@BindingName` с именем метаданных, чтобы получить значение.

Например, `queryValue` в следующем фрагменте кода будет иметь значение `"test"`, если запрошенный URL-адрес — это `http://{example.host}/api/metadata?name=test`.

```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.serverless.functions.annotation.*;

public class MyClass {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { "get", "post" }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```

## <a name="functions-execution-context"></a>Контекст выполнения функций

Взаимодействие со средой выполнения Функций Azure выполняется с помощью объекта `ExecutionContext`, который определен в пакете `azure-functions-java-core`. Используйте объект `ExecutionContext`, чтобы в коде программы получить сведения о вызове и среде выполнения функции.

### <a name="logging"></a>Ведение журналов

Доступ к средству ведения журнала среды выполнения функций выполняется с помощью объекта `ExecutionContext`. Это средство привязано к Azure Monitor и позволяет вам сохранять предупреждения и ошибки, возникшие во время выполнения функции.

Следующий пример кода записывает в журнал предупреждение, если получено пустое тело запроса.

```java
import com.microsoft.azure.serverless.functions.annotation.HttpTrigger;
import com.microsoft.azure.serverless.functions.ExecutionContext;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="environment-variables"></a>Переменные среды

Часто желательно отделять секретные данные от исходного кода по соображениям безопасности. Это позволяет публиковать код в репозиториях исходного кода без случайного предоставления учетных данных другим разработчикам. Это достигается просто путем использования переменных среды, как при локальном выполнении Функций Azure, так и при развертывании ваших функций в Azure.

Чтобы легко задать переменные среды при локальном выполнении Функций Azure, можно добавить эти переменные в файл local.settings.json. Если этот файл отсутствует в корневом каталоге проекта функции, вы можете создать его. Файл должен выглядеть следующим образом:

```xml
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "AzureWebJobsDashboard": ""
  }
}
```

Каждое сопоставление ключа и значения в карте `values` станет доступно во время выполнения как переменная среды, доступ к которой осуществляется путем вызова `System.getenv("<keyname>")`, например `System.getenv("AzureWebJobsStorage")`. Добавление дополнительных пар ключа и значения приемлемо и рекомендуется.

> [!NOTE]
> При таком подходе не забудьте определить, добавлять ли файл local.settings.json в файл игнорирования репозитория, чтобы он не был зафиксирован.

Так как ваш код теперь зависит от этих переменных среды, можно войти на портал Azure, чтобы задать те же пары "ключ-значение" в настройках приложения-функции. В таком случае код будет функционировать одинаково как при локальном тестировании, так и при развертывании в Azure.

## <a name="next-steps"></a>Дополнительная информация
Для получения дополнительных сведений см. следующие ресурсы:

* [Рекомендации по функциям Azure](functions-best-practices.md)
* [Справочник разработчика по функциям Azure](functions-reference.md)
* [Azure Functions triggers and bindings (Триггеры и привязки в Функциях Azure)](functions-triggers-bindings.md)
* [Remote Debug Java Azure Functions with Visual Studio Code (Удаленная отладка функций Azure на языке Java с помощью Visual Studio Code)](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
