---
title: "Проверка подключения VPN-шлюза | Документация Майкрософт"
description: "В этой статье показано, как проверить подключение VPN-шлюза виртуальной сети."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 7e3d1043-caa9-4472-96d3-832f4e2c91ee
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/30/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: b5bad71095e4b7e3b26df15780467526200ffa10
ms.openlocfilehash: 68d94a6402b1497f65c4d03fb987ba800e86c2a3


---
# <a name="verify-a-vpn-gateway-connection"></a>Проверка подключения VPN-шлюза
Подключение VPN-шлюза виртуальной сети можно проверить с помощью портала или PowerShell. Информация, приведенная в этой статье, относится к классической модели развертывания и модели развертывания с помощью Resource Manager.

## <a name="verify-using-the-azure-portal"></a>Проверка с помощью портала Azure

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="verify-using-powershell"></a>Проверка с помощью PowerShell

Чтобы выполнить проверку с помощью PowerShell, потребуется установить последнюю версию командлетов PowerShell для Azure Resource Manager. Дополнительные сведения об установке командлетов PowerShell см. в статье [Установка и настройка Azure PowerShell](/powershell/azureps-cmdlets-docs). Дополнительную информацию об использовании командлетов Resource Manager см. в статье [Использование Azure PowerShell с Azure Resource Manager](../powershell-azure-resource-manager.md).

### <a name="log-in-to-your-azure-account"></a>Вход в учетную запись Azure
1. Откройте консоль PowerShell с повышенными привилегиями и подключитесь к своей учетной записи.
   
        Login-AzureRmAccount
2. Просмотрите подписки учетной записи.
   
        Get-AzureRmSubscription 
3. Укажите подписку, которую нужно использовать.
   
        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

### <a name="verify-your-connection"></a>Проверка подключения

[!INCLUDE [Powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="verify-using-the-azure-portal-classic"></a>Проверка с помощью портала Azure (классическая модель)
[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


## <a name="verify-using-powershell-classic"></a>Проверка с помощью PowerShell (классическая модель)
Чтобы выполнить проверку с помощью PowerShell, потребуется установить последние версии командлетов PowerShell. Обязательно скачайте и установите версии для Resource Manager и для управления службами (SM). Дополнительные сведения об установке командлетов PowerShell см. в статье [Установка и настройка Azure PowerShell](/powershell/azureps-cmdlets-docs). 

### <a name="log-in-to-your-azure-account"></a>Вход в учетную запись Azure
1. Откройте консоль PowerShell с повышенными привилегиями и подключитесь к своей учетной записи.
   
        Login-AzureRmAccount
2. Просмотрите подписки учетной записи.
   
        Get-AzureRmSubscription 
3. Укажите подписку, которую нужно использовать.
   
        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
4. Выполните вход, чтобы использовать командлеты для управления службами для классической модели развертывания.

        Add-AzureAccount

### <a name="verify-your-connection"></a>Проверка подключения
[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>Дальнейшие действия
* В виртуальные сети можно добавлять виртуальные машины. Инструкции см. в статье о [создании виртуальной машины](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




<!--HONumber=Jan17_HO5-->


