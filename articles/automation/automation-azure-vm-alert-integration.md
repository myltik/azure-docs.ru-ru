---
title: " Обработка оповещений виртуальной машины Azure с помощью модулей Runbook службы автоматизации | Документация Майкрософт"
description: "В этой статье показано, как интегрировать оповещения виртуальной машины Azure с модулями Runbook службы автоматизации Azure и автоматически устранять проблемы"
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: 1f7baa7f-7283-4a4f-9385-3f5cd1062c7f
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: csand;magoedte
ms.openlocfilehash: ef18a2de8ce62de945c49b91dc74aca7d7f408b0
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2017
---
# <a name="azure-automation-scenario---remediate-azure-vm-alerts"></a>Сценарий службы автоматизации Azure: обработка оповещений виртуальной машины Azure
Для службы автоматизации Azure и виртуальных машин Azure доступна новая функция, которая позволяет настроить оповещения виртуальных машин для запуска модулей Runbook службы автоматизации. Эта новая возможность позволяет автоматически выполнять стандартное исправление в ответ на оповещения виртуальных машин, например перезапускать или останавливать виртуальную машину.

Ранее при создании правила оповещений виртуальной машины можно было [указать веб-перехватчик службы автоматизации](https://azure.microsoft.com/blog/using-azure-automation-to-take-actions-on-azure-alerts/), запускающий модуль Runbook при возникновении оповещения. Но для этого необходимо было создавать модуль Runbook, создавать веб-перехватчик для этого модуля и вставлять веб-перехватчик при создании правила оповещения. В этом новом выпуске процесс упростился. Теперь можно непосредственно выбрать модуль Runbook из списка при создании правила оповещения. А также выбрать или создать учетную запись службы автоматизации, которая будет запускать модуль Runbook.

В этой статье мы покажем, насколько просто настроить оповещение виртуальной машины Azure и модуль Runbook службы автоматизации, чтобы он запускался при инициализации оповещения. Примеры сценариев включают перезапуск виртуальной машины, когда использование памяти превышает определенный предел из-за приложения на виртуальной машине с утечкой памяти, или остановку виртуальной машины, если время использования ЦП за последний час составляло меньше 1 % и если ЦП не используется. Кроме того, мы рассмотрим, как автоматическое создание субъекта-службы в учетной записи службы автоматизации упрощает использование модулей Runbook при обработке оповещений Azure.

## <a name="create-an-alert-on-a-vm"></a>Создание оповещения на виртуальной машине
Выполните следующие действия, чтобы настроить оповещение для запуска модуля Runbook, когда достигается пороговое значение.

> [!NOTE]
> В этом выпуске поддерживаются только виртуальные машины V2. Поддержка классических виртуальных машин будет добавлена в ближайшее время.  
> 
> 

1. Войдите на портал Azure и щелкните элемент **Виртуальные машины**.  
2. Выберите одну из виртуальных машин.  
3. На экране виртуальной машины в разделе **Мониторинг** щелкните **Правила оповещений**.
4. На странице **Правила оповещений** щелкните **Добавить оповещение**.

Откроется страница **Добавление правила оповещения**, на которой можно настроить условия для оповещения и выбрать один или все варианты: отправить электронное сообщение, использовать веб-перехватчик для пересылки оповещения в другую систему и (или) запустить модуль Runbook службы автоматизации, чтобы попытаться устранить проблему.

## <a name="configure-a-runbook"></a>Настройка модуля Runbook
Чтобы настроить модуль Runbook для запуска при достижении порога оповещения виртуальной машины, выберите пункт **Модуль Runbook службы автоматизации**. В области **Настройка модуля Runbook** можно выбрать модуль Runbook для запуска, а также учетную запись службы автоматизации, в которой необходимо запускать этот модуль.

![Настройка модуля Runbook службы автоматизации и создание учетной записи службы автоматизации](media/automation-azure-vm-alert-integration/ConfigureRunbookNewAccount.png)

> [!NOTE]
> В этом выпуске можно выбрать один из трех модулей Runbook, предоставляемых службой: "Перезапуск виртуальной машины", "Остановка виртуальной машины" или "Удаление виртуальной машины".  Возможность выбора других или одного из собственных модулей Runbook будет доступна в будущих выпусках.
> 
> 

![Модули Runbook для выбора](media/automation-azure-vm-alert-integration/RunbooksToChoose.png)

После выбора одного из трех доступных модулей Runbook отобразится раскрывающийся список **Учетная запись службы автоматизации**, где можно выбрать учетную запись службы автоматизации, от имени которой будет запускаться модуль Runbook. Модули Runbook должны запускаться в контексте [учетной записи службы автоматизации](automation-security-overview.md), которая находится в вашей подписке Azure. Можно выбрать уже созданную учетную запись службы автоматизации или создать новую учетную запись.

Проверка подлинности предоставленных модулей Runbook выполняется в Azure с помощью субъекта-службы. Если вы решили запустить модуль Runbook в одной из существующих учетных записей службы автоматизации, субъект-служба будет создан автоматически. Если вы решили создать учетную запись службы автоматизации, субъект-служба будет создан автоматически вместе с учетной записью. В обоих случаях в учетной записи службы автоматизации будут созданы два ресурса: ресурс сертификата с именем **AzureRunAsCertificate** и ресурс подключения с именем **AzureRunAsConnection**. Модули Runbook будут использовать ресурс **AzureRunAsConnection** для аутентификации в Azure, чтобы выполнить действие управления для виртуальной машины.

> [!NOTE]
> Субъект-служба создается в области действия подписки и назначается роли участника. Эта роль необходима для того, чтобы учетная запись имела разрешение на запуск модулей Runbook службы автоматизации для управления виртуальными машинами Azure.  Создание учетной записи службы автоматизации и/или субъекта-службы является одноразовым событием. После их создания эту учетную запись можно использовать для запуска модулей Runbook для оповещений других виртуальных машин Azure.
> 
> 

После нажатия кнопки **ОК** выполняется настройка оповещения. Если выбран вариант создания учетной записи службы автоматизации, то она создается вместе с субъектом-службой.  Для завершения этого процесса может понадобиться несколько секунд.  

![Настройка модуля Runbook](media/automation-azure-vm-alert-integration/RunbookBeingConfigured.png)

После завершения настройки вы увидите имя модуля Runbook на странице **Добавление правила оповещения**.

![Модуль Runbook настроен](media/automation-azure-vm-alert-integration/RunbookConfigured.png)

На странице **Добавление правила оповещения** нажмите кнопку **OK**.  Правило генерации оповещений будет создано и активировано, когда виртуальная машина находится в состоянии выполнения.

### <a name="enable-or-disable-a-runbook"></a>Включение и отключение модуля Runbook
Если для оповещения настроен модуль Runbook, его можно отключить, не удаляя конфигурацию модуля Runbook. Это позволяет продолжить выполнение оповещения и, возможно, проверить некоторые правила оповещения и позже включить модуль Runbook повторно.

## <a name="create-a-runbook-that-works-with-an-azure-alert"></a>Создание модуля Runbook, который работает с оповещениями Azure
При выборе модуля Runbook в качестве части правила генерации оповещений Azure он должен содержать логику для управления данными оповещений, которые в него поступают.  При настройке модуля Runbook в правиле генерации оповещений для него создается webhook, который затем используется для запуска модуля Runbook каждый раз, когда активируется оповещение.  Фактический вызов для запуска модуля Runbook осуществляется с помощью HTTP-запроса POST на URL-адрес webhook. Текст запроса POST содержит объект в формате JSON с полезными свойствами, связанными с оповещением.  Как видно ниже, данные оповещения содержат такие сведения, как идентификатор подписки, имя группы ресурсов, имя ресурса и тип ресурса.

### <a name="example-of-alert-data"></a>Пример данных оповещения
```
{
    "WebhookName": "AzureAlertTest",
    "RequestBody": "{
    \"status\":\"Activated\",
    \"context\": {
        \"id\":\"/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/microsoft.insights/alertrules/AlertTest\",
        \"name\":\"AlertTest\",
        \"description\":\"\",
        \"condition\": {
            \"metricName\":\"CPU percentage guest OS\",
            \"metricUnit\":\"Percent\",
            \"metricValue\":\"4.26337916666667\",
            \"threshold\":\"1\",
            \"windowSize\":\"60\",
            \"timeAggregation\":\"Average\",
            \"operator\":\"GreaterThan\"},
        \"subscriptionId\":\<subscriptionID> \",
        \"resourceGroupName\":\"TestResourceGroup\",
        \"timestamp\":\"2016-04-24T23:19:50.1440170Z\",
        \"resourceName\":\"TestVM\",
        \"resourceType\":\"microsoft.compute/virtualmachines\",
        \"resourceRegion\":\"westus\",
        \"resourceId\":\"/subscriptions/<subscriptionId>/resourceGroups/TestResourceGroup/providers/Microsoft.Compute/virtualMachines/TestVM\",
        \"portalLink\":\"https://portal.azure.com/#resource/subscriptions/<subscriptionId>/resourceGroups/TestResourceGroup/providers/Microsoft.Compute/virtualMachines/TestVM\"
        },
    \"properties\":{}
    }",
    "RequestHeader": {
        "Connection": "Keep-Alive",
        "Host": "<webhookURL>"
    }
}
```

Когда webhook службы автоматизации получает HTTP-запрос POST, он извлекает данные оповещения и передает их в модуль Runbook в качестве входного параметра WebhookData модуля Runbook.  Ниже приведен пример модуля Runbook, в котором показано, как использовать параметр WebhookData, извлекать данные оповещения и использовать его для управления ресурсом Azure, активировавшим оповещение.

### <a name="example-runbook"></a>Пример модуля Runbook
```
#  This runbook restarts an ARM (V2) VM in response to an Azure VM alert.

[OutputType("PSAzureOperationResponse")]

param ( [object] $WebhookData )

if ($WebhookData)
{
    # Get the data object from WebhookData
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Assure that the alert status is 'Activated' (alert condition went from false to true)
    # and not 'Resolved' (alert condition went from true to false)
    if ($WebhookBody.status -eq "Activated")
    {
        # Get the info needed to identify the VM
        $AlertContext = [object] $WebhookBody.context
        $ResourceName = $AlertContext.resourceName
        $ResourceType = $AlertContext.resourceType
        $ResourceGroupName = $AlertContext.resourceGroupName
        $SubId = $AlertContext.subscriptionId

        # Assure that this is the expected resource type
        Write-Verbose "ResourceType: $ResourceType"
        if ($ResourceType -eq "microsoft.compute/virtualmachines")
        {
            # This is an ARM (V2) VM

            # Authenticate to Azure with service principal and certificate
            $ConnectionAssetName = "AzureRunAsConnection"
            $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null) {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
            Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

            # Restart the VM
            Restart-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName
        } else {
            Write-Error "$ResourceType is not a supported resource type for this runbook."
        }
    } else {
        # The alert status was not 'Activated' so no action taken
        Write-Verbose ("No action taken. Alert status: " + $WebhookBody.status)
    }
} else {
    Write-Error "This runbook is meant to be started from an Azure alert only."
}
```

## <a name="summary"></a>Сводка
При настройке оповещения на виртуальной машине Azure вы получаете возможность легко настраивать модуль Runbook службы автоматизации для автоматического выполнения действия исправления при инициализации оповещения. В этом выпуске можно выбрать модуль Runbook для перезапуска, остановки или удаления виртуальной машины в зависимости от вашего сценария оповещений. Это только начало использования сценариев, в которых вы управляете действиями (оповещение, устранение неполадок, исправление), автоматически предпринимаемыми при инициализации оповещения.

## <a name="next-steps"></a>Следующие шаги
* Чтобы начать работу с графическими модулями Runbook, см. инструкции в статье [Первый графический Runbook](automation-first-runbook-graphical.md).
* Чтобы приступить к работе с модулями Runbook рабочих процессов PowerShell, обратитесь к статье [Мой первый модуль Runbook рабочего процесса PowerShell](automation-first-runbook-textual.md)
* Дополнительные сведения о типах модулей Runbook, их преимуществах и ограничениях см. в статье [Типы модулей Runbook в службе автоматизации Azure](automation-runbook-types.md).

