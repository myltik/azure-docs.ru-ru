---
title: "Настройка DHCPv6 для виртуальных машин Linux | Документы Microsoft"
description: "Узнайте, как настроить DHCPv6 для виртуальных машин Linux."
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
keywords: "IPv6, Azure Load Balancer, двойной стек, общедоступный IP-адрес, встроенная поддержка Ipv6, мобильное устройство, Интернет вещей"
ms.assetid: b32719b6-00e8-4cd0-ba7f-e60e8146084b
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: b46c2107dcfda5f02407e08daf08bd42d722dfda
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/09/2017
---
# <a name="configure-dhcpv6-for-linux-vms"></a>Настройка DHCPv6 для виртуальных машин Linux

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Некоторые из образов виртуальной машины Linux в Azure Marketplace нет Dynamic Host Configuration Protocol версии 6 (DHCPv6), настроенные по умолчанию. Для поддержки IPv6, необходимо настроить DHCPv6 в ОС Linux распределение, которое вы используете. Различные дистрибутивы Linux настройте DHCPv6 в различными способами, потому что они используют разные пакеты.

> [!NOTE]
> В недавно выпущенных образах SUSE Linux и CoreOS в Azure Marketplace протокол DHCPv6 предварительно настроен. При использовании этих изображений, без дополнительных изменений не требуется.

В этом документе описывается, как включить DHCPv6, чтобы виртуальная машина Linux получила IPv6-адрес.

> [!WARNING]
> Путем неправильно изменения файлов конфигурации сети, вы можете потерять доступ к сети для виртуальной Машины. Рекомендуется сначала протестировать изменения конфигурации на нерабочих системах. Приведенные в этой статье инструкции были протестированы на последних версиях образов Linux в Azure Marketplace. Более подробные инструкции см. в документации для версии Linux.

## <a name="ubuntu"></a>Ubuntu

1. Изменить */etc/dhcp/dhclient6.conf* файл и добавьте следующую строку:

        timeout 10;

2. Замените конфигурацию сети для интерфейса eth0 следующей конфигурацией:

   * На **Ubuntu 12.04 и 14.04**, изменить */etc/network/interfaces.d/eth0.cfg* файла. 
   * На **Ubuntu 16.04**, изменить */etc/network/interfaces.d/50-cloud-init.cfg* файла.

         iface eth0 inet6 auto
             up sleep 5
             up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Обновите IPv6-адрес:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="debian"></a>Debian

1. Изменить */etc/dhcp/dhclient6.conf* файл и добавьте следующую строку:

        timeout 10;

2. Изменить */etc/network/interfaces* и добавьте следующую конфигурацию:

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Обновите IPv6-адрес:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-centos-and-oracle-linux"></a>RHEL, CentOS и Oracle Linux

1. Изменить */etc/sysconfig/network* и добавьте следующий параметр:

        NETWORKING_IPV6=yes

2. Изменить */etc/sysconfig/network-scripts/ifcfg-eth0* и добавьте следующие два параметра:

        IPV6INIT=yes
        DHCPV6C=yes

3. Обновите IPv6-адрес:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-and-opensuse-13"></a>SLES 11 и 13 openSUSE

Последние SUSE Linux Enterprise Server (SLES) и openSUSE образы в Azure были предварительно настроенную DHCPv6. При использовании этих изображений, без дополнительных изменений не требуется. При наличии виртуальной Машины, которая основана на старую или пользовательского образа SUSE, выполните следующее:

1. При необходимости установите пакет `dhcp-client` :

    ```bash
    sudo zypper install dhcp-client
    ```

2. Изменить */etc/sysconfig/network/ifcfg-eth0* и добавьте следующий параметр:

        DHCLIENT6_MODE='managed'

3. Обновите IPv6-адрес:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 и openSUSE Leap

В недавно выпущенных образах SLES и openSUSE в Azure протокол DHCPv6 предварительно настроен. При использовании этих изображений, без дополнительных изменений не требуется. При наличии виртуальной Машины, которая основана на старую или пользовательского образа SUSE, выполните следующее:

1. Изменить */etc/sysconfig/network/ifcfg-eth0* и замените `#BOOTPROTO='dhcp4'` параметр со следующим значением:

        BOOTPROTO='dhcp'

2. Чтобы */etc/sysconfig/network/ifcfg-eth0* файл, добавьте следующий параметр:

        DHCLIENT6_MODE='managed'

3. Обновите IPv6-адрес:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

В недавно выпущенных образах CoreOS в Azure протокол DHCPv6 предварительно настроен. При использовании этих изображений, без дополнительных изменений не требуется. Если у вас есть на основе CoreOS более раннюю версию или пользовательского образа виртуальной Машины, выполните следующее:

1. Изменить */etc/systemd/network/10_dhcp.network* файла:

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Обновите IPv6-адрес:

    ```bash
    sudo systemctl restart systemd-networkd
    ```
