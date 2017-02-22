---
title: "Индексирование источника данных DocumentDB для службы поиска Azure | Документация Майкрософт"
description: "В этой статье показано, как создать индексатор службы поиска Azure с использованием DocumentDB в качестве источника данных."
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 
ms.service: documentdb
ms.devlang: rest-api
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: search
ms.date: 02/08/2017
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: d19a85e127b548e5f8979358879e8b9354934904
ms.openlocfilehash: ca09ac90dfcf125291bc0b312b16e28160a18527


---
# <a name="connecting-documentdb-with-azure-search-using-indexers"></a>Подключение DocumentDB к службе поиска Azure с помощью индексаторов

Если вы хотите реализовать эффективные возможности поиска по данным DocumentDB, можно настроить и запустить индексатор службы поиска Azure, который извлекает данные и переносит их в индекс службы поиска Azure. В этой статье показано, как интегрировать Azure DocumentDB со службой поиска Azure без написания кода для поддержки инфраструктуры индексирования.

Чтобы настроить это, потребуются [служба поиска Azure](search-create-service-portal.md), а также индекс, индексатор и источник данных. Можно создать эти объекты с помощью одного из таких методов: с помощью [портала](search-import-data-portal.md), [пакета SDK для .NET](/dotnet/api/microsoft.azure.search) или [REST API](/rest/api/searchservice/) для всех языков, отличных от .NET. 

Если вы выбрали портал, [мастер импорта данных](search-import-data-portal.md) поможет создать все эти объекты. Как правило, создается индекс по умолчанию.

> [!NOTE]
> На панели мониторинга DocumentDB можно запустить мастер **импорта данных**, чтобы упростить индексирование для источника данных. Чтобы приступить к работе, в области навигации слева выберите **Коллекции** > **Add Azure Search** (Добавить поиск Azure).

## <a name="a-idconceptsaazure-search-indexer-concepts"></a><a id="Concepts"></a>Понятия индексатора в службе Поиск Azure
Служба поиска Azure поддерживает создание и управление источниками данных (включая DocumentDB) и индексаторами, работающими в этих источниках данных.

**Источник данных** указывает данные для индексирования, учетные данные и политики для обнаружения изменений в данных (например, измененные или удаленные документы в коллекции). Источник данных определяется как независимый ресурс, который может использоваться несколькими индексаторами.

**Индексатор** описывает процесс передачи данных из источника данных в целевой индекс поиска. Для каждой комбинации целевого индекса и источника данных необходимо запланировать создание одного индексатора. Несмотря на то, что можно использовать несколько индексаторов, выполняющих запись в тот же индекс, индексатор может выполнять запись только в один индекс. Индексатор используется для выполнения следующих задач:

* однократное копирование данных для заполнения индекса;
* Для синхронизации индекса с изменениями в источнике данных по расписанию. Расписание является частью определения индексатора.
* Вызов по требованию обновлений индекса.

## <a name="a-idcreatedatasourceastep-1-create-a-data-source"></a><a id="CreateDataSource"></a>Шаг 1. Создание источника данных
Вызовите запрос HTTP POST для создания источника данных в службе поиска Azure, включая следующие заголовки запроса.

    POST https://[Search service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

Параметр `api-version` является обязательным. Допустимые значения — `2015-02-28` или более поздняя версия. Список всех поддерживаемых версий API в службе поиска приведен [здесь](search-api-versions.md) .

Текст запроса содержит определение источника данных, который должен включать следующие поля.

* **name**: имя базы данных DocumentDB.
* **type**: используйте `documentdb`.
* **credentials**:
  
  * **connectionString**: обязательное поле. Укажите сведения о подключении к базе данных Azure DocumentDB в следующем формате: `AccountEndpoint=<DocumentDB endpoint url>;AccountKey=<DocumentDB auth key>;Database=<DocumentDB database id>`
* **container**:
  
  * **name**: обязательное поле. Укажите идентификатор коллекции DocumentDB, которая будет индексироваться.
  * **query**: необязательное поле. Можно указать запрос на сведение произвольного документа JSON в неструктурированную схему, индексируемую поиском Azure.
* **dataChangeDetectionPolicy**: необязательное поле. Ознакомьтесь с разделом [Политика обнаружения изменения данных](#DataChangeDetectionPolicy) ниже.
* **dataDeletionDetectionPolicy**: необязательное поле. Ознакомьтесь с разделом [Политика обнаружения удаления данных](#DataDeletionDetectionPolicy) ниже.

Ознакомьтесь с [примером текста запроса](#CreateDataSourceExample) ниже.

### <a name="a-iddatachangedetectionpolicyacapturing-changed-documents"></a><a id="DataChangeDetectionPolicy"></a>Запись измененных документов
Политика обнаружения изменения данных предназначена для эффективного определения измененных элементов данных. В настоящее время единственной поддерживаемой политикой является политика `High Water Mark`, использующая свойство последней измененной отметки времени `_ts`, предоставленное DocumentDB. Эта политика указывается следующим образом.

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

Кроме того, потребуется добавить `_ts` в проекцию и предложение `WHERE` для запроса. Например:

    SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts >= @HighWaterMark

### <a name="a-iddatadeletiondetectionpolicyacapturing-deleted-documents"></a><a id="DataDeletionDetectionPolicy"></a>Запись удаленных документов
Строки, удаляемые из исходной таблицы, также следует удалить из индекса поиска. Политика обнаружения удаления данных предназначена для эффективного определения удаленных элементов данных. В настоящее время единственной поддерживаемой политикой является политика `Soft Delete` (удаление помечается особым флагом), которая указывается следующим образом:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

> [!NOTE]
> При использовании пользовательской проекции в предложение SELECT потребуется включить свойство softDeleteColumnName.
> 
> 

### <a name="a-idleveagingqueriesaleveraging-queries"></a><a id="LeveagingQueries"></a>Использование запросов
Кроме фиксирования измененных и удаленных документов, запрос DocumentDB можно использовать для преобразования вложенных свойств в строку, очистки массивов, проектирования свойств JSON и фильтрации данных, которые будут индексироваться. Обработка данных для индексирования может повысить производительность индексатора в службе поиска Azure.

Пример документа:

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "documentdb", "search"]
    }


Запрос на преобразование в строку:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark
    
    
Запрос на проектирование:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark


Запрос на очистку массива:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark
    
    
Запрос на фильтрацию:

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark


### <a name="a-idcreatedatasourceexamplearequest-body-example"></a><a id="CreateDataSourceExample"></a>Пример тела запроса
В следующем примере создается источник данных с настраиваемым запросом и подсказками.

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": {
            "name": "myDocDbCollectionId",
            "query": "SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts > @HighWaterMark"
        },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

### <a name="response"></a>Ответ
Если источник данных был успешно создан, вы получите ответ HTTP 201 — Создано.

## <a name="a-idcreateindexastep-2-create-an-index"></a><a id="CreateIndex"></a>Шаг 2. Создание индекса
Создайте целевой индекс поиска Azure, если это еще не сделано. Создать индекс можно с помощью [пользовательского интерфейса портала Azure](search-create-index-portal.md), [REST API создания индекса](/rest/api/searchservice/create-index) или [класса индекса](/dotnet/api/microsoft.azure.search.models.index).

    POST https://[Search service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]


Убедитесь, что схема целевого индекса совместима с исходными документами JSON или выходными данными настраиваемой проекции запроса.

> [!NOTE]
> Для секционированных коллекций ключом документа по умолчанию является свойство `_rid` DocumentDB, которое в Поиске Azure переименовано в `rid`. Кроме того, значения `_rid` DocumentDB содержат знаки, недопустимые в ключах службы поиска Azure. Поэтому значения `_rid` представлены в кодировке Base64.
> 
> 

### <a name="figure-a-mapping-between-json-data-types-and-azure-search-data-types"></a>Рисунок А. Сопоставление типов данных JSON и типов данных службы Поиск Azure
| ТИП ДАННЫХ JSON | СОВМЕСТИМЫЕ ТИПЫ ПОЛЕЙ ЦЕЛЕВОГО ИНДЕКСА |
| --- | --- |
| Bool |Edm.Boolean, Edm.String |
| Числа, которые выглядят как целые числа |Edm.Int32, Edm.Int64, Edm.String |
| Числа, которые выглядят как числа с плавающей запятой |Edm.Double, Edm.String |
| Строка |Edm.String |
| Массивы типов-примитивов, например a, b, c |Collection(Edm.String) |
| Строки, которые выглядят как даты |Edm.DateTimeOffset, Edm.String |
| Например, { "тип": "Точка", "координаты": [ долгота, широта ] } |Edm.GeographyPoint |
| Другие объекты JSON |Недоступно |

### <a name="a-idcreateindexexamplearequest-body-example"></a><a id="CreateIndexExample"></a>Пример тела запроса
В следующем примере создается индекс с идентификатором и полем описания.

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

### <a name="response"></a>Ответ
Если индекс был успешно создан, вы получите ответ HTTP 201 — Создано.

## <a name="a-idcreateindexerastep-3-create-an-indexer"></a><a id="CreateIndexer"></a>Шаг 3. Создание индексатора
Можно создать индексатор в службе поиска Azure с помощью запроса HTTP POST со следующими заголовками.

    POST https://[Search service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

Текст запроса содержит определение индексатора, которое должно включать следующие поля.

* **name**: обязательное поле. Имя индексатора.
* **dataSourceName**: обязательное поле. Имя существующего источника данных.
* **targetIndexName**: обязательное поле. Имя существующего индекса.
* **schedule**: необязательное поле. Ознакомьтесь с разделом [Расписание индексирования](#IndexingSchedule) ниже.

### <a name="a-idindexingschedulearunning-indexers-on-a-schedule"></a><a id="IndexingSchedule"></a>Выполнение индексаторов по расписанию
Индексатор может дополнительно задавать расписание. Если расписание уже имеется, индексатор будет выполняться согласно расписанию. Расписание имеет следующие атрибуты.

* **interval**: обязательное поле. Значение длительности, указывающее интервал или период между запусками индексатора. Наименьший допустимый интервал — 5 минут, наибольший — один день. Значение должно быть отформатировано как значение dayTimeDuration XSD (ограниченное подмножество значения [продолжительности ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Используется следующий шаблон: `P(nD)(T(nH)(nM))`. Примеры: `PT15M` для каждых 15 минут, `PT2H` для каждых 2 часов.
* **startTime**: обязательное поле. Параметр UTC datetime, указывающий время начала выполнения индексатора.

### <a name="a-idcreateindexerexamplearequest-body-example"></a><a id="CreateIndexerExample"></a>Пример тела запроса
В следующем примере создается индексатор, который копирует данные из коллекции, на которую ссылается источник данных `myDocDbDataSource`, в индекс `mySearchIndex` по расписанию, начинающемуся 1 января 2015 г. Периодичность выполнения — ежечасно.

    {
        "name" : "mysearchindexer",
        "dataSourceName" : "mydocdbdatasource",
        "targetIndexName" : "mysearchindex",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" }
    }

### <a name="response"></a>Ответ
Если указатель был успешно создан, вы получите ответ HTTP 201 — Создано.

## <a name="a-idrunindexerastep-4-run-an-indexer"></a><a id="RunIndexer"></a>Шаг 4. Запуск индексатора
Помимо периодического выполнения по расписанию, индексатор можно вызвать по запросу, выполнив следующий запрос HTTP POST.

    POST https://[Search service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [Search service admin key]

### <a name="response"></a>Ответ
Если индексатор успешно был вызван, вы получите ответ HTTP 202 — Принято.

## <a name="a-namegetindexerstatusastep-5-get-indexer-status"></a><a name="GetIndexerStatus"></a>Шаг 5. Получение состояния индексатора
Для получения текущего состояния и истории выполнения индексатора можно выполнить запрос HTTP GET.

    GET https://[Search service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [Search service admin key]

### <a name="response"></a>Ответ
Вместе с ответом HTTP 200 — ОК — будет возвращен текст ответа, содержащий сведения об общем состоянии работоспособности индексатора, последнем вызове индексатора, а также истории последних вызовов индексатора (при их наличии).

Ответ должен выглядеть так:

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

История выполнения включает не более 50 последних завершенных выполнений, которые сортируются в обратном хронологическом порядке (то есть в ответе первым отображается последнее выполнение).

## <a name="a-namenextstepsanext-steps"></a><a name="NextSteps"></a>Дальнейшие действия
Поздравляем! Вы только что узнали, как интегрировать Azure DocumentDB со службой поиска Azure с помощью индексатора для DocumentDB.

* Дополнительные сведения об Azure DocumentDB см. на [странице документации по службе DocumentDB](https://azure.microsoft.com/services/documentdb/).
* Дополнительные сведения о Поиске Azure см. на [странице документации по службе поиска](https://azure.microsoft.com/services/search/).


<!--HONumber=Feb17_HO2-->


