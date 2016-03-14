<properties 
	pageTitle="Управление Поиском Azure с помощью сценариев PowerShell | Microsoft Azure | Размещенная облачная служба поиска" 
	description="Управление службой Поиска Azure в Microsoft Azure с помощью сценариев PowerShell. Создание или обновление службы Поиска Azure и управление ключами администратора Поиска Azure"  
	services="search" 
	documentationCenter="" 
	authors="seansaleh" 
	manager="mblythe" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="search" 
	ms.devlang="na" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="powershell" 
	ms.date="02/25/2016" 
	ms.author="seasa"/>

# Управление службой поиска в Microsoft Azure с помощью PowerShell
> [AZURE.SELECTOR]
- [Портал](search-manage.md)
- [PowerShell](search-manage-powershell.md)
- [ИНТЕРФЕЙС REST API](search-get-started-management-api.md)

В этом разделе описаны команды PowerShell, предназначенные для выполнения многих задач управления служб Поиска Azure. Мы рассмотрим создание службы поиска, ее масштабирование и управления ее ключами API. Эти команды дублируют возможности управления, доступные в [API REST управления Поиском Azure](http://msdn.microsoft.com/library/dn832684.aspx).

## Предварительные требования
 
- Необходимо установить Azure PowerShell 1.0 или более поздней версии. Инструкции см. в разделе [Установка и настройка Azure PowerShell](../powershell-install-configure.md).
- Вы должны войти в свою подписку Azure в PowerShell, как описано ниже.

Сначала необходимо войти в Azure с помощью следующей команды.

	Login-AzureRmAccount

Укажите электронный адрес и пароль своей учетной записи Azure в диалоговом окне входа в Microsoft Azure.

Вы также можете [войти в неинтерактивном режиме с помощью субъекта-службы](../resource-group-authenticate-service-principal.md).

Если у вас несколько подписок Azure, необходимо выбрать одну из них. Чтобы просмотреть список текущих подписок, выполните следующую команду.

	Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Чтобы указать подписку, выполните указанную ниже команду. В приведенном ниже примере имя подписки — `ContosoSubscription`.

	Select-AzureRmSubscription -SubscriptionName ContosoSubscription

## Команды, которые помогут приступить к работе

	$serviceName = "your-service-name-lowercase-with-dashes"
	$sku = "free" # or "standard" for a paid service
	$location = "West US"
	# You can get a list of potential locations with
	# (Get-AzureRmResourceProvider -ListAvailable | Where-Object {$_.ProviderNamespace -eq 'Microsoft.Search'}).Locations
	$resourceGroupName = "YourResourceGroup" 
	# If you don't already have this resource group, you can create it with with 
	# New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

	# Register the arm provider idempotently. This must be done once per subscription
	Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Search" -Force

	# Create a new search service
	# This command will return once the service is fully created
	New-AzureRmResourceGroupDeployment `
		-ResourceGroupName $resourceGroupName `
		-TemplateUri "https://gallery.azure.com/artifact/20151001/Microsoft.Search.1.0.9/DeploymentTemplates/searchServiceDefaultTemplate.json" `
		-NameFromTemplate $serviceName `
		-Sku $sku `
		-Location $location `
		-PartitionCount 1 `
		-ReplicaCount 1
	
	# Get information about your new service and store it in $resource
	$resource = Get-AzureRmResource `
		-ResourceType "Microsoft.Search/searchServices" `
		-ResourceGroupName $resourceGroupName `
		-ResourceName $serviceName `
		-ApiVersion 2015-08-19
	
	# View your resource
	$resource
	
	# Get the primary admin api key
	$primaryKey = (Invoke-AzureRmResourceAction `
		-Action listAdminKeys `
		-ResourceId ($resource.ResourceId) `
		-ApiVersion 2015-08-19).PrimaryKey

	# Regenerate the secondary admin api Key
	$secondaryKey = (Invoke-AzureRmResourceAction `
		-ResourceType "Microsoft.Search/searchServices/regenerateAdminKey" `
		-ResourceGroupName $resourceGroupName `
		-ResourceName $serviceName `
		-ApiVersion 2015-08-19 `
		-Action secondary).SecondaryKey

	# Create a query key for read only access to your indexes
	$queryKeyDescription = "query-key-created-from-powershell"
	$queryKey = (Invoke-AzureRmResourceAction `
		-ResourceType "Microsoft.Search/searchServices/createQueryKey" `
		-ResourceGroupName $resourceGroupName `
		-ResourceName $serviceName `
		-ApiVersion 2015-08-19 `
		-Action $queryKeyDescription).Key

	# Delete query key
	Remove-AzureRmResource `
		-ResourceType "Microsoft.Search/searchServices/deleteQueryKey/$($queryKey)" `
		-ResourceGroupName $resourceGroupName `
		-ResourceName $serviceName `
		-ApiVersion 2015-08-19
		
	# Scale your service up
	# Note that this will only work if you made a non "free" service
	# This command will not return until the operation is finished
	# It can take 15 minutes or more to provision the additional resources
	$resource.Properties.ReplicaCount = 2
	$resource | Set-AzureRmResource
	
	# Delete your service
	# Deleting your service will delete all indexes and data in the service
	$resource | Remove-AzureRmResource
	
## Дальнейшие действия
	
После создания службы можно перейти к следующим шагам: создать [индекс](search-what-is-an-index.md), [отправить запросы в индекс](search-query-overview.md) и, наконец, создать приложение поиска, использующее службу поиска Azure, и управлять им.

- [Создание индекса службы поиска Azure на классическом портале Azure](search-create-index-portal.md)

- [Отправка запросов в индекс службы поиска Azure с помощью обозревателя поиска на портале Azure](search-explorer.md)

- [Настройка индексатора для загрузки данных из других служб](search-indexer-overview.md)

- [Как использовать Поиск Azure в приложении .NET](search-howto-dotnet-sdk.md)

- [Анализ трафика Поиска Azure](search-traffic-analytics.md)

<!---HONumber=AcomDC_0302_2016-->