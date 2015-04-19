<properties
   pageTitle="Использование Hadoop Hive в HDInsight для платформы Azure"
   description="Использование Hive в Hadoop в HDInsight с помощью PowerShell"
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

#Выполнение запросов Hive с помощью PowerShell

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

В этом документе приведен пример использования PowerShell для выполнения запросов Hive в Hadoop на кластере HDInsight.

> [AZURE.NOTE] В этом документе не приводится подробное описание действия инструкций HiveQL, используемых в данных примерах. Сведения о HiveQL, используемом в этом примере, см. в разделе  <a href="../hdinsight-use-hive/" target="_blank">ИспользованиеHive с Hadoop в HDInsight</a>.


##<a id="prereq"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее.

* Кластер Azure HDInsight (Hadoop в HDInsight)(на платформе Windows или Linux)

* <a href="http://azure.microsoft.com/documentation/articles/install-configure-powershell/" target="_blank">Azure PowerShell</a>


##<a id="powershell"></a>Выполнение запросов Hive с помощью PowerShell

Azure PowerShell предоставляет *cmdlets*, позволяющие удаленно запускать запросы Hive в HDInsight. Внутренне это достигается с помощью вызовов REST к <a href="https://cwiki.apache.org/confluence/display/Hive/WebHCat" target="_blank">WebHCat</a> (ранее называвшийся Templeton), запущенном на кластере HDInsight.

При выполнении запросов Hive на удаленном кластере HDInsight, используются следующие командлеты.

* **Add-AzureAccount** - выполняет аутентификацию PowerShell в подписке Azure.

* **New-AzureHDInsightHiveJobDefinition** - создает новое *определение задания* с использованием заданных инструкций HiveQL

* **Start-AzureHDInsightJob** - отправляет определение задания в HDInsight, запускает задание и возвращает объект *job*, который можно использовать для проверки состояния задания

* **Wait-AzureHDInsightJob** - использует объект job для проверки состояния задания. Он будет ждать завершения задания или превышения времени ожидания.

* **Get-AzureHDInsightJobOutput** - используется для получения выходных данных задания.

* **Invoke-Hive** - служит для запуска операторов HiveQL и блока, который он завершает. Затем возвращает результаты.

* **Use-AzureHDInsightCluster** - указывает, что при выполнении команды **Invoke-Hive** должен использоваться текущий кластер.

Следующие шаги показывают, как использовать эти командлеты для выполнения задания в кластере HDInsight. 

1. С помощью редактора сохраните следующий код в виде **hivejob.ps1**. Параметр **CLUSTERNAME** необходимо заменить именем кластера HDInsight.

		#Login to your Azure subscription
		# Is there an active Azure subscription?
		$sub = Get-AzureSubscription -ErrorAction SilentlyContinue
		if(-not($sub))
		{
		    Add-AzureAccount
		}
		
		#Specify the cluster name
		$clusterName = "CLUSTERNAME" 
		
		#HiveQL
		$queryString = "DROP TABLE log4jLogs;" +
				       "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb:///example/data/';" +
				       "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"
		
		#Create an HDInsight Hive job definition
		$hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString
		
		#Submit the job to the cluster
		Write-Host "Start the Hive job..." -ForegroundColor Green
		$hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition
		
		#Wait for the Hive job to complete
		Write-Host "Wait for the job to complete..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600
		
		# Print the output
		Write-Host "Display the standard output..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput

2. Откройте новую командную строку **Microsoft Azure PowerShell**. Перейдите к расположению файла **hivejob.ps1**, а затем используйте следующую команду для запуска сценария.

		.\hivejob.ps1

7. После завершения задания должна быть возвращена информация следующего вида.

		Display the standard output...
		[ERROR]	3

4. Как уже упоминалось, **Invoke-Hive** можно использовать для отправки запроса и ожидания ответа. Используйте следующие команды, заменив **CLUSTERNAME** именем кластера.

		Use-AzureHDInsightCluster CLUSTERNAME
		Invoke-Hive -Query @"
		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
		SELECT * FROM errorLogs;
		"@

	The output will look like the following.

		2012-02-03	18:35:34	SampleClass0	[ERROR]	incorrect	id	
		2012-02-03	18:55:54	SampleClass1	[ERROR]	incorrect	id	
		2012-02-03	19:25:27	SampleClass4	[ERROR]	incorrect	id

	> [AZURE.NOTE] Для более длинных запросов HiveQL вы можете использовать PowerShell Here-Strings или файл сценария HiveQL. В приведенном ниже отрывке кода показано, как использовать командлет *Invoke-Hive* для запуска файла сценария HiveQL. Файл сценария HiveQL необходимо передать в WASB.
	>
	> `Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
	>
	> Дополнительные сведения о конструкции Here-Strings см. в разделе  <a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">Использование конструкции Here-Strings в Windows PowerShell</a>.

##<a id="troubleshooting"></a>Устранение неполадок

Если данные не возвращаются по завершении задания, возможно, во время обработки произошла ошибка. Чтобы просмотреть информацию об ошибке для данного задания, добавьте следующий текст в конце файла **hivejob.ps1**, сохраните его, а затем запустите снова.

	# Print the output of the Hive job.
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardError

Возвращает информацию, которая записывается в STDERR на сервере при запуске задания, и может помочь определить причину сбоя задания.

##<a id="summary"></a>Сводка

Как можно видеть, Azure PowerShell позволяет с легкостью выполнять запросы Hive в кластере HDInsight, отслеживать состояние задания и получать выходные данные.

##<a id="nextsteps"></a>Дальнейшие действия

Общая информация о Hive в HDInsight.

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight.

* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)

* [Использование MapReduce с Hadoop в HDInsight](hdinsight-use-mapreduce.md)

<!--HONumber=47-->
