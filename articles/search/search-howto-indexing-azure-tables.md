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
ms.date: 12/15/2016
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: 714045750ab16364ecd1095f1f346d3da1d4c4a5
ms.openlocfilehash: 4bfcf719cb071a28421c64dbb4d6c132f45ba9f9

---

# <a name="indexing-azure-table-storage-with-azure-search"></a>Индексирование хранилища таблиц Azure с помощью поиска Azure
В этой статье показано, как использовать поиск Azure для индексирования данных в хранилище таблиц Azure. Новый индексатор таблиц поиска Azure позволяет сделать этот процесс быстрым и эффективным.

## <a name="setting-up-azure-table-indexing"></a>Настройка индексирования таблиц Azure
Для установки и настройки индексатора таблиц Azure можно использовать REST API поиска Azure. С помощью API вы сможете создать **индексаторы** и **источники данных**, а также управлять ими, как описано в разделе [Операции индексатора](https://msdn.microsoft.com/library/azure/dn946891.aspx). Можно также использовать [версию 2.0-preview](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx) пакета SDK для .NET. В будущем поддержка индексирования таблиц будет добавлена на портал Azure.

Источник данных определяет следующее: какие данные нужно индексировать, какие учетные данные требуются для доступа к этим данным, а также какие политики нужны, чтобы служба поиска Azure могла эффективно определять изменения в данных (новые, измененные или удаленные строки).

Индексатор считывает данные из источника данных и загружает их в целевой индекс поиска.

Настройка индексации таблицы:

1. Создание источника данных
   * Задайте для параметра `type` значение `azuretable`.
   * Передайте строку подключения учетной записи хранения как параметр `credentials.connectionString`. Строку подключения можно получить на портале Azure, перейдя в колонку учетной записи хранения и щелкнув **Параметры** > **Ключи** (для классических учетных записей хранения) или **Параметры** > **Ключи доступа** (для учетных записей хранения ARM). Обратите внимание, что Поиск Azure в настоящее время не поддерживает учетные данные на основе подписанного URL-адреса. Если вы хотите использовать SAS, проголосуйте за [это предложение на сайте UserVoice](https://feedback.azure.com/forums/263029-azure-search/suggestions/12368244-support-shared-access-signature-for-blob-datasourc).
   * Укажите имя таблицы с помощью параметра `container.name`.
   * При необходимости укажите запрос с помощью параметра `container.query`. По возможности используйте фильтр для PartitionKey для наилучшей производительности. Любой другой запрос приведет к полному сканированию таблицы, что может стать причиной снижения производительности для больших таблиц.
2. Создание индекса службы поиска со схемой, соответствующей столбцам в таблице, которую необходимо проиндексировать.
3. Создайте индексатор путем подключения источника данных к индексу службы поиска.

### <a name="create-data-source"></a>Создание источника данных
    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

Чтобы больше узнать об API создания источника данных, ознакомьтесь с [созданием источника данных](https://msdn.microsoft.com/library/azure/dn946876.aspx).

### <a name="create-index"></a>Создание индекса
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

Чтобы больше узнать об API создания индекса, ознакомьтесь с [созданием индекса](https://msdn.microsoft.com/library/dn798941.aspx).

### <a name="create-indexer"></a>Создание индексатора
Наконец, создайте индексатор со ссылкой на источник данных и целевой индекс. Например:

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Дополнительные сведения об API создания индексатора см. в разделе [Создание индексатора](https://msdn.microsoft.com/library/azure/dn946899.aspx).

Это все. Удачного индексирования!

## <a name="dealing-with-different-field-names"></a>Работа с различными именами полей
Имена полей в существующем индексе часто отличаются от имен свойств в таблице. Для сопоставления имен свойств таблицы с именами полей в индексе поиска можно использовать **сопоставления полей**. Дополнительные сведения о сопоставлениях полей см. в статье [Сопоставления полей индексатора в поиске Azure устраняют расхождения между источниками данных и индексами поиска](search-indexer-field-mappings.md).

## <a name="handling-document-keys"></a>Обработка ключей документа
В службе поиска Azure ключ документа однозначно определяет документ. Каждый индекс поиска должен содержать только одно поле ключа типа `Edm.String`. Поле ключа является обязательным для каждого документа, который добавляется к индексу (собственно, это единственное обязательное для заполнения поле).

Так как строки таблицы имеют составной ключ, Поиск Azure создает синтетическое поле с именем `Key` , которое представляет собой объединение значений ключа секции и ключа строки. Например, если PartitionKey равен `PK1` и RowKey равен `RK1`, то значение поля `Key` будет равно `PK1RK1`.

> [!NOTE]
> Значение `Key` может содержать знаки, недопустимые в ключах документов, например дефисы. С недопустимыми знаками можно работать с помощью [функции сопоставления полей](search-indexer-field-mappings.md#base64EncodeFunction) `base64Encode`. В этом случае при передаче ключей документов в вызовах API (например, при поиске) необходимо также использовать безопасное кодирование строки входных данных в Base64.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>Добавочное индексирование и обнаружение удаления 
При настройке запуска индексатора таблицы по расписанию он повторно индексирует только новые или обновленные строки в соответствии со значением строки `Timestamp` . Политику обнаружения изменений указывать не нужно — добавочное индексирование будет включено автоматически.

Чтобы указать, что определенные документы необходимо удалить из индекса, можно воспользоваться стратегией обратимого удаления — вместо строки добавьте свойство, указывающее, что строка удалена, и настройте политику обнаружения обратимого удаления в источнике данных. Например, для указанной ниже политики строка удаляется, если она имеет свойство `IsDeleted` со значением `"true"`.

    PUT https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "query" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   


## <a name="help-us-make-azure-search-better"></a>Помогите нам усовершенствовать службу поиска Azure
Если вам нужна какая-либо функция или у вас есть идеи, которые можно было бы реализовать, сообщите об этом на [сайте UserVoice](https://feedback.azure.com/forums/263029-azure-search/).



<!--HONumber=Dec16_HO3-->


