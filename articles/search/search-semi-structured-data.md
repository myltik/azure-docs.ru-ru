---
title: Поиск частично структурированных данных в облачном хранилище Azure
description: Поиск частично структурированных данных больших двоичных объектов с помощью службы "Поиск Azure".
author: roygara
manager: cgronlun
services: search
ms.service: search
ms.topic: tutorial
ms.date: 10/12/2017
ms.author: v-rogara
ms.openlocfilehash: 7579862e132724d101e4267023afd9e3336bc3b1
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31795047"
---
# <a name="part-2-search-semi-structured-data-in-cloud-storage"></a>Часть 2. Поиск частично структурированных данных в облачном хранилище

В этом руководстве из двух частей показано, как выполнять поиск частично структурированных и неструктурированных данных с помощью службы "Поиск Azure". В [части 1](../storage/blobs/storage-unstructured-search.md) подробно описан поиск неструктурированных данных, а также включены важные предварительные требования для этого руководства, такие как создание учетной записи хранения. 

В части 2 внимание уделяется поиску частично структурированных данных, таких как JSON, хранящихся в больших двоичных объектах Azure. Частично структурированные данные содержат теги или метки, отделяющие содержимое в данных. Этим они отличаются от неструктурированных данных, которые должны быть проиндексированы комплексно, и от формально структурированных данных, которые соответствуют модели данных, например схеме реляционной базы данных, и поддерживают сканирование по отдельным полям.

В части 2 вы изучите следующее:

> [!div class="checklist"]
> * Настройка источника данных службы "Поиск Azure" для контейнера больших двоичных объектов.
> * Создание и заполнение индекса и создание индексатора службы "Поиск Azure" для сканирования контейнера и извлечения содержимого, поддерживающего поиск.
> * Поиск по индексу, который вы только что создали.

> [!NOTE]
> Это руководство основывается на поддержке массивов JSON. В данный момент эта функция находится на этапе предварительной версии в службе "Поиск Azure". Она недоступна на портале. Поэтому используется предварительная версия REST API, предоставляющая эту возможность, и клиентский инструмент REST для вызова API.

## <a name="prerequisites"></a>предварительным требованиям

* Выполнение инструкций [предыдущего руководства](../storage/blobs/storage-unstructured-search.md) для создания учетной записи хранения и службы поиска.

* Установленный клиент REST и знания о том, как составлять HTTP-запросы. В целях этого руководства используется [Postman](https://www.getpostman.com/). Вы можете использовать другой клиент REST, если вы уже работали с одним из них.

## <a name="set-up-postman"></a>Настройка Postman

Запустите Postman и настройте HTTP-запрос. Если вы не работали с этим инструментом, изучите раздел [Работа с REST API службы "Поиск Azure" с помощью Fiddler или Postman](search-fiddler.md) для получения дополнительной информации.

Метод запроса для каждого вызова в этом руководстве — POST. Заголовки ключей — Content-type и api-key. Значения ключей заголовков — application/json и admin key (ключ администратора — это заполнитель для первичного ключа поиска) соответственно. Тело — это расположение фактического содержимого вызова. В зависимости от используемого клиента возможны некоторые различия в способах создания запросов, однако эти являются основными.

  ![Частично структурированный поиск](media/search-semi-structured-data/postmanoverview.png)

Для вызовов REST, описанных в этом руководстве, требуется api-key поиска. api-key можно найти в разделе **Ключи** в службе поиска. Этот api-key необходимо указать в заголовке каждого вызова API (замените "ключ администратора" из предыдущего снимка экрана ним), который необходимо сделать в этом руководстве. Сохраните ключ, так как он необходим для каждого вызова.

  ![Частично структурированный поиск](media/search-semi-structured-data/keys.png)

## <a name="download-the-sample-data"></a>Скачивание примера данных

Для вас будет подготовлен пример набора данных. **Скачайте файл [clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip)** и распакуйте его в собственной папке.

В файле содержатся примеры файлов JSON, которые изначально являлись текстовыми файлами, полученными из [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results). Для удобства мы преобразовали их в формат JSON.

## <a name="log-in-to-azure"></a>Вход в Azure

Войдите на [портал Azure](http://portal.azure.com).

## <a name="upload-the-sample-data"></a>Отправка примеров данных

На портале Azure перейдите к учетной записи хранения, созданной в [предыдущем руководстве](../storage/blobs/storage-unstructured-search.md). Затем откройте контейнер **данных** и щелкните **Отправить**.

Щелкните **Дополнительно**, введите clinical-trials-json, а затем отправьте скачанные файлы JSON.

  ![Частично структурированный поиск](media/search-semi-structured-data/clinicalupload.png)

После завершения отправки файлы должны появиться в собственной вложенной папке внутри контейнера данных.

## <a name="connect-your-search-service-to-your-container"></a>Подключение службы поиска к контейнеру

Мы используем Postman для трех вызовов API к службе поиска для создания источника данных, индекса и индексатора. Источник данных содержит указатель к вашей учетной записи хранения и данным JSON. Служба поиска создает подключение при отправке данных.

Строка запроса должна содержать **api-version=2016-09-01-Preview**, а каждый вызов должен возвращать **201 — Создан ресурс**. В общедоступной версии API пока нет возможности обрабатывать JSON в качестве jsonArray. В настоящее время это можно сделать только в предварительной версии API.

Выполните следующие три вызова API из клиента REST.

### <a name="create-a-datasource"></a>Создание источника данных

Источник данных указывает данные, которые нужно добавить в индекс.

Конечная точка этого вызова — `https://[service name].search.windows.net/datasources?api-version=2016-09-01-Preview`. Замените `[service name]` именем службы поиска.

Для этого вызова требуется указать имя учетной записи хранения и ключ учетной записи хранения. Ключ учетной записи хранения можно найти на портале Azure в разделе учетной записи хранения **Ключи доступа**. На следующем рисунке показано расположение:

  ![Частично структурированный поиск](media/search-semi-structured-data/storagekeys.png)

Обязательно замените `[storage account name]` и `[storage account key]` в тексте вызова перед его выполнением.

```json
{
    "name" : "clinical-trials-json",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
    "container" : { "name" : "data", "query" : "clinical-trials-json" }
}
```

Результат должен выглядеть следующим образом:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
    "@odata.etag": "\"0x8D505FBC3856C9E\"",
    "name": "clinical-trials-json",
    "description": null,
    "type": "azureblob",
    "subtype": null,
    "credentials": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[mystorageaccounthere];AccountKey=[[myaccountkeyhere]]];"
    },
    "container": {
        "name": "data",
        "query": "clinical-trials-json"
    },
    "dataChangeDetectionPolicy": null,
    "dataDeletionDetectionPolicy": null
}
```

### <a name="create-an-index"></a>Создание индекса
    
Второй вызов API создает индекс. Индекс указывает все параметры и их атрибуты.

URL-адрес этого вызова — `https://[service name].search.windows.net/indexes?api-version=2016-09-01-Preview`. Замените `[service name]` именем службы поиска.

Сначала замените URL-адрес. Затем скопируйте и вставьте следующий код в тело и выполните запрос.

```json
{
  "name": "clinical-trials-json-index",  
  "fields": [
  {"name": "FileName", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "Description", "type": "Edm.String", "searchable": true, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MinimumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Title", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "URL", "type": "Edm.String", "searchable": false, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MyURL", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "Gender", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "MaximumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Summary", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "NCTID", "type": "Edm.String", "key": true, "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "Phase", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Date", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "OverallStatus", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "OrgStudyId", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": false},
  {"name": "HealthyVolunteers", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Keywords", "type": "Collection(Edm.String)", "searchable": true, "retrievable": true, "facetable": true, "filterable": false, "sortable": false},
  {"name": "metadata_storage_last_modified", "type":"Edm.DateTimeOffset", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_storage_size", "type":"Edm.String", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_content_type", "type":"Edm.String", "searchable": true, "retrievable": true, "filterable": true, "sortable": false}
  ],
  "suggesters": [
  {
    "name": "sg",
    "searchMode": "analyzingInfixMatching",
    "sourceFields": ["Title"]
  }
  ]
}
```

Результат должен выглядеть следующим образом:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexes/$entity",
    "@odata.etag": "\"0x8D505FC00EDD5FA\"",
    "name": "clinical-trials-json-index",
    "fields": [
        {
            "name": "FileName",
            "type": "Edm.String",
            "searchable": false,
            "filterable": false,
            "retrievable": true,
            "sortable": true,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "retrievable": false,
            "sortable": false,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        ...
          "scoringProfiles": [],
    "defaultScoringProfile": null,
    "corsOptions": null,
    "suggesters": [],
    "analyzers": [],
    "tokenizers": [],
    "tokenFilters": [],
    "charFilters": []
}
```

### <a name="create-an-indexer"></a>Создание индексатора

Индексатор подключает источник данных к целевому индексу поиска и при необходимости предоставляет расписание для автоматизации обновления данных.

URL-адрес этого вызова — `https://[service name].search.windows.net/indexers?api-version=2016-09-01-Preview`. Замените `[service name]` именем службы поиска.

Сначала замените URL-адрес. Затем скопируйте и вставьте следующий код в тело и выполните запрос.

```json
{
  "name" : "clinical-trials-json-indexer",
  "dataSourceName" : "clinical-trials-json",
  "targetIndexName" : "clinical-trials-json-index",
  "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

Результат должен выглядеть следующим образом:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
    "@odata.etag": "\"0x8D505FDE143D164\"",
    "name": "clinical-trials-json-indexer",
    "description": null,
    "dataSourceName": "clinical-trials-json",
    "targetIndexName": "clinical-trials-json-index",
    "schedule": null,
    "parameters": {
        "batchSize": null,
        "maxFailedItems": null,
        "maxFailedItemsPerBatch": null,
        "base64EncodeKeys": null,
        "configuration": {
            "parsingMode": "jsonArray"
        }
    },
    "fieldMappings": [],
    "enrichers": [],
    "disabled": null
}
```

## <a name="search-your-json-files"></a>Поиск JSON-файлов

Теперь, когда служба поиска была подключена к контейнеру данных, можно начать поиск файлов.

Откройте портал Azure и перейдите к службе поиска так же, как в предыдущем руководстве.

  ![Неструктурированный поиск](media/search-semi-structured-data/indexespane.png)

### <a name="user-defined-metadata-search"></a>Поиск определяемых пользователем метаданных

Как и прежде, данные можно запрашивать несколькими способами: полнотекстовый поиск, системные свойства или определяемые пользователем метаданные. Поиск свойств системы и определяемых пользователем метаданных можно выполнить только с параметром `$select`, если они были помечены как **извлекаемые** во время создания целевого индекса. Параметры индекса невозможно изменить после создания, но можно добавить дополнительные параметры.

Пример простого запроса — `$select=Gender,metadata_storage_size`, который ограничивает возвращаемые значения этими двумя параметрами.

  ![Частично структурированный поиск](media/search-semi-structured-data/lastquery.png)

Пример более сложного запроса — `$filter=MinimumAge ge 30 and MaximumAge lt 75`, который возвращает только результаты, в которых значение параметра MinimumAge не меньше 30, а MaximumAge — не больше 75.

  ![Частично структурированный поиск](media/search-semi-structured-data/metadatashort.png)

Поэкспериментируйте и попробуйте создать несколько дополнительных запросов самостоятельно, если хотите. Вы можете использовать логические операторы (and, or, not) и операторы сравнения (eq, ne, gt, lt, ge, le). При сравнении строк учитывается регистр.

Параметр `$filter` работает только с метаданными, которые отмечены как фильтруемые при создании индекса.

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали о поиске частично структурированных данных с помощью службы "Поиск Azure", а также о том, как выполнять следующие задачи:

> [!div class="checklist"]
> * создание индекса службы "Поиск Azure" с помощью REST API;
> * использование службы "Поиск Azure" для поиска контейнера.

Перейдите по ссылке для получения дополнительных сведений о поиске.

> [!div class="nextstepaction"]
> [Индексирование документов в хранилище BLOB-объектов Azure с помощью службы поиска Azure](search-howto-indexing-azure-blob-storage.md)