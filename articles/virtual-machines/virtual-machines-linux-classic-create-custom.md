<properties
	pageTitle="Создание виртуальной машины Linux | Microsoft Azure"
	description="Узнайте, как создать настраиваемую виртуальную машину под управлением Linux с помощью классической модели развертывания."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/23/2016"
	ms.author="iainfou"/>

# Создание настраиваемой виртуальной машины Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

В этой статье описано создание настраиваемой виртуальной машины с помощью интерфейса командной строки Azure и классической модели развертывания. Мы используем образ Linux из раздела **ОБРАЗЫ** в Azure. Интерфейс командной строки Azure позволяет настраивать, помимо прочих, следующие параметры:

- Подключение виртуальной машины к виртуальной сети.
- Добавление виртуальной машины к существующей облачной службе.
- Добавление виртуальной машины к существующей учетной записи хранения
- Добавление виртуальной машины в группу доступности или расположение.

> [AZURE.IMPORTANT] Если к виртуальной машине необходимо подключаться с помощью имени узла по виртуальной сети или настроить подключения между организациями, при создании виртуальной машины обязательно укажите виртуальную сеть. Виртуальную машину можно настроить для подключения к виртуальной сети только при ее создании. Дополнительную информацию о виртуальных сетях см. в разделе [Обзор виртуальных сетей Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).


## Создание виртуальной машины Linux с использованием классической модели развертывания

[AZURE.INCLUDE [virtual-machines-create-LinuxVM](../../includes/virtual-machines-create-linuxvm.md)]

<!----HONumber=AcomDC_0824_2016-->