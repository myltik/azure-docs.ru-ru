<properties 
   pageTitle="Настройка кластера HDInsight и хранилища озера данных с помощью Azure PowerShell | Azure" 
   description="Настройте кластер HDInsight Hadoop для работы с озером данных Azure, используя Azure PowerShell." 
   services="data-lake" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/28/2015"
   ms.author="nitinme"/>

# Подготовка кластера HDInsight и хранилища озера данных с помощью Azure PowerShell

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Using PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)


Узнайте, как с помощью Azure PowerShell настроить кластер HDInsight (Hadoop, HBase или Storm) для работы с хранилищем озера данных Azure. Работая с этим выпуском, учитывайте следующие моменты. * **В кластерах Hadoop и Storm (Windows и Linux)** хранилище озера данных может использоваться только как дополнительная учетная запись хранения. По умолчанию в этих кластерах в качестве учетной записи хранения используются BLOB-объекты хранилища Azure (WASB). * **В кластерах HBase (Windows и Linux)** хранилище озера данных можно использовать в качестве стандартного или дополнительного хранилища.

В этой статье мы подготовим кластер Hadoop, в котором хранилище озера данных будет дополнительным хранилищем.

Настройка в HDInsight хранилища озера данных с помощью PowerShell состоит из нескольких этапов:

* создание хранилища озера данных Azure;
* настройка проверки подлинности для доступа к хранилищу озера данных на основе ролей;
* создание кластера HDInsight с проверкой подлинности в хранилище озера данных;
* выполнение тестового задания в кластере.

## Предварительные требования

Перед началом работы с этим учебником вам необходимо:

- Оформить **подписка Azure**. См. [Бесплатная пробная версия Azure](https://azure.microsoft.com/ru-RU/pricing/free-trial/).
- **Настроить в подписке Azure** общедоступную предварительную версию хранилища озера данных. См. [инструкции](data-lake-store-get-started-portal.md#signup).
- Установить **пакет SDK Windows**. Его можно установить [отсюда](https://dev.windows.com/ru-RU/downloads). Пакет используется для создания сертификата безопасности.
- **Azure PowerShell (начиная с версии 1.0)**. Инструкции см. в статье [Установка и настройка Azure PowerShell](../install-configure-powershell.md). После установки Azure PowerShell 1.0 (или более новой версии) выполните следующий командлет, чтобы установить модуль хранилища озера данных Azure.

		Install-Module AzureRM.DataLakeStore

	Дополнительные сведения о модуле **AzureRM.DataLakeStore** см. в [коллекции PowerShell](http://www.powershellgallery.com/packages/AzureRM.DataLakeStore).
 

## Создание хранилища озера данных Azure

Чтобы создать хранилище озера данных, сделайте следующее.

1. На своем компьютере откройте новое окно Azure PowerShell и введите следующий фрагмент кода. При появлении запроса на вход введите учетные данные администратора или владельца подписки.

        # Log in to your Azure account
		Login-AzureRmAccount
        
		# List all the subscriptions associated to your account
		Get-AzureRmSubscription
		
		# Select a subscription 
		Set-AzureRmContext -SubscriptionId <subscription ID>

		# Register for Data Lake Store
		Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

3. Учетная запись хранилища озера данных Azure связывается с группой ресурсов Azure. Создайте эту группу.

		$resourceGroupName = "<your new resource group name>"
    	New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

	![Создание группы ресурсов Azure](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateResourceGroup.png "Создание группы ресурсов Azure")

2. Создайте учетную запись хранилища озера данных Azure. Имя новой учетной записи должно содержать только строчные буквы и цифры.

		$dataLakeStoreName = "<your new Data Lake Store name>"
    	New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

	![Создание учетной записи озера данных Azure](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateADLAcc.png "Создание учетной записи озера данных Azure")

3. Убедитесь, что учетная запись создана.

		Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

	Командлет должен возвратить результат **True**.

4. Отправьте пример данных в озеро данных Azure. Позже мы проверим, доступны ли эти данные из кластера HDInsight. Если у вас нет под рукой подходящих для этих целей данных, загрузите папку **Ambulance Data** из [репозитория Git для озера данных Azure](https://github.com/MicrosoftBigData/ProjectKona/tree/master/SQLIPSamples/SampleData/AmbulanceData).

		
		$myrootdir = "/"
		Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## Настройка проверки подлинности для доступа к хранилищу озера данных на основе ролей

Каждая подписка Azure связана с Azure Active Directory. Пользователи и службы, получающие доступ к ресурсам подписки через портал Azure или API диспетчера ресурсов Azure, сначала должны пройти проверку подлинности в соответствующей службе Azure Active Directory. Доступ к подпискам и службам Azure предоставляется путем назначения соответствующей роли в ресурсе Azure. В случае со службами субъект-служба идентифицирует службу в Azure Active Directory (AAD). В этом разделе мы расскажем, как предоставить службе приложения, в частности HDInsight, доступ к ресурсу Azure (созданной ранее учетной записи хранилища озера данных Azure). Для этого с помощью Azure PowerShell мы создадим для приложения субъект-службу и назначим ему роли.

Чтобы настроить для озера данных Azure проверку подлинности в Active Directory, вам необходимо сделать следующее:

* создать самозаверяющий сертификат;
* создать приложение в Azure Active Directory и субъект-службу.

### Создание самозаверяющего сертификата

Прежде чем выполнять дальнейшие действия, описанные в этом разделе, убедитесь, что на вашем компьютере установлен [пакет SDK Windows](https://dev.windows.com/ru-RU/downloads). Вам также необходимо создать каталог, например **C:\\mycertdir**, в котором будет создан сертификат.

1. В окне PowerShell перейдите в расположение, где установлен пакет SDK Windows (обычно это `C:\Program Files (x86)\Windows Kits\10\bin\x86`), и с помощью служебной программы [MakeCert][makecert] создайте самозаверяющий сертификат и закрытый ключ. Используйте такие команды:

		$certificateFileDir = "<my certificate directory>"
		cd $certificateFileDir
		$startDate = (Get-Date).ToString('MM/dd/yyyy')
		$endDate = (Get-Date).AddDays(365).ToString('MM/dd/yyyy')

		makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -b $startDate -e $endDate -r -len 2048

	Вам будет предложено ввести пароль для закрытого ключа. После успешного выполнения команды в указанном каталоге должны появиться файлы **CertFile.cer** и **mykey.pvk**.

4. С помощью служебной программы [Pvk2Pfx][pvk2pfx] преобразуйте созданные программой MakeCert файлы PVK и CER в файл PFX. Выполните следующую команду:

		pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po myPassword

	При появлении соответствующего запроса введите указанный ранее пароль для закрытого ключа. Значение, указываемое для параметра **-po** — это пароль, связанный с файлом PFX. После успешного выполнения команды вы должны увидеть в указанном каталоге сертификата файл CertFile.pfx.

###  Создание приложения в Azure Active Directory и субъекта-службы

Следуя описаниям, приведенным в этом разделе, вы создадите субъект-службу для приложения Azure Active Directory, назначите роль субъекту-службе и пройдете проверку подлинности в качестве субъекта-службы, используя сертификат. Чтобы создать приложение в Azure Active Directory, выполните следующие команды.

1. В окне консоли PowerShell вставьте из буфера обмена следующие командлеты. Укажите для свойства **-DisplayName** уникальное значение. Значения свойств **-HomePage** и **- IdentiferUris** — это заполнители, которые не проверяются. 

		$certificateFilePath = "$certificateFileDir\CertFile.pfx"
		
		$password = Read-Host –Prompt "Enter the password" –AsSecureString  # This is the password you specified for the .pfx file (e.g. "myPassword")
		
		$certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)
		
		$rawCertificateData = $certificatePFX.GetRawCertData()
		
		$credential = [System.Convert]::ToBase64String($rawCertificateData)

		$application = New-AzureRmADApplication `
					-DisplayName "HDIADL" ` 
					-HomePage "https://contoso.com" `
					-IdentifierUris "https://mycontoso.com" `
					-KeyValue $credential  `
					-KeyType "AsymmetricX509Cert"  `
					-KeyUsage "Verify"  `
					-StartDate $startDate  `
					-EndDate $endDate

		$applicationId = $application.ApplicationId

2. С помощью идентификатора приложения создайте субъект-службу.

		$servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId
		
		$objectId = $servicePrincipal.Id

3. Предоставьте субъекту-службе доступ к созданному ранее хранилищу озера данных.
		
		Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All

	При появлении соответствующего запроса введите **Y** для подтверждения.

## Создание кластера HDInsight с проверкой подлинности в хранилище озера данных

В этом разделе мы создадим кластер HDInsight Hadoop. В этом выпуске кластер HDInsight и хранилище озера данных должны быть в одном расположении (Восток США 2).

1. Сначала получите идентификатор клиента подписки. Позже он вам понадобится.

		$tenantID = (Get-AzureRmContext).Tenant.TenantId

2. В этом выпуске в кластере Hadoop хранилище озера данных может использоваться только как дополнительное хранилище кластера. Хранилищем по умолчанию по-прежнему будут BLOB-объекты хранилища Azure (WASB). Поэтому мы сначала создадим учетную запись хранения и контейнеры хранилища, необходимые для кластера.

		# Create an Azure storage account
		$location = "East US 2"
		$storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name
		
		New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS
 
		# Create an Azure Blob Storage container
		$containerName = "<ContainerName>"              # Provide a container name
		$storageAccountKey = Get-AzureRmStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		New-AzureStorageContainer -Name $containerName -Context $destContext

3. Создайте кластер HDInsight, выполнив следующие командлеты.

		# Set these variables
		$clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
		$clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
		$httpCredentials = Get-Credential
		$rdpCredentials = Get-Credential
		
		New-AzureRmHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.2" -RdpCredential $rdpCredentials -RdpAccessExpiry (Get-Date).AddDays(14) -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password

	В случае успешного выполнения командлета должен появиться такой результат:

		Name                      : hdiadlcluster
		Id                        : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/hdiadlgroup/providers/Mi
		                            crosoft.HDInsight/clusters/hdiadlcluster
		Location                  : East US 2
		ClusterVersion            : 3.2.7.707
		OperatingSystemType       : Windows
		ClusterState              : Running
		ClusterType               : Hadoop
		CoresUsed                 : 16
		HttpEndpoint              : hdiadlcluster.azurehdinsight.net
		Error                     :
		DefaultStorageAccount     :
		DefaultStorageContainer   :
		ResourceGroup             : hdiadlgroup
		AdditionalStorageAccounts : 

## Выполнение тестовых заданий в кластере HDInsight

После настройки кластера HDInsight выполните в нем тестовые задания, чтобы проверить, доступно ли ему хранилище озера данных. Для этого запустите образец задания Hive, создающего таблицу с данными, которые вы ранее отправили в хранилище озера данных.

С помощью следующих командлетов выполните запрос Hive. Этот запрос создает таблицу на основе данных в хранилище озера данных, а затем в созданной таблице выполняет запрос на выборку.

	$queryString = "DROP TABLE vehicles;" + "CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://$dataLakeStoreName.azuredatalakestore.net:443/';" + "SELECT * FROM vehicles LIMIT 10;"
	
	$hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString

	$hiveJob = Start-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $httpCredentials

	Wait-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $httpCredentials

Командлеты возвращают следующий результат. Если **ExitValue** имеет значение 0, это означает, что задание выполнилось успешно.

	Cluster         : hdiadlcluster.
	HttpEndpoint    : hdiadlcluster.azurehdinsight.net
	State           : SUCCEEDED
	JobId           : job_1445386885331_0012
	ParentId        :
	PercentComplete :
	ExitValue       : 0
	User            : admin
	Callback        :
	Completed       : done

Извлеките выходные данные из задания с помощью следующего командлета.

	Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $hiveJob.JobId -DefaultContainer $containerName -DefaultStorageAccountName $storageAccountName -DefaultStorageAccountKey $storageAccountKey -ClusterCredential $httpCredentials

Результат будет выглядеть так:

	1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
	1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
	1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
	1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
	1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
	1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
	1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
	1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
	1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
	1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1


	

## Доступ к хранилищу озера данных с помощью команд HDFS

Настроив в кластере HDInsight параметры для работы с хранилищем озера данных, используйте для доступа к хранилищу команды оболочки HDFS.

1. Войдите на новый [портал предварительной версии Azure](https://portal.azure.com).

2. Последовательно щелкните **Обзор** и **Кластеры HDInsight**, а затем выберите кластер HDInsight, который вы создали.

3. В колонке кластера нажмите кнопку **Удаленный рабочий стол**, а затем в колонке **Удаленный рабочий стол** щелкните **Подключиться**.

	![Удаленное подключение к кластеру HDI](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.HDI.PS.Remote.Desktop.png "Создание группы ресурсов Azure")

	При появлении соответствующего запроса введите учетные данные, которые вы указали для пользователя удаленного рабочего стола.

4. Во время удаленного сеанса запустите Windows PowerShell и, используя команды файловой системы HDFS, отобразите список файлов в озере данных Azure.

	 	hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

	Эта команда должна показать файл, который вы ранее отправили в хранилище озера данных.

		15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
		Found 1 items
		-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/vehicle1_09142014.csv

	С помощью команды `hdfs dfs -put` вы можете отправить в озеро данных Azure новые файлы, а затем с помощью команды `hdfs dfs -ls` проверить, успешно ли они передались.

## См. также

* [Портал: создание кластера HDInsight для работы с хранилищем озера данных](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/ru-RU/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/ru-RU/library/windows/desktop/ff550672(v=vs.85).aspx

<!---HONumber=Nov15_HO1-->