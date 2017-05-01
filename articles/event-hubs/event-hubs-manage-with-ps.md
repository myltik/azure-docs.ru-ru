---
title: "Управление ресурсами концентраторов событий Azure с помощью PowerShell | Документация Майкрософт"
description: "Создание концентраторов событий и управление ими с помощью модуля PowerShell"
services: event-hubs
documentationcenter: .NET
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/06/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: 9ca861788d9c327237c8637e11312fb099837e56
ms.lasthandoff: 04/11/2017


---
# <a name="use-powershell-to-manage-event-hubs-resources"></a>Управление ресурсами концентраторов событий с помощью PowerShell

Microsoft Azure PowerShell — это среда сценариев, которую можно использовать для контроля и автоматизации развертывания служб Azure, а также для управления ими. В этой статье описывается, как с помощью [модуля Resource Manager PowerShell для концентраторов событий](/powershell/module/azurerm.eventhub/?view=azurermps-3.7.0#event_hub) подготавливать сущности концентраторов событий (пространства имен, концентраторы событий и группы потребителей) и управлять ими, используя локальную консоль или сценарий Azure PowerShell.

Вы также можете управлять ресурсами концентраторов событий с помощью шаблонов Azure Resource Manager. Дополнительные сведения см. в статье [Создание пространства имен концентраторов событий с концентратором событий и группой потребителей с помощью шаблона Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub.md).

## <a name="prerequisites"></a>Предварительные требования

Для этого потребуются следующие компоненты.

* Подписка Azure. Дополнительные сведения о получении подписки см. на страницах [Как приобрести Azure][purchase options], [Предложения для участников][member offers] или [Создайте бесплатную учетную запись Azure уже сегодня][free account].
* Компьютер с Azure PowerShell. Инструкции см. в статье [Приступая к работе с командлетами Azure PowerShell](/powershell/azure/get-started-azureps).
* Общее представление о сценариях PowerShell, пакетах NuGet и платформе .NET Framework.

## <a name="get-started"></a>Начало работы

Сначала мы используем PowerShell для входа в учетную запись Azure и подписку Azure. Выполните инструкции, приведенные в статье [Приступая к работе с командлетами Azure PowerShell](/powershell/azure/get-started-azureps), чтобы войти в учетную запись Azure, а также получить и просмотреть ресурсы в подписке Azure.

## <a name="provision-an-event-hubs-namespace"></a>Подготовка пространства имен концентраторов событий

При работе с пространствами имен концентраторов событий можно использовать командлеты [Get-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/get-azurermeventhubnamespace), [New-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/new-azurermeventhubnamespace), [Remove-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/remove-azurermeventhubnamespace) и [Set-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/set-azurermeventhubnamespace).

В этом примере мы создадим несколько локальных переменных в сценарии, в частности `$Namespace` и `$Location`.

* `$Namespace` — имя пространства имен концентраторов событий, которое мы будем использовать.
* `$Location` — центр обработки данных, в котором мы подготовим пространство имен к работе.
* `$CurrentNamespace` — место хранения полученного (или созданного) исходного пространства имен.

В фактическом сценарии переменные `$Namespace` и `$Location` могут передаваться как параметры.

Эта часть сценария выполняет следующее:

1. Пытается получить пространство имен концентраторов событий с заданным именем.
2. Если пространство имен найдено, появляется сообщение о том, что именно нашел сценарий.
3. Если пространство имен не найдено, сценарий создает его и возвращает созданное пространство.

    ```powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzureRmEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
    }
    ```

## <a name="create-an-event-hub"></a>Создание концентратора событий

Чтобы создать концентратор событий, проверьте пространство имен с помощью сценария, приведенного в предыдущем разделе. Затем создайте концентратор событий, используя командлет New-[AzureRmEventHub](/powershell/module/azurerm.eventhub/new-azurermeventhub):

```powershell
# Check if Event Hub already exists
$CurrentEH = Get-AzureRMEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName

if($CurrentEH)
{
    Write-Host "The Event Hub $EventHubName already exists in the $Location region:"
    # Report what was found
    Get-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
}
else
{
    Write-Host "The $EventHubName Event Hub does not exist."
    Write-Host "Creating the $EventHubName Event Hub in the $Location region..."
    New-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -Location $Location -MessageRetentionInDays 3
    $CurrentEH = Get-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
    Write-Host "The $EventHubName Event Hub in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="create-a-consumer-group"></a>Создание группы потребителей

Чтобы создать в концентраторе событий группу потребителей, проверьте пространство имен и концентратор событий с помощью сценариев, приведенных в предыдущем разделе. Затем создайте группу потребителей в концентраторе событий, используя командлет [New-AzureRmEventHubConsumerGroup](/powershell/module/azurerm.eventhub/new-azurermeventhubconsumergroup). Например:

```powershell
# Check if consumer group already exists
$CurrentCG = Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName -ErrorAction Ignore

if($CurrentCG)
{
    Write-Host "The consumer group $ConsumerGroupName in Event Hub $EventHubName already exists in the $Location region:"
    # Report what was found
    Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
}
else
{
    Write-Host "The $ConsumerGroupName consumer group does not exist."
    Write-Host "Creating the $ConsumerGroupName consumer group in the $Location region..."
    New-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
    $CurrentCG = Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
    Write-Host "The $ConsumerGroupName consumer group in Event Hub $EventHubName in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

#### <a name="set-user-metadata"></a>Определение метаданных пользователей

После выполнения сценариев из предыдущих разделов можно воспользоваться командлетом [Set-AzureRmEventHubConsumerGroup](/powershell/module/azurerm.eventhub/set-azurermeventhubconsumergroup), чтобы обновить свойства группы потребителей, как показано в следующем примере:

```powershell
# Set some user metadata on the CG
Write-Host "Setting the UserMetadata field to 'Testing'"
Set-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName -UserMetadata "Testing"
# Show result
Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
```

## <a name="remove-event-hub"></a>Удаление концентратора событий

Для удаления созданных вами сущностей концентраторов событий можно использовать командлеты `Remove-*`, как показано в следующем примере:

```powershell
# Clean up
Remove-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
Remove-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
Remove-AzureRmEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
```

## <a name="next-steps"></a>Дальнейшие действия

- С полной документацией по модулю Resource Manager PowerShell для концентраторов событий можно ознакомиться [здесь](/powershell/module/azurerm.eventhub/?view=azurermps-3.7.0#event_hub). На этой странице перечислены все доступные командлеты.
- Дополнительные сведения о шаблонах Azure Resource Manager см. в статье [Создание пространства имен концентраторов событий с концентратором событий и группой потребителей с помощью шаблона Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub.md).
- Сведения о [библиотеках управления .NET для концентраторов событий](event-hubs-management-libraries.md).

[purchase options]: http://azure.microsoft.com/pricing/purchase-options/
[member offers]: http://azure.microsoft.com/pricing/member-offers/
[free account]: http://azure.microsoft.com/pricing/free-trial/

