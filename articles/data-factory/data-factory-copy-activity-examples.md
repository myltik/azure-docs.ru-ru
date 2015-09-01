<properties 
	pageTitle="Примеры использования действия копирования в фабрике данных Azure" 
	description="Предоставлены примеры использования действия копирования в фабрике данных Azure." 
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
	ms.date="08/03/2015" 
	ms.author="spelluru"/>

# Примеры использования действия копирования в фабрике данных Azure
С помощью **действия копирования** в конвейере вы можете выполнять пакетное копирование данных из источника в приемник (место назначения). В этом разделе приведено несколько примеров использования действия копирования в конвейере фабрики данных.

## Копирование данных из локальной базы данных SQL Server в большой двоичный объект Azure
В этом примере входная и выходная таблицы определяются и используются в действии копирования внутри конвейера, копирующего данные из локальной базы данных SQL Server в большой двоичный объект Azure.

### Предположения
В этом примере предполагается, что у вас уже есть следующие артефакты фабрики данных Azure:

* группа ресурсов с именем **ADF**;
* фабрика данных Azure с именем **CopyFactory**;
* созданный шлюз управления данными **mygateway**, доступный в сети.  

### Создание связанной службы SQL Server в исходной локальной базе данных
На этом шаге вы создаете связанную службу **MyOnPremisesSQLDB**, указывающий на локальную базу данных SQL Server.

	{
	  "name": "MyOnPremisesSQLDB",
	  "properties": {
	    "type": "OnPremisesSqlServer",
	    "typeProperties": {
	      "connectionString": "Data Source=<servername>;Initial Catalog=<database>;Integrated Security=False;User ID=<username>;Password=<password>;",
	      "gatewayName": "mygateway"
	    }
	  }
	}

Обратите внимание на следующее.

- параметр **type** имеет значение **OnPremisesSqlServer**;
- **connectionString** присвоена строка подключения к базе данных SQL Server. 
- **gatewayName** присвоено имя шлюза управления данными, установленного на локальном компьютере и зарегистрированного на портале службы фабрики данных Azure. 

Подробные сведения об элементах JSON для определения локальной связанной службы SQL см. в разделе [Локальная связанная служба SQL](https://msdn.microsoft.com/library/dn893523.aspx).
 
### Создание связанной службы для целевого большого двоичного объекта Azure
На этом шаге создается связанная служба с именем **MyAzureStorage**, которая указывает на хранилище больших двоичных объектов Azure.

	{
	  "name": "MyAzureStorage",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

Обратите внимание на следующее.

- параметр **type** имеет значение **AzureBlob**;
- в параметре **connectionString** укажите имя и ключ учетной записи для службы хранилища Azure.

Подробные сведения об элементах JSON для определения связанной службы хранилища Azure см. в разделе [Связанная служба хранилища Azure](https://msdn.microsoft.com/library/dn893522.aspx).

### JSON входной таблицы
Следующий скрипт JSON определяет входную таблицу, которая ссылается на таблицу SQL **MyTable** в локальной базе данных SQL Server, которую определяет связанная служба **MyOnPremisesSQLDB**. Обратите внимание, что **name** — это имя таблицы фабрики данных Azure, а **tableName** — это имя таблицы SQL в базе данных SQL Server.

	         
	{
	  "name": "MyOnPremTable",
	  "properties": {
	    "type": "SqlServerTable",
	    "linkedServiceName": "MyOnPremisesSQLDB",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

Обратите внимание на следующее.

- параметр **type** имеет значение **SqlServerTable**.
- в параметре **tableName** задана таблица **MyTable**, которая содержит исходные данные; 
- в параметре **linkedServiceName** задана связанная служба **MyOnPremisesSQLDB**, созданная для локальной базы данных SQL.

Подробные сведения об элементах JSON для определения таблицы фабрики данных, ссылающейся на таблицу SQL Server, см. в разделе [Элемент TypeProperties: Локальная база данных SQL Server](https://msdn.microsoft.com/library/mt185722.aspx#OnPremSQL) .

### JSON выходной таблицы
Следующий скрипт JSON определяет выходную таблицу **MyAzureBlob**, которая ссылается на большой двоичный объект Azure **MyBlob** в папке больших двоичных объектов **MySubFolder** в контейнере больших двоичных объектов **MyContainer**.
         
	{
	  "name": "MyAzureBlob",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "MyAzureStorage",
	    "typeProperties": {
	      "folderPath": "MyContainer/MySubFolder",
	      "fileName": "MyBlob",
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": ";",
	        "EscapeChar": "$",
	        "NullValue": "NaN"
	      }
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

Обратите внимание на следующее.
 
- для параметра **type** задано значение **AzureBlob**;
- в параметре **folderPath** задан объект **MyContainer/MySubFolder**, который содержит большой двоичный объект, содержащий скопированные данные; 
- в параметре **fileName** задан большой двоичный объект **MyBlob**, который будет содержать выходные данные;
- в параметре **linkedServiceName** задана связанная служба **MyAzureStorge**, созданная для хранилища Azure.    

Подробные сведения об элементах JSON для определения таблицы фабрики данных, ссылающейся на большой двоичный объект Azure, см. в разделе [Элемент TypeProperties: BLOB-объект Azure](https://msdn.microsoft.com/library/mt185722.aspx#AzureBlob).

### JSON конвейера (с действием копирования)
В этом примере конвейер **CopyActivityPipeline** определяют следующие свойства:

- свойству **type** присваивается значение **CopyActivity**;
- В качестве входных данных (тэг **inputs**) указывается свойство **MyOnPremTable**;
- Направлением передачи выходных данных (тэг **outputs**) указывается свойство **MyAzureBlob**.
- В разделе **Transformation** есть два подраздела: **source** и **sink**. Для источника (source) задан тип **SqlSource**, а для приемника (sink) — тип **BlobSink**. **sqlReaderQuery** определяет преобразование (проекцию), которое необходимо выполнить в источнике. Подробную информацию обо всех свойствах см. в [справочнике по сценариям JSON](https://msdn.microsoft.com/library/dn835050.aspx).

         
		{
		  "name": "CopyActivityPipeline",
		  "properties": {
		    "description": "This is a sample pipeline to copy data from SQL Server to Azure Blob",
		    "activities": [
		      {
		        "name": "CopyActivity",
		        "description": "description",
		        "type": "Copy",
		        "inputs": [
		          {
		            "name": "MyOnPremTable"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "MyAzureBlob"
		          }
		        ],
		        "typeProperties": {
		          "source": {
		            "type": "SqlSource",
		            "sqlReaderQuery": "select * from MyTable"
		          },
		          "sink": {
		            "type": "BlobSink"
		          }
		        }
		      }
		    ]
		  }
		}

В [справочнике по JSON конвейера](https://msdn.microsoft.com/library/dn834988.aspx) вы можете найти сведения об элементах JSON для определения конвейера фабрики данных, а раздел [Поддерживаемые источники и приемники](https://msdn.microsoft.com/library/dn894007.aspx) содержит информацию о свойствах SqlSource (например: **sqlReaderQuery ** в приведенном примере) и BlobSink.


## Копирование данных из локальной файловой системы в большой двоичный объект Azure
Действие копирования можно использовать для копирования файлов из локальной файловой системы (общих сетевых ресурсов Windows и Linux или локального узла Windows) в большой двоичный объект Azure. Это может быть узел Windows или Linux с настройкой Samba. Шлюз управления данными должен устанавливаться на компьютере с ОС Windows, которая может подключиться к узлу.

### Предположения
В этом примере предполагается следующее:

- **Host** — имя сервера, на котором размещена файловая система: **\\contoso**.
- **Folder** — имя папки, которая содержит входные файлы: **marketingcampaign\\regionaldata\\{срез}, где файлы секционированы в папке с именем {срез}, например 2014121112 (год 2014, месяц 12, день 11, час 12).

### Создание связанной службы локальной файловой системы
Следующий пример JSON можно использовать для создания связанной службы с именем **FolderDataStore** и типом **OnPremisesFileServer**.

	{
	  "name": "FolderDataStore",
	  "properties": {
	    "type": "OnPremisesFileServer",
	    "typeProperties": {
	      "host": "\\\\contoso",
	      "userId": "username",
	      "password": "password",
	      "gatewayName": "ContosoGateway"
	    }
	  }
	}

> [AZURE.NOTE] Помните, что необходимо использовать escape-символ '\' для имен узлов и папок в файлы JSON. Для **\\Contoso** укажите **\\\\Contoso**.

Подробные сведения об элементах JSON для определения связанной службы локальной файловой системы см. в разделе [Связанная служба локальной файловой системы](https://msdn.microsoft.com/library/dn930836.aspx).

### Создание связанной службы для целевого большого двоичного объекта Azure
Следующий пример JSON можно использовать для создания связанной службы с именем **MyAzureStorage** и типом **AzureStorageLinkedSerivce**.

	{
	  "name": "MyAzureStorage",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

Подробные сведения об элементах JSON для определения связанной службы хранилища Azure см. в разделе [Связанная служба хранилища Azure](https://msdn.microsoft.com/library/dn893522.aspx).

### Создание входной таблицы
Следующий скрипт JSON определяет входную таблицу, которая ссылается на связанную службу локальной файловой системы, созданную ранее.

	{
	  "name": "OnPremFileSource",
	  "properties": {
	    "type": "FileShare",
	    "linkedServiceName": "FolderDataStore",
	    "typeProperties": {
	      "folderPath": "marketingcampaign\\regionaldata\\{Slice}",
	      "partitionedBy": [
	        {
	          "name": "Slice",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyyMMddHH"
	          }
	        }
	      ]
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 24
	    }
	  }
	}

Подробные сведения об элементах JSON для определения таблицы фабрики данных, ссылающейся на локальную файловую систему, см. в разделе [Элемент TypeProperties: локальная файловая система](https://msdn.microsoft.com/library/mt185722.aspx#OnPremFileSystem).

### Создание выходной таблицы
Следующий скрипт JSON определяет выходную таблицу **AzureBlobDest**, которая ссылается на большой двоичный объект Azure **MyBlob** в папке больших двоичных объектов **MySubFolder** в контейнере больших двоичных объектов **MyContainer**.
         
	{
	  "name": "AzureBlobDest",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "MyAzureStorage",
	    "typeProperties": {
	      "folderPath": "MyContainer/MySubFolder",
	      "fileName": "MyBlob",
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": ";",
	        "EscapeChar": "$",
	        "NullValue": "NaN"
	      }
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

Подробные сведения об элементах JSON для определения таблицы фабрики данных, ссылающейся на большой двоичный объект Azure, см. в разделе [Элемент TypeProperties: BLOB-объект Azure](https://msdn.microsoft.com/library/mt185722.aspx#AzureBlob).

### Создание конвейера
Следующий JSON определяет конвейер с действием копирования, которое копирует данные из локальной файловой системы в целевой большой двоичный объект Azure.
 
	{
	  "name": "CopyFileToBlobPipeline",
	  "properties": {
	    "activities": [
	      {
	        "name": "Ingress",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "OnPremFileSource"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobDest"
	          }
	        ],
	        "typeProperties": {
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

Конвейер в этом примере копирует содержимое в двоичном формате, без синтаксического анализа и без выполнения каких-либо преобразований. Обратите внимание, что можно использовать **параллелизм** для параллельного копирования срезов файлов. Это удобно, когда необходимо переместить срезы, уже произошедшие в прошлом.

> [AZURE.NOTE] Параллельные действия копирования с одним и тем же узлом с помощью UNC-пути с разными учетными записями пользователей могут привести к ошибкам, таким как «Множественное подключение к серверу или к общим ресурсам одним пользователем с использованием более одного имени пользователя не разрешено». Это ограничение операционной системы по соображениям безопасности. Запланируйте действия копирования с разными шлюзами или установите шлюз в узле и используйте localhost или local вместо UNC-пути.

В [справочнике по JSON конвейера](https://msdn.microsoft.com/library/dn834988.aspx) см. сведения об элементах JSON для определения конвейера фабрики данных, а в разделе [Поддерживаемые источники и приемники](https://msdn.microsoft.com/library/dn894007.aspx) см. свойства FileSystemSource и BlobSink.

### Дополнительные сценарии использования таблиц файловой системы

#### Копирование всех файлов в определенную папку
Обратите внимание, что в примере JSON указывается только **folderPath**.

	"typeProperties": {
		"folderPath": "marketingcampaign\\regionaldata\\na",
	}
 
#### Копирование всех CSV-файлов в определенную папку
Обратите внимание, что для **fileFilter** задано значение ****.csv**.

    "typeProperties": {
        "folderPath": "marketingcampaign\\regionaldata\\na",
        "fileFilter": "*.csv",
    }

#### Копирование определенного файла
Обратите внимание, что в **fileFiter** задан определенный файл: **201501.csv**.

    "typeProperties": {
        "folderPath": "marketingcampaign\\regionaldata\\na",
        "fileFilter": "201501.csv",
    }

## Копирование данных из локальной базы данных Oracle в большой двоичный объект Azure
Действие копирования можно использовать для копирования файлов из локальной базы данных Oracle в большой двоичный объект Azure.

### Создание связанной службы для локальной базы данных Oracle
Для создания связанной службы, указывающей на локальную базу данных Oracle, используется следующий JSON. Обратите внимание, что параметр **type** имеет значение **OnPremisesOracle**.

	{
	    "name": "OnPremOracleSource",
	    "properties": {
	        "type": "OnPremisesOracle",
			"typeProperties": {			
	        	"ConnectionString": "data source=ds;User Id=uid;Password=pwd;",
	        	"gatewayName": "SomeGateway"	
			}
	    }
	}

Подробные сведения об элементах JSON для определения связанной службы локальной базы данных Oracle см. в разделе [Связанная служба локальной базы данных Oracle](https://msdn.microsoft.com/library/dn948537.aspx).

### Создание связанной службы для целевого большого двоичного объекта Azure
Следующий пример JSON можно использовать для создания связанной службы с именем **MyAzureStorage** и типом **AzureStorage**.

	{
	    "name": "AzureBlobDest",
	    "properties":
	    {
	        "type": "AzureStorage",
			"typeProperties": {
	        	"connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
			}
	    }
	}

Подробные сведения об элементах JSON для определения связанной службы хранилища Azure см. в разделе [Связанная служба хранилища Azure](https://msdn.microsoft.com/library/dn893522.aspx).

### Создание входной таблицы
Следующий пример JSON можно использовать для создания таблицы фабрики данных Azure, которая ссылается на таблицу в локальной базе данных Oracle. Обратите внимание, что параметр **type** имеет значение **OracleTable**.

	{
	  "name": "TableOracle",
	  "properties": {
	    "type": "OracleTable",
	    "linkedServiceName": "OnPremOracleSource",
	    "typeProperties": {
	      "tableName": "LOG"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": "1"
	    },
	    "policy": {}
	  }
	}

Подробные сведения об элементах JSON для определения таблицы фабрики данных, ссылающейся на локальную таблицу Oracle, см. в разделе [Элемент TypeProperties: локальная база данных Oracle](https://msdn.microsoft.com/library/mt185722.aspx#Oracle) .

### Создание выходной таблицы
Следующий скрипт JSON определяет выходную таблицу **MyAzureBlob**, которая ссылается на большой двоичный объект Azure **MyBlob** в папке больших двоичных объектов **MySubFolder** в контейнере больших двоичных объектов **MyContainer**.
         
	{
	  "name": "MyAzureBlob",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "AzureBlobDest",
	    "typeProperties": {
	      "folderPath": "MyContainer/MySubFolder",
	      "fileName": "MyBlob",
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": ";",
	        "EscapeChar": "$",
	        "NullValue": "NaN"
	      }
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

Подробные сведения об элементах JSON для определения таблицы фабрики данных, ссылающейся на большой двоичный объект Azure, см. в разделе [Элемент TypeProperties: BLOB-объект Azure](https://msdn.microsoft.com/library/mt185722.aspx#AzureBlob).

### Создание конвейера
В следующем примере конвейера используется действие копирования, которое копирует данные из таблицы базы данных Oracle в большой двоичный объект службы хранилища Azure.
	
	{
	  "name": "PipelineCopyOracleToBlob",
	  "properties": {
	    "activities": [
	      {
	        "name": "CopyActivity",
	        "description": "copy slices of oracle records to azure blob",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "TableOracle"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "TableAzureBlob"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "OracleSource",
	            "oracleReaderQuery": "$$Text.Format('select * from LOG where "Timestamp" >= to_date(\\'{0:yyyy-MM-dd}\\', \\'YYYY-MM-DD\\') AND "Timestamp" < to_date(\\'{1:yyyy-MM-dd}\\', \\'YYYY-MM-DD\\')', WindowStart, WindowEnd)"
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

В [справочнике по JSON конвейера](https://msdn.microsoft.com/library/dn834988.aspx) см. сведения об элементах JSON для определения конвейера фабрики данных, а в разделе [Поддерживаемые источники и приемники](https://msdn.microsoft.com/library/dn894007.aspx) см. свойства OracleSource и BlobSink.

## См. также

- [Действие копирования. Справочник по скриптам JSON](https://msdn.microsoft.com/library/dn835035.aspx)
- [Основные сведения о копировании данных с помощью фабрики данных Azure][copy-activity-video] (видео)


[adf-copyactivity]: data-factory-copy-activity.md
[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/

<!----HONumber=August15_HO7-->