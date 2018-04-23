---
title: Индексирование хранилища BLOB-объектов Azure с помощью службы поиска Azure
description: Узнайте, как индексировать хранилище BLOB-объектов Azure и извлекать текст из документов с помощью службы поиска Azure.
author: chaosrealm
manager: jlembicz
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: eugenesh
ms.openlocfilehash: c997982868213bc1e0315a8d19c95b7c97f81de3
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
---
# <a name="indexing-documents-in-azure-blob-storage-with-azure-search"></a>Индексирование документов в хранилище BLOB-объектов Azure с помощью службы поиска Azure
В этой статье показано, как использовать поиск Azure для индексации документов (например, файлов PDF, Microsoft Office и некоторых других распространенных форматов), которые хранятся в хранилище BLOB-объектов Azure. Во-первых, объясняются основные принципы установки и настройки индексатора больших двоичных объектов. Затем предлагается более углубленно изучить возможные сценарии и поведения.

## <a name="supported-document-formats"></a>Поддерживаемые форматы документов
Индексатор больших двоичных объектов может извлечь текст из документов следующих форматов:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="setting-up-blob-indexing"></a>Настройка индексирования больших двоичных объектов
Можно настроить индексатор хранилище BLOB-объектов Azure с помощью следующих инструментов:

* [портал Azure](https://ms.portal.azure.com)
* [REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations) Поиска Azure;
* [пакета SDK .NET для Поиска Azure](https://aka.ms/search-sdk);

> [!NOTE]
> Некоторые функции (например, сопоставление полей) еще не доступны на портале и должны быть использованы посредством кода.
>
>

Здесь демонстрируется поток с использованием интерфейса REST API.

### <a name="step-1-create-a-data-source"></a>Шаг 1. Создание источника данных
Источник данных определяет, какие данные следует индексировать, какие учетные данные требуются для доступа к данным и какие политики нужны, чтобы эффективно выявлять изменения в данных (новые, измененные или удаленные строки). Источник данных могут использовать несколько индексаторов в одной службе поиска.

Чтобы индексировать большие двоичные объекты, источник данных должен иметь следующие необходимые свойства.

* Свойство **name** — уникальное имя источника данных в службе поиска.
* Свойство **type** должно иметь значение `azureblob`.
* Свойство **credentials** предоставляют строку подключения к учетной записи как параметр `credentials.connectionString`. Дополнительные сведения см. в разделе [Как указать учетные данные](#Credentials) ниже.
* Свойство **container** определяет контейнер в вашей учетной записи хранения. По умолчанию все большие двоичные объекты в контейнере доступны для извлечения. Если требуется индексирование больших двоичных объектов из определенного виртуального каталога, можно указать этот каталог с помощью дополнительного параметра **query**.

Создание источника данных

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }   

Чтобы больше узнать об API создания источника данных, ознакомьтесь с [созданием источника данных](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>Как указать учетные данные ####

Учетные данные для контейнера BLOB-объектов можно указать одним из описанных ниже способов.

- **Строка подключения учетной записи хранения с полным доступом**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>`. Строку подключения можно получить на портале Azure, перейдя в колонку учетной записи хранения и щелкнув "Параметры" > "Ключи" (для классических учетных записей хранения) или "Параметры" > "Ключи доступа" (для учетных записей хранения Azure Resource Manager).
- **Строка подключения с подписанным URL-адресом (SAS) учетной записи хранения**. `BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl` Подписанный URL-адрес должен иметь разрешения на перечисление и чтение для контейнеров и объектов (в этом случае BLOB-объектов).
-  **Подписанный URL-адрес контейнера**. `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl` Подписанный URL-адрес должен иметь разрешения на перечисление и чтение для контейнера.

Дополнительные сведения о подписанных URL-адресах см. в статье [Использование подписанных URL-адресов (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Если используются учетные данные SAS, то необходимо периодически обновлять учетные данные источника данных с помощью обновленных подписей, чтобы не истек их срок действия. В случае истечения срока действия учетных данных SAS произойдет сбой индексатора и появится сообщение об ошибке примерно следующего содержания: `Credentials provided in the connection string are invalid or have expired.`.  

### <a name="step-2-create-an-index"></a>Шаг 2. Создание индекса
Индекс задает поля в документе, атрибуты, и другие компоненты, которые определяют процедуру поиска.

Ниже описан процесс создания индекса с доступным для поиска полем `content` для хранения текста, извлеченного из больших двоичных объектов:   

    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }

Дополнительные сведения о создании индексов см. в статье [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) (Создание индекса).

### <a name="step-3-create-an-indexer"></a>Шаг 3. Создание индексатора
Индексатор соединяет источник данных с целевым индексом поиска и предоставляет расписание для автоматизации обновления данных.

Когда индекс и источник данных уже созданы, можно создать индексатор:

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Этот индексатор будет выполняться каждые два часа (интервал расписания имеет значение "PT2H"). Чтобы запускать индексатор каждые 30 минут, задайте интервал "PT30M". Самый короткий интервал, который можно задать, составляет 5 минут. Расписание является необязательным. Если оно не указано, то индексатор выполняется только один раз при его создании. Однако индексатор можно запустить по запросу в любое время.   

Дополнительные сведения об API создания индексатора см. в разделе [Создание индексатора](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

## <a name="how-azure-search-indexes-blobs"></a>Индексирование больших двоичных объектов с помощью службы поиска Azure

В зависимости от [конфигурации](#PartsOfBlobToIndex) индексатор больших двоичных объектов может индексировать только метаданные хранилища (удобно, если вам необходимо индексировать только метаданные и не требуется индексировать содержимое больших двоичных объектов), метаданные хранилища и содержимое или и метаданные, и текстовое содержимое. По умолчанию индексатор извлекает и метаданные, и содержимое.

> [!NOTE]
> По умолчанию большие двоичные объекты со структурированным содержимым, например JSON- или CSV-файлы, индексируются как один блок текста. Если необходимо индексировать большие двоичные объекты JSON и CSV структурированным способом, сведения о предварительной версии этих функций см. в статьях [Индексирование больших двоичных объектов JSON с помощью индексатора больших двоичных объектов службы поиска Azure](search-howto-index-json-blobs.md) и [Индексирование больших двоичных объектов CSV с помощью индексатора больших двоичных объектов службы поиска Azure](search-howto-index-csv-blobs.md).
>
> Составной или внедренный документ (например, ZIP-файл или документ Word с внедренным электронным сообщением Outlook, содержащим вложения) также индексируется как один документ.

* Текстовое содержимое документа извлекается в строковое поле `content`.

> [!NOTE]
> Поиск Azure ограничивает объем извлекаемого текста в зависимости от ценовой категории: 32 000 знаков для уровня "Бесплатный", 64 000 знаков для уровня "Базовый" и 4 миллиона знаков для уровней "Стандартный", Standard S2 и Standard S3. Предупреждение об усеченных документах отобразится в возвращенном состоянии индексатора.  

* В большом двоичном объекте определяемые пользователем свойства метаданных извлекаются без изменений.
* Стандартные свойства метаданных большого двоичного объекта извлекаются в указанные ниже поля.

  * **metadata\_storage\_name** (Edm.String) — имя файла большого двоичного объекта. Например, для большого двоичного объекта /my-container/my-folder/subfolder/resume.pdf значение этого поля — `resume.pdf`.
  * **metadata\_storage\_path** (Edm.String) — полный универсальный код ресурса (URI) большого двоичного объекта, включая учетную запись хранения. Например, `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`
  * **metadata\_storage\_content\_type** (Edm.String) — тип содержимого, указанный в коде для отправки большого двоичного объекта. Например, `application/octet-stream`.
  * **metadata\_storage\_last\_modified** (Edm.DateTimeOffset) — последняя измененная метка времени для большого двоичного объекта. Поиск Azure использует эту метку времени для выявления измененных больших двоичных объектов, чтобы не выполнять повторное полное индексирование.
  * **metadata\_storage\_size** (Edm.Int64) — размер большого двоичного объекта в байтах.
  * **metadata\_storage\_content\_metadatastoragecontentmd5** (Edm.String) — хэш MD5 содержимого большого двоичного объекта, если он доступен.
* Определенные для каждого формата документа свойства метаданных извлекаются в поля, список которых приводится [здесь](#ContentSpecificMetadata).

Вам не нужно определять поля для всех перечисленных свойств в индексе поиска — достаточно записать свойства, необходимые для приложения.

> [!NOTE]
> Как правило, имена полей в существующем индексе отличаются от имен полей, созданных при извлечении документа. Можно использовать **сопоставления полей** для сопоставления имен свойств, предоставленных Поиском Azure, с именами полей в индексе поиска. Ниже вы увидите пример использования сопоставления полей.
>
>

<a name="DocumentKeys"></a>
### <a name="defining-document-keys-and-field-mappings"></a>Определение ключей документа и сопоставлений полей
В службе поиска Azure ключ документа однозначно определяет документ. Каждый индекс поиска должен содержать только одно поле ключа типа Edm.String. Поле ключа является обязательным для каждого документа, который добавляется к индексу (собственно, это единственное обязательное для заполнения поле).  

Следует определить, какие извлеченные поля должны сопоставляться с полем ключа индекса. Основные варианты представлены ниже.

* **metadata\_storage\_name** — удобный вариант, но следует учесть следующее. 1) Имена могут не быть уникальными, так как возможно наличие больших двоичных объектов с одинаковыми именами в разных папках. 2) Имя может содержать знаки, недопустимые для использования в ключах документов, например дефисы. Устранить проблему недопустимых знаков можно с помощью [функции сопоставления полей](search-indexer-field-mappings.md#base64EncodeFunction) `base64Encode`. После ее применения обязательно закодируйте ключи документов для передачи в вызовах API, таких как вызов для поиска. (Например, в .NET для этого можно использовать [метод UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx)).
* **metadata\_storage\_path** — использование полного пути гарантирует уникальность, но такой путь определенно содержит знаки `/`, [недопустимые в ключе документа](https://docs.microsoft.com/rest/api/searchservice/naming-rules).  Как упоминалось выше, вы можете закодировать ключи с помощью [функции](search-indexer-field-mappings.md#base64EncodeFunction) `base64Encode`.
* Если ни один вариант не подходит, вы можете добавить пользовательское свойство метаданных в большие двоичные объекты. Однако для этого варианта требуется, чтобы при передаче эти свойства метаданных добавлялись ко всем большим двоичным объектам. Поскольку ключ является обязательным свойством, все большие двоичные объекты без этого свойства индексироваться не будут.

> [!IMPORTANT]
> Если в индексе отсутствует явное сопоставление поля ключа, то Поиск Azure автоматически использует `metadata_storage_path` в качестве ключа и применяет кодировку base-64 для значений ключей (второй вариант из перечисленных выше).
>
>

В этом примере мы выберем поле `metadata_storage_name` в качестве ключа документа. Предположим, что индекс содержит поле ключа с именем `key` и поле `fileSize` для хранения данных о размере документа. Чтобы правильно связать все компоненты при создании или обновлении индексатора, укажите следующие сопоставления полей:

    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]

Чтобы объединить все компоненты, можно добавить сопоставления полей и активировать кодировку ключей base-64 для существующего индексатора:

    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "dataSourceName" : " blob-datasource ",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "fieldMappings" : [
        { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
        { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
      ]
    }

> [!NOTE]
> Дополнительные сведения о сопоставлении полей см. в [этой статье](search-indexer-field-mappings.md).
>
>

<a name="WhichBlobsAreIndexed"></a>
## <a name="controlling-which-blobs-are-indexed"></a>Управление индексированием больших двоичных объектов
Вы можете выбирать, какие большие двоичные объекты необходимо индексировать, а какие — пропустить.

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>Индексация только BLOB-объектов с определенными расширениями файлов
Параметр конфигурации индексатора `indexedFileNameExtensions` позволяет индексировать большие двоичные объекты только с указанными расширениями имен файлов. Значение представлено строкой, содержащей разделенный запятыми список расширений файлов (с предшествующей точкой). Например, чтобы индексировать только большие двоичные объекты PDF и DOCX, выполните следующую команду:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }

### <a name="exclude-blobs-with-specific-file-extensions"></a>Исключение больших двоичных объектов с определенными расширениями файлов
Большие двоичные объекты с определенными расширениями файлов можно исключить из индексации с помощью параметра конфигурации `excludedFileNameExtensions`. Значение представлено строкой, содержащей разделенный запятыми список расширений файлов (с предшествующей точкой). Например, для индексации всех больших двоичных объектов, кроме объектов с расширениями PNG и JPEG, выполните следующую команду:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }

Если присутствуют оба параметра `indexedFileNameExtensions` и `excludedFileNameExtensions`, то Azure сначала выполняет поиск по `indexedFileNameExtensions`, а затем по `excludedFileNameExtensions`. Это означает, что если одно расширение файла присутствует в обоих списках, объект будет исключен из индексации.

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>Управление индексированием частей большого двоичного объекта

Вы можете выбрать, какие части большого двоичного объекта необходимо индексировать, с помощью параметра конфигурации `dataToExtract`. Этот параметр может принимать перечисленные ниже значения.

* `storageMetadata` — указывает, что необходимо индексировать только [стандартные свойства большого двоичного объекта и метаданные, определяемые пользователем](../storage/blobs/storage-properties-metadata.md).
* `allMetadata` — указывает, что необходимо индексировать метаданные хранилища и [метаданные определенного типа содержимого](#ContentSpecificMetadata), извлеченные из содержимого большого двоичного объекта.
* `contentAndMetadata` — указывает, что необходимо индексировать все метаданные и текстовое содержимое, извлеченное из большого двоичного объекта. Это значение по умолчанию.

Например, чтобы индексировать только метаданные хранилища, используйте следующую команду:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>Использование метаданных большого двоичного объекта для определения способа индексирования больших двоичных объектов

Описанные выше параметры конфигурации применяются ко всем большим двоичным объектам. В некоторых случаях вам может потребоваться определять способ индексирования *отдельных больших двоичных объектов*. Это можно сделать, добавив следующие свойства и значения метаданных большого двоичного объекта.

| Имя свойства | Значение свойства | Пояснение |
| --- | --- | --- |
| AzureSearch_Skip |True |Указывает индексатору больших двоичных объектов пропустить весь большой двоичный объект. Не извлекаются ни метаданные, ни содержимое. Это полезно, когда обработка определенного большого двоичного объекта постоянно завершается сбоем и индексирование прерывается. |
| AzureSearch_SkipContent |True |Это эквивалент параметра `"dataToExtract" : "allMetadata"`, описанного [выше](#PartsOfBlobToIndex), относящегося к определенному большому двоичному объекту. |

<a name="DealingWithErrors"></a>
## <a name="dealing-with-errors"></a>Обработка ошибок

По умолчанию индексатор больших двоичных объектов останавливается, как только обнаружит большой двоичный объект с неподдерживаемым типом содержимого (например, изображение). Чтобы пропустить определенные типы содержимого, можно воспользоваться параметром `excludedFileNameExtensions`. Тем не менее может потребоваться индексировать большие двоичные объекты, не зная все возможные типы их содержимого. Чтобы продолжить индексирование при обнаружении неподдерживаемого типа содержимого, задайте для параметра конфигурации `failOnUnsupportedContentType` значение `false`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }

Для некоторых больших двоичных объектов служба "Поиск Azure" не может определить тип содержимого или обработать документ либо другой поддерживаемый тип содержимого. Чтобы пропустить этот сбой, задайте для `failOnUnprocessableDocument` параметра конфигурации значение False:

      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }

Поиск Azure ограничивает размер больших двоичных объектов, которые индексируются. Эти ограничения описаны в [ограничениях служб в Поиске Azure](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity). Большие двоичные объекты слишком большого размера по умолчанию считаются ошибками. Но вы все равно можете индексировать метаданные хранилища больших двоичных объектов слишком большого размера, если для параметра конфигурации `indexStorageMetadataOnlyForOversizedDocuments` задать значение true: 

    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }

Вы также можете продолжить индексирование, если ошибки возникают в какой-либо момент обработки — при анализе больших двоичных объектов или при добавлении документов в индекс. Чтобы пропустить определенное количество ошибок, задайте для параметров конфигурации `maxFailedItems` и `maxFailedItemsPerBatch` нужные значения. Например: 

    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }

## <a name="incremental-indexing-and-deletion-detection"></a>Добавочное индексирование и обнаружение удаления 
Когда для индексатора больших двоичных объектов вы настраиваете запуск по расписанию, повторно индексируются только измененные большие двоичные объекты. Они определяются по метке времени большого двоичного объекта `LastModified`.

> [!NOTE]
> Нет необходимости указывать политику обнаружения изменений — добавочное индексирование будет активировано автоматически.

Для удаления документов используйте механизм обратимого удаления. Если просто удалить большие двоичные объекты, соответствующие документы останутся в индексе поиска. Чтобы этого не произошло, выполните следующие действия.  

1. Добавьте в большой двоичный объект пользовательское свойство метаданных, указывающее Поиску Azure, что этот объект логически удален.
2. Настройте политику обнаружения обратимого удаления в источнике данных.
3. После обработки большого двоичного объекта индексатором (подтвержденной состоянием API индексатора) можно будет физически удалить большой двоичный объект.

Например, в соответствии с приведенной ниже политикой большой двоичный объект считается удаленным, если у него есть свойство метаданных `IsDeleted` со значением `true`.

    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",     
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }   

## <a name="indexing-large-datasets"></a>Индексирование больших наборов данных

Индексирование больших двоичных объектов может занимать много времени. Если вам необходимо индексировать миллионы больших двоичных объектов, этот процесс можно ускорить, секционировав данные и используя несколько индексаторов для обработки данных в параллельном режиме. Вот как это можно сделать.

- Секционируйте данные в несколько контейнеров больших двоичных объектов или виртуальных папок.
- Настройте несколько источников данных службы поиска Azure, по одному на контейнер или папку. Чтобы указать папку большого двоичного объекта, используйте параметр `query`:

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- Создайте соответствующий индексатор для каждого источника данных. Все индексаторы могут указывать на один и тот же целевой индекс поиска.  

- Одна единица поиска в службе в определенный момент времени может запустить один индексатор. Создание нескольких индексаторов, как описано выше, полезно только при их фактическом параллельном выполнении. Для параллельного выполнения нескольких индексаторов следует увеличить масштаб службы поиска, создав достаточное число секций и реплик. Например, если служба поиска содержит 6 единиц поиска (скажем, 2 секции x 3 реплики), то параллельно могут выполняться 6 индексаторов. Это позволяет достичь шестикратного увеличения пропускной способности индексации. Дополнительные сведения о масштабировании и планировании емкости см. в статье [Масштабирование уровней ресурсов для рабочих нагрузок запросов и индексирования в Поиске Azure](search-capacity-planning.md).

## <a name="indexing-documents-along-with-related-data"></a>Индексация документов и связанных данных

Возможно, вам нужно создать в индексе "сборку" документов из нескольких источников. Например, можно объединить текст из BLOB-объектов с другими метаданными, хранящимися в Cosmos DB. Можно даже использовать API принудительного индексирования вместе с другими индексаторами, чтобы создавать документы для поиска из нескольких частей. 

Чтобы это работало, ключ документа должен быть согласован для всех индексаторов и других компонентов. Подробное пошаговое руководство см. в статье [Combine documents with other data in Azure Search](http://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html) (Объединение документов с другими данными в службе "Поиск Azure").

<a name="IndexingPlainText"></a>
## <a name="indexing-plain-text"></a>Индексирование обычного текста 

Если все BLOB-объекты содержат обычный текст в одной и той же кодировке, можно существенно повысить производительность индексирования, используя **текстовый режим анализа**. Чтобы использовать текстовый режим анализа, задайте для свойства конфигурации `parsingMode` значение `text`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }

По умолчанию предполагается кодировка `UTF-8`. Чтобы указать другую кодировку, используйте свойство конфигурации `encoding`: 

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }


<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>Свойства метаданных определенного типа содержимого
В таблице ниже содержатся сводные данные обработки для каждого формата документа, а также описание свойств метаданных, извлеченных службой поиска Azure.

| Формат документа или тип содержимого | Свойства метаданных определенного типа содержимого | Сведения об обработке |
| --- | --- | --- |
| HTML (`text/html`) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Удаление разметки HTML и извлечение текста |
| PDF (`application/pdf`) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Извлечение текста, включая внедренные документы (кроме изображений) |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Извлечение текста, включая внедренные документы |
| DOC (application/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Извлечение текста, включая внедренные документы |
| XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Извлечение текста, включая внедренные документы |
| XLS (application/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Извлечение текста, включая внедренные документы |
| PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Извлечение текста, включая внедренные документы |
| PPT (application/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Извлечение текста, включая внедренные документы |
| MSG (application/vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_message_bcc`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Извлечение текста, включая вложения |
| ZIP (application/zip) |`metadata_content_type` |Извлечение текста из всех документов в архиве |
| XML (application/xml) |`metadata_content_type`</br>`metadata_content_encoding`</br> |Удаление разметки XML и извлечение текста |
| JSON (application/json) |`metadata_content_type`</br>`metadata_content_encoding` |Извлечение текста<br/>Примечание. Инструкции по извлечению нескольких полей документа из большого двоичного объекта JSON см. в статье [Индексирование BLOB-объектов JSON с помощью индексатора BLOB-объектов службы поиска Azure](search-howto-index-json-blobs.md). |
| EML (message/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Извлечение текста, включая вложения |
| RTF (приложение или RTF) |`metadata_content_type`</br>`metadata_author`</br>`metadata_character_count`</br>`metadata_creation_date`</br>`metadata_page_count`</br>`metadata_word_count`</br> | Извлечение текста|
| Обычный текст (text/plain) |`metadata_content_type`</br>`metadata_content_encoding`</br> | Извлечение текста|


## <a name="help-us-make-azure-search-better"></a>Помогите нам усовершенствовать службу поиска Azure
Если вам нужна какая-либо функция или у вас есть идеи, которые можно было бы реализовать, сообщите об этом на [сайте UserVoice](https://feedback.azure.com/forums/263029-azure-search/).
