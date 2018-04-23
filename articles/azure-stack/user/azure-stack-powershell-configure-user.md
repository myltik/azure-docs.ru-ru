---
title: Настройка пользовательской среды PowerShell в Azure Stack | Документация Майкрософт
description: Настройка пользовательской среды PowerShell в Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: F4ED2238-AAF2-4930-AA7F-7C140311E10F
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: mabrigg
ms.openlocfilehash: 44a5030bd9dfb694968a6b0f64ff9198ecccd84a
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="configure-the-azure-stack-users-powershell-environment"></a>Настройка пользовательской среды PowerShell в Azure Stack

Как пользователь Azure Stack вы можете настроить среду PowerShell для работы с Пакетом средств разработки Azure Stack. После этого PowerShell можно использовать для администрирования ресурсов Azure Stack. Вы сможете подписываться на предложения, создавать виртуальные машины, развертывать шаблоны Azure Resource Manager и т. д. В этом разделе описывается только использование пользовательских сред. Но вы также можете [настроить среду PowerShell оператора Azure Stack](../azure-stack-powershell-configure-admin.md). 

## <a name="prerequisites"></a>предварительным требованиям 

Выполните следующие предварительные требования с помощью либо [пакета средств разработки](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), либо внешнего клиента на базе Windows (при [подключении через VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)).

* Установите [совместимые с Azure Stack модули Azure PowerShell](azure-stack-powershell-install.md).  
* Скачайте [средства, необходимые для работы с Azure Stack](azure-stack-powershell-download.md). 

## <a name="configure-the-user-environment-and-sign-in-to-azure-stack"></a>Настройка пользовательской среды и вход в Azure Stack

Выполните один из следующих сценариев с учетом типа развертывания (Azure AD или AD FS), чтобы настроить PowerShell для работы в Azure Stack (обязательно замените значения AAD tenantName, GraphAudience endpoint и ArmEndpoint в соответствии с конфигурацией своей среды).

### <a name="azure-active-directory-aad-based-deployments"></a>Развертывания на базе Azure Active Directory (AAD)
       
  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack development kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAudience endpoint for your environment>"

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
  Connect-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantID 
   ```

### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>Развертывания на базе служб федерации Active Directory (AD FS) 
          
  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAudience endpoint for your environment>"

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
  Connect-AzureRmAccount `
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

Теперь, когда все настроено, вы можете создавать ресурсы в Azure Stack с помощью Azure PowerShell. Например, можно создать группу ресурсов для приложения и добавить виртуальную машину. Используйте команду ниже, чтобы создать группу ресурсов с именем MyResourceGroup.

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Дополнительная информация
* [Разработка шаблонов для Azure Stack](azure-stack-develop-templates.md)
* [Развертывание шаблонов с помощью PowerShell](azure-stack-deploy-template-powershell.md)
