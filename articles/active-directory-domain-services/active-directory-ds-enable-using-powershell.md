---
title: Включение доменных служб Azure Active Directory с помощью PowerShell | Документация Майкрософт
description: Включение доменных служб Azure Active Directory с помощью PowerShell
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: maheshu
ms.openlocfilehash: 386157360e8349c3df3b7351ae25b34ae9bdcc4b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34588511"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Включение доменных служб Azure Active Directory с помощью PowerShell
В этой статье показано, как включить доменные службы Azure Active Directory (AD) с помощью PowerShell.

## <a name="task-1-install-the-required-powershell-modules"></a>Задача 1. Установка необходимых модулей PowerShell

### <a name="install-and-configure-azure-ad-powershell"></a>Установка и настройка Azure PowerShell
Выполните инструкции из статьи [об установке модуля Azure AD PowerShell и подключении к Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Установка и настройка Azure PowerShell
Выполните инструкции из статьи [Установка и настройка Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="task-2-create-the-required-service-principal-in-your-azure-ad-directory"></a>Задача 2. Создание обязательного субъекта-службы в каталоге Azure AD
Введите следующую команду PowerShell, чтобы создать в каталоге Azure AD субъекта-службу, которая необходима для доменных служб Azure AD.
```powershell
# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId “2565bd9d-da50-47d4-8b85-4c97f669dc36”
```

## <a name="task-3-create-and-configure-the-aad-dc-administrators-group"></a>Задача 3. Создание и настройка группы "Администраторы контроллера домена AAD"
Теперь вам нужно создать группу администраторов, которая позволит делегировать административные задачи для управляемого домена.
```powershell
# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Создав группу, добавьте в нее несколько пользователей.
```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso100.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="task-4-register-the-azure-ad-domain-services-resource-provider"></a>Задача 4. Регистрация поставщика ресурсов доменных служб Azure AD
Введите следующую команду PowerShell, чтобы зарегистрировать поставщика ресурсов для доменных служб Azure AD:
```powershell
# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AAD
```

## <a name="task-5-create-a-resource-group"></a>Задача 5. Создание группы ресурсов
Выполните следующую команду PowerShell, чтобы создать группу ресурсов:
```powershell
$ResourceGroupName = "ContosoAaddsRg"
$AzureLocation = "westus"

# Create the resource group.
New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

В этой группе ресурсов вы можете создать виртуальную сеть и управляемый домен доменных служб Azure AD.


## <a name="task-6-create-and-configure-the-virtual-network"></a>Задача 6. Создание и настройка виртуальной сети
Теперь создайте виртуальную сеть, чтобы включить в ней доменные службы Azure AD. Для доменных служб Azure Active Directory нужно обязательно предоставить выделенную подсеть. Не развертывайте в эту подсеть виртуальные машины для рабочих нагрузок.

Введите следующие команды PowerShell, чтобы создать виртуальную сеть с выделенной подсетью для доменных служб Azure AD DS.

```powershell
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```


## <a name="task-7-provision-the-azure-ad-domain-services-managed-domain"></a>Задача 7. Подготовка управляемого домена доменных служб Azure AD
Введите следующие команды PowerShell, чтобы включить доменные службы Azure AD для каталога:

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso100.com"
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"
$AzureLocation = "westus"

# Enable Azure AD Domain Services for the directory.
New-AzureRmResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> **Не забудьте про дополнительные шаги настройки, которые нужно выполнить после подготовки управляемого домена.**
> Завершив подготовку управляемого домена, нужно выполнить следующие дополнительные задачи.
> * **[Обновите параметры DNS](active-directory-ds-getting-started-dns.md)** для виртуальной сети, чтобы виртуальные машины могли найти управляемый домен для присоединения к нему или для аутентификации.
* **[Включите синхронизацию паролей с доменными службами Azure AD](active-directory-ds-getting-started-password-sync.md)**, чтобы пользователи могли входить в управляемый домен с рабочими учетными данными.
>


## <a name="powershell-script"></a>Сценарий PowerShell
Ниже приведен сценарий PowerShell, который выполняет все описанные в этой статье задачи. Скопируйте его и сохраните в текстовый файл с расширением .ps1. Выполните сценарий в PowerShell или в интегрированной среде сценариев (ISE) PowerShell.

> [!NOTE]
> **Необходимые разрешения для запуска этого скрипта.** Чтобы включить доменные службы Azure AD, вам нужны полномочия глобального администратора для каталога Azure AD. Кроме того, нужны по меньшей мере права участника для виртуальной сети, в которой вы включаете доменные службы Azure AD.
>

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso100.onmicrosoft.com"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso100.com"
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"
$AzureLocation = "westus"

# Connect to your Azure AD directory.
Connect-AzureAD

# Login to your Azure subscription.
Connect-AzureRmAccount

# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId “2565bd9d-da50-47d4-8b85-4c97f669dc36”

# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"

# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq '$AaddsAdminUserUpn'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId

# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AAD

# Create the resource group.
New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $AzureLocation `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet

# Enable Azure AD Domain Services for the directory.
New-AzureRmResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> **Не забудьте про дополнительные шаги настройки, которые нужно выполнить после подготовки управляемого домена.**
> Завершив подготовку управляемого домена, нужно выполнить следующие дополнительные задачи.
> * Обновите параметры DNS для виртуальной сети, чтобы виртуальные машины могли найти управляемый домен для присоединения к нему или для аутентификации.
* Включите синхронизацию паролей с доменными службами Azure AD, чтобы пользователи могли входить в управляемый домен с рабочими учетными данными.
>

## <a name="next-steps"></a>Дополнительная информация
После создания управляемого домена выполните следующие операции настройки, чтобы использовать управляемый домен.

* [Обновление настроек DNS для виртуальной сети Azure](active-directory-ds-getting-started-dns.md)
* [Включение синхронизации паролей с доменными службами Azure AD](active-directory-ds-getting-started-password-sync.md)
