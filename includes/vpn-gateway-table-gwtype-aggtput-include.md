---
title: включение файла
description: включение файла
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c9457e51858d4a073d8baffdd435c8100d95d566
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
|**SKU**   | **Туннели<br>S2S или "виртуальная сеть — виртуальная сеть"** | **Подключения<br>P2S** | **Эталонная агрегированная<br>пропускная способность** |
|---       | ---                             | ---                    | ---                         |
|**VpnGw1**| Макс. 30                         | Макс. 128               | 650 Мбит/с                    |
|**VpnGw2**| Макс. 30                         | Макс. 128               | 1 Гбит/с                      |
|**VpnGw3**| Макс. 30                         | Макс. 128               | 1,25 Гбит/с                   |
|**базовая;** | Макс. 10                         | Макс. 128               | 100 Мбит/с                    | 
|          |                                 |                        |                             | 

- Эталонная агрегированная пропускная способность основана на измерениях нескольких туннелей, агрегированных для одного шлюза. Определенное значение пропускной способности не может гарантироваться. Оно зависит от характеристик интернет-трафика и особенностей работы вашего приложения.

- Дополнительные сведения о ценах можно просмотреть на [этой странице](https://azure.microsoft.com/pricing/details/vpn-gateway) .

- См. сведения о [соглашении об уровне обслуживания](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

- Номера SKU VpnGw1, VpnGw2 и VpnGw3 поддерживаются только для VPN-шлюзов, созданных на основе модели развертывания с помощью Resource Manager.