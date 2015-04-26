<properties title="Invoke MapReduce Program from Azure Data Factory" pageTitle="Вызов программы MapReduce из фабрики данных Azure" description="Узнайте, как обрабатывать данные путем запуска программы MapReduce в кластере Azure HDInsight из фабрики данных Azure." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/13/2014" ms.author="spelluru" />

# Вызов программы MapReduce из фабрики данных
В этой статье описано, как вызывать программу **MapReduce** из конвейера фабрики данных Azure с помощью **действия HDInsight** с **преобразованием MapReduce**. 

## Введение 
Конвейер фабрики данных Azure обрабатывает данные в связанных службах хранения с помощью связанных вычислительных служб. В нем содержится последовательность действий, каждое из которых выполняет определенную операцию обработки. 

- **Действие копирования** копирует данные из исходного хранилища в место назначения. Дополнительные сведения о действии копирования см. в разделе [Копирование данных с помощью фабрики данных][data-factory-copy-activity]. 
- **Действие HDInsight** обрабатывает данные, запуская сценарии Hive и Pig или программы MapReduce в кластере HDInsight. Действие HDInsight поддерживает три типа преобразований: **Hive**, **Pig** и **MapReduce**. Действие HDInsight может использовать одно или несколько входных данных и создавать одно или несколько выходных.
 
Дополнительные сведения о запуске сценариев Pig и Hive в кластере HDInsight из конвейера фабрики данных Azure с помощью преобразований Pig и Hive действия HDInsight см. в разделе [Использование сценариев Pig и Hive в фабрике данных][data-factory-pig-hive-activities]. В этой статье описывается использование преобразования MapReduce действия HDInsight.

## Конвейер JSON
В файле JSON для конвейера:
 
1. В поле **тип** **действия** задайте параметр **HDInsightActivity**.
2. В поле **тип** **преображения** задайте параметр **MapReduce**.
3. Укажите имя класса для свойства **className**.
4. Укажите путь к JAR-файлу, включив в него имя файла для свойства **jarFilePath**.
5. Укажите связанную службу, которая обращается к хранилищу BLOB-объектов Azure, содержащему JAR-файл для свойства **jarLinkedService**.   
6. Укажите необходимые аргументы для программы MapReduce в разделе **аргументы**. 

Выполнять JAR-файлы MapReduce в кластере HDInsight можно с помощью преобразования MapReduce. В приведенном ниже образце определения конвейера JSON действие HDInsight настроено на запуск JAR-файла Mahout.   
 

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

## Образец
Загрузить образец для использования действия HDInsight с преобразованием MapReduce можно по этой ссылке: [Примеры фабрики данных на GitHub][data-factory-samples].  

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


[data-factory-samples]: http://go.microsoft.com/fwlink/?LinkId=516907
[data-factory-pig-hive-activities]: ../data-factory-pig-hive-activities
[data-factory-copy-activity]: ..//data-factory-copy-activity
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

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com

<!--HONumber=35.2-->
