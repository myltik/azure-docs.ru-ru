<properties 
	pageTitle="Соединитель PostgreSQL: перемещение данных из PostgreSQL" 
	description="Сведения о соединителе PostgreSQL для службы фабрики данных, с помощью которого можно перемещать данные из базы данных PostgreSQL." 
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
	ms.date="07/29/2015" 
	ms.author="spelluru"/>

# Соединитель PostgreSQL: перемещение данных из PostgreSQL с помощью фабрики данных Azure

В этой статье описано использование действия копирования в фабрике данных Azure для перемещения данных из PostgreSQL в другое хранилище данных. Эта статья основана на статье о [действиях перемещения данных](data-factory-data-movement-activities.md), в которой приведены общие сведения о перемещении данных с помощью действия копирования и поддерживаемых сочетаниях хранилищ данных.

Служба фабрики данных поддерживает подключение к локальным источникам PostgreSQL с помощью шлюза управления данными. В статье [Перемещение данных между локальными и облачными ресурсами](data-factory-move-data-between-onprem-and-cloud.md) приведены сведения о шлюзе управления данными и пошаговые инструкции по его настройке.

**Примечание.** Необходимо использовать шлюз для подключения к PostgreSQL, даже если он размещается на виртуальных машинах Azure IaaS. Если вы подключаетесь к экземпляру PostgreSQL, размещенному в облаке, экземпляр шлюза можно тоже установить на виртуальную машину IaaS.

Фабрика данных поддерживает только перемещение данных из PostgreSQL в другие хранилища данных, а не наоборот.

## Установка 

Для подключения шлюза управления данными к базе данных PostgreSQL необходимо установить [Поставщик данных Ngpsql для PostgreSQL](http://go.microsoft.com/fwlink/?linkid=282716) в одной системе со шлюзом управления данными.

## Пример копирования данных из PostgreSQL в BLOB-объект Azure

В примере ниже показано следующее.

1.	Связанная служба типа OnPremisesPostgreSql.
2.	Связанная служба типа AzureStorage.
3.	Входной набор данных типа RelationalTable.
4.	Выходной набор данных типа BlobSink.
4.	Конвейер с действием копирования, которое использует RelationalSource и BlobSink. 

В примере данные из результата выполнения запроса к базе данных PostgreSQL каждый час копируются в BLOB-объект. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

Сначала настройте шлюз управления данными согласно инструкциям в статье [Перемещение данных между локальными и облачными ресурсами](data-factory-move-data-between-onprem-and-cloud.md).

**Связанная служба PostgreSQL**

	{
	    "name": "OnPremPostgreSqlLinkedService",
	    "properties": {
	        "type": "OnPremisesPostgreSql",
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
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
	    }
	  }
	}

**Входной набор данных PostgreSQL**

В примере предполагается, что вы уже создали таблицу MyTable в PostgreSQL и она содержит столбец с именем timestamp для данных временных рядов.

Если для параметра external задать значение true и указать политику externalData, фабрика данных будет считать эту таблицу внешней по отношению к себе и не созданной в результате какого-либо действия в фабрике данных.

	{
	    "name": "PostgreSqlDataSet",
	    "properties": {
	        "type": "RelationalTable",
	        "linkedServiceName": "OnPremPostgreSqlLinkedService",
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

Данные записываются в новый BLOB-объект каждый час (frequency: hour, interval: 1). Путь к папке с BLOB-объектом и имя файла вычисляются динамически на основе времени начала обрабатываемого среза. В пути к папке используется год, месяц, день и час времени начала.

	{
	    "name": "AzureBlobPostgreSqlDataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/postgresql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


**Действие копирования**

Конвейер содержит действие копирования (Copy), которое использует входной и выходной наборы данных (см. выше) и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **RelationalSource**, а для типа **sink** — значение **BlobSink**. SQL-запрос, заданный для свойства **query**, выбирает данные из таблицы public.usstates в базе данных PostgreSQL.
	
	{
	    "name": "CopyPostgreSqlToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "RelationalSource",
	                        "query": "select * from public.usstates"
	                    },
	                    "sink": {
	                        "type": "BlobSink"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "PostgreSqlDataSet"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobPostgreSqlDataSet"
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
	                "name": "PostgreSqlToBlob"
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z"
	    }
	}


## Свойства связанной службы PostgreSQL

В следующей таблице содержится описание элементов JSON, которые относятся к связанной службе PostgreSQL.

Свойство | Описание | Обязательно
-------- | ----------- | --------
type | Для свойства type необходимо задать значение **OnPremisesPostgreSql**. | Да
server | Имя сервера, PostgreSQL. | Да 
database | Имя базы данных PostgreSQL. | Да 
schema | Имя схемы в базе данных. | Нет 
authenticationType | Тип проверки подлинности, используемый для подключения к базе данных PostgreSQL. Возможными значениями являются: анонимная, обычная и Windows. | Да 
username | При использовании обычной проверки подлинности или проверки подлинности Windows укажите имя пользователя. | Нет 
password | Введите пароль для учетной записи пользователя, указанной для выбранного имени пользователя. | Нет 
gatewayName | Имя шлюза, который следует использовать службе фабрики данных для подключения к локальной базе данных PostgreSQL. | Да 

## Свойства типа «Набор данных PostgreSQL»

Полный список разделов и свойств, используемых для определения наборов данных, см. в статье [Создание наборов данных](data-factory-create-datasets.md). Разделы структуры, доступности и политики JSON набора данных одинаковы для всех типов наборов данных (SQL Azure, BLOB-объекты Azure, таблицы Azure и т. д.).

Раздел typeProperties во всех типах наборов данных разный. В нем содержатся сведения о расположении данных в хранилище данных. Раздел typeProperties набора данных с типом **RelationalTable** (который включает набор данных PostgreSQL) имеет приведенные ниже свойства.

Свойство | Описание | Обязательно
-------- | ----------- | --------
tableName | Имя таблицы в экземпляре базы данных PostgreSQL, на которое ссылается связанная служба. | Да 

## Свойства типа «Действие копирования PostgreSQL»

Полный список разделов и свойств, используемых для определения действий, см. в статье [Создание конвейеров](data-factory-create-pipelines.md). Такие свойства, как имя, описание, входные и выходные таблицы, различные политики и т. д., доступны для всех типов действий.

То, какие свойства будут доступны в разделе typeProperties, зависит от типа действия, а в случае с действием копирования — еще и от типов источников и приемников.

В случае действия копирования, когда источник относится к типу **RelationalSource** (который содержит PostgreSQL), в разделе typeProperties доступны следующие свойства.

Свойство | Описание | Допустимые значения | Обязательно
-------- | ----------- | -------------- | --------
запрос | Используйте пользовательский запрос для чтения данных. | Строка запроса SQL. Например, select * from MyTable. | Нет

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## Сопоставление типов для PostgreSQL

Как упоминалось в статье о [действиях перемещения данных](data-factory-data-movement-activities.md), во время копирования типы источников автоматически преобразовываются в типы приемников. Такое преобразование выполняется в два этапа:

1. Преобразование собственных типов источников в тип .NET.
1. Преобразование типа .NET в собственный тип приемника.

При перемещении данных в PostgreSQL будут использоваться следующие сопоставления из типов PostgreSQL в типы .NET.

Тип базы данных PostgreSQL |	Псевдонимы PostgresSQL | Тип .NET Framework
------------------------ | -------------------- | ---------------------
abstime | | Datetime
bigint | int8 | Int64
bigserial | serial8 | Int64
bit [ (n) ] | | Byte, String
bit varying [ (n) ] | varbit | Byte, String
Логическое | bool | Логический
box | | Byte, String
bytea | | Byte, String
character [ (n) ] | char [ (n) ] | Строка
character varying [ (n) ] | varchar [ (n) ] | Строка
cid | | Строка
cidr | | Строка
circle | | Byte, String
дата | | Datetime
daterange | | Строка
double precision| float8 | Double
inet | | Byte, String
intarry | | Строка
int4range | | Строка
int8range | | Строка
целое число | int, int4 | Int32
interval [ fields ] [ (p) ] | | Timespan
json | | Строка
jsonb | | Byte
line | | Byte, String
lseg | | Byte, String
macaddr | | Byte, String
money; | | Decimal
numeric [ (p, s) ] | decimal [ (p, s) ] | Decimal
numrange | | Строка
oid | | Int32
path | | Byte, String
pg\_lsn | | Int64
point | | Byte, String
polygon | | Byte, String
real; | float4 | Single
smallint; | int2 | Int16
smallserial | serial2 | Int16
serial | serial4 | Int32
text | | Строка


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

<!---HONumber=August15_HO6-->