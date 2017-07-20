---
title: "Устранение проблемы подключения VPN типа \"сеть — сеть\" Azure | Документация Майкрософт"
description: "Узнайте, как устранить проблему подключения VPN типа \"сеть — сеть\" (когда отсутствует подключение и невозможно выполнить повторное подключение)."
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: cshepard
editor: 
tags: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/21/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: 61fd58063063d69e891d294e627ae40cb878d65b
ms.openlocfilehash: a72a46e4c2c93fdb3ab1a26c8854adb8cdc488a5
ms.contentlocale: ru-ru
ms.lasthandoff: 06/23/2017

---

# <a name="troubleshooting-azure-site-to-site-vpn-connection-cannot-connect-and-stops-working"></a>Устранение проблемы подключения VPN типа "сеть — сеть" Azure

Вы узнаете, как настроить подключение VPN типа "сеть — сеть" между локальной сетью и виртуальной сетью Microsoft Azure. VPN-подключение внезапно завершается сбоем и его невозможно восстановить. В этой статье приведены действия по устранению этой проблемы. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Действия по устранению неполадок

Чтобы устранить эту проблему, попробуйте сначала [сбросить VPN-шлюз виртуальной сети Azure](vpn-gateway-resetgw-classic.md), а также сбросить туннель в локальном VPN-устройстве. Если проблему устранить не удалось, выполните действия ниже, чтобы определить причину проблемы.

### <a name="prerequisite-step"></a>Предварительные требования

Проверьте тип шлюза виртуальной сети Azure.

1. Перейдите на [портал Azure](https://portal.azure.com).
2. Ознакомьтесь со страницей **обзора** шлюза виртуальной сети, чтобы получить сведения о типе шлюза.
    
    ![Обзор шлюза](media\vpn-gateway-troubleshoot-site-to-site-cannot-connect\gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Шаг 1. Узнайте, проверено ли локальное устройство VPN

1. Узнайте, используете ли вы [проверенную версию ОС и VPN-устройства](vpn-gateway-about-vpn-devices.md#a-namedevicetableavalidated-vpn-devices-and-device-configuration-guides). Если устройство является непроверенным VPN-устройством, может потребоваться обратиться к изготовителю устройства, чтобы узнать о возможных проблемах совместимости.
2. Убедитесь, что VPN-устройство настроено правильно. Дополнительные сведения см. на странице об [изменении примеров конфигурации устройств](/vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-verify-the-shared-keypsk"></a>Шаг 2. Проверьте общий ключ (PSK)

Сравните **общий ключ** от локального VPN-устройства и виртуальной сети, чтобы убедиться, что они совпадают. 

Чтобы просмотреть общий ключ для подключения VPN Azure, используйте один из методов ниже:

**Портал Azure**

1. Перейдите к созданному подключению VPN типа "сеть — сеть".
2. В разделе **Параметры** щелкните **Общий ключ**.
    
    ![Общий ключ](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/sharedkey.png)

**Azure PowerShell**

Для модели развертывания диспетчера ресурсов

    Get-AzureRmVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group name>

Для классической модели развертывания

    Get-AzureVNetGatewayKey -VNetName -LocalNetworkSiteName

### <a name="step-3-verify-the-vpn-peer-ips"></a>Шаг 3. Проверьте IP-адреса VPN-узла

-   Определение IP-адреса в объекте **шлюза локальной сети** в Azure должно совпадать с IP-адресом локального устройства.
-   Определение IP-адреса шлюза Azure, установленное в локальном устройстве, должно совпадать с IP-адресом шлюза Azure.

### <a name="step-4-nsg-and-udr-on-gateway-subnet"></a>Шаг 4. Проверьте в подсети шлюза определяемые пользователем маршруты (UDR) и группу безопасности сети (NSG)

Проверьте, есть ли в подсети шлюза определяемые пользователем маршруты (UDR) или группы безопасности сети (NSG) и удалите их, если они имеются. Затем проверьте, успешно ли выполнена операция удаления. Если проблему удалось устранить, проверьте, соответствующим ли образом настроены параметры для NSG и UDR.

### <a name="step-5-check-on-premises-vpn-device-external-interface-address"></a>Шаг 5. Проверьте внешний адрес интерфейса для локального VPN-устройства

- Если IP-адрес для Интернета VPN-устройства включен в определение **локальной сети** в Azure, вы можете столкнуться с нерегулярными отключениями.
- К внешнему интерфейсу устройства должен быть прямой доступ через Интернет. Между Интернетом и устройством не должно использоваться преобразование адресов сети или брандмауэр.
-  Если вы настраивайте виртуальный IP-адрес для кластеризации брандмауэра, вы должны отключить кластер и предоставить устройство VPN непосредственно общедоступному интерфейсу, с которым может взаимодействовать шлюз.

### <a name="step-6-verify--subnets-match-exactly-azure-policy-based-gateways"></a>Шаг 6. Проверьте полное соответствие подсетей (шлюзов на основе политик Azure)

-   Для виртуальной сети Azure подсети между виртуальной сетью Azure и локальными определениями должны полностью совпадать.
-   Для локальной сети подсети между **шлюзом локальной сети** и локальными определениями должны полностью совпадать.

### <a name="step-5-verify-azure-gateway-health-probe"></a>Шаг 7. Проверьте работоспособность шлюза Azure

1. Перейдите сюда: https://&lt;YourVirtualNetworkGatewayIP&gt;:8081/healthprobe
2. Просмотрите предупреждение о сертификате.
3. Если вы получите ответ, шлюз виртуальной сети считается работоспособным. Если вы не получите ответ, шлюз может быть неработоспособным, или проблема может быть вызвана наличием группы безопасности сети (NSG) в подсети шлюза. Пример ответа приведен ниже:

    &lt;?xml version="1.0"?>  <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Основной экземпляр: GatewayTenantWorker_IN_1 GatewayTenantVersion: 14.7.24.6</string&gt;

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-pfs-feature-enabled"></a>Шаг 8. Проверьте, включена ли для локального VPN-устройства функция полной безопасности пересылки (PFS)

Функция полной безопасности пересылки (PFS) может вызвать проблемы отключения. Если для локального VPN-устройства включена функция полной безопасности пересылки (PFS), отключите эту функцию. Затем обновите политику IPsec шлюза виртуальной сети.

## <a name="next-steps"></a>Дальнейшие действия

-   [Создание подключения типа "сеть — сеть" на портале Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
-   [Настройка политики IPsec/IKE для VPN-подключений типа "сеть — сеть" или "виртуальная сеть — виртуальная сеть"](vpn-gateway-ipsecikepolicy-rm-powershell.md)

