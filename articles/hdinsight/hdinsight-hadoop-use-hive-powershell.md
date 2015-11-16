<properties
   pageTitle="Использование Hive в Hadoop в HDInsight с помощью PowerShell | Microsoft Azure"
   description="Использование PowerShell для выполнения запросов Hive в Hadoop в HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="11/02/2015"
   ms.author="larryfr"/>

#Выполнение запросов Hive с помощью PowerShell

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

В этом документе приведен пример использования Azure PowerShell в режиме группы ресурсов Azure для выполнения запросов Hive в Hadoop в кластере HDInsight. Сведения об использовании Azure PowerShell в режиме службы Azure см. в разделе [Выполнение запросов Hive с помощью PowerShell](hdinsight-hadoop-use-hive-powershell-v1.md).

> [AZURE.NOTE]В этом документе не приводится подробное описание процессов, которые выполняют инструкции HiveQL, используемые в примерах. Информацию об операторах HiveQL, используемых в данном примере, см. в статье [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md).


**Предварительные требования**

Чтобы выполнить действия, описанные в этой статье, необходимо следующее.

- **Кластер Azure HDInsight (Hadoop в HDInsight) (на платформе Windows или Linux)**
- **Рабочая станция с Azure PowerShell**. Обратитесь к разделу [Установка и использование Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

##Выполнение запросов Hive с помощью Azure PowerShell

Azure PowerShell предоставляет *командлеты*, позволяющие удаленно запускать запросы Hive в HDInsight. Внутренне это достигается с помощью выполнения вызовов REST для [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (прежнее название — Templeton) на кластере HDInsight.

При выполнении запросов Hive на удаленном кластере HDInsight используются следующие командлеты:

* **Add-AzureRmAccount** — выполняет проверку подлинности Azure PowerShell для подписки Azure.

* **New-AzureRmHDInsightHiveJobDefinition** — создает новое *определение задания* с использованием заданных операторов HiveQL.

* **Start-AzureRmHDInsightJob** — отправляет определение задания в HDInsight, запускает задание и возвращает объект-*задание*, который можно использовать для проверки состояния задания.

* **Wait-AzureRmHDInsightJob** — использует объект-задание для проверки состояния задания. Он будет ждать завершения задания или превышения времени ожидания.

* **Get-AzureRmHDInsightJobOutput** — используется для получения выходных данных задания.

* **Invoke-AzureRmHDInsightHiveJob** — используется для выполнения операторов HiveQL. При этом выполнение запроса блокируется, после чего возвращаются результаты.

* **Use-AzureRmHDInsightCluster** — указывает, что при выполнении команды **Invoke-AzureRmHDInsightHiveJob** должен использоваться текущий кластер.

Следующие шаги показывают, как использовать эти командлеты для выполнения задания в кластере HDInsight:

1. С помощью редактора сохраните следующий код как **hivejob.ps1**. Параметр **CLUSTERNAME** необходимо заменить именем кластера HDInsight.

		#Specify the values
		$clusterName = "CLUSTERNAME"
		$creds=Get-Credential
        		
		# Login to your Azure subscription
		# Is there an active Azure subscription?
		$sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
		if(-not($sub))
		{
		    Add-AzureRmAccount
		}

		#HiveQL
		$queryString = "DROP TABLE log4jLogs;" +
				       "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb:///example/data/';" +
				       "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"

		#Create an HDInsight Hive job definition
		$hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString 

		#Submit the job to the cluster
		Write-Host "Start the Hive job..." -ForegroundColor Green

		$hiveJob = Start-AzureRmHDInsightJob -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $creds


		#Wait for the Hive job to complete
		Write-Host "Wait for the job to complete..." -ForegroundColor Green
		Wait-AzureRmHDInsightJob -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $creds

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup `
            | %{ $_.Key1 }
		# Print the output
		Write-Host "Display the standard output..." -ForegroundColor Green
		Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $hiveJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds
            
2. Откройте новую командную строку **Azure PowerShell**. Перейдите к расположению файла **hivejob.ps1**, а затем используйте следующую команду для запуска сценария:

		.\hivejob.ps1

    При запуске сценария будет предложено ввести сведения HTTPS/учетные данные учетной записи администратора для кластера. Вам также может быть предложено выполнить вход в свою подписку Azure.
    
7. После завершения задания должна быть возвращена информация следующего вида:

		Display the standard output...
		[ERROR]	3

4. Как уже упоминалось, **Invoke-Hive** можно использовать для отправки запроса и ожидания ответа. Используйте следующие команды, заменив **CLUSTERNAME** именем кластера:

        Use-AzureRmHDInsightCluster -ClusterName $clusterName
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup `
            | %{ $_.Key1 }
        Invoke-AzureRmHDInsightHiveJob `
            -StatusFolder "wasb:///example/statusout" `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -Query @"
        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
        SELECT * FROM errorLogs;
        "@

	Выходные данные будут выглядеть следующим образом:

		2012-02-03	18:35:34	SampleClass0	[ERROR]	incorrect	id
		2012-02-03	18:55:54	SampleClass1	[ERROR]	incorrect	id
		2012-02-03	19:25:27	SampleClass4	[ERROR]	incorrect	id

	> [AZURE.NOTE]Для более длинных запросов HiveQL вы можете использовать командлет Azure PowerShell **Here-Strings** или файлы сценариев HiveQL. В приведенном ниже отрывке кода показано, как использовать командлет **Invoke-Hive** для запуска файла сценария HiveQL. Файл сценария HiveQL необходимо передать в wasb://.
	>
	> `Invoke-AzureRmHDInsightHiveJob -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
	>
	> Дополнительную информацию о командлете **Here-Strings** см. в разделе <a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">Использование Windows PowerShell Here-Strings</a>.

##Устранение неполадок

Если данные не возвращаются по завершении задания, возможно, во время обработки произошла ошибка. Чтобы просмотреть информацию об ошибке для данного задания, добавьте следующий текст в конце файла **hivejob.ps1**, сохраните его, а затем запустите снова.

	# Print the output of the Hive job.
	Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Будет возвращена информация, которая записывается в STDERR на сервере при запуске задания и может помочь определить причину сбоя задания.

##Сводка

Как можно видеть, Azure PowerShell позволяет с легкостью выполнять запросы Hive в кластере HDInsight, отслеживать состояние задания и получать выходные данные.

##Дальнейшие действия

Общая информация о Hive в HDInsight:

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight:

* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)

* [Использование MapReduce с Hadoop в HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=Nov15_HO2-->