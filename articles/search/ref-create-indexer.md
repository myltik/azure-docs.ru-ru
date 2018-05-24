---
title: Создание индексатора (REST службы "Поиск Azure" api-version=2017-11-11-Preview)
description: В предварительной версии API индексаторы расширены, чтобы включать параметры outputFieldMapping, используемые для сопоставления выходных данных обогащения с полем в индекс Поиска Azure.
services: search
manager: pablocas
author: luiscabrer
ms.author: luisca
ms.date: 05/01/2018
ms.prod: azure
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: language-reference
ms.openlocfilehash: d47b14342caf0312e052584d20f1a9c86ca29cad
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786923"
---
# <a name="create-indexer-azure-search-service-rest-api-version2017-11-11-preview"></a>Создание индексатора (REST службы "Поиск Azure" api-version=2017-11-11-Preview)

Этот справочник по API относится к предварительной версии документации и охватывает усовершенствования когнитивного поиска для индексирования.

Как и в случае с [общедоступными версиями](https://docs.microsoft.com/rest/api/searchservice/create-indexer), вы можете создать индексатор в службе "Поиск Azure" с помощью запроса HTTP POST. 

```http
POST https://[service name].search.windows.net/indexers?api-version=[api-version]  
    Content-Type: application/json  
    api-key: [admin key]  
```  
**api-key** должен быть ключом администратора (в отличие от ключа запроса). Подробнее о ключах см. в разделе "Проверка подлинности" статьи [Безопасность в службе "Поиск Azure"](search-security-overview.md). Статья [Создание службы "Поиск Azure" на портале](search-create-service-portal.md) содержит инструкции по получению URL-адреса службы и пояснения к ключевым свойствам, используемым в запросе.

Вы также можете использовать метод PUT и указать имя источника данных в универсальном коде ресурса (URI). Если такого источника данных не существует, он будет создан.  

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]  
```  
**api-version** является обязательным. Текущая версия — `2016-09-01`. Дополнительные сведения см. в статье [Версии API в службе "Поиск Azure"](search-api-versions.md).

Чтобы получить указания по созданию индексаторов для конкретной платформы данных, начните со статьи [Общие сведения об индексаторах](search-indexer-overview.md), которая содержит полный список [других статей на эту тему](search-indexer-overview.md#next-steps).

> [!NOTE]  
>  Максимальное разрешенное количество индексаторов зависит от ценовой категории. В бесплатной версии службы можно использовать до трех индексаторов. Стандартная служба позволяет использовать до 50 индексаторов. Подробные сведения можно найти в разделе [Ограничения службы](search-limits-quotas-capacity.md) .    

## <a name="request"></a>Запрос  
 Текст запроса содержит определение индексатора, которое указывает на источник данных и целевой индекс для индексирования, а также необязательное расписание индексирования и параметры.  

 Ниже описан синтаксис полезных данных запроса. Далее в этом разделе приведен пример запроса.  

```json
{   
    "name" : "Required for POST, optional for PUT. The name of the indexer",  
    "description" : "Optional. Anything you want, or null",  
    "dataSourceName" : "Required. The name of an existing data source",  
    "targetIndexName" : "Required. The name of an existing index",  
    "schedule" : { Optional. See Indexing Schedule below. },  
    "parameters" : { Optional. See Indexing Parameters below. },  
    "fieldMappings" : { Optional. See fieldMappings below. },
    "outputFieldMappings" : { Required for enrichment pipelines. See outputFieldMappings below. },
    "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.
}  
```

### <a name="indexer-schedule"></a>Расписание индексатора  
Индексатор может дополнительно задавать расписание. Если расписание уже имеется, индексатор выполняется согласно ему. Планировщик встроен, поэтому использовать внешний планировщик невозможно. **Расписание** имеет следующие атрибуты: 

-   **interval**: обязательное поле. Значение длительности, указывающее интервал или период между запусками индексатора. Наименьший допустимый интервал — 5 минут, наибольший — один день. Значение должно быть отформатировано как значение dayTimeDuration XSD (ограниченное подмножество значения [продолжительности ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Для этого используется следующий шаблон: `"P[nD][T[nH][nM]]".` Примеры: `PT15M` для каждых 15 минут, `PT2H` для каждых 2 часов.  

-   **startTime** — необязательный параметр. Дата и время начала работы индексатора в формате UTC.  

### <a name="indexer-parameters"></a>Параметры индексатора  
 При необходимости индексатор может указывать несколько параметров, которые влияют на его поведение. Все указанные ниже параметры необязательны.  

-   **maxFailedItems**. Количество не прошедших индексацию элементов, превышение которого рассматривается как сбой индексатора. Значение по умолчанию — 0. Сведения об ошибочных элементах возвращаются операцией [получения состояния индексатора &#40;REST API службы Поиск Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).  

-   **maxFailedItemsPerBatch**. Количество не прошедших индексацию элементов в каждом пакете, превышение которого рассматривается как сбой индексатора. Значение по умолчанию — 0.  

-   **batchSize**. Указывает количество элементов, которые считываются из источника данных и индексируются в рамках одного пакета для повышения производительности. Значение по умолчанию зависит от типа источника данных: 1000 — для SQL Azure и Azure Cosmos DB и 10 — для хранилища BLOB-объектов Azure.

### <a name="field-mapping-parameters"></a>Параметры сопоставления полей

Определения индексаторов содержат сопоставления полей для сопоставления исходного и конечного полей в индексе Поиска Azure. Существует два типа сопоставлений в зависимости от того, осуществляется ли передача содержимого по прямому или обогащенному пути:

+ **fieldMappings** являются необязательными и применяются, когда имена исходного и конечного полей не совпадают или требуется указать функцию.
+ **outputFieldMappings** необходимы при создании [конвейера обогащения](cognitive-search-concept-intro.md). Поле выходных данных в конвейере обогащения — это конструкция, определенная во время процесса обогащения. Например, поле выходных данных может быть составной структурой, созданной во время обогащения из двух отдельных полей в исходном документе. 

#### <a name="fieldmappings"></a>fieldMappings

Рассмотрим исходную таблицу с полем `_id` в следующем примере. В службе "Поиск Azure" запрещено использовать имена полей, начинающиеся с символа подчеркивания, поэтому поле необходимо переименовать. Для этого выполните следующие действия с помощью свойства `fieldMappings` индексатора.

```json
"fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
```

Можно задать несколько сопоставлений полей.

```json
"fieldMappings" : [
    { "sourceFieldName" : "_id", "targetFieldName" : "id" },
    { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" }
]
```

В исходных и целевых именах полей не учитывается регистр.

Дополнительные сведения о сценариях, в которых сопоставление полей оказывается полезным, см. в разделе [Сопоставления полей в индексаторах поиска](https://docs.microsoft.com/azure/search/search-indexer-field-mappings).

#### <a name="outputfieldmappings"></a>outputFieldMappings

В сценариях когнитивного поиска, где набор навыков привязан к индексатору, нужно добавить `outputFieldMappings`, чтобы связать любые выходные данные этапа обогащения, предоставляющего содержимое, с поддерживающим поиск полем в индексе.

```json
  "outputFieldMappings" : [
        {
          "sourceFieldName" : "/document/organizations", 
          "targetFieldName" : "organizations"
        },
        {
          "sourceFieldName" : "/document/pages/*/keyPhrases/*", 
          "targetFieldName" : "keyphrases"
        },
        {
            "sourceFieldName": "/document/languageCode",
            "targetFieldName": "language",
            "mappingFunction": null
        }      
   ],
```

<a name="FieldMappingFunctions"></a>

#### <a name="field-mapping-functions"></a>Функции сопоставления полей

Сопоставления полей также можно использовать для преобразования исходных значений полей, используя *функции сопоставления полей*. Например, произвольное строковое значение может иметь кодировку base64, чтобы его можно было использовать для заполнения поля ключа документа.

Дополнительные сведения о том, как и когда использовать функции сопоставления полей, см. в разделе [Функции сопоставления полей](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#field-mapping-functions).

### <a name="request-body-examples"></a>Примеры текста запроса  
 В следующем примере создается индексатор, копирующий данные из таблицы, указанной источником данных `ordersds`, в индекс `orders` по расписанию, которое начинается 1 января 2015 г. Индексатор запускается каждый час. Каждый вызов индексатора будет успешным, если происходит сбой при индексировании не более 5 элементов в каждом пакете и всего не удается индексировать не более 10 элементов.  

```json
{
    "name" : "myindexer",  
    "description" : "a cool indexer",  
    "dataSourceName" : "ordersds",  
    "targetIndexName" : "orders",  
    "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },  
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5 }  
}
```

## <a name="response"></a>Ответ  
 Успешный запрос возвращает код состояния "201 — Создан ресурс".  

## <a name="see-also"></a>См. также

+ [Обзор когнитивного поиска](cognitive-search-concept-intro.md)
+ [Краткое руководство: знакомство с когнитивным поиском](cognitive-search-quickstart-blob.md)
+ [Сопоставление полей](cognitive-search-output-field-mapping.md)
