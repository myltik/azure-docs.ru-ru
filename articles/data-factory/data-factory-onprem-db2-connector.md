---
title: "Перемещение данных из DB2 с помощью фабрики данных Azure | Документация Майкрософт"
description: "Узнайте, как перемещать данные из базы данных DB2 с использованием фабрики данных Azure"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: c1644e17-4560-46bb-bf3c-b923126671f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 0d9afb1554158a4d88b7f161c62fa51c1bf61a7d
ms.openlocfilehash: 6d54203797ad970d590b853b171b383708dbcb5d
ms.lasthandoff: 04/12/2017


---
# <a name="move-data-from-db2-using-azure-data-factory"></a>Перемещение данных из DB2 с помощью фабрики данных Azure
В этой статье описывается, как скопировать данные из локальной базы данных DB2 в любое хранилище данных, указанное в разделе [Поддерживаемые хранилища данных и форматы](data-factory-data-movement-activities.md#supported-data-stores-and-formats) (см. столбец приемников), с использованием действия копирования в фабрике данных Azure. В этой статье мы продолжим тему о [действиях перемещения данных](data-factory-data-movement-activities.md) , в которой приведены общие сведения о перемещении данных с помощью действия копирования и поддерживаемых сочетаниях хранилищ данных.

Сейчас фабрика данных поддерживает только перемещение данных из базы данных DB2 в другие [поддерживаемые хранилища-приемники](data-factory-data-movement-activities.md#supported-data-stores-and-formats), но не наоборот.

## <a name="prerequisites"></a>Предварительные требования
Фабрика данных поддерживает подключение к локальной базе данных DB2 с помощью шлюза управления данными. Сведения о шлюзе управления данными см. в статье [Шлюз управления данными](data-factory-data-management-gateway.md), а пошаговые инструкции по настройке шлюза для перемещения данных с использованием конвейера — в статье [Перемещение данных между локальными источниками и облаком с помощью шлюза управления данными](data-factory-move-data-between-onprem-and-cloud.md).

Шлюз является обязательным, даже если база данных DB2 размещается на виртуальной машине Azure IaaS. Шлюз можно установить на той же ВМ IaaS, на которой размещается хранилище данных, или на другой ВМ. Важно, чтобы шлюз мог подключиться к базе данных.

Шлюз управления данными предоставляет встроенный драйвер DB2, поэтому при копировании данных из DB2 вам не потребуется устанавливать драйвер вручную.

> [!NOTE]
> Советы по устранению неполадок, связанных со шлюзом или подключением, см. в разделе [Устранение неполадок в работе шлюза](data-factory-data-management-gateway.md#troubleshooting-gateway-issues).

## <a name="supported-versions"></a>Поддерживаемые версии
Этот соединитель DB2 поддерживает приведенные ниже платформы и версии IBM DB2 с архитектурой распределенной реляционной базы данных (DRDA) SQL Access Manager (SQLAM) версии 9, 10 и 11:

* IBM DB2 для z/OS версии 11.1
* IBM DB2 для z/OS версии 10.1
* IBM DB2 для i версии 7.2
* IBM DB2 для i версии 7.1
* IBM DB2 для LUW версии 11
* IBM DB2 для LUW версии 10.5
* IBM DB2 для LUW версии 10.1

> [!TIP]
> Если произошла ошибка "Пакет, соответствующий запросу на выполнение инструкции SQL, не найден. SQLSTATE=51002 SQLCODE=-805", используйте учетную запись с высоким уровнем привилегий (опытный пользователь или администратор) для однократного запуска действия копирования. После этого необходимый пакет будет автоматически создаваться при копировании. Для выполнения последующих действий копирования можно переключиться на обычную учетную запись.

## <a name="getting-started"></a>Приступая к работе
Вы можете создать конвейер с действием копирования, которое перемещает данные из локального хранилища данных DB2, с помощью различных средств и API-интерфейсов. 

- Проще всего создать конвейер с помощью **мастера копирования**. В статье [Руководство. Создание конвейера с действием копирования с помощью мастера копирования фабрики данных](data-factory-copy-data-wizard-tutorial.md) приведены краткие пошаговые указания по созданию конвейера с помощью мастера копирования данных. 
- Также для создания конвейера можно использовать следующие инструменты: **портал Azure**, **Visual Studio**, **Azure PowerShell**, **шаблон Azure Resource Manager**, **API .NET** и **REST API**. Пошаговые инструкции по созданию конвейера с действием копирования см. в [руководстве по действию копирования](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

Независимо от используемого средства или API-интерфейса, для создания конвейера, который перемещает данные из источника данных в приемник, выполняются следующие шаги:

1. Создайте **связанные службы**, чтобы связать входные и выходные данные с фабрикой данных.
2. Создайте **наборы данных**, которые представляют входные и выходные данные для операции копирования. 
3. Создайте **конвейер** с действием копирования, который принимает входной набор данных и возвращает выходной набор данных. 

Если вы используете мастер, то он автоматически создает определения JSON для сущностей фабрики данных (связанных служб, наборов данных и конвейера). При использовании средств и API-интерфейсов (за исключением .NET API) эти сущности фабрики данных определяются в формате JSON.  Пример определений JSON для сущностей фабрики данных, которые используются для копирования данных из локального хранилища данных DB2, вы найдете в разделе [Пример JSON. Копирование данных из DB2 в хранилище BLOB-объектов Azure](#json-example-copy-data-from-db2-to-azure-blob) этой статьи. 

Следующие разделы содержат сведения о свойствах JSON, которые используются для определения сущностей фабрики данных, характерных для хранилища данных DB2.

## <a name="linked-service-properties"></a>Свойства связанной службы
В следующей таблице содержится описание элементов JSON, которые относятся к связанной службе DB2.

| Свойство | Описание | Обязательно |
| --- | --- | --- |
| type |Для свойства type необходимо задать значение **OnPremisesDB2** |Да |
| server |Имя сервера DB2. |Да |
| database |Имя базы данных DB2. |Да |
| schema |Имя схемы в базе данных. Имя схемы чувствительно к регистру. |Нет |
| authenticationType |Тип проверки подлинности, используемый для подключения к базе данных DB2. Возможными значениями являются: анонимная, обычная и Windows. |Да |
| Имя пользователя |При использовании обычной проверки подлинности или проверки подлинности Windows укажите имя пользователя. |Нет |
| пароль |Введите пароль для учетной записи пользователя, указанной для выбранного имени пользователя. |Нет |
| gatewayName |Имя шлюза, который следует использовать службе фабрики данных для подключения к локальной базе данных DB2. |Да |


## <a name="dataset-properties"></a>Свойства набора данных
Полный список разделов и свойств, используемых для определения наборов данных, см. в статье [Наборы данных](data-factory-create-datasets.md). Разделы структуры, доступности и политики JSON набора данных одинаковы для всех типов наборов данных (SQL Azure, большие двоичные объекты Azure, таблицы Azure и т. д.).

Раздел typeProperties во всех типах наборов данных разный. В нем содержатся сведения о расположении данных в хранилище данных. Раздел typeProperties набора данных с типом RelationalTable (который включает набор данных DB2) имеет следующие свойства.

| Свойство | Описание | Обязательно |
| --- | --- | --- |
| tableName |Имя таблицы в экземпляре базы данных DB2, на которое ссылается связанная служба. Свойство tableName чувствительно к регистру. |Нет (если для свойства **RelationalSource** задано значение **query**). |

## <a name="copy-activity-properties"></a>Свойства действия копирования
Полный список разделов и свойств, используемых для определения действий, см. в статье [Создание конвейеров](data-factory-create-pipelines.md). Свойства (такие как имя, описание, входные и выходные таблицы, политики и т. д.) доступны для всех типов действий.

В свою очередь свойства, доступные в разделе typeProperties действия, зависят от конкретного типа действия. Для действия копирования они различаются в зависимости от типов источников и приемников.

Для действия копирования, когда источник относится к типу **RelationalSource** (который содержит DB2), в разделе typeProperties доступны следующие свойства.

| Свойство | Описание | Допустимые значения | Обязательно |
| --- | --- | --- | --- |
| query |Используйте пользовательский запрос для чтения данных. |Строка запроса SQL. Например, `"query": "select * from "MySchema"."MyTable""`. |Нет (если для свойства **tableName** задано значение **dataset**). |

> [!NOTE]
> В именах схем и таблиц учитывается регистр. Заключите имя в двойные кавычки ("") в запросе.  

**Пример**

```sql
"query": "select * from "DB2ADMIN"."Customers""
```


## <a name="json-example-copy-data-from-db2-to-azure-blob"></a>Пример JSON. Копирование данных из DB2 в хранилище BLOB-объектов Azure
Ниже приведены примеры с определениями JSON, которые можно использовать для создания конвейера с помощью [портала Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) или [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Вы узнаете, как копировать данные из базы данных DB2 и хранилища BLOB-объектов Azure. Тем не менее данные можно копировать в любой из указанных [здесь](data-factory-data-movement-activities.md#supported-data-stores-and-formats) приемников. Это делается с помощью действия копирования в фабрике данных Azure.

Образец состоит из следующих сущностей фабрики данных.

1. Связанная служба типа [OnPremisesDb2](data-factory-onprem-db2-connector.md#linked-service-properties).
2. Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Входной [набор данных](data-factory-create-datasets.md) типа [RelationalTable](data-factory-onprem-db2-connector.md#dataset-properties).
4. Выходной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [RelationalSource](data-factory-onprem-db2-connector.md#copy-activity-properties) и [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

В примере данные из результата запроса в базе данных DB2 каждый час копируются в BLOB-объект Azure. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

Установите и настройте шлюз управления данными. Инструкции см. в статье [Перемещение данных между локальными источниками и облаком с помощью шлюза управления данными](data-factory-move-data-between-onprem-and-cloud.md).

**Связанная служба DB2**

```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

**Связанная служба хранилища BLOB-объектов Azure**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorageLinkedService",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```

**Входной набор данных DB2**

В примере предполагается, что таблица MyTable уже создана в DB2 и содержит столбец с именем timestamp для данных временных рядов.

Если параметру external присвоить значение true, фабрика данных воспримет этот набор данных как внешний и созданный не в результате какого-либо действия в этой службе. Обратите внимание, что параметру **type** присвоено значение **RelationalTable**.

```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
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

Данные записываются в новый BLOB-объект каждый час (frequency: hour, interval: 1). Путь к папке BLOB-объекта вычисляется динамически на основе времени начала обрабатываемого среза. В пути к папке используется год, месяц, день и час времени начала.

```json
{
    "name": "AzureBlobDb2DataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

Конвейер содержит действие копирования, которое использует входной и выходной наборы данных и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **RelationalSource**, а для типа **sink** — значение **BlobSink**. SQL-запрос, заданный для свойства **query** , выбирает данные из таблицы Orders.

```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"Orders\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "Db2DataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDb2DataSet"
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
                "name": "Db2ToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```


## <a name="type-mapping-for-db2"></a>Сопоставление типов для DB2
Как упоминалось в статье о [действиях перемещения данных](data-factory-data-movement-activities.md), во время копирования типы источников автоматически преобразовываются в типы приемников. Такое преобразование выполняется в 2 этапа:

1. Преобразование собственных типов источников в тип .NET.
2. Преобразование типа .NET в собственный тип приемника.

Когда данные перемещаются в DB2, между типом DB2 и типом .NET используются следующие сопоставления.

| Тип базы данных DB2 | Тип .NET Framework |
| --- | --- |
| SmallInt |Int16 |
| Число |Int32 |
| BigInt |Int64 |
| Real |Single |
| Double |Double |
| Float |Double |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Числовой |Decimal |
| Дата |Datetime |
| Время |TimeSpan |
| Timestamp |Datetime |
| xml |Byte[] |
| Char |Строка |
| VarChar |Строка |
| LongVarChar |Строка |
| DB2DynArray |Строка |
| Binary |Byte[] |
| VarBinary |Byte[] |
| LongVarBinary |Byte[] |
| Graphic |Строка |
| VarGraphic |Строка |
| LongVarGraphic |Строка |
| Clob |Строка |
| BLOB-объект |Byte[] |
| DbClob |Строка |
| SmallInt |Int16 |
| Число |Int32 |
| BigInt |Int64 |
| Real |Single |
| Double |Double |
| Float |Double |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Числовой |Decimal |
| Дата |Datetime |
| Время |TimeSpan |
| Timestamp |Datetime |
| xml |Byte[] |
| Char |Строка |

## <a name="map-source-to-sink-columns"></a>Сопоставление столбцов источника и приемника
Дополнительные сведения о сопоставлении столбцов в наборе данных, используемом в качестве источника, со столбцами в приемнике см. в [этой статье](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Повторяющиеся операции чтения из реляционных источников
При копировании данных из реляционных хранилищ важно помнить о повторяемости, чтобы избежать непредвиденных результатов. В фабрике данных Azure можно вручную повторно выполнить срез. Вы можете также настроить для набора данных политику повтора, чтобы при сбое срез выполнялся повторно. При повторном выполнении среза в любом случае необходимо убедиться в том, что считываются те же данные, независимо от того, сколько раз выполняется срез. Ознакомьтесь с разделом [Повторяющиеся операции чтения из реляционных источников](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Производительность и настройка
Ознакомьтесь со статьей [Руководство по настройке производительности действия копирования](data-factory-copy-activity-performance.md), в которой описываются ключевые факторы, влияющие на производительность перемещения данных (действие копирования) в фабрике данных Azure, и различные способы оптимизации этого процесса.

