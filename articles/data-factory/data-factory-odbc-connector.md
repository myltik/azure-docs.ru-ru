<properties 
	pageTitle="Перемещение данных из хранилищ данных ODBC | Фабрика данных Azure" 
	description="Узнайте, как перемещать данные из хранилищ данных ODBC с помощью фабрики данных Azure." 
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
	ms.date="09/12/2016" 
	ms.author="jingwang"/>

# Перемещение данных из хранилищ данных ODBC с помощью фабрики данных Azure
В этой статье описано использование действия копирования в фабрике данных Azure для перемещения данных из локального хранилища данных ODBC в другое хранилище данных. В этой статье мы продолжим тему о [действиях перемещения данных](data-factory-data-movement-activities.md), в которой приведены общие сведения о перемещении данных с помощью действия копирования и поддерживаемых сочетаниях хранилищ данных.

В настоящее время фабрика данных поддерживает только перемещение данных из локального хранилища данных ODBC в другие хранилища данных. Она не поддерживает перемещение данных из других хранилищ данных в локальное хранилище данных ODBC.


## Включение соединения
Служба фабрики данных поддерживает подключение к локальным источникам ODBC с помощью шлюза управления данными. В статье [Перемещение данных между локальными и облачными ресурсами](data-factory-move-data-between-onprem-and-cloud.md) приведены сведения о шлюзе управления данными и пошаговые инструкции по его настройке. Используйте шлюз для подключения к локальному хранилищу данных ODBC, даже если он размещен на виртуальной машине IaaS Azure.

Шлюз можно установить на тот же локальный компьютер или виртуальную машину Azure, что и хранилище данных ODBC. Тем не менее рекомендуется установить шлюз на отдельный компьютер или виртуальную машину IaaS SQL Azure, чтобы избежать конфликта ресурсов, а также для повышения производительности. При установке шлюза на отдельный компьютер у компьютера должен быть доступ к машине с хранилищем данных ODBC.

Помимо шлюза управления данными на компьютере с шлюзом необходимо также установить драйвер ODBC для хранилища данных.

> [AZURE.NOTE] Советы по устранению неполадок, связанных со шлюзом или подключением, см. в разделе [Устранение неполадок в работе шлюза](data-factory-data-management-gateway.md#troubleshoot-gateway-issues).

## Мастер копирования данных
Самый простой способ создать конвейер, копирующий данные из источника ODBC, — использовать мастер копирования данных. В статье [Руководство. Создание конвейера с действием копирования с помощью мастера копирования фабрики данных](data-factory-copy-data-wizard-tutorial.md) приведены краткие пошаговые указания по созданию конвейера с помощью мастера копирования данных.

Ниже приведены примеры с определениями JSON, которые можно использовать для создания конвейера с помощью [портала Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) или [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Вы узнаете, как копировать данные из источника ODBC в хранилище BLOB-объектов Azure. Однако данные можно копировать в любой из указанных [здесь](data-factory-data-movement-activities.md#supported-data-stores) приемников. Это делается с помощью действия копирования в фабрике данных Azure.


## Пример. Копирование данных из хранилища данных ODBC в BLOB-объект Azure

Из этого примера вы узнаете, как копировать данные из хранилища данных ODBC в хранилище BLOB-объектов Azure. Однако данные можно **напрямую** копировать в любой из указанных [здесь](data-factory-data-movement-activities.md#supported-data-stores) приемников. Это делается с помощью действия копирования в фабрике данных Azure.
 
Образец состоит из следующих сущностей фабрики данных.

1.	Связанная служба типа [OnPremisesOdbc](#odbc-linked-service-properties).
2.	Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Входной [набор данных](data-factory-create-datasets.md) типа [RelationalTable](#odbc-dataset-type-properties).
4.	Выходной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	[Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [RelationalSource](#odbc-copy-activity-type-properties) и [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

В примере данные из результата выполнения запроса к хранилищу данных ODBC каждый час копируются в BLOB-объект. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

Сначала настройте шлюз управления данными. Инструкции приведены в статье [Перемещение данных между локальными источниками и облаком с помощью шлюза управления данными](data-factory-move-data-between-onprem-and-cloud.md).

**Связанная служба ODBC**. В этом примере используется обычная проверка подлинности. Сведения о различных типах аутентификации, которые можно использовать, см. в разделе [Свойства связанной службы ODBC](#odbc-linked-service-properties).

	{
	    "name": "OnPremOdbcLinkedService",
	    "properties":
	    {
	        "type": "OnPremisesOdbc",
	        "typeProperties":
	        {
	            "authenticationType": "Basic",
	            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
	            "userName": "username",
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

**Входной набор данных ODBC**

В примере предполагается, что таблица MyTable уже создана в базе данных ODBC и содержит столбец с именем timestampcolumn для данных временных рядов.

Если параметру external присвоить значение true, фабрика данных воспримет этот набор данных как внешний и созданный не в результате какого-либо действия в этой службе.
	
	{
	    "name": "ODBCDataSet",
	    "properties": {
	        "published": false,
	        "type": "RelationalTable",
	        "linkedServiceName": "OnPremOdbcLinkedService",
	        "typeProperties": {},
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



**Выходной набор данных BLOB-объекта Azure**

Данные записываются в новый BLOB-объект каждый час (frequency: hour, interval: 1). Путь к папке BLOB-объекта вычисляется динамически на основе времени начала обрабатываемого среза. В пути к папке используется год, месяц, день и час времени начала.

	{
	    "name": "AzureBlobOdbcDataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/odbc/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	            "format": {
	                "type": "TextFormat",
	                "rowDelimiter": "\n",
	                "columnDelimiter": "\t"
	            },
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
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}



**Конвейер с действием копирования**

Конвейер содержит действие копирования, которое использует эти входной и выходной наборы данных и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **RelationalSource**, а для типа **sink** — значение **BlobSink**. SQL-запрос, указанный для свойства **query**, выбирает для копирования данные за последний час.
	
	{
	    "name": "CopyODBCToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "RelationalSource",
	                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "OdbcDataSet"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobOdbcDataSet"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1
	                },
	                "scheduler": {
	                    "frequency": "Hour",
	                    "interval": 1
	                },
	                "name": "OdbcToBlob"
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z"
	    }
	}



## Свойства связанной службы ODBC

В следующей таблице содержится описание элементов JSON, которые относятся к связанной службе ODBC.

| Свойство | Описание | Обязательно |
| -------- | ----------- | -------- | 
| type | Для свойства type необходимо задать значение **OnPremisesOdbc**. | Да |
| connectionString | Учетные данные в строке подключения, не используемые для получения доступа, а также дополнительные зашифрованные учетные данные. Примеры приведены в следующих разделах. | Да
| credential | Учетные данные в строке подключения, используемые для получения доступа и указанные в формате "драйвер-определенное свойство-значение". Пример: “Uid=<ИД\_пользователя>;Pwd=<пароль>;RefreshToken=<секретный\_маркер\_обновления>;”. | Нет
| authenticationType | Тип проверки подлинности, используемый для подключения к хранилищу данных ODBC. Возможными значениями являются: "Анонимная" и "Обычная". | Да | 
| Имя пользователя | При использовании обычной проверки подлинности укажите имя пользователя. | Нет | 
| пароль | Введите пароль для учетной записи пользователя, указанной для выбранного имени пользователя. | Нет | 
| gatewayName | Имя шлюза, который следует использовать службе фабрики данных для подключения к хранилищу данных ODBC. | Да |


Дополнительные сведения о настройке учетных данных для локального хранилища данных ODBC см. в статье [Перемещение данных между локальными источниками и облаком с помощью шлюза управления данными](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security).

### Использовать обычную проверку подлинности

	{
	    "name": "odbc",
	    "properties":
	    {
	        "type": "OnPremisesOdbc",
	        "typeProperties":
	        {
	            "authenticationType": "Basic",
	            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
	            "userName": "username",
	            "password": "password",
	            "gatewayName": "mygateway"
	        }
	    }
	}

### Использовать обычную проверку подлинности и шифрование учетных данных
Учетные данные можно зашифровать с помощью командлета [New-AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) (Azure PowerShell версии 1.0) или [New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/dn834940.aspx) (Azure PowerShell версии 0.9 или более ранней).

	{
	    "name": "odbc",
	    "properties":
	    {
	        "type": "OnPremisesOdbc",
	        "typeProperties":
	        {
	            "authenticationType": "Basic",
	            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
	            "gatewayName": "mygateway"
	        }
	    }
	}


### Использовать анонимную проверку подлинности

	{
	    "name": "odbc",
	    "properties":
	    {
	        "type": "OnPremisesOdbc",
	        "typeProperties":
	        {
	            "authenticationType": "Anonymous",
	            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
	            "credential": "UID={uid};PWD={pwd}",
	            "gatewayName": "mygateway"
	        }
	    }
	}



## Свойства типа "Набор данных ODBC"

Полный список разделов и свойств, используемых для определения наборов данных, см. в статье [Наборы данных](data-factory-create-datasets.md). Разделы структуры, доступности и политики JSON набора данных одинаковы для всех типов наборов данных (SQL Azure, большие двоичные объекты Azure, таблицы Azure и т. д.).

Раздел **typeProperties** во всех типах наборов данных разный. В нем содержится информация о расположении данных в хранилище данных. Раздел typeProperties набора данных с типом **RelationalTable** (который включает набор данных ODBC) имеет следующие свойства.

| Свойство | Описание | Обязательно |
| -------- | ----------- | -------- |
| tableName | Имя таблицы в хранилище данных ODBC. | Да | 

## Свойства типа "Действие копирования ODBC"

Полный список разделов и свойств, используемых для определения действий, см. в статье [Создание конвейеров](data-factory-create-pipelines.md). Свойства (такие как имя, описание, входные и выходные таблицы, политики и т. д.) доступны для всех типов действий.

С другой стороны, свойства, доступные в разделе **typeProperties** действия, зависят от конкретного типа действия. Для действия копирования они различаются в зависимости от типов источников и приемников.

В случае действия копирования, когда источник относится к типу **RelationalSource** (который содержит ODBC), в разделе typeProperties доступны следующие свойства.

| Свойство | Описание | Допустимые значения | Обязательно |
| -------- | ----------- | -------------- | -------- |
| запрос | Используйте пользовательский запрос для чтения данных. | Строка запроса SQL. Например, select * from MyTable. | Да | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Сопоставление типов для ODBC

Как упоминалось в статье о [действиях перемещения данных](data-factory-data-movement-activities.md), во время копирования типы источников автоматически преобразовываются в типы приемников. Такое преобразование выполняется в два этапа:

1. Преобразование собственных типов источников в тип .NET.
2. Преобразование типа .NET в собственный тип приемника.

При перемещении данных из хранилищ данных ODBC типы данных ODBC сопоставляются с типами .NET. См. статью [Сопоставления типов данных ODBC](https://msdn.microsoft.com/library/cc668763.aspx).


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## Хранилище GE Historian
Создание связанной службы ODBC для связи хранилища данных [Proficy Historian GE (теперь GE Historian)](http://www.geautomation.com/products/proficy-historian) с фабрикой данных Azure выполняется, как показано в следующем примере:

	{
	    "name": "HistorianLinkedService",
	    "properties":
	    {
	        "type": "OnPremisesOdbc",
	        "typeProperties":
	        {
			    "connectionString": "DSN=<name of the GE Historian store>",
			    "gatewayName": "<gateway name>",
			    "authenticationType": "Basic",
			    "userName": "<user name>",
			    "password": "<password>"
	        }
	    }
	}

Установите шлюз управления данными на локальном компьютере и зарегистрируйте этот шлюз на портале. Шлюз, установленный на локальном компьютере, использует драйвер ODBC для GE Historian для подключения к хранилищу данных GE Historian. Поэтому необходимо установить драйвер на компьютере шлюза. Подробные сведения см. в разделе [Включение соединения](#enabling-connectivity).

Прежде чем использовать хранилище GE Historian в решении фабрики данных, убедитесь, что шлюз может подключаться к хранилищу данных с помощью инструкций в следующем разделе.

В начале статьи приводится подробный обзор использования хранилищ данных ODBC в качестве исходных хранилищ данных в ходе операции копирования.

## Устранение проблем подключения
Для устранения проблем подключения используйте вкладку **Диагностика** **диспетчера конфигурации шлюза управления данными**.

1. Запустите **диспетчер конфигурации шлюза управления данными**. Можно запустить файл "C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared\\ConfigManager.exe" напрямую или выполнить поиск по слову **шлюз**, чтобы найти ссылку на приложение **Шлюз управления данными Майкрософт**, как показано на следующем рисунке.

	.![Поиск шлюза](./media/data-factory-odbc-connector/search-gateway.png)
2. Перейдите на вкладку **Диагностика**.

	.![Диагностика шлюза](./media/data-factory-odbc-connector/data-factory-gateway-diagnostics.png)
3. Выберите **тип** хранилища данных (связанная служба).
4. Укажите тип **аутентификации** и введите **учетные данные** или **строку подключения** для подключения к хранилищу данных.
5. Щелкните **Проверить подключение**, чтобы проверить подключение к хранилищу данных.

## Производительность и настройка  
См. статью [Руководство по настройке производительности действия копирования](data-factory-copy-activity-performance.md), в которой описываются ключевые факторы, влияющие на производительность перемещения данных (действие копирования) в фабрике данных Azure, и различные способы оптимизации этого процесса.

<!---HONumber=AcomDC_0928_2016-->