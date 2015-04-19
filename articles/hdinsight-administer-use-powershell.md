<properties 
	pageTitle="Управление кластерами Hadoop в HDInsight с использованием Azure PowerShell | Azure" 
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
	ms.date="11/21/2014" 
	ms.author="jgao"/>

# Управление кластерами Hadoop в HDInsight с использованием Azure PowerShell

Azure PowerShell - это полнофункциональная среда сценариев, которую можно использовать для контроля и автоматизации развертывания рабочих нагрузок, а также управления ими в Azure. В этой статье вы узнаете, как управлять кластерами Hadoop в HDInsight с помощью локальной консоли Azure PowerShell, используя Windows PowerShell. Список командлетов HDInsight PowerShell см. в [справочнике по командлетам HDInsight][hdinsight-powershell-reference].

**Предварительные требования:**

Перед началом работы с этой статьей необходимо иметь следующее:

- Подписка Azure. Azure - это платформа на основе подписок. Командлеты HDInsight PowerShell предназначены для выполнения задач по подписке. Дополнительные сведения о получении подписки см. в разделах [Варианты приобретения][azure-purchase-options], [Предложения для участников][azure-member-offers] или [Бесплатное пробное использование][azure-free-trial].

- Рабочая станция с Azure PowerShell. Инструкции см. в разделе [Установка и настройка Azure PowerShell][Powershell-install-configure].

			
	

## Содержание

* [Подготовка кластера к работе](#provision)
* [Отображение кластеров](#listshow)
* [Удаление кластера](#delete)
* [Предоставление и отмена доступа к службам HTTP](#httpservices)
* [Отправка заданий MapReduce](#mapreduce)
* [Отправка заданий Hive](#hive)
* [Отправка данных в хранилище BLOB-объектов](#upload)
* [Скачивание выходных данных MapReduce из хранилища BLOB-объектов](#download)


##<a id="provision"></a> Подготовка кластера HDInsight
HDInsight использует контейнер хранилища BLOB-объектов Azure в качестве файловой системы по умолчанию. Перед созданием кластера HDInsight требуются учетная запись хранения Azure и контейнер хранилища. 

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**Создание учетной записи хранения Azure**

После импорта файла publishsettings используйте следующую команду для создания учетной записи хранения:

	# Create an Azure storage account
	$storageAccountName = "<StorageAcccountName>"
	$location = "<Microsoft data center>"           # For example, "West US"

	New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

> [AZURE.NOTE] Учетная запись хранения должна находиться в том же центре обработки данных, что и кластер HDInsight. В настоящее время можно подготовить только кластеры HDInsight в следующих центрах обработки данных:

><ul>
<li>Юго-Восточная Азия</li>
<li>Северная Европа</li>
<li>Западная Европа</li>
<li>Восток США</li>
<li>Запад США</li>
</ul>



Информацию о создании новой учетной записи хранения Azure с помощью портала управления см. в разделе [Создание и удаление учетной записи хранения, а также управление ей](storage-create-storage-account.md).

Если у вас уже есть учетная запись хранения, но вы не знаете имени и ключа учетной записи, можно использовать следующие команды для получения нужных сведений:

	# List storage accounts for the current subscription
	Get-AzureStorageAccount
	# List the keys for a storage account
	Get-AzureStorageKey <StorageAccountName>

Подробную информацию о получении сведений с использованием портала управления см. в подразделе *Практическое руководство. Просмотр, копирование и повторное создание ключей доступа к хранилищу* раздела [Создание и удаление учетной записи хранения, а также управление ей](storage-create-storage-account.md).

**Создание контейнера хранилища Azure**

PowerShell не может создать контейнер BLOB-объектов в процессе подготовки HDInsight. Его можно создать с помощью следующего сценария:

	$storageAccountName = "<StorageAccountName>"
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$containerName="<ContainerName>"

	# Create a storage context object
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName 
	                                       -StorageAccountKey $storageAccountKey  
	
	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext

**Подготовка кластера к работе**

После создания учетной записи хранения и подготовки контейнера BLOB-объектов все готово к созданию кластера.    
		
	$storageAccountName = "<StorageAccountName>"
	$containerName = "<ContainerName>"

	$clusterName = "<HDInsightClusterName>"
	$location = "<MicrosoftDataCenter>"
	$clusterNodes = <ClusterSizeInNodes>

	# Get the storage account key
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

	# Create a new HDInsight cluster
	New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes


На следующем снимке экрана показано выполнение сценария:

![HDI.PS.Provision][image-hdi-ps-provision]




##<a id="listshow"></a> Отображение сведений о кластере
Используйте следующие команды для отображения сведений о кластере:

**Получение списка всех кластеров в текущей подписке**

	Get-AzureHDInsightCluster 

**Отображение данных о конкретном кластере в текущей подписке**

	Get-AzureHDInsightCluster -Name <ClusterName> 

##<a id="delete"></a> Удаление кластера
Используйте следующую команду для удаления кластера:

	Remove-AzureHDInsightCluster -Name <ClusterName> 

##<a id="httpservice"></a> Предоставление и отмена доступа к службам HTTP

В кластерах HDInsight имеются следующие веб-службы HTTP (все эти службы имеют конечные точки RESTful):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


По умолчанию эти службы предоставляются для доступа. Вы можете отменить или предоставить доступ.  Пример:

	Revoke-AzureHDInsightHttpServicesAccess -Name hdiv2 -Location "East US"

В этом примере <i>hdiv2</i> - это имя вашего кластера HDInsight.

>[AZURE.NOTE] Предоставляя или отменяя доступ, вы сбрасываете имя пользователя и пароль кластера.

Это можно сделать также с помощью портала управления Windows Azure. См. раздел [Администрирование кластеров HDInsight с использованием портала управления][hdinsight-admin-portal].

##<a id="mapreduce"></a> Отправка заданий MapReduce
Кластер HDInsight поставляется с некоторыми примерами MapReduce. Один из примеров предназначен для подсчета частотности слов в исходных файлах.

**Отправка задания MapReduce**

Следующий сценарий PowerShell отправляет пример задания для подсчета слов: 
	
	$clusterName = "<HDInsightClusterName>"            
	
	# Define the MapReduce job
	$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"
	
	# Run the job and show the standard error 
	$wordCountJobDefinition | Start-AzureHDInsightJob -Cluster $clusterName | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | %{ Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $_.JobId -StandardError}
	
> [AZURE.NOTE] *hadoop-examples.jar* предоставляется вместе с кластерами HDInsight версии 2.1. В кластерах HDInsight версии 3.0 файл был переименован в *hadoop-mapreduce.jar*.

Сведения о префиксе WASB см. в разделе [Использование хранилища BLOB-объектов Azure для HDInsight][hdinsight-storage].

**Скачивание выходных данных задания MapReduce**

Следующий сценарий PowerShell возвращает выходные данные задания MapReduce из последней процедуры:

	$storageAccountName = "<StorageAccountName>"   
	$containerName = "<ContainerName>"             
		
	# Create the storage account context object
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	# Download the output to local computer
	Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
	
	# Display the output
	cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

Дополнительные сведения о разработке и выполнении заданий MapReduce см. в разделе [Использование MapReduce с HDInsight][hdinsight-use-mapreduce].






































##<a id="hive"></a> Отправка заданий Hive
Кластер HDInsight поставляется с примером таблицы Hive, которая называется *hivesampletable*. Для получения списка таблиц Hive в кластере можно использовать HiveQL-команду "show tables;".

**Отправка задания Hive**

Следующий сценарий отправляет задание Hive в список таблиц Hive:
	
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


##<a id="upload"></a>Отправка данных в хранилище больших двоичных объектов
См. раздел [Отправка данных в HDInsight][hdinsight-upload-data].

##<a id="download"></a>Загрузка выходных данных MapReduce из хранилища BLOB-объектов
См. раздел [Отправка заданий MapReduce](#mapreduce) в этой статье.

## См. также
* [Справочная документация по командлетам HDInsight][hdinsight-powershell-reference]
* [Администрирование HDInsight с использованием портала управления][hdinsight-admin-portal]
* [Администрирование HDInsight с использованием интерфейса командной строки][hdinsight-admin-cli]
* [Подготовка кластеров HDInsight к работе][hdinsight-provision]
* [Отправка данных в HDInsight][hdinsight-upload-data]
* [Отправка заданий Hadoop программными средствами][hdinsight-submit-jobs]
* [Приступая к работе с Azure HDInsight][hdinsight-get-started]


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-provision]: ../hdinsight-provision-clusters/

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/

[hdinsight-admin-cli]: ../hdinsight-administer-use-command-line/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-upload-data]: ../hdinsight-upload-data/

[hdinsight-powershell-reference]: http://msdn.microsoft.com/library/windowsazure/dn479228.aspx

[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png


<!--HONumber=42-->
