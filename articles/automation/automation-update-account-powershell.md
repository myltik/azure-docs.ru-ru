---
title: "Создание учетной записи запуска от имени службы автоматизации Azure с помощью PowerShell | Документация Майкрософт"
description: "В этой статье описывается обновление учетной записи службы автоматизации с помощью PowerShell для создания учетных записей запуска от имени в случае, если не удалось выполнить этот шаг во время изначального создания на портале."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/14/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: d34bb4a285f77f5c21a4af2d544bda3221d59e8d
ms.lasthandoff: 04/27/2017

---

# <a name="update-automation-run-as-account-using-powershell"></a>Обновление учетной записи запуска от имени службы автоматизации с помощью PowerShell
Имеющуюся учетную запись службы автоматизации можно обновлять с помощью PowerShell в следующих случаях:

* Вы создали учетную запись службы автоматизации, но не создали учетную запись запуска от имени.
* У вас уже есть учетная запись службы автоматизации для управления ресурсами Resource Manager, и вы хотите обновить ее, чтобы включить учетную запись запуска от имени в процедуру проверки подлинности модуля runbook.
* У вас есть учетная запись службы автоматизации для управления классическими ресурсами, и вы хотите обновить ее, чтобы использовать классическую учетную запись запуска от имени, а не создавать учетную запись и переносить в нее модули runbook и ресурсы.   
* Вы хотите создать учетную запись запуска от имени и классическую учетную запись запуска от имени, используя сертификат, выданный центром сертификации предприятия.

## <a name="prerequisites"></a>Предварительные требования

* Этот сценарий можно выполнять только в ОС Windows 10 и Windows Server 2016 с модулями Azure Resource Manager 2.01 или более поздней версии. Более ранние версии Windows не поддерживаются.
* Azure PowerShell 1.0 или более поздней версии. Сведения о выпуске PowerShell 1.0 см. в статье [Приступая к работе с командлетами Azure PowerShell](/powershell/azure/overview).
* Учетная запись службы автоматизации, указанная как значение для параметров *-AutomationAccountName* и *-ApplicationDisplayName* в приведенных ниже сценариях PowerShell.

Чтобы получить значения для параметров *SubscriptionID*, *ResourceGroup* и *AutomationAccountName*, которые являются обязательными для сценариев, сделайте следующее:
1. На портале Azure выберите свою учетную запись службы автоматизации в колонке **Учетная запись службы автоматизации** и выберите **Все параметры**. 
2. В колонке **Все параметры** в разделе **Параметры учетной записи** выберите **Свойства**. 
3. Обратите внимание на значения в колонке **Свойства**.

![Колонка "Свойства" учетной записи службы автоматизации](media/automation-sec-configure-azure-runas-account/automation-account-properties.png)  

## <a name="create-run-as-account-powershell-script"></a>Создание сценария PowerShell для учетной записи запуска от имени
Этот сценарий PowerShell включает в себя поддержку следующих конфигураций:

* создание учетной записи запуска от имени с использованием самозаверяющего сертификата;
* создание учетной записи запуска от имени и классической учетной записи запуска от имени с использованием самозаверяющего сертификата;
* создание учетной записи запуска от имени и классической учетной записи запуска от имени Azure с использованием корпоративного сертификата;
* создание учетной записи запуска от имени и классической учетной записи запуска от имени с использованием самозаверяющего сертификата в облаке Azure для государственных организаций.

В зависимости от выбранного варианта конфигурации сценарий создает приведенные ниже элементы.

**Для учетной записи запуска от имени:**

* Приложение Azure AD, используемое для экспорта самозаверяющего сертификата или открытого ключа корпоративного сертификата, и учетную запись субъекта-службы для этого приложения в Azure AD, а также назначает роль участника в текущей подписке. Вместо этой роли можно использовать роль владельца или любую другую роль. Дополнительные сведения см. в статье [Управление доступом на основе ролей в службе автоматизации Azure](automation-role-based-access-control.md).
* Ресурс сертификатов службы автоматизации с именем *AzureRunAsCertificate* в указанной учетной записи службы автоматизации. Этот ресурс содержит закрытый ключ сертификата, используемый в приложении Azure AD.
* Ресурс подключений службы автоматизации с именем *AzureRunAsConnection* в указанной учетной записи службы автоматизации. Этот ресурс содержит идентификаторы приложения, клиента и подписки, а также отпечаток сертификата.

**Для классической учетной записи запуска от имени Azure:**

* Ресурс сертификатов службы автоматизации с именем *AzureClassicRunAsCertificate* в указанной учетной записи службы автоматизации. Этот ресурс содержит закрытый ключ сертификата, используемый в сертификате управления.
* Ресурс подключений службы автоматизации с именем *AzureClassicRunAsConnection* в указанной учетной записи службы автоматизации. Этот ресурс содержит имя подписки, идентификатор подписки и имя ресурса сертификатов.

>[!NOTE]
> Если вы выбрали создание классической учетной записи запуска от имени, после выполнения сценария отправьте открытый сертификат (файл в формате CER) в хранилище управления подписки, в которой создана учетная запись службы автоматизации.
> 

1. Сохраните приведенный ниже сценарий на компьютере. В этом примере используйте имя файла *New-RunAsAccount.ps1*.

        #Requires -RunAsAdministrator
         Param (
        [Parameter(Mandatory=$true)]
        [String] $ResourceGroup,

        [Parameter(Mandatory=$true)]
        [String] $AutomationAccountName,

        [Parameter(Mandatory=$true)]
        [String] $ApplicationDisplayName,

        [Parameter(Mandatory=$true)]
        [String] $SubscriptionId,

        [Parameter(Mandatory=$true)]
        [Boolean] $CreateClassicRunAsAccount,

        [Parameter(Mandatory=$true)]
        [String] $SelfSignedCertPlainPassword,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPathForRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPlainPasswordForRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPathForClassicRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

        [Parameter(Mandatory=$false)]
        [ValidateSet("AzureCloud","AzureUSGovernment")]
        [string]$EnvironmentName="AzureCloud",

        [Parameter(Mandatory=$false)]
        [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
        )

        function CreateSelfSignedCertificate([string] $keyVaultName, [string] $certificateName, [string] $selfSignedCertPlainPassword,
                                      [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
        $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
           -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
           -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired)

        $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
        }

        function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {  
        $CurrentDate = Get-Date
        $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
        $KeyId = (New-Guid).Guid

        $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $KeyCredential.StartDate = $CurrentDate
        $KeyCredential.EndDate= [DateTime]$PfxCert.GetExpirationDateString()
        $KeyCredential.EndDate = $KeyCredential.EndDate.AddDays(-1)
        $KeyCredential.KeyId = $KeyId
        $KeyCredential.CertValue  = $keyValue

        # Use key credentials and create an Azure AD application
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $KeyCredential
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id

        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6)
        {
           Sleep -s 10
           New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
           $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
           $Retries++;
        }
           return $Application.ApplicationId.ToString();
        }

        function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName,[string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force   
        Remove-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
        }

        function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzureRmAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
        }

        Import-Module AzureRM.Profile
        Import-Module AzureRM.Resources

        $AzureRMProfileVersion= (Get-Module AzureRM.Profile).Version
        if (!(($AzureRMProfileVersion.Major -ge 2 -and $AzureRMProfileVersion.Minor -ge 1) -or ($AzureRMProfileVersion.Major -gt 2)))
        {
           Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
           return
        }

        Login-AzureRmAccount -EnvironmentName $EnvironmentName
        $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId

        # Create a Run As account by using a service principal
        $CertifcateAssetName = "AzureRunAsCertificate"
        $ConnectionAssetName = "AzureRunAsConnection"
        $ConnectionTypeName = "AzureServicePrincipal"

        if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
        $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
        $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
        } else {
          $CertificateName = $AutomationAccountName+$CertifcateAssetName
          $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
          $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
          $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
          CreateSelfSignedCertificate $KeyVaultName $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create a service principal
        $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
        $ApplicationId=CreateServicePrincipal $PfxCert $ApplicationDisplayName

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

        # Populate the ConnectionFieldValues
        $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
        $TenantID = $SubscriptionInfo | Select TenantId -First 1
        $Thumbprint = $PfxCert.Thumbprint
        $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

        if ($CreateClassicRunAsAccount) {
            # Create a Run As account by using a service principal
            $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
            $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
            $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
            $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
                    "Log in to the Microsoft Azure Management portal (https://manage.windowsazure.com) and select Settings -> Management Certificates." + [Environment]::NewLine +
                    "Then click Upload and upload the .cer format of #CERT#"

             if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
             $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
             $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
             $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
        } else {
             $ClassicRunAsAccountCertificateName = $AutomationAccountName+$ClassicRunAsAccountCertifcateAssetName
             $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
             $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
             $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
             $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
             CreateSelfSignedCertificate $KeyVaultName $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

        # Populate the ConnectionFieldValues
        $SubscriptionName = $subscription.Subscription.SubscriptionName
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName $ClassicRunAsAccountConnectionFieldValues

        Write-Host -ForegroundColor red $UploadMessage
        }

2. На компьютере запустите с повышенными правами **Windows PowerShell** с **начального** экрана.
3. Из оболочки командной строки с повышенными привилегиями перейдите в папку, которая содержит сценарий, созданный на этапе 1.  
4. Выполните этот сценарий, установив значения параметров в зависимости от требуемой конфигурации.

    **Создание учетной записи запуска от имени с использованием самозаверяющего сертификата**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false`

    **Создание учетной записи запуска от имени и классической учетной записи запуска от имени с использованием самозаверяющего сертификата**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true`

    **Создание учетной записи запуска от имени и классической учетной записи запуска от имени Azure с использованием корпоративного сертификата**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>`

    **Создание учетной записи запуска от имени и классической учетной записи запуска от имени с использованием самозаверяющего сертификата в облаке Azure для государственных организаций**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment`

    > [!NOTE]
    > После выполнения сценария появится запрос на проверку подлинности в Azure. Войдите в систему, используя учетную запись, которая является участником роли "Администраторы подписки" и соадминистратором подписки.
    >
    >

После выполнения сценария обратите внимание на следующее.
* Если вы создали классическую учетную запись запуска от имени с использованием самозаверяющего открытого сертификата (CER-файл), сценарий создает ее и сохраняет в папке временных файлов на компьютере в профиле пользователя, который выполнял сеанс PowerShell: *%Профиль_пользователя%\AppData\Local\Temp*.
* Если вы создали классическую учетную запись запуска от имени с использованием открытого корпоративного сертификата (CER-файл), используйте этот сертификат. Следуя инструкциям, [отправьте сертификат управления API на классический портал Azure](../azure-api-management-certs.md), а затем используйте [пример кода для проверки подлинности](automation-verify-runas-authentication.md#classic-run-as-authentication), чтобы проверить конфигурацию учетных данных с помощью классических ресурсов развертывания Azure. 
* Если вы *не* создали классическую учетную запись запуска от имени, выполните проверку подлинности с помощью ресурсов Resource Manager и проверьте конфигурацию учетных данных, используя [этот пример кода](automation-verify-runas-authentication.md#automation-run-as-authentication).

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о субъектах-службах см. в статье [Объекты приложения и субъекта-службы в Azure Active Directory](../active-directory/active-directory-application-objects.md).
* Дополнительные сведения о сертификатах и службах Azure см. в статье [Общие сведения о сертификатах для облачных служб Azure](../cloud-services/cloud-services-certs-create.md).

