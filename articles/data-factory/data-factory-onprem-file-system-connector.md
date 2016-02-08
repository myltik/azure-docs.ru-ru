<properties 
	pageTitle="Перемещение данных в файловую систему или из нее | Фабрика данных Azure" 
	description="Узнайте, как переместить данные в локальную базу данных или из нее c помощью фабрики данных Azure." 
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
	ms.date="11/09/2015" 
	ms.author="spelluru"/>

# Перемещение данных в локальную файловую систему или из нее с помощью фабрики данных Azure

В этой статье описано использование действия копирования в фабрике данных Azure для перемещения данных из локальной файловой системы и обратно. Эта статья основана на статье о [действиях перемещения данных](data-factory-data-movement-activities.md), в которой приведены общие сведения о перемещении данных с помощью действия копирования и поддерживаемых сочетаниях хранилищ данных.

Фабрика данных поддерживает обмен данным с локальной файловой системой через шлюз управления данными. В статье [Перемещение данных между локальными и облачными ресурсами](data-factory-move-data-between-onprem-and-cloud.md) приведены сведения о шлюзе управления данными и пошаговые инструкции по его настройке.

> [AZURE.NOTE] 
Для обмена данными с файловой системой устанавливать какие-либо двоичные файлы, кроме шлюза управления данными, не требуется.
> 
> Советы по устранению неполадок, связанных с шлюзом или подключением, см. в разделе [Устранение неполадок шлюза](data-factory-move-data-between-onprem-and-cloud.md#gateway-troubleshooting).

## Файловый ресурс Linux 

Чтобы использовать файловый ресурс Linux со связанной службой файлового сервера, сделайте вот что:

- Установите [Samba](https://www.samba.org/) на сервер Linux.
- Установите и настройте шлюз управления данными на сервере Windows. Установка шлюза на сервер Linux не поддерживается. 
 
## Пример копирования данных из локальной файловой системы в хранилище BLOB-объектов Azure

В примере ниже показано следующее.

1.	Связанная служба типа [OnPremisesFileServer](data-factory-onprem-file-system-connector.md#onpremisesfileserver-linked-service-properties).
2.	Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Входной [набор данных](data-factory-create-datasets.md) типа [FileShare](data-factory-onprem-file-system-connector.md#on-premises-file-system-dataset-type-properties).
4.	Выходной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	[Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [FileSystemSource](data-factory-onprem-file-system-connector.md#file-share-copy-activity-type-properties) и [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties). 

В следующем примере данные из временного ряда каждый час копируются из локальной файловой системы в хранилище BLOB-объектов Azure. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

Сначала настройте шлюз управления данными. Инструкции, как это сделать, см. в статье [Перемещение данных между локальными и облачными ресурсами](data-factory-move-data-between-onprem-and-cloud.md).

**Связанная служба локального файлового сервера**

	{
	  "Name": "OnPremisesFileServerLinkedService",
	  "properties": {
	    "type": "OnPremisesFileServer",
	    "typeProperties": {
	      "host": "\\\Contosogame-Asia.<region>.corp.<company>.com",
	      "userid": "Admin",
	      "password": "123456",
	      "gatewayName": "mygateway"
	    }
	  }
	}

В качестве узла можно указать **Local** или **localhost**, если общая папка располагается непосредственно на компьютере шлюза. Также мы рекомендуем использовать свойство **encryptedCredential** вместо свойств **userid** и **password**. Подробные сведения о связанной службе файловой системы см. в [соответствующем разделе](#onpremisesfileserver-linked-service-properties).

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

**Входной набор данных локальной файловой системы**

Данные берутся из нового файла каждый час. Путь и имя файла отражают дату и время по часам.

Если для параметра external задать значение true и указать политику externalData, фабрика данных Azure будет считать эту таблицу внешней по отношению к себе и не созданной в результате какого-либо действия в фабрике данных.

	{
	  "name": "OnpremisesFileSystemInput",
	  "properties": {
	    "type": " FileShare",
	    "linkedServiceName": " OnPremisesFileServerLinkedService ",
	    "typeProperties": {
	      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
	      "folderPath": "mycontainer/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
	            "format": "%HH"
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

**Действие копирования**

Конвейер содержит действие копирования (Copy), которое использует входной и выходной наборы данных (см. выше) и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **FileSystemSource**, а для типа **sink** — значение **BlobSink**.
	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2015-06-01T18:00:00",
	    "end":"2015-06-01T19:00:00",
	    "description":"Pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "OnpremisesFileSystemtoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "OnpremisesFileSystemInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
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

##Пример копирования данных из Azure SQL в локальную файловую систему 

В примере ниже показано следующее.

1.	Связанная служба типа AzureSqlDatabase.
2.	Связанная служба типа OnPremisesFileServer.
3.	Входной набор данных типа AzureSqlTable. 
3.	Выходной набор данных типа FileShare.
4.	Конвейер с действием копирования, в котором используются SqlSource и FileSystemSink.

В этом примере данные из временного ряда каждый час копируются из таблицы в базе данных SQL Azure в локальную файловую систему. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

**Связанная служба SQL Azure**

	{
	  "name": "AzureSqlLinkedService",
	  "properties": {
	    "type": "AzureSqlDatabase",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Связанная служба локального файлового сервера**

	{
	  "Name": "OnPremisesFileServerLinkedService",
	  "properties": {
	    "type": "OnPremisesFileServer",
	    "typeProperties": {
	      "host": "\\\Contosogame-Asia.<region>.corp.<company>.com",
	      "userid": "Admin",
	      "password": "123456",
	      "gatewayName": "mygateway"
	    }
	  }
	}

В качестве узла можно указать **Local** или **localhost**, если общая папка располагается непосредственно на компьютере шлюза. Также мы рекомендуем использовать свойство **encryptedCredential** вместо свойств **userid** и **password**. Подробные сведения о связанной службе файловой системы см. в [соответствующем разделе](#onpremisesfileserver-linked-service-properties).

**Входной набор данных SQL Azure**

В примере предполагается, что таблица MyTable уже создана в SQL Azure и содержит столбец с именем timestampcolumn для данных временных рядов.

Если для параметра external задать значение true и указать политику externalData, фабрика данных будет считать эту таблицу внешней по отношению к себе и не созданной в результате какого-либо действия в фабрике данных.

	{
	  "name": "AzureSqlInput",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
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

**Выходной набор данных локальной файловой системы**

Данные копируются в новый файл каждый час. Путь к BLOB-объекту отражает дату и время по часам.

	{
	  "name": "OnpremisesFileSystemOutput",
	  "properties": {
	    "type": "FileShare",
	    "linkedServiceName": " OnPremisesFileServerLinkedService ",
	    "typeProperties": {
	      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
	            "format": "%HH"
	          }
	        }
	      ]
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

**Конвейер с действием копирования**. Конвейер содержит действие копирования (Copy), которое использует входной и выходной наборы данных (см. выше) и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **SqlSource**, а для типа **sink** — значение **FileSystemSink**. SQL-запрос, указанный для свойства **SqlReaderQuery**, выбирает для копирования данные за последний час.

	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2015-06-01T18:00:00",
	    "end":"2015-06-01T20:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "AzureSQLtoOnPremisesFile",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureSQLInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "OnpremisesFileSystemOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "SqlSource",
	            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
	          },
	          "sink": {
	            "type": "FileSystemSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 3,
	          "timeout": "01:00:00"
	        }
	      }
	     ]
	   }
	}

## Свойства связанной службы OnPremisesFileServer

Вы можете связать локальную файловую систему с фабрикой данных Azure при помощи связанной службы локального файлового сервера. В следующей таблице содержится описание элементов JSON, которые относятся к связанной службе локального файлового сервера.

Свойство | Описание | Обязательно
-------- | ----------- | --------
type | Свойству type необходимо присвоить значение **OnPremisesFileServer**. | Да 
host | Имя узла сервера. Используйте символ \\ в качестве escape-символа: если имя общей папки — \\servername, укажите \\\servername.<p>Если используется файловая система компьютера со шлюзом, укажите Local или localhost. Если используется файловая система другого компьютера, используйте \\\servername.</p> | Да
userid | Укажите идентификатор пользователя, имеющего доступ к серверу | Нет (если выбрать encryptedcredential)
пароль | Укажите пароль для учетной записи пользователя (userid) | Нет (если выбрать encryptedcredential) 
encryptedCredential | Укажите зашифрованные учетные данные, которые можно получить, выполнив командлет New-AzureRmDataFactoryEncryptValue.<p>**Примечание.** Чтобы использовать такие командлеты, как New-AzureRmDataFactoryEncryptValue с параметром type, имеющим значение OnPremisesFileSystemLinkedService, необходимо использовать Azure PowerShell, начиная с версии 0.8.14.</p> | Нет (если имя пользователя и пароль указываются в виде обычного текста)
gatewayName | Имя шлюза, который следует использовать службе фабрики данных для подключения к локальному файловому серверу. | Да

Дополнительную информацию о настройке учетных данных для локальной файловой системы в качестве источника данных см. в разделе [Настройка учетных данных и безопасность](data-factory-move-data-between-onprem-and-cloud.md#setting-credentials-and-security).

**Пример указания имени пользователя и пароля в виде обычного текста**
	
	{
	  "Name": "OnPremisesFileServerLinkedService",
	  "properties": {
	    "type": "OnPremisesFileServer",
	    "typeProperties": {
	      "host": "\\\Contosogame-Asia",
	      "userid": "Admin",
	      "password": "123456",
	      "gatewayName": "mygateway"
	    }
	  }
	}
	
**Пример использования encryptedcredential**

	{
	  "Name": " OnPremisesFileServerLinkedService ",
	  "properties": {
	    "type": "OnPremisesFileServer",
	    "typeProperties": {
	      "host": "localhost",
	      "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
	      "gatewayName": "mygateway"
	    }
	  }
	}

## Свойства типа "Набор данных локальной файловой системы"

Полный список разделов и свойств, используемых для определения наборов данных, см. в статье [Наборы данных](data-factory-create-datasets.md). Разделы, определяющие структуру, доступность и политику JSON набора данных, одинаковы для всех типов наборов данных (SQL Azure, BLOB-объекты Azure, таблицы Azure, локальные файловые системы и т. д.).

Раздел typeProperties во всех типах наборов данных разный. В нем содержатся сведения о расположении данных в хранилище данных, формате и т. д. Раздел typeProperties набора данных с типом **FileShare** имеет следующие свойства.

Свойство | Описание | Обязательно
-------- | ----------- | --------
folderPath | Путь к папке, например myfolder.<p>Чтобы указать специальные символы в строке, используйте escape-символ \\. Например: для пути folder\\subfolder укажите folder\\\subfolder, а для пути d:\\samplefolder укажите d:\\\samplefolder.</p><p>Чтобы получить пути с учетом даты и времени начала и окончания среза, используйте **partitionBy**.</p> | Да
fileName | Укажите имя файла в папке **folderPath**, если таблица должна ссылаться на определенный файл в папке. Если этому свойству не присвоить значение, таблица будет указывать на все файлы в папке.<p>Если свойство fileName не указано для выходного набора данных, имя созданного файла будет иметь следующий формат: </p><p>Data.<Guid>.txt (например, Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.</p> | Нет
partitionedBy | Чтобы указать для временного ряда данных динамические путь к папке и имя файла, используйте свойство partitionedBy. Например, путь к папке (folderPath) каждый час будет другим. | Нет
Формат | Поддерживаются два типа форматов: **TextFormat** и **AvroFormat**. Свойству type в разделе format необходимо присвоить одно из этих значений. Если для свойства Format указано значение TextFormat, для формата можно указать дополнительные свойства. Дополнительные сведения см. в разделе по форматам ниже. **Свойство "Формат" в настоящее время не поддерживается в локальных файловых системах. В ближайшее время оно будет реализовано в описанном здесь виде.** | Нет
fileFilter | Укажите фильтр для выбора подмножества файлов из folderPath. Фильтр дает возможность выбирать только некоторые файлы, а не все. <p>Допустимые значения: * (несколько символов) и ? (один символ).</p><p>Пример 1: "fileFilter": "*.log"</p>Пример 2: "fileFilter": 2014-1-?.txt"</p><p>**Примечание**. Свойство fileFilter применяется ко входному набору данных FileShare.</p> | Нет
| compression | Укажите тип и уровень сжатия данных. Поддерживаемые типы: GZip, Deflate и BZip2. Поддерживаемые уровни: Optimal и Fastest. Дополнительные сведения см. в разделе [Поддержка сжатия](#compression-support). | Нет |

> [AZURE.NOTE] Свойства filename и fileFilter нельзя использовать одновременно.

### Использование свойства partionedBy

Как сказано выше, для временных рядов данных путь к папке и имя файла можно указывать динамически. Это делается с помощью свойства partitionedBy. Вы можете это сделать при помощи макроса фабрики данных и системных переменных SliceStart и SliceEnd, которые определяют логический период имеющегося среза данных.

Дополнительные сведения о наборах данных временных рядов, планировании и срезах см. в статьях [Наборы данных](data-factory-create-datasets.md), [Планирование и исполнение с использованием фабрики данных](data-factory-scheduling-and-execution.md) и [Основные сведения о конвейерах и действиях](data-factory-create-pipelines.md).

#### Пример 1

	"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
	"partitionedBy": 
	[
	    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
	],

В примере выше {Slice} заменяется значением SliceStart (системная переменная фабрики данных) в формате YYYYMMDDHH. SliceStart указывает время начала среза. Значение folderPath отличается для каждого среза. Например: wikidatagateway/wikisampledataout/2014100103 или wikidatagateway/wikisampledataout/2014100104.

#### Пример 2

	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
	"fileName": "{Hour}.csv",
	"partitionedBy": 
	 [
	    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
	    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
	    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
	    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
	],

В примере выше год, месяц, день и время SliceStart извлекаются в отдельные переменные, используемые в свойствах folderPath и fileName.

### Определение TextFormat

Если для свойства format задано значение **TextFormat**, в подразделе **Format** раздела **typeProperties** можно указать приведенные ниже **необязательные** свойства.

Свойство | Описание | Обязательно
-------- | ----------- | --------
columnDelimiter | Один или несколько символов, используемых в качестве разделителя столбцов в файле. Значение по умолчанию — запятая (,). | Нет
rowDelimiter | Один или несколько символов, используемых в качестве необработанного разделителя в файле. По умолчанию используется одно из следующих значений: [\\r\\n, \\r, \\n]. | Нет
escapeChar | Специальный символ, используемый для экранирования разделителя столбцов в содержимом. Значение по умолчанию отсутствует. Для этого свойства следует указывать не более одного символа.<p>Например, если в качестве разделителя столбцов используется запятая и этот же символ встречается в тексте (например, «Hello, world»), назначьте $ escape-символом и используйте в исходном коде строку «Hello$, world».</p><p>Обратите внимание, что для таблицы нельзя одновременно указать свойства escapeChar и quoteChar.</p> | Нет
quoteChar | Специальный символ, который используется для заключения строкового значения в кавычки. Разделители столбцов и строк внутри кавычек будут рассматриваться как часть строкового значения. Значение по умолчанию отсутствует. Для этого свойства следует указывать не более одного символа.<p>Например, если в качестве разделителя столбцов используется запятая и этот же символ встречается в тексте (например, <Hello  world>), назначьте в качестве символа кавычек прямые кавычки (") и используйте в исходном коде строку <"Hello, world">. Это свойство применимо ко входным и выходным таблицам.</p><p>Обратите внимание, что для таблицы нельзя одновременно указать свойства escapeChar и quoteChar.</p> | Нет
nullValue | Один или несколько символов, используемых для обозначения нулевого значения в файле BLOB-объекта. Значение по умолчанию — \\N. | Нет
encodingName | Имя кодировки. Список допустимых имен кодировок см. в описании свойства Encoding.EncodingName. <p>Например: windows-1250 или shift\_jis. По умолчанию используется значение UTF-8.</p> | Нет

#### Примеры

В следующем примере показаны некоторые свойства формата для **TextFormat**.

	"typeProperties":
	{
	    "folderPath": "MyFolder",
	    "fileName": "MyFileName"
	    "format":
	    {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": ";",
	        "quoteChar": """,
	        "NullValue": "NaN"
	    }
	},

Чтобы использовать escapeChar вместо quoteChar, вместо строки с quoteChar укажите следующее:

	"escapeChar": "$",

### Определение AvroFormat

Если для свойства format выбрано значение **AvroFormat**, вам не нужно указывать какие-либо свойства в подразделе Format раздела typeProperties. Пример:

	"format":
	{
	    "type": "AvroFormat",
	}
	
Сведения об использовании формата Avro в последующей таблице Hive см. в [руководстве по Apache Hive](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## Свойства типа "Действие копирования общей папки"

**FileSystemSource** поддерживает следующие свойства:

| Свойство | Описание | Допустимые значения | Обязательно |
| -------- | ----------- | -------------- | -------- |
| recursive | Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. | True, False (по умолчанию)| Нет | 

**FileSystemSink** поддерживает следующие свойства:

| Свойство | Описание | Допустимые значения | Обязательно |
| -------- | ----------- | -------------- | -------- |
| copyBehavior | Это свойство определяет поведение функции копирования, когда в качестве источника используется BlobSource или FileSystem. | <p>Для свойства copyBehavior можно использовать три значения. </p><ul><li>**PreserveHierarchy:** сохраняет иерархии файлов в целевую папку, т. е. относительный путь исходного файла к исходной папке идентичен относительному пути целевого файла к целевой папке.</li><li>**FlattenHierarchy:** все файлы из исходной папки будут на первом уровне целевой папки. Целевые файлы будут иметь автоматически сформированное имя. </li></ul> | Нет |

### Примеры recursive и copyBehavior
В данном разделе описываются результаты выполнения операции копирования при использовании различных сочетаний значений recursive и copyBehavior.

recursive | copyBehavior | Результаты выполнения операции
--------- | ------------ | --------
Да | preserveHierarchy | <p>Для исходной папки «Папка1» со структурой</p> <p>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;ВложеннаяПапка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5</p>целевая папка «Папка1» будет иметь такую же структуру, как исходная папка<p>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;ВложеннаяПапка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5</p>.  
Да | flattenHierarchy | <p>Для исходной папки «Папка1» со структурой</p> <p>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;ВложеннаяПапка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5</p>целевая папка «Папка1» будет иметь структуру<p>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для файла «Файл1»<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для файла «Файл2»<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для файла «Файл3»<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для файла «Файл4»<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для файла «Файл5»</p>
Да | mergeFiles | <p>Для исходной папки «Папка1» со структурой</p> <p>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;ВложеннаяПапка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5</p>целевая папка «Папка1» будет иметь структуру <p>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Содержимое файлов «Файл1», «Файл2», «Файл3», «Файл4» и «Файл5» будет объединено в один файл с автоматически созданным именем файла.</p>
нет | preserveHierarchy | <p>Для исходной папки "Папка1" со структурой</p> <p>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;ВложеннаяПапка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5</p>целевая папка "Папка1" будет иметь структуру<p>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/></p><p>"ВложеннаяПапка1" с файлами "Файл3", "Файл4" и "Файл5" не будут включены в эту папку.</p>.
нет | flattenHierarchy | <p>Для исходной папки "Папка1" со структурой</p> <p>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;ВложеннаяПапка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5</p>целевая папка "Папка1" будет иметь структуру<p>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для файла "Файл1"<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для файла "Файл2"<br/></p><p>Папка "ВложеннаяПапка1" с файлами "Файл3", "Файл4" и "Файл5" не будет включена в эту папку.</p>
нет | mergeFiles | <p>Для исходной папки «Папка1» со структурой</p> <p>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;ВложеннаяПапка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5</p>целевая папка «Папка1» будет иметь структуру<p>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Содержимое файлов «Файл1» и «Файл2» будет объединено в один файл с автоматически созданным именем для файла «Файл1»</p><p>«ВложеннаяПапка1» с файлами «Файл3», «Файл4» и «Файл5» не будет включена в эту папку.</p>.


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]








 

<!---HONumber=AcomDC_0128_2016-->