<properties 
	pageTitle="Перемещение данных из Salesforce с помощью фабрики данных | Microsoft Azure " 
	description="Узнайте, как перемещать данные из Salesforce с использованием фабрики данных Azure." 
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

# Перемещение данных из Salesforce с помощью фабрики данных Azure
В этой статье описано, как переместить данные из Salesforce в любое хранилище данных, указанное в разделе [Поддерживаемые хранилища данных](data-factory-data-movement-activities.md#supported-data-stores) (столбец "Приемники"), с использованием действия копирования в фабрике данных Azure. Эта статья основана на статье о [действиях перемещения данных](data-factory-data-movement-activities.md), в которой приведены общие сведения о перемещении данных с помощью действия копирования и поддерживаемых сочетаниях хранилищ данных.

Сейчас фабрика данных поддерживает только перемещение данных из Salesforce в другие [поддерживаемые хранилища-приемники]((data-factory-data-movement-activities.md#supported-data-stores), но не наоборот.

## Предварительные требования
- Необходимо использовать один из следующих выпусков: Developer Edition, Professional Edition, Enterprise Edition или Unlimited Edition.
- Требуется включить разрешения API. Дополнительные сведения о включении доступа к API в Salesforce с помощью набора разрешений см. [здесь](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/).
- Чтобы скопировать данные из Salesforce в локальное хранилище данных, в локальной среде необходимо установить шлюз управления данными версии 2.0 или более поздней.

## Мастер копирования данных
Самый простой способ создать конвейер, копирующий данные из Salesforce в любое поддерживаемое хранилище-приемник, — использовать мастер копирования данных. В статье [Руководство. Создание конвейера с действием копирования с помощью мастера копирования фабрики данных](data-factory-copy-data-wizard-tutorial.md) приведены краткие пошаговые указания по созданию конвейера с помощью мастера копирования данных.

Ниже приведены примеры с определениями JSON, которые можно использовать для создания конвейера с помощью [портала Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) или [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

## Пример копирования данных из Salesforce в большой двоичный объект Azure
В этом примере данные из Salesforce каждый час копируются в большой двоичный объект Azure. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами. Однако данные можно напрямую копировать в любые приемники, указанные в статье [Перемещение данных и действие копирования. Перенос данных в облако и между облачными хранилищами](data-factory-data-movement-activities.md#supported-data-stores). Для этого применяется действие копирования в фабрике данных Azure.

- Связанная служба типа [Salesforce](#salesforce-linked-service-properties).
- Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
- Входной [набор данных](data-factory-create-datasets.md) типа [RelationalTable](#salesforce-dataset-properties).
- Выходной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- [Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [RelationalSource](#relationalsource-type-properties) и [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

**Связанная служба SalesForce**

В этом примере используется связанная служба **Salesforce**. Список свойств, поддерживаемых этой связанной службой, приведен в разделе [Связанная служба SalesForce](#salesforce-linked-service-properties). Инструкции по получению и сбросу маркера безопасности см. в разделе [Get security token](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) (Получение маркера безопасности).

	{
		"name": "SalesforceLinkedService",
		"properties":
		{
			"type": "Salesforce",
			"typeProperties":
			{
				"username": "<user name>",
				"password": "<password>",
				"securityToken": "<security token>"
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

**Входной набор данных Salesforce**

	{
		"name": "SalesforceInput",
		"properties": {
			"linkedServiceName": "SalesforceLinkedService",
			"type": "RelationalTable",
			"typeProperties": {
				"tableName": "AllDataType__c"  
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

> [AZURE.IMPORTANT]  Имя API для любых настраиваемых объектов должно содержать приставку \_\_c.

![Фабрика данных — подключение к Salesforce — имя API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

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
	            "folderPath": "adfgetstarted/alltypes_c"
	        },
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}


**Конвейер с действием копирования**

Конвейер содержит действие копирования (Copy), которое использует входной и выходной наборы данных (см. выше) и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **RelationalSource**, а для типа **sink** — значение **BlobSink**.

Список свойств, поддерживаемых RelationalSource, см. в разделе [Свойства типа RelationalSource](#relationalsource-type-properties).
	
	{  
		"name":"SamplePipeline",
		"properties":{  
			"start":"2016-06-01T18:00:00",
			"end":"2016-06-01T19:00:00",
			"description":"pipeline with copy activity",
			"activities":[  
			{
				"name": "SaleforceToAzureBlob",
				"description": "Copy from Salesforce to an Azure blob",
				"type": "Copy",
				"inputs": [
				{
					"name": "SalesforceInput"
				}
				],
				"outputs": [
				{
					"name": "AzureBlobOutput"
				}
				],
				"typeProperties": {
					"source": {
						"type": "RelationalSource",
						"query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"				
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

> [AZURE.IMPORTANT]  Имя API для любых настраиваемых объектов должно содержать приставку \_\_c.

![Фабрика данных — подключение к Salesforce — имя API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## Свойства связанной службы Salesforce

В следующей таблице содержится описание элементов JSON, которые относятся к связанной службе Salesforce.

| Свойство | Описание | Обязательно |
| -------- | ----------- | -------- | 
| type | Для свойства type нужно задать значение **Salesforce** | Да | 
| Имя пользователя |Укажите имя пользователя для учетной записи пользователя | Да |
| пароль | Укажите пароль для учетной записи пользователя. | Да |
| securityToken | Укажите маркер безопасности для учетной записи пользователя. Инструкции по получению и сбросу маркера безопасности см. в разделе [Get security token](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) (Получение маркера безопасности). Общие сведения о маркере безопасности см. в разделе [Security and the API](https://developer.salesforce.com/docs/atlas.ru-RU.api.meta/api/sforce_api_concepts_security.htm) (Безопасность и API) | Да | 

## Свойства типа "Набор данных Salesforce"

Полный список разделов и свойств, используемых для определения наборов данных, см. в статье [Наборы данных](data-factory-create-datasets.md). Разделы структуры, доступности и политики JSON набора данных одинаковы для всех типов наборов данных (SQL Azure, BLOB-объекты Azure, таблицы Azure и т. д.).

Раздел **typeProperties** во всех типах наборов данных разный. В нем содержится информация о расположении данных в хранилище данных. Раздел typeProperties для набора данных с типом **RelationalTable** имеет следующие свойства.

| Свойство | Описание | Обязательно |
| -------- | ----------- | -------- |
| tableName | Имя таблицы в Salesforce | Нет (если задан **запрос** свойства **RelationalSource**) | 

> [AZURE.IMPORTANT]  Имя API для любых настраиваемых объектов должно содержать приставку \_\_c.

![Фабрика данных — подключение к Salesforce — имя API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

## Свойства типа RelationalSource
Полный список разделов и свойств, используемых для определения действий, см. в статье [Создание конвейеров](data-factory-create-pipelines.md). Такие свойства, как имя, описание, входные и выходные таблицы, различные политики и т. д., доступны для всех типов действий.

То, какие свойства будут доступны в разделе typeProperties, зависит от типа действия, а в случае с действием копирования — еще и от типов источников и приемников.

В случае действия копирования, когда источник относится к типу **RelationalSource** (который содержит Salesforce), в разделе typeProperties доступны следующие свойства.

| Свойство | Описание | Допустимые значения | Обязательно |
| -------- | ----------- | -------------- | -------- |
| запрос | Используйте пользовательский запрос для чтения данных. | Запрос SQL-92 или запрос, написанный на [объектно-ориентированном языке запросов Salesforce (SOQL)](https://developer.salesforce.com/docs/atlas.ru-RU.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm). Например, select * from MyTable\_\_c | Нет (если для **dataset** задано значение **tableName**) |

> [AZURE.IMPORTANT]  Имя API для любых настраиваемых объектов должно содержать приставку \_\_c. <br><br> Используйте язык SOQL при указании запроса с использованием предложения WHERE в столбце даты и времени. Например, $$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', WindowStart, WindowEnd). Или запрос SQL, например, $$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts'{0:yyyy-MM-dd HH:mm:ss}'}} AND LastModifiedDate < {{ts'{1:yyyy-MM-dd HH:mm:ss}'}}', WindowStart, WindowEnd).

![Фабрика данных — подключение к Salesforce — имя API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## Извлечение данных из отчета Salesforce
Из отчетов Salesforce можно извлекать данные, указывая запросы в формате {call "<имя\_отчета>"}, например: "query": "{call "TestReport"}".

## Ограничения запросов Salesforce
Для Salesforce установлены ограничения на общее число запросов API и одновременных запросов API. Дополнительные сведения см. в разделе **API Request Limits** (Ограничения запросов API) статьи [Salesforce API Request Limits](http://resources.docs.salesforce.com/200/20/ru-RU/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) (Ограничения запросов API в Salesforce).
 
Если количество одновременных запросов превышает ограничение, выполняется регулирование и возникают случайные ошибки. В случае превышения ограничения на общее число запросов учетная запись Salesforce блокируется на 24 часа. Кроме того, в обоих случаях вы можете получить ошибку REQUEST\_LIMIT\_EXCEEDED.
 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Сопоставление типов для Salesforce
Тип данных Salesforce | Тип данных на основе .NET
--------------- | ---------------
Автонумерация | Строка
Флажок | Логический
Валюта | Double
Дата | DateTime
Дата и время | DateTime
Email | Строка
Идентификатор | Строка
Связь для подстановки | Строка
Список множественного выбора | Строка
Number | Double
Процент | Double
Номер телефона | Строка
Список выбора | Строка
текст | Строка
Текстовое поле | Строка
Текстовое поле (длинное) | Строка
Текстовое поле (расширенное) | Строка
Текст (зашифрованный) | Строка
URL-адрес | Строка

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]
[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## Производительность и настройка  
См. статью [Руководство по настройке производительности действия копирования](data-factory-copy-activity-performance.md), в которой описываются ключевые факторы, влияющие на производительность перемещения данных (действие копирования) в фабрике данных Azure, и различные способы оптимизации этого процесса.

<!---HONumber=AcomDC_0727_2016-->