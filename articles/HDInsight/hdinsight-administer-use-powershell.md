<properties 
	pageTitle="Управление кластерами Hadoop в HDInsight с помощью PowerShell | Microsoft Azure" 
	description="Узнайте, как осуществлять управление кластерами Hadoop в HDInsight с использованием Azure PowerShell." 
	services="hdinsight" 
	editor="cgronlun" 
	manager="paulettm" 
	authors="mumian" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="jgao"/>

# Управление кластерами Hadoop в HDInsight с помощью Azure PowerShell


Azure PowerShell — это полнофункциональная среда сценариев, которую можно использовать для контроля и автоматизации развертывания и управления вашей рабочей нагрузкой в Azure. В этой статье вы узнаете, как управлять кластерами Hadoop в Azure HDInsight с помощью локальной консоли Azure PowerShell, используя Windows PowerShell. Список командлетов HDInsight PowerShell см. в [Справочнике по командлетам HDInsight][hdinsight-powershell-reference].

##Предварительные требования

Перед началом работы с этой статьей необходимо иметь следующее:

- **Подписка Azure.**. См. [Бесплатная пробная версия Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Рабочая станция с Azure PowerShell**. См. [Установка и использование Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).


##Подготовка кластеров HDInsight к работе
HDInsight использует контейнер хранилища BLOB-объектов Azure в качестве файловой системы по умолчанию. Для создания кластера HDInsight требуются учетная запись хранения Azure и контейнер хранилища.

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**Создание учетной записи хранения Azure**

После импорта файла publishsettings создайте учетную запись хранения с помощью следующей команды:

	# Create an Azure Storage account
	$storageAccountName = "<StorageAcccountName>"
	$location = "<Microsoft data center>"           # For example, "West US"

	New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location


[AZURE.INCLUDE [список центров обработки данных](../../includes/hdinsight-pricing-data-centers-clusters.md)]


Сведения о создании новой учетной записи хранения Azure с помощью портала Azure см. в статье [Создание и удаление учетной записи хранения, а также управление ею](../storage-create-storage-account/).

Если у вас уже есть учетная запись хранения, но вы не знаете имени и ключа учетной записи, можно использовать следующие команды для получения нужных сведений:

	# List Storage accounts for the current subscription
	Get-AzureStorageAccount
	# List the keys for a Storage account
	Get-AzureStorageKey <StorageAccountName>

Дополнительные сведения о получении данных с помощью портала Azure см. в разделе «Просмотр, копирование и повторное создание ключей доступа к хранилищу» статьи [Создание и удаление учетной записи хранения, а также управление ею](../storage-create-storage-account/).

**Создание контейнера хранилища Azure**

Azure PowerShell не может создать контейнер больших двоичных объектов в процессе подготовки HDInsight. Его можно создать с помощью следующего скрипта:

	$storageAccountName = "<StorageAccountName>"
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$containerName="<ContainerName>"

	# Create a storage context object
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext

**Подготовка кластера**

После создания учетной записи хранения и подготовки контейнера больших двоичных объектов все готово к созданию кластера.
		
	$storageAccountName = "<StorageAccountName>"
	$containerName = "<ContainerName>"

	$clusterName = "<HDInsightClusterName>"
	$location = "<MicrosoftDataCenter>"
	$clusterNodes = <ClusterSizeInNodes>

	# Get the Storage account key
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

	# Create a new HDInsight cluster
	New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes


На следующем снимке экрана показано выполнение сценария:

![HDI.PS.Provision][image-hdi-ps-provision]




##Список сведений о кластере
Чтобы получить список всех кластеров в текущей подписке, используйте следующую команду:

	Get-AzureHDInsightCluster 

Чтобы отобразить сведения о конкретном кластере в текущей подписке, используйте следующую команду:

	Get-AzureHDInsightCluster -Name <ClusterName> 

##Удаление кластеров
Используйте следующую команду для удаления кластера:

	Remove-AzureHDInsightCluster -Name <ClusterName> 



##Предоставление и отмена доступа к службам HTTP

В кластерах HDInsight имеются следующие веб-службы HTTP (все эти службы имеют конечные точки RESTful):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


По умолчанию эти службы предоставляются для доступа. Вы можете отменить или предоставить доступ. Пример:

	Revoke-AzureHDInsightHttpServicesAccess -Name hdiv2 -Location "East US"

В этом примере <i>hdiv2</i> — это имя кластера HDInsight.

>[AZURE.NOTE]Предоставляя или отменяя доступ, вы сбрасываете имя пользователя и пароль кластера.

Это также можно сделать через портал Azure. См. раздел [Администрирование HDInsight с помощью портала Azure][hdinsight-admin-portal]

##Масштабирование кластеров
См. раздел [Масштабирование кластеров Hadoop в HDInsight](hdinsight-hadoop-cluster-scaling.md)

##Отправка заданий MapReduce
Кластер HDInsight поставляется с некоторыми примерами MapReduce. Один из примеров предназначен для подсчета частотности слов в исходных файлах.

**Отправка задания MapReduce**

Следующий скрипт PowerShell отправляет пример задания для подсчета слов:
	
	$clusterName = "<HDInsightClusterName>"            
	
	# Define the MapReduce job
	$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"
	
	# Run the job and show the standard error 
	$wordCountJobDefinition | Start-AzureHDInsightJob -Cluster $clusterName | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | %{ Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $_.JobId -StandardError}
	
Сведения о префиксе **wasb** см. в разделе [Использование хранилища больших двоичных объектов Azure для HDInsight][hdinsight-storage]

**Загрузка выходных данных задания MapReduce**

Следующий скрипт Azure PowerShell возвращает выходные данные задания MapReduce из последней процедуры:

	$storageAccountName = "<StorageAccountName>"   
	$containerName = "<ContainerName>"             
		
	# Create the Storage account context object
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	# Download the output to local computer
	Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
	
	# Display the output
	cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

Дополнительные сведения о разработке и выполнении заданий MapReduce см. в разделе [Использование MapReduce с HDInsight][hdinsight-use-mapreduce].






































##Отправка заданий Hive
Кластер HDInsight поставляется с примером таблицы Hive, которая называется *hivesampletable*. Для получения списка таблиц Hive в кластере можно использовать команду HiveQL **SHOW TABLES**.

**Отправка задания Hive**

Следующий скрипт отправляет задание Hive в таблицы Hive:
	
	$clusterName = "<HDInsightClusterName>"               
	
	# HiveQL query
	$querystring = @"
		SHOW TABLES;
		SELECT * FROM hivesampletable 
			WHERE Country='United Kingdom'
			LIMIT 10;
	"@

	Use-AzureHDInsightCluster -Name $clusterName
	Invoke-Hive $querystring

Задание Hive сначала отобразит таблицы Hive, созданные в кластере, и данные, возвращенные из таблицы hivesampletable.

Дополнительные сведения об использовании Hive см. в разделе [Использование Hive с HDInsight][hdinsight-use-hive].


##Отправка данных в хранилище BLOB-объектов Azure
См. раздел [Отправка данных в HDInsight][hdinsight-upload-data].

##Загрузка выходных данных задания из хранилища больших двоичных объектов Azure
См. раздел [Отправка заданий MapReduce](#mapreduce) в этой статье.

## См. также
* [Справочная документация по командлетам HDInsight][hdinsight-powershell-reference]
* [Администрирование HDInsight с помощью портала Azure][hdinsight-admin-portal]
* [Администрирование HDInsight с помощью интерфейса командной строки][hdinsight-admin-cli]
* [Подготовка кластеров HDInsight к работе][hdinsight-provision]
* [Отправка данных в HDInsight][hdinsight-upload-data]
* [Отправка заданий Hadoop в HDInsight][hdinsight-submit-jobs]
* [Начало работы с Azure HDInsight][hdinsight-get-started]


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-provision-custom-options]: hdinsight-provision-clusters.md#configuration
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png


 

<!---HONumber=62-->