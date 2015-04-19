<properties
   pageTitle="Использование Hadoop Pig в HDInsight для платформы Azure"
   description="Информация об отправке заданий Pig в Hadoop в HDInsight с помощью PowerShell."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

#Выполнение заданий Pig с помощью PowerShell

[AZURE.INCLUDE [pig-selector](../includes/hdinsight-selector-use-pig.md)]

В этом документе приведен пример использования PowerShell для отправки заданий Pig в Hadoop в кластере HDInsight. Pig позволяет написать задания MapReduce с использованием языка (Pig Latin), который моделирует преобразования данных, а не функции сопоставления и приведения.

> [AZURE.NOTE] В этом документе не приводится подробное описание процессов, которые выполняют операторы Pig Latin, используемые в примерах. Информацию об операторах Pig Latin, используемых в данном примере, см. в статье <a href="../hdinsight-use-pig/" target="_blank">Использование Pig с Hadoop в HDInsight</a>.

##<a id="prereq"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее.

* Кластер Azure HDInsight (Hadoop в HDInsight) (на платформе Windows или Linux)

* <a href="http://azure.microsoft.com/documentation/articles/install-configure-powershell/" target="_blank">Azure PowerShell</a>


##<a id="powershell"></a>Выполнение заданий Pig с помощью PowerShell

Azure PowerShell предоставляет *командлеты*, позволяющие удаленно запускать задания Pig в HDInsight. Внутренне это достигается с помощью выполнения вызовов REST для <a href="https://cwiki.apache.org/confluence/display/Hive/WebHCat" target="_blank">WebHCat</a> (ранее называвшегося Templeton), запущенного в кластере HDInsight.

При выполнении заданий Pig на удаленном кластере HDInsight, используются следующие командлеты.

* **Add-AzureAccount** - выполняет аутентификацию PowerShell в подписке Azure.

* **New-AzureHDInsightPigJobDefinition** - создает новое *определение задания* с использованием заданных операторов Pig Latin.

* **Start-AzureHDInsightJob** - отправляет определение задания в HDInsight, запускает задание и возвращает объект *задания*, который можно использовать для проверки состояния задания.

* **Wait-AzureHDInsightJob** - использует объект job для проверки состояния задания. Он будет ждать завершения задания или превышения времени ожидания.

* **Get-AzureHDInsightJobOutput** - используется для получения выходных данных задания.

Следующие шаги показывают, как использовать эти командлеты для выполнения задания в кластере HDInsight. 

1. С помощью редактора сохраните следующий код в виде **pigjob.ps1**. Параметр **CLUSTERNAME** необходимо заменить именем кластера HDInsight.

		#Login to your Azure subscription
		Add-AzureAccount

		#Specify the cluster name
		$clusterName = "CLUSTERNAME"
		#Where the output will be saved
		$statusFolder = "/tutorial/pig/status"

		#Store the Pig Latin into $QueryString
		$QueryString =  "LOGS = LOAD 'wasb:///example/data/sample.log';" +
		"LEVELS = foreach LOGS generate REGEX_EXTRACT(`$0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
		"FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
		"GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
		"FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
		"RESULT = order FREQUENCIES by COUNT desc;" +
		"DUMP RESULT;"

		#Create a new HDInsight Pig Job definition
		$pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $QueryString -StatusFolder $statusFolder

		# Start the Pig job on the HDInsight cluster
		Write-Host "Start the Pig job ..." -ForegroundColor Green
		$pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition

		# Wait for the Pig job to complete
		Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

		# Print the output of the Pig job.
		Write-Host "Display the standard output ..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput

2. Откройте новую командную строку **Microsoft Azure PowerShell**. Перейдите к расположению файла **pigjob.ps1**, а затем используйте следующую команду для запуска сценария.

		.\pigjob.ps1

7. После завершения задания должна быть возвращена информация следующего вида.

		Start the Pig job ...
		Wait for the Pig job to complete ...

		Cluster         : CLUSTERNAME
		ExitCode        : 0
		Name            :
		PercentComplete : 100% complete
		Query           : LOGS = LOAD 'wasb:///example/data/sample.log';LEVELS = foreach LOGS generate REGEX_EXTRACT($0,
			'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is
			not null;GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;FREQUENCIES = foreach GROUPEDLEVELS
			generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;RESULT = order FREQUENCIES by
			COUNT desc;DUMP RESULT;
			State           : Completed
			StatusDirectory : /tutorial/pig/status
			SubmissionTime  : 11/20/2014 4:04:58 PM
			JobId           : job_1415949758166_0023

			Display the standard output ...
			(TRACE,816)
			(DEBUG,434)
			(INFO,96)
			(WARN,11)
			(ERROR,6)
			(FATAL,2)

##<a id="troubleshooting"></a>Устранение неполадок

Если данные не возвращаются по завершении задания, возможно, во время обработки произошла ошибка. Чтобы просмотреть информацию об ошибке для данного задания, добавьте следующий текст в конце файла **pigjob.ps1**, сохраните его, а затем запустите снова.

	# Print the output of the Pig job.
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardError

Возвращает информацию, которая записывается в STDERR на сервере при запуске задания, и может помочь определить причину сбоя задания.

##<a id="summary"></a>Сводка

Как можно видеть, Azure PowerShell позволяет с легкостью выполнять задания Pig в кластере HDInsight, отслеживать состояние задания и получать выходные данные.

##<a id="nextsteps"></a>Дальнейшие действия

Общая информация о Pig в HDInsight.

* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight.

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)

* [Использование MapReduce с Hadoop в HDInsight](hdinsight-use-mapreduce.md)
<!--HONumber=47-->
