---
title: Создание и передача виртуального жесткого диска Red Hat Enterprise Linux для использования в Azure | Корпорация Майкрософт
description: Узнайте, как создать и передать виртуальный жесткий диск (VHD) Azure, содержащий операционную систему RedHat Linux.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 6c6b8f72-32d3-47fa-be94-6cb54537c69f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: szark
ms.openlocfilehash: 9893ab83584e2fc93ea9cf29c0b74b957080d5ad
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33778398"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Подготовка виртуальной машины на основе Red Hat для Azure
В этой статье вы узнаете, как подготовить виртуальную машину Red Hat Enterprise Linux (RHEL) для использования в Azure. В статье описываются версии RHEL 6.7+ и 7.1+. Низкоуровневые оболочки для подготовки, о которых идет речь в этой статье, — это Hyper-V, Kernel-based Virtual Machine (KVM) и VMware. Подробнее о требованиях к участникам в программе Red Hat Cloud Access см. на [веб-сайте Red Hat Cloud Access](http://www.redhat.com/en/technologies/cloud-computing/cloud-access) и странице [запуска RHEL в Azure](https://access.redhat.com/ecosystem/ccsp/microsoft-azure).

## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>Подготовка виртуальной машины под управлением Red Hat в диспетчере Hyper-V

### <a name="prerequisites"></a>предварительным требованиям
В этом разделе предполагается, что вы уже получили ISO-файл с веб-сайта Red Hat и установили образ RHEL на виртуальный жесткий диск. Дополнительные сведения о том, как использовать диспетчер Hyper-V для установки образа операционной системы, см. в статье [Установка Hyper-V и создание виртуальной машины](http://technet.microsoft.com/library/hh846766.aspx).

**Замечания по установке RHEL**

* Формат VHDX не поддерживается в Azure. В Azure поддерживается только формат фиксированного виртуального жесткого диска. Вы можете преобразовать диск в формат VHD с помощью диспетчера Hyper-V или командлета convert-vhd. Если вы используете VirtualBox, при создании диска выберите **фиксированный размер** вместо динамически выделяемого (по умолчанию).
* В Azure поддерживаются только виртуальные машины поколения 1. Виртуальную машину первого поколения можно преобразовать из формата VHDX в формат VHD, а также переключить с динамически расширяемого диска на диск фиксированного размера. Вы не можете изменить поколение виртуальной машины. Дополнительные сведения см. в статье [Необходимо создать виртуальную машину поколения 1 или 2 в Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
* Максимально допустимый размер виртуального жесткого диска составляет 1023 ГБ.
* При установке операционной системы Linux рекомендуется использовать стандартные разделы, а не диспетчер логических томов (LVM), который часто используется по умолчанию при установке. Это позволит избежать конфликта имен LVM при клонировании виртуальных машин, особенно если диск с OC может быть подключен к другой идентичной виртуальной машине в целях устранения неполадок. Для дисков данных можно использовать [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) или [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Требуется поддержка ядра для подключения файловых систем UDF. При первой загрузке в Azure UDF-носитель, подключенный к гостевой машине, передает конфигурацию подготовки в виртуальную машину Linux. Агент Azure Linux должен иметь возможность подключать файловую систему UDF для считывания конфигурации и подготовки виртуальной машины.
* Версии ядра Linux (до версии 2.6.37) не поддерживают доступ к неоднородной памяти (NUMA) в Hyper-V с виртуальными машинами больших размеров. Эта проблема влияет в основном на дистрибутивы более ранних версий, в которых используется исходное ядро Red Hat 2.6.32, и была исправлена в RHEL 6.6 (kernel-2.6.32-504). В системах под управлением пользовательских ядер старше версии 2.6.37 или ядер RHEL старше 2.6.32-504 в командной строке ядра необходимо задать параметр загрузки `numa=off` в файле grub.conf. Дополнительные сведения см. на странице о [проблемах гостевой машины Red Hat Enterprise Linux 6 на виртуальной машине Microsoft Hyper-V](https://access.redhat.com/solutions/436883).
* Не настраивайте раздел swap на диске операционной системы. Вы можете настроить агент Linux для создания файла подкачки на временном диске ресурсов.  Дополнительные сведения описаны ниже.
* Размер виртуальной памяти всех VHD в Azure должен быть округлен до 1 МБ. При конвертации диска в формате RAW в виртуальный жесткий диск убедитесь, что размер диска RAW в несколько раз превышает 1 МБ. Дополнительные сведения можно найти в инструкциях ниже. См. также дополнительные сведения в [примечаниях по установке Linux](create-upload-generic.md#general-linux-installation-notes).

### <a name="prepare-a-rhel-6-virtual-machine-from-hyper-v-manager"></a>Подготовка виртуальной машины RHEL 6 в диспетчере Hyper-V

1. В диспетчере Hyper-V выберите виртуальную машину.

2. Щелкните **Подключение** , чтобы открыть окно консоли для виртуальной машины.

3. В RHEL 6 NetworkManager может мешать работе агента Linux для Azure. Установите пакет, выполнив следующую команду:
   
        # sudo rpm -e --nodeps NetworkManager

4. Создайте или измените файл `/etc/sysconfig/network`, добавив следующий текст:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5. Создайте или измените файл `/etc/sysconfig/network-scripts/ifcfg-eth0`, добавив следующий текст:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6. Переместите (или удалите) правила udev, чтобы не создавать статические правила для интерфейса Ethernet. Эти правила приводят к появлению проблем при клонировании виртуальной машины в Microsoft Azure или Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

7. Убедитесь, что сетевая служба запускается во время загрузки, выполнив следующую команду:

        # sudo chkconfig network on

8. Зарегистрируйте подписку Red Hat, чтобы активировать установку пакетов из репозитория RHEL, запустив следующую команду:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

9. Пакет WALinuxAgent `WALinuxAgent-<version>` был отправлен в репозиторий дополнений Red Hat. Включите репозиторий дополнений, выполнив следующую команду:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

10. Измените строку загрузки ядра в конфигурации grub, чтобы включить дополнительные параметры ядра для Azure. Для этого откройте файл `/boot/grub/menu.lst` в текстовом редакторе и укажите для ядра по умолчанию следующие параметры:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Это также гарантирует отправку всех сообщений консоли на первый последовательный порт, что может помочь технической поддержке Azure в плане отладки.
    
    В дополнение к вышесказанному мы рекомендуем удалить следующие параметры:
    
        rhgb quiet crashkernel=auto
    
    Графическая и "тихая" загрузка бесполезны в облачной среде, в которой нам нужно, чтобы все журналы отправлялись на последовательный порт.  При желании можно оставить параметр `crashkernel`. Обратите внимание, что этот параметр сокращает объем доступной памяти на виртуальной машине на 128 MБ и более. Это может оказаться проблемой для виртуальных машин небольшого размера.

    >[!Important]
    Для RHEL 6.5 и более ранних версий необходимо также указать параметр ядра `numa=off`. См. посвященный Red Hat [раздел 436883](https://access.redhat.com/solutions/436883).

11. Убедитесь, что SSH-сервер установлен и настроен для включения во время загрузки (что обычно задано по умолчанию). Измените файл /etc/ssh/sshd_config, включив в него следующую строку:

        ClientAliveInterval 180

12. Установите агент Linux для Azure, выполнив следующую команду:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

    Установка пакета WALinuxAgent приводит к удалению пакетов NetworkManager и NetworkManager-gnome, если они еще не были удалены, как описано на шаге 3.

13. Не создавайте пространство подкачки на диске ОС.

    Агент Linux для Azure может автоматически настраивать размер области подкачки с использованием локального диска ресурсов, подключенного к виртуальной машине после ее подготовки в Azure. Следует отметить, что локальный диск ресурсов является временным и должен быть очищен при отмене подготовки виртуальной машины. После установки агента Linux для Azure (см. предыдущий шаг) измените соответствующим образом следующие параметры в файле /etc/waagent.conf:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14. Отмените регистрацию подписки (при необходимости), выполнив следующую команду:

        # sudo subscription-manager unregister

15. Выполните следующие команды, чтобы отменить подготовку виртуальной машины и подготовить ее в Azure:

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

16. В диспетчере Hyper-V выберите **Действие** > **Завершение работы**. Виртуальный жесткий диск Linux готов к передаче в Azure.


### <a name="prepare-a-rhel-7-virtual-machine-from-hyper-v-manager"></a>Подготовка виртуальной машины RHEL 7 в диспетчере Hyper-V

1. В диспетчере Hyper-V выберите виртуальную машину.

2. Щелкните **Подключение** , чтобы открыть окно консоли для виртуальной машины.

3. Создайте или измените файл `/etc/sysconfig/network`, добавив следующий текст:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4. Создайте или измените файл `/etc/sysconfig/network-scripts/ifcfg-eth0`, добавив следующий текст:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

5. Убедитесь, что сетевая служба запускается во время загрузки, выполнив следующую команду:

        # sudo systemctl enable network

6. Зарегистрируйте подписку Red Hat, чтобы активировать установку пакетов из репозитория RHEL, запустив следующую команду:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7. Измените строку загрузки ядра в конфигурации grub, чтобы включить дополнительные параметры ядра для Azure. Для этого откройте файл `/etc/default/grub` в текстовом редакторе и измените параметр `GRUB_CMDLINE_LINUX`. Например: 
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Это также гарантирует отправку всех сообщений консоли на первый последовательный порт, что может помочь технической поддержке Azure в плане отладки. Также будут отключены новые соглашения об именовании RHEL 7 для сетевых карт. В дополнение к вышесказанному мы рекомендуем удалить следующие параметры:
   
        rhgb quiet crashkernel=auto
   
    Графическая и "тихая" загрузка бесполезны в облачной среде, в которой нам нужно, чтобы все журналы отправлялись на последовательный порт. При желании можно оставить параметр `crashkernel`. Обратите внимание, что этот параметр сокращает объем доступной памяти на виртуальной машине на 128 MБ и более. Это может оказаться проблемой для виртуальных машин небольшого размера.

8. После внесения изменений в файл `/etc/default/grub`выполните следующую команду, чтобы повторно создать конфигурацию grub:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

9. Убедитесь, что SSH-сервер установлен и настроен для включения во время загрузки (что обычно задано по умолчанию). Измените файл `/etc/ssh/sshd_config` , добавив в него следующую строку:

        ClientAliveInterval 180

10. Пакет WALinuxAgent `WALinuxAgent-<version>` был отправлен в репозиторий дополнений Red Hat. Включите репозиторий дополнений, выполнив следующую команду:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

11. Установите агент Linux для Azure, выполнив следующую команду:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

12. Не создавайте пространство подкачки на диске ОС.

    Агент Linux для Azure может автоматически настраивать размер области подкачки с использованием локального диска ресурсов, подключенного к виртуальной машине после ее подготовки в Azure. Следует отметить, что локальный диск ресурсов является временным и должен быть очищен при отмене подготовки виртуальной машины. После установки агента Linux для Azure (см. предыдущий шаг) измените соответствующим образом следующие параметры в файле `/etc/waagent.conf`:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. Чтобы отменить регистрацию подписки, выполните следующую команду:

        # sudo subscription-manager unregister

14. Выполните следующие команды, чтобы отменить подготовку виртуальной машины и подготовить ее в Azure:

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

15. В диспетчере Hyper-V выберите **Действие** > **Завершение работы**. Виртуальный жесткий диск Linux готов к передаче в Azure.


## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Подготовка виртуальной машины под управлением Red Hat в KVM
### <a name="prepare-a-rhel-6-virtual-machine-from-kvm"></a>Подготовка виртуальной машины RHEL 6 в KVM

1. Скачайте образ KVM RHEL 6 с веб-сайта Red Hat.

2. Задайте пароль пользователя root.

    Создайте зашифрованный пароль и скопируйте результат выполнения команды:

        # openssl passwd -1 changeme

    Установите корневой пароль с помощью Guestfish:
        
        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Во втором поле привилегированного пользователя замените "!!" зашифрованным паролем.

3. Создайте виртуальную машину в KVM из образа qcow2. Укажите тип диска **qcow2** и задайте для модели устройства интерфейса виртуальной сети значение **virtio**. Затем запустите виртуальную машину и войдите в систему как привилегированный пользователь.

4. Создайте или измените файл `/etc/sysconfig/network`, добавив следующий текст:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5. Создайте или измените файл `/etc/sysconfig/network-scripts/ifcfg-eth0`, добавив следующий текст:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6. Переместите (или удалите) правила udev, чтобы не создавать статические правила для интерфейса Ethernet. Эти правила приводят к появлению проблем при клонировании виртуальной машины в Azure или Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

7. Убедитесь, что сетевая служба запускается во время загрузки, выполнив следующую команду:

        # chkconfig network on

8. Зарегистрируйте подписку Red Hat, чтобы активировать установку пакетов из репозитория RHEL, запустив следующую команду:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

9. Измените строку загрузки ядра в конфигурации grub, чтобы включить дополнительные параметры ядра для Azure. Для этого откройте файл `/boot/grub/menu.lst` в текстовом редакторе и укажите для ядра по умолчанию следующие параметры:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Это также гарантирует отправку всех сообщений консоли на первый последовательный порт, что может помочь технической поддержке Azure в плане отладки.
    
    В дополнение к вышесказанному мы рекомендуем удалить следующие параметры:
    
        rhgb quiet crashkernel=auto
    
    Графическая и "тихая" загрузка бесполезны в облачной среде, в которой нам нужно, чтобы все журналы отправлялись на последовательный порт. При желании можно оставить параметр `crashkernel`. Обратите внимание, что этот параметр сокращает объем доступной памяти на виртуальной машине на 128 MБ и более. Это может оказаться проблемой для виртуальных машин небольшого размера.

    >[!Important]
    Для RHEL 6.5 и более ранних версий необходимо также указать параметр ядра `numa=off`. См. посвященный Red Hat [раздел 436883](https://access.redhat.com/solutions/436883).

10. Добавьте модули Hyper-V в initramfs.  

    Измените файл `/etc/dracut.conf`, добавив следующее содержимое:

        add_drivers+="hv_vmbus hv_netvsc hv_storvsc"

    Повторно создайте initramfs:

        # dracut -f -v

11. Удаление cloud-init:

        # yum remove cloud-init

12. Убедитесь, что SSH-сервер установлен и настроен для включения во время загрузки:

        # chkconfig sshd on

    Измените файл /etc/ssh/sshd_config, включив в него следующие строки:

        PasswordAuthentication yes
        ClientAliveInterval 180

13. Пакет WALinuxAgent `WALinuxAgent-<version>` был отправлен в репозиторий дополнений Red Hat. Включите репозиторий дополнений, выполнив следующую команду:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

14. Установите агент Linux для Azure, выполнив следующую команду:

        # yum install WALinuxAgent

        # chkconfig waagent on

15. Агент Linux для Azure может автоматически настраивать размер области подкачки с использованием локального диска ресурсов, подключенного к виртуальной машине после ее подготовки в Azure. Следует отметить, что локальный диск ресурсов является временным и должен быть очищен при отмене подготовки виртуальной машины. После установки агента Linux для Azure (см. предыдущий шаг) измените соответствующим образом следующие параметры в файле **/etc/waagent.conf**:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Отмените регистрацию подписки (при необходимости), выполнив следующую команду:

        # subscription-manager unregister

17. Выполните следующие команды, чтобы отменить подготовку виртуальной машины и подготовить ее в Azure:

        # waagent -force -deprovision

        # export HISTSIZE=0

        # logout

18. Остановите работу виртуальной машины в KVM.

19. Конвертируйте образ qcow2 в формат VHD.

> [!NOTE]
> В команде qemu-img версии 2.2.1 и более поздних версиях есть ошибка, которая приводит к созданию неверного формата VHD. Эта проблема устранена в QEMU версии 2.6. Рекомендуется использовать qemu-img версии 2.2.0 или более ранних версий. Либо выполните обновление до версии 2.6 или выше. Справочные материалы: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f qcow2 -O raw rhel-6.9.qcow2 rhel-6.9.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.9.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd

        
### <a name="prepare-a-rhel-7-virtual-machine-from-kvm"></a>Подготовка виртуальной машины RHEL 7 в KVM

1. Скачайте образ KVM RHEL 7 с веб-сайта Red Hat. В примере используется RHEL 7.

2. Задайте пароль пользователя root.

    Создайте зашифрованный пароль и скопируйте результат выполнения команды:

        # openssl passwd -1 changeme

    Установите корневой пароль с помощью Guestfish:

        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Во втором поле привилегированного пользователя замените "!!" зашифрованным паролем.

3. Создайте виртуальную машину в KVM из образа qcow2. Укажите тип диска **qcow2** и задайте для модели устройства интерфейса виртуальной сети значение **virtio**. Затем запустите виртуальную машину и войдите в систему как привилегированный пользователь.

4. Создайте или измените файл `/etc/sysconfig/network`, добавив следующий текст:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5. Создайте или измените файл `/etc/sysconfig/network-scripts/ifcfg-eth0`, добавив следующий текст:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

6. Убедитесь, что сетевая служба запускается во время загрузки, выполнив следующую команду:

        # sudo systemctl enable network

7. Зарегистрируйте подписку Red Hat, чтобы активировать установку пакетов из репозитория RHEL, запустив следующую команду:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

8. Измените строку загрузки ядра в конфигурации grub, чтобы включить дополнительные параметры ядра для Azure. Для этого откройте файл `/etc/default/grub` в текстовом редакторе и измените параметр `GRUB_CMDLINE_LINUX`. Например: 
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Эта команда также гарантирует отправку всех сообщений консоли на первый последовательный порт, что может помочь технической поддержке Azure в плане отладки. Также будут отключены новые соглашения об именовании RHEL 7 для сетевых карт. В дополнение к вышесказанному мы рекомендуем удалить следующие параметры:
   
        rhgb quiet crashkernel=auto
   
    Графическая и "тихая" загрузка бесполезны в облачной среде, в которой нам нужно, чтобы все журналы отправлялись на последовательный порт. При желании можно оставить параметр `crashkernel`. Обратите внимание, что этот параметр сокращает объем доступной памяти на виртуальной машине на 128 MБ и более. Это может оказаться проблемой для виртуальных машин небольшого размера.

9. После внесения изменений в файл `/etc/default/grub`выполните следующую команду, чтобы повторно создать конфигурацию grub:

        # grub2-mkconfig -o /boot/grub2/grub.cfg

10. Добавьте модули Hyper-V в initramfs.

    Измените файл `/etc/dracut.conf` , добавив в него следующее содержимое:

        add_drivers+="hv_vmbus hv_netvsc hv_storvsc"

    Повторно создайте initramfs:

        # dracut -f -v

11. Удаление cloud-init:

        # yum remove cloud-init

12. Убедитесь, что SSH-сервер установлен и настроен для включения во время загрузки:

        # systemctl enable sshd

    Измените файл /etc/ssh/sshd_config, включив в него следующие строки:

        PasswordAuthentication yes
        ClientAliveInterval 180

13. Пакет WALinuxAgent `WALinuxAgent-<version>` был отправлен в репозиторий дополнений Red Hat. Включите репозиторий дополнений, выполнив следующую команду:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

14. Установите агент Linux для Azure, выполнив следующую команду:

        # yum install WALinuxAgent

    Включите службу waagent:

        # systemctl enable waagent.service

15. Не создавайте пространство подкачки на диске ОС.

    Агент Linux для Azure может автоматически настраивать размер области подкачки с использованием локального диска ресурсов, подключенного к виртуальной машине после ее подготовки в Azure. Следует отметить, что локальный диск ресурсов является временным и должен быть очищен при отмене подготовки виртуальной машины. После установки агента Linux для Azure (см. предыдущий шаг) измените соответствующим образом следующие параметры в файле `/etc/waagent.conf`:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Отмените регистрацию подписки (при необходимости), выполнив следующую команду:

        # subscription-manager unregister

17. Выполните следующие команды, чтобы отменить подготовку виртуальной машины и подготовить ее в Azure:

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

18. Остановите работу виртуальной машины в KVM.

19. Конвертируйте образ qcow2 в формат VHD.

> [!NOTE]
> В команде qemu-img версии 2.2.1 и более поздних версиях есть ошибка, которая приводит к созданию неверного формата VHD. Эта проблема устранена в QEMU версии 2.6. Рекомендуется использовать qemu-img версии 2.2.0 или более ранних версий. Либо выполните обновление до версии 2.6 или выше. Справочные материалы: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.4.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>Подготовка виртуальной машины под управлением Red Hat в VMware
### <a name="prerequisites"></a>предварительным требованиям
В этом разделе предполагается, что вы уже установили виртуальную машину RHEL в VMWare. Дополнительные сведения об установке операционной системы на виртуальной машине VMware см. [здесь](http://partnerweb.vmware.com/GOSIG/home.html).

* При установке операционной системы Linux мы рекомендуем использовать стандартные разделы, а не LVM (который зачастую используется по умолчанию при установке). Это позволит избежать конфликта имен LVM при клонировании виртуальных машин, особенно если диск с OC может быть подключен к другой идентичной виртуальной машине в целях устранения неполадок. При желании на дисках с данными можно использовать LVM или RAID.
* Не настраивайте раздел swap на диске операционной системы. Вы можете настроить агент Linux для создания файла подкачки на временном диске с ресурсами. Дополнительные сведения приведены ниже.
* При создании виртуального жесткого диска выберите параметр **Store virtual disk as a single file**(Сохранять виртуальный диск как один файл).

### <a name="prepare-a-rhel-6-virtual-machine-from-vmware"></a>Подготовка виртуальной машины RHEL 6 в VMware
1. В RHEL 6 NetworkManager может мешать работе агента Linux для Azure. Установите пакет, выполнив следующую команду:
   
        # sudo rpm -e --nodeps NetworkManager

2. Создайте файл с именем **network** в каталоге /etc/sysconfig/. Файл должен содержать следующий текст:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

3. Создайте или измените файл `/etc/sysconfig/network-scripts/ifcfg-eth0`, добавив следующий текст:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

4. Переместите (или удалите) правила udev, чтобы не создавать статические правила для интерфейса Ethernet. Эти правила приводят к появлению проблем при клонировании виртуальной машины в Azure или Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

5. Убедитесь, что сетевая служба запускается во время загрузки, выполнив следующую команду:

        # sudo chkconfig network on

6. Зарегистрируйте подписку Red Hat, чтобы активировать установку пакетов из репозитория RHEL, запустив следующую команду:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7. Пакет WALinuxAgent `WALinuxAgent-<version>` был отправлен в репозиторий дополнений Red Hat. Включите репозиторий дополнений, выполнив следующую команду:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

8. Измените строку загрузки ядра в конфигурации grub, чтобы включить дополнительные параметры ядра для Azure. Для этого откройте файл `/etc/default/grub` в текстовом редакторе и измените параметр `GRUB_CMDLINE_LINUX`. Например: 
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"
   
   Это также гарантирует отправку всех сообщений консоли на первый последовательный порт, что может помочь технической поддержке Azure в плане отладки. В дополнение к вышесказанному мы рекомендуем удалить следующие параметры:
   
        rhgb quiet crashkernel=auto
   
    Графическая и "тихая" загрузка бесполезны в облачной среде, в которой нам нужно, чтобы все журналы отправлялись на последовательный порт. При желании можно оставить параметр `crashkernel`. Обратите внимание, что этот параметр сокращает объем доступной памяти на виртуальной машине на 128 MБ и более. Это может оказаться проблемой для виртуальных машин небольшого размера.

9. Добавьте модули Hyper-V в initramfs.

    Измените файл `/etc/dracut.conf`, добавив следующее содержимое:

        add_drivers+="hv_vmbus hv_netvsc hv_storvsc"

    Повторно создайте initramfs:

        # dracut -f -v

10. Убедитесь, что SSH-сервер установлен и настроен для включения во время загрузки (что обычно задано по умолчанию). Измените файл `/etc/ssh/sshd_config` , добавив в него следующую строку:

    ClientAliveInterval 180

11. Установите агент Linux для Azure, выполнив следующую команду:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

12. Не создавайте пространство подкачки на диске ОС.

    Агент Linux для Azure может автоматически настраивать размер области подкачки с использованием локального диска ресурсов, подключенного к виртуальной машине после ее подготовки в Azure. Следует отметить, что локальный диск ресурсов является временным и должен быть очищен при отмене подготовки виртуальной машины. После установки агента Linux для Azure (см. предыдущий шаг) измените соответствующим образом следующие параметры в файле `/etc/waagent.conf`:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. Отмените регистрацию подписки (при необходимости), выполнив следующую команду:

        # sudo subscription-manager unregister

14. Выполните следующие команды, чтобы отменить подготовку виртуальной машины и подготовить ее в Azure:

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

15. Завершите работу виртуальной машины и конвертируйте файл VMDK в формат VHD.

> [!NOTE]
> В команде qemu-img версии 2.2.1 и более поздних версиях есть ошибка, которая приводит к созданию неверного формата VHD. Эта проблема устранена в QEMU версии 2.6. Рекомендуется использовать qemu-img версии 2.2.0 или более ранних версий. Либо выполните обновление до версии 2.6 или выше. Справочные материалы: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f vmdk -O raw rhel-6.9.vmdk rhel-6.9.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.9.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd


### <a name="prepare-a-rhel-7-virtual-machine-from-vmware"></a>Подготовка виртуальной машины RHEL 7 в VMware
1. Создайте или измените файл `/etc/sysconfig/network`, добавив следующий текст:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

2. Создайте или измените файл `/etc/sysconfig/network-scripts/ifcfg-eth0`, добавив следующий текст:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

3. Убедитесь, что сетевая служба запускается во время загрузки, выполнив следующую команду:

        # sudo systemctl enable network

4. Зарегистрируйте подписку Red Hat, чтобы активировать установку пакетов из репозитория RHEL, запустив следующую команду:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

5. Измените строку загрузки ядра в конфигурации grub, чтобы включить дополнительные параметры ядра для Azure. Для этого откройте файл `/etc/default/grub` в текстовом редакторе и измените параметр `GRUB_CMDLINE_LINUX`. Например: 
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Это также гарантирует отправку всех сообщений консоли на первый последовательный порт, что может помочь технической поддержке Azure в плане отладки. Также будут отключены новые соглашения об именовании RHEL 7 для сетевых карт. В дополнение к вышесказанному мы рекомендуем удалить следующие параметры:
   
        rhgb quiet crashkernel=auto
   
    Графическая и "тихая" загрузка бесполезны в облачной среде, в которой нам нужно, чтобы все журналы отправлялись на последовательный порт. При желании можно оставить параметр `crashkernel`. Обратите внимание, что этот параметр сокращает объем доступной памяти на виртуальной машине на 128 MБ и более. Это может оказаться проблемой для виртуальных машин небольшого размера.

6. После внесения изменений в файл `/etc/default/grub`выполните следующую команду, чтобы повторно создать конфигурацию grub:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

7. Добавьте модули Hyper-V в initramfs.

    Измените файл `/etc/dracut.conf`, добавив в него следующее содержимое:

        add_drivers+="hv_vmbus hv_netvsc hv_storvsc"

    Повторно создайте initramfs:

        # dracut -f -v

8. Убедитесь, что SSH-сервер установлен и настроен для включения во время загрузки. Обычно этот параметр задан по умолчанию. Измените файл `/etc/ssh/sshd_config` , добавив в него следующую строку:

        ClientAliveInterval 180

9. Пакет WALinuxAgent `WALinuxAgent-<version>` был отправлен в репозиторий дополнений Red Hat. Включите репозиторий дополнений, выполнив следующую команду:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

10. Установите агент Linux для Azure, выполнив следующую команду:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

11. Не создавайте пространство подкачки на диске ОС.

    Агент Linux для Azure может автоматически настраивать размер области подкачки с использованием локального диска ресурсов, подключенного к виртуальной машине после ее подготовки в Azure. Следует отметить, что локальный диск ресурсов является временным и должен быть очищен при отмене подготовки виртуальной машины. После установки агента Linux для Azure (см. предыдущий шаг) измените соответствующим образом следующие параметры в файле `/etc/waagent.conf`:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

12. Чтобы отменить регистрацию подписки, выполните следующую команду:

        # sudo subscription-manager unregister

13. Выполните следующие команды, чтобы отменить подготовку виртуальной машины и подготовить ее в Azure:

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

14. Завершите работу виртуальной машины и конвертируйте файл VMDK в формат VHD.

> [!NOTE]
> В команде qemu-img версии 2.2.1 и более поздних версиях есть ошибка, которая приводит к созданию неверного формата VHD. Эта проблема устранена в QEMU версии 2.6. Рекомендуется использовать qemu-img версии 2.2.0 или более ранних версий. Либо выполните обновление до версии 2.6 или выше. Справочные материалы: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f vmdk -O raw rhel-7.4.vmdk rhel-7.4.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.4.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>Подготовка виртуальной машины под управлением Red Hat из ISO-образа с помощью автоматического использования файла kickstart
### <a name="prepare-a-rhel-7-virtual-machine-from-a-kickstart-file"></a>Подготовка виртуальной машины RHEL 7 из файла kickstart

1.  Создайте файл kickstart, который будет включать содержимое ниже, и сохраните его. Дополнительные сведения об установке kickstart см. [здесь](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

        # Kickstart for provisioning a RHEL 7 Azure VM

        # System authorization information
          auth --enableshadow --passalgo=sha512

        # Use graphical install
        text

        # Do not run the Setup Agent on first boot
        firstboot --disable

        # Keyboard layouts
        keyboard --vckeymap=us --xlayouts='us'

        # System language
        lang en_US.UTF-8

        # Network information
        network  --bootproto=dhcp

        # Root password
        rootpw --plaintext "to_be_disabled"

        # System services
        services --enabled="sshd,waagent,NetworkManager"

        # System timezone
        timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

        # Partition clearing information
        clearpart --all --initlabel

        # Clear the MBR
        zerombr

        # Disk partitioning information
        part /boot --fstype="xfs" --size=500
        part / --fstyp="xfs" --size=1 --grow --asprimary

        # System bootloader configuration
        bootloader --location=mbr

        # Firewall configuration
        firewall --disabled

        # Enable SELinux
        selinux --enforcing

        # Don't configure X
        skipx

        # Power down the machine after install
        poweroff

        %packages
        @base
        @console-internet
        chrony
        sudo
        parted
        -dracut-config-rescue

        %end

        %post --log=/var/log/anaconda/post-install.log

        #!/bin/bash

        # Register Red Hat Subscription
        subscription-manager register --username=XXX --password=XXX --auto-attach --force

        # Install latest repo update
        yum update -y

        # Enable extras repo
        subscription-manager repos --enable=rhel-7-server-extras-rpms

        # Install WALinuxAgent
        yum install -y WALinuxAgent

        # Unregister Red Hat subscription
        subscription-manager unregister

        # Enable waaagent at boot-up
        systemctl enable waagent

        # Disable the root account
        usermod root -p '!!'

        # Configure swap in WALinuxAgent
        sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
        sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

        # Set the cmdline
        sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

        # Enable SSH keepalive
        sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

        # Build the grub cfg
        grub2-mkconfig -o /boot/grub2/grub.cfg

        # Configure network
        cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no
        EOF

        # Deprovision and prepare for Azure
        waagent -force -deprovision

        %end

2. Поместите файл kickstart в место, доступное для системы установки.

3. Создайте новую виртуальную машину в диспетчере Hyper-V. На странице **Подключить виртуальный жесткий диск** выберите параметр **Подключить виртуальный жесткий диск позднее** и выполните инструкции в мастере создания виртуальной машины.

4. Откройте параметры виртуальной машины:

    a.  Подключите новый виртуальный жесткий диск к виртуальной машине. Выберите параметры **VHD Format** (Формат VHD) и **Фиксированный размер**.

    Б.  Подключите установочный ISO-образ к DVD-дисководу.

    c.  В BIOS выберите загрузку с компакт-диска.

5. Запустите виртуальную машину. Когда отобразится руководство по установке, нажмите клавишу **TAB** , чтобы настроить параметры загрузки.

6. Введите `inst.ks=<the location of the kickstart file>` в конце параметров загрузки и нажмите клавишу **ВВОД**.

7. Дождитесь завершения процесса установки. По окончании процесса виртуальная машина завершит работу автоматически. Виртуальный жесткий диск Linux готов к передаче в Azure.

## <a name="known-issues"></a>Известные проблемы
### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>Не удалось включить драйвер Hyper-V в начальный электронный диск при использовании низкоуровневой оболочки Hyper V

В некоторых случаях установщики Linux могут не включать драйверы для Hyper-V в начальный электронный диск (initrd или initramfs), если только не обнаружат, что он работает в среде Hyper-V.

Если для подготовки образа Linux используется другая система виртуализации (например, Virtualbox, Xen и пр.), вам может потребоваться выполнить повторную сборку initrd, чтобы убедиться, что на начальном электронном диске доступны по крайней мере модули ядра hv_vmbus и hv_storvsc. Это известная проблема по крайней мере в системах на основе предшествующего дистрибутива Red Hat.

Чтобы устранить эту проблему, необходимо добавить модули Hyper-V в initramfs и выполнить повторную сборку:

Измените файл `/etc/dracut.conf`, добавив следующее содержимое:

        add_drivers+="hv_vmbus hv_netvsc hv_storvsc"

Повторно создайте initramfs:

        # dracut -f -v

Дополнительные сведения см. в описании [повторного создания initramfs](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Дополнительная информация
Теперь виртуальный жесткий диск Red Hat Enterprise Linux можно использовать для создания новых виртуальных машин Azure. Если вы отправляете VHD-файл в Azure впервые, см. раздел [Вариант 1. Передача VHD](upload-vhd.md#option-1-upload-a-vhd).

Чтобы получить дополнительные сведения о низкоуровневых оболочках, сертифицированных для запуска Red Hat Enterprise Linux, посетите [веб-сайт Red Hat](https://access.redhat.com/certified-hypervisors).
