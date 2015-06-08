<properties 
	pageTitle="Вызов программы MapReduce из фабрики данных Azure" 
	description="Узнайте, как обрабатывать данные путем запуска программы MapReduce в кластере Azure HDInsight из фабрики данных Azure." 
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

# Вызов программы MapReduce из фабрики данных
В этой статье описывается вызов **MapReduce** программы из конвейера фабрика данных Azure с помощью **HDInsight действия** с **преобразования MapReduce**.

## Введение 
Конвейер в фабрике данных Azure обрабатывает данные в связанной службе хранилища с помощью связанных вычислительных служб. В нем содержится последовательность действий, каждое из которых выполняет определенную операцию обработки. В этой статье описывается использование преобразования MapReduce действия HDInsight.
 
В разделе [Использование Pig и Hive с фабрикой данных][data-factory-pig-hive-activities] подробные сведения о выполнении Pig и Hive скриптов в HDInsight кластера из конвейера фабрики данных Azure с помощью преобразований Pig и Hive в HDInsight действия.

## JSON для действия HDInsight с помощью преобразования MapReduce 

В определении JSON для действия HDInsight:
 
1. Задайте **тип** из **действия** для **HDInsightActivity**.
2. Задайте **тип** из **преобразования** для **MapReduce**.
3. Укажите имя класса для **className** свойство.
4. Укажите путь к файлу JAR-ФАЙЛ, включающий имя файла для **jarFilePath** свойство.
5. Укажите связанного службы, который ссылается на хранилище Azure BLOB-объект, содержащий JAR-файл для **jarLinkedService** свойство.   
6. Задайте аргументы для программы MapReduce в **аргументы** раздела. 

   
 

		{  
		   "name":"MahoutMapReduceSamplePipeline",
		   "properties":{  
		      "description":"Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
		      "activities":[  
		         {  
		            "name":"MyMahoutActivity",
		            "description":"Custom Map Reduce to generate Mahout result",
		            "type":"HDInsightActivity",
		            "inputs":[  
		               {  
		                  "Name":"MahoutInput"
		               }
		            ],
		            "outputs":[  
		               {  
		                  "Name":"MahoutOutput"
		               }
		            ],
		            "linkedServiceName":"HDInsightLinkedService",
		            "transformation":{  
		               "type":"MapReduce",
		               "className":"org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
		               "jarFilePath":"<container>/Mahout/Jars/mahout-core-0.9.0.2.1.3.2-0002-job.jar",
		               "jarLinkedService":"StorageLinkedService",
		               "arguments":[  
		                  "-s",
		                  "SIMILARITY_LOGLIKELIHOOD",
		                  "--input",
		                  "wasb://<container>@<accountname>.blob.core.windows.net/Mahout/input",
		                  "--output",
		                  "wasb://<container>@<accountname>.blob.core.windows.net/Mahout/output/",
		                  "--maxSimilaritiesPerItem",
		                  "500",
		                  "--tempDir",
		                  "wasb://<container>@<accountname>.blob.core.windows.net/Mahout/temp/mahout"
		               ]
		            },
		            "policy":{  
		               "concurrency":1,
		               "executionPriorityOrder":"OldestFirst",
		               "retry":3,
		               "timeout":"01:00:00"
		            }
		         }
		      ]
		   }
		}

Выполнять JAR-файлы MapReduce в кластере HDInsight можно с помощью преобразования MapReduce. В приведенном ниже образце определения конвейера JSON действие HDInsight настроено на запуск JAR-файла Mahout.

## Образец
Можно загрузить образец для использования с преобразования MapReduce с HDInsight действия: [фабрики образцы данных на GitHub][data-factory-samples].

## См. также

Статья | Описание
------ | ---------------
[Учебник: Перемещение и обрабатывать файлы журнала, с помощью фабрики данных][adf-tutorial] | В этой статье приведены в сквозном Пошаговое руководство, которое показывает, как реализовать near реального мира сценарий, с помощью фабрики данных Azure для преобразования данных из файлов журнала в полезных. В этом учебнике будет использоваться Pig и Hive преобразований для обработки данных. 
[Справочник разработчика фабрики данных Azure][developer-reference] | Справочник разработчика имеется содержимое полный Справочник командлетов, скрипта JSON, функции и т. д... 


[data-factory-samples]: http://go.microsoft.com/fwlink/?LinkId=516907
[data-factory-pig-hive-activities]: data-factory-pig-hive-activities.md
[data-factory-copy-activity]: ..//data-factory-copy-activity
[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-get-started.md
[adfgetstartedmonitoring]: data-factory-get-started.md#MonitorDataSetsAndPipeline
[adftutorial]: data-factory-tutorial.md

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com

<!---HONumber=GIT-SubDir-->