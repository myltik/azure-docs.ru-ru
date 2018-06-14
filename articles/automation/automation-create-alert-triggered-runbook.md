---
title: Использование оповещения для активации runbook службы автоматизации Azure
description: Узнайте, как настроить запуск runbook при появлении оповещения Azure.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e791cddb07d3204f807dbeff98b7fc69419ae23c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34194042"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Использование оповещения для активации runbook службы автоматизации Azure

С помощью [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md?toc=%2fazure%2fautomation%2ftoc.json) можно отслеживать метрики и журналы базового уровня большинства служб Microsoft Azure. Модули runbook службы автоматизации Azure можно вызывать с помощью [групп действий](../monitoring-and-diagnostics/monitoring-action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) или с помощью классических оповещений, чтобы автоматизировать задачи на основе оповещений. В этой статье показано, как настроить и запустить runbook с помощью оповещений.

## <a name="alert-types"></a>Типы оповещений

Модули runbook службы автоматизации можно использовать с тремя типами оповещений:
* классические оповещения о метриках;
* оповещения журнала действий;
* оповещения на основе метрик почти в реальном времени.

Когда оповещение вызывает runbook, фактически осуществляется вызов веб-перехватчика с помощью HTTP-запроса POST. Текст запроса POST содержит объект в формате JSON с полезными свойствами, связанными с оповещением. В следующей таблице приведены ссылки на схему полезных данных для каждого типа оповещения.

|Предупреждение  |ОПИСАНИЕ|Схема полезных данных  |
|---------|---------|---------|
|[Классическое оповещение о метриках](../monitoring-and-diagnostics/insights-alerts-portal.md?toc=%2fazure%2fautomation%2ftoc.json)    |Отправляет уведомление, когда метрика уровня платформы соответствует определенному условию. Например, если значение метрики **% ЦП** на виртуальной машине превышает **90** в течение последних 5 минут.| [Схема полезных данных классического оповещения о метрике](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)         |
|[Оповещение журнала действий](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Отправляет уведомление, когда какое-либо новое событие в журнале действий Azure соответствует определенным условиям. Например, если в **myProductionResourceGroup** выполняется операция `Delete VM` или возникает новое событие службы "Работоспособность служб Azure" в состоянии **Активное**.| [Схема полезных данных оповещения журнала действий](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)        |
|[Оповещения на основе метрик практически в реальном времени](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Отправляет уведомление быстрее, чем оповещения о метриках, когда одна или несколько метрик уровня платформы удовлетворяют указанным условиям. Например, если за последние 5 минут значение метрики **% ЦП** на виртуальной машине превышает **90** и значение метрики **Сеть (входящий трафик)** превышает **500 МБ**.| [Схема полезны данных оповещения на основе метрик почти в реальном времени](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)          |

Так как данные, предоставляемые для каждого типа оповещения, различны, оповещения разных типов обрабатываются по-разному. В следующем разделе вы узнаете, как создать runbook для обработки различных типов оповещений.

## <a name="create-a-runbook-to-handle-alerts"></a>Создание модуля Runbook для обработки оповещений

Чтобы применять автоматизацию к оповещениям, нужен runbook с логикой для управления полезными данными JSON оповещения, которые передаются в runbook. Приведенный ниже пример модуля Runbook должен быть вызван из оповещения Azure. 

Как описано в предыдущем разделе, каждый тип оповещения имеет свою схему. Сценарий принимает данные веб-перехватчика во входном параметре runbook `WebhookData` из оповещения. Затем сценарий оценивает полезные данные JSON, чтобы определить, какой тип оповещения использован. 

В этом примере используется оповещение из виртуальной машины. Он извлекает данные виртуальной машины из полезных данных и использует эту информацию для остановки виртуальной машины. Подключение должно быть настроено в учетной записи службы автоматизации, в которой выполняется runbook.

Runbook будет использовать [учетную запись от имени](automation-create-runas-account.md) **AzureRunAsConnection** для аутентификации в Azure, чтобы выполнить действие управления с виртуальной машиной.

Используйте этот пример для создания runbook **Stop-AzureVmInResponsetoVMAlert**. Можно изменить сценарий PowerShell и использовать его для множества разных ресурсов.

1. Перейдите к своей учетной записи службы автоматизации Azure.
1. В разделе **Автоматизация процессов** щелкните **Модули Runbook**.
1. В верхней части списка модулей runbook выберите **Добавить Runbook**. 
1. На странице **Добавление Runbook** выберите **Быстрое создание**.
1. Введите имя runbook: **Stop-AzureVmInResponsetoVMAlert**. Для типа runbook выберите значение **PowerShell**. Затем выберите **Создать**.  
1. Скопируйте следующий пример PowerShell в область **Изменение**. 

    ```powershell-interactive
    <#
    .SYNOPSIS
    This runbook stops a resource management VM in response to an Azure alert trigger.

    .DESCRIPTION
    This runbook stops a resource management VM in response to an Azure alert trigger.
    The input is alert data that has the information required to identify which VM to stop.
    
    DEPENDENCIES
    - The runbook must be called from an Azure alert via a webhook.
    
    REQUIRED AUTOMATION ASSETS
    - An Automation connection asset called "AzureRunAsConnection" that is of type AzureRunAsConnection.
    - An Automation certificate asset called "AzureRunAsCertificate".

    .PARAMETER WebhookData
    Optional. (The user doesn't need to enter anything, but the service always passes an object.)
    This is the data that's sent in the webhook that's triggered from the alert.

    .NOTES
    AUTHOR: Azure Automation Team
    LASTEDIT: 2017-11-22
    #>

    [OutputType("PSAzureOperationResponse")]

    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData
    )

    $ErrorActionPreference = "stop"

    if ($WebhookData)
    {
        # Get the data object from WebhookData.
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

        # Get the info needed to identify the VM (depends on the payload schema).
        $schemaId = $WebhookBody.schemaId
        Write-Verbose "schemaId: $schemaId" -Verbose
        if ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # The schema isn't supported.
            Write-Error "The alert data schema - $schemaId - is not supported."
        }

        Write-Verbose "status: $status" -Verbose
        if ($status -eq "Activated")
        {
            $ResourceType = $AlertContext.resourceType
            $ResourceGroupName = $AlertContext.resourceGroupName
            $SubId = $AlertContext.subscriptionId
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose

            # Use this only if this is a resource management VM.
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is the VM.
                Write-Verbose "This is a resource management VM." -Verbose

                # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.
                Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
                $ConnectionAssetName = "AzureRunAsConnection"
                Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
                $Conn = Get-AutomationConnection -Name $ConnectionAssetName
                if ($Conn -eq $null)
                {
                    throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
                }
                Write-Verbose "Authenticating to Azure with service principal." -Verbose
                Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
                Write-Verbose "Setting subscription to work against: $SubId" -Verbose
                Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

                # Stop the VM.
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType isn't supported.
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated', so no action taken.
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```
1. Нажмите кнопку **Опубликовать**, чтобы сохранить и опубликовать runbook.

## <a name="create-an-action-group"></a>Создание группы действий

Группа действий представляет собой коллекцию действий, которые активируются оповещением. Модули runbook — это лишь одно из многих действий, доступных для групп действий.

1. На портале Azure выберите **Монитор** > **Параметры** > **Группы действий**.
1. Выберите **Добавить группу действий**, а затем введите требуемые сведения.  
    1. В поле **Имя группы действий** введите имя.
    1. В поле **Короткое имя** введите короткое имя. Короткое имя используется вместо полного имени группы действий при отправке уведомлений с помощью этой группы.
    1. Поле **Подписка** автоматически заполняется вашей текущей подпиской. В этой подписке будет сохранена группа действий.
    1. Выберите группу ресурсов, в которой будет храниться группа действий.

В этом примере вы создаете два действия: действие runbook и действие уведомления.

### <a name="runbook-action"></a>Действие модуля Runbook

Чтобы создать действие runbook в группе действий, выполните следующее.

1. В разделе **Действия** в поле **Имя действия** введите имя действия. Для параметра **Тип действия** выберите **Runbook автоматизации**.
1. В разделе **Сведения** выберите **Изменить сведения**.  
1. На странице **Настройка Runbook** в разделе **Источник модуля Runbook** выберите **Пользователь**.  
1. Выберите свои **подписку** и **учетную запись службы автоматизации**, затем выберите модуль runbook **Stop-AzureVmInResponsetoVMAlert**.  
1. По завершении нажмите кнопку **ОК**.

### <a name="notification-action"></a>Действие уведомления

Чтобы создать действие уведомления в группе действий, выполните следующее.

1. В разделе **Действия** в поле **Имя действия** введите имя действия. Для параметра **Тип действия** выберите **Электронная почта**.  
1. В разделе **Сведения** выберите **Изменить сведения**.  
1. На странице **Электронная почта** введите адрес электронной почты для этого уведомления, а затем нажмите кнопку **ОК**. Удобно также добавить адрес электронной почты в runbook в качестве действия. В этом случае вы будете получать уведомления при запуске этого runbook.  

    Группа действий должна выглядеть как на следующем изображении.

   ![Страница добавления группы действий](./media/automation-create-alert-triggered-runbook/add-action-group.png)
1. Нажмите кнопку **ОК**, чтобы создать группу действий.

Можно использовать эту группу действий в создаваемых [оповещениях журнала действий](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) и [оповещениях почти в реальном времени](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal).

## <a name="classic-alert"></a>Классическое оповещение

Классические оповещения основаны на метриках. Они не используют группы действий. Однако на основе классического оповещения можно настроить действие runbook. 

Вот как можно создать классическое оповещение.

1. Выберите **Добавить оповещение метрики**.
1. Введите имя метрики оповещения: **myVMCPUAlert**. Введите краткое описание оповещения.
1. В качестве условие оповещения о метрике выберите **Больше**. Для параметра **Пороговое значение** выберите значение **10**. Для параметра **Период** выберите значение **Over the last five minutes** (За последние пять минут).
1. В разделе **Выполнить действие** выберите **Запустить модуль Runbook из этого оповещения**.
1. На странице **Настройка модуля Runbook** для параметра **Источник Runbook** выберите **Пользователь**. Выберите учетную запись службы автоматизации и runbook **Stop-AzureVmInResponsetoVMAlert**. Нажмите кнопку **ОК**.
1. Нажмите кнопку **OK**, чтобы сохранить правило генерации оповещений.

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения о запуске runbook службы автоматизации с помощью веб-перехватчика см. в разделе [Запуск Runbook службы автоматизации Azure с помощью объекта webhook](automation-webhooks.md).
* Дополнительные сведения о различных способах запуска модуля Runbook см. в статье [Запуск модуля Runbook в службе автоматизации Azure](automation-starting-a-runbook.md).
* Дополнительные сведения о том, как создать оповещение журнала действий, см. в разделе [Создание оповещений журнала действий](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json).
* Дополнительные сведения о том, как создать оповещение почти в реальном времени, см. в разделе [Создание правила оповещения с помощью портала Azure](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal).
