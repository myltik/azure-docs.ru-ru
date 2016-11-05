---
title: Проверка подключения шлюза | Microsoft Docs
description: Из этой статьи вы узнаете, как проверить подключение шлюза в модели развертывания Resource Manager.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2016
ms.author: cherylmc

---
# Проверка подключения шлюза
Проверить подключение шлюза можно несколькими способами. Из этой статьи вы узнаете, как проверить состояние подключения шлюза Resource Manager с помощью портала Azure и PowerShell.

## Перед началом работы
Если вы планируете использовать PowerShell, установите последнюю версию командлетов PowerShell для Azure Resource Manager. Дополнительную информацию об установке командлетов PowerShell см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md). Дополнительную информацию об использовании командлетов Resource Manager см. в статье [Использование Azure PowerShell с Azure Resource Manager](../powershell-azure-resource-manager.md).

1. Откройте консоль PowerShell и подключитесь к своей учетной записи.
   
        Login-AzureRmAccount
2. Просмотрите подписки учетной записи.
   
        Get-AzureRmSubscription 
3. Укажите подписку, которую нужно использовать.
   
        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## Проверка подключения
[!INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## Дальнейшие действия
* В виртуальные сети можно добавлять виртуальные машины. Инструкции см. в статье [Создание виртуальной машины под управлением Windows на портале Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md).

<!---HONumber=AcomDC_0810_2016-->