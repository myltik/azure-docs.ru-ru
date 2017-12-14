---
title: "Краткое руководство. Установка и настройка PowerShell для Azure Stack | Документация Майкрософт"
description: "Сведения об установке и настройке PowerShell для Azure Stack."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 6996DFC1-5E05-423A-968F-A9427C24317C
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: mabrigg
ms.openlocfilehash: 0a85fc78d31c58ee83721d7dccbcdf46f98b3ddd
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2017
---
# <a name="get-up-and-running-with-powershell-in-azure-stack"></a>Начало работы с PowerShell в Azure Stack

*Область применения: интегрированные системы Azure Stack и комплект разработки Azure Stack*

Это краткое руководство поможет вам установить и настроить среду Azure Stack с помощью PowerShell. В этой статье используется скрипт, действие которого ограничивается только **оператором Azure Stack**.

Эта статья содержит сокращенное описание действий, приведенных в руководствах по [установке PowerShell]( azure-stack-powershell-install.md), [скачиванию средств]( azure-stack-powershell-download.md) и [настройке среды PowerShell оператора Azure Stack]( azure-stack-powershell-configure-admin.md). Используя приведенные здесь скрипты, вы можете настроить PowerShell для сред Azure Stack, развернутых с помощью Azure Active Directory или служб федерации Active Directory (AD FS).  


## <a name="set-up-powershell-for-azure-active-directory-based-deployments"></a>Настройка PowerShell для развертываний на основе Azure Active Directory

Войдите в пакет SDK для Azure Stack или внешний клиент на основе Windows (при подключении через VPN). Откройте сеанс интегрированной среды сценариев PowerShell с повышенными правами и выполните приведенный ниже скрипт. Обязательно обновите переменные **TenantName**, **ArmEndpoint** и **GraphAudience** в соответствии с конфигурацией вашей среды:

> [!IMPORTANT]
> Выпуск модуля AzureRM 1.2.11 PowerShell поставляется со списком критически важных изменений. Чтобы выполнить обновление с версии 1.2.10, см. [руководство по миграции](https://aka.ms/azspowershellmigration).

```powershell
# Specify Azure Active Directory tenant name.
$TenantName = "<mydirectory>.onmicrosoft.com"

# Set the module repository and the execution policy.
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions, and then run the following command:
Get-Module -ListAvailable | `
  Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.11 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module.
cd \

invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is adminvault.local.azurestack.external 
$KeyvaultDnsSuffix = "<Keyvault DNS suffix for your environment>"


# Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName $TenantName `
    -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID 
```

## <a name="set-up-powershell-for-ad-fs-based-deployments"></a>Настройка PowerShell для развертываний на основе AD FS

Вы можете использовать приведенный ниже скрипт, если работаете с Azure Stack с подключением к Интернету. Если же вы работаете с Azure Stack без подключения к Интернету, используйте [способ установки PowerShell без подключения](azure-stack-powershell-install.md#install-powershell-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity). Командлеты для настройки PowerShell будут теми же, что и в этом скрипте. Войдите в пакет SDK для Azure Stack или внешний клиент на основе Windows (при подключении через VPN). Откройте сеанс интегрированной среды сценариев PowerShell с повышенными правами и выполните приведенный ниже скрипт. Обязательно обновите переменные **ArmEndpoint** и **GraphAudience** в соответствии с конфигурацией вашей среды:

```powershell

# Set the module repository and the execution policy.
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions and run the following command:
Get-Module -ListAvailable -Name Azure* | `
  Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.11 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module.
cd \
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
$ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is adminvault.local.azurestack.external 
$KeyvaultDnsSuffix = "<Keyvault DNS suffix for your environment>"

# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack     
$TenantID = Get-AzsDirectoryTenantId `
    -ADFS `
    -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID
```

## <a name="test-the-connectivity"></a>Проверка подключения

Когда вы настроите PowerShell, вы можете протестировать конфигурацию, создав группу ресурсов:

```powershell
New-AzureRMResourceGroup -Name "ContosoVMRG" -Location Local
```

Когда группа ресурсов будет создана, в выходных данных командлета для свойства **состояния подготовки** будет указано значение, обозначающее **успешное** выполнение.

## <a name="next-steps"></a>Дальнейшие действия

* [Установка и настройка CLI](azure-stack-connect-cli.md)

* [Шаблоны разработки](user/azure-stack-develop-templates.md)







