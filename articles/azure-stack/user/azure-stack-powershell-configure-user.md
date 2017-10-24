---
title: "Настройка пользовательской среды PowerShell в Azure Stack | Документация Майкрософт"
description: "Настройка пользовательской среды PowerShell в Azure Stack"
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
ms.date: 08/18/2017
ms.author: sngun
ms.openlocfilehash: 74c34fccffcea6aae370d881791093f9b58a5f3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="configure-the-azure-stack-users-powershell-environment"></a>Настройка пользовательской среды PowerShell в Azure Stack

Как пользователь Azure Stack вы можете настроить среду PowerShell для работы с пакетом SDK для Azure Stack. После этого PowerShell можно использовать для администрирования ресурсов Azure Stack. Вы сможете подписываться на предложения, создавать виртуальные машины, развертывать шаблоны Azure Resource Manager и т. д. В этом разделе описывается использование пользовательских сред. Но вы также можете [настроить среду PowerShell оператора Azure Stack](../azure-stack-powershell-configure-admin.md). 

## <a name="prerequisites"></a>Предварительные требования 

Настройте необходимые компоненты с помощью [пакета средств разработки](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) или внешнего клиента на базе Windows (при [подключении через VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)):

* Установите [модули Azure PowerShell, совместимые с Azure Stack](azure-stack-powershell-install.md).  
* Скачайте [средства, необходимые для работы с Azure Stack](azure-stack-powershell-download.md). 

## <a name="configure-the-user-environment-and-sign-in-to-azure-stack"></a>Настройка пользовательской среды и вход в Azure Stack

Выполните один из следующих скриптов с учетом типа развертывания (Azure AD или AD FS), чтобы настроить PowerShell для работы в Azure Stack (обязательно замените значения AAD tenantName, GraphAudience endpoint и ArmEndpoint в соответствии с конфигурацией своей среды):

### <a name="azure-active-directory-aad-based-deployments"></a>Развертывания на базе Azure Active Directory (AAD)
       
  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack development kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAuidence endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  # Set the GraphEndpointResourceId value
  Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience

  # Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
    -EnvironmentName "AzureStackUser"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantID 
   ```

### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>Развертывания на базе служб федерации Active Directory (AD FS) 
          
  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAuidence endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  # Set the GraphEndpointResourceId value
  Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience `
    -EnableAdfsAuthentication:$true

  # Get the Active Directory tenantId that is used to deploy Azure Stack     
  $TenantID = Get-AzsDirectoryTenantId `
    -ADFS `
    -EnvironmentName "AzureStackUser"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantID 
  ```

## <a name="register-resource-providers"></a>Регистрация поставщиков ресурсов

При использовании созданной подписки пользователя, которая не содержит развернутые с помощью портала ресурсы, поставщики ресурсов не регистрируются автоматически. Вам нужно явно зарегистрировать их с помощью следующего скрипта:

```powershell
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider -Force
    } 
```

## <a name="test-the-connectivity"></a>Проверка подключения

Теперь, когда все настроено, вы можете создавать ресурсы в Azure Stack с помощью Azure PowerShell. Например, можно создать группу ресурсов для приложения и добавить виртуальную машину. Используйте следующую команду, чтобы создать группу ресурсов с именем MyResourceGroup.

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Дальнейшие действия
* [Разработка шаблонов для Azure Stack](azure-stack-develop-templates.md)
* [Развертывание шаблонов с помощью PowerShell](azure-stack-deploy-template-powershell.md)
