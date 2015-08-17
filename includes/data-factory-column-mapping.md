## Сопоставление столбцов с помощью правил переводчика
Сопоставление столбцов можно использовать, чтобы указать, как столбцы, указанные в «structure» исходной схемы таблицы, сопоставляются со столбцами, указанными в «structure» таблицы-приемника. Свойство **ColumnMapping** доступно в разделе **typeProperties** действия копирования.

Сопоставление столбцов применимо в следующих сценариях:

1.	Все столбцы в «structure» исходной таблицы сопоставляется со всеми столбцами в «structure» таблицы-приемника.
2.	Подмножество столбцов в «structure» исходной таблицы сопоставляется со всеми столбцами в «structure» таблицы-приемника.

Ниже приведены неправильные условия, которые приведут к порождению исключения.

1.	Меньше или больше столбцов в «structure» таблицы-приемника, чем указано в сопоставлении.
2.	Повторяющееся сопоставление.
3.	В результате запроса SQL нет имени столбца, который указан в сопоставлении.

## Примеры сопоставления столбцов
> [AZURE.NOTE]Приведенные ниже примеры предназначены для Azure SQL и большого двоичного объекта Azure, но подходят для любого хранилища данных, поддерживающего прямоугольные таблицы. В этих примерах необходимо будет настроить набор данных и определения связанной службы, чтобы указать данные в соответствующем источнике данных.

### Пример 1. Сопоставление столбцов из SQL Azure с большим двоичным объектом Azure
В этом примере входная таблица имеет структуру, и она указывает на таблицу SQL в базе данных SQL Azure.

	{
	    "name": "AzureSQLInput",
	    "properties": {
	        "structure": 
	         [
	           { "name": "userid"},
	           { "name": "name"},
	           { "name": "group"}
	         ],
	        "type": "AzureSqlTable",
	        "linkedServiceName": "AzureSqlLinkedService",
	        "typeProperties": {
	            "tableName": "MyTable"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": "1",
	        },
			"external": true,
			"policy": {
	            "externalData": {
	                "retryInterval": "00:01:00",
	                "retryTimeout": "00:10:00",
	                "maximumRetry": "3"
	            }
			}
	    }
	}

Выходная таблица имеет структуру, и она указывает на большой двоичный объект в хранилище больших двоичных объектов Azure.

	{
	    "name": " AzureBlobOutput",
	    "properties":
	    {
	         "structure": 
	          [
	                { "name": "myuserid"},
	                { "name": "myname" },
	                { "name": "mygroup"}
	          ],
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/myfolder",
	            "fileName":"myfile.csv",
	            "format":
	            {
	                "type": "TextFormat",
	                "columnDelimiter": ","
	            }
	        },
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}

Ниже приведен JSON для действия. Столбцы из источника сопоставляются со столбцами в приемнике (**columnMappings**) с помощью свойства **Translator**.

	{
	    "name": "CopyActivity",
	    "description": "description", 
	    "type": "CopyActivity",
	    "inputs":  [ { "name": " AzureSQLInput"  } ],
	    "outputs":  [ { "name": " AzureBlobOutput" } ],
	    "typeProperties":    {
	        "source":
	        {
	            "type": "SqlSource"
	        },
	        "sink":
	        {
	            "type": "BlobSink"
	        },
	        "Translator": 
	        {
	            "type": "TabularTranslator",
	            "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
	        }
	    },
	   "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        }
	}

**Процесс сопоставления столбцов:**

![Процесс сопоставления столбцов](./media/data-factory-data-stores-with-rectangular-tables/column-mapping-flow.png)

### Пример 2. Сопоставление столбцов из Azure SQL с большим двоичным объектом Azure с помощью SQL-запроса
В этом примере используется SQL-запрос для извлечения данных из SQL Azure, а не просто указывается имя таблицы и имена столбцов в разделе «structure».

	{
	    "name": "CopyActivity",
	    "description": "description", 
	    "type": "CopyActivity",
	    "inputs":  [ { "name": " AzureSQLInput"  } ],
	    "outputs":  [ { "name": " AzureBlobOutput" } ],
	    "typeProperties":
	    {
	        "source":
	        {
	            "type": "SqlSource",
	            "SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
	        },
	        "sink":
	        {
	            "type": "BlobSink"
	        },
	        "Translator": 
	        {
	            "type": "TabularTranslator",
	            "ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
	        }
	    },
	    "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        }
	}

В этом случае сначала результаты запроса сопоставляются со столбцами, указанными в «structure» источника. Затем столбцы из «structure» источника сопоставляются со столбцами в «structure» приемника посредством правил, определенных в columnMappings. Предположим, что запрос возвращает 5 столбцов, на два столбца больше, чем указано в подразделе «structure» источника.

**Процесс сопоставления столбцов**

![Процесс сопоставления столбцов 2](./media/data-factory-data-stores-with-rectangular-tables/column-mapping-flow-2.png)

<!---HONumber=August15_HO6-->