<properties
	pageTitle="Перемещение данных в базу данных SQL Server и обратно | Фабрика данных Azure"
	description="Узнайте, как с помощью фабрики данных Azure перемещать данные в базу данных SQL Server и обратно на локальных компьютерах и виртуальных машинах Azure."
	services="data-factory"
	documentationCenter=""
	authors="linda33wj"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/31/2016"
	ms.author="jingwang"/>

# Перемещение данных в базу данных SQL Server и обратно на локальных компьютерах и виртуальных машинах Azure IaaS с помощью фабрики данных Azure
В этой статье описано использование действия копирования для перемещения данных между SQL Server и другим хранилищем данных. Эта статья основана на статье о [действиях перемещения данных](data-factory-data-movement-activities.md), в которой приведены общие сведения о перемещении данных и хранилищах данных, которые поддерживаются в качестве источников и приемников.

## Поддерживаемые источники и приемники
В таблице [Поддерживаемые хранилища данных](data-factory-data-movement-activities.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования. Данные можно переместить из любого поддерживаемого в качестве источника хранилища данных в SQL Server или из SQL Server в любое поддерживаемое в качестве приемника хранилище данных.

## Создание конвейера
Можно создать конвейер с действием копирования, которое перемещает данные из базы данных SQL Server или в нее с помощью различных инструментов и интерфейсов API.

- Мастер копирования
- Портал Azure
- Visual Studio
- Azure PowerShell
- .NET API
- Интерфейс REST API

Пошаговые инструкции по различным способам создания конвейера с действием копирования см. в [руководстве по действию копирования](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## Включение соединения

Где бы ни размещалась система SQL Server — локально или на виртуальных машинах Azure IaaS (инфраструктура как услуга), — основные понятия и действия, необходимые для соединения с ней, одинаковы. В обоих случаях для подключения необходимо использовать шлюз управления данными.

В статье [Перемещение данных между локальными и облачными ресурсами](data-factory-move-data-between-onprem-and-cloud.md) приведены сведения о шлюзе управления данными и пошаговые инструкции по его настройке. Настройка экземпляра шлюза необходима для подключения к SQL Server.

Вы можете установить шлюз на тот же локальный компьютер или экземпляр облачной виртуальной машины, на котором установлена система SQL Server, однако для более эффективной работы мы рекомендуем устанавливать их на разные компьютеры. Размещение шлюза и SQL Server на разных компьютерах уменьшает конкуренцию за ресурсы.


## Мастер копирования данных
Самый простой способ создать конвейер, копирующий данные из базы данных SQL Server в любое поддерживаемое хранилище-приемник, — использовать мастер копирования данных. В статье [Руководство. Создание конвейера с действием копирования с помощью мастера копирования фабрики данных](data-factory-copy-data-wizard-tutorial.md) приведены краткие пошаговые указания по созданию конвейера с помощью мастера копирования данных.

Ниже приведены примеры с определениями JSON, которые можно использовать для создания конвейера с помощью [портала Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) или [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). В следующих примерах показано, как копировать данные в базу данных SQL Server и хранилище BLOB-объектов Azure и обратно. Тем не менее данные можно копировать **непосредственно** из любых источников в любой указанный [здесь](data-factory-data-movement-activities.md#supported-data-stores) приемник. Это делается с помощью действия копирования в фабрике данных Azure.

## Пример. Копирование данных из SQL Server в хранилище BLOB-объектов Azure

В примере ниже используется следующее:

1.	Связанная служба типа [OnPremisesSqlServer](data-factory-sqlserver-connector.md#sql-server-linked-service-properties).
2.	Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Входной [набор данных](data-factory-create-datasets.md) типа [SqlServerTable](data-factory-sqlserver-connector.md#sql-server-dataset-type-properties).
4.	Выходной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	[Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [SqlSource](data-factory-sqlserver-connector.md#sql-server-copy-activity-type-properties) и [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

В этом примере данные временного ряда каждый час копируются из таблицы SQL Server в большой двоичный объект Azure. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

Сначала настройте шлюз управления данными. Инструкции приведены в статье [Перемещение данных между локальными источниками и облаком с помощью шлюза управления данными](data-factory-move-data-between-onprem-and-cloud.md).

**Связанная служба SQL Server**

	{
	  "Name": "SqlServerLinkedService",
	  "properties": {
	    "type": "OnPremisesSqlServer",
	    "typeProperties": {
	      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
	      "gatewayName": "<gatewayname>"
	    }
	  }
	}

**Связанная служба хранилища BLOB-объектов Azure**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Входной набор данных SQL Server**

В примере предполагается, что вы уже создали таблицу MyTable в SQL Server и она содержит столбец с именем timestampcolumn для данных временного ряда. Можно выполнить запрос к нескольким таблицам в одной базе данных, используя один набор данных, но для typeProperty tableName набора данных нужно указать одну таблицу.

Если для параметра external задать значение true, то фабрика данных воспримет этот набор данных как внешний, который создан не в результате какого-либо действия в этой службе.

	{
	  "name": "SqlServerInput",
	  "properties": {
	    "type": "SqlServerTable",
	    "linkedServiceName": "SqlServerLinkedService",
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

Конвейер содержит действие копирования, которое использует эти входной и выходной наборы данных и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **SqlSource**, а для типа **sink** — значение **BlobSink**. SQL-запрос, указанный для свойства **SqlReaderQuery**, выбирает для копирования данные за последний час.


	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "SqlServertoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": " SqlServerInput"
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


В этом примере для свойства SqlSource указано **sqlReaderQuery**. Действие копирования выполняет этот запрос к источнику базы данных SQL Server с целью получения данных. Кроме того, можно создать хранимую процедуру, указав **sqlReaderStoredProcedureName** и **storedProcedureParameters** (если хранимая процедура принимает параметры). Свойство sqlReaderQuery может ссылаться на несколько таблиц из базы данных, на которую ссылается входной набор данных. Он не ограничивается таблицей, заданной свойством typeProperty в качестве параметра tableName набора данных.


Если не указать sqlReaderQuery или sqlReaderStoredProcedureName, то для построения запроса select к базе данных SQL Server будут использованы столбцы, определенные в разделе структуры. Если у определения набора данных нет структуры, выбираются все столбцы из таблицы.


Список свойств, поддерживаемых SqlSource и BlobSink, см. в разделах [SqlSource](#sqlsource) и [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

## Пример. Копирование данных из BLOB-объекта Azure в базу данных SQL Server

В примере ниже используется следующее:

1.	Связанная служба типа [OnPremisesSqlServer](data-factory-sqlserver-connector.md#sql-server-linked-service-properties).
2.	Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Входной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	Выходной [набор данных](data-factory-create-datasets.md) типа [SqlServerTable](data-factory-sqlserver-connector.md#sql-server-dataset-type-properties).
4.	[Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) и [SqlSink](data-factory-sqlserver-connector.md#sql-server-copy-activity-type-properties).

В этом примере данные временного ряда каждый час копируются из большого двоичного объекта Azure в таблицу SQL Server. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

**Связанная служба SQL Server**

	{
	  "Name": "SqlServerLinkedService",
	  "properties": {
	    "type": "OnPremisesSqlServer",
	    "typeProperties": {
	      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
	      "gatewayName": "<gatewayname>"
	    }
	  }
	}

**Связанная служба хранилища BLOB-объектов Azure**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Входной набор данных BLOB-объекта Azure**

Данные берутся из нового BLOB-объекта каждый час (frequency: hour, interval: 1). Путь к папке с BLOB-объектом и имя файла вычисляются динамически на основе времени начала обрабатываемого среза. В пути к папке используется год, месяц и день начала, а в имени файла — час начала. Когда для параметра external задано значение true, служба фабрики данных считает этот набор данных внешним по отношению к себе и не созданным в результате какого-либо действия в фабрике данных.

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

**Выходной набор данных SQL Server**

В этом примере данные копируются в таблицу MyTable, которая создана в базе данных SQL Server. Создайте в базе данных SQL Server таблицу с тем же количеством столбцов, которое должно быть в CSV-файле большого двоичного объекта. Новые строки добавляются в таблицу каждый час.

	{
	  "name": "SqlServerOutput",
	  "properties": {
	    "type": "SqlServerTable",
	    "linkedServiceName": "SqlServerLinkedService",
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

Конвейер содержит действие копирования, которое использует эти входной и выходной наборы данных и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **BlobSource**, а для типа **sink** — значение **SqlSink**.

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
	            "name": " SqlServerOutput "
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource",
	            "blobColumnSeparators": ","
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

## Свойства связанной службы SQL Server
В примерах использовалась связанная служба типа **OnPremisesSqlServer**, чтобы связать локальную базу данных SQL Server с фабрикой данных. В следующей таблице содержится описание элементов JSON, которые относятся к локальной связанной службе SQL Server.

В следующей таблице содержится описание элементов JSON, которые относятся к связанной службе SQL Server.

| Свойство | Описание | Обязательно |
| -------- | ----------- | -------- |
| type | Свойству type необходимо присвоить значение **OnPremisesSqlServer**. | Да |
| connectionString | Укажите сведения о параметре connectionString, необходимые для подключения к локальной базе данных SQL Server с помощью проверки подлинности SQL или проверки подлинности Windows. | Да |
| gatewayName | Имя шлюза, который службе фабрики данных следует использовать для подключения к локальной базе данных SQL Server. | Да |
| Имя пользователя | При использовании проверки подлинности Windows укажите имя пользователя. Пример: **имя\_домена\\имя\_пользователя**. | Нет |
| пароль | Введите пароль для учетной записи пользователя, указанной для выбранного имени пользователя. | Нет |

Вы можете зашифровать учетные данные с помощью командлета **New-AzureRmDataFactoryEncryptValue** и использовать их в строке подключения, как показано в следующем примере (свойство **EncryptedCredential**).

	"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",

### Примеры

**JSON для использования проверки подлинности SQL**

	{
	    "name": "MyOnPremisesSQLDB",
	    "properties":
	    {
	        "type": "OnPremisesSqlLinkedService",
			"typeProperties": {
	        	"connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
	        	"gatewayName": "<gateway name>"
			}
	    }
	}

**JSON для использования проверки подлинности Windows**

Если указаны имя пользователя и пароль, то шлюз использует их, чтобы действовать от имени соответствующей учетной записи пользователя для подключения к локальной базе данных SQL Server. В противном случае шлюз подключается к SQL Server напрямую с помощью контекста безопасности шлюза (его стартовая учетная запись).

	{
	     "Name": " MyOnPremisesSQLDB",
	     "Properties":
	     {
	         "type": "OnPremisesSqlLinkedService",
			 "typeProperties": {
	         	"ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
	         	"username": "<domain\\username>",
	         	"password": "<password>",
	         	"gatewayName": "<gateway name>"
			}
	     }
	}

Дополнительную информацию о настройке учетных данных для источника данных SQL Server см. в разделе [Настройка учетных данных и безопасность](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security).

## Свойства типов наборов данных в SQL Server
В примерах использовался набор данных типа **SqlServerTable** для представления таблицы в базе данных SQL Server.

Полный список разделов и свойств, используемых для определения наборов данных, см. в статье [Наборы данных](data-factory-create-datasets.md). Разделы JSON набора данных, такие как структура, доступность и политика, одинаковы для всех типов наборов данных (SQL Server, большой двоичный объект Azure, таблица Azure и т. д.).

Раздел typeProperties во всех типах наборов данных разный. В нем содержатся сведения о расположении данных в хранилище данных. Раздел **typeProperties** для набора данных с типом **SqlServerTable** имеет следующие свойства.

| Свойство | Описание | Обязательно |
| -------- | ----------- | -------- |
| tableName | Имя таблицы в экземпляре базы данных SQL Server, на который ссылается связанная служба. | Да |

## Свойства типа "Действие копирования" в SQL Server
При перемещении данных из базы данных SQL Server в действии копирования задается тип источника **SqlSource**. Аналогично, при перемещении данных в базу данных SQL Server в действии копирования задается тип источника **SqlSink**. Этот раздел содержит список свойств, поддерживаемых типами SqlSource и SqlSink.

Полный список разделов и свойств, используемых для определения действий, см. в статье [Создание конвейеров](data-factory-create-pipelines.md). Свойства (такие как имя, описание, входные и выходные таблицы, политики и т. д.) доступны для всех типов действий.

> [AZURE.NOTE] Действие копирования принимает только один набор входных данных и возвращает только один набор выходных.

С другой стороны, свойства, доступные в разделе typeProperties действия, зависят от конкретного типа действия. Для действия копирования они различаются в зависимости от типов источников и приемников.

### SqlSource

Когда источник в действии копирования относится к типу **SqlSource**, в разделе **typeProperties** доступны указанные ниже свойства.

| Свойство | Описание | Допустимые значения | Обязательно |
| -------- | ----------- | -------------- | -------- |
| sqlReaderQuery | Используйте пользовательский запрос для чтения данных. | Строка запроса SQL. Например, select * from MyTable. Может ссылаться на несколько таблиц из базы данных, на которую ссылается входной набор данных. Если не указано другое, выполняется инструкция SQL select from MyTable. | Нет |
| sqlReaderStoredProcedureName | Имя хранимой процедуры, которая считывает данные из исходной таблицы. | Имя хранимой процедуры. | Нет |
| storedProcedureParameters | Параметры для хранимой процедуры. | Пары имен и значений. Имена и регистр параметров должны совпадать с именами и регистром параметров хранимой процедуры. | Нет |

Если для SqlSource указано **sqlReaderQuery**, то действие копирования выполняет этот запрос для источника базы данных SQL Server с целью получения данных.

Кроме того, можно создать хранимую процедуру, указав **sqlReaderStoredProcedureName** и **storedProcedureParameters** (если хранимая процедура принимает параметры).

Если не указать sqlReaderQuery или sqlReaderStoredProcedureName, то для построения запроса select к базе данных SQL Server будут использованы столбцы, определенные в разделе структуры. Если у определения набора данных нет структуры, выбираются все столбцы из таблицы.

> [AZURE.NOTE] При использовании **sqlReaderStoredProcedureName** по-прежнему необходимо указать значение свойства **tableName** в наборе данных JSON. Хотя какие-либо проверки этой таблицы отсутствуют.

### SqlSink

**SqlSink** поддерживает указанные ниже свойства.


| Свойство | Описание | Допустимые значения | Обязательно |
| -------- | ----------- | -------------- | -------- |
| writeBatchTimeout | Время ожидания до выполнения операции пакетной вставки, пока не завершится срок ее действия. | Интервал времени<br/><br/> Пример: 00:30:00 (30 минут). | Нет |
| writeBatchSize | Вставляет данные в таблицу SQL, когда размер буфера достигает значения writeBatchSize. | Целое число (количество строк) | Нет (значение по умолчанию — 10 000).
| sqlWriterCleanupScript | Укажите запрос на выполнение действия копирования, позволяющий убедиться в том, что данные конкретного среза очищены. Дополнительные сведения см. в [разделе о повторяемости](#repeatability-during-copy). | Инструкция запроса. | Нет |
| sliceIdentifierColumnName | Укажите имя столбца, в которое действие копирования добавляет автоматически созданный идентификатор среза. Этот идентификатор используется для очистки данных конкретного среза при повторном запуске. Дополнительные сведения см. в [разделе о повторяемости](#repeatability-during-copy). | Имя столбца с типом данных binary(32). | Нет |
| sqlWriterStoredProcedureName | Имя хранимой процедуры, обновляющей данные или вставляющей их в целевую таблицу. | Имя хранимой процедуры. | Нет |
| storedProcedureParameters | Параметры для хранимой процедуры. | Пары имен и значений. Имена и регистр параметров должны совпадать с именами и регистром параметров хранимой процедуры. | Нет |
| sqlWriterTableType | Укажите имя типа таблицы для использования в хранимой процедуре. Действие копирования делает перемещаемые данные доступными во временной таблице этого типа. Код хранимой процедуры затем можно использовать для объединения копируемых и существующих данных. | Имя типа таблицы. | Нет |

## Устранение неполадок с подключением

1. Настройте SQL Server для приема удаленных подключений. Запустите **SQL Server Management Studio**, щелкните правой кнопкой мыши свой **сервер** и выберите пункт **Свойства**. Выберите в списке пункт **Подключения** и установите флажок **Разрешить удаленные подключения к этому серверу**.

	.![Включение удаленных подключений](.\media\data-factory-sqlserver-connector\AllowRemoteConnections.png)

	Подробные инструкции см. в статье [Настройка параметра конфигурации сервера remote access](https://msdn.microsoft.com/library/ms191464.aspx).
2. Запустите **диспетчер конфигурации SQL Server**. Разверните узел **Сетевая конфигурация SQL Server** для нужного экземпляра и выберите пункт **Protocols for MSSQLSERVER** (Протоколы для MSSQLSERVER). Вы должны увидеть протоколы на панели справа. Включите TCP/TP, щелкнув правой кнопкой мыши имя протокола **TCP/IP** и выбрав пункт **Включить**.

	.![Включение TCP/IP](.\media\data-factory-sqlserver-connector\EnableTCPProptocol.png)

	Подробные сведения и альтернативные способы включения протокола TCP/IP см. в статье [Включение или отключение сетевого протокола сервера](https://msdn.microsoft.com/library/ms191294.aspx).
3. В этом же окне дважды щелкните **TCP/IP**, чтобы открыть окно **TCP/IP Properties** (Свойства TCP/IP).
4. Перейдите на вкладку **IP-адреса**. Прокрутите вниз до раздела **IPAll**. Запишите значение параметра **Порт TCP** (по умолчанию — **1433**).
5. Создайте на компьютере **правило брандмауэра Windows**, чтобы разрешить входящий трафик через этот порт.
6. **Проверьте подключение**. Для этого запустите SQL Server Management Studio на другом компьютере и подключитесь к SQL Server, указав полное имя сервера, например: <компьютер>.<домен>.corp.<компания>.com,1433.

	> [AZURE.IMPORTANT]
	Дополнительные сведения см. в статье [Перемещение данных между локальными источниками и облаком с помощью шлюза управления данными](data-factory-move-data-between-onprem-and-cloud.md#port-and-security-considerations).
	>   
	> Советы по устранению неполадок, связанных со шлюзом или подключением, см. в разделе [Устранение неполадок в работе шлюза](data-factory-data-management-gateway.md#troubleshoot-gateway-issues).

## Столбцы идентификаторов в целевой базе данных
В этом разделе приведен пример копирования данных из исходной таблицы без столбца идентификаторов в целевую таблицу со столбцом идентификаторов.

**Исходная таблица:**

	create table dbo.SourceTbl
	(
	       name varchar(100),
	       age int
	)

**Целевая таблица:**

	create table dbo.TargetTbl
	(
	       id int identity(1,1),
	       name varchar(100),
	       age int
	)


Обратите внимание, что в целевой таблице есть столбец идентификаторов.

**Определение JSON исходного набора данных**

	{
	    "name": "SampleSource",
	    "properties": {
	        "published": false,
	        "type": " SqlServerTable",
	        "linkedServiceName": "TestIdentitySQL",
	        "typeProperties": {
	            "tableName": "SourceTbl"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
	        "external": true,
	        "policy": {}
	    }
	}

**Определение JSON целевого набора данных**

	{
	    "name": "SampleTarget",
	    "properties": {
	        "structure": [
	            { "name": "name" },
	            { "name": "age" }
	        ],
	        "published": false,
	        "type": "AzureSqlTable",
	        "linkedServiceName": "TestIdentitySQLSource",
	        "typeProperties": {
	            "tableName": "TargetTbl"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
	        "external": false,
	        "policy": {}
	    }
	}


Обратите внимание, что у исходной и целевой таблиц разные схемы (в целевой есть дополнительный столбец с идентификаторами). В этом случае необходимо указать свойство **structure** в определении целевого набора данных, которое не включает в себя столбец идентификаторов.

Затем следует сопоставить столбцы из исходного набора данных со столбцами в целевом наборе данных. Ознакомьтесь с примером в разделе [Примеры сопоставления столбцов](#column-mapping-samples).

[AZURE.INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)]


[AZURE.INCLUDE [data-factory-sql-invoke-stored-procedure](../../includes/data-factory-sql-invoke-stored-procedure.md)]


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]


### Сопоставление типов SQL Server и SQL Azure

Как упоминалось в статье о [действиях перемещения данных](data-factory-data-movement-activities.md), во время копирования типы источников автоматически преобразовываются в типы приемников. Такое преобразование выполняется в 2 этапа:

1. Преобразование собственных типов источников в тип .NET.
2. Преобразование типа .NET в собственный тип приемника.

Когда данные перемещаются в базы данных SQL Azure, SQL Server, Sybase и обратно, для преобразования типа SQL в тип .NET и наоборот используются следующие сопоставления.

Сопоставление аналогично сопоставлению типов данных SQL Server для ADO.NET.

| Тип ядра СУБД SQL Server | Тип .NET Framework |
| ------------------------------- | ------------------- |
| bigint | Int64 |
| binary; | Byte |
| bit | Логический |
| char; | String, Char |
| дата | DateTime |
| Datetime | DateTime |
| datetime2; | DateTime |
| Datetimeoffset | DateTimeOffset |
| Decimal | Decimal |
| Атрибут FILESTREAM (varbinary(max)) | Byte |
| Float | Double |
| изображение | Byte |
| int | Int32 |
| money; | Decimal |
| nchar; | String, Char |
| ntext | String, Char |
| numeric | Decimal |
| nvarchar; | String, Char |
| real; | Single |
| rowversion | Byte |
| smalldatetime; | DateTime |
| smallint; | Int16 |
| smallmoney; | Decimal |
| sql\_variant | Object * |
| text | String, Char |
| Twitter в режиме реального | TimeSpan |
| Timestamp | Byte |
| tinyint; | Byte |
| uniqueidentifier | Guid |
| varbinary; | Byte |
| varchar. | String, Char |
| xml | Xml |


[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## Производительность и настройка  
См. статью [Руководство по настройке производительности действия копирования](data-factory-copy-activity-performance.md), в которой описываются ключевые факторы, влияющие на производительность перемещения данных (действие копирования) в фабрике данных Azure, и различные способы оптимизации этого процесса.

<!---HONumber=AcomDC_0928_2016-->