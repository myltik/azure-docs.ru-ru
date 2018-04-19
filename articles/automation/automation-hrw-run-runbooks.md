---
title: Выполнение модулей runbook в гибридной рабочей роли Runbook в службе автоматизации Azure
description: Эта статья содержит сведения о выполнении модулей runbook на компьютерах в локальном центре обработки данных или поставщике облачных решений с помощью гибридной рабочей роли Runbook.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/05/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 1db1bf6f147ae4635b0d23e84faa67dbd8f786bc
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Запуск модулей runbook в гибридной рабочей роли Runbook

В структуре модулей Runbook, которые работают в службе автоматизации Azure, отсутствует разница между ними и теми модулями, которые работают в гибридном компоненте Runbook Worker. Модули runbook, которые используются в каждом из этих вариантов, скорее всего, будут значительно различаться. Причина — модули runbook для гибридной рабочей роли Runbook обычно управляют ресурсами на самом локальном компьютере или ресурсами в локальной среде, в которой они развернуты, а модули runbook в службе автоматизации Azure, как правило, управляют ресурсами в облаке Azure.

Когда вы создаете модули runbook для запуска на гибридной рабочей роли Runbook, настройте и протестируйте их выполнение на компьютере, где размещена эта гибридная рабочая роль. Этот компьютер имеет все модули PowerShell и доступ к сети, необходимые для доступа к локальным ресурсам и управления ими. Настроив и протестировав модуль runbook на компьютере с гибридной рабочей ролью, вы можете передать его в среду службы автоматизации Azure, где он будет доступен для запуска в гибридной рабочей роли. Важно помнить о незначительных нюансах, добавляемых учетной записью Local System, от имени которой выполняются задания, и принимать эти нюансы во внимание при создании модулей runbook для гибридной рабочей Runbook.

## <a name="starting-a-runbook-on-hybrid-runbook-worker"></a>Запуск runbook в гибридной рабочей роли Runbook

[Запуск модуля Runbook в службе автоматизации Azure](automation-starting-a-runbook.md) описываются различные методы запуска модуля Runbook. Гибридный компонент Runbook Worker добавляет параметр **RunOn** , при помощи которого можно указать имя группы гибридных компонентов Runbook Worker. Если группа указана, модуль runbook извлекается и запускается одним из компонентов Worker в этой группе. Если этот параметр не указан, то он будет запущен в службе автоматизации Azure в обычном режиме.

При запуске модуля Runbook на портале Azure отображается запрос **Выполнить в**, где вы можете выбрать в качестве значения варианты **Azure** или **Гибридная рабочая роль**. При выборе значения **Гибридный компонент Worker**можно выбрать группу из раскрывающегося списка.

Используйте параметр **RunOn**. Вы можете использовать следующую команду, чтобы с помощью Windows PowerShell запустить модуль runbook с названием Test-Runbook в группе гибридных рабочих ролей Runbook с именем MyHybridGroup.

```powershell-interactive
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> Параметр **RunOn** был добавлен в командлет **Start-AzureAutomationRunbook** в Microsoft Azure PowerShell версии 0.9.1. Если у вас установлена более ранняя версия, следует [загрузить последнюю версию](https://azure.microsoft.com/downloads/) . Эту версию достаточно установить только на той рабочей станции, на которой вы будете запускать модуль runbook с помощью Windows PowerShell. Вам не нужно устанавливать его на компьютер с компонентом Worker, если вы не собираетесь запускать модули Runbooks с этого компьютера. В настоящий момент вы не можете запускать модуль Runbook в гибридном компоненте Runbook Worker из другого модуля Runbook, поскольку для этого потребуется установить последнюю версию Azure Powershell в учетной записи службы автоматизации. При изменении версии она автоматически обновляется в службе автоматизации Azure и принудительно устанавливается в рабочих ролях.

## <a name="runbook-permissions"></a>Разрешения для модулей Runbook

Так как модули runbook, запущенные в гибридной рабочей роли Runbook, будут обращаться к ресурсам за пределами Azure, они не смогут использовать стандартный метод аутентификации в Azure. Модуль Runbook может предоставить собственную проверку подлинности для локальных ресурсов. Также можно указать учетную запись запуска от имени, чтобы предоставить контекст пользователя для всех модулей.

### <a name="runbook-authentication"></a>Проверка подлинности модуля Runbook

По умолчанию модули runbook выполняются в контексте учетной записи Local System на локальном компьютере. Поэтому они должны предоставлять собственные средства аутентификации для доступа к нужным ресурсам.

В модуле Runbook можно использовать ресурсы [учетных данных](automation-credentials.md) и [сертификата](automation-certificates.md) с командлетами, которые позволяют указывать такие данные для аутентификации доступа к разным ресурсам. В следующем примере показана часть модуля Runbook, предназначенная для перезапуска компьютера. Он извлекает учетные данные из набора учетных данных, а также имя компьютера из набора переменных, после чего использует эти значения в сочетании с командлетом Restart-Computer.

```powershell-interactive
$Cred = Get-AzureRmAutomationCredential -ResourceGroupName "ResourceGroup01" -Name "MyCredential"
$Computer = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" -Name  "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Вы можете также использовать [InlineScript](automation-powershell-workflow.md#inlinescript), что позволяет запускать блоки кода на другом компьютере с учетными данными, указанными в [общем параметре PSCredential](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="runas-account"></a>Учетная запись запуска от имени

Кроме варианта, когда модули Runbook выполняют собственную проверку подлинности на локальных ресурсах, вы можете указать для группы гибридных рабочих ролей учетную запись **Запуск от имени** . Здесь вы можете указать [ресурс учетных данных](automation-credentials.md), который будет использоваться для доступа к локальным ресурсам всеми модулями runbook, которые выполняются гибридными рабочими ролями Runbook, входящими в эту группу.

Имя пользователя для учетных данных должно быть представлено в одном из следующих форматов:

* домен\имя пользователя;
* username@domain
* имя пользователя (для локальных учетных записей на локальном компьютере).

Указать учетную запись запуска от имени для гибридной рабочей роли можно так.

1. Создайте [ресурс учетных данных](automation-credentials.md) с доступом к локальным ресурсам.
2. На портале Azure откройте учетную запись службы автоматизации.
3. Щелкните плитку **Группы гибридных рабочих ролей** и выберите группу.
4. Щелкните **Все параметры**, а затем — **Настройки группы гибридных рабочих ролей**.
5. Измените для параметра **Запуск от имени** значение **По умолчанию** на **Пользовательский**.
6. Выберите учетные данные и нажмите кнопку **Сохранить**.

### <a name="automation-run-as-account"></a>Учетная запись запуска от имени службы автоматизации

В ходе автоматизированного процесса сборки для развертывания ресурсов в Azure вам может потребоваться доступ к локальным системам для выполнения задачи или набора действий, входящих в последовательность развертывания. Чтобы использовать аутентификацию в Azure с использованием учетной записи запуска от имени, необходимо установить сертификат учетной записи запуска от имени.

Модуль runbook PowerShell с именем *Export-RunAsCertificateToHybridWorker* экспортирует сертификат запуска от имени из учетной записи службы автоматизации Azure, а затем скачивает его и импортирует в хранилище сертификатов локального компьютера, относящемся к гибридной рабочей роли, подключенной к этой учетной записи. После этого модуль проверяет, может ли рабочая роль успешно выполнить аутентификацию в Azure с использованием этой учетной записи запуска от имени.

```powershell-interactive
<#PSScriptInfo
.VERSION 1.0
.GUID 3a796b9a-623d-499d-86c8-c249f10a6986
.AUTHOR Azure Automation Team
.COMPANYNAME Microsoft
.COPYRIGHT
.TAGS Azure Automation
.LICENSEURI
.PROJECTURI
.ICONURI
.EXTERNALMODULEDEPENDENCIES
.REQUIREDSCRIPTS
.EXTERNALSCRIPTDEPENDENCIES
.RELEASENOTES
#>

<#
.SYNOPSIS
Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

.DESCRIPTION
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.
Run this runbook in the hybrid worker where you want the certificate installed.
This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running in the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
AUTHOR: Azure Automation Team
LASTEDIT: 2016.10.13
#>

[OutputType([string])]

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

# Stop on errors
$ErrorActionPreference = 'stop'

# Get the management certificate that will be used to make calls into Azure Service Management resources
$RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

# location to store temporary certificate in the Automation service host
$CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

# Save the certificate
$Cert = $RunAsCert.Export("pfx",$Password)
Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
$SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

# Test that authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzureRmContext -SubscriptionId $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm Azure Resource Manager calls are working
Get-AzureRmAutomationAccount | Select-Object AutomationAccountName
```

Сохраните модуль runbook *Export-RunAsCertificateToHybridWorker* на компьютер с расширением `.ps1`. Импортируйте модуль runbook в учетную запись автоматизации и замените значение переменной `$Password` своим паролем. Опубликуйте и запустите модуль runbook для группы гибридных рабочих ролей, в которой для запуска и аутентификации модулей runbook применяется учетная запись запуска от имени. Поток заданий сообщает о попытке импортировать сертификат в хранилище локального компьютера, а затем передает несколько строк с сообщениями в зависимости от числа учетных записей автоматизации, которые определены в подписке, и успешности аутентификации.

## <a name="job-behavior"></a>Поведение заданий

Задания обрабатываются в гибридных рабочих ролях Runbook немного иначе, чем при запуске в "песочницах" Azure. Одно из основных отличий заключается в том, что в гибридных рабочих ролях Runbook нет ограничений на продолжительность заданий. Если у вас есть runbook с длительным временем выполнения, вы наверняка захотите гарантировать его устойчивость к перезапуску, например к перезагрузке компьютера, на котором размещена гибридная рабочая роль. При перезагрузке компьютера, выполняющего гибридную рабочую роль, все выполняемые задания runbook запускаются заново, с самого начала или с последней контрольной точки, если это runbook рабочих процессов PowerShell. Если задание runbook перезапускается более 3 раз, его выполнение приостанавливается.

## <a name="troubleshooting-runbooks-on-hybrid-runbook-worker"></a>Устранение неполадок с модулями Runbook в гибридном компоненте Runbook Worker

Журналы сохраняются локально в каждом гибридном компоненте Worker по адресу C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Гибридные рабочие роли также записывают ошибки и события в журнал событий Windows, хранящийся в папке **Application and Services Logs\Microsoft-SMA\Operational**. События, связанные с модулями runbook, выполненными в рабочей роли, записываются в журнал в папке **Application and Services Logs\Microsoft-Automation\Operational**. Журнал **Microsoft-SMA** содержит многие дополнительные события, связанные с заданием runbook, помещенным в рабочую роль, и с обработкой runbook. Хотя журнал событий **Microsoft-Automation** почти не содержит событий, сведения о которых помогут в устранении неполадок при выполнении runbook, в нем сохраняются результаты задания runbook.

[Выходные данные Runbook и сообщения](automation-runbook-output-and-messages.md) отправляются в службу автоматизации Azure из гибридных рабочих ролей точно так же, как задания Runbook, которые выполняются в облаке. Потоки Verbose и Progress можно активировать точно так же, как и для других модулей Runbook.

Если модули runbook выполняются с ошибками, а в сводке о задании отображается состояние **Приостановлено**, изучите инструкции по устранению неполадок в разделе [Задание Runbook завершается с состоянием "Приостановлено"](automation-troubleshooting-hybrid-runbook-worker.md#a-runbook-job-terminates-with-a-status-of-suspended).

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения о разных методах запуска модуля см. в статье [Запуск модуля Runbook в службе автоматизации Azure](automation-starting-a-runbook.md).
* Описание разных методов работы с модулями Runbook PowerShell и рабочими процессами PowerShell в службе автоматизации Azure с использованием текстового редактора см. в статье [Изменение текстовых модулей Runbook в службе автоматизации Azure](automation-edit-textual-runbook.md).
