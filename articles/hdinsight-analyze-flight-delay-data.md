<properties 
	pageTitle="Анализ данных о задержке рейсов с помощью Hadoop в HDInsight | Microsoft Azure" 
	description="Узнайте, как использовать один скрипт Windows PowerShell для подготовки кластера HDInsight, выполнения задания Hive, выполнения задания Sqoop и удаления кластера." 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="jgao"/>

#Анализ данных о задержке рейсов с помощью Hadoop в HDInsight

Hive предоставляет средства для выполнения заданий Hadoop MapReduce с помощью языка сценариев, аналогичного SQL, под названием *[HiveQL][hadoop-hiveql]*, который применяется для обобщения данных, создания запросов и анализа больших объемов данных.

Одним из самых существенных преимуществ Azure HDInsight является разделение хранилища данных и вычислительных ресурсов. HDInsight использует для хранения данных хранилище BLOB-объектов Azure. Стандартный процесс работы MapReduce можно разделить на 3 части:

1. **Хранение данных в хранилище больших двоичных объектов Azure.** Этот процесс может носить постоянный характер. Например, данные о погодных условиях, данные, получаемые от датчиков, журналы учета сетевой активности и, как в настоящем случае, данные о задержке авиарейсов сохраняются в хранилище больших двоичных объектов Azure.
2. **Выполнение заданий.** Когда наступает время обработки данных, вы запускаете скрипт Windows PowerShell (или клиентское приложение) для подготовки кластера HDInsight, выполняете задания и удаляете этот кластер. Данные, используемые заданиями, размещаются в хранилище BLOB-объектов Azure. Выходные данные сохраняются даже после удаления кластера. Такой подход позволяет платить только за использованные ресурсы. 
3. **Извлечение выходных данных из хранилища больших двоичных объектов Azure** или, как в данном случае, их экспорт в базу данных SQL Azure.

На следующей схеме наглядно демонстрируется такой сценарий, а также приводится структура этого учебника.

![HDI.FlightDelays.flow][img-hdi-flightdelays-flow]

**Примечание**. Номера на схеме соответствуют названиям разделов.

В основной части учебника рассматривается, как использовать один скрипт Windows PowerShell, чтобы выполнить следующие задачи.

- Подготовка кластера HDInsight.
- Выполнение задания Hive в кластере для вычисления средних задержек рейсов в аэропортах. Данные о задержке рейсов хранятся в учетной записи хранилища больших двоичных объектов Azure. 
- Выполнение задания Sqoop для экспорта выходных данных задания Hive в базу данных SQL Azure.
- Удаление кластера HDInsight. 

В приложениях вы можете найти инструкции по отправке данных о задержке рейсов, созданию и отправке строки запроса Hive и подготовке базы данных SQL Azure для задания Sqoop.


##<a id="prerequisite"></a>Предварительные требования

Перед началом работы с этим учебником необходимо иметь следующее:

* Рабочая станция, на которой установлена и настроена среда Azure PowerShell. Инструкции см. в разделе [Установка и настройка Azure PowerShell][powershell-install-configure].
* Подписка Azure. Дополнительные сведения о получении подписки см. в разделах [Варианты приобретения][azure-purchase-options], [Предложения для участников][azure-member-offers] или [Бесплатное пробное использование][azure-free-trial].

###Общие сведения о хранилище HDInsight

Кластеры Hadoop в HDInsight используют для хранения данных хранилища BLOB-объектов Azure. Дополнительные сведения см. в разделе [Использование хранилища больших двоичных объектов Azure с HDInsight][hdinsight-storage].

В процессе подготовки кластера контейнер больших двоичных объектов учетной записи хранения Azure назначается в качестве файловой системы по умолчанию, как в HDFS. Эта учетная запись хранения называется *учетной записью хранения по умолчанию*, а контейнер больших двоичных объектов называется *контейнером больших двоичных объектов по умолчанию* или просто *контейнером по умолчанию*. Учетная запись хранения по умолчанию должна находиться в том же центре обработки данных, что и кластер HDInsight. При удалении кластера HDInsight не происходит удаление контейнера по умолчанию учетной записи хранения по умолчанию.

В процессе подготовки помимо учетной записи хранения по умолчанию к кластеру HDInsight могут быть привязаны другие учетные записи хранения Azure. Привязка — это добавление учетной записи хранения и ключа учетной записи хранения в файл конфигурации, чтобы кластер мог получить доступ к этой учетной записи хранения во время выполнения. Инструкции по добавлению дополнительных учетных записей хранения см. в разделе [Подготовка кластеров Hadoop в HDInsight][hdinsight-provision].

Синтаксис хранилища больших двоичных объектов Azure:

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

>[AZURE.NOTE]Путь к хранилищу больших двоичных объектов является виртуальным путем. Дополнительные сведения см. в разделе [Использование хранилища больших двоичных объектов Azure с HDInsight][hdinsight-storage].

Для файлов, хранящихся в контейнере по умолчанию, доступ из HDInsight может осуществляться с помощью любого из следующих URI (в качестве примера используется файл flightdelays.hql).

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/flightdelays/flightdelays.hql
	wasb:///tutorials/flightdelays/flightdelays.hql
	/tutorials/flightdelays/flightdelays.hql

Для доступа к этому файлу непосредственно из учетной записи хранения используется следующее имя большого двоичного объекта:

	tutorials/flightdelays/flightdelays.hql

Обратите внимание, что в начале имени BLOB-объекта символ "/" отсутствует.

**Файлы, используемые в этом учебнике**

В этом учебнике используются данные о текущей работе рейсов авиакомпании, полученные из [бюро транспортной статистики при администрации по исследованиям и инновационным технологиям][rita-website] (RITA). Эти данные были загружены в контейнер службы хранения BLOB-объектов Azure с правами доступа "Общедоступный BLOB". Так как данный контейнер больших двоичных объектов является общедоступным, вам не требуется привязывать эту учетную запись хранения к кластеру HDInsight, выполняющему скрипт Hive. Скрипт HiveQL загружается в тот же самый контейнер BLOB-объектов. Сведения о получении или отправке данных в собственную учетную запись хранения и о создании и отправке файла скрипта HiveQL см. в [приложении А](#appendix-a) и [приложении Б](#appendix-b).

В следующей таблице перечислены файлы, используемые в этом учебнике:

<table border="1">
<tr><th>Файлы</th><th>Описание</th></tr>
<tr><td>wasb://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql</td><td>Сценарий HiveQL, используемый для запуска соответствующего задания Hive. Этот сценарий был загружен в учетную запись хранилища больших двоичных объектов Azure с общими правами доступа. В <a href="#appendix-b">приложении Б</a> содержатся инструкции по подготовке и отправке этого файла в вашу учетную запись хранилища больших двоичных объектов Azure.</td></tr>
<tr><td>wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data</td><td>Входные данные для задания Hive. Эти данные были отправлены в учетную запись хранилища больших двоичных объектов Azure с общим доступом. В <a href="#appendix-a">приложении А</a> содержатся инструкции по получению данных и их отправке в вашу учетную запись хранилища больших двоичных объектов Azure.</td></tr>
<tr><td>\tutorials\flightdelays\output</td><td>Выходной путь для задания Hive. Для хранения результирующих данных используется контейнер по умолчанию.</td></tr>
<tr><td>\tutorials\flightdelays\jobstatus</td><td>Папка состояния задания Hive в контейнере по умолчанию.</td></tr>
</table>



###Общие сведения о внутренней и внешней таблицах Hive

Есть несколько вещей, которые нужно знать о внутренней и внешней таблицах Hive.

- Команда **CREATE TABLE** создает внутреннюю таблицу. Файл данных должен находиться в контейнере по умолчанию.
- Команда **CREATE TABLE** перемещает файл данных в папку /hive/warehouse/<TableName>.
- Команда **CREATE EXTERNAL TABLE** создает внешнюю таблицу. Файл данных может находиться за пределами контейнера по умолчанию.
- Команда **CREATE EXTERNAL TABLE** не перемещает файл данных.
- Команда **CREATE EXTERNAL TABLE** не разрешает наличие каких-либо папок в LOCATION. Именно по этой причине в учебнике создается копия файла sample.log.

Дополнительные сведения см. в разделе [HDInsight: введение во внутренние и внешние таблицы Hive][cindygross-hive-tables].

> [AZURE.NOTE]Одна из инструкций HiveQL создает внешнюю таблицу Hive. Внешняя таблица Hive хранит файл данных в исходном расположении. Внутренняя таблица Hive перемещает файл данных в hive\\warehouse. Для внутренней таблицы Hive требуется, чтобы файл данных находился в контейнере по умолчанию. Для данных, которые хранятся не в контейнере больших двоичных объектов по умолчанию, необходимо использовать внешнюю таблицу Hive.









##Подготовка кластера HDInsight и выполнение заданий Hive и Sqoop 

Hadoop MapReduce представляет из себя пакетную обработку данных. Наиболее эффективным способом выполнения заданий Hive является подготовка кластера для конкретного задания и удаление этого задания после его выполнения. Приводимый далее скрипт позволяет полностью реализовать этот процесс. Дополнительные сведения о подготовке кластера HDInsight и выполнении заданий Hive см. в разделах [Подготовка кластеров Hadoop в HDInsight][hdinsight-provision] и [Использование Hive с HDInsight][hdinsight-use-hive].

**Выполнение запросов Hive с помощью Windows PowerShell**

1. Создайте базу данных SQL Azure и таблицу для выходных данных задания Sqoop согласно указаниям в [приложении В](#appendix-c).
2. Задайте следующие параметры.

	<table border="1">
<tr><th>Имя переменной</th><th>Примечания</th></tr>
<tr><td>$hdinsightClusterName</td><td>Имя кластера HDInsight. Если кластер не существует, сценарий создаст его с заданным именем.</td></tr>
<tr><td>$storageAccountName</td><td>Учетная запись хранения Azure, которая будет использоваться как учетная запись по умолчанию. Это значение необходимо, только если скрипт должен создать кластер HDInsight. Не указывайте это значение, если для переменной $hdinsightClusterName указано имя существующего кластера HDInsight. Если учетная запись хранения с указанным значением не существует, сценарий создаст ее с заданным именем.</td></tr>
<tr><td>$blobContainerName</td><td>Контейнер больших двоичных объектов, который будет использоваться для файловой системы по умолчанию. Если вы не укажите значение этой переменной, будет использоваться значение переменной $hdinsightClusterName. </td></tr>
<tr><td>$sqlDatabaseServerName</td><td>Имя сервера базы данных SQL Azure. Это должен быть существующий сервер. Сведения о его создании см. в <a href="#appendix-c">приложении В</a>.</td></tr>
<tr><td>$sqlDatabaseUsername</td><td>Имя для входа на сервер базы данных SQL Azure.</td></tr>
<tr><td>$sqlDatabasePassword</td><td>Пароль для входа на сервер базы данных SQL Azure.</td></tr>
<tr><td>$sqlDatabaseName</td><td>База данных SQL, куда Sqoop будет экспортировать данные. По умолчанию используется имя HDISqoop. Имя таблицы для выходных данных задания Sqoop&#160;— AvgDelays. </td></tr>
</table>
3. Откройте интегрированную среду сценариев (ISE) Windows PowerShell.
4. Скопируйте следующий скрипт и вставьте его в область скриптов:

		[CmdletBinding()]
		Param(
		
		    # HDInsight cluster variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the HDInsight cluster name. If the cluster doesn't exist, the script will create one.")]
		    [String]$hdinsightClusterName,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
		    [AllowEmptyString()]
		    [String]$storageAccountName,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
		    [AllowEmptyString()]
		    [String]$blobContainerName,
		
		    #SQL database server variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database Server Name where to export data.")]
		    [String]$sqlDatabaseServerName,  # specify the Azure SQL database server name where you want to export data to.
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database login username.")]
		    [String]$sqlDatabaseUsername,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database login user password.")]
		    [String]$sqlDatabasePassword,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the database name where data will be exported to.")]
		    [String]$sqlDatabaseName  # the default value is HDISqoop
		)
		
		# Treat all errors as terminating
		$ErrorActionPreference = "Stop"
		
		#region - HDInsight cluster variables
		[int]$clusterSize = 1                # One data node is sufficient for this tutorial
		[String]$location = "Central US"     # For better performance, choose a datacenter near you
		[String]$hadoopUserLogin = "admin"   # Use "admin" as the Hadoop login name
		[String]$hadoopUserpw = "Pass@word1" # Use "Pass@word1" as the Hadoop login password
		
		[Bool]$isNewCluster = $false      # Indicates whether a new HDInsight cluster is created by the script  
		                                  # If this variable is true, then the script can optionally delete the cluster after running the Hive and Sqoop jobs
		
		[Bool]$isNewStorageAccount = $false
		
		$storageAccountName = $storageAccountName.ToLower() # Storage account names must be between 3 and 24 characters in length and use numbers and lower-case letters only.
		#endregion
		
		#region - Hive job variables
		[String]$hqlScriptFile = "wasb://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql" # The HiveQL script is located in a public Blob container. Update this URI if you want to use your own script file.
		
		[String]$jobStatusFolder = "/tutorials/flightdelays/jobstatus" # The script saves both the output data and the job status file to the default container.
		                                                               # The output data path is set in the HiveQL file.
		
		#[String]$jobOutputBlobName = "tutorials/flightdelays/output/000000_0" # This is the output file of the Hive job. The path is set in the HiveQL script.
		#endregion
		
		#region - Sqoop job variables
		[String]$sqlDatabaseTableName = "AvgDelays" 
		[String]$sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseUserName@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"
		#endregion Constants and variables
		
		#region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow
		if (-not (Get-AzureAccount)){ Add-AzureAccount}
		#endregion
		
		#region - Validate user input, and provision HDInsight cluster if needed
		Write-Host "`nValidating user input ..." -ForegroundColor Green
		
		# Both the Azure SQL database server and database must exist
		if (-not (Get-AzureSqlDatabaseServer|Where-Object{$_.ServerName -eq $sqlDatabaseServerName})){
		    Write-host "The Azure SQL database server, $sqlDatabaseServerName doesn't exist." -ForegroundColor Red
		    Exit
		}
		else
		{
		    if (-not ((Get-AzureSqlDatabase -ServerName $sqlDatabaseServerName)|Where-Object{$_.Name -eq $sqlDatabaseName})){
		        Write-host "The Azure SQL database, $sqlDatabaseName doesn't exist." -ForegroundColor Red
		        Exit
		    }
		}
		
		if (Test-AzureName -Service -Name $hdinsightClusterName)     # If it is an existing HDInsight cluster ...
		{
		    Write-Host "`tThe HDInsight cluster, $hdinsightClusterName, exists. This cluster will be used to run the Hive job." -ForegroundColor Cyan
		
		    #region - Retrieve the default Storage account/container names if the cluster exists
		    # The Hive job output will be stored in the default container. The 
		    # information is used to download a copy of the output file from 
		    # Blob storage to workstation for the validation purpose.
		    Write-Host "`nRetrieving the HDInsight cluster default storage account information ..." `
		                -ForegroundColor Green
		
		    $hdi = Get-AzureHDInsightCluster -Name $HDInsightClusterName
		
		    # Use the default Storage account and the default container even if the names are different from the user input
		    $storageAccountName = $hdi.DefaultStorageAccount.StorageAccountName `
		                            -replace ".blob.core.windows.net"
		    $blobContainerName = $hdi.DefaultStorageAccount.StorageContainerName
		
		    Write-Host "`tThe default storage account for the cluster is $storageAccountName." `
		                -ForegroundColor Cyan
		    Write-Host "`tThe default Blob container for the cluster is $blobContainerName." `
		                -ForegroundColor Cyan
		    #endregion
		}
		else     #If the cluster doesn't exist, a new one will be provisioned
		{
		    if ([string]::IsNullOrEmpty($storageAccountName))
		    {
		        Write-Host "You must provide a storage account name" -ForegroundColor Red
		        EXit           
		    }
		    else
		    {
		        # If the container name is not specified, use the cluster name as the container name
		        if ([string]::IsNullOrEmpty($blobContainerName))
		        {
		            $blobContainerName = $hdinsightClusterName
		        }
		        $blobContainerName = $blobContainerName.ToLower()
		
		        #region - Provision HDInsight cluster
		        # Create an Azure Storage account if it doesn't exist
		        if (-not (Get-AzureStorageAccount|Where-Object{$_.Label -eq $storageAccountName}))
		        {
		            Write-Host "`nCreating the Azure storage account, $storageAccountName ..." -ForegroundColor Green
		            if (-not (New-AzureStorageAccount -StorageAccountName $storageAccountName.ToLower() -Location $location)){
		                Write-Host "Error creating the storage account, $storageAccountName" -ForegroundColor Red
		                Exit
		            }
		            $isNewStorageAccount = $True
		        }
		
		        # Create a Blob container used as the default container
		        $storageAccountKey = get-azurestoragekey -StorageAccountName $storageAccountName | %{$_.Primary}
		        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		
		        if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
		        {
		            Write-Host "`nCreating the Azure Blob container, $blobContainerName ..." -ForegroundColor Green
		            if (-not (New-AzureStorageContainer -name $blobContainerName -Context $storageContext)){
		                Write-Host "Error creating the Blob container, $blobContainerName" -ForegroundColor Red
		                Exit
		            }
		        }
		
		        # Create a new HDInsight cluster
		        Write-Host "`nProvisioning the HDInsight cluster, $hdinsightClusterName ..." -ForegroundColor Green
		        Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow
		        $hadoopUserPassword = ConvertTo-SecureString -String $hadoopUserpw -AsPlainText -Force     
		        $credential = New-Object System.Management.Automation.PSCredential($hadoopUserLogin,$hadoopUserPassword)
		        if (-not $credential)
		        {
		            Write-Host "Error creating the PSCredential object" -ForegroundColor Red
		            Exit
		        }
		
		        if (-not (New-AzureHDInsightCluster -Name $hdinsightClusterName -Location $location -Credential $credential -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $blobContainerName -ClusterSizeInNodes $clusterSize)){
		            Write-Host "Error provisioning the cluster, $hdinsightClusterName." -ForegroundColor Red
		            Exit
		        }
		        Else
		        {
		            $isNewCluster = $True
		        }
		        #endregion
		    }
		}
		#endregion
		
		#region - Submit Hive job
		Write-Host "`nSubmitting the Hive job ..." -ForegroundColor Green
		Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow
		
		Use-AzureHDInsightCluster $HDInsightClusterName
		$response = Invoke-Hive –File $hqlScriptFile -StatusFolder $jobStatusFolder
		
		Write-Host "`nThe Hive job status" -ForegroundColor Cyan
		Write-Host "---------------------------------------------------------" -ForegroundColor Cyan
		write-Host $response
		Write-Host "---------------------------------------------------------" -ForegroundColor Cyan
		#endregion 
		
		#region - Run Sqoop job
		Write-Host "`nSubmitting the Sqoop job ..." -ForegroundColor Green
		Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow
		
		[String]$exportDir = "wasb://$blobContainerName@$storageAccountName.blob.core.windows.net/tutorials/flightdelays/output"
		
		
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "
		$sqoopJob = Start-AzureHDInsightJob -Cluster $hdinsightClusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
		
		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $hdinsightClusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $hdinsightClusterName -JobId $sqoopJob.JobId -StandardOutput
		#endregion
		
		#region - Delete the HDInsight cluster
		if ($isNewCluster -eq $True)
		{
		    $isDelete = Read-Host 'Do you want to delete the HDInsight Hadoop cluster ' $hdinsightClusterName '? (Y/N)'
		
		    if ($isDelete.ToLower() -eq "y")
		    {
		        Write-Host "`nDeleting the HDInsight cluster ..." -ForegroundColor Green
		        Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow
		        Remove-AzureHDInsightCluster -Name $hdinsightClusterName
		    }
		}
		#endregion
		
		#region - Delete the Storage account
		if ($isNewStorageAccount -eq $True)
		{
		    $isDelete = Read-Host 'Do you want to delete the Azure storage account ' $storageAccountName '? (Y/N)'
		
		    if ($isDelete.ToLower() -eq "y")
		    {
		        Write-Host "`nDeleting the Azure storage account ..." -ForegroundColor Green
		        Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow
		        Remove-AzureStorageAccount -StorageAccountName $storageAccountName
		    }
		}
		#endregion
		
		Write-Host "End of the PowerShell script" -ForegroundColor Green
		Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow

5. Нажмите клавишу **F5** для запуска скрипта. Результат должен быть аналогичен приведенному ниже:

	![HDI.FlightDelays.RunHiveJob.output][img-hdi-flightdelays-run-hive-job-output]
		
6. Подключитесь к базе данных SQL и просмотрите средние значения задержки рейсов по городу в таблице AvgDelays:

	![HDI.FlightDelays.AvgDelays.Dataset][image-hdi-flightdelays-avgdelays-dataset]



---
##<a id="appendix-a"></a>Приложение А. Отправка данных о задержке рейсов в хранилище больших двоичных объектов Azure
Для отправки файла данных и файлов скриптов HiveQL (см. [приложение Б](#appendix-b)) требуется некоторое планирование. Суть заключается в том, чтобы сохранить файлы данных и файл HiveQL до подготовки кластера HDInsight и запуска задания Hive. Существует два варианта.

- **Использование той же учетной записи хранения Azure, которая будет использоваться кластером HDInsight в качестве файловой системы по умолчанию.** Вам не нужно вносить никакие дополнительные изменения, поскольку кластер HDInsight будет иметь ключ доступа к учетной записи хранения.
- **Использование учетной записи хранения Azure, отличной от той, которая будет использоваться кластером HDInsight в качестве файловой системы по умолчанию.** В этом случае необходимо изменить относящуюся к подготовке часть скрипта Windows PowerShell, которую можно найти в разделе [Подготовка кластера HDInsight и выполнение заданий Hive и Sqoop](#runjob), чтобы включить эту учетную запись хранения как дополнительную учетную запись. Инструкции см. в разделе [Подготовка кластеров Hadoop в HDInsight][hdinsight-provision]. После этого кластеру HDInsight станет известен ключ доступа для этой учетной записи хранения.

>[AZURE.NOTE]Путь к хранилищу больших двоичных объектов для файла данных жестко закодирован в файле скрипта HiveQL. Его следует обновлять соответствующим образом.

**Загрузка данных о рейсах**

1. Перейдите в [Бюро транспортной статистики при администрации по исследованиям и инновационным технологиям][rita-website].
2. На странице выберите следующие значения:

	<table border="1">
<tr><th>Имя</th><th>Значение</th></tr>
<tr><td>Фильтр года</td><td>2013 </td></tr>
<tr><td>Период фильтра</td><td>Январь</td></tr>
<tr><td>Поля</td><td>*Year*, *FlightDate*, *UniqueCarrier*, *Carrier*, *FlightNum*, *OriginAirportID*, *Origin*, *OriginCityName*, *OriginState*, *DestAirportID*, *Dest*, *DestCityName*, *DestState*, *DepDelayMinutes*, *ArrDelay*, *ArrDelayMinutes*, *CarrierDelay*, *WeatherDelay*, *NASDelay*, *SecurityDelay*, *LateAircraftDelay* (очистите все остальные поля)</td></tr>
</table>

3. Щелкните элемент **Загрузить**.
4. Распакуйте файл в папку **C:\\Tutorials\\FlightDelays\\Data**. Каждый файл представляет собой CSV-файл и имеет размер около 60 ГБ.
5.	Переименуйте файл в название месяца, данные по которому он содержит. Например, файл, содержащий данные за январь, следует назвать *January.csv*.
6. Повторите шаги 2 и 5, чтобы загрузить файл для каждого из 12 месяцев 2013 года. Для выполнения руководства вам понадобится как минимум один файл.  

**Отправка данных о задержке рейсов в хранилище больших двоичных объектов Azure**

1. Задайте следующие параметры.

	<table border="1">
<tr><th>Имя переменной</th><th>Примечания</th></tr>
<tr><td>$storageAccountName</td><td>Учетная запись хранения Azure, в которую необходимо отправить данные.</td></tr>
<tr><td>$blobContainerName</td><td>Контейнер больших двоичных объектов, в который необходимо отправить данные.</td></tr>
</table>
2. Откройте интегрированную среду сценариев Azure PowerShell.
3. Добавьте следующий скрипт в область скриптов:

		[CmdletBinding()]
		Param(
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
		    [String]$storageAccountName,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
		    [String]$blobContainerName
		)
		
		#Region - Variables
		$localFolder = "C:\Tutorials\FlightDelays\Data"  # The source folder
		$destFolder = "tutorials/flightdelays/data"     #The blob name prefix for the files to be uploaded
		#EndRegion
		
		#Region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		if (-not (Get-AzureAccount)){ Add-AzureAccount}
		#EndRegion
		
		#Region - Validate user input
		# Validate the Storage account
		if (-not (Get-AzureStorageAccount|Where-Object{$_.Label -eq $storageAccountName}))
		{
		    Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
		    exit
		}
		
		# Validate the container
		$storageAccountKey = get-azurestoragekey -StorageAccountName $storageAccountName | %{$_.Primary}
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		
		if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
		{
		    Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
		    Exit
		}
		#EngRegion
		
		#Region - Copy the file from local workstation to Azure Blob storage  
		if (test-path -Path $localFolder)
		{
		    foreach ($item in Get-ChildItem -Path $localFolder){
		        $fileName = "$localFolder\$item"
		        $blobName = "$destFolder/$item"
		
		        Write-Host "Copying $fileName to $blobName" -ForegroundColor Green
		
		        Set-AzureStorageBlobContent -File $fileName -Container $blobContainerName -Blob $blobName -Context $storageContext
		    }
		}
		else
		{
		    Write-Host "The source folder on the workstation doesn't exist" -ForegroundColor Red
		}
		
		# List the uploaded files on HDInsight
		Get-AzureStorageBlob -Container $blobContainerName  -Context $storageContext -Prefix $destFolder
		#EndRegion

4. Нажмите клавишу **F5** для запуска скрипта.

Если вы решили использовать другой метод для отправки файлов, убедитесь, что путь к файлу имеет значение tutorials/flightdelays/data. Для доступа к файлам используется следующий синтаксис:

	wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelays/data

Путь tutorials/flightdelays/data является виртуальной папкой, созданной вами при отправке файлов. Убедитесь, что присутствует 12 файлов — по одному для каждого месяца.

>[AZURE.NOTE]Для возможности считывания данных из нового расположения необходимо обновить запрос Hive.

> Вы должны либо настроить разрешения для общего доступа к контейнеру, либо привязать учетную запись хранения к кластеру HDInsight. В противном случае строка запроса Hive не сможет получить доступ к файлам данных.

---
##<a id="appendix-b"></a>Приложение Б. Создание и загрузка скрипта HiveQL

С помощью Azure PowerShell можно одновременно запустить несколько инструкций HiveQL по одной за раз или упаковать инструкцию HiveQL в файл скрипта. В этом разделе показывается, как создавать скрипты HiveQL и загружать их в хранилище больших двоичных объектов Azure с помощью Azure PowerShell. Для Hive требуется, чтобы скрипты HiveQL хранились в хранилище больших двоичных объектов Azure.

Скрипт HiveQL выполнит следующее:

1. **Удаляет таблицу delays_raw**, если она уже существует.
2. **Создает внешнюю таблицу Hive delays_raw**, указывающую на расположение хранилища больших двоичных объектов с файлами данных о задержках рейсов. Этот запрос задает то, что поля разделяются с помощью "," и что строки завершаются с помощью "\\n". Это создает проблему при наличии запятых в значениях полей, так как Hive не отличает запятую, которая является разделителем поля, и запятую, которая является частью значения поля (что характерно для значений полей ORIGIN_CITY_NAME и DEST_CITY_NAME). Чтобы решить эту проблему, запрос создает столбцы TEMP для хранения данных, неправильно разбитых на столбцы.  
3. **Удаляет таблицу delays**, если она уже существует.
4. **Создает таблицу delays**. Это удобно для очистки данных перед дальнейшей обработкой. Этот запрос создает новую таблицу *delays* из таблицы delays_raw. Обратите внимание, что столбцы TEMP (как уже упоминалось ранее) не копируются, а функция **substring** используется для удаления кавычек из данных. 
5. **Вычисляет среднюю задержку из-за погоды и группирует результаты по названию города.** Он также будет выводить результаты в хранилище больших двоичных объектов. Обратите внимание, что запрос удаляет апострофы из данных и исключает строки, где **weather_delay** имеет значение null. Это необходимо, так как Sqoop, используемый в этом руководстве, не обрабатывает эти значения надлежащим образом по умолчанию.

Полный список команд HiveQL см. в разделе [Язык описания данных Hive][hadoop-hiveql]. Каждая команда HiveQL должна завершаться точкой с запятой.

**Создание файла скрипта HiveQL**

1. Задайте следующие параметры.

	<table border="1">
<tr><th>Имя переменной</th><th>Примечания</th></tr>
<tr><td>$storageAccountName</td><td>Учетная запись хранения Azure, в которую требуется отправить скрипт HiveQL.</td></tr>
<tr><td>$blobContainerName</td><td>Контейнер больших двоичных объектов, в который необходимо отправить сценарий HiveQL.</td></tr>
</table>
2. Откройте интегрированную среду сценариев Azure PowerShell.

3. Скопируйте следующий скрипт и вставьте его в область скриптов:

		[CmdletBinding()]
		Param(
		
		    # Azure Blob storage variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
		    [String]$storageAccountName,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
		    [String]$blobContainerName
		
		)
		
		#region - Define variables
		# Treat all errors as terminating
		$ErrorActionPreference = "Stop"
		
		# The HiveQL script file is exported as this file before it's uploaded to Blob storage
		$hqlLocalFileName = "C:\tutorials\flightdelays\flightdelays.hql" 
		
		# The HiveQL script file will be uploaded to Blob storage as this blob name
		$hqlBlobName = "tutorials/flightdelays/flightdelays.hql" 
		
		# These two constants are used by the HiveQL script file
		#$srcDataFolder = "tutorials/flightdelays/data" 
		$dstDataFolder = "/tutorials/flightdelays/output"
		#endregion
		
		#region - Validate the file and file path
		
		# Check if a file with the same file name already exists on the workstation
		Write-Host "`nvalidating the folder structure on the workstation for saving the HQL script file ..."  -ForegroundColor Green
		if (test-path $hqlLocalFileName){
		
		    $isDelete = Read-Host 'The file, ' $hqlLocalFileName ', exists.  Do you want to overwirte it? (Y/N)'
		
		    if ($isDelete.ToLower() -ne "y")
		    {
		        Exit
		    }
		}
		
		# Create the folder if it doesn't exist
		$folder = split-path $hqlLocalFileName
		if (-not (test-path $folder))
		{
		    Write-Host "`nCreating folder, $folder ..." -ForegroundColor Green
		
		    new-item $folder -ItemType directory  
		}
		#end region
		
		#region - Add the Azure account 
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		$azureAccounts= Get-AzureAccount
		if (! $azureAccounts)
		{
		    Add-AzureAccount
		}
		#endregion
		
		#region - Write the Hive script into a local file
		Write-Host "`nWriting the Hive script into a file on your workstation ..." `
		            -ForegroundColor Green
		
		$hqlDropDelaysRaw = "DROP TABLE delays_raw;"
		
		$hqlCreateDelaysRaw = "CREATE EXTERNAL TABLE delays_raw (" +
		        "YEAR string, " +
		        "FL_DATE string, " +
		        "UNIQUE_CARRIER string, " +
		        "CARRIER string, " +
		        "FL_NUM string, " +
		        "ORIGIN_AIRPORT_ID string, " +
		        "ORIGIN string, " +
		        "ORIGIN_CITY_NAME string, " +
		        "ORIGIN_CITY_NAME_TEMP string, " +
		        "ORIGIN_STATE_ABR string, " +
		        "DEST_AIRPORT_ID string, " +
		        "DEST string, " +
		        "DEST_CITY_NAME string, " +
		        "DEST_CITY_NAME_TEMP string, " +
		        "DEST_STATE_ABR string, " +
		        "DEP_DELAY_NEW float, " +
		        "ARR_DELAY_NEW float, " +
		        "CARRIER_DELAY float, " +
		        "WEATHER_DELAY float, " +
		        "NAS_DELAY float, " +
		        "SECURITY_DELAY float, " +
		        "LATE_AIRCRAFT_DELAY float) " +
		    "ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' " +
		    "LINES TERMINATED BY '\n' " +
		    "STORED AS TEXTFILE " +
		    "LOCATION 'wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data';" 
		
		$hqlDropDelays = "DROP TABLE delays;"
		
		$hqlCreateDelays = "CREATE TABLE delays AS " +
		    "SELECT YEAR AS year, " +
		        "FL_DATE AS flight_date, " +
		        "substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier, " +
		        "substring(CARRIER, 2, length(CARRIER) -1) AS carrier, " +
		        "substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num, " +
		        "ORIGIN_AIRPORT_ID AS origin_airport_id, " +
		        "substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code, " +
		        "substring(ORIGIN_CITY_NAME, 2) AS origin_city_name, " +
		        "substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr, " +
		        "DEST_AIRPORT_ID AS dest_airport_id, " +
		        "substring(DEST, 2, length(DEST) -1) AS dest_airport_code, " +
		        "substring(DEST_CITY_NAME,2) AS dest_city_name, " +
		        "substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr, " +
		        "DEP_DELAY_NEW AS dep_delay_new, " +
		        "ARR_DELAY_NEW AS arr_delay_new, " +
		        "CARRIER_DELAY AS carrier_delay, " +
		        "WEATHER_DELAY AS weather_delay, " +
		        "NAS_DELAY AS nas_delay, " +
		        "SECURITY_DELAY AS security_delay, " +
		        "LATE_AIRCRAFT_DELAY AS late_aircraft_delay " +
		    "FROM delays_raw;" 
		
		$hqlInsertLocal = "INSERT OVERWRITE DIRECTORY '$dstDataFolder' " +
		    "SELECT regexp_replace(origin_city_name, '''', ''), " +
		        "avg(weather_delay) " +
		    "FROM delays " +
		    "WHERE weather_delay IS NOT NULL " +
		    "GROUP BY origin_city_name;"
		
		$hqlScript = $hqlDropDelaysRaw + $hqlCreateDelaysRaw + $hqlDropDelays + $hqlCreateDelays + $hqlInsertLocal
		
		$hqlScript | Out-File $hqlLocalFileName -Encoding ascii -Force 
		#endregion
		
		#region - Upload the Hive script to the default Blob container
		Write-Host "`nUploading the Hive script to the default Blob container ..." -ForegroundColor Green
		
		# Create a storage context object
		$storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		
		# Upload the file from local workstation to Blob storage
		Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $blobContainerName -Blob $hqlBlobName -Context $destContext 
		#endregion
		
		Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

	В данном сценарии используются три следующие переменные:

	- **$hqlLocalFileName** — этот скрипт сохраняет файл скрипта HiveQL локально перед его отправкой в хранилище больших двоичных объектов. Это — имя файла. По умолчанию используется значение <u>C:\\tutorials\\flightdelays\\flightdelays.hql</u>.
	- **$hqlBlobName** — это имя большого двоичного объекта файла скрипта HiveQL, используемое в хранилище больших двоичных объектов Azure. По умолчанию используется значение tutorials/flightdelays/flightdelays.hql. Так как этот файл записывается непосредственно в хранилище BLOB-объектов Azure, в начале имени BLOB-объекта отсутствует символ "/". Если требуется получить доступ к этому файлу из хранилища больших двоичных объектов, необходимо добавить символ «/» в начало этого имени файла.
	- **$srcDataFolder** и **$dstDataFolder** — = tutorials/flightdelays/data и tutorials/flightdelays/output


---
##<a id="appendix-c"></a>Приложение В. Подготовка базы данных SQL Azure для выходных данных задания Sqoop
**Подготовка базы данных SQL (объединение со скриптом Sqoop)**

1. Задайте следующие параметры.

	<table border="1">
<tr><th>Имя переменной</th><th>Примечания</th></tr>
<tr><td>$sqlDatabaseServerName</td><td>Имя сервера базы данных SQL Azure. Чтобы создать новый сервер, не указывайте ничего.</td></tr>
<tr><td>$sqlDatabaseUsername</td><td>Имя для входа на сервер базы данных SQL Azure. Если значение переменной $sqlDatabaseServerName представляет собой существующий сервер, для выполнения аутентификации на сервере используются имя пользователя и пароль для входа. В противном случае они используются для создания нового сервера.</td></tr>
<tr><td>$sqlDatabasePassword</td><td>Пароль для входа на сервер базы данных SQL Azure.</td></tr>
<tr><td>$sqlDatabaseLocation</td><td>Это значение используется только при создании нового сервера базы данных SQL Azure.</td></tr>
<tr><td>$sqlDatabaseName</td><td>База данных SQL, которая используется для создания таблицы AvgDelays для задания Sqoop. Если это значение не задано, то будет создана база данных с именем HDISqoop. Имя таблицы для выходных данных задания Sqoop&#160;— AvgDelays. </td></tr>
</table>
2. Откройте интегрированную среду сценариев Azure PowerShell. 
3. Скопируйте следующий скрипт и вставьте его в область скриптов:
	
		[CmdletBinding()]
		Param(
		
		    # SQL database server variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database Server Name to use an existing one. Enter nothing to create a new one.")]
		    [AllowEmptyString()]
		    [String]$sqlDatabaseServer,  # Specify the Azure SQL database server name if you have one created. Otherwise use "".
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database admin user.")]
		    [String]$sqlDatabaseUsername,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database admin user password.")]
		    [String]$sqlDatabasePassword,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the region to create the Database in.")]
		    [AllowEmptyString()]
		    [String]$sqlDatabaseLocation,   #For example, West US.
		
		    # SQL database variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the database name if you have created one. Enter nothing to create one.")]
		    [AllowEmptyString()]
		    [String]$sqlDatabaseName # specify the database name if you have one created. Otherwise use "" to have the script create one for you.
		)
		
		# Treat all errors as terminating
		$ErrorActionPreference = "Stop"
		
		#region - Constants and variables
		
		# IP address REST service used for retrieving external IP address and creating firewall rules
		[String]$ipAddressRestService = "http://bot.whatismyipaddress.com"
		[String]$fireWallRuleName = "FlightDelay"
		
		# SQL database variables
		[String]$sqlDatabaseMaxSizeGB = 10
		
		#SQL query string for creating AvgDelays table
		[String]$sqlDatabaseTableName = "AvgDelays"
		[String]$sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
		            [origin_city_name] [nvarchar](50) NOT NULL,
		            [weather_delay] float,
		        CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED   
		        (
		            [origin_city_name] ASC
		        )
		        )"
		#endregion

		#region - Add the Azure account 
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		$azureAccounts= Get-AzureAccount
		if (! $azureAccounts)
		{
		Add-AzureAccount
		}
		#endregion
		
		#region - Create and validate Azure SQL database server
		if ([string]::IsNullOrEmpty($sqlDatabaseServer))
		{
			Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
			$sqlDatabaseServer = (New-AzureSqlDatabaseServer -AdministratorLogin $sqlDatabaseUsername -AdministratorLoginPassword $sqlDatabasePassword -Location $sqlDatabaseLocation).ServerName
		    Write-Host "`tThe new SQL database server name is $sqlDatabaseServer." -ForegroundColor Cyan
		
		    Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
		    $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
		    New-AzureSqlDatabaseServerFirewallRule -ServerName $sqlDatabaseServer -RuleName "$fireWallRuleName-workstation" -StartIpAddress $workstationIPAddress -EndIpAddress $workstationIPAddress	
		    New-AzureSqlDatabaseServerFirewallRule -ServerName $sqlDatabaseServer -RuleName "$fireWallRuleName-Azureservices" -AllowAllAzureServices 
		}
		else
		{
		    $dbServer = Get-AzureSqlDatabaseServer -ServerName $sqlDatabaseServer
		    if (! $dbServer)
		    {
		        throw "The Azure SQL database server, $sqlDatabaseServer, doesn't exist!" 
		    }
		    else
		    {
			    Write-Host "`nUse an existing SQL Database server, $sqlDatabaseServer" -ForegroundColor Green
		    }
		}
		#endregion
		
		#region - Create and validate Azure SQL database
		if ([string]::IsNullOrEmpty($sqlDatabaseName))
		{
			Write-Host "`nCreating SQL Database, HDISqoop ..."  -ForegroundColor Green
		
			$sqlDatabaseName = "HDISqoop"
			$sqlDatabaseServerCredential = new-object System.Management.Automation.PSCredential($sqlDatabaseUsername, ($sqlDatabasePassword  | ConvertTo-SecureString -asPlainText -Force)) 
		
		    $sqlDatabaseServerConnectionContext = New-AzureSqlDatabaseServerContext -ServerName $sqlDatabaseServer -Credential $sqlDatabaseServerCredential 
			
			$sqlDatabase = New-AzureSqlDatabase -ConnectionContext $sqlDatabaseServerConnectionContext -DatabaseName $sqlDatabaseName -MaxSizeGB $sqlDatabaseMaxSizeGB
		}
		else
		{
		    $db = Get-AzureSqlDatabase -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName
		    if (! $db)
		    {
		        throw "The Azure SQL database server, $sqlDatabaseServer, doesn't exist!" 
		    }
		    else
		    {
			    Write-Host "`nUse an existing SQL Database, $sqlDatabaseName" -ForegroundColor Green
		    }
		}
		#endregion
			
		#region -  Execute an SQL command to create the AvgDelays table
			
		Write-Host "`nCreating SQL Database table ..."  -ForegroundColor Green
		$conn = New-Object System.Data.SqlClient.SqlConnection
		$conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseUsername;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
		$conn.open()
		$cmd = New-Object System.Data.SqlClient.SqlCommand
		$cmd.connection = $conn
		$cmd.commandtext = $sqlCreateAvgDelaysTable
		$cmd.executenonquery()
			
		$conn.close()
		
		Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

	>[AZURE.NOTE]Этот сценарий использует службу REST (http://bot.whatismyipaddress.com) для получения вашего внешнего IP-адреса. Этот IP-адрес используется для создания правила брандмауэра для сервера базы данных SQL.

	В данном сценарии используются следующие переменные:

	- **$ipAddressRestService**: значение по умолчанию — http://bot.whatismyipaddress.com. Это общедоступный IP-адрес службы REST для получения внешнего IP-адреса. При необходимости можно использовать и другие службы. Внешний IP-адрес, полученный с помощью данной службы, используется при создании правила брандмауэра для сервера базы данных SQL Azure, поэтому доступ к этой базе данных можно получить с рабочей станции (с помощью скрипта Windows PowerShell).
	- **$fireWallRuleName** — это имя правила брандмауэра для сервера базы данных SQL Azure. Имя по умолчанию: <u>FlightDelay</u>. При необходимости можно использовать другое имя.
	- **$sqlDatabaseMaxSizeGB** — это значение используется только при создании нового сервера базы данных SQL Azure. Значение по умолчанию — 10 ГБ. Размера 10ГБ достаточно для примеров из данного учебника.
	- **$sqlDatabaseName** — это значение используется только при создании новой базы данных SQL Azure. Значение по умолчанию — HDISqoop. Если вы изменяете это имя, то должны соответствующим образом обновить скрипт Windows PowerShell Sqoop. 

4. Нажмите клавишу **F5** для запуска скрипта.
5. Проверка выходных данных скрипта. Позволяет убедиться, что скрипт был выполнен без ошибок.	

##<a id="nextsteps"></a>Дальнейшие действия
Теперь вы знаете, как отправить файл в хранилище больших двоичных объектов, как заполнить таблицу Hive, используя данные из хранилища больших двоичных объектов, как выполнять запросы Hive и как использовать Sqoop для экспорта данных из HDFS в базу данных SQL Azure. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Приступая к работе с HDInsight][hdinsight-get-started]
* [Использование Hive с HDInsight][hdinsight-use-hive]
* [Использование Oozie с HDInsight][hdinsight-use-oozie]
* [Использование Sqoop с HDInsight][hdinsight-use-sqoop]
* [Использование Pig с HDInsight][hdinsight-use-pig]
* [Разработка программ MapReduce на Java для HDInsight][hdinsight-develop-mapreduce]
* [Разработка программ потоковой передачи Hadoop на C# для HDInsight][hdinsight-develop-streaming]



[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
[powershell-install-configure]: install-configure-powershell.md

[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-streaming]: hdinsight-hadoop-develop-deploy-streaming-jobs.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
[hadoop-shell-commands]: http://hadoop.apache.org/docs/r0.18.3/hdfs_shell.html

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

[image-hdi-flightdelays-avgdelays-dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
[img-hdi-flightdelays-run-hive-job-output]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.RunHiveJob.Output.png
[img-hdi-flightdelays-flow]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.Flow.png


<!--HONumber=54-->