<properties
	pageTitle="Управление кластерами Hadoop в HDInsight с помощью PowerShell | Microsoft Azure"
	description="Узнайте, как осуществлять управление кластерами Hadoop в HDInsight с использованием Azure PowerShell."
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	tags="azure-portal"
	authors="mumian"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/04/2015"
	ms.author="jgao"/>

# Управление кластерами Hadoop в HDInsight с помощью Azure PowerShell

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell — это полнофункциональная среда сценариев, которую можно использовать для контроля и автоматизации развертывания и управления вашей рабочей нагрузкой в Azure. В этой статье вы узнаете, как управлять кластерами Hadoop в Azure HDInsight с помощью локальной консоли Azure PowerShell, используя Windows PowerShell. Список командлетов HDInsight PowerShell см. в [Справочнике по командлетам HDInsight][hdinsight-powershell-reference].



**Предварительные требования**

Перед началом работы с этой статьей необходимо иметь следующее:

- **Подписка Azure.**. См. [Бесплатная пробная версия Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Рабочая станция с Azure PowerShell.**. См. раздел [Установка и использование Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

	> [AZURE.NOTE]В сценариях PowerShell, приведенных в данной статье, используется режим диспетчера ресурсов Azure. Чтобы воспроизвести эти примеры, загрузите последнюю версию Azure PowerShell с помощью установщика веб-платформы Майкрософт.

##Создание кластеров

Кластеру HDInsight требуется группа ресурсов Azure и контейнер больших двоичных объектов в учетной записи хранения Azure:

- Группа ресурсов Azure — это логический контейнер для ресурсов Azure. Группа ресурсов Azure и кластер HDInsight не обязательно должны находиться в одном расположении. Дополнительные сведения см. в статье [Использование Azure PowerShell с диспетчером ресурсов Azure](powershell-azure-resource-manager.md).
- HDInsight использует контейнер хранилища больших двоичных объектов хранилища Azure в качестве файловой системы по умолчанию. Для создания кластера HDInsight требуются учетная запись хранения Azure и контейнер хранилища. Учетная запись хранения, используемая по умолчанию, должна находиться в том же расположении, что и кластер HDInsight.

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**Подключение к Azure**

		Login-AzureRmAccount
		Get-AzureRmSubscription  # list your subscriptions and get your subscription ID
		Select-AzureRmSubscription -SubscriptionId "<Your Azure Subscription ID>"

	**Select-AzureRMSubscription** is called in case you have multiple Azure subscriptions.
	
**Создание новой группы ресурсов**

	New-AzureRmResourceGroup -name <New Azure Resource Group Name> -Location "<Azure Location>"  # For example, "EAST US 2"

**Создание учетной записи хранения Azure**

	New-AzureRmStorageAccount -ResourceGroupName <Azure Resource Group Name> -Name <Azure Storage Account Name> -Location "<Azure Location>" -Type <AccountType> # account type example: Standard_LRS for zero redundancy storage
	
	Don't use **Standard_ZRS** because it deson't support Azure Table.  HDInsight uses Azure Table to logging. For a full list of the storage account types, see [https://msdn.microsoft.com/library/azure/hh264518.aspx](https://msdn.microsoft.com/library/azure/hh264518.aspx).

[AZURE.INCLUDE [список центров обработки данных](../../includes/hdinsight-pricing-data-centers-clusters.md)]


Сведения о создании новой учетной записи хранения Azure с помощью портала предварительной версии Azure см. в статье [Об учетных записях хранения Azure](storage-create-storage-account.md).

Если у вас уже есть учетная запись хранения, но вы не знаете имени и ключа учетной записи, можно использовать следующие команды для получения нужных сведений:

	# List Storage accounts for the current subscription
	Get-AzureRmStorageAccount
	# List the keys for a Storage account
	Get-AzureRmStorageAccountKey -ResourceGroupName <Azure Resource Group Name> -name $storageAccountName <Azure Storage Account Name>

Дополнительные сведения о получении данных с помощью портала предварительной версии см. в разделе "Просмотр, копирование и повторное создание ключей доступа к хранилищу" статьи [Об учетных записях хранения Azure](storage-create-storage-account.md).

**Создание контейнера хранилища Azure**

Azure PowerShell не может создать контейнер больших двоичных объектов в процессе создания HDInsight. Его можно создать с помощью следующего скрипта:

	$resourceGroupName = "<AzureResoureGroupName>"
	$storageAccountName = "<Azure Storage Account Name>"
	$storageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount |  %{ $_.Key1 }
	$containerName="<AzureBlobContainerName>"

	# Create a storage context object
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext

**Создание кластера**

После создания учетной записи хранения и подготовки контейнера больших двоичных объектов все готово к созданию кластера.

	$resourceGroupName = "<AzureResoureGroupName>"

	$storageAccountName = "<Azure Storage Account Name>"
	$containerName = "<AzureBlobContainerName>"

	$clusterName = "<HDInsightClusterName>"
	$location = "<AzureDataCenter>"
	$clusterNodes = <ClusterSizeInNodes>

	# Get the Storage account key
	$storageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName | %{ $_.Key1 }

	# Create a new HDInsight cluster
	New-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName `
		-ClusterName $clusterName `
		-Location $location `
		-DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
		-DefaultStorageAccountKey $storageAccountKey `
		-DefaultStorageContainer $containerName  `
		-ClusterSizeInNodes $clusterNodes

##Получение списка кластеров
Чтобы получить список всех кластеров в текущей подписке, используйте следующую команду:

	Get-AzureRmHDInsightCluster

##Отображение кластеров

Чтобы отобразить сведения о конкретном кластере в текущей подписке, используйте следующую команду:

	Get-AzureRmHDInsightCluster -ClusterName <Cluster Name>

##Удаление кластеров
Используйте следующую команду для удаления кластера:

	Remove-AzureRmHDInsightCluster -ClusterName <Cluster Name>

##Масштабирование кластеров
Масштабирование кластера позволяет изменить количество рабочих узлов в кластере, который работает под управлением Azure HDInsight. При этом не требуется повторно создавать кластер.

>[AZURE.NOTE]Поддерживаются только кластеры HDInsight версии 3.1.3 или более поздней. Если вы не знаете версию кластера, см. страницу «Свойства». См. раздел [Знакомство с интерфейсом портала кластера](hdinsight-adminster-use-management-portal/#Get-familiar-with-the-cluster-portal-interface).

Ниже представлены возможности, связанные с изменением количества узлов данных в кластере каждого типа, поддерживаемого в HDInsight.

- Hadoop

	Вы можете легко увеличить количество рабочих узлов в работающем кластере Hadoop. Это не помешает обработке заданий в состоянии ожидания и выполнения. В ходе выполнения операции можно также отправлять новые задания. Сбои операции масштабирования обрабатываются корректно, поэтому кластер всегда пребывает в функциональном состоянии.

	Если уменьшить масштаб кластера Hadoop, сократив количество узлов данных, некоторые службы в нем будут перезапущены. Это приведет к сбою всех выполняющихся и ожидающих заданий при завершении операции масштабирования. Однако после завершения операции вы можете повторно отправить задания.

- HBase

	Вы можете с легкостью добавлять и удалять узлы данных в работающем кластере HBase. Балансировка региональных серверов выполняется автоматически в течение нескольких минут после завершения операции масштабирования. Но их также можно сбалансировать вручную, выполнив вход в головной узел кластера и выполнив следующие команды в окне командной строки:

		>pushd %HBASE_HOME%\bin
		>hbase shell
		>balancer

	Дополнительные сведения об использовании оболочки HBase см.
- Storm

	Вы можете с легкостью добавлять и удалять узлы данных в работающем кластере Storm. Но после успешного завершения операции масштабирования потребуется повторная балансировка топологии.

	Повторную балансировку можно выполнить двумя способами:

	* с помощью веб-интерфейса Storm;
	* с помощью программы командной строки.

	Дополнительную информацию см. в [документации по Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

	В кластере HDInsight доступен веб-интерфейс Storm.

	![hdinsight, storm, масштабирование, перераспределение](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

	Ниже приведен пример использования команды CLI для повторной балансировки топологии Storm:

		## Reconfigure the topology "mytopology" to use 5 worker processes,
		## the spout "blue-spout" to use 3 executors, and
		## the bolt "yellow-bolt" to use 10 executors

		$ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

Чтобы изменить размер кластера Hadoop с помощью Azure PowerShell, выполните следующую команду с клиентского компьютера:

	Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
	
##Поиск группы ресурсов

	$clusterName = "<HDInsight Cluster Name>"
	
	$cluster = Get-AzureRmHDInsightCluster  -ClusterName $clusterName
	$resourceGroupName = $cluster.ResourceGroup


##Поиск учетной записи хранения по умолчанию

В следующем сценарии Powershell показано получение имени учетной записи хранения по умолчанию и ключа учетной записи хранения по умолчанию для кластера.


	$clusterName = "<HDInsight Cluster Name>"
	
	$cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
	$resourceGroupName = $cluster.ResourceGroup
	$defaultStorageAccountName = ($cluster.DefaultStorageAccount).Replace(".blob.core.windows.net", "")
	$defaultBlobContainerName = $cluster.DefaultStorageContainer
	$defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName |  %{ $_.Key1 }
	$defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey 


##Предоставление и отмена доступа

В кластерах HDInsight имеются следующие веб-службы HTTP (все эти службы имеют конечные точки RESTful):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


По умолчанию эти службы предоставляются для доступа. Вы можете отменить или предоставить доступ. Пример:

	Revoke-AzureHDInsightHttpServicesAccess -ClusterName <Cluster Name>

>[AZURE.NOTE]Предоставляя или отменяя доступ, вы сбрасываете имя пользователя и пароль кластера.

Это также можно сделать с помощью портала предварительной версии. См. раздел [Администрирование HDInsight с помощью портала предварительной версии Azure][hdinsight-admin-portal].





##Отправка заданий MapReduce
Кластер HDInsight поставляется с некоторыми примерами MapReduce. Один из примеров предназначен для подсчета частотности слов в исходных файлах.

**Отправка задания MapReduce**

Следующий скрипт PowerShell отправляет пример задания для подсчета слов:

	$clusterName = "<HDInsightClusterName>"

	# Define the MapReduce job
	$wordCountJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
								-JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" `
								-ClassName "wordcount" `
								-Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput1"
	
	# Submit the job and wait for job completion
	$cred = Get-Credential -Message "Enter the HDInsight cluster HTTP user credential:" 
	$wordCountJob = Start-AzureRmHDInsightJob `
						-ResourceGroupName $resourceGroupName `
						-ClusterName $clusterName `
						-HttpCredential $cred `
						-JobDefinition $wordCountJobDefinition 
	
	Wait-AzureRmHDInsightJob `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $clusterName `
		-HttpCredential $cred `
		-JobId $wordCountJob.JobId 

	# Get the job output
	$cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
	$defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
	$defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount |  %{ $_.Key1 }
	$defaultStorageContainer = $cluster.DefaultStorageContainer
	
	Get-AzureRmHDInsightJobOutput `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $clusterName `
		-HttpCredential $cred `
		-DefaultStorageAccountName $defaultStorageAccount `
		-DefaultStorageAccountKey $defaultStorageAccountKey `
		-DefaultContainer $defaultStorageContainer  `
		-JobId $wordCountJob.JobId `
		-DisplayOutputType StandardError
		
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
* [Администрирование HDInsight с помощью портала предварительной версии Azure][hdinsight-admin-portal]
* [Администрирование HDInsight с помощью интерфейса командной строки][hdinsight-admin-cli]
* [Создание кластеров HDInsight][hdinsight-provision]
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

<!---HONumber=Nov15_HO2-->