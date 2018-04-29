---
title: Копирование данных в базу данных Azure Cosmos DB и из нее с помощью фабрики данных | Документация Майкрософт
description: Узнайте, как копировать данные из поддерживаемых исходных хранилищ данных в Azure Cosmos DB или из Cosmos DB в поддерживаемые хранилища-приемники с помощью фабрики данных.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jingwang
ms.openlocfilehash: 58e1c88629c21940e09efd6832d536c0b2b47ace
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="copy-data-to-or-from-azure-cosmos-db-using-azure-data-factory"></a>Копирование данных в базу данных Azure Cosmos DB или из нее с помощью фабрики данных Azure

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Версия 1 — общедоступная](v1/data-factory-azure-documentdb-connector.md)
> * [Версия 2 — предварительная](connector-azure-cosmos-db.md)

Из этой статьи вы узнаете, как с помощью действия копирования в фабрике данных Azure копировать данные в базу данных Azure Cosmos DB и из нее (API SQL). Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если используется служба фабрики данных версии 1, которая является общедоступной версией, ознакомьтесь со статьей [Move data to and from Azure Cosmos DB using Azure Data Factory](v1/data-factory-azure-documentdb-connector.md) (Перемещение данных в базу данных Azure Cosmos DB или из нее с помощью фабрики данных Azure).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные можно скопировать из Azure Cosmos DB в любое поддерживаемое хранилище данных-приемник или скопировать данные из любого исходного хранилища данных в Azure Cosmos DB. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, см. в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

В частности, Azure Cosmos DB поддерживает:

- [API SQL](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction) для Cosmos DB.
- Импорт и экспорт документов JSON "как есть", копирование данных в набор табличных данных и из него, например базу данных SQL, CSV-файлы и т. д.

Сведения о копировании документов "как есть" в JSON-файлы или другую коллекцию Cosmos DB либо из них см. в разделе [Импорт и экспорт документов JSON](#importexport-json-documents).

## <a name="getting-started"></a>Приступая к работе

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, характерных для службы Azure Cosmos DB.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Azure Cosmos DB поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Для свойства type необходимо задать значение: **CosmosDb**. | Yes |
| connectionString |Укажите сведения, необходимые для подключения к базе данных Azure Cosmos DB. Обратите внимание, что необходимо указать сведения о базе данных в строке подключения, как показано в примере ниже. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |Нет  |

**Пример.**

```json
{
    "name": "CosmosDbLinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о наборах данных. Этот раздел содержит список свойств, поддерживаемых набором данных Azure Cosmos DB.

Чтобы скопировать данные в базу данных Azure Cosmos DB или из нее, задайте тип свойства набора данных **DocumentDbCollection**. Поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство type должно иметь значение **DocumentDbCollection**. |Yes |
| collectionName |Имя коллекции документов Cosmos DB. |Yes |

**Пример.**

```json
{
    "name": "CosmosDbDataset",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Схема фабрики данных

Для хранилищ данных без схемы, таких как Azure Cosmos DB, действие копирования определяет схему одним из следующих способов. Таким образом, если нужно [импортировать или экспортировать документы JSON "как есть"](#importexport-json-documents), мы рекомендуем указывать структуру данных в разделе **structure**.

1. Если указать структуру данных с помощью свойства **structure** в определении набора данных, служба фабрики данных считает эту структуру схемой. В этом случае, если строка не содержит значение столбца, ему будет присвоено значение NULL.
2. Если структура данных не указана с помощью свойства **structure** в определении набора данных, то служба фабрики данных определяет схему, используя первую строку данных. В этом случае, если первая строка не содержит полную схему, после операции копирования некоторые столбцы будут отсутствовать.

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником и приемником Azure Cosmos DB.

### <a name="azure-cosmos-db-as-source"></a>Azure DB Cosmos в качестве источника

Чтобы скопировать данные из Azure Cosmos DB, в действии копирования задайте тип источника **DocumentDbCollectionSource**. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство type источника действия копирования должно иметь значение **DocumentDbCollectionSource**. |Yes |
| query |Укажите запрос Cosmos DB для чтения данных.<br/><br/>Пример: `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Нет  <br/><br/>Если не указано, то выполняется инструкция SQL `select <columns defined in structure> from mycollection`. |
| nestingSeparator |Специальный символ, обозначающий, что документ является вложенным, и определяющий, как преобразовать результирующий набор в плоскую структуру.<br/><br/>Например, если запрос Cosmos DB возвращает вложенный результат `"Name": {"First": "John"}`, действие копирования идентифицирует имя колонки как Name.First со значением John, в котором nestedSeparator является точкой. |Нет (значение по умолчанию — точка `.`) |

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromCosmosDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Document DB input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DocumentDbCollectionSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-db-as-sink"></a>Azure DB Cosmos в качестве приемника

Чтобы скопировать данные в Azure Cosmos DB, в действии копирования задайте тип приемника **DocumentDbCollectionSink**. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство type приемника действия копирования должно иметь значение **DocumentDbCollectionSink**. |Yes |
| nestingSeparator |Такой специальный символ в имени исходного столбца, который указывает, что нужен вложенный документ. <br/><br/>Например, `Name.First` в выходной структуре набора данных создает следующую структуру JSON в документе Cosmos DB: `"Name": {"First": "[value maps to this column from source]"}`, в которой nestedSeparator является точкой. |Нет (значение по умолчанию — точка `.`) |

**Пример.**

```json
"activities":[
    {
        "name": "CopyToCosmosDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DocumentDbCollectionSink"
            }
        }
    }
]
```

## <a name="importexport-json-documents"></a>Документы JSON для импорта и экспорта

С помощью этого соединителя Cosmos DB можно выполнять следующие задачи:

* импортировать документы JSON из различных источников в Cosmos DB, включая хранилище BLOB-объектов Azure, Azure Data Lake Store или другие файловые хранилища, поддерживаемые фабрикой данных Azure;
* экспортировать документы JSON из коллекции Cosmos DB в различные файловые хранилища;
* копировать документы между коллекциями Cosmos DB "как есть".

Для обеспечения такого копирования без использования схем:

- Не указывайте раздел structure в наборах данных Cosmos DB, а свойство nestingSeparator — для источника или приемника Cosmos DB в действии копирования.
- При импорте в файлы JSON или экспорте из них в соответствующем наборе данных хранилища файлов укажите тип формата JsonFormat и свойство filePattern (дополнительные сведения см. в разделе о [формате JSON](supported-file-formats-and-compression-codecs.md#json-format)), не указывайте раздел structure и пропустите остальные параметры формата.

## <a name="next-steps"></a>Дополнительная информация
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md##supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
