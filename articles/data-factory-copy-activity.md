<properties title="Copy data with Azure Data Factory" pageTitle="Копирование данных с помощью фабрики данных Azure" description="Learn how to use Copy Activity in Azure Data Factory to copy data from a data source to another data source." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Копирование данных с помощью фабрики данных Azure (действие копирования)
Вы можете использовать **действие копирования** в конвейере, чтобы копировать данные из источника в приемник (назначение) в пакете. Действие копирования можно использовать в следующих сценариях:

- **Входящие данные в Azure**. В этом сценарии данные копируются из локального источника данных (например, SQL Server) в хранилище данных Azure (например, большой двоичный объект Azure, таблицу Azure или базу данных SQL Azure) для следующих подсценариев:
	- Сбор данных в централизованном расположении в Azure для дальнейшей обработки.
	- Перенос в Azure данных из локальной платформы или облачной платформы, отличной от Azure.
	- Архивация или резервное копирование данных в Azure для экономичного многоуровневого хранения.
- **Исходящие данные из Azure**. В этом сценарии данные копируются из Azure (например, большого двоичного объекта Azure, таблицы Azure или базы данных SQL Azure) в локальные киоски данных и хранилище данных (например, SQL Server) для следующих подсценариев:
	- Передача данных в локальную среду из-за отсутствия поддержки хранилища данных облака.
	- Передача данных в локальную среду, что позволит воспользоваться существующим локальным решением или инфраструктурой отчетности.
	- Архивация или резервное копирование данных в локальную среду для многоуровневого хранения.
- **Копирование из Azure в Azure**. В этом сценарии данные, распределенные по источникам данных Azure, объединяются в централизованное хранилище данных Azure. Примеры: из таблицы Azure в большой двоичный объект Azure, из большого двоичного объекта Azure в таблицу Azure, из таблицы Azure в SQL, из большого двоичного объекта Azure в SQL Azure.

См. в разделе [Приступая к работе с фабрикой данных Azure][], как с помощью действия копирования скопировать данные из хранилища больших двоичных объектов Azure в базу данных SQL Azure. Пошаговое руководство по копированию данных из локальной базы данных SQL Server в хранилище больших двоичных объектов Azure с помощью действия копирования см. в разделе [Включение конвейеров для работы с локальными данными][use-onpremises-datasources].


## Действие копирования - компоненты
Вот какие компоненты содержит действие копирования: 

- **Входная таблица**. Таблица является набором данных, который содержит схему и имеет прямоугольную форму. Компонент входной таблицы 
- описывает входные данные для действия, включая следующее: имя таблицы, тип таблицы и связанную службу, которая ссылается на источник данных, содержащий входные данные.
- **Выходная таблица**. Выходная таблица описывает выходные данные для действия, включая следующее: имя таблицы, тип таблицы и связанную службу, которая ссылается на источник данных, содержащий выходные данные.
- **Правила преобразования**. Правила преобразования указывают, как входные данные извлекаются из источника, как выходные данные загружаются в приемник и т. д.
 
Действие копирования может иметь одну **входную таблицу** и одну **выходную таблицу**.

## JSON для действия копирования
Конвейер состоит из одного или нескольких действий. Действия в конвейерах определяются в разделе **activities[]**. Вот как выглядит JSON для конвейера:
         
	{
		"name": "PipelineName",
		"properties": 
    	{
        	"description" : "pipeline description",
        	"activities":
        	[
	
    		]
		}
	}

Каждое действие в разделе **activities** имеет следующую структуру верхнего уровня. Свойству **type** необходимо присвоить значение **CopyActivity**. Действие копирования может иметь только одну входную таблицу и одну выходную таблицу.
         

	{
		"name": "ActivityName",
		"description": "description", 
		"type": "CopyActivity",
		"inputs":  [],
		"outputs":  [],
		"transformation":
		{

		},
		"policy":
		{
		
		}
	}

В следующей таблице описаны теги, используемые с разделом действия. 

<table border="1">	
	<tr>
		<th align="left">Тег</th>
		<th align="left">Описание</th>
		<th align="left">Обязательно</th>
	</tr>	

	<tr>
		<td>name</td>
		<td>Имя действия.</td>
		<td>Да</td>
	</tr>	

	<tr>
		<td>description</td>
		<td>Текст, описывающий, для чего используется действие.</td>
		<td>Да</td>
	</tr>

	<tr>
		<td>type</td>
		<td>Задает тип действия. <br/><br/>Для<b>type</b> необходимо присвоить значение <b>CopyActivity</b>.</td>
		<td>Да</td>
	</tr>

	<tr>
		<td>inputs</td>
		<td>Входные таблицы, используемые действием.  Укажите только одну входную таблицу для действия копирования.</td>
		<td>Да</td>
	</tr>

	<tr>
		<td>outputs</td>
		<td>Выходные таблицы, используемые действием.  Укажите только одну выходную таблицу для действия копирования.</td>
		<td>Да</td>
	</tr>

	<tr>
		<td>transformation</td>
		<td>Свойства в преобразовании зависят от типа.  Для <b>действия копирования</b> необходимо задать разделы <b>source</b> и <b>sink</b> в разделе <b>transformation</b>. Дополнительная информация приведена далее в этой статье. </td>
		<td>Да</td>
	</tr>

	<tr>
		<td>policy</td>
		<td>Политики, которые влияют на поведение во время выполнения действия. Если не указано, будут использоваться значения по умолчанию.</td>
		<td>Нет</td>
	</tr>


</table>

Подробную информацию о свойствах и тегах JSON см. в [справочнике по сценариям JSON][json-script-reference].

## Действие копирования - пример
В этом примере входная и выходная таблицы определяются и используются в действии копирования внутри конвейера, копирующего данные из локальной базы данных SQL Server в большой двоичный объект Azure.

**Предположения**
Следующие артефакты фабрики данных Azure указываются в примерах сценариев JSON, которые приведены далее.

* Группа ресурсов с именем **ADF**.
* Фабрика данных Azure с именем **CopyFactory**.
* Связанная служба с именем **MyOnPremisesSQLDB**, указывающая на локальную базу данных SQL Server.
* Связанная служба с именем **MyAzureStorage**, которая указывает на хранилище больших двоичных объектов Azure.

### JSON входной таблицы
Следующий сценарий JSON определяет входную таблицу, которая ссылается на таблицу SQL: **MyTable** - это локальная база данных SQL Server, которую определяет связанная служба **MyOnPremisesSQLDB**. Обратите внимание, что **name** - это имя таблицы фабрики данных Azure, а **tableName** - это имя таблицы SQL в базе данных SQL Server.

         
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

Следующий пример команды Azure PowerShell использует элемент **New-AzureDataFactoryTable**, который использует JSON-файл, содержащий вышеупомянутый сценарий для создания таблицы (**MyOnPremTable**) в фабрике данных Azure: **CopyFactory**.
         
	New-AzureDataFactoryTable -ResourceGroupName ADF -Name MyOnPremTable -DataFactoryName CopyFactory -File <Filepath>\MyOnPremTable.json.

Подробную информацию о командлетах фабрики данных см. в [справочнике по командлетам][cmdlet-reference]. 

### JSON выходной таблицы
Следующий сценарий JSON определяет выходную таблицу: **MyDemoBlob** ссылается на большой двоичный объект Azure: **MyBlob** в папке больших двоичных объектов: **MySubFolder** в контейнере больших двоичных объектов: **MyContainer**.
         
	{
   		"name": "MyDemoBlob",
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

Следующий пример команды Azure PowerShell использует элемент **New-AzureDataFactoryTable**, который использует JSON-файл, содержащий вышеупомянутый сценарий для создания таблицы (**MyDemoBlob**) в фабрике данных Azure: **CopyFactory**.
         
	New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName CopyFactory -File <Filepath>


### JSON конвейера (с действием копирования)
В этом примере конвейер **CopyActivityPipeline** определяется следующими свойствами: 

- Свойству**type** присваивается значение **CopyActivity**.
- **MyOnPremTable** задается как входные данные (тег **inputs**).
- **MyAzureBlob** задается как выходные данные (тег **outputs**)
Раздел - **transformation** содержит два подраздела: **source** и **sink**. Для source (источник) устанавливается значение типа **SqlSource**, а для sink (приемник) устанавливается значение типа **BlobSink**. **sqlReaderQuery** определяет преобразование (проекцию) для выполнения в источнике. Подробную информацию о всех свойствах см. в [справочнике по сценариям JSON][json-script-reference].

         
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


Следующий пример команды Azure PowerShell использует элемент **New-AzureDataFactoryPipeline**, который использует JSON-файл, содержащий вышеупомянутый сценарий для создания конвейера (**CopyActivityPipeline**) в фабрике данных Azure: **CopyFactory**.
         
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName CopyFactory -File <Filepath>

## Поддерживаемые входные и выходные данные
Вышеупомянутый пример в разделе transformation (преобразование) в качестве источника использует SqlSource, а в качестве приемника - BlobSink. Ниже приведена таблица с перечнем источников и приемников, которые поддерживаются действием копирования. 

<table border="1">	
	<tr>
		<th><i>Приемник/источник<i></th>
		<th>BLOB-объект Azure</th>
		<th>Таблица Azure</th>
		<th>База данных SQL Azure</th>
		<th>Локальный сервер SQL Server</th>
		<th>Сервер SQL Server в IaaS</th>
	</tr>	

	<tr>
		<td><b>BLOB-объект Azure</b></td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
	</tr>

	<tr>
		<td><b>Таблица Azure</b></td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td></td>
		<td></td>
	</tr>	

	<tr>
		<td><b>База данных SQL Azure</b></td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td></td>
		<td></td>
	</tr>


	<tr>
		<td><b>Локальный сервер SQL Server</b></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>Сервер SQL Server в IaaS</b></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

</table>


В следующей таблице перечислены типы источников и приемников, которые могут использоваться в JSON-файле для конвейера, содержащего действие копирования.


<table border="1">	
	<tr>
		<th></th>
		<th align="left">Тип источника</th>
		<th align="left">Тип приемника</th>
	</tr>	

	<tr>
		<td><b>BLOB-объект Azure</b></td>
		<td>BlobSource</td>
		<td>BlobSink</td>
	</tr>

	<tr>
		<td><b>Таблица Azure</b></td>
		<td>AzureTableSource</td>
		<td>AzureTableSink</td>
	</tr>

	<tr>
		<td><b>База данных SQL Azure</b></td>
		<td>SqlSource</td>
		<td>SqlSink</td>
	</tr>

	<tr>
		<td><b>Локальный сервер SQL Server</b></td>
		<td>SqlSource</td>
		<td>SqlSink</td>
	</tr>

	<tr>
		<td><b>SQL в IaaS</b></td>
		<td>SqlSource</td>
		<td>SqlSink</td>
	</tr>
</table>

В следующей таблице перечислены свойства, поддерживаемые этими источниками и приемниками.

<table border="1">

	<tr>
	<th align="left">Источник/приемник</th>
	<th align="left">Поддерживаемое свойство</th>
	<th align="left">Описание</th>
	<th align="left">Допустимые значения</th>
	<th align="left">Обязательно</th>
	</tr>

	<tr>
		<tr>
		<td><b>BlobSource</b></td>
		<td>BlobSourceTreatEmptyAsNull</td>
		<td>Указывает, следует ли интерпретировать null или пустую строку как значение null.</td>
		<td>TRUE<br/>FALSE</td>
		<td>Нет</td>
		</tr>
	</tr>

	<tr>
		<td></td>
		<td>BlobSourceSkipHeaderLineCount</td>
		<td>Укажите, сколько строк необходимо пропустить.</td>
		<td>Целое число от 0 до макс.</td>
		<td>Нет</td>
	</tr>

	<tr>
		<td><b>AzureTableSource</b></td>
		<td>AzureTableSourceQuery</td>
		<td>Используйте пользовательский запрос для чтения данных.</td>
		<td>Строка запроса таблицы Azure.<br/>Пример: "ColumnA eq ValueA"</td>
		<td>Нет</td>
	</tr>

	<tr>
		<td></td>
		<td>AzureTableSourceIgnoreTableNotFound</td>
		<td>Укажите, следует ли игнорировать исключение: "таблица не существует".</td>
		<td>TRUE<br/>FALSE</td>
		<td>Нет</td>
	</tr>


	<tr>
		<td><b>SqlSource</b></td>
		<td>sqlReaderQuery</td>
		<td>Используйте пользовательский запрос для чтения данных.</td>
		<td>Строка запроса SQL.<br/><br/>Например: "Select * from MyTable".<br/><br/> Если не указано, то запрос будет "select <определенные в структуре столбцы> from MyTable".</td>
		<td>Нет</td>
	</tr>


	<tr>
		<td><b>AzureTableSink</b></td>
		<td>azureTableDefaultPartitionKeyValue</td>
		<td>Значение ключа раздела по умолчанию, которое может использоваться приемником.</td>
		<td>Строковое значение.</td>
		<td>Нет</td>
	</tr>

	<tr>
		<td></td>
		<td>azureTablePartitionKeyName</td>
		<td>Задаваемое пользователем имя столбца, значения которого используются в качестве ключа раздела.<br/><br/> Если не указано, в качестве ключа раздела используется AzureTableDefaultPartitionKeyValue.</td>
		<td>Имя столбца.</td>
		<td>Нет</td>
	</tr>

	<tr>
		<td></td>
		<td>azureTableRowKeyName</td>
		<td>Значения столбца с определенным именем используются как ключ строки.<br/><br/>Если не указано, для каждой строки используется GUID.</td>
		<td>Имя столбца.</td>
		<td>Нет</td>
	</tr>

	<tr>
		<td></td>
		<td>azureTableInsertType</td>
		<td>Режим для вставки данных в таблицу Azure.</td>
		<td>"merge"<br/><br/>"replace"</td>
		<td>Нет</td>
	</tr>

	<tr>
		<td></td>
		<td>writeBatchSize</td>
		<td>Вставка данных в таблицу Azure при достижении writeBatchSize или writeBatchTimeout.</td>
		<td>Целое число от 1 до 100 (единица = количество строк)</td>
		<td>N<br/><br/>(по умолчанию = 100)</td>
	</tr>

	<tr>
		<td></td>
		<td>writeBatchTimeout</td>
		<td>Вставка данных в таблицу Azure при достижении writeBatchSize или writeBatchTimeout</td>
		<td>(Единица = timespan)<br/><br/>Пример: "00:20:00" (20 минут)</td>
		<td>N<br/><br/>(по умолчанию значение для времени ожидания клиента хранения - 90 секунд)</td>
	</tr>

	<tr>
		<td><b>SqlSink</b></td>
		<td>SqlWriterStoredProcedureName</td>
		<td>Указывает имя хранимой процедуры upsert (update/insert) для обновления и вставки данных в целевую таблицу.</td>
		<td>Имя хранимой процедуры.</td>
		<td>Нет</td>
	</tr>

	<tr>
		<td></td>
		<td>SqlWriterTableType</td>
		<td>Указывает тип таблицы для использования в вышеупомянутой хранимой процедуре.</td>
		<td>Имя типа таблицы.</td>
		<td>Нет</td>
	</tr>
</table>

### SQL на IaaS (инфраструктура как услуга)
Для SQL на IaaS в качестве поставщика IaaS поддерживается Azure. Поддерживаются следующие топологии сети и VPN. Обратите внимание, что шлюз управления данными требуется для случаев № 2 и № 3, но не требуется для случая № 1. Подробную информацию о шлюзе управления данными см. в разделе [Включение конвейеров для доступа к локальным данным][use-onpremises-datasources].

1.	Виртуальная машина с общедоступным DNS-именем и статическим общим портом: сопоставление частных портов
2.	Виртуальная машина с общедоступным DNS-именем без предоставления конечной точки SQL
3.	Виртуальная сеть
	<ol type='a'>
	<li>Облачная виртуальная частная сеть (VPN) Azure со следующей топологией в конце списка. </li>	
	<li>Виртуальная машина с VPN типа "сеть-сеть" для передачи из локальной среды в облако с использованием виртуальной сети Azure.</li>	
	</ol>  
	![Data Factory with Copy Activity][image-data-factory-copy-actvity]

## Фильтрация столбцов с помощью определения структуры
В зависимости от типа таблицы можно указать подмножество столбцов из источника, задав меньшее число столбцов в определении **Structure** определения таблицы, чем число, существующее в базовом источнике данных. Ниже приведена таблица, которая содержит информацию о логике фильтрации столбцов для различных типов таблицы. 

<table>

	<tr>
		<th align="left">Тип таблицы</th>
		<th align="left">Логика фильтрации столбцов</th>
	<tr>

	<tr>
		<td>AzureBlobLocation</td>
		<td>Определение <b>Structure</b> в JSON таблицы должно соответствовать структуре большого двоичного объекта.  Чтобы выбрать подмножество столбцов, используйте функцию сопоставления столбцов, описанную в следующем разделе: правила преобразования - сопоставление столбцов.</td>
	<tr>

	<tr>
		<td>AzureSqlTableLocation и OnPremisesSqlServerTableLocation</td>
		<td align="left">
			<p>Если свойство <b>SqlReaderQuery</b> задано как часть определения действия копирования, необходимо выровнять определение <b>Structure</b> таблицы со столбцами, выбранными в запросе.</p>
			<p>Если свойство <b>SqlReaderQuery</b> не указано, действие копирования автоматически построит запрос SELECT на основе столбцов, указанных в определении <b>Structure</b> определения таблицы.</p>
		</td>
	<tr>

	<tr>
		<td>AzureTableLocation</td>
		<td>
			Раздел <b>Structure</b> в определении таблицы может содержать полный набор или подмножество столбцов в базовой таблице Azure.
		</td>
	<tr>

</table> 

## Правила преобразования - сопоставление столбцов
Можно использовать сопоставление столбцов, чтобы указать, как столбцы в исходной таблице сопоставляются со столбцами в таблице приемника. Оно поддерживает следующие сценарии:

- Сопоставление всех столбцов в "structure" исходной таблицы со "structure" целевой таблицы.
- Подмножество столбцов в "structure" исходной таблицы сопоставляется со всей "structure" целевой таблицы.

Оно не поддерживает следующие сценарии и выдает исключение: 

- Меньше или больше столбцов в целевой таблице.
- Повторяющееся сопоставление.
- Результат запроса SQL не содержит имени столбца

#### Пример 1 - сопоставление столбцов из сервера SQL Server с большим двоичным объектом Azure
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

Если не указать **fileName** для **входной таблицы**, все файлы или большие двоичные объекты из входной папки (**folderPath**) рассматриваются как входные данные. Если указать fileName в JSON, только указанный файл или большой двоичный объект рассматриваются как входные данные. Образцы файлов см. в [учебнике][adf-tutorial].

Если вы не зададите параметр **fileName** для **выходной таблицы**, формат файлов, созданных в папке **folderPath**, будет следующим: Data.<Guid>.txt (например, : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

Для динамической установки папки **folderPath** и имени **fileName** на основе времени **SliceStart** используйте свойство **partitionedBy**. В следующем примере **folderPath** использует год, месяц и день из SliceStart (время начала обработки среза), а fileName использует время (часы) из SliceStart. Например, если срез выполняется для 2014-10-20T08:00:00, для folderName будет установлено значение wikidatagateway/wikisampledataout/2014/10/20, а для fileName - 08.csv. 

  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
    [
    	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

В этом примере действие в конвейере определяется следующим образом. Столбцы из источника, сопоставленные со столбцами в приемнике (**columnMappings**) с помощью свойства **Translator**.

##### Пример - определение сопоставления столбцов

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

![Column Mapping][image-data-factory-column-mapping-1]

##### Пример 2 - сопоставление столбцов с SQL-запросом от сервера SQL Server к большому двоичному объекту Azure
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
				"SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', Time.AddHours(SliceStart, 0))"
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

![Column Mapping 2][image-data-factory-column-mapping-2]

#### Обработка типа данных с помощью действия копирования

Типы данных, указанные в разделе Structure определения таблицы, принимаются только для **BlobSource**.  В таблице ниже описывается, как типы данных обрабатываются для других типов источника и приемника.

<table>	
	<tr>
		<th align="left">Источник/приемник</th>
		<th align="left">Логика обработки типов данных</th>
	</tr>	

	<tr>
		<td>SqlSource</td>
		<td>Типы данных, определенные в разделе <b>Structure</b> определения таблицы, игнорируются.  Типы данных, определенные в базовой базе данных SQL, будут использоваться для извлечения данных в процессе копирования.</td>
	</tr>

	<tr>
		<td>SqlSink</td>
		<td>Типы данных, определенные в разделе <b>Structure</b> определения таблицы, игнорируются.  Типы данных в базовых исходном и целевом расположениях будут сравниваться, и в случае несоответствия типов будет выполняться неявное преобразование типов.</td>
	</tr>

	<tr>
		<td>BlobSource</td>
		<td><p>При переносе из <b>BlobSource</b> в <b>BlobSink</b> преобразование типов не выполняется. Типы, определенные в разделе <b>Structure</b> определения таблицы, игнорируются.  Для назначений, отличных от <b>BlobSink</b>, типы данных, определенные в разделе <b>Structure</b> определения таблицы, будут приниматься.</p>
		<p>
		Если раздел <b>Structure</b> не задан в определении таблицы, обработка типов зависит от свойства <b>format</b> в <b>BlobSink</b>:
		</p>
		<ul>
			<li> <b>TextFormat:</b> все типы столбцов обрабатываются как строка, а все имена столбцов заданы как "Prop_<0-N>"</li> 
			<li><b>AvroFormat:</b> используйте встроенные типы столбцов и имена в файле Avro.</li> 
			<li><b>JsonFormat:</b> все типы столбцов рассматриваются как строки; используйте встроенные имена столбцов в JSON-файле.</li>
		</ul>
		</td>
	</tr>

	<tr>
		<td>BlobSink</td>
		<td>Типы данных, определенные в разделе <b>Structure</b> определения входной таблицы, игнорируются.  Будут использоваться типы данных, определенные в базовом хранилище входных данных.  Столбцы будут определены как допускающие значение NULL для сериализации Avro.</td>
	</tr>

	<tr>
		<td>AzureTableSource</td>
		<td>Типы данных, определенные в разделе <b>Structure</b> определения таблицы, игнорируются.  Будут использоваться типы данных, определенные в базовой таблице Azure.</td>
	</tr>

	<tr>
		<td>AzureTableSink</td>
		<td>Типы данных, определенные в разделе <b>Structure</b> определения таблицы, игнорируются.  Будут использоваться типы данных, определенные в базовом хранилище входных данных.</td>
	</tr>

</table>

## Ограничения

> [WACOM.NOTE] Когда конвейер приостанавливается или удаляется, или когда удаляется фабрика данных, текущая операция копирования не приостанавливается. Она продолжит выполнение до завершения. Однако, операция копирования, которая задействует локальный источник данных, может быть остановлена в случае перезапуска службы шлюза управления данными с помощью диспетчера конфигураций шлюза управления данными или приложения "Службы". 



## Пошаговые руководства
См. в разделе [Приступая к работе с фабрикой данных Azure][adfgetstarted], как с помощью действия копирования скопировать данные из хранилища больших двоичных объектов Azure в базу данных SQL Azure.
 
Пошаговое руководство, показывающее, как скопировать данные из локальной базы данных SQL Server в хранилище больших двоичных объектов Azure, используя действие копирования, см. в разделе [Включение конвейеров для работы с локальными данными][use-onpremises-datasources]



[adfgetstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[image-data-factory-copy-actvity]: ./media/data-factory-copy-activity/VPNTopology.png
[image-data-factory-column-mapping-1]: ./media/data-factory-copy-activity/ColumnMappingSample1.png
[image-data-factory-column-mapping-2]: ./media/data-factory-copy-activity/ColumnMappingSample2.png
