---
title: Индексирование BLOB-объектов JSON с помощью индексатора BLOB-объектов службы поиска Azure
description: Индексирование BLOB-объектов JSON с помощью индексатора BLOB-объектов службы поиска Azure
services: search
documentationcenter: ''
author: chaosrealm
manager: pablocas
editor: ''

ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/26/2016
ms.author: eugenesh

---
# Индексирование BLOB-объектов JSON с помощью индексатора BLOB-объектов службы поиска Azure
В этой статье показано, как настроить индексатор больших двоичных объектов Поиска Azure для извлечения структурированного содержимого из больших двоичных объектов, содержащих JSON.

## Сценарии
По умолчанию [индексатор больших двоичных объектов Поиска Azure](search-howto-indexing-azure-blob-storage.md) анализирует большие двоичные объекты JSON как один блок текста. Часто требуется сохранить структуру документов JSON. Например, предположим, что у вас есть следующий документ JSON

    { 
        "article" : {
             "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13" 
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

и вы хотите проанализировать его так, чтобы получить документ Поиска Azure, который содержит поля "text", "datePublished" и "tags".

Кроме того, если большие двоичные объекты содержат **массив объектов JSON**, может потребоваться представить каждый элемент массива в виде отдельного документа Поиска Azure. Например, если большой двоичный объект содержит приведенный ниже код JSON:

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

можно заполнить индекс Поиска Azure тремя отдельными документами, каждый из которых содержит поля "id" и "text".

> [!IMPORTANT]
> Сейчас эта функциональность доступна в режиме предварительной версии. Она доступна при использовании REST API версии **2015-02-28-Preview**. Помните, что предварительные версии API предназначены для тестирования и ознакомления. Они не должны использоваться в рабочей среде.
> 
> 

## Настройка индексирования JSON
Чтобы индексировать большие двоичные объекты в формате JSON, задайте для параметра конфигурации `parsingMode` значение `json` (для индексации каждого объекта как отдельного документа) или `jsonArray` (если ваши объекты содержат массив JSON).

    {
      "name" : "my-json-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "json" | "jsonArray" } }
    }

При необходимости используйте **сопоставление полей** для выбора свойств исходного документа JSON, используемых для заполнения целевого индекса поиска. Это подробнее описано ниже.

> [!IMPORTANT]
> При использовании режима анализа `json` или `jsonArray` Поиск Azure предполагает, что все большие двоичные объекты в источнике данных будут в формате JSON. Если необходима поддержка как объектов JSON, так и других объектов в одном источнике данных, сообщите нам об этом на [нашем сайте UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## Построение документов поиска с помощью сопоставления полей
Сейчас служба поиска Azure не может индексировать произвольные документы JSON напрямую, так как она поддерживает только простые типы данных, строковые массивы и точки GeoJSON. Однако с помощью **сопоставления полей** можно выбирать части документа JSON и "поднимать" до полей верхнего уровня документа поиска. Дополнительные сведения об основах сопоставления полей см. в статье [Сопоставления полей индексатора в поиске Azure устраняют расхождения между источниками данных и индексами поиска](search-indexer-field-mappings.md).

Вернемся к нашему примеру документа JSON:

    { 
        "article" : {
             "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13" 
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Предположим, что у вас есть индекс поиска со следующими полями: `text` типа Edm.String, `date` типа Edm.DateTimeOffset и `tags` типа Collection(Edm.String). Чтобы сопоставить JSON с необходимой формой, используйте следующие сопоставления полей:

    "fieldMappings" : [ 
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

Имена полей источника в сопоставлениях задаются с помощью нотации [указателя JSON](http://tools.ietf.org/html/rfc6901). Укажите косую черту (корень документа JSON) и путь до нужного свойства (на произвольном уровне вложенности), разделяя элементы пути косой чертой.

Также можно ссылаться на отдельные элементы массива, используя отсчитываемый от нуля индекс. Например, чтобы выбрать первый элемент массива "tags" из приведенного выше примера, используйте следующее сопоставление полей:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Если имя поля источника в пути сопоставления полей ссылается на свойство, которое не существует в JSON, это сопоставление пропускается без ошибки. Это необходимо для поддержки документов с разными схемами (что часто встречается на практике). Поскольку проверка на ошибки не выполняется, будьте внимательны и не допускайте опечаток в спецификации сопоставления полей.
> 
> 

Если документы JSON содержат только простые свойства верхнего уровня, сопоставление полей может вообще не потребоваться. Например, для следующего документа JSON свойства верхнего уровня "text", "datePublished" и "tags" будут напрямую сопоставляться с соответствующими полями в индексе поиска:

    { 
       "text" : "A hopefully useful article explaining how to parse JSON blobs",
       "datePublished" : "2016-04-13" 
       "tags" : [ "search", "storage", "howto" ]    
     }

## Индексирование вложенных массивов JSON
Что делать, если требуется индексировать массив объектов JSON, но он является вложенным в документ? Можно выбрать, какое свойство содержит массив, с помощью свойства конфигурации `documentRoot`. Например, если большой двоичный объект выглядит следующим образом:

    { 
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" }, 
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    } 

используйте приведенную ниже конфигурацию для индексации массива, указанного в свойстве "level2".

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }


## Примеры запросов
Чтобы свести все описанное выше вместе, воспользуйтесь приведенными далее примерами данных.

Источник данных:

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<my storage connection string>" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

Индексатор:

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "useJsonParser" : true } }, 
      "fieldMappings" : [ 
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }

## Помогите нам усовершенствовать службу поиска Azure
Если вам нужна какая-либо функция или у вас есть идеи, которые можно было бы реализовать, сообщите об этом на [сайте UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

<!---HONumber=AcomDC_0727_2016-->