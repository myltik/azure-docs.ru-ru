---
title: "Реагирование на оповещения Azure с помощью модуля Runbook службы автоматизации | Документация Майкрософт"
description: "Узнайте, как настроить запуск модуля Runbook при появлении оповещений Azure."
services: automation
keywords: 
author: georgewallace
ms.author: gwallace
ms.date: 01/11/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 486a3c80c9d6c19ca0a7ba7d4e3cdcde927053b9
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2018
---
# <a name="respond-to-azure-alerts-with-an-automation-runbook"></a>Реагирование на оповещения Azure с помощью модуля Runbook службы автоматизации

[Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md?toc=%2fazure%2fautomation%2ftoc.json) предоставляет метрики базового уровня, а также журналы для большинства служб Microsoft Azure. Модули Runbook службы автоматизации Azure можно вызывать с помощью [групп действий](../monitoring-and-diagnostics/monitoring-action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) или из классических оповещений для автоматизации задач на основе оповещений. В этой статье показано, как настроить и запустить модуль Runbook на основе этих оповещений.

## <a name="alert-types"></a>Типы оповещений

Модули Runbook можно использовать в качестве действий для всех трех типов оповещений. Когда оповещение вызывает модуль Runbook, фактически осуществляется вызов веб-перехватчика с помощью HTTP-запроса POST. Текст запроса POST содержит объект в формате JSON с полезными свойствами, связанными с оповещением. В следующей таблице содержатся ссылки на схему полезных данных для каждого типа оповещения:

|Предупреждение  |ОПИСАНИЕ|Схема полезных данных  |
|---------|---------|---------|
|[Классическое оповещение о метриках](../monitoring-and-diagnostics/insights-alerts-portal.md?toc=%2fazure%2fautomation%2ftoc.json)    |Получение уведомления, когда данные метрики уровня платформы соответствуют определенному условию (например, загрузка ЦП на виртуальной машине превышает 90 % последние 5 минут).| [Схема полезных данных](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)         |
|[Оповещение журнала действий](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Получение уведомления, когда какое-либо новое событие в журнале действий Azure соответствует определенным условиям (например, когда выполняется операция "Удаление виртуальной машины" в группе ресурсов myProductionResourceGroup или когда появляется новое событие службы работоспособности с состоянием "Активное").| [Схема полезных данных](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)        |
|[Оповещения на основе метрик практически в реальном времени](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Получение уведомлений быстрее, чем оповещений метрик, когда данные одной или нескольких метрик уровня платформы соответствуют определенным условиям (например, загрузка ЦП на виртуальной машине превышает 90 %, а входящий сетевой трафик превышает 500 МБ за последние 5 минут).| [Схема полезных данных](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)          |

Так как данные, предоставляемые каждым оповещением, различны, каждое оповещение должно обрабатываться по-разному. В следующем разделе вы узнаете, как создать модуль Runbook для обработки различных типов оповещений.

## <a name="create-a-runbook-to-handle-alerts"></a>Создание модуля Runbook для обработки оповещений

Чтобы применять автоматизацию к оповещениям, вам нужен модуль Runbook с логикой для управления полезными данными JSON оповещения, которые передаются в модуль. Приведенный ниже пример модуля Runbook должен быть вызван из оповещения Azure. Как описано в предыдущем разделе, каждый тип оповещения имеет свою схему. Скрипт берет данные веб-перехватчика из входного параметра модуля Runbook `WebhookData` из оповещения и оценивает полезные данные JSON, чтобы определить тип используемого оповещения. Следующий пример будет использоваться для оповещений из виртуальной машины. Он извлекает данные виртуальной машины из полезных данных и использует эту информацию для остановки работы виртуальной машины. Соединение должно быть установлено в учетной записи службы автоматизации, в которой выполняется модуль Runbook.

Модуль Runbook будет использовать [учетную запись от имени](automation-create-runas-account.md) **AzureRunAsConnection** для аутентификации в Azure, чтобы выполнить действие управления для виртуальной машины.

Следующий сценарий PowerShell можно изменить для использования со множеством других ресурсов.

С помощью следующего примера создайте модуль Runbook под названием **Stop-AzureVmInResponsetoVMAlert** с образцом PowerShell.

1. Откройте учетную запись автоматизации.
1. Щелкните **Модули Runbook** в разделе **Автоматизация процессов**. Отобразится список модулей runbook.
1. Нажмите кнопку **Добавить Runbook** в верхней части списка. На странице **добавления модуля Runbook** выберите **Быстрое создание**.
1. Введите "Stop-AzureVmInResponsetoVMAlert" в качестве значения для параметра **Имя**, а затем выберите вариант **PowerShell** в списке **Тип Runbook**. Нажмите кнопку **Создать**.
1. Скопируйте следующий пример PowerShell в область редактирования. Нажмите кнопку **Опубликовать**, чтобы сохранить и опубликовать модуль Runbook.

```powershell-interactive
<#
.SYNOPSIS
  This runbook will stop a resource management VM in response to an Azure alert trigger.

.DESCRIPTION
  This runbook will stop an resource management VM in response to an Azure alert trigger.
  Input is alert data with information needed to identify which VM to stop.
  
  DEPENDENCIES
  - The runbook must be called from an Azure alert via a webhook.
  
  REQUIRED AUTOMATION ASSETS
  - An Automation connection asset called "AzureRunAsConnection" that is of type AzureRunAsConnection.
  - An Automation certificate asset called "AzureRunAsCertificate".

.PARAMETER WebhookData
   Optional (user does not need to enter anything, but the service will always pass an object)
   This is the data that is sent in the webhook that is triggered from the alert.

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
    # Get the data object from WebhookData
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Get the info needed to identify the VM (depends on the payload schema)
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
        # Schema not supported
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

        # Do work only if this is a resource management VM
        if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
        {
            # This is the VM
            Write-Verbose "This is a resource management VM." -Verbose

            # Authenticate to Azure with service principal and certificate and set subscription
            Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
            $ConnectionAssetName = "AzureRunAsConnection"
            Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
            $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null)
            {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Write-Verbose "Authenticating to Azure with service principal." -Verbose
            Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
            Write-Verbose "Setting subscription to work against: $SubId" -Verbose
            Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

            # Stop the VM
            Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
            Stop-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
            # [OutputType(PSAzureOperationResponse")]
        }
        else {
            # ResourceType not supported
            Write-Error "$ResourceType is not a supported resource type for this runbook."
        }
    }
    else {
        # The alert status was not 'Activated' so no action taken
        Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
    }
}
else {
    # Error
    Write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="create-an-action-group"></a>Создание группы действий

Группа действий представляет собой коллекцию действий, которые выполняются на основе оповещений. Модули Runbook — это лишь одно из многих действий, доступных с группами действий.

1. На портале выберите **Монитор**.

1. В разделе **Параметры** выберите **Группы действий**.

1. Выберите **Add action group** (Добавить группу действий) и заполните поля.

1. Введите имя в поля "Имя группы действий" и Short name (Короткое имя). Короткое имя используется вместо полного имени группы действий при отправке уведомлений с помощью этой группы.

1. Поле "Подписка" автоматически заполняется вашей текущей подпиской. В этой подписке сохраняются группы действий.
Выберите группу ресурсов, в которой хранится группа действий.

В этом примере вы создаете два действия: действие модуля Runbook и действие уведомления.

### <a name="runbook-action"></a>Действие модуля Runbook

Ниже показано, как создать действие модуля Runbook в группе действий.

1. В разделе **Действия** введите имя действия.

1. Выберите **Runbook автоматизации** для параметра **Тип действия**.

1. В разделе **Сведения** выберите **Изменить сведения**.

1. На странице **Настройка Runbook** в разделе **Источник модуля Runbook** выберите **Пользователь**.

1. Выберите свою **подписку** и **учетную запись автоматизации** и, наконец, выберите созданный ранее модуль Runbook под названием "Stop-AzureVmInResponsetoVMAlert".

1. По завершении нажмите кнопку **ОК**.

### <a name="notification-action"></a>Действие уведомления

Ниже показано, как создать действие уведомления в группе действий.

1. В разделе **Действия** введите имя действия.

1. Выберите **Электронная почта** для параметра **Тип действия**.

1. В разделе **Сведения** выберите **Изменить сведения**.

1. На странице **Электронная почта** введите адрес электронной почты для уведомлений и нажмите кнопку **ОК**. Добавив адрес электронной почты, а также модуль Runbook в качестве действия, вы будете получать уведомление о запуске модуля. Группа действий должна выглядеть как на следующем изображении.

   ![Страница добавления группы действий](./media/automation-create-alert-triggered-runbook/add-action-group.png)

1. Нажмите кнопку **ОК**, чтобы создать группу действий.

Вы можете использовать эту группу действий для создания [оповещений журнала действий](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) или [оповещений практически в реальном времени](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal).

## <a name="classic-alert"></a>Классическое оповещение

Классические оповещения основаны на метриках. Они не используют группы действий, однако на их основе выполняются действия модуля Runbook. Чтобы создать классическое оповещение, выполните следующие действия:

1. Выберите **+ Добавить оповещение метрики**.

1. Введите имя для оповещения метрики myVMCPUAlert и предоставьте краткое описание оповещения.

1. Задайте для параметра "Условие" оповещения метрики значение "Больше чем", для параметра "Пороговое значение" — "10", а для параметра "Период" — "За последние 5 минут".

1. В разделе **Принять меры** выберите **Запустить модуль Runbook из этого оповещения**.

1. На странице **Настройка модуля Runbook** для параметра **Источник Runbook** выберите **Пользователь**. Выберите учетную запись службы автоматизации и модуль Runbook под названием **Stop-AzureVmInResponsetoVMAlert**. Нажмите кнопку **ОК** дважды, чтобы сохранить правило генерации оповещений.

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения о запуске модулей Runbook службы автоматизации с помощью веб-перехватчика см. в [этой статье](automation-webhooks.md).
* Дополнительные сведения о различных способах запуска модуля Runbook см. в статье [Запуск модуля Runbook в службе автоматизации Azure](automation-starting-a-runbook.md).
* Дополнительные сведения о том, как создать оповещение журнала действий, см. в [этой статье](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json).
* Дополнительные сведения о том, как создать оповещения практически в реальном времени, см. в разделе [Create an alert rule with the Azure portal](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal) (Создание правила генерации оповещений с помощью портала Azure).