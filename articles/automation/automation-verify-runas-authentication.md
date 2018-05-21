---
title: Проверка конфигурации учетной записи службы автоматизации Azure
description: В этой статье описывается проверка правильной настройки конфигурации учетной записи службы автоматизации.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9bf5a3a1dc67752e3f911d07f23c7b408e6d38c3
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
---
# <a name="test-azure-automation-run-as-account-authentication"></a>Тестирование проверки подлинности учетной записи запуска от имени службы автоматизации Azure
После успешного создания учетной записи службы автоматизации можно выполнить простой тест, чтобы убедиться, что вы сможете успешно пройти проверку подлинности в Azure Resource Manager или в классическом развертывании Azure с помощью созданной или обновленной учетной записи запуска от имени службы автоматизации.    

## <a name="automation-run-as-authentication"></a>Проверка подлинности учетной записи запуска от имени службы автоматизации
Используйте пример кода ниже для [создания Runbook PowerShell](automation-creating-importing-runbook.md), чтобы выполнить проверку подлинности с помощью учетной записи запуска от имени. Используйте этот код и в пользовательских модулях Runbook, чтобы выполнить проверку подлинности ресурсов в диспетчере ресурсов и управлять ими с помощью учетной записи службы автоматизации.   

    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

        "Logging in to Azure..."
        Connect-AzureRmAccount `
           -ServicePrincipal `
           -TenantId $servicePrincipalConnection.TenantId `
           -ApplicationId $servicePrincipalConnection.ApplicationId `
           -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
       if (!$servicePrincipalConnection)
       {
          $ErrorMessage = "Connection $connectionName not found."
          throw $ErrorMessage
      } else{
          Write-Error -Message $_.Exception
          throw $_.Exception
      }
    }

    #Get all ARM resources from all resource groups
    $ResourceGroups = Get-AzureRmResourceGroup 

    foreach ($ResourceGroup in $ResourceGroups)
    {    
       Write-Output ("Showing resources in resource group " + $ResourceGroup.ResourceGroupName)
       $Resources = Find-AzureRmResource -ResourceGroupNameContains $ResourceGroup.ResourceGroupName | Select ResourceName, ResourceType
       ForEach ($Resource in $Resources)
       {
          Write-Output ($Resource.ResourceName + " of type " +  $Resource.ResourceType)
       }
       Write-Output ("")
    } 

Обратите внимание, что командлет **Connect-AzureRmAccount**, применяемый для проверки подлинности в runbook, использует набор параметров *ServicePrincipalCertificate*.  Он выполняет проверку подлинности с помощью сертификата субъекта-службы, а не учетных данных.  

При [запуске модуля runbook](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal) для проверки подлинности учетной записи запуска от имени будет создано [задание runbook](automation-runbook-execution.md), откроется страница "Задание", а состояние задания будет отображаться на плитке **Сводка по заданию**. Сначала задание получает состояние *В очереди* , указывающее на то, что задание ожидает, пока рабочая роль Runbook в облаке станет доступной. Как только рабочая роль затребует задание, оно получит состояние *Запущено*, а с началом фактического выполнения модуля Runbook — состояние *Выполняется*.  После выполнения задания Runbook должно отобразиться состояние **Выполнено**.

Чтобы просмотреть подробные результаты задания Runbook, щелкните плитку **Выходные данные** .  На странице **Выходные данные** должна быть информация о том, что модуль прошел проверку подлинности и вернул список всех ресурсов во всех группах ресурсов подписки.  

Не забудьте удалить блок кода, начиная с комментария `#Get all ARM resources from all resource groups`, при его повторном использовании для модулей Runbook.

## <a name="classic-run-as-authentication"></a>Проверка подлинности классической учетной записи запуска от имени
Используйте пример кода ниже для [создания Runbook PowerShell](automation-creating-importing-runbook.md), чтобы выполнить проверку подлинности с помощью классической учетной записи запуска от имени. Используйте этот код и в пользовательских модулях Runbook, чтобы выполнить проверку подлинности ресурсов и управлять ими в классической модели развертывания.  

    $ConnectionAssetName = "AzureClassicRunAsConnection"
    # Get the connection
    $connection = Get-AutomationConnection -Name $connectionAssetName        

    # Authenticate to Azure with certificate
    Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
    if ($Conn -eq $null)
    {
       throw "Could not retrieve connection asset: $ConnectionAssetName. Assure that this asset exists in the Automation account."
    }

    $CertificateAssetName = $Conn.CertificateAssetName
    Write-Verbose "Getting the certificate: $CertificateAssetName" -Verbose
    $AzureCert = Get-AutomationCertificate -Name $CertificateAssetName
    if ($AzureCert -eq $null)
    {
       throw "Could not retrieve certificate asset: $CertificateAssetName. Assure that this asset exists in the Automation account."
    }

    Write-Verbose "Authenticating to Azure with certificate." -Verbose
    Set-AzureSubscription -SubscriptionName $Conn.SubscriptionName -SubscriptionId $Conn.SubscriptionID -Certificate $AzureCert
    Select-AzureSubscription -SubscriptionId $Conn.SubscriptionID
    
    #Get all VMs in the subscription and return list with name of each
    Get-AzureVM | ft Name

При [запуске модуля Runbook](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal) для проверки подлинности учетной записи запуска от имени будет создано [задание Runbook](automation-runbook-execution.md), откроется страница "Задание", а состояние задания будет отображаться на плитке **Сводка по заданию**. Сначала задание получает состояние *В очереди* , указывающее на то, что задание ожидает, пока рабочая роль Runbook в облаке станет доступной. Как только рабочая роль затребует задание, оно получит состояние *Запущено*, а с началом фактического выполнения модуля Runbook — состояние *Выполняется*.  После выполнения задания Runbook должно отобразиться состояние **Выполнено**.

Чтобы просмотреть подробные результаты задания Runbook, щелкните плитку **Выходные данные** .  На странице **Выходные данные** должна быть информация о том, что модуль прошел проверку подлинности и вернул список всех виртуальных машин по имени, развернутых в подписке.  

Не забудьте удалить командлет **Get-AzureVM** при повторном использовании кода для модулей Runbook.

## <a name="next-steps"></a>Дополнительная информация
* Сведения о том, как начать работу с модулями Runbook, см. в статье [Мой первый модуль Runbook PowerShell](automation-first-runbook-textual-powershell.md).
* Дополнительные сведения о графической разработке в службе автоматизации Azure см. в [этой статье](automation-graphical-authoring-intro.md).
