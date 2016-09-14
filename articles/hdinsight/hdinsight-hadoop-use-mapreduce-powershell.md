<properties
   pageTitle="Использование MapReduce и PowerShell с Hadoop | Microsoft Azure"
   description="Информация об использовании PowerShell для удаленного выполнения заданий MapReduce с помощью Hadoop в HDInsight."
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
   ms.date="08/29/2016"
   ms.author="larryfr"/>

# Выполнение заданий MapReduce с помощью PowerShell с использованием Hadoop в HDInsight

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

В этом документе приведен пример использования Azure PowerShell для выполнения задания MapReduce в Hadoop на кластере HDInsight.

##<a id="prereq"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее.

- **Кластер Azure HDInsight (Hadoop в HDInsight) (на платформе Windows или Linux)**

- **Рабочая станция с Azure PowerShell.**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

##<a id="powershell"></a>Выполнение задания MapReduce с помощью Azure PowerShell

Azure PowerShell предоставляет *командлеты*, позволяющие удаленно запускать задания MapReduce в HDInsight. Внутренне это достигается с помощью выполнения вызовов REST для [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (прежнее название — Templeton) на кластере HDInsight.

При выполнении заданий MapReduce в удаленном кластере HDInsight используются следующие командлеты:

* **Login-AzureRmAccount** — выполняет аутентификацию Azure PowerShell для подписки Azure.

* **New-AzureRmHDInsightMapReduceJobDefinition** — создает новое *определение задания*, используя заданную информацию MapReduce.

* **Start-AzureRmHDInsightJob** — отправляет определение задания в HDInsight, запускает задание и возвращает объект-*задание*, который можно использовать для проверки состояния задания.

* **Wait-AzureRmHDInsightJob** — использует объект-задание для проверки состояния задания. Он ожидает завершения задания или превышения времени ожидания.

* **Get-AzureRmHDInsightJobOutput** — используется для получения выходных данных задания.

Следующие шаги показывают, как использовать эти командлеты для выполнения задания в кластере HDInsight:

1. С помощью редактора сохраните следующий код как **mapreducejob.ps1**. Параметр **CLUSTERNAME** необходимо заменить именем кластера HDInsight.

        #Specify the values
        $clusterName = "CLUSTERNAME"
                
        # Login to your Azure subscription
        # Is there an active Azure subscription?
        $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Login-AzureRmAccount
        }

        #Get HTTPS/Admin credentials for submitting the job later
        $creds = Get-Credential
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value

        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        #Define the MapReduce job
        #NOTE: If using an HDInsight 2.0 cluster, use hadoop-examples.jar instead.
        # -JarFile = the JAR containing the MapReduce application
        # -ClassName = the class of the application
        # -Arguments = The input file, and the output directory
        $wordCountJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
            -JarFile "wasbs:///example/jars/hadoop-mapreduce-examples.jar" `
            -ClassName "wordcount" `
            -Arguments `
                "wasbs:///example/data/gutenberg/davinci.txt", `
                "wasbs:///example/data/WordCountOutput"

        #Submit the job to the cluster
        Write-Host "Start the MapReduce job..." -ForegroundColor Green
        $wordCountJob = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $wordCountJobDefinition `
            -HttpCredential $creds

        #Wait for the job to complete
        Write-Host "Wait for the job to complete..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $wordCountJob.JobId `
            -HttpCredential $creds
        # Download the output
        Get-AzureStorageBlobContent `
            -Blob 'example/data/WordCountOutput/part-r-00000' `
            -Container $container `
            -Destination output.txt `
            -Context $context
        # Print the output
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $wordCountJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds
            
2. Откройте новую командную строку **Azure PowerShell**. Перейдите к расположению файла **mapreducejob.ps1**, а затем используйте следующую команду для запуска сценария:

		.\mapreducejob.ps1
    
    При запуске сценария вам может быть предложено аутентифицироваться в подписке Azure. Также вам будет предложено ввести сведения HTTPS/имя и пароль учетной записи администратора для кластера HDInsight.

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

	> [AZURE.NOTE] Если значение **ExitCode** не равно 0, см. раздел [Устранение неполадок](#troubleshooting).

    В этом примере скачанные файлы будут храниться в файле **output.txt** в каталоге, из которого запускается сценарий.

###Просмотр выходных данных

Откройте файл **output.txt** в текстовом редакторе, чтобы просмотреть слова и статистику, полученные при выполнении задания.

> [AZURE.NOTE] Выходные файлы задания MapReduce являются неизменяемыми. Поэтому при повторном выполнении этого примера потребуется изменить имя выходного файла.

##<a id="troubleshooting"></a>Устранение неполадок

Если данные не возвращаются по завершении задания, возможно, во время обработки произошла ошибка. Чтобы просмотреть информацию об ошибке для данного задания, добавьте следующую команду в конец файла **mapreducejob.ps1**, сохраните его, а затем запустите снова.

	# Print the output of the WordCount job.
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $wordCountJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Будет возвращена информация, которая записывается в STDERR на сервере при запуске задания и может помочь определить причину сбоя задания.

##<a id="summary"></a>Сводка

Как можно видеть, Azure PowerShell позволяет с легкостью выполнять задания MapReduce в кластере HDInsight, отслеживать состояние задания и получать выходные данные.

##<a id="nextsteps"></a>Дальнейшие действия

Общая информация о заданиях MapReduce в HDInsight:

* [Использование MapReduce в Hadoop в HDInsight](hdinsight-use-mapreduce.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight:

* [Использование Hive с Hadoop в HDInsight](hdinsight-use-hive.md)

* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)

<!---HONumber=AcomDC_0831_2016-->