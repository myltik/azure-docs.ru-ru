---
title: "Действие поиска в фабрике данных Azure | Документация Майкрософт"
description: "Узнайте, как использовать действие поиска для поиска значений во внешнем источнике. На эти выходные данные можно затем ссылаться в последующих действиях."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: shlo
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: spelluru
ms.openlocfilehash: f287b0287ad85ffe1654e0d574cd44aa4dd81a0f
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2017
---
# <a name="lookup-activity-in-azure-data-factory"></a>Действие поиска в фабрике данных Azure
Действие поиска можно использовать для считывания или поиска записи, имени таблицы и значения из внешних источников. На эти выходные данные можно затем ссылаться в последующих действиях. 

Действие поиска полезно, если необходимо динамически получить список файлов, записей и таблиц из файла конфигурации или источника данных. Выходные данные действия в дальнейшем могут использоваться другими действиями для выполнения определенной обработки только этих элементов.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, см. статью [Introduction to Azure Data Factory](v1/data-factory-introduction.md) (Введение в фабрику данных Azure).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

В настоящее время поиск поддерживается для следующих источников данных:
- JSON-файл в большом двоичном объекте Azure;
- JSON-файл в файловой системе
- база данных SQL Azure (данные JSON, преобразованные из запроса);
- Хранилище данных SQL Azure (данные JSON, преобразованные из запроса)
- SQL Server (данные JSON, преобразованные из запроса)
- хранилище таблиц Azure (данные JSON, преобразованные из запроса).

## <a name="syntax"></a>Синтаксис

```json
{
    "name": "LookupActivity",
    "type": "Lookup",
    "typeProperties": {
        "source": {
            "type": "<source type>"
            <additional source specific properties (optional)>
        },
        "dataset": { 
            "referenceName": "<source dataset name>",
            "type": "DatasetReference"
        },
        "firstRowOnly": false
    }
}
```

## <a name="type-properties"></a>Свойства типа
ИМЯ | ОПИСАНИЕ | type | Требуется
---- | ----------- | ---- | --------
dataset | Атрибут набора данных требуется для ссылки на набор данных для поиска. В настоящее время поддерживаются следующие типы наборов данных:<ul><li>`AzureBlobDataset`для [хранилища больших двоичных объектов](connector-azure-blob-storage.md#dataset-properties) как источник</li><li>`FileShareDataset`для [файловой системы](connector-file-system.md#dataset-properties) как источник</li><li>`AzureSqlTableDataset`для [базы данных SQL Azure](connector-azure-sql-database.md#dataset-properties) или [хранилище данных SQL Azure](connector-azure-sql-data-warehouse.md#dataset-properties) как источник</li><li>`SqlServerTable`для [SQL Server](connector-sql-server.md#dataset-properties) как источник</li><li>`AzureTableDataset`для [табличного хранилища Azure](connector-azure-table-storage.md#dataset-properties) как источник</li> | Пара "ключ — значение" | Yes
источник | Свойства источника конкретного набора данных, то же, что источник копирования действия. Дополнительные сведения из раздела «Копировать свойства действия» в каждой соответствующей статьи соединителя. | Пара "ключ — значение" | Yes
firstRowOnly | Укажите, следует ли возвращать только в первой строке или все строки. | Логическое | Нет. Значение по умолчанию — `ture`.

## <a name="use-lookup-activity-result-in-subsequent-activity"></a>Использовать результат поиска действия в последующие действия

Результат поиска возвращается в `output` раздела результат при выполнении действия.

**Когда `firstRowOnly` равно `true` (по умолчанию)**, имеет следующий формат выходных данных. Результат поиска находится в фиксированный `firstRow` ключа. Чтобы использовать в последующих действий, используйте шаблон `@{activity('MyLookupActivity').output.firstRow.TableName}`.

```json
{
    "firstRow":
    {
        "Id": "1",
        "TableName" : "Table1"
    }
}
```

**Когда `firstRowOnly` равно `false`** , имеет следующий формат выходных данных. Объект `count` поле показывает, сколько записей и подробные значения находятся в фиксированный `value` массива. В этом случае действия уточняющего запроса обычно следуют [действия Foreach](control-flow-for-each-activity.md), можно передать `value` массива действия ForEach `items` поля, используя шаблон `@activity('MyLookupActivity').output.value`. Для доступа к элементам в `value`, используйте следующий синтаксис: `@{activity('lookupActivity').output.value[zero based index].propertyname}`. Ниже приведен пример:`@{activity('lookupActivity').output.value[0].tablename}`

```json
{
    "count": "2",
    "value": [
        {
            "Id": "1",
            "TableName" : "Table1"
        },
        {
            "Id": "2",
            "TableName" : "Table2"
        }
    ]
} 
```

## <a name="example"></a>Пример
В этом примере действие копирования копирует данные из таблицы SQL в базе данных Azure SQL для хранилища больших двоичных объектов. Имя таблицы SQL хранится в файле JSON в хранилище BLOB-объектов. Действие подстановки находит имя таблицы во время выполнения. Такой подход позволяет динамически изменять файл JSON без повторного развертывания конвейеров или наборов данных. 

В этом примере показано только первая строка для поиска. Поиск всех строк и цепочки, с помощью действия ForEach, см. в разделе [учебника - копирование данных в большом количестве](tutorial-bulk-copy.md) образца.

### <a name="pipeline"></a>Конвейер
Этот конвейер содержит два действия: **поиск** и **копирование**. 

- Действие поиска настроено для использования LookupDataset, который ссылается на расположение в хранилище BLOB-объектов Azure. Действие поиска считывает имя таблицы SQL из файла JSON в этом расположении. 
- Действие копирования использует выходные данные действия поиска (имя таблицы SQL). Параметр tableName в исходном наборе данных (SourceDataset) настроен для использования выходных данных операции поиска. Действие копирования копирует данные из таблицы SQL в расположение в хранилище BLOB-объектов Azure, которое задается параметром SinkDataset. 


```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "dataset": { 
                        "referenceName": "LookupDataset", 
                        "type": "DatasetReference" 
                    }
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": { 
                        "type": "SqlSource", 
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.firstRow.tableName}" 
                    },
                    "sink": { 
                        "type": "BlobSink" 
                    }
                },                
                "dependsOn": [ 
                    { 
                        "activity": "LookupActivity", 
                        "dependencyConditions": [ "Succeeded" ] 
                    }
                 ],
                "inputs": [ 
                    { 
                        "referenceName": "SourceDataset", 
                        "type": "DatasetReference" 
                    } 
                ],
                "outputs": [ 
                    { 
                        "referenceName": "SinkDataset", 
                        "type": "DatasetReference" 
                    } 
                ]
            }
        ]
    }
}
```

### <a name="lookup-dataset"></a>Набор данных поиска
Набор данных поиска — это файл sourcetable.json в папке поиска в хранилище Azure, указанном параметром AzureStorageLinkedService. 

```json
{
    "name": "LookupDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "lookup",
            "fileName": "sourcetable.json",
            "format": {
                "type": "JsonFormat",
                "filePattern": "SetOfObjects"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="source-dataset-for-the-copy-activity"></a>Исходный набор данных для действия копирования
Исходный набор данных использует выходные данные действия поиска — имя таблицы SQL. Действие копирования копирует данные из таблицы SQL в расположение в хранилище BLOB-объектов Azure, которое задается набором данных приемника. 

```json
{
    "name": "SourceDataset",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties":{
            "tableName": "@{activity('LookupActivity').output.firstRow.tableName}"
        },
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="sink-dataset-for-the-copy-activity"></a>Набор данных приемника для действия копирования
Действие копирования копирует данные из таблицы SQL в файл filebylookup.csv в папке csv в хранилище Azure, указанном с помощью параметра AzureStorageLinkedService. 

```json
{
    "name": "SinkDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "csv",
            "fileName": "filebylookup.csv",
            "format": {
                "type": "TextFormat"                                                                    
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Связанная служба хранилища Azure
Эта учетная запись хранения содержит файл JSON с именами таблиц SQL. 

```json
{
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<StorageAccountKey>",
                "type": "SecureString"
            }
        }
    },
        "name": "AzureStorageLinkedService"
}
```

### <a name="azure-sql-database-linked-service"></a>Связанная служба базы данных SQL Azure
Эта база данных SQL Azure содержит данные, копируемые в хранилище BLOB-объектов. 

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": {
                "value": "Server=<server>;Initial Catalog=<database>;User ID=<user>;Password=<password>;",
                "type": "SecureString"
            }
        }
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.JSON

#### <a name="set-of-objects"></a>Набор объектов

```json
{
  "Id": "1",
  "tableName": "Table1",
}
{
   "Id": "2",
  "tableName": "Table2",
}
```

#### <a name="array-of-objects"></a>Массив объектов

```json
[ 
    {
        "Id": "1",
          "tableName": "Table1",
    }
    {
        "Id": "2",
        "tableName": "Table2",
    }
]
```

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь с другими действиями потока управления, которые поддерживаются фабрикой данных: 

- [Действие выполнения конвейера](control-flow-execute-pipeline-activity.md)
- [Действие ForEach](control-flow-for-each-activity.md)
- [Действие получения метаданных](control-flow-get-metadata-activity.md)
- [Веб-действие](control-flow-web-activity.md)
