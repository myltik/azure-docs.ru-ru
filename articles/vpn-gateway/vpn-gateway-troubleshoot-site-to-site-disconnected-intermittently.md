---
title: Устранение проблемы периодических разрывов подключений VPN типа "сеть — сеть" Azure | Документация Майкрософт
description: Сведения об устранении регулярных разрывов подключений VPN типа "сеть — сеть".
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: cshepard
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 9c827469080195054d4ff70ab72fc123365a73df
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34160594"
---
# <a name="troubleshooting-azure-site-to-site-vpn-disconnects-intermittently"></a>Устранение проблемы периодических разрывов подключений VPN типа "сеть — сеть" Azure

У вас может возникнуть проблема: новое или существующее VPN-подключение типа "сеть — сеть" Microsoft Azure может быть нестабильным или регулярно отключаться. В этой статье приведены действия, которые помогут определить и устранить причину проблемы. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Действия по устранению неполадок

### <a name="prerequisite-step"></a>Предварительные действия

Проверьте тип шлюза виртуальной сети Azure.

1. Перейдите на [портал Azure](https://portal.azure.com).
2. Ознакомьтесь со страницей **обзора** шлюза виртуальной сети, чтобы получить сведения о типе шлюза.
    
    ![Обзор шлюза](media\vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently\gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Шаг 1. Узнайте, проверено ли локальное устройство VPN

1. Узнайте, используете ли вы [проверенную версию VPN-устройства и операционной системы](vpn-gateway-about-vpn-devices.md#devicetable). Если VPN-устройство непроверенное, может потребоваться обратиться к изготовителю устройства, чтобы узнать о возможных проблемах совместимости.
2. Убедитесь, что VPN-устройство настроено правильно. Дополнительные сведения см. на странице об [изменении примеров конфигурации устройств](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-check-the-security-association-settingsfor-policy-based-azure-virtual-network-gateways"></a>Шаг 2. Проверьте параметры сопоставления безопасности (для шлюзов виртуальной сети Azure на основе политик)

1. Убедитесь, что виртуальная сеть, подсети и диапазоны в определении **шлюза локальной сети** в Microsoft Azure соответствуют конфигурации локального VPN-устройства.
2. Убедитесь, что параметры сопоставления безопасности совпадают.

### <a name="step-3-check-for-user-defined-routes-or-network-security-groups-on-gateway-subnet"></a>Шаг 3. Проверьте определяемые пользователем маршруты или группы безопасности сети в подсети шлюза

Определяемый пользователем маршрут в подсети шлюза может ограничивать часть трафика и разрешать другой трафик. Таким образом VPN-подключение является ненадежным для одной части трафика и хорошим для другой. 

### <a name="step-4-check-the-one-vpn-tunnel-per-subnet-pair-setting-for-policy-based-virtual-network-gateways"></a>Шаг 4. Проверьте параметр One VPN Tunnel per Subnet Pair (Один VPN-туннель на пару подсетей) (для шлюзов виртуальной сети на основе политик)

Убедитесь, что для локального VPN-устройства задан параметр **One VPN tunnel per subnet pair** (Один VPN-туннель на пару подсетей) для шлюзов виртуальной сети на основе политик.

### <a name="step-5-check-for-security-association-limitation-for-policy-based-virtual-network-gateways"></a>Шаг 5. Проверьте ограничения сопоставления безопасности (для шлюзов виртуальной сети на основе политик)

Шлюз виртуальной сети на основе политик имеет ограничение на сопоставления безопасности подсети в 200 пар. Если количество подсетей виртуальной сети Azure, умноженное на количество локальных подсетей, превышает 200, могут случайно отключаться подсети.

### <a name="step-6-check-on-premises-vpn-device-external-interface-address"></a>Шаг 6. Проверьте внешний адрес интерфейса для локального VPN-устройства

- Если IP-адрес для Интернета VPN-устройства включен в определение **шлюза локальной сети** в Azure, вы можете столкнуться с нерегулярными отключениями.
- К внешнему интерфейсу устройства должен быть прямой доступ через Интернет. Между Интернетом и устройством не должно использоваться преобразование сетевых адресов (NAT) или брандмауэр.
-  Если вы настраивайте виртуальный IP-адрес для кластеризации брандмауэра, вы должны отключить кластер и предоставить устройство VPN непосредственно общедоступному интерфейсу, с которым может взаимодействовать шлюз.

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-enabled"></a>Шаг 7. Проверьте, включена ли для локального VPN-устройства функция полной безопасности пересылки

Функция **полной безопасности пересылки** (PFS) может вызвать проблемы отключения. Если для VPN-устройства включена функция **полной безопасности пересылки** (PFS), отключите эту функцию. Затем [обновите политику IPsec шлюза виртуальной сети](vpn-gateway-ipsecikepolicy-rm-powershell.md#managepolicy).

## <a name="next-steps"></a>Дополнительная информация

- [Создание подключения типа "сеть — сеть" на портале Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Настройка политики IPsec/IKE для VPN-подключений типа "сеть — сеть" или "виртуальная сеть — виртуальная сеть"](vpn-gateway-ipsecikepolicy-rm-powershell.md)

