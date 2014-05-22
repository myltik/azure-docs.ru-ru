<properties linkid="manage-services-hdinsight-use-sqoop" urlDisplayName="Использование Sqoop с примерами HDInsight" pageTitle="Использование Sqoop с HDInsight | Azure" metaKeywords="" description="Узнайте, как использовать Azure PowerShell с рабочей станции для запуска импорта и экспорта Sqoop между кластером HDInsight и базой данных Azure SQL." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Использование Sqoop с HDInsight" authors="jgao" />

# Использование Sqoop с HDInsight
 
Узнайте, как использовать Azure PowerShell и HDInsight .NET SDK с рабочей станции для запуска импорта и экспорта Sqoop между кластером HDInsight и базой данных Azure SQL.

**Предполагаемое время выполнения:** 30 минут


##Содержание

- [Что такое Sqoop?](#whatissqoop)
- [Предварительные требования](#prerequisites)
- [Ознакомление со сценарием учебника](#scenario)
- [Подготовка к работе с учебником](#prepare)
- [Использование PowerShell для выполнения экспорта Sqoop](#export)
- [Использование HDInsight SDK для выполнения экспорта Sqoop](#export-sdk)
- [Использование PowerShell для выполнения импорта Sqoop](#import)
- [Дальнейшие действия](#nextsteps)


## <a id="whatissqoop"></a> Что такое Sqoop?

Хотя Hadoop оптимально подходит для обработки частично структурированных и неструктурированных данных, таких как журналы и файлы, может существовать необходимость обработки структурированных данных, хранящихся в реляционных базах данных.

[Sqoop][sqoop-user-guide-1.4.4] — это средство, предназначенное для передачи данных между кластерами Hadoop и реляционными базами данных. С его помощью можно импортировать данные из системы управления реляционными базами данных (РСУБД), например SQL, MySQL или Oracle, в распределенную файловую систему Hadoop (HDFS), преобразовать данные в Hadoop с использованием MapReduce или Hive, а затем экспортировать данные обратно в РСУБД. В этом учебнике для реляционной базы данных используется база данных SQL.

Сведения о поддерживаемых версиях Sqoop в кластерах HDInsight см. в разделе [Новые возможности версий кластеров, предоставляемых HDInsight][hdinsight-versions].




##<a id="prerequisites"></a>Предварительные требования

Перед началом работы с этим учебником необходимо иметь следующее:

- **Рабочая станция**, на которой установлена и настроена среда Azure PowerShell. Инструкции см. в разделе [Установка и настройка Azure PowerShell][powershell-install]. Для выполнения скриптов PowerShell необходимо запустить Azure PowerShell с правами администратора и задать политику выполнения *RemoteSigned*. См. раздел [Выполнение скриптов Windows PowerShell][powershell-script].

- **Кластер Azure HDInsight**. Инструкции по подготовке кластеров см. в разделе [Приступая к работе с HDInsight][hdinsight-get-started] или [Подготовка кластеров HDInsight][hdinsight-provision]. Для выполнения учебника необходимы следующие данные:

	<table border="1">
	<tr><th>Свойство кластера</th><th>Имя переменной PowerShell</th><th>Значение</th><th>Описание</th></tr>
	<tr><td>Имя кластера HDInsight</td><td>$clusterName</td><td></td><td>Это имя вашего кластера HDInsight.</td></tr>
	<tr><td>Имя учетной записи хранения Azure</td><td>$storageAccountName</td><td></td><td>Учетная запись хранения Azure, доступная для кластера HDInsight. В этом учебнике используйте учетную запись хранения по умолчанию, указанную в процессе подготовки кластера.</td></tr>
	<tr><td>Имя контейнера BLOB-объектов Azure</td><td>$containerName</td><td></td><td>Для этого примера применяйте контейнер хранилища BLOB-объектов Azure, используемый для файловой системы кластера HDInsight по умолчанию. По умолчанию его имя совпадает с именем кластера HDInsight.</td></tr>
	</table>

- **База данных SQL Azure**. Необходимо настроить правило брандмауэра для сервера базы данных SQL, чтобы разрешить доступ к рабочей станции. Инструкции по созданию базы данных SQL и настройке брандмауэра см. в разделе [Приступая к работе с базой данных SQL Azure][sqldatabase-get-started]. Эта статья содержит скрипт PowerShell для создания таблицы базы данных SQL, необходимой в рамках этого учебника. 

	<table border="1">
	<tr><th>Свойство базы данных SQL</th><th>Имя переменной PowerShell</th><th>Значение</th><th>Описание</th></tr>
	<tr><td>Имя сервера базы данных SQL</td><td>$sqlDatabaseServer</td><td></td><td>Сервер базы данных SQL, на который Sqoop будет экспортировать данные или с которого будут импортироваться данные. </td></tr>
	<tr><td>Имя входа базы данных SQL</td><td>$sqlDatabaseLogin</td><td></td><td>Имя входа базы данных SQL.</td></tr>
	<tr><td>Пароль для входа базы данных SQL</td><td>$sqlDatabasePassword</td><td></td><td>Пароль для входа базы данных SQL.</td></tr>
	<tr><td>Имя базы данных SQL</td><td>$sqlDatabaseName</td><td></td><td>База данных SQL Azure, на которую Sqoop будет экспортировать данные или из которой будут импортироваться данные. </td></tr>
	</table>

	> [WACOM.NOTE] По умолчанию база данных SQL Azure разрешает подключения из служб Azure, таких как Azure HDInsight. Если этот параметр брандмауэра отключен, вы должны включить его на портале управления Azure. Инструкции по созданию базы данных SQL и настройке правил брандмауэра см. в разделе [Создание и настройка базы данных SQL][sqldatabase-create-configue]. 


> [WACOM.NOTE] Введите значения в таблицы.  Это будет полезно при прохождении данного учебника.

## <a id="scenario"></a>Ознакомление со сценарием
Кластер HDInsight имеет несколько примеров данных. Будут использоваться два следующих примера:

- Файл журнала log4j, расположенный в */example/data/sample.log*. Из файла извлекаются следующие журналы:

		2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
		2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
		2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
		...

- Таблица Hive с именем *hivesampletable*, которая ссылается на файл данных, расположенный в */hive/warehouse/hivesampletable*. Эта таблица содержит некоторые данные о мобильных устройствах. Схема таблицы Hive:

	<table border="1">
	<tr><th>Поле</th><th>Тип данных</th></tr>
	<tr><td>clientid</td><td>string</td></tr>
	<tr><td>querytime</td><td>string</td></tr>
	<tr><td>market</td><td>string</td></tr>
	<tr><td>deviceplatform</td><td>string</td></tr>
	<tr><td>devicemake</td><td>string</td></tr>
	<tr><td>devicemodel</td><td>string</td></tr>
	<tr><td>state</td><td>string</td></tr>
	<tr><td>country</td><td>string</td></tr>
	<tr><td>querydwelltime</td><td>double</td></tr>
	<tr><td>sessionid</td><td>bigint</td></tr>
	<tr><td>sessionpagevieworder</td><td>bigint</td></tr>
	</table>

Сначала вы экспортируете sample.log и hivesampletable в базу данных SQL, а затем импортируете таблицу с данными о мобильных устройствах обратно в HDInsight, используя следующий путь:

	/tutorials/usesqoop/importeddata

###Общие сведения о хранилище HDInsight

HDInsight использует для хранения данных хранилище BLOB-объектов Azure.  Оно называется *WASB* или *Хранилище Azure - BLOB-объекты*. WASB — это реализация HDFS на базе хранилища BLOB-объектов Azure от корпорации Майкрософт. Дополнительные сведения см. в разделе [Использование хранилища BLOB-объектов Azure с HDInsight][hdinsight-storage]. 

В процессе подготовки кластера HDInsight в качестве файловой системы по умолчанию устанавливаются учетная запись хранения Azure и конкретный контейнер хранилища BLOB-объектов из этой учетной записи, так же как и в HDFS. Помимо этой учетной записи хранения в процессе подготовки можно добавить дополнительные учетные записи хранения из той же подписки Azure или других подписок Azure. Инструкции по добавлению дополнительных учетных записей хранения см. в разделе [Подготовка кластеров HDInsight][hdinsight-provision]. Чтобы упростить скрипт PowerShell, используемый в этом учебнике, все файлы хранятся в контейнере файловой системы по умолчанию, расположенном в */tutorials/usesqoop*. Имя контейнера по умолчанию совпадает с именем кластера HDInsight. 
Синтаксис WASB:

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] Только синтаксис *wasb://* поддерживается в кластере HDInsight версии 3.0. Старый синтаксис *asv://* поддерживается в кластерах HDInsight 2.1 и 1.6, однако он не поддерживается в кластерах HDInsight 3.0 и не будет поддерживаться в более поздних версиях.

> [WACOM.NOTE] Путь WASB является виртуальным.  Дополнительные сведения см. в разделе [Использование хранилища BLOB-объектов Azure с HDInsight][hdinsight-storage]. 

Доступ к файлу, хранящемуся в контейнере файловой системы по умолчанию, из HDInsight может осуществляться с помощью любого из следующих URI (sample.log используется в качестве примера):

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
	wasb:///example/data/sample.log
	/example/data/sample.log

Если требуется доступ к этому файлу непосредственно из учетной записи хранения, имя BLOB-объекта для файла имеет следующее значение:

	example/data/sample.log


##<a id="prepare"></a>Подготовка к работе с учебником

Вы создадите две таблицы базы данных SQL, используемые для экспорта Sqoop позднее в этом учебнике. Также необходимо предварительно обработать файл sample.log перед его обработкой средством Sqoop.

**Создание таблиц базы данных SQL**

1. Откройте интегрированную среду сценариев Windows PowerShell (на начальном экране Windows 8 введите **PowerShell_ISE** и щелкните элемент **Интегрированная среда сценариев Windows PowerShell**. См. раздел [Запуск Windows PowerShell в Windows 8 и Windows][powershell-start]).

2. Скопируйте следующий скрипт в область скриптов и задайте первые шесть переменных:
		
		#SQL database variables
		$sqlDatabaseServer = "<SQLDatabaseServerName>" 
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "<SQLDatabasePassword>"
		$sqlDatabaseName = "<SQLDatabaseName>" 

		$sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"

	Дополнительные описания переменных см. в разделе [Предварительные требования](#prerequisites) данного учебника. 

3. Добавьте следующее содержимое в скрипт в области скриптов. Это инструкции SQL, определяющие две таблицы и их кластеризованные индексы.  База данных SQL требует кластеризованный индекс.

		# SQL query strings for creating tables and clustered indexes
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

4. Добавьте следующий скрипт в область скриптов для выполнения команд SQL:

		Write-Host "Connect to the SQL Database ..." -ForegroundColor Green
		$conn = New-Object System.Data.SqlClient.SqlConnection
		$conn.ConnectionString = $sqlDatabaseConnectionString
		$conn.Open()
		
		Write-Host "Create log4j table and clustered index ..." -ForegroundColor Green
		$cmd = New-Object System.Data.SqlClient.SqlCommand
		$cmd.Connection = $conn
		$cmd.CommandText = $cmdCreateLog4jTable
		$ret = $cmd.ExecuteNonQuery()
		$cmd.CommandText = $cmdCreateLog4jClusteredIndex
		$cmd.ExecuteNonQuery()
		
		Write-Host "Create log4j table and clustered index ..." -ForegroundColor Green
		$cmd.CommandText = $cmdCreateMobileTable
		$cmd.ExecuteNonQuery()
		$cmd.CommandText = $cmdCreateMobileDataClusteredIndex
		$cmd.ExecuteNonQuery()
		
		Write-Host "Close connection ..." -ForegroundColor Green		
		$conn.close()
		
		Write-Host "Done" -ForegroundColor Green
	
5. Нажмите кнопку **Запустить скрипт** или нажмите клавишу **F5** для выполнения скрипта. 
6. Используйте [портал управления Azure][azure-management-portal] для анализа таблиц и кластеризованных индексов.

В этом учебнике вы экспортируете файл журнала log4j (файл с разделителями) и таблицу Hive в базу данных SQL.  Файл с разделителями — */example/data/sample.log*. Ранее в учебнике вы видели несколько примеров журналов log4j. В файле журнала существует несколько пустых строк, а некоторые другие строки похожи на следующее:

	java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
		at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)

Может понадобиться удалить *java.lang.Exception:* из строки, чтобы запись могла быть проанализирована правильно. 

Экспорт Sqoop не будет выполнен, если присутствует пустая строка или строка, число элементов в которой меньше количества полей, определенных в таблице базы данных SQL. Таблица log4jlogs содержит 7 полей типа "строка".

**Предварительная обработка файла sample.log**

1. Откройте интегрированную среду сценариев Windows PowerShell.
2. В нижней области выполните следующую команду для подключения к подписке Azure:

		Add-AzureAccount

	Появится запрос на ввод учетных данных учетной записи Azure. Этот метод добавления подключения подписки имеет срок действия, и после 12 часов вам потребуется снова выполнить вход. 

	> [WACOM.NOTE] Если имеется несколько подписок Azure и должна использоваться подписка, отличная от подписки по умолчанию, воспользуйтесь командлетом <strong>Select-AzureSubscription</strong> для выбора текущей подписки.

3. Скопируйте следующий скрипт в область скриптов и задайте первые две переменные:
		
		$storageAccountName = "<WindowsAzureStorageAccountName>"
		$containerName = "<BlobContainerName>"
		
		$sourceBlobName = "example/data/sample.log"
		$destBlobName = "tutorials/usesqoop/data/sample.log"

	Дополнительные описания переменных см. в разделе [Предварительные требования](#prerequisites) данного учебника. 
 
4. Добавьте следующее содержимое в скрипт в области скриптов:

		# Define the connection string
		$storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
		
		# Create block blob objects referencing the source and destination blob.
		$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
		$storageClient = $storageAccount.CreateCloudBlobClient();
		$storageContainer = $storageClient.GetContainerReference($containerName)
		$sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
		$destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
		
		# Define a MemoryStream and a StreamReader for reading from the source file
		$stream = New-Object System.IO.MemoryStream
		$stream = $sourceBlob.OpenRead()
		$sReader = New-Object System.IO.StreamReader($stream)
		
		# Define a MemoryStream and a StreamWriter for writing into the destination file
		$memStream = New-Object System.IO.MemoryStream
		$writeStream = New-Object System.IO.StreamWriter $memStream
		
		# process the source blob
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

5. Нажмите кнопку **Запустить скрипт** или нажмите клавишу **F5** для выполнения скрипта.  
6. Чтобы проанализировать измененный файл данных, можно использовать портал управления Azure, средство обозревателя хранилища Azure или Azure PowerShell.  В разделе [Приступая к работе с HDInsight][hdinsight-get-started] есть пример кода, где PowerShell используется для загрузки файла и отображения его содержимого.










































































##<a id="export"></a>Использование PowerShell для выполнения экспорта Sqoop

В этом разделе вы будете использовать Azure PowerShell для выполнения команды экспорта Sqoop для экспорта таблицы Hive и файла данных Sqoop в базу данных SQL Azure. Следующий раздел содержит пример HDInsight .NET.

**Экспорт файла журнала log4j**

1. Откройте интегрированную среду сценариев Windows PowerShell.
2. В нижней области выполните следующую команду для подключения к подписке Azure:

		Add-AzureAccount

	Появится запрос на ввод учетных данных учетной записи Azure.

3. Скопируйте следующий скрипт в область скриптов и задайте первые семь переменных:

		# Define the cluster variables
		$clusterName = "<HDInsightClusterName>"
		$storageAccountName = "<WindowsAzureStorageAccount>"
		$containerName = "<BlobStorageContainerName>"
		
		# Define the SQL database variables
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "SQLDatabasePassword>"
		$databaseName = "<SQLDatabaseName>"

		$tableName_log4j = "log4jlogs"
		
		$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
		
		$exportDir_log4j = "/tutorials/usesqoop/data"
	
	Дополнительные описания переменных см. в разделе [Предварительные требования](#prerequisites) данного учебника. 

	Обратите внимание, что $exportDir_log4j не имеет указанного имени файла sample.log. Sqoop будет экспортировать данные из всех файлов в этой папке.

4. Добавьте следующее содержимое в скрипт в области скриптов:

		# Submit a Sqoop job
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
		
		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

	Обратите внимание, что в качестве разделителя полей используется **\0x20**, то есть пробел. Разделитель определяется в файле sample.log в скрипте PowerShell предварительной обработки. Чтобы узнать о **-m 1**, см. [руководство пользователя Sqoop][sqoop-user-guide-1.4.4].

5. Нажмите кнопку **Запустить скрипт** или нажмите клавишу **F5** для выполнения скрипта.  
6. Используйте [портал управления Azure][azure-management-portal] для анализа экспортированных данных.

**Экспорт таблицы hivesampletable Hive**

1. Откройте интегрированную среду сценариев Windows PowerShell.
2. В нижней области выполните следующую команду для подключения к подписке Azure:

		Add-AzureAccount

	Появится запрос на ввод учетных данных учетной записи Azure.

3. Скопируйте следующий скрипт в область скриптов и задайте первые семь переменных:

		# Define the cluster variables
		$clusterName = "<HDInsightClusterName>"
		$storageAccountName = "<WindowsAzureStorageAccount>"
		$containerName = "<BlobStorageContainerName>"
		
		# Define the SQL database variables
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "SQLDatabasePassword>"
		$databaseName = "SQLDatabaseName"

		$tableName_mobile = "mobiledata"
		
		$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
		
		$exportDir_mobile = "/hive/warehouse/hivesampletable"
	
	Дополнительные описания переменных см. в разделе [Предварительные требования](#prerequisites) данного учебника. 

4. Добавьте следующее содержимое в скрипт в области скриптов:
		
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"
		
		
		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
		
		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5. Нажмите кнопку **Запустить скрипт** или нажмите клавишу **F5** для выполнения скрипта.   
6. Используйте [портал управления Azure][azure-management-portal] для анализа экспортированных данных.



##<a id="export-sdk"></a>Использование HDInsight .NET SDK для выполнения экспорта Sqoop

Ниже приведен пример на C#, использующий пакет SDK .NET HDInsight для выполнения экспорта Sqoop. Общие сведения об использовании пакета HDInsight .NET SDK см. в разделе [Отправка заданий Hadoop программными средствами][hdinsight-submit-jobs].


	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using System.IO;
	using System.Threading;
	using System.Security.Cryptography.X509Certificates;
	using Microsoft.WindowsAzure.Management.HDInsight;
	using Microsoft.Hadoop.Client;
	
	namespace sqoopSDKSample
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            // Set the variables
	            string subscriptionID = "<WindowsAzureSubscriptionID>";
	            string clusterName = "<HDInsightClusterName>";
	            string certFriendlyName = "<WindowsAzureCertificateFriendlyName>";
	            string sqlDatabaseServerName = "<SQLDatabaseServerName>";
	            string sqlDatabaseLogin = "<SQLDatabaseLogin>" + "@" + sqlDatabaseServerName;
	            string sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
	            string sqlDatabaseDatabaseName = "hdisqoop";
	            string sqlDatabaseTableName = "log4jlogs";
	
	            cmdExport = @"export";
	            cmdExport = cmdExport + @" --connect jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
	            cmdExport = cmdExport + @" --table " + sqlDatabaseTableName;
	            cmdExport = cmdExport + @" --export-dir /tutorials/usesqoop/data";
	            cmdExport = cmdExport + @" --input-fields-terminated-by \0x20 -m 1";
	
	            SqoopJobCreateParameters sqoopJobDefinition = new SqoopJobCreateParameters()
	            {
	                Command = cmdExport,
	                StatusFolder = "/tutorials/usesqoop/jobStatus"
	            };
	
	            // Get the certificate object from certificate store using the friendly name to identify it
	            X509Store store = new X509Store();
	            store.Open(OpenFlags.ReadOnly);
	            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
	            JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);
	
	            // Submit the Hive job
	            var jobClient = JobSubmissionClientFactory.Connect(creds);
	            JobCreationResults jobResults = jobClient.CreateSqoopJob(sqoopJobDefinition);
	
	            // Wait for the job to complete
	            WaitForJobCompletion(jobResults, jobClient);
	
	            // Print the Hive job output
	            System.IO.Stream stream = jobClient.GetJobErrorLogs(jobResults.JobId);
	
	            StreamReader reader = new StreamReader(stream);
	            Console.WriteLine(reader.ReadToEnd());
	
	            Console.WriteLine("Press ENTER to continue.");
	            Console.ReadLine();
	        }
	
	        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
	        {
	            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
	            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
	            {
	                jobInProgress = client.GetJob(jobInProgress.JobId);
	                Thread.Sleep(TimeSpan.FromSeconds(10));
	            }
	        }
	    }
	}

Чтобы выполнить файл скрипта, можно заменить:

	Command = cmdExport,

 на:

	File = "/tutorials/usesqoop/sqoopexport.txt",

Этот файл скрипта должен находиться в WASB.




##<a id="import"></a>Использование PowerShell для выполнения импорта Sqoop

В этом разделе вы будете импортировать журналы log4j (экспортированные в базу данных SQL) обратно в HDInsight.

1. Откройте интегрированную среду сценариев Windows PowerShell.
2. В нижней области выполните следующую команду для подключения к подписке Azure:

		Add-AzureAccount

	Появится запрос на ввод учетных данных учетной записи Azure.

3. Скопируйте следующий скрипт в область скриптов и задайте первые семь переменных:

		# Define the cluster variables
		$clusterName = "<HDInsightClusterName>"
		$storageAccountName = "<WindowsAzureStorageAccount>"
		$containerName = "<BlobStorageContainerName>"
		
		# Define the SQL database variables
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "SQLDatabasePassword>"
		$databaseName = "SQLDatabaseName"

		$tableName_log4j = "log4jlogs"
		
		$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
		
		$tableName_mobile = "mobiledata"
		$targetDir_mobile = "/tutorials/usesqoop/importeddata/"
	
	Дополнительные описания переменных см. в разделе [Предварительные требования](#prerequisites) данного учебника. 

4. Добавьте следующее содержимое в скрипт в области скриптов:
	
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"
		
		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
		
		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5. Нажмите кнопку **Запустить скрипт** или нажмите клавишу **F5** для выполнения скрипта. 
6. Чтобы проанализировать измененный файл данных, можно использовать портал управления Azure, средство обозревателя хранилища Azure или Azure PowerShell.  В разделе [Приступая к работе с HDInsight][hdinsight-get-started] есть пример кода, где PowerShell используется для загрузки файла и отображения его содержимого.

##<a id="nextsteps"></a>Дальнейшие действия

Теперь вы узнали, как использовать Sqoop. Дополнительные сведения см. на следующих ресурсах:

- [Использование Oozie с HDInsight][hdinsight-oozie]: используйте действие Sqoop в процессе Oozie.
- [Анализ данных о задержке рейсов с помощью HDInsight][hdinsight-analyze-flight-delay-data]: используйте Hive для анализа данных о задержке рейсов, а затем используйте Sqoop для экспорта данных в базу данных SQL.
- [Передача данных в HDInsight][hdinsight-upload-data]: узнайте о других способах отправки данных в HDInsight/хранилище BLOB-объектов Azure.


 

[azure-management-portal]: https://manage.windowsazure.com/

[hdinsight-versions]:  ../hdinsight-component-versioning/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-analyze-flight-delay-data]: ../hdinsight-analyze-flight-delay-data/
[hdinsight-oozie]: ../hdinsight-use-oozie/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/

[sqldatabase-get-started]: ../sql-database-get-started/
[sqldatabase-create-configue]: ../sql-database-create-configure/

[powershell-start]: http://technet.microsoft.com/ru-ru/library/hh847889.aspx
[powershell-install]: ../install-configure-powershell
[powershell-script]: http://technet.microsoft.com/ru-ru/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html


