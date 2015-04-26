<properties 
	pageTitle="Запрос большого объема данных из Hadoop-совместимого хранилища больших двоичных объектов Azure" 
	description="HDInsight использует Hadoop-совместимое хранилище больших двоичных для хранения данных большого размера для HDFS. Узнайте, как выполнять запросы в хранилище больших двоичных данных и сохранять результаты анализа." 
	services="storage, hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="mollybos"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/12/2014" 
	ms.author="jgao"/>


# Запрос большого объема данных из Hadoop-совместимого хранилища больших двоичных объектов для анализа в HDInsight

Малозатратное хранилище больших двоичных объектов - это надежное универсальное решение хранилища Azure, совместимое с Hadoop, которое незаметно интегрируется с HDInsight. С помощью интерфейса распределенной файловой системы Hadoop (HDFS) все компоненты HDInsight могут напрямую взаимодействовать с данными в хранилище больших двоичных объектов. В этом учебнике вы узнаете о том, как настроить контейнер хранилища больших двоичных объектов и обращаться к хранимым в нем данным.

Хранение данных в больших двоичных объектах позволяет удалять кластеры HDInsight, использовавшиеся для вычислений, без потери пользовательских данных. 

> [AZURE.NOTE]	Синтаксис *asv://* не поддерживается в кластерах HDInsight версии 3.0 и не будет поддерживаться в более поздних версиях. Это означает, что все задания, отправляемые в кластер HDInsight версии 3.0, в которых явно используется синтаксис asv://, завершатся с ошибкой. Вместо этого следует использовать синтаксис *wasb://*. Задания, отправленные в кластеры HDInsight версии 3.0 и созданные с использованием существующего метахранилища, в которых содержатся явные ссылки на ресурсы с использованием синтаксиса asv://, также завершатся неудачей. Эти метахранилища потребуется создать повторно с использованием синтаксиса wasb://, чтобы адресовать ресурсы.

> В настоящее время HDInsight поддерживает только большие двоичные объекты блочного типа.

> Большинство команд HDFS, таких как <b>ls</b>, <b>copyFromLocal</b>, <b>mkdir</b> и т. д., по-прежнему работают должным образом. Только те команды, которые относятся к базовой реализации HDFS (который называется DFS), такие как <b>fschk</b> и <b>dfsadmin</b>, будут отображаться по-разному в хранилище BLOB-объектов Azure.

Дополнительные сведения о подготовке кластера HDInsight см. в разделе [Приступая к работе с HDInsight][hdinsight-get-started] или [Подготовка кластеров HDInsight][hdinsight-provision].

## Содержание

* [Архитектура хранилища HDInsight](#architecture)
* [Преимущества использования хранилища BLOB-объектов Azure](#benefits)
* [Подготовка контейнера для хранения BLOB-объектов](#preparingblobstorage)
* [Обращение к файлам в хранилище BLOB-объектов](#addressing)
* [Доступ к BLOB-объекту с помощью PowerShell](#powershell)
* [Дальнейшие действия](#nextsteps)

## <a id="architecture"></a>Архитектура хранилища HDInsight
Следующая схема является абстрактным представлением архитектуры хранилища HDInsight:

![Hadoop clusters in HDInsight access and store big data in cost-effective, scalable Hadoop-compatible Azure Blob storage in the cloud.](./media/hdinsight-use-blob-storage/HDI.ASVArch.png "HDInsight Storage Architecture")
  
HDInsight предоставляет доступ к распределенной файловой системе, которая локально присоединена к вычислительным узлам. Доступ к этой файловой системе может осуществляться с использованием полного URI. Например: 

	hdfs://<namenodehost>/<path>

Кроме того, HDInsight предоставляет возможность доступа к данным, содержащимся в хранилище BLOB-объектов. Синтаксис для доступа к хранилищу BLOB-объектов:

	wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>


В Hadoop поддерживается концепция файловой системы по умолчанию. Файловая система по умолчанию подразумевает стандартную схему и полномочный орган. Она также может использоваться для разрешения относительных путей. В процессе подготовки HDInsight в качестве файловой системы по умолчанию устанавливаются учетная запись хранения Azure и конкретный контейнер хранилища BLOB-объектов из этой учетной записи.

Помимо этой учетной записи хранения в процессе подготовки можно добавить дополнительные учетные записи хранения из той же подписки Azure или других подписок Azure. Инструкции по добавлению дополнительных учетных записей хранения см. в разделе [Подготовка кластеров HDInsight][hdinsight-provision]. 

- **Контейнеры в учетных записях хранения, подключенные к   кластеру:** поскольку имя учетной записи и ключ хранятся в *core-site.xml*, вы имеете полный доступ к BLOB-объектам в этих контейнерах.
- **Общедоступные контейнеры или общедоступные BLOB-объекты в учетных записях хранения, не подключенные к кластеру:** вы получаете разрешение только на чтение BLOB-объектов в контейнерах.

	> [AZURE.NOTE]
        > Общедоступный контейнер позволяет получить список всех доступных в этом контейнере BLOB-объектов, а также метаданные контейнера. Общедоступный BLOB-объект позволяет   получать доступ к BLOB-объектов только в том случае, если известен точный URL-адрес. Дополнительные сведения см. в разделе <a href="http://msdn.microsoft.com/library/windowsazure/dd179354.aspx">Ограничение доступа к контейнерам и большим двоичным объектам</a>.

- **Закрытые контейнеры в учетных записях хранения, не подключенные к кластеру:** отсутствует доступ к BLOB-объектам в контейнерах, если при отправке задания WebHCat не определить учетную запись хранения. Это объясняется далее в этой статье.


Учетные записи хранения, определенные в процессе подготовки, и их ключи хранятся в %HADOOP_HOME%/conf/core-site.xml.  По умолчанию HDInsight будет использовать учетные записи хранения, описанные в файле core-site.xml. Не рекомендуется изменять файл core-site.xml, поскольку образ головного (ведущего) узла кластера может быть в любое время установлен повторно или перенесен, и все изменения в этих файлах будут утеряны.

Несколько заданий WebHCat, в том числе Hive, MapReduce, потоковая передача Hadoop и Pig, могут переносить описание учетных записей хранения и метаданные вместе с ними (в настоящее время эта функция работает для Pig с учетными записями хранения, но не с метаданными). В разделе [Доступ к BLOB-объекту с помощью PowerShell](#powershell) данной статьи приводится пример использования данной функции. Для получения дополнительной информации см. раздел [Использование кластера HDInsight с дополнительными учетными записями хранения и метахранилищами](http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

В контейнерах хранилища BLOB-объектов данные хранятся в виде пар "ключ-значение" и отсутствует иерархия каталогов. Однако в имени ключа может использоваться символ "/", чтобы оно выглядело так, как будто файл хранится в структуре каталогов. Например, ключ BLOB-объекта может выглядеть следующим образом:  *input/log1.txt*. Фактически никакого каталога *input* не существует, но из-за наличия символа "/" имя ключа выглядит как путь к файлу.










## <a id="benefits"></a>Преимущества хранилища BLOB-объектов Azure
Предполагаемые рабочие затраты из-за отсутствия совмещенных вычислительных ресурсов и ресурсов хранения снижаются за счет того, что вычислительные кластеры подготавливаются близко к ресурсам учетных записей хранения в центре обработки данных Windows Azure, где высокоскоростная сеть обеспечивает высокую эффективность доступа вычислительных узлов к данным в хранилище BLOB-объектов.

Ниже перечислены некоторые преимущества, связанные с хранением данных в хранилище BLOB-объектов (вместо HDFS).

* **Повторное использование данных и совместное использование:** данные в HDFS находятся внутри вычислительного кластера. Только приложения, которые имеют доступ к вычислительному кластеру, могут использовать данные с помощью интерфейса API HDFS. Доступ к данным в хранилище BLOB-объектов может осуществляться через API HDFS или через [интерфейс API REST хранилища BLOB-объектов][blob-storage-restAPI]. Таким образом, можно использовать больший ассортимент приложений (включая другие кластеры HDInsight) и средств для создания и использования данных.
* **Архивация данных:** хранение данных в хранилище BLOB-объектов позволяет безопасно удалять кластеры HDInsight, используемым для расчетов, без потери пользовательских данных. 
* **Затраты на хранение данных:** хранение данных в DFS в долгосрочной перспективе является более дорогостоящим, чем хранение данных в хранилище BLOB-объектов, поскольку стоимость вычислительного кластера превышает стоимость контейнера хранилища BLOB-объектов. Кроме того, поскольку нет необходимости выполнять повторную загрузку данных для каждого поколения вычислительного кластера, имеется экономия и на загрузке данных.
* **Гибкое масштабирование:** хотя HDFS и предоставляет масштабируемую файловую систему, масштаб определяется количеством узлов, подготавливаемых для кластера. Изменение масштаба может стать более сложным процессом, чем при использовании гибких возможностей масштабирования хранилища BLOB-объектов, которые вы получаете автоматически.
* **Георепликация:** ваши контейнеры хранилища BLOB-объектов могут географически реплицироваться на портале Azure. В то время как это предоставляет возможности географического восстановления и избыточность данных, переход в другое расположение существенно влияет на производительность и может потребовать дополнительных затрат. Поэтому рекомендуется рационально выбирать географическую репликацию, и только в том случае, если ценность данных стоит дополнительных затрат.

Определенные задания и пакеты MapReduce могут создавать промежуточные результаты, которые в действительности вовсе не нужно сохранять в контейнере хранилища BLOB-объектов. В этом случае можно выбрать хранение данных в локальной системе HDFS. На деле HDInsight использует DFS для некоторых таких промежуточных результатов в заданиях Hive и других процессах. 





## <a id="preparingblobstorage"></a>Подготовка контейнера для хранения BLOB-объектов
Чтобы использовать BLOB-объекты, сначала создайте [учетную запись хранилища Azure][azure-storage-create]. В ходе этого процесса вы указываете центр обработки данных Azure, в котором будут храниться объекты, создаваемые с помощью этой учетной записи. Кластер и учетная запись хранения должны размещаться в одном центре обработки данных (база данных SQL метахранилища Hive и база данных SQL метахранилища Oozie также должны находиться в одном центре обработки данных). Где бы ни находился созданный BLOB-объект, он принадлежит контейнеру в вашей учетной записи хранения. Этим контейнером может быть существующий контейнер хранилища BLOB-объектов, созданный вне HDInsight, или контейнер, создаваемый для кластера HDInsight. 



### Создание контейнера BLOB-объекта для HDInsight с использованием портала управления

При подготовке кластера HDInsight на портале управления Azure предлагаются два варианта: *quick create* и *custom create*. Для быстрого создания требуется созданная заранее учетная запись хранения Azure.  Инструкции см. в разделе [Создание учетной записи хранения][azure-storage-create]. 

При использовании быстрого создания можно выбрать существующую учетную запись хранения. В процессе подготовки создается новый контейнер, имя которого совпадает с именем кластера HDInsight. Если контейнер с таким именем уже существует, будет использоваться имя <clusterName>-<x>. Например, myHDIcluster-1. Этот контейнер используется в качестве файловой системы по умолчанию.

![Using Quick Create for a new Hadoop cluster in HDInsight in the Azure portal.][img-hdi-quick-create]
 
При использовании настраиваемого создания существует два варианта для учетной записи хранения:

- Использовать существующее хранилище
- Создать новое хранилище
- Использовать хранилище другой подписки

Кроме этого, имеется возможность создать собственный контейнер Blob-объектов или использовать существующий.
 
![Option to use an existing storage account for your HDInsight cluster.][img-hdi-custom-create-storage-account]
  




### Создание контейнера с помощью Azure PowerShell.
[Azure PowerShell][powershell-install] может использоваться для создания контейнеров BLOB-объектов. Ниже приведен пример сценария PowerShell.

	$subscriptionName = "<SubscriptionName>"	# Azure subscription name
	$storageAccountName = "<AzureStorageAccountName>" # The storage account that you will create
	$containerName="<BlobContainerToBeCreated>" # The Blob container name that you will create

	# Connect to your Azure account and selec the current subscription
	Add-AzureAccount # The connection will expire in 12 hours.
	Select-AzureSubscription $subscriptionName #only required if you have multiple subscriptions
	
	# Create a storage context object
	$storageAccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext 


## <a id="addressing"></a>Обращение к файлам в хранилище BLOB-объектов

Схема URI для доступа к файлам в хранилище BLOB-объектов: 

	wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>


> [AZURE.NOTE] Для обращения к файлам в эмуляторе хранения (эмуляторе, работающем в HDInsight) используется синтаксис: <i>wasb://&lt;имя_контейнера&gt;@эмулятор_хранилища</i>.



Эта схема URI предоставляет как незашифрованный доступ с префиксом  *wasb:*, так и доступ с использованием SSL-шифрования и префикса *wasbs*. Мы рекомендуем использовать *wasbs* везде, где это возможно, даже в случае доступа к данным, расположенным внутри того же центра обработки данных Azure.
	
&lt;BlobStorageContainerName&gt; определяет имя контейнера хранилища BLOB-объектов.
&lt;StorageAccountName&gt; определяет имя учетной записи хранения Azure. Обязательно использовать полное доменное имя (FQDN).
	
Если ни &lt;BlobStorageContainerName&gt;, ни &lt;StorageAccountName&gt; не заданы, то используется файловая система по умолчанию. Для файлов в файловой системе по умолчанию можно использовать относительный или абсолютный путь. Например, для ссылки на файл hadoop-mapreduce-examples.jar, который поставляется с кластерами HDInsight, можно использовать один из следующих вариантов:

	wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
	wasb:///example/jars/hadoop-mapreduce-examples.jar
	/example/jars/hadoop-mapreduce-examples.jar
	
> [AZURE.NOTE] Имя файла: <i>hadoop-examples.jar</i> в кластере HDInsight версии 1.6 и 2.1.


&lt;путь&gt; - это имя пути к файлу или каталогу HDFS. Поскольку контейнеры хранилища BLOB-объектов содержат данные типа "ключ-значение", подлинная иерархическая файловая система в них отсутствует. Символ "/" в ключе BLOB-объекта интерпретируется как разделитель каталогов. Например, имя BLOB-объекта для файла  *hadoop-mapreduce-examples.jar* выглядит следующим образом:

	example/jars/hadoop-mapreduce-examples.jar
	

## <a id="powershell"></a>Доступ к BLOB-объекту с помощью Azure PowerShell

Сведения об установке и настройке Azure PowerShell на рабочей станции см. в разделе [Установка и настройка Azure PowerShell][powershell-install]. Для выполнения командлетов PowerShell можно использовать окно консоли Azure PowerShell или PowerShell_ISE. 

Чтобы просмотреть список командлетов, связанных с BLOB-объектом, используйте следующую команду:

	Get-Command *blob*

![List of Blob-related PowerShell cmdlets.][img-hdi-powershell-blobcommands]


**Пример PowerShell для отправки файла**

См. [Передача данных в HDInsight][hdinsight-upload-data].

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

В следующем сценарии показано, как удалить файл.
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

**Пример доступа к неопределенной учетной записи хранения с помощью PowerShell**
	
В этом пример показано, как получить содержимое папки из учетной записи хранения, которая не была задана во время подготовки.
	$clusterName = "<HDInsightClusterName>"
	
	$undefinedStorageAccount = "<UnboundedStorageAccountUnderTheSameSubscription>"
	$undefinedContainer = "<UnboundedBlobContainerAssociatedWithTheStorageAccount>"
	
	$undefinedStorageKey = Get-AzureStorageKey $undefinedStorageAccount | %{ $_.Primary }
	
	Use-AzureHDInsightCluster $clusterName
	
	$defines = @{}
	$defines.Add("fs.azure.account.key.$undefinedStorageAccount.blob.core.windows.net", $undefinedStorageKey)

	Invoke-Hive -Defines $defines -Query "dfs -ls wasb://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"
 
## <a id="nextsteps"></a>Дальнейшие действия

Из этой статьи вы узнали, как использовать хранилище BLOB-объектов с HDInsight, а также тот факт, что это хранилища BLOB-объектов является основным компонентом HDInsight. Это позволяет создавать масштабируемые, долгосрочные решения для получения данных архивирования с помощью хранилища BLOB-объектов Azure, а также использовать HDInsight для разблокирования информации внутри хранимых данных.

Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Приступая к работе с Azure HDInsight][hdinsight-get-started]
* [Передача данных в HDInsight][hdinsight-upload-data]
* [Использование Hive с HDInsight][hdinsight-use-hive]
* [Использование Pig с HDInsight][hdinsight-use-pig]

[Powershell-install]: ../install-configure-powershell/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/

[Powershell-install]: ../install-configure-powershell/
[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]: ../storage-create-storage-account/

[img-hdi-powershell-blobcommands]: ./media/hdinsight-use-blob-storage/HDI.PowerShell.BlobCommands.png 
[img-hdi-quick-create]: ./media/hdinsight-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-use-blob-storage/HDI.CustomCreateStorageAccount.png  

<!--HONumber=42-->
