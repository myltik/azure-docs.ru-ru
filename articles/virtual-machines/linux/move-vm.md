---
title: "Перемещение виртуальной машины Linux в Azure | Документация Майкрософт"
description: "Из этой статьи вы узнаете, как перенести виртуальную машину Linux в другую подписку или группу ресурсов Azure в рамках модели развертывания Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d635f0a5-4458-4b95-a5f8-eed4f41eb4d4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 12/14/2017
ms.author: cynthn
ms.openlocfilehash: 459e0d591e2279b63864a273f713e4c1df8c0858
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2017
---
# <a name="move-a-linux-vm-to-another-subscription-or-resource-group"></a>Перемещение виртуальной машины Linux в другую подписку или группу ресурсов
В этой статье описано перемещение виртуальной машины Linux между группами ресурсов или подписками. Перемещение ВМ между подписками может понадобиться, если вы создали виртуальную машину в личной подписке и вам нужно переместить ее в корпоративную подписку.

> [!IMPORTANT]
>В настоящее время переместить управляемые диски невозможно. 
>
>Во время перемещения создаются новые идентификаторы ресурсов. После перемещения виртуальной машины вам нужно будет обновить средства и сценарии, чтобы использовать новые идентификаторы ресурсов. 
> 
> 

## <a name="use-the-azure-cli-to-move-a-vm"></a>Использование Azure CLI для перемещения виртуальной машины


Перед перемещением виртуальной Машины с помощью CLI, необходимо убедиться, что исходный и целевой подписки существует в пределах того же клиента. Чтобы проверить, что оба подписки имеют один и тот же идентификатор клиента, используйте [az учетной записи, отображают](/cli/azure/account#az_account_show).

```azurecli-interactive
az account show --subscription mySourceSubscription --query tenantId
az account show --subscription myDestinationSubscription --query tenantId
```
Если идентификаторы клиентов для исходной и целевой подписок не совпадают, необходимо обратиться в [службу поддержки](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) для перемещения ресурсов в новый клиент.

Для успешного перемещения виртуальной машины необходимо перенести саму виртуальную машину и все вспомогательные ресурсы. Используйте [список ресурсов az](/cli/azure/resource#az_resource_list) команду, чтобы вывести список всех ресурсов в группе ресурсов и их идентификаторы. Желательно сохранить выходные данные этой команды в файл, чтобы позже можно было копировать и вставлять идентификаторы в командах.

```azurecli-interactive
az resource list --resource-group "mySourceResourceGroup" --query "[].{Id:id}" --output table
```

Чтобы переместить виртуальную Машину и его ресурсам в другой группе ресурсов, используйте [перемещения ресурса az](/cli/azure/resource#az_resource_move). Ниже показано, как переместить ВМ и наиболее распространенные ресурсы, которые для этого понадобятся. Используйте **-идентификаторы** параметра и передайте его в список с разделителями запятыми (без пробелов) идентификаторов для ресурсов для перемещения.

```azurecli-interactive
vm=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
nic=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkInterfaces/myNIC
nsg=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG
pip=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIPAddress
vnet=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/virtualNetworks/myVNet
diag=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mydiagnosticstorageaccount
storage=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageacountname    

az resource move \
    --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag \
    --destination-group "myDestinationResourceGroup"
```

Если вы хотите переместить виртуальную Машину и его ресурсы в другую подписку, добавьте **--subscriptionId назначения** для указания назначения подписки.

Если вам будет предложено подтвердить перемещения указанного ресурса. Введите **Y** , чтобы подтвердить перемещение ресурсов.

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Дальнейшие действия
Вы можете перемещать разные типы ресурсов между группами ресурсов и подписками. Дополнительные сведения см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](../../resource-group-move-resources.md).    

