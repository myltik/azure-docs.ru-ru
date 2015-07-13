<properties
	pageTitle="Установка MySQL на виртуальной машине под управлением OpenSUSE Linux в Azure"
	description="Узнайте, как установить MySQL на виртуальной машине в Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""
	tags="mysql"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/22/2015"
	ms.author="kathydav"/>

# Установка MySQL на виртуальной машине под управлением OpenSUSE Linux в Azure

[MySQL][MySQL] — это популярная база данных SQL с открытым исходным кодом. В этом учебнике показано:

- Как использовать [портал управления Azure][AzurePortal] для создания виртуальной машины OpenSUSE Linux из образа, доступного в Azure.
- Как подключаться к виртуальной машине с использованием SSH или PuTTY.
- Как установить MySQL на виртуальной машине.

[AZURE.INCLUDE [antares-iaas-signup-iaas](../../includes/antares-iaas-signup-iaas.md)]

## Создание виртуальной машины под управлением OpenSUSE Linux

[AZURE.INCLUDE [create-and-configure-opensuse-vm-in-portal](../../includes/create-and-configure-opensuse-vm-in-portal.md)]

##Установка и запуск MySQL на виртуальной машине

[AZURE.INCLUDE [install-and-run-mysql-on-opensuse-vm](../../includes/install-and-run-mysql-on-opensuse-vm.md)]

##Сводка
В этом учебнике вы научились создавать виртуальную машину под управлением OpenSUSE Linux и удаленно подключаться к ней с помощью SSH или PuTTY. Вы также узнали, как установить и настроить MySQL на виртуальной машине Linux. Дополнительные сведения о MySQL см. в [документации по MySQL][MySQLDocs].

[MySQLDocs]: http://dev.mysql.com/doc/
[MySQL]: http://www.mysql.com
[AzurePortal]: http://manage.windowsazure.com
 

<!---HONumber=July15_HO1-->