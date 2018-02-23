---
title: "Установка сертификата клиента для подключений типа \"точка — сеть\" | Azure"
description: "Установка сертификата клиента Mac или Windows для аутентификации на основе сертификата для подключений типа \"точка — сеть\"."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: 
tags: azure-resource-manager, azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2018
ms.author: cherylmc
ms.openlocfilehash: de98201b65f5531f334aded1056f622cecb6e190
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2018
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

## <a name="next-steps"></a>Дополнительная информация

Продолжайте настраивать параметры подключения типа "точка — сеть".

* [портал Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
* [Портал Azure (классический)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)