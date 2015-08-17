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
	ms.date="07/26/2015" 
	ms.author="spelluru"/>

# Использование Pig и Hive с фабрикой данных
Конвейер в фабрике данных Azure обрабатывает данные в связанной службе хранилища с помощью связанных вычислительных служб. В нем содержится последовательность действий, каждое из которых выполняет определенную операцию обработки. В этой статье рассматривается использование действия HDInsight с преобразованием Pig или Hive в конвейере фабрики данных Azure. Подробные сведения о запуске программ MapReduce в кластере HDInsight из конвейера фабрики данных Azure см. в статье [Вызов программы MapReduce из фабрики данных][data-factory-map-reduce].

## Пошаговое руководство. Использование Hive с фабрикой данных Azure
В этом пошаговом руководстве приведены подробные инструкции по использованию действия HDInsight с преобразованием Hive в конвейере данных фабрики.

### Предварительные требования
1. Изучите инструкции, описанные в статье [Приступая к работе с фабрикой данных Azure][adfgetstarted].
2. Создайте файл **hivequery.hql** в подпапке **Hive** в каталоге **C:\\ADFGetStarted** со следующим содержимым.
    		
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

	> [AZURE.NOTE]Чтобы использовать механизм **Tez** для выполнения запросов Hive в файле HQL, добавьте в начало файла строку **set hive.execution.engine=tez**.
		
3.  Загрузите файл **hivequery.hql** в контейнер **adftutorial** в хранилище больших двоичных объектов.


### Пошаговое руководство

#### Создание выходной таблицы
        
1. В **редакторе фабрики данных** нажмите кнопку **Создать набор данных** и щелкните **Хранилище BLOB-объектов Azure** на панели команд.
2. Замените сценарий JSON в правой области на следующий:

		{
    		"name": "HiveOutputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
	    			"folderPath": "adftutorial/hiveoutput/",
            		"linkedServiceName": "StorageLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Day",
            		"interval": 1
        		}
    		}
		}

2. Чтобы развернуть таблицу, нажмите кнопку **Развернуть** на панели команд.


### Создание связанной службы для кластера HDInsight
Служба фабрики данных Azure поддерживает создание кластера по запросу и использует его для обработки входных данных, чтобы создать выходные данные. Вы также можете использовать для этого собственный кластер. Кластер HDInsight по запросу автоматически создается и управляется службой фабрики данных Azure для обработки данных. Для получения дополнительной информации о связанной службе HDInsight по запросу см. [Связанная служба HDInsight по запросу](https://msdn.microsoft.com/library/dn893526.aspx). Давайте в качестве примера используем кластер по запросу. Обратите внимание, что создание кластера HDInsight по запросу занимает более 15 минут, и с вас будет взята плата только за время, когда кластер HDInsight находится в рабочем состоянии и выполняет задания.

#### Использование кластера HDInsight по запросу
1. Щелкните **Создать вычисление** на панели команд и выберите в меню пункт **Кластер HDInsight по запросу**.
2. В сценарии JSON выполните такие действия: 
	1. В свойстве **clusterSize** укажите размер кластера HDInsight.
	2. В свойстве **jobsContainer** укажите имя контейнера, в котором по умолчанию будут сохраняться журналы кластера. Для целей данного учебника введите **adfjobscontainer**.
	3. В свойстве **timeToLive** укажите, как долго кластер может простаивать, прежде чем он будет удален. 
	4. В свойстве **version** укажите версию HDInsight, которую хотите использовать. Если исключить это свойство, будет использоваться последняя версия.  
	5. В качестве значения свойства **linkedServiceName** укажите службу **StorageLinkedService**, которую вы создали в учебнике по началу работы. 

			{
		    	"name": "HDInsightOnDemandLinkedService",
				    "properties": {
		    	    "type": "HDInsightOnDemandLinkedService",
		    	    "clusterSize": "4",
		    	    "jobsContainer": "adfjobscontainer",
		    	    "timeToLive": "00:05:00",
		    	    "version": "3.1",
		    	    "linkedServiceName": "StorageLinkedService"
		    	}
			}

2. Чтобы развернуть эту службу, нажмите кнопку **Развернуть** на панели команд.
   
   
#### Использование собственного кластера HDInsight 

1. Щелкните **Создать вычисление** на панели команд и выберите в меню пункт **Кластер HDInsight**.
2. В сценарии JSON выполните такие действия: 
	1. В свойстве **clusterUri** укажите URL-адрес вашего кластера HDInsight, Например, https://<clustername>.azurehdinsight.net/     
	2. В свойстве **UserName** введите имя пользователя, у которого есть доступ к кластеру HDInsight.
	3. В свойстве **Password** укажите пароль этого пользователя. 
	4. В свойстве **LinkedServiceName** введите **StorageLinkedService**. Это связанная служба, которая была создана в учебнике по началу работы. 

2. Чтобы развернуть эту службу, нажмите кнопку **Развернуть** на панели команд.

### Создание и планирование конвейера
   
1. Нажмите кнопку **Создать конвейер** на панели команд. Если вы не видите эту команду, нажмите кнопку **... (многоточие)**, чтобы отобразить ее. 
2. Замените сценарий JSON в правой области на следующий: Если вы хотите использовать собственный кластер и выполнили все шаги по созданию связанной службы **HDInsightLinkedService**, замените значение **HDInsightOnDemandLinkedService** на **HDInsightLinkedService** в следующем JSON-файле. 


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
						"inputs": [],
						"outputs": [ {"name": "HiveOutputBlobTable"} ],
						"linkedServiceName": "HDInsightOnDemandLinkedService",
						"transformation":
						{
                    		"type": "Hive",
                    		"defines":
                    		{
                        		"RESULTOUTPUT": "wasb://adftutorial@<your storage account>.blob.core.windows.net/hiveoutput/",
		                        "Year":"$$Text.Format('{0:yyyy}',SliceStart)",
		                        "Month":"$$Text.Format('{0:%M}',SliceStart)",
		                        "Day":"$$Text.Format('{0:%d}',SliceStart)"
		                    },
		                    "scriptPath": "adftutorial\\hivequery.hql",
						    "scriptLinkedService": "StorageLinkedService"
						},
						"policy":
						{
							"concurrency": 1,
							"executionPriorityOrder": "NewestFirst",
							"retry": 1,
							"timeout": "01:00:00"
						}
            		}
        		],
				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z",
        		"isPaused": false

      		}
		}

	> [AZURE.NOTE]Замените значение **StartDateTime** датой трехдневной давности, а значение **EndDateTime** — текущей датой. StartDateTime и EndDateTime необходимо указывать в [формате ISO](http://ru.wikipedia.org/wiki/ISO_8601) (например, 2014-10-14T16:32:41Z). Согласно расписанию выходная таблица будет создаваться ежедневно, поэтому всего будет произведено три среза.
	> 
	> Замените **your storage account** в сценарии JSON на имя вашей учетной записи хранения.
	
	Подробную информацию о свойствах JSON см. в [Справочнике по сценариям JSON](http://go.microsoft.com/fwlink/?LinkId=516971).
2. Чтобы развернуть конвейер, нажмите кнопку **Развернуть** на панели команд.
4. См. раздел [Мониторинг наборов данных и конвейеров][adfgetstartedmonitoring] статьи [Приступая к работе с фабрикой данных][adfgetstarted]. 

	> [AZURE.NOTE]В колонке **СВЕДЕНИЯ О ВЫПОЛНЕННОМ ДЕЙСТВИИ** для среза выходной таблицы (выберите на портале выходную таблицу -> срез -> действие) вы увидите ссылки на журналы, созданные кластером HDInsight. Их можно просмотреть на самом портале или скачать на компьютер.
  

## Пример JSON Pig
При определении действия Pig или Hive в конвейере JSON необходимо задать для свойства **type** значение **HDInsightActivity**.

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
			"defines":
			{	
				"param1": "param1Value"
 			}
		}
	}

**Обратите внимание на следующее:**
	
- параметру **type** действия задано значение **HDInsightActivity**;
- свойству **linkedServiceName** задано значение **MyHDInsightLinkedService**. Подробные сведения о создании связанной службы HDInsight см. в соответствующем разделе ниже.
- Для параметра **type** **преобразования** задано значение **Pig**.
- Вы можете указать встроенный сценарий Pig, используя свойство **script**, или сохранить файлы сценария в хранилище BLOB-объектов Azure и связать их с файлом с помощью свойства **scriptPath**, о котором мы расскажем ниже. 
- Задать параметры сценария Pig можно с помощью свойства **defines**. Дополнительная информация приведена далее в этой статье. 


## Пример JSON Hive


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
			"defines":
			{	
				"param1": "param1Value"
            }
		}
	}

**Обратите внимание на следующее:**
	
- параметру **type** действия задано значение **HDInsightActivity**;
- свойству **linkedServiceName** задано значение **MyHDInsightLinkedService**. 
- Параметру **type** **преобразования** задано значение **Hive**.
- Вы можете указать встроенный сценарий Hive, используя свойство **script**, или сохранить файлы сценария в хранилище BLOB-объектов Azure и связать их с файлом с помощью свойства **scriptPath**, о котором мы расскажем далее. 
- Задать параметры сценария Hive можно с помощью свойства **defines**. Дополнительная информация приведена далее в этой статье. 

> [AZURE.NOTE]Подробную информацию о командлетах, схемах JSON и используемых в них свойствах см. в [справочнике разработчика](http://go.microsoft.com/fwlink/?LinkId=516908).


## Использование сценариев Pig и Hive в действии HDInsight
Вы можете сохранять сценарии Pig/Hive в хранилище больших двоичных объектов Azure, связанном с кластером HDInsight, и ссылаться на них из действий Pig/Hive, используя следующие свойства в JSON:

* **scriptPath** — путь к файлу сценария Pig или Hive;
* **scriptLinkedService** — учетная запись хранения Azure, в которой содержится файл сценария.

В следующем примере JSON для конвейера используется действие Hive. Оно ссылается на файл **transformdata.hql** из папки **scripts** в контейнере **adfwalkthrough** хранилища BLOB-объектов Azure, которое предоставляется службой **StorageLinkedService**.

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
    					"scriptPath": "adfwalkthrough\\scripts\\transformdata.hql",    		
						"scriptLinkedService": "StorageLinkedService", 
						"defines":
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


> [AZURE.NOTE]Чтобы запросы Hive обрабатывались с помощью механизма **Tez**, перед отправкой запроса выполните команду **set hive.execution.engine=tez**.
> 
> Подробную информацию о командлетах, схемах JSON и используемых в них свойствах см. в [справочнике разработчика](http://go.microsoft.com/fwlink/?LinkId=516908).

## Параметризованные запросы Pig и Hive
Действия Pig и Hive фабрики данных позволяют задавать параметры, используемые в сценариях Pig и Hive, с помощью свойства **defines**. Раздел defines состоит из имени и значения параметра.

В следующем примере показано, как задавать параметры для сценария Hive с помощью свойства **defines**. Чтобы использовать параметризованные сценарии Hive, выполните следующие действия.

1.	Задайте параметры в разделе **defines**.
2.	Во встроенном сценарии Hive или файле сценария Hive, сохраненном в хранилище больших двоичных объектов, создайте ссылку на параметр, используя синтаксис **${hiveconf:parameterName}**.

   
    		
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
							"defines":
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


## См. также

Статья | Описание
------ | ---------------
[Учебник. Перемещение и обработка файлов журнала с помощью фабрики данных][adf-tutorial] | В этом пошаговом руководстве показано, как с помощью фабрики данных Azure реализовать реалистичный сценарий преобразования данных из файлов журнала в полезные сведения.
[Справочник разработчика фабрики данных Azure][developer-reference] | Справочник разработчика содержит полные справочные данные по командлетам, скриптам JSON, функциям и т. п. 

[data-factory-copy-activity]: ..//data-factory-copy-activity
[data-factory-map-reduce]: ..//data-factory-map-reduce

[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[data-factory-blade]: ./media/data-factory-pig-hive-activities/DataFactoryBlade.png


[adfgetstarted]: data-factory-get-started.md
[adfgetstartedmonitoring]: data-factory-get-started.md#MonitorDataSetsAndPipeline
[adftutorial]: data-factory-tutorial.md

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com
 

<!---HONumber=August15_HO6-->