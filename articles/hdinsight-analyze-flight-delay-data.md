<properties linkid="manage-services-hdinsight-analyze-flight-delay-data" urlDisplayName="Analyze flight delay data with Hadoop in HDInsight" pageTitle="Analyze flight delay data using Hadoop in HDInsight | Azure" metaKeywords="" description="Learn how to upload data to HDInsight, how to process the data using Hive, and how to export the results to SQL Database using Sqoop." metaCanonical="" services="hdinsight" documentationCenter="" title="Analyze flight delay data using Hadoop in HDInsight " authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Анализ данных о задержке рейсов с помощью Hadoop в HDInsight

Hive предоставляет средства для выполнения задания Hadoop MapReduce через SQL-подобный язык сценариев под названием *[HiveQL][HiveQL]*, который может применяться к сведению данных, созданию запросов и анализу больших объемов данных. В этом учебнике показано, как использовать Hive для расчета значений средней задержки по аэропортам и как использовать Sqoop для экспорта результатов в базу данных SQL.

**Предварительные требования:**

Перед началом работы с этим учебником необходимо иметь следующее:

-   Кластер Azure HDInsight. Сведения о подготовке кластера HDInsight см. в разделе [Приступая к работе с HDInsight][Приступая к работе с HDInsight] или [Подготовка кластеров HDInsight][Подготовка кластеров HDInsight].
-   Рабочая станция, на которой установлена и настроена среда Azure PowerShell. Инструкции см. в разделе [Установка и настройка Azure PowerShell][Установка и настройка Azure PowerShell].

**Предполагаемое время выполнения:** 30 минут

## В этом учебнике рассматриваются следующие темы:

-   [Подготовка к работе с учебником][Подготовка к работе с учебником]
-   [Создание и отправка скрипта HiveQL][Создание и отправка скрипта HiveQL]
-   [Выполнение скрипта HiveQL][Выполнение скрипта HiveQL]
-   [Экспорт выходных данных в базу данных SQL Azure][Экспорт выходных данных в базу данных SQL Azure]
-   [Дальнейшие действия][Дальнейшие действия]

## <span id="prepare"></span></a>Подготовка к работе с учебником

В этом учебнике данные о текущей работе рейсов авиакомпании, полученные из [бюро транспортной статистики при администрации по исследованиям и инновационным технологиям][бюро транспортной статистики при администрации по исследованиям и инновационным технологиям] (RITA), используются на вашей рабочей станции. Будут выполнены следующие задачи:

1.  Загрузка данных о текущей работе из RITA на вашу рабочую станцию с помощью веб-браузера.
2.  Отправка данных в HDInsight с использованием Azure PowerShell.
3.  Подготовка базы данных SQL для экспорта данных с помощью Azure PowerShell.

**Общие сведения о хранилище HDInsight**

HDInsight использует для хранения данных хранилище BLOB-объектов Azure. Оно называется *WASB* или *Хранилище Azure — BLOB-объекты*. WASB — это реализация HDFS на базе хранилища BLOB-объектов Azure от корпорации Майкрософт. Дополнительные сведения см. в разделе [Использование хранилища BLOB-объектов Azure с HDInsight][Использование хранилища BLOB-объектов Azure с HDInsight].

При подготовке кластера HDInsight контейнер хранилища BLOB-объектов используется в качестве файловой системы по умолчанию, так же как и в HDFS. Помимо этого контейнера в процессе подготовки можно добавить дополнительные контейнеры из той же учетной записи хранения Azure или других учетных записей хранения Azure. Инструкции по добавлению дополнительных учетных записей хранения см. в разделе [Подготовка кластеров HDInsight][Подготовка кластеров HDInsight].

Чтобы упростить скрипт PowerShell, используемый в этом учебнике, все файлы хранятся в контейнере файловой системы по умолчанию, расположенном в */tutorials/flightdelays*. Имя контейнера по умолчанию совпадает с именем кластера HDInsight.

Синтаксис WASB:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] Кластер HDInsight версии 3.0 поддерживает только синтаксис *wasb://*. Прежний синтаксис *asv://* поддерживается в кластерах HDInsight 2.1 и 1.6, но не поддерживается в кластерах HDInsight 3.0 и не будет поддерживаться в последующих версиях..

> Путь WASB является виртуальным. Дополнительные сведения см. в разделе [Использование хранилища BLOB-объектов Azure с HDInsight][Использование хранилища BLOB-объектов Azure с HDInsight].

Для файла, хранящегося в контейнере файловой системы по умолчанию, доступ из HDInsight может осуществляться с помощью любого из следующих URI (flightdelays.hql используется в качестве примера):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/flightdelays/flightdelays.hql
    wasb:///tutorials/flightdelays/flightdelays.hql
    /tutorials/flightdelays/flightdelays.hql

Если требуется доступ к этому файлу непосредственно из учетной записи хранения, имя BLOB-объекта для файла имеет следующее значение:

    tutorials/flightdelays/flightdelays.hql

В следующей таблице перечислены файлы, используемые в этом учебнике:

| **Файлы**                                   | **Описание**                     |
|---------------------------------------------|----------------------------------|
| \\tutotirals\\flightdelays\\data            | входные данные о рейсах для Hive |
| \\tutorials\\flightdelays\\output           | выходные данные для Hive         |
| \\tutorials\\flightdelays\\flightdelays.hql | Файл скрипта HiveQL.             |
| \\tutorials\\flightdelays\\jobstatus        | состояние задания Hadoop         |

**Общие сведения о внутренней и внешней таблицах Hive**

Есть несколько вещей, которые нужно знать о внутренней и внешней таблицах Hive.

-   Команда CREATE TABLE создает внутреннюю таблицу. Файл данных должен находиться в контейнере по умолчанию.
-   Команда CREATE TABLE перемещает файл данных в папку /hive/warehouse/<tablename>.
-   Команда CREATE EXTERNAL TABLE создает внешнюю таблицу. Файл данных может находиться за пределами контейнера по умолчанию.
-   Команда CREATE EXTERNAL TABLE не перемещает файл данных.
-   Команда CREATE EXTERNAL TABLE не разрешает наличие любых папок в LOCATION. Именно по этой причине в учебнике создается копия файла sample.log.

Дополнительные сведения см. в разделе [HDInsight: введение по внутренним и внешним таблицам Hive][cindygross-hive-tables].

> [WACOM.NOTE] Одна из инструкций HiveQL создает внешнюю таблицу Hive. Внешняя таблица Hive хранит файл данных в исходном расположении. Внутренняя таблица Hive перемещает файл данных в hive\\warehouse. Внешняя таблица Hive требует, чтобы файл данных находился в контейнере по умолчанию файловой системы WASB. Если для хранения файлов данных о рейсах выбран контейнер, отличный от контейнера BLOB-объектов по умолчанию, необходимо использовать внутренние таблицы Hive.

**Загрузка данных о рейсах**

1.  Перейдите к [Бюро транспортной статистики при администрации по исследованиям и инновационным технологиям][бюро транспортной статистики при администрации по исследованиям и инновационным технологиям] (RITA).
2.  На странице выберите следующие значения:

    | Имя            | Значение                                                                                                                                                                                                                                                                                                                                                                                      |
    |----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Фильтр года    | 2012                                                                                                                                                                                                                                                                                                                                                                                          |
    | Период фильтра | Январь                                                                                                                                                                                                                                                                                                                                                                                        |
    | Поля:          | \*Year\*, \*FlightDate\*, \*UniqueCarrier\*, \*Carrier\*, \*FlightNum\*, \*OriginAirportID\*, \*Origin\*, \*OriginCityName\*, \*OriginState\*, \*DestAirportID\*, \*Dest\*, \*DestCityName\*, \*DestState\*, \*DepDelayMinutes\*, \*ArrDelay\*, \*ArrDelayMinutes\*, \*CarrierDelay\*, \*WeatherDelay\*, \*NASDelay\*, \*SecurityDelay\*, \*LateAircraftDelay\* (очистите все остальные поля) |

3.  Щелкните элемент **Загрузить**. Загрузка каждого файла может занять до 15 минут.
4.  Распакуйте файл в папку **C:\\Tutorials\\FlightDelays\\Data**. Каждый файл представляет собой CSV-файл и имеет размер около 60 ГБ.
5.  Переименуйте файл в название месяца, данные по которому он содержит. Например, файл, содержащий данные за январь, следует назвать *January.csv*.
6.  Повторите шаг 2 и 5, чтобы загрузить файл для каждого из 12 месяцев в 2012 году. Для запуска учебника вам потребуется хотя бы один файл.

**Отправка данных о задержке рейсов в хранилище BLOB-объектов Azure**

1.  Откройте Azure PowerShell. Инструкции см. в разделе [Установка и настройка Azure PowerShell][Установка и настройка Azure PowerShell].
2.  Выполните следующую команду для подключения к подписке Azure:

        Add-AzureAccount

    Появится запрос на ввод учетных данных учетной записи Azure.

3.  Задайте первые три переменные, а затем выполните команды.

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $ContainerName = "<BlobStorageContainerName>"

        $localFolder = "C:\Tutorials\FlightDelays\Data"
        $destFolder = "tutorials/flightdelays/data"

        $month = "January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"

    Ниже приведены переменные и их описание:

    <table border="1">

    <tr>
    <td>
    **Имя переменной**

    </td>
    <td>
    **Описание**

    </td>
    </tr>

    <tr>
    <td>
    $subscriptionName

    </td>
    <td>
    Имя подписки Azure.

    </td>
    </tr>

    <tr>
    <td>
    $storageAccountName

    </td>
    <td>
    Учетная запись хранения Azure, используемая для хранения файлов данных о рейсах. Рекомендуется использовать учетную запись хранения по умолчанию.

    </td>
    </tr>

    <tr>
    <td>
    $containerName

    </td>
    <td>
    Контейнер хранилища BLOB-объектов Azure, используемый для хранения файлов данных о рейсах. Рекомендуется использовать контейнер BLOB-объектов файловой системы кластера HDInsight по умолчанию. По умолчанию его имя совпадает с именем кластера HDInsight.

    </td>
    </tr>

    <tr>
    <td>
    $localFolder

    </td>
    <td>
    Это папка на рабочей станции, где хранятся файлы данных о задержках рейсов.

    </td>
    </tr>

    <tr>
    <td>
    $destFolder

    </td>
    <td>
    Путь WASB, куда будут отправлены данные о задержке рейсов. Путь Hadoop (HDInsight) указывается с учетом регистра.

    </td>
    </tr>

    <tr>
    <td>
    $month

    </td>
    <td>
    Если вы не загрузили все 12 файлов, необходимо обновить эту переменную.

    </td>
    </tr>

    </table>
    </p>
4.  Выполните следующие команды, чтобы отправить и перечислить файлы:

        # Create a storage context object
        Select-AzureSubscription $subscriptionName
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Copy the file from local workstation to WASB        
        foreach ($item in $month){
            $fileName = "$localFolder\$item.csv"
            $blobName = "$destFolder/$item.csv"

            Write-Host "Copying $fileName to $blobName" -BackgroundColor Green

            Set-AzureStorageBlobContent -File $fileName -Container $dataContainerName -Blob $blobName -Context $destContext
        }

        # List the uploaded files on HDinsight
        Get-AzureStorageBlob -Container $dataContainerName  -Context $destContext -Prefix $destFolder

Если вы решили использовать другой метод для отправки файлов, убедитесь, что путь к файлу имеет значение *tutorials/flightdelays/data*. Для доступа к файлам используется следующий синтаксис:

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelays/data

*tutorials/flightdelays/data* является виртуальной папкой, созданной вами при отправке файлов. Убедитесь, что присутствует 12 файлов — по одному для каждого месяца.

**Подготовка базы данных SQL**

1.  Откройте Azure PowerShell.
2.  Выполните следующую команду для подключения к подписке Azure:

        Add-AzureAccount

    Появится запрос на ввод учетных данных учетной записи Azure.

3.  Задайте первые шесть переменных в следующем скрипте, а затем выполните команды:

        # Azure subscription name
        $subscriptionName = "<AzureSubscriptionName>"

        # SQL database server variables
        $sqlDatabaseServer = ""  # specify the Azure SQL database server name if you have one created. Otherwise use "".
        $sqlDatabaseUsername = "<SQLDatabaseUserName>"
        $sqlDatabasePassword = "<SQLDatabasePassword>"
        $sqlDatabaseLocation = "<MicrosoftDataCenter>"   #For example, West US.

        # SQL database variables
        $sqlDatabaseName = ""  # specify the database name if you have one created.  Otherwise use "" to have the script create one for you.
        $sqlDatabaseMaxSizeGB = 10

        #SQL query string for creating AvgDelays table
        $sqlDatabaseTableName = "AvgDelays"
        $sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                [origin_city_name] [nvarchar](50) NOT NULL,
                [weather_delay] float,
            CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED   
            (
                [origin_city_name] ASC
            )
            )"

    Ниже приведены переменные и их описание:

    <table border="1">

    <tr>
    <td>
    **Имя переменной**

    </td>
    <td>
    **Описание**

    </td>
    </tr>

    <tr>
    <td>
    $subscriptionName

    </td>
    <td>
    Имя подписки Azure.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseServer

    </td>
    <td>
    Имя сервера базы данных SQL, используемое Sqoop для экспорта данных. Если не вносить никаких изменений, сценарий сам создаст это имя. В противном случае укажите существующую базу данных SQL или SQL Server.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseUsername

    </td>
    <td>
    Имя пользователя базы данных SQL/SQL Server.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabasePassword

    </td>
    <td>
    Пароль пользователя базы данных SQL/SQL Server.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseLocation

    </td>
    <td>
    Используется только в том случае, если требуется, чтобы скрипт сам создал сервер базы данных SQL.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseName

    </td>
    <td>
    Имя базы данных SQL, используемое Sqoop для экспорта данных. Если не вносить никаких изменений, сценарий сам создаст это имя. В противном случае укажите существующую базу данных SQL или SQL Server.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseMaxSizeGB

    </td>
    <td>
    Используется только в том случае, если требуется, чтобы скрипт сам создал базу данных SQL.

    </td>
    </tr>

    </table>
    </p>
4.  Выполните следующие команды для создания сервера, базы данных или таблицы базы данных SQL.

        # Select the current Azure subscription in case there are multiple subscriptions
        Select-AzureSubscription $subscriptionName

        # create a new Azure SQL Database if requested
        if ([string]::IsNullOrEmpty($sqlDatabaseServer))
        {
            $sqlDatabaseServer = New-AzureSqlDatabaseServer -AdministratorLogin $sqlDatabaseUsername -AdministratorLoginPassword $sqlDatabasePassword -Location $sqlDatabaseLocation 
            Write-Host "The new SQL Database server is $sqlDatabaseServer."  -BackgroundColor Green

        }
        else
        {
            Write-Host "Use an existing SQL Database server: $sqlDatabaseServer" -BackgroundColor Green
        }

        # Create a new SQL database if requested
        if ([string]::IsNullOrEmpty($sqlDatabaseName))
        {
            $sqlDatabaseName = "HDISqoop"
            $sqlDatabaseServerCredential = new-object System.Management.Automation.PSCredential($sqlDatabaseUsername, ($sqlDatabasePassword  | ConvertTo-SecureString -asPlainText -Force)) 
            $sqlDatabaseServerConnectionContext = New-AzureSqlDatabaseServerContext -ServerName $sqlDatabaseServer -Credential $sqlDatabaseServerCredential 

            $sqlDatabase = New-AzureSqlDatabase -ConnectionContext $sqlDatabaseServerConnectionContext -DatabaseName $sqlDatabaseName -MaxSizeGB $sqlDatabaseMaxSizeGB

            Write-Host "The new SQL Database is $sqlDatabaseName."  -BackgroundColor Green

        }
        else
        {
            Write-Host "Use an existing SQL Database : $sqlDatabaseName" -BackgroundColor Green
        }

        #Create AvgDelays table
        $conn = New-Object System.Data.SqlClient.SqlConnection
        $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseUsername;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
        $conn.open()
        $cmd = New-Object System.Data.SqlClient.SqlCommand
        $cmd.connection = $conn
        $cmd.commandtext = $sqlCreateAvgDelaysTable
        $cmd.executenonquery()

        $conn.close()

## <span id="createscript"></span></a>Создание и отправка скрипта HiveQL

С помощью Azure PowerShell можно одновременно запустить несколько инструкций HiveQL по одной за раз или упаковать инструкцию HiveQL в файл скрипта. В этом учебнике будет создан скрипт HiveQL. Этот файл скрипта необходимо отправить в WASB. В следующем разделе будет выполнен файл скрипта с помощью Azure PowerShell.

Скрипт HiveQL выполнит следующее:

1.  **Удаляет таблицу delays\_raw**, если она уже существует.
2.  **Создает внешнюю таблицу Hive delays\_raw**, указывающую на расположение WASB с файлами данных о задержках рейсов. Этот запрос задает то, что поля разделяются с помощью "," и что строки завершаются с помощью "\\n". Это создает проблему при *наличии* запятых в значениях полей, так как Hive не отличает запятую, которая является разделителем поля, и запятую, которая является частью значения поля (что характерно для значений полей ORIGIN\_CITY\_NAME и DEST\_CITY\_NAME). Чтобы решить эту проблему, запрос создает столбцы TEMP для хранения данных, неправильно разбитых на столбцы.
3.  **Удаляет таблицу delays**, если она уже существует.
4.  **Создает таблицу delays**. Это удобно для очистки данных перед дальнейшей обработкой. Этот запрос создает новую таблицу *delays* из таблицы *delays\_raw*. Обратите внимание, что столбцы TEMP (как уже упоминалось ранее) не копируются, а функция *substring* используется для удаления кавычек из данных.
5.  **Вычисляет среднюю задержку из-за погоды и группирует результаты по названию города.** Он также выводит результаты в WASB. Обратите внимание, что запрос удаляет апострофы из данных и исключает строки, где *weather\_deal*y имеет значение *null*, что необходимо, так как Sqoop, используемый далее в этом руководстве, по умолчанию обрабатывает эти значения некорректно.

Полный список команд HiveQL см. в разделе [Язык описания данных Hive][HiveQL]. Каждая команда HiveQL должна завершаться точкой с запятой.

**Создание файла скрипта HiveQL**

1.  Откройте Azure PowerShell.
2.  Выполните следующую команду для подключения к подписке Azure:

        Add-AzureAccount

3.  Задайте первые две переменные, а затем выполните команды.

        $storageAccountName = "<AzureStorageAccountName>"
        $containerName ="<BlobStorageContainerName>"

        $hqlLocalFileName = "C:\tutorials\flightdelays\flightdelays.hql"  
        $hqlBlobName = "tutorials/flightdelays/flightdelays.hql" 

        $srcDataFolder = "tutorials/flightdelays/data" 
        $dstDataFolder = "tutorials/flightdelays/output"

    Ниже приведены переменные и их описание:

    <table border="1">

    <tr>
    <td>
    **Имя переменной**

    </td>
    <td>
    **Описание**

    </td>
    </tr>

    <tr>
    <td>
    $storageAccountName

    </td>
    <td>
    Учетная запись хранения Azure, используемая для хранения файла скрипта HiveQL. Скриптам PowerShell в этом учебнике требуются как файлы данных о рейсах, так и файл скрипта, расположенный в той же учетной записи хранения Azure и контейнере BLOB-объектов.

    </td>
    </tr>

    <tr>
    <td>
    $containerName

    </td>
    <td>
    Контейнер хранилища BLOB-объектов Azure, используемый для хранения файла скрипта HiveQL. Скриптам PowerShell в этом учебнике требуются как файлы данных о рейсах, так и файл скрипта, расположенный в той же учетной записи хранения Azure и контейнере BLOB-объектов.

    </td>
    </tr>

    <tr>
    <td>
    $hqlLocalFileName

    </td>
    <td>
    Локальное имя файла для скрипта HiveQL до его отправки в WASB. Чтобы упростить сценарий PowerShell, вы запишете файл локально, а затем отправите файл скрипта в HDInsight с помощью командлета Set-AzureStorageBlobContent.

    </td>
    </tr>

    <tr>
    <td>
    $hqlBlobName

    </td>
    <td>
    Имя файла скрипта с путем в WASB.

    </td>
    </tr>

    <tr>
    <td>
    $srcDataFolder

    </td>
    <td>
    Это папка в WASB, откуда скрипт HiveQL извлекает данные.

    </td>
    </tr>

    <tr>
    <td>
    $dstDataFolder

    </td>
    <td>
    Это папка в WASB, куда скрипт HiveQL отправляет выходные данные. Позже в этом учебнике вы будете использовать Sqoop для экспорта данных из этой папки в базу данных SQL Azure.

    </td>
    </tr>

    </table>
    </p>
4.  Выполните следующие команды, чтобы определить инструкции HiveQL:

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
            "LOCATION 'wasb://$containerName@$storageAccountName.blob.core.windows.net/$srcDataFolder';" 

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

        $hqlInsertLocal = "INSERT OVERWRITE DIRECTORY 'wasb://$containerName@$storageAccountName.blob.core.windows.net/$dstDataFolder' " +
            "SELECT regexp_replace(origin_city_name, '''', ''), " +
                "avg(weather_delay) " +
            "FROM delays " +
            "WHERE weather_delay IS NOT NULL " +
            "GROUP BY origin_city_name;"

        $hqlScript = $hqlDropDelaysRaw + $hqlCreateDelaysRaw + $hqlDropDelays + $hqlCreateDelays + $hqlInsertLocal

5.  Выполните следующие команды, чтобы записать файл скрипта на рабочей станции и отправить его в WASB:

        # Write the Hive script into a local file
        $hqlScript | Out-File $hqlLocalFileName -Encoding ascii

        # Create a storage context object
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Copy the file from local workstation to WASB
        Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $containerName -Blob $hqlBlobName -Context $destContext

        # List the script file from WASB
        Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $hqlBlobName

    Результат должен быть аналогичен приведенному ниже:

        Container Uri: https://xxxxxxxx.blob.core.windows.net/hdi0212v3

        Name                              BlobType   Length                            ContentType                       LastModified                      SnapshotTime
        ----                              --------   ------                            -----------                       ------------                      ------------
        tutorials/flightdelays/flightd... BlockBlob  1938                              application/octet-stream          2/12/2014 9:57:28 PM +00:00

## <span id="executehqlscript"></span></a>Выполнение скрипта HiveQL

Существует несколько командлетов Azure PowerShell, которые можно использовать для запуска Hive. В этом учебнике используется Invoke-Hive. Другие способы см. в разделе [Использование Hive с HDInsight][Использование Hive с HDInsight]. Используя Invoke-Hive, можно запустить инструкцию HiveQL или скрипт HiveQL. Вы будете использовать скрипт HiveQL, который вы создали и отправили в хранилище BLOB-объектов Azure.

Существует известная проблема с путем Hive. Инструкции по устранению этой проблемы можно найти на [вики-сайте TechNet][вики-сайте TechNet].

**Выполнение запросов Hive с помощью PowerShell**

1.  Откройте Azure PowerShell.
2.  Выполните следующую команду, чтобы изменить текущий каталог:

    cd \\Tutorials\\FlightDelays\\

    Этот шаг является обязательным, поскольку вы будете загружать копию выходных данных Hive на рабочую станцию. По умолчанию у вас нет разрешения на запись в папку PowerShell.

3.  Выполните следующую команду для подключения к подписке Azure:

        Add-AzureAccount

4.  Задайте первые три переменные, а затем выполните их:

        $clusterName = "<HDInsightClusterName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureBlobStorageContainerName>"
        $hqlScriptFile = "wasb://$containerName@$storageAccountName.blob.core.windows.net/tutorials/flightdelays/flightdelays.hql"
        $outputBlobName = "tutorials/flightdelays/output/000000_0"

    Ниже приведены переменные и их описание:

    <table border="1">

    <tr>
    <td>
    **Имя переменной**

    </td>
    <td>
    **Описание**

    </td>
    </tr>

    <tr>
    <td>
    $clusterName

    </td>
    <td>
    Кластер HDInsight, который будет выполнять скрипт Hive и экспорт Sqoop.

    </td>
    </tr>

    <tr>
    <td>
    $storageAccountName

    </td>
    <td>
    Учетная запись хранения Azure, используемая для хранения скрипта HiveQL. См. раздел [Создание и отправка скрипта HiveQL][1].

    </td>
    </tr>

    <tr>
    <td>
    $containerName

    </td>
    <td>
    Контейнер хранилища BLOB-объектов Azure, используемый для хранения скрипта HiveQL. См. раздел [Создание и отправка скрипта HiveQL][1].

    </td>
    </tr>

    <tr>
    <td>
    $hqlScriptFile

    </td>
    <td>
    Это URI для файла скрипта HiveQL.

    </td>
    </tr>

    <tr>
    <td>
    $outputBlobName

    </td>
    <td>
    Это файл выходных данных скрипта HiveQL. Имя файла по умолчанию: *000000\_0*.

    </td>
    </tr>

    </table>
    </p>
5.  Выполните следующую команду, чтобы вызвать Hive:

        Use-AzureHDInsightCluster $clusterName

        Invoke-Hive –File $hqlScriptFile

6.  Выполните следующую команду, чтобы проверить результаты:

        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

        Get-AzureStorageBlobContent -Container $ContainerName -Blob $outputBlobName -Context $storageContext 

        cat ".\$outputBlobName" | findstr /B "Ch"

    Результат должен быть аналогичен приведенному ниже:

        Champaign/Urbana 19.023255813953487
        Charleston 24.93975903614458
        Charleston/Dunbar 16.954545454545453
        Charlotte 16.88616981831665
        Charlotte Amalie 12.88888888888889
        Charlottesville 25.444444444444443
        Chattanooga 19.883561643835616
        Cheyenne 9.875
        Chicago 16.77321649680922
        Chico 12.340909090909092
        Christiansted 12.318181818181818

    Между каждым городом и временной задержкой присутствует разделитель, который не отображается в окне выходных данных PowerShell. Он имеет значение "\\001". Этот разделитель будет использоваться при выполнении экспорта Sqoop.

## <span id="exportdata"></span></a>Экспорт выходных данных задания Hive в базу данных SQL Azure

Последние действия заключаются в выполнении экспорта Sqoop, чтобы экспортировать данные в базу данных SQL. Ранее в этом учебнике вы создали базу данных SQL и таблицу AvgDelays.

**Экспорт данных в базу данных SQL**

1.  Откройте Azure PowerShell.
2.  Выполните следующую команду для подключения к подписке Azure:

        Add-AzureAccount

3.  Задайте первые пять переменных, а затем выполните команды:

        $clusterName = "<HDInsightClusterName>"

        $sqlDatabaseServerName = "<SQLDatabaseServerName>"
        $sqlDatabaseUserName = "<SQLDatabaseUsername>"
        $sqlDatabasePassword = "<SQLDatabasePassword>"

        $sqlDatabaseName = "<SQLDatabaseName>" # The default name is "HDISqoop"
        $sqlDatabaseTableName = "AvgDelays" 

        $exportDir = "wasb://$containerName@$storageAccountName.blob.core.windows.net/tutorials/flightdelays/output"

        $sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseUserName@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"

    Ниже приведены переменные и их описание:

    <table border="1">

    <tr>
    <td>
    **Имя переменной**

    </td>
    <td>
    **Примечание.**

    </td>
    </tr>

    <tr>
    <td>
    $clusterName

    </td>
    <td>
    Имя кластера HDInsight.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseServer

    </td>
    <td>
    Сервер базы данных SQL, куда Sqoop экспортирует данные.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseUsername

    </td>
    <td>
    Имя пользователя базы данных SQL.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabasePassword

    </td>
    <td>
    Пароль пользователя базы данных SQL.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseName

    </td>
    <td>
    Сервер базы данных SQL, куда Sqoop экспортирует данные. Имя файла по умолчанию: \*HDISqoop".

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseTableName

    </td>
    <td>
    Сервер базы данных SQL, куда Sqoop экспортирует данные. Имя по умолчанию: AvgDelays. Это таблица, созданная вами ранее в этом учебнике.

    </td>
    </tr>

    <tr>
    <td>
    $exportDir

    </td>
    <td>
    Это расположение файла выходных данных Hive. Sqoop будет экспортировать файлы из этого расположения в базу данных SQL.

    </td>
    </tr>

    </table>
    </p>
4.  Выполните следующую команду, чтобы экспортировать данные:

        $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "

        $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
        Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob

        Write-Host "Standard Error" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
        Write-Host "Standard Output" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5.  Подключитесь к базе данных SQL и просмотрите средние значения задержки рейсов по городу в таблице *AvgDelays*:

    ![HDI.FlightDelays.AvgDelays.Dataset][HDI.FlightDelays.AvgDelays.Dataset]

## <span id="nextsteps"></span></a>Дальнейшие действия

Теперь вы знаете, как отправить файл в хранилища BLOB-объектов, как заполнить таблицу Hive, используя данные из хранилища BLOB-объектов, как выполнять запросы Hive и использовать Sqoop для экспорта данных из HDFS в базу данных SQL Azure. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

-   [Приступая к работе с HDInsight][Приступая к работе с HDInsight]
-   [Использование Hive с HDInsight][Использование Hive с HDInsight]
-   [Использование Oozie с HDInsight][Использование Oozie с HDInsight]
-   [Использование Sqoop вместе с HDInsight][Использование Sqoop вместе с HDInsight]
-   [Использование Pig с HDInsight][Использование Pig с HDInsight]
-   [Разработка программ MapReduce на Java для HDInsight][Разработка программ MapReduce на Java для HDInsight]
-   [Разработка программ потоковой передачи Hadoop на C# для HDInsight][Разработка программ потоковой передачи Hadoop на C# для HDInsight]

  [HiveQL]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
  [Приступая к работе с HDInsight]: ../hdinsight-get-started/
  [Подготовка кластеров HDInsight]: ../hdinsight-provision-clusters/
  [Установка и настройка Azure PowerShell]: ../install-configure-powershell/
  [Подготовка к работе с учебником]: #prepare
  [Создание и отправка скрипта HiveQL]: #createscript
  [Выполнение скрипта HiveQL]: #executehqlscript
  [Экспорт выходных данных в базу данных SQL Azure]: #exportdata
  [Дальнейшие действия]: #nextsteps
  [бюро транспортной статистики при администрации по исследованиям и инновационным технологиям]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
  [Использование хранилища BLOB-объектов Azure с HDInsight]: ../hdinsight-use-blob-storage/
  [Использование Hive с HDInsight]: ../hdinsight-use-hive/
  [вики-сайте TechNet]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
  [1]: #createScript
  [HDI.FlightDelays.AvgDelays.Dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
  [Использование Oozie с HDInsight]: ../hdinsight-use-oozie/
  [Использование Sqoop вместе с HDInsight]: ../hdinsight-use-sqoop/
  [Использование Pig с HDInsight]: ../hdinsight-use-pig/
  [Разработка программ MapReduce на Java для HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/
  [Разработка программ потоковой передачи Hadoop на C# для HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
