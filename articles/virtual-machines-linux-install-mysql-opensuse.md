<properties urlDisplayName="Install MySQL" pageTitle="Установка MySQL на виртуальной машине Linux в Azure" metaKeywords="Azure vm OpenSUSE, Linux vm" description="Узнайте, как создать виртуальную машину Azure с ОС OpenSUSE Linux, а затем использовать SSH или PuTTY для установки MySQL." metaCanonical="" services="virtual-machines" documentationCenter="" title="Установка MySQL на виртуальной машине под управлением OpenSUSE Linux в Azure" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt" />

# Установка MySQL на виртуальной машине под управлением OpenSUSE Linux в Azure

[MySQL][MySQL] является популярной базой данных SQL с открытым исходным кодом. С помощью [портала управления Azure][портала управления Azure] можно создать виртуальную машину под управлением OpenSUSE Linux из коллекции образов. Затем можно установить и настроить базу данных MySQL на виртуальной машине.

В этом руководстве вы узнаете следующее:

-   Как использовать портал управления для создания виртуальной машины OpenSUSE Linux из коллекции.

-   Как подключаться к виртуальной машине с использованием SSH или PuTTY.

-   Как установить MySQL на виртуальной машине.

## Создание виртуальной машины под управлением OpenSUSE Linux

[WACOM.INCLUDE [create-and-configure-opensuse-vm-in-portal](../includes/create-and-configure-opensuse-vm-in-portal.md)]

## Установка и запуск MySQL на виртуальной машине

[WACOM.INCLUDE [install-and-run-mysql-on-opensuse-vm](../includes/install-and-run-mysql-on-opensuse-vm.md)]

## Сводка

В этом учебнике было показано, как создать виртуальную машину под управлением OpenSUSE Linux и удаленно подключаться к ней с помощью SSH или PuTTY. Вы также узнали, как установить и настроить MySQL на виртуальной машине Linux. Дополнительные сведения о MySQL см. в [документации по MySQL][документации по MySQL].

  [MySQL]: http://www.mysql.com
  [портала управления Azure]: http://manage.windowsazure.com
  [документации по MySQL]: http://dev.mysql.com/doc/
