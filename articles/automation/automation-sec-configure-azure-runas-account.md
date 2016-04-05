<properties
    pageTitle="Настройка в Azure учетной записи запуска от имени | Microsoft Azure"
    description="В этом руководстве приводятся пошаговые инструкции по созданию и тестированию, а также пример проверки подлинности с помощью субъекта безопасности в службе автоматизации Azure."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="03/30/2016"
    ms.author="magoedte"/>

# Проверка подлинности модулей Runbook в Azure с помощью учетной записи запуска от имени
В этом разделе показано, как настроить учетную запись службы автоматизации на портале Azure с помощью новой функции "Учетная запись запуска от имени" (также известной как субъект-служба). Выполнив эту настройку, вы можете получить доступ к ресурсам Azure Resource Manager в подписке с модулями Runbook службы автоматизации. Когда вы создаете учетную запись службы автоматизации на портале Azure, портал автоматически создает новый субъект-службу и по умолчанию назначает ему роль участника RBAC в подписке. Это упрощает процесс и помогает быстро начать построение и развертывание модулей Runbook для решения конкретных задач автоматизации.

Используя субъект-службу, вы можете:

* внедрить стандартную процедуру проверки подлинности в Azure при управлении ресурсами Azure ARM с помощью модулей Runbook;
* автоматизировать использование глобальных модулей Runbook, настроенных в службе оповещений Azure.


>[AZURE.NOTE] Для работы [функции интеграции оповещений](../azure-portal/insights-receive-alert-notifications.md) Azure вместе с глобальными модулями Runbook службы автоматизации требуется учетная запись автоматизации, настроенная для работы с субъектом-службой. Вы можете выбрать учетную запись службы автоматизации, в которой уже определен пользователь субъекта-службы, или создать новую.

Мы покажем, как создать учетную запись службы автоматизации с помощью портала Azure и Azure PowerShell, а также как пройти проверку подлинности с помощью этого субъекта-службы в модулях Runbook.

## Создание учетной записи службы автоматизации на портале Azure
В этом разделе указаны действия, с помощью которых на портале Azure можно создать новую учетную запись службы автоматизации Azure и субъект-службу.

>[AZURE.NOTE] Пользователь, выполняющий эти действия, *должен* быть участником роли "Администраторы подписки".

1. Войдите на портал Azure как администратор службы для подписки Azure, которой вы хотите управлять.
2. Выберите элемент **Учетные записи службы автоматизации**.
3. В колонке "Учетные записи службы автоматизации" щелкните **Добавить**.<br>![Добавление учетной записи службы автоматизации](media/automation-sec-configure-azure-runas-account/add-automation-account-properties.png)
4. В колонке **Добавление учетной записи службы автоматизации** в поле **Имя** введите имя новой учетной записи службы автоматизации.
5. Если у вас несколько подписок, укажите одну из них для новой учетной записи, а также новую или существующую **группу ресурсов** и **расположение** центра обработки данных Azure.
6. Убедитесь, что для параметра **Создать учетную запись запуска от имени в Azure** выбрано значение **Да**, и нажмите кнопку **Создать**.  
7. Создание учетной записи службы автоматизации в Azure можно отслеживать в разделе **Уведомления** в меню.

По завершении будет создана учетная запись службы автоматизации с ресурсом-контейнером сертификата с именем **AzureRunAsCertificate** и сроком действия в один год, а также ресурсом-контейнером подключения с именем **AzureRunAsConnection**.

## Обновление учетной записи службы автоматизации с помощью PowerShell
С помощью описанной ниже процедуры можно обновить существующую учетную запись службы автоматизации и создать субъект-службу, используя PowerShell. Эта процедура необходима, если вы создали учетную запись, но не создали учетную запись запуска от имени.

Прежде чем продолжить, проверьте, выполнены ли следующие действия:

1. Скачан и установлен [модуль Azure Active Directory для Windows PowerShell (64-разрядная версия)](http://go.microsoft.com/fwlink/p/?linkid=236297).
2. Создана учетная запись службы автоматизации. Эта учетная запись будет указана как значение для параметров -AutomationAccountName и -ApplicationDisplayName в приведенном ниже сценарии.

Сценарий PowerShell настроит следующую конфигурацию:

* Приложение Azure AD, которое может использовать для проверки подлинности самозаверяющий сертификат, и учетную запись субъекта-службы для этого приложения в Azure AD. Кроме того, роль участника (вместо нее может использоваться роль владельца или любая другая роль) будет назначена этой учетной записи в вашей текущей подписке. Дополнительные сведения см. в статье [Управление доступом на основе ролей в службе автоматизации Azure](../automation/automation-role-based-access-control.md).  
* Ресурс-контейнер сертификата службы автоматизации в указанной учетной записи службы автоматизации с именем **AzureRunAsCertificate**, содержащий сертификат, используемый в субъекте-службе.
* Ресурс-контейнер подключения к службе автоматизации в указанной учетной записи службы автоматизации с именем **AzureRunAsConnection**, содержащий идентификатор приложения, идентификатор клиента, идентификатор подписки и отпечаток сертификата.<br>

### Запуск сценария PowerShell

1. Сохраните приведенный ниже сценарий на компьютере. В этом примере используйте имя файла **New-AzureServicePrincipal.ps1**.  

 ```
    #Requires - RunAsAdministrator
    Param (
    [Parameter(Mandatory=$true)]
    [String] $ResourceGroup,

    [Parameter(Mandatory=$true)]
    [String] $AutomationAccountName,
   
    [Parameter(Mandatory=$true)]
    [String] $ApplicationDisplayName,
   
    [Parameter(Mandatory=$true)]
    [String] $CertPlainPassword,
   
    [Parameter(Mandatory=$false)]
    [int] $NoOfMonthsUntilExpired = 12
    )
   
    Login-AzureRmAccount
    Import-Module AzureRM.Resources
		
    $CurrentDate = Get-Date
    $EndDate = $CurrentDate.AddMonths($NoOfMonthsUntilExpired)
    $KeyId = (New-Guid).Guid
    $CertPath = Join-Path $env:TEMP ($ServicePrincipalDisplayName + ".pfx")

    $Cert = New-SelfSignedCertificate -DnsName $ServicePrincipalDisplayName -CertStoreLocation cert:\LocalMachine\My -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider"

    $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
    Export-PfxCertificate -Cert ("Cert:\localmachine\my" + $Cert.Thumbprint) -FilePath $CertPath -Password $CertPassword -Force | Write-Verbose

    $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate -ArgumentList @($CertPath, $CertPlainPassword)
    $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

    $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
    $KeyCredential.StartDate = $CurrentDate
    $KeyCredential.EndDate= $EndDate
    $KeyCredential.KeyId = $KeyId
    $KeyCredential.Type = "AsymmetricX509Cert"
    $KeyCredential.Usage = "Verify"
    $KeyCredential.Value = $KeyValue

    # Use Key credentials
    $Application = New-AzureRmADApplication -DisplayName $ServicePrincipalDisplayName -HomePage ("http://" + $ServicePrincipalDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $keyCredential

    New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId | Write-Verbose
    Get-AzureRmADServicePrincipal | Where {$_.ApplicationId -eq $Application.ApplicationId} | Write-Verbose

    $NewRole = $null
    $Retries = 0;
    While ($NewRole -eq $null -and $Retries -le 2)
    {
      # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
      Sleep 5
      New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose
      Sleep 5
      $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
      $Retries++;
    }

    # Get the tenant id for this subscription
    $SubscriptionInfo = Get-AzureRmSubscription
    $TenantID = $SubscriptionInfo | Select TenantId -First 1

    # Create the automation resources
    Remove-AzureRmAutomationVariable -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name ServicePrincipalApplicationID -Force -ErrorAction SilentlyContinue
    New-AzureRmAutomationVariable -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name ServicePrincipalApplicationID -Value $Application.ApplicationId -Encrypted $false | write-verbose
    Remove-AzureRmAutomationVariable -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name ServicePrincipalTenantID -Force -ErrorAction SilentlyContinue
    New-AzureRmAutomationVariable -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name ServicePrincipalTenantID -Value $TenantID.TenantId -Encrypted $false | write-verbose
    Remove-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name AzureRunAsCertificate -Force -ErrorAction SilentlyContinue | write-verbose
    New-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Path $CertPath -Name AzureRunAsCertificate -Password $CertPassword -Exportable | write-verbose

    # Create a Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    $ConnectionAssetName = "AzureRunAsConnection"
    $SubscriptionId = $SubscriptionInfo | Select SubscriptionId -First 1
    Remove-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -Force -ErrorAction SilentlyContinue
    $ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId.SubscriptionId}
    New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
 ```


2. На компьютере запустите **Windows PowerShell** с **начального** экрана с повышенными правами пользователя.
3. Из оболочки командной строки PowerShell с повышенными правами перейдите к папке, которая содержит сценарий, созданный на этапе 1, и выполните сценарий, изменив значения параметров *-ResourceGroup*, *-AutomationAccountName*, *-ApplicationDisplayName* и *-CertPlainPassword*.<br> ```.\New-AzureServicePrincipal.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -ApplicationDisplayName <DisplayNameofAutomationAccount> -CertPlainPassword "<StrongPassword>"``` <br>

    >[AZURE.NOTE] После выполнения сценария появится запрос на проверку подлинности в Azure. Вам *необходимо* войти с помощью учетной записи администратора службы в подписке. <br>

4. Когда сценарий успешно завершится, перейдите к следующему разделу для тестирования и проверки новой конфигурации учетных данных.

### Проверка подлинности от имени субъекта-службы
Далее мы выполним небольшой тест, чтобы убедиться, что вы сможете успешно пройти проверку подлинности с помощью нового субъекта-службы. Если вам не удалось пройти проверку подлинности, вернитесь к шагу 1 и снова проверьте каждое из выполненных ранее действий.

1. На портале Azure откройте учетную запись службы автоматизации, созданную ранее.  
2. Щелкните плитку **Модули Runbook**, чтобы открыть список модулей Runbook.
3. Создайте новый модуль Runbook, нажав кнопку **Добавить Runbook**, а затем в колонке **Добавление модуля Runbook** щелкните **Создать новый Runbook**.
4. Присвойте модулю Runbook имя *Test-SecPrin-Runbook* и в качестве **типа модуля Runbook** выберите PowerShell. Щелкните **Создать**, чтобы создать модуль Runbook.
5. В колонке **Изменение модуля Runbook PowerShell** добавьте на холст следующий код:<br>
       ```
       $Conn = Get-AutomationConnection -Name AzureRunAsConnection
       Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
       ```
6. Щелкните **Сохранить**, чтобы сохранить модуль Runbook.
7. Щелкните **область тестирования**, чтобы открыть колонку **Тест**.
8. Щелкните **Пуск**, чтобы начать тестирование.
9. При этом создается объект [Задание Runbook](automation-runbook-execution.md), а его состояние отображается на панели. Сначала задание получает состояние *В очереди*, показывающее, что задание ожидает доступа к рабочей роли Runbook в облаке. Как только исполнитель затребует задание, оно получит состояние *Запущено*, а с началом фактического выполнения модуля Runbook — состояние *Выполняется*.  
10. Когда задание модуля Runbook будет выполнено, на экране появится результат. В нашем случае должно отобразиться состояние **Завершено**.<br> ![Тестирование модуля Runbook субъекта безопасности](media/automation-sec-configure-azure-runas-account/runbook-test-results.png)<br>
11. Закройте колонку **Тест**, чтобы вернуться на холст.
12. Закройте колонку **Изменение модуля Runbook PowerShell**.
13. Закройте колонку **Test-SecPrin-Runbook**.

Приведенный выше код, с помощью которого можно проверить, правильно ли настроена новая учетная запись, используется во всех модулях Runbook PowerShell для проверки подлинности в службе автоматизации Azure. Такая проверка подлинности необходима для управления ресурсами ARM. Конечно, вы можете и дальше использовать для проверки подлинности учетную запись службы автоматизации, которую использовали до сих пор.

## Дальнейшие действия
- Дополнительные сведения о субъектах-службах см. в статье [Объекты приложений и объекты участников-служб](../active-directory/active-directory-application-objects.md).

<!---HONumber=AcomDC_0330_2016-->