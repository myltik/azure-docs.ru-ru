---
title: Индексирование BLOB-объектов JSON с помощью индексатора BLOB-объектов службы поиска Azure
description: Индексирование BLOB-объектов JSON с помощью индексатора BLOB-объектов службы поиска Azure
author: chaosrealm
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: eugenesh
ms.openlocfilehash: 752df29200a5e020ccf10f511ae2f02c0d72bd48
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34363008"
---
# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>Индексирование BLOB-объектов JSON с помощью индексатора BLOB-объектов службы поиска Azure
В этой статье показано, как настроить индексатор больших двоичных объектов в службе поиска Azure для извлечения структурированного содержимого из больших двоичных объектов JSON в хранилище BLOB-объектов Azure.

Большие двоичные объекты JSON в хранилище BLOB-объектов Azure обычно представляют собой отдельный документ JSON или массив JSON. Индексатор больших двоичных объектов в службе поиска Azure может анализировать любую конструкцию в зависимости от значения параметра **parsingMode** в запросе.

| Документ JSON | parsingMode | ОПИСАНИЕ | Доступность |
|--------------|-------------|--------------|--------------|
| Один на большой двоичный объект | `json` | Анализирует большие двоичные объекты JSON как отдельный блок текста. Каждый большой двоичный объект JSON становится отдельным документом в службе поиска Azure. | Общедоступен в API-интерфейсах [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) и [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer). |
| Несколько на большой двоичный объект | `jsonArray` | Анализирует массив JSON в большом двоичном объекте, где каждый элемент массива становится отдельным документом в службе поиска Azure.  | В предварительной версии в [REST API версии `2017-11-11-Preview`](search-api-2017-11-11-preview.md) и [.NET SDK Preview](https://aka.ms/search-sdk-preview). |

> [!Note]
> Предварительные версии API предназначены для тестирования и ознакомления. Они не должны использоваться в рабочей среде.
>

## <a name="setting-up-json-indexing"></a>Настройка индексирования JSON
Индексирование больших двоичных объектов JSON подобно извлечению стандартного документа в рабочем процессе, включающем три этапа, общем для всех индексаторов в службе поиска Azure.

### <a name="step-1-create-a-data-source"></a>Шаг 1. Создание источника данных

Первым шагом является указание сведений о подключении к источнику данных, используемых индексатором. Тип источника данных, указанный здесь как `azureblob`, определяет, какие поведения извлечения данных инициируются индексатором. Для индексирования больших двоичных объектов JSON источник данных представляет собой определение, одинаковое для обоих документов JSON и массивов. 

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="step-2-create-a-target-search-index"></a>Шаг 2. Создание целевого индекса поиска 

Индексаторы связываются со схемой индекса. При использовании API-интерфейса (а не портала) необходимо предварительно подготовить индекс для указания в операции индексатора. 

> [!Note]
> Индексаторы доступны на портале. Действие **импорта** позволяет получить доступ к ограниченному количеству общедоступных индексаторов. Часто с помощью рабочего процесса импорта можно создавать предварительный индекс на основе метаданных в источнике. Дополнительные сведения см. в статье [Импорт данных в службу поиска Azure с помощью портала](search-import-data-portal.md).

### <a name="step-3-configure-and-run-the-indexer"></a>Шаг 3. Настройка и выполнение индексатора

До этого момента определения индекса и источника данных не зависели от параметра parsingMode. Тем не менее на шаге 3 для конфигурации индексатора путь отличается в зависимости от способа анализа и упорядочения содержимого большого двоичного объекта JSON в индексе службы поиска Azure.

При вызове индексатора выполните следующее:

+ Задайте для параметра **parsingMode** значение `json` (для индексации каждого большого двоичного объекта в виде единого документа) или `jsonArray` (если большой двоичный объект содержит массивы JSON и требуется каждый элемент массива обрабатывать как отдельный документ).

+ При необходимости используйте **сопоставление полей** для выбора свойств исходного документа JSON, применяемых для заполнения целевого индекса поиска. Для массивов JSON, если массив существует как свойство нижнего уровня, можно задать корневой каталог документов, указывающий на расположение массива в пределах большого двоичного объекта.

> [!IMPORTANT]
> При использовании режима анализа `json` или `jsonArray` служба поиска Azure предполагает, что все большие двоичные объекты в источнике данных содержат JSON. Если необходима поддержка как объектов JSON, так и других объектов в одном источнике данных, сообщите нам об этом на [нашем сайте UserVoice](https://feedback.azure.com/forums/263029-azure-search).


## <a name="how-to-parse-single-json-blobs"></a>Как анализировать отдельные большие двоичные объекты JSON

По умолчанию [индексатор больших двоичных объектов Поиска Azure](search-howto-indexing-azure-blob-storage.md) анализирует большие двоичные объекты JSON как один блок текста. Часто требуется сохранить структуру документов JSON. Например, предположим, что в хранилище BLOB-объектов Azure имеется следующий документ JSON:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

### <a name="indexer-definition-for-single-json-blobs"></a>Определение индексатора для отдельных больших двоичных объектов JSON

С помощью индексатора больших двоичных объектов службы поиска Azure документ JSON, подобный документу в примере выше, преобразуется в отдельный документ в службе поиска Azure. Индексатор загружает индекс, сопоставляя значения "text", "datePublished" и "tags" из источника с идентичными по названию и типу целевыми полями.

Конфигурация содержится в тексте операции индексатора. Вспомните, что ранее определенный объект источника данных указывает тип источника данных и сведения о подключении. Кроме того, целевой индекс должен также существовать как пустой контейнер в службе. Расписание и параметры необязательны, но если опустить их, сразу же запускается индексатор, используя `json` как режим анализа.

Полностью указанный запрос может выглядеть следующим образом:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

Как уже отмечалось, сопоставления полей необязательны. Если имеется индекс с полями "text", "datePublished и "tags", индексатор больших двоичных объектов может определить правильное сопоставление без наличия сопоставления полей в запросе.

## <a name="how-to-parse-json-arrays-preview"></a>Как анализировать массивы JSON (предварительная версия)

Кроме того, вы можете выбрать функцию предварительной версии массива JSON. Эту функцию можно использовать, если большие двоичные объекты содержат *массив объектов JSON*, и вы хотите представить каждый элемент массива в виде отдельного документа в службе поиска Azure. Например, если имеется следующий большой двоичный объект JSON, можно заполнить индекс поиска Azure тремя отдельными документами, каждый из которых содержит поля "id" и "text".  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

### <a name="indexer-definition-for-a-json-array"></a>Определение индексатора для массива JSON

Для массива JSON запрос индексатора использует API предварительной версии и анализатор `jsonArray`. Это единственные два требования к массиву для индексирования больших двоичных объектов JSON.

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

Снова отметим, что сопоставления полей необязательны. Если имеется индекс с полями "id" и "text", индексатор больших двоичных объектов может определить правильное сопоставление без списка сопоставления полей.

<a name="nested-json-arrays"></a>

### <a name="nested-json-arrays"></a>Вложенные массивы JSON
Что делать, если требуется индексировать массив объектов JSON, но он является вложенным в документ? Можно выбрать, какое свойство содержит массив, с помощью свойства конфигурации `documentRoot` . Например, если большой двоичный объект выглядит следующим образом:

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

Используйте эту конфигурацию для индексации массива, содержащегося в свойстве `level2`:

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

## <a name="using-field-mappings-to-build-search-documents"></a>Построение документов поиска с помощью сопоставления полей

Если исходные и целевые поля не полностью согласованы, можно определить область сопоставления полей в тексте запроса для их явного сопоставления.

Сейчас служба поиска Azure не может индексировать произвольные документы JSON напрямую, так как она поддерживает только простые типы данных, строковые массивы и точки GeoJSON. Однако с помощью **сопоставления полей** можно выбирать части документа JSON и "поднимать" до полей верхнего уровня документа поиска. Общие сведения о сопоставлении полей см. в статье [Сопоставление полей в индексаторах Поиска Azure](search-indexer-field-mappings.md).

Вернемся к нашему примеру документа JSON:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Допустим, что у вас есть индекс поиска со следующими полями: `text` типа `Edm.String`, `date` типа `Edm.DateTimeOffset` и `tags` типа `Collection(Edm.String)`. Обратите внимание на несоответствие между полем "datePublished" в источнике и полем `date` в индексе. Чтобы сопоставить JSON с необходимой формой, используйте следующие сопоставления полей:

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

Имена полей источника в сопоставлениях задаются с помощью нотации [указателя JSON](http://tools.ietf.org/html/rfc6901) . Укажите косую черту (корень документа JSON) и путь до нужного свойства (на произвольном уровне вложенности), разделяя элементы пути косой чертой.

Также можно ссылаться на отдельные элементы массива, используя отсчитываемый от нуля индекс. Например, чтобы выбрать первый элемент массива "tags" из приведенного выше примера, используйте следующее сопоставление полей:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Если имя поля источника в пути сопоставления полей ссылается на свойство, которое не существует в JSON, это сопоставление пропускается без ошибки. Это необходимо для поддержки документов с разными схемами (что часто встречается на практике). Поскольку проверка на ошибки не выполняется, будьте внимательны и не допускайте опечаток в спецификации сопоставления полей.
>
>

## <a name="example-indexer-request-with-field-mappings"></a>Пример: запрос индексатора с сопоставлениями полей

В примере ниже показаны полностью указанные полезные данные индексатора, включая сопоставления полей:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } },
      "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }


## <a name="help-us-make-azure-search-better"></a>Помогите нам усовершенствовать службу поиска Azure
Если вам нужна какая-либо функция или у вас есть идеи, которые можно было бы реализовать, сообщите об этом на [сайте UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="see-also"></a>См. также

+ [Индексаторы в службе поиска Azure](search-indexer-overview.md)
+ [Индексирование BLOB-объектов JSON с помощью индексатора BLOB-объектов службы поиска Azure](search-howto-index-json-blobs.md)
+ [Индексирование BLOB-объектов в формате CSV с помощью индексатора BLOB-объектов службы поиска Azure](search-howto-index-csv-blobs.md)
+ [Поиск частично структурированных данных в облачном хранилище](search-semi-structured-data.md)