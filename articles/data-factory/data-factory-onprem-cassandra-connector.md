<properties 
	pageTitle="Перемещение данных из базы данных Cassandra с помощью фабрики данных | Microsoft Azure" 
	description="Узнайте, как перемещать данные из локальной базы данных Cassandra с помощью фабрики данных Azure." 
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
	ms.date="07/07/2016" 
	ms.author="spelluru"/>

# Перемещение данных из локальной базы данных Cassandra с помощью фабрики данных Azure
В этой статье описывается, как переместить данные из локальной базы данных Cassandra в любое хранилище данных, указанное в разделе [Поддерживаемые хранилища данных](data-factory-data-movement-activities.md#supported-data-stores) (столбец "Приемники"), с использованием действия копирования в фабрике данных Azure. Эта статья основана на статье о [действиях перемещения данных](data-factory-data-movement-activities.md), в которой приведены общие сведения о перемещении данных с помощью действия копирования и поддерживаемых сочетаниях хранилищ данных.

Сейчас фабрика данных поддерживает только перемещение данных из базы данных Cassandra в другие [поддерживаемые хранилища-приемники](data-factory-data-movement-activities.md#supported-data-stores), но не наоборот.

## Предварительные требования
Чтобы служба фабрики данных Azure могла подключаться к локальной базе данных Cassandra, необходимо установить следующие компоненты.

- Шлюз управления данными версии 2.0 или выше на том же компьютере, на котором размещена база данных, или на отдельном компьютере во избежание конкуренции за ресурсы. Шлюз управления данными — это программное обеспечение, которое обеспечивает безопасное и управляемое подключение локальных источников данных к облачным службам. Сведения о шлюзе управления данными см. в статье [Перемещение данных между локальными источниками и облаком](data-factory-move-data-between-onprem-and-cloud.md).
  
	Вместе со шлюзом автоматически устанавливается драйвер ODBC Microsoft Cassandra, который используется для подключения к базе данных Cassandra.

> [AZURE.NOTE] Советы по устранению неполадок, связанных со шлюзом или подключением, см. в разделе [Устранение неполадок в работе шлюза](data-factory-data-management-gateway.md#troubleshoot-gateway-issues).

## Мастер копирования данных
Самый простой способ создать конвейер, копирующий данные из базы данных Cassandra в любое поддерживаемое хранилище-приемник, — использовать мастер копирования данных. В статье [Руководство. Создание конвейера с действием копирования с помощью мастера копирования фабрики данных](data-factory-copy-data-wizard-tutorial.md) приведены краткие пошаговые указания по созданию конвейера с помощью мастера копирования данных.

Ниже приведены примеры с определениями JSON, которые можно использовать для создания конвейера с помощью [портала Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) или [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

## Пример копирования данных из базы данных Cassandra в большой двоичный объект
В этом примере данные из базы данных Cassandra каждый час копируются в большой двоичный объект Azure. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами. Однако данные можно напрямую копировать в любые приемники, указанные в статье [Перемещение данных и действие копирования. Перенос данных в облако и между облачными хранилищами](data-factory-data-movement-activities.md#supported-data-stores). Для этого применяется действие копирования в фабрике данных Azure.

- Связанная служба типа [OnPremisesCassandra](#onpremisescassandra-linked-service-properties).
- Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
- Входной [набор данных](data-factory-create-datasets.md) типа [CassandraTable](#cassandratable-properties).
- Выходной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- [Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [CassandraSource](#cassandrasource-type-properties) и [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

**Связанная служба Cassandra**

В этом примере используется связанная служба **Cassandra**. Список свойств, поддерживаемых этой связанной службой, приведен в разделе [Свойства связанной службы OnPremisesCassandra](#onpremisescassandra-linked-service-properties).

	{
    	"name": "CassandraLinkedService",
    	"properties":
    	{
			"type": "OnPremisesCassandra",
			"typeProperties":
			{
				"authenticationType": "Basic",
				"host": "mycassandraserver",
				"port": 9042,
				"username": "user",
				"password": "password",
				"gatewayName": "mygateway"
        	}
    	}
	}


**Связанная служба хранения Azure**

	{
		"name": "AzureStorageLinkedService",
		"properties": {
		"type": "AzureStorage",
			"typeProperties": {
				"connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
			}
		}
	}

**Входной набор данных Cassandra**

	{
		"name": "CassandraInput",
		"properties": {
			"linkedServiceName": "CassandraLinkedService",
			"type": "CassandraTable",
			"typeProperties": {
				"tableName": "mytable",
				"keySpace": "mykeyspace" 
			},
			"availability": {
				"frequency": "Hour",
				"interval": 1
			},
			"external": true,
			"policy": {
				"externalData": {
					"retryInterval": "00:01:00",
					"retryTimeout": "00:10:00",
					"maximumRetry": 3
				}
			}
		}
	}

Если для параметра **external** задать значение **true**, фабрика данных воспримет этот набор данных как внешний, который создан не в результате какого-либо действия в этой службе.

**Выходной набор данных BLOB-объекта Azure**

Данные записываются в новый BLOB-объект каждый час (frequency: hour, interval: 1).

	{
		"name": "AzureBlobOutput",
		"properties":
		{
			"type": "AzureBlob",
			"linkedServiceName": "AzureStorageLinkedService",
			"typeProperties":
			{
				"folderPath": "adfgetstarted/fromcassandra"
			},
			"availability":
			{
				"frequency": "Hour",
				"interval": 1
			}
		}
	}


**Конвейер с действием копирования**

Конвейер содержит действие копирования (Copy), которое использует входной и выходной наборы данных (см. выше) и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **CassandraSource**, а для типа **sink** — значение **BlobSink**.

Список свойств, поддерживаемых RelationalSource, см. в разделе [Свойства типа RelationalSource](#cassandrasource-type-properties).
	
	{  
		"name":"SamplePipeline",
		"properties":{  
			"start":"2016-06-01T18:00:00",
			"end":"2016-06-01T19:00:00",
			"description":"pipeline with copy activity",
			"activities":[  
			{
				"name": "CassandraToAzureBlob",
				"description": "Copy from Cassandra to an Azure blob",
				"type": "Copy",
				"inputs": [
				{
					"name": "CassandraInput"
				}
				],
				"outputs": [
				{
					"name": "AzureBlobOutput"
				}
				],
				"typeProperties": {
					"source": {
						"type": "CassandraSource",
						"query": "select id, firstname, lastname from mykeyspace.mytable"
		
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
## Свойства связанной службы OnPremisesCassandra

В следующей таблице содержится описание элементов JSON, которые относятся к связанной службе Cassandra.

| Свойство | Описание | Обязательно |
| -------- | ----------- | -------- | 
| type | Для свойства type необходимо задать значение **OnPremisesCassandra** | Да |
| host | Один или несколько IP-адресов или имен узлов серверов Cassandra.<br/><br/>Укажите через запятую список IP-адресов или имен узлов для одновременного подключения ко всем серверам | Да | 
| порт | TCP-порт, используемый сервером Cassandra для прослушивания клиентских подключений | Нет. Значение по умолчанию — 9042 |
| authenticationType | Укажите тип Basic или Anonymous | Да |
| Имя пользователя |Укажите имя пользователя для учетной записи пользователя | Да (если для свойства authenticationType задано значение Basic) |
| пароль | Укажите пароль для учетной записи пользователя. | Да (если для свойства authenticationType задано значение Basic) |
| gatewayName | Имя шлюза, который будет использоваться для подключения к локальной базе данных Cassandra | Да |
| encryptedCredential | Учетные данные, зашифрованные шлюзом | Нет | 

## Свойства CassandraTable

Полный список разделов и свойств, используемых для определения наборов данных, см. в статье [Наборы данных](data-factory-create-datasets.md). Разделы структуры, доступности и политики JSON набора данных одинаковы для всех типов наборов данных (SQL Azure, BLOB-объекты Azure, таблицы Azure и т. д.).

Раздел **typeProperties** во всех типах наборов данных разный. В нем содержится информация о расположении данных в хранилище данных. Раздел typeProperties набора данных с типом **CassandraTable** имеет следующие свойства.

| Свойство | Описание | Обязательно |
| -------- | ----------- | -------- |
| keyspace | Имя пространства ключей или схемы в базе данных Cassandra | Да (если **запрос** к **CassandraSource** не определен) |
| tableName | Имя таблицы в базе данных Cassandra | Да (если **запрос** к **CassandraSource** не определен) |


## Свойства типа CassandraSource
Полный список разделов и свойств, используемых для определения действий, см. в статье [Создание конвейеров](data-factory-create-pipelines.md). Такие свойства, как имя, описание, входные и выходные таблицы, различные политики и т. д., доступны для всех типов действий.

То, какие свойства будут доступны в разделе typeProperties, зависит от типа действия, а в случае с действием копирования — еще и от типов источников и приемников.

В случае действия копирования, когда источник относится к типу **CassandraSource**, в разделе typeProperties доступны указанные ниже свойства.

| Свойство | Описание | Допустимые значения | Обязательно |
| -------- | ----------- | -------------- | -------- |
| запрос | Используйте пользовательский запрос для чтения данных. | Запрос SQL-92 или CQL. См. [справочные материалы по CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Если используется запрос SQL, таблицу, к которой необходимо отправить запрос, укажите в формате **имя\_пространства\_ключей.имя\_таблицы** | Нет (если в наборе данных определены свойства tableName и keyspace) |
| consistencyLevel | Определяет количество реплик, которые должны ответить на запрос на чтение перед возвращением данных в клиентское приложение. Чтобы выполнить запрос на чтение, база данных Cassandra проверяет наличие указанного количества реплик для данных | ONE, TWO, THREE, QUORUM, ALL, LOCAL\_QUORUM, EACH\_QUORUM, LOCAL\_ONE. Дополнительные сведения см. в статье [Configuring data consistency](http://docs.datastax.com/en//cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) (Настройка согласованности данных) | Нет. Значение по умолчанию — ONE |  


### Сопоставление типов для Cassandra
Тип данных Cassandra | Тип данных на основе .NET
--------------- | ---------------
ASCII |	Строка 
BIGINT | Int64
BLOB | Byte
BOOLEAN | Логический
DECIMAL | Decimal
DOUBLE | Double
FLOAT | Single
INET | Строка
INT | Int32
TEXT | Строка
TIMESTAMP | DateTime
TIMEUUID | Guid
UUID | Guid
VARCHAR | Строка
VARINT | Decimal

> [AZURE.NOTE]  
Дополнительные сведения о типах коллекций (сопоставлениях, наборах, списках и т. д.) см. в разделе [Работа с коллекциями с использованием виртуальной таблицы](#work-with-collections-using-virtual-table).
> 
> Пользовательские типы не поддерживаются.
> 
> Длина столбца двоичного кода и столбца строки не может превышать 4000 знаков.

## Работа с коллекциями с использованием виртуальной таблицы
В фабрике данных Azure используется встроенный драйвер ODBC, который позволяет подключаться к базе данных Cassandra и копировать из нее данные. Для типов коллекций, в том числе сопоставлений, наборов и списков, драйвер ренормализует данные в виде соответствующих виртуальных таблиц. В частности, если таблица содержит столбцы с данными типа коллекции, драйвер создает следующие виртуальные таблицы.

-	**Базовая таблица**. В ней содержатся те же данные, что и в исходной таблице, кроме столбцов данных типа коллекции. Имена базовой таблицы и таблицы, которую она представляет, совпадают.
-	**Виртуальная таблица**. Эта таблица создается для каждого столбца с данными типа коллекции. В ней представлены вложенные данные в развернутом виде. Виртуальным таблицам, представляющим коллекции, присваиваются имена в следующем формате: "имя\_исходной\_таблицы_\_vt\__имя\_столбца".

Виртуальные таблицы ссылаются на данные в исходной таблице, что позволяет драйверу подключаться к денормализованным данным. Дополнительные сведения см. в разделе "Примеры". Доступ к содержимому коллекций Cassandra можно получить при помощи отправки запроса и соединения виртуальных таблиц.

Просмотреть список таблиц, в том числе виртуальных, в базе данных Cassandra и содержащихся в них данных можно с помощью [мастера копирования](data-factory-data-movement-activities.md#data-factory-copy-wizard). Он также позволяет создать запрос и просмотреть результат выполнения.

### Пример
Ниже приведен пример таблицы ExampleTable в базе данных Cassandra. В этой таблице содержатся такие столбцы: столбец первичного ключа pk\_int (целое число), текстовый столбец "Значение", столбец "Список", "Сопоставление" и StringSet.

pk\_int | Значение | список | Сопоставление |	StringSet
------ | ----- | ---- | --- | --------
1 | "пример значения 1" | ["1", "2", "3"] | {"S1": "a", "S2": "b"} | {"A", "B", "C"}
3 | "пример значения 3" | ["100", "101", "102", "105"] | {"S1": "t"} | {"A", "E"}

Для представления такой одной таблицы драйвер создает несколько виртуальных таблиц. Столбцы внешнего ключа в виртуальных таблицах ссылаются на столбцы первичного ключа в исходных таблицах и указывают, какая строка исходной таблицы соответствует строке виртуальной таблицы.

Ниже приведен пример первой базовой виртуальной таблицы ExampleTable. В этой таблице содержатся те же данные, что и в исходной, но без коллекций. Они развернуты в других виртуальных таблицах.

pk\_int | Значение
------ | -----
1 | "пример значения 1"
3 | "пример значения 3"

Ниже приведены виртуальные таблицы, в которых ренормализированы данные из столбцов "Список", "Сопоставление" и StringSet. Столбцы, имена которых заканчиваются на \_index или \_key, указывают на позицию данных в исходном списке или сопоставлении. Столбцы, имена которых заканчиваются на \_value, содержат развернутые данные из коллекции.

#### Таблица ExampleTable\_vt\_List
pk\_int | List\_index | List\_value
------ | ---------- | ----------
1 | 0 | 1
1 | 1 | 2
1 | 2 | 3
3 | 0 | 100
3 | 1 | 101
3 | 2 | 102
3 | 3 | 103

#### Таблица ExampleTable\_vt\_Map
pk\_int | Map\_key | Map\_value
------ | ------- | ---------
1 | S1 | Файл ,
1 | S2 | b
3 | S1 | t

#### Таблица ExampleTable\_vt\_StringSet:
pk\_int | StringSet\_value
------ | ---------------
1 | Файл ,
1 | B
1 | C
3 | Файл ,
3 | E





[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]
[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## Производительность и настройка  
См. [руководство по настройке производительности действия копирования](data-factory-copy-activity-performance.md), в котором описываются ключевые факторы, влияющие на производительность перемещения данных (действие копирования) в фабрике данных Azure, и различные способы оптимизации этого процесса.

<!---HONumber=AcomDC_0803_2016-->