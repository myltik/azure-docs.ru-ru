---
title: Проверка подключения VPN-шлюза | Документация Майкрософт
description: В этой статье показано, как проверить подключение VPN-шлюза виртуальной сети.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 7e3d1043-caa9-4472-96d3-832f4e2c91ee
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2017
ms.author: cherylmc
ms.openlocfilehash: b2d702ecdd5e1fca342e7c84c6e75339097f0bcd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23124952"
---
# <a name="verify-a-vpn-gateway-connection"></a>Проверка подключения VPN-шлюза

В этой статье вы узнаете, как проверить подключение VPN-шлюза в классической модели и в модели Resource Manager.

## <a name="azure-portal"></a>Портал Azure

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="powershell"></a>PowerShell

Чтобы проверить подключение VPN-шлюза для модели развертывания Resource Manager с помощью PowerShell, установите последнюю версию [командлетов PowerShell для Azure Resource Manager](/powershell/azure/overview).

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="azure-cli"></a>Инфраструктура CLI Azure

Чтобы проверить подключение VPN-шлюза для модели развертывания Resource Manager с помощью Azure CLI, установите последнюю версию [командлетов CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0 или более поздней версии).

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]


## <a name="azure-portal-classic"></a>Портал Azure (классическая модель)

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="powershell-classic"></a>PowerShell (классическая модель)

Чтобы проверить подключение VPN-шлюза для классической модели развертывания с помощью PowerShell, установите последнюю версию командлетов Azure PowerShell. Не забудьте загрузить и установить модуль [управления службами](https://docs.microsoft.com/powershell/azure/install-azure-ps?view=azuresmps-3.7.0). Используйте командлет Add-AzureAccount, чтобы войти в классическую модель развертывания.

[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>Дополнительная информация

* В виртуальные сети можно добавлять виртуальные машины. Инструкции см. в статье о [создании виртуальной машины](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).