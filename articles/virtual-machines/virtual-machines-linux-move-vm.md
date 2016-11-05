---
title: Перемещение виртуальной машины Linux | Microsoft Docs
description: Из этой статьи вы узнаете, как перенести виртуальную машину Linux в другую подписку или группу ресурсов Azure в рамках модели развертывания Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2016
ms.author: cynthn

---
# Перемещение виртуальной машины Linux в другую подписку или группу ресурсов
В этой статье описано перемещение виртуальной машины Linux между группами ресурсов или подписками. Перемещение ВМ между подписками может понадобиться, если вы создали виртуальную машину в личной подписке и вам нужно переместить ее в корпоративную подписку.

> [!NOTE]
> Во время перемещения создаются новые идентификаторы ресурсов. После перемещения виртуальной машины вам нужно будет обновить средства и сценарии, чтобы использовать новые идентификаторы ресурсов.
> 
> 

## Использование Azure CLI для перемещения виртуальной машины
Для успешного перемещения виртуальной машины необходимо перенести саму виртуальную машину и все вспомогательные ресурсы. Используйте команду **azure group show**, чтобы вывести список всех ресурсов в группе ресурсов и их идентификаторы. Желательно сохранить выходные данные этой команды в файл, чтобы позже можно было копировать и вставлять идентификаторы в командах.

    azure group show <resourceGroupName>

Чтобы переместить виртуальную машину и ее ресурсы в другую группу, используйте команду CLI **azure resource move**. Ниже показано, как переместить ВМ и наиболее распространенные ресурсы, которые для этого понадобятся. Мы используем параметр **-i** и передаем разделенный запятыми список (без пробелов) идентификаторов ресурсов для перемещения.

    vm=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Compute/virtualMachines/<vmName>
    nic=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/networkInterfaces/<nicName>
    nsg=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/networkSecurityGroups/<nsgName>
    pip=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/publicIPAddresses/<publicIPName>
    vnet=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/virtualNetworks/<vnetName>
    diag=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Storage/storageAccounts/<diagnosticStorageAccountName>
    storage=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Storage/storageAccounts/<storageAcountName>      

    azure resource move --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag -d "<destinationResourceGroup>"

Если вы хотите переместить виртуальную машину и ее ресурсы в другую подписку, добавьте параметр **--destination-subscriptionId &#60;destinationSubscriptionID&#62;**, чтобы указать целевую подписку.

Если вы работаете на компьютере под управлением Windows, добавьте из командной строки символ **$** перед именами переменных при их объявлении. В Linux это не требуется.

Появится запрос на подтверждение перемещения указанного ресурса. Введите **Y**, чтобы подтвердить перемещение ресурсов.

[!INCLUDE [virtual-machines-common-move-vm](../../includes/virtual-machines-common-move-vm.md)]

## Дальнейшие действия
Вы можете перемещать разные типы ресурсов между группами ресурсов и подписками. Дополнительные сведения см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](../resource-group-move-resources.md).

<!---HONumber=AcomDC_0810_2016-->