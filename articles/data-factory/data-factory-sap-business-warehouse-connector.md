---
title: "Перемещение данных из SAP Business Warehouse с помощью фабрики данных Azure | Документация Майкрософт"
description: "Узнайте, как перемещать данные из SAP Business Warehouse с помощью фабрики данных Azure."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 6a14e5e0cab25b26782ebd45b52aa7542b7e24d5
ms.lasthandoff: 03/17/2017


---
# <a name="move-data-from-sap-business-warehouse-using-azure-data-factory"></a>Перемещение данных из SAP Business Warehouse с помощью фабрики данных Azure
В этой статье описано, как можно использовать действие копирования в конвейере фабрики данных Azure для перемещения данных из SAP Business Warehouse в другое хранилище данных. В этой статье мы продолжим тему о [действиях перемещения данных](data-factory-data-movement-activities.md) , в которой приведены общие сведения о перемещении данных с помощью действия копирования и поддерживаемых сочетаниях хранилищ данных. Сейчас фабрика данных поддерживает перемещение данных из SAP Business Warehouse в другие хранилища данных, но не наоборот.


## <a name="supported-versions-and-installation"></a>Поддерживаемые версии и установка
Этот соединитель поддерживает SAP Business Warehouse версии 7.x. Он поддерживает копирование данных из InfoCube и QueryCubes (включая запросы BEx) с помощью запросов многомерных выражений.

Чтобы обеспечить возможность подключения к экземпляру SAP Business Warehouse, установите следующие компоненты.
- **Шлюз управления данными**. Служба фабрики данных поддерживает подключение к локальным хранилищам данным (включая SAP Business Warehouse) с помощью компонента, который называется шлюзом управления данными. В статье [Перемещение данных между локальными и облачными ресурсами](data-factory-move-data-between-onprem-and-cloud.md) приведены сведения о шлюзе управления данными и пошаговые инструкции по его настройке. Шлюз является обязательным, даже если хранилище SAP Business Warehouse размещается на виртуальной машине IaaS Azure. Шлюз можно установить на той же ВМ, на которой размещается хранилище данных, или на другой ВМ. Важно, чтобы шлюз мог подключиться к базе данных.
- **Библиотека SAP NetWeaver** на компьютере шлюза. Библиотеку SAP Netweaver можно получить у администратора SAP или непосредственно на странице [SAP Software Download Center](https://support.sap.com/swdc) (Центр загрузки программного обеспечения SAP). Найдите **примечание к SAP № 1025361**, чтобы узнать адрес для скачивания самой последней версии. Убедитесь, что архитектура библиотеки SAP NetWeaver (32-разрядная или 64-разрядная) соответствует установленному шлюзу. Установите все файлы, включенные в состав пакета SDK RFC для SAP NetWeaver, согласно примечанию к SAP. Библиотека SAP NetWeaver также включена в состав клиентских инструментов SAP.

## <a name="supported-sinks"></a>Поддерживаемые приемники
В таблице [Поддерживаемые хранилища данных](data-factory-data-movement-activities.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников или приемников для действия копирования. Данные из SAP Business Warehouse можно переместить в любое поддерживаемое хранилище данных-приемник. 

## <a name="copy-data-wizard"></a>Мастер копирования данных
Самый простой способ создать конвейер, копирующий данные из SAP Business Warehouse в любое поддерживаемое хранилище данных-приемник, — использовать мастер копирования данных. В статье [Руководство. Создание конвейера с действием копирования с помощью мастера копирования фабрики данных](data-factory-copy-data-wizard-tutorial.md) приведены краткие пошаговые указания по созданию конвейера с помощью мастера копирования данных.

Ниже приведены примеры с определениями JSON, которые можно использовать для создания конвейера с помощью [портала Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) или [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). В этом примере показано, как скопировать данные из локальной системы SAP Business Warehouse в хранилище BLOB-объектов Azure. Тем не менее данные можно копировать **непосредственно** в любой из указанных [здесь](data-factory-data-movement-activities.md#supported-data-stores-and-formats) приемников. Это делается с помощью действия копирования в фабрике данных Azure.  

> [!IMPORTANT]
> Этот пример содержит фрагменты кода JSON. Он не включает в себя пошаговые инструкции по созданию фабрики данных. Эти инструкции приведены в статье [Перемещение данных между локальными источниками и облаком с помощью шлюза управления данными](data-factory-move-data-between-onprem-and-cloud.md) .

## <a name="sample-copy-data-from-sap-business-warehouse-to-azure-blob"></a>Пример. Копирование данных из SAP Business Warehouse в большой двоичный объект Azure
Образец состоит из следующих сущностей фабрики данных.

1. Связанная служба типа [SapBw](#sap-bw-linked-service).
2. Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service).
3. Входной [набор данных](data-factory-create-datasets.md) типа [RelationalTable](#sap-bw-dataset).
4. Выходной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5. [Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [RelationalSource](#sap-bw-source-in-copy-activity) и [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

Пример копирует данные из экземпляра SAP Business Warehouse в большой двоичный объект Azure раз в час. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

Сначала настройте шлюз управления данными. Инструкции приведены в статье [Перемещение данных между локальными источниками и облаком с помощью шлюза управления данными](data-factory-move-data-between-onprem-and-cloud.md) .

### <a name="sap-business-warehouse-linked-service"></a>Связанная служба SAP Business Warehouse
Это связанная служба связывает экземпляр SAP Business Warehouse с фабрикой данных. Свойству type присваивается значение **SapBw**. Раздел typeProperties содержит сведения о подключении для экземпляра SAP Business Warehouse. 

```json
{
    "name": "SapBwLinkedService",
    "properties":
    {
        "type": "SapBw",
        "typeProperties":
        {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Связанная служба хранения Azure
Связанная служба связывает учетную запись хранения Azure с фабрикой данных. Для свойства type задано значение **AzureStorage** Раздел typeProperties содержит сведения о подключении для учетной записи хранения Azure.

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

### <a name="sap-bw-input-dataset"></a>Входной набор данных SAP Business Warehouse
Этот набор данных определяет набор данных SAP Business Warehouse. Для набора данных фабрики данных задается тип **RelationalTable**. В настоящее время какие-либо свойства типа для набора данных SAP Business Warehouse не указываются пользователем. Запрос в определении действия копирования указывает, какие данные следует считывать из экземпляра SAP Business Warehouse. 

Если для свойства external задать значение true, то фабрика данных воспримет эту таблицу как внешнюю, которая создана не в результате какого-либо действия в фабрике данных.

Свойства frequency и interval определяют расписание. В этом случае данные считываются из экземпляра SAP Business Warehouse раз в час. 

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```



### <a name="azure-blob-output-dataset"></a>Выходной набор данных BLOB-объекта Azure
Этот набор данных определяет выходной набор данных больших двоичных объектов Azure. Для свойства type задано значение AzureBlob. В разделе typeProperties указывается, где сохраняются данные, скопированные из экземпляра SAP Business Warehouse. Данные записываются в новый большой двоичный объект раз в час (frequency: hour, interval: 1). Путь к папке BLOB-объекта вычисляется динамически на основе времени начала обрабатываемого среза. В пути к папке используется год, месяц, день и час времени начала.

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sapbw/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="pipeline-with-copy-activity"></a>Конвейер с действием копирования
Конвейер содержит действие копирования, которое использует входной и выходной наборы данных и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **RelationalSource** (для источника SAP Business Warehouse), а для типа **sink** — значение **BlobSink**. Запрос, указанный для свойства **query**, выбирает для копирования данные за последний час.

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<MDX query for SAP BW>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapBwDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDataSet"
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
                "name": "SapBwToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```


## <a name="sap-bw-linked-service"></a>Связанная служба SAP Business Warehouse
В таблице ниже приведено описание элементов JSON, которые относятся к связанной службе SAP Business Warehouse.

Свойство | Описание | Допустимые значения | Обязательно
-------- | ----------- | -------------- | --------
server | Имя сервера, на котором размещен экземпляр SAP Business Warehouse. | string | Да
systemNumber | Номер системы SAP Business Warehouse. | Двузначное десятичное число, представленное в виде строки. | Да
clientid | Идентификатор клиента в системе SAP Business Warehouse. | Трехзначное десятичное число, представленное в виде строки. | Да
Имя пользователя | Имя пользователя, имеющего доступ к серверу SAP. | строка | Да
пароль | Пароль для пользователя | string | Да
gatewayName | Имя шлюза, который следует использовать службе фабрики данных для подключения к локальному экземпляру SAP Business Warehouse. | строка | Да
encryptedCredential | Строка зашифрованных учетных данных. | string | Нет

## <a name="sap-bw-dataset"></a>Набор данных SAP Business Warehouse.
Полный список разделов и свойств, используемых для определения наборов данных, см. в статье [Наборы данных](data-factory-create-datasets.md). Разделы структуры, доступности и политики JSON набора данных одинаковы для всех типов наборов данных (SQL Azure, большие двоичные объекты Azure, таблицы Azure и т. д.).

Раздел **typeProperties** во всех типах наборов данных разный. В нем содержатся сведения о расположении данных в хранилище данных. Для набора данных SAP Business Warehouse типа **RelationalTable** не поддерживаются какие-либо свойства типа. 


## <a name="sap-bw-source-in-copy-activity"></a>Источник SAP Business Warehouse в действии копирования
Полный список разделов и свойств, используемых для определения действий, см. в статье [Создание конвейеров](data-factory-create-pipelines.md). Свойства (такие как имя, описание, входные и выходные таблицы, политики и т. д.) доступны для всех типов действий.

В свою очередь свойства, доступные в разделе **typeProperties** действия, зависят от конкретного типа действия. Для действия копирования они различаются в зависимости от типов источников и приемников.

Если источник действия копирования относится к типу **RelationalSource** (к которому относится SAP Business Warehouse), в разделе typeProperties доступны следующие свойства.

| Свойство | Описание | Допустимые значения | Обязательно |
| --- | --- | --- | --- |
| query | Указывает запрос многомерных выражений для чтения данных из экземпляра SAP Business Warehouse. | Запрос многомерных выражений. | Да |

### <a name="type-mapping-for-sap-bw"></a>Сопоставление типов для SAP Business Warehouse
Как упоминалось в статье о [действиях перемещения данных](data-factory-data-movement-activities.md), во время копирования типы источников автоматически преобразовываются в типы приемников. Такое преобразование выполняется в два этапа:

1. Преобразование собственных типов источников в тип .NET.
2. Преобразование типа .NET в собственный тип приемника.

При перемещении данных из SAP Business Warehouse для преобразования типов SAP Business Warehouse в типы .NET используются следующие сопоставления.

Тип данных в словаре ABAP | Тип данных .NET
-------------------------------- | --------------
ACCP |    int
CHAR | Строка
CLNT | Строка
CURR | Decimal
CUKY | Строка
DEC | Decimal
FLTP | Double
INT1 | Byte
INT2 | Int16
INT4 | int
LANG | Строка
LCHR | string
LRAW | Byte[]
PREC | Int16
QUAN | Decimal
RAW | Byte[]
RAWSTRING | Byte[]
STRING | string
ЕДИНИЦА ИЗМЕРЕНИЯ | Строка
DATS | Строка
NUMC | Строка
TIMS | Строка

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]
[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]
[!INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Производительность и настройка
Ознакомьтесь со статьей [Руководство по настройке производительности действия копирования](data-factory-copy-activity-performance.md), в которой описываются ключевые факторы, влияющие на производительность перемещения данных (действие копирования) в фабрике данных Azure, и различные способы оптимизации этого процесса.

