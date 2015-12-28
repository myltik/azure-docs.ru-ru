<properties
	pageTitle="Перенос данных из локального SQL Server в SQL Azure с фабрикой данных Azure | Azure"
	description="Настройка конвейера ADF, который объединяет два действия миграции данных, которые ежедневно перемещают данные между локальными базами данных и в облаке."
	services="machine-learning"
	documentationCenter=""
	authors="fashah"
	manager="jacob.spoelstra"
	editor=""
	videoId=""
	scriptId="" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/06/2015"
	ms.author="fashah;bradsev" />


# Перенос данных из локального SQL Server в SQL Azure с фабрикой данных Azure

В этом разделе показано, как перенести данные из базы данных локального SQL Server в базу данных SQL Azure через хранилище больших двоичных объектов Azure с помощью фабрики данных Azure (ADF).

## <a name="intro"></a>Введение. Что такое ADF и когда ее использовать при переносе данных?

Фабрика данных Azure представляет собой полностью управляемую облачную службу интеграции информации, которая организует и автоматизирует перемещение и преобразование данных. Ключевым принципом модели ADF является конвейер. Конвейеры — это логические группы действий, каждая из которых определяет действия, выполняемые над данными, содержащимися в наборах данных. Информация, необходимая фабрике данных для подключения к внешним ресурсам, определяется связанными службами.

С ADF существующие службы обработки данных могут быть включены в конвейеры данных, обладающие высокой доступностью и управляемые в облаке. Для этих конвейеров данных можно запланировать прием, подготовку, преобразование, анализ и публикацию данных, а всеми сложными данными и обработкой зависимостей будет управлять ADF. Решения можно быстро построить и развернуть в облаке, подключив большое количество локальных и облачных источников данных.

ADF стоит использовать в том случае, когда данные нужно постоянно переносить в гибридном сценарии, при котором осуществляется доступ как к локальным, так и к облачным ресурсам, и когда в процессе переноса данные необходимо обработать, изменить или добавить к ним операции бизнес-логики. ADF позволяет выполнять планирование и отслеживание заданий с помощью простых сценариев JSON, управляющих перемещением данных на периодической основе. ADF также обладает другими возможностями, такими как поддержка сложных операций. Дополнительные сведения об ADF см. в [документации по фабрике данных Azure](http://azure.microsoft.com/services/data-factory/).

## <a name="scenario"></a>Сценарий

Мы настроим конвейер ADF, который объединяет два действия миграции данных, которые ежедневно перемещают данные между локальной базой SQL и базой данных SQL Azure в облаке. Эти два действия таковы:

* копирование данных из локальной базы данных SQL Server в учетную запись хранилища больших двоичных объектов Azure;
* копирование данных из учетной записи хранилища больших двоичных объектов Azure в базу данных SQL Azure.

**Для справки**: описанные здесь действия были адаптированы из более подробного руководства [Конвейеры для работы с локальными данными](data-factory-use-onpremises-datasources.md), предоставленного разработчиками ADF, и в этой статье по мере необходимости приводятся ссылки на соответствующие разделы этого руководства.


## <a name="prereqs"></a>Предварительные требования
Для выполнения действий, описанных в этом учебнике, вам необходимо следующее.

* **Подписка Azure**. Если у вас нет подписки, вы можете зарегистрироваться для получения [бесплатной пробной версии](https://azure.microsoft.com/pricing/free-trial/).
* **Учетная запись хранения Azure**. Учетная запись хранения Azure будет использоваться для хранения данных в этом учебнике. Если у вас ее нет, прочитайте статью [Создание учетной записи хранения](storage-create-storage-account.md#create-a-storage-account). После создания учетной записи хранения необходимо получить ключ, используемый для доступа к хранилищу. См. статью [Просмотр, копирование и повторное создание ключей доступа к хранилищу](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
* Доступ к **базе данных SQL Azure**. Если необходимо установить базу данных SQL Azure, обратитесь к статье [Приступая к работе с базой данных SQL Microsoft Azure](sql-database-get-started.md), которая содержит сведения о том, как подготовить новый экземпляр базы данных SQL Azure.
* Установленная и настроенная локально среда **Azure PowerShell**. Инструкции см. в статье [Установка и настройка Azure PowerShell](powershell-install-configure.md).

> [AZURE.NOTE]В этой процедуре используется [портал Azure](https://ms.portal.azure.com/).

##<a name="upload-data"></a>Загрузка данных в локальный SQL Server

Для демонстрации процесса переноса данных мы используем [набор данных о такси Нью-Йорка](http://chriswhong.com/open-data/foil_nyc_taxi/). Набор данных о такси Нью-Йорка доступен, как отмечено в этой статье, в хранилище больших двоичных объектов Azure [здесь](http://www.andresmh.com/nyctaxitrips/). Данные содержатся в двух файлах: trip\_data.csv, содержащем сведения о поездке, и trip\_far.csv, содержащем сведения о тарифе для каждой поездке. Пример и описание этих файлов приведены в [Описании набора данных «Поездки такси Нью-Йорка»](machine-learning-data-science-process-sql-walkthrough.md#dataset).


Вы можете либо адаптировать описанные здесь процедуры к собственному набору данных, либо выполнить описанные действия с набором данных о такси Нью-Йорка. Для загрузки набора данных о такси Нью-Йорка в базу данных локального SQL Server выполните процедуру, описанную в разделе [Массовый импорт данных в базу данных SQL Server](machine-learning-data-science-process-sql-walkthrough.md#dbload). Эти инструкции предназначены для SQL Server на виртуальной машине Azure, но для локального SQL Server процедура идентична.

##<a name="create-adf"></a> Создание фабрики данных Azure

Инструкции по созданию новой фабрики данных Azure и группы ресурсов на [портале Azure](https://ms.portal.azure.com/) представлены [здесь](data-factory-build-your-first-pipeline-using-editor.md#step-1-creating-the-data-factory). Задайте имя *adfdsp* для нового экземпляра ADF и имя *adfdsprg* для созданной группы ресурсов.

## Установка и настройка шлюза управления данными

Чтобы конвейеры фабрики данных Azure могли работать с локальным SQL Server, необходимо добавить его в качестве связанной службы фабрики данных. Чтобы добавить связанную службу к локальному SQL Server, нужно скачать и установить шлюз управления данными Microsoft на локальный компьютер и настроить использование шлюза в связанной службе для локального источника данных. Шлюз управления данными сериализует и десериализует данные источника и приемника на компьютере, на котором он размещен.

Инструкции по настройке и сведения о шлюзе управления данными см. в разделе [Использование конвейеров для работы с локальными данными](data-factory-use-onpremises-datasources.md).


## <a name="adflinkedservices"></a>Создание связанных служб для подключения к ресурсам данных

Информация, необходимая фабрике данных для подключения к внешним ресурсам, определяется связанными службами. Пошаговая инструкция по созданию связанных служб приведена в статье [Создание связанных служб](data-factory-use-onpremises-datasources.md#step-2-create-linked-services).

В данном сценарии есть три ресурса, для которых требуются связанные службы.

1. [Связанная служба для локального SQL Server](#adf-linked-service-onprem-sql)
2. [Связанная служба для хранилища больших двоичных объектов Azure](#adf-linked-service-blob-store)
3. [Связанная служба для базы данных SQL Azure](#adf-linked-service-azure-sql)


###<a name="adf-linked-service-onprem-sql"></a>Связанная служба для базы данных локального SQL Server
Для создания связанной службы для локального экземпляра SQL Server щелкните **Хранилище данных** на стартовой странице ADF классического портала Azure, выберите *SQL* и введите *имя пользователя* и *пароль* для локального экземпляра SQL Server. Имя сервера необходимо указать в следующем формате: **полное имя сервера, обратная косая черта, имя экземпляра (имя\_сервера\\имя\_экземпляра)**. Укажите имя *adfonpremsql* для связанной службы.

###<a name="adf-linked-service-blob-store"></a>Связанная служба для больших двоичных объектов
Для создания связанной службы для хранилища BLOB-объектов Azure нажмите **Хранилище данных** на целевой странице ADF классического портала Azure, выберите *учетную запись хранения Azure* и введите ключ учетной записи хранилища BLOB-объектов Azure и имя контейнера больших двоичных объектов. Укажите имя *adfds* для связанной службы.

###<a name="adf-linked-service-azure-sql"></a>Связанная служба для базы данных SQL Azure
Для создания связанной службы для базы данных SQL Azure щелкните **Хранилище данных** на целевой странице ADF классического портала Azure, выберите *Azure SQL* и введите *имя пользователя* и *пароль* для локального экземпляра SQL Server. *Имя пользователя* необходимо указать как **user@servername*.


##<a name="adf-tables"></a>Определение и создание таблиц для определения доступа к наборам данных

Для создания таблиц, которые определяют структуру, расположение и доступность наборов данных, используются следующие процедуры на основе сценариев. Для определения таблиц используются файлы JSON. Дополнительные сведения о структуре этих файлов см. в разделе [Наборы данных](data-factory-create-datasets.md).

> [AZURE.NOTE]Перед выполнением командлета [New-AzureDataFactoryTable](https://msdn.microsoft.com/library/azure/dn835096.aspx) необходимо проверить, правильно ли выбрана подписка для его выполнения, выполнив командлет `Add-AzureAccount`. Документацию по этим командлетам см. в разделе [Add-AzureAccount](https://msdn.microsoft.com/library/azure/dn790372.aspx).

В определениях на основе JSON в таблицах используются следующие имена:

* **имя таблицы** в локальном SQL server — *nyctaxi\_data*;
* **имя контейнера** в учетной записи хранилища больших двоичных объектов Azure — *containername*.  

Для этого конвейера ADF необходимо определить три таблицы:

1. [локальная таблица SQL](#adf-table-onprem-sql);
2. [таблица больших двоичных объектов](#adf-table-blob-store);
3. [таблица SQL Azure](#adf-table-azure-sql).

> [AZURE.NOTE]В следующих процедурах для определения и создания действий ADF используется Azure PowerShell. Однако эти задачи также можно выполнить на портале Azure. Дополнительные сведения см. в статье [Создание входных и выходных наборов данных](data-factory-use-onpremises-datasources.md#step-3-create-input-and-output-datasets).

###<a name="adf-table-onprem-sql"></a>Локальная таблица SQL

Определение таблицы для локального SQL Server производится в следующем файле JSON.

    	{
	    	"name": "OnPremSQLTable",
	    	"properties":
	    	{
		    	"location":
		    	{
		    	"type": "OnPremisesSqlServerTableLocation",
		    	"tableName": "nyctaxi_data",
		    	"linkedServiceName": "adfonpremsql"
		    	},
		    	"availability":
		    	{
		    	"frequency": "Day",
		    	"interval": 1,   
		    	"waitOnExternal":
		    	{
		    	"retryInterval": "00:01:00",
		    	"retryTimeout": "00:10:00",
		    	"maximumRetry": 3
		    	}

		    	}
	    	}
    	}
Обратите внимание, что сюда не были включены имена столбцов. Для выбора имен столбцов с помощью подзапроса включите их сюда (дополнительные сведения приведены в [документации по ADF](data-factory-copy-activity.md)).

Скопируйте определение таблицы JSON в файл с именем *onpremtabledef.json* и сохраните его в известном месте (например, *C:\\temp\\onpremtabledef.json*). Создайте таблицу в ADF с помощью следующего командлета Azure PowerShell.

	New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


###<a name="adf-table-blob-store"></a>Таблица больших двоичных объектов
Определение таблицы выходных больших двоичных объектов задается следующим образом (здесь данные, полученные из локального расположения, сопоставляются с большим двоичным объектом Azure).

	    {
		    "name": "OutputBlobTable",
		    "properties":
		    {
			    "location":
			    {
			    "type": "AzureBlobLocation",
			    "folderPath": "containername",
			    "format":
			    {
			    "type": "TextFormat",
			    "columnDelimiter": "\t"
			    },
			    "linkedServiceName": "adfds"
			    },
			    "availability":
			    {
			    "frequency": "Day",
			    "interval": 1
			    }
		    }
	    }

Скопируйте определение таблицы JSON в файл с именем *bloboutputtabledef.json* и сохраните его в известном месте (например, *C:\\temp\\bloboutputtabledef.json*). Создайте таблицу в ADF с помощью следующего командлета Azure PowerShell.

	New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json  

###<a name="adf-table-azure-sq"></a>Таблица SQL Azure
Определение таблицы для выходных данных SQL Azure задается следующим образом (эта схема сопоставляет данные, полученные из большого двоичного объекта).

	{
	    "name": "OutputSQLAzureTable",
	    "properties":
	    {
	        "structure":
	        [
				{ "name": "column1", type": "String"},
				{ "name": "column2", type": "String"}                
	        ],
	        "location":
	        {
	            "type": "AzureSqlTableLocation",
	            "tableName": "your_db_name",
	            "linkedServiceName": "adfdssqlazure_linked_servicename"
	        },
	        "availability":
	        {
	            "frequency": "Day",
	            "interval": 1            
	        }
	    }
	}

Скопируйте определение таблицы JSON в файл с именем *AzureSqlTable.json* и сохраните его в известном месте (например, *C:\\temp\\AzureSqlTable.json*). Создайте таблицу в ADF с помощью следующего командлета Azure PowerShell.

	New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json  

##<a name="adf-pipeline"></a>Определение и создание конвейера

Укажите действия, которые принадлежат конвейеру, и создайте конвейер с помощью следующих процедур на основе сценариев. Для определения свойств конвейера используется файл JSON.

* Сценарий предполагает, что**имя конвейера** — *AMLDSProcessPipeline*.
* Также обратите внимание, что мы задаем периодичность выполнения конвейера ежедневно и используем время выполнения задания по умолчанию (00:00 по Гринвичу).

> [AZURE.NOTE]В следующих процедурах для определения и создания конвейера ADF используется Azure PowerShell. Однако эту задачу также можно выполнить на портале Azure. Дополнительные сведения см. в разделе [Создание и запуск конвейера](data-factory-use-onpremises-datasources.md#step-4-create-and-run-a-pipeline).

Используя приведенные выше определения таблиц, определение конвейера ADF указывается следующим образом.

		{
		    "name": "AMLDSProcessPipeline",
		    "properties":
		    {
		        "description" : "This pipeline has one Copy activity that copies data from an on-premise SQL to Azure blob",
		         "activities":
		        [
		            {
		                "name": "CopyFromSQLtoBlob",
		                "description": "Copy data from on-premise SQL server to blob",     
		                "type": "CopyActivity",
		                "inputs": [ {"name": "OnPremSQLTable"} ],
		                "outputs": [ {"name": "OutputBlobTable"} ],
		                "transformation":
		                {
		                    "source":
		                    {                               
		                        "type": "SqlSource",
		                        "sqlReaderQuery": "select * from nyctaxi_data"
		                    },
		                    "sink":
		                    {
		                        "type": "BlobSink"
		                    }   
		                },
		                "Policy":
		                {
		                    "concurrency": 3,
		                    "executionPriorityOrder": "NewestFirst",
		                    "style": "StartOfInterval",
		                    "retry": 0,
		                    "timeout": "01:00:00"
		                }       

		             },

					{
						"name": "CopyFromBlobtoSQLAzure",
						"description": "Push data to Sql Azure",		
						"type": "CopyActivity",
						"inputs": [ {"name": "OutputBlobTable"} ],
						"outputs": [ {"name": "OutputSQLAzureTable"} ],
						"transformation":
						{
							"source":
							{                               
								"type": "BlobSource"
							},
							"sink":
							{
								"type": "SqlSink",
								"WriteBatchTimeout": "00:5:00",				
							}			
						},
						"Policy":
						{
							"concurrency": 3,
							"executionPriorityOrder": "NewestFirst",
							"style": "StartOfInterval",
							"retry": 2,
							"timeout": "02:00:00"
						}
				     }
		        ]
		    }
		}

Скопируйте определение таблицы JSON в файл с именем *pipelinedef.json* и сохраните его в известном месте (например, *C:\\temp\\pipelinedef.json*). Создайте конвейер в ADF с помощью следующего командлета Azure PowerShell.

	New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json

Убедитесь, что конвейер в ADF отображается на классическом портале Azure следующим образом (при щелчке схемы).

![](media/machine-learning-data-science-move-sql-azure-adf/DJP1kji.png)

##<a name="adf-pipeline-start"></a>Запуск конвейера
Конвейер можно запустить с помощью следующей команды:

	Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

Значения параметров *startdate* и *enddate* необходимо заменить фактическими датами, определяющими временной интервал запуска конвейера.

После запуска конвейера вы сможете увидеть, как в контейнере большого двоичного объекта начнут появляться данные, по одному файлу в день.

Обратите внимание, что мы не используем возможность поэтапного поступления данных в конвейер, предоставляемую ADF. Дополнительные сведения об этой и других возможностях ADF см. в [документации ADF](http://azure.microsoft.com/services/data-factory/).

<!---HONumber=AcomDC_1217_2015-->