<properties 
	pageTitle="Дополнительные сценарии использования действия копирования в фабрике данных Azure" 
	description="Описываются расширенные сценарии использования действия копирования в фабрике данных Azure." 
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
	ms.date="07/21/2015" 
	ms.author="spelluru"/>

# Дополнительные сценарии использования действия копирования в фабрике данных Azure 
## Обзор
С помощью **действия копирования** в конвейере вы можете выполнять пакетное копирование данных из источника в приемник (место назначения). В этом разделе описываются сложные сценарии, которые поддерживает действие копирования. Подробное описание действия копирования и основных сценариев, которые оно поддерживает, см. в разделе [Копирование данных с помощью фабрики данных Azure][adf-copyactivity].


## Фильтрация столбцов с помощью определения структуры
В зависимости от типа таблицы можно указать подмножество столбцов из источника, задав меньшее число столбцов в определении **Structure** таблицы, чем число, существующее в базовом источнике данных. Ниже приведена таблица, которая содержит информацию о логике фильтрации столбцов для различных типов таблицы.

| Тип таблицы | Логика фильтрации столбцов |
|-------------------|----------------------- |
| AzureBlobLocation |Определение Structure в таблице JSON должно соответствовать структуре большого двоичного объекта. Чтобы выбрать подмножество столбцов, воспользуйтесь функцией сопоставления столбцов, описанной в разделе «Правила преобразования — сопоставление столбцов». | 
| AzureSqlTableLocation и OnPremisesSqlServerTableLocation | Если свойство SqlReaderQuery задано как часть определения действия копирования, необходимо выровнять определение Structure таблицы со столбцами, выбранными в запросе. Если свойство SqlReaderQuery не указано, то действие копирования будет автоматически создавать запрос SELECT на основе столбцов, указанных в определении Structure таблицы. |
| AzureTableLocation | Раздел Structure в определении таблицы может содержать полный набор или подмножество столбцов из базовой таблицы Azure.

## Правила преобразования — сопоставление столбцов
Можно использовать сопоставление столбцов, чтобы указать, как столбцы в исходной таблице сопоставляются со столбцами в таблице приемника. Оно поддерживает следующие сценарии:

- Сопоставление всех столбцов в «structure» исходной таблицы со «structure» целевой таблицы.
- Подмножество столбцов в «structure» исходной таблицы сопоставляется со всей «structure» целевой таблицы.

Оно не поддерживает следующие сценарии и выдает исключение:

- Меньше или больше столбцов в целевой таблице.
- Повторяющееся сопоставление.
- Результат запроса SQL не содержит имени столбца

В частности, при копировании данных между двумя большими двоичными объектами Azure действие копирования будет в основном обрабатывать его как прямое копирование двоичных данных, кроме следующих 3 сценариев:


1. если входная и выходная таблицы имеют разный формат, то действие копирования будет выполнять преобразование формата;
2. если входная таблица задана как папка, которая может содержать много файлов, а выходная таблица задана как файл, то действие копирования будет объединять файлы в исходной папке в один файл-приемник;
3. если указано «columnMapping», то действие копирования будет выполнять соответствующее преобразование данных.


### Пример 1 — сопоставление столбцов из сервера SQL Server с большим двоичным объектом Azure
В этом примере **входная таблица** определяется следующим образом. Входная таблица имеет структуру, и она указывает на таблицу SQL в базе данных SQL Server.
         
		{
		    "name": "MyOnPremTable",
    		"properties":
    		{
				"structure": 
				[
            		{ "name": "userid", "type": "String"},
            		{ "name": "name", "type": "String"},
            		{ "name": "group", "type": "Decimal"}
				],
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

В этом примере **выходная таблица** определяется следующим образом. Выходная таблица имеет структуру, и она указывает на большой двоичный объект в хранилище больших двоичных объектов Azure.
         
		
	{
		"name": "MyDemoBlob",
		"properties":
		{
    		"structure":
			[
        	    { "name": "myuserid", "type": "String"},
        	    { "name": "mygroup", "type": "String"},
        	    { "name": "myname", "type": "Decimal"}
			],
			"location":
    		{
    	    	"type": "AzureBlobLocation",
		        "folderPath": "MyContainer/MySubFolder",
				"fileName": "MyBlobName",
    	    	"linkedServiceName": "MyLinkedService",
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

Если не указать **fileName** для **выходной таблицы**, то созданные в**folderPath** файлы получают имена в следующем формате: Data.<Guid>.txt (например: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

Чтобы динамически установить параметры **folderPath** и **fileName** на основе времени **SliceStart**, используйте свойство **partitionedBy**. В следующем примере параметр **folderPath** использует год, месяц и день из SliceStart (времени начала обработки среза), а параметр fileName использует час из SliceStart. Например, если срез выполняется для временной отметки 2014-10-20T08:00:00, folderName получает значение wikidatagateway/wikisampledataout/2014/10/20, а fileName – 08.csv.

  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
    [
    	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

#### Пример — определение сопоставления столбцов
В этом примере действие в конвейере определяется следующим образом. Столбцы из источника сопоставляются со столбцами в приемнике (**columnMappings**) с помощью свойства **Translator**.

	{
		"name": "CopyActivity",
		"description": "description", 
		"type": "CopyActivity",
		"inputs":  [ { "name": "MyOnPremTable"  } ],
		"outputs":  [ { "name": "MyDemoBlob" } ],
		"transformation":
		{
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
		}
	}

![Сопоставление столбцов][image-data-factory-column-mapping-1]

### Пример 2 — сопоставление столбцов с SQL-запросом от сервера SQL Server к большому двоичному объекту Azure
В этом примере SQL-запрос (в отличие от таблицы в предыдущем примере) используется для извлечения данных из локального сервера SQL Server, а столбцы из результатов запроса сопоставляются с исходным артефактом, а затем с целевым артефактом. В данном примере запрос возвращает 5 столбцов.

	{
		"name": "CopyActivity",
		"description": "description", 
		"type": "CopyActivity",
		"inputs":  [ { "name": "InputSqlDA"  } ],
		"outputs":  [ { "name": "OutputBlobDA" } ],
		"transformation":
		{
			"source":
			{
				"type": "SqlSource",
				"SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \'{0:yyyyMMdd-HH}\'', SliceStart)"
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
		}
	}

![Сопоставление столбцов 2][image-data-factory-column-mapping-2]

## Обработка типа данных с помощью действия копирования

Типы данных, указанные в разделе Structure определения таблицы, принимаются только для **BlobSource**. В таблице ниже описывается, как типы данных обрабатываются для других типов источника и приемника.

| Источник/приемник | Логика обработки типов данных |
| ----------- | ------------------------ |
| SqlSource | Типы данных, определенные в разделе Structure определения таблицы, игнорируются. Типы данных, определенные в основной базе данных SQL, будут использоваться для извлечения данных при действии копирования. |
| SqlSink | Типы данных, определенные в разделе Structure определения таблицы, игнорируются. Будут сравниваться типы данных в основном источнике и назначении, а в случае несоответствия типов будет выполняться их неявное преобразование. |
| BlobSource | При переносе из BlobSource в BlobSink не выполняются никакие преобразования типов; типы данных, определенные в разделе Structure определения таблицы, игнорируются. Для назначений, отличных от BlobSink, типы данных, определенные в разделе Structure определения таблицы, будут приниматься. Если раздел Structure не указан в определении таблицы, то тип обработки зависит от свойства формата таблицы BlobSource: TextFormat: все типы столбцов обрабатываются как строка, а все имена столбцов заданы как «Prop_<0-N>». AvroFormat: используются встроенные типы столбцов и имена в файле Avro.
| BlobSink | Типы данных, определенные в разделе Structure определения таблицы, игнорируются. Используются типы данных, определенные в базовом хранилище входных данных. Столбцы задаются как допускающие значение null для сериализации Avro. |
| AzureTableSource | Типы данных, определенные в разделе Structure определения таблицы, игнорируются. Используются типы данных, определенные в базовой таблице Azure. |
| AzureTableSink | Типы данных, определенные в разделе Structure определения таблицы, игнорируются. Используются типы данных, определенные в базовом хранилище входных данных. |

**Примечание.** Таблица Azure поддерживает только ограниченный набор типов данных; см. раздел [Основные сведения о модели данных службы таблиц][azure-table-data-type].

## Вызов хранимой процедуры для приемника SQL.
Для копирования данных на сервер SQL Server или в базу данных SQL Azure можно настроить и вызвать указанную пользователем хранимую процедуру с дополнительными параметрами.
### Пример
1. Задайте JSON выходной таблицы следующим образом (в качестве примера возьмите таблицу базы данных SQL Azure).

    	{
    		"name": "MyAzureSQLTable",
    		"properties":
    		{
    			"location":
    			{
    				"type": "AzureSqlTableLocation",
    				"tableName": "Marketing",
    				"linkedServiceName": "AzureSqlLinkedService"
    			},
    			"availability":
    			{
    				"frequency": "Hour",
    				"interval": 1
    			}
    		}
    	}

2. Определите раздел **SqlSink** в действии копирования JSON следующим образом. Для вызова хранимой процедуры во время вставки данных требуются свойства **SqlWriterStoredProcedureName** и **SqlWriterTableType**.

		"sink":
	    {
			"type": "SqlSink",
	        "SqlWriterTableType": "MarketingType",
		    "SqlWriterStoredProcedureName": "spOverwriteMarketing",	
			"storedProcedureParameters":
					{
                    	"stringData": 
						{
                        	"value": "str1"		
						}
                    }
	    }

3. В своей базе данных определите хранимую процедуру с тем же именем, что и **SqlWriterStoredProcedureName**. Она обрабатывает входные данные из указанного источника и вставляет их в выходную таблицу. Обратите внимание, что имя параметра хранимой процедуры должно совпадать с именем **tableName**, заданным в JSON-файле таблицы.

		CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
		AS
		BEGIN
			DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
    		INSERT [dbo].[Marketing](ProfileID, State)
    		SELECT * FROM @Marketing
		END


4. В своей базе данных определите тип таблицы с тем же именем, что и **SqlWriterTableType**. Обратите внимание, что схема типа таблицы должны быть той же, что и схема, возвращаемая входными данными.

		CREATE TYPE [dbo].[MarketingType] AS TABLE(
    	    [ProfileID] [varchar](256) NOT NULL,
    	    [State] [varchar](256) NOT NULL,
    	)

Функциональность хранимой процедуры использует преимущества [параметров с табличным значением][table-valued-parameters].

## Указание кодировки для текстовых файлов
Хотя кодировка UTF-8 весьма распространена, исторически сложилось, что текстовые файлы в большом двоичном объекте Azure часто используют другие кодировки. Свойство **EncodingName** позволяет указать кодировку с помощью имени кодовой страницы для таблиц типа TextFormat. Список допустимых имен кодировок см. в описании свойства Encoding.EncodingName. Например: windows-1250 или shift_jis. По умолчанию используется значение UTF-8. Допустимые имена кодировок см. в разделе [Класс Encoding](https://msdn.microsoft.com/library/system.text.encoding.aspx).

## См. также

- [Примеры использования действия копирования][copy-activity-examples]
- [Копирование данных с помощью фабрики данных Azure][adf-copyactivity]
- [Действие копирования. Справочник по скриптам JSON](https://msdn.microsoft.com/library/dn835035.aspx)
- [Основные сведения о копировании данных с помощью фабрики данных Azure][copy-activity-video] (видео)


[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/
[table-valued-parameters]: http://msdn.microsoft.com/library/bb675163.aspx


[adfgetstarted]: data-factory-get-started.md
[adf-copyactivity]: data-factory-copy-activity.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[copy-activity-examples]: data-factory-copy-activity-examples.md

[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[azure-table-data-type]: https://msdn.microsoft.com/ru-ru/library/azure/dd179338.aspx

[image-data-factory-copy-actvity]: ./media/data-factory-copy-activity/VPNTopology.png
[image-data-factory-column-mapping-1]: ./media/data-factory-copy-activity-advanced/ColumnMappingSample1.png
[image-data-factory-column-mapping-2]: ./media/data-factory-copy-activity-advanced/ColumnMappingSample2.png
 

<!---HONumber=July15_HO4-->