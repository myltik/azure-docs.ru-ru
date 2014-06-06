<properties linkid="manage-services-hdinsight-howto-blob-store" urlDisplayName="Хранилище BLOB-объектов с HDInsight" pageTitle="Использование хранилища BLOB-объектов с HDInsight | Azure" metaKeywords="" description="Узнайте как HDInsight использует хранилище BLOB-объектов в качестве основного хранилища данных для HDFS и как можно запрашивать данные из хранилища." metaCanonical="" services="storage,hdinsight" documentationCenter="" title="Использование хранилища BLOB-объектов Azure с HDInsight" authors=""  solutions="" writer="jgao" manager="paulettm" editor="mollybos"  />




#Использование хранилища BLOB-объектов Azure с HDInsight




Azure HDInsight поддерживает хранение данных и в распределенной файловой системе Hadoop (HDFS), и в хранилище BLOB-объектов Azure. Хранилище BLOB-объектов — надежное, универсальное решение для хранилища Azure. Хранилище BLOB-объектов предоставляет полноценный интерфейс файловой системы HDFS, обеспечивающий удобство работы, предоставляя полный набор компонентов экосистемы Hadoop, который можно (по умолчанию) применять непосредственно к данным. Хранилище BLOB-объектов не только является экономичным решением — хранение данных в BLOB-объектах позволяет удалять кластеры HDInsight, используемые для расчетов, без потери пользовательских данных. 

> [WACOM.NOTE]	Синтаксис *asv://* не поддерживается в кластерах HDInsight версии 3.0 и не будет поддерживаться в более поздних версиях. Это означает, что все задания, отправляемые в кластер HDInsight версии 3.0, в которых явно используется синтаксис "asv://", завершатся неудачей. Вместо этого нужно использовать синтаксис *wasb://*. Задания, отправленные в кластеры HDInsight версии 3.0 и созданные с использованием существующего метахранилища, в которых содержатся явные ссылки на ресурсы с использованием синтаксиса asv://, также завершатся неудачей. Эти метахранилища потребуется создать повторно с использованием синтаксиса wasb://, чтобы адресовать ресурсы.

> [WACOM.NOTE] В настоящее время HDInsight поддерживает только BLOB-объекты блочного типа.

> [WACOM.NOTE]
> Большинство команд HDFS, таких как <b>ls</b>, <b>copyFromLocal</b>, <b>mkdir</b> и т. д., по-прежнему работают должным образом. В хранилище BLOB-объектов Azure будет отличаться поведение только тех команд, которые относятся к базовой реализации HDFS (под названием DFS), например <b>fschk</b> и <b>dfsadmin</b>.

Сведения о подготовке кластера HDInsight см. в разделе [Приступая к работе с HDInsight][hdinsight-getting-started] или [Подготовка кластеров HDInsight][hdinsight-provision].

##Содержание

* [Архитектура хранилища HDInsight](#architecture)
* [Преимущества хранилища BLOB-объектов Azure](#benefits)
* [Подготовка контейнера для хранения BLOB-объектов](#preparingblobstorage)
* [Обращение к файлам в хранилище BLOB-объектов](#addressing)
* [Доступ к BLOB-объекту с помощью PowerShell](#powershell)
* [Дальнейшие действия](#nextsteps)

##<a id="architecture"></a>Архитектура хранилища HDInsight
Следующая схема является абстрактным представлением архитектуры хранилища HDInsight:

![HDI.ASVArch](./media/hdinsight-use-blob-storage/HDI.ASVArch.png "HDInsight Storage Architecture")
  
HDInsight предоставляет доступ к распределенной файловой системе, которая локально присоединена к вычислительным узлам. Доступ к этой файловой системе может осуществляться с использованием полного URI. Например: 

	hdfs://<namenodehost>/<path>

Кроме того, HDInsight предоставляет возможность доступа к данным, содержащимся в хранилище BLOB-объектов. Синтаксис для доступа к хранилищу BLOB-объектов:

	wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>


В Hadoop поддерживается концепция файловой системы по умолчанию. Файловая система по умолчанию подразумевает стандартную схему и полномочный орган. Она также может использоваться для разрешения относительных путей. В процессе подготовки HDInsight в качестве файловой системы по умолчанию устанавливаются учетная запись хранения Azure и конкретный контейнер хранилища BLOB-объектов из этой учетной записи.

Помимо этой учетной записи хранения в процессе подготовки можно добавить дополнительные учетные записи хранения из той же подписки Azure или других подписок Azure. Инструкции по добавлению дополнительных учетных записей хранения см. в разделе [Подготовка кластеров HDInsight][hdinsight-provision]. 

* **Контейнеры в учетных записях хранения, подключенные к кластеру:** поскольку имя учетной записи и ключ хранятся в *core-site.xml*, вы получаете полный доступ к BLOB-объектам в этих контейнерах.
* **Общедоступные контейнеры или общедоступные BLOB-объекты в учетных записях хранения, не подключенные к кластеру:** вы получаете разрешение только на чтение BLOB-объектов в контейнерах.

	> [WACOM.NOTE]
        > Общедоступный контейнер позволяет получить список всех доступных в этом контейнере BLOB-объектов, а также метаданные контейнера. Общедоступный BLOB-объект позволяет получить доступ к BLOB-объектам только при условии, что вам известен точный URL-адрес. Дополнительные сведения см. в разделе <a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179354.aspx">Ограничение доступа к контейнерам и BLOB-объектам</a>.

* **Закрытые контейнеры в учетных записях хранения, не подключенные к кластеру:** вы не можете получить доступ к BLOB-объектам в контейнерах.


В контейнерах хранилища BLOB-объектов данные хранятся в виде пар "ключ-значение" и отсутствует иерархия каталогов. Однако в имени ключа может использоваться символ "/", чтобы оно выглядело так, как будто файл хранится в структуре каталогов. Например, ключ BLOB-объекта может выглядеть следующим образом: *input/log1.txt*. Фактически никакого каталога *input* не существует, но из-за наличия символа "/" имя ключа выглядит как путь к файлу.










##<a id="benefits"></a>Преимущества хранилища BLOB-объектов Azure
Предполагаемые рабочие затраты из-за отсутствия совмещенных вычислительных ресурсов и ресурсов хранения снижаются за счет того, что вычислительные кластеры подготавливаются близко к ресурсам учетных записей хранения в центре обработки данных Azure, где высокоскоростная сеть обеспечивает высокую эффективность доступа вычислительных узлов к данным в хранилище BLOB-объектов.

Ниже перечислены некоторые преимущества, связанные с хранением данных в хранилище BLOB-объектов (вместо HDFS).

* **Повторное использование данных и общий доступ к данным.** Данные в файловой системе HDFS находятся внутри вычислительного кластера. Только приложения, имеющие доступ к вычислительному кластеру, могут использовать данные через API HDFS. Доступ к данным в хранилище BLOB-объектов может осуществляться через интерфейсы API HDFS или через [интерфейсы API REST хранилища BLOB-объектов](http://msdn.microsoft.com/ru-ru/library/windowsazure/dd135733.aspx). Таким образом, для создания и использования данных можно применять больший набор приложений (включая другие кластеры HDInsight) и средств.
* **Архивирование данных.** Хранение данных в BLOB-объектах позволяет удалять кластеры HDInsight, использовавшиеся для вычислений, без потери пользовательских данных. 
* **Затраты на хранение данных.** Хранение данных в файловой системе DFS в долгосрочной перспективе более затратное, чем хранение данных в BLOB-объектах, поскольку стоимость вычислительного кластера превышает стоимость контейнера хранилища BLOB-объектов. Кроме того, поскольку данные не требуется повторно загружать при создании каждого вычислительного кластера, вы экономите также на загрузке данных.
* **Гибкое масштабирование.** Хотя HDFS и предоставляет масштабируемую файловую систему, масштаб определяется количеством узлов, подготавливаемых для кластера. Изменение масштаба может оказаться более сложным процессом, чем использование гибких возможностей масштабирования хранилища BLOB-объектов, которые вы получаете автоматически.
* **Георепликация.** Возможна георепликация контейнеров хранилища BLOB-объектов через портал Azure. Это обеспечивает возможность географического восстановления и избыточность данных, однако переход в расположение геореплицированных данных при отработке отказа заметно скажется на производительности и может потребовать дополнительных затрат. Поэтому мы рекомендуем взвешенно подходить к выбору георепликации и выбирать ее только в том случае, если ценность данных окупит дополнительные затраты.

Определенные задания и пакеты MapReduce могут создавать промежуточные результаты, которые в действительности вовсе не нужно сохранять в контейнере хранилища BLOB-объектов. В этом случае можно выбрать хранение данных в локальной системе HDFS. На деле HDInsight использует DFS для некоторых таких промежуточных результатов в заданиях Hive и других процессах. 





##<a id="preparingblobstorage"></a>Подготовка контейнера для хранения BLOB-объектов
Чтобы использовать BLOB-объекты, сначала создайте [учетную запись хранения Azure](/ru-ru/manage/services/storage/how-to-create-a-storage-account/). В ходе этого процесса вы указываете центр обработки данных Azure, в котором будут храниться объекты, создаваемые с помощью этой учетной записи. Кластер и учетная запись хранения должны размещаться в одном центре обработки данных (база данных SQL метахранилища Hive и база данных SQL метахранилища Oozie также должны находиться в одном центре обработки данных). Где бы ни находился созданный BLOB-объект, он принадлежит контейнеру в вашей учетной записи хранения. Этим контейнером может быть существующий контейнер хранилища BLOB-объектов, созданный вне HDInsight, или контейнер, создаваемый для кластера HDInsight. 



###Создание контейнера BLOB-объекта для HDInsight с использованием портала управления

При подготовке кластера HDInsight на портале управления Azure предлагаются два варианта: *быстрое создание* и *настраиваемое создание*. Для быстрого создания требуется созданная заранее учетная запись хранения Azure.  Инструкции см. в разделе [Создание учетной записи хранения]( /ru-ru/manage/services/storage/how-to-create-a-storage-account/). 

При использовании быстрого создания можно выбрать существующую учетную запись хранения. В процессе подготовки создается новый контейнер, имя которого совпадает с именем кластера HDInsight. Этот контейнер используется в качестве файловой системы по умолчанию.

![HDI.QuickCreate](./media/hdinsight-use-blob-storage/HDI.QuickCreateCluster.png "HDInsight Cluster Quick Create")
 
При использовании настраиваемого создания можно выбрать существующий контейнер хранилища BLOB-объектов или создать контейнер по умолчанию. Имя контейнера по умолчанию совпадает с именем кластера HDInsight.

![HDI.CustomCreateStorageAccount](./media/hdinsight-use-blob-storage/HDI.CustomCreateStorageAccount.png  "Custom Create Storage Account" )
  




### Создание контейнера с помощью Azure PowerShell.
[Azure PowerShell][powershell-install] может использоваться для создания контейнеров BLOB-объектов. Ниже приведен пример сценария PowerShell.

	$subscriptionName = "<SubscriptionName>"
	$storageAccountName = "<WindowsAzureStorageAccountName>"
	$containerName="<BlobContainerToBeCreated>"

	Add-AzureAccount # The connection is good for 12 hours.
	Select-AzureSubscription $subscriptionName #only required if you have multiple subscriptions
	
	# Create a storage context object
	$storageAccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext 


##<a id="addressing"></a>Обращение к файлам в хранилище BLOB-объектов

Схема URI для доступа к файлам в хранилище BLOB-объектов: 

	wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>


> [WACOM.NOTE] Для обращения к файлам в эмуляторе хранилища (эмуляторе, работающем в HDInsight) используется синтаксис: <i>wasb://&lt;ContainerName&gt;@storageemulator</i>.



Эта схема URI предоставляет как незашифрованный доступ с префиксом *wasb:*, так и доступ с использованием SSL-шифрования и префикса *wasbs*. Мы рекомендуем использовать *wasbs* везде, где это возможно, даже при обращении к данным, расположенным внутри того же центра обработки данных Azure.
	
&lt;BlobStorageContainerName&gt; определяет имя контейнера хранилища BLOB-объектов.
&lt;StorageAccountName&gt; определяет имя учетной записи хранения Azure. Обязательно использовать полное доменное имя (FQDN).
	
Если ни &lt;BlobStorageContainerName&gt;, ни &lt;StorageAccountName&gt; не указано, то используется файловая система по умолчанию. Для файлов в файловой системе по умолчанию можно использовать относительный или абсолютный путь. Например, для ссылки на файл hadoop-mapreduce-examples.jar, который поставляется с кластерами HDInsight, можно использовать один из следующих вариантов:

	wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
	wasb:///example/jars/hadoop-mapreduce-examples.jar
	/example/jars/hadoop-mapreduce-examples.jar
	
> [WACOM.NOTE] В кластерах HDInsight версий 1.6 и 2.1 файл называется <i>hadoop-examples.jar</i>.


&lt;Путь&gt; — это имя пути к файлу или каталогу HDFS. Поскольку контейнеры хранилища BLOB-объектов содержат данные типа "ключ-значение", подлинная иерархическая файловая система в них отсутствует. Символ "/" в ключе BLOB-объекта интерпретируется как разделитель каталогов. Например, имя BLOB-объекта для файла *hadoop-mapreduce-examples.jar* выглядит следующим образом:

	example/jars/hadoop-mapreduce-examples.jar
	

##<a id="powershell"></a>Доступ к BLOB-объекту с помощью Azure PowerShell

Сведения об установке и настройке Azure PowerShell на рабочей станции см. в разделе [Установка и настройка Azure PowerShell][powershell-install]. Для выполнения командлетов PowerShell можно использовать окно консоли Azure PowerShell или PowerShell_ISE. 

Чтобы просмотреть список командлетов, связанных с BLOB-объектом, используйте следующую команду:

	Get-Command *blob*

![Blob.PowerShell.cmdlets][img-hdi-powershell-blobcommands]


**Пример PowerShell для отправки файла**

См. раздел [Отправка данных в HDInsight][hdinsight-upload-data].

**Пример PowerShell для загрузки файла**

В приведенном ниже сценарии BLOB-объект блочного типа загружается в текущую папку. Перед выполнением сценария измените каталог на папку, для которой вы имеете разрешение на запись. 


	$storageAccountName = "<WindowsAzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
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

	$storageAccountName = "<WindowsAzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
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
	
	Write-Host "Delete the blob ..." -ForegroundColor Green
	Remove-AzureStorageBlob -Container $containerName -Context $storageContext -blob $blob 
	

**Пример PowerShell для получения списка файлов в папке**

	$storageAccountName = "<WindowsAzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
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

##<a id="nextsteps"></a>Дальнейшие действия

Из этой статьи вы узнали, как использовать хранилище BLOB-объектов с HDInsight, а также тот факт, что это хранилища BLOB-объектов является основным компонентом HDInsight. Это позволяет создавать масштабируемые, долгосрочные решения для получения данных архивирования с помощью хранилища BLOB-объектов Azure, а также использовать HDInsight для разблокирования информации внутри хранимых данных.

Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Приступая к работе с Azure HDInsight][hdinsight-getting-started]
* [Отправка данных в HDInsight][hdinsight-upload-data]
* [Использование Hive с HDInsight][hdinsight-hive]
* [Использование Pig с HDInsight][hdinsight-pig]

[Powershell-install]: ../install-configure-powershell/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-getting-started]: ../hdinsight-get-started-hdinsight/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-hive]: ../hdinsight-using-hive/
[hdinsight-pig]: ../hdinsight-using-pig/

[Powershell-install]: ../install-configure-powershell/

[img-hdi-powershell-blobcommands]: ./media/hdinsight-use-blob-storage/HDI.PowerShell.BlobCommands.png 


