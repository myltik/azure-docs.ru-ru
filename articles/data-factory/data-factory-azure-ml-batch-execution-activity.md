<properties 
	pageTitle="Создание прогнозирующих конвейеров с помощью действия пакетного выполнения Машинного обучения Azure | Microsoft Azure" 
	description="Описывается, как создавать прогнозирующие конвейеры с помощью фабрики данных Azure и машинного обучения Azure." 
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
	ms.date="08/24/2015" 
	ms.author="spelluru"/>

# Создание прогнозирующих конвейеров с помощью действия пакетного выполнения Машинного обучения Azure   
## Обзор

> [AZURE.NOTE]См. статьи [Введение в фабрику данных Azure](data-factory-introduction.md) и [Построение первого конвейера](data-factory-build-your-first-pipeline.md), чтобы быстро приступить к работе со службой фабрики данных Azure.

Фабрика данных Azure позволяет легко создавать конвейеры, в которых для прогнозной аналитики используется опубликованная веб-служба [Машинного обучения Azure][azure-machine-learning]. С помощью фабрики данных Azure можно использовать конвейеры больших данных (например, Pig и Hive) для обработки данных, принятых из различных источников данных, и использовать веб-службы Машинного обучения Azure для создания прогнозов по данным в пакете.

Можно управлять перемещением и обработкой данных с помощью фабрики данных Azure, а затем осуществлять пакетное выполнение, используя Машинное обучение Azure. Для этого необходимо сделать следующее.

1. Создайте связанную службу Машинного обучения Azure. Вам потребуется следующее:
	1. **Универсальный код ресурса (URI) запроса** для API пакетного выполнения. Универсальный код ресурса (URI) запроса можно найти, щелкнув ссылку **ВЫПОЛНЕНИЕ ПАКЕТА** на странице веб-служб, как показано ниже.
	1. **Ключ API** для опубликованной веб-службы Машинного обучения Azure. Чтобы найти ключ API, щелкните опубликованную веб-службу. 
 2. Используйте действие **AzureMLBatchExecution**.

	![Панель мониторинга машинного обучения](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

	![Универсальный код ресурса (URI) пакета](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)


## Сценарий: эксперименты с вводом-выводом веб-службы, ссылающейся на данные в хранилище больших двоичных объектов Azure
В этом сценарии веб-служба Машинного обучения Azure делает прогнозы с использованием данных из файла в хранилище больших двоичных объектов Azure и сохраняет результаты прогнозирования в хранилище больших двоичных объектов. Следующий код JSON определяет конвейер фабрики данных Azure действием AzureMLBatchExecution. У действия входной набор данных **DecisionTreeInputBlob** и выходной — **DecisionTreeResultBlob**. **DecisionTreeInputBlob** передается в веб-службу в качестве входных данных с помощью свойства JSON **webServiceInput**, а **DecisionTreeResultBlob** в качестве выходных данных — с помощью свойства JSON **webServiceOutputs**. Только наборы данных, которые являются входными и выходными для действия, могут передаваться как входные и выходные данные веб-службы.


	{
	  "name": "PredictivePipeline",
	  "properties": {
	    "description": "use AzureML model",
	    "activities": [
	      {
	        "name": "MLActivity",
	        "type": "AzureMLBatchExecution",
	        "description": "prediction analysis on batch input",
	        "inputs": [
	          {
	            "name": "DecisionTreeInputBlob"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "DecisionTreeResultBlob"
	          }
	        ],
	        "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties":
            {
                "webServiceInput": "DecisionTreeInputBlob ",
                "webServiceOutputs": {
                    "output1": "DecisionTreeResultBlob "
                }                
            },
	        "policy": {
	          "concurrency": 3,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 1,
	          "timeout": "02:00:00"
	        }
	      }
	    ],
	    "start": "2015-02-13T00:00:00Z",
	    "end": "2015-02-14T00:00:00Z"
	  }
	}

> [AZURE.NOTE]Только входные и выходные данные действия AzureMLBatchExecution могут передаваться как параметры веб-службы. Например, в приведенном выше фрагменте JSON DecisionTreeInputBlob —входные данные действия AzureMLBatchExecution, которые передаются в качестве входных данных в веб-службу через параметр webServiceInput.

### Пример

В этом примере для размещения входных и выходных данных используется служба хранилища Azure.

Прежде чем приступить к этому примеру, рекомендуется изучить учебник [Построение первого конвейера с помощью фабрики данных Azure][adf-build-1st-pipeline] и с помощью редактора фабрики данных создать артефакты фабрики данных (связанные службы, наборы данных, конвейер) для этого примера.
 

1. Создайте **связанную службу** для **службы хранилища Azure**. Если входные и выходные файлы находятся в разных учетных записях хранения, то потребуются две связанные службы. Ниже приведен пример JSON:

		{
		  "name": "StorageLinkedService",
		  "properties": {
		    "type": "AzureStorage",
		    "typeProperties": {
		      "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
		    }
		  }
		}

2. Создайте **входной** **набор данных** фабрики данных Azure. Обратите внимание, что в отличие от некоторых других наборов данных фабрики данных эти должны содержать оба значения **folderPath** и **fileName**. Можно использовать секционирование для выполнения каждого пакета (каждого среза данных) для обработки или создания уникальных входных и выходных файлов. Скорее всего, потребуется включить некоторые вышестоящие действия для преобразования входных данных в формат CSV-файла и его размещения в учетной записи хранения для каждого среза. В этом случае не будут включены параметры **external** и **externalData**, как показано на примере ниже, и ScoringInputBlob будет выходным набором данных другого действия.

		{
		  "name": "DecisionTreeInputBlob",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "azuremltesting/input",
		      "fileName": "in.csv",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Day",
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
	
	Входной CSV-файл должен содержать строку заголовков столбцов. Если вы используете **действие копирования** для создания или перемещения CSV-файла в хранилище больших двоичных объектов, следует установить для свойства **blobWriterAddHeader** приемника значение **true**. Например:
	
	     sink: 
	     {
	         "type": "BlobSink",     
	         "blobWriterAddHeader": true 
	     }
	 
	если CSV-файл не имеет строку заголовков, может появиться следующая ошибка: **Ошибка в действии: ошибка при чтении строки. Непредвиденный токен: StartObject. Путь '', строка 1, позиция 1**.
3. Создайте **выходной** **набор данных** фабрики данных Azure. В этом примере используется секционирование для создания уникального выходного пути при выполнении каждого из срезов. Без этого действие перезапишет файл.

		{
		  "name": "DecisionTreeResultBlob",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "azuremltesting/scored/{folderpart}/",
		      "fileName": "{filepart}result.csv",
		      "partitionedBy": [
		        {
		          "name": "folderpart",
		          "value": {
		            "type": "DateTime",
		            "date": "SliceStart",
		            "format": "yyyyMMdd"
		          }
		        },
		        {
		          "name": "filepart",
		          "value": {
		            "type": "DateTime",
		            "date": "SliceStart",
		            "format": "HHmmss"
		          }
		        }
		      ],
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "availability": {
		      "frequency": "Day",
		      "interval": 15
		    }
		  }
		}

4. Создайте **связанную службу** типа **AzureMLLinkedService**, предоставляющую ключ API и URL-адрес пакетного выполнения модели.
		
		{
		  "name": "MyAzureMLLinkedService",
		  "properties": {
		    "type": "AzureML",
		    "typeProperties": {
		      "mlEndpoint": "https://[batch execution endpoint]/jobs",
		      "apiKey": "[apikey]"
		    }
		  }
		}
5. Наконец, создайте конвейер, содержащий действие **AzureMLBatchExecution**. Он получит расположение входного файла из входных наборов данных, вызовет API пакетного выполнения Машинного обучения Azure и скопирует выходные данные пакетного выполнения в заданный большой двоичный объект в выходном наборе данных. 

	> [AZURE.NOTE]У действия AzureMLBatchExecution может быть ноль или более входных наборов данных и один или несколько выходных наборов данных.

		{
		  "name": "PredictivePipeline",
		  "properties": {
		    "description": "use AzureML model",
		    "activities": [
		      {
		        "name": "MLActivity",
		        "type": "AzureMLBatchExecution",
		        "description": "prediction analysis on batch input",
		        "inputs": [
		          {
		            "name": "DecisionTreeInputBlob"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "DecisionTreeResultBlob"
		          }
		        ],
		        "linkedServiceName": "MyAzureMLLinkedService",
                "typeProperties":
                {
                    "webServiceInput": "DecisionTreeInputBlob ",
                    "webServiceOutputs": {
                        "output1": "DecisionTreeResultBlob "
                    }                
                },
		        "policy": {
		          "concurrency": 3,
		          "executionPriorityOrder": "NewestFirst",
		          "retry": 1,
		          "timeout": "02:00:00"
		        }
		      }
		    ],
		    "start": "2015-02-13T00:00:00Z",
		    "end": "2015-02-14T00:00:00Z"
		  }
		}

	Значения даты и времени как начала (**start**), так и окончания (**end**) должны быть указаны в [формате ISO](http://en.wikipedia.org/wiki/ISO_8601). Например, 2014-10-14T16:32:41Z. Значение времени окончания (**end**) указывать необязательно. Если не указать значение свойства **end**, оно вычисляется по формуле **время начала + 48 часов**. Чтобы запустить конвейер в течение неопределенного срока, укажите значение **9999-09-09** в качестве значения свойства **окончание**. Подробную информацию о свойствах JSON см. в [Справочнике по сценариям JSON](https://msdn.microsoft.com/library/dn835050.aspx).

	> [AZURE.NOTE]Указывать входные данные для действия AzureMLBatchExecution необязательно.

## Сценарий: эксперименты со использованием модулей чтения и записи для обращения к данным в различных хранилищах

При создании экспериментов с Машинным обучением Azure еще одним распространенным сценарием является использование модулей чтения и записи. Модуль чтения используется для загрузки данных в эксперимент, а модуль записи — для сохранения данных экспериментов. Дополнительную информацию о модулях чтения и записи см. в разделах [Модуль чтения](https://msdn.microsoft.com/library/azure/dn905997.aspx) и [Модуль записи](https://msdn.microsoft.com/library/azure/dn905984.aspx) в библиотеке MSDN.

При использовании модулей чтения и записи рекомендуется указывать параметр веб-службы для каждого их свойства. Эти параметры веб-службы позволяют настроить значения во время выполнения. Например, можно создать эксперимент с модулем чтения, который использует базу данных SQL Azure: XXX.database.windows.net. После развертывания веб-службы вам необходимо включить объекты-получатели веб-службы, чтобы указать другой сервер Azure SQL, YYY.database.windows.net. Вы можете использовать параметр веб-службы, чтобы разрешить настройку этого значения.

> [AZURE.NOTE]Входные и выходные данные веб-службы отличаются от параметров веб-службы. В первом сценарии вы узнали, как можно указать ввод и вывод для веб-службы Машинного обучения Azure. В этом сценарии вы передадите параметры для веб-службы, соответствующие свойствам модулей чтения и записи.

Рассмотрим сценарий использования параметров веб-службы. Вы развернули веб-службу Машинного обучения Azure, которая использует модуль чтения для чтения данных из одного из поддерживаемых источников данных Машинного обучения Azure (например, базы данных SQL Azure). После пакетного выполнения результаты записываются с помощью модуля записи (база данных SQL Azure). В экспериментах не определены входные и выходные данные веб-службы. В этом случае рекомендуется настроить соответствующие параметры веб-службы для модулей чтения и записи. Это позволит настроить их при использовании действия AzureMLBatchExecution. Параметры веб-службы указываются в разделе **globalParameters** действия JSON, как показано ниже.


	"typeProperties": {
		"globalParameters": {
			"Param 1": "Value 1",
			"Param 2": "Value 2"
		}
	}

Вы также можете использовать [функции фабрики данных](https://msdn.microsoft.com/library/dn835056.aspx) при передаче значений для параметров веб-службы, как показано в следующем примере:

	"typeProperties": {
    	"globalParameters": {
    	   "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    	}
  	}
 
> [AZURE.NOTE]В параметрах веб-службы учитывается регистр, поэтому убедитесь, что имена, которые указываются в действии JSON, соответствуют именам, предоставляемым веб-службой.

### Использование модуля чтения для чтения данных из нескольких файлов в большом двоичном объекте Azure
Конвейеры больших данных (Pig, Hive, т. д.) могут формировать один или несколько выходных файлов без расширений. Например, если указать внешнюю таблицу Hive, данные для нее могут храниться в хранилище больших двоичных объектов с следующим именем: 000000\_0. В эксперименте можно применять модуль чтения для чтения нескольких файлов и использовать их для прогнозирования.

При использовании модуля чтения в эксперименте Машинного обучения Azure вы можете указать в качестве входных данных большой двоичный объект Azure. Файлы в хранилище больших двоичных объектов Azure могут представлять собой выходные файлы (например, 000000\_0), созданные с помощью сценария Pig и Hive, который выполняется в HDInsight. Модуль чтения позволяет читать файлы (без расширения), настроив свойство **Путь к контейнеру, каталогу или большому двоичному объекту** модуля чтения таким образом, чтобы оно указывало контейнер или папку, содержащую файлы (см. ниже). Обратите внимание, что звездочка (*) **указывает на то, что все файлы в контейнере или папке (например, data/aggregateddata/year=2014/month-6/*)** будут считываться как часть эксперимента.

![Свойства большого двоичного объекта Azure](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)


### Пример 
#### Конвейер с действием AzureMLBatchExecution с параметрами веб-службы

	{
	  "name": "MLWithSqlReaderSqlWriter",
	  "properties": {
	    "description": "Azure ML model with sql azure reader/writer",
	    "activities": [
	      {
	        "name": "MLSqlReaderSqlWriterActivity",
	        "type": "AzureMLBatchExecution",
	        "description": "test",
	        "inputs": [
	          {
	            "name": "MLSqlInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "MLSqlOutput"
	          }
	        ],
	        "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
            "typeProperties":
            {
                "webServiceInput": "MLSqlInput",
                "webServiceOutputs": {
                    "output1": "MLSqlOutput"
                }
	          	"globalParameters": {
	            	"Database server name": "<myserver>.database.windows.net",
		            "Database name": "<database>",
		            "Server user account name": "<user name>",
		            "Server user account password": "<password>"
	          	}              
            },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 1,
	          "timeout": "02:00:00"
	        },
	      }
	    ],
	    "start": "2015-02-13T00:00:00Z",
	    "end": "2015-02-14T00:00:00Z"
	  }
	}
 
В приведенном выше примере JSON:

- Развернутая веб-служба Машинного обучения Azureиспользует модуль чтения и модуль записи для чтения данных из базы данных SQL Azure и записи их в нее. Эта веб-служба предоставляет следующие параметры: Database server name, Database name, Server user account name и Server user account password.  
- Значения даты и времени как начала (**start**), так и окончания (**end**) должны быть указаны в [формате ISO](http://en.wikipedia.org/wiki/ISO_8601). Например, 2014-10-14T16:32:41Z. Значение времени окончания (**end**) указывать необязательно. Если не указать значение свойства **end**, оно вычисляется по формуле **время начала + 48 часов**. Чтобы запустить конвейер в течение неопределенного срока, укажите значение **9999-09-09** в качестве значения свойства **окончание**. Подробную информацию о свойствах JSON см. в [Справочнике по сценариям JSON](https://msdn.microsoft.com/library/dn835050.aspx).
 

## Часто задаваемые вопросы

**Вопрос.** Я использую действие AzureMLBatchScoring. Следует ли перейти к использованию AzureMLBatchExecution?

**Ответ.** Да. Если вы используете действие AzureMLBatchScoring для интеграции с Машинным обучением Azure, рекомендуется использовать последнее действие AzureMLBatchExecution. Действие AzureMLBatchScoring устарело и будет удалено в будущем выпуске.

Действие AzureMLBatchExecution введено в 2015 году в августовском выпуске пакета SDK для Azure и Azure PowerShell.

Для действия AzureMLBatchExecution не требуются входные данные (если не требуются входные зависимости). Оно также позволяет явно указать, должны ли использоваться входные и выходные данные веб-службы. Если вы решили использовать ввод-вывод веб-службы, это дает возможность указать соответствующие наборы данных больших двоичных объектов Azure для использования. Кроме того, вы можете явно указать значения параметров, предоставляемых веб-службой.

Если вы хотите продолжить использование действия AzureMLBatchScoring, дополнительную информацию см. в статье [Действие оценки пакета машинного обучения Azure](data-factory-create-predictive-pipelines.md).


**Вопрос.** Имеется несколько файлов, сформированных моими конвейерами больших данных. Можно использовать действие AzureMLBatchExecution для работы с этими файлами?

**Ответ.** Да. Дополнительную информацию см. в разделе **Использование модуля чтения для чтения данных из нескольких файлов в большом двоичном объекте Azure**.


## См. также

- [Запись блога Azure: «Приступая к работе с фабрикой данных Azure и Машинным обучением Azure»](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)





[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/


 

<!---HONumber=Oct15_HO3-->