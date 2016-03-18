<properties 
	pageTitle="Перемещение данных в хранилище данных DocumentDB и из него | Фабрика данных Azure" 
	description="Узнайте, как переместить данные в коллекцию Azure DocumentDB и из нее с помощью фабрики данных Azure." 
	services="data-factory, documentdb" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="multiple" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/24/2016" 
	ms.author="spelluru"/>

# Перемещение данных в хранилище данных DocumentDB и из него с помощью фабрики данных Azure

В этой статье рассказывается, как с помощью действия копирования в фабрике данных Azure можно перемещать данные в Azure DocumentDB из другого хранилища данных и обратно. Эта статья основана на статье о [действиях перемещения данных](data-factory-data-movement-activities.md), в которой приведены общие сведения о перемещении данных с помощью действия копирования и поддерживаемых сочетаниях хранилищ данных.

В следующих примерах показано, как копировать данные в службу Azure DocumentDB и хранилище BLOB-объектов Azure и обратно. Однако данные можно **напрямую** копировать из любых источников в любой указанный [здесь](data-factory-data-movement-activities.md#supported-data-stores) приемник. Это делается с помощью действия копирования в фабрике данных Azure.


## Пример копирования данных из базы данных DocumentDB в BLOB-объект Azure

В примере ниже показано следующее.

1. Связанная служба типа [DocumentDb](#azure-documentdb-linked-service-properties).
2. Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties). 
3. Входной набор данных [dataset](data-factory-create-datasets.md) типа [DocumentDbCollection](#azure-documentdb-dataset-type-properties). 
4. Выходной набор данных [dataset](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Конвейер [pipeline](data-factory-create-pipelines.md) с действием копирования, в котором используются [DocumentDbCollectionSource](#azure-documentdb-copy-activity-type-properties) и [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

В этом примере данные копируются из Azure DocumentDB в BLOB-объект Azure. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

**Связанная служба Azure DocumentDB**

	{
	  "name": "DocumentDbLinkedService",
	  "properties": {
	    "type": "DocumentDb",
	    "typeProperties": {
	      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
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

**Входной набор данных Azure Document DB**

В этом примере предполагается, что в используемой базе данных Azure DocumentDB есть коллекция **Person**.
 
Если для параметра external задать значение true и указать политику externalData, фабрика данных Azure будет считать эту таблицу внешней по отношению к себе и не созданной в результате какого-либо действия в фабрике данных.

	{
	  "name": "PersonDocumentDbTable",
	  "properties": {
	    "type": "DocumentDbCollection",
	    "linkedServiceName": "DocumentDbLinkedService",
	    "typeProperties": {
	      "collectionName": "Person"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}


**Выходной набор данных BLOB-объекта Azure**

Данные копируются в новый BLOB-объект каждый час. Путь к BLOB-объекту отражает дату и время по часам.

	{
	  "name": "PersonBlobTableOut",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "docdb",
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "nullValue": "NULL"
	      }
	    },
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

Образец JSON-документа в коллекции Person в базе данных DocumentDB

	{
	  "PersonId": 2,
	  "Name": {
	    "First": "Jane",
	    "Middle": "",
	    "Last": "Doe"
	  }
	}

DocumentDB поддерживает выполнение запросов к документам, используя для обработки иерархических JSON-документов синтаксис наподобие SQL.

Пример: SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person

В конвейере ниже данные копируются из коллекции Person, находящейся в базе данных DocumentDB, в BLOB-объект Azure. Для действия копирования указаны входные и выходные наборы данных.
	
	{
	  "name": "DocDbToBlobPipeline",
	  "properties": {
	    "activities": [
	      {
	        "type": "Copy",
	        "typeProperties": {
	          "source": {
	            "type": "DocumentDbCollectionSource",
	            "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
	            "nestingSeparator": "."
	          },
	          "sink": {
	            "type": "BlobSink",
	            "blobWriterAddHeader": true,
	            "writeBatchSize": 1000,
	            "writeBatchTimeout": "00:00:59"
	          }
	        },
	        "inputs": [
	          {
	            "name": "PersonDocumentDbTable"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "PersonBlobTableOut"
	          }
	        ],
	        "policy": {
	          "concurrency": 1
	        },
	        "name": "CopyFromDocDbToBlob"
	      }
	    ],
	    "start": "2015-04-01T00:00:00Z",
	    "end": "2015-04-02T00:00:00Z"
	  }
	}

## Пример копирования данных из хранилища BLOB-объектов Azure в базу данных Azure DocumentDB

В примере ниже показано следующее.

1. Связанная служба типа [DocumentDb](#azure-documentdb-linked-service-properties).
2. Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3. Входной набор данных [dataset](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Выходной набор данных [dataset](data-factory-create-datasets.md) типа [DocumentDbCollection](#azure-documentdb-dataset-type-properties). 
4. Конвейер [pipeline](data-factory-create-pipelines.md) с действием копирования, в котором используются [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) и [DocumentDbCollectionSink](#azure-documentdb-copy-activity-type-properties).


В этом примере данные копируются из BLOB-объекта Azure в базу данных Azure DocumentDB. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

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

**Связанная служба Azure DocumentDB**
	
	{
	  "name": "DocumentDbLinkedService",
	  "properties": {
	    "type": "DocumentDb",
	    "typeProperties": {
	      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
	    }
	  }
	}

**Входной набор данных BLOB-объекта Azure**

	{
	  "name": "PersonBlobTableIn",
	  "properties": {
	    "structure": [
	      {
	        "name": "Id",
	        "type": "Int"
	      },
	      {
	        "name": "FirstName",
	        "type": "String"
	      },
	      {
	        "name": "MiddleName",
	        "type": "String"
	      },
	      {
	        "name": "LastName",
	        "type": "String"
	      }
	    ],
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "fileName": "input.csv",
	      "folderPath": "docdb",
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "nullValue": "NULL"
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

**Выходной набор данных Azure DocumentDB**

В примере данные копируются в коллекцию Person.

	{
	  "name": "PersonDocumentDbTableOut",
	  "properties": {
	    "structure": [
	      {
	        "name": "Id",
	        "type": "Int"
	      },
	      {
	        "name": "Name.First",
	        "type": "String"
	      },
	      {
	        "name": "Name.Middle",
	        "type": "String"
	      },
	      {
	        "name": "Name.Last",
	        "type": "String"
	      }
	    ],
	    "type": "DocumentDbCollection",
	    "linkedServiceName": "DocumentDbLinkedService",
	    "typeProperties": {
	      "collectionName": "Person"
	    },
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

В приведенном ниже конвейере данные копируются из BLOB-объекта Azure в коллекцию Person, находящуюся в DocumentDB. Для действия копирования указаны входные и выходные наборы данных.
	
	{
	  "name": "BlobToDocDbPipeline",
	  "properties": {
	    "activities": [
	      {
	        "type": "Copy",
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource"
	          },
	          "sink": {
	            "type": "DocumentDbCollectionSink",
	            "nestingSeparator": ".",
	            "writeBatchSize": 2,
	            "writeBatchTimeout": "00:00:00"
	          }
	          "translator": {
	              "type": "TabularTranslator",
	              "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix, EmailPromotion: EmailPromotion, rowguid: rowguid, ModifiedDate: ModifiedDate"
	          }
	        },
	        "inputs": [
	          {
	            "name": "PersonBlobTableIn"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "PersonDocumentDbTableOut"
	          }
	        ],
	        "policy": {
	          "concurrency": 1
	        },
	        "name": "CopyFromBlobToDocDb"
	      }
	    ],
	    "start": "2015-04-14T00:00:00Z",
	    "end": "2015-04-15T00:00:00Z"
	  }
	}
 
Если пример входных данных BLOB-объекта выглядит так:

	1,John,,Doe

то JSON выходных данных в DocumentDB будет выглядеть так:

	{
	  "Id": 1,
	  "Name": {
	    "First": "John",
	    "Middle": null,
	    "Last": "Doe"
	  },
	  "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
	}
	
DocumentDB — это хранилище NoSQL для JSON-документов, в которых разрешена иерархическая структура. Фабрика данных Azure позволяет обозначать иерархию с помощью разделителя **nestingSeparator**, которым в этом примере является точка. Благодаря этому разделителю действие копирование создаст объект Name с тремя дочерними элементами, First, Middle и Last, в соответствии с элементами Name.First, Name.Middle и Name.Last в определении таблицы.

## Свойства связанной службы Azure DocumentDB

В следующей таблице содержится описание элементов JSON, которые относятся к связанной службе Azure DocumentDB.

| **Свойство** | **Описание** | **Обязательный** |
| -------- | ----------- | --------- |
| type | Для свойства надо указать тип **DocumentDb**. | Да |
| connectionString | Указываемые сведения, необходимые для подключения к базе данных Azure DocumentDB. | Да |

## Свойства типа набора данных Azure DocumentDB

Полный список разделов и свойств, используемых для определения наборов данных, см. в статье [Создание наборов данных](data-factory-create-datasets.md). Разделы структуры, доступности и политики JSON набора данных одинаковы для всех типов наборов данных (SQL Azure, BLOB-объекты Azure, таблицы Azure и т. д.).
 
Раздел typeProperties во всех типах наборов данных разный. В нем содержатся сведения о расположении данных в хранилище данных. Раздел typeProperties для набора данных с типом **DocumentDbCollection** имеет приведенные ниже свойства.

| **Свойство** | **Описание** | **Обязательный** |
| -------- | ----------- | -------- |
| collectionName | Имя коллекции документов DocumentDB. | Да |


Пример:

	{
	  "name": "PersonDocumentDbTable",
	  "properties": {
	    "type": "DocumentDbCollection",
	    "linkedServiceName": "DocumentDbLinkedService",
	    "typeProperties": {
	      "collectionName": "Person"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

### Схема фабрики данных
Для хранилищ данных без схемы, таких как DocumentDB, служба фабрики данных определяет схему одним из следующих способов.

1.	Если указать структуру данных с помощью свойства **structure** в определении набора данных, служба фабрики данных считает эту структуру схемой. В этом случае, если строка не содержит значение столбца, ему будет присвоено значение NULL.
2.	Если структура данных не указана с помощью свойства **structure** в определении набора данных, служба фабрики данных определяет схему, используя первую строку данных. В этом случае, если первая строка не содержит полную схему, после операции копирования некоторые столбцы будут отсутствовать.

Таким образом для источников данных без схемы рекомендуется указывать структуру данных с помощью свойства **structure**.

## Свойства типа "Действие копирования Azure DocumentDB"

Полный список разделов и свойств, используемых для определения действий, см. в статье [Создание конвейеров](data-factory-create-pipelines.md). Такие свойства, как имя, описание, входные и выходные таблицы, различные политики и т. д., доступны для всех типов действий.
 
**Примечание**. Действие копирования принимает только один входной набор данных и возвращает только один выходной набор.

То, какие свойства будут доступны в разделе typeProperties, зависит от типа действия, а в случае с действием копирования — еще и от типов источников и приемников.

В случае действия копирования, когда источник относится к типу **DocumentDbCollectionSource**, в разделе **typeProperties** доступны приведенные ниже свойства.

| **Свойство** | **Описание** | **Допустимые значения** | **Обязательный** |
| ------------ | --------------- | ------------------ | ------------ |
| запрос | Запрос, нужный для чтения данных. | Строка запроса, поддерживаемая базой данных DocumentDB. <p>Пример. SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > "2009-01-01T00:00:00"</p> | <p>Если не указано другое, выполняется инструкция SQL: select <columns defined in structure> from mycollection </p>
| nestingSeparator | Специальный символ, обозначающий, что документ является вложенным. | Любой символ. <p>DocumentDB — это хранилище NoSQL для JSON-документов, в которых разрешена вложенная структура. Фабрика данных Azure позволяет обозначать иерархию с помощью разделителя nestingSeparator, которым в приведенных выше примерах является точка (.). Благодаря этому разделителю действие копирование создаст объект Name с тремя дочерними элементами, First, Middle и Last, в соответствии с элементами Name.First, Name.Middle и Name.Last в определении таблицы.</p> | Нет

**DocumentDbCollectionSink** поддерживает приведенные ниже свойства.

| **Свойство** | **Описание** | **Допустимые значения** | **Обязательный** |
| -------- | ----------- | -------------- | -------- |
| nestingSeparator | Такой специальный символ в имени исходного столбца, который указывает, что нужен вложенный документ. <p>См. пример выше: элемент Name.First в выходной таблице обуславливает в документе DocumentDB такую структуру JSON:</p><p>"Name": {<br/> "First": "John"<br/>},</p> | Символ, используемый для разделения уровней вложенности.<p>Значение по умолчанию — «.» (точка).</p> | Символ, используемый для разделения уровней вложенности. <p>Значение по умолчанию — «.» (точка).</p> | Нет | 
| writeBatchSize | Число параллельных запросов к DocumentDB на создание документов.<p>С помощью этого свойства вы можете улучшить производительность копирования данных в базу данных DocumentDB и из нее. Если увеличить значение свойства writeBatchSize, то производительность повышается, потому что к базе данных DocumentDB начинает уходить больше параллельных запросов. При этом количество запросов нужно регулировать осмотрительно, чтобы не появилось сообщение об ошибке «Слишком большая частота запросов».</p><p>Регулируя количество запросов, нужно принимать во внимание разные факторы, например размер документов, количество терминов в документах, политику индексирования в целевой коллекции и т. д. Для операций копирования вы можете использовать коллекцию получше (например, S3), чтобы обеспечить максимальную пропускную способность (2500 единиц запроса в секунду).</p> | Целое значение | Нет |
| writeBatchTimeout | Время ожидания до выполнения операции, пока не завершится срок ее действия. | (Единица — интервал времени) Пример: 00:30:00 (30 минут). | Нет |
 
## Приложение
1. **Вопрос.** Поддерживает ли действие копирования обновление существующих записей?

	**Ответ.** Нет.

2. **Вопрос.** Как при повторной попытке копирования в DocumentDB обрабатываются уже скопированные записи?

	**Ответ.** Если у записи есть поле идентификатора, и операция копирования пытается вставить запись с тем же идентификатором, операция копирования завершается ошибкой.
 
3. **Вопрос.** Поддерживает ли фабрика данных [секционирование данных по диапазонам или хэш-секционирование]( https://azure.microsoft.com/documentation/articles/documentdb-partition-data/)?

	**Ответ.** Нет. 
4. **Вопрос.** Можно ли указать больше одной коллекции DocumentDB для таблицы?
	
	**Ответ.** Нет. Сейчас можно указать только одну коллекцию.
     

<!---HONumber=AcomDC_0302_2016--->