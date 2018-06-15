---
title: включение файла
description: включение файла
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9cdb608505e594e0020eb33abc869c6bf4b6b263
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/20/2018
ms.locfileid: "30326519"
---
| **поставщик** | **Семейство устройств** | **Версия встроенного ПО** |
| --- | --- | --- |
|Cisco | ISR| IOS 15.1 (предварительная версия)|
|Cisco | ASA | ASA (\*) RouteBased (IKEv2 — без BGP) для ASA ниже версии 9.8 |
|Cisco | ASA | ASA RouteBased (IKEv2 — без BGP) для ASA 9.8+ |
|Juniper | SRX_GA | 12.x|
|Juniper | SSG_GA | ScreenOS 6.2.x|
|Juniper | JSeries_GA | JunOS 12.x|
|Ubiquiti| EdgeRouter| Routebased v1.10x EdgeOS VTI|
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased BGP|

> [!NOTE]
> (*) Требуется: NarrowAzureTrafficSelectors и CustomAzurePolicies (протокол IKE или IPsec)
>