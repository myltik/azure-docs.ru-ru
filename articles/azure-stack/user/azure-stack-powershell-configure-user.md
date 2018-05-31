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
ms.date: 5/15/2018
ms.author: mabrigg
ms.reviewer: Balsu.G
ms.openlocfilehash: 2655b682d35dd1879c649ed58d524ecd80808896
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34258028"
---
# <a name="configure-the-azure-stack-users-powershell-environment"></a>Настройка пользовательской среды PowerShell в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Инструкции в этой статье позволяют настроить среду PowerShell для пользователя Azure Stack.
После настройки среды можно будет использовать PowerShell для управления ресурсами Azure Stack. Например, с помощью PowerShell можно будет подписываться на предложения, создавать виртуальные машины и развертывать шаблоны Azure Resource Manager.

>[!NOTE]
>В этой статье рассматриваются пользовательские среды Azure Stack. Вы также можете настроить среду PowerShell для облачного оператора. Это описывается в разделе [Настройка среды PowerShell оператора Azure Stack](../azure-stack-powershell-configure-admin.md).

## <a name="prerequisites"></a>предварительным требованиям

Приведенные ниже предварительные требования можно выполнить либо с помощью [пакета средств разработки](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), либо с помощью внешнего клиента на основе Windows, если используется [VPN-подключение](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

* Установите [совместимые с Azure Stack модули Azure PowerShell](azure-stack-powershell-install.md).
* Скачайте [средства, необходимые для работы с Azure Stack](azure-stack-powershell-download.md).

## <a name="configure-the-user-environment-and-sign-in-to-azure-stack"></a>Настройка пользовательской среды и вход в Azure Stack

В зависимости от типа развертывания Azure Stack (Azure AD или AD FS), выполните один из следующих сценариев, чтобы настроить PowerShell для Azure Stack.

Обязательно замените приведенные ниже переменные сценария значениями из конфигурации Azure Stack:

* AAD tenantName
* GraphAudience endpoint
* ArmEndpoint

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
  Login-AzureRmAccount `
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
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantID
  ```

## <a name="register-resource-providers"></a>Регистрация поставщиков ресурсов

Поставщики ресурсов не регистрируются автоматически в новых подписках пользователей, для которых на портале не развернуты какие-либо ресурсы. Можно явно зарегистрировать поставщик ресурсов, выполнив следующий сценарий.

```powershell
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider -Force
    }
```

## <a name="test-the-connectivity"></a>Проверка подключения

Настроив все необходимое, проверьте возможности подключения, воспользовавшись PowerShell для создания ресурсов в Azure Stack. В целях проверки создайте группу ресурсов для приложения и добавьте в нее виртуальную машину. Используйте команду ниже, чтобы создать группу ресурсов MyResourceGroup.

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Дополнительная информация

* [Разработка шаблонов для Azure Stack](azure-stack-develop-templates.md)
* [Развертывание шаблонов с помощью PowerShell](azure-stack-deploy-template-powershell.md)
