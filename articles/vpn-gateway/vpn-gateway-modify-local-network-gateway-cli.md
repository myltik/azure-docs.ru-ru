---
title: "Изменение префиксов IP-адресов шлюза локальной сети и IP-адреса VPN- шлюза | Azure | Интерфейс командной строки | Документация Майкрософт"
description: "Из этой статьи вы узнаете, как изменять префиксы IP-адресов для шлюза локальной сети с помощью Azure CLI."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 8c7db48f-d09a-44e7-836f-1fb6930389df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/24/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 81c1a6e2357c8d336449224fac841be8dcc2a3cd
ms.contentlocale: ru-ru
ms.lasthandoff: 04/27/2017


---
# <a name="modify-local-network-gateway-settings-using-the-azure-cli"></a>Изменение параметров шлюза локальной сети с помощью Azure CLI

Иногда такие параметры шлюза локальной сети, как AddressPrefix или GatewayIPAddress, могут изменяться. В этой статье описывается, как изменить параметры шлюза локальной сети. Эти параметры можно изменить на портале Azure или с помощью PowerShell.

## <a name="before-you-begin"></a>Перед началом работы

Установите последнюю версию команд интерфейса командной строки (версии 2.0 или более позднюю). Сведения об установке команд интерфейса командной строки см. в статье [Install Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (Установка Azure CLI 2.0).

[!INCLUDE [CLI-login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="modify-ip-address-prefixes"></a>Изменение префиксов IP-адресов

[!INCLUDE [modify-prefix](../../includes/vpn-gateway-modify-ip-prefix-cli-include.md)]

## <a name="modify-the-gateway-ip-address"></a>Изменение IP-адреса шлюза

[!INCLUDE [modify-gateway-IP](../../includes/vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

## <a name="next-steps"></a>Дальнейшие действия

Проверьте подключение шлюза. См. статью [Проверка подключения шлюза](vpn-gateway-verify-connection-resource-manager.md).


