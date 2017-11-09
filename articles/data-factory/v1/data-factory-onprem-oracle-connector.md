---
title: "Копирование данных в базу данных Oracle и из нее с помощью фабрики данных | Документация Майкрософт"
description: "Узнайте, как копировать данные в локальную базу данных Oracle или из нее с помощью фабрики данных Azure."
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
ms.date: 10/01/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 09850217018321f67e2e20270aadd054258c90a2
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2017
---
# <a name="copy-data-tofrom-on-premises-oracle-using-azure-data-factory"></a>Копирование данных в локальную базу данных Oracle и обратно с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Версия 1 — общедоступная](data-factory-onprem-oracle-connector.md)
> * [Версия 2 — предварительная](../connector-oracle.md)

> [!NOTE]
> Статья относится к версии 1 фабрики данных, которая является общедоступной версией. Если вы используете версию 2 службы фабрики данных, которая находится на этапе предварительной версии, см. статью [Copy data from and to Oracle using Azure Data Factory](../connector-oracle.md) (Копирование данных в и из Oracle с помощью фабрики данных Azure).


В этой статье рассказывается, как с помощью действия копирования в фабрике данных Azure перемещать данные в локальную базу данных Oracle и обратно. Это продолжение статьи о [действиях перемещения данных](data-factory-data-movement-activities.md), в которой приведены общие сведения о перемещении данных с помощью действия копирования.

## <a name="supported-scenarios"></a>Поддерживаемые сценарии использования.
Данные можно скопировать **из базы данных Oracle** в следующие хранилища данных:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Данные можно скопировать **в базу данных Oracle** из следующих хранилищ данных:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>Предварительные требования
Фабрика данных поддерживает подключение к локальным источникам Oracle с помощью шлюза управления данными. Сведения о шлюзе управления данными см. в статье [Шлюз управления данными](data-factory-data-management-gateway.md), а пошаговые инструкции по настройке шлюза для перемещения данных с использованием конвейера — в статье [Перемещение данных между локальными источниками и облаком с помощью шлюза управления данными](data-factory-move-data-between-onprem-and-cloud.md).

Шлюз является обязательным, даже если база данных Oracle размещается на виртуальной машине Azure IaaS. Шлюз можно установить на той же ВМ IaaS, на которой размещается хранилище данных, или на другой ВМ. Важно, чтобы шлюз мог подключиться к базе данных.

> [!NOTE]
> Советы по устранению неполадок, связанных со шлюзом или подключением, см. в разделе [Устранение неполадок в работе шлюза](data-factory-data-management-gateway.md#troubleshooting-gateway-issues).

## <a name="supported-versions-and-installation"></a>Поддерживаемые версии и установка
Этот соединитель Oracle поддерживает две версии драйверов.

- **Драйвер Майкрософт для Oracle (рекомендуется)**. Начиная со шлюза управления данными версии 2.7, драйвер Майкрософт для Oracle устанавливается автоматически вместе со шлюзом. Поэтому нет необходимости дополнительно устанавливать драйвер для подключения к Oracle. Кроме того, производительность копирования при использовании этого драйвера может увеличиться. Ниже перечислены поддерживаемые версии баз данных Oracle.
    - Oracle 12c R1 (12.1)
    - Oracle 11g R1, R2 (11.1, 11.2)
    - Oracle 10g R1, R2 (10.1, 10.2)
    - Oracle 9i R1, R2 (9.0.1, 9.2)
    - Oracle 8i R3 (8.1.7)

> [!IMPORTANT]
> В настоящее время драйвер Майкрософт для Oracle поддерживает копирование данных из базы данных Oracle, но не поддерживает запись к базу данных Oracle. Обратите внимание, что этот драйвер не поддерживает возможность тестирования подключения на вкладке "Диагностика" в шлюзе управления данными. Кроме того, для проверки подключения можно воспользоваться мастером копирования.
>

- **Поставщик данных Oracle для .NET**: для копирования данных в базу данных Oracle и из нее также можно использовать поставщик данных Oracle. Входит в состав [компонентов доступа к данным Oracle для Windows](http://www.oracle.com/technetwork/topics/dotnet/downloads/). Установите соответствующую версию (32- или 64- разрядную) на компьютере, на котором установлен шлюз. [Поставщик данных Oracle для .NET 12.1](http://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) может обращаться к Oracle Database 10g версии 2 или более поздней версии.

    Если вы выбрали "XCopy Installation" (Установка XCopy), то следуйте инструкциям в файле readme.htm. Мы советуем выбрать установщик через пользовательский интерфейс (а не через XCopy).

    После установки поставщика **перезапустите** на компьютере службу узла шлюза управления данными, используя приложение "Службы" или диспетчер конфигурации шлюза управления данными.  

При использовании мастера копирования для создания конвейера копирования тип драйвера будет определен автоматически. Драйвер Майкрософт будет использоваться по умолчанию, если только вы не используете версию шлюза ниже 2.7 или выбрали Oracle в качестве приемника.

## <a name="getting-started"></a>Приступая к работе
Создать конвейер с действием копирования, который перемещает данные из локальной базы данных Oracle или в нее, можно с помощью различных инструментов и интерфейсов API.

Проще всего создать конвейер с помощью **мастера копирования**. В статье [Руководство. Создание конвейера с действием копирования с помощью мастера копирования фабрики данных](data-factory-copy-data-wizard-tutorial.md) приведены краткие пошаговые указания по созданию конвейера с помощью мастера копирования данных.

Также для создания конвейера можно использовать следующие инструменты: **портал Azure**, **Visual Studio**, **Azure PowerShell**, **шаблон Azure Resource Manager**, **API .NET** и **REST API**. Пошаговые инструкции по созданию конвейера с действием копирования см. в [руководстве по действию копирования](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Независимо от используемого средства или API-интерфейса, для создания конвейера, который перемещает данные из источника данных в приемник, выполняются следующие шаги:

1. Создание **фабрики данных**. Фабрика данных может содержать один или несколько конвейеров. 
2. Создайте **связанные службы**, чтобы связать входные и выходные данные с фабрикой данных. Например, при копировании данных из базы данных Oralce в хранилище BLOB-объектов Azure создайте две связанные службы, чтобы связать базу данных Oralce и учетную запись хранения Azure с фабрикой данных. Сведения о свойствах связанной службы, относящихся к Oralce, см. в разделе [Свойства связанной службы](#linked-service-properties).
3. Создайте **наборы данных**, которые представляют входные и выходные данные для операции копирования. В примере, упомянутом на последнем шаге, создайте набор данных, чтобы указать таблицу в базе данных Oracle, содержащую входные данные. И создайте другой набор данных, чтобы указать контейнер BLOB-объектов и папку, содержащую данные, скопированные из базы данных Oracle. Сведения о свойствах набора данных, относящихся к Oracle, см. в разделе [Свойства набора данных](#dataset-properties).
4. Создайте **конвейер** с действием копирования, который принимает входной набор данных и возвращает выходной набор данных. В примере выше OracleSource используется как источник, а BlobSink — как приемник для действия копирования. Аналогично, при копировании из хранилища BLOB-объектов Azure в базу данных Oracle в действии копирования используются BlobSource и OracleSink. Сведения о свойствах действия копирования, относящихся к базе данных Oracle, см. в разделе [Свойства действия копирования](#copy-activity-properties). Для получения сведений о том, как использовать хранилище данных в качестве источника или приемника, щелкните ссылку в предыдущем разделе об источнике данных. 

Если вы используете мастер, то он автоматически создает определения JSON для сущностей фабрики данных (связанных служб, наборов данных и конвейера). При использовании инструментов и интерфейсов API (за исключением API .NET) вы самостоятельно определяете эти сущности фабрики данных в формате JSON.  Примеры с определениями JSON для сущностей фабрики данных, которые используются для копирования данных в локальную базу данных Oracle и обратно, см. в разделе [Примеры JSON](#json-examples-for-copying-data-to-and-from-oracle-database) далее в этой статье.

Следующие разделы содержат сведения о свойствах JSON, которые используются для определения сущностей фабрики данных.

## <a name="linked-service-properties"></a>Свойства связанной службы
В следующей таблице содержится описание элементов JSON, которые относятся к связанной службе Oracle.

| Свойство | Описание | Обязательно |
| --- | --- | --- |
| type |Для свойства type необходимо задать значение **OnPremisesOracle** |Да |
| driverType | Укажите, какой драйвер следует использовать для копирования данных в базу данных Oracle и из нее. Допустимые значения: **Майкрософт** или **ODP** (по умолчанию). Дополнительные сведения о драйверах см. в разделе [Поддерживаемые версии и установка](#supported-versions-and-installation). | Нет |
| connectionString | В свойстве connectionString указываются сведения, необходимые для подключения к экземпляру базы данных Oracle. | Да |
| gatewayName | Имя шлюза, который будет использоваться для подключения к локальному серверу Oracle. |Да |

**Пример: использование драйвера Майкрософт**
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Пример: использование драйвера ODP**

Допустимые форматы приведены на [этом сайте](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/).

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<hostname>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<SID>)));
User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных
Полный список разделов и свойств, используемых для определения наборов данных, см. в статье [Наборы данных](data-factory-create-datasets.md). Такие разделы, как структура, доступность и политика набора данных JSON, одинаковы для всех видов наборов данных (Oracle, большие двоичные объекты Azure, таблицы Azure и т. д.).

Раздел typeProperties во всех типах наборов данных разный. В нем содержатся сведения о расположении данных в хранилище данных. Раздел typeProperties набора данных с типом OracleTable обладает следующими свойствами.

| Свойство | Описание | Обязательно |
| --- | --- | --- |
| tableName |Имя таблицы в базе данных Oracle, на которое ссылается связанная служба. |Нет (если указан параметр **oracleReaderQuery** объекта **OracleSource**) |

## <a name="copy-activity-properties"></a>Свойства действия копирования
Полный список разделов и свойств, используемых для определения действий, см. в статье [Создание конвейеров](data-factory-create-pipelines.md). Свойства (включая имя, описание, входные и выходные таблицы, политику и т. д.) доступны для всех типов действий.

> [!NOTE]
> Действие копирования принимает только один набор входных данных и возвращает только один набор выходных.

В свою очередь свойства, доступные в разделе typeProperties действия, зависят от конкретного типа действия. Для действия копирования они различаются в зависимости от типов источников и приемников.

### <a name="oraclesource"></a>OracleSource
Если источник относится к типу **OracleSource**, в разделе **typeProperties** для действия копирования доступны следующие свойства:

| Свойство | Описание | Допустимые значения | Обязательно |
| --- | --- | --- | --- |
| oracleReaderQuery |Используйте пользовательский запрос для чтения данных. |Строка запроса SQL. Например, select * from MyTable <br/><br/>Если не указано другое, то выполняется следующая инструкция SQL: select * from MyTable |Нет (если для свойства **tableName** задано значение **dataset**). |

### <a name="oraclesink"></a>OracleSink
**OracleSink** поддерживает следующие свойства:

| Свойство | Описание | Допустимые значения | Обязательно |
| --- | --- | --- | --- |
| writeBatchTimeout |Время ожидания до выполнения операции пакетной вставки, пока не завершится срок ее действия. |Интервал времени<br/><br/> Пример: 00:30:00 (30 минут). |Нет |
| writeBatchSize |Вставляет данные в таблицу SQL, когда размер буфера достигает значения writeBatchSize. |Целое число (количество строк) |Нет (значение по умолчанию — 100) |
| sqlWriterCleanupScript |Укажите запрос на выполнение действия копирования, позволяющий убедиться в том, что данные конкретного среза очищены. |Инструкция запроса. |Нет |
| sliceIdentifierColumnName |Укажите имя столбца, в которое действие копирования добавляет автоматически созданный идентификатор среза. Этот идентификатор используется для очистки данных конкретного среза при повторном запуске. |Имя столбца с типом данных binary(32). |Нет |

## <a name="json-examples-for-copying-data-to-and-from-oracle-database"></a>Примеры JSON для копирования данных в базу данных Oracle и обратно
Ниже приведены примеры с определениями JSON, которые можно использовать для создания конвейера с помощью [портала Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) или [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Вы узнаете, как копировать данные между базой данных Oracle и хранилищем BLOB-объектов Azure. Тем не менее данные можно копировать в любой из указанных [здесь](data-factory-data-movement-activities.md#supported-data-stores-and-formats) приемников. Это делается с помощью действия копирования в фабрике данных Azure.   

## <a name="example-copy-data-from-oracle-to-azure-blob"></a>Пример копирования данных из Oracle в большой двоичный объект Azure

Образец состоит из следующих сущностей фабрики данных.

1. Связанная служба типа [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
2. Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Входной [набор данных](data-factory-create-datasets.md) типа [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
4. Выходной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [OracleSource](data-factory-onprem-oracle-connector.md#copy-activity-properties) в качестве источника и [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) в качестве приемника.

В примере данные из локальной таблицы Oracle ежечасно копируются в хранилище BLOB-объектов. Дополнительные сведения о различных свойствах, используемых в примере, см. в документации, ссылки на которую приведены в разделах после примеров.

**Связанная служба Oracle**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Связанная служба хранилища BLOB-объектов Azure**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
    }
}
```

**Входной набор данных Oracle**

В примере предполагается, что таблица MyTable уже создана в Oracle и содержит столбец с именем timestampcolumn для данных временных рядов.

Если параметру external присвоить значение true, фабрика данных воспримет этот набор данных как внешний и созданный не в результате какого-либо действия в этой службе.

```json
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
```

**Выходной набор данных BLOB-объекта Azure**

Данные записываются в новый BLOB-объект каждый час (frequency: hour, interval: 1). Путь к папке с BLOB-объектом и имя файла вычисляются динамически на основе времени начала обрабатываемого среза. В пути к папке используется год, месяц, день и час времени начала.

```json
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
```

**Конвейер с действием копирования**

Конвейер содержит действие копирования, которое использует входные и выходные наборы данных и выполняется ежечасно. В определении JSON конвейера для типа **source** устанавливается значение **OracleSource**, а для типа **sink** — значение **BlobSink**.  SQL-запрос, указанный для свойства **oracleReaderQuery** , выбирает для копирования данные за последний час.

```json
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
```

## <a name="example-copy-data-from-azure-blob-to-oracle"></a>Пример копирования данных из большого двоичного объекта Azure в Oracle
В этом примере показано, как скопировать данные из хранилища BLOB-объектов Azure в локальную базу данных Oracle. Однако данные можно **напрямую** копировать из любого указанного [здесь](data-factory-data-movement-activities.md#supported-data-stores-and-formats) источника, используя действие копирования в фабрике данных Azure.  

Образец состоит из следующих сущностей фабрики данных.

1. Связанная служба типа [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
2. Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Входной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Выходной [набор данных](data-factory-create-datasets.md) типа [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties).
5. [Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) в качестве источника и [OracleSink](data-factory-onprem-oracle-connector.md#copy-activity-properties) в качестве приемника.

В примере данные из хранилища BLOB-объектов каждый час копируются в таблицу в локальной базе данных Oracle. Дополнительные сведения о различных свойствах, используемых в примере, см. в документации, ссылки на которую приведены в разделах после примеров.

**Связанная служба Oracle**
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<hostname>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<SID>)));
            User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Связанная служба хранилища BLOB-объектов Azure**
```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
    }
}
```

**Входной набор данных BLOB-объекта Azure**

Данные берутся из нового BLOB-объекта каждый час (frequency: hour, interval: 1). Путь к папке с BLOB-объектом и имя файла вычисляются динамически на основе времени начала обрабатываемого среза. В пути к папке используется год, месяц и день начала, а в имени файла — час начала. Когда для параметра external задано значение true, служба фабрики данных считает эту таблицу внешней и созданной не в результате какого-либо действия в фабрике данных.

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
            "frequency": "Day",
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
```

**Выходной набор данных Oracle**

В примере предполагается, что вы создали в Oracle таблицу MyTable. Таблицу в Oracle нужно создать с таким же количеством столбцов, которое должно быть в CSV-файле большого двоичного объекта. Новые строки добавляются в таблицу каждый час.

```json
{
    "name": "OracleOutput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Day",
            "interval": "1"
        }
    }
}
```

**Конвейер с действием копирования**

Конвейер содержит действие копирования, которое использует входной и выходной наборы данных и выполняется каждый час. В определении JSON конвейера для типа **source** устанавливается значение **BlobSource**, а для типа **sink** — значение **OracleSink**.  

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-05T19:00:00",
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
                    "frequency": "Day",
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
```


## <a name="troubleshooting-tips"></a>Советы по устранению неполадок
### <a name="problem-1-net-framework-data-provider"></a>Проблема 1: поставщик данных .NET Framework

Отображается следующее **сообщение об ошибке**:

    Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .Net Framework Data Provider. It may not be installed”.  

**Возможные причины:**

1. Поставщик данных .NET Framework для Oracle не был установлен.
2. Поставщик данных .NET Framework для Oracle был установлен в .NET Framework 2.0 и не найден в папках .NET Framework 4.0.

**Решение:**

1. Если поставщик данных .NET для Oracle не установлен, [установите его](http://www.oracle.com/technetwork/topics/dotnet/downloads/) и повторите сценарий.
2. Если сообщение об ошибке появляется даже после установки поставщика, выполните следующие действия:
   1. Откройте файл конфигурации .NET 2.0 из папки: <system disk> \Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
   2. Найдите **поставщик данных Oracle для .NET** и вы сможете найти запись, как показано в следующем примере, в разделе **system.data** -> **DbProviderFactories**: “<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Поставщик данных Oracle для .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />”
3. Скопируйте эту запись в файл конфигурации компьютера в следующей папке v4.0: <system disk>\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config, измените версию на 4.xxx.x.x.
4. Установите файл <путь установки ODP.NET>\11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll в глобальный кэш сборок (GAC), выполнив команду `gacutil /i [provider path]`.## Советы по устранению неполадок

### <a name="problem-2-datetime-formatting"></a>Проблема 2: формат даты и времени

Отображается следующее **сообщение об ошибке**:

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

**Решение:**

Необходимо настроить строку запроса в действии копирования в зависимости от настроек дат в базе данных Oracle, как показано в следующем примере (используя функцию to_date):

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\')  AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"


## <a name="type-mapping-for-oracle"></a>Сопоставление типов для Oracle
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
| FLOAT |десятичное число, строка (если точность больше 28) |
| INTEGER |десятичное число, строка (если точность больше 28) |
| INTERVAL YEAR TO MONTH |Int32 |
| INTERVAL DAY TO SECOND |Интервал времени |
| LONG |Строка |
| LONG RAW |Byte[] |
| NCHAR |Строка |
| NCLOB |Строка |
| NUMBER |десятичное число, строка (если точность больше 28) |
| NVARCHAR2 |Строка |
| RAW |Byte[] |
| ROWID |Строка |
| TIMESTAMP |DateTime |
| TIMESTAMP WITH LOCAL TIME ZONE |DateTime |
| TIMESTAMP WITH TIME ZONE |DateTime |
| UNSIGNED INTEGER |NUMBER |
| VARCHAR2 |Строка |
| XML |Строка |

> [!NOTE]
> При использовании драйвера Майкрософт типы данных **INTERVAL YEAR TO MONTH** и **INTERVAL DAY TO SECOND** не поддерживаются.

## <a name="map-source-to-sink-columns"></a>Сопоставление столбцов источника и приемника
Дополнительные сведения о сопоставлении столбцов в наборе данных, используемом в качестве источника, со столбцами в приемнике см. в [этой статье](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Повторяющиеся операции чтения из реляционных источников
При копировании данных из реляционных хранилищ важно помнить о повторяемости, чтобы избежать непредвиденных результатов. В фабрике данных Azure можно вручную повторно выполнить срез. Вы можете также настроить для набора данных политику повтора, чтобы при сбое срез выполнялся повторно. При повторном выполнении среза в любом случае необходимо убедиться в том, что считываются те же данные, независимо от того, сколько раз выполняется срез. Ознакомьтесь с разделом [Повторяющиеся операции чтения из реляционных источников](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Производительность и настройка
Ознакомьтесь со статьей [Руководство по настройке производительности действия копирования](data-factory-copy-activity-performance.md), в которой описываются ключевые факторы, влияющие на производительность перемещения данных (действие копирования) в фабрике данных Azure, и различные способы оптимизации этого процесса.
