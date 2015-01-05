<properties urlDisplayName="Analyze flight delay data with Hadoop in HDInsight" pageTitle="Анализ данных о задержке рейсов с помощью Hadoop в HDInsight | Azure" metaKeywords="" description="Learn how to  use one PowerShell script to provision HDInsight cluster, run Hive job, run Sqool job, and delete the cluster." metaCanonical="" services="hdinsight" documentationCenter="" title="Analyze flight delay data using Hadoop in HDInsight " authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/04/2014" ms.author="jgao" />

#Анализ данных о задержке рейсов с помощью Hadoop в HDInsight

Hive предоставляет средства для выполнения заданий Hadoop MapReduce с помощью языка сценариев, аналогичного SQL, под названием [HiveQL][hadoop-hiveql], который применяется для обобщения данных, создания запросов и анализа больших объемов данных. 

Одним из самых существенных преимуществ HDInsight является разделение хранилища данных и вычислительных ресурсов. HDInsight использует для хранения данных хранилище BLOB-объектов Azure. Стандартный процесс работы MapReduce можно разделить на 3 части:

1. **Хранение данных в хранилище больших двоичных объектов Azure.**  Этот процесс может носить постоянный характер. Например, данные о погодных условиях, данные, получаемые от датчиков, журналы учета сетевой активности и, как в настоящем случае, данные о задержке авиарейсов, размещаются в хранилищах BLOB-объектов.
2. **Выполнение заданий.**  При возникновении необходимости обработки данных, следует запустить соответствующий скрипт PowerShell (или клиентское приложение), чтобы подготовить кластер HDInsight к работе, выполнить необходимые задания и удалить этот кластер.  Данные, используемые заданиями, размещаются в хранилище BLOB-объектов Azure. Итоговые данные сохраняются даже после удаления кластера. Такой подход позволяет платить только за использованные ресурсы. 
3. **Извлечение выходных данных из хранилища больших двоичных объектов **или, как в данном случае, их экспорт в базу данных SQL Azure.

На следующем рисунке наглядно демонстрируется такой сценарий, а также приводится структура настоящей статьи:

![HDI.FlightDelays.flow][img-hdi-flightdelays-flow]

**Примечание**. Числовые величины на рисунке соответствуют названиям разделов.

В основной части учебника рассматривается, как использовать один сценарий PowerShell, чтобы выполнить следующие задачи.

- Подготовка кластера HDInsight.
- Выполнение задания Hive в кластере для вычисления средних значений задержки рейсов в аэропортах.  Данные о задержке рейсов хранятся в учетной записи хранилища больших двоичных объектов Azure. 
- Выполнение задания Sqoop для экспорта выходных данных задания Hive в базу данных SQL Azure.
- Удаление кластера HDInsight. 

В приложениях вы можете найти указания по передаче данных о задержке рейсов, создании и передаче строки запроса Hive и подготовке базы данных SQL Azure для задания Sqoop.

##В этом учебнике рассматриваются следующие темы:

* [Предварительные требования](#prerequisite)
* [Подготовка кластера HDInsight и выполнение заданий Hive и Sqoop (M1)](#runjob)
* [Приложение А. Передача данных о задержке рейсов в хранилище больших двоичных объектов Azure (A1)](#appendix-a)
* [Приложение Б. Создание и передача сценария HiveQL (A2)](#appendix-b)
* [Приложение В. Подготовка базы данных SQL Azure для выходных данных задания Sqoop (A3)](#appendix-c)
* [Дальнейшие действия](#nextsteps)

##<a id="prerequisite"></a>Предварительные требования

Перед началом работы с этим учебником необходимо иметь следующее:

* Рабочая станция, на которой установлена и настроена среда Azure PowerShell. Указания см. в статье [Установка и настройка Azure PowerShell][powershell-install-configure].
* Подписка Azure. Дополнительную информацию о получении подписки см. в разделах [Варианты приобретения][azure-purchase-options], [Предложения для участников][azure-member-offers] или [Бесплатное пробное использование][azure-free-trial].

###Общие сведения о хранилище HDInsight

Кластеры Hadoop в HDInsight используют для хранения данных хранилища BLOB-объектов Azure.  Оно называется WASB или "Служба хранилища Azure - большие двоичные объекты". WASB - это реализация HDFS на базе хранилища больших двоичных объектов Azure от корпорации Майкрософт. Дополнительную информацию см. в статье [Использование хранилища BLOB-объектов Azure с HDInsight][hdinsight-storage]. 

При подготовке кластера HDInsight контейнер хранилища BLOB-объектов, принадлежащий учетной записи хранения Azure, используется в качестве файловой системы по умолчанию, так же как и HDFS. Эта учетная запись хранения используется в качестве учетной записи хранения по умолчанию, а контейнер больших двоичных объектов - как контейнер больших двоичных объектов по умолчанию или просто контейнер по умолчанию. Учетная запись хранения, используемая по умолчанию, должна находиться в том же центре обработки данных, что и кластер HDInsight. При удалении кластера HDInsight удаления используемого по умолчанию контейнера или используемой по умолчанию учетной записи хранения не происходит.

В дополнение к учетной записи хранения по умолчанию другие учетные записи хранения Azure могут быть привязаны к кластеру HDInsight во время процесса его подготовки. Такая привязка выражается в добавлении самой учетной записи хранения или ключа учетной записи хранения в файл конфигурации. Таким образом, кластер получает во время работы доступ к этим учетным записям хранения. Указания по добавлению дополнительных учетных записей хранения см. в разделе [Подготовка кластеров Hadoop в HDInsight][hdinsight-provision]. 

Синтаксис WASB:

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

>[WACOM.NOTE] Путь WASB является виртуальным.  Дополнительную информацию см. в статье [Использование хранилища BLOB-объектов Azure с HDInsight][hdinsight-storage]. 

Для файла, хранящегося в контейнере по умолчанию, доступ из HDInsight может осуществляться с помощью любого из следующих URI (flightdelays.hql используется в качестве примера):

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/flightdelays/flightdelays.hql
	wasb:///tutorials/flightdelays/flightdelays.hql
	/tutorials/flightdelays/flightdelays.hql

Если вы пытаетесь получить доступ к этому файлу непосредственно из учетной записи хранения, имя BLOB-объекта для файла будет следующим:

	tutorials/flightdelays/flightdelays.hql

Обратите внимание, что в начале имени BLOB-объекта символ "/" отсутствует.

**Файлы, используемые в этом учебнике**

В этом учебнике используются данные о текущей работе рейсов авиакомпании, полученные из [Бюро транспортной статистики при администрации по исследованиям и инновационным технологиям][rita-website] (RITA). Эти данные были загружены в контейнер службы хранения BLOB-объектов Azure с правами доступа "Общедоступный BLOB". Так как данный контейнер больших двоичных объектов является общедоступным, вам не требуется привязывать эту учетную запись хранения к кластеру HDInsight, выполняемому сценарий Hive. Скрипт HiveQL загружается в тот же самый контейнер BLOB-объектов. Информацию о том, как извлекать данные из собственной учетной записи хранения и размещать их в этой учетной записи, а также о том, как создавать и передавать сценарии HiveQL, см. в [Приложении А](#appendix-a) и [Приложении Б](#appendix-b).

В следующей таблице перечислены файлы, используемые в этом учебнике:

<table border="1">
<tr><th>Файлы</th><th>Описание</th></tr>
<tr><td>wasb://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql</td><td>Сценарий HiveQL, используемый для запуска соответствующего задания Hive. Этот сценарий был загружен в учетную запись хранилища больших двоичных объектов Azure с общими правами доступа.  В <a href="#appendix-b">приложении Б</a> содержатся указания по подготовке и передаче этого файла в вашу учетную запись хранилища больших двоичных объектов Azure.</td></tr>
<tr><td>wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data</td><td>Исходные данные для задания Hive. Эти данные были переданы в учетную запись хранилища больших двоичных объектов Azure с общими правами доступа.  В <a href="#appendix-a">приложении А</a> содержатся указания по получению и передаче данных в вашу учетную запись хранилища больших двоичных объектов Azure.</td></tr>
<tr><td>\tutorials\flightdelays\output</td><td>Путь вывода данных для задания Hive. Для хранения результирующих данных используется контейнер по умолчанию.</td></tr>
<tr><td>\tutorials\flightdelays\jobstatus</td><td>Папка состояния задания Hive в контейнере по умолчанию.</td></tr>
</table>



###Общие сведения о внутренней и внешней таблицах Hive

Есть несколько вещей, которые нужно знать о внутренней и внешней таблицах Hive.

- Команда CREATE TABLE создает внутреннюю таблицу. Файл данных должен находиться в контейнере по умолчанию.
- Команда CREATE TABLE перемещает файл данных в папку /hive/warehouse/<TableName>.
- Команда CREATE EXTERNAL TABLE создает внешнюю таблицу. Файл данных может находиться за пределами контейнера по умолчанию.
- Команда CREATE EXTERNAL TABLE не перемещает файл данных.
- Команда CREATE EXTERNAL TABLE не разрешает наличие любых папок в LOCATION. Именно по этой причине в учебнике создается копия файла sample.log.

Дополнительную информацию см. в статье [HDInsight: Hive Internal and External Tables Intro][cindygross-hive-tables] (HDInsight: введение по внутренним и внешним таблицам Hive).

> [WACOM.NOTE] Одна из инструкций HiveQL создает внешнюю таблицу Hive. Внешняя таблица Hive хранит файл данных в исходном расположении. Внутренняя таблица Hive перемещает файл данных в hive\warehouse. Для внутренней таблицы Hive требуется, чтобы файл данных находился в контейнере по умолчанию. Для данных, которые хранятся не в контейнере BLOB-объектов по умолчанию, используется внешняя таблица Hive.









##<a id="runjob"></a>Подготовка кластера HDInsight и выполнение заданий Hive и Sqoop 

Hadoop MapReduce представляет из себя пакетную обработку данных. Наиболее эффективным способом выполнения заданий Hive является подготовка кластера к конкретному заданию и удаление задания после его выполнения. Приводимый далее скрипт позволяет полностью реализовать этот процесс. Дополнительную информацию о подготовке кластера HDInsight и выполнении заданий Hive см. в статьях [Подготовка кластеров Hadoop в HDInsight][hdinsight-provision] и [Использование Hive с HDInsight][hdinsight-use-hive]. 

**Выполнение задания Hive с помощью PowerShell**

1. Создайте базу данных SQL Azure и таблицу для выходных данных задания Sqoop согласно указаниям в [Приложении В](#appendix-c).
2. Задайте следующие параметры.

	<table border="1">
	<tr><th>Имя переменной</th><th>Примечания</th></tr>
	<tr><td>$hdinsightClusterName</td><td>Имя кластера HDInsight. Если кластер не существует, сценарий создаст его с заданным именем.</td></tr>
	<tr><td>$storageAccountName</td><td>Учетная запись хранения Azure, которая будет использоваться по умолчанию. Это значение необходимо, только если сценарию требуется создать кластер HDInsight. Не указывайте это значение, если для переменной $hdinsightClusterName указано имя существующего кластера HDInsight. Если учетная запись хранения с указанным значением не существует, сценарий создаст ее с заданным именем.</td></tr>
	<tr><td>$blobContainerName</td><td>Контейнер больших двоичных объектов, который будет использоваться для файловой системы по умолчанию. Если вы не укажите значение этой переменной, будет использоваться значение переменной $hdinsightClusterName. </td></tr>
	<tr><td>$sqlDatabaseServerName</td><td>Имя сервера базы данных SQL Azure. Это должен быть существующий сервер. Информацию о создании сервера базы данных <a href="#appendix-c">см. в</a> Приложении C.</td></tr>
	<tr><td>$sqlDatabaseUsername</td><td>Имя для входа на сервер базы данных SQL Azure.</td></tr>
	<tr><td>$sqlDatabasePassword</td><td>Пароль для входа на сервер базы данных SQL Azure.</td></tr>
	<tr><td>$sqlDatabaseName</td><td>Сервер базы данных SQL, куда Sqoop экспортирует данные. Имя файла по умолчанию - HDISqoop. Имя таблицы для выходных данных задания Sqooop - AvgDelays. </td></tr>
	</table>
2. Откройте интегрированную среду сценариев PowerShell.
2. Скопируйте следующий скрипт и вставьте его в область скриптов:

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
		
		#region - HDinsight cluster variables
		[int]$clusterSize = 1                # One data node is sufficient for this tutorial.
		[String]$location = "Central US"     # For better performance, choose a data center near you.
		[String]$hadoopUserLogin = "admin"   # Use "admin" as the Hadoop login name
		[String]$hadoopUserpw = "Pass@word1" # Use "Pass@word1" as te the Hadoop login password
		
		[Bool]$isNewCluster = $false      # Indicates whether a new HDInsight cluster is created by the script.  
		                                  # If this variable is true, then the script can optionally delete the cluster after running the Hive and Sqoop jobs.
		
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
		
		# Both the Azure SQL database server and database must exist.
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
		
		    #region - Retrieve the default storage account/container names of the cluster exists
		    # The Hive job output will be stored in the default container. The 
		    # information is used to download a copy of the output file from 
		    # Blob storage to workstation for the validation purpose.
		    Write-Host "`nRetrieving the HDInsight cluster default storage account information ..." `
		                -ForegroundColor Green
		
		    $hdi = Get-AzureHDInsightCluster -Name $HDInsightClusterName
		
		    # Use the default storage account and the default container even if the names are different from the user input
		    $storageAccountName = $hdi.DefaultStorageAccount.StorageAccountName `
		                            -replace ".blob.core.windows.net"
		    $blobContainerName = $hdi.DefaultStorageAccount.StorageContainerName
		
		    Write-Host "`tThe default storage account for the cluster is $storageAccountName." `
		                -ForegroundColor Cyan
		    Write-Host "`tThe default Blob container for the cluster is $blobContainerName." `
		                -ForegroundColor Cyan
		    #endregion
		}
		else     #If the cluster doesn't exist, a new one will be provisioned.
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
		
		        #region - Provision HDInsigtht cluster
		        # Create an Azure storage account if it doesn't exist
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
		$response = Invoke-Hive -File $hqlScriptFile -StatusFolder $jobStatusFolder
		
		Write-Host "`nThe Hive job status" -ForegroundColor Cyan
		Write-Host "---------------------------------------------------------" -ForegroundColor Cyan
		write-Host $response
		Write-Host "---------------------------------------------------------" -ForegroundColor Cyan
		#endregion 
		
		#region - run Sqoop job
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
		
		#region - Delete the storage account
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

4. Нажмите клавишу **F5**, чтобы запустить сценарий. Результат должен быть аналогичен приведенному ниже:

	![HDI.FlightDelays.RunHiveJob.output][img-hdi-flightdelays-run-hive-job-output]
		
5. Подключитесь к базе данных SQL и просмотрите средние значения задержки рейсов по городу в таблице AvgDelays:

	![HDI.FlightDelays.AvgDelays.Dataset][image-hdi-flightdelays-avgdelays-dataset]



---
##<a id="appendix-a"></a>Приложение А. Передача данных о задержке рейсов в хранилище больших двоичных объектов Azure
Прежде чем передать файл данных и файлы сценария HiveQL, просмотрите [Приложение Б],(#appendix-b)так как для этого требуется спланировать действия. Суть заключается в том, чтобы сохранить файлы данных и файл HiveQL до подготовки кластера HDInsight и выполнения задания Hive.  Существует два варианта.

- **Использование учетной записи хранения Azure, которая будет использоваться в качестве файловой системы по умолчанию для кластера HDInsight.** Вам не нужно вносить никакие дополнительные изменения, поскольку у кластера HDInsight будет ключ доступа к учетной записи хранения.
- **Использование учетной записи хранения Azure, отличной от той, которая будет использоваться в качестве файловой системы по умолчанию для кластера HDInsight.** В этом случае вам следует изменить часть сценария PowerShell с подготовкой, которую можно найти в разделе [Подготовка кластера HDInsight и выполнение заданий Hive и Sqoop],(#runjob) чтобы добавить учетную запись хранения в качестве дополнительной. Указания см. в статье [Подготовка кластеров Hadoop в HDInsight][hdinsight-provision]. После этого кластеру HDInsight станет известен ключ доступа к учетной записи хранения.

>[WACOM.NOTE] Путь WASB к файлу данных жестко запрограммирован в файле сценария HiveQL. Его следует обновлять соответствующим образом.

**Скачивание данных о рейсах**

1. Перейдите на страницу [Бюро транспортной статистики при администрации по исследованиям и инновационным технологиям][rita-website] (RITA).
2. На странице выберите следующие значения:

	<table border="1">
	<tr><th>Имя</th><th>Значение</th></tr>
	<tr><td>Фильтр года</td><td>2013 </td></tr>
	<tr><td>Период фильтра</td><td>Январь</td></tr>
	<tr><td>Поля:</td><td>*Year*, *FlightDate*, *UniqueCarrier*, *Carrier*, *FlightNum*, *OriginAirportID*, *Origin*, *OriginCityName*, *OriginState*, *DestAirportID*, *Dest*, *DestCityName*, *DestState*, *DepDelayMinutes*, *ArrDelay*, *ArrDelayMinutes*, *CarrierDelay*, *WeatherDelay*, *NASDelay*, *SecurityDelay*, *LateAircraftDelay* (очистите все остальные поля)</td></tr>
	</table>

3. Щелкните **Скачать**. 
4. Распакуйте файл в папку **C:\Tutorials\FlightDelays\Data**.  Каждый файл представляет собой CSV-файл и имеет размер около 60 ГБ.
5.	Переименуйте файл в название месяца, данные по которому он содержит. Например, файл, содержащий данные за январь, следует назвать January.csv.
6. Повторите шаг 2 и 5, чтобы скачать файл для каждого из 12 месяцев 2013 года. Чтобы запустить учебник, вам понадобится как минимум один файл.  

**Передача данных о задержке рейсов в хранилище больших двоичных объектов Azure**

1. Задайте следующие параметры.

	<table border="1">
	<tr><th>Имя переменной</th><th>Примечания</th></tr>
	<tr><td>$storageAccountName</td><td>Учетная запись хранения Azure, в которую необходимо отправить данные.</td></tr>
	<tr><td>$blobContainerName</td><td>Контейнер больших двоичных объектов, в который необходимо отправить данные.</td></tr>
	</table>
2. Откройте интегрированную среду сценариев PowerShell.
2. Добавьте следующий скрипт в область скриптов:

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
		$localFolder = "C:\Tutorials\FlightDelays\Data"  # the source folder
		$destFolder = "tutorials/flightdelays/data"     #the blob name prefix for the files to be uploaded
		#EndRegion
		
		#Region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		if (-not (Get-AzureAccount)){ Add-AzureAccount}
		#EndRegion
		
		#Region - Validate user inpute
		# Validate the storage account
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
		
		# List the uploaded files on HDinsight
		Get-AzureStorageBlob -Container $blobContainerName  -Context $storageContext -Prefix $destFolder
		#EndRegion

3. Нажмите клавишу **F5**, чтобы запустить сценарий.

Если вы решили использовать другой метод для отправки файлов, убедитесь, что для пути к файлу не указано значение tutorials/flightdelays/data. Для доступа к файлам используется следующий синтаксис:

	wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelays/data

tutorials/flightdelays/data - это виртуальная папка, которую вы создали при отправке файлов. Убедитесь, что присутствует 12 файлов - по одному для каждого месяца.

>[WACOM.NOTE] Для возможности считывания данных из нового расположения необходимо обновить запрос Hive.

> Вы должны настроить разрешения для общего доступа к контейнеру или привязать учетную запись хранения к кластеру HDInsight.  В противном случае строка запроса Hive не сможет получить доступ к файлам данных. 

---
##<a id="appendix-b"></a>Приложение Б. Создание и отправка сценариев HiveQL

С помощью Azure PowerShell можно одновременно запустить несколько инструкций HiveQL по одной за раз или упаковать инструкцию HiveQL в файл скрипта. В данном разделе рассказывается, как создавать скрипты HiveQL и загружать их в хранилище BLOB-объектов Azure с помощью PowerShell. Для Hive требуется, чтобы скрипты HiveQL хранились в WASB.

Скрипт HiveQL выполнит следующее:

1. **Удаляет таблицу delays_raw**, если она уже существует.
2. **Создает внешнюю таблицу Hive delays_raw**, указывающую на расположение WASB с файлами данных о задержках рейсов. Этот запрос задает то, что поля разделяются с помощью "," и что строки завершаются с помощью "\n". Это создает проблему при наличии запятых в значениях полей, так как Hive не отличает запятую, которая является разделителем поля, и запятую, которая является частью значения поля (что характерно для значений полей ORIGIN\_CITY\_NAME и DEST\_CITY\_NAME). Для решения этой проблемы запрос создает столбцы TEMP для хранения данных, неправильно разбитых на столбцы.  
3. **Удаляет таблицу delays**, если она уже существует.
4. **Создает таблицу delays**. Это удобно для очистки данных перед дальнейшей обработкой. Этот запрос создает новую таблицу delays из таблицы delays_raw. Обратите внимание, что столбцы TEMP (как уже упоминалось ранее) не копируются, а функция substring используется для удаления кавычек из данных. 
5. **Вычисляет среднюю задержку из-за погоды и группирует результаты по названию города.** Он также выводит результаты в WASB. Обратите внимание, что запрос удаляет апострофы из данных и исключает строки, где для weather_delay указано значение null, что необходимо, так как Sqoop, используемый далее в этом учебнике, по умолчанию обрабатывает эти значения некорректно.

Полный список команд HiveQL см. в статье [Hive Data Definition Language][hadoop-hiveql] (Язык описания данных Hive). Каждая команда HiveQL должна завершаться точкой с запятой.

**Создание файла сценария HiveQL**

1. Задайте следующие параметры.

	<table border="1">
	<tr><th>Имя переменной</th><th>Примечания</th></tr>
	<tr><td>$storageAccountName</td><td>Учетная запись хранения Azure, в которую необходимо отправить сценарий HiveQL.</td></tr>
	<tr><td>$blobContainerName</td><td>Контейнер больших двоичных объектов, в который необходимо отправить сценарий HiveQL.</td></tr>
	</table>
2. Откройте интегрированную среду сценариев Azure PowerShell.

2. Скопируйте следующий скрипт и вставьте его в область скриптов:

		[CmdletBinding()]
		Param(
		
		    # Azure blob storage variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
		    [String]$storageAccountName,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
		    [String]$blobContainerName
		
		)
		
		#region - define variables
		# Treat all errors as terminating
		$ErrorActionPreference = "Stop"
		
		# the HQL script file is exported as this file before uploaded to WASB
		$hqlLocalFileName = "C:\tutorials\flightdelays\flightdelays.hql" 
		
		# the HQL script file will be upload to WASB as this blob name
		$hqlBlobName = "tutorials/flightdelays/flightdelays.hql" 
		
		# this two constants are used by the HQL scrpit file
		#$srcDataFolder = "tutorials/flightdelays/data" 
		$dstDataFolder = "/tutorials/flightdelays/output"
		#endregion
		
		#region - Validate the file and file path
		
		# check if a file with the same file name already exist on the workstation
		Write-Host "`nvalidating the folder structure on the workstation for saving the HQL script file ..."  -ForegroundColor Green
		if (test-path $hqlLocalFileName){
		
		    $isDelete = Read-Host 'The file, ' $hqlLocalFileName ', exists.  Do you want to overwirte it? (Y/N)'
		
		    if ($isDelete.ToLower() -ne "y")
		    {
		        Exit
		    }
		}
		
		# create the folder if it doesn't exist
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
		
		# Upload the file from local workstation to WASB
		Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $blobContainerName -Blob $hqlBlobName -Context $destContext 
		#endregion
		
		Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

	В данном сценарии используются три следующие переменные:

	- **$hqlLocalFileName**. Этот скрипт сохраняет скрипт HiveQL локально перед его загрузкой на WASB. Это имя файла. По умолчанию используется значение <u>C:\tutorials\flightdelays\flightdelays.hql</u>.
	- **$hqlBlobName**: Это название BLOB-объекта для файла скрипта HiveQL, используемое в хранилище BLOB-объектов Azure.. По умолчанию используется значение <u>tutorials/flightdelays/flightdelays.hql</u>. Так как этот файл записывается непосредственно в хранилище BLOB-объектов Azure, в начале имени BLOB-объекта отсутствует символ "/". Если требуется получить доступ к этому файлу из WASB, необходимо добавить символ "/" в начало имени этого файла.
	- **$srcDataFolder** и **$dstDataFolder**:  = "tutorials/flightdelays/data" 
 = "tutorials/flightdelays/output"


---
##<a id="appendix-c"></a>Приложение В. Подготовка базы данных SQL Azure для выходных данных задания Sqoop
**Подготовка базы данных SQL (объедините со сценарием Sqoop)**

1. Задайте следующие параметры.

	<table border="1">
	<tr><th>Имя переменной</th><th>Примечания</th></tr>
	<tr><td>$sqlDatabaseServerName</td><td>Имя сервера базы данных SQL Azure. Чтобы создать новый сервер, не указывайте ничего.</td></tr>
	<tr><td>$sqlDatabaseUsername</td><td>Имя для входа на сервер базы данных SQL Azure. Если значение переменной $sqlDatabaseServerName представляет собой существующий сервер, для выполнения аутентификации на сервере используются имя пользователя и пароль для входа.  В противном случае они используются для создания нового сервера.</td></tr>
	<tr><td>$sqlDatabasePassword</td><td>Пароль для входа на сервер базы данных SQL Azure.</td></tr>
	<tr><td>$sqlDatabaseLocation</td><td>Этот параметр используется только при создании нового сервера базы данных SQL Azure.</td></tr>
	<tr><td>$sqlDatabaseName</td><td>База данных SQL, которая используется для создания таблицы AvgDelays для задания Sqoop. Если не указывать значение этой переменной, будет создана база данных с названием HDISqoop. Имя таблицы для выходных данных задания Sqooop - AvgDelays. </td></tr>
	</table>
2. Откройте интегрированную среду сценариев PowerShell. 
2. Скопируйте следующий скрипт и вставьте его в область скриптов:
	
		[CmdletBinding()]
		Param(
		
		    # SQL database server variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database Server Name to use an existing one. Enter nothing to create a new one.")]
		    [AllowEmptyString()]
		    [String]$sqlDatabaseServer,  # specify the Azure SQL database server name if you have one created. Otherwise use "".
		
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
		    [String]$sqlDatabaseName # specify the database name if you have one created.  Otherwise use "" to have the script create one for you.
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
		
		#region - Create and validate Azure SQL Database server
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
			
		#region -  Excute a SQL command to create the AvgDelays table
			
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

	>[WACOM.NOTE] Этот сценарий использует службу REST (http://bot.whatismyipaddress.com) для получения вашего внешнего IP-адреса. Этот IP-адрес используется для создания правила брандмауэра, необходимого для работы сервера базы данных SQL.  

	В данном сценарии используются следующие переменные:

	- **$ipAddressRestService**. Значение по умолчанию - <u>http://bot.whatismyipaddress.com</u>. Этот общедоступный IP-адрес службы REST предназначен для получения внешнего IP-адреса. При необходимости можно использовать и другие службы. Внешний IP-адрес, полученный посредством данной службы, используется при создании правила брандмауэра для сервера базы данных SQL в Azure, поэтому доступ к этой базе данных можно получить с рабочей станции (с помощью скрипта PowerShell).
	- **$fireWallRuleName**. Это название правила брандмауэра предназначено для обеспечения работы базы данных SQL в Azure. Имя по умолчанию - <u>FlightDelay</u>. При необходимости можно использовать другое имя.
	- **$sqlDatabaseMaxSizeGB**. Этот параметр используется только при создании нового сервера базы данных SQL в Azure. Значение по умолчанию - <u>10GB</u>. Размера 10ГБ достаточно для примеров из данного учебника.
	- **$sqlDatabaseName**. Этот параметр используется только при создании новой базы данных SQL в Azure. Значение по умолчанию - <u>HDISqoop</u>. Если вы присваиваете данному параметру другое имя, необходимо обновить скрипт PowerShell в Sqoop. 

4. Нажмите клавишу **F5**, чтобы запустить сценарий. 
5. Проверка выходных данных скрипта. Позволяет убедиться, что скрипт был выполнен без ошибок.	

##<a id="nextsteps"></a> Дальнейшие действия
Теперь вы знаете, как отправить файл в хранилища BLOB-объектов, как заполнить таблицу Hive, используя данные из хранилища BLOB-объектов, как выполнять запросы Hive и использовать Sqoop для экспорта данных из HDFS в базу данных SQL Azure. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Getting Started with HDInsight][hdinsight-get-started] (Приступая к работе с HDInsight)
* [Использование Hive с HDInsight][hdinsight-use-hive]
* [Использование Oozie с HDInsight][hdinsight-use-oozie]
* [Использование Sqoop с HDInsight][hdinsight-use-sqoop]
* [Использование Pig с HDInsight][hdinsight-use-pig]
* [Разработка программ MapReduce на Java для HDInsight][hdinsight-develop-mapreduce]
* [Разработка программ потоковой передачи Hadoop на C# для HDInsight][hdinsight-develop-streaming]



[azure-purchase-options]: http://azure.microsoft.com/ru-ru/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/ru-ru/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/ru-ru/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
[Powershell-install-configure]: ../install-configure-powershell/

[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-develop-streaming]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-develop-mapreduce]: ../hdinsight-develop-deploy-java-mapreduce/

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
[hadoop-shell-commands]: http://hadoop.apache.org/docs/r0.18.3/hdfs_shell.html

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

[image-hdi-flightdelays-avgdelays-dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
[img-hdi-flightdelays-run-hive-job-output]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.RunHiveJob.Output.png
[img-hdi-flightdelays-flow]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.Flow.png


<!--HONumber=35.1-->
