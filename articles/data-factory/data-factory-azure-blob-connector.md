---
title: "Копирование данных в хранилище BLOB-объектов Azure и из него | Документация Майкрософт"
description: "Как копировать данные BLOB-объектов в фабрике данных Azure. Используйте наш пример: как копировать данные в базу данных SQL Azure и хранилище BLOB-объектов Azure и обратно."
keywords: "данные BLOB-объектов, копирование BLOB-объекта Azure"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: bec8160f-5e07-47e4-8ee1-ebb14cfb805d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2016
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 701d82971b7da92fb0946cbfc7f708ad32501ef3
ms.openlocfilehash: 69b530c085cc99959e66e75f2c8ebb391c9d26f4


---
# <a name="move-data-to-and-from-azure-blob-using-azure-data-factory"></a>Перемещение данных в большой двоичный объект Azure и из него с помощью фабрики данных Azure
В этой статье рассказывается, как с помощью действия копирования в фабрике данных Azure можно перемещать данные в BLOB-объект Azure из другого хранилища данных и обратно. В этой статье мы продолжим тему о [действиях перемещения данных](data-factory-data-movement-activities.md) , в которой приведены общие сведения о перемещении данных с помощью действия копирования и поддерживаемых сочетаниях хранилищ данных.

## <a name="supported-sources-and-sinks"></a>Поддерживаемые источники и приемники
В таблице [Поддерживаемые хранилища данных](data-factory-data-movement-activities.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования. Данные можно переместить из любого поддерживаемого в качестве источника хранилища данных в хранилище BLOB-объектов Azure или из хранилища BLOB-объектов Azure в любое поддерживаемое в качестве приемника хранилище данных.

Действие копирования поддерживает копирование данных как в учетные записи хранения Azure общего назначения и в хранилище горячих и холодных BLOB-объектов, так и из них. Это действие поддерживает чтение данных из блочных, добавочных или страничных BLOB-объектов, однако запись поддерживается только в блочные BLOB-объекты.

## <a name="create-pipeline"></a>Создание конвейера
Можно создать конвейер с действием копирования, которое перемещает данные из хранилища BLOB-объектов Azure или в него с помощью различных инструментов и интерфейсов API.  

* Мастер копирования
* Портал Azure
* Visual Studio
* Azure PowerShell
* .NET API
* Интерфейс REST API

Пошаговые инструкции по различным способам создания конвейера с действием копирования см. в [руководстве по действию копирования](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="copy-data-wizard"></a>Мастер копирования данных
Самый простой способ создать конвейер, копирующий данные в хранилище BLOB-объектов Azure или из него, — использовать мастер копирования данных. В статье [Руководство. Создание конвейера с действием копирования с помощью мастера копирования фабрики данных](data-factory-copy-data-wizard-tutorial.md) приведены краткие пошаговые указания по созданию конвейера с помощью мастера копирования данных.

Ниже приведены примеры с определениями JSON, которые можно использовать для создания конвейера с помощью [портала Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) или [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). В них показано, как копировать данные в базу данных SQL Azure и хранилище BLOB-объектов Azure и обратно. Однако данные можно скопировать данные **непосредственно** из любых источников на любой из поддерживаемых приемников. Дополнительные сведения см. в разделе "Поддерживаемые хранилища данных и форматы" статьи [Перемещение данных с помощью действия копирования](data-factory-data-movement-activities.md).

## <a name="sample-copy-data-from-azure-blob-to-azure-sql"></a>Пример копирования данных из BLOB-объекта Azure в SQL Azure
В примере ниже используется следующее:

1. Связанная служба типа [AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties).
2. Связанная служба типа [AzureStorage](#azure-storage-linked-service-properties).
3. Входной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](#azure-blob-dataset-type-properties).
4. Выходной [набор данных](data-factory-create-datasets.md) типа [AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties).
5. [Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [BlobSource](#azure-blob-copy-activity-type-properties) и [SqlSink](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties).

В этом примере данные временного ряда каждый час копируются из большого двоичного объекта Azure в таблицу SQL Azure. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

**Связанная служба SQL Azure**

    {
      "name": "AzureSqlLinkedService",
      "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
          "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
      }
    }

**Связанная служба хранилища Azure**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

Фабрика данных Azure поддерживает два типа связанных служб хранилища Azure: **AzureStorage** и **AzureStorageSas**. Для первой необходимо указать строку подключения, содержащую ключ учетной записи, а для второй — URI подписанного URL-адреса. Дополнительные сведения см. в разделе [Связанные службы](#linked-services).  

**Входной набор данных BLOB-объекта Azure**

Данные берутся из нового BLOB-объекта каждый час (frequency: hour, interval: 1). Путь к папке с BLOB-объектом и имя файла вычисляются динамически на основе времени начала обрабатываемого среза. В пути к папке используется год, месяц и день начала, а в имени файла — час начала. Когда для параметра external задано значение true, фабрика данных считает эту таблицу внешней по отношению к себе и не созданной в результате какого-либо действия в фабрике данных.

    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
          "fileName": "{Hour}.csv",
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
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": "\n"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "externalData": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
          }
        }
      }
    }

**Выходной набор данных SQL Azure**

В этом примере данные копируются в таблицу с именем MyOutputTable в базе данных SQL Azure. Создайте таблицу в базе данных SQL Azure с тем количеством столбцов, которое должно быть в CSV-файле большого двоичного объекта. Новые строки добавляются в таблицу каждый час.

    {
      "name": "AzureSqlOutput",
      "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
          "tableName": "MyOutputTable"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

**Конвейер с действием копирования**

Конвейер содержит действие копирования, которое использует входной и выходной наборы данных и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **BlobSource**, а для типа **sink** — значение **SqlSink**.

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
          {
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureSqlOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "SqlSink"
              }
            },
           "scheduler": {
              "frequency": "Hour",
              "interval": 1
            },
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "OldestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
          ]
       }
    }

## <a name="sample-copy-data-from-azure-sql-to-azure-blob"></a>Пример копирования данных из SQL Azure в BLOB-объект Azure
В примере ниже используется следующее:

1. Связанная служба типа [AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties).
2. Связанная служба типа [AzureStorage](#azure-storage-linked-service-properties).
3. Входной [набор данных](data-factory-create-datasets.md) типа [AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties).
4. Выходной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](#azure-blob-dataset-type-properties).
5. [Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [SqlSource](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties) и [BlobSink](#azure-blob-copy-activity-type-properties).

В этом примере данные временного ряда каждый час копируются из таблицы SQL Azure в большой двоичный объект Azure. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

**Связанная служба SQL Azure**

    {
      "name": "AzureSqlLinkedService",
      "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
          "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
      }
    }

**Связанная служба хранилища Azure**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

Фабрика данных Azure поддерживает два типа связанных служб хранилища Azure: **AzureStorage** и **AzureStorageSas**. Для первой необходимо указать строку подключения, содержащую ключ учетной записи, а для второй — URI подписанного URL-адреса. Дополнительные сведения см. в разделе [Связанные службы](#linked-services).  

**Входной набор данных SQL Azure**

В примере предполагается, что таблица MyTable уже создана в SQL Azure и содержит столбец с именем timestampcolumn для данных временных рядов.

Если для параметра external задать значение true, то фабрика данных воспримет эту таблицу как внешнюю, которая создана не в результате какого-либо действия в этой службе.

    {
      "name": "AzureSqlInput",
      "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
          "tableName": "MyTable"
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "externalData": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
          }
        }
      }
    }


**Выходной набор данных BLOB-объекта Azure**

Данные записываются в новый BLOB-объект каждый час (frequency: hour, interval: 1). Путь к папке BLOB-объекта вычисляется динамически на основе времени начала обрабатываемого среза. В пути к папке используется год, месяц, день и час времени начала.

    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
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
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": "\t",
            "rowDelimiter": "\n"
          }
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

**Конвейер с действием копирования**

Конвейер содержит действие копирования, которое использует входной и выходной наборы данных и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **SqlSource**, а для типа **sink** — значение **BlobSink**. SQL-запрос, указанный для свойства **SqlReaderQuery** , выбирает для копирования данные за последний час.

    {  
        "name":"SamplePipeline",
        "properties":{  
            "start":"2014-06-01T18:00:00",
            "end":"2014-06-01T19:00:00",
            "description":"pipeline for copy activity",
            "activities":[  
                  {
                    "name": "AzureSQLtoBlob",
                    "description": "copy activity",
                    "type": "Copy",
                    "inputs": [
                      {
                        "name": "AzureSQLInput"
                      }
                    ],
                    "outputs": [
                      {
                        "name": "AzureBlobOutput"
                      }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                          },
                          "sink": {
                            "type": "BlobSink"
                          }
                    },
                       "scheduler": {
                          "frequency": "Hour",
                          "interval": 1
                    },
                    "policy": {
                          "concurrency": 1,
                          "executionPriorityOrder": "OldestFirst",
                          "retry": 0,
                          "timeout": "01:00:00"
                    }
                  }
             ]
        }
    }

## <a name="linked-services"></a>Связанные службы
В примерах использовалась связанная служба типа **AzureStorage** , чтобы связать учетную запись хранения Azure с фабрикой данных. В таблице ниже приведено описание элементов JSON, которые относятся к связанной службе хранилища Azure.

Для связи хранилища BLOB-объектов Azure с фабрикой данных Azure можно использовать два типа связанных служб: **AzureStorage** и **AzureStorageSas**. Связанная служба хранилища Azure предоставляет фабрике данных глобальный доступ к хранилищу Azure, а связанная служба SAS хранилища Azure — ограниченный или привязанный ко времени доступ к хранилищу Azure. Других различий между этими связанными службами нет. Выберите связанную службу, соответствующую вашим задачам. Более подробно эти службы описаны в следующих разделах.

[!INCLUDE [data-factory-azure-storage-linked-services](../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="azure-blob-dataset-type-properties"></a>Свойства типа "Набор данных большого двоичного объекта Azure"
В примерах использовался набор данных типа **AzureBlob** , чтобы представить контейнер больших двоичных объектов и папку в хранилище BLOB-объектов Azure.

Полный список разделов и свойств JSON, используемых для определения наборов данных, см. в статье [Создание наборов данных](data-factory-create-datasets.md). Разделы структуры, доступности и политики JSON набора данных одинаковы для всех типов наборов данных (SQL Azure, большие двоичные объекты Azure, таблицы Azure и т. д.).

Раздел **typeProperties** отличается для разных типов наборов данных. Он содержит сведения о расположении данных в хранилище данных, формате данных и т. д. Раздел typeProperties набора данных типа **AzureBlob** содержит следующие свойства.

| Свойство | Описание | Обязательно |
| --- | --- | --- |
| folderPath |Путь контейнеру и папке в хранилище BLOB-объектов. Пример: myblobcontainer\myblobfolder\ |Да |
| fileName |Имя большого двоичного объекта. Свойство fileName является необязательным и в нем учитывается регистр знаков.<br/><br/>Если указать имя файла, то действие (включая копирование) работает с определенным большим двоичным объектом.<br/><br/>Если значение fileName не указано, то копируются все большие двоичные объекты в folderPath для входного набора данных.<br/><br/>Если свойство fileName не указано для выходного набора данных, то имя созданного файла будет иметь следующий формат: Data.<Guid>.txt (например, Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.). |Нет |
| partitionedBy |Необязательное свойство. Его можно использовать, чтобы указать динамические путь к папке и имя файла для временного ряда данных. Например, путь к папке (значение folderPath) каждый час может быть другим. Дополнительные сведения и примеры см. ниже в разделе [Использование свойства partitionedBy](#using-partitionedBy-property). |Нет |
| свойства |Поддерживаются следующие типы формата: **TextFormat**, **AvroFormat**, **JsonFormat**, **OrcFormat**, **ParquetFormat**. Свойству **type** в разделе format необходимо присвоить одно из этих значений. Дополнительные сведения см. в разделах [Определение TextFormat](#specifying-textformat), [Определение AvroFormat](#specifying-avroformat), [Указание JsonFormat](#specifying-jsonformat), [Указание OrcFormat](#specifying-orcformat) и [Указание ParquetFormatt](#specifying-parquetformat). Если требуется скопировать файлы между файловыми хранилищами "как есть" (двоичное копирование), можно пропустить раздел форматирования в определениях входного и выходного наборов данных. |Нет |
| compression |Укажите тип и уровень сжатия данных. Поддерживаемые типы: **GZip**, **Deflate** и **BZip2**. Поддерживаемые уровни: **Optimal** и **Fastest**. Дополнительные сведения см. в разделе [Поддержка сжатия](#compression-support). Сейчас для данных в формате **AvroFormat**, **OrcFormat** или **ParquetFormat** параметры сжатия не поддерживаются. Для чтения данных в этих форматах фабрика данных использует в метаданных кодек сжатия. Тем не менее при записи в файл в одном из этих форматов фабрика данных выбирает кодек сжатия по умолчанию для этого формата. Например, SNAPPY для ParquetFormat и ZLIB для OrcFormat. |Нет |

### <a name="using-partitionedby-property"></a>Использование свойства partitionedBy
Как упоминалось в предыдущем разделе, динамические значения folderPath и fileName для данных временных рядов можно задать в разделе **partitionedBy** , а также с помощью макросов фабрики данных и системных переменных SliceStart и SliceEnd, указывающих время начала и окончания среза данных.

Дополнительные сведения о наборах данных временных рядов, планировании и срезах см. в статьях [Наборы данных в фабрике данных Azure](data-factory-create-datasets.md) и [Планирование и исполнение с использованием фабрики данных](data-factory-scheduling-and-execution.md).

#### <a name="sample-1"></a>Пример 1
    "folderPath": "wikidatagateway/wikisampledataout/{Slice}",
    "partitionedBy":
    [
        { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
    ],

В этом примере {Slice} заменяется значением SliceStart (системная переменная фабрики данных) в формате ГГГГММДДЧЧ. SliceStart указывает время начала среза. Значение folderPath отличается для каждого среза. Например: wikidatagateway/wikisampledataout/2014100103 или wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Пример 2
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy":
     [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
    ],

В этом примере год, месяц, день и время SliceStart извлекаются в отдельные переменные, используемые в свойствах folderPath и fileName.

[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

[!INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="azure-blob-copy-activity-type-properties"></a>Свойства типа "Действие копирования большого двоичного объекта Azure"
Полный список разделов и свойств, используемых для определения действий, см. в статье [Создание конвейеров](data-factory-create-pipelines.md). Свойства, например имя, описание, входные и выходные таблицы, политики и т. д., доступны для всех типов действий.

С другой стороны, свойства, доступные в разделе typeProperties действия, зависят от конкретного типа действия. Для действия копирования они различаются в зависимости от типов источников и приемников.

При перемещении данных из хранилища BLOB-объектов Azure в действии копирования задается тип источника **BlobSource**. Аналогично, при перемещении данных в хранилище BLOB-объектов Azure в действии копирования задается тип приемника **BlobSink**. Этот раздел содержит список свойств, поддерживаемых типами BlobSource и BlobSink.

Для **BlobSource** в разделе **typeProperties** могут быть указаны следующие свойства.

| Свойство | Описание | Допустимые значения | Обязательно |
| --- | --- | --- | --- |
| recursive |Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. |True (значение по умолчанию), False |Нет |

Для **BlobSink** в разделе **typeProperties** могут быть указаны следующие свойства.

| Свойство | Описание | Допустимые значения | Обязательно |
| --- | --- | --- | --- |
| copyBehavior |Это свойство определяет поведение функции копирования, когда в качестве источника используется BlobSource или FileSystem. |/ **PreserveHierarchy:** сохраняет иерархию файлов в целевой папке. Относительный путь исходного файла в исходной папке идентичен относительному пути целевого файла в целевой папке.<br/><br/>**FlattenHierarchy**: все файлы из исходной папки размещаются на первом уровне в целевой папке. Целевые файлы имеют автоматически сформированное имя. <br/><br/>**MergeFiles: (по умолчанию)** объединяет все файлы из исходной папки в один файл. Если указано имя файла или большого двоичного объекта, именем объединенного файла будет указанное имя; в противном случае имя файла будет автоматически сформировано. |Нет |

**BlobSource** также поддерживает эти два свойства для обеспечения обратной совместимости.

* **treatEmptyAsNull**указывает, следует ли интерпретировать null или пустую строку как значение null.
* **skipHeaderLineCount** указывает, сколько строк необходимо пропустить. Применяется, только когда для входного набора данных используется TextFormat.

Аналогичным образом **BlobSink** поддерживает следующее свойство для обеспечения обратной совместимости.

* **blobWriterAddHeader**указывает, следует ли добавлять заголовок определений столбцов при записи в выходной набор данных.

Наборы данных поддерживают теперь такие свойства, которые реализуют те же функции: **treatEmptyAsNull**, **skipLineCount**, **firstRowAsHeader**.

В таблице ниже приведены инструкции по использованию новых свойств набора данных вместо указанных свойств источника и приемника больших двоичных объектов.

| Свойство "Действие копирования" | Свойство "Набор данных" |
|:--- |:--- |
| skipHeaderLineCount в BlobSource |skipLineCount и firstRowAsHeader. Сначала строки пропускаются, а затем первая строка считывается как заголовок. |
| treatEmptyAsNull в BlobSource |treatEmptyAsNull во входном наборе данных |
| blobWriterAddHeader в BlobSink |firstRowAsHeader в выходном наборе данных |

Подробную информацию об этих свойствах см. в разделе [Определение TextFormat](#specifying-textformat).    

### <a name="recursive-and-copybehavior-examples"></a>Примеры recursive и copyBehavior
В данном разделе описываются результаты выполнения операции копирования при использовании различных сочетаний значений recursive и copyBehavior.

| recursive | copyBehavior | Результаты выполнения операции |
| --- | --- | --- |
| Да |preserveHierarchy |Если у исходной папки "Папка1" такая структура:  <br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5<br/><br/>Целевая папка "Папка1" создается с такой же структурой, как и исходная папка.<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 |
| Да |flattenHierarchy |Если у исходной папки "Папка1" такая структура:  <br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5<br/><br/>Целевая папка "Папка1" создается со следующей структурой: <br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл1"<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл2"<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл3"<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл4"<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл5" |
| Да |mergeFiles |Если у исходной папки "Папка1" такая структура:  <br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5<br/><br/>Целевая папка "Папка1" создается со следующей структурой: <br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Содержимое файлов "Файл1", "Файл2", "Файл3", "Файл4" и "Файл5" объединяется в один файл с автоматически созданным именем. |
| нет |preserveHierarchy |Если у исходной папки "Папка1" такая структура:  <br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5<br/><br/>Целевая папка "Папка1" создается со следующей структурой:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/><br/><br/>Папка "Вложенная_папка1" с файлами "Файл3", "Файл4" и "Файл5" не будет включена в эту папку. |
| нет |flattenHierarchy |Если у исходной папки "Папка1" такая структура: <br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5<br/><br/>Целевая папка "Папка1" создается со следующей структурой:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл1"<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл2"<br/><br/><br/>Папка "Вложенная_папка1" с файлами "Файл3", "Файл4" и "Файл5" не будет включена в эту папку. |
| нет |mergeFiles |Если у исходной папки "Папка1" такая структура: <br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5<br/><br/>Целевая папка "Папка1" создается со следующей структурой:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Содержимое файлов "Файл1" и "Файл2" объединяется в один файл с автоматически созданным именем. Автоматически созданное имя для "Файл1"<br/><br/>Папка "Вложенная_папка1" с файлами "Файл3", "Файл4" и "Файл5" не будет включена в эту папку. |

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[!INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>Производительность и настройка
Ознакомьтесь со статьей [Руководство по настройке производительности действия копирования](data-factory-copy-activity-performance.md), в которой описываются ключевые факторы, влияющие на производительность перемещения данных (действие копирования) в фабрике данных Azure, и различные способы оптимизации этого процесса.



<!--HONumber=Nov16_HO3-->


