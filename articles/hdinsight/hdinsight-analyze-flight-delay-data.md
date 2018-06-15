---
title: Анализ данных по задержке рейсов с помощью Hadoop в HDInsight — Azure | Документы Майкрософт
description: Узнайте, как использовать один сценарий Windows PowerShell для создания кластера HDInsight, выполнения задания Hive, выполнения задания Sqoop и удаления кластера.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 00e26aa9-82fb-4dbe-b87d-ffe8e39a5412
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: eec5d0eb3c9cb0ae6e3e7f4eadfc58c4ab039cfd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33770578"
---
# <a name="analyze-flight-delay-data-by-using-hive-in-hdinsight"></a>Анализ данных о задержке рейсов с помощью Hive в HDInsight
Hive предоставляет средства для выполнения заданий Hadoop MapReduce с помощью языка сценариев, аналогичного SQL, под названием *[HiveQL][hadoop-hiveql]*, который применяется для обобщения данных, создания запросов и анализа больших объемов данных.

> [!IMPORTANT]
> Действия, описанные в этом документе, требуют наличия кластера HDInsight на основе Windows. Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](hdinsight-component-versioning.md#hdinsight-windows-retirement). Инструкции по работе с кластерами под управлением Linux см. в статье [Анализ данных о задержке рейсов с помощью Hive в HDInsight в Linux](hdinsight-analyze-flight-delay-data-linux.md).

Одним из самых существенных преимуществ Azure HDInsight является разделение хранилища данных и вычислительных ресурсов. HDInsight использует для хранения данных хранилище BLOB-объектов Azure. Типичное задание состоит из 3 частей:

1. **Хранение данных в хранилище больших двоичных объектов Azure.**  Например, данные о погодных условиях, данные, получаемые от датчиков, журналы учета сетевой активности и, как в настоящем случае, данные о задержке авиарейсов сохраняются в хранилище больших двоичных объектов Azure.
2. **Выполнение заданий.** Когда наступает время обработки данных, вы запускаете скрипт Windows PowerShell (или клиентское приложение) для создания кластера HDInsight, выполнения заданий и удаления кластера. Данные, используемые заданиями, размещаются в хранилище BLOB-объектов Azure. Выходные данные сохраняются даже после удаления кластера. Такой подход позволяет платить только за использованные ресурсы.
3. **Извлечение выходных данных из хранилища больших двоичных объектов Azure**или, как в данном случае, их экспорт в базу данных SQL Azure.

На следующей схеме наглядно демонстрируется такой сценарий, а также приводится структура этого учебника.

![HDI.FlightDelays.flow][img-hdi-flightdelays-flow]

Обратите внимание, что номера на схеме соответствуют названиям разделов. **M** означает основной процесс. **A** означает содержимое в приложении.

В основной части руководства показано, как использовать один скрипт Windows PowerShell, чтобы выполнить приведенные ниже задачи.

* Создание кластера HDInsight.
* Выполнение задания Hive в кластере для вычисления средних задержек рейсов в аэропортах. Данные о задержке рейсов хранятся в учетной записи хранилища больших двоичных объектов Azure.
* Выполнение задания Sqoop для экспорта выходных данных задания Hive в базу данных SQL Azure.
* Удаление кластера HDInsight.

В приложениях вы можете найти инструкции по отправке данных о задержке рейсов, созданию и отправке строки запроса Hive и подготовке базы данных SQL Azure для задания Sqoop.

> [!NOTE]
> Действия, описанные в этом документе, относятся только к кластерам HDInsight для Windows. Инструкции по работе с кластерами под управлением Linux см. в статье [Анализ данных о задержке рейсов с помощью Hive в HDInsight](hdinsight-analyze-flight-delay-data-linux.md) (для Linux).

### <a name="prerequisites"></a>предварительным требованиям
Перед началом работы с этим руководством необходимо иметь следующее:

* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Рабочая станция с Azure PowerShell**.

    > [!IMPORTANT]
    > Поддержка Azure PowerShell для управления ресурсами HDInsight с помощью диспетчера служб Azure (ASM) объявлена **устаревшей** и будет прекращена с 1 января 2017 г. В описанных в этом документе инструкциях используются новые командлеты HDInsight, которые работают с Azure Resource Manager.
    >
    > Чтобы установить последнюю версию Azure PowerShell, выполните действия из статьи [Установка и настройка Azure PowerShell](/powershell/azureps-cmdlets-docs). Если у вас есть сценарии, в которые нужно добавить новые командлеты, работающие с Azure Resource Manager, см. статью [Переход к средствам разработки на основе Azure Resource Manager для кластеров HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md).

**Файлы, используемые в этом учебнике**

В этом руководстве используются данные о текущей работе рейсов авиакомпании, полученные из [бюро транспортной статистики при администрации по исследованиям и инновационным технологиям][rita-website] (RITA).
Копия этих данных была передана в контейнер хранилища BLOB-объектов Azure с правами доступа «Общедоступный большой двоичный объект».
Часть скрипта PowerShell копирует данные из общедоступного контейнера BLOB-объектов в контейнер BLOB-объектов по умолчанию в вашем кластере. Сценарий HiveQL копируется в тот же самый контейнер больших двоичных объектов.
Сведения о получении или отправке данных в учетную запись хранения и о создании и отправке файла скрипта HiveQL см. в [приложении А](#appendix-a) и [приложении Б](#appendix-b).

В следующей таблице перечислены файлы, используемые в этом учебнике:

<table border="1">
<tr><th>Файлы</th><th>ОПИСАНИЕ</th></tr>
<tr><td>wasb://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql</td><td>Для запуска задания Hive используется скрипт HiveQL. Этот сценарий был загружен в учетную запись хранилища больших двоичных объектов Azure с общими правами доступа. В <a href="#appendix-b">приложении Б</a> содержатся инструкции о том, как подготовить и отправить файл в учетную запись хранения больших двоичных объектов Azure.</td></tr>
<tr><td>wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data</td><td>Входные данные для задания Hive. Эти данные были отправлены в учетную запись хранилища больших двоичных объектов Azure с общим доступом. В <a href="#appendix-a">приложении А</a> содержатся инструкции о том, как получить данные и отправить их в учетную запись хранения больших двоичных объектов Azure.</td></tr>
<tr><td>\tutorials\flightdelays\output</td><td>Выходной путь для задания Hive. Для хранения результирующих данных используется контейнер по умолчанию.</td></tr>
<tr><td>\tutorials\flightdelays\jobstatus</td><td>Папка состояния задания Hive в контейнере по умолчанию.</td></tr>
</table>

## <a name="create-cluster-and-run-hivesqoop-jobs"></a>Создание кластера и выполнение заданий Hive и Sqoop
Hadoop MapReduce представляет из себя пакетную обработку данных. Наиболее эффективным способом выполнения заданий Hive является создание кластера под конкретное задание и удаление этого задания после его выполнения. Приводимый далее скрипт позволяет полностью реализовать этот процесс.
Дополнительные сведения о создании кластера HDInsight и выполнении заданий Hive см. в статьях [Создание кластеров Hadoop в HDInsight][hdinsight-provision] и [Использование Hive с HDInsight][hdinsight-use-hive].

**Выполнение запросов Hive с помощью PowerShell**

1. Создайте базу данных SQL Azure и таблицу для выходных данных задания Sqoop согласно указаниям в [приложении В](#appendix-c).
2. Откройте интегрированную среду сценариев Windows PowerShell и выполните следующий сценарий:

    ```powershell
    $subscriptionID = "<Azure Subscription ID>"
    $nameToken = "<Enter an Alias>"

    ###########################################
    # You must configure the follwing variables
    # for an existing Azure SQL Database
    ###########################################
    $existingSqlDatabaseServerName = "<Azure SQL Database Server>"
    $existingSqlDatabaseLogin = "<Azure SQL Database Server Login>"
    $existingSqlDatabasePassword = "<Azure SQL Database Server login password>"
    $existingSqlDatabaseName = "<Azure SQL Database name>"

    $localFolder = "E:\Tutorials\Downloads\" # A temp location for copying files.
    $azcopyPath = "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy" # depends on the version, the folder can be different

    ###########################################
    # (Optional) configure the following variables
    ###########################################

    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

    $resourceGroupName = $namePrefix + "rg"
    $location = "EAST US 2"

    $HDInsightClusterName = $namePrefix + "hdi"
    $httpUserName = "admin"
    $httpPassword = "<Enter the Password>"

    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $HDInsightClusterName # use the cluster name

    $existingSqlDatabaseTableName = "AvgDelays"
    $sqlDatabaseConnectionString = "jdbc:sqlserver://$existingSqlDatabaseServerName.database.windows.net;user=$existingSqlDatabaseLogin@$existingSqlDatabaseServerName;password=$existingSqlDatabaseLogin;database=$existingSqlDatabaseName"

    $hqlScriptFile = "/tutorials/flightdelays/flightdelays.hql"

    $jobStatusFolder = "/tutorials/flightdelays/jobstatus"

    ###########################################
    # Login
    ###########################################
    try{
        $acct = Get-AzureRmSubscription
    }
    catch{
        Connect-AzureRmAccount
    }
    Select-AzureRmSubscription -SubscriptionID $subscriptionID

    ###########################################
    # Create a new HDInsight cluster
    ###########################################

    # Create ARM group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create the default storage account
    New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName -Location $location -Type Standard_LRS

    # Create the default Blob container
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer -Name $defaultBlobContainerName -Context $defaultStorageAccountContext

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
        -DefaultStorageContainer $existingDefaultBlobContainerName

    ###########################################
    # Prepare the HiveQL script and source data
    ###########################################

    # Create the temp location
    New-Item -Path $localFolder -ItemType Directory -Force

    # Download the sample file from Azure Blob storage
    $context = New-AzureStorageContext -StorageAccountName "hditutorialdata" -Anonymous
    $blobs = Get-AzureStorageBlob -Container "flightdelay" -Context $context
    #$blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

    # Upload data to default container

    $azcopycmd = "cmd.exe /C '$azcopyPath\azcopy.exe' /S /Source:'$localFolder' /Dest:'https://$defaultStorageAccountName.blob.core.windows.net/$defaultBlobContainerName/tutorials/flightdelays' /DestKey:$defaultStorageAccountKey"

    Invoke-Expression -Command:$azcopycmd

    ###########################################
    # Submit the Hive job
    ###########################################
    Use-AzureRmHDInsightCluster -ClusterName $HDInsightClusterName -HttpCredential $httpCredential
    $response = Invoke-AzureRmHDInsightHiveJob `
                    -Files $hqlScriptFile `
                    -DefaultContainer $defaultBlobContainerName `
                    -DefaultStorageAccountName $defaultStorageAccountName `
                    -DefaultStorageAccountKey $defaultStorageAccountKey `
                    -StatusFolder $jobStatusFolder

    write-Host $response

    ###########################################
    # Submit the Sqoop job
    ###########################################
    $exportDir = "wasb://$defaultBlobContainerName@$defaultStorageAccountName.blob.core.windows.net/tutorials/flightdelays/output"

    $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
                    -Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "
    $sqoopJob = Start-AzureRmHDInsightJob `
                    -ResourceGroupName $resourceGroupName `
                    -ClusterName $hdinsightClusterName `
                    -HttpCredential $httpCredential `
                    -JobDefinition $sqoopDef #-Debug -Verbose

    Wait-AzureRmHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $HDInsightClusterName `
        -HttpCredential $httpCredential `
        -WaitTimeoutInSeconds 3600 `
        -Job $sqoopJob.JobId

    Get-AzureRmHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -HttpCredential $httpCredential `
        -DefaultContainer $existingDefaultBlobContainerName `
        -DefaultStorageAccountName $defaultStorageAccountName `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -JobId $sqoopJob.JobId `
        -DisplayOutputType StandardError

    ###########################################
    # Delete the cluster
    ###########################################
    Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName
    ```
3. Подключитесь к базе данных SQL и просмотрите средние значения задержки рейсов по городу в таблице AvgDelays:

    ![HDI.FlightDelays.AvgDelays.Dataset][image-hdi-flightdelays-avgdelays-dataset]

- - -

## <a id="appendix-a"></a>Приложение А. Отправка данных о задержке рейсов в хранилище больших двоичных объектов Azure
Отправление файла данных и файлов скриптов HiveQL (см. [приложение Б](#appendix-b)) нужно спланировать. Суть заключается в том, чтобы сохранить файлы данных и файл HiveQL до создания кластера HDInsight и запуска задания Hive. Существует два варианта.

* **Использование той же учетной записи хранения Azure, которая будет использоваться кластером HDInsight в качестве файловой системы по умолчанию.** Вам не нужно вносить никакие дополнительные изменения, поскольку кластер HDInsight будет иметь ключ доступа к учетной записи хранения.
* **Использование учетной записи хранения Azure, отличной от той, которая используется кластером HDInsight в качестве файловой системы по умолчанию.** В этом случае необходимо изменить относящуюся к созданию часть скрипта Windows PowerShell, которую можно найти в разделе [Создание кластера HDInsight и выполнение заданий Hive и Sqoop](#runjob), чтобы привязать эту учетную запись хранения как дополнительную. Инструкции см. в статье [Создание кластеров Hadoop в HDInsight][hdinsight-provision]. После этого кластеру HDInsight станет известен ключ доступа для этой учетной записи хранения.

> [!NOTE]
> Путь к хранилищу больших двоичных объектов для файла данных жестко закодирован в файле скрипта HiveQL. Его следует обновлять соответствующим образом.

**Загрузка данных о рейсах**

1. Перейдите на страницу [бюро транспортной статистики при администрации по исследованиям и инновационным технологиям][rita-website].
2. На странице выберите следующие значения:

    <table border="1">
    <tr><th>ИМЯ</th><th>Значение</th></tr>
    <tr><td>Фильтр года</td><td>2013 </td></tr>
    <tr><td>Период фильтра</td><td>Январь</td></tr>
    <tr><td>Поля</td><td>*Year*, *FlightDate*, *UniqueCarrier*, *Carrier*, *FlightNum*, *OriginAirportID*, *Origin*, *OriginCityName*, *OriginState*, *DestAirportID*, *Dest*, *DestCityName*, *DestState*, *DepDelayMinutes*, *ArrDelay*, *ArrDelayMinutes*, *CarrierDelay*, *WeatherDelay*, *NASDelay*, *SecurityDelay*, *LateAircraftDelay* (очистите остальные поля)</td></tr>
    </table>

3. Щелкните элемент **Загрузить**.
4. Распакуйте файл в папку **C:\Tutorials\FlightDelay\2013Data**. Каждый файл представляет собой CSV-файл и имеет размер около 60 ГБ.
5. Переименуйте файл в название месяца, данные по которому он содержит. Например, файл, содержащий данные за январь, следует назвать *January.csv*.
6. Повторите шаги 2 и 5, чтобы загрузить файл для каждого из 12 месяцев 2013 года. Для выполнения руководства вам понадобится как минимум один файл.

**Отправка данных о задержке рейсов в хранилище больших двоичных объектов Azure**

1. Задайте следующие параметры.

    <table border="1">
    <tr><th>Имя переменной</th><th>Заметки</th></tr>
    <tr><td>$storageAccountName</td><td>Учетная запись хранения Azure, в которую необходимо отправить данные.</td></tr>
    <tr><td>$blobContainerName</td><td>Контейнер больших двоичных объектов, в который необходимо отправить данные.</td></tr>
    </table>
    
2. Откройте интегрированную среду сценариев Azure PowerShell.
3. Добавьте следующий скрипт в область скриптов:

    ```powershell
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
    $localFolder = "C:\Tutorials\FlightDelay\2013Data"  # The source folder
    $destFolder = "tutorials/flightdelay/2013data"     #The blob name prefix for the files to be uploaded
    #EndRegion

    #Region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Connect-AzureRmAccount}
    #EndRegion

    #Region - Validate user input
    Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
    # Validate the Storage account
    if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
    {
        Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
        exit
    }
    else{
        $resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
    }

    # Validate the container
    $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
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

    # List the uploaded files on HDInsight
    Get-AzureStorageBlob -Container $blobContainerName  -Context $storageContext -Prefix $destFolder
    #EndRegion
    ```
4. Нажмите клавишу **F5** для запуска скрипта.

Если вы решили использовать другой метод для передачи файлов, убедитесь, что путь к файлу имеет значение tutorials/flightdelay/data. Для доступа к файлам используется следующий синтаксис:

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelay/data

Путь tutorials/flightdelay/data является виртуальной папкой, созданной вами при передаче файлов. Убедитесь, что присутствует 12 файлов — по одному для каждого месяца.

> [!NOTE]
> Для возможности считывания данных из нового расположения необходимо обновить запрос Hive.
>
> Вы должны либо настроить разрешения для общего доступа к контейнеру, либо привязать учетную запись хранения к кластеру HDInsight. В противном случае строка запроса Hive не сможет получить доступ к файлам данных.

- - -

## <a id="appendix-b"></a>Приложение Б. Создание и загрузка скрипта HiveQL
С помощью Azure PowerShell можно одновременно запустить несколько инструкций HiveQL по одной за раз или упаковать инструкцию HiveQL в файл скрипта. В этом разделе показывается, как создавать скрипты HiveQL и загружать их в хранилище больших двоичных объектов Azure с помощью Azure PowerShell. Для Hive требуется, чтобы скрипты HiveQL хранились в хранилище больших двоичных объектов Azure.

Скрипт HiveQL выполнит следующее:

1. **Удалит таблицу delays_raw**, если она уже существует.
2. **Создаст внешнюю таблицу Hive delays_raw**, указывающую на расположение хранилища BLOB-объектов с файлами данных о задержках рейсов. Этот запрос задает то, что поля разделяются с помощью "," и что строки завершаются с помощью "\n". Проблема возникает, когда в значении полей есть запятые, потому что Hive не различает запятую как разделитель поля и запятую как часть значения поля (что характерно для значений полей ORIGIN\_CITY\_NAME и DEST\_CITY\_NAME). Чтобы решить эту проблему, запрос создает столбцы TEMP для хранения данных, неправильно разбитых на столбцы.
3. **Удаляет таблицу delays**, если она уже существует.
4. **Создает таблицу delays**. Это удобно для очистки данных перед дальнейшей обработкой. Этот запрос создает новую таблицу *delays* из таблицы delays_raw. Обратите внимание, что столбцы TEMP (как уже упоминалось ранее) не копируются, а функция **substring** используется для удаления кавычек из данных.
5. **Вычисляет среднюю задержку из-за погоды и группирует результаты по названию города.** Он также будет выводить результаты в хранилище больших двоичных объектов. Обратите внимание, что запрос удаляет апострофы из данных и исключает строки, где значение **weather_delay** равно null. Это необходимо, так как Sqoop, используемый в этом руководстве, не обрабатывает эти значения надлежащим образом по умолчанию.

Полный список команд HiveQL см. в разделе [Язык описания данных Hive][hadoop-hiveql]. Каждая команда HiveQL должна завершаться точкой с запятой.

**Создание файла скрипта HiveQL**

1. Задайте следующие параметры.

    <table border="1">
    <tr><th>Имя переменной</th><th>Заметки</th></tr>
    <tr><td>$storageAccountName</td><td>Учетная запись хранения Azure, в которую требуется отправить скрипт HiveQL.</td></tr>
    <tr><td>$blobContainerName</td><td>Контейнер больших двоичных объектов, в который необходимо отправить сценарий HiveQL.</td></tr>
    </table>
    
2. Откройте интегрированную среду сценариев Azure PowerShell.  

3. Скопируйте следующий скрипт и вставьте его в область скриптов:  

    ```powershell
    [CmdletBinding()]
    Param(

        # Azure Blob storage variables
        [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
        [String]$storageAccountName,

        [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
        [String]$blobContainerName
    )

    #region - Define variables
    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    # The HiveQL script file is exported as this file before it's uploaded to Blob storage
    $hqlLocalFileName = "e:\tutorials\flightdelay\flightdelays.hql"

    # The HiveQL script file will be uploaded to Blob storage as this blob name
    $hqlBlobName = "tutorials/flightdelay/flightdelays.hql"

    # These two constants are used by the HiveQL script file
    #$srcDataFolder = "tutorials/flightdelay/data"
    $dstDataFolder = "/tutorials/flightdelay/output"
    #endregion

    #Region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Connect-AzureRmAccount}
    #EndRegion

    #Region - Validate user input
    Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
    # Validate the Storage account
    if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
    {
        Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
        exit
    }
    else{
        $resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
    }

    # Validate the container
    $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

    if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
    {
        Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
        Exit
    }
    #EngRegion

    #region - Validate the file and file path

    # Check if a file with the same file name already exists on the workstation
    Write-Host "`nvalidating the folder structure on the workstation for saving the HQL script file ..."  -ForegroundColor Green
    if (test-path $hqlLocalFileName){

        $isDelete = Read-Host 'The file, ' $hqlLocalFileName ', exists.  Do you want to overwirte it? (Y/N)'

        if ($isDelete.ToLower() -ne "y")
        {
            Exit
        }
    }

    # Create the folder if it doesn't exist
    $folder = split-path $hqlLocalFileName
    if (-not (test-path $folder))
    {
        Write-Host "`nCreating folder, $folder ..." -ForegroundColor Green

        new-item $folder -ItemType directory
    }
    #end region

    #region - Write the Hive script into a local file
    Write-Host "`nWriting the Hive script into a file on your workstation ..." `
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
        "LOCATION 'wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data';"

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

    $hqlInsertLocal = "INSERT OVERWRITE DIRECTORY '$dstDataFolder' " +
        "SELECT regexp_replace(origin_city_name, '''', ''), " +
            "avg(weather_delay) " +
        "FROM delays " +
        "WHERE weather_delay IS NOT NULL " +
        "GROUP BY origin_city_name;"

    $hqlScript = $hqlDropDelaysRaw + $hqlCreateDelaysRaw + $hqlDropDelays + $hqlCreateDelays + $hqlInsertLocal

    $hqlScript | Out-File $hqlLocalFileName -Encoding ascii -Force
    #endregion

    #region - Upload the Hive script to the default Blob container
    Write-Host "`nUploading the Hive script to the default Blob container ..." -ForegroundColor Green

    # Create a storage context object
    $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

    # Upload the file from local workstation to Blob storage
    Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $blobContainerName -Blob $hqlBlobName -Context $destContext
    #endregion

    Write-host "`nEnd of the PowerShell script" -ForegroundColor Green
    ```

    В данном сценарии используются три следующие переменные:

   * **$hqlLocalFileName** — этот скрипт сохраняет файл скрипта HiveQL локально перед его отправкой в хранилище больших двоичных объектов. Это — имя файла. По умолчанию используется значение <u>C:\tutorials\flightdelay\flightdelays.hql</u>.
   * **$hqlBlobName** — это имя большого двоичного объекта файла скрипта HiveQL, используемое в хранилище больших двоичных объектов Azure. По умолчанию используется значение tutorials/flightdelay/flightdelays.hql. Так как этот файл записывается непосредственно в хранилище BLOB-объектов Azure, в начале имени BLOB-объекта отсутствует символ "/". Если требуется получить доступ к этому файлу из хранилища больших двоичных объектов, необходимо добавить символ «/» в начало этого имени файла.
   * **$srcDataFolder** и **$dstDataFolder** - = "tutorials/flightdelay/data" = "tutorials/flightdelay/output"

- - -
## <a id="appendix-c"></a>Приложение В. Подготовка базы данных SQL Azure для выходных данных задания Sqoop
**Подготовка базы данных SQL (объединение со скриптом Sqoop)**

1. Задайте следующие параметры.

    <table border="1">
    <tr><th>Имя переменной</th><th>Заметки</th></tr>
    <tr><td>$sqlDatabaseServerName</td><td>Имя сервера базы данных SQL Azure. Чтобы создать новый сервер, не указывайте ничего.</td></tr>
    <tr><td>$sqlDatabaseUsername</td><td>Имя для входа на сервер базы данных SQL Azure. Если значение переменной $sqlDatabaseServerName представляет собой существующий сервер, для выполнения аутентификации на сервере используются имя пользователя и пароль для входа. В противном случае они используются для создания нового сервера.</td></tr>
    <tr><td>$sqlDatabasePassword</td><td>Пароль для входа на сервер базы данных SQL Azure.</td></tr>
    <tr><td>$sqlDatabaseLocation</td><td>Это значение используется только при создании нового сервера базы данных SQL Azure.</td></tr>
    <tr><td>$sqlDatabaseName</td><td>База данных SQL, которая используется для создания таблицы AvgDelays для задания Sqoop. Если это значение не задано, то будет создана база данных с именем HDISqoop. Имя таблицы для выходных данных задания Sqoop&#160;— AvgDelays. </td></tr>
    </table>
    
2. Откройте интегрированную среду сценариев Azure PowerShell.

3. Скопируйте следующий скрипт и вставьте его в область скриптов:  

    ```powershell
    [CmdletBinding()]
    Param(

        # Azure Resource group variables
        [Parameter(Mandatory=$True,
                HelpMessage="Enter the Azure resource group name. It will be created if it doesn't exist.")]
        [String]$resourceGroupName,

        # SQL database server variables
        [Parameter(Mandatory=$True,
                HelpMessage="Enter the Azure SQL Database Server Name. It will be created if it doesn't exist.")]
        [String]$sqlDatabaseServer,

        [Parameter(Mandatory=$True,
                HelpMessage="Enter the Azure SQL Database admin user.")]
        [String]$sqlDatabaseLogin,

        [Parameter(Mandatory=$True,
                HelpMessage="Enter the Azure SQL Database admin user password.")]
        [String]$sqlDatabasePassword,

        [Parameter(Mandatory=$True,
                HelpMessage="Enter the region to create the Database in.")]
        [String]$sqlDatabaseLocation,   #For example, West US.

        # SQL database variables
        [Parameter(Mandatory=$True,
                HelpMessage="Enter the database name. It will be created if it doesn't exist.")]
        [String]$sqlDatabaseName # specify the database name if you have one created. Otherwise use "" to have the script create one for you.
    )

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    #region - Constants and variables

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
    #endregion

    #Region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Connect-AzureRmAccount}
    #EndRegion

    #region - Create and validate Azure resouce group
    try{
        Get-AzureRmResourceGroup -Name $resourceGroupName
    }
    catch{
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $sqlDatabaseLocation
    }

    #EndRegion

    #region - Create and validate Azure SQL database server
    try{
        Get-AzureRmSqlServer -ServerName $sqlDatabaseServer -ResourceGroupName $resourceGroupName}
    catch{
        Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green

        $sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
        $credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)

        $sqlDatabaseServer = (New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -SqlAdministratorCredentials $credential -Location $sqlDatabaseLocation).ServerName
        Write-Host "`tThe new SQL database server name is $sqlDatabaseServer." -ForegroundColor Cyan

        Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
        $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
        New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-workstation" -StartIpAddress $workstationIPAddress -EndIpAddress $workstationIPAddress

        #To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. Note that this allows Azure traffic from any Azure subscription to access the server.
        New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-Azureservices" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
    }

    #endregion

    #region - Create and validate Azure SQL database

    try {
        Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName
    }
    catch {
        Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
        New-AzureRMSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName -Edition "Standard" -RequestedServiceObjectiveName "S1"
    }

    #endregion

    #region -  Execute an SQL command to create the AvgDelays table

    Write-Host "`nCreating SQL Database table ..."  -ForegroundColor Green
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
    $conn.open()
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.connection = $conn
    $cmd.commandtext = $sqlCreateAvgDelaysTable
    $cmd.executenonquery()

    $conn.close()

    Write-host "`nEnd of the PowerShell script" -ForegroundColor Green
    ```

   > [!NOTE]
   > В этом скрипте, чтобы получить внешний IP-адрес, используется служба передачи репрезентативного состояния (REST): http://bot.whatismyipaddress.com. Этот IP-адрес используется для создания правила брандмауэра для сервера базы данных SQL.

    В данном сценарии используются следующие переменные:

   * **$ipAddressRestService.** Значение по умолчанию — http://bot.whatismyipaddress.com. Это общедоступный IP-адрес службы REST для получения внешнего IP-адреса. При необходимости можно использовать и другие службы. Внешний IP-адрес, полученный с помощью данной службы, используется при создании правила брандмауэра для сервера базы данных SQL Azure, поэтому доступ к этой базе данных можно получить с рабочей станции (с помощью скрипта Windows PowerShell).
   * **$fireWallRuleName** — это имя правила брандмауэра для сервера базы данных SQL Azure. Имя по умолчанию: <u>FlightDelay</u>. При необходимости можно использовать другое имя.
   * **$sqlDatabaseMaxSizeGB** — это значение используется только при создании нового сервера базы данных SQL Azure. Значение по умолчанию — 10 ГБ. Размера 10ГБ достаточно для примеров из данного учебника.
   * **$sqlDatabaseName** — это значение используется только при создании новой базы данных SQL Azure. Значение по умолчанию — HDISqoop. Если вы изменяете это имя, то должны соответствующим образом обновить скрипт Windows PowerShell Sqoop.
4. Нажмите клавишу **F5** для запуска скрипта.
5. Проверка выходных данных скрипта. Позволяет убедиться, что скрипт был выполнен без ошибок.

## <a id="nextsteps"></a>Дальнейшие действия
Теперь вы знаете, как отправить файл в хранилище больших двоичных объектов, как заполнить таблицу Hive, используя данные из хранилища больших двоичных объектов, как выполнять запросы Hive и как использовать Sqoop для экспорта данных из HDFS в базу данных SQL Azure. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Руководство по Hadoop. Начало работы с Hadoop в HDInsight на платформе Linux][hdinsight-get-started]
* [Использование Hive с HDInsight][hdinsight-use-hive]
* [Использование Oozie с HDInsight][hdinsight-use-oozie]
* [Использование Sqoop с Hadoop в HDInsight][hdinsight-use-sqoop]
* [Использование Pig с HDInsight][hdinsight-use-pig]
* [Разработка программ MapReduce на Java для Hadoop в HDInsight на платформе Linux][hdinsight-develop-mapreduce]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-develop-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
[hadoop-shell-commands]: http://hadoop.apache.org/docs/r0.18.3/hdfs_shell.html

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

[image-hdi-flightdelays-avgdelays-dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
[img-hdi-flightdelays-run-hive-job-output]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.RunHiveJob.Output.png
[img-hdi-flightdelays-flow]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.Flow.png
