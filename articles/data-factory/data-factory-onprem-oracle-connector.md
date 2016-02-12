<properties 
	pageTitle="Перемещение данных из Oracle | Фабрика данных Azure" 
	description="Узнайте, как переместить данные в локальную базу данных Oracle или из нее с помощью фабрики данных Azure." 
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
	ms.date="02/01/2016" 
	ms.author="spelluru"/>

# Перемещение данных из локальной базы данных Oracle с помощью фабрики данных Azure 

В этой статье описано использование действия копирования в фабрике данных Azure для перемещения данных из Oracle в другое хранилище данных. Эта статья основана на статье о [действиях перемещения данных](data-factory-data-movement-activities.md), в которой приведены общие сведения о перемещении данных с помощью действия копирования и поддерживаемых сочетаниях хранилищ данных.

## Установка 
Чтобы служба фабрики данных Azure могла подключаться к локальной базе данных Oracle, необходимо установить следующие компоненты.

- Шлюз управления данными на том же компьютере, на котором размещена база данных, или на отдельном компьютере во избежание конкуренции за ресурсы. Шлюз управления данными — это программное обеспечение, которое обеспечивает безопасное и управляемое подключение локальных источников данных к облачным службам. Сведения о шлюзе управления данными см. в статье [Перемещение данных между локальными источниками и облаком](data-factory-move-data-between-onprem-and-cloud.md). 
- [Компоненты доступа к данным Oracle (ODAC) для Windows](http://www.oracle.com/technetwork/topics/dotnet/downloads/index.html). Должны быть установлены на главном компьютере, на котором установлен шлюз.

> [AZURE.NOTE] Советы по устранению неполадок, связанных с шлюзом или подключением, см. в разделе [Устранение неполадок шлюза](data-factory-move-data-between-onprem-and-cloud.md#gateway-troubleshooting).

## Пример копирования данных из Oracle в хранилище BLOB-объектов Azure
В этом примере показано, как скопировать данные из локальной базы данных Oracle в хранилище BLOB-объектов Azure. Однако данные можно **напрямую** копировать в любой указанный [здесь](data-factory-data-movement-activities.md#supported-data-stores) приемник. Это делается с помощью действия копирования в фабрике данных Azure.
 
Образец состоит из следующих сущностей фабрики данных.

1.	Связанная служба типа [OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties).
2.	Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Входной [набор данных](data-factory-create-datasets.md) типа [OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties). 
4.	Выходной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5.	[Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [OracleSource](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) в качестве источника и [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) в качестве приемника.

В примере данные из локальной таблицы Oracle каждый час копируются в хранилище BLOB-объектов. Дополнительные сведения о различных свойствах, используемых в примере, см. в документации, ссылки на которую приведены в разделах после примеров.

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

Если для параметра external задать значение true и указать политику externalData, фабрика данных будет считать эту таблицу внешней по отношению к себе и не созданной в результате какого-либо действия в фабрике данных.

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

Конвейер содержит действие копирования (Copy), которое использует входной и выходной наборы данных (см. выше) и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **RelationalSource**, а для типа **sink** — значение **BlobSink**. SQL-запрос, указанный для свойства **oracleReaderQuery**, выбирает для копирования данные за последний час.

	
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

## Свойства связанной службы Oracle

В следующей таблице содержится описание элементов JSON, которые относятся к связанной службе Oracle.

Свойство | Описание | Обязательно
-------- | ----------- | --------
type | Для свойства type необходимо задать значение **OnPremisesOracle**. | Да
connectionString | В свойстве connectionString указываются сведения, необходимые для подключения к экземпляру базы данных Oracle. | Да 
gatewayName | Имя шлюза, который будет использоваться для подключения к локальному серверу Oracle. | Да

Подробную информацию о настройке учетных данных для локального источника данных Oracle см. в разделе [Настройка учетных данных и безопасность](data-factory-move-data-between-onprem-and-cloud.md#setting-credentials-and-security).
## Свойства типа "Набор данных Oracle"

Полный список разделов и свойств, используемых для определения наборов данных, см. в статье [Создание наборов данных](data-factory-create-datasets.md). Разделы структуры, доступности и политики JSON набора данных одинаковы для всех типов наборов данных (Oracle, BLOB-объекты Azure, таблицы Azure и т. д.).
 
Раздел typeProperties во всех типах наборов данных разный. В нем содержатся сведения о расположении данных в хранилище данных. Раздел TypeProperties набора данных с типом OracleTable имеет следующие свойства.

Свойство | Описание | Обязательно
-------- | ----------- | --------
tableName | Имя таблицы в базе данных Oracle, на которое ссылается связанная служба. | Нет (если указан параметр **oracleReaderQuery** объекта **SqlSource**)

## Свойства типа "Действие копирования Oracle"

Полный список разделов и свойств, используемых для определения действий, см. в статье [Создание конвейеров](data-factory-create-pipelines.md). Такие свойства, как имя, описание, входные и выходные таблицы, различные политики и т. д., доступны для всех типов действий.

**Примечание**. Действие копирования принимает только один входной набор данных и возвращает только один выходной набор.

То, какие свойства будут доступны в разделе typeProperties, зависит от типа действия, а в случае с действием копирования — еще и от типов источников и приемников.

Для действия копирования, когда источник относится к типу **OracleSource**, в разделе **typeProperties** доступны указанные ниже свойства.

Свойство | Описание |Допустимые значения | Обязательно
-------- | ----------- | ------------- | --------
oracleReaderQuery | Используйте пользовательский запрос для чтения данных. | Строка запроса SQL. 
Например: select * from MyTable <p>Если не указано другое, выполняется следующий оператор SQL: select * from MyTable</p> | Нет (если для **dataset** задано значение **tableName**)

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Сопоставление типов для Oracle

Как упоминалось в статье о [действиях перемещения данных](data-factory-data-movement-activities.md), во время копирования типы источников автоматически преобразуются в типы приемников. Такое преобразование состоит из следующих двух этапов.

1. Преобразование собственных типов источников в тип .NET.
2. Преобразование типа .NET в собственный тип приемника.

Когда данные перемещаются из Oracle, для преобразования типа данных Oracle в тип .NET и наоборот используются следующие сопоставления.

Тип данных Oracle | Тип данных платформы .NET
---------------- | ------------------------
BFILE | Byte
BLOB | Byte
CHAR | Строка
CLOB | Строка
DATE | DateTime
FLOAT | Decimal
INTEGER | Decimal
INTERVAL YEAR TO MONTH | Int32
INTERVAL DAY TO SECOND | TimeSpan
LONG | String
LONG RAW | Byte
NCHAR | String
NCLOB | String
NUMBER | Decimal
NVARCHAR2 | String
RAW | Byte
ROWID | Строка
TIMESTAMP | DateTime
TIMESTAMP WITH LOCAL TIME ZONE | DateTime
TIMESTAMP WITH TIME ZONE | DateTime
UNSIGNED INTEGER | Number
VARCHAR2 | String
XML | Строка

## Советы по устранению неполадок

**** Проблема. ** Появится следующее **сообщение об ошибке**: Действие копирования обнаружило недопустимые параметры: "UnknownParameterName". Подробное сообщение: Не удалось найти запрошенный .Net Framework Data Provider. Возможно, он не установлен.

**Возможные причины**

1. Поставщик данных .NET Framework для Oracle не был установлен.
2. Поставщик данных .NET Framework для Oracle был установлен в .NET Framework 2.0 и не найден в папках .NET Framework 4.0. 

**Решение**

1. Если поставщик данных .NET для Oracle не установлен, [установите](http://www.oracle.com/technetwork/topics/dotnet/utilsoft-086879.html) его и повторите сценарий. 
2. Если сообщение об ошибке появляется даже после установки поставщика, выполните следующие действия. 
	1. Откройте файл конфигурации .NET 2.0 из папки: <system disk> \\Windows\\Microsoft.NET\\Framework64\\v2.0.50727\\CONFIG\\machine.config.
	2. Найдите **поставщик данных Oracle для .NET** и запись, аналогичную следующей, в разделе **system.data** -> **DbProviderFactories**: <add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />
2.	Скопируйте эту запись в файл конфигурации компьютера в следующей папке v4.0: <system disk>\\Windows\\Microsoft.NET\\Framework64\\v4.0.30319\\Config\\machine.config, измените версию на 4.xxx.x.x.
3.	Установите файл <ODP.NET Installed Path>\\11.2.0\\client\_1\\odp.net\\bin\\4\\Oracle.DataAccess.dll в глобальный кэш сборок (GAC), выполнив команду "gacutil /i [путь\_к\_поставщику]".



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

<!---HONumber=AcomDC_0204_2016-->