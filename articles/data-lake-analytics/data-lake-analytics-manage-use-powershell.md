<properties 
   pageTitle="Управление аналитикой озера данных Azure с помощью Azure PowerShell | Azure" 
   description="Узнайте, как управлять заданиями аналитики озера данных, источниками данных и пользователями." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="12/01/2015"
   ms.author="jgao"/>

# Управление аналитикой озера данных Azure с помощью Azure PowerShell

[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Узнайте, как управлять учетными записями, источниками данных, пользователями и заданиями аналитики озера данных Azure с помощью Azure PowerShell. Для просмотра статей, посвященных управлению с помощью других средств, щелкните вкладку выделения выше.

**Предварительные требования**

Перед началом работы с этим учебником необходимо иметь следующее:

- **Подписка Azure.**. См. [Бесплатная пробная версия Azure]https://azure.microsoft.com/ru-RU/pricing/free-trial/).


<!-- ################################ -->
<!-- ################################ -->


##Установка Azure PowerShell 1.0 и более поздних версий

Сначала необходимо удалить версии 0.9x.

Проверка версии установленной оболочки PowerShell:

	Get-Module *azure*
	
Для удаления старой версии запустите «Программы и компоненты» в панели управления.

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

- [Установщик веб-платформы Майкрософт (WebPI)](http://aka.ms/webpi-azps). Если вы установили Azure PowerShell 0.9.x, появится запрос на удаление версии 0.9.x. Если вы установили модули Azure PowerShell из коллекции PowerShell, установщик требует удалить эти модули перед установкой, чтобы обеспечить целостность среды PowerShell Azure. Инструкции см. в разделе [Установка Azure PowerShell 1.0 с помощью установщика веб-платформы](https://azure.microsoft.com/blog/azps-1-0/).

Обновления для установщика веб-платформы будут выпускаться ежемесячно. Обновления для коллекции PowerShell будут выпускаться на постоянной основе. Если вы решите использовать для установки коллекцию PowerShell, она станет основным источником всего нового и лучшего в Azure PowerShell.

**Вывод списка командлетов**.

	Get-Command *Azure*DataLakeAnalytics*

**Для подключения к Azure воспользуйтесь следующими командлетами**.

	Login-AzureRmAccount
	Get-AzureRmSubscription  # for finding the Azure Subscription ID
	Set-AzureRmContext -SubscriptionID <Azure Subscription ID>
	
## Управление учетными записями

Перед выполнением любого задания аналитики озера данных необходимо иметь учетную запись аналитики озера данных. В отличие от Azure HDInsight учетная запись аналитики не оплачивается, если ни одно задание не выполняется. Вы платите только за время, когда выполняется задание. Дополнительные сведения см. в разделе [Обзор аналитики озера данных Microsoft Azure](data-lake-analytics-overview.md).

###Создание учетных записей

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeStoreName = "<DataLakeAccountName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	$location = "<Microsoft Data Center>"
	
	Write-Host "Create a resource group ..." -ForegroundColor Green
	New-AzureRmResourceGroup `
		-Name  $resourceGroupName `
		-Location $location
	
	Write-Host "Create a Data Lake account ..."  -ForegroundColor Green
	New-AzureRmDataLakeStoreAccount `
		-ResourceGroupName $resourceGroupName `
		-Name $dataLakeStoreName `
		-Location $location 
	
	Write-Host "Create a Data Lake Analytics account ..."  -ForegroundColor Green
	New-AzureRmDataLakeAnalyticsAccount `
		-Name $dataLakeAnalyticsAccountName `
		-ResourceGroupName $resourceGroupName `
		-Location $location `
		-DefaultDataLake $dataLakeStoreName
	
	Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
	Get-AzureRmDataLakeAnalyticsAccount `
		-ResourceGroupName $resourceGroupName `
		-Name $dataLakeAnalyticsAccountName  

Также можно использовать шаблон группы ресурсов Azure. Шаблон для создания учетной записи аналитики озера данных и зависимой от нее учетной записи хранилища озера данных доступен в [Приложении A](#appendix-a). Сохраните шаблон в файл с шаблоном .json, а затем воспользуйтесь следующим скриптом PowerShell, чтобы вызвать его.


	$AzureSubscriptionID = "<Your Azure Subscription ID>"
	
	$ResourceGroupName = "<New Azure Resource Group Name>"
	$Location = "EAST US 2"
	$DefaultDataLakeStoreAccountName = "<New Data Lake Store Account Name>"
	$DataLakeAnalyticsAccountName = "<New Data Lake Analytics Account Name>"
	
	$DeploymentName = "MyDataLakeAnalyticsDeployment"
	$ARMTemplateFile = "E:\Tutorials\ADL\ARMTemplate\azuredeploy.json"  # update the Json template path 
	
	Login-AzureRmAccount
	
	Select-AzureRmSubscription -SubscriptionId $AzureSubscriptionID
	
	# Create the resource group
	New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
	
	# Create the Data Lake Analytics account with the default Data Lake Store account.
	$parameters = @{"adlAnalyticsName"=$DataLakeAnalyticsAccountName; "adlStoreName"=$DefaultDataLakeStoreAccountName}
	New-AzureRmResourceGroupDeployment -Name $DeploymentName -ResourceGroupName $ResourceGroupName -TemplateFile $ARMTemplateFile -TemplateParameterObject $parameters 

 
###Список учетных записей

Список учетных записей аналитики озера данных в текущей подписке

	Get-AzureRmDataLakeAnalyticsAccount
	
Результаты:

	Id         : /subscriptions/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/resourceGroups/learn1021rg/providers/Microsoft.DataLakeAnalytics/accounts/learn1021adla
	Location   : eastus2
	Name       : learn1021adla
	Properties : Microsoft.Azure.Management.DataLake.Analytics.Models.DataLakeAnalyticsAccountProperties
	Tags       : {}
	Type       : Microsoft.DataLakeAnalytics/accounts

Список учетных записей аналитики озера данных в конкретной группе ресурсов

	Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName

Получение сведений о конкретной учетной записи аналитики озера данных

	Get-AzureRmDataLakeAnalyticsAccount -Name $adlAnalyticsAccountName

Проверка наличия конкретной учетной записи аналитики озера данных

	Test-AzureRmDataLakeAnalyticsAccount -Name $adlAnalyticsAccountName

Командлет возвращает значение **True** или **False**.

###Удаление учетных записей аналитики озера данных

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	
	Remove-AzureRmDataLakeAnalyticsAccount -Name $dataLakeAnalyticsAccountName 

Удаление учетной записи аналитики не приведет к удалению зависимой учетной записи хранения озера данных . В следующем примере удаляются учетная запись аналитики озера данных и учетная запись хранения озера данных по умолчанию.

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	$dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount

	Remove-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName 
	Remove-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName

<!-- ################################ -->
<!-- ################################ -->
## Управление источниками данных учетной записи

Аналитика озера данных в настоящее время поддерживает следующие источники данных:

- [Хранилище озера данных Azure](data-lake-storage-overview.md)
- [Хранилище Azure](storage-introduction.md)

При создании учетной записи аналитики необходимо указать учетную запись хранения озера данных Azure в качестве учетной записи хранения по умолчанию. Учетная запись хранения озера данных по умолчанию используется для хранения метаданных задания и журналов аудита задания. После создания учетной записи аналитики можно добавить дополнительные учетные записи хранения озера данных и учетные записи хранения Azure.

### Поиск учетной записи хранения озера данных по умолчанию

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	$dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount


### Добавление дополнительных учетных записей хранения больших двоичных объектов Azure

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	$AzureStorageAccountName = "<AzureStorageAccountName>"
	$AzureStorageAccountKey = "<AzureStorageAccountKey>"
	
	Add-AzureRmDataLakeAnalyticsDataSource -ResourceGroupName $resourceGroupName -AccountName $dataLakeAnalyticName -AzureBlob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

### Добавление дополнительных учетных записей хранения озера данных

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	$AzureDataLakeName = "<DataLakeStoreName>"
	
	Add-AzureRmDataLakeAnalyticsDataSource -ResourceGroupName $resourceGroupName -AccountName $dataLakeAnalyticName -DataLake $AzureDataLakeName 

### Список источников данных

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

	(Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DataLakeStoreAccounts
	(Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.StorageAccounts
	


<!-- ################################ -->
<!-- ################################ -->
## Управление заданиями

Для создания любого задания требуется учетная запись аналитики озера данных. Дополнительные сведения см. в разделе [Управление учетными записями аналитики озера данных](#manage-data-lake-analytics-accounts).

### Список заданий

	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	
	Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName
	
	Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName -State Running, Queued
	#States: Accepted, Compiling, Ended, New, Paused, Queued, Running, Scheduling, Starting
	
	Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName -Result Cancelled
	#Results: Cancelled, Failed, None, Successed 
	
	Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName -Name <Job Name>
	Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName -Submitter <Job submitter>

	# List all jobs submitted on January 1 (local time)
	Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName `
		-SubmittedAfter "2015/01/01"
		-SubmittedBefore "2015/01/02"	

	# List all jobs that succeeded on January 1 after 2 pm (UTC time)
	Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName `
		-State Ended
		-Result Succeeded
		-SubmittedAfter "2015/01/01 2:00 PM -0"
		-SubmittedBefore "2015/01/02 -0"

	# List all jobs submitted in the past hour
	Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName `
		-SubmittedAfter (Get-Date).AddHours(-1)

### Получение сведений о задании

	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName -JobID <Job ID>
	
### Отправка заданий

	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

	#Pass script via path
	Submit-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName `
		-Name $jobName `
		-ScriptPath $scriptPath

	#Pass script contents
	Submit-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName `
		-Name $jobName `
		-Script $scriptContents

> [AZURE.NOTE]Приоритет задания по умолчанию — 1000, а степень параллелизма по умолчанию для задания — 1.


### Отмена задания

	Stop-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName `
		-JobID $jobID


## Управление элементами каталога

Каталог U-SQL используется для структурирования данных и кода, чтобы их могли совместно использовать сценарии U-SQL. Каталог обеспечивает максимальную производительность, возможную с данными в озере данных Azure. Дополнительные сведения см. в разделе [Использование каталога U-SQL](data-lake-analytics-use-u-sql-catalog.md).

###Список элементов каталога

	#List databases
	Get-AzureRmDataLakeAnalyticsCatalogItem `
		-AccountName $adlAnalyticsAccountName `
		-ItemType Database
	
	
	
	#List tables
	Get-AzureRmDataLakeAnalyticsCatalogItem `
		-AccountName $adlAnalyticsAccountName `
		-ItemType Table `
		-Path "master.dbo"

###Получение сведений об элементе каталога 

	#Get a database
	Get-AzureRmDataLakeAnalyticsCatalogItem `
		-AccountName $adlAnalyticsAccountName `
		-ItemType Database `
		-Path "master"
	
	#Get a table
	Get-AzureRmDataLakeAnalyticsCatalogItem `
		-AccountName $adlAnalyticsAccountName `
		-ItemType Table `
		-Path "master.dbo.mytable"

###Проверка наличия элемента каталога

	Test-AzureRmDataLakeAnalyticsCatalogItem  `
		-AccountName $adlAnalyticsAccountName `
		-ItemType Database `
		-Path "master"

###Создание секрета каталога
	New-AzureRmDataLakeAnalyticsCatalogSecret  `
			-AccountName $adlAnalyticsAccountName `
			-DatabaseName "master" `
			-Secret (Get-Credential -UserName "username" -Message "Enter the password")

### Изменение секрета каталога
	Set-AzureRmDataLakeAnalyticsCatalogSecret  `
			-AccountName $adlAnalyticsAccountName `
			-DatabaseName "master" `
			-Secret (Get-Credential -UserName "username" -Message "Enter the password")



###Удаление секрета каталога
	Remove-AzureRmDataLakeAnalyticsCatalogSecret  `
			-AccountName $adlAnalyticsAccountName `
			-DatabaseName "master"


## Использование групп диспетчера ресурсов Azure

Обычно приложения состоят из множества компонентов, например веб-приложения, базы данных, сервера базы данных, хранилища и служб сторонних поставщиков. Диспетчер ресурсов Azure (ARM) позволяет работать с ресурсами в приложении в виде группы, которая называется группой ресурсов Azure. Вы можете развертывать, обновлять, отслеживать или удалять все ресурсы для приложения в рамках одной скоординированной операции. Для развертывания вы используете шаблон, который можно использовать для разных сред, в том числе для тестовой, промежуточной и рабочей. Вы можете уточнить счета для своей организации, просмотрев сведенные затраты для всей группы. Дополнительную информацию см. в статье [Общие сведения о диспетчере ресурсов Azure](resource-group-overview.md).

Служба аналитики озера данных может включать следующие компоненты:

- Учетная запись аналитики озера данных Azure
- Требуемая учетная запись хранения озера данных Azure по умолчанию
- Дополнительные учетные записи хранения озера данных Azure
- Дополнительные учетные записи хранения Azure

Можно создать все эти компоненты в одной группе ARM, чтобы ими было проще управлять.

![Аналитика озера данных Azure: учетная запись и хранилище](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Учетная запись аналитики озера данных и зависимые учетные записи хранения должны находиться в одном центре обработки данных Azure. Однако группа ARM может находиться в другом центре обработки данных.

##См. также 

- [Обзор аналитики озера данных Microsoft Azure](data-lake-analytics-overview.md)
- [Начало работы с аналитикой озера данных с помощью портала Azure](data-lake-analytics-get-started-portal.md)
- [Управление аналитикой озера данных Azure с помощью портала Azure](data-lake-analytics-use-portal.md)
- [Мониторинг заданий аналитики озера данных Azure и устранение связанных с ними неполадок с помощью портала Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

##Приложение А. Шаблон ARM аналитики озера данных

Следующий шаблон ARM можно использовать для развертывания учетной записи аналитики озера данных и зависимой от нее учетной записи хранения озера данных. Сохраните его в формате json и затем используйте скрипт PowerShell для вызова шаблона. Дополнительные сведения см. в разделе [Развертывание приложения с использованием шаблона диспетчера ресурсов Azure](resource-group-template-deploy.md#deploy-with-powershell) и [Создание шаблонов диспетчера ресурсов Azure](resource-group-authoring-templates.md).

	{
		"$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"adlAnalyticsName": {
				"type": "string",
				"metadata": {
					"description": "The name of the Data Lake Analytics account to create."
				}
			},
			"adlStoreName": {
				"type": "string",
				"metadata": {
					"description": "The name of the Data Lake Store account to create."
				}
			}
		},
		"resources": [{
			"name": "[parameters('adlAnalyticsName')]",
			"type": "Microsoft.DataLakeAnalytics/accounts",
			"location": "East US 2",
			"apiVersion": "2015-10-01-preview",
			"dependsOn": ["[concat('Microsoft.DataLakeStore/accounts/',parameters('adlStoreName'))]"],
			"tags": {
				
			},
			"properties": {
				"defaultDataLakeAccount": "[parameters('adlStoreName')]"
				}
			}
		},
		{
			"name": "[parameters('adlName')]",
			"type": "Microsoft.DataLakeStore/accounts",
			"location": "East US 2",
			"apiVersion": "2015-10-01-preview",
			"dependsOn": [],
			"tags": {
				
			}
		}],
		"outputs": {
			"adlAnalyticsAccount": {
				"type": "object",
				"value": "[reference(resourceId('Microsoft.DataLakeAnalytics/accounts',parameters('adlAnalyticsName')))]"
			},
			"adlStoreAccount": {
				"type": "object",
				"value": "[reference(resourceId('Microsoft.DataLakeStore/accounts',parameters('adlStoreName')))]"
			}
		}
	}

<!---HONumber=AcomDC_1203_2015-->