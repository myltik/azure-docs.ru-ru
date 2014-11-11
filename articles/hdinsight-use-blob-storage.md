<properties linkid="manage-services-hdinsight-howto-blob-store" urlDisplayName="Blob Storage with  Hadoop in HDInsight" pageTitle="Use Blob storage with Hadoop in HDInsight | Azure" metaKeywords="" description="Learn how HDInsight uses Blob storage as the underlying data store for HDFS and how you can query data from the store." metaCanonical="" services="storage,hdinsight" documentationCenter="" title="Use Azure Blob storage with Hadoop in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# Использование хранилища BLOB-объектов Azure с HDInsight

Azure HDInsight поддерживает хранение данных и в распределенной файловой системе Hadoop (HDFS), и в хранилище BLOB-объектов Azure. Хранилище BLOB-объектов является надежным решением хранилища Azure общего назначения. Хранилище BLOB-объектов предоставляет полноценный интерфейс файловой системы HDFS, обеспечивающий удобство работы, предоставляя полный набор компонентов экосистемы Hadoop, который можно (по умолчанию) применять непосредственно к данным. Хранилище BLOB-объектов не только является экономичным решением — хранение данных в BLOB-объектах позволяет удалять кластеры HDInsight, используемые для расчетов, без потери пользовательских данных.

> [WACOM.NOTE] Синтаксис *asv://* не поддерживается в кластерах HDInsight версии 3.0 и не будет поддерживаться в более поздних версиях. Это означает, что все задания, отправляемые в кластер HDInsight версии 3.0, в которых явно используется синтаксис "asv://", завершатся неудачей. Вместо этого нужно использовать синтаксис *wasb://*. Задания, отправленные в кластеры HDInsight версии 3.0 и созданные с использованием существующего метахранилища, в которых содержатся явные ссылки на ресурсы с использованием синтаксиса asv://, также завершатся неудачей. Эти метахранилища потребуется создать повторно с использованием синтаксиса wasb://, чтобы адресовать ресурсы.

> [WACOM.NOTE] В настоящее время HDInsight поддерживает только BLOB-объекты блочного типа.

> [WACOM.NOTE]
> Большинство команд HDFS, таких как **ls**, **copyFromLocal**, **mkdir** и т. д., по-прежнему работают должным образом. В хранилище BLOB-объектов Azure будет отличаться поведение только тех команд, которые относятся к базовой реализации HDFS (под названием DFS), например, **fschk** и **dfsadmin**.

Сведения о подготовке кластера HDInsight см. в разделе [Приступая к работе с HDInsight][Приступая к работе с HDInsight] или [Подготовка кластеров HDInsight][Подготовка кластеров HDInsight].

## Содержание

-   [Архитектура хранилища HDInsight][Архитектура хранилища HDInsight]
-   [Преимущества хранилища BLOB-объектов Azure][Преимущества хранилища BLOB-объектов Azure]
-   [Подготовка контейнера для хранения BLOB-объектов][Подготовка контейнера для хранения BLOB-объектов]
-   [Обращение к файлам в хранилище BLOB-объектов][Обращение к файлам в хранилище BLOB-объектов]
-   [Доступ к BLOB-объекту с помощью PowerShell][Доступ к BLOB-объекту с помощью PowerShell]
-   [Дальнейшие действия][Дальнейшие действия]

## <span id="architecture"></span></a>Архитектура хранилища HDInsight

Следующая схема является абстрактным представлением архитектуры хранилища HDInsight:

![HDI.ASVArch][HDI.ASVArch]

HDInsight предоставляет доступ к распределенной файловой системе, которая локально присоединена к вычислительным узлам. Доступ к этой файловой системе может осуществляться с использованием полного URI. Например:

    hdfs://<namenodehost>/<path>

Кроме того, HDInsight предоставляет возможность доступа к данным, содержащимся в хранилище BLOB-объектов. Синтаксис для доступа к хранилищу BLOB-объектов:

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

В Hadoop поддерживается концепция файловой системы по умолчанию. Файловая система по умолчанию подразумевает стандартную схему и полномочный орган. Она также может использоваться для разрешения относительных путей. В процессе подготовки HDInsight в качестве файловой системы по умолчанию устанавливаются учетная запись хранения Azure и конкретный контейнер хранилища BLOB-объектов из этой учетной записи.

Помимо этой учетной записи хранения в процессе подготовки можно добавить дополнительные учетные записи хранения из той же подписки Azure или других подписок Azure. Инструкции по добавлению дополнительных учетных записей хранения см. в разделе [Подготовка кластеров HDInsight][Подготовка кластеров HDInsight].

-   **Контейнеры в учетных записях хранения, подключенные к кластеру:** поскольку имя учетной записи и ключ хранятся в *core-site.xml*, вы получаете полный доступ к BLOB-объектам в этих контейнерах.
-   **Общедоступные контейнеры или общедоступные BLOB-объекты в учетных записях хранения, не подключенные к кластеру:** вы получаете разрешение только на чтение BLOB-объектов в контейнерах.

    > [WACOM.NOTE]
    >  \> Общедоступный контейнер позволяет получить список всех доступных в этом контейнере BLOB-объектов, а также метаданные контейнера. Общедоступный BLOB-объект позволяет получить доступ к BLOB-объектам только при условии, что вам известен точный URL-адрес. Дополнительные сведения см. в разделе [Ограничение доступа к контейнерам и BLOB-объектам][Ограничение доступа к контейнерам и BLOB-объектам].

-   **Закрытые контейнеры в учетных записях хранения, не подключенные к кластеру:** вы не можете получить доступ к BLOB-объектам в контейнерах, если только не будет задана учетная запись хранения при отправке заданий WebHCat. Эта процедура рассматривается далее в статье.

Учетный записи хранения, определенные в процессе подготовки, и их ключи хранятся в %HADOOP\_HOME%/conf/core-site.xml. По умолчанию HDInsight использует учетные записи хранения, определенные в файле core-site.xml file. Не рекомендуется изменять файл core-site.xml, поскольку образ головного (ведущего) узла кластера может быть в любое время установлен повторно или перенесен, и все изменения в этих файлах будут утеряны.

Несколько заданий WebHCat, в том числе Hive, MapReduce, потоковая передача Hadoop и Pig, могут переносить описание учетных записей хранения и метаданные вместе с ними (в настоящее время эта функция работает для Pig с учетными записями хранения, но не с метаданными.) В разделе [Доступ к BLOB-объекту с помощью Power][Доступ к BLOB-объекту с помощью PowerShell] этой статьи приводится пример данной функции. Для получения дополнительной информации см. раздел [Использование кластера HDInsight с дополнительными учетными записями хранения и метахранилищами][Использование кластера HDInsight с дополнительными учетными записями хранения и метахранилищами].

В контейнерах хранилища BLOB-объектов данные хранятся в виде пар "ключ-значение" и отсутствует иерархия каталогов. Однако в имени ключа может использоваться символ "/", чтобы оно выглядело так, как будто файл хранится в структуре каталогов. Например, ключ BLOB-объекта может выглядеть следующим образом: *input/log1.txt*. Фактически никакого каталога *input* не существует, но из-за наличия символа "/" имя ключа выглядит как путь к файлу.

## <span id="benefits"></span></a>Преимущества хранилища BLOB-объектов Azure

Предполагаемые рабочие затраты из-за отсутствия совмещенных вычислительных ресурсов и ресурсов хранения снижаются за счет того, что вычислительные кластеры подготавливаются близко к ресурсам учетных записей хранения в центре обработки данных Windows Azure, где высокоскоростная сеть обеспечивает высокую эффективность доступа вычислительных узлов к данным в хранилище BLOB-объектов.

Ниже перечислены некоторые преимущества, связанные с хранением данных в хранилище BLOB-объектов (вместо HDFS).

-   **Повторное использование данных и общий доступ к данным:** Данные в файловой системе HDFS находятся внутри вычислительного кластера. Только приложения, имеющие доступ к вычислительному кластеру, могут использовать данные через API HDFS. Доступ к данным в хранилище BLOB-объектов может осуществляться через интерфейсы API HDFS или через [интерфейсы API REST хранилища BLOB-объектов][интерфейсы API REST хранилища BLOB-объектов]. Таким образом, для создания и использования данных можно применять больший набор приложений (включая другие кластеры HDInsight) и средств.
-   **Архивирование данных:** Хранение данных в BLOB-объектах позволяет удалять кластеры HDInsight, использовавшиеся для вычислений, без потери пользовательских данных.
-   **Затраты на хранение данных:** Хранение данных в файловой системе DFS в долгосрочной перспективе более затратное, чем хранение данных в BLOB-объектах, поскольку стоимость вычислительного кластера превышает стоимость контейнера хранилища BLOB-объектов. Кроме того, поскольку данные не требуется повторно загружать при создании каждого вычислительного кластера, вы экономите также на загрузке данных.
-   **Гибкое масштабирование:** Хотя HDFS и предоставляет масштабируемую файловую систему, масштаб определяется количеством узлов, подготавливаемых для кластера. Изменение масштаба может оказаться более сложным процессом, чем использование гибких возможностей масштабирования хранилища BLOB-объектов, которые вы получаете автоматически.
-   **Георепликация:** Возможна георепликация контейнеров хранилища BLOB-объектов через портал Azure. Это обеспечивает возможность географического восстановления и избыточность данных, однако переход в расположение геореплицированных данных при отработке отказа заметно скажется на производительности и может потребовать дополнительных затрат. Поэтому мы рекомендуем взвешенно подходить к выбору георепликации и выбирать ее только в том случае, если ценность данных окупит дополнительные затраты.

Определенные задания и пакеты MapReduce могут создавать промежуточные результаты, которые в действительности вовсе не нужно сохранять в контейнере хранилища BLOB-объектов. В этом случае можно выбрать хранение данных в локальной системе HDFS. На деле HDInsight использует DFS для некоторых таких промежуточных результатов в заданиях Hive и других процессах.

## <span id="preparingblobstorage"></span></a>Подготовка контейнера для хранения BLOB-объектов

Чтобы использовать BLOB-объекты, сначала создайте [учетную запись хранилища Azure][учетную запись хранилища Azure]. В ходе этого процесса вы указываете центр обработки данных Azure, в котором будут храниться объекты, создаваемые с помощью этой учетной записи. Кластер и учетная запись хранения должны размещаться в одном центре обработки данных (база данных SQL метахранилища Hive и база данных SQL метахранилища Oozie также должны находиться в одном центре обработки данных). Где бы ни находился созданный BLOB-объект, он принадлежит контейнеру в вашей учетной записи хранения. Этим контейнером может быть существующий контейнер хранилища BLOB-объектов, созданный вне HDInsight, или контейнер, создаваемый для кластера HDInsight.

### Создание контейнера BLOB-объекта для HDInsight с использованием портала управления

При подготовке кластера HDInsight на портале управления Azure предлагаются два варианта: *Быстрое создание* и *Настраиваемое создание*. Для быстрого создания требуется созданная заранее учетная запись хранения Azure. Инструкции см. в разделе [Создание учетной записи хранения][учетную запись хранилища Azure].

При использовании быстрого создания можно выбрать существующую учетную запись хранения. В процессе подготовки создается новый контейнер, имя которого совпадает с именем кластера HDInsight. Если контейнер с таким именем уже существует, будет использоваться имя <clustername>-<x>. Например, myHDIcluster-1. Этот контейнер используется в качестве файловой системы по умолчанию.

![HDI.QuickCreate][HDI.QuickCreate]

При использовании настраиваемого создания существует два варианта для учетной записи хранения:

-   Использовать существующее хранилище
-   Создать новое хранилище
-   Использовать хранилище другой подписки

Кроме этого, имеется возможность создать собственный контейнер Blob-объектов или использовать существующий.

![HDI.CustomCreateStorageAccount][HDI.CustomCreateStorageAccount]

### Создание контейнера с помощью Azure PowerShell.

[Azure PowerShell][Azure PowerShell] может использоваться для создания контейнеров BLOB-объектов. Ниже приведен пример сценария PowerShell.

    $subscriptionName = "<SubscriptionName>"    # Azure subscription name
    $storageAccountName = "<AzureStorageAccountName>" # The storage account that you will create
    $containerName="<BlobContainerToBeCreated>" # The Blob container name that you will create

    # Connect to your Azure account and selec the current subscription
    Add-AzureAccount # The connection will expire in a few hours.
    Select-AzureSubscription $subscriptionName #only required if you have multiple subscriptions

    # Create a storage context object
    $storageAccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    # Create a Blob storage container
    New-AzureStorageContainer -Name $containerName -Context $destContext 

## <span id="addressing"></span></a>Обращение к файлам в хранилище BLOB-объектов

Схема URI для доступа к файлам в хранилище BLOB-объектов:

    wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>

> [WACOM.NOTE] Для обращения к файлам в эмуляторе хранилища (эмуляторе, работающем в HDInsight) используется синтаксис: <i>wasb://&lt;ContainerName&gt;@storageemulator</i>.

Схема URI предоставляет как незашифрованный доступ с префиксом *wasb:* так и доступ с использованием SSL-шифрования и префикса *wasbs*. Мы рекомендуем использовать *wasbs* везде, где это возможно, даже при обращении к данным, расположенным внутри того же центра обработки данных Azure.

\<BlobStorageContainerName\> определяет имя контейнера хранилища BLOB-объектов.
The \<StorageAccountName\> определяет имя учетной записи хранения Azure. Обязательно использовать полное доменное имя (FQDN).

Если ни \<BlobStorageContainerName\>, ни \<StorageAccountName\> не заданы, то используется файловая система по умолчанию. Для файлов в файловой системе по умолчанию можно использовать относительный или абсолютный путь. Например, для ссылки на файл hadoop-mapreduce-examples.jar, который поставляется с кластерами HDInsight, можно использовать один из следующих вариантов:

    wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
    wasb:///example/jars/hadoop-mapreduce-examples.jar
    /example/jars/hadoop-mapreduce-examples.jar

> [WACOM.NOTE] Имя файла — *hadoop-examples.jar* в кластерах HDInsight версии 1.6 и 2.1.

\<path\> — это имя пути к файлу или каталогу HDFS. Поскольку контейнеры хранилища BLOB-объектов содержат данные типа "ключ-значение", подлинная иерархическая файловая система в них отсутствует. Символ "/" в ключе BLOB-объекта интерпретируется как разделитель каталогов. Например, имя BLOB-объекта для файла *hadoop-mapreduce-examples.jar* выглядит следующим образом:

    example/jars/hadoop-mapreduce-examples.jar

## <span id="powershell"></span></a>Доступ к BLOB-объекту с помощью Azure PowerShell

Сведения об установке и настройке Azure PowerShell на рабочей станции см. в разделе [Установка и настройка Azure PowerShell][Azure PowerShell]. Для выполнения командлетов PowerShell можно использовать окно консоли Azure PowerShell или PowerShell\_ISE.

Чтобы просмотреть список командлетов, связанных с BLOB-объектом, используйте следующую команду:

    Get-Command *blob*

![Blob.PowerShell.cmdlets][Blob.PowerShell.cmdlets]

**Пример PowerShell для отправки файла**

См. раздел [Отправка данных в HDInsight][Отправка данных в HDInsight].

**Пример PowerShell для загрузки файла**

В приведенном ниже сценарии BLOB-объект блочного типа загружается в текущую папку. Перед выполнением сценария измените каталог на папку, для которой вы имеете разрешение на запись.

    $storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
    $containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
    $blob = "example/data/sample.log" # The name of the blob to be downloaded.

    # Use Add-AzureAccount if you haven't connected to your Azure subscription
    #Add-AzureAccount # The connection is good for 12 hours

    # Use these two commands if you have multiple subscriptions
    #$subscriptionName = "<SubscriptionName>"       
    #Select-AzureSubscription $subscriptionName

    Write-Host "Create a context object ... " -ForegroundColor Green
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    Write-Host "Download the blob ..." -ForegroundColor Green
    Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force

    Write-Host "List the downloaded file ..." -ForegroundColor Green
    cat "./$blob"

**Пример PowerShell для удаления файла**

В следующем скрипте показано, как удалить файл.
 $storageAccountName = "<azurestorageaccountname>" \# учетная запись хранения для файловой системы по умолчанию, определяемая при подготовке.
 $containerName = "<blobstoragecontainername>" \# Контейнер файловой системы по умолчанию имеет то же имя, что и кластер.
 $blob = "example/data/sample.log" \# Имя blob-объекта для загрузки.

    # Use Add-AzureAccount if you haven't connected to your Azure subscription
    #Add-AzureAccount # The connection is good for 12 hours

    # Use these two commands if you have multiple subscriptions
    #$subscriptionName = "<SubscriptionName>"       
    #Select-AzureSubscription $subscriptionName

    Write-Host "Create a context object ... " -ForegroundColor Green
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    Write-Host "Delete the blob ..." -ForegroundColor Green
    Remove-AzureStorageBlob -Container $containerName -Context $storageContext -blob $blob 

**Пример PowerShell для получения списка файлов в папке**

В следующем скрипте показано, как получить список файлов в папке. В следующем примере показано, как использовать командлет Invoke-Hive для выполнения команды dfs ls, чтобы получить список папки.

    $storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
    $containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
    $blobPrefix = "example/data/"

    # Use Add-AzureAccount if you haven't connected to your Azure subscription
    #Add-AzureAccount # The connection is good for 12 hours

    # Use these two commands if you have multiple subscriptions
    #$subscriptionName = "<SubscriptionName>"       
    #Select-AzureSubscription $subscriptionName

    Write-Host "Create a context object ... " -ForegroundColor Green
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    Write-Host "List the files in $blobPrefix ..."
    Get-AzureStorageBlob -Container $containerName -Context $storageContext -prefix $blobPrefix

**Пример доступа к незаданной учетной записи хранения с помощью PowerShell**

В этом пример показано, как получить список папки из учетной записи хранения, которая не была задана во время подготовки.
 $clusterName = "<hdinsightclustername>"

    $undefinedStorageAccount = "<UnboundedStorageAccountUnderTheSameSubscription>"
    $undefinedContainer = "<UnboundedBlobContainerAssociatedWithTheStorageAccount>"

    $undefinedStorageKey = Get-AzureStorageKey $undefinedStorageAccount | %{ $_.Primary }

    Use-AzureHDInsightCluster $clusterName

    $defines = @{}
    $defines.Add("fs.azure.account.key.$undefinedStorageAccount.blob.core.windows.net", $undefinedStorageKey)

    Invoke-Hive -Defines $defines -Query "dfs -ls wasb://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"

## <span id="nextsteps"></span></a>Дальнейшие действия

Из этой статьи вы узнали, как использовать хранилище BLOB-объектов с HDInsight, а также тот факт, что это хранилища BLOB-объектов является основным компонентом HDInsight. Это позволяет создавать масштабируемые, долгосрочные решения для получения данных архивирования с помощью хранилища BLOB-объектов Azure, а также использовать HDInsight для разблокирования информации внутри хранимых данных.

Для получения дополнительных сведений ознакомьтесь со следующими статьями:

-   [Приступая к работе с Azure HDInsight][Приступая к работе с HDInsight]
-   [Отправка данных в HDInsight][Отправка данных в HDInsight]
-   [Использование Hive с HDInsight][Использование Hive с HDInsight]
-   [Использование Pig с HDInsight][Использование Pig с HDInsight]

  [Приступая к работе с HDInsight]: ../hdinsight-get-started/
  [Подготовка кластеров HDInsight]: ../hdinsight-provision-clusters/
  [Архитектура хранилища HDInsight]: #architecture
  [Преимущества хранилища BLOB-объектов Azure]: #benefits
  [Подготовка контейнера для хранения BLOB-объектов]: #preparingblobstorage
  [Обращение к файлам в хранилище BLOB-объектов]: #addressing
  [Доступ к BLOB-объекту с помощью PowerShell]: #powershell
  [Дальнейшие действия]: #nextsteps
  [HDI.ASVArch]: ./media/hdinsight-use-blob-storage/HDI.ASVArch.png "Архитектура хранилища HDInsight"
  [Ограничение доступа к контейнерам и BLOB-объектам]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179354.aspx
  [Использование кластера HDInsight с дополнительными учетными записями хранения и метахранилищами]: http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
  [интерфейсы API REST хранилища BLOB-объектов]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dd135733.aspx
  [учетную запись хранилища Azure]: ../storage-create-storage-account/
  [HDI.QuickCreate]: ./media/hdinsight-use-blob-storage/HDI.QuickCreateCluster.png
  [HDI.CustomCreateStorageAccount]: ./media/hdinsight-use-blob-storage/HDI.CustomCreateStorageAccount.png
  [Azure PowerShell]: ../install-configure-powershell/
  [Blob.PowerShell.cmdlets]: ./media/hdinsight-use-blob-storage/HDI.PowerShell.BlobCommands.png
  [Отправка данных в HDInsight]: ../hdinsight-upload-data/
  [Использование Hive с HDInsight]: ../hdinsight-use-hive/
  [Использование Pig с HDInsight]: ../hdinsight-use-pig/
