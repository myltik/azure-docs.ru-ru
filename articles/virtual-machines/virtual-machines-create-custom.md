<properties
	pageTitle="Создание настраиваемой виртуальной машины | Microsoft Azure"
	description="Узнайте, как создать настраиваемую виртуальную машину на портале Azure и использованием классической модели развертывания."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="cynthn"/>

#Создание настраиваемой виртуальной машины


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Модель диспетчера ресурсов.
 


*Настраиваемая* виртуальная машина — это просто виртуальная машина, созданная с помощью параметра **Из коллекции**, что дает больший выбор настроек, чем параметр **Быстрое создание**. Вот какие параметры доступны:

- подключение виртуальной машины к виртуальной сети;
- установка агента виртуальной машины Azure и расширений виртуальной машины Azure, например для защиты от вредоносных программ;
- добавление виртуальной машины в существующую облачную службу;
- добавление виртуальной машины в существующую учетную запись хранения;
- добавление виртуальной машины в группу доступности.

> [AZURE.IMPORTANT]Если к виртуальной машине необходимо подключаться с помощью имени узла по виртуальной сети или настроить подключения между организациями, при создании виртуальной машины обязательно укажите виртуальную сеть. Виртуальную машину можно настроить для подключения к виртуальной сети только при ее создании. Дополнительную информацию о виртуальных сетях см. в разделе [Обзор виртуальных сетей Azure](virtual-networks-overview.md).

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

<!---HONumber=Oct15_HO3-->