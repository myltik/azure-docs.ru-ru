<properties linkid="manage-linux-common-task-mongodb-virtual-machine" urlDisplayName="Install MongoDB" pageTitle="Install MongoDB on a Linux virtual machine in Azure" metaKeywords="Azure vm CentOS, Azure vm Linux, Linux vm, Linux MongoDB" description="Learn how to create an Azure virtual machine with CentOS Linux, and then use SSH or PuTTY to install MongoDB." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MongoDB on a virtual machine running CentOS Linux in Azure" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Установка MongoDB на виртуальную машину под управлением CentOS Linux в Azure

[MongoDB][MongoDB] является популярной, высокопроизводительной базой данных NoSQL с открытым исходным кодом. С помощью портала управления Azure можно создать виртуальную машину под управлением CentOS Linux из коллекции образов. Затем можно установить и настроить базу данных MongoDB на виртуальной машине.

Вы узнаете:

-   Как использовать портал управления для выбора и установки виртуальной машины под управлением CentOS Linux из коллекции.

-   Как подключаться к виртуальной машине с использованием SSH или PuTTY.
-   Как установить MongoDB на виртуальной машине.

## Создание виртуальной машины под управлением CentOS Linux

[WACOM.INCLUDE [create-and-configure-centos-vm-in-portal][create-and-configure-centos-vm-in-portal]]

## Присоединение диска данных

[WACOM.INCLUDE [attach-data-disk-centos-vm-in-portal][attach-data-disk-centos-vm-in-portal]]

## Установка и запуск MongoDB на виртуальной машине

[WACOM.INCLUDE [install-and-run-mongo-on-centos-vm][install-and-run-mongo-on-centos-vm]]

## Сводка

В этом учебнике было показано, как создать виртуальную машину под управлением Linux и удаленно подключаться к ней с помощью SSH или PuTTY. Вы также узнали, как установить и настроить MongoDB на виртуальной машине Linux. Дополнительные сведения о MongoDB см. в [документации по MongoDB][документации по MongoDB].

  [MongoDB]: http://www.mongodb.org/
  [create-and-configure-centos-vm-in-portal]: ../includes/create-and-configure-centos-vm-in-portal.md
  [attach-data-disk-centos-vm-in-portal]: ../includes/attach-data-disk-centos-vm-in-portal.md
  [install-and-run-mongo-on-centos-vm]: ../includes/install-and-run-mongo-on-centos-vm.md
  [документации по MongoDB]: http://www.mongodb.org/display/DOCS/Home
