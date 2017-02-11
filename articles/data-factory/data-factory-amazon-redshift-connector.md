---
title: "Перемещение данных из Amazon Redshift с помощью фабрики данных | Документация Майкрософт"
description: "Узнайте о том, как перемещать данные из Amazon Redshift с помощью фабрики данных Azure."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 01d15078-58dc-455c-9d9d-98fbdf4ea51e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2016
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: c2350ae447ccebf1a6b85a563e7fa1d7c12b16d7
ms.openlocfilehash: 4ca6e9a70eeba688912dd1f2c840fe0289a365c9


---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Перемещение данных из Amazon Redshift с помощью фабрики данных Azure
В этой статье мы рассказываем, как с помощью действия копирования перемещать данные из Amazon Redshift в другое хранилище данных. Здесь мы развиваем темы, освещенные в статье о [действиях перемещения данных](data-factory-data-movement-activities.md) , которая кратко описывает, как перемещать данные с помощью действия копирования, и содержит список исходных и целевых хранилищ данных (источников и приемников).  

Сейчас фабрика данных поддерживает перемещение данных из Amazon Redshift в другие хранилища данных, но не наоборот.

## <a name="prerequisites"></a>Предварительные требования
* При перемещении данных в локальное хранилище предоставьте шлюзу управления данными доступ к кластеру Amazon Redshift (используйте IP-адрес компьютера). Инструкции см. в статье об [авторизации доступа к кластеру](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html).
* Если вы перемещаете данные в хранилище данных Azure, вам нужно знать диапазоны IP-адресов вычислительных ресурсов (в том числе диапазоны SQL), используемые центрами обработки данных Microsoft Azure. Эти диапазоны приведены в документе [Azure Data Center IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="copy-data-wizard"></a>Мастер копирования данных
Самый простой способ создать конвейер, копирующий данные из базы данных Amazon Redshift, — использовать мастер копирования данных. В статье [Руководство. Создание конвейера с действием копирования с помощью мастера копирования фабрики данных](data-factory-copy-data-wizard-tutorial.md) приведены краткие пошаговые указания по созданию конвейера с помощью мастера копирования данных.

Ниже приведены примеры с определениями JSON, которые можно использовать для создания конвейера с помощью [портала Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) или [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Здесь показано, как скопировать данные из Amazon Redshift в хранилище BLOB-объектов Azure. Тем не менее данные можно скопировать на любой приемник из перечисленных [здесь](data-factory-data-movement-activities.md#supported-data-stores-and-formats).

## <a name="sample-copy-data-from-amazon-redshift-to-azure-blob"></a>Пример копирования данных из Amazon Redshift в большой двоичный объект Azure
В этом примере показано, как скопировать данные из базы данных Amazon Redshift в хранилище BLOB-объектов Azure. Тем не менее данные можно копировать **непосредственно** в любой из указанных [здесь](data-factory-data-movement-activities.md#supported-data-stores-and-formats) приемников. Это делается с помощью действия копирования в фабрике данных Azure.  

Образец состоит из следующих сущностей фабрики данных.

* Связанная служба типа [AmazonRedshift](#linked-service-properties).
* Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service).
* Входной [набор данных](data-factory-create-datasets.md) типа [RelationalTable](#dataset-type-properties).
* Выходной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
* [Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [RelationalSource](#copy-activity-type-properties) и [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

В примере данные из результата запроса к Amazon Redshift каждый час копируются в большой двоичный объект. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

**Связанная служба Amazon Redshift**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "< The IP address or host name of the Amazon Redshift server >",
            "port": <The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>,
            "database": "<The database name of the Amazon Redshift database>",
            "username": "<username>",
            "password": "<password>"
        }
    }
}
```

**Связанная служба хранения Azure**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Входной набор данных Amazon Redshift**

Значение true параметра **external** сообщает службе фабрики данных, что этот набор данных является внешним, и что он не является результатом какого-либо действия в этой службе. Присвойте значение true этому параметру входного набора данных, который не является результатом какого-либо действия в конвейере.

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Выходной набор данных BLOB-объекта Azure**

Данные записываются в новый BLOB-объект каждый час (frequency: hour, interval: 1). Путь к папке BLOB-объекта вычисляется динамически на основе времени начала обрабатываемого среза. В пути к папке используется год, месяц, день и час времени начала.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Конвейер с действием копирования**

Конвейер содержит действие копирования, которое использует входной и выходной наборы данных и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **RelationalSource**, а для типа **sink** — значение **BlobSink**. SQL-запрос, указанный для свойства **query** , выбирает для копирования данные за последний час.

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AmazonRedshiftInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AmazonRedshiftToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```


## <a name="linked-service-properties"></a>Свойства связанной службы
В таблице ниже приведено описание элементов JSON, которые относятся к связанной службе Amazon Redshift.

| Свойство | Описание | Обязательно |
| --- | --- | --- |
| type |Для свойства type необходимо задать значение **AmazonRedshift**. |Да |
| server |IP-адрес или имя узла сервера Amazon Redshift. |Да |
| порт |Номер TCP-порта, используемого сервером Amazon Redshift для прослушивания клиентских подключений. |Нет, значение по умолчанию — 5439. |
| database |Имя базы данных Amazon Redshift. |Да |
| Имя пользователя |Имя пользователя, имеющего доступ к базе данных. |Да |
| пароль |Пароль для учетной записи пользователя. |Да |

## <a name="dataset-type-properties"></a>Свойства типа "Набор данных"
Полный список разделов и свойств, используемых для определения наборов данных, см. в статье [Наборы данных](data-factory-create-datasets.md). Разделы structure, availability и policy одинаковы для всех типов наборов данных (SQL Azure, большие двоичные объекты Azure, таблицы Azure и т. д.).

Раздел **typeProperties** во всех типах наборов данных разный. В нем содержатся сведения о расположении данных в хранилище данных. Раздел typeProperties набора данных типа **RelationalTable** (который включает в себя набор данных Amazon Redshift) содержит следующие свойства.

| Свойство | Описание | Обязательно |
| --- | --- | --- |
| tableName |Имя таблицы в базе данных Amazon Redshift, на которое ссылается связанная служба. |Нет (если для свойства **RelationalSource** задано значение **query**). |

## <a name="copy-activity-type-properties"></a>Свойства типа "Действие копирования"
Полный список разделов и свойств, используемых для определения действий, см. в статье [Создание конвейеров](data-factory-create-pipelines.md). Свойства (такие как имя, описание, входные и выходные таблицы, политики и т. д.) доступны для всех типов действий.

С другой стороны, свойства, доступные в разделе **typeProperties** действия, зависят от конкретного типа действия. Для действия копирования они различаются в зависимости от типов источников и приемников.

Когда источник действия копирования относится к типу **RelationalSource** (который включает Amazon Redshift), в разделе typeProperties доступны следующие свойства.

| Свойство | Описание | Допустимые значения | Обязательно |
| --- | --- | --- | --- |
| query |Используйте пользовательский запрос для чтения данных. |Строка запроса SQL. Например, select * from MyTable. |Нет (если для свойства **tableName** задано значение **dataset**). |

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-amazon-redshift"></a>Сопоставление типов для Amazon Redshift
Как упоминалось в статье о [действиях перемещения данных](data-factory-data-movement-activities.md), во время копирования типы источников автоматически преобразовываются в типы приемников. Такое преобразование выполняется в два этапа:

1. Преобразование собственных типов источников в тип .NET.
2. Преобразование типа .NET в собственный тип приемника.

Когда данные перемещаются в Amazon Redshift, для преобразования типа Amazon Redshift в тип .NET используются следующие сопоставления.

| Тип Amazon Redshift | Тип данных на основе .NET |
| --- | --- |
| SMALLINT |Int16 |
| INTEGER |Int32 |
| BIGINT |Int64 |
| DECIMAL |DECIMAL |
| REAL |Single |
| DOUBLE PRECISION |Double |
| BOOLEAN |Строка |
| CHAR |Строка |
| VARCHAR |Строка |
| DATE |DateTime |
| TIMESTAMP |DateTime |
| TEXT |Строка |

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Производительность и настройка
Ознакомьтесь со статьей [Руководство по настройке производительности действия копирования](data-factory-copy-activity-performance.md), в которой описываются ключевые факторы, влияющие на производительность перемещения данных (действие копирования) в фабрике данных Azure, и различные способы оптимизации этого процесса.

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь со следующими статьями:

* [руководстве по действию копирования](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .



<!--HONumber=Nov16_HO3-->


