---
title: "Создание и передача виртуального жесткого диска с ОС Ubuntu Linux в Azure"
description: "Узнайте, как создать и передать виртуальный жесткий диск (VHD-файл) Azure, содержащий операционную систему Ubuntu Linux."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 3e097959-84fc-4f6a-8cc8-35e087fd1542
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
translationtype: Human Translation
ms.sourcegitcommit: 7e77858b36d07049333422d4454c29a9e1acb748
ms.openlocfilehash: bdec7eb0b32cd8853dc01791466abf48c61a5fe8


---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Подготовка виртуальной машины Ubuntu для Azure
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="official-ubuntu-cloud-images"></a>Официальные образы облаков Ubuntu
Теперь Ubuntu публикует официальные виртуальные жесткие диски Azure, загрузить которые можно по адресу: [http://cloud-images.ubuntu.com/](http://cloud-images.ubuntu.com/). Если вам нужно создать свой собственный, особый образ Ubuntu для Azure, не выполняйте описанную ниже процедуру, а начните с таких заведомо рабочих виртуальных жестких дисков и настройте их так, как вам требуется. Последние выпуски образов можно всегда найти в следующих расположениях:

* Ubuntu 12.04/Precise: [ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/precise/release/ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 14.04/Trusty: [ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 16.04/Xenial: [ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)

## <a name="prerequisites"></a>Предварительные требования
В этой статье предполагается, что вы уже установили операционную систему Ubuntu Linux на виртуальный жесткий диск. Существует несколько средств для создания VHD-файлов, например решение для виртуализации, такое как Hyper-V. Инструкции см. в разделе [Установка роли Hyper-V и настройка виртуальной машины](http://technet.microsoft.com/library/hh846766.aspx).

**Замечания по установке Ubuntu**

* Дополнительные сведения о подготовке Linux для Azure см. в разделе [Общие замечания по установке Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes).
* Формат VHDX не поддерживается в Azure, поддерживается только **фиксированный VHD**.  Можно преобразовать диск в формат VHD с помощью диспетчера Hyper-V или командлета convert-vhd.
* При установке системы Linux рекомендуется использовать стандартные разделы, а не LVM (как правило, значение по умолчанию во многих дистрибутивах). Это позволит избежать конфликта имен LVM при клонировании виртуальных машин, особенно если диск с OC может быть подключен к другой ВМ в целях устранения неполадок. Для дисков данных можно использовать [LVM](virtual-machines-linux-configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) или [RAID](virtual-machines-linux-configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Не настраивайте раздел подкачки на диске с ОС. Можно настроить агент Linux для создания файла подкачки на временном диске ресурсов.  Дополнительные сведения описаны далее.
* Все VHD-диски должны иметь размер, кратный 1 МБ.

## <a name="manual-steps"></a>Создание вручную
> [!NOTE]
> Прежде чем создавать собственный образ Ubuntu для Azure, рассмотрите возможность применения предварительно созданных и проверенных образов с сайта [http://cloud-images.ubuntu.com/](http://cloud-images.ubuntu.com/) .
> 
> 

1. На центральной панели диспетчера Hyper-V выберите виртуальную машину.

2. Щелкните **Подключение** , чтобы открыть окно виртуальной машины.

3. Замените текущие репозитории в образе на репозитории Ubuntu Azure. Эти действия могут незначительно отличаться в зависимости от версии Ubuntu.
   
    Перед редактированием `/etc/apt/sources.list` рекомендуется сделать резервную копию:
   
        # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

    Ubuntu 12,04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 14.04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 16.04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

4. Теперь образы Azure Ubuntu используют ядро с *расширенной поддержкой оборудования* (HWE). Обновите операционную систему до последней версии ядра, выполнив следующие команды.

    Ubuntu 12,04:
   
        # sudo apt-get update
        # sudo apt-get install linux-image-generic-lts-trusty linux-cloud-tools-generic-lts-trusty
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
   
        # sudo reboot
   
    Ubuntu 14.04:
   
        # sudo apt-get update
        # sudo apt-get install linux-image-virtual-lts-vivid linux-lts-vivid-tools-common
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
   
        # sudo reboot

    Ubuntu 16.04:
   
        # sudo apt-get update
        # sudo apt-get install linux-generic-hwe-16.04 linux-cloud-tools-generic-hwe-16.04
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot

    **См. также:**
    - [https://wiki.ubuntu.com/Kernel/LTSEnablementStack](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)
    - [https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack](https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack)


5. Измените строку загрузки ядра в конфигурации Grub, чтобы включить дополнительные параметры ядра для Azure. Для этого откройте файл `/etc/default/grub` в текстовом редакторе, найдите переменную `GRUB_CMDLINE_LINUX_DEFAULT` (или добавьте ее, если это необходимо) и измените ее, включив следующие параметры:
   
        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

    Сохраните и закройте файл, а затем выполните команду `sudo update-grub`. Это гарантирует отправку всех сообщений консоли на первый последовательный порт, что может помочь технической поддержке Azure в плане отладки.

6. Убедитесь, что SSH-сервер установлен и настроен для включения во время загрузки.  Обычно это сделано по умолчанию.

7. Установите агент Linux для Azure:
   
        # sudo apt-get update
        # sudo apt-get install walinuxagent

    >[!Note]
    Установка пакета `walinuxagent` приведет к удалению пакетов `NetworkManager` и `NetworkManager-gnome` (если они установлены).

8. Выполните следующие команды, чтобы отменить подготовку виртуальной машины и подготовить ее в Azure:
   
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

9. В диспетчере Hyper-V выберите **Действие -> Завершение работы**. Виртуальный жесткий диск Linux готов к передаче в Azure.

## <a name="next-steps"></a>Дальнейшие действия
Теперь виртуальный жесткий диск Ubuntu Linux можно использовать для создания новых виртуальных машин Azure. Если вы загружаете VHD-файл в Azure впервые, обратитесь к шагам 2 и 3 в статье [Создание и загрузка виртуального жесткого диска, содержащего операционную систему Linux](virtual-machines-linux-classic-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="references"></a>Ссылки
Ядро Ubuntu с расширенной поддержкой оборудования (HWE):

* [http://blog.utlemming.org/2015/01/ubuntu-1404-azure-images-now-tracking.html](http://blog.utlemming.org/2015/01/ubuntu-1404-azure-images-now-tracking.html)
* [http://blog.utlemming.org/2015/02/1204-azure-cloud-images-now-using-hwe.html](http://blog.utlemming.org/2015/02/1204-azure-cloud-images-now-using-hwe.html)




<!--HONumber=Feb17_HO1-->


