---
title: "Индексирование хранилища таблиц Azure с помощью поиска Azure"
description: "Узнайте, как индексировать данные в таблицах Azure с помощью поиска Azure"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 1cc27411-d0cc-40ed-8aed-c7cb9ab402b9
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 04/10/2017
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: 757d6f778774e4439f2c290ef78cbffd2c5cf35e
ms.openlocfilehash: 9b45ab6b86ab0a336b2a4b90e702fa4ff098d41c
ms.lasthandoff: 04/10/2017

---

# <a name="indexing-azure-table-storage-with-azure-search"></a>Индексирование хранилища таблиц Azure с помощью поиска Azure
В этой статье показано, как использовать поиск Azure для индексирования данных в хранилище таблиц Azure.

## <a name="setting-up-azure-table-indexing"></a>Настройка индексирования таблиц Azure

Индексатор таблицы Azure можно настроить с помощью следующих средств.

* [Портал Azure](https://ms.portal.azure.com)
* [REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations) Поиска Azure;
* [пакета SDK .NET для Поиска Azure](https://aka.ms/search-sdk);

Здесь демонстрируется поток с использованием интерфейса REST API. 

### <a name="step-1-create-a-data-source"></a>Шаг 1. Создание источника данных

Источник данных определяет следующее: какие данные нужно индексировать, какие учетные данные требуются для доступа к этим данным, а также какие политики нужны, чтобы служба поиска Azure могла эффективно определять изменения в данных.

Чтобы индексировать таблицы, источник данных должен иметь следующие свойства.

- Свойство **name** — уникальное имя источника данных в службе поиска.
- Свойство **type** должно иметь значение `azuretable`.
- Параметр **credentials** содержит строку подключения учетной записи хранилища. Дополнительные сведения см. в разделе [Как указать учетные данные](#Credentials).
- Свойство **container** задает имя таблицы и необязательный запрос.
    - Укажите имя таблицы с помощью параметра `name`.
    - При необходимости укажите запрос с помощью параметра `query`. 

> [!IMPORTANT] 
> По возможности для повышения производительности используйте фильтр на PartitionKey. После любого другого запроса выполняется сканирование всей таблицы, что приводит к снижению производительности для больших таблиц. См. раздел [Рекомендации по производительности](#Performance).


Создание источника данных

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

Чтобы больше узнать об API создания источника данных, ознакомьтесь с [созданием источника данных](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>Как указать учетные данные ####

Учетные данные для таблицы можно указать одним из описанных ниже способов. 

- **Строка подключения учетной записи хранения с полным доступом**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>`. Строку подключения можно получить на портале Azure, перейдя в колонку учетной записи хранения и щелкнув "Параметры" > "Ключи" (для классических учетных записей хранения) или "Параметры" > "Ключи доступа" (для учетных записей хранения Azure Resource Manager).
- Строка подключения с **подписанным URL-адресом (SAS) учетной записи хранения**: `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl`. Подписанный URL-адрес должен иметь разрешения "Список" и "Чтение" для контейнеров (в данном случае — таблиц) и объектов (строк таблицы).
-  **Подписанный URL-адрес таблицы**: `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r`. Подписанный URL-адрес должен иметь разрешения на запрос (чтение) для таблицы.

Дополнительные сведения о подписанных URL-адресах см. в статье [Использование подписанных URL-адресов (SAS)](../storage/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Если используются учетные данные SAS, то необходимо периодически обновлять учетные данные источника данных с помощью обновленных подписей, чтобы не истек их срок действия. В случае истечения срока действия учетных данных SAS произойдет сбой индексатора и появится сообщение об ошибке примерно следующего содержания: `Credentials provided in the connection string are invalid or have expired.`.  

### <a name="step-2-create-an-index"></a>Шаг 2. Создание индекса
Индекс задает поля в документе, атрибуты, и другие компоненты, которые определяют процедуру поиска.

Вот как можно создать индекс:

    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
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
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Этот индексатор выполняется каждые два часа (интервал расписания имеет значение PT2H). Чтобы запускать индексатор каждые 30 минут, задайте интервал "PT30M". Самый короткий интервал, который можно задать, составляет 5 минут. Расписание является необязательным. Если оно не указано, то индексатор выполняется только один раз при его создании. Однако индексатор можно запустить по запросу в любое время.   

Дополнительные сведения об API создания индексатора см. в разделе [Создание индексатора](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

## <a name="dealing-with-different-field-names"></a>Работа с различными именами полей
Иногда имена полей в существующем индексе отличаются от имен свойств в таблице. Для сопоставления имен свойств таблицы с именами полей в индексе поиска можно использовать **сопоставления полей**. Дополнительные сведения о сопоставлениях полей см. в статье [Сопоставления полей индексатора в поиске Azure устраняют расхождения между источниками данных и индексами поиска](search-indexer-field-mappings.md).

## <a name="handling-document-keys"></a>Обработка ключей документа
В службе поиска Azure ключ документа однозначно определяет документ. Каждый индекс поиска должен содержать только одно поле ключа типа `Edm.String`. Поле ключа является обязательным для каждого документа, который добавляется к индексу (собственно, это единственное обязательное для заполнения поле).

Так как строки таблицы имеют составной ключ, Поиск Azure создает синтетическое поле с именем `Key` , которое представляет собой объединение значений ключа секции и ключа строки. Например, если PartitionKey равен `PK1` и RowKey равен `RK1`, то значение поля `Key` равно `PK1RK1`.

> [!NOTE]
> Значение `Key` может содержать знаки, недопустимые в ключах документов, например дефисы. С недопустимыми знаками можно работать с помощью [функции сопоставления полей](search-indexer-field-mappings.md#base64EncodeFunction) `base64Encode`. В этом случае при передаче ключей документов в вызовах API (например, при поиске) необходимо также использовать безопасное кодирование строки входных данных в Base64.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>Добавочное индексирование и обнаружение удаления 
При настройке запуска индексатора таблицы по расписанию он повторно индексирует только новые или обновленные строки в соответствии со значением строки `Timestamp` . Политику обнаружения изменений указывать не нужно — добавочное индексирование будет включено автоматически.

Чтобы указать, что определенные документы необходимо удалить из индекса, можно использовать стратегию обратимого удаления. Вместо удаления строки добавьте свойство, чтобы указать, что она удалена, и настройте политику обнаружения обратимого удаления в источнике данных. Например, следующая политика считает строку удаленной, если строка имеет свойство `IsDeleted` со значением `"true"`:

    PUT https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "<query>" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   

<a name="Performance"></a>
## <a name="performance-considerations"></a>Рекомендации по производительности

По умолчанию Поиск Azure использует следующий фильтр запросов: `Timestamp >= HighWaterMarkValue`. Поскольку в таблицах Azure отсутствует вторичный индекс в поле `Timestamp`, запрос такого типа требует просмотра всей таблицы и поэтому выполняется медленно для больших таблиц.


Ниже приведены два возможных метода повышения производительности индексирования таблиц. Оба эти метода зависят от секционирования таблиц: 

- Если данные естественным образом можно разделить на несколько секций, создайте источник данных и соответствующий индексатор для каждой секции. Каждый индексатор теперь должен обрабатывать только отдельную секцию, что повышает производительность при обработке запросов. Если индексируемые данные содержат небольшое количество фиксированных секций, это еще лучше — каждый индексатор только сканирует секцию. Например, чтобы создать источник данных для обработки секции с ключами от `000` до `100`, используйте запрос, аналогичный следующему. 
    ```
    "container" : { "name" : "my-table", "query" : "PartitionKey ge '000' and PartitionKey lt '100' " }
    ```

- Если данные секционированы по времени (например, секции могут создаваться каждый день или каждую неделю), рекомендуется следующий метод. 
    - Используйте запрос в таком формате: `(PartitionKey ge <TimeStamp>) and (other filters)` 
    - Отслеживайте работу индексатора с помощью [Получить API-интерфейс состояния индексатора](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) и периодически обновляйте условие `<TimeStamp>` запроса, основываясь на значении последней успешной метки максимального уровня. 
    - В этом методе, если необходимо запустить полное повторное индексирование, следует сбросить запрос источника данных помимо сброса собственно индексатора. 


## <a name="help-us-make-azure-search-better"></a>Помогите нам усовершенствовать службу поиска Azure
Если вам нужна какая-либо функция или у вас есть идеи, которые можно было бы реализовать, сообщите об этом на [сайте UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

