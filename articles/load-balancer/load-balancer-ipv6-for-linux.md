---
title: "Настройка DHCPv6 для виртуальных машин Linux | Документация Майкрософт"
description: "Узнайте, как настроить DHCPv6 для виртуальных машин Linux."
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
keywords: "IPv6, Azure Load Balancer, двойной стек, общедоступный IP-адрес, встроенная поддержка Ipv6, мобильное устройство, Интернет вещей"
ms.assetid: b32719b6-00e8-4cd0-ba7f-e60e8146084b
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: cf1eafc7bca5bddeb32f1e1e05e660d6877ed805
ms.openlocfilehash: f14a0473ebcf21e04759cb8eb77eb684c4d9a9cb

---

# <a name="configuring-dhcpv6-for-linux-vms"></a>Настройка DHCPv6 для виртуальных машин Linux

В некоторых образах виртуальных машин Linux в Azure Marketplace протокол DHCPv6 не настроен по умолчанию. Для поддержки IPv6 в используемом дистрибутиве ОС Linux необходимо настроить DHCPv6. В разных дистрибутивах Linux для настройки DHCPv6 применяются различные способы, так как они используют разные пакеты.

> [!NOTE]
> В недавно выпущенных образах SUSE Linux и CoreOS в Azure Marketplace протокол DHCPv6 предварительно настроен. При использовании этих образов дополнительные изменения не требуются.

В этом документе описывается, как включить DHCPv6, чтобы виртуальная машина Linux получила IPv6-адрес.

> [!WARNING]
> Неправильное изменение файлов конфигурации сети может привести к тому, что виртуальная машина утратит доступ к сети. Рекомендуется сначала протестировать изменения конфигурации на нерабочих системах. Приведенные в этой статье инструкции были протестированы на последних версиях образов Linux в Azure Marketplace. Подробные инструкции см. в документации к своей версии Linux.

## <a name="ubuntu"></a>Ubuntu

1. Измените файл `/etc/dhcp/dhclient6.conf` , добавив следующую строку:

        timeout 10;

2. Замените конфигурацию сети для интерфейса eth0 следующей конфигурацией:

   * В **Ubuntu 12.04 и 14.04** измените файл `/etc/network/interfaces.d/eth0.cfg`.
   * В **Ubuntu 16.04** измените файл `/etc/network/interfaces.d/50-cloud-init.cfg`.

        iface eth0 inet6 auto        up sleep 5        up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Обновите IPv6-адрес:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="debian"></a>Debian

1. Измените файл `/etc/dhcp/dhclient6.conf` , добавив следующую строку:

        timeout 10;

2. Измените файл `/etc/network/interfaces` , добавив следующую конфигурацию:

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Обновите IPv6-адрес:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel--centos--oracle-linux"></a>RHEL / CentOS / Oracle Linux

1. Измените файл `/etc/sysconfig/network` , добавив следующий параметр:

        NETWORKING_IPV6=yes

2. Измените файл `/etc/sysconfig/network-scripts/ifcfg-eth0` , добавив следующие два параметра:

        IPV6INIT=yes
        DHCPV6C=yes

3. Обновите IPv6-адрес:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11--opensuse-13"></a>SLES 11 и openSUSE 13

В недавно выпущенных образах SLES и openSUSE в Azure протокол DHCPv6 предварительно настроен. При использовании этих образов дополнительные изменения не требуются. Если у вас есть виртуальная машина, использующая более старый или пользовательский образ SUSE, то выполните описанные ниже действия.

1. При необходимости установите пакет `dhcp-client` :

    ```bash
    sudo zypper install dhcp-client
    ```

2. Измените файл `/etc/sysconfig/network/ifcfg-eth0` , добавив следующий параметр:

        DHCLIENT6_MODE='managed'

3. Обновите IPv6-адрес:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 и openSUSE Leap

В недавно выпущенных образах SLES и openSUSE в Azure протокол DHCPv6 предварительно настроен. При использовании этих образов дополнительные изменения не требуются. Если у вас есть виртуальная машина, использующая более старый или пользовательский образ SUSE, то выполните описанные ниже действия.

1. Измените файл `/etc/sysconfig/network/ifcfg-eth0` , заменив параметр

        #BOOTPROTO='dhcp4'

    следующим значением:

        BOOTPROTO='dhcp'

2. Добавьте следующий параметр в `/etc/sysconfig/network/ifcfg-eth0`:

        DHCLIENT6_MODE='managed'

3. Обновите IPv6-адрес:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

В недавно выпущенных образах CoreOS в Azure протокол DHCPv6 предварительно настроен. При использовании этих образов дополнительные изменения не требуются. Если у вас есть виртуальная машина, использующая более старый или пользовательский образ CoreOS, то выполните описанные ниже действия.

1. Измените файл `/etc/systemd/network/10_dhcp.network`

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Обновите IPv6-адрес:

    ```bash
    sudo systemctl restart systemd-networkd
    ```



<!--HONumber=Nov16_HO3-->


