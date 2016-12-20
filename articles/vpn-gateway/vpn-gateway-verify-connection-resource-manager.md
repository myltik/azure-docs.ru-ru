---
title: "Проверка подключения шлюза | Документация Майкрософт"
description: "Из этой статьи вы узнаете, как проверить подключение шлюза в модели развертывания Resource Manager."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 7e3d1043-caa9-4472-96d3-832f4e2c91ee
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/14/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 420b193d558d00f574d24c87ab9db6e9e757a952


---
# <a name="verify-a-gateway-connection"></a>Проверка подключения шлюза
Проверить подключение шлюза можно несколькими способами. Из этой статьи вы узнаете, как проверить состояние подключения шлюза Resource Manager с помощью портала Azure и PowerShell.

## <a name="verify-using-powershell"></a>Проверка с помощью PowerShell
Вам потребуется установить последнюю версию командлетов PowerShell диспетчера ресурсов Azure. Дополнительные сведения об установке командлетов PowerShell см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md). Дополнительную информацию об использовании командлетов Resource Manager см. в статье [Использование Azure PowerShell с Azure Resource Manager](../powershell-azure-resource-manager.md).

### <a name="step-1-log-in-to-your-azure-account"></a>Шаг 1. Вход в учетную запись Azure
1. Откройте консоль PowerShell с повышенными привилегиями и подключитесь к своей учетной записи.
   
        Login-AzureRmAccount
2. Просмотрите подписки учетной записи.
   
        Get-AzureRmSubscription 
3. Укажите подписку, которую нужно использовать.
   
        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

### <a name="step-2-verify-your-connection"></a>Шаг 2. Проверка подключения
[!INCLUDE [verify connection powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="verify-using-the-azure-portal"></a>Проверка с помощью портала Azure
[!INCLUDE [verify connection portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="next-steps"></a>Дальнейшие действия
* В виртуальные сети можно добавлять виртуальные машины. Инструкции см. в статье о [создании виртуальной машины](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




<!--HONumber=Nov16_HO3-->


