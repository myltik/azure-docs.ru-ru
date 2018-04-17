---
title: Подключение к виртуальной машине под управлением Linux с помощью удаленного рабочего стола | Документация Майкрософт
description: Узнайте, как установить и настроить удаленный рабочий стол для подключения к виртуальной машине Microsoft Azure под управлением Linux для классической модели развертывания.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 34348659-ddb7-41da-82d6-b5885859e7e4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: iainfou
ms.openlocfilehash: 0e1bfe468e1572ca98be956d39d82df562dce0e6
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
---
# <a name="using-remote-desktop-to-connect-to-a-microsoft-azure-linux-vm"></a>Использование удаленного рабочего стола для подключения к виртуальной машине Microsoft Azure на базе Linux
> [!IMPORTANT] 
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../../../resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов. Обновленная версия этой статьи для модели развертывания с помощью Resource Manager доступна [здесь](../use-remote-desktop.md).

## <a name="overview"></a>Обзор
RDP (протокол удаленного рабочего стола) — это защищаемый протокол, используемый для Windows. Как можно использовать протокол RDP для удаленного подключения к виртуальной машине под управлением Linux?

Ответ вы найдете в этом руководстве! Оно поможет установить и настроить xrdp на виртуальной машине Microsoft Azure на базе Linux, а также подключиться к ней с помощью удаленного рабочего стола на компьютере под управлением Windows. В качестве примера в этом руководстве используется виртуальная машина Linux под управлением Ubuntu или OpenSUSE.

Инструмент xrdp представляет собой сервер RDP с открытым исходным кодом, позволяющий подключиться к серверу на базе Linux с компьютера Windows через удаленный рабочий стол. Производительность сервера RDP лучше, чем производительность VNC (Virtual Network Computing). VNC выполняет подготовку с помощью графики в качестве JPEG, и это занимает некоторое время, тогда как RDP работает быстро и без проблем.

> [!NOTE]
> Вам необходима виртуальная машина Microsoft Azure под управлением Linux. Чтобы создать и настроить виртуальную машину на базе Linux, ознакомьтесь с [учебником по виртуальным машинам Azure под управлением Linux](createportal-classic.md).
> 
> 

## <a name="create-an-endpoint-for-remote-desktop"></a>Создание конечной точки для удаленного рабочего стола
Здесь мы будем использовать конечную точку по умолчанию 3389 для удаленного рабочего стола. Настройте конечную точку 3389 как `Remote Desktop` для виртуальной машины Linux, как показано ниже:

![изображение](./media/remote-desktop/endpoint-for-linux-server.png)

Если вы не знаете, как настроить конечную точку для виртуальной машины, ознакомьтесь с [этим руководством](setup-endpoints.md).

## <a name="install-gnome-desktop"></a>Установка Gnome Desktop
Подключитесь к виртуальной машине на базе Linux с помощью `putty` и установите `Gnome Desktop`.

Для Ubuntu используйте:

```bash
sudo apt-get update
sudo apt-get install ubuntu-desktop
```

Для OpenSUSE используйте следующую команду:

```bash
sudo zypper install gnome-session
```

## <a name="install-xrdp"></a>Установка xrdp
Для Ubuntu используйте:

```bash
sudo apt-get install xrdp
```

Для OpenSUSE используйте следующую команду:

> [!NOTE]
> Обновите версию OpenSUSE на версию, используемую в команде ниже. В примере ниже используется версия `OpenSUSE 13.2`.
> 
> 

```bash
sudo zypper in http://download.opensuse.org/repositories/X11:/RemoteDesktop/openSUSE_13.2/x86_64/xrdp-0.9.0git.1401423964-2.1.x86_64.rpm
sudo zypper install tigervnc xorg-x11-Xvnc xterm remmina-plugin-vnc
```

## <a name="start-xrdp-and-set-xdrp-service-at-boot-up"></a>Запуск xrdp и настройка запуска xdrp при загрузке системы
Для OpenSUSE используйте следующую команду:

```bash
sudo systemctl start xrdp
sudo systemctl enable xrdp
```

В Ubuntu программа xrdp после установки автоматически запустится и включится при загрузке системы.

## <a name="using-xfce-if-you-are-using-an-ubuntu-version-later-than-ubuntu-1204lts"></a>Использование xfce при работе с версией Ubuntu, выпущенной после Ubuntu 12.04LTS
Так как текущая версия xrdp не поддерживает Gnome Desktop из версий Ubuntu, выпущенных после Ubuntu 12.04LTS, вместо нее мы будем использовать `xfce` Desktop.

Чтобы установить `xfce`, используйте следующую команду:

```bash
sudo apt-get install xubuntu-desktop
```

Затем включите `xfce` с помощью команды:

```bash
echo xfce4-session >~/.xsession
```

Измените файл конфигурации `/etc/xrdp/startwm.sh`:

```bash
sudo vi /etc/xrdp/startwm.sh   
```

Добавьте строку `xfce4-session` перед строкой `/etc/X11/Xsession`.

Перезапустите службу xrdp:

```bash
sudo service xrdp restart
```

## <a name="connect-your-linux-vm-from-a-windows-machine"></a>Подключение к виртуальной машине на базе Linux с компьютера Windows
На компьютере с Windows запустите клиент удаленного рабочего стола и введите DNS-имя виртуальной машины Linux. Или перейдите на панель мониторинга виртуальной машины на портале Azure и щелкните `Connect`, чтобы подключить виртуальную машину Linux. В этом случае появится окно входа:

![изображение](./media/remote-desktop/no2.png)

Войдите с помощью имени пользователя и пароля виртуальной машины Linux.

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения об использовании xrdp см. здесь: [http://www.xrdp.org/](http://www.xrdp.org/).
