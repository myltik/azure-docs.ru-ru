---
title: Настройка среды PowerShell оператора Azure Stack | Документация Майкрософт
description: Сведения о том, как настроить среду PowerShell оператора Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 37D9CAC9-538B-4504-B51B-7336158D8A6B
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: mabrigg
ms.openlocfilehash: 52faec51b5bd6ceb8eebfb1a6a0d46b2e4b8efce
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="configure-the-azure-stack-operators-powershell-environment"></a>Настройка среды PowerShell оператора Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Azure Stack можно настроить для управления такими ресурсами, как создание предложений, планов, квот и предупреждений, с помощью PowerShell. Этот раздел поможет настроить среду оператора. Если вы хотите настроить PowerShell для пользовательской среды, см. статью [Настройка пользовательской среды PowerShell в Azure Stack](user/azure-stack-powershell-configure-user.md).

## <a name="prerequisites"></a>предварительным требованиям

Выполните следующие предварительные требования с помощью либо [пакета средств разработки](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), либо внешнего клиента на базе Windows (при [подключении через VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)). 

* Установите [совместимые с Azure Stack модули Azure PowerShell](azure-stack-powershell-install.md).  
* Скачайте [средства, необходимые для работы с Azure Stack](azure-stack-powershell-download.md).  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>Настройка среды оператора и вход в Azure Stack

Настройте среду оператора Azure Stack с помощью PowerShell. С учетом типа развертывания (Azure AD или AD FS) выполните один из следующих сценариев: замените tenantName Azure AD, конечную точку GraphAudience и значение ArmEndpoint собственной конфигурацией среды.

### <a name="azure-active-directory-azure-ad-based-deployments"></a>Развертывания на базе Azure Active Directory (Azure AD)

````powershell  
#  Create an administrator environment
Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint "https://adminmanagement.local.azurestack.external"

# Get the value of your Directory Tenant ID
$TenantID = Get-AzsDirectoryTenantId -AADTenantName "<mydirectorytenant>.onmicrosoft.com" -EnvironmentName AzureStackAdmin

# After registering the AzureRM environment, cmdlets can be 
# easily targeted at your Azure Stack instance.
Connect-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID
````


### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>Развертывания на базе служб федерации Active Directory (AD FS)

````powershell  
#  Create an administrator environment
Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint "https://adminmanagement.local.azurestack.external"

# Get the value of your Directory Tenant ID
$TenantID = Get-AzsDirectoryTenantId -ADFS -EnvironmentName AzureStackAdmin

# After registering the AzureRM environment, cmdlets can be 
# easily targeted at your Azure Stack instance.
Connect-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID
````

## <a name="test-the-connectivity"></a>Проверка подключения

Теперь, когда все настроено, можно создавать ресурсы в Azure Stack с помощью Azure PowerShell. Например, можно создать группу ресурсов для приложения и добавить виртуальную машину. Используйте команду ниже, чтобы создать группу ресурсов с именем MyResourceGroup.

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Дополнительная информация
* [Разработка шаблонов для Azure Stack](user/azure-stack-develop-templates.md)
* [Развертывание шаблонов с помощью PowerShell](user/azure-stack-deploy-template-powershell.md)
