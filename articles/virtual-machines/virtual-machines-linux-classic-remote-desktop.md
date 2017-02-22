---
title: "Подключение к виртуальной машине под управлением Linux с помощью удаленного рабочего стола | Документация Майкрософт"
description: "Узнайте, как установить и настроить удаленный рабочий стол для подключения к виртуальной машине Microsoft Azure под управлением Linux."
services: virtual-machines-linux
documentationcenter: 
author: SuperScottz
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 34348659-ddb7-41da-82d6-b5885859e7e4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/01/2016
ms.author: mingzhan
translationtype: Human Translation
ms.sourcegitcommit: f6537e4ebac76b9f3328223ee30647885ee15d3e
ms.openlocfilehash: 5acf43ac97fca98bc6d06a174b5fcc66f06a03b2


---
# <a name="using-remote-desktop-to-connect-to-a-microsoft-azure-linux-vm"></a>Использование удаленного рабочего стола для подключения к виртуальной машине Microsoft Azure на базе Linux
> [!IMPORTANT] 
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель диспетчера ресурсов и классическая модель](../azure-resource-manager/resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов.

## <a name="overview"></a>Обзор
RDP (протокол удаленного рабочего стола) — это защищаемый протокол, используемый для Windows. Как можно использовать протокол RDP для удаленного подключения к виртуальной машине под управлением Linux?

Ответ вы найдете в этом руководстве! Оно поможет установить и настроить xrdp на виртуальной машине Microsoft Azure на базе Linux, а также подключиться к ней с помощью удаленного рабочего стола на компьютере под управлением Windows. В качестве примера в этом руководстве используется виртуальная машина Linux под управлением Ubuntu или OpenSUSE.

Xrdp представляет собой сервер RDP с открытым исходным кодом, позволяющий подключиться к серверу на базе Linux с компьютера Windows через удаленный рабочий стол. Он выполняется гораздо лучше, чем VNC (Virtual Network Computing). VNC отличается проблемами с качеством, характерными для JPEG, и медленной работой, а RDP работает быстро и без проблем.

> [!NOTE]
> Вам необходима виртуальная машина Microsoft Azure под управлением Linux. Чтобы создать и настроить виртуальную машину на базе Linux, ознакомьтесь с [учебником по виртуальным машинам Azure под управлением Linux](virtual-machines-linux-classic-createportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).
> 
> 

## <a name="create-endpoint-for-remote-desktop"></a>Создание конечной точки для удаленного рабочего стола
Здесь мы будем использовать конечную точку по умолчанию 3389 для удаленного рабочего стола. Настройте конечную точку 3389 как удаленный рабочий стол для виртуальной машины Linux, как показано ниже:

![изображение](./media/virtual-machines-linux-classic-remote-desktop/no1.png)

Если вы не знаете, как настроить конечную точку для виртуальной машины, воспользуйтесь [руководством](virtual-machines-linux-classic-setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="install-gnome-desktop"></a>Установка Gnome Desktop
Подключитесь к виртуальной машине на базе Linux с помощью putty и установите `Gnome Desktop`.

Для Ubuntu используйте:

    #sudo apt-get update
    #sudo apt-get install ubuntu-desktop


Для OpenSUSE используйте следующую команду:

    #sudo zypper install gnome-session

## <a name="install-xrdp"></a>Установка xrdp
Для Ubuntu используйте:

    #sudo apt-get install xrdp

Для OpenSUSE используйте следующую команду:

> [!NOTE]
> Укажите в приведенной ниже команде используемую вами версию OpenSUSE (пример приведен для `OpenSUSE 13.2`).
> 
> 

    #sudo zypper in http://download.opensuse.org/repositories/X11:/RemoteDesktop/openSUSE_13.2/x86_64/xrdp-0.9.0git.1401423964-2.1.x86_64.rpm
    #sudo zypper install tigervnc xorg-x11-Xvnc xterm remmina-plugin-vnc


## <a name="start-xrdp-and-set-xdrp-service-at-boot-up"></a>Запуск xrdp и настройка запуска xdrp при загрузке системы
Для OpenSUSE используйте следующую команду:

    #sudo systemctl start xrdp
    #sudo systemctl enable xrdp

В Ubuntu после установки управляющей программы xrdp она автоматически запустится и включится при загрузке системы.

## <a name="using-xfce-if-you-are-using-ubuntu-version-later-than-ubuntu-1204lts"></a>Использование xfce при работе с версией Ubuntu, выпущенной после Ubuntu 12.04LTS
Так как текущая версия xrdp не поддерживает среду Gnome Desktop из версий Ubuntu, выпущенных после Ubuntu 12.04LTS, вместо нее мы будем использовать `xfce` Desktop.

Установите `xfce`с помощью следующей команды:

    #sudo apt-get install xubuntu-desktop

Затем включите `xfce`с помощью следующей команды:

    #echo xfce4-session >~/.xsession

Измените файл конфигурации `/etc/xrdp/startwm.sh`с помощью следующей команды:

    #sudo vi /etc/xrdp/startwm.sh   

Добавьте строку `xfce4-session` перед строкой `/etc/X11/Xsession`.

Перезапустите службу xrdp, используя следующую команду:

    #sudo service xrdp restart


## <a name="connect-your-linux-vm-from-a-windows-machine"></a>Подключение к виртуальной машине на базе Linux с компьютера Windows
На компьютере с Windows запустите клиент удаленного рабочего стола, введите DNS-имя виртуальной машины на базе Linux или откройте `Dashboard` своей виртуальной машины на классическом портале Azure и нажмите кнопку `Connect`, после чего отобразится следующее окно входа:

![изображение](./media/virtual-machines-linux-classic-remote-desktop/no2.png)

Войдите в систему, указав значения `user` & `password` для виртуальной машины на базе Linux, и сразу же приступайте к использованию удаленного рабочего стола из виртуальной машины Microsoft Azure на базе Linux!

## <a name="next"></a>Далее
Дополнительные сведения об использовании xrdp см. [здесь](http://www.xrdp.org/).




<!--HONumber=Feb17_HO3-->


