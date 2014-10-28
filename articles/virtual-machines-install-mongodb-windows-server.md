<properties linkid="manage-windows-common-task-mongodb-vm" urlDisplayName="Install MongoDB" pageTitle="Install MongoDB on a Windows Server virtual machine" metaKeywords="Azure vm, Azure MongoDB, Azure remote desktop" description="Learn how to install MongoDB on an Azure VM running Windows Server." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MongoDB on a virtual machine running Windows Server in Azure" authors="kathydav" solutions="" manager="dongill" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Установка MongoDB на виртуальной машине под управлением Windows Server в Azure

[MongoDB][MongoDB] является популярной, высокопроизводительной базой данных NoSQL с открытым исходным кодом. С помощью [портала управления Azure][портала управления Azure] можно создать виртуальную машину под управлением Windows Server из коллекции образов. Затем можно установить и настроить базу данных MongoDB на виртуальной машине.

В этой статье рассматриваются следующие темы.

-   Использование портала управления для создания виртуальной машины Windows Server из коллекции.
-   Подключение к виртуальной машине через удаленный рабочий стол.
-   Подключение диска данных к виртуальной машине.
-   Установка MongoDB на виртуальной машине.

## Создание виртуальной машины под управлением Windows Server

Далее приводятся общие инструкции; их можно изменить, создав конечную точку для разрешения удаленного доступа к MongoDB. (Ее также можно создать позднее; этот процесс описывается после инструкций по установке MongoDB.) На последней странице мастера добавьте конечную точку и настройте ее следующим образом.

-   Дайте ей имя **Mongo**.
-   Укажите **TCP** в качестве протокола.
-   Установите для общедоступного и частного портов значение **27017**.

[WACOM.INCLUDE [виртуальные-машины-создание-WindowsVM][виртуальные-машины-создание-WindowsVM]]

## Подключение диска данных

Чтобы предоставить хранилище для виртуальной машины, подключите диск данных, а затем инициализируйте, чтобы его можно было использовать в Windows. Можно подключать как существующий диск, если имеются данные, которые планируется использовать, так и пустой диск.

[WACOM.INCLUDE [руководство-присоединение-диск-windows-linux][руководство-присоединение-диск-windows-linux]]

Инструкции по инициализации диска см. в разделе "Практическое руководство. Инициализация нового диска данных в Windows Server" в статье [Как присоединить диск данных к виртуальной машине][Как присоединить диск данных к виртуальной машине].

## Установка и запуск MongoDB на виртуальной машине

[WACOM.INCLUDE [install-and-run-mongo-on-win2k8-vm][install-and-run-mongo-on-win2k8-vm]]

## Сводка

В этом учебнике вы научились создавать виртуальную машину Windows Server, удаленно подключаться к ней и подключать диск данных. Вы также узнали, как установить и настроить MongoDB на виртуальной машине Windows. Дополнительные сведения о MongoDB см. в [документации по MongoDB][документации по MongoDB].

  [MongoDB]: http://www.mongodb.org/
  [портала управления Azure]: http://manage.windowsazure.com
  [виртуальные-машины-создание-WindowsVM]: ../includes/virtual-machines-create-WindowsVM.md
  [руководство-присоединение-диск-windows-linux]: ../includes/howto-attach-disk-windows-linux.md
  [Как присоединить диск данных к виртуальной машине]: http://azure.microsoft.com/ru-ru/documentation/articles/storage-windows-attach-disk/
  [install-and-run-mongo-on-win2k8-vm]: ../includes/install-and-run-mongo-on-win2k8-vm.md
  [документации по MongoDB]: http://www.mongodb.org/display/DOCS/Home
