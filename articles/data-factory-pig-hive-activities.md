<properties 
	pageTitle="Использование Pig и Hive с фабрикой данных Azure" 
	description="Узнайте, как обрабатывать данные, выполняя сценарии Pig и Hive в кластере Azure HDInsight из фабрики данных Azure." 
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
	ms.date="2/10/2015" 
	ms.author="spelluru"/>

# Использование Pig и Hive с фабрикой данных
Конвейер фабрики данных Azure обрабатывает данные в связанных службах хранения с помощью связанных вычислительных служб. В нем содержится последовательность действий, каждое из которых выполняет определенную операцию обработки. 

- **Действие копирования** копирует данные из исходного хранилища в место назначения. Дополнительные сведения о действии копирования см. в разделе [Копирование данных с помощью фабрики данных][data-factory-copy-activity]. 
- **Действие HDInsight** обрабатывает данные, запуская сценарии Hive и Pig или программы MapReduce в кластере HDInsight. Действие HDInsight поддерживает три типа преобразований: **Hive**, **Pig** и **MapReduce**. Действие HDInsight может использовать одно или несколько входных данных и создавать одно или несколько выходных.
 
Подробные сведения о запуске программ MapReduce в кластере HDInsight из конвейера фабрики данных Azure с помощью преобразования MapReduce действия HDInsight см. в разделе [Вызов программ MapReduce из фабрики данных][data-factory-map-reduce]. В этой статье описывается использование преобразований Pig и Hive действия HDInsight.

## Содержание

Раздел | Описание
------- | -----------
[Пример JSON Pig](#PigJSON) | В этом разделе содержится схема JSON по определению действия HDInsight, которое использует преобразование Pig. 
[Пример JSON Hive](#HiveJSON) | В этом разделе содержится схема JSON по определению действия HDInsight, которое использует преобразование Hive. 
[Использование сценариев Pig и Hive, которые хранятся в хранилище BLOB-объектов Azure](#ScriptInBlob) | В этом разделе описывается, как ссылаться на сценарии Pig и Hive, которые хранятся в хранилище BLOB-объектов Azure, из действия HDInsight с помощью преобразований Pig и Hive.
[Параметризованные запросы Pig и Hive](#ParameterizeQueries) | В этом разделе описывается, как задавать значения для параметров, используемых в сценариях Pig и Hive с помощью свойства JSON **extendedProperties**.
[Пошаговое руководство: Использование Hive с фабрикой данных Azure](#Waltkthrough) | Здесь содержатся пошаговые инструкции по созданию конвейера, который использует сценарий Hive при обработке данных.  



При определении действия Pig или Hive в конвейере JSON свойству **type** необходимо задать значение: **HDInsightActivity**.

## <a name="PigJSON"></a> Пример JSON Pig

    {
		"name": "Pig Activity",
		"description": "description", 
		"type": "HDInsightActivity",
		"inputs":  [ { "name": "InputSqlDA"  } ],
		"outputs":  [ { "name": "OutputBlobDA" } ],
		"linkedServiceName": "MyHDInsightLinkedService",
		"transformation":
		{
			"type": "Pig",
			"script": "pig script",
			"extendedProperties":
			{	
				"param1": "param1Value"
 			}
		}
	}

**Обратите внимание на следующее:**
	
- Параметру **type** действия задано значение **HDInsightActivity**.
- Свойству **linkedServiceName** задано значение **MyHDInsightLinkedService**. 
- Параметру **type** **преобразования** задано значение **Pig**.
- Вы можете задать встраиваемый сценарий Pig для свойства **script** или сохранить файлы сценария в хранилище BLOB-объектов Azure и связать их с файлом с помощью свойства **scriptPath**, о котором мы расскажем далее в этой статье. 
- Указывать параметры сценария Pig можно с помощью свойства **extendedProperties**. Дополнительная информация приведена далее в этой статье. 


## <a name="HiveJSON"></a> ## Пример JSON Hive


    {
		"name": "Hive Activity",
		"description": "description", 
		"type": "HDInsightActivity",
		"inputs":  [ { "name": "InputSqlDA"  } ],
		"outputs":  [ { "name": "OutputBlobDA" } ],
		"linkedServiceName": "MyHDInsightLinkedService",
		"transformation":
		{
			"type": "Hive",
			"script": "Hive script",
			"extendedProperties":
			{	
				"param1": "param1Value"
            }
		}
	}

**Обратите внимание на следующее:**
	
- Параметру **type** действия задано значение **HDInsightActivity**.
- Свойству **linkedServiceName** задано значение **MyHDInsightLinkedService**. 
- Параметру **type** **преобразования** задано значение **Hive**.
- Вы можете задать встраиваемый сценарий Hive для свойства **script** или сохранить файлы сценария в хранилище BLOB-объектов Azure и связать их с файлом с помощью свойства **scriptPath**, о котором мы расскажем далее в этой статье. 
- Указывать параметры сценария Hive можно с помощью свойства **extendedProperties**. Дополнительная информация приведена далее в этой статье. 

> [WACOM.NOTE] Подробную информацию о командлетах, схемах JSON и свойствах в схемах см. в [справочнике разработчика](http://go.microsoft.com/fwlink/?LinkId=516908). 


## <a name="ScriptInBlob"></a>Использование сценариев Pig и Hive, которые хранятся в хранилище больших двоичных объектов Azure
Вы можете сохранять сценарии Pig/Hive в хранилище больших двоичных объектов Azure, связанном с кластером HDInsight, и ссылаться на них из действий Pig/Hive, используя следующие свойства в JSON: 

* **scriptPath** - путь к файлу сценария Pig или Hive
* **scriptLinkedService** - учетная запись хранения Azure, в которой содержится файл сценария

В следующем примере JSON для образца конвейера используется действие Hive. Оно ссылается на файл **transformdata.hql** из папки **scripts** в контейнере **adfwalkthrough** хранилища BLOB-объектов Azure, которое предоставляется службой **StorageLinkedService**.

    {
    	"name": "AnalyzeMarketingCampaignPipeline",
    	"properties":
    	{
	        "description" : " Enriched Gamer Fact Data and push to SQL Azure",
    	    "activities":
    	    [
    	        {
					"name": "JoinData",
					"description": "Join Regional Campaign data with Enriched Gamer Fact Data",
					"type": "HDInsightActivity",
					"inputs": [ {"name": "EnrichedGameEventsTable"}, 
                            {"name": "RefMarketingCampaignTable"} ],
					"outputs": [ {"name": "MarketingCampaignEffectivenessBlobTable"} ],
					"linkedServiceName": "MyHDInsightLinkedService",
					"transformation":
					{
    					"type": "Hive",
    					"scriptpath": "adfwalkthrough\\scripts\\transformdata.hql",    		
						"scriptLinkedService": "StorageLinkedService", 
						"extendedProperties":
						{
						}		
					},
					"policy":
					{
						"concurrency": 1,
						"executionPriorityOrder": "NewestFirst",
						"retry": 1,
						"timeout": "01:00:00"
					}
            	}
        	]
      	}
	}

  

> [WACOM.NOTE] Подробную информацию о командлетах, схемах JSON и свойствах в схемах см. в [справочнике разработчика](http://go.microsoft.com/fwlink/?LinkId=516908).

## <a name="ParameterizeQueries"></a>Параметризованные запросы Pig и Hive
Действия Pig и Hive фабрики данных позволяют задавать значения для параметров, используемых в сценариях Pig и Hive, с помощью **extendedProperties**. Раздел extendedProperties состоит из имени и значения параметра.

В следующем примере показано, как указывать параметры для сценария Hive с помощью **extendedProperties**. Чтобы использовать параметризованные сценарии Hive, выполните следующие действия.

1.	Определите параметры в свойстве **extendedProperties**.
2.	Во встроенном сценарии Hive (или) файле сценария Hive, сохраненном в хранилище больших двоичных объектов, создайте ссылку на параметр с помощью **${hiveconf:parameterName}**.

   
    		
    	{
			"name": "ParameterizedHivePipeline",
			"properties": 
			{
	    		"description" : "Example - Parameterized Hive Pipeline",
		   	 "activities": 
				[
					{
						"name": "ProcessLog",
					  	"type": "HDInsightActivity",
					  	"inputs": [{"Name": "DA_Input"}],
						"outputs": [{"Name": "DA_Output1"}, {"Name": "DA_Output2"}],
				  		"linkedServiceName": "MyHDInsightLinkedService",
				  		"transformation":
				  		{
							"type": "Hive", 
							"extendedProperties":
							{
								"Param1": "$$Text.Format('{0:yyyy-MM-dd}', SliceStart)",
								"Param2": "value"
						  	},
    						"script": "ADD FILE ${hiveconf:Param1}://${hiveconf:Param2}/MyFile.DLL;"
    					}
					}
			   	]
			}
		}


-  

## <a name="Walkthrough"></a>Пошаговое руководство. Использование Hive с фабрикой данных Azure
### Предварительные требования
1. Выполните инструкции, описанные в статье [Приступая к работе с фабрикой данных Azure][adfgetstarted].
2. Передайте файл **emp.txt**, созданный в вышеупомянутой статье как **hiveinput\emp.txt**, в контейнер adftutorial в хранилище BLOB-объектов. Папка **hiveinput** автоматически создается в контейнере **adftutorial**, когда вы передаете файл emp.txt с этим синтаксисом. 
2. Создайте файл **hivequery.hql** в подпапке **Hive** в каталоге **C:\ADFGetStarted** со следующим содержимым.
    		
    	DROP TABLE IF EXISTS adftutorialhivetable; 
		CREATE EXTERNAL TABLE  adftutorialhivetable
		(                                  
 			country         string,                                   
 			state           string,   
 			sessioncount int                                 
		) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RESULTOUTPUT}/${hiveconf:Year}/${hiveconf:Month}/${hiveconf:Day}'; 

		INSERT OVERWRITE TABLE adftutorialhivetable 
		SELECT  country, state, count(*) 
		FROM hivesampletable 
		group by country, state;
		
3.  Передайте файл **hivequery.hql** в контейнер **adftutorial** в хранилище BLOB-объектов


### Пошаговое руководство

#### Создание входной таблицы
1. Создайте JSON-файл с именем **HiveInputBlobTable.json** в папке **C:\ADFGetStarted\Hive** со следующим содержимым.
    		
		{
    		"name": "HiveInputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
            		"folderPath": "adftutorial/hiveinput",
            		"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "Day",
            		"interval": 1,
            		"waitonexternal": {}
        		}
    		}
		}

 
	**Обратите внимание на следующее:**
	
	- в качестве расположения свойства **type** задано значение **AzureBlobLocation**.
	- для свойства **linkedServiceName** задано значение **MyBlobStore**, которое означает учетную запись хранения Azure.
	- параметр **folderPath** задает папку или контейнер BLOB-объектов для входных данных. 
	- параметры **frequency=Day** и **interval=1** означают, что срезы доступны ежедневно
	- параметр **waitOnExternal** означает, что эти данные не производятся другим конвейером; они производятся извне в фабрику данных. 
	

	Описания свойств JSON см. в [справочнике разработчика фабрики данных][developer-reference].  

2. Запустите **Azure PowerShell** и при необходимости перейдите в режим **AzureResourceManager**.
    		
    	Switch-AzureMode AzureResourceManager

5. Перейдите к папке: **C:\ADFGetStarted\Hive**.
6. Выполните следующую команду, чтобы создать входную таблицу в **ADFTutorialDataFactory**.

		New-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\HiveInputBlobTable.json

	Подробный обзор командлетов фабрики данных см. в [справочнике по командлетам фабрики данных][cmdlet-reference]. 
#### Создание выходной таблицы
        
1. Создайте JSON-файл с именем **HiveOutputBlobTable.json** со следующим содержимым и сохраните его в папке **C:\ADFGetStarted\Hive**.

		{
    		"name": "HiveOutputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
	    			"folderPath": "adftutorial/hiveoutput/",
            		"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "Day",
            		"interval": 1
        		}
    		}
		}

2. Выполните следующую команду, чтобы создать выходную таблицу в **ADFTutorialDataFactory**.
 
		New-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\HiveOutputBlobTable.json

### Создание связанной службы для кластера HDInsight
Служба фабрики данных Azure поддерживает создание кластера по запросу и использует его для обработки входных данных, чтобы создать выходные данные. Вы также можете использовать для этого собственный кластер. Когда вы используете кластер HDInsight по запросу, для каждого среза создается отдельный кластер. В то же время, когда вы используете свой собственный кластер HDInsight, кластер готов к обработке среза немедленно. Поэтому при использовании кластера по запросу выходные данные могут выводится не так быстро, как при использовании собственного кластера. Давайте в качестве примера используем кластер по запросу. 

#### Использование кластера HDInsight по запросу
1. Создайте JSON-файл с именем **HDInsightOnDemandCluster.json** со следующим содержимым и сохраните его в папке **C:\ADFGetStarted\Hive**.


		{
    		"name": "HDInsightOnDemandCluster",
    		"properties": 
    		{
        		"type": "HDInsightOnDemandLinkedService",
				"clusterSize": "4",
        		"timeToLive": "00:05:00",
        		"linkedServiceName": "MyBlobStore"
    		}
		}

2. Запустите **Azure PowerShell** и выполните следующую команду, чтобы перейти в режим **AzureResourceManager**. Командлеты фабрики данных Azure доступны в режиме **AzureResourceManager**.

         switch-azuremode AzureResourceManager
		

3. Перейдите к папке **C:\ADFGetstarted\Hive**.
4. Выполните следующую команду, чтобы создать связанную службу для кластера HDInsight по запросу.
 
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\HDInsightOnDemandCluster.json
  
3. Вы увидите таблицы и связанные службы в колонке **Фабрика данных** на **портале предварительной версии Azure**.    
   
#### Использование собственного кластера HDInsight 

1. Создайте JSON-файл с именем **MyHDInsightCluster.json** со следующим содержимым и сохраните его в папку **C:\ADFGetStarted\Hive**. Прежде чем сохранить JSON-файл, замените clustername, username и password соответствующими значениями.  

		{
   			"Name": "MyHDInsightCluster",
    		"Properties": 
			{
        		"Type": "HDInsightBYOCLinkedService",
	        	"ClusterUri": "https://<clustername>.azurehdinsight.net/",
    	    	"UserName": "<username>",
    	    	"Password": "<password>",
    	    	"LinkedServiceName": "MyBlobStore"
    		}
		}

2. Запустите **Azure PowerShell** и выполните следующую команду, чтобы перейти в режим **AzureResourceManager**. Командлеты фабрики данных Azure доступны в режиме **AzureResourceManager**.

         switch-azuremode AzureResourceManager
		

3. Перейдите к папке **C:\ADFGetstarted\Hive**.
4. Выполните следующую команду, чтобы создать связанную службы для вашего собственного кластера HDInsight.
 
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\MyHDInsightCluster.json

### Создание и планирование конвейера
   
1. Создайте JSON-файл с именем **ADFTutorialHivePipeline.json** со следующим содержимым и сохраните его в папке **C:\ADFGetStarted\Hive**. Если вы хотите использовать собственный кластер и выполнили все шаги по созданию связанной службы **MyHDInsightCluster**, замените параметр **HDInsightOnDemandCluster** значением **MyHDInsightCluster** в следующем JSON-файле. 


    	{
    		"name": "ADFTutorialHivePipeline",
    		"properties":
    		{
        		"description" : "It runs a HiveQL query and stores the result set in a blob",
        		"activities":
        		[
            		{
						"name": "RunHiveQuery",
						"description": "Runs a hive query",
						"type": "HDInsightActivity",
						"inputs": [{"name": "HiveInputBlobTable"}],
						"outputs": [ {"name": "HiveOutputBlobTable"} ],
						"linkedServiceName": "HDInsightOnDemandCluster",
						"transformation":
						{
                    		"type": "Hive",
                    		"extendedProperties":
                    		{
                        		"RESULTOUTPUT": "wasb://adftutorial@spestore.blob.core.windows.net/hiveoutput/",
		                        "Year":"$$Text.Format('{0:yyyy}',SliceStart)",
		                        "Month":"$$Text.Format('{0:%M}',SliceStart)",
		                        "Day":"$$Text.Format('{0:%d}',SliceStart)"
		                    },
		                    "scriptpath": "adftutorial\\hivequery.hql",
						    "scriptLinkedService": "MyBlobStore"
						},
						"policy":
						{
							"concurrency": 1,
							"executionPriorityOrder": "NewestFirst",
							"retry": 1,
							"timeout": "01:00:00"
						}
            		}
        		]
      		}
		}

2. Чтобы создать конвейер, выполните следующую команду.
    	
		New-AzureDataFactoryPipeline -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\ADFTutorialHivePipeline.json
    	
3. Задайте расписание конвейера.
    	
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-27 -EndDateTime 2014-09-30 -Name ADFTutorialHivePipeline 

	> [WACOM.NOTE] Замените значение **StartDateTime** датой трехдневной давности, а значение **EndDateTime** - текущей датой. StartDateTime и EndDateTime являются значениями времени в формате UTC, и потому должны быть заданы в [формате ISO](http://en.wikipedia.org/wiki/ISO_8601). Например: 2014-10-14T16:32:41Z. 
	> Если не указывать значение **EndDateTime**, оно будет рассчитываться по формуле "**StartDateTime + 48 часов**". Чтобы конвейер выполнялся в течение неопределенного срока, укажите значение **9/9/9999** для параметра **EndDateTime**.
  	
	Согласно расписанию выходная таблица будет создаваться ежедневно, поэтому всего будет произведено три среза. 

4. См. раздел [Мониторинг наборов данных и конвейеров][adfgetstartedmonitoring] статьи [Приступая к работе с фабрикой данных][adfgetstarted].   

## См. также

Статья | Описание
------ | ---------------
[Введение в фабрику данных Azure][data-factory-introduction] | Данная статья содержит обзор служб фабрики данных Azure, основных ее принципов, предоставляемых значений и поддерживаемых сценариев.
[Приступая к работе с фабрикой данных Azure][adf-getstarted] | В этой статье содержится подробный учебник по созданию образца фабрики данных Azure, которая копирует данные из большого двоичного объекта Azure в базу данных SQL Azure.
[Включение конвейеров для работы с локальными данными][use-onpremises-datasources] | Эта статья содержит пошаговое руководство, которое показывает, как скопировать данные из локальной базы данных SQL Server в большой двоичный объект Azure.
[Учебник. Перемещение и обработка файлов журнала с помощью фабрики данных][adf-tutorial] | В этой статье содержится подробное пошаговое руководство, которое показывает, как реализовать практически реальный сценарий с использованием фабрики данных Azure для преобразования данных из файлов журнала в подробные данные.
[Использование пользовательских действий в фабрике данных][use-custom-activities] | В этой статье приводится подробное руководство с пошаговыми инструкциями по созданию пользовательских действий и использованию их в конвейере. 
[Устранение неполадок фабрики данных][troubleshoot] | В этой статье описывается устранение неполадок в работе фабрики данных Azure.  
[Справочник разработчика фабрики данных Azure][developer-reference] | Справочник разработчика содержит полную справку по командлетам, сценарию JSON, функциям и т. д. 

[data-factory-copy-activity]: ..//data-factory-copy-activity
[data-factory-map-reduce]: ..//data-factory-map-reduce

[adf-getstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: ../data-factory-get-started
[adfgetstartedmonitoring]:../data-factory-get-started#MonitorDataSetsAndPipeline 
[adftutorial]: ../data-factory-tutorial

[Справочник разработчика]: http://go.microsoft.com/fwlink/?LinkId=516908
[Портал Azure]: http://portal.azure.com

<!--HONumber=35.2-->

<!--HONumber=46--> 
