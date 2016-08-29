.<properties
	pageTitle="Управление кластерами Hadoop в HDInsight с помощью PowerShell | Microsoft Azure"
	description="Узнайте, как осуществлять управление кластерами Hadoop в HDInsight с использованием Azure PowerShell."
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	tags="azure-portal"
	authors="mumian"
	documentationCenter=""/>

.<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/10/2016"
	ms.author="jgao"/>

# Управление кластерами Hadoop в HDInsight с помощью Azure PowerShell

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell — это полнофункциональная среда сценариев, которую можно использовать для контроля и автоматизации развертывания и управления вашей рабочей нагрузкой в Azure. В этой статье вы узнаете, как управлять кластерами Hadoop в Azure HDInsight с помощью локальной консоли Azure PowerShell, используя Windows PowerShell. Список командлетов HDInsight PowerShell см. в [Справочнике по командлетам HDInsight][hdinsight-powershell-reference].



**Предварительные требования**

Перед началом работы с этой статьей необходимо иметь следующее:

- **Подписка Azure.**. См. [Бесплатная пробная версия Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##Установка Azure PowerShell

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

Если вы установили Azure PowerShell версии 0.9x, то перед установкой новой версии ее необходимо удалить.

Проверка версии установленной оболочки PowerShell:

	Get-Module *azure*
	
Для удаления старой версии запустите "Программы и компоненты" в панели управления.


##Создание кластеров

Ознакомьтесь с разделом [Создание кластеров под управлением Linux в HDInsight с помощью Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md).

##Получение списка кластеров
Чтобы получить список всех кластеров в текущей подписке, используйте следующую команду:

	Get-AzureRmHDInsightCluster

##Отображение кластеров

Чтобы отобразить сведения о конкретном кластере в текущей подписке, используйте следующую команду:

	Get-AzureRmHDInsightCluster -ClusterName <Cluster Name>

##Удаление кластеров

Используйте следующую команду для удаления кластера:

	Remove-AzureRmHDInsightCluster -ClusterName <Cluster Name>

Можно также удалить кластер, удалив группу ресурсов, которая содержит этот кластер. Обратите внимание, это приведет к удалению всех ресурсов в группе, включая учетную запись хранения по умолчанию.

	Remove-AzureRmResourceGroup -Name <Resource Group Name>
			
##Масштабирование кластеров
Масштабирование кластера позволяет изменить количество рабочих узлов в кластере, который работает под управлением Azure HDInsight. При этом не требуется повторно создавать кластер.

>[AZURE.NOTE] Поддерживаются только кластеры HDInsight версии 3.1.3 или более поздней. Если вы не знаете версию кластера, см. страницу «Свойства». См. раздел [Отображение кластеров](hdinsight-administer-use-portal-linux.md#list-and-show-clusters).

Ниже представлены возможности, связанные с изменением количества узлов данных в кластере каждого типа, поддерживаемого в HDInsight.

- Hadoop

	Вы можете легко увеличить количество рабочих узлов в работающем кластере Hadoop. Это не помешает обработке заданий в состоянии ожидания и выполнения. В ходе выполнения операции можно также отправлять новые задания. Сбои операции масштабирования обрабатываются корректно, поэтому кластер всегда пребывает в функциональном состоянии.

	Если уменьшить масштаб кластера Hadoop, сократив количество узлов данных, некоторые службы в нем будут перезапущены. Это приведет к сбою всех выполняющихся и ожидающих заданий при завершении операции масштабирования. Однако после завершения операции вы можете повторно отправить задания.

- HBase

	Вы можете с легкостью добавлять и удалять узлы данных в работающем кластере HBase. Балансировка региональных серверов выполняется автоматически в течение нескольких минут после завершения операции масштабирования. Но их также можно сбалансировать вручную, выполнив вход в головной узел кластера и выполнив следующие команды в окне командной строки:

		>pushd %HBASE_HOME%\bin
		>hbase shell
		>balancer

- Storm

	Вы можете с легкостью добавлять и удалять узлы данных в работающем кластере Storm. Но после успешного завершения операции масштабирования потребуется повторная балансировка топологии.

	Повторную балансировку можно выполнить двумя способами:

	* с помощью веб-интерфейса Storm;
	* с помощью программы командной строки.

	Дополнительную информацию см. в [документации по Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

	В кластере HDInsight доступен веб-интерфейс Storm.

	![HDInsight, storm, масштабирование, перераспределение](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

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

Это также можно сделать через портал. См. раздел [Администрирование HDInsight с помощью портала Azure][hdinsight-admin-portal]

##Обновление учетных данных пользователя HTTP

Эта процедура аналогична [предоставлению или запрету доступа HTTP](#grant/revoke-access). Если кластеру был предоставлен доступ по протоколу HTTP, необходимо сначала отменить его. После этого предоставьте доступ с новыми учетными данными пользователя HTTP.


##Поиск учетной записи хранения по умолчанию

В следующем сценарии Powershell показано получение имени учетной записи хранения по умолчанию и ключа учетной записи хранения по умолчанию для кластера.

	$clusterName = "<HDInsight Cluster Name>"
	
	$cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
	$resourceGroupName = $cluster.ResourceGroup
	$defaultStorageAccountName = ($cluster.DefaultStorageAccount).Replace(".blob.core.windows.net", "")
	$defaultBlobContainerName = $cluster.DefaultStorageContainer
	$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
	$defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey 

##Поиск группы ресурсов

В режиме Resource Manager каждый кластер HDInsight относится к группе ресурсов Azure. Поиск группы ресурсов:

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

<!---HONumber=AcomDC_0817_2016-->