<properties urlDisplayName="Install MySQL" pageTitle="Установка MySQL на виртуальной машине под управлением OpenSUSE Linux в Azure" metaKeywords="Azure, MySQL" description="Learn to install MySQL on a virtual machine in Azure." metaCanonical="" services="" documentationCenter="" title="Install MongoDB on a virtual machine running CentOS Linux in Azure" authors="kathydav" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="12/11/2014" ms.author="kathydav" />

# Установка MySQL на виртуальной машине под управлением OpenSUSE Linux в Azure

[MySQL][MySQL] - это популярная база данных SQL с открытым исходным кодом.  С помощью [портала управления Azure][AzurePortal] можно создать виртуальную машину под управлением OpenSUSE Linux.  Затем можно установить и настроить базу данных MySQL на виртуальной машине.

В этом учебнике показано:

- как использовать портал управления для создания виртуальной машины OpenSUSE Linux из образа, доступного в Azure;
- Как подключаться к виртуальной машине с использованием SSH или PuTTY.
- Как установить MySQL на виртуальной машине.

[WACOM.INCLUDE [antares-iaas-signup-iaas](../includes/antares-iaas-signup-iaas.md)]

## Создание виртуальной машины под управлением OpenSUSE Linux

[WACOM.INCLUDE [create-and-configure-opensuse-vm-in-portal](../includes/create-and-configure-opensuse-vm-in-portal.md)]

##Установка и запуск MySQL на виртуальной машине

[WACOM.INCLUDE [install-and-run-mysql-on-opensuse-vm](../includes/install-and-run-mysql-on-opensuse-vm.md)]

##Сводка
В этом учебнике вы научились создавать виртуальную машину под управлением OpenSUSE Linux и удаленно подключаться к ней с помощью SSH или PuTTY.  Вы также узнали, как установить и настроить MySQL на виртуальной машине Linux.  Дополнительную информацию о MySQL см. в [документации по MySQL][MySQLDocs].

[MySQLDocs]: http://dev.mysql.com/doc/
[MySQL]: http://www.mysql.com
[AzurePortal]: http://manage.windowsazure.com

<!--HONumber=35.1-->
