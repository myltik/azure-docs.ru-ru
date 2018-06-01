---
title: Поддержка мультитенантности в Azure Stack | Документация Майкрософт
description: Узнайте, как организовать поддержку нескольких каталогов Azure Active Directory в Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: mabrigg
ms.openlocfilehash: 59b0f8e4c7234b246d4fb54d065ff318939e2662
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301841"
---
# <a name="enable-multi-tenancy-in-azure-stack"></a>Поддержка мультитенантности в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Вы можете настроить в Azure Stack поддержку пользователей из нескольких клиентов Azure Active Directory (Azure AD), чтобы они использовали службы в Azure Stack. Давайте рассмотрим следующий пример:

 - Вы являетесь администратором служб для contoso.onmicrosoft.com, где используется Azure Stack.
 - Мария является администратором каталога гостевых пользователей fabrikam.onmicrosoft.com. 
 - Компания, в которой работает Мария, использует службы IaaS и PaaS вашей компании. Вам нужно разрешить вход и использование ресурсов Azure Stack в contoso.onmicrosoft.com для пользователей из гостевого каталога (fabrikam.onmicrosoft.com).

Далее описана процедура, которая позволяет настроить мультитенантность в Azure Stack для описанного сценария.  В нашем примере вам и Марии придется выполнить ряд действий, чтобы пользователи из компании Fabrikam могли выполнять вход в развертывание Azure Stack компании Contoso и использовать ресурсы этого развертывания.  

## <a name="before-you-begin"></a>Перед началом работы
Перед настройкой мультитенантности в Azure Stack следует обратить внимание на ряд предварительных условий.
  
 - Вам необходимо согласовать с Марией административные действия в обоих каталогах: в Contoso, где установлена инфраструктура Azure Stack, и в гостевом каталоге Fabrikam.  
 - Убедитесь, что у вас [установлена](azure-stack-powershell-install.md) и [настроена](azure-stack-powershell-configure-admin.md) среда PowerShell для Azure Stack.
 - [Скачайте средства для Azure Stack](azure-stack-powershell-download.md) и импортируйте модули Connect и Identity.

    ````PowerShell
        Import-Module .\Connect\AzureStack.Connect.psm1
        Import-Module .\Identity\AzureStack.Identity.psm1
    ```` 
 - Марии потребуется доступ к Azure Stack через [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn). 

## <a name="configure-azure-stack-directory"></a>Настройка каталога Azure Stack
В этом разделе вы настроите Azure Stack, чтобы разрешить вход с учетными данными клиентов каталога Fabrikam Azure AD.

### <a name="onboard-guest-directory-tenant"></a>Подключение клиента гостевого каталога
Теперь нужно подключить клиент гостевого каталога Fabrikam к Azure Stack.  После этого Azure Resource Manager будет принимать пользователей и субъекты-службы из клиента гостевого каталога.

````PowerShell
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantToBeOnboarded = "fabrikam.onmicrosoft.com"

## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
$ResourceGroupName = "system.local"

Register-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
 -DirectoryTenantName $azureStackDirectoryTenant `
 -GuestDirectoryTenantName $guestDirectoryTenantToBeOnboarded `
 -Location "local" `
 -ResourceGroupName $ResourceGroupName
````



## <a name="configure-guest-directory"></a>Настройка гостевого каталога
Когда вы завершите все действия в каталоге Azure Stack, Мария должна подтвердить согласие на доступ Azure Stack к гостевому каталогу и зарегистрировать Azure Stack в этом гостевом каталоге. 

### <a name="registering-azure-stack-with-the-guest-directory"></a>Регистрация Azure Stack в гостевом каталоге
Когда администратор гостевого каталога предоставит согласие на доступ Azure Stack к каталогу Fabrikam, Марие следует зарегистрировать Azure Stack в клиенте гостевого каталога Fabrikam.

````PowerShell
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName `
 -Verbose 
````
## <a name="direct-users-to-sign-in"></a>Информирование пользователей о возможности входа
Итак, вы с Марией завершили все действия по подключению ее каталога, и теперь она может сообщить пользователям Fabrikam сведения о процедуре входа в ваш каталог.  Пользователи Fabrikam (то есть пользователи с суффиксом fabrikam.onmicrosoft.com) войти через страницу https://portal.local.azurestack.external.  

Всем [внешним участникам](../role-based-access-control/rbac-and-directory-admin-roles.md) каталога Fabrikam (без суффикса fabrikam.onmicrosoft.com, но включенным в каталог Fabrikam) Мария предоставит другой URL-адрес для входа: https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.  Если они попытаются использовать обычный URL-адрес, то будут перенаправлены к каталогу по умолчанию (Fabrikam) и увидят сообщение о том, что администратор не предоставил разрешение на подключение.

## <a name="next-steps"></a>Дальнейшие действия

- [Управление делегированными поставщиками](azure-stack-delegated-provider.md).
- [Основные понятия Azure Stack](azure-stack-key-features.md).
