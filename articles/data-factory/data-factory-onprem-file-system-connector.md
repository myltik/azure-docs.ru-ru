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
	ms.date="07/13/2016" 
	ms.author="spelluru"/>

# Перемещение данных в локальную файловую систему или из нее с помощью фабрики данных Azure

В этой статье описано использование действия копирования в фабрике данных Azure для перемещения данных из локальной файловой системы и обратно. Для получения списка хранилищ, которые можно использовать как источники и приемники с локальной файловой системой, см. раздел [Поддерживаемые хранилища данных](data-factory-data-movement-activities.md#supported-data-stores). Эта статья основана на статье о [действиях перемещения данных](data-factory-data-movement-activities.md), в которой приведены общие сведения о перемещении данных с помощью действия копирования и поддерживаемых сочетаниях хранилищ данных.

Фабрика данных поддерживает обмен данным с локальной файловой системой через шлюз управления данными. В статье [Перемещение данных между локальными и облачными ресурсами](data-factory-move-data-between-onprem-and-cloud.md) приведены сведения о шлюзе управления данными и пошаговые инструкции по его настройке.

> [AZURE.NOTE] 
Для обмена данными с файловой системой устанавливать какие-либо двоичные файлы, кроме шлюза управления данными, не требуется.
> 
> Советы по устранению неполадок, связанных со шлюзом или подключением, см. в разделе [Устранение неполадок в работе шлюза](data-factory-data-management-gateway.md#troubleshoot-gateway-issues).

## Файловый ресурс Linux 

Чтобы использовать файловый ресурс Linux со связанной службой файлового сервера, сделайте вот что:

- Установите [Samba](https://www.samba.org/) на сервер Linux.
- Установите и настройте шлюз управления данными на сервере Windows. Установка шлюза на сервер Linux не поддерживается.
 
## Пример копирования данных из локальной файловой системы в BLOB-объект Azure

В этом примере показано, как скопировать данные из локальной файловой системы в хранилище BLOB-объектов Azure. Однако данные можно **напрямую** копировать в любой указанный [здесь](data-factory-data-movement-activities.md#supported-data-stores) приемник. Это делается с помощью действия копирования в фабрике данных Azure.
 
Образец состоит из следующих сущностей фабрики данных.

- Связанная служба типа [OnPremisesFileServer](data-factory-onprem-file-system-connector.md#onpremisesfileserver-linked-service-properties).
- Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
- Входной [набор данных](data-factory-create-datasets.md) типа [FileShare](data-factory-onprem-file-system-connector.md#on-premises-file-system-dataset-type-properties).
- Выходной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- [Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [FileSystemSource](data-factory-onprem-file-system-connector.md#file-share-copy-activity-type-properties) и [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

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

##Пример копирования данных из SQL Azure в локальную файловую систему 

В примере ниже показано следующее.

- Связанная служба типа AzureSqlDatabase.
- Связанная служба типа OnPremisesFileServer.
- Входной набор данных типа AzureSqlTable.
- Выходной набор данных типа FileShare.
- Конвейер с действием копирования, в котором используются SqlSource и FileSystemSink.

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
host | Корневой путь папки, которую необходимо скопировать. Чтобы указать специальные знаки в строке, используйте escape-знак "\". Примеры см. в разделе [Примеры определений связанной службы и набора данных](#sample-linked-service-and-dataset-definitions) | Да
userid | Укажите идентификатор пользователя, имеющего доступ к серверу | Нет (если выбрать encryptedcredential)
пароль | Укажите пароль для учетной записи пользователя (userid) | Нет (если выбрать encryptedcredential) 
encryptedCredential | Укажите зашифрованные учетные данные, которые можно получить, выполнив командлет New-AzureRmDataFactoryEncryptValue.<br/><br/>**Примечание.** Чтобы использовать такие командлеты, как New-AzureRmDataFactoryEncryptValue, с параметром type, имеющим значение OnPremisesFileSystemLinkedService, необходимо использовать версию Azure PowerShell не старее 0.8.14. | Нет (если имя пользователя и пароль указываются в виде обычного текста)
gatewayName | Имя шлюза, который следует использовать службе фабрики данных для подключения к локальному файловому серверу. | Да

Дополнительную информацию о настройке учетных данных для локальной файловой системы в качестве источника данных см. в разделе [Настройка учетных данных и безопасность](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security).

### Примеры определений связанной службы и набора данных 
Сценарий | Размещение в определении связанной службы | Путь к файлу в определении набора данных
-------- | --------------------------------- | --------------------- |
Локальная папка на компьютере со шлюзом управления данными. <br/><br/>Например, "D:\\*" или "D:\\папка\\вложенная\_папка\"* | "D:\\" (для шлюза версии 2.0 и выше) <br/><br/> localhost (для шлюза версии ниже 2.0) | ".\\" или "папка\\\вложенная\_папка" (для шлюза версии 2.0 и выше) <br/><br/>"D:\\" или "D:\\\папка\\\вложенная\_папка" (для шлюза версии ниже 2.0)
Удаленная общая папка. <br/><br/>Например, "\\\myserver\\общая\_папка\"* или "\\\myserver\\общая\_папка\\папка\\вложенная\_папка\"* | "\\\\\\myserver\\\общая\_папка" | ".\\" или "папка\\\вложенная\_папка"

Чтобы узнать **версию** установленного шлюза, запустите на компьютере [диспетчер конфигурации шлюза управления данными](data-factory-data-management-gateway.md#data-management-gateway-configuration-manager) и перейдите на вкладку **Справка**.

> [AZURE.NOTE] Если в сценарии с локальной папкой указать для свойства host значение localhost, действие копирования все еще будет поддерживаться для любой версии шлюза, но вы не сможете использовать мастер копирования для настройки этого действия. Рекомендуется [обновить шлюз до версии 2.0 или выше](data-factory-data-management-gateway.md#update-data-management-gateway). После этого вы сможете использовать указанные выше новые конфигурации в инструменте JSON и мастере копирования, чтобы ваш сценарий работал.

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
folderPath | Подпуть к папке. Чтобы указать специальные знаки в строке, используйте escape-знак "\". Примеры см. в разделе [Примеры определений связанной службы и набора данных](#sample-linked-service-and-dataset-definitions).<br/><br/>Можно задать параметр **partitionBy**, чтобы в путях к папкам учитывалась дата и время начала и окончания среза. | Да
fileName | Укажите имя файла в папке **folderPath**, если таблица должна ссылаться на определенный файл в папке. Если этому свойству не присвоить значение, таблица будет указывать на все файлы в папке.<br/><br/>Если свойство fileName не указано для выходного набора данных, имя созданного файла будет иметь следующий формат: <br/><br/>Data.<GUID>.txt (например, Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt). | Нет
partitionedBy | Чтобы указать для временного ряда данных динамические путь к папке и имя файла, используйте свойство partitionedBy. Например, путь к папке (folderPath) каждый час будет другим. | Нет
Формат | Поддерживаются следующие типы формата: **TextFormat**, **AvroFormat**, **JsonFormat** и **OrcFormat**. Свойству **type** в разделе format необходимо присвоить одно из этих значений. Дополнительные сведения см. в разделах [Определение TextFormat](#specifying-textformat), [Определение AvroFormat](#specifying-avroformat), [Определение JsonFormat](#specifying-jsonformat) и [Определение OrcFormat](#specifying-orcformat). Если требуется скопировать файлы между файловыми хранилищами "как есть" (двоичное копирование), можно пропустить раздел форматирования в определениях входного и выходного наборов данных. | Нет
fileFilter | Укажите фильтр для выбора подмножества файлов из folderPath. Фильтр дает возможность выбирать только некоторые файлы, а не все. <br/><br/>Допустимые значения: * (несколько знаков) и ? (один знак).<br/><br/>Пример 1: "fileFilter": "*.log"<br/>Пример 2: "fileFilter": "2014-1-?.txt"<br/><br/>**Примечание**. Свойство fileFilter применяется ко входному набору данных FileShare. | Нет
| compression | Укажите тип и уровень сжатия данных. Поддерживаемые типы: **GZip**, **Deflate** и **BZip2**. Поддерживаемые уровни: **Optimal** и **Fastest**. Обратите внимание, что сейчас для данных в формате **AvroFormat** или **OrcFormat** параметры сжатия не поддерживаются. Дополнительные сведения см. в разделе [Поддержка сжатия](#compression-support). | Нет |

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

[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]  
[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## Свойства типа "Действие копирования общей папки"

**FileSystemSource** поддерживает следующие свойства:

| Свойство | Описание | Допустимые значения | Обязательно |
| -------- | ----------- | -------------- | -------- |
| recursive | Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. | True, False (по умолчанию)| Нет | 

**FileSystemSink** поддерживает следующие свойства:

| Свойство | Описание | Допустимые значения | Обязательно |
| -------- | ----------- | -------------- | -------- |
| copyBehavior | Это свойство определяет поведение функции копирования, когда в качестве источника используется BlobSource или FileSystem. | **PreserveHierarchy**: сохраняет иерархию файлов в целевой папке, т. е. относительный путь каждого целевого файла в целевой папке будет совпадать с относительным путем соответствующего исходного файла в исходной папке. <br/><br/>**FlattenHierarchy**: все файлы из исходной папки копируются на первый уровень целевой папки. Имена целевых файлов будут генерироваться автоматически.<br/><br/>**MergeFiles**: объединяет все файлы из исходной папки в один файл. Если указано имя файла или большого двоичного объекта, именем объединенного файла будет указанное имя; в противном случае имя файла будет автоматически сформировано. | Нет |

### Примеры recursive и copyBehavior
В данном разделе описываются результаты выполнения операции копирования при использовании различных сочетаний значений recursive и copyBehavior.

recursive | copyBehavior | Результаты выполнения операции
--------- | ------------ | --------
Да | preserveHierarchy | Для исходной папки "Папка1" со следующей структурой:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;ВложеннаяПапка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5<br/><br/>у целевой папки "Папка1" будет такая же структура, что и у исходной<br/><br/>>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;ВложеннаяПапка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5  
Да | flattenHierarchy | Для исходной папки "Папка1" со следующей структурой:<br/><br/>Папка1<br/> &nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;ВложеннаяПапка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 <br/><br/> у целевой папки "Папка1" будет следующая структура: <br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для файла "Файл1"<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для файла "Файл2"<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для файла "Файл3"<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для файла "Файл4"<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для файла "Файл5"
Да | mergeFiles | Для исходной папки "Папка1" со следующей структурой:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;ВложеннаяПапка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5<br/><br/>у целевой папки "Папка1" будет следующая структура: <br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Содержимое файлов "Файл1", "Файл2", "Файл3", "Файл4" и "Файл5" будет объединено в один файл с автоматически созданным именем файла<
нет | preserveHierarchy | Для исходной папки "Папка1" со следующей структурой:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;ВложеннаяПапка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 <br/><br/> у целевой папки "Папка1" будет следующая структура:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/><br/>Папка "ВложеннаяПапка1" с файлами "Файл3", "Файл4" и "Файл5" не будут включены в эту папку.
нет | flattenHierarchy | Для исходной папки "Папка1" со следующей структурой:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;ВложеннаяПапка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 <br/><br/> у целевой папки "Папка1" будет следующая структура:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для файла "Файл1"<br/>&nbsp;&nbsp;&nbsp;&nbsp;автоматически созданное имя для файла "Файл2"<br/><br/> Папка "ВложеннаяПапка1" с файлами "Файл3", "Файл4" и "Файл5" не будет включена в эту папку.<
нет | mergeFiles | Для исходной папки "Папка1" со следующей структурой:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;ВложеннаяПапка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 <br/><br/> у целевой папки "Папка1" будет следующая структура:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Содержимое файлов "Файл1" и "Файл2" будет объединено в один файл с автоматически созданным именем для файла "Файл1"<br/><br/>Папка "ВложеннаяПапка1" с файлами "Файл3", "Файл4" и "Файл5" не будет включена в эту папку.


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## Производительность и настройка  
См. [руководство по настройке производительности действия копирования](data-factory-copy-activity-performance.md), в котором описываются ключевые факторы, влияющие на производительность перемещения данных (действие копирования) в фабрике данных Azure, и различные способы оптимизации этого процесса.






 

<!---HONumber=AcomDC_0803_2016-->