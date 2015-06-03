<properties 
	pageTitle="Подготовка Hadoop Sqoop в HDInsight для платформы Azure" 
	description="Вы узнаете, как использовать Azure PowerShell с рабочей станции для запуска Sqoop, импорта и экспорта между кластером HDInsight и базой данных Azure SQL." 
	editor="cgronlun" 
	manager="paulettm" 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="jgao"/>

#Использование Sqoop с Hadoop в HDInsight
 
Узнайте, как использовать Azure PowerShell и SDK HDInsight .NET с рабочей станции для запуска Sqoop и импорта или экспорта между кластером HDInsight и базой данных SQL Azure либо SQL Server.


##Что такое Sqoop?

Хотя Hadoop оптимально подходит для обработки частично структурированных и неструктурированных данных, таких как журналы и файлы, может существовать необходимость обработки структурированных данных, хранящихся в реляционных базах данных.

[Sqoop][sqoop-user-guide-1.4.4] — это средство, предназначенное для передачи данных между кластерами Hadoop и реляционными базами данных. С его помощью можно импортировать данные из системы управления реляционной базой данных (реляционной СУБД), например SQL Server, MySQL или Oracle, в распределенную файловую систему Hadoop (HDFS), преобразовать данные в системе Hadoop с использованием MapReduce или Hive, а затем экспортировать данные обратно в реляционную СУБД. В этом учебнике в качестве реляционной базы данных используется база данных SQL.

Сведения о версиях Sqoop, поддерживаемых в кластерах HDInsight, см. в разделе [Новые возможности версий кластеров, образованных с помощью HDInsight][hdinsight-versions].




##Предварительные требования

Перед началом работы с этим учебником необходимо иметь следующее:

- **Рабочая станция**: компьютер, на котором установлена и настроена среда Azure PowerShell. Инструкции см. в разделе [Установка и настройка Azure PowerShell][powershell-install]. Для выполнения сценариев Azure PowerShell необходимо запустить Azure PowerShell с правами администратора и задать политику выполнения *RemoteSigned*. См. раздел [Выполнение скриптов Windows PowerShell][powershell-script].

- **Кластер Azure HDInsight**: указания по подготовке кластеров см. в статье [Приступая к работе с HDInsight][hdinsight-get-started] или [Подготовка кластеров HDInsight к работе][hdinsight-provision]. Для прохождения учебника необходимы следующие данные:

	<table border="1">
	<tr><th>Свойство кластера</th><th>Имя переменной Azure PowerShell</th><th>Значение</th><th>Описание</th></tr>
	<tr><td>Имя кластера HDInsight.</td><td>$clusterName</td><td></td><td>Имя кластера HDInsight.</td></tr>
	<tr><td>Имя учетной записи хранения Azure</td><td>$storageAccountName</td><td></td><td>Учетная запись хранения Azure, доступная для кластера HDInsight. В этом учебнике используйте учетную запись хранения по умолчанию, указанную в процессе подготовки кластера.</td></tr>
	<tr><td>Имя контейнера BLOB-объектов Azure</td><td>$containerName</td><td></td><td>Для этого примера используйте имя большого двоичного объекта, который применяется для стандартной файловой системы кластера HDInsight. По умолчанию его имя совпадает с именем кластера HDInsight.</td></tr>
	</table>

- **База данных SQL Azure**: необходимо настроить правило брандмауэра для сервера базы данных SQL Azure, чтобы разрешить доступ с рабочей станции. Инструкции по созданию базы данных SQL Azure и настройке брандмауэра см. в статье [Приступая к работе с базой данных SQL Azure][sqldatabase-get-started]. Эта статья включает сценарий Windows PowerShell для создания таблицы базы данных SQL Azure, необходимой в рамках этого учебника.

	<table border="1">
	<tr><th>Свойство базы данных SQL Azure</th><th>Имя переменной Azure PowerShell</th><th>Значение</th><th>Описание</th></tr>
	<tr><td>Имя сервера базы данных SQL Azure</td><td>$sqlDatabaseServer</td><td></td><td>Сервер базы данных SQL Azure, на который Sqoop экспортирует данные или с которого он импортирует их. </td></tr>
	<tr><td>Имя для входа в базу данных SQL Azure</td><td>$sqlDatabaseLogin</td><td></td><td>Имя для входа в базу данных SQL Azure.</td></tr>
	<tr><td>Пароль для входа в базу данных SQL Azure</td><td>$sqlDatabasePassword</td><td></td><td>Пароль для входа в базу данных SQL Azure.</td></tr>
	<tr><td>Имя базы данных SQL Azure</td><td>$sqlDatabaseName</td><td></td><td>Сервер базы данных SQL Azure, на который Sqoop экспортирует данные или с которого он импортирует их. </td></tr>
	</table>

	> [AZURE.NOTE]По умолчанию в базе данных SQL Azure разрешены подключения из служб Azure, таких как Azure HDInsight. Если этот параметр брандмауэра отключен, вы должны включить его на портале Azure. Инструкции по созданию базы данных SQL Azure и настройке правил брандмауэра см. в статье [Создание и настройка базы данных SQL Azure][sqldatabase-create-configue].

* **SQL Server**: если ваш кластер HDInsight находится в той же виртуальной сети Azure, что и SQL Server, эта статья поможет вам разобраться с импортом данных в базу данных SQL Server и их экспортом из нее.

	> [AZURE.NOTE]HDInsight поддерживает только географически привязанные виртуальные сети и на данный момент не работает с виртуальными сетями на основе территориальных групп.

	* Сведения о создании и настройке виртуальной сети см. в статье [Задачи настройки виртуальной сети](http://msdn.microsoft.com/library/azure/jj156206.aspx).

		* При использовании SQL Server в центре обработки данных необходимо настроить тип соединения виртуальной сети либо как *сеть-сеть*, либо *как точка-сеть*.

			> [AZURE.NOTE]Для виртуальных сетей с типом соединения **точка-сеть** на SQL Server должно быть запущено приложение настройки VPN-клиента, которое доступно на **панели мониторинга** в конфигурации виртуальной сети Azure.

		* При использовании SQL Server на виртуальной машине Azure можно использовать любую конфигурацию виртуальной сети, если виртуальная машина с SQL Server находится в той же виртуальной сети, что и HDInsight.

	* Сведения о подготовке кластера HDInsight в виртуальной сети см. в статье [Подготовка кластеров Hadoop в HDInsight с использованием настраиваемых параметров](hdinsight-provision-clusters.md).

	> [AZURE.NOTE] Кроме того, SQL Server должен разрешать проверку подлинности. Для выполнения шагов, описанных в этой статье, вам понадобится использовать данные для входа на SQL Server.

	<table border="1">
	<tr><th>Свойство базы данных SQL Server</th><th>Имя переменной Azure PowerShell</th><th>Значение</th><th>Описание</th></tr>
	<tr><td>Имя сервера SQL</td><td>$sqlDatabaseServer</td><td></td><td>Сервер SQL, куда Sqoop экспортирует данные или откуда импортирует данные. </td></tr>
	<tr><td>Имя пользователя для сервера SQL</td><td>$sqlDatabaseLogin</td><td></td><td>Имя для входа на SQL Server.</td></tr>
	<tr><td>Пароль для входа на SQL Server</td><td>$sqlDatabasePassword</td><td></td><td>Пароль имени для входа на SQL Server.</td></tr>
	<tr><td>Имя базы данных сервера SQL</td><td>$sqlDatabaseName</td><td></td><td>База данных SQL Server, в которую Sqoop экспортирует данные и из которой он импортирует их. </td></tr>
	</table>


> [AZURE.NOTE]Введите значения в предыдущие таблицы. Это будет полезно при прохождении данного учебника.

##Ознакомление со сценарием
Кластер HDInsight включает несколько примеров данных. Вы будете использовать два следующих примера:

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

Сначала вы экспортируете *sample.log* и *hivesampletable* в базу данных SQL Azur или на SQL Server, а затем импортируете таблицу с данными о мобильных устройствах обратно в HDInsight, используя следующий путь:

	/tutorials/usesqoop/importeddata

###Общие сведения о хранилище HDInsight

HDInsight использует для хранения данных хранилище BLOB-объектов Azure. Дополнительные сведения см. в разделе [Использование хранилища больших двоичных объектов Azure с HDInsight][hdinsight-storage].

В процессе подготовки кластера HDInsight в качестве файловой системы по умолчанию задаются учетная запись хранения Azure и конкретный контейнер хранилища больших двоичных объектов из этой учетной записи (как и в HDFS). Помимо этой учетной записи хранения в процессе подготовки вы можете добавить дополнительные учетные записи хранения из той же или других подписок на Azure.

Инструкции по добавлению дополнительных учетных записей хранения см. в разделе [Подготовка кластеров HDInsight][hdinsight-provision]. Чтобы упростить сценарий Windows PowerShell, используемый в этом учебнике, все файлы хранятся в контейнере файловой системы по умолчанию, расположенном в */tutorials/usesqoop*. Имя контейнера по умолчанию совпадает с именем кластера HDInsight. Синтаксис :

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [AZURE.NOTE]Кластер HDInsight версии 3.0 поддерживает только синтаксис *wasb://*. Прежний синтаксис *asv://* поддерживается в кластерах HDInsight 2.1 и 1.6, однако не поддерживается в кластерах HDInsight 3.0.

> [AZURE.NOTE]Путь *wasb://* является виртуальным. Дополнительные сведения см. в разделе [Использование хранилища больших двоичных объектов Azure с HDInsight][hdinsight-storage]. 

Доступ к файлу, который хранится в большом  двоичном объекте файловой системы, используемой по умолчанию, из HDInsight может осуществляться с помощью любого из следующих URI (в последующих примерах используется sample.log):

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
	wasb:///example/data/sample.log
	/example/data/sample.log

Если требуется доступ к этому файлу непосредственно из учетной записи хранения, имя BLOB-объекта для файла имеет следующее значение:

	example/data/sample.log


##Подготовка к работе с учебником

Вам нужно создать две таблицы в базе данных SQL Azure или на SQL Server. Таблицы будут использоваться Sqoop для экспорта далее в учебнике. Также необходимо обработать файл sample.log перед его обработкой средством Sqoop.

###Создание базы данных SQL

**Для базы данных SQL Azure**

1. Откройте интегрированную среду сценариев Windows PowerShell (на начальном экране Windows 8 введите **PowerShell_ISE** и затем щелкните **Интегрированная среда сценариев Windows PowerShell**. См. раздел [Запуск Windows PowerShell в Windows 8 и Windows][powershell-start]).

2. Скопируйте следующий скрипт в область скриптов и задайте первые четыре переменных:
		
		#SQL database variables
		$sqlDatabaseServer = "<SQLDatabaseServerName>" 
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "<SQLDatabasePassword>"
		$sqlDatabaseName = "<SQLDatabaseName>" 

		$sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"

	Дополнительные описания переменных см. в разделе [Предварительные требования](#prerequisites) данного учебника.

3. Добавьте следующий сценарий в область сценариев: Это инструкции SQL, определяющие две таблицы и их кластеризованные индексы. Для базы данных SQL Azure требуется кластеризованный индекс.

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

4. Добавьте следующий сценарий в область сценариев для выполнения команд SQL:

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
	
5. Для выполнения скрипта щелкните **Выполнить скрипт** или нажмите клавишу **F5**.
6. Используйте [портал Аzure][azure-management-portal] для анализа таблиц и кластеризованных индексов.

**Для SQL Server**

1. Откройте **SQL Server Management Studio** и подключитесь к SQL Server.

2. Создайте новую базу данных с именем **sqoopdb**.

3. Выберите базу данных **sqoopdb**, затем выберите **Новый запрос** на панели в верхней части SQL Server Management Studio.

4. Введите следующую информацию в окно запросов:

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

5. Нажмите клавишу **F5** или выберите **! Запустите команду** на панели, чтобы выполнить запрос. Под запросом должно появиться следующее сообщение:

		Command(s) completed successfully.

6. Закройте среду SQL Server Management Studio

###Создание данных

В этом учебнике вы экспортируете файл журнала log4j (файл с разделителями) и таблицу Hive в базу данных SQL Azure. Файл с разделителями — */example/data/sample.log*. Ранее в учебнике вы видели несколько примеров журналов log4j. В файле журнала есть несколько пустых строк и несколько строк, похожих на следующие:

	java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
		at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)

Это нормально для других примеров, которые используют эти данные, но для импорта в базу данных SQL Azure или на SQL Server нам необходимо удалить такие исключения. Экспорт Sqoop не будет выполнен, если присутствует пустая строка или строка, число элементов в которой меньше количества полей, определенных в таблице базы данных SQL Azure. Таблица log4jlogs содержит 7 полей типа "строка".

**Обработка файла sample.log**

1. Откройте интегрированную среду сценариев Windows PowerShell.
2. В нижней области выполните следующую команду для подключения к подписке Azure:

		Add-AzureAccount

	Появится запрос на ввод учетных данных учетной записи Azure. Этот метод добавления подключения подписки имеет срок действия, и после 12 часов вам потребуется снова выполнить вход.

	> [AZURE.NOTE]Если имеется несколько подписок Azure и должна использоваться подписка, отличная от подписки по умолчанию, воспользуйтесь командлетом <strong>Select-AzureSubscription</strong> для выбора текущей подписки.

3. Скопируйте следующий скрипт в область скриптов и задайте первые две переменные:
		
		$storageAccountName = "<AzureStorageAccountName>"
		$containerName = "<BlobContainerName>"
		
		$sourceBlobName = "example/data/sample.log"
		$destBlobName = "tutorials/usesqoop/data/sample.log"

	Дополнительные описания переменных см. в разделе [Предварительные требования](#prerequisites) данного учебника.
 
4. Добавьте следующий сценарий в область сценариев:

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

5. Для выполнения скрипта щелкните **Выполнить скрипт** или нажмите клавишу **F5**.
6. Чтобы проанализировать измененный файл данных, вы можете использовать портал Azure, средство обозревателя службы хранилища Azure или Azure PowerShell. В статье [Приступая к работе с HDInsight][hdinsight-get-started] есть пример кода, где Azure PowerShell используется для скачивания файла и отображения его содержимого.


##Использование PowerShell для выполнения экспорта Sqoop

В этом разделе вы будете использовать Azure PowerShell для выполнения команды экспорта Sqoop с целью экспорта таблицы Hive и файла данных в базу данных SQL Azure или на SQL Server. Следующий раздел содержит пример HDInsight .NET.

> [AZURE.NOTE] Кроме информации в строке соединения, описанные в этом разделе шаги должны подходить как для базы данных SQL Azure, так и для SQL Server. Эти шаги были протестированы для следующей конфигурации:
> 
> * **Конфигурация виртуальной сети Azure типа "точка-сеть"**: кластер HDInsight, подключенный через виртуальную сеть к SQL Server в частном центре обработки данных. Дополнительную информацию см. в разделе [Настройка VPN "точка-сеть" на портале управления](http://msdn.microsoft.com/library/azure/dn133792.aspx) .
> * **Azure HDInsight 3.1**: см. статью [Подготовка кластеров Hadoop в HDInsight с использованием настраиваемых параметров](hdinsight-provision-clusters.md) для получения более подробной информации о создании кластера в виртуальной сети.
> * **SQL Server 2014**: настроенный для разрешения проверки подлинности и использующий пакет настройки VPN-клиента для надежного подключения к виртуальной сети.

**Экспорт файла журнала log4j**

1. Откройте интегрированную среду сценариев Windows PowerShell.
2. В нижней области выполните следующую команду для подключения к подписке Azure:

		Add-AzureAccount

	Появится запрос на ввод учетных данных учетной записи Azure.

3. Скопируйте следующий скрипт в область скриптов и задайте первые семь переменных:

		# Define the cluster variables
		$clusterName = "<HDInsightClusterName>"
		$storageAccountName = "<AzureStorageAccount>"
		$containerName = "<BlobStorageContainerName>"
		
		# Define the SQL database variables
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "<SQLDatabasePassword>"
		$databaseName = "<SQLDatabaseName>"

		$tableName_log4j = "log4jlogs"
		
		# Connection string for Azure SQL Database.
		# Comment if using SQL Server
		$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
		# Connection string for SQL Server.
		# Uncomment if using SQL Server.
		#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$databaseName"
		
		$exportDir_log4j = "/tutorials/usesqoop/data"
	
	Дополнительные описания переменных см. в разделе [Предварительные требования](#prerequisites) данного учебника.

	Обратите внимание на то, что для $exportDirlog4j не указано имя файла sample.log. Sqoop будет экспортировать данные из всех файлов в этой папке.

4. Добавьте следующий сценарий в область сценариев:

		# Submit a Sqoop job
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
		
		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

	Обратите внимание на то, что в качестве разделителя полей используется **\0x20**, то есть пробел. Разделитель определяется в файле sample.log сценария Azure PowerShell. Чтобы узнать о **-m 1**, см. [руководство пользователя Sqoop][sqoop-user-guide-1.4.4].

5. Для выполнения скрипта щелкните **Выполнить скрипт** или нажмите клавишу **F5**.
6. Используйте [портал Azure][azure-management-portal] для анализа экспортированных данных.

**Экспорт таблицы hivesampletable Hive**

1. Откройте интегрированную среду сценариев Windows PowerShell.
2. В нижней области выполните следующую команду для подключения к подписке Azure:

		Add-AzureAccount

	Появится запрос на ввод учетных данных учетной записи Azure.

3. Скопируйте следующий скрипт в область скриптов и задайте первые семь переменных:

		# Define the cluster variables
		$clusterName = "<HDInsightClusterName>"
		$storageAccountName = "<AzureStorageAccount>"
		$containerName = "<BlobStorageContainerName>"
		
		# Define the SQL database variables
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "SQLDatabasePassword>"
		$databaseName = "SQLDatabaseName"

		$tableName_mobile = "mobiledata"
		
		# Connection string for Azure SQL Database.
		# Comment if using SQL Server
		$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
		# Connection string for SQL Server.
		# Uncomment if using SQL Server
		#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$databaseName"
		
		$exportDir_mobile = "/hive/warehouse/hivesampletable"
	
	Дополнительные описания переменных см. в разделе [Предварительные требования](#prerequisites) данного учебника.

4. Добавьте следующий сценарий в область сценариев:
		
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"
		
		
		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
		
		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5. Для выполнения скрипта щелкните **Выполнить скрипт** или нажмите клавишу **F5**.
6. Используйте [портал Azure][azure-management-portal] для анализа экспортированных данных.



##Использование SDK HDInsight .NET для экспорта Sqoop

Ниже приведен пример на C#, использующий пакет SDK HDInsight .NET для экспорта Sqoop. Общие сведения об использовании пакета SDK HDInsight .NET см. в статье [Отправка заданий Hadoop программными средствами][hdinsight-submit-jobs].


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
	            string subscriptionID = "<AzureSubscriptionID>";
	            string clusterName = "<HDInsightClusterName>";
	            string certFriendlyName = "<AzureCertificateFriendlyName>";
	            string sqlDatabaseServerName = "<SQLDatabaseServerName>";
	            string sqlDatabaseLogin = "<SQLDatabaseLogin>";
	            string sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
	            string sqlDatabaseDatabaseName = "hdisqoop";
	            string sqlDatabaseTableName = "log4jlogs";
	
	            cmdExport = @"export";
				// Connection string for using Azure SQL Database.
				// Comment if using SQL Server
	            cmdExport = cmdExport + @" --connect jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
				// Connection string for using SQL Server.
				// Uncomment if using SQL Server
				//cmdExport = cmdExport + @" --connect jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
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

Этот файл сценария должен находиться в хранилище больших двоичных объектов Azure.




##Использование Azure PowerShell для импорта Sqoop

В этом разделе вы импортируете журналы log4j (экспортированные в базу данных Azure SQL) обратно в HDInsight.

1. Откройте интегрированную среду сценариев Windows PowerShell.
2. В нижней области выполните следующую команду для подключения к подписке Azure:

		Add-AzureAccount

	Появится запрос на ввод учетных данных учетной записи Azure.

3. Скопируйте следующий скрипт в область скриптов и задайте первые семь переменных:

		# Define the cluster variables
		$clusterName = "<HDInsightClusterName>"
		$storageAccountName = "<AzureStorageAccount>"
		$containerName = "<BlobStorageContainerName>"
		
		# Define the SQL database variables
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "SQLDatabasePassword>"
		$databaseName = "SQLDatabaseName"

		$tableName_log4j = "log4jlogs"
		
		# Connection string for Azure SQL Database
		# Comment if using SQL Server
		$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
		# Connection string for SQL Server
		# Uncomment if using SQL Server
		#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$databaseName"
		
		$tableName_mobile = "mobiledata"
		$targetDir_mobile = "/tutorials/usesqoop/importeddata/"
	
	Дополнительные описания переменных см. в разделе [Предварительные требования](#prerequisites) данного учебника.

4. Добавьте следующий сценарий в область сценариев:
	
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"
		
		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
		
		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5. Для выполнения скрипта щелкните **Выполнить скрипт** или нажмите клавишу **F5**.
6. Чтобы проанализировать измененный файл данных, вы можете использовать портал Azure, средство обозревателя службы хранилища Azure или Azure PowerShell. В статье [Приступая к работе с HDInsight][hdinsight-get-started] есть пример кода, где Azure PowerShell используется для скачивания файла и отображения его содержимого.

##Дальнейшие действия

Теперь вы узнали, как использовать Sqoop. Дополнительные сведения см. на следующих ресурсах:

- [Использование Oozie с HDInsight][hdinsight-use-oozie]: используйте действие Sqoop в рабочем процессе Oozie.
- [Анализ данных о задержке рейсов с помощью HDInsight][hdinsight-analyze-flight-data]: используйте Hive для анализа данных о задержке рейсов, а затем используйте Sqoop для экспорта данных в базу данных SQL Azure.
- [Передача данных в HDInsight][hdinsight-upload-data]: узнайте о других способах отправки данных в HDInsight и хранилище больших двоичных объектов Azure.


 

[azure-management-portal]: https://manage.windowsazure.com/

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-get-started.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: sql-database-get-started.md
[sqldatabase-create-configue]: sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: install-configure-powershell.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

<!--HONumber=54-->