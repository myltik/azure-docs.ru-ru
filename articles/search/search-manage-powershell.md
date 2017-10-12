---
title: "Управление поиском Azure с помощью сценариев PowerShell | Документация Майкрософт"
description: "Управление службой поиска Azure с помощью сценариев PowerShell. Создание или обновление службы Поиска Azure и управление ключами администратора Поиска Azure"
services: search
documentationcenter: 
author: seansaleh
manager: mblythe
editor: 
tags: azure-resource-manager
ms.assetid: 9b3dc1f2-3619-4235-ba1f-d2d6f5c45dd5
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: powershell
ms.date: 08/15/2016
ms.author: seasa
ms.openlocfilehash: aa51c846efef12461ec382274199bc049c42aaa3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-your-azure-search-service-with-powershell"></a>Управление службой поиска Azure с помощью PowerShell
> [!div class="op_single_selector"]
> * [Портал](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> 
> 

В этом разделе описаны команды PowerShell, предназначенные для выполнения многих задач управления служб Поиска Azure. Мы рассмотрим создание службы поиска, ее масштабирование и управления ее ключами API.
Эти команды дублируют возможности управления, доступные в [API REST управления Поиском Azure](http://msdn.microsoft.com/library/dn832684.aspx).

## <a name="prerequisites"></a>Предварительные требования
* Необходимо установить Azure PowerShell 1.0 или более поздней версии. Инструкции см. в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview).
* Вы должны войти в свою подписку Azure в PowerShell, как описано ниже.

Сначала необходимо войти в Azure с помощью следующей команды.

    Login-AzureRmAccount

Укажите электронный адрес и пароль своей учетной записи Azure в диалоговом окне входа в Microsoft Azure.

Вы также можете [войти в неинтерактивном режиме с помощью субъекта-службы](../azure-resource-manager/resource-group-authenticate-service-principal.md).

Если у вас несколько подписок Azure, необходимо выбрать одну из них. Чтобы просмотреть список текущих подписок, выполните следующую команду.

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Чтобы указать подписку, выполните указанную ниже команду. В приведенном ниже примере имя подписки — `ContosoSubscription`.

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

## <a name="commands-to-help-you-get-started"></a>Команды, которые помогут приступить к работе
    $serviceName = "your-service-name-lowercase-with-dashes"
    $sku = "free" # or "basic" or "standard" for paid services
    $location = "West US"
    # You can get a list of potential locations with
    # (Get-AzureRmResourceProvider -ListAvailable | Where-Object {$_.ProviderNamespace -eq 'Microsoft.Search'}).Locations
    $resourceGroupName = "YourResourceGroup" 
    # If you don't already have this resource group, you can create it with 
    # New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Register the ARM provider idempotently. This must be done once per subscription
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Search"

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

    # Get the primary admin API key
    $primaryKey = (Invoke-AzureRmResourceAction `
        -Action listAdminKeys `
        -ResourceId $resource.ResourceId `
        -ApiVersion 2015-08-19).PrimaryKey

    # Regenerate the secondary admin API Key
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

    # View your query key
    $queryKey

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

## <a name="next-steps"></a>Дальнейшие действия
После создания службы можно перейти к следующим шагам: создать [индекс](search-what-is-an-index.md), [отправить запросы в индекс](search-query-overview.md) и, наконец, создать приложение поиска, использующее службу поиска Azure, и управлять им.

* [Создание индекса службы "Поиск Azure" на портале Azure](search-create-index-portal.md)
* [Отправка запросов в индекс службы поиска Azure с помощью портала Azure](search-explorer.md)
* [Настройка индексатора для загрузки данных из других служб](search-indexer-overview.md)
* [Как использовать Поиск Azure в приложении .NET](search-howto-dotnet-sdk.md)
* [Анализ трафика Поиска Azure](search-traffic-analytics.md)

