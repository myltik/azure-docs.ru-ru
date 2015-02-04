<properties urlDisplayName="Use Hadoop Oozie in HDInsight" pageTitle="Использование Hadoop Oozie в HDInsight для платформы Azure" metaKeywords="" description="Использование Oozie с Hadoop в HDInsight — решение для работы с данными большого размера. Вы узнаете, как определить рабочий процесс и отправить задание для Oozie." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Hadop Oozie in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/12/2014" ms.author="jgao" />


# Использование Oozie с Hadoop в HDInsight

Узнайте, как определить рабочий процесс и выполнить его в HDInsight. Чтобы больше узнать о координаторе Oozie, см. раздел [Использование временного координатора Oozie с HDInsight][hdinsight-oozie-coordinator-time].

##Содержание

0. [Что такое Oozie](#whatisoozie)
1. [Предварительные требования](#prerequisites)
2. [Определение файла рабочего процесса Oozie](#defineworkflow)
2. [Развертывание проекта Oozie и подготовка учебника](#deploy)
3. [Запуск рабочего процесса](#run)
4. [Дальнейшие действия](#nextsteps)

##<a id="whatisoozie"></a>Что такое Oozie

Apache Oozie - это система рабочих процессов и координации, управляющая заданиями Hadoop. Это решение интегрировано со стеком Hadoop и поддерживает задания Hadoop Apache MapReduce, Apache Pig, Apache Hive и Apache Sqoop.. Его также можно использовать для планирования относящихся к системе заданий, таких как Java-программ и скриптов оболочки.

Рабочий процесс, который вы будете реализовывать, содержит два действия: 

![Workflow diagram][img-workflow-diagram]

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

	Дополнительную информацию о Hive см. в разделе [Использование Hive с HDInsight][hdinsight-use-hive].
	
2.  Действие Sqoop экспортирует выходные данные действия HiveQL в таблицу в базе данных SQL Azure. Дополнительные сведения о Sqoop см. в разделе [Использование Sqoop с HDInsight][hdinsight-use-sqoop].

> [WACOM.NOTE] Информацию о поддерживаемых версиях Oozie в кластерах HDInsight см. в разделе [Новые возможности версий кластеров, предоставляемых HDInsight][hdinsight-versions].

> [WACOM.NOTE] В этом учебнике показана работа с кластером HDInsight версии 2.1 и 3.0. Эта статья не проверялась на эмуляторе HDInsight.


##<a id="prerequisites"></a>Предварительные требования

Перед началом работы с этим учебником необходимо иметь следующее:

- **Рабочая станция**, на которой установлена и настроена среда Azure PowerShell. Указания см. в разделе [Установка и настройка Azure PowerShell][powershell-install-configure]. Для выполнения сценариев PowerShell необходимо запустить Azure PowerShell с правами администратора и задать политику выполнения RemoteSigned. См. раздел [Выполнение сценариев Windows PowerShell][powershell-script].
- **Кластер HDInsight**. Дополнительную информацию о создании кластера HDInsight см. в разделе [Подготовка кластеров HDInsight][hdinsight-provision] или [Приступая к работе с эмулятором HDInsight][hdinsight-get-started]. Для выполнения учебника необходимы следующие данные:

	<table border = "1">
	<tr><th>Свойство кластера</th><th>Имя переменной PowerShell</th><th>Значение</th><th>Описание</th></tr>
	<tr><td>Имя кластера HDInsight.</td><td>$clusterName</td><td></td><td>Кластер HDInsight, на котором будет выполняться данный учебник.</td></tr>
	<tr><td>Имя пользователя кластера HDInsigh</td><td>$clusterUsername</td><td></td><td>Имя пользователя кластера HDInsight. </td></tr>
	<tr><td>Пароль пользователя кластера HDInsight. </td><td>$clusterPassword</td><td></td><td>Пароль пользователя кластера HDInsight.</td></tr>
	<tr><td>Имя учетной записи хранения Azure</td><td>$storageAccountName</td><td></td><td>Учетная запись хранения Azure, доступная для кластера HDInsight. В этом учебнике используйте учетную запись хранения по умолчанию, указанную в процессе подготовки кластера.</td></tr>
	<tr><td>Имя контейнера BLOB-объектов Azure</td><td>$containerName</td><td></td><td>Для этого примера применяйте контейнер хранилища BLOB-объектов Azure, используемый для файловой системы кластера HDInsight по умолчанию. По умолчанию его имя совпадает с именем кластера HDInsight.</td></tr>
	</table>

- **База данных SQL Azure**. Необходимо настроить правило брандмауэра для сервера базы данных SQL, чтобы разрешить доступ к рабочей станции. Указания по созданию базы данных SQL и настройке брандмауэра см. в разделе [Приступая к работе с базой данных SQL Azure][sqldatabase-get-started]. Эта статья содержит скрипт PowerShell для создания таблицы базы данных SQL, необходимой в рамках этого учебника. 

	<table border = "1">
	<tr><th>Свойство базы данных SQL</th><th>Имя переменной PowerShell</th><th>Значение</th><th>Описание</th></tr>
	<tr><td>Имя сервера базы данных SQL</td><td>$sqlDatabaseServer</td><td></td><td>Сервер базы данных SQL, куда Sqoop экспортирует данные. </td></tr>
	<tr><td>Имя для входа базы данных SQL</td><td>$sqlDatabaseLogin</td><td></td><td>Имя для входа базы данных SQL</td></tr>
	<tr><td>Пароль для входа базы данных SQL</td><td>$sqlDatabaseLoginPassword</td><td></td><td>Пароль для входа базы данных SQL</td></tr>
	<tr><td>Имя базы данных SQL</td><td>$sqlDatabaseName</td><td></td><td>База данных Azure SQL, куда Sqoop экспортирует данные. </td></tr>
	</table>

	> [WACOM.NOTE] По умолчанию база данных SQL Azure разрешает подключения из служб Azure, таких как Azure HDInsight. Если этот параметр брандмауэра отключен, вы должны включить его на портале управления Azure. Указания по созданию базы данных SQL и настройке правил брандмауэра см. в разделе [Создание и настройка базы данных SQL][sqldatabase-create-configue]. 


> [WACOM.NOTE] Введите значения в таблицы.  Это будет полезно при прохождении данного учебника.


##<a id="defineworkflow"></a>Определение рабочего процесса Oozie и связанного с ним скрипта HiveQL

Определения рабочих процессов Oozie записываются в hPDL (язык определения процессов XML). Для файла рабочего процесса по умолчанию используется имя workflow.xml.  Позднее в этом учебнике вы сохраните файл рабочего процесса в локальной среде и развернете его в кластере HDInsight с помощью Azure PowerShell.

Действие Hive в рабочем процессе вызывает файл скрипта HiveQL. Этот файл скрипта содержит три инструкции HiveQL:

1. **Инструкция DROP TABLE** удаляет таблицу log4j Hive, если она существует.
2. **Инструкция CREATE TABLE** создает внешнюю таблицу log4j Hive, указывающую на местоположение файла журнала log4j. Разделителем поля является ",". Разделителем строки по умолчанию является "\n".  Внешняя таблица Hive используется для предотвращения удаления файла данных из исходного расположения данных в случае, когда требуется запустить рабочий процесс Oozie несколько раз.
3. **Инструкция INSERT OVERWRITE** подсчитывает экземпляры каждого типа уровня журнала в таблице log4j Hive и сохраняет выходные данные в расположении больших двоичных объектов хранилища Azure (WASB). 

Существует известная проблема с путем Hive. Эта проблема возникает при отправке задания Oozie. Указания по устранению этой проблемы можно найти на [вики-сайте TechNet][technetwiki-hive-error].

**Определение файла сценария HiveQL, вызываемого рабочим процессом:**

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

	Существует два действия, определенные в рабочем процессе. Первым действием является RunHiveScript. Если действие выполнено со статусом "ok", выполняется следующее действие RunSqoopExport.

	RunHiveScript имеет несколько переменных. Вы передадите эти значения при отправке задания Oozie с рабочей станции с помощью Azure PowerShell.

	<table border = "1">
	<tr><th>Переменные рабочего процесса</th><th>Описание</th></tr>
	<tr><td>${jobTracker}</td><td>Укажите URL-адрес средства отслеживания заданий Hadoop. Использовать <strong>jobtrackerhost:9010</strong> в кластере HDInsight версии 2.0 и 3.0.</td></tr>
	<tr><td>${nameNode}</td><td>Укажите URL-адрес узла имен заданий Hadoop. Используйте адрес WASB файловой системы по умолчанию. Например: <i>wasb://&lt;containerName&gt;@&lt;storageAccountName&gt;.blob.core.windows.net</i>.</td></tr>
	<tr><td>${queueName}</td><td>Указывает имя очереди, в которую будет отправлено задание. Использовать <strong>по умолчанию</strong>.</td></tr>
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

	Дополнительную информацию о рабочем процессе Oozie и использовании его действий см. в разделе [Документация по Apache Oozie 4.0][apache-oozie-400] (для кластера HDInsight версии 3.0) или [Документация по Apache Oozie 3.3.2][apache-oozie-332] (для кластера HDInsight версии 2.1).

2. Сохраните файл как **C:\Tutorials\UseOozie\workflow.xml** в кодировке ANSI (ASCII) (если текстовый редактор не позволяет сделать это, используйте Блокнот).
	
##<a id="deploy"></a>Развертывание проекта Oozie и подготовка учебника

Вы выполните скрипт Azure PowerShell для достижения следующих результатов:

- Копирование скрипта HiveQL (useoozie.hql) в хранилище BLOB-объектов Azure, wasb:///tutorials/useoozie/useoozie.hql.
- Копирование workflow.xml в wasb:///tutorials/useoozie/workflow.xml.
- Скопируйте файл с данными (/example/data/sample.log) на wasb:///tutorials/useoozie/data/sample.log. 
- Создание таблицы базы данных SQL для хранения данных экспорта Sqoop.  Имя таблицы - log4jLogCount.

**Общие сведения о хранилище HDInsight**

HDInsight использует для хранения данных хранилище BLOB-объектов Azure.  Оно называется WASB или "хранилище больших двоичных объектов Azure". WASB - это реализация HDFS на базе хранилища больших двоичных объектов Azure от корпорации Майкрософт. Дополнительную информацию см. в статье [Использование хранилища BLOB-объектов Azure с HDInsight][hdinsight-storage]. 

В процессе подготовки кластера HDInsight в качестве файловой системы по умолчанию устанавливаются учетная запись хранения Azure и конкретный контейнер хранилища BLOB-объектов из этой учетной записи, так же как и в HDFS. Помимо этой учетной записи хранения в процессе подготовки можно добавить дополнительные учетные записи хранения из той же подписки Azure или других подписок Azure. Указания по добавлению дополнительных учетных записей хранения см. в разделе [Подготовка кластеров HDInsight][hdinsight-provision]. Чтобы упростить сценарий PowerShell, используемый в этом учебнике, все файлы хранятся в контейнере файловой системы по умолчанию, расположенном в /tutorials/useoozie. Имя контейнера по умолчанию совпадает с именем кластера HDInsight. 
Синтаксис WASB:

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] Кластер HDInsight версии 3.0 поддерживает только синтаксис wasb://. Прежний синтаксис asv:// поддерживается в кластерах HDInsight 2.1 и 1.6, однако не поддерживается в кластерах HDInsight 3.0 и не будет поддерживаться в последующих версиях.

> [WACOM.NOTE] Путь WASB является виртуальным.  Дополнительную информацию см. в статье [Использование хранилища BLOB-объектов Azure с HDInsight][hdinsight-storage]. 

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

Дополнительную информацию см. в разделе [HDInsight: введение по внутренним и внешним таблицам Hive][cindygross-hive-tables].

**Подготовка учебника**

1. Откройте интегрированную среду сценариев Windows PowerShell (на начальном экране Windows 8 введите **PowerShell_ISE** и затем щелкните элемент **Интегрированная среда сценариев Windows PowerShell**. См. статью [Start Windows PowerShell on Windows 8 and Windows][powershell-start] (Запуск Windows PowerShell в Windows 8 и Windows)).
2. В нижней области выполните следующую команду для подключения к подписке Azure:

		Add-AzureAccount

	Появится запрос на ввод учетных данных учетной записи Azure. Этот метод добавления подключения подписки имеет срок действия, и после 12 часов вам потребуется снова запустить командлет. 

	> [WACOM.NOTE] Если имеется несколько подписок Azure и должна использоваться подписка, отличная от подписки по умолчанию, воспользуйтесь <strong>Командлет Select-AzureSubscription</strong> командлетом Select-AzureSubscription для выбора текущей подписки.

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
		$workflowDefinition = "C:\Tutorials\UseOozie\workflow.xml"
		$hiveQLScript = "C:\Tutorials\UseOozie\useooziewf.hql"
		
		# WASB folder for storing the Oozie tutorial files.
		$destFolder = "tutorials/useoozie"  # Do NOT use the long path here


	Дополнительное описание переменных см. в разделе [Предварительные требования](#prerequisites) данного учебника. 

3. Добавьте следующее содержимое в скрипт в области скриптов:
		
		# Create a storage context object
		$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
		
		function uploadOozieFiles()
		{		
		    Write-Host "Copy workflow definition and HiveQL script file ..." -ForegroundColor Green
			Set-AzureStorageBlobContent -File $workflowDefinition -Container $containerName -Blob "$destFolder/workflow.xml" -Context $destContext
			Set-AzureStorageBlobContent -File $hiveQLScript -Container $containerName -Blob "$destFolder/useooziewf.hql" -Context $destContext
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

4. Для выполнения сценария нажмите кнопку **Запустить сценарий** или нажмите клавишу **F5**. Результат должен быть аналогичен приведенному ниже:

	![Tutorial preparation output][img-preparation-output]

##<a id="run"></a>Запуск проекта Oozie

В настоящее время Azure PowerShell не предоставляет командлеты для определения заданий Oozie. Можно использовать командлет Invoke-RestMethod PowerShell для вызова веб-служб Oozie. API-интерфейс веб-служб Oozie представляет собой HTTP REST JSON API. Дополнительную информацию об API веб-служб Oozie см. в разделе [Документация по Apache Oozie 4.0][apache-oozie-400] (для кластера HDInsight версии 3.0) или [Документация по Apache Oozie 3.3.2][apache-oozie-332] (для кластера HDInsight версии 2.1).

**Отправка задания Oozie**

1. Откройте интегрированную среду сценариев Windows PowerShell (на начальном экране Windows 8 введите **PowerShell_ISE** и затем щелкните элемент **Интегрированная среда сценариев Windows PowerShell**. См. статью [Start Windows PowerShell on Windows 8 and Windows][powershell-start] (Запуск Windows PowerShell в Windows 8 и Windows)).

3. Скопируйте следующий сценарий в область сценариев и задайте первые десять переменных (пропустите шестую - $storageUri). 

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
		
		#Oozie WF variables
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


	Дополнительное описание переменных см. в разделе [Предварительные требования](#prerequisites) данного учебника.

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
		       <value>$clusterUsername</value>
		   </property>
		
		   <property>
		       <name>oozie.wf.application.path</name>
		       <value>$oozieWFPath</value>
		   </property>
		
		</configuration>
		"@
		
4. Добавьте следующее содержимое в скрипт. Эта часть проверяет состояние веб-служб Oozie:	
			
	    Write-Host "Checking Oozie server status..." -ForegroundColor Green
	    $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
	    $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus 
	    
	    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
	    $oozieServerSatus = $jsonResponse[0].("systemMode")
	    Write-Host "Oozie server status is $oozieServerSatus..."
	
5. Добавьте следующее содержимое в скрипт. Эта часть создает и запускает задание Oozie:	

	    # create Oozie job
	    Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
	    Write-Host "`n--------`n$OoziePayload`n--------"
	    $clusterUriCreateJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
	    $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $creds -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName #-debug
	
	    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
	    $oozieJobId = $jsonResponse[0].("id")
	    Write-Host "Oozie job id is $oozieJobId..."
	
	    # start Oozie job
	    Write-Host "Starting the Oozie job $oozieJobId..." -ForegroundColor Green
	    $clusterUriStartJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=start"
	    $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $creds | Format-Table -HideTableHeaders #-debug
		
6. Добавьте следующее содержимое в скрипт. Эта часть проверяет состояние задания Oozie:		

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
	
	    Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!" -ForegroundColor Green

7. При наличии кластера HDinsight версии 2.1 замените "https://$clusterName.azurehdinsight.net:443/oozie/v2/" на "https://$clusterName.azurehdinsight.net:443/oozie/v1/". Кластер HDInsight версии 2.1 не поддерживает веб-службы версии 2.

8. Для выполнения сценария нажмите кнопку **Запустить сценарий** или нажмите клавишу **F5**. Результат должен быть аналогичен приведенному ниже:

	![Tutorial run workflow output][img-runworkflow-output]

8. Подключитесь к базе данных SQL для просмотра экспортированных данных.

**Проверка журнала ошибок**

Для диагностики рабочего процесса можно использовать файл журнала Oozie, расположенный в 
C:\apps\dist\oozie-3.3.2.1.3.2.0-05\oozie-win-distro\logs\Oozie.log или C:\apps\dist\oozie-4.0.0.2.0.7.0-1528\oozie-win-distro\logs\Oozie.log из головного узла кластера. Дополнительную информацию о RDP см. в разделе [Администрирование кластеров HDInsight с использованием портала управления][hdinsight-admin-portal].

**Повторное выполнение учебника**

Чтобы запустить рабочий процесс повторно, необходимо выполнить следующее:

- удалить файл выходных данных скрипта Hive;
- удалить данные в таблице log4jLogsCount.

Ниже приведен пример скрипта PowerShell, который можно использовать:

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


##<a id="nextsteps"></a>Дальнейшие действия
В этом руководстве вы узнали, как определить рабочий процесс Oozie и выполнять задания Oozie с помощью Azure PowerShell. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

- [Использование временного координатора Oozie с HDInsight][hdinsight-oozie-coordinator-time]
- [Приступая к работе с HDInsight][hdinsight-get-started]
- [Приступая к работе с эмулятором HDInsight][hdinsight-get-started-emulator]
- [Использование хранилища BLOB-объектов Azure с HDInsight][hdinsight-storage]
- [Администрирование HDInsight с использованием PowerShell][hdinsight-admin-powershell]
- [Отправка данных в HDInsight][hdinsight-upload-data]
- [Использование Sqoop с HDInsight][hdinsight-use-sqoop]
- [Использование Hive с HDInsight][hdinsight-use-hive]
- [Использование Pig с HDInsight][hdinsight-use-pig]
- [Разработка заданий потоковой передачи Hadoop на C# для HDInsight][hdinsight-develop-streaming-jobs]
- [Разработка программ MapReduce на Java для HDInsight][hdinsight-develop-mapreduce]


[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563



[hdinsight-oozie-coordinator-time]: ../hdinsight-use-oozie-coordinator-time/
[hdinsight-versions]:  ../hdinsight-component-versioning/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/


[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-get-started-emulator]: ../hdinsight-get-started-emulator/

[hdinsight-develop-streaming-jobs]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-develop-mapreduce]: ../hdinsight-develop-deploy-java-mapreduce/

[sqldatabase-create-configue]: ../sql-database-create-configure/
[sqldatabase-get-started]: ../sql-database-get-started/

[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/ 

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/ru-ru/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: ../install-and-configure-powershell/
[powershell-start]: http://technet.microsoft.com/ru-ru/library/hh847889.aspx
[powershell-script]: http://technet.microsoft.com/ru-ru/library/ee176949.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png  
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png 

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

<!--HONumber=35.1-->
