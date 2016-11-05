---
title: Как настроить определение Swagger для PowerApps и логических потоков | Microsoft Docs
description: Ознакомьтесь с расширениями схемы, необходимыми Swagger для работы с PowerApps и логическими потоками.
services: ''
suite: powerapps
documentationcenter: ''
author: sunaysv
manager: erikre
editor: ''

ms.service: powerapps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2016
ms.author: mandia

---
# Настройка определения Swagger для PowerApps и логических потоков
> [!IMPORTANT]
> Этот раздел был перемещен на сайт [powerapps.microsoft.com](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/). См. последнюю версию на сайте PowerApps. Эта статья Azure архивируется.
> 
> 

## Создание определения Swagger 2.0 для своего API
Чтобы узнать, как добавить Swagger в веб-API, см. раздел, посвященный [Swashbuckle][1].

## Расширения схемы
Помимо стандартных спецификаций Swagger, существует несколько дополнительных расширений Swagger, доступных при создании настраиваемого API для PowerApps и логических потоков. В этом разделе приводится список этих расширений с описаниями.

##### x-ms-summary
Строка с описанием отображаемых имен для сущностей, у которых нет поля `summary`, определенного в спецификации Swagger. Например, это могут быть **имена параметров**.

##### x-ms-visibility
Это значение указывает, отображается ли сущность в конструкторе логических потоков. Доступны следующие значения:

* "none" (по умолчанию)
* "advanced"
* "internal" — конструктор логических потоков не отображает эти операции

Если операция отмечена как "важная", клиент логических потоков должен ее выделить.

##### x-ms-trigger
Определяет, может ли эта операция использоваться в логическом потоке как триггер. Доступные параметры:

* none (по умолчанию): операция не может использоваться как триггер.
* single: операция может использоваться как триггер.
* batched: операция может использоваться как триггер. Кроме того, эта операция возвращает "массив" объектов JSON, а логический поток запускает триггер для каждого элемента в массиве.

##### x-ms-dynamic-values
Указывает конструктору логических потоков, что API предоставляет список динамически разрешенных значений для этого параметра. Конструктор логических потоков может вызывать операцию, определенную значением этого поля, и извлекать возможные значения из результата. Затем конструктор логических потоков может отображать эти значения в виде параметров для конечного пользователя.

Значение — это объект, который содержит следующие свойства:

* `operationId`: строка, которая совпадает с идентификатором operationId для вызываемой операции.
* `parameters`: объект, свойства которого определяют параметры, необходимые для операции.
* `value-collection`: строка пути, значением которой является массив объектов в полезных данных ответа.
* `value-path`: строка пути в объекте из value-collection, которая ссылается на значение параметра.
* `value-title`: строка пути в объекте из value-collection, которая ссылается на описание значения.

Пример:

```javascript
"/api/tables/{table}/items": {
  "post": {
    "operationId": "TableData_CreateItem",
    "summary": "Create an object in {Salesforce}",
    "parameters": [
      {
        "name": "table",
        "x-ms-summary": "Object Type",
        "x-ms-dynamic-values": {
          "operationId": "TableMetadata_ListTables",      // operation that needs to be invoked
          "parameters": { },                              // parameters for the above operation, if any
          "value-collection": "values",                   // field that contains the collection
          "value-path": "Name",                           // field that contains the value
          "value-title": "DisplayName"                    // field that contains a display name for the value
      }
      // ...
    ]
    // ...
  }
  // ...
}
```

В этом примере Swagger определяет операцию `TableData_CreateItem`, которая создает новый объект в Salesforce.

В Salesforce есть много встроенных объектов. `x-ms-dynamic-values` помогает конструктору определить список встроенных объектов Salesforce. Он получает список, вызывая `TableMetadata_ListTables`.

##### x-ms-dynamic-schema
Указывает конструктору логических потоков, что схема для этого параметра (или ответа) является динамической. Расширение может вызывать операцию, определенную значением этого поля, и динамически определять схему. После этого оно отображает соответствующий пользовательский интерфейс, где пользователи вводят данные, или отображаются доступные поля.

Пример:

```javascript
{
  "name": "item",
  "in": "body",
  "required": true,
  "x-ms-dynamic-schema": {
    "operationId": "Metadata_GetTableSchema",
    "parameters": {
      "tablename": "{table}"              // the value that the user has selected from the above parameter
    },
    "value-path": "Schema"                // the field that contains the JSON schema
  }
},
```

Это расширение полезно в сценариях, где входные данные для операции являются динамическими. Возьмем для примера SQL. У всех таблиц разные схемы. Поэтому, когда пользователь выбирает определенную таблицу, конструктору логических потоков приходится анализировать структуру таблицы, чтобы отобразить имена столбцов. В этом контексте, если определение Swagger содержит `x-ms-dynamic-schema`, то для получения схемы вызывается соответствующая операция.

<!--Reference links in article-->
[1]: https://github.com/domaindrivendev/Swashbuckle/blob/master/README.md

<!---HONumber=AcomDC_0713_2016-->