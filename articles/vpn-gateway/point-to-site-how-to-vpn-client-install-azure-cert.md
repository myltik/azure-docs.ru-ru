---
title: "Установка сертификата клиента для подключений типа \"точка — сеть\" | Azure"
description: "В этой статье описано, как установить сертификат клиента для аутентификации на основе сертификата для подключений типа \"точка — сеть\"."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager, azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2017
ms.author: cherylmc
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: fc0cc37794ef291c9d27b094211b38cec90da55c
ms.contentlocale: ru-ru
ms.lasthandoff: 09/25/2017

---
# <a name="install-a-client-certificate-for-point-to-site-azure-certificate-authentication-connections"></a>Установка сертификата клиента для аутентификации Azure на основе сертификата при подключениях типа "точка — сеть"

Для настройки подключений типа "точка — сеть" между клиентами и виртуальной сетью с использованием аутентификации Azure на основе сертификата требуется сертификат клиента. В этой статье описано, как установить сертификат клиента для аутентификации при подключении типа "точка — сеть" к виртуальной сети.

## <a name="generate"></a>Создание и экспорт сертификата клиента

Вы можете создать сертификат клиента либо на основе корневого сертификата, созданного с помощью корпоративного ЦС, либо на основе самозаверяющего сертификата. Дополнительные сведения см. в инструкциях по использованию [PowerShell](vpn-gateway-certificates-point-to-site.md) или [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). Создав сертификаты клиента, экспортируйте их как PFX-файлы. Не забудьте при экспорте включить всю цепочку сертификатов.

## <a name="installwin"></a>Установка сертификата на клиентах Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="installmac"></a>Установка сертификата на клиентах Mac

VPN-клиенты Mac поддерживают только модель развертывания на основе Resource Manager. Классическую модель использовать нельзя.

> [!NOTE]
>  Протокол IKEv2 сейчас доступен в предварительной версии.
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="next-steps"></a>Дальнейшие действия

Продолжайте настраивать параметры подключения типа "точка — сеть".

* [Портал Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
* [Портал Azure (классический)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
