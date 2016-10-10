<properties
    pageTitle="Настройка DHCPv6 для виртуальных машин Linux | Microsoft Azure"
    description="Узнайте, как настроить DHCPv6 для виртуальных машин Linux."
    services="load-balancer"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="load-balancer"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/14/2016"
    ms.author="sewhee"
/>

# Настройка DHCPv6 для виртуальных машин Linux

В некоторых образах виртуальных машин Linux в Azure Marketplace протокол DHCPv6 не настроен по умолчанию. Для поддержки IPv6 в используемом дистрибутиве ОС Linux необходимо настроить DHCPv6. В разных дистрибутивах Linux для настройки DHCPv6 применяются различные способы, так как они используют разные пакеты.

>[AZURE.NOTE] В недавно выпущенных образах SUSE Linux и CoreOS в Azure Marketplace протокол DHCPv6 предварительно настроен. При использовании этих образов дополнительные изменения не требуются.

В этом документе описывается, как включить DHCPv6, чтобы виртуальная машина Linux получила IPv6-адрес.

>[AZURE.WARNING] Неправильное изменение файлов конфигурации сети может привести к тому, что виртуальная машина утратит доступ к сети. Рекомендуется сначала протестировать изменения конфигурации на нерабочих системах. Приведенные в этой статье инструкции были протестированы на последних версиях образов Linux в Azure Marketplace. Подробные инструкции см. в документации к своей версии Linux.

## Ubuntu

1. Измените файл `/etc/dhcp/dhclient6.conf`, добавив следующую строку:

        timeout 10;

2. Замените конфигурацию сети для интерфейса eth0 следующей конфигурацией:

    * В **Ubuntu 12.04 и 14.04** измените файл `/etc/network/interfaces.d/eth0.cfg`
    * В **Ubuntu 16.04** измените файл `/etc/network/interfaces.d/50-cloud-init.cfg`

    ```bash
    iface eth0 inet6 auto
        up sleep 5
        up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true
    ```

3. Обновите IPv6-адрес:

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## Debian

1. Измените файл `/etc/dhcp/dhclient6.conf`, добавив следующую строку:

        timeout 10;

2. Измените файл `/etc/network/interfaces`, добавив следующую конфигурацию:

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Обновите IPv6-адрес:

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## RHEL / CentOS / Oracle Linux

1. Измените файл `/etc/sysconfig/network`, добавив следующий параметр:

        NETWORKING_IPV6=yes

2. Измените файл `/etc/sysconfig/network-scripts/ifcfg-eth0`, добавив следующие два параметра:

        IPV6INIT=yes
        DHCPV6C=yes

3. Обновите IPv6-адрес:

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## SLES 11 и openSUSE 13

В недавно выпущенных образах SLES и openSUSE в Azure протокол DHCPv6 предварительно настроен. При использовании этих образов дополнительные изменения не требуются. Если у вас есть виртуальная машина, использующая более старый или пользовательский образ SUSE, то выполните описанные ниже действия.

1. При необходимости установите пакет `dhcp-client`:

    ```bash
    # sudo zypper install dhcp-client
    ```

2. Измените файл `/etc/sysconfig/network/ifcfg-eth0`, добавив следующий параметр:

        DHCLIENT6_MODE='managed'

3. Обновите IPv6-адрес:

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## SLES 12 и openSUSE Leap

В недавно выпущенных образах SLES и openSUSE в Azure протокол DHCPv6 предварительно настроен. При использовании этих образов дополнительные изменения не требуются. Если у вас есть виртуальная машина, использующая более старый или пользовательский образ SUSE, то выполните описанные ниже действия.

1. Измените файл `/etc/sysconfig/network/ifcfg-eth0`, заменив параметр

        #BOOTPROTO='dhcp4'

    следующим значением:

        BOOTPROTO='dhcp'

2. Добавьте следующий параметр в `/etc/sysconfig/network/ifcfg-eth0`:

        DHCLIENT6_MODE='managed'

3. Обновите IPv6-адрес:

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## CoreOS

В недавно выпущенных образах CoreOS в Azure протокол DHCPv6 предварительно настроен. При использовании этих образов дополнительные изменения не требуются. Если у вас есть виртуальная машина, использующая более старый или пользовательский образ CoreOS, то выполните описанные ниже действия.

1. Измените файл `/etc/systemd/network/10_dhcp.network`:

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Обновите IPv6-адрес:

    ```bash
    # sudo systemctl restart systemd-networkd
    ```

<!---HONumber=AcomDC_0928_2016-->