---

title: "Мониторинг VPN-шлюзов с помощью средства устранения неполадок Наблюдателя за сетями Azure | Документация Майкрософт"
description: "В этой статье описывается, как диагностировать локальное подключение с помощью службы автоматизации Azure и Наблюдателя за сетями."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 9a6f42e9b7b737e9316dcc1ff39ea532c4b923c5
ms.lasthandoff: 03/29/2017


---

# <a name="monitor-vpn-gateways-with-network-watcher-troubleshooting"></a>Мониторинг VPN-шлюзов с помощью средства устранения неполадок Наблюдателя за сетями

Чтобы предоставлять клиентам надежное обслуживание, вы должны знать все о производительности своей сети. Поэтому важно быстро обнаруживать аварийные состояния сети и принимать необходимые меры, чтобы свести к минимуму последствия этих состояний. Служба автоматизации Azure позволяет реализовать и выполнить эту задачу программными средствами с помощью модулей runbook. С помощью службы автоматизации Azure вы можете выполнять упреждающий непрерывный мониторинг сети и создавать оповещения.

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

## <a name="scenario"></a>Сценарий

На следующем изображении показано многоуровневое приложение, локальное подключение к которому обеспечивают VPN-шлюз и туннель. Для производительности приложений крайне важно обеспечить включение и работу VPN-шлюза.

Модуль runbook создается со скриптом для проверки состояния подключения VPN-туннеля с помощью API устранения неполадок в ресурсах. Если туннель не работает, администраторам электронной почтой отправляется триггер.

![Пример сценария][scenario]

Вы узнаете:

- Как создать модуль runbook, вызывающий командлет `Start-AzureRmNetworkWatcherResourceTroubleshooting` для устранения неполадок подключения.
- Как подключить расписание к модулю runbook.

## <a name="before-you-begin"></a>Перед началом работы

Прежде чем приступить к работе с этим сценарием, следует подготовить такие необходимые компоненты:

- Учетная запись службы автоматизации Azure.
- Набор учетных данных, настроенных в службе автоматизации Azure. См. дополнительные сведения о [безопасности службы автоматизации Azure](../automation/automation-security-overview.md).
- Допустимый SMTP-сервер (Office 365, локальный или другой адрес электронной почты) и учетные данные, определенные в службе автоматизации Azure.
- Настроенный шлюз виртуальной сети в Azure.

> [!NOTE]
> Инфраструктура, показанная на предыдущем рисунке, предназначена для иллюстрации. Она не создается с помощью выполнения инструкций из этой статьи.

### <a name="create-the-runbook"></a>Создание модуля runbook

Чтобы настроить пример, сначала нужно создать модуль runbook. В этом примере используется учетная запись запуска от имени. Сведения об учетных записях запуска от имени см. в разделе [Создание учетной записи службы автоматизации на портале Azure](../automation/automation-sec-configure-azure-runas-account.md#create-an-automation-account-from-the-azure-portal).

### <a name="step-1"></a>Шаг 1

Перейдите к службе автоматизации Azure на [портале Azure](https://portal.azure.com) и нажмите кнопку **Модули Runbook**.

![Обзор учетной записи службы автоматизации][1]

### <a name="step-2"></a>Шаг 2

Нажмите кнопку **Добавить Runbook**, чтобы начать создание модуля Runbook.

![Колонка модулей runbook][2]

### <a name="step-3"></a>Шаг 3.

В разделе **Быстрое создание**, нажмите кнопку **Создать новый Runbook**, чтобы создать модуль runbook.

![Колонка добавления модуля runbook][3]

### <a name="step-4"></a>Шаг 4.

На этом этапе присвойте модулю runbook имя. В этом примере он называется **Get-VPNGatewayStatus**. Необходимо указать описательное имя модуля runbook. Кроме того, рекомендуется присвоить ему имя, которое соответствует стандартам именования PowerShell. Тип модуля runbook в этом примере — **PowerShell**. Другие варианты: графический, рабочий процесс PowerShell и графический рабочий процесс PowerShell.

![Колонка модуля runbook][4]

### <a name="step-5"></a>Шаг 5

На этом шаге создается модуль runbook. В примере ниже содержится весь необходимый код. Элементы \<value\> в этом коде необходимо заменить значениями из вашей подписки.

Используйте следующий код и нажмите кнопку **Сохранить**.

```PowerShell
# Get credentials for Office 365 account
$MyCredential = "Office 365 account"
$Cred = Get-AutomationPSCredential -Name $MyCredential

# Get the connection "AzureRunAsConnection "
$connectionName = "AzureRunAsConnection"
$servicePrincipalConnection=Get-AutomationConnection -Name $connectionName
$subscriptionId = "<subscription id>"
"Logging in to Azure..."
Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $servicePrincipalConnection.TenantId `
    -ApplicationId $servicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
"Setting context to a specific subscription"
Set-AzureRmContext -SubscriptionId $subscriptionId

$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name "2to3" -ResourceGroupName "testrg"
$sa = New-AzureRmStorageAccount -Name "contosoexamplesa" -SKU "Standard_LRS" -ResourceGroupName "testrg" -Location "WestCentralUS"
$result = Start-AzureRmNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath "$($sa.PrimaryEndpoints.Blob)logs"


if($result.code -ne "Healthy")
    {
        $Body = "Connection for ${vpnconnectionName} is: $($result.code). View the logs at $($sa.PrimaryEndpoints.Blob)logs to learn more."
        $subject = "${connectionname} Status"
        Send-MailMessage `
        -To 'admin@contoso.com' `
        -Subject $subject `
        -Body $Body `
        -UseSsl `
        -Port 587 `
        -SmtpServer 'smtp.office365.com' `
        -From "${$username}" `
        -BodyAsHtml `
        -Credential $Cred
    }
else
    {
    Write-Output ("Connection Status is: $($result.connectionStatus)")
    }
```

![Шаг 5][5]

### <a name="step-6"></a>Шаг 6

После сохранения модуля runbook его необходимо подключить к расписанию, чтобы автоматизировать запуск модуля. Чтобы начать, нажмите кнопку **Расписание**.

![Шаг 6][6]

## <a name="link-a-schedule-to-the-runbook"></a>Подключение расписания к модулю runbook

Необходимо создать новое расписание. Щелкните **Связать расписание с модулем runbook**.

![Шаг 7][7]

### <a name="step-1"></a>Шаг 1

В колонке **Расписание** щелкните **Создать новое расписание**.

![Шаг 8][8]

### <a name="step-2"></a>Шаг 2

В колонке **Новое расписание** заполните сведения о расписании. Вы можете указать значения, перечисленные ниже.

- **Имя** — понятное имя расписания.
- **Описание** — описание расписания.
- **Начало** — это значение включает дату, время и часовой пояс, которые формируют время активации расписания.
- **Повторение** — это значение определяет повторение расписания.  Допустимые значения: **Однократно** или **Периодически**.
- **Повторять каждые** — интервал повторения расписания в часах, днях, неделях или месяцах.
- **Установить срок действия** — значение определяет, есть ли у расписания срок действия. Возможные варианты: **Да** или **Нет**. Если выбран вариант "Да", необходимо указать допустимое значение даты и времени.

> [!NOTE]
> Если модуль runbook необходимо запускать чаще, чем каждый час, необходимо создать несколько расписаний с разными интервалами (то есть 15, 30, 45 минут после часа)

![Шаг 9.][9]

### <a name="step-3"></a>Шаг 3.

Нажмите кнопку "Сохранить", чтобы сохранить расписание для модуля runbook.

![Шаг 10][10]

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать, как интегрировать средство устранения неполадок Наблюдателя за сетями со службой автоматизации Azure, научитесь активировать захват пакетов на основе предупреждений виртуальной машины. Инструкции см. в статье о том, как [с помощью Наблюдателя за сетями Azure создать захват пакетов, который активируется оповещениями](network-watcher-alert-triggered-packet-capture.md).

<!-- images -->
[scenario]: ./media/network-watcher-monitor-with-azure-automation/scenario.png
[1]: ./media/network-watcher-monitor-with-azure-automation/figure1.png
[2]: ./media/network-watcher-monitor-with-azure-automation/figure2.png
[3]: ./media/network-watcher-monitor-with-azure-automation/figure3.png
[4]: ./media/network-watcher-monitor-with-azure-automation/figure4.png
[5]: ./media/network-watcher-monitor-with-azure-automation/figure5.png
[6]: ./media/network-watcher-monitor-with-azure-automation/figure6.png
[7]: ./media/network-watcher-monitor-with-azure-automation/figure7.png
[8]: ./media/network-watcher-monitor-with-azure-automation/figure8.png
[9]: ./media/network-watcher-monitor-with-azure-automation/figure9.png
[10]: ./media/network-watcher-monitor-with-azure-automation/figure10.png

