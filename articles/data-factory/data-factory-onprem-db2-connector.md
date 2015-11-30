<properties 
	pageTitle="Перемещение данных из DB2 | Фабрика данных Azure" 
	description="Узнайте, как перемещать данные из базы данных DB2 с использованием фабрики данных Azure" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/12/2015" 
	ms.author="spelluru"/>

# Перемещение данных из DB2 с помощью фабрики данных Azure
В этой статье описано использование действия копирования в фабрике данных Azure для перемещения данных из DB2 в другое хранилище данных. Эта статья основана на статье о [действиях перемещения данных](data-factory-data-movement-activities.md), в которой приведены общие сведения о перемещении данных с помощью действия копирования и поддерживаемых сочетаниях хранилищ данных.

Фабрика данных поддерживает подключение к локальным источникам DB2 с помощью шлюза управления данными. В статье [Перемещение данных между локальными и облачными ресурсами](data-factory-move-data-between-onprem-and-cloud.md) приведены сведения о шлюзе управления данными и пошаговые инструкции по его настройке.

**Примечание.** Шлюз используется для подключения к источнику DB2, даже если он размещен на виртуальных машинах Azure IaaS. Если вы подключаетесь к экземпляру DB2, размещенному в облаке, экземпляр шлюза можно тоже установить на виртуальную машину IaaS.

Сейчас фабрика данных поддерживает перемещение данных из DB2 в другие хранилища данных, но не наоборот.

## Установка 

Чтобы подключить шлюз управления данными к базе данных DB2, необходимо установить [драйвер сервера данных IBM DB2](http://go.microsoft.com/fwlink/p/?LinkID=274911) на тот же компьютер, что и шлюз управления данными.

Компания IBM выявила ряд проблем, связанных с установкой драйвера сервера данных IBM DB2 на Windows 8. При возникновении этих проблем потребуется выполнить дополнительные действия. Дополнительные сведения об установке драйвера сервера данных IBM DB2 на Windows 8 см. по адресу [http://www-01.ibm.com/support/docview.wss?uid=swg21618434](http://www-01.ibm.com/support/docview.wss?uid=swg21618434).

## Пример копирования данных из DB2 в хранилище BLOB-объектов Azure

В примере ниже показано следующее.

1.	Связанная служба типа [OnPremisesDb2](data-factory-onprem-db2-connector.md#db2-linked-service-properties).
2.	Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties). 
3.	Входной [набор данных](data-factory-create-datasets.md) типа [RelationalTable](data-factory-onprem-db2-connector.md#db2-dataset-type-properties).
4.	Выходной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties). 
5.	[Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [RelationalSource](data-factory-onprem-db2-connector.md#db2-copy-activity-type-properties) и [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties). 

В примере данные из результата выполнения запроса к базе данных DB2 каждый час копируются в BLOB-объект. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

Сначала настройте шлюз управления данными. Указания, как это сделать, см. в статье [Перемещение данных между локальными и облачными ресурсами](data-factory-move-data-between-onprem-and-cloud.md).

**Связанная служба DB2**

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


**Связанная служба хранилища BLOB-объектов Azure**

	{
	    "name": "AzureStorageLinkedService",
	    "properties": {
	        "type": "AzureStorageLinkedService",
			"typeProperties": {
	        	"connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
			}
	    }
	}

**Входной набор данных DB2**

В примере предполагается, что таблица MyTable уже создана в DB2 и содержит столбец с именем timestamp для данных временных рядов.

Если для параметра external задать значение true и указать политику externalData, фабрика данных будет считать эту таблицу внешней по отношению к себе и не созданной в результате какого-либо действия в фабрике данных. Обратите внимание, что для параметра **type** установлено значение **RelationalTable**.

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


**Выходной набор данных BLOB-объекта Azure**

Данные записываются в новый BLOB-объект каждый час (frequency: hour, interval: 1). Путь к папке BLOB-объекта вычисляется динамически на основе времени начала обрабатываемого среза. В пути к папке используется год, месяц, день и час времени начала.

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
	                        "format": "%M"
	                    }
	                },
	                {
	                    "name": "Day",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%d"
	                    }
	                },
	                {
	                    "name": "Hour",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%H"
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

Конвейер содержит действие копирования (Copy), которое использует входной и выходной наборы данных (см. выше) и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **RelationalSource**, а для типа **sink** — значение **BlobSink**. SQL-запрос, заданный для свойства **query**, выбирает данные из таблицы Orders.

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
	                        "query": "select * from "Orders""
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


## Свойства связанной службы DB2

В следующей таблице содержится описание элементов JSON, которые относятся к связанной службе DB2.

| Свойство | Описание | Обязательно |
| -------- | ----------- | -------- | 
| type | Для свойства type необходимо задать значение **OnPremisesDB2**. | Да |
| server | Имя сервера DB2. | Да |
| database | Имя базы данных DB2. | Да |
| schema | Имя схемы в базе данных. | Нет |
| authenticationType | Тип проверки подлинности, используемый для подключения к базе данных DB2. Возможными значениями являются: анонимная, обычная и Windows. | Да |
| Имя пользователя | При использовании обычной проверки подлинности или проверки подлинности Windows укажите имя пользователя. | Нет |
| пароль | Введите пароль для учетной записи пользователя, указанной для выбранного имени пользователя. | Нет |
| gatewayName | Имя шлюза, который следует использовать службе фабрики данных для подключения к локальной базе данных DB2. | Да |

Дополнительную информацию о настройке учетных данных для локального источника данных DB2 см. в разделе [Настройка учетных данных и безопасность](data-factory-move-data-between-onprem-and-cloud.md#setting-credentials-and-security).


## Свойства типа «Набор данных DB2»

Полный список разделов и свойств, используемых для определения наборов данных, см. в статье [Создание наборов данных](data-factory-create-datasets.md). Разделы структуры, доступности и политики JSON набора данных одинаковы для всех типов наборов данных (SQL Azure, BLOB-объекты Azure, таблицы Azure и т. д.).

Раздел typeProperties во всех типах наборов данных разный. В нем содержатся сведения о расположении данных в хранилище данных. Раздел typeProperties набора данных с типом RelationalTable (который включает набор данных DB2) имеет следующие свойства.

| Свойство | Описание | Обязательно |
| -------- | ----------- | -------- | 
| tableName | Имя таблицы в экземпляре базы данных DB2, на которое ссылается связанная служба. | Да |

## Свойства типа «Действие копирования DB2»

Полный список разделов и свойств, используемых для определения действий, см. в статье [Создание конвейеров](data-factory-create-pipelines.md). Такие свойства, как имя, описание, входные и выходные таблицы, различные политики и т. д., доступны для всех типов действий.

То, какие свойства будут доступны в разделе typeProperties, зависит от типа действия, а в случае с действием копирования — еще и от типов источников и приемников.

В случае действия копирования, когда источник относится к типу **RelationalSource** (который содержит DB2), в разделе typeProperties доступны следующие свойства.


| Свойство | Описание | Допустимые значения | Обязательно |
| -------- | ----------- | -------- | -------------- |
| запрос | Используйте пользовательский запрос для чтения данных. | Строка запроса SQL. Например, select * from MyTable. | Нет |

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## Сопоставление типов для DB2
Как упоминалось в статье о [действиях перемещения данных](data-factory-data-movement-activities.md), во время копирования типы источников автоматически преобразовываются в типы приемников. Такое преобразование выполняется в 2 этапа:

1. Преобразование собственных типов источников в тип .NET.
2. Преобразование типа .NET в собственный тип приемника.

Когда данные перемещаются в DB2, для преобразования типа DB2 в тип .NET используются следующие сопоставления.

Тип базы данных DB2 | Тип .NET Framework 
----------------- | ------------------- 
SmallInt | Int16
Число | Int32
BigInt | Int64
Real | Single
Double | Double
Float | Double
Decimal | Decimal
DecimalFloat | Decimal
Числовой | Decimal
Дата | Datetime
Время | TimeSpan
Timestamp | DateTime
Xml | Byte
Char | Строка
VarChar | Строка
LongVarChar | Строка
DB2DynArray | Строка
Binary | Byte
VarBinary | Byte
LongVarBinary | Byte
Graphic | Строка
VarGraphic | Строка
LongVarGraphic | Строка
Clob | Строка
BLOB-объект | Byte
DbClob | Строка
SmallInt | Int16
Число | Int32
BigInt | Int64
Real | Single
Double | Double
Float | Double
Decimal | Decimal
DecimalFloat | Decimal
Числовой | Decimal
Дата | Datetime
Время | TimeSpan
Timestamp | DateTime
Xml | Byte
Char | Строка


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]


[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

<!---HONumber=Nov15_HO4-->