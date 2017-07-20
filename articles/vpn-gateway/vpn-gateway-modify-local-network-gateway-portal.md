---
title: "Изменение префиксов IP-адресов шлюза локальной сети и IP-адреса VPN-шлюза | Azure | Портал | Документация Майкрософт"
description: "Из этой статьи вы узнаете, как изменять префиксы IP-адресов для шлюза локальной сети с помощью портала Azure."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: ebe57f497a007c5219c3b7f543ffb3bae9d161a8
ms.contentlocale: ru-ru
ms.lasthandoff: 06/20/2017


---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Изменение параметров шлюза локальной сети с помощью портала Azure

Иногда такие параметры шлюза локальной сети, как AddressPrefix или GatewayIPAddress, могут изменяться. В этой статье описывается, как изменить параметры шлюза локальной сети. Эти параметры можно изменить с использованием другого метода, выбрав вариант из следующего списка:

> [!div class="op_single_selector"]
> * [Портал Azure](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Интерфейс командной строки Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>


## <a name="modify-ip-address-prefixes"></a>Изменение префиксов IP-адресов

При изменении префиксов IP-адресов выполняемые действия зависят от того, имеет ли шлюз локальной сети подключение.

[!INCLUDE [modify prefix](../../includes/vpn-gateway-modify-ip-prefix-portal-include.md)]

## <a name="modify-the-gateway-ip-address"></a>Изменение IP-адреса шлюза

Если общедоступный IP-адрес VPN-устройства, к которому вы хотите подключиться, изменился, измените шлюз локальной сети в соответствии с изменениями. При изменении общедоступного IP-адреса выполняемые действия зависят от того, имеет ли шлюз локальной сети подключение.

[!INCLUDE [modify gateway IP](../../includes/vpn-gateway-modify-lng-gateway-ip-portal-include.md)]

## <a name="next-steps"></a>Дальнейшие действия

Проверьте подключение шлюза. См. статью [Проверка подключения шлюза](vpn-gateway-verify-connection-resource-manager.md).
