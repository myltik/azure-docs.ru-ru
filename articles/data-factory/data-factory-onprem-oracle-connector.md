---
title: "Перемещение данных в базу данных Oracle и обратно с помощью фабрики данных | Документация Майкрософт"
description: "Узнайте, как переместить данные в локальную базу данных Oracle или из нее с помощью фабрики данных Azure."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 3c20aa95-a8a1-4aae-9180-a6a16d64a109
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2016
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 6ec8ac288a4daf6fddd6d135655e62fad7ae17c2
ms.openlocfilehash: c3161d1b870936ff51bbe092f1ca8ff320cf956e


---
# <a name="move-data-tofrom-on-premises-oracle-using-azure-data-factory"></a>Перемещение данных в локальную базу данных Oracle и обратно с помощью фабрики данных Azure
В этой статье описано использование действия копирования в фабрике данных для перемещения данных в Oracle из другого хранилища данных и обратно. В этой статье мы продолжим тему о [действиях перемещения данных](data-factory-data-movement-activities.md) , в которой приведены общие сведения о перемещении данных с помощью действия копирования и поддерживаемых сочетаниях хранилищ данных.

## <a name="supported-versions"></a>Поддерживаемые версии
Благодаря установке поставщика данных Oracle для .NET 12.1 на компьютере шлюза управления данными этот соединитель Oracle можно использовать для копирования данных в экземплярах Oracle более ранних версий. Дополнительные сведения о настройке см. в разделе [Установка](#installation).

* Oracle 12c
* Oracle 11g
* Oracle 10g Release 2

## <a name="installation"></a>Установка
Чтобы служба фабрики данных Azure могла подключаться к локальной базе данных Oracle, необходимо установить следующие компоненты:

* Шлюз управления данными на том же компьютере, на котором размещена база данных, или на отдельном компьютере во избежание конкуренции за ресурсы. Шлюз управления данными — это агент клиента, который обеспечивает безопасное и управляемое подключение локальных источников данных к облачным службам. Сведения о шлюзе управления данными см. в статье [Перемещение данных между локальными источниками и облаком](data-factory-move-data-between-onprem-and-cloud.md).
* Поставщик данных Oracle для .NET. Входит в состав [компонентов доступа к данным Oracle для Windows](http://www.oracle.com/technetwork/topics/dotnet/downloads/). Установите соответствующую версию (32- или 64- разрядную) на главном компьютере, на котором установлен шлюз. [Поставщик данных Oracle для .NET 12.1](http://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) может обращаться к Oracle Database 10g версии 2 или более поздней версии.

    Если вы выбрали "XCopy Installation" (Установка XCopy), то следуйте инструкциям в файле readme.htm. Мы советуем выбрать установщик через пользовательский интерфейс (а не через XCopy).

    После установки поставщика перезапустите на компьютере службу узла шлюза управления данными, используя приложение "Службы" или диспетчер конфигурации шлюза управления данными.  

> [!NOTE]
> Советы по устранению неполадок, связанных со шлюзом или подключением, см. в разделе [Устранение неполадок в работе шлюза](data-factory-data-management-gateway.md#troubleshooting-gateway-issues).
>
>

## <a name="copy-data-wizard"></a>Мастер копирования данных
Самый простой способ создать конвейер, копирующий данные в базу данных Oracle или из нее в любое поддерживаемое хранилище-приемник, — использовать мастер копирования данных. В статье [Руководство. Создание конвейера с действием копирования с помощью мастера копирования фабрики данных](data-factory-copy-data-wizard-tutorial.md) приведены краткие пошаговые указания по созданию конвейера с помощью мастера копирования данных.

Ниже приведены примеры с определениями JSON, которые можно использовать для создания конвейера с помощью [портала Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) или [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Вы узнаете, как копировать данные между базой данных Oracle и хранилищем BLOB-объектов Azure. Тем не менее данные можно копировать в любой из указанных [здесь](data-factory-data-movement-activities.md#supported-data-stores-and-formats) приемников. Это делается с помощью действия копирования в фабрике данных Azure.   

## <a name="sample-copy-data-from-oracle-to-azure-blob"></a>Пример копирования данных из Oracle в хранилище BLOB-объектов Azure
В этом примере показано, как скопировать данные из локальной базы данных Oracle в хранилище BLOB-объектов Azure. Тем не менее данные можно копировать **непосредственно** в любой из указанных [здесь](data-factory-data-movement-activities.md#supported-data-stores-and-formats) приемников. Это делается с помощью действия копирования в фабрике данных Azure.  

Образец состоит из следующих сущностей фабрики данных.

1. Связанная служба типа [OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties).
2. Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service).
3. Входной [набор данных](data-factory-create-datasets.md) типа [OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties).
4. Выходной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5. [Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [OracleSource](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) в качестве источника и [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) в качестве приемника.

В примере данные из локальной таблицы Oracle ежечасно копируются в хранилище BLOB-объектов. Дополнительные сведения о различных свойствах, используемых в примере, см. в документации, ссылки на которую приведены в разделах после примеров.

**Связанная служба Oracle**

    {
      "name": "OnPremisesOracleLinkedService",
      "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
          "ConnectionString": "data source=<data source>;User Id=<User Id>;Password=<Password>;",
          "gatewayName": "<gateway name>"
        }
      }
    }

**Связанная служба хранилища BLOB-объектов Azure**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
      }
    }

**Входной набор данных Oracle**

В примере предполагается, что таблица MyTable уже создана в Oracle и содержит столбец с именем timestampcolumn для данных временных рядов.

Если параметру external присвоить значение true, фабрика данных воспримет этот набор данных как внешний и созданный не в результате какого-либо действия в этой службе.

    {
        "name": "OracleInput",
        "properties": {
            "type": "OracleTable",
            "linkedServiceName": "OnPremisesOracleLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
               "external": true,
            "availability": {
                "offset": "01:00:00",
                "interval": "1",
                "anchorDateTime": "2014-02-27T12:00:00",
                "frequency": "Hour"
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

Данные записываются в новый BLOB-объект каждый час (frequency: hour, interval: 1). Путь к папке с BLOB-объектом и имя файла вычисляются динамически на основе времени начала обрабатываемого среза. В пути к папке используется год, месяц, день и час времени начала.

    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

Конвейер содержит действие копирования, которое использует входные и выходные наборы данных и выполняется ежечасно. В определении JSON конвейера для типа **source** устанавливается значение **OracleSource**, а для типа **sink** — значение **BlobSink**.  SQL-запрос, указанный для свойства **oracleReaderQuery** , выбирает для копирования данные за последний час.

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
          {
            "name": "OracletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [
              {
                "name": " OracleInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "OracleSource",
                "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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


Необходимо настроить строку запроса в зависимости от того, как настроены даты в базе данных Oracle. Если вы видите следующее сообщение об ошибке:

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

может потребоваться изменить запрос, как показано ниже (используя функцию to_date).

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\')  AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"

## <a name="sample-copy-data-from-azure-blob-to-oracle"></a>Пример копирования данных из BLOB-объекта Azure в Oracle
В этом примере показано, как скопировать данные из хранилища BLOB-объектов Azure в локальную базу данных Oracle. Однако данные можно **напрямую** копировать из любого указанного [здесь](data-factory-data-movement-activities.md#supported-data-stores-and-formats) источника, используя действие копирования в фабрике данных Azure.  

Образец состоит из следующих сущностей фабрики данных.

1. Связанная служба типа [OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties).
2. Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service).
3. Входной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Выходной [набор данных](data-factory-create-datasets.md) типа [OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties).
5. [Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) в качестве источника и [OracleSink](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) в качестве приемника.

В примере данные из хранилища BLOB-объектов каждый час копируются в таблицу в локальной базе данных Oracle. Дополнительные сведения о различных свойствах, используемых в примере, см. в документации, ссылки на которую приведены в разделах после примеров.

**Связанная служба Oracle**

    {
      "name": "OnPremisesOracleLinkedService",
      "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
          "ConnectionString": "data source=<data source>;User Id=<User Id>;Password=<Password>;",
          "gatewayName": "<gateway name>"
        }
      }
    }

**Связанная служба хранилища BLOB-объектов Azure**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
      }
    }

**Входной набор данных BLOB-объекта Azure**

Данные берутся из нового BLOB-объекта каждый час (frequency: hour, interval: 1). Путь к папке с BLOB-объектом и имя файла вычисляются динамически на основе времени начала обрабатываемого среза. В пути к папке используется год, месяц и день начала, а в имени файла — час начала. Когда для параметра external задано значение true, служба фабрики данных считает эту таблицу внешней и созданной не в результате какого-либо действия в фабрике данных.

    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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

**Выходной набор данных Oracle**

В примере предполагается, что вы создали в Oracle таблицу MyTable. Таблицу в Oracle нужно создать с таким же количеством столбцов, которое должно быть в CSV-файле большого двоичного объекта. Новые строки добавляются в таблицу каждый час.

    {
        "name": "OracleOutput",
        "properties": {
            "type": "OracleTable",
            "linkedServiceName": "OnPremisesOracleLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": "1"
            }
        }
    }


**Конвейер с действием копирования**

Конвейер содержит действие копирования, которое использует входной и выходной наборы данных и выполняется каждый час. В определении JSON конвейера для типа **source** устанавливается значение **BlobSource**, а для типа **sink** — значение **OracleSink**.  

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
          {
            "name": "AzureBlobtoOracle",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": "OracleOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "OracleSink"
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


## <a name="oracle-linked-service-properties"></a>Свойства связанной службы Oracle
В следующей таблице содержится описание элементов JSON, которые относятся к связанной службе Oracle.

| Свойство | Описание | Обязательно |
| --- | --- | --- |
| type |Для свойства type необходимо задать значение **OnPremisesOracle** |Да |
| connectionString |В свойстве connectionString указываются сведения, необходимые для подключения к экземпляру базы данных Oracle. |Да |
| gatewayName |Имя шлюза, который будет использоваться для подключения к локальному серверу Oracle. |Да |

Дополнительные сведения о настройке учетных данных для локального источника данных Oracle см. в статье [Перемещение данных между локальными источниками и облаком с помощью шлюза управления данными](data-factory-move-data-between-onprem-and-cloud.md).

## <a name="oracle-dataset-type-properties"></a>Свойства типа "Набор данных Oracle"
Полный список разделов и свойств, используемых для определения наборов данных, см. в статье [Наборы данных](data-factory-create-datasets.md). Такие разделы, как структура, доступность и политика набора данных JSON, одинаковы для всех видов наборов данных (Oracle, большие двоичные объекты Azure, таблицы Azure и т. д.).

Раздел typeProperties во всех типах наборов данных разный. В нем содержатся сведения о расположении данных в хранилище данных. Раздел typeProperties набора данных с типом OracleTable обладает следующими свойствами.

| Свойство | Описание | Обязательно |
| --- | --- | --- |
| tableName |Имя таблицы в базе данных Oracle, на которое ссылается связанная служба. |Нет (если указан параметр **oracleReaderQuery** объекта **OracleSource**) |

## <a name="oracle-copy-activity-type-properties"></a>Свойства типа "Действие копирования Oracle"
Полный список разделов и свойств, используемых для определения действий, см. в статье [Создание конвейеров](data-factory-create-pipelines.md). Свойства (включая имя, описание, входные и выходные таблицы, политику и т. д.) доступны для всех типов действий.

> [!NOTE]
> Действие копирования принимает только один набор входных данных и возвращает только один набор выходных.
>
>

С другой стороны, свойства, доступные в разделе typeProperties действия, зависят от конкретного типа действия. Для действия копирования они различаются в зависимости от типов источников и приемников.

### <a name="oraclesource"></a>OracleSource
Если источник относится к типу **OracleSource**, в разделе **typeProperties** для действия копирования доступны следующие свойства:

| Свойство | Описание | Допустимые значения | Обязательно |
| --- | --- | --- | --- |
| oracleReaderQuery |Используйте пользовательский запрос для чтения данных. |Строка запроса SQL. Например: select * from MyTable <br/><br/>Если не указано другое, выполняется следующая инструкция SQL: select * from MyTable |Нет (если для свойства **tableName** задано значение **dataset**). |

### <a name="oraclesink"></a>OracleSink
**OracleSink** поддерживает следующие свойства:

| Свойство | Описание | Допустимые значения | Обязательно |
| --- | --- | --- | --- |
| writeBatchTimeout |Время ожидания до выполнения операции пакетной вставки, пока не завершится срок ее действия. |Интервал времени<br/><br/>  Пример: 00:30:00 (30 минут). |Нет |
| writeBatchSize |Вставляет данные в таблицу SQL, когда размер буфера достигает значения writeBatchSize. |Целое число (количество строк) |Нет (значение по умолчанию — 100) |
| sqlWriterCleanupScript |Укажите запрос на выполнение действия копирования, позволяющий убедиться в том, что данные конкретного среза очищены. |Инструкция запроса. |Нет |
| sliceIdentifierColumnName |Укажите имя столбца, в которое действие копирования добавляет автоматически созданный идентификатор среза. Этот идентификатор используется для очистки данных конкретного среза при повторном запуске. |Имя столбца с типом данных binary(32). |Нет |

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-oracle"></a>Сопоставление типов для Oracle
Как упоминалось в статье о [действиях перемещения данных](data-factory-data-movement-activities.md), во время копирования типы источников автоматически преобразовываются в типы приемников. Такое преобразование выполняется в два этапа:

1. Преобразование собственных типов источников в тип .NET.
2. Преобразование типа .NET в собственный тип приемника.

Когда данные перемещаются из Oracle, для преобразования типа данных Oracle в тип .NET и наоборот используются следующие сопоставления.

| Тип данных Oracle | Тип данных платформы .NET |
| --- | --- |
| BFILE |Byte[] |
| BLOB |Byte[] |
| CHAR |Строка |
| CLOB |Строка |
| DATE |DateTime |
| FLOAT |Decimal |
| INTEGER |Decimal |
| INTERVAL YEAR TO MONTH |Int32 |
| INTERVAL DAY TO SECOND |Интервал времени |
| LONG |Строка |
| LONG RAW |Byte[] |
| NCHAR |Строка |
| NCLOB |Строка |
| NUMBER |Decimal |
| NVARCHAR2 |Строка |
| RAW |Byte[] |
| ROWID |Строка |
| TIMESTAMP |DateTime |
| TIMESTAMP WITH LOCAL TIME ZONE |DateTime |
| TIMESTAMP WITH TIME ZONE |DateTime |
| UNSIGNED INTEGER |NUMBER |
| VARCHAR2 |Строка |
| XML |Строка |

## <a name="troubleshooting-tips"></a>Советы по устранению неполадок
**Проблема.** Появляется следующее **сообщение об ошибке**: "Действие копирования обнаружило недопустимые параметры: UnknownParameterName, подробное сообщение: "Не удалось найти запрошенный поставщик данных .NET Framework". Возможно, он не установлен.  

**Возможные причины:**

1. Поставщик данных .NET Framework для Oracle не был установлен.
2. Поставщик данных .NET Framework для Oracle был установлен в .NET Framework 2.0 и не найден в папках .NET Framework 4.0.

**Решение:**

1. Если поставщик данных .NET для Oracle не установлен, [установите его](http://www.oracle.com/technetwork/topics/dotnet/downloads/) и повторите сценарий.
2. Если сообщение об ошибке появляется даже после установки поставщика, выполните следующие действия:
   1. Откройте файл конфигурации .NET 2.0 из папки: <system disk> \Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
   2. Найдите **поставщик данных Oracle для .NET** и вы сможете найти запись, как показано в следующем примере, в разделеder **system.data** -> **DbProviderFactories**:
           “<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />”
3. Скопируйте эту запись в файл конфигурации компьютера в следующей папке v4.0: <system disk>\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config, измените версию на 4.xxx.x.x.
4. Установите файл <путь установки ODP.NET>\11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll в глобальный кэш сборок (GAC), выполнив команду `gacutil /i [provider path]`.

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>Производительность и настройка
Ознакомьтесь со статьей [Руководство по настройке производительности действия копирования](data-factory-copy-activity-performance.md), в которой описываются ключевые факторы, влияющие на производительность перемещения данных (действие копирования) в фабрике данных Azure, и различные способы оптимизации этого процесса.



<!--HONumber=Nov16_HO3-->


