---
title: Управление ресурсами служебной шины Azure с помощью PowerShell | Документация Майкрософт
description: Создание ресурсов служебной шины и управление ими с помощью модуля PowerShell
services: service-bus-messaging
documentationcenter: .NET
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/21/2017
ms.author: sethm
ms.openlocfilehash: 4fbc6e18565ec14a3ccb4499b24804f681026023
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/22/2017
ms.locfileid: "27159744"
---
# <a name="use-powershell-to-manage-service-bus-resources"></a>Управление ресурсами служебной шины с помощью модуля PowerShell

Microsoft Azure PowerShell — это среда сценариев, которую можно использовать для контроля и автоматизации развертывания служб Azure, а также для управления ими. В этой статье описывается использование [модуля PowerShell для диспетчера ресурсов Resource Manager служебной шины](/powershell/module/azurerm.servicebus) для подготовки и управления сущностями служебной шины (пространством имен, очередями, разделами и подписками) с помощью локальной консоли или сценария Azure PowerShell.

Сущностями служебной шины можно также управлять с помощью шаблонов Azure Resource Manager. Дополнительные сведения см. в статье [Создание ресурсов служебной шины с использованием шаблонов Azure Resource Manager](service-bus-resource-manager-overview.md).

## <a name="prerequisites"></a>предварительным требованиям

Перед началом работы проверьте следующие необходимые компоненты:

* Подписка Azure. Дополнительные сведения о получении подписки см. на страницах [Как приобрести Azure][purchase options], [Предложения для участников][member offers] или [Создайте бесплатную учетную запись Azure уже сегодня][free account].
* Компьютер с Azure PowerShell. Инструкции см. в статье [Приступая к работе с командлетами Azure PowerShell](/powershell/azure/get-started-azureps).
* Общее представление о сценариях PowerShell, пакетах NuGet и платформе .NET Framework.

## <a name="get-started"></a>Начало работы

Сначала мы используем PowerShell для входа в учетную запись Azure и подписку Azure. Выполните инструкции, приведенные в статье [Приступая к работе с командлетами Azure PowerShell](/powershell/azure/get-started-azureps), чтобы войти в учетную запись Azure, а также получить и просмотреть ресурсы в подписке Azure.

## <a name="provision-a-service-bus-namespace"></a>Подготовка пространства имен Service Bus

При работе с пространствами имен служебной шины можно использовать командлеты [Get-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespace), [New-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespace), [Remove-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespace) и [Set-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/set-azurermservicebusnamespace).

В этом примере мы создадим несколько локальных переменных в сценарии, в частности `$Namespace` и `$Location`.

* `$Namespace` — имя пространства имен служебной шины, которое мы будем использовать.
* `$Location` — центр обработки данных, в котором мы подготовим пространство имен к работе.
* `$CurrentNamespace` — место хранения полученного (или созданного) исходного пространства имен.

В фактическом сценарии переменные `$Namespace` и `$Location` могут передаваться как параметры.

Эта часть сценария выполняет следующее:

1. Пытается получить пространство имен служебной шины с заданным именем.
2. Если пространство имен найдено, появляется сообщение о том, что именно нашел сценарий.
3. Если пространство имен не найдено, сценарий создает его и возвращает созданное пространство.
   
    ``` powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureRMServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzureRMServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzureRmServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzureRMServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
                
    }
    ```

### <a name="create-a-namespace-authorization-rule"></a>Создание правила авторизации для пространства имен

В следующем примере показано, как управлять правилами авторизации пространства имен с помощью командлетов [New-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespaceauthorizationrule), [Get-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespaceauthorizationrule), [Set-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/set-azurermservicebusnamespaceauthorizationrule) и [Remove-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespaceauthorizationrule).

```powershell
# Query to see if rule exists
$CurrentRule = Get-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

# Check if the rule already exists or needs to be created
if ($CurrentRule)
{
    Write-Host "The $AuthRule rule already exists for the namespace $Namespace."
}
else
{
    Write-Host "The $AuthRule rule does not exist."
    Write-Host "Creating the $AuthRule rule for the $Namespace namespace..."
    New-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule -Rights @("Listen","Send")
    $CurrentRule = Get-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
    Write-Host "The $AuthRule rule for the $Namespace namespace has been successfully created."

    Write-Host "Setting rights on the namespace"
    $authRuleObj = Get-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

    Write-Host "Remove Send rights"
    $authRuleObj.Rights.Remove("Send")
    Set-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Add Send and Manage rights to the namespace"
    $authRuleObj.Rights.Add("Send")
    Set-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj
    $authRuleObj.Rights.Add("Manage")
    Set-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Show value of primary key"
    $CurrentKey = Get-AzureRmServiceBusNamespaceKey -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
        
    Write-Host "Remove this authorization rule"
    Remove-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
}
```

## <a name="create-a-queue"></a>Создание очереди

Чтобы создать очередь или раздел, с помощью сценария проверьте пространство имен, как описано в предыдущем разделе. Затем создайте очередь.

```powershell
# Check if queue already exists
$CurrentQ = Get-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName

if($CurrentQ)
{
    Write-Host "The queue $QueueName already exists in the $Location region:"
}
else
{
    Write-Host "The $QueueName queue does not exist."
    Write-Host "Creating the $QueueName queue in the $Location region..."
    New-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -EnablePartitioning $True
    $CurrentQ = Get-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName
    Write-Host "The $QueueName queue in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="modify-queue-properties"></a>Изменение свойств очереди

После выполнения сценария в предыдущем разделе можно использовать командлет [Set-AzureRmServiceBusQueue](/powershell/module/azurerm.servicebus/set-azurermservicebusqueue) для обновления свойства очереди, как показано в следующем примере.

```powershell
$CurrentQ.DeadLetteringOnMessageExpiration = $True
$CurrentQ.MaxDeliveryCount = 7
$CurrentQ.MaxSizeInMegabytes = 2048
$CurrentQ.EnableExpress = $True

Set-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -QueueObj $CurrentQ
```

## <a name="provisioning-other-service-bus-entities"></a>Подготовка других сущностей Service Bus

Можно использовать [модуль PowerShell для служебной шины](/powershell/module/azurerm.servicebus) для подготовки других сущностей, таких как разделы и подписки. Эти командлеты синтаксически аналогичны командлетам создания очередей, показанным в предыдущем разделе.

## <a name="next-steps"></a>Дополнительная информация

- С полной документацией по модулю Resource Manager PowerShell для служебной шины можно ознакомиться [здесь](/powershell/module/azurerm.servicebus). На этой странице перечислены все доступные командлеты.
- Дополнительные сведения об использовании шаблонов Azure Resource Manager см. в статье [Создание ресурсов служебной шины с использованием шаблонов Azure Resource Manager](service-bus-resource-manager-overview.md).
- Сведения о [библиотеках управления служебной шины .NET](service-bus-management-libraries.md).

Существуют альтернативные способы управления сущностями служебной шины, как описано в следующих блогах.

* [Как создать запросы, разделы и подписки Service Bus с помощью сценария PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Как создать пространство имен и концентратор событий служебной шины с помощью сценария PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)
* [Сценарии PowerShell для Service Bus](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[purchase options]: http://azure.microsoft.com/pricing/purchase-options/
[member offers]: http://azure.microsoft.com/pricing/member-offers/
[free account]: http://azure.microsoft.com/pricing/free-trial/
