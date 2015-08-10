<properties
	pageTitle="Создание настраиваемой виртуальной машины под управлением Linux в Azure"
	description="Узнайте, как создать настраиваемую виртуальную машину под управлением Linux в Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/24/2015"
	ms.author="dkshir"/>

# Как создать настраиваемую виртуальную машину под управлением Linux в Azure

В этой статье описывается создание *пользовательской* виртуальной машины с помощью интерфейса командной строки Azure для Mac, Linux и Windows в режиме управления службами Azure. Мы будем использовать образ Linux из раздела **ОБРАЗЫ** в Azure. Интерфейс командной строки Azure позволяет настраивать, помимо прочих, следующие параметры:

- Подключение виртуальной машины к виртуальной сети.
- Добавление виртуальной машины к существующей облачной службе.
- Добавление виртуальной машины к существующей учетной записи хранения
- Добавление виртуальной машины в группу доступности или расположение.

> [AZURE.IMPORTANT]Если к виртуальной машине необходимо подключаться с помощью имени узла по виртуальной сети или настроить подключения между организациями, при создании виртуальной машины обязательно укажите виртуальную сеть. Виртуальную машину можно настроить для подключения к виртуальной сети только при ее создании. Дополнительную информацию о виртуальных сетях см. в разделе [Обзор виртуальных сетей Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).

<p/>
[AZURE.INCLUDE [service-management-pointer-to-resource-manager](../../includes/service-management-pointer-to-resource-manager.md)]

- [Создание виртуальной машины с ОС Linux](virtual-machines-linux-tutorial.md)


## Создание виртуальной машины Linux в режиме управления службами Azure

[AZURE.INCLUDE [virtual-machines-create-LinuxVM](../../includes/virtual-machines-create-LinuxVM.md)]

<!---HONumber=July15_HO5-->