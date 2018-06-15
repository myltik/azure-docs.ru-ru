---
title: Подготовка виртуального жесткого диска Debian Linux в Azure | Документация Майкрософт
description: Дополнительные сведения о создании VHD-файлов Debian 7 и 8 для развертывания в Azure.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: a6de7a7c-cc70-44e7-aed0-2ae6884d401a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: f8e98ae823d03dae475efca48a4ce32f27317882
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30908849"
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Подготовка виртуального жесткого диска Debian для Azure
## <a name="prerequisites"></a>предварительным требованиям
В этом разделе предполагается, что вы уже установили на виртуальный жесткий диск (VHD) операционную систему Debian Linux из ISO-файла, полученного с [веб-сайта Debian](https://www.debian.org/distrib/). Существует несколько средств для создания VHD-файлов. Hyper-V — это лишь один из примеров. Инструкции по работе с Hyper-V см. в разделе [Установка роли Hyper-V и настройка виртуальной машины](https://technet.microsoft.com/library/hh846766.aspx).

## <a name="installation-notes"></a>Замечания по установке
* Дополнительные сведения о подготовке Linux для Azure см. в разделе [Общие замечания по установке Linux](create-upload-generic.md#general-linux-installation-notes).
* Более новый формат VHDX не поддерживается в Azure. Можно преобразовать диск в формат VHD с помощью диспетчера Hyper-V или командлета **convert-vhd** .
* При установке системы Linux рекомендуется использовать стандартные разделы, а не LVM (как правило, значение по умолчанию во многих дистрибутивах). Это позволит избежать конфликта имен LVM при клонировании виртуальных машин, особенно если диск с OC может быть подключен к другой ВМ в целях устранения неполадок. Для дисков данных можно использовать [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) или [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Не настраивайте раздел подкачки на диске с ОС. Можно настроить агент Linux для Azure для создания файла подкачки на временном диске ресурсов. Дополнительные сведения описаны далее.
* Размер виртуальной памяти всех VHD в Azure должен быть округлен до 1 МБ. При конвертации диска в формате RAW в виртуальный жесткий диск убедитесь, что размер диска RAW в несколько раз превышает 1 МБ. См. дополнительные сведения в [примечаниях по установке Linux](create-upload-generic.md#general-linux-installation-notes).

## <a name="use-azure-manage-to-create-debian-vhds"></a>Создание виртуальных жестких дисков Debian с помощью Azure-Manage
Создавать виртуальные жесткие диски Debian для Azure можно с помощью сценариев [azure-manage](https://github.com/credativ/azure-manage) от компании [credativ](http://www.credativ.com/). Рекомендуется использовать их, а не создавать образ с нуля. Например, чтобы создать виртуальный жесткий диск Debian 8, выполните следующие команды для скачивания azure-manage (и зависимостей) и выполнения скрипта azure_build_image:

    # sudo apt-get update
    # sudo apt-get install git qemu-utils mbr kpartx debootstrap

    # sudo apt-get install python3-pip python3-dateutil python3-cryptography
    # sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
    # git clone https://github.com/credativ/azure-manage.git
    # cd azure-manage
    # sudo pip3 install .

    # sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## <a name="manually-prepare-a-debian-vhd"></a>Подготовка виртуального жесткого диска Debian вручную
1. В диспетчере Hyper-V выберите виртуальную машину.
2. Щелкните **Подключение** , чтобы открыть окно консоли для виртуальной машины.
3. Закомментируйте строку **deb cdrom** в `/etc/apt/source.list`, если вы настроили виртуальную машину в соответствии с ISO-файлом.
4. Отредактируйте файл `/etc/default/grub` и измените параметр **GRUB_CMDLINE_LINUX** следующим образом, чтобы включить дополнительные параметры ядра для Azure.
   
        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200 earlyprintk=ttyS0,115200 rootdelay=30"
5. Перестройте grub и выполните команду:
   
        # sudo update-grub
6. Добавьте репозитории Azure Debian в файл /etc/apt/sources.list для Debian 7 или 8.
   
    **Debian 7.x "Wheezy"**
   
        deb http://debian-archive.trafficmanager.net/debian wheezy-backports main
        deb-src http://debian-archive.trafficmanager.net/debian wheezy-backports main
        deb http://debian-archive.trafficmanager.net/debian-azure wheezy main
        deb-src http://debian-archive.trafficmanager.net/debian-azure wheezy main

    **Debian 8.x "Jessie"**

        deb http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb http://debian-archive.trafficmanager.net/debian-azure jessie main
        deb-src http://debian-archive.trafficmanager.net/debian-azure jessie main


1. Установите агент Linux для Azure:
   
        # sudo apt-get update
        # sudo apt-get install waagent
2. Для Debian 7 необходимо запустить ядро на основе версии 3.16 из репозитория wheezy-backports. Сначала создайте файл с именем /etc/apt/preferences.d/linux.pref со следующим содержимым:
   
        Package: linux-image-amd64 initramfs-tools
        Pin: release n=wheezy-backports
        Pin-Priority: 500
   
    Затем выполните команду "sudo apt-get install linux-image-amd64", чтобы установить новое ядро.
3. Отмените подготовку виртуальной машины, подготовьте ее к работе в среде Azure и выполните команду:
   
        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout
4. В диспетчере Hyper-V выберите **Действие** -> Завершение работы. Виртуальный жесткий диск Linux готов к передаче в Azure.

## <a name="next-steps"></a>Дополнительная информация
Теперь виртуальный жесткий диск Debian можно использовать для создания новых виртуальных машин Azure. Если вы отправляете VHD-файл в Azure впервые, см. раздел [Вариант 1. Передача VHD](upload-vhd.md#option-1-upload-a-vhd).

