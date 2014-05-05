<properties linkid="manage-windows-common-task-mongodb-vm" urlDisplayName="Установка MongoDB" pageTitle="Установка MongoDB на виртуальной машине Windows Server" metaKeywords="Виртуальная машина Azure, Azure MongoDB, удаленный рабочий стол Azure" description="Узнайте, как создать виртуальную машину Azure с помощью Windows Server 2008 R2, а затем использовать удаленный рабочий стол для установки MongoDB." metaCanonical="" services="virtual-machines" documentationCenter="" title="Установка MongoDB на виртуальной машине под управлением Windows Server в Azure" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />




#Установка MongoDB на виртуальной машине под управлением Windows Server в Azure

[MongoDB][MongoDB] является популярной, высокопроизводительной базой данных NoSQL с открытым исходным кодом.  С помощью [портала управления Azure][AzurePreviewPortal] можно создать виртуальную машину под управлением Windows Server из коллекции образов.  Затем можно установить и настроить базу данных MongoDB на виртуальной машине.

В этом руководстве вы узнаете следующее:

- Как использовать портал управления для создания виртуальной машины Windows Server из коллекции.
- Как подключаться к виртуальной машине с использованием удаленного рабочего стола.
- Как установить MongoDB на виртуальной машине.

## Создание виртуальной машины под управлением ОС Windows Server 2008 R2

[WACOM.INCLUDE [create-and-configure-windows-server-2008-vm-in-portal](../includes/create-and-configure-windows-server-2008-vm-in-portal.md)]

## Присоединение диска данных

[WACOM.INCLUDE [attach-data-disk-windows-server-2008-vm-in-portal](../includes/attach-data-disk-windows-server-2008-vm-in-portal.md)]

## Установка и запуск MongoDB на виртуальной машине 

[WACOM.INCLUDE [install-and-run-mongo-on-win2k8-vm](../includes/install-and-run-mongo-on-win2k8-vm.md)]

##Сводка.
В этом учебнике вы научились создавать виртуальную машину Windows Server и удаленно подключаться к ней.  Вы также узнали, как установить и настроить MongoDB на виртуальной машине Windows. Дополнительные сведения о MongoDB см. в [документации по MongoDB][MongoDocs].

[MongoDocs]: http://www.mongodb.org/display/DOCS/Home
[MongoDB]: http://www.mongodb.org/
[AzurePreviewPortal]: http://manage.windowsazure.com

