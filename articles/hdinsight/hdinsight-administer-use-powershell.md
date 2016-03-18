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
	ms.date="01/04/2016"
	ms.author="jgao"/>

# Управление кластерами Hadoop в HDInsight с помощью Azure PowerShell

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell — это полнофункциональная среда сценариев, которую можно использовать для контроля и автоматизации развертывания и управления вашей рабочей нагрузкой в Azure. В этой статье вы узнаете, как управлять кластерами Hadoop в Azure HDInsight с помощью локальной консоли Azure PowerShell, используя Windows PowerShell. Список командлетов HDInsight PowerShell см. в [Справочнике по командлетам HDInsight][hdinsight-powershell-reference].



**Предварительные требования**

Перед началом работы с этой статьей необходимо иметь следующее:

- **Подписка Azure.**. См. [Бесплатная пробная версия Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##См. статью "Установка Azure PowerShell 1.0 и более поздних версий".

Сначала необходимо удалить версии 0.9x.

Проверка версии установленной оболочки PowerShell:

	Get-Module *azure*
	
Для удаления старой версии запустите "Программы и компоненты" в панели управления.

Существует два основных варианта установки Azure PowerShell.

- [Коллекция PowerShell](https://www.powershellgallery.com/). Выполните следующие команды из интегрированной среды сценариев с повышенными привилегиями PowerShell или консоли Windows PowerShell с повышенными привилегиями:

		# Install the Azure Resource Manager modules from PowerShell Gallery
		Install-Module AzureRM
		Install-AzureRM
		
		# Install the Azure Service Management module from PowerShell Gallery
		Install-Module Azure
		
		# Import AzureRM modules for the given version manifest in the AzureRM module
		Import-AzureRM
		
		# Import Azure Service Management module
		Import-Module Azure

	Дополнительные сведения см. в статье [Коллекция PowerShell](https://www.powershellgallery.com/).

- [Установщик веб-платформы Майкрософт (WebPI)](http://aka.ms/webpi-azps). Если вы установили Azure PowerShell 0.9.x, появится запрос для удаления версии 0.9.x. Если вы установили модули Azure PowerShell из коллекции PowerShell, установщик требует удалить эти модули перед установкой, чтобы обеспечить целостность среды PowerShell Azure. Инструкции см. в разделе [Установка Azure PowerShell 1.0 с помощью установщика веб-платформы](https://azure.microsoft.com/blog/azps-1-0/).

Обновления для установщика веб-платформы будут выпускаться ежемесячно. Обновления для коллекции PowerShell будут выпускаться на постоянной основе. Если вы решите установить коллекцию PowerShell, она станет основным источником всего нового и лучшего в Azure PowerShell.

##Создание кластеров

Кластеру HDInsight требуется группа ресурсов Azure и контейнер больших двоичных объектов в учетной записи хранения Azure:

- Группа ресурсов Azure — это логический контейнер для ресурсов Azure. Группа ресурсов Azure и кластер HDInsight не обязательно должны находиться в одном расположении. Дополнительные сведения см. в статье [Использование Azure PowerShell с диспетчером ресурсов Azure](../powershell-azure-resource-manager.md).
- HDInsight использует контейнер хранилища больших двоичных объектов хранилища Azure в качестве файловой системы по умолчанию. Для создания кластера HDInsight требуются учетная запись хранения Azure и контейнер хранилища. Учетная запись хранения, используемая по умолчанию, должна находиться в том же расположении, что и кластер HDInsight.

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**Подключение к Azure**

	Login-AzureRmAccount
	Get-AzureRmSubscription  # list your subscriptions and get your subscription ID
	Select-AzureRmSubscription -SubscriptionId "<Your Azure Subscription ID>"

**Select-AzureRMSubscription** вызывается, если у вас есть несколько подписок Azure.
	
**Создание новой группы ресурсов**

	New-AzureRmResourceGroup -name <New Azure Resource Group Name> -Location "<Azure Location>"  # For example, "EAST US 2"

**Создание учетной записи хранения Azure**

	New-AzureRmStorageAccount -ResourceGroupName <Azure Resource Group Name> -Name <Azure Storage Account Name> -Location "<Azure Location>" -Type <AccountType> # account type example: Standard_LRS for zero redundancy storage
	
	Don't use **Standard_ZRS** because it deson't support Azure Table.  HDInsight uses Azure Table to logging. For a full list of the storage account types, see [https://msdn.microsoft.com/library/azure/hh264518.aspx](https://msdn.microsoft.com/library/azure/hh264518.aspx).

[AZURE.INCLUDE [список центров обработки данных](../../includes/hdinsight-pricing-data-centers-clusters.md)]


Сведения о создании новой учетной записи хранения Azure с помощью портала Azure см. в статье [Об учетных записях хранения Azure](../storage/storage-create-storage-account.md).

Если у вас уже есть учетная запись хранения, но вы не знаете имени и ключа учетной записи, можно использовать следующие команды для получения нужных сведений:

	# List Storage accounts for the current subscription
	Get-AzureRmStorageAccount
	# List the keys for a Storage account
	Get-AzureRmStorageAccountKey -ResourceGroupName <Azure Resource Group Name> -name $storageAccountName <Azure Storage Account Name>

Дополнительные сведения о получении данных с помощью портала см. в разделе "Просмотр, копирование и повторное создание ключей доступа к хранилищу" статьи [Об учетных записях хранения Azure](../storage/storage-create-storage-account.md).

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

>[AZURE.NOTE] Поддерживаются только кластеры HDInsight версии 3.1.3 или более поздней. Если вы не знаете версию кластера, см. страницу «Свойства». См. раздел [Знакомство с интерфейсом портала кластера](hdinsight-adminster-use-management-portal/#Get-familiar-with-the-cluster-portal-interface).

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
	

##Предоставление и отмена доступа

В кластерах HDInsight имеются следующие веб-службы HTTP (все эти службы имеют конечные точки RESTful):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


По умолчанию эти службы предоставляются для доступа. Вы можете отменить или предоставить доступ. Для отмены:

	Revoke-AzureRmHDInsightHttpServicesAccess -ClusterName <Cluster Name>

Для предоставления:

	$clusterName = "<HDInsight Cluster Name>"

	# Credential option 1
	$hadoopUserName = "admin"
	$hadoopUserPassword = "<Enter the Password>"
	$hadoopUserPW = ConvertTo-SecureString -String $hadoopUserPassword -AsPlainText -Force
	$credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$hadoopUserPW)

	# Credential option 2
	#$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"
	
	Grant-AzureRmHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential

>[AZURE.NOTE] Предоставляя или отменяя доступ, вы сбрасываете имя пользователя и пароль кластера.

Это также можно сделать через портал. См. статью [Администрирование HDInsight с помощью портала Azure][hdinsight-admin-portal].

##Обновление учетных данных пользователя HTTP

Эта процедура аналогична [предоставлению или запрету доступа HTTP](#grant/revoke-access). Если кластеру был предоставлен доступ по протоколу HTTP, необходимо сначала отменить его. После этого предоставьте доступ с новыми учетными данными пользователя HTTP.


##Поиск учетной записи хранения по умолчанию

В следующем сценарии Powershell показано получение имени учетной записи хранения по умолчанию и ключа учетной записи хранения по умолчанию для кластера.

	$clusterName = "<HDInsight Cluster Name>"
	
	$cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
	$resourceGroupName = $cluster.ResourceGroup
	$defaultStorageAccountName = ($cluster.DefaultStorageAccount).Replace(".blob.core.windows.net", "")
	$defaultBlobContainerName = $cluster.DefaultStorageContainer
	$defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName |  %{ $_.Key1 }
	$defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey 

##Поиск группы ресурсов

В режиме ARM каждый кластер HDInsight относится к группе ресурсов Azure. Поиск группы ресурсов:

	$clusterName = "<HDInsight Cluster Name>"
	
	$cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
	$resourceGroupName = $cluster.ResourceGroup


##Отправка заданий

**Отправка заданий MapReduce**

См. статью [Запуск примеров выполнения Hadoop MapReduce в HDInsight под управлением Windows](hdinsight-run-samples.md).

**Отправка заданий Hive**

См. статью [Выполнение запросов Hive с помощью PowerShell](hdinsight-hadoop-use-hive-powershell.md).

**Отправка заданий Pig**

См. статью [Выполнение заданий Pig с помощью PowerShell](hdinsight-hadoop-use-pig-powershell.md).

**Отправка заданий Sqoop**

См. раздел [Использование Sqoop с HDInsight](hdinsight-use-sqoop.md).

**Отправка заданий Oozie**

См. статью [Использование Oozie с Hadoop для определения и выполнения рабочего процесса в HDInsight](hdinsight-use-oozie.md).

##Отправка данных в хранилище BLOB-объектов Azure
См. раздел [Отправка данных в HDInsight][hdinsight-upload-data].


## См. также
* [Справочная документация по командлетам HDInsight][hdinsight-powershell-reference]
* [Администрирование HDInsight с помощью портала Azure][hdinsight-admin-portal]
* [Администрирование HDInsight с помощью интерфейса командной строки][hdinsight-admin-cli]
* [Создание кластеров HDInsight][hdinsight-provision]
* [Отправка данных в HDInsight][hdinsight-upload-data]
* [Отправка заданий Hadoop в HDInsight][hdinsight-submit-jobs]
* [Начало работы с Azure HDInsight][hdinsight-get-started]


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-provision-custom-options]: hdinsight-provision-clusters.md#configuration
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: powershell-install-configure.md

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png

<!---HONumber=AcomDC_0218_2016-->