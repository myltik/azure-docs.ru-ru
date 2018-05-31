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
ms.openlocfilehash: 595502ecf0a78491c73800e8077de65707c7a486
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365191"
---
# <a name="create-indexer-azure-search-service-rest-api-version2017-11-11-preview"></a>Создание индексатора (REST службы "Поиск Azure" api-version=2017-11-11-Preview)

Этот справочник по API относится к предварительной версии документации и охватывает добавление элементов [когнитивного поиска](cognitive-search-concept-intro.md) в API создания индексатора. Как и в случае с [общедоступными версиями](https://docs.microsoft.com/rest/api/searchservice/create-indexer), вы можете создать индексатор в службе "Поиск Azure" с помощью запроса HTTP POST. 

```http
POST https://[service name].search.windows.net/indexers?api-version=2017-11-11-Preview
    Content-Type: application/json  
    api-key: [admin key]  
```  
**api-key** должен быть ключом администратора (в отличие от ключа запроса). Подробнее о ключах см. в разделе "Проверка подлинности" статьи [Безопасность в службе "Поиск Azure"](search-security-overview.md). Статья [Создание службы "Поиск Azure" на портале](search-create-service-portal.md) содержит инструкции по получению URL-адреса службы и пояснения к ключевым свойствам, используемым в запросе.

Вы также можете использовать метод PUT и указать имя источника данных в универсальном коде ресурса (URI). Если такого источника данных не существует, он будет создан.  

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]  
```  
**api-version** является обязательным. Текущая общедоступная версия — `api-version=2017-11-11`, но вам нужна предварительная версия службы когнитивного поиска: `api-version=2017-11-11-Preview`.  Дополнительные сведения см. в статье [Версии API в службе "Поиск Azure"](search-api-versions.md).

Чтобы получить указания по созданию индексаторов для конкретной платформы данных, начните со статьи [Общие сведения об индексаторах](search-indexer-overview.md), которая содержит полный список [других статей на эту тему](search-indexer-overview.md#next-steps).

> [!NOTE]  
>  Максимальное разрешенное количество индексаторов зависит от ценовой категории. В бесплатной версии службы можно использовать до трех индексаторов. Стандартная служба позволяет использовать до 50 индексаторов. Службы обеспечения высокой четкости уровня "Стандартный" вообще не поддерживают индексаторы. Подробные сведения можно найти в разделе [Ограничения службы](search-limits-quotas-capacity.md) .    

## <a name="request"></a>Запрос  

[Источник данных](https://docs.microsoft.com/rest/api/searchservice/create-data-source), [индекс](https://docs.microsoft.com/rest/api/searchservice/create-index) и [набор квалификационных навыков](ref-create-skillset.md) входят в определение [индексатора](search-indexer-overview.md), но являются независимыми компонентами, которые могут использоваться в разных сочетаниях. Например, можно использовать один и тот же источник данных или индекс с несколькими индексаторами, или использовать несколько индексаторов, записывающих данные в один индекс.

 Текст запроса содержит определение индексатора, состоящее из следующих частей.

+ [dataSourceName](#dataSourceName)
+ [targetIndexName](#targetIndexName)
+ [skillsetName](#skillset)
+ [schedule](#indexer-schedule)
+ [parameters](#indexer-parameters)
+ [fieldMappings](#field-mappings)
+ [outputFieldMappings](#output-fieldmappings)

## <a name="request-syntax"></a>Синтаксис запроса

Ниже описана синтаксическая конструкция полезных данных запроса. Далее в этой статье приведен пример запроса.  

```json
{   
    "name" : "Required for POST, optional for PUT. The name of the indexer",  
    "description" : "Optional. Anything you want, or null",  
    "dataSourceName" : "Required. The name of an existing data source",  
    "targetIndexName" : "Required. The name of an existing index",  
    "skillsetName" : "Required for cognitive search enrichment",
    "schedule" : { Optional, but immediately runs once if unspecified. See Indexing Schedule below. },  
    "parameters" : { Optional. See Indexing Parameters below. },  
    "fieldMappings" : { Optional. See fieldMappings below. },
    "outputFieldMappings" : { Required for enrichment pipelines. See outputFieldMappings below. },
    "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.
}  
```
<a name="dataSourceName"></a>

### <a name="datasourcename"></a>"dataSourceName"

[Определение источника данных](https://docs.microsoft.com/rest/api/searchservice/create-data-source) часто включает в себя свойства, которые индексатор может использовать для работы с характеристиками платформы источника. Фактически источник данных, который передается в индексатор, определяет доступность определенных свойств и параметров. Например, это может быть фильтр содержимого больших двоичных объектов Azure или время ожидания запроса для базы данных SQL Azure. 

<a name="targetIndexName"></a>

### <a name="targetindexname"></a>"targetIndexName"

[Схема индекса](https://docs.microsoft.com/rest/api/searchservice/create-index) определяет коллекцию полей, содержащий поддерживающие поиск, фильтрацию, извлечение и пр. определения, задающие способы использования поля. Во время индексирования индексатор сканирует источник данных, при необходимости вскрывая документы и извлекая информацию, сериализует результаты в формат JSON и индексирует полезные данные на основе схемы, определенной для вашего индекса.

<a name="skillset"></a>

### <a name="skillsetname"></a>"skillsetName"

[Когнитивный поиск (предварительная версия)](cognitive-search-concept-intro.md) относится к естественному языку и возможностям обработки изображений Azure. Он применяется во время приема данных для извлечения сущностей, ключевых фраз, языка, сведений из изображений и так далее. Преобразование содержимого выполняется с помощью *наборов квалификационных навыков*, которые объединяются в один [*набор квалификационных навыков*](ref-create-skillset.md) (по одному на индексатор). Как и источники данных и индексы, набор квалификационных навыков — это независимый компонент, который подключается к индексатору. Можно перенацелить набор квалификационных навыков на другие индексаторы, но индексатор может одновременно использовать только один набор квалификационных навыков.
 
<a name="indexer-schedule"></a>

### <a name="schedule"></a>"schedule"  
Индексатор может дополнительно задавать расписание. Без расписания индексатор запускается сразу же при отправке запроса: он подключается к источнику данных, сканирует его и затем индексирует. В некоторых сценариях с длительными заданиями индексации расписания используются, чтобы [период обработки](search-howto-reindex.md#scale-out-indexing) мог превысить максимальную длительность, равную 24 часам. Если расписание уже имеется, индексатор выполняется согласно ему. Планировщик встроен, поэтому использовать внешний планировщик невозможно. **Расписание** имеет следующие атрибуты: 

-   **interval**: обязательное поле. Значение длительности, указывающее интервал или период между запусками индексатора. Наименьшее допустимое значение — пять минут, наибольшее — один день. Значение должно быть отформатировано как значение dayTimeDuration XSD (ограниченное подмножество значения [продолжительности ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Для этого используется следующий шаблон: `"P[nD][T[nH][nM]]".` Примеры: `PT15M` для каждых 15 минут, `PT2H` для каждых 2 часов.  

-   **startTime** — необязательный параметр. Дата и время начала работы индексатора в формате UTC.  

<a name="indexer-parameters"></a>

### <a name="parameters"></a>"parameters"

Индексатор при необходимости может принять параметры конфигурации, которые изменяют поведение во время выполнения. Параметры конфигурации разделяются запятыми в запросе индексатора. 

```json
    {
      "name" : "my-blob-indexer-for-cognitive-search",
      ... other indexer properties
      "parameters" : { "maxFailedItems" : "15", "batchSize" : "100", "configuration" : { "parsingMode" : "json", "indexedFileNameExtensions" : ".json, .jpg, .png", "imageAction" : "generateNormalizedImages", "dataToExtract" : "contentAndMetadata" } }
    }
```

#### <a name="general-parameters-for-all-indexers"></a>Общие параметры для всех индексаторов

| Параметр | Тип и допустимые значения   | Использование  |
|-----------|------------|--------------------------|--------|
| `"batchSize"` | Целое число <br/>По умолчанию зависит от источника (1000 для Базы данных SQL Azure и Azure Cosmos DB, 10 для хранилища BLOB-объектов Azure) | Указывает количество элементов, которые считываются из источника данных и индексируются в рамках одного пакета для повышения производительности. |
| `"maxFailedItems"` | Целое число <br/>Значение по умолчанию — 0. | Допустимое количество ошибок, после достижения которого выполнение индексатора считается неудачным. Установите значение "-1", если вы не хотите, чтобы какие-либо ошибки приводили к остановке процесса индексирования. Получить сведения об ошибочных элементах можно с помощью операции [Получение состояния индексатора](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).  |
| `"maxFailedItemsPerBatch"` | Целое число <br/>Значение по умолчанию — 0. | Допустимое количество ошибок в каждом пакете, после достижения которого выполнение индексатора считается неудачным. Установите значение "-1", если вы не хотите, чтобы какие-либо ошибки приводили к остановке процесса индексирования. |

#### <a name="blob-configuration-parameters"></a>Параметры конфигурации больших двоичных объектов

Несколько параметров являются взаимоисключающими для конкретного индексатора, в том числе для [индексирования больших двоичных объектов Azure](search-howto-indexing-azure-blob-storage.md).

| Параметр | Тип и допустимые значения   | Использование  |
|-----------|---------------------------|--------|
| `"parsingMode"` | Строка<br/>`"text"`<br/>`"delimitedText"`<br/>`"json"`<br/>`"jsonArray"`  | Для [больших двоичных объектов Azure](search-howto-indexing-azure-blob-storage.md) задайте значение `text`, чтобы повысить производительность индексирования обычных текстовых файлов в хранилище BLOB-объектов. <br/>Если большие [двоичные объекты являются обычными CSV-файлами](search-howto-index-csv-blobs.md), задайте значение `delimitedText`. <br/>Для [больших двоичных объектов в формате JSON](search-howto-index-json-blobs.md) задайте значение `json`, чтобы извлекать структурированное содержимое, или значение `jsonArray` (предварительная версия), чтобы извлекать отдельные элементы в массиве в виде отдельных документов в службе "Поиск Azure". |
| `"excludedFileNameExtensions"` | Строка<br/>список с разделителями-запятыми | Для [больших двоичных объектов Azure](search-howto-indexing-azure-blob-storage.md) можно игнорировать любые типы файлов в списке. Например, можно исключить расширения ".png, .png, .mp4", чтобы пропустить эти файлы во время индексирования. | 
| `"indexedFileNameExtensions"` | Строка<br/>список с разделителями-запятыми | Позволяет выбрать [большие двоичные объекты Azure](search-howto-indexing-azure-blob-storage.md), если соответствующее расширение файла указано в списке. Например, можно направить индексирование на файлы определенного приложения, указав расширения ".docx, .pptx, .msg", чтобы специально включить эти типы файлов. | 
| `"failOnUnsupportedContentType"` | Да <br/>false (по умолчанию) | Для [больших двоичных объектов Azure](search-howto-indexing-azure-blob-storage.md) задайте значение `false`, если требуется продолжить индексирование при обнаружении неподдерживаемого типа содержимого и вам не известны все типы содержимого (расширения файлов) заранее. |
| `"failOnUnprocessableDocument"` | Да <br/>false (по умолчанию)| Для [больших двоичных объектов Azure](search-howto-indexing-azure-blob-storage.md) задайте значение `false`, чтобы продолжить индексирование в случае сбоя индексирования документа. |
| `"indexStorageMetadataOnlyForOversizedDocuments"` | Да <br/>false (по умолчанию)| Для [больших двоичных объектов Azure](search-howto-indexing-azure-blob-storage.md) присвойте этому свойству значение `true`, чтобы все равно индексировать метаданные хранилища для содержимого больших двоичных объектов, размер которых слишком велик для обработки.  Большие двоичные объекты слишком большого размера по умолчанию считаются ошибками. Ограничения размера больших двоичных объектов указаны в разделе [Ограничения поиска Azure](search-limits-quotas-capacity.md). |
| `"delimitedTextHeaders"` | Строка<br/>список с разделителями-запятыми| Для [больших двоичных объектов в формате CSV (предварительная версия)](search-howto-index-csv-blobs.md) указывает список заголовков столбцов с разделителями-запятыми, удобный для сопоставления полей источника с полями назначения в индексе. |
| `"delimitedTextDelimiter"` | Строка<br/>определяемое пользователем значение | Для [больших двоичных объектов в формате CSV (предварительная версия)](search-howto-index-csv-blobs.md) задает разделитель конца строки в CSV-файлах, в которых каждая строка начинает новый документ (например, `"|"`).  |
| `"firstLineContainsHeaders"` | true (по умолчанию) <br/>false | Для [больших двоичных объектов в формате CSV (предварительная версия)](search-howto-index-csv-blobs.md) указывает, что первая (непустая) строка каждого большого двоичного объекта содержит заголовки.|
| `"documentRoot"`  | Строка<br/>определяемое пользователем значение | Для [массивов JSON (предварительная версия)](search-howto-index-json-blobs.md#nested-json-arrays), при наличии структурированного или полуструктурированного документа, с помощью этого свойства можно указать путь к массиву. |
| `"dataToExtract"` | Строка<br/>`"storageMetadata"`<br/>`"allMetadata"`<br/>`"contentAndMetadata"` (по умолчанию) | Для [больших двоичных объектов Azure](search-howto-indexing-azure-blob-storage.md):<br/>Задайте значение `"storageMetadata"`, чтобы индексировать только [стандартные свойства больших двоичных объектов и метаданные, определяемые пользователем](../storage/blobs/storage-properties-metadata.md). <br/>Задайте значение `"allMetadata"`, чтобы извлекать метаданные, предоставляемые подсистемой хранения BLOB-объектов Azure, и индексировать [метаданные в зависимости от типа содержимого](search-howto-indexing-azure-blob-storage.md#ContentSpecificMetadata) (например, исключительно метаданные PNG-файлов). <br/>Задайте значение `"contentAndMetadata"`, чтобы извлекать все метаданные и текстовое содержимое из каждого большого двоичного объекта. <br/><br/>Если для [анализа изображений в службе когнитивного поиска (предварительная версия)](cognitive-search-concept-image-scenarios.md) параметру `"imageAction"` присвоено значение `"generateNormalizedImages"`, то параметр `"dataToExtract"` указывает индексатору, какие данные необходимо извлечь из содержимого изображения. Применяется к содержимому внедренных изображений в PDF-файлах или другом приложении или к файлам изображений, например JPG- и PNG-файлам, в больших двоичных объектах Azure.  |
| `"imageAction"` |  Строка<br/>`"none"`<br/>`"generateNormalizedImages"` | Для [больших двоичных объектов Azure](search-howto-indexing-azure-blob-storage.md) задайте значение `"none"`, чтобы игнорировать внедренные изображения или файлы изображений в наборе данных. Это уровень по умолчанию. <br/><br/>Для [анализа изображений в службе когнитивного поиска](cognitive-search-concept-image-scenarios.md) задайте значение `"generateNormalizedImages"`, чтобы извлекать текст из изображений (например, слово "стоп" из знака остановки движения) и вставлять его как часть поля содержимого. Во время анализа изображений индексатор создает массив нормализованных изображений в процессе вскрытия документов и внедряет созданную информацию в поле содержимого. Для этого действия параметр `"dataToExtract"` должен иметь значение `"contentAndMetadata"`. Нормализованное изображение получается путем дополнительной обработки, результатом которой является унифицированное изображение, размер и ориентация которого обеспечивают согласованную визуализацию при добавлении в результаты визуального поиска (например, фотографии одинакового размера в элементе управления графа, как показано в [демонстрации JFK](https://github.com/Microsoft/AzureSearch_JFK_Files)). Эти сведения создаются для каждого изображения. |


#### <a name="other-configuration-parameters"></a>Прочие параметры конфигурации

Следующие параметры относятся к базе данных SQL Azure.

| Параметр | Тип и допустимые значения   | Использование  |
|-----------|---------------------------|--------|
| `"queryTimeout"` | Строка<br/>"чч:мм:сс"<br/>"00:05:00"| Задайте этот параметр для [базы данных SQL Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), чтобы увеличить время ожидания сверх 5 минут (значение по умолчанию).|

<a name="field-mappings"></a>

### <a name="fieldmappings"></a>"fieldMappings"

Определения индексаторов содержат сопоставления полей для сопоставления исходного и конечного полей в индексе Поиска Azure. Существует два типа сопоставлений в зависимости от того, осуществляется ли передача содержимого по прямому или обогащенному пути:

+ **fieldMappings** являются необязательными и применяются, когда имена исходного и конечного полей не совпадают или требуется указать функцию.
+ **outputFieldMappings** необходимы при создании [конвейера обогащения](cognitive-search-concept-intro.md). Поле выходных данных в конвейере обогащения — это конструкция, определенная во время процесса обогащения. Например, поле выходных данных может быть составной структурой, созданной во время обогащения из двух отдельных полей в исходном документе. 

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

<a name="output-fieldmappings"></a>

### <a name="outputfieldmappings"></a>"outputFieldMappings"

В сценариях [когнитивного поиска](cognitive-search-concept-intro.md), где набор квалификационных навыков привязан к индексатору, нужно добавить `outputFieldMappings`, чтобы связать любые выходные данные этапа обогащения, предоставляющего содержимое, с поддерживающим поиск полем в индексе.

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

### <a name="field-mapping-functions"></a>Функции сопоставления полей

Сопоставления полей также можно использовать для преобразования исходных значений полей, используя *функции сопоставления полей*. Например, произвольное строковое значение может иметь кодировку base64, чтобы его можно было использовать для заполнения поля ключа документа.

Дополнительные сведения о том, как и когда использовать функции сопоставления полей, см. в разделе [Функции сопоставления полей](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#field-mapping-functions).

## <a name="request-examples"></a>Примеры запросов  
 В первом примере создается индексатор, копирующий данные из таблицы, указанной источником данных `ordersds`, в индекс `orders` по расписанию, которое начинается 1 января 2015 года. Индексатор запускается каждый час. Каждый вызов индексатора будет успешным, если происходит сбой при индексировании не более 5 элементов в каждом пакете и всего не удается индексировать не более 10 элементов.  

```json
{
    "name" : "myindexer",  
    "description" : "a cool indexer",  
    "dataSourceName" : "ordersds",  
    "targetIndexName" : "orders",  
    "schedule" : { "interval" : "PT1H", "startTime" : "2018-01-01T00:00:00Z" },  
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5 }  
}
```

Во втором примере показана операция когнитивных поиска, указанная с помощью ссылки на набор квалификационных навыков и [outputFieldMappings](#output-fieldmappings). [Наборы квалификационных навыков](ref-create-skillset.md) — это ресурсы высокого уровня, определяемые по отдельности. Этот пример представляет собой сокращение определения индексатора в [руководстве по когнитивному поиску](cognitive-search-tutorial-blob.md).

```json
{
  "name":"demoindexer", 
  "dataSourceName" : "demodata",
  "targetIndexName" : "demoindex",
  "skillsetName" : "demoskillset",
  "fieldMappings" : [
    {
        "sourceFieldName" : "content",
        "targetFieldName" : "content"
    }
   ],
  "outputFieldMappings" : 
  [
    {
        "sourceFieldName" : "/document/organizations", 
        "targetFieldName" : "organizations"
    },
  ],
  "parameters":
  {
    "maxFailedItems":-1,
    "configuration": 
    {
    "dataToExtract": "contentAndMetadata",
    "imageAction": "generateNormalizedImages"
    }
  }
}
```

## <a name="response"></a>Ответ  
 Успешный запрос возвращает код состояния "201 — Создан ресурс".  

## <a name="see-also"></a>См. также

+ [Обзор индексатора](search-indexer-overview.md)
+ [Обзор когнитивного поиска](cognitive-search-concept-intro.md)
+ [Краткое руководство: знакомство с когнитивным поиском](cognitive-search-quickstart-blob.md)
+ [Сопоставление полей](cognitive-search-output-field-mapping.md)
