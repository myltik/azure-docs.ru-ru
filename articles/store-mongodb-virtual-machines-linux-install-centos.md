<properties linkid="manage-linux-common-task-mongodb-virtual-machine" urlDisplayName="Установка MongoDB" pageTitle="Установка MongoDB на виртуальной машине под управлением Linux в Azure" metaKeywords="Azure виртуальная машина CentOS, Azure виртуальная машина Linux, виртуальная машина Linux, Linux MongoDB" description="Узнайте, как создать виртуальную машину Azure с помощью CentOS Linux, а затем воспользоваться SSH или PuTTY для установки MongoDB." metaCanonical="" services="virtual-machines" documentationCenter="" title="Установка MongoDB на виртуальную машину под управлением CentOS Linux в Azure" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />





#Установка MongoDB на виртуальную машину под управлением CentOS Linux в Azure

[MongoDB](http://www.mongodb.org/) является популярной, высокопроизводительной базой данных NoSQL с открытым исходным кодом. С помощью портала управления Azure можно создать виртуальную машину под управлением CentOS Linux из коллекции образов. Затем можно установить и настроить базу данных MongoDB на виртуальной машине.

Вы узнаете следующее:

- Как использовать портал управления для выбора и установки виртуальной машины под управлением CentOS Linux из коллекции.

- Как подключаться к виртуальной машине с использованием SSH или PuTTY.
- Как установить MongoDB на виртуальной машине.


##Создание виртуальной машины под управлением CentOS Linux

[WACOM.INCLUDE [create-and-configure-centos-vm-in-portal](../includes/create-and-configure-centos-vm-in-portal.md)]

## Присоединение диска данных

[WACOM.INCLUDE [attach-data-disk-centos-vm-in-portal](../includes/attach-data-disk-centos-vm-in-portal.md)]

##Установка и запуск MongoDB на виртуальной машине

[WACOM.INCLUDE [install-and-run-mongo-on-centos-vm](../includes/install-and-run-mongo-on-centos-vm.md)]

##Сводка

В этом учебнике было показано, как создать виртуальную машину под управлением Linux и удаленно подключаться к ней с помощью SSH или PuTTY. Вы также узнали, как установить и настроить MongoDB на виртуальной машине Linux. Дополнительные сведения о MongoDB см. в [документации по MongoDB](http://www.mongodb.org/display/DOCS/Home).



