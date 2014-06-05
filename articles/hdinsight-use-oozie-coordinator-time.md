<properties linkid="hdinsight-use-time-based-oozie-coordinator-with-hdinsight" urlDisplayName="Использование временного координатора Oozie с HDInsight" pageTitle="Использование временного координатора Oozie с HDInsight | Microsoft Azure" metaKeywords="" description="Использование временного координатора Oozie с HDInsight, решение для больших объемов данных. Узнайте, как определять рабочие процессы и координаторы Oozie, а также как отправлять задания координатора Oozie." metaCanonical="" services="hdinsight" documentationCenter="" title="Использование временного координатора Oozie с HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />


# Использование временного координатора Oozie с HDInsight

Узнайте, как определять рабочие процессы и координаторы, а также как по времени запускать задания координатора заданий. Перед чтением этой статьи рекомендуется изучить [Использование Oozie с HDInsight][hdinsight-oozie]. 



**Предполагаемое время выполнения:** 40 минут

##Содержание

0. [Что такое Oozie](#whatisoozie)
1. [Предварительные требования](#prerequisites)
2. [Определение файла рабочего процесса Oozie](#defineworkflow)
2. [Развертывание проекта Oozie и подготовка учебника](#deploy)
3. [Запуск рабочего процесса](#run)
4. [Дальнейшие действия](#nextsteps)

##<a id="whatisoozie"></a>Что такое Oozie

Apache Oozie — это система рабочих процессов и координации, управляющая заданиями Hadoop. Это решение интегрировано со стеком Hadoop и поддерживает задания Hadoop Apache MapReduce, Apache Pig, Apache Hive и Apache Sqoop.. Его также можно использовать для планирования относящихся к системе заданий, таких как Java-программ и скриптов оболочки.

Рабочий процесс, который вы будете реализовывать, содержит два действия: 

![Схема рабочих процессов][img-workflow-diagram]

1. Действие Hive запускает скрипт HiveQL для подсчета экземпляров каждого типа уровня журнала в файле журнала log4j. Каждый журнал log4j состоит из строки полей, содержащей поле [УРОВЕНЬ ЖУРНАЛА] для отображения типа и серьезности. Например:

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

	Дополнительные сведения о Hive см. в разделе [Использование Hive с HDInsight][hdinsight-hive].
	
2.  Действие Sqoop экспортирует выходные данные действия HiveQL в таблицу в базе данных SQL Azure. Дополнительные сведения о Sqoop см. в разделе [Использование Sqoop с HDInsight][hdinsight-sqoop].

> [WACOM.NOTE] Сведения о поддерживаемых версиях Oozie в кластерах HDInsight см. в разделе [Новые возможности версий кластеров, предоставляемых HDInsight][hdinsight-versions].

> [WACOM.NOTE] В этом учебнике показана работа с кластером HDInsight версии 2.1 и 3.0. Эта статья не проверялась на эмуляторе HDInsight.


##<a id="prerequisites"></a>Предварительные требования

Перед началом работы с этим учебником необходимо иметь следующее:

- **Рабочая станция**, на которой установлена и настроена среда Azure PowerShell. Инструкции см. в разделе [Установка и настройка Azure PowerShell][powershell-install-configure]. Для выполнения скриптов PowerShell необходимо запустить Azure PowerShell с правами администратора и задать политику выполнения *RemoteSigned*. См. раздел [Выполнение скриптов Windows PowerShell][powershell-script].
- **Кластер HDInsight**. Сведения о создании кластера HDInsight см. в разделе [Подготовка кластеров HDInsight][hdinsight-provision] или [Приступая к работе с эмулятором HDInsight][hdinsight-get-started]. Для выполнения учебника необходимы следующие данные:

	<table border = "1">
	<tr><th>Свойство кластера</th><th>Имя переменной PowerShell</th><th>Значение</th><th>Описание</th></tr>
	<tr><td>Имя кластера HDInsight</td><td>$clusterName</td><td></td><td>Кластер HDInsight, в котором будет выполняться этот учебник.</td></tr>
	<tr><td>Имя пользователя кластера HDInsight</td><td>$clusterUsername</td><td></td><td>Имя пользователя кластера HDInsight. </td></tr>
	<tr><td>Пароль пользователя кластера HDInsight </td><td>$clusterPassword</td><td></td><td>Пароль пользователя кластера HDInsight.</td></tr>
	<tr><td>Имя учетной записи хранения Azure</td><td>$storageAccountName</td><td></td><td>Учетная запись хранения Azure, доступная для кластера HDInsight. В этом учебнике используйте учетную запись хранения по умолчанию, указанную в процессе подготовки кластера.</td></tr>
	<tr><td>Имя контейнера BLOB-объектов Azure</td><td>$containerName</td><td></td><td>Для этого примера применяйте контейнер хранилища BLOB-объектов Azure, используемый для файловой системы кластера HDInsight по умолчанию. По умолчанию его имя совпадает с именем кластера HDInsight.</td></tr>
	</table>

- **База данных SQL Azure**. Необходимо настроить правило брандмауэра для сервера базы данных SQL, чтобы разрешить доступ к рабочей станции. Инструкции по созданию базы данных SQL и настройке брандмауэра см. в разделе [Приступая к работе с базой данных SQL Azure][sqldatabase-get-started]. Эта статья содержит скрипт PowerShell для создания таблицы базы данных SQL, необходимой в рамках этого учебника. 

	<table border = "1">
	<tr><th>Свойство базы данных SQL</th><th>Имя переменной PowerShell</th><th>Значение</th><th>Описание</th></tr>
	<tr><td>Имя сервера базы данных SQL</td><td>$sqlDatabaseServer</td><td></td><td>Сервер базы данных SQL, на который Sqoop будет экспортировать данные. </td></tr>
	<tr><td>Имя входа базы данных SQL</td><td>$sqlDatabaseLogin</td><td></td><td>Имя входа базы данных SQL.</td></tr>
	<tr><td>Пароль для входа базы данных SQL</td><td>$sqlDatabaseLoginPassword</td><td></td><td>Пароль для входа базы данных SQL.</td></tr>
	<tr><td>Имя базы данных SQL</td><td>$sqlDatabaseName</td><td></td><td>База данных SQL Azure, в которую Sqoop будет экспортировать данные. </td></tr>
	</table>

	> [WACOM.NOTE] По умолчанию база данных SQL Azure разрешает подключения из служб Azure, таких как Azure HDInsight. Если этот параметр брандмауэра отключен, вы должны включить его на портале управления Azure. Инструкции по созданию базы данных SQL и настройке правил брандмауэра см. в разделе [Создание и настройка базы данных SQL][sqldatabase-create-configue]. 


> [WACOM.NOTE] Введите значения в таблицы.  Это будет полезно при прохождении данного учебника.


##<a id="defineworkflow"></a>Определение рабочего процесса Oozie и связанного с ним скрипта HiveQL

Определения рабочих процессов Oozie записываются в hPDL (язык определения процессов XML). Для файла рабочего процесса по умолчанию используется имя *workflow.xml*.  Позднее в этом учебнике вы сохраните файл рабочего процесса в локальной среде и развернете его в кластере HDInsight с помощью Azure PowerShell.

Действие Hive в рабочем процессе вызывает файл скрипта HiveQL. Этот файл скрипта содержит три инструкции HiveQL:

1. **Инструкция DROP TABLE** удаляет таблицу log4j Hive, если она существует.
2. **Инструкция CREATE TABLE** создает внешнюю таблицу log4j Hive, указывающую на
3.  расположение файла журнала log4j. Разделителем поля является ",". Разделителем строки по умолчанию является "\n".  Внешняя таблица Hive используется для предотвращения удаления файла данных из исходного расположения данных в случае, когда требуется запустить рабочий процесс Oozie несколько раз.
3. **Инструкция INSERT OVERWRITE** подсчитывает экземпляры каждого типа уровня журнала в таблице log4j Hive и сохраняет выходные данные в расположении "Хранилище Azure - BLOB-объекты" (WASB). 

Существует известная проблема с путем Hive. Эта проблема возникает при отправке задания Oozie. Инструкции по устранению этой проблемы можно найти на [вики-сайте TechNet][technetwiki-hive-error].

**Определение файла скрипта HiveQL, вызываемого рабочим процессом:**

1. Создайте текстовый файл со следующим содержимым:

		DROP TABLE ${hiveTableName};
		CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
		INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

	Существует три переменные, используемые в данном скрипте:

	- ${hiveTableName}
	- ${hiveDataFolder}
	- ${hiveOutputFolder}
			
	Файл определения рабочего процесса (workflow.xml в этом учебнике) передает эти значения в данный скрипт HiveQL во время выполнения.
		
2. Сохраните файл как **C:\Tutorials\UseOozie\useooziewf.hql** с кодировкой **ANSI(ASCII)** (если текстовый редактор не позволяет сделать это, используйте Блокнот). Позже в этом учебнике этот файл скрипта будет развернут в кластере HDInsight.



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

	Существует два действия, определенные в рабочем процессе. Первым действием является *RunHiveScript*. Если действие выполняется со статусом *ok*, выполняется следующее действие *RunSqoopExport*.

	RunHiveScript имеет несколько переменных. Вы передадите эти значения при отправке задания Oozie с рабочей станции с помощью Azure PowerShell.

	<table border = "1">
	<tr><th>Переменные рабочего процесса</th><th>Описание</th></tr>
	<tr><td>${jobTracker}</td><td>Укажите URL-адрес средства отслеживания заданий Hadoop. Используйте <strong>jobtrackerhost:9010</strong> в кластере HDInsight версии 2.0 и 3.0.</td></tr>
	<tr><td>${nameNode}</td><td>Укажите URL-адрес узла имен заданий Hadoop. Используйте адрес WASB файловой системы по умолчанию. Например, <i>wasb://&lt;имя_контейнера&gt;@&lt;имя_учетной-записи_хранения&gt;.blob.core.windows.net</i>.</td></tr>
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
	<tr><td>${sqlDatabaseTableName}</td><td>Таблица базы данных SQL, в которую будут экспортированы данные.</td></tr>
	<tr><td>${hiveOutputFolder}</td><td>Папка результатов для инструкции Hive INSERT OVERWRITE. Это та же папка, что и каталог для экспорта Sqoop export-dir.</td></tr>
	</table>

	Дополнительные сведения о рабочем процессе Oozie и использовании его действий см. в разделе [Документация по Apache Oozie 4.0][apache-oozie-400] (для кластера HDInsight версии 3.0) или [Документация по Apache Oozie 3.3.2][apache-oozie-332] (для кластера HDInsight версии 2.1).

2. Сохраните файл как **C:\Tutorials\UseOozie\workflow.xml** с кодировкой ANSI (ASCII) (если текстовый редактор не позволяет сделать это, используйте Блокнот).

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

	Переменная | Описание
	------------------|------------
	${coordFrequency} | Время приостановки заданий. Частота всегда выражается в минутах.
	${coordStart}     | Время запуска задания.
	${coordEnd}       | Время окончания задания.
    ${coordTimezone}  | Oozie обрабатывает задания координатора в фиксированном часовом поясе без DST (обычно UTC), этот часовой пояс называется "Часовой пояс обработки Oozie".
	${wfPath}         | Путь для workflow.xml.  Если имя файла рабочего процесса не является именем файла по умолчанию (workflow.xml), необходимо его указать.
	
2. Сохраните файл как **C:\Tutorials\UseOozie\coordinator.xml** с кодировкой ANSI (ASCII) (если текстовый редактор не позволяет сделать это, используйте Блокнот).
	
##<a id="deploy"></a>Развертывание проекта Oozie и подготовка учебника

Вы выполните скрипт Azure PowerShell для достижения следующих результатов:

- Скопируйте скрипт HiveQL (useoozie.hql) в хранилище BLOB-объектов Azure, wasb:///tutorials/useoozie/useoozie.hql.
- Копирование workflow.xml в wasb:///tutorials/useoozie/workflow.xml.
- Скопируйте workflow.xml в wasb:///tutorials/useoozie/coordinator.xml.
- Копирование файла данных (/example/data/sample.log) в wasb:///tutorials/useoozie/data/sample.log. 
- Создание таблицы базы данных SQL для хранения данных экспорта Sqoop.  Имя таблицы — *log4jLogCount*.

**Общие сведения о хранилище HDInsight**

HDInsight использует для хранения данных хранилище BLOB-объектов Azure.  Это хранилище называется *WASB* или *Хранилище Windows Azure — BLOB-объекты*. WASB — это реализация HDFS на базе хранилища BLOB-объектов Azure от корпорации Майкрософт. Дополнительные сведения см. в разделе [Использование хранилища BLOB-объектов Azure с HDInsight][hdinsight-storage]. 

В процессе подготовки кластера HDInsight в качестве файловой системы по умолчанию устанавливаются учетная запись хранения Azure и конкретный контейнер хранилища BLOB-объектов из этой учетной записи, так же как и в HDFS. Помимо этой учетной записи хранения в процессе подготовки можно добавить дополнительные учетные записи хранения из той же подписки Azure или других подписок Azure. Инструкции по добавлению дополнительных учетных записей хранения см. в разделе [Подготовка кластеров HDInsight][hdinsight-provision]. Чтобы упростить скрипт PowerShell, используемый в этом учебнике, все файлы хранятся в контейнере файловой системы по умолчанию, расположенном в */tutorials/useoozie*. Имя контейнера по умолчанию совпадает с именем кластера HDInsight. 
Синтаксис WASB:

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] Кластер HDInsight версии 3.0 поддерживает синтаксис *wasb://*. Прежний синтаксис *asv://* поддерживается в кластерах HDInsight 2.1 и 1.6, но не поддерживается в кластерах HDInsight 3.0 и не будет поддерживаться в последующих версиях..

> [WACOM.NOTE] Путь WASB является виртуальным.  Дополнительные сведения см. в разделе [Использование хранилища BLOB-объектов Azure с HDInsight][hdinsight-storage]. 

Доступ к файлу, хранящемуся в контейнере файловой системы по умолчанию, из HDInsight может осуществляться с помощью любого из следующих URI (workflow.xml используется в качестве примера):

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
	wasb:///tutorials/useoozie/workflow.xml
	/tutorials/useoozie/workflow.xml

Если требуется доступ к этому файлу непосредственно из учетной записи хранения, имя BLOB-объекта для файла имеет следующее значение:

	tutorials/useoozie/workflow.xml

**Общие сведения о внутренней и внешней таблицах Hive**

Есть несколько вещей, которые нужно знать о внутренней и внешней таблицах Hive.

- Команда CREATE TABLE создает внутреннюю таблицу, также известную как управляемая таблица. Файл данных должен находиться в контейнере по умолчанию.
- Команда CREATE TABLE перемещает файл данных в папку /hive/warehouse/<TableName> в контейнере по умолчанию.
- Команда CREATE EXTERNAL TABLE создает внешнюю таблицу. Файл данных может находиться за пределами контейнера по умолчанию.
- Команда CREATE EXTERNAL TABLE не перемещает файл данных.
- Команда CREATE EXTERNAL TABLE не позволяет использовать вложенные папки в папке, указанной в предложении LOCATION. Именно по этой причине в учебнике создается копия файла sample.log.

Дополнительные сведения см. в разделе [HDInsight: введение по внутренним и внешним таблицам Hive][cindygross-hive-tables].

**Подготовка учебника**

1. Откройте интегрированную среду сценариев Windows PowerShell (на начальном экране Windows 8 введите **PowerShell_ISE** и щелкните элемент **Интегрированная среда сценариев Windows PowerShell**. См. раздел [Запуск Windows PowerShell в Windows 8 и Windows][powershell-start]).
2. В нижней области выполните следующую команду для подключения к подписке Azure:

		Add-AzureAccount

	Появится запрос на ввод учетных данных учетной записи Azure. Этот метод добавления подключения подписки имеет срок действия, и после 12 часов вам потребуется снова запустить командлет. 

	> [WACOM.NOTE] Если имеется несколько подписок Azure и должна использоваться подписка, отличная от подписки по умолчанию, воспользуйтесь командлетом <strong>Select-AzureSubscription</strong> для выбора текущей подписки.

3. Скопируйте следующий скрипт в область скриптов и задайте первые шесть переменных
			
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

4. Нажмите кнопку **Запустить скрипт** или нажмите клавишу **F5** для выполнения скрипта. Результат должен быть аналогичен приведенному ниже:

	![Выходные данные подготовки учебника][img-preparation-output]

##<a id="run"></a>Запуск проекта Oozie

В настоящее время Azure PowerShell не предоставляет командлеты для определения заданий Oozie. Можно использовать
командлет Invoke-RestMethod PowerShell для вызова веб-служб Oozie. API-интерфейс веб-служб Oozie представляет собой HTTP REST JSON API. Дополнительные сведения об API веб-служб Oozie см. в разделе [Документация по Apache Oozie 4.0][apache-oozie-400] (для кластера HDInsight версии 3.0) или [Документация по Apache Oozie 3.3.2][apache-oozie-332] (для кластера HDInsight версии 2.1).

**Отправка задания Oozie**

1. Откройте интегрированную среду сценариев Windows PowerShell (на начальном экране Windows 8 введите **PowerShell_ISE** и щелкните элемент **Интегрированная среда сценариев Windows PowerShell**. См. раздел [Запуск Windows PowerShell в Windows 8 и Windows][powershell-start]).

3. Скопируйте следующий скрипт в область скриптов и задайте первые четырнадцать переменных (пропустите шестую — $storageUri). 

		#HDInsight cluster variables
		$clusterName = "<HDInsightClusterName>"
		$clusterUsername = "<HDInsightClusterUsername>"
		$clusterPassword = "<HDInsightClusterUserPassword>"
		
		#Azure Blob storage (WASB) variables
		$storageAccountName = "<StorageAccountName>"
		$storageContainerName = "<BlobContainerName>"
		$storageUri="wasb://$storageContainerName@$storageAccountName.blob.core.windows.net"
		
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

	$coordstart и $coordend — это время начала и окончания рабочего процесса. Чтобы узнать время UTC/GMT, выполните поиск "время utc" с помощью bing.com. Переменная $coordFrequency определяет, через сколько минут нужно выполнять рабочий процесс. 

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
		       <value>&quot;$sqlDatabaseConnectionString&quot;</value>
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

	>[WACOM.NOTE] Основным различием по сравнению с файлом полезных данных отправки рабочего процесса является переменная **oozie.coord.application.path**. При отправке задания рабочего процесса используйте вместо нее **oozie.wf.application.path**.

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

	> [WACOM.NOTE] При отправке задания рабочего процесса необходимо вызвать другую веб-службу, чтобы запустить задание после его создания. В этом случае задание координатора запускается по времени.  Задание будет запускаться автоматически.

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
  
7. Добавьте следующее содержимое в скрипт:

		checkOozieServerStatus
		# listOozieJobs
		$oozieJobId = createOozieJob($oozieJobId)
		checkOozieJobStatus($oozieJobId)
		# ShowOozieJobLog($oozieJobId)
		# killOozieJob($oozieJobId)

	Удалите значки #, если нужно выполнить дополнительные функции.

7. Если используется кластер HDinsight версии 2.1, замените "https://$clusterName.azurehdinsight.net:443/oozie/v2/" на "https://$clusterName.azurehdinsight.net:443/oozie/v1/". Кластер HDInsight версии 2.1 не поддерживает веб-службы версии 2.

7. Для выполнения скрипта нажмите кнопку **Запустить скрипт** или нажмите клавишу **F5**. Результат должен быть аналогичен приведенному ниже:

	![Выходные данные запуска рабочего процесса в учебнике][img-runworkflow-output]

8. Подключитесь к базе данных SQL для просмотра экспортированных данных.

**Проверка журнала ошибок**

Для устранения неполадок рабочего процесса можно использовать файл журнала Oozie C:\apps\dist\oozie-3.3.2.1.3.2.0-05\oozie-win-distro\logs\Oozie.log на головном узле кластера. Сведения о RDP см. в разделе [Администрирование кластеров HDInsight с использованием портала управления][hdinsight-admin-portal].

**Повторное выполнение учебника**

Чтобы запустить рабочий процесс повторно, необходимо выполнить следующее:

- удалить файл выходных данных скрипта Hive;
- удалить данные в таблице log4jLogsCount.

Ниже приведен пример скрипта PowerShell, который можно использовать:

	$storageAccountName = "<WindowsAzureStorageAccountName>"
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


##<a id="nextsteps"></a>Дальнейшие действия
В этом руководстве вы узнали, как определить рабочий процесс Oozie и выполнять задания Oozie с помощью Azure PowerShell. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

- [Приступая к работе с HDInsight][hdinsight-get-started]
- [Приступая к работе с эмулятором HDInsight][hdinsight-emulator]
- [Использование хранилища BLOB-объектов Azure с HDInsight][hdinsight-storage]
- [Администрирование HDInsight с использованием PowerShell][hdinsight-admin-powershell]
- [Отправка данных в HDInsight][hdinsight-upload-data]
- [Использование Sqoop вместе с HDInsight][hdinsight-sqoop]
- [Использование Hive с HDInsight][hdinsight-hive]
- [Использование Pig с HDInsight][hdinsight-pig]
- [Разработка заданий потоковой передачи Hadoop на C# для HDInsight][hdinsight-develop-streaming]
- [Разработка программ MapReduce на Java для HDInsight][hdinsight-develop-mapreduce]





[hdinsight-versions]:  /ru-ru/documentation/articles/hdinsight-component-versioning/
[hdinsight-storage]: /ru-ru/documentation/articles/hdinsight-use-blob-storage/
[hdinsight-get-started]: /ru-ru/documentation/articles/hdinsight-get-started/
[hdinsight-admin-portal]: /ru-ru/documentation/articles/hdinsight-administer-use-management-portal/


[hdinsight-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-provision]: /ru-ru/documentation/articles/hdinsight-provision-clusters/

[hdinsight-admin-powershell]: /ru-ru/documentation/articles/hdinsight-administer-use-powershell/

[hdinsight-upload-data]: /ru-ru/documentation/articles/hdinsight-upload-data/

[hdinsight-mapreduce]: /ru-ru/documentation/articles/hdinsight-use-mapreduce/
[hdinsight-hive]: /ru-ru/documentation/articles/hdinsight-use-hive/

[hdinsight-pig]: /ru-ru/documentation/articles/hdinsight-use-pig/

[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563
[hdinsight-storage]: /ru-ru/documentation/articles/hdinsight-use-blob-storage/

[hdinsight-emulator]: /ru-ru/documentation/articles/hdinsight-get-started-emulator/

[hdinsight-develop-streaming]: /ru-ru/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-develop-mapreduce]: /ru-ru/documentation/articles/hdinsight-develop-deploy-java-mapreduce/
[hdinsight-oozie]: ../hdinsight-use-oozie/

[sqldatabase-create-configue]: ../sql-database-create-configure/
[sqldatabase-get-started]: ../sql-database-get-started/

[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: /ru-ru/manage/services/storage/how-to-create-a-storage-account/ 

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://www.windowsazure.com/ru-ru/manage/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /ru-ru/manage/install-and-configure-windows-powershell/
[powershell-start]: http://technet.microsoft.com/ru-ru/library/hh847889.aspx
[powershell-script]: http://technet.microsoft.com/ru-ru/library/ee176949.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Preparation.Output1.png  
[img-runworkflow-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.RunCoord.Output.png  

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

