---
title: "Индексирование хранилища BLOB-объектов Azure с помощью службы поиска Azure"
description: "Узнайте, как индексировать хранилище BLOB-объектов Azure и извлекать текст из документов с помощью службы поиска Azure."
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 2a5968f4-6768-4e16-84d0-8b995592f36a
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 11/24/2016
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: 2b62ddb83b35194b9fcd23c60773085a9551b172
ms.openlocfilehash: 041b47ed2aba11d45ed6ae02dadb73916046dd78

---

# <a name="indexing-documents-in-azure-blob-storage-with-azure-search"></a>Индексирование документов в хранилище BLOB-объектов Azure с помощью службы поиска Azure
В этой статье показано, как использовать поиск Azure для индексации документов (например, файлов PDF, Microsoft Office и некоторых других распространенных форматов), которые хранятся в хранилище BLOB-объектов Azure. Новый индексатор больших двоичных объектов поиска Azure позволяет сделать этот процесс быстрым и эффективным.

## <a name="supported-document-formats"></a>Поддерживаемые форматы документов
Индексатор больших двоичных объектов может извлечь текст из документов следующих форматов:

* PDF;
* форматы Microsoft Office: DOCX/DOC, XLSX/XLS, PPTX/PPT, MSG (сообщения электронной почты Outlook);  
* HTML
* XML
* ZIP;
* EML
* Обычные текстовые файлы  
* JSON (сведения о предварительной версии функции см. в статье [Индексирование больших двоичных объектов JSON с помощью индексатора больших двоичных объектов службы поиска Azure](search-howto-index-json-blobs.md));
* CSV (сведения о предварительной версии функции см. в статье [Индексирование больших двоичных объектов CSV с помощью индексатора больших двоичных объектов службы поиска Azure](search-howto-index-csv-blobs.md)).

> [!IMPORTANT]
> Файлы CSV и JSON сейчас поддерживаются только в предварительной версии. Эти форматы доступны только при использовании версии **2015-02-28-Preview** REST API или версии 2.x-preview пакета SDK для .NET. Помните, что предварительные версии API предназначены для тестирования и ознакомления. Они не должны использоваться в рабочей среде.
>
>

## <a name="setting-up-blob-indexing"></a>Настройка индексирования больших двоичных объектов
Можно настроить индексатор хранилище BLOB-объектов Azure с помощью следующих инструментов:

* [Портал Azure](https://ms.portal.azure.com)
* [REST API](https://msdn.microsoft.com/library/azure/dn946891.aspx) Поиска Azure;
* пакет SDK .NET для Поиска Azure [версии 2.0-preview](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx).

> [!NOTE]
> Некоторые функции (например, сопоставление полей) еще не доступны на портале и должны быть использованы посредством кода.
>
>

В этой статье мы настроим индексатор с использованием REST API. Сначала мы создадим источник данных, затем — индекс и, наконец, настроим индексатор.

После этого мы подробно обсудим то, как индексатор больших двоичных объектов анализирует большие двоичные объекты, как выбрать большие двоичные объекты для индексирования, как работать с большими двоичными объектами неподдерживаемых типов содержимого, а также параметры конфигурации. 

### <a name="step-1-create-a-data-source"></a>Шаг 1. Создание источника данных
Источник данных определяет, какие данные следует индексировать, какие учетные данные требуются для доступа к данным и какие политики нужны, чтобы эффективно выявлять изменения в данных (новые, измененные или удаленные строки). Источник данных могут использовать несколько индексаторов в одной службе поиска.

Чтобы индексировать большие двоичные объекты, источник данных должен иметь следующие необходимые свойства.

* Свойство **name** — уникальное имя источника данных в службе поиска.
* Свойство **type** должно иметь значение `azureblob`.
* Свойство **credentials** предоставляют строку подключения к учетной записи как параметр `credentials.connectionString`. Строку подключения можно получить на портале Azure. Перейдите к колонке нужной учетной записи хранения, выберите **Настройки** > **Ключи** и используйте значение "Первичная строка подключения" или "Вторичная строка подключения".
* Свойство **container** определяет контейнер в вашей учетной записи хранения. По умолчанию все большие двоичные объекты в контейнере доступны для извлечения. Если требуется индексирование больших двоичных объектов из определенного виртуального каталога, можно указать этот каталог с помощью дополнительного параметра **query**.

В следующем примере проиллюстрировано определение источника данных:

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<my storage connection string>" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }   

Чтобы больше узнать об API создания источника данных, ознакомьтесь с [созданием источника данных](https://msdn.microsoft.com/library/azure/dn946876.aspx).

### <a name="step-2-create-an-index"></a>Шаг 2. Создание индекса
Индекс задает поля в документе, атрибуты, и другие компоненты, которые определяют процедуру поиска.  

Чтобы индексировать большие двоичные объекты, убедитесь, что в индексе есть поддерживающее поиск поле `content` для хранения большого двоичного объекта.

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

Чтобы больше узнать об API создания индекса, ознакомьтесь с [созданием индекса](https://msdn.microsoft.com/library/dn798941.aspx).

### <a name="step-3-create-an-indexer"></a>Шаг 3. Создание индексатора
Индексатор соединяет источники данных с целевыми индексами поиска и предоставляет сведения о планировании, чтобы вы могли автоматизировать обновление данных. Создав индекс и источник данных, вы можете легко создать индексатор, который ссылается на источник данных и целевой индекс. Например:

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

Дополнительные сведения об API создания индексатора см. в разделе [Создание индексатора](https://msdn.microsoft.com/library/azure/dn946899.aspx).

## <a name="how-azure-search-indexes-blobs"></a>Индексирование больших двоичных объектов с помощью службы поиска Azure

В зависимости от [конфигурации](#PartsOfBlobToIndex) индексатор больших двоичных объектов может индексировать только метаданные хранилища (удобно, если вам необходимо индексировать только метаданные и не требуется индексировать содержимое больших двоичных объектов), метаданные хранилища и содержимое или и метаданные, и текстовое содержимое. По умолчанию индексатор извлекает и метаданные, и содержимое. 

> [!NOTE]
> По умолчанию большие двоичные объекты со структурированным содержимым, например файлы JSON, CSV или XML, индексируются как один блок текста. Если необходимо индексировать большие двоичные объекты JSON и CSV структурированным способом, сведения о предварительной версии этих функций см. в статьях [Индексирование больших двоичных объектов JSON с помощью индексатора больших двоичных объектов службы поиска Azure](search-howto-index-json-blobs.md) и [Индексирование больших двоичных объектов CSV с помощью индексатора больших двоичных объектов службы поиска Azure](search-howto-index-csv-blobs.md). Анализ содержимого XML сейчас не поддерживается. Если вам это необходимо, добавьте предложение на нашем сайте [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> Составной или внедренный документ (например, ZIP-файл или документ Word с внедренным электронным сообщением Outlook, содержащим вложения) также индексируется как один документ.

* Все текстовое содержимое документа извлекается в поле строки с именем `content`.
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

### <a name="picking-the-document-key-field-and-dealing-with-different-field-names"></a>Выбор поля ключа документа и работа с разными именами полей
В службе поиска Azure ключ документа однозначно определяет документ. Каждый индекс поиска должен содержать только одно поле ключа типа Edm.String. Поле ключа является обязательным для каждого документа, который добавляется к индексу (собственно, это единственное обязательное для заполнения поле).  

Следует определить, какие извлеченные поля должны сопоставляться с полем ключа индекса. Основные варианты представлены ниже.

* **metadata\_storage\_name** — удобный вариант, но следует учесть следующее. 1) Имена могут не быть уникальными, так как возможно наличие больших двоичных объектов с одинаковыми именами в разных папках. 2) Имя может содержать знаки, недопустимые для использования в ключах документов, например дефисы. Устранить проблему недопустимых знаков можно с помощью [функции сопоставления полей](search-indexer-field-mappings.md#base64EncodeFunction) `base64Encode`. После ее применения обязательно закодируйте ключи документов для передачи в вызовах API, таких как вызов для поиска. (Например, в .NET для этого можно использовать [метод UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx)).
* **metadata\_storage\_path** — использование полного пути гарантирует уникальность, но такой путь определенно содержит знаки `/`, [недопустимые в ключе документа](https://msdn.microsoft.com/library/azure/dn857353.aspx).  Как упоминалось выше, вы можете закодировать ключи с помощью [функции](search-indexer-field-mappings.md#base64EncodeFunction) `base64Encode`.
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

### <a name="dealing-with-unsupported-content-types"></a>Работа с неподдерживаемыми типами содержимого

По умолчанию индексатор больших двоичных объектов останавливается, как только обнаружит большой двоичный объект с неподдерживаемым типом содержимого (например, изображение). Чтобы пропустить определенные типы содержимого, можно воспользоваться параметром `excludedFileNameExtensions`. Тем не менее может потребоваться индексировать большие двоичные объекты, не зная все возможные типы их содержимого. Чтобы продолжить индексирование при обнаружении неподдерживаемого типа содержимого, задайте для параметра конфигурации `failOnUnsupportedContentType` значение `false`: 

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    } 

### <a name="ignoring-parsing-errors"></a>Игнорирование ошибок анализа

Логика извлечения документов службы поиска Azure несовершенна, поэтому иногда происходит сбой анализа документов поддерживаемого типа содержимого, например DOCX или PDF. Если вы не хотите прерывать индексирование в таких случаях, задайте для параметров конфигурации `maxFailedItems` и `maxFailedItemsPerBatch` приемлемые значения. Например: 

    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    } 

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>Управление индексированием частей большого двоичного объекта

Вы можете выбрать, какие части большого двоичного объекта необходимо индексировать, с помощью параметра конфигурации `dataToExtract`. Этот параметр может принимать перечисленные ниже значения. 

* `storageMetadata` — указывает, что необходимо индексировать только [стандартные свойства большого двоичного объекта и метаданные, определяемые пользователем](../storage/storage-properties-metadata.md).
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

## <a name="incremental-indexing-and-deletion-detection"></a>Добавочное индексирование и обнаружение удаления 
Когда для индексатора больших двоичных объектов вы настраиваете запуск по расписанию, повторно индексируются только измененные большие двоичные объекты. Они определяются по метке времени большого двоичного объекта `LastModified`.

> [!NOTE]
> Нет необходимости указывать политику обнаружения изменений — добавочное индексирование будет активировано автоматически.

Для удаления документов используйте механизм обратимого удаления. Если просто удалить большие двоичные объекты, соответствующие документы останутся в индексе поиска. Чтобы этого не произошло, выполните следующие действия.  

1. Добавьте в большой двоичный объект пользовательское свойство метаданных, указывающее Поиску Azure, что этот объект логически удален.
2. Настройте политику обнаружения обратимого удаления в источнике данных.
3. После обработки большого двоичного объекта индексатором (подтвержденной состоянием API индексатора) можно будет физически удалить большой двоичный объект.

Например, в соответствии с приведенной ниже политикой большой двоичный объект считается удаленным, если у него есть свойство метаданных `IsDeleted` со значением `true`.

    PUT https://[service name].search.windows.net/datasources?api-version=2016-09-01
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
| Обычный текст (text/plain) |`metadata_content_type`</br>`metadata_content_encoding`</br> | |

## <a name="help-us-make-azure-search-better"></a>Помогите нам усовершенствовать службу поиска Azure
Если вам нужна какая-либо функция или у вас есть идеи, которые можно было бы реализовать, сообщите об этом на [сайте UserVoice](https://feedback.azure.com/forums/263029-azure-search/).



<!--HONumber=Nov16_HO4-->


