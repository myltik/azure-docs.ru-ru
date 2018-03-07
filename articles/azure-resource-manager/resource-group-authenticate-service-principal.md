---
title: "Создание удостоверения для приложения Azure с помощью PowerShell | Документация Майкрософт"
description: "Использование Azure PowerShell для создания приложения Azure Active Directory и субъекта-службы с последующим предоставлением доступа к ресурсам посредством управления доступом на основе ролей. В статье показано, как выполнять проверку подлинности приложения с помощью пароля или сертификата."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/28/2017
ms.author: tomfitz
ms.openlocfilehash: 103e4ca5ffd6c9dfe5043af9d8f75763705eb939
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2018
---
# <a name="use-azure-powershell-to-create-a-service-principal-to-access-resources"></a>Использование Azure PowerShell для создания субъекта-службы и доступа к ресурсам

При наличии приложения или сценария, которому требуется доступ к ресурсам, можно настроить удостоверение для приложения и аутентифицировать его с помощью собственных учетных данных. Этот идентификатор известен как субъект-служба. Такой подход позволяет выполнить следующие действия:

* Назначить удостоверению приложения разрешения, которые отличаются от ваших разрешений. Как правило, приложение получает именно те разрешения, которые требуются для его работы.
* Использовать сертификат для аутентификации при выполнении автоматического сценария.

В этой статье показано, как настроить использование собственных учетных данных и удостоверения для приложения с помощью [Azure PowerShell](/powershell/azure/overview).

## <a name="required-permissions"></a>Необходимые разрешения
Для работы с этой статьей у вас должен быть достаточный уровень разрешений в подписках Azure Active Directory и Azure. В частности, вы должны иметь право на создание приложения в Azure Active Directory и назначение роли субъекту-службе. 

Проверить, есть ли у вас соответствующие разрешения, проще всего на портале. Ознакомьтесь с [проверкой наличия необходимых разрешений](resource-group-create-service-principal-portal.md#required-permissions).

Теперь перейдите к разделу для проверки подлинности с помощью следующего:

* [password](#create-service-principal-with-password)
* [самозаверяющий сертификат](#create-service-principal-with-self-signed-certificate)
* [сертификат из центра сертификации](#create-service-principal-with-certificate-from-certificate-authority)

## <a name="powershell-commands"></a>Команды PowerShell

Для настройки субъекта-службы используются следующие команды.

| Get-Help | ОПИСАНИЕ |
| ------- | ----------- | 
| [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) | Создает субъект-службу Azure Active Directory. |
| [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) | Присваивает указанную роль RBAC заданному субъекту в определенной области. |


## <a name="create-service-principal-with-password"></a>Создание субъекта-службы с использованием пароля

Чтобы создать субъект-службу с ролью "Участник" для своей подписки, используйте следующий сценарий. 

```powershell
Login-AzureRmAccount
$password = convertto-securestring {provide-password} -asplaintext -force
$sp = New-AzureRmADServicePrincipal -DisplayName exampleapp -Password $password
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

Пример бездействует 20 секунд, чтобы данные нового субъекта-службы распространились в Azure Active Directory. Если скрипт не подождет достаточно долго, появится следующее сообщение об ошибке: "Субъект {ИД} не существует в каталоге {DIR-ID}".

Следующий сценарий позволяет указать область, отличную от подписки по умолчанию, и повторяет назначение роли при возникновении ошибки.

```powershell
Param (

 # Use to set scope to resource group. If no value is provided, scope is set to subscription.
 [Parameter(Mandatory=$false)]
 [String] $ResourceGroup,

 # Use to set subscription. If no value is provided, default subscription is used. 
 [Parameter(Mandatory=$false)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $Password
)

 Login-AzureRmAccount
 Import-Module AzureRM.Resources

 if ($SubscriptionId -eq "") 
 {
    $SubscriptionId = (Get-AzureRmContext).Subscription.Id
 }
 else
 {
    Set-AzureRmContext -SubscriptionId $SubscriptionId
 }

 if ($ResourceGroup -eq "")
 {
    $Scope = "/subscriptions/" + $SubscriptionId
 }
 else
 {
    $Scope = (Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop).ResourceId
 }

 $SecurePassword = convertto-securestring $Password -asplaintext -force
 
 # Create Service Principal for the AD app
 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName -Password $SecurePassword
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
```

На что следует обратить внимание в сценарии:

* Чтобы предоставить удостоверению доступ к подписке по умолчанию, вам не нужно указывать параметры ResourceGroup и SubscriptionId.
* Укажите параметр ResourceGroup только в том случае, если требуется ограничить область назначения роли определенной группой ресурсов.
*  В этом примере субъект-служба добавляется в роль участника. Сведения о других ролях см. в статье [RBAC: встроенные роли](../active-directory/role-based-access-built-in-roles.md).
* Сценарий бездействует 15 секунд, чтобы данные нового субъекта-службы распространились в Azure Active Directory. Если скрипт не подождет достаточно долго, появится следующее сообщение об ошибке: "Субъект {ИД} не существует в каталоге {DIR-ID}".
* Чтобы предоставить субъекту-службе доступ к другим подпискам или группам ресурсов, выполните командлет `New-AzureRMRoleAssignment` повторно, указав другие области.


### <a name="provide-credentials-through-powershell"></a>Предоставление учетных данных с помощью PowerShell
Теперь следует войти в систему от имени приложения для выполнения операций. В качестве имени пользователя используйте значение `ApplicationId`, которое вы создали для приложения. Укажите пароль, который вы задали при создании учетной записи. 

```powershell   
$creds = Get-Credential
Login-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId {tenant-ID}
```

Идентификатор клиента не является конфиденциальным, поэтому его можно внедрить непосредственно в сценарий. Чтобы получить идентификатор клиента, используйте следующий командлет:

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

## <a name="create-service-principal-with-self-signed-certificate"></a>Создание субъекта-службы с самозаверяющим сертификатом

Чтобы создать субъект-службу с самозаверяющим сертификатом и ролью "Участник" для своей подписки, используйте следующий сценарий. 

```powershell
Login-AzureRmAccount
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleappScriptCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzureRMADServicePrincipal -DisplayName exampleapp -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

Пример бездействует 20 секунд, чтобы данные нового субъекта-службы распространились в Azure Active Directory. Если скрипт не подождет достаточно долго, появится следующее сообщение об ошибке: "Субъект {ИД} не существует в каталоге {DIR-ID}".

Следующий сценарий позволяет указать область, отличную от подписки по умолчанию, и повторяет назначение роли при возникновении ошибки. Необходимо наличие Azure PowerShell 2.0 в Windows 10 или Windows Server 2016.

```powershell
Param (

 # Use to set scope to resource group. If no value is provided, scope is set to subscription.
 [Parameter(Mandatory=$false)]
 [String] $ResourceGroup,

 # Use to set subscription. If no value is provided, default subscription is used. 
 [Parameter(Mandatory=$false)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName
 )

 Login-AzureRmAccount
 Import-Module AzureRM.Resources

 if ($SubscriptionId -eq "") 
 {
    $SubscriptionId = (Get-AzureRmContext).Subscription.Id
 }
 else
 {
    Set-AzureRmContext -SubscriptionId $SubscriptionId
 }

 if ($ResourceGroup -eq "")
 {
    $Scope = "/subscriptions/" + $SubscriptionId
 }
 else
 {
    $Scope = (Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop).ResourceId
 }

 $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleappScriptCert" -KeySpec KeyExchange
 $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
```

На что следует обратить внимание в сценарии:

* Чтобы предоставить удостоверению доступ к подписке по умолчанию, вам не нужно указывать параметры ResourceGroup и SubscriptionId.
* Укажите параметр ResourceGroup только в том случае, если требуется ограничить область назначения роли определенной группой ресурсов.
* В этом примере субъект-служба добавляется в роль участника. Сведения о других ролях см. в статье [RBAC: встроенные роли](../active-directory/role-based-access-built-in-roles.md).
* Сценарий бездействует 15 секунд, чтобы данные нового субъекта-службы распространились в Azure Active Directory. Если скрипт не подождет достаточно долго, появится следующее сообщение об ошибке: "Субъект {ИД} не существует в каталоге {DIR-ID}".
* Чтобы предоставить субъекту-службе доступ к другим подпискам или группам ресурсов, выполните командлет `New-AzureRMRoleAssignment` повторно, указав другие области.

Если вы **не используете Windows 10 или Windows Server 2016 Technical Preview**, скачайте сценарий [генератора самозаверяющих сертификатов](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) из центра сценариев Microsoft. Извлеките содержимое сценария и импортируйте требуемый командлет.

```powershell  
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```
  
Замените в сценарии следующие две строки, чтобы создать сертификат.
  
```powershell
New-SelfSignedCertificateEx  -StoreLocation CurrentUser -StoreName My -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Предоставление сертификата с помощью автоматизированного сценария PowerShell
При каждом входе в приложение AD в качестве субъекта-службы необходимо указать идентификатор клиента каталога. Клиент — это экземпляр Azure Active Directory. Если у вас только одна подписка, используйте команду:

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertSubject,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -match $CertSubject }).Thumbprint
 Login-AzureRmAccount -ServicePrincipal -CertificateThumbprint $Thumbprint -ApplicationId $ApplicationId -TenantId $TenantId
```

Идентификаторы приложения и клиента не являются конфиденциальными, поэтому их можно внедрить непосредственно в сценарий. Чтобы получить идентификатор клиента, используйте следующий командлет:

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

Чтобы получить идентификатор приложения, используйте следующий командлет:

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>Создание субъекта-службы с помощью сертификата из центра сертификации
Чтобы создать субъект-службу с помощью сертификата, выданного центром сертификации, используйте следующий сценарий:

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

 Login-AzureRmAccount
 Import-Module AzureRM.Resources
 Set-AzureRmContext -SubscriptionId $SubscriptionId
 
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

На что следует обратить внимание в сценарии:

* Доступ предоставляется в пределах подписки.
* В этом примере субъект-служба добавляется в роль участника. Сведения о других ролях см. в статье [RBAC: встроенные роли](../active-directory/role-based-access-built-in-roles.md).
* Сценарий бездействует 15 секунд, чтобы данные нового субъекта-службы распространились в Azure Active Directory. Если скрипт не подождет достаточно долго, появится следующее сообщение об ошибке: "Субъект {ИД} не существует в каталоге {DIR-ID}".
* Чтобы предоставить субъекту-службе доступ к другим подпискам или группам ресурсов, выполните командлет `New-AzureRMRoleAssignment` повторно, указав другие области.

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
 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $Thumbprint = $PFXCert.Thumbprint

 Login-AzureRmAccount -ServicePrincipal -CertificateThumbprint $Thumbprint -ApplicationId $ApplicationId -TenantId $TenantId
```

Идентификаторы приложения и клиента не являются конфиденциальными, поэтому их можно внедрить непосредственно в сценарий. Чтобы получить идентификатор клиента, используйте следующий командлет:

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
Remove-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -All
```

Чтобы добавить пароль, используйте следующую команду.

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -Password p@ssword!
```

Чтобы добавить значение сертификата, создайте самозаверяющий сертификат, как показано в этой статье. Затем используйте следующую команду.

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
```

## <a name="save-access-token-to-simplify-log-in"></a>Сохранение маркера доступа для быстрого входа
Чтобы не вводить учетные данные субъекта-службы каждый раз при входе, вы можете сохранить маркер доступа.

Сохраните профиль, чтобы повторно использовать текущий маркер доступа в следующем сеансе.
   
```powershell
Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
```
   
Откройте профиль и изучите его содержимое. Обратите внимание, что он содержит маркер доступа. Вместо выполнения входа вручную загрузите профиль.
   
```powershell
Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
```

> [!NOTE]
> Маркер доступа имеет ограниченный срок действия, поэтому сохраненный профиль работает только в пределах этого срока.
>  

Кроме того, чтобы выполнить вход, можно вызывать операции REST в PowerShell. Из ответа службы аутентификации можно получить маркер доступа, пригодный для использования в других операциях. Пример получения маркера доступа путем вызова операции REST приведен в разделе [Create the request](/rest/api/#create-the-request) (Создание запроса).

## <a name="debug"></a>Отладка

При создании субъекта-службы могут возникнуть следующие ошибки:

* **"Authentication_Unauthorized"** (Аутентификация не выполнена) или **"No subscription found in the context"** (В контексте не удалось найти подписку). — Эта ошибка может отобразиться, если учетная запись не имеет [необходимых разрешений](#required-permissions) в Azure Active Directory для регистрации приложения. Как правило, эта ошибка возникает в тех случаях, когда регистрировать приложения в Azure Active Directory могут только администраторы, а ваша учетная запись не является административной. Попросите администратора назначить вам роль администратора или разрешить пользователям регистрировать приложения.

* Ваша учетная запись **"не авторизована для выполнения действия 'Microsoft.Authorization/roleAssignments/write' с областью '/subscriptions/{guid}'."** Эта ошибка возникает, когда учетная запись не имеет достаточно разрешений для назначения роли удостоверению. Попросите администратора подписки назначить вам роль администратора доступа пользователей.

## <a name="sample-applications"></a>Примеры приложений
Сведения о входе в систему в качестве приложения с помощью различных платформ см. в следующих разделах:

* [.NET](/dotnet/azure/dotnet-sdk-azure-authenticate?view=azure-dotnet)
* [Java](/java/azure/java-sdk-azure-authenticate)
* [Node.js](/nodejs/azure/node-sdk-azure-get-started?view=azure-node-2.0.0)
* [Python](/python/azure/python-sdk-azure-authenticate?view=azure-python)
* [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>Дополнительная информация
* Подробные инструкции по интеграции приложения в Azure для управления ресурсами см. в [Управление ресурсами клиента с помощью Azure Active Directory и Resource Manager](resource-manager-api-authentication.md).
* Более подробное описание приложений и субъектов-служб см. в статье [Объекты приложений и объекты участников-служб](../active-directory/active-directory-application-objects.md). 
* Дополнительные сведения о проверке подлинности Azure Active Directory см. в статье [Сценарии проверки подлинности в Azure Active Directory](../active-directory/active-directory-authentication-scenarios.md).
* Список доступных действий, которые можно разрешить или запретить пользователям, см. в разделе [Операции поставщиков ресурсов Azure Resource Manager](../active-directory/role-based-access-control-resource-provider-operations.md).

