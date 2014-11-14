<properties urlDisplayName="Use Hadoo Sqoop in HDInsight" pageTitle="Подготовка Hadoop Sqoop в HDInsight для платформы Azure" metaKeywords="" description="Вы узнаете, как использовать Azure PowerShell с рабочей станции для запуска Sqoop, импорта и экспорта между кластером HDInsight и базой данных Azure SQL." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Использование Hadoop Sqoop в HDInsight" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Использование Sqoop с Hadoop в HDInsight

Вы узнаете, как использовать Azure PowerShell и HDInsight .NET SDK с рабочей станции для запуска Sqoop, импорта и экспорта между кластером HDInsight и базой данных Azure SQL или базой данных SQL Server.

**Предполагаемое время выполнения:**30 минут

## Содержание

-   [Что такое Sqoop?][Что такое Sqoop?]
-   [Предварительные требования][Предварительные требования]
-   [Ознакомление со сценарием учебника][Ознакомление со сценарием учебника]
-   [Подготовка к работе с учебником][Подготовка к работе с учебником]
-   [Использование PowerShell для выполнения экспорта Sqoop][Использование PowerShell для выполнения экспорта Sqoop]
-   [Использование HDInsight SDK для выполнения экспорта Sqoop][Использование HDInsight SDK для выполнения экспорта Sqoop]
-   [Использование PowerShell для выполнения импорта Sqoop][Использование PowerShell для выполнения импорта Sqoop]
-   [Дальнейшие действия][Дальнейшие действия]

## <span id="whatissqoop"></span></a>Что такое Sqoop?

Хотя Hadoop оптимально подходит для обработки частично структурированных и неструктурированных данных, таких как журналы и файлы, может существовать необходимость обработки структурированных данных, хранящихся в реляционных базах данных.

[Sqoop][Sqoop] — это средство, предназначенное для передачи данных между кластерами Hadoop и реляционными базами данных. С его помощью можно импортировать данные из системы управления реляционными базами данных (РСУБД), например SQL, MySQL или Oracle, в распределенную файловую систему Hadoop (HDFS), преобразовать данные в Hadoop с использованием MapReduce или Hive, а затем экспортировать данные обратно в РСУБД. В этом учебнике для реляционной базы данных используется база данных SQL.

Сведения о поддерживаемых версиях Sqoop в кластерах HDInsight см. в разделе [Новые возможности версий кластеров, предоставляемых HDInsight][Новые возможности версий кластеров, предоставляемых HDInsight].

## <span id="prerequisites"></span></a>Предварительные требования

Перед началом работы с этим учебником необходимо иметь следующее:

-   **Рабочая станция**, на которой установлена и настроена среда Azure PowerShell. Инструкции см. в разделе [Установка и настройка Azure PowerShell][Установка и настройка Azure PowerShell]. Для выполнения скриптов PowerShell необходимо запустить Azure PowerShell с правами администратора и задать политику выполнения *RemoteSigned*. См. раздел [Выполнение скриптов Windows PowerShell][Выполнение скриптов Windows PowerShell].

-   **Кластер Azure HDInsight**. Инструкции по подготовке кластеров см. в разделе [Приступая к работе с HDInsight][Приступая к работе с HDInsight] или [Подготовка кластеров HDInsight][Подготовка кластеров HDInsight]. Для выполнения учебника необходимы следующие данные:

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
    <td align="left">Это имя вашего кластера HDInsight.</td>
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
    <td align="left">Сервер базы данных SQL, куда Sqoop экспортирует данные или откуда импортирует данные.</td>
    </tr>
    <tr class="even">
    <td align="left">Имя для входа базы данных SQL</td>
    <td align="left">$sqlDatabaseLogin</td>
    <td align="left"></td>
    <td align="left">Имя для входа базы данных SQL</td>
    </tr>
    <tr class="odd">
    <td align="left">Пароль для входа базы данных SQL</td>
    <td align="left">$sqlDatabasePassword</td>
    <td align="left"></td>
    <td align="left">Пароль для входа базы данных SQL</td>
    </tr>
    <tr class="even">
    <td align="left">Имя базы данных SQL</td>
    <td align="left">$sqlDatabaseName</td>
    <td align="left"></td>
    <td align="left">Сервер базы данных SQL Azure, куда Sqoop экспортирует данные или откуда импортирует данные.</td>
    </tr>
    </tbody>
    </table>

    > [WACOM.NOTE] По умолчанию база данных SQL Azure разрешает подключения из служб Azure, таких как Azure HDInsight. Если этот параметр брандмауэра отключен, вы должны включить его на портале управления Azure. Инструкции по созданию базы данных SQL и настройке правил брандмауэра см. в разделе [Создание и настройка базы данных SQL][Создание и настройка базы данных SQL].

-   **SQL сервер**. Если ваш кластер HDInsight находится на том же сервере виртуальной сети Azure, что и SQL сервер, эта статья поможет вам разобраться с импортом и экспортом данных в базу данных SQL Server. Дополнительные сведения см. в следующих руководствах.

    > [WACOM.NOTE] \> Azure HDInsight поддерживает только географически привязанные виртуальные сети и на данный момент не поддерживает совместные групповые виртуальные сети.

    -   Для **создания и настройки виртуальной сети Azure**обратитесь к разделу [Настройка виртуальной сети][Настройка виртуальной сети].

        -   При использовании SQL сервера **в центре обработки данных** необходимо настроить тип соединения виртуальной сети либо как *сеть-сеть*, либо как *точка-сеть*.

            > [WACOM.NOTE] Для виртуальных сетей с типом соединения **точка-сеть** приложение настройки VPN клиента должно быть запущено на SQL сервере. Приложение доступно на **Панели инструментов** в настройках виртуальной сети Azure.

        -   При использовании SQL сервера на **Виртуальной машине Azure** можно использовать любую настройку виртуальной сети, если виртуальная машина с установленным SQL сервером и HDInsight находятся в одной виртуальной сети.

    -   Информация о **распределении кластеров HDInsight в виртуальной сети**доступна в разделе [Распределение кластеров Hadoop в HDInsight с использованием пользовательских настроек][Распределение кластеров Hadoop в HDInsight с использованием пользовательских настроек]

    > [WACOM.NOTE] Также SQL Server должен разрешать аутентификацию SQL. При выполнении заданий данного руководства вам понадобится имя пользователя SQL.

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
    <td align="left">Имя сервера SQL</td>
    <td align="left">$sqlDatabaseServer</td>
    <td align="left"></td>
    <td align="left">Сервер SQL, куда Sqoop экспортирует данные или откуда импортирует данные.</td>
    </tr>
    <tr class="even">
    <td align="left">Имя пользователя для сервера SQL</td>
    <td align="left">$sqlDatabaseLogin</td>
    <td align="left"></td>
    <td align="left">Имя пользователя для SQL.</td>
    </tr>
    <tr class="odd">
    <td align="left">Пароль для входа SQL</td>
    <td align="left">$sqlDatabasePassword</td>
    <td align="left"></td>
    <td align="left">Пароль для входа SQL</td>
    </tr>
    <tr class="even">
    <td align="left">Имя базы данных сервера SQL</td>
    <td align="left">$sqlDatabaseName</td>
    <td align="left"></td>
    <td align="left">База данных SQL, куда Sqoop экспортирует данные или откуда импортирует данные.</td>
    </tr>
    </tbody>
    </table>

> [WACOM.NOTE] Введите значения в таблицы. Это будет полезно при прохождении данного учебника.

## <span id="scenario"></span></a>Ознакомление со сценарием

Кластер HDInsight имеет несколько примеров данных. Будут использоваться два следующих примера:

-   Файл журнала log4j, расположенный в */example/data/sample.log*. Из файла извлекаются следующие журналы:

        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...

-   Таблица Hive с именем *hivesampletable*, которая ссылается на файл данных, расположенный в */hive/warehouse/hivesampletable*. Эта таблица содержит некоторые данные о мобильных устройствах. Схема таблицы Hive:

    | Поле                 | Тип данных |
    |----------------------|------------|
    | clientid             | string     |
    | querytime            | string     |
    | market               | string     |
    | deviceplatform       | string     |
    | devicemake           | string     |
    | devicemodel          | string     |
    | state                | string     |
    | country              | string     |
    | querydwelltime       | double     |
    | sessionid            | bigint     |
    | sessionpagevieworder | bigint     |

Сначала вы экспортируете sample.log и hivesampletable в базу данных SQL или на сервер SQL, а затем импортируете таблицу с данными о мобильных устройствах обратно в HDInsight, используя следующий путь:

    /tutorials/usesqoop/importeddata

### Общие сведения о хранилище HDInsight

HDInsight использует для хранения данных хранилище BLOB-объектов Azure. Оно называется *WASB* или *Хранилище Azure — BLOB-объекты*. WASB — это реализация HDFS на базе хранилища BLOB-объектов Azure от корпорации Майкрософт. Дополнительные сведения см. в разделе [Использование хранилища BLOB-объектов Azure с HDInsight][Использование хранилища BLOB-объектов Azure с HDInsight].

В процессе подготовки кластера HDInsight в качестве файловой системы по умолчанию устанавливаются учетная запись хранения Azure и конкретный контейнер хранилища BLOB-объектов из этой учетной записи, так же как и в HDFS. Помимо этой учетной записи хранения в процессе подготовки можно добавить дополнительные учетные записи хранения из той же подписки Azure или других подписок Azure. Инструкции по добавлению дополнительных учетных записей хранения см. в разделе [Подготовка кластеров HDInsight][Подготовка кластеров HDInsight]. Чтобы упростить скрипт PowerShell, используемый в этом учебнике, все файлы хранятся в контейнере файловой системы по умолчанию, расположенном в */tutorials/usesqoop*. Имя контейнера по умолчанию совпадает с именем кластера HDInsight.
 Синтаксис WASB:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] Кластер HDInsight версии 3.0 поддерживает только синтаксис *wasb://*. Прежний синтаксис *asv://* поддерживается в кластерах HDInsight 2.1 и 1.6, но не поддерживается в кластерах HDInsight 3.0 и не будет поддерживаться в последующих версиях..

> [WACOM.NOTE] Путь WASB является виртуальным. Дополнительные сведения см. в разделе [Использование хранилища BLOB-объектов Azure с HDInsight][Использование хранилища BLOB-объектов Azure с HDInsight].

Доступ к файлу, хранящемуся в контейнере файловой системы по умолчанию, из HDInsight может осуществляться с помощью любого из следующих URI (sample.log используется в качестве примера):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
    wasb:///example/data/sample.log
    /example/data/sample.log

Если требуется доступ к этому файлу непосредственно из учетной записи хранения, имя BLOB-объекта для файла имеет следующее значение:

    example/data/sample.log

## <span id="prepare"></span></a>Подготовка к работе с учебником

Вам нужно создать две таблицы в базе данных SQL и на сервере SQL. Таблицы будут позже использоваться для экспорта Sqoop на более поздних стадиях руководства. Также необходимо предварительно обработать файл sample.log перед его обработкой средством Sqoop.

### Создание базы данных SQL

**Для создания базы данных SQL Azure**

1.  Откройте интегрированную среду сценариев Windows PowerShell (на начальном экране Windows 8 введите **PowerShell\_ISE** и затем щелкните элемент **Интегрированная среда сценариев Windows PowerShell**. См. раздел [Запуск Windows PowerShell в Windows 8 и Windows][Запуск Windows PowerShell в Windows 8 и Windows]).

2.  Скопируйте следующий скрипт в область скриптов и задайте первые четыре переменных:

        #SQL database variables
        $sqlDatabaseServer = "<SQLDatabaseServerName>" 
        $sqlDatabaseLogin = "<SQLDatabaseUsername>"
        $sqlDatabasePassword = "<SQLDatabasePassword>"
        $sqlDatabaseName = "<SQLDatabaseName>" 

        $sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"

    Дополнительные описания переменных см. в разделе [Предварительные требования][Предварительные требования] данного учебника.

3.  Добавьте следующее содержимое в скрипт в области скриптов. Это инструкции SQL, определяющие две таблицы и их кластеризованные индексы. База данных SQL требует кластеризованный индекс.

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

4.  Добавьте следующий скрипт в область скриптов для выполнения команд SQL:

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

5.  Для выполнения скрипта щелкните **Выполнить скрипт** или нажмите клавишу **F5**.
6.  Используйте [Портал управления Аzure][Портал управления Аzure] для анализа таблиц и кластеризованных индексов.

**Для создания SQL сервера**

1.  Откройте **Среду SQL Server Management Studio** и создайте соединение с сервером SQL.

2.  Создайте новую базу данных с именем **sqoopdb**.

3.  Выберите базу данных **sqoopdb**, затем выберите **Новый запрос** на панели в верхней части SQL Server Management Studio.

4.  Откройте окно создания запроса.

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

5.  Нажмите функциональную клавишу **F5** или выберите **! Запустите команду** на панели, чтобы выполнить запрос. Под окном выполнения запроса должно появиться сообщение со следующим содержанием:

        Command(s) completed successfully.

6.  Закройте среду SQL Server Management Studio

### Создание данных

В этом учебнике вы экспортируете файл журнала log4j (файл с разделителями) и таблицу Hive в базу данных SQL. Файл с разделителями — */example/data/sample.log*. Ранее в учебнике вы видели несколько примеров журналов log4j. В файле журнала существует несколько пустых строк, а некоторые другие строки похожи на следующее:

    java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
        at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)

Это нормально для всех остальных программ, которые используют эти данные, но для импорта в базу данных SQL или на сервер SQL нам необходимо их удалить. Экспорт Sqoop не будет выполнен, если присутствует пустая строка или строка, число элементов в которой меньше количества полей, определенных в таблице базы данных SQL. Таблица log4jlogs содержит 7 полей типа "строка".

**Предварительная обработка файла sample.log**

1.  Откройте интегрированную среду сценариев Windows PowerShell.
2.  В нижней области выполните следующую команду для подключения к подписке Azure:

        Add-AzureAccount

    Появится запрос на ввод учетных данных учетной записи Azure. Этот метод добавления подключения подписки имеет срок действия, и после 12 часов вам потребуется снова выполнить вход.

    > [WACOM.NOTE] Если имеется несколько подписок Azure и должна использоваться подписка, отличная от подписки по умолчанию, воспользуйтесь командлетом **Select-AzureSubscription** для выбора текущей подписки.

3.  Скопируйте следующий скрипт в область скриптов и задайте первые две переменные:

        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<BlobContainerName>"

        $sourceBlobName = "example/data/sample.log"
        $destBlobName = "tutorials/usesqoop/data/sample.log"

    Дополнительные описания переменных см. в разделе [Предварительные требования][Предварительные требования] данного учебника.

4.  Добавьте следующий скрипт в область скриптов:

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

5.  Для выполнения скрипта щелкните **Выполнить скрипт** или нажмите клавишу **F5**.
6.  Чтобы проанализировать измененный файл данных, можно использовать портал управления Azure, средство обозревателя хранилища Azure или Azure PowerShell. В разделе [Приступая к работе с HDInsight][Приступая к работе с HDInsight] есть пример кода, где PowerShell используется для загрузки файла и отображения его содержимого.

## <span id="export"></span></a>Использование PowerShell для выполнения экспорта Sqoop

В этом разделе вы будете использовать Azure PowerShell для выполнения команды экспорта Sqoop для экспорта таблицы Hive и файла данных Sqoop в базу данных SQL Azure или на сервер SQL. Следующий раздел содержит пример HDInsight .NET.

> [WACOM.NOTE] Кроме информации в строке соединения, описанные в этом разделе решения должны работать для базы данных SQL Azure или сервера SQL. Данные решения были протестированы для следующей конфигурации:
>
> -   **Конфигурация виртуальной сети Azure типа "точка-сеть"** — Соединение через виртуальную сеть кластера HDInsight с сервером SQL в частном центре обработки данных. Дополнительную информацию см. в разделе [Настройка VPN "точка-сеть" на портале управления][Настройка VPN "точка-сеть" на портале управления] .
> -   **Azure HDInsight 3.1** — См. раздел [Распределение кластеров Hadoop в HDInsight с применением пользовательских настроек][Распределение кластеров Hadoop в HDInsight с использованием пользовательских настроек] для получения более подробной информации о создании кластера в виртуальной сети.
> -   **SQL Server 2014** — Настроенный под разрешение аутентификации SQL и использующий пакет программ настройки VPN-клиента для надежного соединения с виртуальной сетью.

**Экспорт файла журнала log4j**

1.  Откройте интегрированную среду сценариев Windows PowerShell.
2.  В нижней области выполните следующую команду для подключения к подписке Azure:

        Add-AzureAccount

    Появится запрос на ввод учетных данных учетной записи Azure.

3.  Скопируйте следующий скрипт в область скриптов и задайте первые семь переменных:

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

    Дополнительные описания переменных см. в разделе [Предварительные требования][Предварительные требования] данного учебника.

    Обратите внимание, что $exportDirlog4j не имеет указанного имени файла sample.log. Sqoop будет экспортировать данные из всех файлов в этой папке.

4.  Добавьте следующий скрипт в область скриптов:

        # Submit a Sqoop job
        $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
        $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
        Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob

        Write-Host "Standard Error" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
        Write-Host "Standard Output" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

    Обратите внимание, что в качестве разделителя полей используется **\\0x20**, то есть пробел. Разделитель определяется в файле sample.log в скрипте PowerShell предварительной обработки. Чтобы узнать о **-m 1**, см. [руководство пользователя Sqoop][Sqoop].

5.  Для выполнения скрипта щелкните **Выполнить скрипт** или нажмите клавишу **F5**.
6.  Используйте [Портал управления Azure][Портал управления Аzure] для анализа экспортированных данных.

**Экспорт таблицы hivesampletable Hive**

1.  Откройте интегрированную среду сценариев Windows PowerShell.
2.  В нижней области выполните следующую команду для подключения к подписке Azure:

        Add-AzureAccount

    Появится запрос на ввод учетных данных учетной записи Azure.

3.  Скопируйте следующий скрипт в область скриптов и задайте первые семь переменных:

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

    Дополнительные описания переменных см. в разделе [Предварительные требования][Предварительные требования] данного учебника.

4.  Добавьте следующий скрипт в область скриптов:

        $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"


        $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
        Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob

        Write-Host "Standard Error" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
        Write-Host "Standard Output" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5.  Для выполнения скрипта щелкните **Выполнить скрипт** или нажмите клавишу **F5**.
6.  Используйте [Портал управления Azure][Портал управления Аzure] для анализа экспортированных данных.

## <span id="export-sdk"></span></a>Использование HDInsight .NET SDK для выполнения экспорта Sqoop

Ниже приведен пример на C#, использующий пакет SDK для HDInsight. Net для выполнения экспорта Sqoop. Общие сведения об использовании пакета HDInsight .NET SDK см. в разделе [Отправка заданий Hadoop программными средствами][Отправка заданий Hadoop программными средствами].

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

Этот файл скрипта должен находиться в WASB.

## <span id="import"></span></a>Использование PowerShell для выполнения импорта Sqoop

В этом разделе вы будете импортировать журналы log4j (экспортированные в базу данных SQL) обратно в HDInsight.

1.  Откройте интегрированную среду сценариев Windows PowerShell.
2.  В нижней области выполните следующую команду для подключения к подписке Azure:

        Add-AzureAccount

    Появится запрос на ввод учетных данных учетной записи Azure.

3.  Скопируйте следующий скрипт в область скриптов и задайте первые семь переменных:

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

    Дополнительные описания переменных см. в разделе [Предварительные требования][Предварительные требования] данного учебника.

4.  Добавьте следующий скрипт в область скриптов:

        $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"

        $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
        Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob

        Write-Host "Standard Error" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
        Write-Host "Standard Output" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5.  Для выполнения скрипта щелкните **Выполнить скрипт** или нажмите клавишу **F5**.
6.  Чтобы проанализировать измененный файл данных, можно использовать портал управления Azure, средство обозревателя хранилища Azure или Azure PowerShell. В разделе [Приступая к работе с HDInsight][Приступая к работе с HDInsight] есть пример кода, где PowerShell используется для загрузки файла и отображения его содержимого.

## <span id="nextsteps"></span></a> Дальнейшие действия

Теперь вы узнали, как использовать Sqoop. Дополнительные сведения см. на следующих ресурсах:

-   [Использование Oozie с HDInsight][Использование Oozie с HDInsight]: используйте действие Sqoop в рабочем процессе Oozie.
-   [Анализ данных о задержке рейсов с помощью HDInsight][Анализ данных о задержке рейсов с помощью HDInsight]: используйте Hive для анализа данных о задержке рейсов, а затем используйте Sqoop для экспорта данных в базу данных SQL.
-   [Отправка данных в HDInsight][Отправка данных в HDInsight]: узнайте о других способах отправки данных в HDInsight/хранилище BLOB-объектов Azure.

  [Что такое Sqoop?]: #whatissqoop
  [Предварительные требования]: #prerequisites
  [Ознакомление со сценарием учебника]: #scenario
  [Подготовка к работе с учебником]: #prepare
  [Использование PowerShell для выполнения экспорта Sqoop]: #export
  [Использование HDInsight SDK для выполнения экспорта Sqoop]: #export-sdk
  [Использование PowerShell для выполнения импорта Sqoop]: #import
  [Дальнейшие действия]: #nextsteps
  [Sqoop]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
  [Новые возможности версий кластеров, предоставляемых HDInsight]: ../hdinsight-component-versioning/
  [Установка и настройка Azure PowerShell]: ../install-configure-powershell
  [Выполнение скриптов Windows PowerShell]: http://technet.microsoft.com/ru-ru/library/ee176949.aspx
  [Приступая к работе с HDInsight]: ../hdinsight-get-started/
  [Подготовка кластеров HDInsight]: ../hdinsight-provision-clusters/
  [Приступая к работе с базой данных SQL Azure]: ../sql-database-get-started/
  [Создание и настройка базы данных SQL]: ../sql-database-create-configure/
  [Настройка виртуальной сети]: http://msdn.microsoft.com/ru-ru/library/azure/jj156206.aspx
  [Распределение кластеров Hadoop в HDInsight с использованием пользовательских настроек]: /ru-ru/documentation/articles/hdinsight-provision-clusters/
  [Использование хранилища BLOB-объектов Azure с HDInsight]: ../hdinsight-use-blob-storage/
  [Запуск Windows PowerShell в Windows 8 и Windows]: http://technet.microsoft.com/ru-ru/library/hh847889.aspx
  [Портал управления Аzure]: https://manage.windowsazure.com/
  [Настройка VPN "точка-сеть" на портале управления]: http://msdn.microsoft.com/ru-ru/library/azure/dn133792.aspx
  [Отправка заданий Hadoop программными средствами]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Использование Oozie с HDInsight]: ../hdinsight-use-oozie/
  [Анализ данных о задержке рейсов с помощью HDInsight]: ../hdinsight-analyze-flight-delay-data/
  [Отправка данных в HDInsight]: ../hdinsight-upload-data/
