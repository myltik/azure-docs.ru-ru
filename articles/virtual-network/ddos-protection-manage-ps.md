---
title: "Управление защитой от атак DDoS Azure уровня \"Стандартный\" с помощью Azure PowerShell | Документы Майкрософт"
description: "Сведения об управлении защитой от атак DDoS Azure уровня \"Стандартный\" с помощью Azure PowerShell."
services: virtual-network
documentationcenter: na
author: kumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: kumud
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 8badb3455fa508e4eef56ec7fc1901fbbc8b23d0
ms.contentlocale: ru-ru
ms.lasthandoff: 09/25/2017

---
# <a name="manage-azure-ddos-protection-standard-using-azure-powershell"></a>Управление защитой от атак DDoS Azure уровня "Стандартный" с помощью Azure PowerShell

>[!IMPORTANT]
>Защита от атак DDoS Azure уровня "Стандартный" (также "защита от атак DDoS") сейчас находится в предварительной версии. Защита от атак DDoS поддерживается ограниченным числом ресурсов Azure и только в некоторых регионах. Чтобы включить защиту от атак DDoS для своей подписки, необходимо [зарегистрироваться для использования службы](http://aka.ms/ddosprotection) во время действия ограниченной предварительной версии. После регистрации с вами свяжутся сотрудники команды защиты от атак DDoS Azure. С их помощью вы сможете включить защиту. Защита от атак DDoS доступна в регионах "Восточная часть США", "Западная часть США" и "Центральная часть США". При использовании предварительной версии плата за использование службы не взимается.

В этой статье показано, как включить и отключить защиту от атак DDoS, а также как использовать данные телеметрии для устранения атаки с помощью Azure PowerShell.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу. Если необходимо установить или обновить Azure PowerShell, см. раздел [Установка модуля Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="log-in-to-azure"></a>Вход в Azure

Войдите в подписку Azure с помощью команды `Login-AzureRmAccount` и следуйте инструкциям на экране.

```powershell
Login-AzureRmAccount
```

## <a name="enable-ddos-protection"></a>Включение защиты от атак DDoS

### <a name="create-a-new-virtual-network-and-enable-ddos-protection"></a>Создание новой виртуальной сети и включение защиты от атак DDoS

Чтобы создать виртуальную сеть с включенной защитой от атак DDoS, выполните следующую команду:

```powershell
New-AzureRmResourceGroup -Name <ResourceGroupName> -Location westcentralus 
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <frontendSubnet> -AddressPrefix "10.0.1.0/24" 
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <backendSubnet> -AddressPrefix "10.0.2.0/24" 
New-AzureRmVirtualNetwork -Name <MyVirtualNetwork> -ResourceGroupName <ResourceGroupName>  -Location westcentralus  -AddressPrefix "10.0.0.0/16" -Subnet $frontendSubnet,$backendSubnet -DnsServer 10.0.1.5,10.0.1.6 -EnableDDoSProtection
```

Этот пример создает виртуальную сеть с двумя подсетями и двумя серверами DNS. При указании DNS-серверов в виртуальной сети эти DNS-серверы будут установлены по умолчанию для сетевых адаптеров или виртуальных машин, которые развертываются в этой виртуальной сети. Защита от атак DDoS включена для всех защищаемых ресурсов в виртуальной сети.

### <a name="enable-ddos-protection-on-an-existing-virtual-network"></a>Включение защиты от атак DDoS в существующей виртуальной сети

Чтобы включить защиту от атак DDoS в существующей виртуальной сети, выполните следующую команду:

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $true
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName "ResourceGroupName" -ResourceName "ResourceName" -ResourceType Microsoft.Network/virtualNetworks
```

## <a name="disable-ddos-protection-on-a-virtual-network"></a>Отключение защиты от атак DDoS в виртуальной сети

Чтобы отключить защиту от атак DDoS в виртуальной сети, выполните следующую команду:

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $false
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName <RessourceGroupName> -ResourceName <ResourceName> -ResourceType "Microsoft.Network/virtualNetworks"
```

## <a name="review-the-ddos-protection-status-of-virtual-networks"></a>Просмотр состояния защиты от атак DDoS в виртуальных сетях 

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps
```

## <a name="use-ddos-protection-telemetry"></a>Использование данных телеметрии защиты от атак DDoS

Данные телеметрии для атаки предоставляются Azure Monitor в режиме реального времени. Данные телеметрии доступны только в тот интервал времени, в который для общедоступного IP-адреса выполняется устранение атаки. Вы не сможете просмотреть данные телеметрии до или после устранения атаки.

### <a name="configure-alerts-on-ddos-protection-metrics"></a>Настройка оповещений о метриках защиты от атак DDoS

В Azure Monitor вы можете настроить оповещение для любой из доступных метрик защиты от атак DDoS, которое будет выдаваться во время устранения атаки.

#### <a name="configure-email-alert-rules-via-azure-powershell"></a>Настройка правила оповещений по электронной почте с помощью Azure PowerShell

1. Получите список доступных вам подписок. Убедитесь, что вы работаете с нужной подпиской. В противном случае установите необходимую подписку на основе выходных данных командлета Get-AzureRmSubscription. 

    ```powershell
    Get-AzureRmSubscription 
    Get-AzureRmContext 
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```

2. Чтобы получить список существующих правил для группы ресурсов, используйте следующую команду. 

    ```powershell
    Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
    ```

3. Чтобы создать правило, необходимо сначала получить определенные важные сведения. 

    - Идентификатор ресурса, для которого необходимо установить оповещение.
    - Доступные определения метрик для этого ресурса. Получить идентификатор ресурса можно на портале Azure. Если ресурс уже создан, выберите его на портале Azure. Затем на следующей странице в разделе *Параметры* выберите *Свойства*. **Идентификатор ресурса** указан в поле на следующей странице. Кроме того, для получения идентификатора ресурса можно использовать [Azure Resource Explorer](https://resources.azure.com/). Пример идентификатора ресурса для общедоступного IP-адреса: `/subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Network/publicIPAddresses/mypublicip`

    Следующий пример устанавливает оповещение для общедоступного IP-адреса, связанного с виртуальной сетью. Метрика, для которой создается оповещение — **Подвергаюсь ли я DDoS-атаке**. Это логическое значение (1 или 0). Значение **1** означает, что вы подвергаетесь атаке. Значение **0** означает, что вы не подвергаетесь атаке. Оповещение создается, если вы подвергались атаке в последние 5 минут.

    Чтобы при активации оповещения создавался веб-перехватчик или отправлялось электронное сообщение, необходимо сначала создать электронный адрес и (или) веб-перехватчик. Сразу после этого создайте правило с тегом -Actions, как показано в следующем примере. Вы не можете связать веб-перехватчик или адреса электронной почты с уже созданными правилами с помощью PowerShell.

    ```powershell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com 
    Add-AzureRmMetricAlertRule -Name <myMetricRuleWithEmail> -Location "West Central US" -ResourceGroup <myresourcegroup> -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mypublicip -MetricName "IfUnderDDoSAttack" -Operator GreaterThan -Threshold 0 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail-Description "Under DDoS Attack" 
    ```

4. Можно проверить, правильно ли созданы оповещения, просмотрев отдельные правила.

    ```powershell
    Get-AzureRmAlertRule -Name myMetricRuleWithEmail -ResourceGroup myresourcegroup -DetailedOutput 
    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```

Для создания оповещений вы также можете ознакомиться с дополнительными сведениями о [настройке веб-перехватчиков](../monitoring-and-diagnostics/insights-webhooks-alerts.md) и [приложениях логики](../logic-apps/logic-apps-what-are-logic-apps.md).

## <a name="configure-logging-on-ddos-protection-metrics"></a>Настройка ведения журналов для метрик защиты от атак DDoS

Чтобы получить помощь по включению и настройке ведения журналов диагностики Azure в PowerShell, обратитесь к [примерам для PowerShell](../monitoring-and-diagnostics/insights-powershell-samples.md).

## <a name="next-steps"></a>Дальнейшие действия

- [Дополнительные сведения о журналах диагностики Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)
- [Сбор журналов и метрик для служб Azure для использования в Log Analytics](../log-analytics/log-analytics-azure-storage.md)
- [Приступая к работе с концентраторами событий](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
