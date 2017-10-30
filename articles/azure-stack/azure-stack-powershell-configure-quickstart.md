---
title: "Краткое руководство. Установка и настройка PowerShell для Azure Stack | Документация Майкрософт"
description: "Сведения об установке и настройке PowerShell для Azure Stack."
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: sngun
ms.openlocfilehash: 6e5c420ed80127213e38849ac1999bba199e36c2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="get-up-and-running-with-powershell-in-azure-stack"></a>Начало работы с PowerShell в Azure Stack

*Область применения: интегрированные системы Azure Stack и пакет SDK для Azure Stack*

Эта статья поможет вам быстро приступить к установке и настройке среды Azure Stack с помощью PowerShell. В ней используется скрипт, действие которого ограничивается только **оператором Azure Stack**.

Эта статья содержит сокращенное описание действий, приведенных в руководствах по [установке PowerShell]( azure-stack-powershell-install.md), [скачиванию средств]( azure-stack-powershell-download.md) и [настройке среды PowerShell оператора Azure Stack]( azure-stack-powershell-configure-admin.md). Используя приведенные здесь скрипты, вы можете настроить PowerShell для сред Azure Stack, развернутых с помощью Azure Active Directory или служб федерации Active Directory (AD FS).  


## <a name="set-up-powershell-for-aad-based-deployments"></a>Настройка PowerShell для развертываний на основе AAD

Войдите в пакет SDK для Azure Stack или внешний клиент на основе Windows (при подключении через VPN). Откройте сеанс интегрированной среды сценариев PowerShell с повышенными привилегиями и выполните следующий скрипт (обязательно обновите переменные TenantName ArmEndpoint и GraphAudience в соответствии с конфигурацией среды):

```powershell
# Specify Azure Active Directory tenant name
$TenantName = "<mydirectory>.onmicrosoft.com"

# Set the module repository and the execution policy
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions and run the following command:
Get-Module -ListAvailable | `
  where-Object {$_.Name -like “Azure*”} | `
  Uninstall-Module

# Install PowerShell for Azure Stack
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.10 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module
cd \

invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module `
  .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
$ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
$GraphAudience = "<GraphAuidence endpoint for your environment>"

# Configure the Azure Stack operator’s PowerShell environment.
Add-AzureRMEnvironment `
  -Name "AzureStackAdmin" `
  -ArmEndpoint $ArmEndpoint

Set-AzureRmEnvironment `
  -Name "AzureStackAdmin" `
  -GraphAudience $GraphAudience

$TenantID = Get-AzsDirectoryTenantId `
  -AADTenantName $TenantName `
  -EnvironmentName AzureStackAdmin

# Sign-in to the operator's portal.
Login-AzureRmAccount `
  -EnvironmentName "AzureStackAdmin" `
  -TenantId $TenantID 

```

## <a name="set-up-powershell-for-ad-fs-based-deployments"></a>Настройка PowerShell для развертываний на основе AD FS 

Войдите в пакет SDK для Azure Stack или внешний клиент на основе Windows (при подключении через VPN). Откройте сеанс интегрированной среды сценариев PowerShell с повышенными привилегиями и выполните следующий скрипт (обязательно обновите переменные ArmEndpoint и GraphAudience в соответствии с конфигурацией среды):

```powershell

# Set the module repository and the execution policy
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions and run the following command:
Get-Module -ListAvailable | `
  where-Object {$_.Name -like “Azure*”} | `
  Uninstall-Module

# Install PowerShell for Azure Stack
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.10 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module
cd \

invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module `
  .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
$ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
$GraphAudience = "<GraphAuidence endpoint for your environment>"

# Configure the cloud administrator’s PowerShell environment.
Add-AzureRMEnvironment `
  -Name "AzureStackAdmin" `
  -ArmEndpoint $ArmEndpoint

Set-AzureRmEnvironment `
  -Name "AzureStackAdmin" `
  -GraphAudience $GraphAudience `
  -EnableAdfsAuthentication:$true

$TenantID = Get-AzsDirectoryTenantId `
  -ADFS `
  -EnvironmentName "AzureStackAdmin"

# Sign-in to the operator's portal.
Login-AzureRmAccount `
  -EnvironmentName "AzureStackAdmin" `
  -TenantId $TenantID 

```

## <a name="test-the-connectivity"></a>Проверка подключения

Когда вы настроите PowerShell, вы можете протестировать конфигурацию, создав группу ресурсов:

```powershell
New-AzureRMResourceGroup -Name "ContosoVMRG" -Location Local
```

Когда группа ресурсов будет создана, в выходных данных командлета для свойства состояния подготовки будет указано значение, указывающее на успешное выполнение.

## <a name="next-steps"></a>Дальнейшие действия

* [Установка и настройка CLI](azure-stack-connect-cli.md)

* [Шаблоны разработки](user/azure-stack-develop-templates.md)







