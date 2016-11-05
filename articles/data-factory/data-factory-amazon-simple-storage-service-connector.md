---
title: Перемещение данных из Amazon Simple Storage Service с помощью фабрики данных | Microsoft Docs
description: Узнайте, как переместить данные из Amazon Simple Storage Service (S3) с помощью фабрики данных Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: jhubbard
editor: monicar

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2016
ms.author: jingwang

---
# <a name="move-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Перемещение данных из Amazon Simple Storage Service с помощью фабрики данных Azure
В этой статье описывается, как с помощью действия копирования в фабрике данных Azure переместить данные из Amazon Simple Storage Service (S3) в другое хранилище данных. Эта статья основана на материалах статьи о [действиях перемещения данных](data-factory-data-movement-activities.md) , в которой в общем описывается перемещение данных и перечисляются исходные и целевые хранилища данных (источники и приемники), поддерживающие действие копирования.  

Сейчас фабрика данных поддерживает только перемещение данных из Amazon S3 в другие хранилища данных, но не наоборот.

## <a name="copy-data-wizard"></a>Мастер копирования данных
Самый простой способ создать конвейер, копирующий данные из базы данных Amazon S3, — использовать мастер копирования данных. В статье [Руководство. Создание конвейера с действием копирования с помощью мастера копирования фабрики данных](data-factory-copy-data-wizard-tutorial.md) приведены краткие пошаговые указания по созданию конвейера с помощью мастера копирования данных. 

Ниже приведены примеры с определениями JSON, которые можно использовать для создания конвейера с помощью [портала Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) или [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Здесь показано, как скопировать данные из Amazon S3 в хранилище BLOB-объектов Azure. Тем не менее данные можно скопировать на любой из перечисленных [здесь](data-factory-data-movement-activities.md#supported-data-stores)приемников.

## <a name="sample:-copy-data-from-amazon-s3-to-azure-blob"></a>Пример. Копирование данных из Amazon S3 в большой двоичный объект Azure
В этом примере показано, как скопировать данные из Amazon S3 в хранилище BLOB-объектов Azure. Тем не менее данные можно копировать **непосредственно** в любой из указанных [здесь](data-factory-data-movement-activities.md#supported-data-stores) приемников. Это делается с помощью действия копирования в фабрике данных Azure.  

Образец состоит из следующих сущностей фабрики данных.

* Связанная служба типа [AwsAccessKey](#linked-service-properties).
* Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
* Входной [набор данных](data-factory-create-datasets.md) типа [AmazonS3](#dataset-type-properties).
* Выходной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
* [Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [FileSystemSource](#copy-activity-type-properties) и [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

В этом примере данные из Amazon S3 каждый час копируются в большой двоичный объект Azure. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами. 

**Связанная служба Amazon S3**

    {
        "name": "AmazonS3LinkedService",
        "properties": {
            "type": "AwsAccessKey",
            "typeProperties": {
                "accessKeyId": "<access key id>",
                "secretAccessKey": "<secret access key>"
            }
        }
    }

**Связанная служба хранения Azure**

    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Входной набор данных Amazon S3**

Если параметру **external** присвоить значение true, то фабрика данных воспримет этот набор данных как внешний и созданный не в результате какого-либо действия в этой фабрике данных. Присвойте значение true этому параметру входного набора данных, который не является результатом какого-либо действия в конвейере.

    {
        "name": "AmazonS3InputDataset",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "AmazonS3LinkedService",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }



**Выходной набор данных BLOB-объекта Azure**

Данные записываются в новый BLOB-объект каждый час (frequency: hour, interval: 1). Путь к папке BLOB-объекта вычисляется динамически на основе времени начала обрабатываемого среза. В пути к папке используется год, месяц, день и час времени начала.

    {
        "name": "AzureBlobOutputDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/fromamazons3/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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



**Конвейер с действием копирования**

Конвейер содержит действие копирования, которое использует входной и выходной наборы данных и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **FileSystemSource**, а для типа **sink** — значение **BlobSink**. 

    {
        "name": "CopyAmazonS3ToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "FileSystemSource",
                            "recursive": true
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AmazonS3InputDataset"
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
                    "name": "AmazonS3ToBlob"
                }
            ],
            "start": "2014-08-08T18:00:00Z",
            "end": "2014-08-08T19:00:00Z"
        }
    }



## <a name="linked-service-properties"></a>Свойства связанной службы
В таблице ниже приведено описание элементов JSON, которые относятся к связанной службе Amazon S3 (**AwsAccessKey**).

| Свойство | Описание | Допустимые значения | Обязательно |
| --- | --- | --- | --- |
| accessKeyID |Идентификатор секретного ключа доступа. |string |Да |
| secretAccessKey |Сам секретный ключ доступа. |Зашифрованная строка секрета |Да |

## <a name="dataset-type-properties"></a>Свойства типа "Набор данных"
Полный список разделов и свойств, используемых для определения наборов данных, см. в статье [Наборы данных](data-factory-create-datasets.md). Разделы structure, availability и policy одинаковы для всех типов наборов данных (SQL Azure, большие двоичные объекты Azure, таблицы Azure и т. д.).

Раздел **typeProperties** во всех типах наборов данных разный. В нем содержатся сведения о расположении данных в хранилище данных. Раздел typeProperties набора данных типа **AmazonS3** (который включает в себя набор данных Amazon S3) содержит следующие свойства.

| Свойство | Описание | Допустимые значения | Обязательно |
| --- | --- | --- | --- |
| bucketName |Имя контейнера S3. |string |Да |
| key |Ключ объекта S3. |string |Нет |
| prefix |Префикс для ключа объекта S3. Выбираются объекты, ключи которых начинаются с этого префикса. Применяется, только если ключ пустой. |string |Нет |
| версия |Версия объекта S3, если включено управление версиями S3. |string |Нет |
| свойства |Поддерживаются следующие типы формата: **TextFormat**, **AvroFormat**, **JsonFormat**, **OrcFormat**, **ParquetFormat**. Свойству **type** в разделе format необходимо присвоить одно из этих значений. Дополнительные сведения см. в разделах [Определение TextFormat](#specifying-textformat), [Определение AvroFormat](#specifying-avroformat), [Указание JsonFormat](#specifying-jsonformat), [Указание OrcFormat](#specifying-orcformat) и [Указание ParquetFormatt](#specifying-parquetformat). Если требуется скопировать файлы между файловыми хранилищами "как есть" (двоичное копирование), можно пропустить раздел форматирования в определениях входного и выходного наборов данных. |Нет | |
| compression |Укажите тип и уровень сжатия данных. Поддерживаемые типы: **GZip**, **Deflate** и **BZip2**. Поддерживаемые уровни: **Optimal** и **Fastest**. Сейчас для данных в формате **AvroFormat** или **OrcFormat** параметры сжатия не поддерживаются. Дополнительные сведения см. в разделе [Поддержка сжатия](#compression-support). |Нет | |

> [!NOTE]
> Свойства bucketName и key указывают расположение объекта S3, где контейнер — это корневой контейнер для объектов S3, а ключ — это полный путь к объекту S3.
> 
> 

### <a name="sample-dataset-with-prefix"></a>Пример набора данных с префиксом
    {
        "name": "dataset-s3",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "link- testS3",
            "typeProperties": {
                "prefix": "testFolder/test",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }

### <a name="sample-data-set-(with-version)"></a>Пример набора данных (с версией)
    {
        "name": "dataset-s3",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "link- testS3",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "version": "WBeMIxQkJczm0CJajYkHf0_k6LhBmkcL",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }


### <a name="dynamic-paths-for-s3"></a>Динамические пути для S3
В этом примере мы используем фиксированные значения для свойств key и bucketName в наборе данных Amazon S3. 

    "key": "testFolder/test.orc",
    "bucketName": "testbucket",

Можно настроить фабрику данных для динамического вычисления значений свойств key и bucketName во время выполнения с помощью системных переменных, например SliceStart.

    "key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
    "bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"

То же самое можно сделать и для свойства prefix набора данных Amazon S3. В статье [Фабрика данных Azure — функции и системные переменные](data-factory-functions-variables.md) приведен список поддерживаемых функций и переменных. 

[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

[!INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="copy-activity-type-properties"></a>Свойства типа "Действие копирования"
Полный список разделов и свойств, используемых для определения действий, см. в статье [Создание конвейеров](data-factory-create-pipelines.md). Свойства (такие как имя, описание, входные и выходные таблицы, политики и т. д.) доступны для всех типов действий. 

С другой стороны, свойства, доступные в разделе **typeProperties** действия, зависят от конкретного типа действия. Для действия копирования они различаются в зависимости от типов источников и приемников.

Когда источник действия копирования имеет тип **FileSystemSource** (который включает в себя Amazon S3), в разделе typeProperties доступны приведенные ниже свойства.

| Свойство | Описание | Допустимые значения | Обязательно |
| --- | --- | --- | --- |
| recursive |Указывает, следует ли отображать рекурсивный список объектов S3 в каталоге. |True или false |Нет |

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Производительность и настройка
Ознакомьтесь со статьей [Руководство по настройке производительности действия копирования](data-factory-copy-activity-performance.md), в которой описываются ключевые факторы, влияющие на производительность перемещения данных (действие копирования) в фабрике данных Azure, и различные способы оптимизации этого процесса.

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь со следующими статьями: 

* [руководстве по действию копирования](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) . 

<!--HONumber=Oct16_HO2-->


