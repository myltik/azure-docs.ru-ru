<properties urlDisplayName="Install MongoDB" pageTitle="Установка MongoDB на виртуальную машину под управлением CentOS Linux в Azure" metaKeywords="Azure, MongoDB" description="Узнайте, как установить MongoDB на виртуальную машину в Azure." metaCanonical="" services="" documentationCenter="" title="Установка MongoDB на виртуальную машину под управлением CentOS Linux в Azure" authors="bbenz, MSOpenTech" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="" ms.topic="article" ms.date="01/01/1900" ms.author="bbenz, MSOpenTech" />

# Установка MongoDB на виртуальную машину под управлением CentOS Linux в Azure

[MongoDB][MongoDB] является популярной, высокопроизводительной базой данных NoSQL с открытым исходным кодом. С помощью [портала управления Azure][портала управления Azure] можно создать виртуальную машину под управлением CentOS Linux из коллекции образов. Затем можно установить и настроить базу данных MongoDB на виртуальной машине.

Вы узнаете:

-   Как использовать портал управления для создания виртуальной машины под управлением CentOS Linux из коллекции.
-   Как подключаться к виртуальной машине с использованием SSH или PuTTY.
-   Как установить MongoDB на виртуальной машине.

## Создание виртуальной машины под управлением CentOS Linux

[WACOM.INCLUDE [create-and-configure-centos-vm-in-portal](../includes/create-and-configure-centos-vm-in-portal.md)]

## Установка и запуск MongoDB на виртуальной машине

[WACOM.INCLUDE [install-and-run-mongo-on-centos-vm](../includes/install-and-run-mongo-on-centos-vm.md)]

## Сводка

В этом учебнике было показано, как создать виртуальную машину под управлением Linux и удаленно подключаться к ней с помощью SSH или PuTTY. Вы также узнали, как установить и настроить MongoDB на виртуальной машине Linux. Дополнительные сведения о MongoDB см. в [документации по MongoDB][документации по MongoDB].

  [MongoDB]: http://www.mongodb.org/
  [портала управления Azure]: http://manage.windowsazure.com
  [документации по MongoDB]: http://www.mongodb.org/display/DOCS/Home
