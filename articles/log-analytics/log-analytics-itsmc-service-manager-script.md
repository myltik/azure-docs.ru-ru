---
title: "Автоматизированный скрипт для создания веб-приложения Service Manager для подключения к OMS с помощью соединителя управления ИТ-службами | Документация Microsoft"
description: "Создание веб-приложения Service Manager с помощью автоматизированного скрипта для подключения к OMS с помощью соединителя управления ИТ-службами, а также централизованный мониторинг и управление рабочими элементами ITSM."
services: log-analytics
documentationcenter: 
author: JYOTHIRMAISURI
manager: riyazp
editor: 
ms.assetid: 879e819f-d880-41c8-9775-a30907e42059
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2017
ms.author: v-jysur
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 5218a5f8f74e7a26696e4b3d9b88d5cef3e67ff8
ms.contentlocale: ru-ru
ms.lasthandoff: 05/09/2017


---

# <a name="create-service-manager-web-app-using-the-automated-script-preview"></a>Создание веб-приложения Service Manager с помощью автоматизированного скрипта (предварительная версия)

Чтобы создать веб-приложение для экземпляра Service Manager используйте следующий скрипт. См. дополнительные сведения о подключении [Service Manager](log-analytics-itsmc-connections.md#create-and-deploy-service-manager-web-app-service).

Запустите скрипт, указав следующие данные:

- сведения о подписке Azure;
- Имя группы ресурсов
- Расположение
- сведения о сервере Service Manager (имя сервера, домен, имя пользователя и пароль);
- префикс имени сайта для веб-приложения;
- пространство имен ServiceBus.

Скрипт создает веб-приложение, используя имя, которое вы указали (с несколькими дополнительными строками, обеспечивающими его уникальность). Он создаст **URL-адрес веб-приложения**, **идентификатор клиента** и **секрет клиента**.

Сохраните эти значения. Вы сможете их использовать при создании подключения с помощью соединителя управления ИТ-службами.

## <a name="prerequisites"></a>Предварительные требования

 Windows Management Framework версии 5.0 или выше.
По умолчанию в Windows 10 используется версия 5.1. Скачать нужную версию можно [отсюда](https://www.microsoft.com/download/details.aspx?id=53347):

Используйте следующий скрипт:

```
###################################
# User Configuration Section Begins
####################################
# Subscription name in Azure account. Check in Azure Portal.
$azureSubscriptionName = ""

# Resource group name for resource deployment. Could be an existing resource group or a new one to be created.
$resourceGroupName = ""

# Location for existing resource group or new resource group deployment
################################### List of available regions #################################################
# centralus,eastasia,southeastasia,eastus,eastus2,westus,westus2,northcentralus,southcentralus,westcentralus,
# northeurope,westeurope,japaneast,japanwest,brazilsouth,australiasoutheast,australiaeast,westindia,southindia,
# centralindia,canadacentral,canadaeast,uksouth,ukwest.
###############################################################################################################
$location = ""

# Service Manager Authentication Settings
$serverName = ""
$domain = ""
$username = ""
$password = ""


# Azure site Name Prefix. Default is "smoc". It can be configured to any desired value.
$siteNamePrefix = ""

# Service Bus namespace. Please provide an already existing service bus namespace.
# If it doesn't exist, a new one will be created with name $siteName + "sbn" which can also be later reused for any other hybrid connections.
$serviceName = ""

##################################
# User Configuration Section Ends
##################################

################
# Installations
################

# Allowing the execution of the script for current user.  
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

Write-Host "Checking for required modules..."
if(!(Get-PackageProvider -Name NuGet))
{
   Write-Host "Installing NuGet Package Provider..."
   Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Scope CurrentUser -Force -WarningAction SilentlyContinue
}
$module = Get-Module -ListAvailable -Name AzureRM
if(!$module -or ($module.Version.Major -lt 3))
{
    Write-Host "Installing AzureRm Module..."  
    try
    {
        # In case of Win 10 Anniversary update
        Install-Module AzureRM -MinimumVersion 3.3.0 -Scope CurrentUser -Force -WarningAction SilentlyContinue -AllowClobber
   }
    catch
    {
        Install-Module AzureRM -MinimumVersion 3.3.0 -Scope CurrentUser -Force -WarningAction SilentlyContinue
    }

}

Write-Host "Requirement check complete!!"

#############
# Parameters
#############

$errorActionPreference = "Stop"

$templateUri = "https://raw.githubusercontent.com/SystemCenterServiceManager/SMOMSConnector/master/azuredeploy.json"

if(!$siteNamePrefix)
{
    $siteNamePrefix = "smoc"
}

Add-AzureRmAccount

$context = Set-AzureRmContext -SubscriptionName $azureSubscriptionName -WarningAction SilentlyContinue
do
{
    $rand = Get-Random -Maximum 32000

    $siteName = $siteNamePrefix + $rand

    $resource = Find-AzureRmResource -ResourceNameContains $siteName -ResourceType Microsoft.Web/sites

}while($resource)

$azureSite = "https://"+$siteName+".azurewebsites.net"

##############
# MAIN Begins
##############

# Web App Deployment
####################

$tenant = $context.Tenant.TenantId

try
{
    Get-AzureRmResourceGroup -Name $resourceGroupName
}
catch
{
    New-AzureRmResourceGroup -Location $location -Name $resourceGroupName
}

Write-Output "Web App Deployment in progress...."

New-AzureRmResourceGroupDeployment -TemplateUri $templateUri -siteName $siteName -ResourceGroupName $resourceGroupName

Write-Output "Web App Deployed successfully!!"

# AAD Authentication
####################

Add-Type -AssemblyName System.Web

$clientSecret = [System.Web.Security.Membership]::GeneratePassword(30,2).ToString()

try
{

    Write-Host "Creating AzureAD application..."

    $adApp = New-AzureRmADApplication -DisplayName $siteName -HomePage $azureSite -IdentifierUris $azureSite -Password $clientSecret

    Write-Host "AzureAD application created succesfully!!"
}
catch
{
    # Delete the deployed web app if Azure AD application fails
    Remove-AzureRmResource -ResourceGroupName $resourceGroupName -ResourceName $siteName -ResourceType Microsoft.Web/sites -Force

    Write-Host "Faiure occured in Azure AD application....Try again!!"

    exit

}

$clientId = $adApp.ApplicationId

$servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $clientId

# Web App Configuration
#######################

Write-Host "Configuring deployed Web-App..."
$webApp = Get-AzureRMWebAppSlot -ResourceGroupName $resourceGroupName -Name $siteName -Slot production -WarningAction SilentlyContinue

$appSettingList = $webApp.SiteConfig.AppSettings

$appSettings = @{}
ForEach ($item in $appSettingList) {
    $appSettings[$item.Name] = $item.Value
}
$appSettings['ida:Tenant'] = $tenant
$appSettings['ida:Audience'] = $azureSite
$appSettings['ida:ServerName'] = $serverName
$appSettings['ida:Domain'] = $domain
$appSettings['ida:Username'] = $userName

$connStrings = @{}
$kvp = @{"Type"="Custom"; "Value"=$password}
$connStrings['ida:Password'] = $kvp

Set-AzureRMWebAppSlot -ResourceGroupName $resourceGroupName -Name $siteName -AppSettings $appSettings -ConnectionStrings $connStrings -Slot production -WarningAction SilentlyContinue

# Relay Namespace
###################

if(!$serviceName)
{
    $serviceName = $siteName + "sbn"
}
$resource = Find-AzureRmResource -ResourceNameContains $serviceName -ResourceType Microsoft.Relay/namespaces

if(!$resource)
{
    $serviceName = $siteName + "sbn"
    $properties = @{
                    "sku" = @{
            "name"= "Standard"
            "tier"= "Standard"
            "capacity"= 1
         }
    }
    try
    {
        Write-Host "Creating Service Bus namespace..."
        New-AzureRmResource -ResourceName $serviceName -Location $location -PropertyObject $properties -ResourceGroupName $resourceGroupName -ResourceType Microsoft.Relay/namespaces -ApiVersion 2016-07-01 -Force
    }
    catch
    {
        $err = $TRUE
        "Creation of Service Bus Namespace failed...Please create it manually from Azure Portal.`n"
    }

}

Write-Host "Note: Please Configure Hybrid connection in the Networking section of the application in Azure Portal to link to the on-premises system.`n"
Write-Host "App Details"
Write-Host "============"
Write-Host "App Name:"  $siteName
Write-Host "Client Id:"  $clientId
Write-Host "Client Secret:"  $clientSecret
Write-Host "URI:"  $azureSite
if(!$err)
{
    Write-Host "ServiceBus Namespace:"  $serviceName  
}

```
## <a name="next-steps"></a>Дальнейшие действия
[Настройка гибридного подключения](log-analytics-itsmc-connections.md#configure-the-hybrid-connection).

