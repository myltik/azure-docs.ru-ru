---
title: Сопоставление столбцов наборов данных в фабрике данных Azure | Документация Майкрософт
description: Узнайте, как сопоставить исходные столбцы с целевыми столбцами.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 1c8ea748297b8fe4052b60f08b8f00b1e41275fb
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34620363"
---
# <a name="map-source-dataset-columns-to-destination-dataset-columns"></a>Сопоставление столбцов исходного набора данных со столбцами целевого набора данных
> [!NOTE]
> Статья относится к версии 1 фабрики данных, которая является общедоступной версией. 

Сопоставление столбцов можно использовать, чтобы указать, как столбцы, указанные в «structure» исходной схемы таблицы, сопоставляются со столбцами, указанными в «structure» таблицы-приемника. Свойство **ColumnMapping** доступно в разделе **typeProperties** действия копирования.

Сопоставление столбцов применимо в следующих сценариях:

* Все столбцы в разделе structure набора данных, используемого в качестве источника, сопоставляются со всеми столбцами в разделе structure набора данных, используемого в качестве приемника.
* Подмножество столбцов в разделе structure набора данных, используемого в качестве источника, сопоставляется со всеми столбцами в разделе structure набора данных, используемого в качестве приемника.

Ниже приведены неправильные условия, которые приводят к порождению исключения.

* Меньше или больше столбцов в «structure» таблицы-приемника, чем указано в сопоставлении.
* Повторяющееся сопоставление.
* В результате запроса SQL нет имени столбца, который указан в сопоставлении.

> [!NOTE]
> Приведенные ниже примеры предназначены для SQL Azure и большого двоичного объекта Azure, но подходят для любого хранилища данных, поддерживающего прямоугольные наборы данных. В этих примерах можно настроить набор данных и определения связанных служб, чтобы указать данные в соответствующем источнике данных.

## <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>Пример 1. Сопоставление столбцов из SQL Azure с большим двоичным объектом Azure
В этом примере входная таблица имеет структуру, и она указывает на таблицу SQL в базе данных SQL Azure.

```json
{
    "name": "AzureSQLInput",
    "properties": {
        "structure": 
         [
           { "name": "userid"},
           { "name": "name"},
           { "name": "group"}
         ],
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Выходная таблица имеет структуру, и она указывает на большой двоичный объект в хранилище больших двоичных объектов Azure.

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
         "structure": 
          [
                { "name": "myuserid"},
                { "name": "myname" },
                { "name": "mygroup"}
          ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Ниже приведен фрагмент JSON, который определяет действие копирования в конвейере. Столбцы из источника сопоставляются со столбцами в приемнике (**ColumnMappings**) с помощью свойства **Translator**.

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "Copy",
    "inputs":  [ { "name": "AzureSQLInput"  } ],
    "outputs":  [ { "name": "AzureBlobOutput" } ],
    "typeProperties":    {
        "source":
        {
            "type": "SqlSource"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
        }
    },
   "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
**Процесс сопоставления столбцов:**

![Процесс сопоставления столбцов](./media/data-factory-map-columns/column-mapping-flow.png)

## <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>Пример 2. Сопоставление столбцов из Azure SQL с большим двоичным объектом Azure с помощью SQL-запроса
В этом примере используется SQL-запрос для извлечения данных из SQL Azure, а не просто указывается имя таблицы и имена столбцов в разделе «structure». 

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "CopyActivity",
    "inputs":  [ { "name": " AzureSQLInput"  } ],
    "outputs":  [ { "name": " AzureBlobOutput" } ],
    "typeProperties":
    {
        "source":
        {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "Translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
        }
    },
    "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
В этом случае сначала результаты запроса сопоставляются со столбцами, указанными в «structure» источника. Затем столбцы из «structure» источника сопоставляются со столбцами в «structure» приемника посредством правил, определенных в columnMappings.  Предположим, что запрос возвращает 5 столбцов, то есть на два столбца больше, чем указано в разделе structure источника.

**Процесс сопоставления столбцов**

![Процесс сопоставления столбцов 2](./media/data-factory-map-columns/column-mapping-flow-2.png)

## <a name="next-steps"></a>Дополнительная информация
Ознакомьтесь с руководством по использованию действия копирования в следующей статье: 

- [Копирование данных из хранилища BLOB-объектов Azure в базу данных SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
