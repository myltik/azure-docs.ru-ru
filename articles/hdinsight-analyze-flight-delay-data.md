<properties urlDisplayName="Analyze flight delay data with Hadoop in HDInsight" pageTitle="Анализ данных о задержке рейсов с помощью Hadoop в HDInsight | Azure" metaKeywords="" description="Узнайте, как загружать данные в HDInsight, как обрабатывать данные с помощью Hive, а также как экспортировать полученные результаты в базу данных SQL с помощью Sqoop." metaCanonical="" services="hdinsight" documentationCenter="" title="Анализ данных о задержке рейсов с помощью Hadoop в HDInsight " authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Анализ данных о задержке рейсов с помощью Hadoop в HDInsight

Hive предоставляет средства для выполнения заданий Hadoop MapReduce с помощью языка сценариев, аналогичного SQL, под названием *[HiveQL][HiveQL]*, который применяется для обобщения данных, создания запросов и анализа больших объемов данных. В этом учебнике показано, как использовать Hive для расчета средних значений задержки рейсов в аэропортах, а также как использовать Sqoop для экспорта результатов в базу данных SQL, имеющуюся в Azure.

Одним из самых существенных преимуществ HDInsight является разделение хранилища данных и вычислительных ресурсов. HDInsight использует для хранения данных хранилище BLOB-объектов Azure. Стандартный процесс работы HDInsight можно разделить на 3 части:

1.  Хранение данных в хранилище BLOB-объектов Azure. Этот процесс может носить постоянный характер. Например, данные о погодных условиях, данные, получаемые от датчиков, журналы учета сетевой активности и, как в настоящем случае, данные о задержке авиарейсов, размещаются в хранилищах BLOB-объектов.
2.  Выполнение заданий. При возникновении необходимости обработки данных, следует запустить соответствующий скрипт PowerShell (или клиентское приложение), чтобы подготовить кластер HDInsight к работе, выполнить необходимые задания и удалить этот кластер. Данные, используемые заданиями, размещаются в хранилище BLOB-объектов Azure. А итоговые данные сохраняются даже после удаления кластера. Такой подход позволяет платить только за использованные ресурсы.
3.  Полученные результаты следует извлечь из хранилища BLOB-объектов или, как в данном случае, экспортировать их в базу данных SQL в Azure.

На следующем рисунке наглядно демонстрируется такой сценарий, а также приводится структура настоящей статьи:

![HDI.FlightDelays.flow][HDI.FlightDelays.flow]

Примечание. Числовые величины на рисунке соответствуют названиям разделов.

**Предполагаемое время выполнения:**30 минут

## В этом учебнике рассматриваются следующие темы:

-   [Предварительные требования][Предварительные требования]
-   [Выполнение задания Hive на вновь создаваемом или уже существующем кластере HDInsight (M1)][Выполнение задания Hive на вновь создаваемом или уже существующем кластере HDInsight (M1)]
-   [Использование Sqoop для экспорта получаемых результатов в базу данных SQL на Azure (M2)][Использование Sqoop для экспорта получаемых результатов в базу данных SQL на Azure (M2)]
-   [Дальнейшие действия][Дальнейшие действия]
-   [Приложение A. Загрузка данных о задержке рейсов в хранилище больших двоичных объектов Azure (A1)][Приложение A. Загрузка данных о задержке рейсов в хранилище больших двоичных объектов Azure (A1)]
-   [Приложение B. Создание и загрузка скрипта HiveQL (A2)][Приложение B. Создание и загрузка скрипта HiveQL (A2)]

## <span id="prerequisite"></span></a>Предварительные требования

Перед началом работы с этим учебником необходимо иметь следующее:

-   Рабочая станция, на которой установлена и настроена среда Azure PowerShell. Инструкции см. в разделе [Установка и настройка Azure PowerShell][Установка и настройка Azure PowerShell].
-   Подписка Azure. \*\*\*

**Общие сведения о хранилище HDInsight**

Кластеры Hadoop в HDInsight используют для хранения данных хранилища BLOB-объектов Azure. Оно называется *WASB* или *Хранилище Azure — BLOB-объекты*. WASB — это реализация *HDFS* на базе хранилища BLOB-объектов Azure от корпорации Майкрософт. Дополнительные сведения см. в разделе [Использование хранилища BLOB-объектов Azure с HDInsight][Использование хранилища BLOB-объектов Azure с HDInsight].

При подготовке кластера HDInsight контейнер хранилища BLOB-объектов, принадлежащий учетной записи хранения Azure, используется в качестве файловой системы по умолчанию, так же как и HDFS. Такая учетная запись хранения используется в качестве *учетной записи хранения по умолчанию*, а контейнер BLOB-объектов — как *контейнер BLOB-объектов по умолчанию* или просто *контейнер по умолчанию*. Учетная запись хранения, используемая по умолчанию, должна находиться в том же центре обработки данных, что и кластер HDInsight. При удалении кластера HDInsight удаления используемого по умолчанию контейнера или используемой по умолчанию учетной записи хранения не происходит.

В дополнение к учетной записи хранения по умолчанию другие учетные записи хранения Azure могут быть привязаны к кластеру HDInsight во время процесса его подготовки. Такая привязка выражается в добавлении самой учетной записи хранения или ключа учетной записи хранения в файл конфигурации. Таким образом, кластер получает во время работы доступ к этим учетным записям хранения. Инструкции по добавлению дополнительных учетных записей хранения см. в разделе [Подготовка кластеров Hadoop в HDInsight][Подготовка кластеров Hadoop в HDInsight].

Синтаксис WASB:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] Путь WASB является виртуальным. Дополнительные сведения см. в разделе [Использование хранилища BLOB-объектов Azure с HDInsight][Использование хранилища BLOB-объектов Azure с HDInsight].

Для файла, хранящегося в контейнере по умолчанию, доступ из HDInsight может осуществляться с помощью любого из следующих URI (flightdelays.hql используется в качестве примера):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/flightdelays/flightdelays.hql
    wasb:///tutorials/flightdelays/flightdelays.hql
    /tutorials/flightdelays/flightdelays.hql

Если вы пытаетесь получить доступ к этому файлу непосредственно из учетной записи хранения, имя BLOB-объекта для файла будет следующим:

    tutorials/flightdelays/flightdelays.hql

Обратите внимание, что в начале имени BLOB-объекта символ "/" отсутствует.

**Файлы, используемые в настоящем учебнике**

В следующей таблице перечислены файлы, используемые в этом учебнике:

| Файлы                                                                     | Описание                                                                                                     |
|---------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------|
| wasb://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql | Скрипт HiveQL, который требуется для запуска соответствующего задания Hive.                                  |
| wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data         | Исходные данные для задания Hive.                                                                            |
| \\tutorials\\flightdelays\\output                                         | Путь вывода данных для задания Hive. Для хранения результирующих данных используется контейнер по умолчанию. |
| \\tutorials\\flightdelays\\jobstatus                                      | Папка состояния для задания Hive                                                                             |

В этом учебнике используются данные о текущей работе рейсов авиакомпании, полученные из [бюро транспортной статистики при администрации по исследованиям и инновационным технологиям][бюро транспортной статистики при администрации по исследованиям и инновационным технологиям] (RITA). Эти данные были загружены в контейнер службы хранения BLOB-объектов Azure с правами доступа "Общедоступный BLOB". Так как данный контейнер BLOB-объектов является общедоступным, вам не требуется привязывать эту учетную запись хранения к кластеру HDInsight. Скрипт HiveQL загружается в тот же самый контейнер BLOB-объектов. С информацией о том, как извлекать данные из собственной учетной записи хранения и размещать их в этой учетной записи, а также о том, как создавать и загружать скрипты HiveQL, можно ознакомиться в [приложении][приложении].

**Общие сведения о внутренней и внешней таблицах Hive**

Есть несколько вещей, которые нужно знать о внутренней и внешней таблицах Hive.

-   Команда CREATE TABLE создает внутреннюю таблицу. Файл данных должен находиться в контейнере по умолчанию.
-   Команда CREATE TABLE перемещает файл данных в папку /hive/warehouse/<tablename>.
-   Команда CREATE EXTERNAL TABLE создает внешнюю таблицу. Файл данных может находиться за пределами контейнера по умолчанию.
-   Команда CREATE EXTERNAL TABLE не перемещает файл данных.
-   Команда CREATE EXTERNAL TABLE не разрешает наличие любых папок в LOCATION. Именно по этой причине в учебнике создается копия файла sample.log.

Дополнительные сведения см. в разделе [HDInsight: введение по внутренним и внешним таблицам Hive][HDInsight: введение по внутренним и внешним таблицам Hive].

> [WACOM.NOTE] Одна из инструкций HiveQL создает внешнюю таблицу Hive. Внешняя таблица Hive хранит файл данных в исходном расположении. Внутренняя таблица Hive перемещает файл данных в hive\\warehouse. Для внутренней таблицы Hive требуется, чтобы файл данных находился в контейнере по умолчанию. Для данных, которые хранятся не в контейнере BLOB-объектов по умолчанию, используется внешняя таблица Hive.

## <span id="runjob"></span></a>Запуск задания Hive на новом или существующем кластере HDInsight

Технология Hadoop представляет из себя пакетную обработку данных. Наиболее эффективным способом выполнения заданий Hive является подготовка кластера к конкретному заданию и удаление задания после его выполнения. Приводимый далее скрипт позволяет полностью реализовать этот процесс. Более подробная информация о подготовке кластера HDInsight и выполнении заданий Hive приводится в разделах [Подготовка кластеров Hadoop в HDInsight][Подготовка кластеров Hadoop в HDInsight] и [Использование Hive вместе с HDInsight][Использование Hive вместе с HDInsight].

**Выполнение запросов Hive с помощью PowerShell**

1.  Откройте интегрированную среду сценариев PowerShell.
2.  Скопируйте следующий скрипт и вставьте его в область скриптов:

        *** add script here

3.  Нажмите клавишу **F5** для запуска скрипта. Результат должен быть аналогичен приведенному ниже:

    ![HDI.FlightDelays.RunHiveJob.output][HDI.FlightDelays.RunHiveJob.output]

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
-   [Использование Hive с HDInsight][Использование Hive вместе с HDInsight]
-   [Использование Oozie с HDInsight][Использование Oozie с HDInsight]
-   [Использование Sqoop вместе с HDInsight][Использование Sqoop вместе с HDInsight]
-   [Использование Pig с HDInsight][Использование Pig с HDInsight]
-   [Разработка программ MapReduce на Java для HDInsight][Разработка программ MapReduce на Java для HDInsight]
-   [Разработка программ потоковой передачи Hadoop на C\# для HDInsight][Разработка программ потоковой передачи Hadoop на C\# для HDInsight]

## <span id="appendix-a"></span></a>Приложение A. Загрузка данных о задержке рейсов в хранилище больших двоичных объектов Azure

**Загрузка данных о рейсах**

1.  Перейдите к [Бюро транспортной статистики при администрации по исследованиям и инновационным технологиям][бюро транспортной статистики при администрации по исследованиям и инновационным технологиям] (RITA).
2.  На странице выберите следующие значения:

    | Имя            | Значение                                                                                                                                                                                                                                                                                                                                                                                      |
    |----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Фильтр года    | 2013                                                                                                                                                                                                                                                                                                                                                                                          |
    | Период фильтра | Январь                                                                                                                                                                                                                                                                                                                                                                                        |
    | Поля:          | \*Year\*, \*FlightDate\*, \*UniqueCarrier\*, \*Carrier\*, \*FlightNum\*, \*OriginAirportID\*, \*Origin\*, \*OriginCityName\*, \*OriginState\*, \*DestAirportID\*, \*Dest\*, \*DestCityName\*, \*DestState\*, \*DepDelayMinutes\*, \*ArrDelay\*, \*ArrDelayMinutes\*, \*CarrierDelay\*, \*WeatherDelay\*, \*NASDelay\*, \*SecurityDelay\*, \*LateAircraftDelay\* (очистите все остальные поля) |

3.  Щелкните элемент **Загрузить**.
4.  Распакуйте файл в папку **C:\\Tutorials\\FlightDelays\\Data**. Каждый файл представляет собой CSV-файл и имеет размер около 60 ГБ.
5.  Переименуйте файл в название месяца, данные по которому он содержит. Например, файл, содержащий данные за январь, следует назвать *January.csv*.
6.  Повторите шаг 2 и 5, чтобы загрузить файл для каждого из 12 месяцев в 2013 году. Для запуска учебника вам потребуется хотя бы один файл.

**Отправка данных о задержке рейсов в хранилище BLOB-объектов Azure**

1.  Откройте интегрированную среду сценариев PowerShell.
2.  Добавьте следующий скрипт в область скриптов:

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
        $localFolder = "C:\Tutorials\FlightDelays\2014Data"  # the source folder
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

3.  Нажмите клавишу **F5** для запуска скрипта.

## <span id="appendix-b"></span></a>Приложение B. Создание и загрузка скрипта HiveQL

С помощью Azure PowerShell можно одновременно запустить несколько инструкций HiveQL по одной за раз или упаковать инструкцию HiveQL в файл скрипта. В данном разделе рассказывается, как создавать скрипты HiveQL и загружать их в хранилище BLOB-объектов Azure с помощью PowerShell. Для Hive требуется, чтобы скрипты HiveQL хранились в WASB.

Скрипт HiveQL выполнит следующее:

1.  **Удаляет таблицу delays\_raw**, если она уже существует.
2.  **Создает внешнюю таблицу Hive delays\_raw**, указывающую на расположение WASB с файлами данных о задержках рейсов. Этот запрос задает то, что поля разделяются с помощью "," и что строки завершаются с помощью "\\n". Это создает проблему при *наличии* запятых в значениях полей, так как Hive не отличает запятую, которая является разделителем поля, и запятую, которая является частью значения поля (что характерно для значений полей ORIGIN\_CITY\_NAME и DEST\_CITY\_NAME). Чтобы решить эту проблему, запрос создает столбцы TEMP для хранения данных, неправильно разбитых на столбцы.
3.  **Удаляет таблицу delays**, если она уже существует.
4.  **Создает таблицу delays**. Это удобно для очистки данных перед дальнейшей обработкой. Этот запрос создает новую таблицу *delays* из таблицы *delays\_raw*. Обратите внимание, что столбцы TEMP (как уже упоминалось ранее) не копируются, а функция *substring* используется для удаления кавычек из данных.
5.  **Вычисляет среднюю задержку из-за погоды и группирует результаты по названию города.** Он также выводит результаты в WASB. Обратите внимание, что запрос удаляет апострофы из данных и исключает строки, где *weather\_deal*y имеет значение *null*, что необходимо, так как Sqoop, используемый далее в этом руководстве, по умолчанию обрабатывает эти значения некорректно.

Полный список команд HiveQL см. в разделе [Язык описания данных Hive][HiveQL]. Каждая команда HiveQL должна завершаться точкой с запятой.

**Создание файла скрипта HiveQL**

1.  Откройте интегрированную среду сценариев Azure PowerShell.
2.  Скопируйте следующий скрипт и вставьте его в область скриптов:

        [CmdletBinding()]
        Param(

            # Azure blob storage variables
            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the HDInsight cluster name.")]
            [String]$HDInsightClusterName
        )

        #--------------------------------------------------------------------
        # Constants used in the script
        #--------------------------------------------------------------------
        # the HQL script file is exported as this file before uploaded to WASB
        $hqlLocalFileName = "C:\tutorials\flightdelays\flightdelays.hql" 
        # the HQL script file will be upload to WASB as this blob name
        $hqlBlobName = "tutorials/flightdelays/flightdelays.hql" 

        # this two constants are used by the HQL scrpit file
        $srcDataFolder = "tutorials/flightdelays/data" 
        $dstDataFolder = "tutorials/flightdelays/output"

        #--------------------------------------------------------------------
        # Validate the file and file path
        #--------------------------------------------------------------------

        # check if a file with the same file name already exist on the workstation
        Write-Host "`nvalidating the folder structure on the workstation for saving the HQL script file ..."  -ForegroundColor Green
        if (test-path $hqlLocalFileName){

            $yes = New-Object System.Management.Automation.Host.ChoiceDescription "&Yes", `
                "Overwrite the existing file."

            $no = New-Object System.Management.Automation.Host.ChoiceDescription "&No", `
                "Not overwrite the existing file."

            $options = [System.Management.Automation.Host.ChoiceDescription[]]($yes, $no)

            $result = $Host.UI.PromptForChoice("Write file", 
                                                "Overwrite the existing script file, 
                                                $hqlLocalFileName?", 
                                                $options, 
                                                0)

            switch ($result)
            {
            0 {
                # create the folder if it doesn't exist
                $folder = split-path $hqlLocalFileName
                if (-not (test-path $folder))
                {
                    Write-Host "`nCreating folder, $folder ..." -ForegroundColor Green

                    new-item $folder -ItemType directory  
                }
            }
            1 { throw "User cancelled the operation."}
            }
        }

        #--------------------------------------------------------------------
        # Add the Azure account 
        #--------------------------------------------------------------------
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        Add-AzureAccount


        #--------------------------------------------------------------------
        # Retrieve the default storage account and the default container name
        #--------------------------------------------------------------------
        Write-Host "`nRetrieving the HDInsight cluster default storage account information ..." `
                    -ForegroundColor Green

        $hdi = Get-AzureHDInsightCluster -Name $HDInsightClusterName

        if (-not $hdi){
            throw "The HDInsight cluster, $HDInsightClusterName, doesn't exist!" 
        }
        else{
            $storageAccountName = $hdi.DefaultStorageAccount.StorageAccountName `
                                    -replace ".blob.core.windows.net"
            $containerName = $hdi.DefaultStorageAccount.StorageContainerName

            Write-Host "`tThe default storage account for the cluster is $storageAccountName." `
                        -ForegroundColor Cyan
            Write-host "`tThe default Blob container for the cluster is $containerName." `
                        -ForegroundColor Cyan
        }

        #--------------------------------------------------------------------
        # Write the Hive script into a local file
        #--------------------------------------------------------------------
        Write-Host "Writing the Hive script into a file on your workstation ..." `
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

        $hqlScript | Out-File $hqlLocalFileName -Encoding ascii -Force 

        #--------------------------------------------------------------------
        # Upload the Hive script to the default Blob container
        #--------------------------------------------------------------------
        Write-Host "Uploading the Hive script to the default Blob container ..." -ForegroundColor Green

        # Create a storage context object
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Upload the file from local workstation to WASB
        Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $containerName -Blob $hqlBlobName -Context $destContext 

        Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

    В данном скрипте используются три следующие константы:

    -   **$hqlLocalFileName**. Этот скрипт сохраняет скрипт HiveQL локально перед его загрузкой на WASB. Это — имя файла. По умолчанию используется значение <u>C:\\tutorials\\flightdelays\\flightdelays.hql</u>.
    -   **$hqlBlobName**. Это название BLOB-объекта для файла скрипта HiveQL, используемое в хранилище BLOB-объектов Azure.. По умолчанию используется значение <u>tutorials/flightdelays/flightdelays.hql</u>. Так как этот файл записывается непосредственно в хранилище BLOB-объектов Azure, в начале имени BLOB-объекта отсутствует символ "/". Если требуется получить доступ к этому файлу из WASB, необходимо добавить символ "/" в начало имени этого файла.
    -   **$srcDataFolder** и **$dstDataFolder**. = "tutorials/flightdelays/data"
         = "tutorials/flightdelays/output"

3.  Выполните следующую команду для подключения к подписке Azure:

        Add-AzureAccount

4.  Задайте первые две переменные, а затем выполните команды.

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
5.  Выполните следующие команды, чтобы определить инструкции HiveQL:

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

6.  Выполните следующие команды, чтобы записать файл скрипта на рабочей станции и отправить его в WASB:

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

## Подготовка базы данных SQL

**Для выполнения подготовки базы данных SQL (объедините со скриптом Sqoop)**

1.  Откройте интегрированную среду сценариев PowerShell.
2.  Скопируйте следующий скрипт и вставьте его в область скриптов:

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
            [String]$sqlDatabaseLocation,   #For example, West US.

            # SQL database variables
            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the database name if you have created one. Enter nothing to create one.")]
            [AllowEmptyString()]
            [String]$sqlDatabaseName # specify the database name if you have one created.  Otherwise use "" to have the script create one for you.
        )

        #--------------------------------------------------------------------
        # Constants used in the script
        #--------------------------------------------------------------------
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

        #--------------------------------------------------------------------
        # Add the Azure account 
        #--------------------------------------------------------------------
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        Add-AzureAccount

        #--------------------------------------------------------------------
        # Create and validate Azure SQL Database server
        #--------------------------------------------------------------------
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

        #--------------------------------------------------------------------
        # Create and validate Azure SQL database
        #--------------------------------------------------------------------   
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

        #--------------------------------------------------------------------
        # Excute a SQL command to create the AvgDelays table
        #--------------------------------------------------------------------   
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

    > WACOM.NOTE Этот скрипт использует службу REST (http://bot.whatismyipaddress.com) для получения вашего внешнего IP-адреса. Этот IP-адрес используется для создания правила брандмауэра, необходимого для работы сервера базы данных SQL.

    В данном скрипте используются следующие константы:

    -   **$ipAddressRestService**. Значение по умолчанию — <u>http://bot.whatismyipaddress.com</u>. Этот общедоступный IP-адрес службы REST предназначен для получения внешнего IP-адреса. При необходимости можно использовать и другие службы. Внешний IP-адрес, полученный посредством данной службы, используется при создании правила брандмауэра для сервера базы данных SQL в Azure, поэтому доступ к этой базе данных можно получить с рабочей станции (с помощью скрипта PowerShell).
    -   **$fireWallRuleName**. Это название правила брандмауэра предназначено для обеспечения работы базы данных SQL в Azure. Имя по умолчанию: <u>FlightDelay</u>. При необходимости можно использовать другое имя.
    -   **$sqlDatabaseMaxSizeGB**. Этот параметр используется только при создании нового сервера базы данных SQL в Azure. По умолчанию значение равно <u>10ГБ</u>. Размера 10ГБ достаточно для примеров из данного учебника.
    -   **$sqlDatabaseName**. Этот параметр используется только при создании новой базы данных SQL в Azure. По умолчанию значение равно <u>HDISqoop</u>. Если вы присваиваете данному параметру другое имя, необходимо обновить скрипт PowerShell в Sqoop.

3.  Нажмите клавишу **F5** для запуска скрипта. Необходимо указать в подписке Azure собственные учетные данные и следующие параметры:

    -   **sqlDatabaseServer**. Используется для указания имени сервера базы данных SQL в Azure, куда вы намерены экспортировать итоговые данные из Hive. Чтобы создать новый сервер, не указывайте ничего.
    -   **sqlDatabaseUsername**. Используется для указания имени пользователя базы данных. Этот параметр необходимо задавать как при создании нового сервера базы данных, так и при использовании уже существующего.
    -   **sqlDatabasePassword**. Используется для указания входного пароля пользователя базы данных. Этот параметр необходимо задавать как при создании нового сервера базы данных, так и при использовании уже существующего.
    -   **sqlDatabaseLocation**. Этот параметр предназначен для указания региона. Он используется только при создании нового сервера базы данных.
    -   **$sqlDatabaseName**. Используется для указания имени базы данных SQL в Azure. Чтобы создать новую базу данных, не указывайте ничего. Имя базы данных по умолчанию: **HDISqoop**.

4.  Проверка выходных данных скрипта. Позволяет убедиться, что скрипт был выполнен без ошибок.

Если вы решили использовать другой метод для отправки файлов, убедитесь, что путь к файлу имеет значение *tutorials/flightdelays/data*. Для доступа к файлам используется следующий синтаксис:

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelays/data

*tutorials/flightdelays/data* является виртуальной папкой, созданной вами при отправке файлов. Убедитесь, что присутствует 12 файлов — по одному для каждого месяца.

  [HiveQL]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
  [HDI.FlightDelays.flow]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.Flow.png
  [Предварительные требования]: #prerequisite
  [Выполнение задания Hive на вновь создаваемом или уже существующем кластере HDInsight (M1)]: #runjob
  [Использование Sqoop для экспорта получаемых результатов в базу данных SQL на Azure (M2)]: #exportdata
  [Дальнейшие действия]: #nextsteps
  [Приложение A. Загрузка данных о задержке рейсов в хранилище больших двоичных объектов Azure (A1)]: #appdendix-a
  [Приложение B. Создание и загрузка скрипта HiveQL (A2)]: #appendix-b
  [Установка и настройка Azure PowerShell]: ../install-configure-powershell/
  [Использование хранилища BLOB-объектов Azure с HDInsight]: ../hdinsight-use-blob-storage/
  [Подготовка кластеров Hadoop в HDInsight]: ../hdinsight-provision-clusters/
  [бюро транспортной статистики при администрации по исследованиям и инновационным технологиям]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
  [приложении]: #appendix
  [HDInsight: введение по внутренним и внешним таблицам Hive]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
  [Использование Hive вместе с HDInsight]: ../hdinsight-use-hive/
  [HDI.FlightDelays.RunHiveJob.output]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.RunHiveJob.Output.png
  [HDI.FlightDelays.AvgDelays.Dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
  [Приступая к работе с HDInsight]: ../hdinsight-get-started/
  [Использование Oozie с HDInsight]: ../hdinsight-use-oozie/
  [Использование Sqoop вместе с HDInsight]: ../hdinsight-use-sqoop/
  [Использование Pig с HDInsight]: ../hdinsight-use-pig/
  [Разработка программ MapReduce на Java для HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/
  [Разработка программ потоковой передачи Hadoop на C\# для HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
