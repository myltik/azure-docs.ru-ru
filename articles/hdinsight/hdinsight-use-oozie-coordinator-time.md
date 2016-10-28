<properties
	pageTitle="Использование временного координатора Oozie с Hadoop в Azure HDInsight | Microsoft Azure"
	description="Использование временного координатора Oozie с Hadoop в Azure HDInsight — службы для работы с данными большого размера. Узнайте, как определить рабочие процессы и координаторы в Oozie, а также как отправлять задания."
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/25/2016"
	ms.author="jgao"/>


# Используйте учитывающий время координатор Oozie с Hadoop в HDInsight для определения рабочих процессов и координации заданий

Узнайте, как определять рабочие процессы и координаторы, а также как по времени запускать задания координатора. Перед чтением этой статьи рекомендуется изучить [Использование Oozie с HDInsight][hdinsight-use-oozie]. Помимо Oozie, задания можно планировать с помощью фабрики данных Azure. Для получения сведений о фабрике данных Azure см. статью [Использование Pig и Hive с фабрикой данных](../data-factory/data-factory-data-transformation-activities.md).

> [AZURE.NOTE] В этой статье требуется кластер HDInsight на основе Windows. Дополнительные сведения об использовании Oozie, включая задания на основе времени, в кластере под управлением Linux см. в разделе[Использование Oozie с Hadoop для определения и запуска рабочих процессов в HDInsight под управлением Linux](hdinsight-use-oozie-linux-mac.md)

##Что такое Oozie

Apache Oozie — это система рабочих процессов и координации, управляющая заданиями Hadoop. Это решение интегрировано со стеком Hadoop и поддерживает задания Hadoop Apache MapReduce, Apache Pig, Apache Hive и Apache Sqoop. Его также можно использовать для планирования относящихся к системе заданий, например Java-программ и сценариев оболочки.

На следующем рисунке показан рабочий процесс, который вам потребуется реализовывать:

![Схема рабочих процессов][img-workflow-diagram]

Рабочий процесс содержит два действия:

1. Действие Hive запускает сценарий HiveQL для подсчета экземпляров каждого типа уровня журнала в файле журнала log4j. Каждый журнал log4j состоит из строки полей, содержащей поле [LOG LEVEL] для отображения типа и серьезности, например:

		2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
		2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
		2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
		...

	Выходные данные скрипта Hive аналогичны приведенным ниже:

		[DEBUG] 434
		[ERROR] 3
		[FATAL] 1
		[INFO]  96
		[TRACE] 816
		[WARN]  4

	Дополнительные сведения о Hive см. в разделе [Использование Hive с HDInsight][hdinsight-use-hive].

2.  Действие Sqoop экспортирует выходные данные действия HiveQL в таблицу в базе данных SQL Azure. Дополнительные сведения о Sqoop см. в статье [Использование Sqoop с HDInsight][hdinsight-use-sqoop].

> [AZURE.NOTE] Информацию о поддерживаемых версиях Oozie в кластерах HDInsight см. в статье [Новые возможности версий кластеров, предоставляемых HDInsight][hdinsight-versions].


##Предварительные требования

Перед началом работы с этим учебником необходимо иметь следующее:

- **Рабочая станция с Azure PowerShell.**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- **Кластер HDInsight**. Сведения о создании кластера HDInsight см. в статьях [Создание кластеров Hadoop под управлением Windows в HDInsight][hdinsight-provision] и [Руководство по Hadoop. Начало работы с Hadoop в HDInsight на платформе Linux][hdinsight-get-started]. Для выполнения учебника необходимы следующие данные:

	<table border = "1">
	<tr><th>Свойство кластера</th><th>Имя переменной Windows PowerShell</th><th>Значение</th><th>Описание</th></tr>
	<tr><td>Имя кластера HDInsight.</td><td>$clusterName</td><td></td><td>Кластер HDInsight, на котором будет выполняться данный учебник.</td></tr>
	<tr><td>Имя пользователя кластера HDInsigh</td><td>$clusterUsername</td><td></td><td>Имя пользователя кластера HDInsight. </td></tr>
	<tr><td>Пароль пользователя кластера HDInsight. </td><td>$clusterPassword</td><td></td><td>Пароль пользователя кластера HDInsight.</td></tr>
	<tr><td>Имя учетной записи хранения Azure</td><td>$storageAccountName</td><td></td><td>Учетная запись хранения Azure, доступная для кластера HDInsight. В этом учебнике используйте учетную запись хранения по умолчанию, указанную в процессе подготовки кластера.</td></tr>
	<tr><td>Имя контейнера BLOB-объектов Azure</td><td>$containerName</td><td></td><td>Для этого примера применяйте контейнер хранилища BLOB-объектов Azure, используемый для файловой системы кластера HDInsight по умолчанию. По умолчанию его имя совпадает с именем кластера HDInsight.</td></tr>
	</table>

- **База данных SQL Azure.** Необходимо настроить правило брандмауэра для сервера базы данных SQL, чтобы разрешить доступ к рабочей станции. Инструкции по созданию базы данных Azure SQL и настройке брандмауэра см. в статье [Приступая к работе с базой данных SQL Azure][sqldatabase-get-started]. Эта статья включает сценарий Windows PowerShell для создания таблицы базы данных Azure SQL, необходимой в рамках этого учебника.

	<table border = "1">
	<tr><th>Свойство базы данных SQL</th><th>Имя переменной Windows PowerShell</th><th>Значение</th><th>Описание</th></tr>
	<tr><td>Имя сервера базы данных SQL</td><td>$sqlDatabaseServer</td><td></td><td>Сервер базы данных SQL, куда Sqoop экспортирует данные. </td></tr>
	<tr><td>Имя для входа базы данных SQL</td><td>$sqlDatabaseLogin</td><td></td><td>Имя для входа базы данных SQL</td></tr>
	<tr><td>Пароль для входа базы данных SQL</td><td>$sqlDatabaseLoginPassword</td><td></td><td>Пароль для входа базы данных SQL</td></tr>
	<tr><td>Имя базы данных SQL</td><td>$sqlDatabaseName</td><td></td><td>База данных Azure SQL, куда Sqoop экспортирует данные. </td></tr>
	</table>

	> [AZURE.NOTE] По умолчанию в базе данных SQL Azure разрешены подключения из служб Azure, в частности из службы Azure HDInsight. Если этот параметр брандмауэра отключен, нужно включить его на портале Azure. Инструкции по созданию базы данных SQL и настройке правил брандмауэра см. в статье [Создание и настройка базы данных SQL][sqldatabase-get-started].


> [AZURE.NOTE] Введите значения в таблицы. Это будет полезно при прохождении данного учебника.


##Определение рабочего процесса Oozie и связанного с ним скрипта HiveQL

Определения рабочих процессов Oozie записываются в hPDL (язык определения процессов XML). Для файла рабочего процесса по умолчанию используется имя *workflow.xml*. Позднее в этом учебнике вы сохраните файл рабочего процесса в локальной среде и развернете его в кластере HDInsight с помощью Azure PowerShell.

Действие Hive в рабочем процессе вызывает файл скрипта HiveQL. Этот файл скрипта содержит три инструкции HiveQL:

1. **Инструкция DROP TABLE** удаляет таблицу log4j Hive, если она существует.
2. **Инструкция CREATE TABLE** создает внешнюю таблицу log4j Hive, указывающую на местоположение файла журнала log4j.
3.  **Расположение файла журнала log4j.** Разделителем поля является ",". Разделителем строки по умолчанию является "\\n". Внешняя таблица Hive используется для предотвращения удаления файла данных из исходного расположения данных в случае, когда требуется запустить рабочий процесс Oozie несколько раз.
3. **Инструкция INSERT OVERWRITE** подсчитывает экземпляры каждого типа уровня журнала в таблице log4j Hive и сохраняет выходные данные в расположение хранилища BLOB-объектов Azure.

**Примечание.** Существует известная проблема с путем к Hive. Эта проблема возникает при отправке задания Oozie. Инструкции по устранению этой проблемы можно найти на вики-сайте TechNet [Ошибка Hive HDInsight: не удается переименовать][technetwiki-hive-error].

**Определение файла сценария HiveQL, вызываемого рабочим процессом**

1. Создайте текстовый файл со следующим содержимым:

		DROP TABLE ${hiveTableName};
		CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
		INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

	Существует три переменные, используемые в данном скрипте:

	- ${hiveTableName}
	- ${hiveDataFolder}
	- ${hiveOutputFolder}

	Файл определения рабочего процесса (workflow.xml в этом учебнике) передает эти значения в данный сценарий HiveQL во время выполнения.

2. Сохраните файл как **C:\\Tutorials\\UseOozie\\useooziewf.hql** в кодировке ANSI (ASCII). (Используйте Блокнот, если ваш текстовый редактор не предоставляет такую возможность.) Позже в этом учебнике этот файл сценария будет развернут в кластере HDInsight.



**Определение рабочего процесса**

1. Создайте текстовый файл со следующим содержимым:

		<workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
		    <start to = "RunHiveScript"/>

		    <action name="RunHiveScript">
		        <hive xmlns="uri:oozie:hive-action:0.2">
		            <job-tracker>${jobTracker}</job-tracker>
		            <name-node>${nameNode}</name-node>
		            <configuration>
		                <property>
		                    <name>mapred.job.queue.name</name>
		                    <value>${queueName}</value>
		                </property>
		            </configuration>
		            <script>${hiveScript}</script>
			    	<param>hiveTableName=${hiveTableName}</param>
		            <param>hiveDataFolder=${hiveDataFolder}</param>
		            <param>hiveOutputFolder=${hiveOutputFolder}</param>
		        </hive>
		        <ok to="RunSqoopExport"/>
		        <error to="fail"/>
		    </action>

		    <action name="RunSqoopExport">
		        <sqoop xmlns="uri:oozie:sqoop-action:0.2">
		            <job-tracker>${jobTracker}</job-tracker>
		            <name-node>${nameNode}</name-node>
		            <configuration>
		                <property>
		                    <name>mapred.compress.map.output</name>
		                    <value>true</value>
		                </property>
		            </configuration>
			    <arg>export</arg>
			    <arg>--connect</arg>
			    <arg>${sqlDatabaseConnectionString}</arg>
			    <arg>--table</arg>
			    <arg>${sqlDatabaseTableName}</arg>
			    <arg>--export-dir</arg>
			    <arg>${hiveOutputFolder}</arg>
			    <arg>-m</arg>
			    <arg>1</arg>
			    <arg>--input-fields-terminated-by</arg>
			    <arg>"\001"</arg>
		        </sqoop>
		        <ok to="end"/>
		        <error to="fail"/>
		    </action>

		    <kill name="fail">
		        <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
		    </kill>

		   <end name="end"/>
		</workflow-app>

	Существует два действия, определенные в рабочем процессе. Первым действием является *RunHiveScript*. Если действие выполняется со статусом *ОК*, запускается следующее действие (*RunSqoopExport*).

	RunHiveScript имеет несколько переменных. Вы передадите эти значения при отправке задания Oozie с рабочей станции с помощью Azure PowerShell.

	<table border = "1">
	<tr><th>Переменные рабочего процесса</th><th>Описание</th></tr>
	<tr><td>${jobTracker}</td><td>Укажите URL-адрес средства отслеживания заданий Hadoop. Используйте <strong>jobtrackerhost:9010</strong> в кластере HDInsight версии 3.0 и 2.0.</td></tr>
	<tr><td>${nameNode}</td><td>Укажите URL-адрес узла имен заданий Hadoop. Используйте стандартный адрес wasbs:// для файловой системы, например <i>wasbs://&lt;имя_контейнера>@&lt;имя_учетной_записи_хранения>.blob.core.windows.net</i>.</td></tr>
	<tr><td>${queueName}</td><td>Указывает имя очереди, в которую будет отправлено задание. Используйте <strong>значение по умолчанию</strong>.</td></tr>
	</table>


	<table border = "1">
	<tr><th>Переменная действия Hive</th><th>Описание</th></tr>
	<tr><td>${hiveDataFolder}</td><td>Исходный каталог для команды создания таблицы Hive.</td></tr>
	<tr><td>${hiveOutputFolder}</td><td>Папка результатов для инструкции INSERT OVERWRITE.</td></tr>
	<tr><td>${hiveTableName}</td><td>Имя таблицы Hive, ссылающейся на файлы данных log4j.</td></tr>
	</table>


	<table border = "1">
	<tr><th>Переменная действия Sqoop</th><th>Описание</th></tr>
	<tr><td>${sqlDatabaseConnectionString}</td><td>Строка подключения для базы данных SQL.</td></tr>
	<tr><td>${sqlDatabaseTableName}</td><td>Таблица базы данных  Azure SQL, в которую будут экспортированы данные.</td></tr>
	<tr><td>${hiveOutputFolder}</td><td>Папка результатов для инструкции Hive INSERT OVERWRITE. Это та же папка, что и каталог для экспорта Sqoop (export-dir).</td></tr>
	</table>

	Дополнительные сведения о рабочем процессе Oozie и использовании его действий см. в [документации на Apache Oozie 4.0][apache-oozie-400] \(для кластера HDInsight версии 3.0) или [документации на Apache Oozie 3.3.2][apache-oozie-332] \(для кластера HDInsight версии 2.1).

2. Сохраните файл как **C:\\Tutorials\\UseOozie\\workflow.xml** в кодировке ANSI (ASCII). (Используйте Блокнот, если ваш текстовый редактор не предоставляет такую возможность.)

**Определение координатора**

1. Создайте текстовый файл со следующим содержимым:

		<coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
		   <action>
		      <workflow>
		         <app-path>${wfPath}</app-path>
		      </workflow>
		   </action>
		</coordinator-app>

	В файле определения используется пять переменных:

	| Переменная | Описание |
	| ------------------|------------ |
	| ${coordFrequency} | Время приостановки заданий. Частота всегда выражается в минутах. |
	| ${coordStart} | Время запуска задания. |
	| ${coordEnd} | Время окончания задания. |
    | ${coordTimezone} | Oozie обрабатывает задания координатора в фиксированном часовом поясе без перехода на летнее время (обычно представляется в виде времени в формате UTC). Этот часовой пояс называется часовым поясом обработки Oozie. |
	| ${wfPath} | Путь для workflow.xml. Если имя файла рабочего процесса не является именем файла по умолчанию (workflow.xml), необходимо его указать. |

2. Сохраните файл как **C:\\Tutorials\\UseOozie\\coordinator.xml** в кодировке ANSI (ASCII). (Используйте Блокнот, если ваш текстовый редактор не предоставляет такую возможность.)

##Развертывание проекта Oozie и подготовка учебника

Вы выполните скрипт Azure PowerShell для достижения следующих результатов:

- Скопируйте сценарий HiveQL (useoozie.hql) в хранилище BLOB-объектов Azure (wasbs:///tutorials/useoozie/useoozie.hql).
- Скопируйте workflow.xml в wasbs:///tutorials/useoozie/workflow.xml.
- Скопируйте coordinator.xml в wasbs:///tutorials/useoozie/coordinator.xml.
- Скопируйте файл данных (/example/data/sample.log в wasbs:///tutorials/useoozie/data/sample.log.
- Создайте таблицу базы данных Azure SQL для хранения данных экспорта Sqoop. Имя таблицы — *log4jLogCount*.

**Общие сведения о хранилище HDInsight**

HDInsight использует хранилище BLOB-объектов Azure для хранения данных. wasbs:// является реализацией распределенной файловой системы Hadoop (HDFS) в хранилище BLOB-объектов Azure, предоставляемой корпорацией Майкрософт. Дополнительные сведения см. в разделе [Использование хранилища BLOB-объектов Azure с HDInsight][hdinsight-storage].

В процессе подготовки кластера HDInsight в качестве файловой системы по умолчанию устанавливаются учетная запись хранения BLOB-объектов Azure и конкретный контейнер из этой учетной записи, так же как и в HDFS. Помимо этой учетной записи хранения в процессе подготовки можно добавить дополнительные учетные записи хранения из той же или других подписок Azure. Инструкции по добавлению дополнительных учетных записей хранения см. в разделе [Подготовка кластеров HDInsight][hdinsight-provision]. Чтобы упростить сценарий Azure PowerShell, используемый в этом учебнике, все файлы хранятся в контейнере файловой системы по умолчанию, расположенном в */tutorials/useoozie*. Имя контейнера по умолчанию совпадает с именем кластера HDInsight. Синтаксис:

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [AZURE.NOTE] Кластер HDInsight версии 3.0 поддерживает только синтаксис *wasb://*. Прежний синтаксис *asv://* поддерживается в кластерах HDInsight 2.1 и 1.6, однако не поддерживается в кластерах HDInsight 3.0.

> [AZURE.NOTE] Путь wasb:// является виртуальным. Дополнительные сведения см. в разделе [Использование хранилища BLOB-объектов Azure с HDInsight][hdinsight-storage].

Доступ к файлу, хранящемуся в контейнере файловой системы по умолчанию, из HDInsight может осуществляться с помощью любого из следующих URI (workflow.xml используется в качестве примера):

	wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
	wasbs:///tutorials/useoozie/workflow.xml
	/tutorials/useoozie/workflow.xml

Если требуется доступ к этому файлу непосредственно из учетной записи хранения, имя BLOB-объекта для файла имеет следующее значение:

	tutorials/useoozie/workflow.xml

**Общие сведения о внутренней и внешней таблицах Hive**

Есть несколько вещей, которые нужно знать о внутренней и внешней таблицах Hive.

- Команда CREATE TABLE создает внутреннюю таблицу, также известную как управляемая таблица. Файл данных должен находиться в контейнере по умолчанию.
- Команда CREATE TABLE перемещает файл данных в папку /hive/warehouse/<имя\_таблицы> в контейнере по умолчанию.
- Команда CREATE EXTERNAL TABLE создает внешнюю таблицу. Файл данных может находиться за пределами контейнера по умолчанию.
- Команда CREATE EXTERNAL TABLE не перемещает файл данных.
- Команда CREATE EXTERNAL TABLE не позволяет использовать вложенные папки в папке, указанной в предложении LOCATION. Именно по этой причине в учебнике создается копия файла sample.log.

Дополнительные сведения см. в статье [HDInsight: общие сведения о внутренних и внешних таблицах Hive][cindygross-hive-tables].

**Подготовка учебника**

1. Откройте интегрированную среду сценариев Windows PowerShell (на начальном экране Windows 8 введите **PowerShell\_ISE** и затем щелкните **Интегрированная среда сценариев Windows PowerShell**. См. раздел [Запуск Windows PowerShell в Windows 8 и Windows][powershell-start] для получения более подробной информации).
2. В нижней области выполните следующую команду для подключения к подписке Azure.

		Add-AzureAccount

	Появится запрос на ввод учетных данных учетной записи Azure. Этот метод добавления подключения по подписке имеет срок действия, и после 12 часов вам потребуется снова выполнить командлет.

	> [AZURE.NOTE] Если имеется несколько подписок Azure и должна использоваться подписка, отличная от подписки по умолчанию, воспользуйтесь командлетом <strong>Select-AzureSubscription</strong> для выбора подписки.

3. Скопируйте следующий сценарий в область сценариев и задайте первые шесть переменных

		# WASB variables
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<BlobStorageContainerName>"

		# SQL database variables
		$sqlDatabaseServer = "<SQLDatabaseServerName>"  
		$sqlDatabaseLogin = "<SQLDatabaseLoginName>"
		$sqlDatabaseLoginPassword = "SQLDatabaseLoginPassword>"
		$sqlDatabaseName = "<SQLDatabaseName>"  
		$sqlDatabaseTableName = "log4jLogsCount"

		# Oozie files for the tutorial
		$hiveQLScript = "C:\Tutorials\UseOozie\useooziewf.hql"
		$workflowDefinition = "C:\Tutorials\UseOozie\workflow.xml"
		$coordDefinition =  "C:\Tutorials\UseOozie\coordinator.xml"

		# WASB folder for storing the Oozie tutorial files.
		$destFolder = "tutorials/useoozie"  # Do NOT use the long path here


	Дополнительные описания переменных см. в разделе [Предварительные требования](#prerequisites) данного учебника.

3. Добавьте следующее содержимое в скрипт в области скриптов:

		# Create a storage context object
		$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

		function uploadOozieFiles()
		{
		    Write-Host "Copy HiveQL script, workflow definition and coordinator definition ..." -ForegroundColor Green
			Set-AzureStorageBlobContent -File $hiveQLScript -Container $containerName -Blob "$destFolder/useooziewf.hql" -Context $destContext
			Set-AzureStorageBlobContent -File $workflowDefinition -Container $containerName -Blob "$destFolder/workflow.xml" -Context $destContext
			Set-AzureStorageBlobContent -File $coordDefinition -Container $containerName -Blob "$destFolder/coordinator.xml" -Context $destContext
		}

		function prepareHiveDataFile()
		{
			Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green
			Start-CopyAzureStorageBlob -SrcContainer $containerName -SrcBlob "example/data/sample.log" -Context $destContext -DestContainer $containerName -destBlob "$destFolder/data/sample.log" -DestContext $destContext
		}

		function prepareSQLDatabase()
		{
			# SQL query string for creating log4jLogsCount table
			$cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
				    [Level] [nvarchar](10) NOT NULL,
				    [Total] float,
				CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
				(
				[Level] ASC
				)
				)"

			#Create the log4jLogsCount table
		    Write-Host "Create Log4jLogsCount table ..." -ForegroundColor Green
			$conn = New-Object System.Data.SqlClient.SqlConnection
			$conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
			$conn.open()
			$cmd = New-Object System.Data.SqlClient.SqlCommand
			$cmd.connection = $conn
			$cmd.commandtext = $cmdCreateLog4jCountTable
			$cmd.executenonquery()

			$conn.close()
		}

		# upload workflow.xml, coordinator.xml, and ooziewf.hql
		uploadOozieFiles;

		# make a copy of example/data/sample.log to example/data/log4j/sample.log
		prepareHiveDataFile;

		# create log4jlogsCount table on SQL database
		prepareSQLDatabase;

4. Для выполнения скрипта щелкните **Выполнить скрипт** или нажмите клавишу **F5**. Результат должен быть аналогичен приведенному ниже:

	![Выходные данные подготовки учебника][img-preparation-output]

##Запуск проекта Oozie

В настоящее время Azure PowerShell не предоставляет командлеты для определения заданий Oozie. Вы можете использовать командлет **Invoke-RestMethod** для вызова веб-служб Oozie. API веб-служб Oozie представляет собой API HTTP REST JSON. Дополнительные сведения об API веб-служб Oozie см. в [документации на Apache Oozie 4.0][apache-oozie-400] \(для кластера HDInsight версии 3.0) или [документации на Apache Oozie 3.3.2][apache-oozie-332] \(для кластера HDInsight версии 2.1).

**Отправка задания Oozie**

1. Откройте интегрированную среду сценариев Windows PowerShell (на начальном экране Windows 8 введите **PowerShell\_ISE** и затем щелкните **Интегрированная среда сценариев Windows PowerShell**. См. раздел [Запуск Windows PowerShell в Windows 8 и Windows][powershell-start] для получения более подробной информации).

3. Скопируйте следующий сценарий в область сценариев и задайте первые четырнадцать переменных (пропустите **$storageUri**).

		#HDInsight cluster variables
		$clusterName = "<HDInsightClusterName>"
		$clusterUsername = "<HDInsightClusterUsername>"
		$clusterPassword = "<HDInsightClusterUserPassword>"

		#Azure Blob storage (WASB) variables
		$storageAccountName = "<StorageAccountName>"
		$storageContainerName = "<BlobContainerName>"
		$storageUri="wasbs://$storageContainerName@$storageAccountName.blob.core.windows.net"

		#Azure SQL database variables
		$sqlDatabaseServer = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseLoginName>"
		$sqlDatabaseLoginPassword = "<SQLDatabaseloginPassword>"
		$sqlDatabaseName = "<SQLDatabaseName>"  

		#Oozie WF/coordinator variables
		$coordStart = "2014-03-21T13:45Z"
		$coordEnd = "2014-03-21T13:45Z"
		$coordFrequency = "1440"	# in minutes, 24h x 60m = 1440m
		$coordTimezone = "UTC"	#UTC/GMT

		$oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
		$waitTimeBetweenOozieJobStatusCheck=10

		#Hive action variables
		$hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
		$hiveTableName = "log4jlogs"
		$hiveDataFolder = "$storageUri/tutorials/useoozie/data"
		$hiveOutputFolder = "$storageUri/tutorials/useoozie/output"

		#Sqoop action variables
		$sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServer.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServer;password=$sqlDatabaseLoginPassword;database=$sqlDatabaseName"
		$sqlDatabaseTableName = "log4jLogsCount"

		$passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

	Дополнительные описания переменных см. в разделе [Предварительные требования](#prerequisites) данного учебника.

	$coordstart и $coordend — это время начала и окончания рабочего процесса. Чтобы узнать время UTC/GMT, выполните поиск "время utc" с помощью bing.com. Переменная $coordFrequency определяет, через сколько минут следует выполнить рабочий процесс.

3. Добавьте следующее содержимое в скрипт. Эта часть определяет полезные данные Oozie:

		#OoziePayload used for Oozie web service submission
		$OoziePayload =  @"
		<?xml version="1.0" encoding="UTF-8"?>
		<configuration>

		   <property>
		       <name>nameNode</name>
		       <value>$storageUrI</value>
		   </property>

		   <property>
		       <name>jobTracker</name>
		       <value>jobtrackerhost:9010</value>
		   </property>

		   <property>
		       <name>queueName</name>
		       <value>default</value>
		   </property>

		   <property>
		       <name>oozie.use.system.libpath</name>
		       <value>true</value>
		   </property>

		   <property>
		       <name>oozie.coord.application.path</name>
		       <value>$oozieWFPath</value>
		   </property>

		   <property>
		       <name>wfPath</name>
		       <value>$oozieWFPath</value>
		   </property>

		   <property>
		       <name>coordStart</name>
		       <value>$coordStart</value>
		   </property>

		   <property>
		       <name>coordEnd</name>
		       <value>$coordEnd</value>
		   </property>

		   <property>
		       <name>coordFrequency</name>
		       <value>$coordFrequency</value>
		   </property>

		   <property>
		       <name>coordTimezone</name>
		       <value>$coordTimezone</value>
		   </property>

		   <property>
		       <name>hiveScript</name>
		       <value>$hiveScript</value>
		   </property>

		   <property>
		       <name>hiveTableName</name>
		       <value>$hiveTableName</value>
		   </property>

		   <property>
		       <name>hiveDataFolder</name>
		       <value>$hiveDataFolder</value>
		   </property>

		   <property>
		       <name>hiveOutputFolder</name>
		       <value>$hiveOutputFolder</value>
		   </property>

		   <property>
		       <name>sqlDatabaseConnectionString</name>
		       <value>";$sqlDatabaseConnectionString";</value>
		   </property>

		   <property>
		       <name>sqlDatabaseTableName</name>
		       <value>$SQLDatabaseTableName</value>
		   </property>

		   <property>
		       <name>user.name</name>
		       <value>admin</value>
		   </property>

		</configuration>
		"@

	>[AZURE.NOTE] Основным различием по сравнению с файлом полезных данных отправки рабочего процесса является переменная **oozie.coord.application.path**. При отправке задания рабочего процесса используйте вместо нее **oozie.wf.application.path**.

4. Добавьте следующее содержимое в скрипт. Эта часть проверяет состояние веб-служб Oozie:

		function checkOozieServerStatus()
		{
		    Write-Host "Checking Oozie server status..." -ForegroundColor Green
		    $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
		    $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus

		    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
		    $oozieServerSatus = $jsonResponse[0].("systemMode")
		    Write-Host "Oozie server status is $oozieServerSatus..."

		    if($oozieServerSatus -notmatch "NORMAL")
		    {
		        Write-Host "Oozie server status is $oozieServerSatus...cannot submit Oozie jobs. Check the server status and re-run the job."
		        exit 1
		    }
		}

5. Добавьте следующее содержимое в скрипт. Эта часть создает задание Oozie:

		function createOozieJob()
		{
		    # create Oozie job
		    Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
		    Write-Host "`n--------`n$OoziePayload`n--------"
		    $clusterUriCreateJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
		    $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $creds -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName -debug -Verbose

		    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
		    $oozieJobId = $jsonResponse[0].("id")
		    Write-Host "Oozie job id is $oozieJobId..."

		    return $oozieJobId
		}

	> [AZURE.NOTE] При отправке задания рабочего процесса необходимо вызвать другую веб-службу, чтобы запустить задание после его создания. В этом случае задание координатора запускается по времени. Задание будет запускаться автоматически.

6. Добавьте следующее содержимое в скрипт. Эта часть проверяет состояние задания Oozie:

		function checkOozieJobStatus($oozieJobId)
		{
		    # get job status
		    Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
		    Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck

		    Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
		    $clusterUriGetJobStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
		    $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds
		    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
		    $JobStatus = $jsonResponse[0].("status")

		    while($JobStatus -notmatch "SUCCEEDED|KILLED")
		    {
		        Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
		        Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
		        $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds
		        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
		        $JobStatus = $jsonResponse[0].("status")
		    }

		    Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!"
		    if($JobStatus -notmatch "SUCCEEDED")
		    {
		        Write-Host "Check logs at http://headnode0:9014/cluster for detais."
		        exit -1
		    }
		}

7. (Необязательно) Добавьте следующее содержимое в скрипт.

		function listOozieJobs()
		{
		    Write-Host "Listing Oozie jobs..." -ForegroundColor Green
		    $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
		    $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds

		    write-host "Job ID                                   App Name        Status      Started                         Ended"
		    write-host "----------------------------------------------------------------------------------------------------------------------------------"
		    foreach($job in $response.workflows)
		    {
		        Write-Host $job.id "`t" $job.appName "`t" $job.status "`t" $job.startTime "`t" $job.endTime
		    }
		}

		function ShowOozieJobLog($oozieJobId)
		{
		    Write-Host "Showing Oozie job info..." -ForegroundColor Green
		    $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/$oozieJobId" + "?show=log"
		    $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds
		    write-host $response
		}

		function killOozieJob($oozieJobId)
		{
		    Write-Host "Killing the Oozie job $oozieJobId..." -ForegroundColor Green
		    $clusterUriStartJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=kill" #Valid values for the 'action' parameter are 'start', 'suspend', 'resume', 'kill', 'dryrun', 'rerun', and 'change'.
		    $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $creds | Format-Table -HideTableHeaders -debug
		}

7. Добавьте следующее содержимое в скрипт.

		checkOozieServerStatus
		# listOozieJobs
		$oozieJobId = createOozieJob($oozieJobId)
		checkOozieJobStatus($oozieJobId)
		# ShowOozieJobLog($oozieJobId)
		# killOozieJob($oozieJobId)

	Удалите значки #, если нужно выполнить дополнительные функции.

7. При наличии кластера HDinsight версии 2.1 замените "https://$clusterName.azurehdinsight.net:443/oozie/v2/" на "https://$clusterName.azurehdinsight.net:443/oozie/v1/". Кластер HDInsight версии 2.1 не поддерживает веб-службы версии 2.

7. Для выполнения скрипта щелкните **Выполнить скрипт** или нажмите клавишу **F5**. Результат должен быть аналогичен приведенному ниже:

	![Выходные данные запуска рабочего процесса в учебнике][img-runworkflow-output]

8. Подключитесь к базе данных SQL для просмотра экспортированных данных.

**Проверка журнала ошибок задания**

Для устранения неполадок рабочего процесса можно использовать файл журнала Oozie C:\\apps\\dist\\oozie-3.3.2.1.3.2.0-05\\oozie-win-distro\\logs\\Oozie.log на головном узле кластера. Дополнительные сведения об RDP см. в статье [Управление кластерами Hadoop в HDInsight с помощью портала Azure][hdinsight-admin-portal].

**Повторное выполнение учебника**

Чтобы запустить рабочий процесс повторно:

- Удалите файл выходных данных сценария Hive.
- Удалите данные в таблице log4jLogsCount.

Ниже приведен пример сценария PowerShell, который вы можете использовать:

	$storageAccountName = "<AzureStorageAccountName>"
	$containerName = "<ContainerName>"

	#SQL database variables
	$sqlDatabaseServer = "<SQLDatabaseServerName>"
	$sqlDatabaseLogin = "<SQLDatabaseLoginName>"
	$sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>"
	$sqlDatabaseName = "<SQLDatabaseName>"
	$sqlDatabaseTableName = "log4jLogsCount"

	Write-host "Delete the Hive script output file ..." -ForegroundColor Green
	$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
	Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $containerName

	Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
	$conn = New-Object System.Data.SqlClient.SqlConnection
	$conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
	$conn.open()
	$cmd = New-Object System.Data.SqlClient.SqlCommand
	$cmd.connection = $conn
	$cmd.commandtext = "delete from $sqlDatabaseTableName"
	$cmd.executenonquery()

	$conn.close()


##Дальнейшие действия
В этом руководстве вы узнали, как определить рабочий процесс Oozie и координатор Oozie и как выполнять задания координатора Oozie с помощью Azure PowerShell. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

- [Приступая к работе с HDInsight][hdinsight-get-started]
- [Использование хранилища больших двоичных объектов Azure с HDInsight][hdinsight-storage]
- [Администрирование HDInsight с помощью Azure PowerShell][hdinsight-admin-powershell]
- [Отправка данных в HDInsight][hdinsight-upload-data]
- [Использование Sqoop с HDInsight][hdinsight-use-sqoop]
- [Использование Hive с HDInsight][hdinsight-use-hive]
- [Использование Pig с HDInsight][hdinsight-use-pig]
- [Разработка программ MapReduce на Java для HDInsight][hdinsight-develop-java-mapreduce]



[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md


[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-develop-java-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md

[sqldatabase-get-started]: ../sql-database/sql-database-get-started.md

[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: ../powershell-install-configure.md
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.RunCoord.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

<!---HONumber=AcomDC_0914_2016-->