---
title: Создание и передача виртуального жесткого диска с операционной системой Linux в Azure
description: Узнайте, как создать и передать виртуальный жесткий диск (VHD-файл) Azure, содержащий операционную систему Linux.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: d351396c-95a0-4092-b7bf-c6aae0bbd112
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: 17b4df83b141d5365a8d6244c4ab73b0eba5ed73
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33944592"
---
# <a name="information-for-non-endorsed-distributions"></a>Информация о нерекомендованных дистрибутивах
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Соглашение об уровне обслуживания для платформы Azure применяется для виртуальных машин с ОС Linux, только если используется один из [рекомендованных дистрибутивов](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Для рекомендованных дистрибутивов предоставляются образы Linux, представленные в коллекции в Azure Marketplace с необходимой конфигурацией.

* [Linux в Azure — рекомендованные дистрибутивы](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Поддержка образов Linux в Microsoft Azure](https://support.microsoft.com/kb/2941892)

Все дистрибутивы, работающие в Azure, должны соответствовать ряду предварительных требований для правильной работы на платформе.  Эта статья далеко не исчерпывающая, так как каждый дистрибутив имеет свои отличия; возможно, что даже при соблюдении всех изложенных ниже критериев необходимо будет в значительной мере настроить систему Linux для ее надлежащей работы на платформе.

Именно по этой причине мы рекомендуем по возможности начать работу с [рекомендованного дистрибутива Linux в Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). В следующей статье описывается подготовка различных рекомендованных дистрибутивов Linux, которые поддерживаются в Azure:

* **[Дистрибутивы на основе CentOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES и OpenSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Далее в этой статье приводятся общие рекомендации по работе с дистрибутивом Linux в Azure.

## <a name="general-linux-installation-notes"></a>Общие замечания по установке Linux
* Формат VHDX не поддерживается в Azure, поддерживается только **постоянный VHD**.  Можно преобразовать диск в формат VHD с помощью диспетчера Hyper-V или командлета convert-vhd. Если вы используете VirtualBox, при создании диска по умолчанию нужно выбрать **фиксированный размер** вместо динамически выделяемого.
* Azure поддерживает только виртуальные машины поколения 1. Виртуальную машину первого поколения можно преобразовать из формата VHDX в формат VHD, а также переключить с динамически расширяемого диска на диск фиксированного размера. Но вы не можете изменить поколение виртуальной машины. Дополнительные сведения см. в статье о том, [как выбрать поколение для виртуальной машины в Hyper-V](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
* Максимально допустимый размер виртуального жесткого диска составляет 1023 ГБ.
* При установке системы Linux *рекомендуется* использовать стандартные разделы, а не LVM (как правило, значение по умолчанию во многих дистрибутивах). Это позволит избежать конфликта имен LVM c клонированными виртуальными машинами, особенно если диск с OC может быть подключен к другой идентичной виртуальной машине в целях устранения неполадок. Для дисков данных можно использовать [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) или [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Требуется поддержка ядра для монтирования файловых систем UDF. При первой загрузке в Azure конфигурация подготовки передается в виртуальную машину Linux через UDF-носитель, подключенный к гостевой машине. Агент Azure Linux должен иметь возможность подключать файловую систему UDF для считывания конфигурации и подготовки виртуальной машины.
* Версии ядра Linux ниже 2.6.37 не поддерживают NUMA в Hyper-V с виртуальными машинами большего размера. Эта проблема влияет в основном на дистрибутивы более ранних версий, в которых используется исходное ядро Red Hat 2.6.32, и была исправлена в RHEL 6.6 (kernel-2.6.32-504). В системах под управлением модифицированных ядер старше версии 2.6.37 или ядер RHEL старше 2.6.32-504 в командной строке ядра необходимо задать параметр загрузки `numa=off` в файле grub.conf. Дополнительные сведения см. в статье базы знаний Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* Не настраивайте раздел подкачки на диске с ОС. Можно настроить агент Linux для создания файла подкачки на временном диске ресурсов.  Дополнительные сведения описаны далее.
* Размер виртуальной памяти всех VHD в Azure должен быть округлен до 1 МБ. При конвертации диска в формате RAW в виртуальный жесткий диск убедитесь, что размер диска RAW в несколько раз превышает 1 МБ. Дополнительные сведения можно найти в инструкциях ниже.

### <a name="installing-kernel-modules-without-hyper-v"></a>Установка модулей ядра без Hyper-V
Так как Azure запускается на гипервизоре Hyper-V, Linux требует установки определенных модулей ядра для запуска в Azure. При наличии виртуальной машины, созданной вне Hyper-V, установщики Linux могут не включать драйверы для Hyper-V в начальный электронный диск (initrd или initramfs), если только не обнаружат, что он работает в среде Hyper-V. Если для подготовки образа Linux используется другая система виртуализации (например, Virtualbox или KVM), вам может потребоваться выполнить повторную сборку initrd, чтобы убедиться в том, что на начальном электронном диске доступны по крайней мере модули ядра `hv_vmbus` и `hv_storvsc`.  Это известная проблема по крайней мере в системах на основе предшествующего дистрибутива Red Hat.

Механизм повторной сборки образа initrd или initramfs может отличаться в зависимости от дистрибутива. Чтобы узнать правильную процедуру для вашего дистрибутива, см. документацию по дистрибутиву или раздел поддержки.  Ниже приведен пример выполнения повторной сборки initrd с помощью служебной программы `mkinitrd`.

Сначала создайте резервную копию существующего образа initrd:

    # cd /boot
    # sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak

Затем выполните повторную сборку initrd с помощью модулей ядра `hv_vmbus` и `hv_storvsc`:

    # sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`


### <a name="resizing-vhds"></a>Изменение размера VHD
Размер виртуальной памяти образов VHD в Azure должен быть округлен до 1 МБ.  Как правило, размер VHD, созданных с помощью Hyper-V, настроен правильно.  Если виртуальный жесткий диск (VHD) настроен неправильно, при попытке создать *образ* из VHD-файла может появиться следующее сообщение об ошибке:

    "The VHD http://<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd has an unsupported virtual size of 21475270656 bytes. The size must be a whole number (in MBs).”

Чтобы исправить эту ошибку, измените размер виртуальной машины с помощью консоли диспетчера Hyper-V или командлета PowerShell [Resize-VHD](http://technet.microsoft.com/library/hh848535.aspx).  Если вы работаете не в среде Windows, воспользуйтесь командой qemu-img для преобразования (если необходимо) и изменения размера VHD.

> [!NOTE]
> В команде qemu-img версии 2.2.1 и более поздних версиях есть ошибка, которая приводит к созданию неверного формата VHD. Эта проблема устранена в QEMU версии 2.6. Рекомендуется использовать qemu-img версии 2.2.0 или более ранних версий. Либо выполните обновление до версии 2.6 или выше. Справочные материалы: https://bugs.launchpad.net/qemu/+bug/1490611.
> 
> 

1. Изменение размера VHD с непосредственным использованием таких инструментов, как `qemu-img` или `vbox-manage`, может привести к сбою загрузки VHD.  Поэтому мы советуем сначала преобразовать VHD в образ необработанного диска.  Если образ виртуальной машины уже был создан в качестве образа необработанного диска (по умолчанию для некоторых гипервизоров, например, KVM), вы можете пропустить этот шаг:
   
       # qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw

2. Рассчитайте необходимый размер образа диска, чтобы округлить размер виртуальной памяти до 1 МБ.  Это можно сделать с помощью следующего сценария оболочки Bash.  Скрипт использует `qemu-img info` для определения виртуального размера образа диска и вычисляет размер с округлением до 1 МБ в большую сторону:
   
       rawdisk="MyLinuxVM.raw"
       vhddisk="MyLinuxVM.vhd"
   
       MB=$((1024*1024))
       size=$(qemu-img info -f raw --output json "$rawdisk" | \
              gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
   
       rounded_size=$((($size/$MB + 1)*$MB))
       echo "Rounded Size = $rounded_size"

3. Измените размер необработанного диска с помощью $rounded_size, как показано в сценарии выше.
   
       # qemu-img resize MyLinuxVM.raw $rounded_size

4. А теперь преобразуйте необработанный диск обратно в VHD с фиксированным размером памяти.
   
       # qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd

   Или с помощью qemu версии **2.6 или выше** добавьте параметр `force_size`:

       # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd

## <a name="linux-kernel-requirements"></a>Рекомендации по ядру Linux
Драйверы Linux Integration Services (LIS) для Hyper-V и Azure встраиваются непосредственно в основное ядро Linux. Во многих дистрибутивах, которые включают последнюю версию ядра Linux (то есть 3.x), эти драйверы уже доступны, или же в ядре предоставляются их более ранние версии.  Эти ядра в основном ядре постоянно обновляются с помощью новых исправлений и функций, поэтому по возможности рекомендуется использовать [рекомендованный дистрибутив](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), включающий все исправления и обновления.

Если вы работаете с одним из вариантов Red Hat Enterprise Linux версий **6.0–6.3**, вам необходимо установить последние драйверы LIS для Hyper-V. Они доступны [по этой ссылке](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). Начиная с RHEL **6.4+** (и его производных вариантов), драйверы LIS уже включены в ядро. Поэтому дополнительные пакеты установки для работы этих системы в Azure не требуются.

Если необходимо собственное ядро, рекомендуется использовать одну из последних версий (то есть **3.8 или более позднюю**). Для дистрибутивов или поставщиков, предоставляющих собственное ядро, рекомендуется регулярно переносить драйверы LIS из основного ядра в собственное.  Даже если вы работаете с относительно свежей версией ядра, рекомендуется отслеживать исправления драйверов LIS в основном ядре и по мере необходимости обновлять драйверы. Расположение файлов исходного кода драйверов LIS указано в файле [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) в дереве исходного кода ядра Linux:

    F:    arch/x86/include/asm/mshyperv.h
    F:    arch/x86/include/uapi/asm/hyperv.h
    F:    arch/x86/kernel/cpu/mshyperv.c
    F:    drivers/hid/hid-hyperv.c
    F:    drivers/hv/
    F:    drivers/input/serio/hyperv-keyboard.c
    F:    drivers/net/hyperv/
    F:    drivers/scsi/storvsc_drv.c
    F:    drivers/video/fbdev/hyperv_fb.c
    F:    include/linux/hyperv.h
    F:    tools/hv/

Известно, что отсутствие перечисленных ниже исправлений приводит к возникновению проблем в Azure, поэтому их необходимо включить в ядро. Этот список ни в коем случае не является исчерпывающим и полным для всех дистрибутивов:

* [ata_piix: предоставление дисков драйверам Hyper-V по умолчанию;](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc: принятие во внимание передаваемых пакетов в папке RESET;](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc: предотвращение использования WRITE_SAME;](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc: отключение WRITE SAME для драйверов RAID и адаптеров виртуальных узлов;](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc: исправление разыменования пустого указателя;](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc: ошибки кольцевого буфера могут привести к заморозке операций ввода-вывода.](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs: защита от двойного выполнения __scsi_remove_device.](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>Агент Linux для Azure
Для правильной подготовки виртуальной машины Linux в Azure необходим [агент Linux для Azure](../extensions/agent-linux.md) (waagent). Можно получить его последнюю версию, узнать о проблемах с файлами или отправить запрос можно в [репозитории GitHub для агента Linux](https://github.com/Azure/WALinuxAgent).

* Агент Linux выпускается по лицензии Apache 2.0. Многие дистрибутивы уже предоставляют пакеты RPM или deb для этого агента, поэтому в некоторых случаях его можно установить и обновить с минимальными усилиями.
* Для работы агента Linux для Azure требуется Python v2.6+.
* Для агента также необходим модуль python-pyasn1. В большинстве дистрибутивов он предоставляется в виде отдельно устанавливаемого пакета.
* В некоторых случаях агент Linux для Azure может быть несовместим с NetworkManager. Многие пакеты RPM/Deb, предоставленные вместе с дистрибутивами, настраивают NetworkManager с использованием параметров, конфликтующих с пакетом waagent, поэтому это приведет к удалению NetworkManager при установке пакета агента Linux.
* Версия агента Linux для Azure должна быть выше минимальной поддерживаемой версии. Дополнительные сведения см. в этой [статье](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

## <a name="general-linux-system-requirements"></a>Общие системные требования для Linux

* Измените строку загрузки ядра в GRUB или GRUB2 для включения следующих параметров: Эта команда также гарантирует отправку всех сообщений консоли на первый последовательный порт, что может помочь службе поддержки Azure при отладке.
  
        console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
  
    Эта команда также гарантирует отправку всех сообщений консоли на первый последовательный порт, что может помочь службе поддержки Azure при отладке.
  
    Помимо вышесказанного, рекомендуется *удалить* следующие параметры, если они существуют:
  
        rhgb quiet crashkernel=auto
  
    Графическая и "тихая" загрузка бесполезны в облачной среде, в которой требуется, чтобы все журналы отправлялись на последовательный порт. При желании можно настроить параметр `crashkernel`, однако учитывайте, что он сокращает объем доступной памяти в виртуальной машине на 128 МБ или более, что может оказаться проблемой в виртуальных машинах небольшого размера.

* Установка агента Linux для Azure
  
    Агент Linux для Azure необходим для подготовки образа Linux в Azure.  Во многих дистрибутивах агент предоставляется в виде пакета RPM или Deb (пакет обычно называется "WALinuxAgent" или "walinuxagent").  Агент также можно установить вручную, как описано в [руководстве по агенту Linux](../extensions/agent-linux.md).

* Убедитесь, что SSH-сервер установлен и настроен для включения во время загрузки.  Обычно это сделано по умолчанию.

* Не создавайте пространство подкачки на диске с ОС
  
    Агент Linux для Azure может автоматически настраивать пространство подкачки с использованием диска на локальном ресурсе, подключенном к виртуальной машине после подготовки для работы в среде Azure. Локальный диск ресурсов является *временным* и может быть очищен при отмене подготовки виртуальной машины. После установки агента Linux для Azure (см. предыдущий шаг) измените следующие параметры в /etc/waagent.conf соответствующим образом:
  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

* На последнем этапе выполните следующие команды, чтобы отозвать виртуальную машину:
  
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
  
  > [!NOTE]
  > В Virtualbox после выполнения команды waagent -force -deprovision может появиться следующая ошибка: `[Errno 5] Input/output error`. Это сообщение об ошибке не является важным и может быть пропущено.
  > 
  > 

* Завершите работу виртуальной машины и передайте виртуальный жесткий диск в Azure.

