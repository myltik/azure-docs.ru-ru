<properties
   pageTitle="Использование MapReduce и PowerShell с Hadoop | Microsoft Azure"
   description="Информация об использовании PowerShell для удаленного выполнения заданий MapReduce с помощью Hadoop в HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

#Выполнение запросов Hive с помощью PowerShell с использованием Hadoop в HDInsight

[AZURE.INCLUDE [mapreduce-selector](../includes/hdinsight-selector-use-mapreduce.md)]

В этом документе приведен пример использования Azure PowerShell для выполнения задания MapReduce в Hadoop на кластере HDInsight.

##<a id="prereq"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

* Кластер Azure HDInsight (Hadoop в HDInsight) (на платформе Windows или Linux).

* <a href="http://azure.microsoft.com/documentation/articles/install-configure-powershell/" target="_blank">Azure PowerShell</a>.

##<a id="powershell"></a>Выполнение задания MapReduce с помощью Azure PowerShell

Azure PowerShell предоставляет *командлеты*, позволяющие удаленно запускать задания MapReduce в HDInsight. Внутренне это достигается с помощью выполнения вызовов REST для <a href="https://cwiki.apache.org/confluence/display/Hive/WebHCat" target="_blank">WebHCat</a> (ранее называвшегося Templeton), запущенного в кластере HDInsight.

При выполнении заданий MapReduce в удаленном кластере HDInsight используются следующие командлеты:

* **Add-AzureAccount** — выполняет аутентификацию Azure PowerShell для подписки Azure.

* **New-AzureHDInsightMapReduceJobDefinition** — создает новое *задание*, используя заданную информацию MapReduce.

* **Start-AzureHDInsightJob** — отправляет определение задания в HDInsight, запускает задание и возвращает объект *задания*, который можно использовать для проверки состояния задания.

* **Wait-AzureHDInsightJob** — использует объект задания для проверки состояния задания. Он ожидает завершения задания или превышения времени ожидания.

* **Get-AzureHDInsightJobOutput** — используется для получения выходных данных задания.

Следующие шаги показывают, как использовать эти командлеты для выполнения задания в кластере HDInsight:

1. С помощью редактора сохраните следующий код как **mapreducejob.ps1**. Параметр **CLUSTERNAME** необходимо заменить именем кластера HDInsight.

		#Login to your Azure subscription
		# Is there an active Azure subscription?
		$sub = Get-AzureSubscription -ErrorAction SilentlyContinue
		if(-not($sub))
		{
		    Add-AzureAccount
		}

		#Specify the cluster name
		$clusterName = "CLUSTERNAME"

		#Define the MapReduce job
		#NOTE: If using an HDInsight 2.0 cluster, use hadoop-examples.jar instead.
		# -JarFile = the JAR containing the MapReduce application
		# -ClassName = the class of the application
		# -Arguments = The input file, and the output directory
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" `
		                          -ClassName "wordcount" `
		                          -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

		#Submit the job to the cluster
		Write-Host "Start the MapReduce job..." -ForegroundColor Green
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition

		#Wait for the job to complete
		Write-Host "Wait for the job to complete..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600

		# Print the output
		Write-Host "Display the standard output..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardOutput

2. Откройте новую командную строку **Azure PowerShell**. Перейдите к расположению файла **mapreducejob.ps1**, а затем используйте следующую команду для запуска сценария:

		.\mapreducejob.ps1

3. По завершении задания выходные данные должны выглядеть примерно так:

		Cluster         : CLUSTERNAME
		ExitCode        : 0
		Name            : wordcount
		PercentComplete : map 100% reduce 100%
		Query           :
		State           : Completed
		StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
		SubmissionTime  : 12/5/2014 8:34:09 PM
		JobId           : job_1415949758166_0071

	Такие выходные данные означают, что задание завершено успешно.
	
	> [AZURE.NOTE]Если значение **ExitCode** не равно 0, см. раздел [Устранение неполадок](#troubleshooting).

##<a id="results"></a>Просмотр выходных данных задания

Результаты операций задания MapReduce сохраняются в хранилище больших двоичных объектов Azure по пути **wasb:///example/data/WordCountOutput**, который указывается в качестве аргумента задания. Доступ к хранилищу больших двоичных объектов Azure можно получить с помощью Azure PowerShell, но для этого необходимо знать имя учетной записи хранения, ключ и контейнер, которые используются кластером HDInsight для непосредственного доступа к файлам.

Эту информацию можно получить с помощью следующих командлетов Azure PowerShell:

* **Get-AzureHDInsightCluster** — возвращает информацию о кластере HDInsight, включая все связанные с ним учетные записи хранения. С кластером всегда связанна учетная запись хранения по умолчанию.
* **New-AzureStorageContext** — при наличии имени учетной записи хранения и ключа, полученных с помощью **Get-AzureHDInsightCluster**, возвращает объект контекста, который может использоваться для доступа к учетной записи хранения.
* **Get-AzureStorageBlob** — при наличии контекста объекта и имени контейнера возвращает список больших двоичных объектов в контейнере.
* **Get-AzureStorageBlobContent** — при наличии объекта контекста, пути к файлу, имени, а также имени контейнера (которые возвращает **Get AzureHDinsightCluster**) скачивает файл из хранилища больших двоичных объектов Azure.

Следующий пример извлекает информацию о хранилище, а затем скачивает выходные данные из **wasb:///example/data/WordCountOutput**. Параметр **CLUSTERNAME** нужно заменить именем кластера HDInsight.

		#Login to your Azure subscription
		# Is there an active Azure subscription?
		$sub = Get-AzureSubscription -ErrorAction SilentlyContinue
		if(-not($sub))
		{
		    Add-AzureAccount
		}

		#Specify the cluster name
		$clusterName = "CLUSTERNAME"

		#Retrieve the cluster information
		$clusterInfo = Get-AzureHDInsightCluster -ClusterName $clusterName

		#Get the storage account information
		$storageAccountName = $clusterInfo.DefaultStorageAccount.StorageAccountName
		$storageAccountKey = $clusterInfo.DefaultStorageAccount.StorageAccountKey
		$storageContainer = $clusterInfo.DefaultStorageAccount.StorageContainerName

		#Create the context object
		$context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

		#Download the files from wasb:///example/data/WordCountOutput
		#Use the -blob switch to filter only blobs contained in example/data/WordCountOutput
		Get-AzureStorageBlob -Container $storageContainer -Blob example/data/WordCountOutput/* -Context $context | Get-AzureStorageBlobContent -Context $context

> [AZURE.NOTE]В этом примере скачанные файлы будут храниться в папке **example/data/WordCountOutput** в каталоге, из которого запускается сценарий.

Выходные данные задания MapReduce сохраняются в файлах с именем *part-r-№№№№№*. Откройте файл **example/data/WordCountOutput/part-r-00000** в текстовом редакторе, чтобы просмотреть слова и статистику, полученные в результате выполнения задания.

> [AZURE.NOTE]Выходные файлы задания MapReduce являются неизменяемыми. Поэтому при повторном выполнении этого примера потребуется изменить имя выходного файла.

##<a id="troubleshooting"></a>Устранение неполадок

Если данные не возвращаются по завершении задания, возможно, во время обработки произошла ошибка. Чтобы просмотреть информацию об ошибке для данного задания, добавьте следующую команду в конец файла **mapreducejob.ps1**, сохраните его, а затем запустите снова.

	# Print the output of the WordCount job.
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError

Будет возвращена информация, которая записывается в STDERR на сервере при запуске задания и может помочь определить причину сбоя задания.

##<a id="summary"></a>Сводка

Как можно видеть, Azure PowerShell позволяет с легкостью выполнять задания MapReduce в кластере HDInsight, отслеживать состояние задания и получать выходные данные.

##<a id="nextsteps"></a>Дальнейшие действия

Общая информация о заданиях MapReduce в HDInsight:

* [Использование MapReduce в Hadoop в HDInsight](hdinsight-use-mapreduce.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight:

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)

* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)

<!--HONumber=54-->