<properties linkid="hdinsight-use-hadoop-oozie-in-hdinsight" urlDisplayName="Use Hadoop Oozie in HDInsight" pageTitle="Use Hadoop Oozie in HDInsight | Azure" metaKeywords="" description="Use Hadoop Oozie in HDInsight, a big data solution. Learn how to define an Oozie workflow, and submit an Oozie job." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Hadop Oozie in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"/>

# Использование Oozie с Hadoop в HDInsight

Узнайте, как определить рабочий процесс и выполнить его в HDInsight. Для получения сведений о координаторе Oozie см. раздел [Использование временного координатора Oozie с HDInsight][Использование временного координатора Oozie с HDInsight].

**Предполагаемое время выполнения:** 40 минут

## Содержание

1.  [Что такое Oozie][Что такое Oozie]
2.  [Предварительные требования][Предварительные требования]
3.  [Определение файла рабочего процесса Oozie][Определение файла рабочего процесса Oozie]
4.  [Развертывание проекта Oozie и подготовка учебника][Развертывание проекта Oozie и подготовка учебника]
5.  [Запуск рабочего процесса][Запуск рабочего процесса]
6.  [Дальнейшие действия][Дальнейшие действия]

## <span id="whatisoozie"></span></a>Что такое Oozie

Apache Oozie — это система рабочих процессов и координации, управляющая заданиями Hadoop. Это решение интегрировано со стеком Hadoop и поддерживает задания Hadoop Apache MapReduce, Apache Pig, Apache Hive и Apache Sqoop.. Его также можно использовать для планирования относящихся к системе заданий, таких как Java-программ и скриптов оболочки.

Рабочий процесс, который вы будете реализовывать, содержит два действия:

![Схема рабочих процессов][Схема рабочих процессов]

1.  Действие Hive запускает скрипт HiveQL для подсчета экземпляров каждого типа уровня журнала в файле журнала log4j. Каждый журнал log4j состоит из строки полей, содержащей поле УРОВЕНЬ ЖУРНАЛА для отображения типа и серьезности. Например:

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

    Дополнительные сведения о Hive см. в разделе [Использование Hive с HDInsight][Использование Hive с HDInsight].

2.  Действие Sqoop экспортирует выходные данные действия HiveQL в таблицу в базе данных SQL Azure. Дополнительные сведения о Sqoop см. в разделе [Использование Sqoop с HDInsight][Использование Sqoop с HDInsight].

> [WACOM.NOTE] Сведения о поддерживаемых версиях Oozie в кластерах HDInsight см. в разделе [Новые возможности версий кластеров, предоставляемых HDInsight][Новые возможности версий кластеров, предоставляемых HDInsight].

> [WACOM.NOTE] В этом учебнике показана работа с кластером HDInsight версии 2.1 и 3.0. Эта статья не проверялась на эмуляторе HDInsight.

## <span id="prerequisites"></span></a>Предварительные требования

Перед началом работы с этим учебником необходимо иметь следующее:

-   **Рабочая станция**, на которой установлена и настроена среда Azure PowerShell. Инструкции см. в разделе [Установка и настройка Azure PowerShell][Установка и настройка Azure PowerShell]. Для выполнения скриптов PowerShell необходимо запустить Azure PowerShell с правами администратора и задать политику выполнения *RemoteSigned*. См. раздел [Выполнение скриптов Windows PowerShell][Выполнение скриптов Windows PowerShell].
-   **Кластер HDInsight**. Сведения о создании кластера HDInsight см. в разделе [Подготовка кластеров HDInsight][Подготовка кластеров HDInsight] или [Приступая к работе с эмулятором HDInsight][Приступая к работе с эмулятором HDInsight]. Для выполнения учебника необходимы следующие данные:

    <table>
    <colgroup>
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Свойство кластера</th>
    <th align="left">Имя переменной PowerShell</th>
    <th align="left">Значение</th>
    <th align="left">Описание</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">Имя кластера HDInsight.</td>
    <td align="left">$clusterName</td>
    <td align="left"></td>
    <td align="left">Кластер HDInsight, на котором будет выполняться данный учебник.</td>
    </tr>
    <tr class="even">
    <td align="left">Имя пользователя кластера HDInsigh</td>
    <td align="left">$clusterUsername</td>
    <td align="left"></td>
    <td align="left">Имя пользователя кластера HDInsight.</td>
    </tr>
    <tr class="odd">
    <td align="left">Пароль пользователя кластера HDInsight.</td>
    <td align="left">$clusterPassword</td>
    <td align="left"></td>
    <td align="left">Пароль пользователя кластера HDInsight.</td>
    </tr>
    <tr class="even">
    <td align="left">Имя учетной записи хранения Azure</td>
    <td align="left">$storageAccountName</td>
    <td align="left"></td>
    <td align="left">Учетная запись хранения Azure, доступная для кластера HDInsight. В этом учебнике используйте учетную запись хранения по умолчанию, указанную в процессе подготовки кластера.</td>
    </tr>
    <tr class="odd">
    <td align="left">Имя контейнера BLOB-объектов Azure</td>
    <td align="left">$containerName</td>
    <td align="left"></td>
    <td align="left">Для этого примера применяйте контейнер хранилища BLOB-объектов Azure, используемый для файловой системы кластера HDInsight по умолчанию. По умолчанию его имя совпадает с именем кластера HDInsight.</td>
    </tr>
    </tbody>
    </table>

-   **База данных SQL Azure**. Необходимо настроить правило брандмауэра для сервера базы данных SQL, чтобы разрешить доступ к рабочей станции. Инструкции по созданию базы данных SQL и настройке брандмауэра см. в разделе [Приступая к работе с базой данных SQL Azure][Приступая к работе с базой данных SQL Azure]. Эта статья содержит скрипт PowerShell для создания таблицы базы данных SQL, необходимой в рамках этого учебника.

    <table>
    <colgroup>
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Свойство базы данных SQL</th>
    <th align="left">Имя переменной PowerShell</th>
    <th align="left">Значение</th>
    <th align="left">Описание</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">Имя сервера базы данных SQL</td>
    <td align="left">$sqlDatabaseServer</td>
    <td align="left"></td>
    <td align="left">Сервер базы данных SQL, куда Sqoop экспортирует данные.</td>
    </tr>
    <tr class="even">
    <td align="left">Имя для входа базы данных SQL</td>
    <td align="left">$sqlDatabaseLogin</td>
    <td align="left"></td>
    <td align="left">Имя для входа базы данных SQL</td>
    </tr>
    <tr class="odd">
    <td align="left">Пароль для входа базы данных SQL</td>
    <td align="left">$sqlDatabaseLoginPassword</td>
    <td align="left"></td>
    <td align="left">Пароль для входа базы данных SQL</td>
    </tr>
    <tr class="even">
    <td align="left">Имя базы данных SQL</td>
    <td align="left">$sqlDatabaseName</td>
    <td align="left"></td>
    <td align="left">База данных Azure SQL, куда Sqoop экспортирует данные.</td>
    </tr>
    </tbody>
    </table>

    > [WACOM.NOTE] По умолчанию база данных SQL Azure разрешает подключения из служб Azure, таких как Azure HDInsight. Если этот параметр брандмауэра отключен, вы должны включить его на портале управления Azure. Инструкции по созданию базы данных SQL и настройке правил брандмауэра см. в разделе [Создание и настройка базы данных SQL][Создание и настройка базы данных SQL].

> [WACOM.NOTE] Введите значения в таблицы. Это будет полезно при прохождении данного учебника.

## <span id="defineworkflow"></span></a>Определение рабочего процесса Oozie и связанного с ним скрипта HiveQL

Определения рабочих процессов Oozie записываются в hPDL (язык определения процессов XML). Для файла рабочего процесса по умолчанию используется имя *workflow.xml*. Позднее в этом учебнике вы сохраните файл рабочего процесса в локальной среде и развернете его в кластере HDInsight с помощью Azure PowerShell.

Действие Hive в рабочем процессе вызывает файл скрипта HiveQL. Этот файл скрипта содержит три инструкции HiveQL:

1.  **Инструкция DROP TABLE** удаляет таблицу log4j Hive, если она существует.
2.  **Инструкция CREATE TABLE** создает внешнюю таблицу log4j Hive, указывающую на местоположение файла журнала log4j. Разделителем поля является ",". Разделителем строки по умолчанию является "\\n". Внешняя таблица Hive используется для предотвращения удаления файла данных из исходного расположения данных в случае, когда требуется запустить рабочий процесс Oozie несколько раз.
3.  **Инструкция INSERT OVERWRITE** подсчитывает экземпляры каждого типа уровня журнала в таблице log4j Hive и сохраняет выходные данные в расположении "Хранилище Azure BLOB-объекты" (WASB).

Существует известная проблема с путем Hive. Эта проблема возникает при отправке задания Oozie. Инструкции по устранению этой проблемы можно найти на [вики-сайте TechNet][вики-сайте TechNet].

**Определение файла скрипта HiveQL, вызываемого рабочим процессом:**

1.  Создайте текстовый файл со следующим содержимым:

        DROP TABLE ${hiveTableName};
        CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
        INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

    Существует три переменные, используемые в данном скрипте:

    -   ${hiveTableName}
    -   ${hiveDataFolder}
    -   ${hiveOutputFolder}

    Файл определения рабочего процесса (workflow.xml в этом учебнике) передает эти значения в данный скрипт HiveQL во время выполнения.

2.  Сохраните файл как **C:\\Tutorials\\UseOozie\\useooziewf.hql** с кодировкой **ANSI(ASCII)** (если текстовый редактор не позволяет сделать это, используйте Блокнот). Позже в этом учебнике этот файл скрипта будет развернут в кластере HDInsight.

**Определение рабочего процесса**

1.  Создайте текстовый файл со следующим содержимым:

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

    | Переменные рабочего процесса | Описание                                                                                                                                                                            |
    <table border = "1">
<tr><th>Переменные рабочего процесса</th><th>Описание</th></tr>
<tr><td>${jobTracker}</td><td>Укажите URL-адрес средства отслеживания заданий Hadoop. Используйте <strong>jobtrackerhost:9010</strong> в кластере HDInsight версии 2.0 и 3.0.</td></tr>
<tr><td>${nameNode}</td><td>Укажите URL-адрес узла имен заданий Hadoop. Используйте адрес WASB файловой системы по умолчанию. Напрмер, <i>wasb://&lt;containerName&gt;@&lt;storageAccountName&gt;.blob.core.windows.net</i>.</td></tr>
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

Дополнительные сведения о рабочем процессе Oozie и использовании его действий см. в разделе [Документация по Apache Oozie 4.0][Документация по Apache Oozie 4.0] (для кластера HDInsight версии 3.0) или [Документация по Apache Oozie 3.3.2][Документация по Apache Oozie 3.3.2] (для кластера HDInsight версии 2.1).


 2.Сохраните файл как **C:\\Tutorials\\UseOozie\\workflow.xml** с кодировкой ANSI (ASCII) (если текстовый редактор не позволяет сделать это, используйте Блокнот).

## <span id="deploy"></span></a>Развертывание проекта Oozie и подготовка учебника

Вы выполните скрипт Azure PowerShell для достижения следующих результатов:

-   Копирование скрипта HiveQL (useoozie.hql) в хранилище BLOB-объектов Azure, wasb:///tutorials/useoozie/useoozie.hql.
-   Копирование workflow.xml в wasb:///tutorials/useoozie/workflow.xml.
-   Копирование файла данных (/example/data/sample.log) в wasb:///tutorials/useoozie/data/sample.log.
-   Создание таблицы базы данных SQL для хранения данных экспорта Sqoop. Имя таблицы — *log4jLogCount*.

**Общие сведения о хранилище HDInsight**

HDInsight использует для хранения данных хранилище BLOB-объектов Azure. Оно называется *WASB* или *Хранилище Azure — BLOB-объекты*. WASB — это реализация HDFS на базе хранилища BLOB-объектов Azure от корпорации Майкрософт. Дополнительные сведения см. в разделе [Использование хранилища BLOB-объектов Azure с HDInsight][Использование хранилища BLOB-объектов Azure с HDInsight].

В процессе подготовки кластера HDInsight в качестве файловой системы по умолчанию устанавливаются учетная запись хранения Azure и конкретный контейнер хранилища BLOB-объектов из этой учетной записи, так же как и в HDFS. Помимо этой учетной записи хранения в процессе подготовки можно добавить дополнительные учетные записи хранения из той же подписки Azure или других подписок Azure. Инструкции по добавлению дополнительных учетных записей хранения см. в разделе [Подготовка кластеров HDInsight][Подготовка кластеров HDInsight]. Чтобы упростить скрипт PowerShell, используемый в этом учебнике, все файлы хранятся в контейнере файловой системы по умолчанию, расположенном в */tutorials/useoozie*. Имя контейнера по умолчанию совпадает с именем кластера HDInsight.
 Синтаксис WASB:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] Кластер HDInsight версии 3.0 поддерживает только синтаксис *wasb://*. Прежний синтаксис *asv://* поддерживается в кластерах HDInsight 2.1 и 1.6, но не поддерживается в кластерах HDInsight 3.0 и не будет поддерживаться в последующих версиях..

> [WACOM.NOTE] Путь WASB является виртуальным. Дополнительные сведения см. в разделе [Использование хранилища BLOB-объектов Azure с HDInsight][Использование хранилища BLOB-объектов Azure с HDInsight].

Доступ к файлу, хранящемуся в контейнере файловой системы по умолчанию, из HDInsight может осуществляться с помощью любого из следующих URI (workflow.xml используется в качестве примера):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
    wasb:///tutorials/useoozie/workflow.xml
    /tutorials/useoozie/workflow.xml

Если требуется доступ к этому файлу непосредственно из учетной записи хранения, имя BLOB-объекта для файла имеет следующее значение:

    tutorials/useoozie/workflow.xml

**Общие сведения о внутренней и внешней таблицах Hive**

Есть несколько вещей, которые нужно знать о внутренней и внешней таблицах Hive.

-   Команда CREATE TABLE создает внутреннюю таблицу, также известную как управляемая таблица. Файл данных должен находиться в контейнере по умолчанию.
-   Команда CREATE TABLE перемещает файл данных в папку /hive/warehouse/<tablename> в контейнере по умолчанию.
-   Команда CREATE EXTERNAL TABLE создает внешнюю таблицу. Файл данных может находиться за пределами контейнера по умолчанию.
-   Команда CREATE EXTERNAL TABLE не перемещает файл данных.
-   Команда CREATE EXTERNAL TABLE не позволяет использовать вложенные папки в папке, указанной в предложении LOCATION. Именно по этой причине в учебнике создается копия файла sample.log.

Дополнительные сведения см. в разделе [HDInsight: введение по внутренним и внешним таблицам Hive][HDInsight: введение по внутренним и внешним таблицам Hive].

**Подготовка учебника**

1.  Откройте интегрированную среду сценариев Windows PowerShell (на начальном экране Windows 8 введите **PowerShell\_ISE** и затем щелкните элемент **Интегрированная среда сценариев Windows PowerShell**. См. раздел [Запуск Windows PowerShell в Windows 8 и Windows][Запуск Windows PowerShell в Windows 8 и Windows]).
2.  В нижней области выполните следующую команду для подключения к подписке Azure:

        Add-AzureAccount

    Появится запрос на ввод учетных данных учетной записи Azure. Этот метод добавления подключения подписки имеет срок действия, и после 12 часов вам потребуется снова запустить командлет.

    > [WACOM.NOTE] Если имеется несколько подписок Azure и должна использоваться подписка, отличная от подписки по умолчанию, воспользуйтесь командлетом **Select-AzureSubscription** для выбора текущей подписки.

3.  Скопируйте следующий скрипт в область скриптов и задайте первые шесть переменных

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

    Дополнительные описания переменных см. в разделе [Предварительные требования][Предварительные требования] данного учебника.

4.  Добавьте следующее содержимое в скрипт в области скриптов:

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

5.  Для выполнения скрипта щелкните **Выполнить скрипт** или нажмите клавишу **F5**. Результат должен быть аналогичен приведенному ниже:

    ![Выходные данные подготовки учебника][Выходные данные подготовки учебника]

## <span id="run"></span></a>Запуск проекта Oozie

В настоящее время Azure PowerShell не предоставляет командлеты для определения заданий Oozie. Можно использовать командлет
Invoke-RestMethod PowerShell для вызова веб-служб Oozie. API-интерфейс веб-служб Oozie представляет собой HTTP REST JSON API. Дополнительные сведения об API веб-служб Oozie см. в разделе [Документация по Apache Oozie 4.0][Документация по Apache Oozie 4.0] (для кластера HDInsight версии 3.0) или [Документация по Apache Oozie 3.3.2][Документация по Apache Oozie 3.3.2] (для кластера HDInsight версии 2.1).

**Отправка задания Oozie**

1.  Откройте интегрированную среду сценариев Windows PowerShell (на начальном экране Windows 8 введите **PowerShell\_ISE** и затем щелкните элемент **Интегрированная среда сценариев Windows PowerShell**. См. раздел [Запуск Windows PowerShell в Windows 8 и Windows][Запуск Windows PowerShell в Windows 8 и Windows]).

2.  Скопируйте следующий скрипт в область скриптов и задайте первые десять переменных (пропустите шестую — $storageUri).

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

    Дополнительные описания переменных см. в разделе [Предварительные требования][Предварительные требования] данного учебника.

3.  Добавьте следующее содержимое в скрипт. Эта часть определяет полезные данные Oozie:

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

4.  Добавьте следующее содержимое в скрипт. Эта часть проверяет состояние веб-служб Oozie:

        Write-Host "Checking Oozie server status..." -ForegroundColor Green
        $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus 

        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $oozieServerSatus = $jsonResponse[0].("systemMode")
        Write-Host "Oozie server status is $oozieServerSatus..."

5.  Добавьте следующее содержимое в скрипт. Эта часть создает и запускает задание Oozie:

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

6.  Добавьте следующее содержимое в скрипт. Эта часть проверяет состояние задания Oozie:

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

7.  Если ваш кластер HDinsight имеет версию 2.1, замените "<https://$clusterName.azurehdinsight.net:443/oozie/v2/>" на "<https://$clusterName.azurehdinsight.net:443/oozie/v1/>". Кластер HDInsight версии 2.1 не поддерживает веб-службы версии 2.

8.  Для выполнения скрипта щелкните **Выполнить скрипт** или нажмите клавишу **F5**. Результат должен быть аналогичен приведенному ниже:

    ![Выходные данные запуска рабочего процесса в учебнике][Выходные данные запуска рабочего процесса в учебнике]

9.  Подключитесь к базе данных SQL для просмотра экспортированных данных.

**Проверка журнала ошибок**

Для устранения неполадок рабочего процесса можно использовать файл журнала Oozie:
*C:\\apps\\dist\\oozie-3.3.2.1.3.2.0-05\\oozie-win-distro\\logs\\Oozie.log* или *C:\\apps\\dist\\oozie-4.0.0.2.0.7.0-1528\\oozie-win-distro\\logs\\Oozie.log* из головного узла кластера. Сведения о RDP см. в разделе [Администрирование кластеров HDInsight с использованием портала управления][Администрирование кластеров HDInsight с использованием портала управления].

**Повторное выполнение учебника**

Чтобы запустить рабочий процесс повторно, необходимо выполнить следующее:

-   удалить файл выходных данных скрипта Hive;
-   удалить данные в таблице log4jLogsCount.

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

## <span id="nextsteps"></span></a>Дальнейшие действия

В этом руководстве вы узнали, как определить рабочий процесс Oozie и выполнять задания Oozie с помощью Azure PowerShell. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

-   [Использование временного координатора Oozie с HDInsight][Использование временного координатора Oozie с HDInsight]
-   [Приступая к работе с HDInsight][Приступая к работе с эмулятором HDInsight]
-   [Приступая к работе с эмулятором HDInsight][1]
-   [Использование хранилища BLOB-объектов Azure с HDInsight][Использование хранилища BLOB-объектов Azure с HDInsight]
-   [Администрирование HDInsight с использованием PowerShell][Администрирование HDInsight с использованием PowerShell]
-   [Отправка данных в HDInsight][Отправка данных в HDInsight]
-   [Использование Sqoop вместе с HDInsight][Использование Sqoop с HDInsight]
-   [Использование Hive с HDInsight][Использование Hive с HDInsight]
-   [Использование Pig с HDInsight][Использование Pig с HDInsight]
-   [Разработка заданий потоковой передачи Hadoop на C# для HDInsight][Разработка заданий потоковой передачи Hadoop на C# для HDInsight]
-   [Разработка программ MapReduce на Java для HDInsight][Разработка программ MapReduce на Java для HDInsight]

  [Использование временного координатора Oozie с HDInsight]: ../hdinsight-use-oozie-coordinator-time/
  [Что такое Oozie]: #whatisoozie
  [Предварительные требования]: #prerequisites
  [Определение файла рабочего процесса Oozie]: #defineworkflow
  [Развертывание проекта Oozie и подготовка учебника]: #deploy
  [Запуск рабочего процесса]: #run
  [Дальнейшие действия]: #nextsteps
  [Схема рабочих процессов]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
  [Использование Hive с HDInsight]: ../hdinsight-use-hive/
  [Использование Sqoop с HDInsight]: ../hdinsight-use-sqoop/
  [Новые возможности версий кластеров, предоставляемых HDInsight]: ../hdinsight-component-versioning/
  [Установка и настройка Azure PowerShell]: ../install-and-configure-powershell/
  [Выполнение скриптов Windows PowerShell]: http://technet.microsoft.com/ru-ru/library/ee176949.aspx
  [Подготовка кластеров HDInsight]: ../hdinsight-provision-clusters/
  [Приступая к работе с эмулятором HDInsight]: ../hdinsight-get-started/
  [Приступая к работе с базой данных SQL Azure]: ../sql-database-get-started/
  [Создание и настройка базы данных SQL]: ../sql-database-create-configure/
  [вики-сайте TechNet]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
  [Документация по Apache Oozie 4.0]: http://oozie.apache.org/docs/4.0.0/
  [Документация по Apache Oozie 3.3.2]: http://oozie.apache.org/docs/3.3.2/
  [Использование хранилища BLOB-объектов Azure с HDInsight]: ../hdinsight-use-blob-storage/
  [HDInsight: введение по внутренним и внешним таблицам Hive]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
  [Запуск Windows PowerShell в Windows 8 и Windows]: http://technet.microsoft.com/ru-ru/library/hh847889.aspx
  [Выходные данные подготовки учебника]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png
  [Выходные данные запуска рабочего процесса в учебнике]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png
  [Администрирование кластеров HDInsight с использованием портала управления]: ../hdinsight-administer-use-management-portal/
  [1]: ../hdinsight-get-started-emulator/
  [Администрирование HDInsight с использованием PowerShell]: ../hdinsight-administer-use-powershell/
  [Отправка данных в HDInsight]: ../hdinsight-upload-data/
  [Использование Pig с HDInsight]: ../hdinsight-use-pig/
  [Разработка заданий потоковой передачи Hadoop на C# для HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [Разработка программ MapReduce на Java для HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/
