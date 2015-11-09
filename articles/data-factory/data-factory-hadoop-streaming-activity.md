<properties 
	pageTitle="Потоковая активность Hadoop" 
	description="Узнайте, как с помощью действия потоковой передачи Hadoop в фабрике данных Azure выполнять программы потоковой передачи Hadoop в собственном кластере HDInsight или кластере по требованию." 
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
	ms.date="10/25/2015" 
	ms.author="spelluru"/>

# Потоковая активность Hadoop
Можно использовать действие HDInsightStreamingActivity для вызова задания потоковой передачи Hadoop из конвейера фабрики данных Azure. В следующем фрагменте JSON показан синтаксис для использования действия HDInsightStreamingActivity в JSON-файле конвейера.

Действие потоковой передачи HDInsight в [конвейере](data-factory-create-pipelines.md) фабрики данных выполняет программы потоковой передачи Hadoop [самостоятельно](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) или в кластере HDInsight [по требованию](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Эта статья основана на материалах статьи [Действия преобразования данных](data-factory-data-transformation-activities.md), в которой приведен общий обзор преобразования данных и поддерживаемые действия преобразования.

## Пример

	{
	    "name": "HadoopStreamingPipeline",
	    "properties":
	    {
	        "description" : "Hadoop Streaming Demo",
	        "activities":
	        [
	           {
	               "name": "RunHadoopStreamingJob",
	               "description": "Run a Hadoop streaming job",
	               "type": "HDInsightStreaming",
	               "getDebugInfo": "Failure",
	               "inputs": [ ],
	               "outputs": [ {"name": "OutputTable"} ],
	               "linkedServiceName": "HDInsightLinkedService",
	               "typeProperties":
	               {
	                   "mapper": "cat.exe",
	                   "reducer": "wc.exe",
	                   "input":  "wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt",
	                   "output": " wasb://adfsample@<account name>.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
	                   "filePaths": [ 
	                       "adfsample/example/apps/wc.exe" , 
	                       "adfsample/example/apps/cat.exe" 
	                   ],
	                   "fileLinkedService" : "StorageLinkedService",
	                   "arguments":[
	                   ]
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

Обратите внимание на следующее.

1. Укажите в качестве значения параметра **linkedServiceName** имя связанной службы, которое указывает на ваш кластер HDInsight, где будет выполняться задание потоковой передачи mapreduce.
2. Задайте в качестве типа действия значение **HDInsightStreaming**.
3. Для свойства **mapper** укажите имя исполняемого файла mapper. В примере выше таким файлом является cat.exe.
4. Для свойства **reducer** укажите имя исполняемого файла reducer. В примере выше таким файлом является wc.exe.
5. Для свойства **input** укажите входной файл (включая местоположение) для свойства mapper. В примере wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt adfsample — это контейнер больших двоичных объектов, example/data/Gutenberg — это папка, а davinci.txt — это большой двоичный объект.
6. Для свойства **output** укажите выходной файл (включая местоположение) для свойства reducer. Результат задания потоковой передачи Hadoop записывается в расположение, заданное для этого свойства.
7. В разделе **filePaths** укажите пути для исполняемых файлов mapper и reducer. В примере adfsample/example/apps/wc.exe adfsample — это контейнер больших двоичных объектов, example/apps — это папка, а wc.exe — это исполняемый файл.
8. Для свойства **fileLinkedService** укажите связанную службу хранилища Azure, которая представляет хранилище Azure, где хранятся указанные в разделе filePaths файлы.
9. Для свойства **arguments** укажите аргументы для задания потоковой передачи.
10. Свойство **GetDebugInfo** является необязательным элементом. Если для него установлено значение Failure, журналы скачиваются только при сбое. Если для него установлено значение All, журналы скачиваются всегда, независимо от состояния выполнения. 

	

<!---HONumber=Nov15_HO1-->