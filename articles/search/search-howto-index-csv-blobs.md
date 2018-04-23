---
title: Индексирование больших двоичных объектов в формате CSV с помощью индексатора больших двоичных объектов службы поиска Azure | Документация Майкрософт
description: Из этой статьи вы узнаете, как индексировать BLOB-объекты в формате CSV с помощью службы поиска Azure.
author: chaosrealm
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 12/28/2017
ms.author: eugenesh
ms.openlocfilehash: dfb1bd48a47e45363e8761a3d79901e5171b37d1
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>Индексирование BLOB-объектов в формате CSV с помощью индексатора BLOB-объектов службы поиска Azure
По умолчанию [индексатор BLOB-объектов службы поиска Azure](search-howto-indexing-azure-blob-storage.md) анализирует текстовые BLOB-объекты (с разделителями) как один блок текста. Однако в больших двоичных объектах, содержащих CSV-данные, часто возникает необходимость обрабатывать каждую строку объекта как отдельный документ. Например, такой текст с разделителями — 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

— может понадобиться проанализировать так, чтобы получить два документа, каждый с полями "id", "datePublished" и "tags".

Из этой статьи вы узнаете, как анализировать большие двоичные объекты в формате CSV с помощью индексатора BLOB-объектов службы поиска Azure. 

> [!IMPORTANT]
> Сейчас эта функциональность доступна в режиме предварительной версии. Она доступна при использовании REST API версии **2015-02-28-Preview**. Помните, что предварительные версии API предназначены для тестирования и ознакомления. Они не должны использоваться в рабочей среде. 
> 
> 

## <a name="setting-up-csv-indexing"></a>Настройка индексирования CSV
Чтобы индексировать BLOB-объекты в формате CSV, создайте или обновите определение индексатора с помощью режима анализа `delimitedText` .  

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

Дополнительные сведения об API создания индексатора см. в разделе [Создание индексатора](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

`firstLineContainsHeaders` указывает, что первая (непустая) строка каждого BLOB-объекта содержит заголовки.
Если большие двоичные объекты не содержат строку заголовка, заголовки следует указать в конфигурации индексатора. 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Можно настроить символ разделителя с помощью параметра конфигурации `delimitedTextDelimiter`. Например: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> Сейчас поддерживается только кодирование UTF-8. Если вам нужна поддержка других форматов кодирования, сообщите нам об этом на [сайте UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> Когда вы используете режим анализа текста с разделителями, служба поиска Azure предполагает, что все BLOB-объекты в источнике данных являются CSV-объектами. Если необходима поддержка как CSV-объектов, так и других больших двоичных объектов в одном источнике данных, сообщите нам об этом на [нашем сайте UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Примеры запросов
Чтобы свести все описанное выше вместе, ознакомьтесь с приведенными далее примерами данных. 

Источник данных: 

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Индексатор:

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-search-better"></a>Помогите нам усовершенствовать службу поиска Azure
Если вам нужна какая-либо функция или у вас есть идеи, которые можно было бы реализовать, сообщите об этом на [сайте UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

