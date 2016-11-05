---
title: Создание и передача виртуального жесткого диска Red Hat Enterprise Linux для использования в Azure
description: Узнайте, как создать и передать виртуальный жесткий диск (VHD) Azure, содержащий операционную систему RedHat Linux.
services: virtual-machines-linux
documentationcenter: ''
author: SuperScottz
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/17/2016
ms.author: mingzhan

---
# Подготовка виртуальной машины на основе Red Hat для Azure
В этой статье вы узнаете, как подготовить виртуальную машину Red Hat Enterprise Linux (RHEL) для использования в Azure. В статье описываются версии RHEL 6.7, 7.1 и 7.2. Низкоуровневые оболочки для подготовки, о которых идет речь в статье, — это Hyper-V, Kernel-based Virtual Machine (KVM) и VMware. Подробнее о требованиях к участникам в программе Red Hat Cloud Access см. на [веб-сайте Red Hat Cloud Access](http://www.redhat.com/en/technologies/cloud-computing/cloud-access) и в статье [Запуск RHEL в Azure](https://access.redhat.com/articles/1989673).

[Подготовка виртуальной машины RHEL 6.7 в диспетчере Hyper-V](#rhel67hyperv)

[Подготовка виртуальной машины RHEL 7.1/7.2 в диспетчере Hyper-V](#rhel7xhyperv)

[Подготовка виртуальной машины RHEL 6.7 в KVM](#rhel67kvm)

[Подготовка виртуальной машины RHEL 7.1/7.2 в KVM](#rhel7xkvm)

[Подготовка виртуальной машины RHEL 6.7 в VMWare](#rhel67vmware)

[Подготовка виртуальной машины RHEL 7.1/7.2 в VMWare](#rhel7xvmware)

[Подготовка виртуальной машины RHEL 7.1/7.2 из файла kickstart](#rhel7xkickstart)

## Подготовка виртуальной машины под управлением Red Hat в диспетчере Hyper-V
### Предварительные требования
В этом разделе предполагается, что вы уже установили на виртуальный жесткий диск (VHD) образ RHEL (из ISO-файла, полученного на веб-сайте Red Hat). Дополнительные сведения о том, как использовать диспетчер Hyper-V для установки образа операционной системы, см. в разделе [Установка роли Hyper-V и настройка виртуальной машины](http://technet.microsoft.com/library/hh846766.aspx).

**Замечания по установке RHEL**

* Дополнительные сведения о подготовке Linux для Azure см. в разделе [Общие замечания по установке Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes).
* Более новый формат VHDX не поддерживается в Azure. Вы можете преобразовать диск в формат VHD с помощью диспетчера Hyper-V или командлета PowerShell **convert-vhd**.
* Виртуальные жесткие диски должны создаваться как "фиксированные", так как динамические виртуальные жесткие диски не поддерживаются.
* При установке системы Linux мы рекомендуем использовать стандартные разделы, а не LVM (как правило, значение по умолчанию во многих дистрибутивах). Это позволит избежать конфликта имен LVM при клонировании виртуальных машин, особенно если диск с OC может быть подключен к другой ВМ в целях устранения неполадок. Для дисков данных можно использовать LVM или [RAID](virtual-machines-linux-configure-raid.md).
* Не настраивайте раздел подкачки на диске с ОС. Вы можете настроить агент Linux для создания файла подкачки на временном диске с ресурсами. Дополнительные сведения об этом приводятся далее.
* Все VHD-диски должны иметь размер, кратный 1 МБ.
* Когда вы используете команду **qemu-img**, чтобы преобразовать образы дисков в формат VHD, обратите внимание, что в версии 2.2.1 и более поздних версиях этой команды есть ошибка. которая приводит к созданию неправильно отформатированного диска VHD. Эта ошибка должна быть устранена в следующем выпуске qemu-img. Сейчас мы рекомендуем использовать qemu-img 2.2.0 или более ранней версии.

### <a id="rhel67hyperv"> </a> Подготовка виртуальной машины RHEL 6.7 в диспетчере Hyper-V
1. В диспетчере Hyper-V выберите виртуальную машину.
2. Щелкните **Подключение**, чтобы открыть окно консоли для виртуальной машины.
3. Удалите NetworkManager, выполнив следующую команду:
   
   # sudo rpm -e --nodeps NetworkManager
   Обратите внимание: если пакет еще не установлен, эта команда завершится сбоем с сообщением об ошибке. Это ожидаемое поведение.
4. Создайте файл с именем **network** в каталоге `/etc/sysconfig/`. Файл должен содержать следующий текст:
   
     NETWORKING=yes
     HOSTNAME=localhost.localdomain
5. Создайте файл с именем **ifcfg-eth0** в каталоге `/etc/sysconfig/network-scripts/`. Файл должен содержать следующий текст:
   
     DEVICE=eth0
     ONBOOT=yes
     BOOTPROTO=dhcp
     TYPE=Ethernet
     USERCTL=no
     PEERDNS=yes
     IPV6INIT=no
6. Переместите или удалите правила udev во избежание создания статических правил для интерфейса Ethernet. Эти правила приводят к появлению проблем при клонировании виртуальной машины в Microsoft Azure или Hyper-V:
   
   # sudo mkdir -m 0700 /var/lib/waagent
   # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
   # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/
7. Убедитесь, что сетевая служба запускается во время загрузки, выполнив следующую команду:
   
   # sudo chkconfig network on
8. Зарегистрируйте подписку Red Hat, чтобы активировать установку пакетов из репозитория RHEL, запустив следующую команду:
   
   # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
9. Пакет WALinuxAgent `WALinuxAgent-<version>` был отправлен в репозиторий дополнений Red Hat. Включите репозиторий дополнений, выполнив следующую команду:
   
   # subscription-manager repos --enable=rhel-6-server-extras-rpms
10. Измените строку загрузки ядра в конфигурации grub, чтобы включить дополнительные параметры ядра для Azure. Для этого откройте файл `/boot/grub/menu.lst` в текстовом редакторе и укажите для ядра по умолчанию следующие параметры:
    
     console=ttyS0
     earlyprintk=ttyS0
     rootdelay=300
     numa=off
    
    Это также гарантирует отправку всех сообщений консоли на первый последовательный порт, что может помочь технической поддержке Azure в плане отладки. Это приведет к отключению NUMA в связи с неисправностью версии ядра, используемого RHEL 6.
    
    Кроме того, рекомендуем удалить следующие параметры:
    
     rhgb quiet crashkernel=auto
    
    Графическая и "тихая" загрузка бесполезны в облачной среде, в которой нам нужно, чтобы все журналы отправлялись на последовательный порт.
    
    При желании можно настроить параметр crashkernel, однако учтите, что он сокращает объем доступной памяти на виртуальной машине на 128 МБ и более. Это может оказаться проблемой на виртуальных машинах небольшого размера.
11. Убедитесь, что SSH-сервер установлен и настроен для включения во время загрузки. Обычно это сделано по умолчанию. Измените файл /etc/ssh/sshd\_config, включив в него следующую строку:
    
     ClientAliveInterval 180
12. Установите агент Linux для Azure, выполнив следующую команду:
    
    # sudo yum install WALinuxAgent
    # sudo chkconfig waagent on
    Обратите внимание, что установка пакета WALinuxAgent приведет к удалению пакетов NetworkManager и NetworkManager-gnome, если они еще не были удалены, как описано в шаге 2.
13. Не создавайте пространство подкачки на диске с ОС. Агент Linux для Azure может автоматически настраивать размер области подкачки с использованием локального диска с ресурсами, подключенного к виртуальной машине после подготовки в Azure. Следует отметить, что локальный диск ресурсов является временным диском и должен быть очищен при отмене подготовки виртуальной машины. После установки агента Linux для Azure (см. предыдущий шаг) измените соответствующим образом следующие параметры в файле /etc/waagent.conf:
    
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
16. В диспетчере Hyper-V выберите **Действие > Завершение работы**. Виртуальный жесткий диск Linux готов к передаче в Azure.

### <a id="rhel7xhyperv"> </a> Подготовка виртуальной машины RHEL 7.1 или 7.2 в диспетчере Hyper-V
1. В диспетчере Hyper-V выберите виртуальную машину.
2. Щелкните **Подключение**, чтобы открыть окно консоли для виртуальной машины.
3. Создайте файл с именем **network** в каталоге `/etc/sysconfig/`. Файл должен содержать следующий текст:
   
     NETWORKING=yes
     HOSTNAME=localhost.localdomain
4. Создайте файл с именем **ifcfg-eth0** в каталоге `/etc/sysconfig/network-scripts/`. Файл должен содержать следующий текст:
   
     DEVICE=eth0
     ONBOOT=yes
     BOOTPROTO=dhcp
     TYPE=Ethernet
     USERCTL=no
     PEERDNS=yes
     IPV6INIT=no
5. Убедитесь, что сетевая служба запускается во время загрузки, выполнив следующую команду:
   
   # sudo chkconfig network on
6. Зарегистрируйте подписку Red Hat, чтобы активировать установку пакетов из репозитория RHEL, запустив следующую команду:
   
   # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
7. Измените строку загрузки ядра в конфигурации grub, чтобы включить дополнительные параметры ядра для Azure. Для этого откройте файл `/etc/default/grub` в текстовом редакторе и измените параметр **GRUB\_CMDLINE\_LINUX**. Например:
   
     GRUB_CMDLINE_LINUX="rootdelay=300
     console=ttyS0
     earlyprintk=ttyS0"
   
   Это также гарантирует отправку всех сообщений консоли на первый последовательный порт, что может помочь технической поддержке Azure в плане отладки. Кроме того, рекомендуем удалить следующие параметры:
   
     rhgb quiet crashkernel=auto
   
   Графическая и "тихая" загрузка бесполезны в облачной среде, в которой нам нужно, чтобы все журналы отправлялись на последовательный порт. При желании можно настроить параметр crashkernel, однако учтите, что он сокращает объем доступной памяти на виртуальной машине на 128 МБ и более. Это может оказаться проблемой на виртуальных машинах небольшого размера.
8. После внесения изменений в файл `/etc/default/grub` выполните следующую команду, чтобы повторно создать конфигурацию grub:
   
   # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
9. Убедитесь, что SSH-сервер установлен и настроен для включения во время загрузки. Обычно это сделано по умолчанию. Измените файл `/etc/ssh/sshd_config`, добавив в него следующую строку:
   
     ClientAliveInterval 180
10. Пакет WALinuxAgent `WALinuxAgent-<version>` был отправлен в репозиторий дополнений Red Hat. Включите репозиторий дополнений, выполнив следующую команду:
    
    # subscription-manager repos --enable=rhel-7-server-extras-rpms
11. Установите агент Linux для Azure, выполнив следующую команду:
    
    # sudo yum install WALinuxAgent
    # sudo systemctl enable waagent.service
12. Не создавайте пространство подкачки на диске с ОС. Агент Linux для Azure может автоматически настраивать размер области подкачки с использованием локального диска с ресурсами, подключенного к виртуальной машине после подготовки в Azure. Следует отметить, что локальный диск ресурсов является временным диском и должен быть очищен при отмене подготовки виртуальной машины. После установки агента Linux для Azure (см. предыдущий шаг) измените соответствующим образом следующие параметры в файле `/etc/waagent.conf`:
    
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
15. В диспетчере Hyper-V выберите **Действие > Завершение работы**. Виртуальный жесткий диск Linux готов к передаче в Azure.

## Подготовка виртуальной машины под управлением Red Hat в KVM
### <a id="rhel67kvm"> </a> Подготовка виртуальной машины RHEL 6.7 в KVM
1. Загрузите KVM-образ RHEL 6.7 с веб-сайта Red Hat.
2. Задайте пароль пользователя root.
   
   Создайте зашифрованный пароль и скопируйте выходные данные команды:
   
   # openssl passwd -1 changeme
   Установите корневой пароль с помощью Guestfish:
   
   # guestfish --rw -a <image-name>
   > <fs> run
   > <fs> list-filesystems
   > <fs> mount /dev/sda1 /
   > <fs> vi /etc/shadow
   > <fs> exit
   > 
   > 
   
   Во втором поле привилегированного пользователя замените "!!" зашифрованным паролем.
3. Создайте виртуальную машину в KVM из образа qcow2, укажите тип диска **qcow2** и задайте для модели устройства интерфейса виртуальной сети значение **virtio**. Затем запустите виртуальную машину и войдите в систему как привилегированный пользователь.
4. Создайте файл с именем **network** в каталоге `/etc/sysconfig/`. Файл должен содержать следующий текст:
   
     NETWORKING=yes
     HOSTNAME=localhost.localdomain
5. Создайте файл с именем **ifcfg-eth0** в каталоге `/etc/sysconfig/network-scripts/`. Файл должен содержать следующий текст:
   
     DEVICE=eth0
     ONBOOT=yes
     BOOTPROTO=dhcp
     TYPE=Ethernet
     USERCTL=no
     PEERDNS=yes
     IPV6INIT=no
6. Переместите (или удалите) правила udev, чтобы не создавать статические правила для интерфейса Ethernet. Эти правила приводят к появлению проблем при клонировании виртуальной машины в Microsoft Azure или Hyper-V:
   
   # mkdir -m 0700 /var/lib/waagent
   # mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
   # mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/
7. Убедитесь, что сетевая служба запускается во время загрузки, выполнив следующую команду:
   
   # chkconfig network on
8. Зарегистрируйте подписку Red Hat, чтобы активировать установку пакетов из репозитория RHEL, запустив следующую команду:
   
   # subscription-manager register --auto-attach --username=XXX --password=XXX
9. Измените строку загрузки ядра в конфигурации grub, чтобы включить дополнительные параметры ядра для Azure. Для этого откройте файл `/boot/grub/menu.lst` в текстовом редакторе и укажите для ядра по умолчанию следующие параметры:
   
     console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off
   
   Это также гарантирует отправку всех сообщений консоли на первый последовательный порт, что может помочь технической поддержке Azure в плане отладки. Это приведет к отключению NUMA в связи с неисправностью версии ядра, используемого RHEL 6.
   
   Кроме того, рекомендуем удалить следующие параметры:
   
     rhgb quiet crashkernel=auto
   
   Графическая и "тихая" загрузка бесполезны в облачной среде, в которой нам нужно, чтобы все журналы отправлялись на последовательный порт. При желании можно настроить параметр crashkernel, однако учтите, что он сокращает объем доступной памяти на виртуальной машине на 128 МБ и более. Это может оказаться проблемой на виртуальных машинах небольшого размера.
10. Добавьте модули Hyper-V в initramfs:
    
    Измените файл `/etc/dracut.conf` и добавьте содержимое: add\_drivers+="hv\_vmbus hv\_netvsc hv\_storvsc".
    
    Повторно создайте initramfs: # dracut –f -v
11. Удаление cloud-init:
    
    # yum remove cloud-init
12. Убедитесь, что SSH-сервер установлен и настроен для включения во время загрузки:
    
    # chkconfig sshd on
    Измените файл /etc/ssh/sshd\_config, включив в него следующие строки:
    
     PasswordAuthentication yes
     ClientAliveInterval 180
    
    Перезапустите sshd:
    
    # service sshd restart
13. Пакет WALinuxAgent `WALinuxAgent-<version>` был отправлен в репозиторий дополнений Red Hat. Включите репозиторий дополнений, выполнив следующую команду:
    
    # subscription-manager repos --enable=rhel-6-server-extras-rpms
14. Установите агент Linux для Azure, выполнив следующую команду:
    
    # yum install WALinuxAgent
    # chkconfig waagent on
15. Агент Linux для Azure может автоматически настраивать размер области подкачки с использованием локального диска с ресурсами, подключенного к виртуальной машине после подготовки в Azure. Следует отметить, что локальный диск ресурсов является временным диском и должен быть очищен при отмене подготовки виртуальной машины. После установки агента Linux для Azure (см. предыдущий шаг) измените соответствующим образом следующие параметры в файле **/etc/waagent.conf**:
    
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
18. Завершите работу виртуальной машины в KVM.
19. Конвертируйте образ qcow2 в формат VHD. Для начала конвертируйте образ в формат RAW:
    
    # qemu-img convert -f qcow2 –O raw rhel-6.7.qcow2 rhel-6.7.raw
    Убедитесь, что размер образа в формате RAW соответствует 1 МБ. Если это не так, округлите размер образа до 1 МБ: # MB=$((1024*1024)) # size=$(qemu-img info -f raw --output json "rhel-6.7.raw" | \\ gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}') # rounded\_size=$((($size/$MB + 1)*$MB))
    
    # qemu-img resize rhel-6.7.raw $rounded_size
    Конвертируйте диск в формате RAW в виртуальный жесткий диск фиксированного размера:
    
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd

### <a id="rhel7xkvm"> </a> Подготовка виртуальной машины RHEL 7.1 или 7.2 в KVM
1. Скачайте KVM-образ RHEL 7.1 (или 7.2) с веб-сайта Red Hat. В этом примере мы используем RHEL 7.1.
2. Задайте пароль пользователя root.
   
   Создайте зашифрованный пароль и скопируйте результат выполнения команды:
   
   # openssl passwd -1 changeme
   Задайте пароль пользователя root с помощью guestfish.
   
   # guestfish --rw -a <image-name>
   > <fs> run
   > <fs> list-filesystems
   > <fs> mount /dev/sda1 /
   > <fs> vi /etc/shadow
   > <fs> exit
   > 
   > 
   
   Во втором поле привилегированного пользователя измените значение "!!" на зашифрованный пароль.
3. Создайте виртуальную машину в KVM из образа qcow2, укажите тип диска **qcow2** и задайте для модели устройства интерфейса виртуальной сети значение **virtio**. Затем запустите виртуальную машину и войдите в систему как привилегированный пользователь.
4. Создайте файл с именем **network** в каталоге `/etc/sysconfig/`. Файл должен содержать следующий текст:
   
     NETWORKING=yes
     HOSTNAME=localhost.localdomain
5. Создайте файл с именем **ifcfg-eth0** в каталоге `/etc/sysconfig/network-scripts/`. Файл должен содержать следующий текст:
   
     DEVICE=eth0
     ONBOOT=yes
     BOOTPROTO=dhcp
     TYPE=Ethernet
     USERCTL=no
     PEERDNS=yes
     IPV6INIT=no
6. Убедитесь, что сетевая служба запускается во время загрузки, выполнив следующую команду:
   
   # chkconfig network on
7. Зарегистрируйте подписку Red Hat, чтобы активировать установку пакетов из репозитория RHEL, запустив следующую команду:
   
   # subscription-manager register --auto-attach --username=XXX --password=XXX
8. Измените строку загрузки ядра в конфигурации grub, чтобы включить дополнительные параметры ядра для Azure. Для этого откройте файл `/etc/default/grub` в текстовом редакторе и измените параметр **GRUB\_CMDLINE\_LINUX**. Например:
   
     GRUB_CMDLINE_LINUX="rootdelay=300
     console=ttyS0
     earlyprintk=ttyS0"
   
   Это также гарантирует отправку всех сообщений консоли на первый последовательный порт, что может помочь технической поддержке Azure в плане отладки. Кроме того, рекомендуем удалить следующие параметры:
   
     rhgb quiet crashkernel=auto
   
   Графическая и "тихая" загрузка бесполезны в облачной среде, в которой нам нужно, чтобы все журналы отправлялись на последовательный порт. При желании можно настроить параметр crashkernel, однако учтите, что он сокращает объем доступной памяти на виртуальной машине на 128 МБ и более. Это может оказаться проблемой на виртуальных машинах небольшого размера.
9. После внесения изменений в файл `/etc/default/grub` выполните следующую команду, чтобы повторно создать конфигурацию grub:
   
   # grub2-mkconfig -o /boot/grub2/grub.cfg
10. Добавьте модули Hyper-V в initramfs:
    
    Измените файл `/etc/dracut.conf`, добавив в него следующее содержимое:
    
     add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”
    
    Повторно создайте initramfs:
    
    # dracut –f -v
11. Удаление cloud-init:
    
    # yum remove cloud-init
12. Убедитесь, что SSH-сервер установлен и настроен для включения во время загрузки:
    
    # systemctl enable sshd
    Измените файл /etc/ssh/sshd\_config, включив в него следующие строки:
    
     PasswordAuthentication yes
     ClientAliveInterval 180
    
    Перезапустите sshd:
    
     systemctl restart sshd
13. Пакет WALinuxAgent `WALinuxAgent-<version>` был отправлен в репозиторий дополнений Red Hat. Включите репозиторий дополнений, выполнив следующую команду:
    
    # subscription-manager repos --enable=rhel-7-server-extras-rpms
14. Установите агент Linux для Azure, выполнив следующую команду:
    
    # yum install WALinuxAgent
    Включите службу waagent:
    
    # systemctl enable waagent.service
15. Не создавайте пространство подкачки на диске с ОС. Агент Linux для Azure может автоматически настраивать размер области подкачки с использованием локального диска с ресурсами, подключенного к виртуальной машине после подготовки в Azure. Следует отметить, что локальный диск ресурсов является временным диском и должен быть очищен при отмене подготовки виртуальной машины. После установки агента Linux для Azure (см. предыдущий шаг) измените соответствующим образом следующие параметры в файле `/etc/waagent.conf`:
    
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
    
    Для начала конвертируйте образ в формат RAW:
    
    # qemu-img convert -f qcow2 –O raw rhel-7.1.qcow2 rhel-7.1.raw
    Убедитесь, что размер образа в формате RAW соответствует 1 МБ. Если это не так, округлите размер до 1 МБ:
    
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
               gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-7.1.raw $rounded_size
    Конвертируйте диск в формате RAW в виртуальный жесткий диск фиксированного размера:
    
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd

## Подготовка виртуальной машины под управлением Red Hat в VMware
### Предварительные требования
В этом разделе предполагается, что вы уже установили виртуальную машину RHEL в VMWare. Дополнительные сведения об установке операционной системы в VMware см. в [руководстве по установке операционной системы на виртуальной машине VMware](http://partnerweb.vmware.com/GOSIG/home.html).

* При установке операционной системы Linux мы рекомендуем использовать стандартные разделы, а не LVM (который зачастую используется по умолчанию при установке). Это позволит избежать конфликта имен LVM при клонировании виртуальных машин, особенно если диск с OC может быть подключен к другой ВМ в целях устранения неполадок. При желании на дисках с данными можно использовать LVM или RAID.
* Не настраивайте раздел подкачки на диске с ОС. Вы можете настроить агент Linux для создания файла подкачки на временном диске с ресурсами. Дополнительные сведения приведены далее.
* При создании виртуального жесткого диска выберите параметр **Store virtual disk as a single file** (Сохранять виртуальный диск как один файл).

### <a id="rhel67vmware"> </a> Подготовка виртуальной машины RHEL 6.7 в VMware
1. Удалите NetworkManager, выполнив следующую команду:
   
   # sudo rpm -e --nodeps NetworkManager
   Обратите внимание: если пакет еще не установлен, эта команда завершится сбоем с сообщением об ошибке. Это ожидаемое поведение.
2. Создайте файл с именем **network** в каталоге /etc/sysconfig/. Файл должен содержать следующий текст:
   
     NETWORKING=yes
     HOSTNAME=localhost.localdomain
3. Создайте файл с именем **ifcfg-eth0** в каталоге /etc/sysconfig/network-scripts/. Файл должен содержать следующий текст:
   
     DEVICE=eth0
     ONBOOT=yes
     BOOTPROTO=dhcp
     TYPE=Ethernet
     USERCTL=no
     PEERDNS=yes
     IPV6INIT=no
4. Переместите (или удалите) правила udev, чтобы не создавать статические правила для интерфейса Ethernet. Эти правила приводят к появлению проблем при клонировании виртуальной машины в Microsoft Azure или Hyper-V:
   
   # sudo mkdir -m 0700 /var/lib/waagent
   # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
   # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/
5. Убедитесь, что сетевая служба запускается во время загрузки, выполнив следующую команду:
   
   # sudo chkconfig network on
6. Зарегистрируйте подписку Red Hat, чтобы активировать установку пакетов из репозитория RHEL, запустив следующую команду:
   
   # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
7. Пакет WALinuxAgent `WALinuxAgent-<version>` был отправлен в репозиторий дополнений Red Hat. Включите репозиторий дополнений, выполнив следующую команду:
   
   # subscription-manager repos --enable=rhel-6-server-extras-rpms
8. Измените строку загрузки ядра в конфигурации grub, чтобы включить дополнительные параметры ядра для Azure. Для этого откройте файл /boot/grub/menu.lst в текстовом редакторе и убедитесь, что ядро по умолчанию включает следующие параметры:
   
     console=ttyS0
     earlyprintk=ttyS0
     rootdelay=300
     numa=off
   
   Это также гарантирует отправку всех сообщений консоли на первый последовательный порт, что может помочь технической поддержке Azure в плане отладки. Это приведет к отключению NUMA в связи с неисправностью версии ядра, используемого RHEL 6. Кроме того, рекомендуем удалить следующие параметры:
   
     rhgb quiet crashkernel=auto
   
   Графическая и "тихая" загрузка бесполезны в облачной среде, в которой нам нужно, чтобы все журналы отправлялись на последовательный порт. При желании можно настроить параметр crashkernel, однако учтите, что он сокращает объем доступной памяти на виртуальной машине на 128 МБ и более. Это может оказаться проблемой на виртуальных машинах небольшого размера.
9. Добавьте модули Hyper-V в initramfs:
   
     Edit `/etc/dracut.conf` and add content:
   
         add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”
   
     Rebuild initramfs:
   
         # dracut –f -v
10. Убедитесь, что SSH-сервер установлен и настроен для включения во время загрузки. Обычно это сделано по умолчанию. Измените файл `/etc/ssh/sshd_config`, добавив в него следующую строку:
    
     ClientAliveInterval 180
11. Установите агент Linux для Azure, выполнив следующую команду:
    
    # sudo yum install WALinuxAgent
    # sudo chkconfig waagent on
12. Не создавайте пространство подкачки на диске с ОС:
    
    Агент Linux для Azure может автоматически настраивать размер области подкачки с использованием локального диска с ресурсами, подключенного к виртуальной машине после подготовки в Azure. Следует отметить, что локальный диск ресурсов является временным диском и должен быть очищен при отмене подготовки виртуальной машины. После установки агента Linux для Azure (см. предыдущий шаг) измените соответствующим образом следующие параметры в файле `/etc/waagent.conf`:
    
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
    
    Для начала конвертируйте образ в формат RAW:
    
    # qemu-img convert -f vmdk –O raw rhel-6.7.vmdk rhel-6.7.raw
    Убедитесь, что размер образа в формате RAW соответствует 1 МБ. Если это не так, округлите размер до 1 МБ:
    
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-6.7.raw" | \
             gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-6.7.raw $rounded_size
    Конвертируйте диск в формате RAW в виртуальный жесткий диск фиксированного размера:
    
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd

### <a id="rhel7xvmware"> </a> Подготовка виртуальной машины RHEL 7.1 или 7.2 в VMware
1. Создайте файл с именем **network** в каталоге /etc/sysconfig/. Файл должен содержать следующий текст:
   
     NETWORKING=yes
     HOSTNAME=localhost.localdomain
2. Создайте файл с именем **ifcfg-eth0** в каталоге /etc/sysconfig/network-scripts/. Файл должен содержать следующий текст:
   
     DEVICE=eth0
     ONBOOT=yes
     BOOTPROTO=dhcp
     TYPE=Ethernet
     USERCTL=no
     PEERDNS=yes
     IPV6INIT=no
3. Убедитесь, что сетевая служба запускается во время загрузки, выполнив следующую команду:
   
   # sudo chkconfig network on
4. Зарегистрируйте подписку Red Hat, чтобы активировать установку пакетов из репозитория RHEL, запустив следующую команду:
   
   # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
5. Измените строку загрузки ядра в конфигурации grub, чтобы включить дополнительные параметры ядра для Azure. Для этого откройте файл `/etc/default/grub` в текстовом редакторе и измените параметр **GRUB\_CMDLINE\_LINUX**. Например:
   
     GRUB_CMDLINE_LINUX="rootdelay=300
     console=ttyS0
     earlyprintk=ttyS0"
   
   Это также гарантирует отправку всех сообщений консоли на первый последовательный порт, что может помочь технической поддержке Azure в плане отладки. Кроме того, рекомендуем удалить следующие параметры:
   
     rhgb quiet crashkernel=auto
   
   Графическая и "тихая" загрузка бесполезны в облачной среде, в которой нам нужно, чтобы все журналы отправлялись на последовательный порт. При желании можно настроить параметр crashkernel, однако учтите, что он сокращает объем доступной памяти на виртуальной машине на 128 МБ и более. Это может оказаться проблемой на виртуальных машинах небольшого размера.
6. После внесения изменений в файл `/etc/default/grub` выполните следующую команду, чтобы повторно создать конфигурацию grub:
   
   # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
7. Добавьте модули Hyper-V в initramfs:
   
   Измените файл `/etc/dracut.conf`, добавив в него следующее содержимое:
   
     add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”
   
   Повторно создайте initramfs:
   
   # dracut –f -v
8. Убедитесь, что SSH-сервер установлен и настроен для включения во время загрузки. Обычно это сделано по умолчанию. Измените файл `/etc/ssh/sshd_config`, добавив в него следующую строку:
   
     ClientAliveInterval 180
9. Пакет WALinuxAgent `WALinuxAgent-<version>` был отправлен в репозиторий дополнений Red Hat. Включите репозиторий дополнений, выполнив следующую команду:
   
   # subscription-manager repos --enable=rhel-7-server-extras-rpms
10. Установите агент Linux для Azure, выполнив следующую команду:
    
    # sudo yum install WALinuxAgent
    # sudo systemctl enable waagent.service
11. Не создавайте пространство подкачки на диске с ОС. Агент Linux для Azure может автоматически настраивать размер области подкачки с использованием локального диска с ресурсами, подключенного к виртуальной машине после подготовки в Azure. Следует отметить, что локальный диск ресурсов является временным диском и должен быть очищен при отмене подготовки виртуальной машины. После установки агента Linux для Azure (см. предыдущий шаг) измените соответствующим образом следующие параметры в файле `/etc/waagent.conf`:
    
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
    
    Для начала конвертируйте образ в формат RAW:
    
    # qemu-img convert -f vmdk –O raw rhel-7.1.vmdk rhel-7.1.raw
    Убедитесь, что размер образа в формате RAW соответствует 1 МБ. Если это не так, округлите размер до 1 МБ:
    
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
              gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-7.1.raw $rounded_size
    Конвертируйте диск в формате RAW в виртуальный жесткий диск фиксированного размера:
    
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd

## Подготовка виртуальной машины под управлением Red Hat из ISO-образа с помощью автоматического использования файла kickstart
### <a id="rhel7xkickstart"> </a> Подготовка виртуальной машины RHEL 7.1 или 7.2 в файле kickstart
1. Создайте файл kickstart, содержащий приведенное ниже содержимое, и сохраните файл. Дополнительные сведения об установке kickstart см. в [руководстве по установке kickstart](https://access.redhat.com/documentation/ru-RU/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

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
        sed -i 's/^(ResourceDisk\.EnableSwap)=[Nn]$/\1=y/g' /etc/waagent.conf
        sed -i 's/^(ResourceDisk\.SwapSizeMB)=[0-9]*$/\1=2048/g' /etc/waagent.conf

        # Set the cmdline
        sed -i 's/^(GRUB_CMDLINE_LINUX)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

        # Enable SSH keepalive
        sed -i 's/^#(ClientAliveInterval).*$/\1 180/g' /etc/ssh/sshd_config

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
        NM_CONTROLLED=yes
        EOF

        # Deprovision and prepare for Azure
        waagent -force -deprovision

        %end

1. Поместите файл kickstart в место, доступное для системы установки.
2. В диспетчере Hyper-V создайте новую виртуальную машину. На странице **Подключить виртуальный жесткий диск** выберите параметр **Подключить виртуальный жесткий диск позднее** и выполните инструкции в мастере создания виртуальной машины.
3. Откройте параметры виртуальной машины:
   
   а. Загрузите новый виртуальный жесткий диск на виртуальную машину. Выберите параметры **Формат VHD** и **Фиксированный размер**.
   
   b. Подключите установочный ISO-образ к DVD-дисководу.
   
   c. В BIOS выберите загрузку с компакт-диска.
4. Запустите виртуальную машину. Когда отобразится руководство по установке, нажмите клавишу **TAB**, чтобы настроить параметры загрузки.
5. Введите `inst.ks=<the location of the kickstart file>` в конце параметров загрузки и нажмите клавишу **ВВОД**.
6. Дождитесь завершения процесса установки. По окончании процесса виртуальная машина завершит работу автоматически. Виртуальный жесткий диск Linux готов к передаче в Azure.

## Известные проблемы
Существуют две известные проблемы, которые могут возникнуть при использовании RHEL 7.1 в Hyper-V и Azure.

### Зависание дискового ввода-вывода
Эта проблема может возникать при частых операциях ввода-вывода, связанных с диском RHEL 7.1 в Hyper-V и Azure.

Частота появления:

проблема возникает периодически. Однако она встречается чаще при большом количестве операций дискового ввода-вывода в Hyper-V и Azure.

[AZURE.NOTE] Эту проблему Red Hat уже устранила. Чтобы установить соответствующие исправления, выполните следующую команду:

    # sudo yum update

### Не удалось включить драйвер Hyper-V в начальный электронный диск при использовании низкоуровневой оболочки Hyper V
В некоторых случаях установщики Linux могут не включать драйверы для Hyper-V в начальный электронный диск (initrd или initramfs), если только не обнаружат, что он работает в среде Hyper-V.

Если для подготовки образа Linux используется другая система виртуализации (например, Virtualbox, Xen и пр.), вам может потребоваться выполнить повторную сборку initrd, чтобы убедиться, что на начальном электронном диске доступны по крайней мере модули ядра hv\_vmbus и hv\_storvsc. Это известная проблема по крайней мере в системах на основе предшествующего дистрибутива Red Hat.

Чтобы устранить эту проблему, необходимо добавить модули Hyper-V в initramfs и выполнить повторную сборку:

Измените файл `/etc/dracut.conf`, добавив в него следующее содержимое:

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

Повторно создайте initramfs:

        # dracut –f -v

Дополнительные сведения см. в описании [повторного создания initramfs](https://access.redhat.com/solutions/1958).

## Дальнейшие действия
Теперь виртуальный жесткий диск Red Hat Enterprise Linux можно использовать для создания новых виртуальных машин Azure. Если вы впервые отправляете VHD-файл в Azure, см. шаги 2 и 3 в статье [Создание и передача виртуального жесткого диска с операционной системой Linux](virtual-machines-linux-classic-create-upload-vhd.md).

Чтобы получить дополнительные сведения о низкоуровневых оболочках, сертифицированных для запуска Red Hat Enterprise Linux, посетите [веб-сайт Red Hat](https://access.redhat.com/certified-hypervisors).

<!---HONumber=AcomDC_0518_2016-->