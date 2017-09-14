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
ms.date: 09/06/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: 763bc597bdfc40395511cdd9d797e5c7aaad0fdf
ms.openlocfilehash: 2e7b6a7c33e1f86133383f116df2fc2256133012
ms.contentlocale: ru-ru
ms.lasthandoff: 09/06/2017

---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Перемещение данных из Amazon Redshift с помощью фабрики данных Azure
В этой статье описывается, как с помощью действия копирования в фабрике данных Azure перемещать данные из Amazon Redshift. Эта статья является продолжением статьи о [действиях перемещения данных](data-factory-data-movement-activities.md), в которой приведены общие сведения о перемещении данных с помощью действия копирования. 

Данные из Amazon Redshift можно скопировать в любое хранилище данных, поддерживаемое в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Сейчас фабрика данных поддерживает перемещение данных из Amazon Redshift в другие хранилища данных, но не наоборот.

> [!TIP]
> Чтобы обеспечить наилучшую производительность при копировании больших объемов данных из Redshift, рекомендуется использовать встроенный механизм Redshift UNLOAD через Amazon S3. Дополнительные сведения см. в разделе [Копирование данных из Amazon Redshift с помощью UNLOAD](#use-unload-to-copy-data-from-amazon-redshift).

## <a name="prerequisites"></a>Предварительные требования
* Для перемещения данных в локальное хранилище установите [шлюз управления данными](data-factory-data-management-gateway.md) на локальный компьютер. Затем предоставьте шлюзу управления данными доступ к кластеру Amazon Redshift (используйте IP-адрес компьютера). Инструкции см. в статье об [авторизации доступа к кластеру](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html).
* Если вы перемещаете данные в хранилище данных Azure, вам нужно знать IP-адреса вычислительных ресурсов и диапазоны SQL, используемые центрами обработки данных Azure. Диапазоны IP-адресов центра обработки данных Azure приведены на [этой странице](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="getting-started"></a>Приступая к работе
Вы можете создать конвейер с действием копирования, который перемещает данные из источника Amazon Redshift, с помощью разных инструментов и интерфейсов API.

Проще всего создать конвейер с помощью **мастера копирования**. В статье [Руководство. Создание конвейера с действием копирования с помощью мастера копирования фабрики данных](data-factory-copy-data-wizard-tutorial.md) приведены краткие пошаговые указания по созданию конвейера с помощью мастера копирования данных.

Также для создания конвейера можно использовать следующие инструменты: **портал Azure**, **Visual Studio**, **Azure PowerShell**, **шаблон Azure Resource Manager**, **API .NET** и **REST API**. Пошаговые инструкции по созданию конвейера с действием копирования см. в [руководстве по действию копирования](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

Независимо от используемого средства или API-интерфейса, для создания конвейера, который перемещает данные из источника данных в приемник, выполняются следующие шаги: 

1. Создайте **связанные службы**, чтобы связать входные и выходные данные с фабрикой данных.
2. Создайте **наборы данных**, которые представляют входные и выходные данные для операции копирования. 
3. Создайте **конвейер** с действием копирования, который принимает входной набор данных и возвращает выходной набор данных. 

Если вы используете мастер, то он автоматически создает определения JSON для сущностей фабрики данных (связанных служб, наборов данных и конвейера). При использовании инструментов и интерфейсов API (за исключением API .NET) вы самостоятельно определяете эти сущности фабрики данных в формате JSON.  Пример определения JSON для сущностей фабрики данных, которые используются для копирования данных из хранилища данных Amazon Redshift, вы найдете в разделе [Пример JSON. Копирование данных из Amazon Redshift в большой двоичный объект Azure](#json-example-copy-data-from-amazon-redshift-to-azure-blob) этой статьи. 

Следующие разделы содержат сведения о свойствах JSON, которые используются для определения сущностей фабрики данных, относящихся к Amazon Redshift. 

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

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, используемых для определения наборов данных, см. в статье [Наборы данных](data-factory-create-datasets.md). Разделы structure, availability и policy одинаковы для всех типов наборов данных (SQL Azure, большие двоичные объекты Azure, таблицы Azure и т. д.).

Разделы **typeProperties** для каждого типа набора данных отличаются. В этом разделе указываются такие сведения, как расположение данных в хранилище данных. Раздел typeProperties набора данных типа **RelationalTable** (который включает в себя набор данных Amazon Redshift) содержит следующие свойства.

| Свойство | Описание | Обязательно |
| --- | --- | --- |
| tableName |Имя таблицы в базе данных Amazon Redshift, на которое ссылается связанная служба. |Нет (если для свойства **RelationalSource** задано значение **query**). |

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Создание конвейеров](data-factory-create-pipelines.md). Свойства (такие как имя, описание, входные и выходные таблицы, политики и т. д.) доступны для всех типов действий.

В свою очередь свойства, доступные в разделе **typeProperties** действия, зависят от конкретного типа действия. Для действия копирования они различаются в зависимости от типов источников и приемников.

Когда источник действия копирования относится к типу **AmazonRedshiftSource**, в разделе typeProperties доступны указанные ниже свойства.

| Свойство | Описание | Обязательно |
| --- | --- | --- |
| query | Используйте пользовательский запрос для чтения данных. |Нет (если для свойства **tableName** задано значение **dataset**). |
| redshiftUnloadSettings | Группа свойств при использовании Amazon Redshift UNLOAD. | Нет |
| s3LinkedServiceName | Относится к службе Amazon S3, которую необходимо использовать в качестве промежуточного хранилища, указав имя связанной службы ADF типа AwsAccessKey. | Требуется в redshiftUnloadSettings |
| bucketName | Укажите контейнер S3 для хранения промежуточных данных. Если он не указан, действие копирования автоматически создаст контейнер. | Требуется в redshiftUnloadSettings |

Кроме того, можно использовать тип **RelationalSource** (включающий Amazon Redshift) со следующим свойством в разделе typeProperties. Обратите внимание, что этот тип источника не поддерживает Redshift UNLOAD.

| Свойство | Описание | Обязательно |
| --- | --- | --- |
| query |Используйте пользовательский запрос для чтения данных. | Нет (если для свойства **tableName** задано значение **dataset**). |

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Копирование данных из Amazon Redshift с помощью UNLOAD

[UNLOAD](http://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) — это механизм, предоставляемый Amazon Redshift, позволяющий выгрузить результаты запроса в один или несколько файлов в Amazon Simple Storage Service (Amazon S3). Компания Amazon рекомендует использовать этот способ для копирования большого набора данных из Redshift.

**Пример. Копирование данных из Amazon Redshift в хранилище данных SQL Azure с помощью UNLOAD, промежуточного копирования и PolyBase**

В этом варианте использования действие копирования сначала выгружает данные из Amazon Redshift в Amazon S3, как задано в redshiftUnloadSettings, затем копирует данные из Amazon S3 в большой двоичный объект Azure, заданный в stagingSettings, и наконец, использует PolyBase для загрузки данных в хранилище данных SQL. Формат промежуточного хранения обрабатывается действием копирования должным образом.

![Рабочий процесс копирования из Redshift в хранилище данных SQL](media\data-factory-amazon-redshift-connector\redshift-to-sql-dw-copy-workflow.png)

```json
{
    "name": "CopyFromRedshiftToSQLDW",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "AmazonRedshiftSource",
            "query": "select * from MyTable",
            "redshiftUnloadSettings": {
                "s3LinkedServiceName":"MyAmazonS3StorageLinkedService",
                "bucketName": "bucketForUnload"
            }
        },
        "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyAzureStorageLinkedService",
            "path": "adfstagingcopydata"
        },
        "cloudDataMovementUnits": 32
        .....
    }
}
```

## <a name="json-example-copy-data-from-amazon-redshift-to-azure-blob"></a>Пример JSON. Копирование данных из Amazon Redshift в большой двоичный объект Azure
В этом примере показано, как скопировать данные из базы данных Amazon Redshift в хранилище BLOB-объектов Azure. Тем не менее данные можно копировать **непосредственно** в любой из указанных [здесь](data-factory-data-movement-activities.md#supported-data-stores-and-formats) приемников. Это делается с помощью действия копирования в фабрике данных Azure.  

Образец состоит из следующих сущностей фабрики данных.

* Связанная служба типа [AmazonRedshift](#linked-service-properties).
* Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Входной [набор данных](data-factory-create-datasets.md) типа [RelationalTable](#dataset-properties).
* Выходной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* [Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [RelationalSource](#copy-activity-properties) и [BlobSink](data-factory-azure-blob-connector.md##copy-activity-properties).

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

**Связанная служба хранилища Azure**

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

Значение `"external": true` означает, что служба фабрики данных считает этот набор данных внешним по отношению к себе и не созданным в результате какого-либо действия в фабрике данных. Присвойте значение true этому параметру входного набора данных, который не является результатом какого-либо действия в конвейере.

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

**Действие копирования в конвейере с Azure Redshift (RelationalSource) в качестве источника и большим двоичным объектом в качестве приемника**

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
                        "type": "AmazonRedshiftSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)",
                        "redshiftUnloadSettings": {
                            "s3LinkedServiceName":"myS3Storage",
                            "bucketName": "bucketForUnload"
                        }
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    },
                    "cloudDataMovementUnits": 32
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

## <a name="map-source-to-sink-columns"></a>Сопоставление столбцов источника и приемника
Дополнительные сведения о сопоставлении столбцов в наборе данных, используемом в качестве источника, со столбцами в приемнике см. в [этой статье](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Повторяющиеся операции чтения из реляционных источников
При копировании данных из реляционных хранилищ важно помнить о повторяемости, чтобы избежать непредвиденных результатов. В фабрике данных Azure можно вручную повторно выполнить срез. Вы можете также настроить для набора данных политику повтора, чтобы при сбое срез выполнялся повторно. При повторном выполнении среза в любом случае необходимо убедиться в том, что считываются те же данные, независимо от того, сколько раз выполняется срез. Ознакомьтесь с разделом [Повторяющиеся операции чтения из реляционных источников](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Производительность и настройка
Ознакомьтесь со статьей [Руководство по настройке производительности действия копирования](data-factory-copy-activity-performance.md), в которой описываются ключевые факторы, влияющие на производительность перемещения данных (действие копирования) в фабрике данных Azure, и различные способы оптимизации этого процесса.

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь со следующими статьями:

* [руководстве по действию копирования](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

