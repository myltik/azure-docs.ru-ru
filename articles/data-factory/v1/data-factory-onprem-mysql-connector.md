---
title: Перемещение данных из MySQL с помощью фабрики данных Azure | Документация Майкрософт
description: Узнайте, как перемещать данные из базы данных MySQL с использованием фабрики данных Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 452f4fce-9eb5-40a0-92f8-1e98691bea4c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 685998729e9aa01f60c80735b5f2f4d278769bdb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="move-data-from-mysql-using-azure-data-factory"></a>Перемещение данных из MySQL с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Версия 1 — общедоступная](data-factory-onprem-mysql-connector.md)
> * [Версия 2 — предварительная](../connector-mysql.md)

> [!NOTE]
> Статья относится к версии 1 фабрики данных, которая является общедоступной версией. Если вы используете версию 2 службы фабрики данных, которая находится на этапе предварительной версии, см. статью [Copy data from MySQL using Azure Data Factory](../connector-mysql.md) (Копирование данных из MySQL с помощью фабрики данных Azure).


В этой статье рассказывается, как с помощью действия копирования в фабрике данных Azure перемещать данные из локальной базы данных MySQL. Этот документ является продолжением статьи о [действиях перемещения данных](data-factory-data-movement-activities.md), в которой приведены общие сведения о перемещении данных с помощью действия копирования.

Вы можете скопировать данные из локального хранилища данных MySQL в любой поддерживаемый приемник данных. Список хранилищ данных, которые поддерживаются в качестве приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Сейчас фабрика данных поддерживает только перемещение данных из локального хранилища данных MySQL в другие хранилища данных, но не наоборот. 

## <a name="prerequisites"></a>предварительным требованиям
Служба фабрики данных поддерживает подключение к локальным источникам MySQL с помощью шлюза управления данными. В статье [Перемещение данных между локальными и облачными ресурсами](data-factory-move-data-between-onprem-and-cloud.md) приведены сведения о шлюзе управления данными и пошаговые инструкции по его настройке.

Шлюз является обязательным, даже если база данных MySQL размещается на виртуальной машине (ВМ) Azure IaaS. Шлюз можно установить на той же ВМ, на которой размещается хранилище данных, или на другой ВМ. Важно, чтобы шлюз мог подключиться к базе данных.

> [!NOTE]
> Советы по устранению неполадок, связанных со шлюзом или подключением, см. в разделе [Устранение неполадок в работе шлюза](data-factory-data-management-gateway.md#troubleshooting-gateway-issues).

## <a name="supported-versions-and-installation"></a>Поддерживаемые версии и установка
Чтобы подключить шлюз управления данными к базе данных MySQL, соединитель [MySQL Connector/Net для Microsoft Windows](https://dev.mysql.com/downloads/connector/net/) (версии 6.6.5 или более поздней) необходимо установить на тот же компьютер, что и шлюз управления данными. Это 32-разрядная версия драйвера совместима с 64-разрядной версией шлюза управления данными. Поддерживается MySQL версии 5.1 и более.

> [!TIP]
> Если произошла ошибка "Аутентификация не пройдена из-за закрытия транспортного потока удаленной стороной", то рекомендуется обновить соединитель MySQL Connector/Net до более поздней версии.

## <a name="getting-started"></a>Приступая к работе
Вы можете создать конвейер с действием копирования, которое перемещает данные из локального хранилища данных Cassandra, с помощью разных инструментов и интерфейсов API. 

- Проще всего создать конвейер с помощью **мастера копирования**. В статье [Руководство. Создание конвейера с действием копирования с помощью мастера копирования фабрики данных](data-factory-copy-data-wizard-tutorial.md) приведены краткие пошаговые указания по созданию конвейера с помощью мастера копирования данных. 
- Также для создания конвейера можно использовать следующие инструменты: **портал Azure**, **Visual Studio**, **Azure PowerShell**, **шаблон Azure Resource Manager**, **API .NET** и **REST API**. Пошаговые инструкции по созданию конвейера с действием копирования см. в [руководстве по действию копирования](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

Независимо от используемого средства или API-интерфейса, для создания конвейера, который перемещает данные из источника данных в приемник, выполняются следующие шаги:

1. Создайте **связанные службы**, чтобы связать входные и выходные данные с фабрикой данных.
2. Создайте **наборы данных**, которые представляют входные и выходные данные для операции копирования. 
3. Создайте **конвейер** с действием копирования, который принимает входной набор данных и возвращает выходной набор данных. 

Если вы используете мастер, то он автоматически создает определения JSON для сущностей фабрики данных (связанных служб, наборов данных и конвейера). При использовании инструментов и интерфейсов API (за исключением API .NET) вы самостоятельно определяете эти сущности фабрики данных в формате JSON.  Пример определения JSON для сущностей фабрики данных, которые используются для копирования данных из локального хранилища данных MySQL, вы найдете в разделе [Пример JSON. Копирование данных из MySQL в большой двоичный объект Azure](#json-example-copy-data-from-mysql-to-azure-blob) далее в этой статье. 

Следующие разделы содержат сведения о свойствах JSON, которые используются для определения сущностей фабрики данных, характерных для хранилища данных MySQL.

## <a name="linked-service-properties"></a>Свойства связанной службы
В следующей таблице содержится описание элементов JSON, которые относятся к связанной службе MySQL.

| Свойство | ОПИСАНИЕ | Обязательно |
| --- | --- | --- |
| Тип |Для свойства type необходимо задать значение **OnPremisesMySql** |Yes |
| server |Имя сервера MySQL. |Yes |
| database |Имя базы данных MySQL. |Yes |
| schema |Имя схемы в базе данных. |Нет  |
| authenticationType |Тип проверки подлинности, используемый для подключения к базе данных MySQL. Возможные значения: `Basic`. |Yes |
| Имя пользователя |Укажите имя пользователя для подключения к базе данных MySQL. |Yes |
| password |Введите пароль для указанной вами учетной записи пользователя. |Yes |
| gatewayName |Имя шлюза, который следует использовать службе фабрики данных для подключения к локальной базе данных MySQL. |Yes |

## <a name="dataset-properties"></a>Свойства набора данных
Полный список разделов и свойств, используемых для определения наборов данных, см. в статье [Наборы данных](data-factory-create-datasets.md). Разделы структуры, доступности и политики JSON набора данных одинаковы для всех типов наборов данных (SQL Azure, большие двоичные объекты Azure, таблицы Azure и т. д.).

Раздел **typeProperties** во всех типах наборов данных разный. В нем содержатся сведения о расположении данных в хранилище данных. Раздел typeProperties набора данных с типом **RelationalTable** (который включает набор данных MySQL) содержит следующие свойства.

| Свойство | ОПИСАНИЕ | Обязательно |
| --- | --- | --- |
| tableName |Имя таблицы в экземпляре базы данных MySQL, на которое ссылается связанная служба. |Нет (если для свойства **RelationalSource** задано значение **query**). |

## <a name="copy-activity-properties"></a>Свойства действия копирования
Полный список разделов и свойств, используемых для определения действий, см. в статье [Создание конвейеров](data-factory-create-pipelines.md). Свойства (такие как имя, описание, входные и выходные таблицы, политики и т. д.) доступны для всех типов действий.

В свою очередь свойства, доступные в разделе **typeProperties** действия, зависят от конкретного типа действия. Для действия копирования они различаются в зависимости от типов источников и приемников.

Если источник действия копирования относится к типу **RelationalSource** (который содержит MySQL), то в разделе typeProperties доступны следующие свойства:

| Свойство | ОПИСАНИЕ | Допустимые значения | Обязательно |
| --- | --- | --- | --- |
| query |Используйте пользовательский запрос для чтения данных. |Строка запроса SQL. Например, select * from MyTable. |Нет (если для свойства **tableName** задано значение **dataset**). |


## <a name="json-example-copy-data-from-mysql-to-azure-blob"></a>Пример JSON. Копирование данных из MySQL в большой двоичный объект Azure
Ниже приведен пример с определениями JSON, которые можно использовать для создания конвейера с помощью [портала Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) или [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). В нем показано, как скопировать данные из локальной базы данных MySQL в хранилище BLOB-объектов Azure. Тем не менее данные можно копировать в любой из указанных [здесь](data-factory-data-movement-activities.md#supported-data-stores-and-formats) приемников. Это делается с помощью действия копирования в фабрике данных Azure.

> [!IMPORTANT]
> Этот пример содержит фрагменты кода JSON. Он не включает в себя пошаговые инструкции по созданию фабрики данных. Эти инструкции приведены в статье [Перемещение данных между локальными источниками и облаком с помощью шлюза управления данными](data-factory-move-data-between-onprem-and-cloud.md) .

Образец состоит из следующих сущностей фабрики данных.

1. Связанная служба типа [OnPremisesMySql](data-factory-onprem-mysql-connector.md#linked-service-properties).
2. Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Входной [набор данных](data-factory-create-datasets.md) типа [RelationalTable](data-factory-onprem-mysql-connector.md#dataset-properties).
4. Выходной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [RelationalSource](data-factory-onprem-mysql-connector.md#copy-activity-properties) и [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

В примере данные из результата выполнения запроса к базе данных MySQL каждый час копируются в BLOB-объект. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

Сначала настройте шлюз управления данными. Инструкции приведены в статье [Перемещение данных между локальными источниками и облаком с помощью шлюза управления данными](data-factory-move-data-between-onprem-and-cloud.md) .

**Связанная служба MySQL:**

```JSON
    {
      "name": "OnPremMySqlLinkedService",
      "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
          "server": "<server name>",
          "database": "<database name>",
          "schema": "<schema name>",
          "authenticationType": "<authentication type>",
          "userName": "<user name>",
          "password": "<password>",
          "gatewayName": "<gateway>"
        }
      }
    }
```

**Связанная служба хранилища Azure**

```JSON
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

**Входной набор данных MySQL:**

В примере предполагается, что таблица MyTable уже создана в MySQL и содержит столбец с именем timestampcolumn для данных временных рядов.

Если для параметра external задать значение true, то фабрика данных воспримет эту таблицу как внешнюю, которая создана не каким-либо действием в этой фабрике данных.

```JSON
    {
        "name": "MySqlDataSet",
        "properties": {
            "published": false,
            "type": "RelationalTable",
            "linkedServiceName": "OnPremMySqlLinkedService",
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

```JSON
    {
        "name": "AzureBlobMySqlDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/mysql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

```JSON
    {
        "name": "CopyMySqlToBlob",
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
                            "name": "MySqlDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobMySqlDataSet"
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
                    "name": "MySqlToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }
```


### <a name="type-mapping-for-mysql"></a>Сопоставление типов для MySQL
Как упоминалось в статье о [действиях перемещения данных](data-factory-data-movement-activities.md), во время копирования типы источников автоматически преобразовываются в типы приемников. Такое преобразование выполняется в два этапа:

1. Преобразование собственных типов источников в тип .NET.
2. Преобразование типа .NET в собственный тип приемника.

Когда данные перемещаются в MySQL, для преобразования типа MySQL в тип .NET используются следующие сопоставления.

| Тип базы данных MySQL | Тип .NET Framework |
| --- | --- |
| bigint unsigned |Decimal |
| bigint |Int64 |
| bit |Decimal |
| blob-объект |Byte[] |
| bool |Логическое |
| char; |Строка |
| дата |DateTime |
| Datetime |DateTime |
| decimal |Decimal |
| double precision |Double |
| Double |Double |
| enum |Строка |
| float; |Single |
| int unsigned |Int64 |
| int |Int32 |
| integer unsigned |Int64 |
| целое число |Int32 |
| long varbinary |Byte[] |
| long varchar |Строка |
| longblob |Byte[] |
| longtext |Строка |
| mediumblob |Byte[] |
| mediumint unsigned |Int64 |
| mediumint |Int32 |
| mediumtext |Строка |
| numeric |Decimal |
| real; |Double |
| set |Строка |
| smallint unsigned |Int32 |
| smallint; |Int16 |
| текст |Строка |
| Twitter в режиме реального |Интервал времени |
| timestamp |DateTime |
| tinyblob |Byte[] |
| tinyint unsigned |Int16 |
| tinyint; |Int16 |
| tinytext |Строка |
| varchar. |Строка |
| year |int |

## <a name="map-source-to-sink-columns"></a>Сопоставление столбцов источника и приемника
Дополнительные сведения о сопоставлении столбцов в наборе данных, используемом в качестве источника, со столбцами в приемнике см. в [этой статье](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Повторяющиеся операции чтения из реляционных источников
При копировании данных из реляционных хранилищ важно помнить о повторяемости, чтобы избежать непредвиденных результатов. В фабрике данных Azure можно вручную повторно выполнить срез. Вы можете также настроить для набора данных политику повтора, чтобы при сбое срез выполнялся повторно. При повторном выполнении среза в любом случае необходимо убедиться в том, что считываются те же данные, независимо от того, сколько раз выполняется срез. Ознакомьтесь с разделом [Повторяющиеся операции чтения из реляционных источников](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Производительность и настройка
Ознакомьтесь со статьей [Руководство по настройке производительности действия копирования](data-factory-copy-activity-performance.md), в которой описываются ключевые факторы, влияющие на производительность перемещения данных (действие копирования) в фабрике данных Azure, и различные способы оптимизации этого процесса.
