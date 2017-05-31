---
title: "Индексирование источника данных Cosmos DB для службы поиска Azure | Документация Майкрософт"
description: "В этой статье показано, как создать индексатор службы поиска Azure с использованием Cosmos DB в качестве источника данных."
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 
ms.service: cosmosdb
ms.devlang: rest-api
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: search
ms.date: 05/01/2017
ms.author: eugenesh
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 333f8320820a1729a14ffc2e29446e7452aa768e
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---
# <a name="connecting-cosmos-db-with-azure-search-using-indexers"></a>Подключение Cosmos DB к службе поиска Azure с помощью индексаторов

Если вы хотите реализовать эффективный поиск в данных Cosmos DB, можно использовать индексатор поиска Azure для извлечения данных в индекс поиска Azure. В этой статье показано, как интегрировать Azure Cosmos DB со службой поиска Azure без написания кода для поддержки инфраструктуры индексирования.

Для настройки индексатора Cosmos DB установите [службу поиска Azure](search-create-service-portal.md) и создайте индекс, источник данных и, наконец, индексатор. Вы можете создать эти объекты с помощью [портала](search-import-data-portal.md), [пакета SDK для .NET](/dotnet/api/microsoft.azure.search) или [REST API](/rest/api/searchservice/) для всех языков, отличных от .NET. 

Если вы выбрали портал, [мастер импорта данных](search-import-data-portal.md) поможет создать все эти ресурсы.

> [!NOTE]
> Cosmos DB — это база данных DocumentDB нового поколения. Несмотря на изменение имени продукта, синтаксис остается таким же, как и прежде. Указывайте базу данных `documentdb`, там где она используется в этой статье. 

> [!TIP]
> На панели мониторинга Cosmos DB можно запустить мастер **импорта данных**, чтобы упростить индексирование для источника данных. Чтобы приступить к работе, в области навигации слева выберите **Коллекции** > **Add Azure Search** (Добавить поиск Azure).

<a name="Concepts"></a>
## <a name="azure-search-indexer-concepts"></a>Понятия индексатора в службе Поиск Azure
Служба поиска Azure поддерживает создание и управление источниками данных (включая Cosmos DB) и индексаторами, работающими в этих источниках данных.

**Источник данных** указывает данные для индексирования, учетные данные и политики для обнаружения изменений в данных (например, измененные или удаленные документы в коллекции). Источник данных определяется как независимый ресурс, который может использоваться несколькими индексаторами.

**Индексатор** описывает процесс передачи данных из источника данных в целевой индекс поиска. Индексатор может использоваться:

* Для выполнения однократного копирования данных с целью заполнения индекса.
* Для синхронизации индекса с изменениями в источнике данных по расписанию. Расписание является частью определения индексатора.
* Вызов по требованию обновлений индекса.

<a name="CreateDataSource"></a>
## <a name="step-1-create-a-data-source"></a>Шаг 1. Создание источника данных
Чтобы создать источник данных, выполните POST:

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": { "name": "myDocDbCollectionId", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }

Текст запроса содержит определение источника данных, который должен включать следующие поля.

* **name**: имя базы данных Cosmos DB.
* **type** должно иметь значение `documentdb`.
* **credentials**:
  
  * **connectionString**: обязательное поле. Укажите сведения о подключении к базе данных Azure Cosmos DB в следующем формате: `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`
* **container**:
  
  * **name**: обязательное поле. Укажите идентификатор коллекции Cosmos DB, которая будет индексироваться.
  * **query**: необязательное поле. Можно указать запрос на сведение произвольного документа JSON в неструктурированную схему, индексируемую поиском Azure.
* **dataChangeDetectionPolicy** — рекомендуемое поле. Ознакомьтесь с разделом [Индексация измененных документов](#DataChangeDetectionPolicy).
* **dataDeletionDetectionPolicy**: необязательное поле. Ознакомьтесь с разделом [удаленных документов](#DataDeletionDetectionPolicy).

### <a name="using-queries-to-shape-indexed-data"></a>Использование запросов для формирования индексированных данных
Вы можете указать запрос Cosmos DB для преобразования вложенных свойств или массивов, проецирования свойств JSON, а также для фильтрации данных, подлежащих индексированию. 

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

Запрос на фильтрацию:

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark

Преобразование запросов:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark
    
    
Запрос на проектирование:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark


Массив преобразованных запросов:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark

<a name="CreateIndex"></a>
## <a name="step-2-create-an-index"></a>Шаг 2. Создание индекса
Создайте целевой индекс поиска Azure, если это еще не сделано. Вы можете создать индекс с помощью [пользовательского интерфейса портала Azure](search-create-index-portal.md), [REST API создания индекса](/rest/api/searchservice/create-index) или [класса индекса](/dotnet/api/microsoft.azure.search.models.index).

В следующем примере создается индекс с идентификатором и полем описания.

    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
    Content-Type: application/json
    api-key: [Search service admin key]

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

Убедитесь, что схема целевого индекса совместима с исходными документами JSON или выходными данными настраиваемой проекции запроса.

> [!NOTE]
> Для секционированных коллекций ключом документа по умолчанию является свойство `_rid` Cosmos DB, которое в Поиске Azure переименовано в `rid`. Кроме того, значения `_rid` Cosmos DB содержат символы, недопустимые в ключах службы поиска Azure. Поэтому значения `_rid` представлены в кодировке Base64.
> 
> 

### <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>Сопоставление типов данных JSON и типов данных службы поиска Azure
| ТИП ДАННЫХ JSON | СОВМЕСТИМЫЕ ТИПЫ ПОЛЕЙ ЦЕЛЕВОГО ИНДЕКСА |
| --- | --- |
| Bool |Edm.Boolean, Edm.String |
| Числа, которые выглядят как целые числа |Edm.Int32, Edm.Int64, Edm.String |
| Числа, которые выглядят как числа с плавающей запятой |Edm.Double, Edm.String |
| Строка |Edm.String |
| Массивы типов-примитивов, например [a, b, c] |Collection(Edm.String) |
| Строки, которые выглядят как даты |Edm.DateTimeOffset, Edm.String |
| Геообъекты JSON, например { "тип": "Точка", "координаты": [ долгота, широта ] } |Edm.GeographyPoint |
| Другие объекты JSON |Недоступно |

<a name="CreateIndexer"></a>
## <a name="step-3-create-an-indexer"></a>Шаг 3. Создание индексатора

Когда индекс и источник данных уже созданы, можно создать индексатор:

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mydocdbindexer",
      "dataSourceName" : "mydocdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

Этот индексатор выполняется каждые два часа (интервал расписания имеет значение PT2H). Чтобы запускать индексатор каждые 30 минут, задайте интервал "PT30M". Самый короткий интервал, который можно задать, составляет 5 минут. Расписание является необязательным. Если оно не указано, то индексатор выполняется только один раз при его создании. Однако индексатор можно запустить по запросу в любое время.   

Дополнительные сведения об API создания индексатора см. в разделе [Создание индексатора](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

<a id="RunIndexer"></a>
### <a name="running-indexer-on-demand"></a>Запуск индексатора по требованию
Помимо периодического выполнения по расписанию индексатор также можно вызывать по запросу:

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2016-09-01
    api-key: [Search service admin key]

> [!NOTE]
> При успешном возвращении API планируется вызов индексатора, но фактическая обработка происходит асинхронно. 

Вы можете отслеживать состояние индексатора на портале или с помощью API получения состояния индексатора, которые описаны далее. 

<a name="GetIndexerStatus"></a>
### <a name="getting-indexer-status"></a>Получение состояния индексатора
Вы можете получить сведения о состоянии и журнал выполнения индексатора:

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2016-09-01
    api-key: [Search service admin key]

Ответ содержит сведения об общем состоянии индексатора, последнем (или текущем) вызове индексатора, а также журнал последних вызовов индексатора.

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

<a name="DataChangeDetectionPolicy"></a>
## <a name="indexing-changed-documents"></a>Индексация измененных документов
Политика обнаружения изменения данных предназначена для эффективного определения измененных элементов данных. Сейчас поддерживается только политика `High Water Mark`, использующая свойство отметки времени `_ts`, предоставленное Cosmos DB. Эта политика указывается следующим образом.

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

Для обеспечения хорошей производительности индексатора мы настоятельно рекомендуем использовать эту политику. 

При использовании пользовательского запроса, убедитесь, что свойство `_ts` проецируется в запросе. 

<a name="DataDeletionDetectionPolicy"></a>
## <a name="indexing-deleted-documents"></a>Индексация удаленных документов
Строки, удаляемые из исходной коллекции, вероятно, также следует удалить из индекса поиска. Политика обнаружения удаления данных предназначена для эффективного определения удаленных элементов данных. В настоящее время единственной поддерживаемой политикой является политика `Soft Delete` (удаление помечается особым флагом), которая указывается следующим образом:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

При использовании пользовательского запроса, убедитесь, что свойство на которое указывает `softDeleteColumnName`, проецируется в запросе.

В следующем примере создается источник данных с политикой мягкого удаления:

    POST https://[Search service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": { "name": "myDocDbCollectionId" },
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

## <a name="NextSteps"></a>Дальнейшие действия
Поздравляем! Вы узнали, как интегрировать Azure Cosmos DB со службой поиска Azure с помощью индексатора для Cosmos DB.

* Дополнительные сведения о базе данных Azure Cosmos DB см. на [странице службы Cosmos DB](https://azure.microsoft.com/services/documentdb/).
* Дополнительные сведения о Поиске Azure см. на [странице документации по службе поиска](https://azure.microsoft.com/services/search/).
