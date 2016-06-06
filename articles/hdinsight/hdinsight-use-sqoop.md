<properties
	pageTitle="Использование Hadoop Sqoop в HDInsight | Microsoft Azure"
	description="Вы узнаете, как использовать Azure PowerShell с рабочей станции для запуска Sqoop, импорта и экспорта между кластером HDInsight и базой данных Azure SQL."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/24/2016"
	ms.author="jgao"/>

#Использование Sqoop с Hadoop в HDInsight

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Узнайте, как использовать Sqoop в HDInsight для импорта и экспорта между кластером HDInsight и базой данных SQL Azure или базой данных SQL Server.

Хотя Hadoop оптимально подходит для обработки частично структурированных и неструктурированных данных, таких как журналы и файлы, может существовать необходимость обработки структурированных данных, хранящихся в реляционных базах данных.

[Sqoop][sqoop-user-guide-1.4.4] — это средство, предназначенное для передачи данных между кластерами Hadoop и реляционными базами данных. С его помощью можно импортировать данные из системы управления реляционной базой данных (реляционной СУБД), например SQL Server, MySQL или Oracle, в распределенную файловую систему Hadoop (HDFS), преобразовать данные в системе Hadoop с использованием MapReduce или Hive, а затем экспортировать данные обратно в реляционную СУБД. В этом учебнике в качестве реляционной базы данных используется база данных SQL.

Сведения о версиях Sqoop, поддерживаемых в кластерах HDInsight, см. в разделе [Новые возможности версий кластеров, образованных с помощью HDInsight][hdinsight-versions].

##Ознакомление со сценарием

Кластер HDInsight имеет несколько примеров данных. Вы будете использовать два следующих примера:

- Файл журнала log4j, расположенный в */example/data/sample.log*. Из файла извлекаются следующие журналы:

		2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
		2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
		2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
		...

- Таблица Hive с именем *hivesampletable*, которая ссылается на файл данных, расположенный в */hive/warehouse/hivesampletable*. Эта таблица содержит некоторые данные о мобильных устройствах.

    | Поле | Тип данных |
    | ----- | --------- |
    | clientid | string |
    | querytime | string |
    | market | string |
    | deviceplatform | string |
    | devicemake | string |
    | devicemodel | string |
    | state | string |
    | country | string |
    | querydwelltime | double |
    | sessionid | bigint |
    | sessionpagevieworder | bigint |

Сначала вы экспортируете *sample.log* и *hivesampletable* в базу данных SQL Azur или на SQL Server, а затем импортируете таблицу с данными о мобильных устройствах обратно в HDInsight, используя следующий путь:

	/tutorials/usesqoop/importeddata

## Создание кластера и базы данных SQL

В этом разделе показано, как создать кластер и схемы базы данных SQL для выполнения заданий учебника с помощью портала Azure и шаблона ARM. Если вы предпочитаете использовать Azure PowerShell, см. [приложение A](#appendix-a---a-powershell-sample).

1. Щелкните следующие изображение, чтобы открыть шаблон ARM на портале Azure.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fusesqoop%2Fcreate-linux-based-hadoop-cluster-in-hdinsight-and-sql-database.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Шаблон ARM расположен в общедоступном контейнере больших двоичных объектов: **https://hditutorialdata.blob.core.windows.net/usesqoop/create-linux-based-hadoop-cluster-in-hdinsight-and-sql-database.json*.
    
    Шаблон ARM вызывает пакет BACPAC для развертывания схем таблиц в базе данных SQL. Пакет BACPAC также находится в общедоступном контейнере больших двоичных объектов: https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac. Чтобы использовать закрытый контейнер для BACPAC-файлов, используйте следующие значения в шаблоне:
    
        "storageKeyType": "Primary",
        "storageKey": "<TheAzureStorageAccountKey>",
    
2. В колонке "Параметры" заполните следующие поля:

    - **Имя кластера**: введите имя создаваемого кластера Hadoop.
    - **Имя для входа и пароль кластера**: имя для входа по умолчанию — admin.
    - **Имя пользователя SSH и пароль**.
    - **Имя для входа на сервер базы данных SQL Azure и пароль**.

    В разделе переменных жестко заданы указанные далее значения.
    
    |Имя учетной записи хранения по умолчанию|<CluterName> store|
    |----------------------------|-----------------|
    |Имя сервера базы данных SQL Azure|<ClusterName> dbserver|
    |Имя базы данных SQL Azure|<ClusterName> db|
    
    Запишите эти значения. Они потребуются позже в данном руководстве.
    
3\. Нажмите кнопку **ОК**, чтобы сохранить параметры.

4\. В колонке **Настраиваемое развертывание** щелкните раскрывающийся список **Группа ресурсов** и выберите пункт **Создать**, чтобы создать группу ресурсов. Группа ресурсов — это контейнер, в который входит кластер, зависимая учетная запись хранения и другие связанные ресурсы.

5\. Щелкните **Условия использования**, а затем нажмите кнопку **Создать**.

6\. Нажмите кнопку **Создать**. Вы увидите новый элемент под названием "Идет отправка развертывания для развертывания шаблона". Процесс создания кластера и базы данных SQL занимает около 20 минут.

Если вы решили использовать существующую базу данных SQL Azure или Microsoft SQL Server

- **База данных SQL Azure**: необходимо настроить правило брандмауэра для сервера базы данных SQL Azure, чтобы разрешить доступ с рабочей станции. Инструкции по созданию базы данных Azure SQL и настройке брандмауэра см. в статье [Приступая к работе с базой данных SQL Azure][sqldatabase-get-started]. 

    > [AZURE.NOTE] По умолчанию в базе данных SQL Azure разрешены подключения из служб Azure, таких как Azure HDInsight. Если этот параметр брандмауэра отключен, вы должны включить его на портале Azure. Инструкции по созданию базы данных SQL Azure и настройке правил брандмауэра см. в статье [Создание и настройка базы данных SQL Azure][sqldatabase-create-configue].

- **SQL Server**: если ваш кластер HDInsight находится в той же виртуальной сети Azure, что и SQL Server, эта статья поможет вам разобраться с импортом данных в базу данных SQL Server и их экспортом из нее.

    > [AZURE.NOTE] HDInsight поддерживает только географически привязанные виртуальные сети и на данный момент не работает с виртуальными сетями на основе территориальных групп.

    * Сведения о создании и настройке виртуальной сети см. в статье [Задачи настройки виртуальной сети](../services/virtual-machines/).

        * При использовании SQL Server в центре обработки данных необходимо настроить тип соединения виртуальной сети либо как *сеть-сеть*, либо *как точка-сеть*.

            > [AZURE.NOTE] Для виртуальных сетей с типом соединения **точка-сеть** на SQL Server должно быть запущено приложение настройки VPN-клиента, которое доступно на **панели мониторинга** в конфигурации виртуальной сети Azure.

        * При использовании SQL Server на виртуальной машине Azure можно использовать любую конфигурацию виртуальной сети, если виртуальная машина с SQL Server находится в той же виртуальной сети, что и HDInsight.

    * Сведения о создании кластера HDInsight в виртуальной сети см. в статье [Создание кластеров Hadoop под управлением Windows в HDInsight](hdinsight-provision-clusters.md).

    > [AZURE.NOTE] Кроме того, SQL Server должен разрешать проверку подлинности. Для выполнения шагов, описанных в этой статье, вам понадобится использовать данные для входа на SQL Server.


## Выполнение заданий Sqoop

HDInsight может выполнять задания Sqoop с помощью различных методов. Используйте следующую таблицу, чтобы решить, какой метод подходит вам, а затем перейдите по ссылке, чтобы открыть пошаговое руководство.

| **Используйте**, если требуется... | ...**интерактивная** оболочка | ...**пакетная** обработка | ... с **кластерной операционной системой** | ...из этого **кластера операционной системы** |
|:--------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-use-sqoop-mac-linux.md) | ✔ | ✔ | Linux | Linux, Unix, Mac OS X или Windows |
| [.NET SDK для Hadoop](hdinsight-hadoop-use-sqoop-dotnet-sdk.md) | &nbsp; | ✔ | Linux или Windows | Windows (сейчас) |
| [Azure PowerShell](hdinsight-hadoop-use-sqoop-powershell.md) | &nbsp; | ✔ | Linux или Windows | Windows |

##Дальнейшие действия

Теперь вы узнали, как использовать Sqoop. Дополнительные сведения см. на следующих ресурсах:

- [Использование Hive с HDInsight](hdinsight-use-hive.md)
- [Использование Pig с HDInsight](hdinsight-use-pig.md)
- [Использование Oozie с HDInsight][hdinsight-use-oozie]\: используйте действие Sqoop в рабочем процессе Oozie.
- [Анализ данных о задержке рейсов с помощью HDInsight][hdinsight-analyze-flight-data]\: используйте Hive для анализа данных о задержке рейсов, а затем используйте Sqoop для экспорта данных в базу данных SQL Azure.
- [Передача данных в HDInsight][hdinsight-upload-data]\: узнайте о других способах отправки данных в HDInsight и хранилище больших двоичных объектов Azure.


## Приложение А. Пример PowerShell

В этом примере PowerShell выполняются приведенные ниже действия.

1. Подключение к Azure.
2. Создание группы ресурсов Azure. Дополнительную информацию см. в статье [Использование Azure PowerShell с диспетчером ресурсов Azure](../powershell-azure-resource-manager.md).
3. Создание сервера базы данных SQL Azure, базы данных SQL Azure и двух таблиц. 

	Если же вы используете SQL Server, воспользуйтесь следующими инструкциями, чтобы создать таблицы:
	
		CREATE TABLE [dbo].[log4jlogs](
		 [t1] [nvarchar](50),
		 [t2] [nvarchar](50),
		 [t3] [nvarchar](50),
		 [t4] [nvarchar](50),
		 [t5] [nvarchar](50),
		 [t6] [nvarchar](50),
		 [t7] [nvarchar](50))

		CREATE TABLE [dbo].[mobiledata](
		 [clientid] [nvarchar](50),
		 [querytime] [nvarchar](50),
		 [market] [nvarchar](50),
		 [deviceplatform] [nvarchar](50),
		 [devicemake] [nvarchar](50),
		 [devicemodel] [nvarchar](50),
		 [state] [nvarchar](50),
		 [country] [nvarchar](50),
		 [querydwelltime] [float],
		 [sessionid] [bigint],
		 [sessionpagevieworder][bigint])

	Проверить базу данных и таблицы проще всего с помощью Visual Studio. Сервер базы данных и базу данных можно проверить с помощью портала Azure.

4. Создание кластера HDInsight.

	Для проверки кластера можно использовать портал Azure или Azure PowerShell.

5. Выполнение предварительной обработки исходного файла данных.

	В этом учебнике вы экспортируете файл журнала log4j (файл с разделителями) и таблицу Hive в базу данных SQL Azure. Файл с разделителями — */example/data/sample.log*. Ранее в учебнике вы видели несколько примеров журналов log4j. В файле журнала есть несколько пустых строк и несколько строк, похожих на следующие:
	
		java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
			at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)
	
	Это нормально для других примеров, которые используют эти данные, но для импорта в базу данных SQL Azure или на SQL Server нам необходимо удалить такие исключения. Экспорт Sqoop не будет выполнен, если присутствует пустая строка или строка, число элементов в которой меньше количества полей, определенных в таблице базы данных SQL Azure. Таблица log4jlogs содержит 7 полей типа "строка".

	Данная процедура создает новый файл в кластере: tutorials/usesqoop/data/sample.log. Чтобы проанализировать измененный файл данных, вы можете использовать портал Azure, средство обозревателя службы хранилища Azure или Azure PowerShell. В статье [Приступая к работе с HDInsight][hdinsight-get-started] есть пример кода, где Azure PowerShell используется для скачивания файла и отображения его содержимого.

6. Экспортирование файла данных в базу данных SQL Azure.

	Исходный файл: tutorials/usesqoop/data/sample.log. Таблица, в которую экспортируются данные, называется log4jlogs.
	
	> [AZURE.NOTE] Кроме информации в строке соединения, описанные в этом разделе шаги должны подходить как для базы данных SQL Azure, так и для SQL Server. Эти шаги были протестированы для следующей конфигурации:
	>
	> * **Конфигурация виртуальной сети Azure типа "точка-сеть"**: кластер HDInsight, подключенный через виртуальную сеть к SQL Server в частном центре обработки данных. Дополнительную информацию см. в разделе [Настройка VPN "точка-сеть" на портале управления](../vpn-gateway/vpn-gateway-point-to-site-create.md) .
	> * **Azure HDInsight 3.1**: см. статью [Создание кластеров Hadoop под управлением Windows в HDInsight](hdinsight-provision-clusters.md), чтобы получить более подробную информацию о создании кластера в виртуальной сети.
	> * **SQL Server 2014**: настроенный для разрешения проверки подлинности и использующий пакет настройки VPN-клиента для надежного подключения к виртуальной сети.

7. Экспортирование таблицы Hive в базу данных SQL Azure.

8. Импортирование таблицы mobiledata в кластер HDInsight.

	Чтобы проанализировать измененный файл данных, вы можете использовать портал Azure, средство обозревателя службы хранилища Azure или Azure PowerShell. В статье [Приступая к работе с HDInsight][hdinsight-get-started] есть пример кода, где Azure PowerShell используется для скачивания файла и отображения его содержимого.


### Пример PowerShell

	# Prepare an Azure SQL database to be used by the Sqoop tutorial
	
	#region - provide the following values
	
	$subscriptionID = "<Enter your Azure Subscription ID>"
	
	$sqlDatabaseLogin = "<Enter a SQL Database Login name>" #SQL Database server login
	$sqlDatabasePassword = "<Enter a Password>"
	
	$httpUserName = "admin"  #HDInsight cluster username
	$httpPassword = "<Enter a Password>"
	
	# used for creating Azure service names
	$nameToken = "<Enter an alias>" 
	$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
	#endregion
	
	#region - variables
	
	# Resource group variables
	$resourceGroupName = $namePrefix + "rg"
	$location = "East US 2" # used by all Azure services defined in this tutorial
	
	# SQL database varialbes
	$sqlDatabaseServerName = $namePrefix + "sqldbserver"
	$sqlDatabaseName = $namePrefix + "sqldb"
	$sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
	$sqlDatabaseMaxSizeGB = 10
	
	# Used for retrieving external IP address and creating firewall rules
	$ipAddressRestService = "http://bot.whatismyipaddress.com"
	$fireWallRuleName = "UseSqoop"
	
	# Used for creating tables and clustered indexes
	$cmdCreateLog4jTable = "CREATE TABLE [dbo].[log4jlogs](
		[t1] [nvarchar](50),
		[t2] [nvarchar](50),
		[t3] [nvarchar](50),
		[t4] [nvarchar](50),
		[t5] [nvarchar](50),
		[t6] [nvarchar](50),
		[t7] [nvarchar](50))"
	
	$cmdCreateLog4jClusteredIndex = "CREATE CLUSTERED INDEX log4jlogs_clustered_index on log4jlogs(t1)"
	
	$cmdCreateMobileTable = " CREATE TABLE [dbo].[mobiledata](
	[clientid] [nvarchar](50),
	[querytime] [nvarchar](50),
	[market] [nvarchar](50),
	[deviceplatform] [nvarchar](50),
	[devicemake] [nvarchar](50),
	[devicemodel] [nvarchar](50),
	[state] [nvarchar](50),
	[country] [nvarchar](50),
	[querydwelltime] [float],
	[sessionid] [bigint],
	[sessionpagevieworder][bigint])"
	
	$cmdCreateMobileDataClusteredIndex = "CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)"
	
	# HDInsight variables
	$hdinsightClusterName = $namePrefix + "hdi"
	$defaultStorageAccountName = $namePrefix + "store"
	$defaultBlobContainerName = $hdinsightClusterName
	#endregion
	
	# Treat all errors as terminating
	$ErrorActionPreference = "Stop"
	
	#region - Connect to Azure subscription
	Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
	try{Get-AzureRmContext}
	catch{Login-AzureRmAccount}
	#endregion
	
	#region - Create Azure resouce group
	Write-Host "`nCreating an Azure resource group ..." -ForegroundColor Green
	try{
		Get-AzureRmResourceGroup -Name $resourceGroupName
	}
	catch{
		New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
	}
	#endregion
	
	#region - Create Azure SQL database server
	Write-Host "`nCreating an Azure SQL Database server ..." -ForegroundColor Green
	try{
		Get-AzureRmSqlServer -ServerName $sqlDatabaseServerName -ResourceGroupName $resourceGroupName}
	catch{
		Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
	
		$sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
		$credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)
	
		$sqlDatabaseServerName = (New-AzureRmSqlServer `
									-ResourceGroupName $resourceGroupName `
									-ServerName $sqlDatabaseServerName `
									-SqlAdministratorCredentials $credential `
									-Location $location).ServerName
		Write-Host "`tThe new SQL database server name is $sqlDatabaseServerName." -ForegroundColor Cyan
	
		Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
		$workstationIPAddress = Invoke-RestMethod $ipAddressRestService
		New-AzureRmSqlServerFirewallRule `
			-ResourceGroupName $resourceGroupName `
			-ServerName $sqlDatabaseServerName `
			-FirewallRuleName "$fireWallRuleName-workstation" `
			-StartIpAddress $workstationIPAddress `
			-EndIpAddress $workstationIPAddress
	
		#To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. 
		#Note that this allows Azure traffic from any Azure subscription to access the server.
		New-AzureRmSqlServerFirewallRule `
			-ResourceGroupName $resourceGroupName `
			-ServerName $sqlDatabaseServerName `
			-FirewallRuleName "$fireWallRuleName-Azureservices" `
			-StartIpAddress "0.0.0.0" `
			-EndIpAddress "0.0.0.0"
	}
	
	#endregion
	
	#region - Create and validate Azure SQL database
	Write-Host "`nCreating an Azure SQL database ..." -ForegroundColor Green
	
	try {
		Get-AzureRmSqlDatabase `
			-ResourceGroupName $resourceGroupName `
			-ServerName $sqlDatabaseServerName `
			-DatabaseName $sqlDatabaseName
	}
	catch {
		Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
		New-AzureRMSqlDatabase `
			-ResourceGroupName $resourceGroupName `
			-ServerName $sqlDatabaseServerName `
			-DatabaseName $sqlDatabaseName `
			-Edition "Standard" `
			-RequestedServiceObjectiveName "S1"
	}
	
	#endregion
	
	#region - Create tables
	Write-Host "Creating the log4jlogs table and the mobiledata table ..." -ForegroundColor Green
	
	$conn = New-Object System.Data.SqlClient.SqlConnection
	$conn.ConnectionString = $sqlDatabaseConnectionString
	$conn.Open()
	
	# Create the log4jlogs table and index
	$cmd = New-Object System.Data.SqlClient.SqlCommand
	$cmd.Connection = $conn
	$cmd.CommandText = $cmdCreateLog4jTable
	$ret = $cmd.ExecuteNonQuery()
	$cmd.CommandText = $cmdCreateLog4jClusteredIndex
	$cmd.ExecuteNonQuery()
	
	# Create the mobiledata table and index
	$cmd.CommandText = $cmdCreateMobileTable
	$cmd.ExecuteNonQuery()
	$cmd.CommandText = $cmdCreateMobileDataClusteredIndex
	$cmd.ExecuteNonQuery()
	
	$conn.close()
	
	#endregion
	
	
	#region - Create HDInsight cluster
	
	Write-Host "Creating the HDInsight cluster and the dependent services ..." -ForegroundColor Green
	
	# Create the default storage account
	New-AzureRmStorageAccount `
		-ResourceGroupName $resourceGroupName `
		-Name $defaultStorageAccountName `
		-Location $location `
		-Type Standard_LRS
	
	# Create the default Blob container
	$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
									-ResourceGroupName $resourceGroupName `
									-Name $defaultStorageAccountName)[0].Value
	$defaultStorageAccountContext = New-AzureStorageContext `
										-StorageAccountName $defaultStorageAccountName `
										-StorageAccountKey $defaultStorageAccountKey 
	New-AzureStorageContainer `
		-Name $defaultBlobContainerName `
		-Context $defaultStorageAccountContext 
	
	# Create the HDInsight cluster
	$pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
	$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)
	
	New-AzureRmHDInsightCluster `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $HDInsightClusterName `
		-Location $location `
		-ClusterType Hadoop `
		-OSType Windows `
		-ClusterSizeInNodes 2 `
		-HttpCredential $httpCredential `
		-DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
		-DefaultStorageAccountKey $defaultStorageAccountKey `
		-DefaultStorageContainer $defaultBlobContainerName 
	
	# Validate the cluster
	Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName
	#endregion
	
	#region - pre-process the source file
	
	Write-Host "Preprocessing the source file ..." -ForegroundColor Green
	
	# This procedure creates a new file with $destBlobName
	$sourceBlobName = "example/data/sample.log"
	$destBlobName = "tutorials/usesqoop/data/sample.log"
	
	# Define the connection string
	$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"
	
	# Create block blob objects referencing the source and destination blob.
	$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
	$storageClient = $storageAccount.CreateCloudBlobClient();
	$storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
	$sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
	$destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
	
	# Define a MemoryStream and a StreamReader for reading from the source file
	$stream = New-Object System.IO.MemoryStream
	$stream = $sourceBlob.OpenRead()
	$sReader = New-Object System.IO.StreamReader($stream)
	
	# Define a MemoryStream and a StreamWriter for writing into the destination file
	$memStream = New-Object System.IO.MemoryStream
	$writeStream = New-Object System.IO.StreamWriter $memStream
	
	# Pre-process the source blob
	$exString = "java.lang.Exception:"
	while(-Not $sReader.EndOfStream){
		$line = $sReader.ReadLine()
		$split = $line.Split(" ")
	
		# remove the "java.lang.Exception" from the first element of the array
		# for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
		if ($split[0] -eq $exString){
			#create a new ArrayList to remove $split[0]
			$newArray = [System.Collections.ArrayList] $split
			$newArray.Remove($exString)
	
			# update $split and $line
			$split = $newArray
			$line = $newArray -join(" ")
		}
	
		# remove the lines that has less than 7 elements
		if ($split.count -ge 7){
			write-host $line
			$writeStream.WriteLine($line)
		}
	}
	
	# Write to the destination blob
	$writeStream.Flush()
	$memStream.Seek(0, "Begin")
	$destBlob.UploadFromStream($memStream)
	
	#endregion
	
	#region - export a log file from the cluster to the SQL database
	
	Write-Host "Preprocessing the source file ..." -ForegroundColor Green
	
	$tableName_log4j = "log4jlogs"
	
	# Connection string for Azure SQL Database.
	# Comment if using SQL Server
	$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"
	# Connection string for SQL Server.
	# Uncomment if using SQL Server.
	#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$sqlDatabaseName"
	
	$exportDir_log4j = "/tutorials/usesqoop/data"
	
	# Submit a Sqoop job
	$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
		-Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
	$sqoopJob = Start-AzureRmHDInsightJob `
					-ClusterName $hdinsightClusterName `
					-HttpCredential $httpCredential `
					-JobDefinition $sqoopDef #-Debug -Verbose
	Wait-AzureRmHDInsightJob `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $hdinsightClusterName `
		-HttpCredential $httpCredential `
		-JobId $sqoopJob.JobId
	
	Write-Host "Standard Error" -BackgroundColor Green
	Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardError
	Write-Host "Standard Output" -BackgroundColor Green
	Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardOutput
	
	#endregion
	
	#region - export a Hive table
	
	$tableName_mobile = "mobiledata"
	$exportDir_mobile = "/hive/warehouse/hivesampletable"
	
	$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
		-Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"
	$sqoopJob = Start-AzureRmHDInsightJob `
					-ClusterName $hdinsightClusterName `
					-HttpCredential $httpCredential `
					-JobDefinition $sqoopDef #-Debug -Verbose
	
	Wait-AzureRmHDInsightJob `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $hdinsightClusterName `
		-HttpCredential $httpCredential `
		-JobId $sqoopJob.JobId
	
	Write-Host "Standard Error" -BackgroundColor Green
	Get-AzureRmHDInsightJobOutput `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $hdinsightClusterName `
		-DefaultStorageAccountName $defaultStorageAccountName `
		-DefaultStorageAccountKey $defaultStorageAccountKey `
		-DefaultContainer $defaultBlobContainerName `
		-HttpCredential $httpCredential `
		-JobId $sqoopJob.JobId `
		-DisplayOutputType StandardError
	
	Write-Host "Standard Output" -BackgroundColor Green
	Get-AzureRmHDInsightJobOutput `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $hdinsightClusterName `
		-DefaultStorageAccountName $defaultStorageAccountName `
		-DefaultStorageAccountKey $defaultStorageAccountKey `
		-DefaultContainer $defaultBlobContainerName `
		-HttpCredential $httpCredential `
		-JobId $sqoopJob.JobId `
		-DisplayOutputType StandardOutput
	
	#endregion
	
	#region - import a database
	
	$targetDir_mobile = "/tutorials/usesqoop/importeddata/"
	
	$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
		-Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"
	
	$sqoopJob = Start-AzureRmHDInsightJob `
					-ClusterName $hdinsightClusterName `
					-HttpCredential $httpCredential `
					-JobDefinition $sqoopDef #-Debug -Verbose
	
	Wait-AzureRmHDInsightJob `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $hdinsightClusterName `
		-HttpCredential $httpCredential `
		-JobId $sqoopJob.JobId
	
	Write-Host "Standard Error" -BackgroundColor Green
	Get-AzureRmHDInsightJobOutput `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $hdinsightClusterName `
		-DefaultStorageAccountName $defaultStorageAccountName `
		-DefaultStorageAccountKey $defaultStorageAccountKey `
		-DefaultContainer $defaultBlobContainerName `
		-HttpCredential $httpCredential `
		-JobId $sqoopJob.JobId `
		-DisplayOutputType StandardError
	
	Write-Host "Standard Output" -BackgroundColor Green
	Get-AzureRmHDInsightJobOutput `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $hdinsightClusterName `
		-DefaultStorageAccountName $defaultStorageAccountName `
		-DefaultStorageAccountKey $defaultStorageAccountKey `
		-DefaultContainer $defaultBlobContainerName `
		-HttpCredential $httpCredential `
		-JobId $sqoopJob.JobId `
		-DisplayOutputType StandardOutput
	
	#endregion



[azure-management-portal]: https://portal.azure.com/

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database/sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

<!---HONumber=AcomDC_0525_2016-->