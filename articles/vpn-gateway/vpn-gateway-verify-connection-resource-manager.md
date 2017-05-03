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
ms.date: 04/24/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 0e9fa1b1397c60985de9d2e60b3f01146036801f
ms.lasthandoff: 04/26/2017


---
# <a name="verify-a-vpn-gateway-connection"></a>Проверка подключения VPN-шлюза

В этой статье вы узнаете, как проверить подключение VPN-шлюза и в классической модели, и в модели Resource Manager.

## <a name="verify-using-the-azure-portal"></a>Проверка с помощью портала Azure

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="verify-using-powershell"></a>Проверка с помощью PowerShell

Чтобы выполнить проверку с помощью PowerShell, потребуется установить последнюю версию командлетов PowerShell для Azure Resource Manager. Дополнительные сведения об установке командлетов PowerShell см. в статье [Установка и настройка Azure PowerShell](/powershell/azureps-cmdlets-docs). Дополнительную информацию об использовании командлетов Resource Manager см. в статье [Использование Azure PowerShell с Azure Resource Manager](../powershell-azure-resource-manager.md).

### <a name="log-in-to-your-azure-account"></a>Вход в учетную запись Azure
1. Откройте консоль PowerShell с повышенными привилегиями и подключитесь к своей учетной записи.

  ```powershell
  Login-AzureRmAccount
  ```
2. Просмотрите подписки учетной записи.

  ```powershell
  Get-AzureRmSubscription
  ``` 
3. Укажите подписку, которую нужно использовать.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```

### <a name="verify-your-connection"></a>Проверка подключения

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="verify-using-the-azure-cli"></a>Проверка с помощью Azure CLI

Для проверки с помощью Azure CLI установите последнюю версию команд интерфейса командной строки (версии 2.0 или более поздней). Сведения об установке команд интерфейса командной строки см. в статье [Install Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (Установка Azure CLI 2.0).

### <a name="log-in-to-your-azure-account"></a>Вход в учетную запись Azure

1. Войдите в подписку Azure с помощью команды [az login](/cli/azure/#login) и следуйте инструкциям на экране.

  ```azurecli
  az login
  ```
2. Если у вас есть несколько подписок Azure, укажите подписки для этой учетной записи.

  ```azurecli
  Az account list --all
  ```
3. Укажите подписку, которую нужно использовать.

  ```azurecli
  Az account set --subscription
  <replace_with_your_subscription_id>
  ```

### <a name="verify-your-connection"></a>Проверка подключения

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

## <a name="verify-using-the-azure-portal-classic"></a>Проверка с помощью портала Azure (классическая модель)
[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


## <a name="verify-using-powershell-classic"></a>Проверка с помощью PowerShell (классическая модель)
Чтобы выполнить проверку с помощью PowerShell, потребуется установить последние версии командлетов PowerShell. Обязательно скачайте и установите версии для Resource Manager и для управления службами (SM). Дополнительные сведения об установке командлетов PowerShell см. в статье [Установка и настройка Azure PowerShell](/powershell/azureps-cmdlets-docs). 

### <a name="log-in-to-your-azure-account"></a>Вход в учетную запись Azure
1. Откройте консоль PowerShell с повышенными привилегиями и подключитесь к своей учетной записи.

  ```powershell
  Login-AzureRmAccount
  ```
2. Просмотрите подписки учетной записи.

  ```powershell
  Get-AzureRmSubscription 
  ```
3. Укажите подписку, которую нужно использовать.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```
4. Выполните вход, чтобы использовать командлеты для управления службами для классической модели развертывания.

  ```powershell
  Add-AzureAccount
  ```

### <a name="verify-your-connection"></a>Проверка подключения
[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>Дальнейшие действия
* В виртуальные сети можно добавлять виртуальные машины. Инструкции см. в статье о [создании виртуальной машины](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


