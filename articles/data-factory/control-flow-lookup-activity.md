---
title: Действие поиска в фабрике данных Azure | Документация Майкрософт
description: Узнайте, как использовать действие поиска для поиска значений во внешнем источнике. На эти выходные данные можно затем ссылаться в последующих действиях.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: f55e85bb424f4f5973fd6d633b6adf9fbca4d0ef
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="lookup-activity-in-azure-data-factory"></a>Действие поиска в фабрике данных Azure
Действие поиска можно использовать для считывания или поиска записи, имени таблицы или значения из внешних источников. На эти выходные данные можно затем ссылаться в последующих действиях. 

Действие поиска полезно, если необходимо динамически получить список файлов, записей или таблиц из файла конфигурации или источника данных. Выходные данные действия в дальнейшем могут использоваться другими действиями для выполнения определенной обработки только этих элементов.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных Azure, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, используйте [документацию по версии 1 фабрики данных](v1/data-factory-introduction.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Сейчас поиск поддерживается для следующих источников данных:
- JSON-файл в хранилище BLOB-объектов Azure;
- JSON-файл в файловой системе;
- база данных SQL Azure (данные JSON, преобразованные из запроса);
- хранилище данных SQL Azure (данные JSON, преобразованные из запроса);
- SQL Server (данные JSON, преобразованные из запроса);
- хранилище таблиц Azure (данные JSON, преобразованные из запроса).

Максимальное количество строк, возвращенных операцией поиска — **5000** размером до **10 МБ**.

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
ИМЯ | ОПИСАНИЕ | type | Обязательный?
---- | ----------- | ---- | --------
dataset | Предоставляет ссылку на набор данных для поиска. В настоящее время поддерживаются следующие типы наборов данных:<ul><li>`AzureBlobDataset` для [хранилища BLOB-объектов Azure](connector-azure-blob-storage.md#dataset-properties), используемого в качестве источника</li><li>`FileShareDataset` для [файловой системы](connector-file-system.md#dataset-properties), используемой в качестве источника</li><li>`AzureSqlTableDataset` для [базы данных Azure SQL](connector-azure-sql-database.md#dataset-properties) или [хранилища данных Azure SQL](connector-azure-sql-data-warehouse.md#dataset-properties), используемых в качестве источника</li><li>`SqlServerTable` для [SQL Server](connector-sql-server.md#dataset-properties) в качестве источника</li><li>`AzureTableDataset` для [хранилища таблиц Azure](connector-azure-table-storage.md#dataset-properties), используемого в качестве источника</li> | Пара "ключ — значение" | Yes
источник | Содержит свойства источника конкретного набора данных, аналогичные источнику действия копирования. Дополнительные сведения можно найти в разделе "Свойства действия копирования" в каждой соответствующей статье о соединителе. | Пара "ключ — значение" | Yes
firstRowOnly | Указывает, следует ли возвращать только первую или все строки. | Логическое | Нет. Значение по умолчанию — `true`.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>Использование результата действия поиска в следующем действии

Результат поиска возвращается в раздел `output` результатов выполнения действия.

* **Когда для `firstRowOnly` задано значение `true` (по умолчанию)**, формат выходного значения выглядит, как показано в следующем коде. Результат поиска находится в предопределенном ключе `firstRow`. Чтобы использовать этот результат в последующем действии, примените шаблон `@{activity('MyLookupActivity').output.firstRow.TableName}`.

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **Когда для `firstRowOnly` задано значение `false`**, формат выходного значения выглядит, как показано в следующем коде. В поле `count` указано количество возвращенных записей и подробных значений, отображаемых в списке предопределенного массива `value`. В этом случае за действием поиска обычно следует [действие Foreach](control-flow-for-each-activity.md). Вы можете указать массив `value` в поле действия ForEach `items` с помощью шаблона `@activity('MyLookupActivity').output.value`. Для получения доступа к элементам массива `value` используйте следующий синтаксис: `@{activity('lookupActivity').output.value[zero based index].propertyname}`. Пример: `@{activity('lookupActivity').output.value[0].tablename}`.

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
В этом примере действие копирования копирует данные из таблицы SQL в экземпляре базы данных SQL Azure в хранилище BLOB-объектов Azure. Имя таблицы SQL хранится в файле JSON в хранилище BLOB-объектов. Действие поиска ищет имя таблицы во время выполнения. Такой подход позволяет динамически изменять файл JSON без необходимости повторного развертывания конвейеров или наборов данных. 

В этом примере демонстрируется поиск только для первой строки. Примеры выполнения поиска всех строк и связывания результатов с действием ForEach см. в статье [Копирование нескольких таблиц в пакетном режиме с помощью фабрики данных Azure](tutorial-bulk-copy.md).

### <a name="pipeline"></a>Конвейер
Этот конвейер содержит два действия: *поиск* и *копирование*. 

- Действие поиска настроено для использования LookupDataset, который ссылается на расположение в хранилище BLOB-объектов Azure. Действие поиска считывает имя таблицы SQL из файла JSON в этом расположении. 
- Действие копирования использует выходные данные действия поиска (имя таблицы SQL). Свойство tableName в исходном наборе данных (SourceDataset) настроено для использования выходных данных действия поиска. Действие копирования копирует данные из таблицы SQL в расположение в хранилище BLOB-объектов Azure, которое задается свойством SinkDataset. 


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
Набор данных поиска — это файл *sourcetable.json* в папке поиска службы хранилища Azure, указанный типом AzureStorageLinkedService. 

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
Действие копирования копирует данные из таблицы SQL в файл *filebylookup.csv* в папке с расширением *CSV* в хранилище Azure, указанном с помощью свойства AzureStorageLinkedService. 

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

### <a name="azure-storage-linked-service"></a>Связанная служба хранения Azure
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
Этот экземпляр базы данных SQL Azure содержит данные, копируемые в хранилище BLOB-объектов. 

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

## <a name="next-steps"></a>Дополнительная информация
Ознакомьтесь с другими действиями потока управления, которые поддерживаются фабрикой данных, в следующих статьях: 

- [Действие выполнения конвейера в фабрике данных Azure](control-flow-execute-pipeline-activity.md)
- [Действие ForEach в фабрике данных Azure](control-flow-for-each-activity.md)
- [Действие получения метаданных в фабрике данных Azure](control-flow-get-metadata-activity.md)
- [Веб-действие](control-flow-web-activity.md)
