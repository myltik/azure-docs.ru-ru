---
title: Изменение префиксов IP-адресов шлюза локальной сети и IP-адреса VPN- шлюза | Azure | Интерфейс командной строки | Документация Майкрософт
description: Из этой статьи вы узнаете, как изменять префиксы IP-адресов для шлюза локальной сети с помощью Azure CLI.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 8c7db48f-d09a-44e7-836f-1fb6930389df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/29/2017
ms.author: cherylmc
ms.openlocfilehash: d62fa14ea24a9b62e793ef7022a761897984df32
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2017
ms.locfileid: "25990628"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-cli"></a>Изменение параметров шлюза локальной сети с помощью Azure CLI

Иногда такие параметры шлюза локальной сети, как AddressPrefix или GatewayIPAddress, могут изменяться. В этой статье описывается, как изменить параметры шлюза локальной сети. Эти параметры можно изменить с использованием другого метода, выбрав вариант из следующего списка:

> [!div class="op_single_selector"]
> * [портал Azure](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [интерфейс командной строки Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before"></a>Перед началом работы

Установите последнюю версию команд интерфейса командной строки (версии 2.0 или более позднюю). Сведения об установке команд интерфейса командной строки см. в статье [Install Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (Установка Azure CLI 2.0).

[!INCLUDE [CLI-login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="ipaddprefix"></a>Изменение префиксов IP-адресов

[!INCLUDE [modify-prefix](../../includes/vpn-gateway-modify-ip-prefix-cli-include.md)]

## <a name="gwip"></a>Изменение IP-адреса шлюза

[!INCLUDE [modify-gateway-IP](../../includes/vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

## <a name="next-steps"></a>Дополнительная информация

Проверьте подключение шлюза. См. статью [Проверка подключения шлюза](vpn-gateway-verify-connection-resource-manager.md).

