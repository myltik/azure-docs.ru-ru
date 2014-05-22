<properties linkid="manage-linux-common-task-mysql-virtual-machine" urlDisplayName="Установка MySQL" pageTitle="Установка MySQL на виртуальной машине под управлением Linux в Azure" metaKeywords="виртуальная машина Azure OpenSUSE, виртуальная машина Linux" description="Узнайте, как создать виртуальную машину Azure с OpenSUSE Linux, а затем использовать SSH или PuTTY для установки MySQL." metaCanonical="" services="virtual-machines" documentationCenter="" title="Установка MySQL на виртуальной машине под управлением OpenSUSE Linux в Azure" authors="" solutions="" manager="" editor="" />




#Установка MySQL на виртуальной машине под управлением OpenSUSE Linux в Azure

[MySQL](http://www.mysql.com) является популярной базой данных SQL с открытым исходным кодом. С помощью [портала управления Azure][AzurePreviewPortal] можно создать виртуальную машину под управлением OpenSUSE Linux из коллекции образов.  Затем можно установить и настроить базу данных MySQL на виртуальной машине.

В этом руководстве вы узнаете следующее:

- Как использовать портал управления для создания виртуальной машины OpenSUSE Linux из коллекции.

- Как подключаться к виртуальной машине с использованием SSH или PuTTY.

- Как установить MySQL на виртуальной машине.

##Создание виртуальной машины под управлением OpenSUSE Linux

[WACOM.INCLUDE [create-and-configure-opensuse-vm-in-portal](../includes/create-and-configure-opensuse-vm-in-portal.md)]

##Установка и запуск MySQL на виртуальной машине

[WACOM.INCLUDE [install-and-run-mysql-on-opensuse-vm](../includes/install-and-run-mysql-on-opensuse-vm.md)]

##Сводка

В этом учебнике было показано, как создать виртуальную машину под управлением OpenSUSE Linux и удаленно подключаться к ней с помощью SSH или PuTTY. Вы также узнали, как установить и настроить MySQL на виртуальной машине Linux. Дополнительные сведения о MySQL см. в [документации по MySQL](http://dev.mysql.com/doc/).

[AzurePreviewPortal]: http://manage.windowsazure.com

