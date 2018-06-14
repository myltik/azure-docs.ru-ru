---
title: Создание удостоверения для приложения Azure с помощью PowerShell | Документация Майкрософт
description: Использование Azure PowerShell для создания приложения Azure Active Directory и субъекта-службы с последующим предоставлением доступа к ресурсам посредством управления доступом на основе ролей. В статье показано, как выполнять аутентификацию приложения с помощью сертификата.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/10/2018
ms.author: tomfitz
ms.openlocfilehash: 3dc62ba21458cc91d1858e11e7abbb6e96b0d5dd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358954"
---
# <a name="use-azure-powershell-to-create-a-service-principal-with-a-certificate"></a>Использование Azure PowerShell для создания субъекта-службы с сертификатом

При наличии приложения или сценария, которому требуется доступ к ресурсам, можно настроить удостоверение для приложения и аутентифицировать его с помощью собственных учетных данных. Этот идентификатор известен как субъект-служба. Такой подход позволяет выполнить следующие действия:

* Назначить удостоверению приложения разрешения, которые отличаются от ваших разрешений. Как правило, приложение получает именно те разрешения, которые требуются для его работы.
* Использовать сертификат для аутентификации при выполнении автоматического сценария.

> [!IMPORTANT]
> Вместо создания субъекта-службы вы можете применить управляемое удостоверение службы (MSI) Azure AD в качестве удостоверения приложения. MSI Azure AD используется в режиме общедоступной предварительной версии. Эта функция Azure Active Directory упрощает создание удостоверения для кода. Если код выполняется в службе, которая поддерживает MSI Azure AD и обращается к ресурсам, которые поддерживают аутентификацию Azure Active Directory, то MSI Azure AD будет оптимальным выбором. Дополнительные сведения об удостоверении MSI Azure AD, в том числе список поддерживаемых служб, см. в статье [Управляемое удостоверение службы (MSI) для ресурсов Azure](../active-directory/managed-service-identity/overview.md).

В этой статье показано, как создать субъект-службу, который выполняет аутентификацию с помощью сертификата. Настройка субъекта-службы с паролем описана в статье [Создание субъекта-службы Azure с помощью Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

Для выполнения задач из этой статься требуется Azure PowerShell [последней версии](/powershell/azure/get-started-azureps).

## <a name="required-permissions"></a>Необходимые разрешения

Для работы с этой статьей у вас должен быть достаточный уровень разрешений в подписках Azure Active Directory и Azure. В частности, вы должны иметь право на создание приложения в Azure Active Directory и назначение роли субъекту-службе.

Проверить, есть ли у вас соответствующие разрешения, проще всего на портале. Ознакомьтесь с [проверкой наличия необходимых разрешений](resource-group-create-service-principal-portal.md#required-permissions).

## <a name="create-service-principal-with-self-signed-certificate"></a>Создание субъекта-службы с самозаверяющим сертификатом

Ниже приводится простой пример сценария. В нем используется команда [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal), чтобы создать субъект-службу с самозаверяющим сертификатом, а затем выполняется [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) для присвоения этому субъекту-службе роли [участника](../role-based-access-control/built-in-roles.md#contributor). Назначение ролей ограничивается текущей выбранной подпиской Azure. Чтобы выбрать другую подписку, выполните [Set-AzureRmContext](/powershell/module/azurerm.profile/set-azurermcontext).

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" `
  -Subject "CN=exampleappScriptCert" `
  -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzureRMADServicePrincipal -DisplayName exampleapp `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

Пример бездействует 20 секунд, чтобы данные нового субъекта-службы распространились в Azure Active Directory. Если период ожидания окажется недостаточным, появится следующее сообщение об ошибке: "Субъект {идентификатор} не существует в каталоге {идентификатор_каталога}". Чтобы устранить эту ошибку, подождите немного и выполните команду **New-AzureRmRoleAssignment** еще раз.

Вы можете задать область назначения ролей для определенной группы ресурсов с помощью параметра **ResourceGroupName**. Кроме того, вы можете задать область для определенного ресурса с помощью параметров **ResourceType** и **ResourceName**. 

Если вы **не используете Windows 10 или Windows Server 2016**, скачайте сценарий [генератора самозаверяющих сертификатов](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) из центра скриптов Microsoft. Извлеките содержимое сценария и импортируйте требуемый командлет.

```powershell
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```

Замените в сценарии следующие две строки, чтобы создать сертификат.

```powershell
New-SelfSignedCertificateEx -StoreLocation CurrentUser `
  -Subject "CN=exampleapp" `
  -KeySpec "Exchange" `
  -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Предоставление сертификата с помощью автоматизированного сценария PowerShell

При каждом входе в приложение AD в качестве субъекта-службы необходимо указать идентификатор клиента каталога. Клиент — это экземпляр Azure Active Directory.

```powershell
$TenantId = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
$ApplicationId = (Get-AzureRmADApplication -DisplayNameStartWith exampleapp).ApplicationId

 $Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -match "CN=exampleappScriptCert" }).Thumbprint
 Connect-AzureRmAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>Создание субъекта-службы с помощью сертификата из центра сертификации

В приведенном ниже примере создается субъект-служба с сертификатом, выданным центром сертификации. Назначение ограничено указанной подпиской Azure. Также в примере этот субъект-служба добавляется в роль [участника](../role-based-access-control/built-in-roles.md#contributor). Если при назначении ролей возникнет ошибка, назначение выполняется повторно.

```powershell
Param (
 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword
 )

 Connect-AzureRmAccount
 Import-Module AzureRM.Resources
 Set-AzureRmContext -Subscription $SubscriptionId
 
 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force

 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName
 New-AzureRmADSpCredential -ObjectId $ServicePrincipal.Id -CertValue $KeyValue -StartDate $PFXCert.NotBefore -EndDate $PFXCert.NotAfter
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
 
 $NewRole
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Предоставление сертификата с помощью автоматизированного сценария PowerShell
При каждом входе в приложение AD в качестве субъекта-службы необходимо указать идентификатор клиента каталога. Клиент — это экземпляр Azure Active Directory.

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
 $PFXCert = New-Object `
  -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 `
  -ArgumentList @($CertPath, $CertPassword)
 $Thumbprint = $PFXCert.Thumbprint

 Connect-AzureRmAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

Идентификаторы приложения и клиента не являются конфиденциальными, поэтому их можно внедрить непосредственно в скрипт. Чтобы получить идентификатор клиента, используйте следующий командлет:

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

Чтобы получить идентификатор приложения, используйте следующий командлет:

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>Изменение учетных данных

Чтобы изменить учетные данные для приложения AD из-за нарушения безопасности или истечения их срока действия, используйте командлеты [Remove-AzureRmADAppCredential](/powershell/resourcemanager/azurerm.resources/v3.3.0/remove-azurermadappcredential) и [New-AzureRmADAppCredential](/powershell/module/azurerm.resources/new-azurermadappcredential).

Чтобы удалить все учетные данные для приложения, используйте следующую команду.

```powershell
Get-AzureRmADApplication -DisplayName exampleapp | Remove-AzureRmADAppCredential
```

Чтобы добавить значение сертификата, создайте самозаверяющий сертификат, как показано в этой статье. Затем используйте следующую команду.

```powershell
Get-AzureRmADApplication -DisplayName exampleapp | New-AzureRmADAppCredential `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
```

## <a name="debug"></a>Отладка

При создании субъекта-службы могут возникнуть следующие ошибки.

* **"Authentication_Unauthorized"** (Аутентификация не выполнена) или **"No subscription found in the context"** (В контексте не удалось найти подписку). — Эта ошибка может отобразиться, если учетная запись не имеет [необходимых разрешений](#required-permissions) в Azure Active Directory для регистрации приложения. Как правило, эта ошибка возникает в тех случаях, когда регистрировать приложения в Azure Active Directory могут только администраторы, а ваша учетная запись не является административной. Попросите администратора назначить вам роль администратора или разрешить пользователям регистрировать приложения.

* Ваша учетная запись **"не авторизована для выполнения действия 'Microsoft.Authorization/roleAssignments/write' с областью '/subscriptions/{guid}'."** Эта ошибка возникает, когда учетная запись не имеет достаточно разрешений для назначения роли удостоверению. Попросите администратора подписки назначить вам роль администратора доступа пользователей.

## <a name="next-steps"></a>Дополнительная информация
* Настройка субъекта-службы с паролем описана в статье [Создание субъекта-службы Azure с помощью Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).
* Подробные инструкции по интеграции приложения в Azure для управления ресурсами см. в [Управление ресурсами клиента с помощью Azure Active Directory и Resource Manager](resource-manager-api-authentication.md).
* Более подробное описание приложений и субъектов-служб см. в статье [Объекты приложений и объекты участников-служб](../active-directory/active-directory-application-objects.md). 
* Дополнительные сведения о проверке подлинности Azure Active Directory см. в статье [Сценарии проверки подлинности в Azure Active Directory](../active-directory/active-directory-authentication-scenarios.md).
