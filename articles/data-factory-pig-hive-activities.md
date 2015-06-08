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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Использование Pig и Hive с фабрикой данных
Конвейер в фабрике данных Azure обрабатывает данные в связанной службе хранилища с помощью связанных вычислительных служб. В нем содержится последовательность действий, каждое из которых выполняет определенную операцию обработки. Этой статье описывается использование HDInsight действия с преобразованием Pig и Hive в конвейер фабрика данных Azure. В разделе [вызова программ MapReduce из фабрики данных][data-factory-map-reduce] подробные сведения о выполнении MapReduce кластера программ на HDInsight из конвейера фабрики данных Azure.

## Пошаговое руководство: Использование Hive с фабрикой данных Azure
В этом пошаговом руководстве приведены пошаговые инструкции по использованию действия HDInsight с преобразованием Hive в конвейере данных фабрики.

### Предварительные требования
1. Работа с учебником из [Приступая к работе с фабрикой данных Azure][adfgetstarted] статьи.
2. Отправка **emp.txt** файл, созданный в учебнике выше как **hiveinput\emp.txt** контейнер adftutorial в хранилище больших двоичных объектов.  **Hiveinput** автоматически создается папка в **adftutorial** контейнера при загрузке файла emp.txt со следующим синтаксисом.

	> [AZURE.NOTE]Файл emp.txt является пустой файл для данного пошагового руководства. Фактические входные данные поступают из **hivesampletable** уже существует в кластере HDInsight. Конвейер вообще не использовать файл emp.txt.
	
2. Создание **hivequery.hql** файл во вложенной папке **Hive** под **C:\ADFGetStarted** со следующим содержимым.
    		
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

	> [AZURE.NOTE]Для использования **Tez** механизм выполнения запросов Hive в файле HQL, добавьте "** набор hive.execution.engine=tez**;" в верхней части файла.
		
3.  Отправка **hivequery.hql** для **adftutorial** контейнер в хранилище BLOB-объектов


### Пошаговое руководство

#### Создание входной таблицы
1. В **ФАБРИКА данных** выноски для **ADFTutorialDataFactory**, щелкните **автора и развернуть** для запуска редактора данных фабрики.
	
	![Выноска фабрика данных][data-factory-blade]

2. В **данных фабрики редактора**, щелкните **новый набор данных**, и нажмите кнопку **хранилища BLOB-объектов Azure** на панели команд.
3. Замените следующий скрипт JSON скрипта JSON в правой области.    
    		
		{
    		"name": "HiveInputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
            		"folderPath": "adftutorial/hiveinput",
            		"linkedServiceName": "StorageLinkedService"
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
	
	- расположение **тип** равен **AzureBlobLocation**.
	- **linkedServiceName** равен **StorageLinkedService** определяющее учетную запись хранилища Azure.
	- **folderPath** указывает container\folder больших двоичных объектов для входных данных. 
	- **Частота = день** и **интервал = 1** означает срезы доступны ежедневно
	- **waitOnExternal** означает, что эти данные не был создан другой конвейер, вместо созданного извне к производству данных. 
	

	В разделе [Справочник разработчика фабрики данных][developer-reference] для описания свойств JSON.

2. Щелкните **Развернуть** на панели команд, чтобы развернуть таблицу.
  
#### Создание выходной таблицы
        
1. В **данных фабрики редактора**, щелкните **новый набор данных**, и нажмите кнопку **хранилища BLOB-объектов Azure** на панели команд.
2. Замените следующий скрипт JSON скрипта JSON в правой области.

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

2. Щелкните **Развернуть** на панели команд, чтобы развернуть таблицу.


### Создание связанной службы для кластера HDInsight
Служба фабрики данных Azure поддерживает создание кластера по запросу и использует его для обработки входных данных, чтобы создать выходные данные. Вы также можете использовать для этого собственный кластер. Когда вы используете кластер HDInsight по запросу, для каждого среза создается отдельный кластер. В то же время, когда вы используете свой собственный кластер HDInsight, кластер готов к обработке среза немедленно. Поэтому при использовании кластера по запросу выходные данные могут выводится не так быстро, как при использовании собственного кластера. Давайте в качестве примера используем кластер по запросу.

#### Использование кластера HDInsight по запросу
1. Щелкните **новых вычислений** из командной строки и выберите **кластера HDInsight по требованию** меню.
2. Выполните следующие действия в скрипт JSON. 
	1. Для **clusterSize** свойство, укажите размер кластера HDInsight.
	2. Для **jobsContainer** свойство, укажите имя контейнера по умолчанию, где будет храниться журнал кластера. В целях этого учебника укажите **adfjobscontainer**.
	3. Для **timeToLive** свойство, укажите, как долго кластера может простаивать перед его удалением. 
	4. Для **версии** свойство, укажите версию HDInsight, вы хотите использовать. Если исключить это свойство используется последняя версия.  
	5. Для **linkedServiceName**, укажите **StorageLinkedService** был создан в Get учебник работы. 

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

2. Щелкните **Развернуть** на панели команд для развертывания связанной службы.
   
   
#### Использование собственного кластера HDInsight 

1. Щелкните **новых вычислений** из командной строки и выберите **кластера HDInsight** в меню.
2. Выполните следующие действия в скрипт JSON. 
	1. Для **clusterUri** свойство, введите URL-адрес для вашей HDInsight. Например: https://<clustername>.azurehdinsight.net/     
	2. Для **пользователя** свойство, введите имя пользователя, который имеет доступ к кластеру HDInsight.
	3. Для **пароль** свойство, введите пароль для пользователя. 
	4. Для **LinkedServiceName** свойство, введите **StorageLinkedService**. Это связанные службы, с которой был создан в учебнике работы Get. 

2. Щелкните **Развернуть** на панели команд для развертывания связанной службы.

### Создание и планирование конвейера
   
1. Щелкните **новому** на панели команд. Если команда не отображается, нажмите кнопку **... (Многоточие)** Чтобы просмотреть его. 
2. Замените следующий скрипт JSON JSON в правой области. Если требуется использовать собственный кластер и затем шаги для создания **HDInsightLinkedService** связанные службы, замените **HDInsightOnDemandLinkedService** с **HDInsightLinkedService** в следующий JSON. 


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
						"linkedServiceName": "HDInsightLinkedService",
						"transformation":
						{
                    		"type": "Hive",
                    		"extendedProperties":
                    		{
                        		"RESULTOUTPUT": "wasb://adftutorial@<your storage account>.blob.core.windows.net/hiveoutput/",
		                        "Year":"$$Text.Format('{0:yyyy}',SliceStart)",
		                        "Month":"$$Text.Format('{0:%M}',SliceStart)",
		                        "Day":"$$Text.Format('{0:%d}',SliceStart)"
		                    },
		                    "scriptpath": "adftutorial\hivequery.hql",
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

	> [AZURE.NOTE]Замените **StartDateTime** значение с трех дней до текущего дня и **EndDateTime** значение с текущего дня. StartDateTime и EndDateTime должны находиться в [Формат ISO](http://en.wikipedia.org/wiki/ISO_8601). Например: 2014-10-14T16:32:41Z. Согласно расписанию выходная таблица будет создаваться ежедневно, поэтому всего будет произведено три среза.
	
	> [AZURE.NOTE]Замените **вашей учетной записи хранилища** в JSON с именем вашей учетной записи хранилища.
	
	В разделе [ссылки сценария JSON](http://go.microsoft.com/fwlink/?LinkId=516971) подробные сведения о свойствах JSON.
2. Щелкните **Развернуть** на панели команд для развертывания в конвейер.
4. См. [отслеживать наборы данных и конвейера][adfgetstartedmonitoring] раздел [Приступая к работе с фабрикой данных][adfgetstarted] статьи. 

	> [AZURE.NOTE]В **сведения о выполнении действия** выноску фрагмента выходную таблицу (выберите выходную таблицу -> выберите срез -> выберите действие запуска на портале), вы увидите ссылки на журналы, созданные в кластере HDInsight. Можно просматривать их с самого портала или загрузить их на компьютер.
  

## Пример JSON Pig
При определении в конвейере JSON, Pig и Hive действия **тип** свойству должно быть присвоено: **HDInsightActivity**.

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
	
- Действие **тип** равен **HDInsightActivity**.
- **linkedServiceName** равен **MyHDInsightLinkedService**. См. связанный HDInsight службы ниже подробные сведения по созданию службы HDInsight связаны.
-  **Тип** из **преобразования** равен **Pig**.
- Можно указать встроенные сценария Pig для **сценарий** свойства или хранилища файлов скрипта в Azure хранилище BLOB-объектов и ссылаться на файл с помощью **scriptPath** свойство, которое описано далее в этой статье. 
- Укажите параметры для сценария Pig с помощью **extendedProperties**. Дополнительная информация приведена далее в этой статье. 


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
			"extendedProperties":
			{	
				"param1": "param1Value"
            }
		}
	}

**Обратите внимание на следующее:**
	
- Действие **тип** равен **HDInsightActivity**.
- **linkedServiceName** равен **MyHDInsightLinkedService**. 
-  **Тип** из **преобразования** равен **Hive**.
- Можно указать встроенный скрипт Hive для **сценарий** свойства или хранилища файлов скрипта в Azure хранилище BLOB-объектов и ссылаться на файл с помощью **scriptPath** свойство, которое описано далее в этой статье. 
- Укажите параметры для скрипта Hive с помощью **extendedProperties**. Дополнительная информация приведена далее в этой статье. 

> [AZURE.NOTE]В разделе [Справочник разработчика](http://go.microsoft.com/fwlink/?LinkId=516908) подробные сведения о командлетах, схем JSON и свойств в схеме.


## С помощью сценариев Pig и Hive в HDInsight действия
Вы можете сохранять сценарии Pig/Hive в хранилище больших двоичных объектов Azure, связанном с кластером HDInsight, и ссылаться на них из действий Pig/Hive, используя следующие свойства в JSON:

* **scriptPath** — путь к файлу сценария Pig и Hive
* **scriptLinkedService** — учетной записи хранилища Azure, которая содержит файл сценария

В следующем примере JSON для конвейера образца используется действие Hive, которое ссылается на **transformdata.hql** файла, хранящегося в **сценариев** папки в **adfwalkthrough** контейнер в хранилище больших двоичных объектов, представленных **StorageLinkedService**.

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
    					"scriptpath": "adfwalkthrough\scripts\transformdata.hql",    		
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


> [AZURE.NOTE]Для использования **Tez** механизм выполнения запроса Hive, запустите "** набор hive.execution.engine=tez**;" перед выполнением запроса Hive.
> 
> В разделе [Справочник разработчика](http://go.microsoft.com/fwlink/?LinkId=516908) подробные сведения о командлетах, схем JSON и свойств в схеме.

## Параметризованные запросы Pig и Hive
Данные фабрики Pig и Hive действий позволяют указать значения для параметров, используемых в скрипты Pig и Hive с помощью **extendedProperties**. Раздел extendedProperties состоит из имени и значения параметра.

См. в следующем примере для указания параметров для скрипта Hive с помощью **extendedProperties**. Чтобы использовать параметризованные сценарии Hive, выполните следующие действия.

1.	Определение параметров в **extendedProperties**.
2.	В скрипт Hive в строке (или) хранятся в хранилище блог файл скрипта, ссылаются на параметр с помощью **${hiveconf:parameterName}**.

   
    		
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


## См. также

Статья | Описание
------ | ---------------
[Учебник: Перемещение и обрабатывать файлы журнала, с помощью фабрики данных][adf-tutorial] | В этой статье приведены в сквозном Пошаговое руководство, которое показывает, как реализовать near реального мира сценарий, с помощью фабрики данных Azure для преобразования данных из файлов журнала в полезных.
[Справочник разработчика фабрики данных Azure][developer-reference] | Справочник разработчика имеется содержимое полный Справочник командлетов, скрипта JSON, функции и т. д... 

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

<!---HONumber=GIT-SubDir-->