---
title: "Действие поиска в фабрике данных Azure | Документация Майкрософт"
description: "Узнайте, как использовать действие Уточняющий запрос для поиска значений из внешнего источника. На эти выходные данные можно затем ссылаться в последующих действиях."
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
ms.openlocfilehash: 3c4f401682e5d1789c6e15597ced145a230bbcd6
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2018
---
# <a name="lookup-activity-in-azure-data-factory"></a>Действие поиска в фабрике данных Azure
Действие подстановки можно использовать для чтения или для поиска записи, имя таблицы или значение из внешних источников. На эти выходные данные можно затем ссылаться в последующих действиях. 

Поиск действия полезно в том случае, если необходимо динамически получить список файлов, записи или таблицы из файла конфигурации или источника данных. Выходные данные действия в дальнейшем могут использоваться другими действиями для выполнения определенной обработки только этих элементов.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных Azure, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, используйте [документацию по версии 1 фабрики данных](v1/data-factory-introduction.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Для поиска в настоящее время поддерживаются следующие источники данных:
- JSON-файла в хранилище больших двоичных объектов Azure
- JSON-файл в файловой системе
- база данных SQL Azure (данные JSON, преобразованные из запроса);
- Хранилище данных SQL Azure (данные JSON, преобразованные из запроса)
- SQL Server (данные JSON, преобразованные из запроса)
- Хранилище таблиц Azure (данные JSON, преобразованные из запроса)

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
dataset | Предоставляет ссылки на набор данных для уточняющего запроса. В настоящее время поддерживаются следующие типы наборов данных:<ul><li>`AzureBlobDataset`для [хранилища больших двоичных объектов Azure](connector-azure-blob-storage.md#dataset-properties) как источник</li><li>`FileShareDataset`для [файловой системы](connector-file-system.md#dataset-properties) как источник</li><li>`AzureSqlTableDataset`для [базы данных SQL Azure](connector-azure-sql-database.md#dataset-properties) или [хранилище данных SQL Azure](connector-azure-sql-data-warehouse.md#dataset-properties) как источник</li><li>`SqlServerTable`для [SQL Server](connector-sql-server.md#dataset-properties) как источник</li><li>`AzureTableDataset`для [хранилище таблиц Azure](connector-azure-table-storage.md#dataset-properties) как источник</li> | Пара "ключ — значение" | Yes
источник | Содержит свойства источника конкретного набора данных, таким же, как источник копий действия. Получение сведений из раздела «Копировать свойства действия» в каждой соответствующей статьи соединителя. | Пара "ключ — значение" | Yes
firstRowOnly | Указывает, следует ли возвращать только в первой строке или всех строк. | Логическое | Нет. Значение по умолчанию — `true`.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>Используйте действие результат поиска в последующие действия

Результат поиска возвращается в `output` части результатов при выполнении действия.

* **Когда `firstRowOnly` равно `true` (по умолчанию)**, выходным форматом является, как показано в следующем коде. Результат поиска находится в фиксированный `firstRow` ключа. Чтобы использовать в последующих действий, используйте шаблон `@{activity('MyLookupActivity').output.firstRow.TableName}`.

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **Когда `firstRowOnly` равно `false`** , выходным форматом является, как показано в следующем коде. Объект `count` поле показывает, сколько записей и подробные значения отображаются в списке фиксированной `value` массива. В этом случае действия уточняющего запроса обычно следуют [действия Foreach](control-flow-for-each-activity.md). Можно передать `value` массива действия ForEach `items` с помощью шаблона `@activity('MyLookupActivity').output.value`. Для доступа к элементам в `value` массива, используйте следующий синтаксис: `@{activity('lookupActivity').output.value[zero based index].propertyname}`. Ниже приведен пример: `@{activity('lookupActivity').output.value[0].tablename}`.

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
В этом примере действие копирования копирует данные из таблицы SQL в экземпляре базы данных SQL Azure для хранения больших двоичных объектов Azure. Имя таблицы SQL хранится в файле JSON в хранилище больших двоичных объектов. Действие поиска ищет имя таблицы во время выполнения. Такой подход позволяет JSON изменяются динамически без необходимости повторного развертывания конвейеров или наборов данных. 

Этот пример демонстрирует поиск только в первой строке. Для поиска всех строк, а в цепочку результаты с действиями по каждому элементу, см. в примерах в [скопировать несколько таблиц в пакетном режиме с помощью фабрики данных Azure](tutorial-bulk-copy.md).

### <a name="pipeline"></a>Конвейер
В этом конвейере содержит два действия: *уточняющего запроса* и *копирования*. 

- Действие поиска настроен на использование LookupDataset, который ссылается на расположение в хранилище больших двоичных объектов Azure. Действие поиска считывает имя таблицы SQL из файла JSON в этом расположении. 
- Действие копирования использует выходные данные действия подстановки (имя таблицы SQL). Свойство tableName в исходном наборе данных (SourceDataset) настроен на использование вывода из операции поиска. Действие копирования копирует данные из таблицы SQL места в хранилище больших двоичных объектов Azure, указанном в свойстве SinkDataset. 


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
Набор данных поиска ссылается на *sourcetable.json* файл в папке хранилища Azure уточняющего запроса, заданную типом AzureStorageLinkedService. 

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
Исходный набор данных использует выходные данные действия поиска — имя таблицы SQL. Действие копирования копирует данные из этой таблицы SQL места в хранилище больших двоичных объектов Azure, указанном приемник набор данных. 

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
Действие копирования копирует данные из таблицы SQL, чтобы *filebylookup.csv* файла в *csv* папки в хранилище Azure, который указан в свойстве AzureStorageLinkedService. 

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
Эта учетная запись хранения содержит JSON-файла с именами таблиц SQL. 

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
Этот экземпляр базы данных SQL Azure содержит данные, копируемые в хранилище больших двоичных объектов. 

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
См. другие действия потока управления, которые поддерживаются фабрики данных. 

- [Выполнение работы конвейера](control-flow-execute-pipeline-activity.md)
- [Для каждого действия](control-flow-for-each-activity.md)
- [Получение метаданных действия](control-flow-get-metadata-activity.md)
- [Веб-действие](control-flow-web-activity.md)
