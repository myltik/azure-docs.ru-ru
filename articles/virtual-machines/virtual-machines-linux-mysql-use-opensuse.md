<properties
	pageTitle="Установка MySQL на виртуальную машину OpenSUSE Linux в Microsoft Azure"
	description="Узнайте, как установить MySQL на виртуальной машине в Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/31/2015"
	ms.author="cynthn"/>

# Установка MySQL на виртуальной машине под управлением OpenSUSE Linux в Azure

[MySQL][MySQL] — это популярная база данных SQL с открытым исходным кодом. В этом учебнике показано, как создать виртуальную машину под управлением OpenSUSE Linux, а затем установить MySQL.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Модель диспетчера ресурсов.


<br>


[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Создание виртуальной машины под управлением OpenSUSE Linux

[AZURE.INCLUDE [create-and-configure-opensuse-vm-in-portal](../../includes/create-and-configure-opensuse-vm-in-portal.md)]

## Установка и запуск MySQL на виртуальной машине

[AZURE.INCLUDE [install-and-run-mysql-on-opensuse-vm](../../includes/install-and-run-mysql-on-opensuse-vm.md)]

## Дальнейшие действия
Дополнительные сведения о MySQL см. в [документации по MySQL][MySQLDocs].

[MySQLDocs]: http://dev.mysql.com/doc/index-topic.html
[MySQL]: http://www.mysql.com
[AzurePortal]: http://manage.windowsazure.com

<!---HONumber=Oct15_HO4-->