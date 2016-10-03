<properties 
	pageTitle="Перемещение данных из базы данных MySQL | Фабрика данных Azure" 
	description="Узнайте, как перемещать данные из базы данных MySQL с использованием фабрики данных Azure." 
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
	ms.date="09/20/2016" 
	ms.author="spelluru"/>

# Перемещение данных из MySQL с помощью фабрики данных Azure

В этой статье описано использование действия копирования в фабрике данных Azure для перемещения данных из MySQL в другое хранилище данных. В этой статье мы продолжим тему о [действиях перемещения данных](data-factory-data-movement-activities.md), в которой приведены общие сведения о перемещении данных с помощью действия копирования и поддерживаемых сочетаниях хранилищ данных.

Служба фабрики данных поддерживает подключение к локальным источникам MySQL с помощью шлюза управления данными. В статье [Перемещение данных между локальными и облачными ресурсами](data-factory-move-data-between-onprem-and-cloud.md) приведены сведения о шлюзе управления данными и пошаговые инструкции по его настройке.

> [AZURE.NOTE] Шлюз является обязательным, даже если база данных MySQL размещается на виртуальной машине (ВМ) Azure IaaS. Шлюз можно установить на той же ВМ, на которой размещается хранилище данных, или на другой ВМ. Важно, чтобы шлюз мог подключиться к базе данных.

Сейчас фабрика данных поддерживает перемещение данных из MySQL в другие хранилища данных, но не наоборот.

## Установка 
Чтобы подключить шлюз управления данными к базе данных MySQL, [соединитель MySQL /Net 6.6.5 для Microsoft Windows](http://go.microsoft.com/fwlink/?LinkId=278885) нужно установить на тот же компьютер, что и шлюз управления данными.

> [AZURE.NOTE] Советы по устранению неполадок, связанных со шлюзом или подключением, см. в разделе [Устранение неполадок в работе шлюза](data-factory-data-management-gateway.md#troubleshoot-gateway-issues).

## Мастер копирования данных
Самый простой способ создать конвейер, копирующий данные из базы данных MySQL в любое поддерживаемое хранилище-приемник, — использовать мастер копирования данных. В статье [Руководство. Создание конвейера с действием копирования с помощью мастера копирования фабрики данных](data-factory-copy-data-wizard-tutorial.md) приведены краткие пошаговые указания по созданию конвейера с помощью мастера копирования данных.

Ниже приведены примеры с определениями JSON, которые можно использовать для создания конвейера. Эти инструкции приведены в статье [Перемещение данных между локальными источниками и облаком с помощью шлюза управления данными](data-factory-move-data-between-onprem-and-cloud.md). Данные можно копировать в любой из указанных [здесь](data-factory-data-movement-activities.md#supported-data-stores) приемников. Это делается с помощью действия копирования в фабрике данных Azure.

## Пример копирования данных из MySQL в хранилище BLOB-объектов Azure
В этом примере показано, как скопировать данные из локальной базы данных MySQL в хранилище BLOB-объектов Azure. Однако данные можно **напрямую** копировать в любой из указанных [здесь](data-factory-data-movement-activities.md#supported-data-stores) приемников. Это делается с помощью действия копирования в фабрике данных Azure.

> [AZURE.IMPORTANT] Этот пример содержит фрагменты кода JSON. Он не включает в себя пошаговые инструкции по созданию фабрики данных. Эти инструкции приведены в статье [Перемещение данных между локальными источниками и облаком с помощью шлюза управления данными](data-factory-move-data-between-onprem-and-cloud.md).
 
Образец состоит из следующих сущностей фабрики данных.

1.	Связанная служба типа [OnPremisesMySql](data-factory-onprem-mysql-connector.md#mysql-linked-service-properties).
2.	Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Входной [набор данных](data-factory-create-datasets.md) типа [RelationalTable](data-factory-onprem-mysql-connector.md#mysql-dataset-type-properties).
4.	Выходной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	[Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [RelationalSource](data-factory-onprem-mysql-connector.md#mysql-copy-activity-type-properties) и [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

В примере данные из результата выполнения запроса к базе данных MySQL каждый час копируются в BLOB-объект. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

Сначала настройте шлюз управления данными. Инструкции приведены в статье [Перемещение данных между локальными источниками и облаком с помощью шлюза управления данными](data-factory-move-data-between-onprem-and-cloud.md).

**Связанная служба MySQL**

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

**Входной набор данных MySQL**

В примере предполагается, что таблица MyTable уже создана в MySQL и содержит столбец с именем timestampcolumn для данных временных рядов.

Если для параметра external задать значение true, то фабрика данных воспримет эту таблицу как внешнюю, которая создана не каким-либо действием в этой фабрике данных.
	
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



**Выходной набор данных BLOB-объекта Azure**

Данные записываются в новый BLOB-объект каждый час (frequency: hour, interval: 1). Путь к папке BLOB-объекта вычисляется динамически на основе времени начала обрабатываемого среза. В пути к папке используется год, месяц, день и час времени начала.

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



**Конвейер с действием копирования**

Конвейер содержит действие копирования, которое использует входной и выходной наборы данных и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **RelationalSource**, а для типа **sink** — значение **BlobSink**. SQL-запрос, указанный для свойства **query**, выбирает для копирования данные за последний час.
	
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



## Свойства связанной службы MySQL

В следующей таблице содержится описание элементов JSON, которые относятся к связанной службе MySQL.

| Свойство | Описание | Обязательно |
| -------- | ----------- | -------- | 
| type | Для свойства type необходимо задать значение **OnPremisesMySql**. | Да |
| server | Имя сервера MySQL. | Да |
| database | Имя базы данных MySQL. | Да | 
| schema | Имя схемы в базе данных. | Нет | 
| authenticationType | Тип проверки подлинности, используемый для подключения к базе данных MySQL. Возможными значениями являются: анонимная, обычная и Windows. | Да | 
| Имя пользователя | При использовании обычной проверки подлинности или проверки подлинности Windows укажите имя пользователя. | Нет | 
| пароль | Введите пароль для учетной записи пользователя, указанной для выбранного имени пользователя. | Нет | 
| gatewayName | Имя шлюза, который следует использовать службе фабрики данных для подключения к локальной базе данных MySQL. | Да |

Дополнительную информацию о настройке учетных данных для локального источника данных MySQL см. в разделе [Настройка учетных данных и безопасность](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security).

## Свойства типа "Набор данных MySQL"

Полный список разделов и свойств, используемых для определения наборов данных, см. в статье [Наборы данных](data-factory-create-datasets.md). Разделы структуры, доступности и политики JSON набора данных одинаковы для всех типов наборов данных (SQL Azure, большие двоичные объекты Azure, таблицы Azure и т. д.).

Раздел **typeProperties** во всех типах наборов данных разный. В нем содержится информация о расположении данных в хранилище данных. Раздел typeProperties набора данных с типом **RelationalTable** (который включает набор данных MySQL) имеет следующие свойства.

| Свойство | Описание | Обязательно |
| -------- | ----------- | -------- |
| tableName | Имя таблицы в экземпляре базы данных MySQL, на которое ссылается связанная служба. | Нет (если задан **запрос** свойства **RelationalSource**) | 

## Свойства типа "Действие копирования MySQL"

Полный список разделов и свойств, используемых для определения действий, см. в статье [Создание конвейеров](data-factory-create-pipelines.md). Свойства (такие как имя, описание, входные и выходные таблицы, политики и т. д.) доступны для всех типов действий.

С другой стороны, свойства, доступные в разделе **typeProperties** действия, зависят от конкретного типа действия. Для действия копирования они различаются в зависимости от типов источников и приемников.

Если источник действия копирования относится к типу **RelationalSource** (который содержит MySQL), в разделе typeProperties доступны следующие свойства.

| Свойство | Описание | Допустимые значения | Обязательно |
| -------- | ----------- | -------------- | -------- |
| запрос | Используйте пользовательский запрос для чтения данных. | Строка запроса SQL. Например, select * from MyTable. | Нет (если для **dataset** задано значение **tableName**) | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Сопоставление типов для MySQL

Как упоминалось в статье о [действиях перемещения данных](data-factory-data-movement-activities.md), во время копирования типы источников автоматически преобразовываются в типы приемников. Такое преобразование выполняется в два этапа:

1. Преобразование собственных типов источников в тип .NET.
2. Преобразование типа .NET в собственный тип приемника.

Когда данные перемещаются в MySQL, для преобразования типа MySQL в тип .NET используются следующие сопоставления.

| Тип базы данных MySQL | Тип .NET Framework |
| ------------------- | ------------------- | 
| bigint unsigned | Decimal |
| bigint | Int64 |
| bit | Decimal |
| blob-объект | Byte |
| bool | Логический | 
| char; | Строка |
| дата | Datetime |
| datetime; | Datetime |
| decimal | Decimal |
| double precision | Double |
| double | Double |
| enum | Строка |
| float; | Single |
| int unsigned | Int64 |
| int | Int32 |
| integer unsigned | Int64 |
| целое число | Int32 | 
| long varbinary | Byte |
| long varchar | Строка |
| longblob | Byte |
| longtext | Строка | 
| mediumblob | 	Byte | 
| mediumint unsigned | Int64 |
| mediumint | Int32 | 
| mediumtext | Строка |
| numeric | Decimal |
| real; | Double |
| set | Строка |
| smallint unsigned | Int32 |
| smallint; | Int16 |
| text | Строка |
| time | TimeSpan |
| Timestamp | Datetime |
| tinyblob | Byte |
| tinyint unsigned | Int16 | 
| tinyint; | Int16 |
| tinytext | Строка | 
| varchar. | Строка | 
| year | Int | 


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## Производительность и настройка  
См. статью [Руководство по настройке производительности действия копирования](data-factory-copy-activity-performance.md), в которой описываются ключевые факторы, влияющие на производительность перемещения данных (действие копирования) в фабрике данных Azure, и различные способы оптимизации этого процесса.

<!---HONumber=AcomDC_0921_2016-->