---
title: Создание и передача виртуального жесткого диска с операционной системой SUSE Linux в Azure
description: Узнайте, как создать и передать виртуальный жесткий диск (VHD-файл) Azure, содержащий операционную систему SUSE Linux.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 066d01a6-2a54-4718-bcd0-90fe7a5303a1
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: 99838a7038672998d4940bfb437bd31311d3600f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34653439"
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Подготовка виртуальной машины SLES или openSUSE для Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>предварительным требованиям
В этой статье предполагается, что вы уже установили операционную систему SUSE или openSUSE Linux на виртуальный жесткий диск. Существует несколько средств для создания VHD-файлов, например решение для виртуализации, такое как Hyper-V. Инструкции см. в разделе [Установка роли Hyper-V и настройка виртуальной машины](http://technet.microsoft.com/library/hh846766.aspx).

### <a name="sles--opensuse-installation-notes"></a>Замечания по установке SLES и openSUSE
* Дополнительные сведения о подготовке Linux для Azure см. в разделе [Общие замечания по установке Linux](create-upload-generic.md#general-linux-installation-notes).
* Формат VHDX не поддерживается в Azure, поддерживается только **фиксированный VHD**.  Можно преобразовать диск в формат VHD с помощью диспетчера Hyper-V или командлета convert-vhd.
* При установке системы Linux рекомендуется использовать стандартные разделы, а не LVM (как правило, значение по умолчанию во многих дистрибутивах). Это позволит избежать конфликта имен LVM при клонировании виртуальных машин, особенно если диск с OC может быть подключен к другой ВМ в целях устранения неполадок. Для дисков данных можно использовать [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) или [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Не настраивайте раздел подкачки на диске с ОС. Можно настроить агент Linux для создания файла подкачки на временном диске ресурсов.  Дополнительные сведения описаны далее.
* Размер виртуальной памяти всех VHD в Azure должен быть округлен до 1 МБ. При конвертации диска в формате RAW в виртуальный жесткий диск убедитесь, что размер диска RAW в несколько раз превышает 1 МБ. См. дополнительные сведения в [примечаниях по установке Linux](create-upload-generic.md#general-linux-installation-notes).

## <a name="use-suse-studio"></a>Использование SUSE Studio
[SUSE Studio](http://www.susestudio.com) можно с легкостью использовать для создания образов SLES и openSUSE для Azure и Hyper-V, а также для управления этими образами. Этот подход рекомендуется для настройки собственных образов SLES и openSUSE.

Вместо того чтобы создать собственный виртуальный жесткий диск, SUSE публикует образы BYOS (использование собственной подписки) для SLES в каталоге [VM Depot](https://vmdepot.msopentech.com/User/Show?user=1007).

## <a name="prepare-suse-linux-enterprise-server-11-sp4"></a>Подготовка SUSE Linux Enterprise Server 11 с пакетом обновления 4 (SP4)
1. На центральной панели диспетчера Hyper-V выберите виртуальную машину.
2. Щелкните **Подключение** , чтобы открыть окно виртуальной машины.
3. Зарегистрируйте систему SUSE Linux Enterprise, чтобы позволить ей скачивать обновления и устанавливать пакеты.
4. Установите в системе последние исправления:
   
        # sudo zypper update
5. Установите агент Linux для Azure из репозитория SLES:
   
        # sudo zypper install WALinuxAgent
6. Убедитесь, что waagent в chkconfig имеет значение on. Если указано другое значение, включите для службы waagent автоматический запуск.
   
        # sudo chkconfig waagent on
7. Убедитесь, что служба waagent работает. Если она не работает, запустите ее: 
   
        # sudo service waagent start
8. Измените строку загрузки ядра в конфигурации grub, чтобы включить дополнительные параметры ядра для Azure. Для этого откройте файл "/boot/grub/menu.lst" в текстовом редакторе и убедитесь, что ядро по умолчанию включает следующие параметры:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
    Это гарантирует отправку всех сообщений консоли на первый последовательный порт, что может помочь технической поддержке Azure в плане отладки.
9. Убедитесь, что файлы /boot/grub/menu.lst и /etc/fstab ссылаются на диск с помощью его UUID (by-uuid), а не с помощью идентификатора диска (by-id). 
   
    Получение UUID диска
   
        # ls /dev/disk/by-uuid/
   
    Если используется /dev/disk/by-id, обновите файлы /boot/grub/menu.lst и /etc/fstab правильным значением uuid.
   
    Перед изменением
   
        root=/dev/disk/by-id/SCSI-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx-part1
   
    После изменения
   
        root=/dev/disk/by-uuid/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
10. Переместите или удалите правила udev, чтобы не создавать статические правила для интерфейса Ethernet. Эти правила приводят к появлению проблем при клонировании виртуальной машины в Microsoft Azure или Hyper-V:
    
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
11. Рекомендуется отредактировать файл /etc/sysconfig/network/dhcp и изменить параметр `DHCLIENT_SET_HOSTNAME` следующим образом:
    
     DHCLIENT_SET_HOSTNAME="no"
12. В "/etc/sudoers" закомментируйте или удалите следующие строки, если они существуют:
    
     Defaults targetpw   # ask for the password of the target user i.e. root ALL    ALL=(ALL) ALL   # WARNING! Используйте только вместе с Defaults targetpw!
13. Убедитесь, что SSH-сервер установлен и настроен для включения во время загрузки.  Обычно это сделано по умолчанию.
14. Не создавайте пространство подкачки на диске с ОС.
    
    Агент Linux для Azure может автоматически настраивать пространство подкачки с использованием диска на локальном ресурсе, подключенном к виртуальной машине после подготовки для работы в среде Azure. Следует отметить, что локальный диск ресурсов является *временным* диском и должен быть очищен при отмене подготовки виртуальной машины. После установки агента Linux для Azure (см. предыдущий шаг) измените следующие параметры в /etc/waagent.conf соответствующим образом:
    
     ResourceDisk.Format=y  ResourceDisk.Filesystem=ext4  ResourceDisk.MountPoint=/mnt/resource  ResourceDisk.EnableSwap=y  ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
15. Выполните следующие команды, чтобы отменить подготовку виртуальной машины и подготовить ее в Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
16. В диспетчере Hyper-V выберите **Действие -> Завершение работы**. Виртуальный жесткий диск Linux готов к передаче в Azure.

- - -
## <a name="prepare-opensuse-131"></a>Подготовка openSUSE 13.1+
1. На центральной панели диспетчера Hyper-V выберите виртуальную машину.
2. Щелкните **Подключение** , чтобы открыть окно виртуальной машины.
3. В консоли оболочки выполните команду`zypper lr`. Если эта команда возвращает результат следующего вида, то репозитории настроены надлежащим образом, и никаких изменений не требуется (обратите внимание, что номера версий могут отличаться):
   
        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes
   
    Если команда возвращает сообщение "No repositories defined..." (Репозитории не определены...), используйте следующие команды для добавления этих репозиториев:
   
        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
        # sudo zypper ar -f http://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates
   
    Чтобы убедиться, что репозитории добавлены, можно еще раз выполнить команду`zypper lr`. Если один из соответствующих репозиториев обновлений не включен, включите его, выполнив следующую команду:
   
        # sudo zypper mr -e [NUMBER OF REPOSITORY]
4. Обновите ядро до последней доступной версии:
   
        # sudo zypper up kernel-default
   
    Либо установите в системе все последние исправления:
   
        # sudo zypper update
5. Установите агент Linux для Azure.
   
        # sudo zypper install WALinuxAgent
6. Измените строку загрузки ядра в конфигурации grub, чтобы включить дополнительные параметры ядра для Azure. Для этого откройте файл /boot/grub/menu.lst в текстовом редакторе и убедитесь, что ядро по умолчанию включает следующие параметры:
   
     console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
   Это гарантирует отправку всех сообщений консоли на первый последовательный порт, что может помочь технической поддержке Azure в плане отладки. Кроме того, удалите следующие параметры из строки загрузки ядра, если таковые существуют:
   
     libata.atapi_enabled=0 reserve=0x1f0,0x8
7. Рекомендуется отредактировать файл /etc/sysconfig/network/dhcp и изменить параметр `DHCLIENT_SET_HOSTNAME` следующим образом:
   
     DHCLIENT_SET_HOSTNAME="no"
8. **Внимание!** В файле /etc/sudoers закомментируйте или удалите следующие строки, если они существуют:
   
     Defaults targetpw   # ask for the password of the target user i.e. root ALL    ALL=(ALL) ALL   # WARNING! Используйте только вместе с Defaults targetpw!
9. Убедитесь, что SSH-сервер установлен и настроен для включения во время загрузки.  Обычно это сделано по умолчанию.
10. Не создавайте пространство подкачки на диске с ОС.
    
    Агент Linux для Azure может автоматически настраивать пространство подкачки с использованием диска на локальном ресурсе, подключенном к виртуальной машине после подготовки для работы в среде Azure. Следует отметить, что локальный диск ресурсов является *временным* диском и должен быть очищен при отмене подготовки виртуальной машины. После установки агента Linux для Azure (см. предыдущий шаг) измените следующие параметры в /etc/waagent.conf соответствующим образом:
    
     ResourceDisk.Format=y  ResourceDisk.Filesystem=ext4  ResourceDisk.MountPoint=/mnt/resource  ResourceDisk.EnableSwap=y  ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
11. Выполните следующие команды, чтобы отменить подготовку виртуальной машины и подготовить ее в Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
12. Убедитесь, что агент Linux для Azure запускается при загрузке:
    
        # sudo systemctl enable waagent.service
13. В диспетчере Hyper-V выберите **Действие -> Завершение работы**. Виртуальный жесткий диск Linux готов к передаче в Azure.

## <a name="next-steps"></a>Дополнительная информация
Теперь виртуальный жесткий диск SUSE Linux можно использовать для создания новых виртуальных машин Azure. Если вы отправляете VHD-файл в Azure впервые, см. раздел [Вариант 1. Передача VHD](upload-vhd.md#option-1-upload-a-vhd).
