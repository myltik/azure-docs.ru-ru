---
title: Перемещение ресурса виртуальной машины Windows в Azure | Документация Майкрософт
description: Из этой статьи вы узнаете, как перенести виртуальную машину Windows в другую подписку или группу ресурсов Azure в модели развертывания Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: cynthn
ms.openlocfilehash: b98b8c947fb34b60c7bd27b006672e0e9d923d3b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30918155"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Перемещение виртуальной машины Windows в другую подписку или группу ресурсов Azure
В этой статье описано перемещение виртуальной машины Windows между группами ресурсов или подписками. Перемещение между подписками может понадобиться, если вы создали виртуальную машину в личной подписке и вам нужно переместить ее в корпоративную подписку, чтобы продолжить работу.

> [!IMPORTANT]
>В настоящее время переместить управляемые диски невозможно. 
>
>Во время перемещения создаются новые идентификаторы ресурсов. После перемещения виртуальной машины вам нужно будет обновить средства и сценарии, чтобы использовать новые идентификаторы ресурсов. 
> 
> 

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Использование PowerShell для перемещения виртуальной машины

Чтобы переместить виртуальную машину в другую группу ресурсов, необходимо убедиться, что также перемещаются все зависимые ресурсы. Чтобы воспользоваться командлетом Move-AzureRMResource, следует указать ResourceId для каждого ресурса. Список ResourceId можно получить с помощью командлета [Find-AzureRMResource](/powershell/module/azurerm.resources/find-azurermresource).

```azurepowershell-interactive
 Find-AzureRMResource -ResourceGroupNameContains <sourceResourceGroupName> | Format-table -Property ResourceId 
```

Чтобы переместить виртуальную машину, необходимо перенести несколько ресурсов. С помощью выходных данных Find-AzureRMResource можно создать список значений ResourceId с разделителями-запятыми и передать его в командлет [Move-AzureRMResource](/powershell/module/azurerm.resources/move-azurermresource), чтобы переместить эти значения в нужное расположение. 

```azurepowershell-interactive

Move-AzureRmResource -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```
    
Чтобы переместить ресурсы в другую подписку, добавьте параметр **-DestinationSubscriptionId**. 

```azurepowershell-interactive
Move-AzureRmResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


Вам будет предложено подтвердить перемещение указанных ресурсов. 

## <a name="next-steps"></a>Дополнительная информация
Вы можете перемещать разные типы ресурсов между группами ресурсов и подписками. Дополнительные сведения см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](../../resource-group-move-resources.md).    

