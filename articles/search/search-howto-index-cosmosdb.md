---
title: Индексация источника данных Azure Cosmos DB для службы "Поиск Azure" | Документация Майкрософт
description: В этой статье показано, как создать индексатор службы "Поиска Azure" для источника данных Azure Cosmos DB.
author: chaosrealm
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: eugenesh
robot: noindex
ms.openlocfilehash: a724057981b5b389011ffc4c2fc93994c2b8be9e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33777497"
---
# <a name="connecting-cosmos-db-with-azure-search-using-indexers"></a>Подключение Cosmos DB к службе поиска Azure с помощью индексаторов

В этой статье раскрываются следующие темы:

> [!div class="checklist"]
> * настройка [индексатора службы "Поиск Azure"](search-indexer-overview.md), использующего коллекцию Azure Cosmos DB в качестве источника данных;
> * создание индекса поиска с типами данных, совместимыми с форматом JSON;
> * настройка индексатора для индексации по требованию и повторяющейся индексации;
> * добавочное обновление индекса в зависимости от изменений в базовых данных.

> [!NOTE]
> Azure Cosmos DB — это база данных DocumentDB нового поколения. Несмотря на изменение имени продукта в индексаторах службы поиска Azure по-прежнему применяется синтаксис `documentdb` для обеспечения обратной совместимости с API-интерфейсами службы поиска Azure и страницами портала. При настройке индексаторов не забудьте указать синтаксис `documentdb`, как описано в этой статье.

В следующем видео руководитель программы Azure Cosmos DB Эндрю Лю (Andrew Liu) показывает, как добавить индекс службы "Поиск Azure" в контейнер Azure Cosmos DB.

>[!VIDEO https://www.youtube.com/embed/OyoYu1Wzk4w]

<a name="supportedAPIs"></a>
## <a name="supported-api-types"></a>Поддерживаемые типы API

Несмотря на то что в Azure Cosmos DB поддерживаются различные модели данных и API-интерфейсы, поддержка индексаторов службы "Поиск Azure" в рабочей среде распространяется только на API SQL. Сейчас поддержка API MongoDB находится в общедоступной предварительной версии.  

В дальнейшем будет добавлена поддержка других API-интерфейсов. Чтобы помочь нам выбрать приоритеты для предоставления поддержки, проголосуйте на веб-сайте User Voice:

* [поддержка источников данных API таблицы](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab);
* [поддержка источников данных API Graph](https://feedback.azure.com/forums/263029-azure-search/suggestions/13285011-add-graph-databases-to-your-data-sources-eg-neo4);
* [поддержка источников данных API Apache Cassandra](https://feedback.azure.com/forums/263029-azure-search/suggestions/32857525-indexer-crawler-for-apache-cassandra-api-in-azu).

## <a name="prerequisites"></a>предварительным требованиям

Помимо учетной записи Cosmos DB вам потребуется [служба "Поиск Azure"](search-create-service-portal.md). 

<a name="Concepts"></a>
## <a name="azure-search-indexer-concepts"></a>Понятия индексатора в службе Поиск Azure

**Источник данных** указывает данные для индексирования, учетные данные и политики для обнаружения изменений в данных (например, измененные или удаленные документы в коллекции). Источник данных определяется как независимый ресурс, который может использоваться несколькими индексаторами.

**Индексатор** описывает процесс передачи данных из источника данных в целевой индекс поиска. Индексатор может использоваться:

* однократное копирование данных для заполнения индекса;
* Для синхронизации индекса с изменениями в источнике данных по расписанию.
* Вызов по требованию обновлений индекса.

Для настройки индексатора Azure Cosmos DB следует создать индекс, источник данных и, наконец, сам индексатор. Вы можете создать эти объекты с помощью [портала](search-import-data-portal.md), [пакета SDK для .NET](/dotnet/api/microsoft.azure.search) или [REST API](/rest/api/searchservice/). 

В этой статье описано, как использовать REST API. Если вы выбрали портал, [мастер импорта данных](search-import-data-portal.md) поможет создать все эти ресурсы, включая индекс.

> [!TIP]
> На панели мониторинга Azure Cosmos DB можно запустить мастер **импорта данных**, чтобы упростить индексирование для источника данных. Чтобы приступить к работе, в области навигации слева выберите **Коллекции** > **Add Azure Search** (Добавить поиск Azure).

> [!NOTE] 
> В настоящее время вы не можете создать или изменить источники данных **MongoDB** с помощью портала Azure или пакета SDK для .NET. Но вы **можете** отслеживать на портале историю выполнения индексаторов MongoDB.  

<a name="CreateDataSource"></a>
## <a name="step-1-create-a-data-source"></a>Шаг 1. Создание источника данных
Чтобы создать источник данных, выполните POST:

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }

Текст запроса содержит определение источника данных, который должен включать следующие поля.

* **name**: имя базы данных.
* **type** должно иметь значение `documentdb`.
* **credentials**:
  
  * **connectionString**: обязательное поле. Укажите сведения о подключении к базе данных Azure Cosmos DB в следующем формате: `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`. Для коллекций MongoDB добавьте в строку подключения **ApiKind=MongoDB**, вот так: `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDB`. 
* **container**:
  
  * **name**: обязательное поле. Укажите идентификатор коллекции базы данных, которая будет индексироваться.
  * **query**: необязательное поле. Можно указать запрос на сведение произвольного документа JSON в неструктурированную схему, индексируемую поиском Azure. Для коллекций MongoDB запросы не поддерживаются. 
* **dataChangeDetectionPolicy** — рекомендуемое поле. Ознакомьтесь с разделом [Индексация измененных документов](#DataChangeDetectionPolicy).
* **dataDeletionDetectionPolicy**: необязательное поле. Ознакомьтесь с разделом [удаленных документов](#DataDeletionDetectionPolicy).

### <a name="using-queries-to-shape-indexed-data"></a>Использование запросов для формирования индексированных данных
Вы можете указать SQL-запрос для преобразования вложенных свойств или массивов в плоскую структуру, проецирования свойств JSON, а также для фильтрации данных, подлежащих индексированию. 

> [!WARNING]
> Пользовательские запросы не поддерживаются для коллекций **MongoDB**. Задайте для параметра `container.query` значение NULL или пропустите его. Если вам нужно использовать пользовательский запрос, сообщите нам об этом через сайт [User Voice](https://feedback.azure.com/forums/263029-azure-search).

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

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

Преобразование запросов:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
Запрос на проектирование:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


Массив преобразованных запросов:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts

<a name="CreateIndex"></a>
## <a name="step-2-create-an-index"></a>Шаг 2. Создание индекса
Создайте целевой индекс поиска Azure, если это еще не сделано. Вы можете создать индекс с помощью [пользовательского интерфейса портала Azure](search-create-index-portal.md), [REST API создания индекса](/rest/api/searchservice/create-index) или [класса индекса](/dotnet/api/microsoft.azure.search.models.index).

В следующем примере создается индекс с идентификатором и полем описания.

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
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
> Для секционированных коллекций в качестве ключа документа по умолчанию используется свойство `_rid` Azure Cosmos DB. Служба "Поиск Azure" автоматически переименовывает его в `rid`, так как имена полей не могут начинаться с символа подчеркивания. Кроме того, значения `_rid` в Azure Cosmos DB содержат символы, недопустимые в ключах службы "Поиск Azure". Поэтому значения `_rid` представлены в кодировке Base64.
> 
> Для коллекций MongoDB служба "Поиск Azure" автоматически переименовывает свойство `_id` в `doc_id`.  

### <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>Сопоставление типов данных JSON и типов данных службы поиска Azure
| Тип данных JSON | Совместимые типы полей целевого индекса |
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

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
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

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2017-11-11
    api-key: [Search service admin key]

> [!NOTE]
> При успешном возвращении API планируется вызов индексатора, но фактическая обработка происходит асинхронно. 

Вы можете отслеживать состояние индексатора на портале или с помощью API получения состояния индексатора, которые описаны далее. 

<a name="GetIndexerStatus"></a>
### <a name="getting-indexer-status"></a>Получение состояния индексатора
Вы можете получить сведения о состоянии и журнал выполнения индексатора:

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2017-11-11
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
Политика обнаружения изменения данных предназначена для эффективного определения измененных элементов данных. Сейчас поддерживается только политика `High Water Mark`, использующая свойство метки времени `_ts`, предоставленное Azure Cosmos DB. Эта политика указывается следующим образом.

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

Для обеспечения хорошей производительности индексатора мы настоятельно рекомендуем использовать эту политику. 

При использовании пользовательского запроса, убедитесь, что свойство `_ts` проецируется в запросе.

<a name="IncrementalProgress"></a>
### <a name="incremental-progress-and-custom-queries"></a>Инкрементное индексирование и пользовательские запросы
Инкрементное индексирование позволяет восстановить работу индексатора при следующем запуске с того места, в котором она была прервана из-за временных сбоев или ограничения времени выполнения, и избежать таким образом повторного индексирования всей коллекции с нуля. Это особенно важно для индексации больших коллекций. 

Чтобы обеспечить инкрементное индексирование при использовании пользовательского запроса, убедитесь, что запрос упорядочивает результаты по столбцу `_ts`. Это позволяет выполнять периодическое сохранение контрольных точек, которые Поиск Azure использует для обеспечения инкрементного индексирования при наличии ошибок.   

В некоторых случаях, даже если запрос содержит предложение `ORDER BY [collection alias]._ts`, Поиск Azure может не определить, что запрос упорядочен по столбцу `_ts`. С помощью свойства конфигурации `assumeOrderByHighWaterMarkColumn` можно сообщить Поиску Azure, что результаты сортируются. Чтобы сделать это, создайте или обновите индексатор, как показано в этом примере: 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

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

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
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
Поздравляем! Вы узнали, как интегрировать Azure Cosmos DB со службой "Поиск Azure" с помощью индексатора.

* Дополнительные сведения об Azure Cosmos DB см. на [странице этой службы](https://azure.microsoft.com/services/cosmos-db/).
* Дополнительные сведения о службе поиска Azure см. на [этой странице](https://azure.microsoft.com/services/search/).
