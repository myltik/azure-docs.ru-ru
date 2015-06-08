<properties 
	pageTitle="Примеры использования копии действия в фабрике Azure данных" 
	description="Примеры для Пол операцию копирования в фабрику данных Azure." 
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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Примеры использования копии действия в фабрике Azure данных
С помощью **действия копирования** в конвейере вы можете выполнять пакетное копирование данных из источника в приемник (место назначения). В этом разделе приведено несколько примеров использования действия копии в конвейере данных фабрики. Подробное описание действия копирования и основных сценариев, которые оно поддерживает, см. в разделе [Копирование данных с помощью фабрики данных Azure][adf-copyactivity].

## Копирование данных из локальной базы данных SQL Server в Azure BLOB-объект
В этом примере входная и выходная таблицы определяются и используются в действии копирования внутри конвейера, копирующего данные из локальной базы данных SQL Server в большой двоичный объект Azure.

### Предположения
В этом примере предполагается, что уже следующие артефакты фабрика данных Azure:

* группа ресурсов с именем **ADF**;
* фабрика данных Azure с именем **CopyFactory**;
* Шлюз управления данными с именем **mygateway** создается и находится в оперативном режиме.  

### Создание связанного службы SQL Server в локальной исходной базы данных
На этом шаге создания связанного с именем службы **MyOnPremisesSQLDB** указывающий на локальную базу данных SQL Server.

	{
	    "name": "MyOnPremisesSQLDB",
	    "properties":
	    {
	        "type": "OnPremisesSqlLinkedService",
	        "connectionString": "Data Source=<servername>;Initial Catalog=<database>;Integrated Security=False;User ID=<username>;Password=<password>;",
	        "gatewayName": "mygateway"
	    }
	}

Обратите внимание на следующее:

- **тип** равен **OnPremisesSqlLinkedService**.
- **connectionString** задать строку подключения для базы данных SQL Server. 
- **gatewayName** присвоено имя шлюза управления данными установки локального компьютера и зарегистрирована на портале службы фабрики данных Azure. 

В разделе [локальной службы связанного SQL](https://msdn.microsoft.com/library/dn893523.aspx) подробные сведения о JSON элементы для определения локального SQL связанные службы.
 
### Создание связанной службы для назначения BLOB-объектов Azure
На этом шаге создания связанного с именем службы **MyAzureStorage** указывающий хранилище больших двоичных объектов.

	{
	    "name": "MyAzureStorage",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

Обратите внимание на следующее:

- **тип** равен **AzureStorageLinkedService**.
- **connectionString** — укажите имя учетной записи и ключ для хранилища Azure учетной записи.

В разделе [связанные службы хранилища Azure](https://msdn.microsoft.com/library/dn893522.aspx) подробные сведения об элементах JSON для определения хранилища Azure связанные службы.

### JSON входной таблицы
Следующий скрипт JSON определяет входную таблицу, которая ссылается на таблицу SQL **MyTable** в локальной базе данных SQL Server, которую определяет связанная служба **MyOnPremisesSQLDB**. Обратите внимание, что **name** — это имя таблицы фабрики данных Azure, а **tableName** — это имя таблицы SQL в базе данных SQL Server.

         
	{
		"name": "MyOnPremTable",
    	"properties":
   		{
			"location":
    		{
    			"type": "OnPremisesSqlServerTableLocation",
    			"tableName": "MyTable",
    			"linkedServiceName": "MyOnPremisesSQLDB"
    		},
    		"availability":
   			{
    			"frequency": "Hour",
    			"interval": 1
   			}
 		}
	}

Обратите внимание на следующее:

- **тип** равен **OnPremisesSqlServerTableLocation**.
- **tableName** равен **MyTable**, которая содержит исходные данные. 
- **linkedServiceName** равен **MyOnPremisesSQLDB**, связанные службы, созданной для локальной базы данных SQL.

В разделе [Свойства расположения локального SQL](https://msdn.microsoft.com/library/dn894089.aspx#OnPremSQL) подробные сведения об элементах JSON для определения таблицы данных фабрики, ссылается на таблицу SQL Server.

### JSON выходной таблицы
Следующий скрипт JSON определяет выходной таблицы: **MyAzureBlob**, которая относится к BLOB-объект Azure: **MyBlob** в папке BLOB-объектов: **MySubFolder** в контейнер больших двоичных объектов: **MyContainer**.
         
	{
   		"name": "MyAzureBlob",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "MyAzureStorage",
        		"format":
        		{
            		"type": "TextFormat",
            		"columnDelimiter": ",",
            		"rowDelimiter": ";",
             		"EscapeChar": "$",
             		"NullValue": "NaN"
        		}
    		},
        	"availability":
      		{
       			"frequency": "Hour",
       			"interval": 1
      		}
   		}
	}

Обратите внимание на следующее:
 
- **тип** равен **AzureBlobLocation**.
- **folderPath** имеет значение **MyContainer/MySubFolder**, который содержит большой двоичный объект, содержащий скопированные данные. 
- **fileName** равен **MyBlob**, большой двоичный объект, который будет содержать выходные данные.
- **linkedServiceName** равен **MyAzureStorge**, связанные службы, созданной для хранилища Azure.    

В разделе [Свойства расположения BLOB-объектов Azure](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob) подробные сведения об элементах JSON для определения таблицы данных фабрики, ссылающийся на большой двоичный объект Azure.

### JSON конвейера (с действием копирования)
В этом примере конвейер **CopyActivityPipeline** определяют следующие свойства:

- свойству **type** присваивается значение **CopyActivity**;
- свойство **MyOnPremTable** определяется как входные данные (тег **inputs**);
- свойство **MyAzureBlob** определяется как выходные данные (тег **outputs**).
- В разделе **Transformation** есть два подраздела: **source** и **sink**. Для источника (source) задан тип **SqlSource**, а для приемника (sink) — тип **BlobSink**. **sqlReaderQuery** определяет преобразование (проекцию), которое необходимо выполнить в источнике. Подробную информацию обо всех свойствах см. в [справочнике по сценариям JSON][json-script-reference].

         
		{
		    "name": "CopyActivityPipeline",
    		"properties":
    		{
				"description" : "This is a sample pipeline to copy data from SQL Server to Azure Blob",
        		"activities":
        		[
      				{
						"name": "CopyActivity",
						"description": "description", 
						"type": "CopyActivity",
						"inputs":  [ { "name": "MyOnPremTable"  } ],
						"outputs":  [ { "name": "MyAzureBlob" } ],
						"transformation":
	    				{
							"source":
							{
								"type": "SqlSource",
                    			"sqlReaderQuery": "select * from MyTable"
							},
							"sink":
							{
                        		"type": "BlobSink"
							}
	    				}
      				}
        		]
    		}
		}

См. [ссылку JSON конвейера](https://msdn.microsoft.com/library/dn834988.aspx) подробные сведения об элементах JSON для определения конвейера данных фабрики и [поддерживается источники и приемники](https://msdn.microsoft.com/library/dn894007.aspx) для свойства SqlSource (например: **sqlReaderQuery **в примере) и BlobSink. 


## Копирование данных из локальной файловой системы в Azure BLOB-объект
Действие копирования можно использовать для копирования файлов из локальной файловой системы (общих сетевых ресурсов Windows, Linux или локального сервера Windows) в BLOB-объект Azure. Это может быть Windows или Linux с Samba настроен. Шлюз управления данными должен устанавливаться на компьютере с ОС Windows, можно подключиться к узлу.

### Предположения
В этом примере предполагается следующее:

- **Узла** -имя сервера, на котором размещена файловая система: **\contoso**.
- **Папки** -имя папки, которая содержит входные файлы: **marketingcampaign\regionaldata\ {срез}, где секционированы файлы в папку с именем {срез}, например 2014121112 (2014 года, месяца, 12, день 11, 12 часов). 
### Создание службы локального файла связанные системы
Следующий пример JSON можно использовать для создания связанных с именем службы **FolderDataStore** типа **OnPremisesFileSystemLinkedService**.

	{
	    "name": "FolderDataStore",
	    "properties": {
	        "type": "OnPremisesFileSystemLinkedService",
	        "host": "\contoso",
	        "userId": "username",
	        "password": "password",
	        "gatewayName": "ContosoGateway"
	    }
	}

> [AZURE.NOTE]Помните, что необходимо использовать escape-символ '' для имен узлов и папок в файлы JSON. Для **\Contoso**, используйте **\Contoso**.

В разделе [связанного служба локальной файловой системы](https://msdn.microsoft.com/library/dn930836.aspx) подробные сведения об элементах JSON для определения в локальной файловой системе связанные службы.

### Создание связанной службы для назначения BLOB-объектов Azure
Следующий пример JSON можно использовать для создания связанных с именем службы **MyAzureStorage** типа **AzureStorageLinkedSerivce**.

	{
	    "name": "MyAzureStorage",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

В разделе [связанные службы хранилища Azure](https://msdn.microsoft.com/library/dn893522.aspx) подробные сведения об элементах JSON для определения хранилища Azure связанные службы.

### Создать входной таблицы
Следующий скрипт JSON определяет входную таблицу, которая ссылается на локальную службу файл связанные системы, созданный ранее.

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\{Slice}",
	            "partitionedBy": [
	                { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } }
	            ],
	            "linkedServiceName": "FolderDataStore"
	        },
	        "availability": {
	            "waitOnExternal": { },
	            "frequency": "Hour",
	            "interval": 24
	        }
	    }
	}

В разделе [Свойства расположения в локальной файловой системе](https://msdn.microsoft.com/library/dn894089.aspx#OnPremFileSystem) подробные сведения об элементах JSON для определения таблицы данных фабрики, относится к локальной файловой системе.

### Создать выходные данные могут
Следующий скрипт JSON определяет выходной таблицы: **AzureBlobDest**, которая относится к BLOB-объект Azure: **MyBlob** в папке BLOB-объектов: **MySubFolder** в контейнер больших двоичных объектов: **MyContainer**.
         
	{
   		"name": "AzureBlobDest",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "MyAzureStorage",
        		"format":
        		{
            		"type": "TextFormat",
            		"columnDelimiter": ",",
            		"rowDelimiter": ";",
             		"EscapeChar": "$",
             		"NullValue": "NaN"
        		}
    		},
        	"availability":
      		{
       			"frequency": "Hour",
       			"interval": 1
      		}
   		}
	}

В разделе [Свойства расположения BLOB-объектов Azure](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob) подробные сведения об элементах JSON для определения таблицы данных фабрики, ссылающийся на большой двоичный объект Azure.

### Создать конвейер
Следующий конвейер JSON определяет конвейера с копии действия, копирующего данные из локальной файловой системы назначения BLOB-объектов Azure.
 
	{
	    "name": "CopyFileToBlobPipeline",
	    "properties": {
	        "activities": [
	            {
	                "name": "Ingress",
	                "type": "CopyActivity",
	                "inputs": [ { "name": "OnPremFileSource" } ],
	                "outputs": [ { "name": "AzureBlobDest" } ],
	                "transformation": {
	                    "source": {
	                        "type": "FileSystemSource"
	                    },
	                    "sink": {
	                        "type": "BlobSink"
	                    }
	                },
	                "policy": {
	                    "concurrency": 4,
	                    "timeout": "00:05:00"
	                }
	            }
	        ]
	    }
	}

Конвейер в этом примере копирует содержимое как двоичный, без разбора или выполняя никаких преобразований. Обратите внимание, что можно использовать **параллелизма** копирование фрагменты файлов параллельно. Это полезно, если вы хотите переместить срезы уже произошло в прошлом.

> [AZURE.NOTE]Действия параллельных Копировать с одного узла с помощью UNC-путь с разными учетными записями пользователей могут привести к ошибкам, такие как «несколько подключений к серверу или общему ресурсу одним пользователем с использованием более одного имени пользователя, не разрешены». Это ограничение по соображениям безопасности операционной системы. Планирование действий копирования с различных шлюзах или установить шлюз в узле и использовать «localhost» или «local», а не в формате UNC.

См. [ссылку JSON конвейера](https://msdn.microsoft.com/library/dn834988.aspx) подробные сведения об элементах JSON для определения конвейера данных фабрики и [поддерживается источники и приемники](https://msdn.microsoft.com/library/dn894007.aspx) для свойства FileSystemSource и BlobSink.

### Дополнительные сценарии для использования файла системных таблиц

#### Скопируйте все файлы в определенной папке
Обратите внимание, что только **folderPath** указанную в образце JSON.

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\na",
	            "linkedServiceName": "FolderDataStore"
	        },
	        ...
	    }
	}
 
#### Скопируйте все файлы CSV в определенной папке
Обратите внимание, что **fileFilter** равен ***.csv**.

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\na",
	            "fileFilter": "*.csv",
	            "linkedServiceName": "FolderDataStore"
	        },
	        ...
	    }
	}

#### Скопируйте определенного файла
Обратите внимание, что **fileFiter** задано для определенного файла: **201501.csv**.

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\na",
	            "fileFilter": "201501.csv",
	            "linkedServiceName": "FolderDataStore"
	        },
	        ...
	    }
	}

## Копирование данных из локальной базы данных Oracle в Azure BLOB-объект
Действие копирования можно использовать для копирования файлов из локальной базы данных Oracle локально в BLOB-объект Azure.

### Создание связанной службы для локальной базы данных Oracle
Создание связанной службы, указывающий на локальную базу данных Oracle используется следующий JSON. Обратите внимание, что **тип** равен **OnPremisesOracleLinkedService**.

	{
	    "name": "OnPremOracleSource",
	    "properties": {
	        "type": "OnPremisesOracleLinkedService",
	        "ConnectionString": "data source=ds;User Id=uid;Password=pwd;",
	        "gatewayName": "SomeGateway"
	    }
	}

В разделе [локальную службу связанного Oracle](https://msdn.microsoft.com/library/dn948537.aspx) подробные сведения об элементах JSON для определения локальных Oracle связан службы.

### Создание связанной службы для назначения BLOB-объектов Azure
Следующий пример JSON можно использовать для создания связанных с именем службы **MyAzureStorage** типа **AzureStorageLinkedSerivce**.

	{
	    "name": "AzureBlobDest",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

В разделе [связанные службы хранилища Azure](https://msdn.microsoft.com/library/dn893522.aspx) подробные сведения об элементах JSON для определения хранилища Azure связанные службы.

### Создать входной таблицы
Следующий пример JSON используется для создания таблицы данных фабрики Azure, ссылается на таблицу в базе данных Oracle в локальной. Обратите внимание, что **тип расположения** равен **OnPremisesOracleTableLocation**.

	{
	    "name": "TableOracle",
	    "properties": {
	        "location": {
	            "type": "OnPremisesOracleTableLocation",
	            "tableName": "LOG",
	            "linkedServiceName": "OnPremOracleSource"
	        },
	        "availability": {
	            "frequency": "Day",
	            "interval": "1",
	            "waitOnExternal": {}
	        },
	        "policy": {}
	    }
	} 

В разделе [Свойства расположения локальной Oracle](https://msdn.microsoft.com/library/dn894089.aspx#Oracle) подробные сведения об элементах JSON для определения таблицы данных фабрики, ссылается на таблицу в базе данных Oracle в локальной.

### Создать выходную таблицу
Следующий скрипт JSON определяет выходной таблицы: **MyAzureBlob**, которая относится к BLOB-объект Azure: **MyBlob** в папке BLOB-объектов: **MySubFolder** в контейнер больших двоичных объектов: **MyContainer**.
         
	{
   		"name": "MyAzureBlob",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "AzureBlobDest",
        		"format":
        		{
            		"type": "TextFormat",
            		"columnDelimiter": ",",
            		"rowDelimiter": ";",
             		"EscapeChar": "$",
             		"NullValue": "NaN"
        		}
    		},
        	"availability":
      		{
       			"frequency": "Hour",
       			"interval": 1
      		}
   		}
	}

В разделе [Свойства расположения BLOB-объектов Azure](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob) подробные сведения об элементах JSON для определения таблицы данных фабрики, ссылающийся на большой двоичный объект Azure.

### Создать конвейер
Конвейер следующий пример использует действие копирования, которое копирует данные из таблицы базы данных Oracle в BLOB-объект хранилища Azure.

	{
	    "name": "PipelineCopyOracleToBlob",
	    "properties": {
	        "activities": [
	            {
	                "name": "CopyActivity",
	                "description": "copy slices of oracle records to azure blob",
	                "type": "CopyActivity",
	                "inputs": [ { "name": "TableOracle" } ],
	                "outputs": [ { "name": "TableAzureBlob" } ],
	                "transformation": {
	                    "source": {
	                        "type": "OracleSource",
	                        "oracleReaderQuery": "$$Text.Format('select * from LOG where "Timestamp" >= to_date('{0:yyyy-MM-dd}', 'YYYY-MM-DD') AND "Timestamp" < to_date('{1:yyyy-MM-dd}', 'YYYY-MM-DD')', SliceStart, SliceEnd)"
	                    },
	                    "sink": {
	                        "type": "BlobSink"
	                    }
	                },
	                "policy": {
	                    "concurrency": 3,
	                    "timeout": "00:05:00"
	                }
	            }
	        ],
	        "start": "2015-03-01T00:00:00Z",
	        "end": "2015-03-15T00:00:00Z",
	        "isPaused": false
	    }
	}

См. [ссылку JSON конвейера](https://msdn.microsoft.com/library/dn834988.aspx) подробные сведения об элементах JSON для определения конвейера данных фабрики и [поддерживается источники и приемники](https://msdn.microsoft.com/library/dn894007.aspx) для свойства OracleSource и BlobSink.

## См. также

- [Копирование данных с помощью фабрики данных Azure][adf-copyactivity]
- [Действие копирования. Справочник по скриптам JSON](https://msdn.microsoft.com/library/dn835035.aspx)
- [Основные сведения о копировании данных с помощью фабрики данных Azure][copy-activity-video] (видео)


[adf-copyactivity]: data-factory-copy-activity.md
[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/

<!---HONumber=GIT-SubDir-->