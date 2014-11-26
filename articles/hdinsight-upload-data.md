<properties linkid="manage-services-hdinsight-upload-data-for-hadoop-jobs-in-hdinsight" urlDisplayName="Upload Data" pageTitle="Upload data for Hadoop jobs in HDInsight | Azure" metaKeywords="" description="Learn how to upload and access data in HDInsight using Azure Storage Explorer, Azure PowerShell, the Hadoop command line, or Sqoop." metaCanonical="" services="storage,hdinsight" documentationCenter="" title="Upload data for Hadoop jobs in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Отправка данных для заданий Hadoop в HDInsight

Azure HDInsight предоставляет полнофункциональную распределенную файловую систему Hadoop (HDFS) через хранилище BLOB-объектов Azure. Это решение создано как расширение HDFS, которое обеспечивает удобную работу, подключая полный набор компонентов экосистемы Hadoop, применяемых непосредственно к обрабатываемым данным. Хранилище BLOB-объектов Azure и HDFS — это разные файловые системы, оптимизированные соответственно для хранения данных и для выполнения расчетов с этими данными. Преимущества использования хранилища BLOB-объектов Azure см.в разделе [Использование хранилища BLOB-объектов Windows Azure с HDInsight][Использование хранилища BLOB-объектов Windows Azure с HDInsight].

Кластеры Azure HDInsight обычно развертываются для выполнения заданий MapReduce и удаляются сразу после завершения этих заданий. Хранение данных в кластерах HDFS после выполнения расчетов было бы дорогостоящим способом хранения данных. Хранилище BLOB-объектов Azure — это доступное, высокомасштабируемое, экономичное и общедоступное решение для хранения данных, которые должны обрабатываться с помощью HDInsight. Хранение данных в BLOB-объектах позволяет освобождать кластеры HDInsight, используемые для расчетов, без потери данных.

Доступ к хранилищу BLOB-объектов Azure может осуществляться через [AzCopy][AzCopy], [Azure PowerShell][Azure PowerShell], [клиентскую библиотеку хранилища Azure для .NET][клиентскую библиотеку хранилища Azure для .NET] или с помощью средств проводника. Некоторые из доступных средств:

-   [Обозреватель хранилищ Azure][Обозреватель хранилищ Azure]
-   [Cloud Storage Studio 2][Cloud Storage Studio 2]
-   [CloudXplorer][CloudXplorer]
-   [Azure Explorer][Azure Explorer]
-   [Azure Explorer PRO][Azure Explorer PRO]

**Предварительные требования**

Перед началом работы с этой статьей необходимо иметь следующее:

-   Кластер Azure HDInsight. Инструкции см. в разделе [Приступая к работе с Azure HDInsight][Приступая к работе с Azure HDInsight] или [Подготовка кластеров HDInsight][Подготовка кластеров HDInsight].

## Содержание

-   [Отправка данных в хранилище BLOB-объектов с использованием AzCopy][Отправка данных в хранилище BLOB-объектов с использованием AzCopy]
-   [Отправка данных в хранилище BLOB-объектов с использованием Azure PowerShell][Отправка данных в хранилище BLOB-объектов с использованием Azure PowerShell]
-   [Отправка данных в хранилище BLOB-объектов с использованием обозревателя хранилищ Azure][Отправка данных в хранилище BLOB-объектов с использованием обозревателя хранилищ Azure]
-   [Отправка данных в хранилище BLOB-объектов с использованием командной строки Hadoop][Отправка данных в хранилище BLOB-объектов с использованием командной строки Hadoop]
-   [Импорт данных из базы данных SQL Azure в хранилище BLOB-объектов с использованием Sqoop][Импорт данных из базы данных SQL Azure в хранилище BLOB-объектов с использованием Sqoop]

## <span id="azcopy"></span></a>Отправка данных в хранилище BLOB-объектов с использованием AzCopy

AzCopy — это служебная программа командной строки, разработанная для упрощения задачи переноса данных в учетную запись хранения Azure и из нее. Ее можно использовать как автономное средство или включить в существующее приложение. [Загрузка AzCopy][Загрузка AzCopy].

Синтаксис AzCopy:

    AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

Дополнительные сведения см. в разделе [AzCopy — отправка и загрузка файлов для BLOB-объектов Azure][AzCopy]

## <span id="powershell"></span></a>Отправка данных в хранилище BLOB-объектов с использованием Azure PowerShell

Azure PowerShell — это полнофункциональная среда сценариев, которую можно использовать для контроля и автоматизации развертывания и управления вашей рабочей нагрузкой в Azure. Azure PowerShell можно использовать для отправки данных в хранилище BLOB-объектов, чтобы данные могли обрабатываться заданиями MapReduce. Сведения о настройке рабочей станции для запуска Windows Azure PowerShell см. в разделе [Установка и настройка Azure PowerShell][Установка и настройка Azure PowerShell].

**Отправка локального файла в хранилище BLOB-объектов**

1.  Откройте окно консоли Azure PowerShell, как указано в разделе [Установка и настройка Azure PowerShell][Установка и настройка Azure PowerShell].
2.  Задайте значения первых пяти переменных в следующем сценарии:

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<StorageAccountName>"
        $containerName = "<ContainerName>"

        $fileName ="<LocalFileName>"
        $blobName = "<BlobName>"

        # Get the storage account key
        Select-AzureSubscription $subscriptionName
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}

        # Create the storage context object
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Copy the file from local workstation to the Blob container        
        Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext

3.  Вставьте скрипт в окно консоли Azure PowerShell, чтобы выполнить его.

В контейнерах хранилища BLOB-объектов данные хранятся в виде пар "ключ-значение" и отсутствует иерархия каталогов. Однако в имени ключа может использоваться символ "/", чтобы оно выглядело так, как будто файл хранится в структуре каталогов. Например, ключ BLOB-объекта может выглядеть следующим образом: *input/log1.txt*. Фактически никакого каталога "input" не существует, но из-за наличия символа "/" имя ключа выглядит как путь к файлу. В предыдущем сценарии вы можете связать с файлом структуру папок, задав переменную $blobname . Например: *$blobname="myfolder/myfile.txt"*.

С помощью средств проводника Azure вы можете заметить некоторые файлы размером 0 байт. Эти файлы служат двум целям.

-   В случае пустых папок они обозначают существование папки. Хранилище BLOB-объектов понимает, что если существует BLOB-объект с названием foo/bar, то существует и папка с именем foo. Но если вы хотите иметь пустую папку с именем foo, то единственный способ обозначить ее — создать в нужном месте специальный файл размером 0 байт.
-   Они содержат некоторые специальные метаданные, необходимые файловой системе Hadoop, в частности разрешения и имена владельцев для папок.

## <span id="storageexplorer"></span></a>Отправка данных в хранилище BLOB-объектов с использованием обозревателя хранилищ Azure

*Обозреватель хранилищ Azure* — это полезное средство для проверки и изменения данных в хранилище Azure. Это бесплатное средство, которое можно загрузить на сайте [][]<http://azurestorageexplorer.codeplex.com/></a>.

Прежде чем использовать средство, необходимо узнать ваше имя учетной записи хранения Azure и ключ учетной записи. Инструкции по получению этой информации см. в "Практическом руководстве: Просмотр, копирование и повторное создание ключей доступа к хранилищу, раздел [Управление учетными записями хранения][Управление учетными записями хранения].

1.  Запустите обозреватель хранилищ Azure.

    ![HDI.AzureStorageExplorer][HDI.AzureStorageExplorer]

2.  Щелкните **Добавить учетную запись**. Добавив один раз учетную запись в обозреватель хранилищ Azure, выполнять этот шаг больше не требуется.

    ![HDI.ASEAddAccount][HDI.ASEAddAccount]

3.  Введите **Имя учетной записи хранения** и **Ключ учетной записи хранения**, затем щелкните **Добавить учетную запись хранения**. Можно добавить несколько учетных записей хранения, и каждая из них будет отображаться на вкладке.
4.  В разделе **Тип хранилища** щелкните **BLOB-объекты**.

    ![HDI.ASEBlob][HDI.ASEBlob]

5.  В разделе **Контейнер** дважды щелкните контейнер, связанный с вашим кластером HDInsight. Контейнер необходимо указать при создании кластера HDInsight. В противном случае процесс создания кластера создает его автоматически.
6.  В разделе **BLOB-объект** щелкните **Отправить**.
7.  Укажите файл для отправки и щелкните **Открыть**.

## <span id="commandline"></span></a> Отправка данных в хранилище BLOB-объектов с использованием командной строки Hadoop

Чтобы использовать командную строку Hadoop, необходимо сначала подключиться к кластеру с помощью удаленного рабочего стола.

1.  Выполните вход на [Портал управления][Портал управления].
2.  Щелкните **HDINSIGHT**. Вы увидите список развернутых кластеров Hadoop.
3.  Щелкните кластер HDInsight, в который хотите отправить данные.
4.  В верхней части страницы щелкните **КОНФИГУРАЦИЯ**.
5.  Если удаленный рабочий стол не включен, щелкните **ВКЛЮЧИТЬ УДАЛЕННЫЙ ДОСТУП** и следуйте инструкциям. В противном случае перейдите к следующему шагу.
6.  В нижней части страницы щелкните **ПОДКЛЮЧИТЬСЯ**.
7.  Щелкните **Открыть**.
8.  Введите свои учетные данные и нажмите кнопку **ОК**.
9.  Щелкните **Да**.
10. На рабочем столе щелкните **Командная строка Hadoop**.
11. В приведенном ниже примере показано, как скопировать файл davinci.txt из локальной файловой системы на головном узле HDInsight в каталог /example/data.

        hadoop dfs -copyFromLocal C:\temp\davinci.txt /example/data/davinci.txt

    Поскольку по умолчанию используется файловая система хранилища BLOB-объектов Azure, файл фактически находится в хранилище BLOB-объектов Azure. Можно также использовать следующую ссылку на файл:

        wasb:///example/data/davinci.txt 

    или

        wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

    При использовании *wasbs* полное доменное имя является обязательным.

12. Чтобы просмотреть список отправленных файлов, используйте следующую команду:

        hadoop dfs -lsr /example/data

## <span id="sqoop"></span></a> Импорт данных в HDFS из базы данных SQL или SQL Server с использованием Sqoop

Sqoop — это средство, предназначенное для передачи данных между Hadoop и реляционными базами данных. С его помощью можно импортировать данные из системы управления реляционными базами данных (РСУБД), например SQL, MySQL или Oracle, в распределенную файловую систему Hadoop (HDFS), преобразовать данные в Hadoop с использованием MapReduce или Hive, а затем экспортировать данные обратно в РСУБД. Дополнительные сведения см. в разделе [Руководство пользователя Sqoop][Руководство пользователя Sqoop].

Прежде чем импортировать данные, необходимо узнать имя сервера базы данных SQL Azure, имя учетной записи базы данных, пароль учетной записи и имя базы данных.

По умолчанию база данных SQL Azure разрешает подключения из служб Azure, таких как Azure HDInsight. Если этот параметр брандмауэра отключен, вы должны включить его на портале управления Azure. Инструкции по созданию базы данных SQL и настройке правил брандмауэра см. в разделе [Создание и настройка базы данных SQL][Создание и настройка базы данных SQL].

В следующей процедуре используется PowerShell для отправки задания Sqoop.

**Импорт данных в HDInsight с помощью Sqoop и PowerShell**

1.  Откройте окно консоли Azure PowerShell, как указано в разделе [Установка и настройка Azure PowerShell][Установка и настройка Azure PowerShell].
2.  Задайте значения первых восьми переменных в следующем сценарии:

        $subscriptionName = "<AzureSubscriptionName>"
        $clusterName = "<HDInsightClusterName>"

        $sqlDatabaseServerName = "<SQLDatabaseServerName>"
        $sqlDatabaseUserName = "<SQLDatabaseDatabaseName>"
        $sqlDatabasePassword = "<SQLDatabasePassword>"
        $sqlDatabaseDatabaseName = "<SQLDatabaseDatabaseName>"
        $tableName = "<SQLDatabaseTableName>"

        $hdfsOutputDir = "<OutputPath>"  # This is the HDFS path for the output file, for example "/lineItemData".

        Select-AzureSubscription $subscriptionName      
        $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "import --connect jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseUserName@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseDatabaseName --table $tableName --target-dir $hdfsOutputDir -m 1" 

        $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
        Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob

        Write-Host "Standard Error" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
        Write-Host "Standard Output" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

3.  Вставьте скрипт в окно консоли Azure PowerShell, чтобы выполнить его. Пример PowerShell для извлечения файла данных из хранилища BLOB-объектов Azure см. в разделе [Приступая к работе с HDInsight][Приступая к работе с Azure HDInsight].

Дополнительные сведения об использовании Sqoop см. в разделе [Использование Sqoop с HDInsight][Использование Sqoop с HDInsight].

## Дальнейшие действия

Теперь, когда вы знаете, как получить данные в HDInsight, узнайте о том, как выполнять анализ, в следующих статьях:

-   [Приступая к работе с Azure HDInsight][Приступая к работе с Azure HDInsight]
-   [Отправка заданий Hadoop программными средствами][Отправка заданий Hadoop программными средствами]
-   [Использование Hive с HDInsight][Использование Hive с HDInsight]
-   [Использование Pig с HDInsight][Использование Pig с HDInsight]

  [Использование хранилища BLOB-объектов Windows Azure с HDInsight]: ../hdinsight-use-blob-storage/
  [AzCopy]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx
  [Azure PowerShell]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj152841.aspx
  [клиентскую библиотеку хранилища Azure для .NET]: /ru-ru/develop/net/how-to-guides/blob-storage/
  [Обозреватель хранилищ Azure]: http://azurestorageexplorer.codeplex.com/
  [Cloud Storage Studio 2]: http://www.cerebrata.com/Products/CloudStorageStudio/
  [CloudXplorer]: http://clumsyleaf.com/products/cloudxplorer
  [Azure Explorer]: http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx
  [Azure Explorer PRO]: http://www.cloudberrylab.com/microsoft-azure-explorer-pro.aspx
  [Приступая к работе с Azure HDInsight]: ../hdinsight-get-started/
  [Подготовка кластеров HDInsight]: ../hdinsight-provision-clusters/
  [Отправка данных в хранилище BLOB-объектов с использованием AzCopy]: #azcopy
  [Отправка данных в хранилище BLOB-объектов с использованием Azure PowerShell]: #powershell
  [Отправка данных в хранилище BLOB-объектов с использованием обозревателя хранилищ Azure]: #storageexplorer
  [Отправка данных в хранилище BLOB-объектов с использованием командной строки Hadoop]: #commandline
  [Импорт данных из базы данных SQL Azure в хранилище BLOB-объектов с использованием Sqoop]: #sqoop
  [Загрузка AzCopy]: http://aka.ms/WaCopy
  [Установка и настройка Azure PowerShell]: ../install-configure-powershell/
  []: http://azurestorageexplorer.codeplex.com/ "Обозреватель хранилищ Azure"
  [Управление учетными записями хранения]: ../storage-manage-storage-account/
  [HDI.AzureStorageExplorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
  [HDI.ASEAddAccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
  [HDI.ASEBlob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png
  [Портал управления]: https://manage.windowsazure.com
  [Руководство пользователя Sqoop]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
  [Создание и настройка базы данных SQL]: ../sql-database-create-configure/
  [Использование Sqoop с HDInsight]: ../hdinsight-use-sqoop/
  [Отправка заданий Hadoop программными средствами]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Использование Hive с HDInsight]: ../hdinsight-use-hive/
  [Использование Pig с HDInsight]: ../hdinsight-use-pig
